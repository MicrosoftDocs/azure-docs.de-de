---
title: 'Tutorial: Verwenden von Apache HBase in Azure HDInsight'
description: Folgen Sie diesem Apache HBase-Tutorial für die ersten Schritte mit Hadoop in HDInsight. Erstellen Sie über die HBase-Shell Tabellen und fragen Sie diese mit Hive ab.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/14/2020
ms.openlocfilehash: d24c63e3a2989173e718cd27fa43cecc50181047
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533494"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Tutorial: Verwenden von Apache HBase in Azure HDInsight

Dieses Tutorial zeigt, wie Sie in Azure HDInsight einen Apache HBase-Cluster erstellen, HBase-Tabellen anlegen und Tabellen mit Apache Hive abfragen können.  Allgemeine Informationen zu HBase finden Sie in der [Übersicht über HDInsight HBase](./apache-hbase-overview.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Apache HBase-Clusters
> * Erstellen von HBase-Tabellen und Einfügen von Daten
> * Verwenden von Apache Hive zum Abfragen von Apache HBase
> * Verwenden der HBase-REST-APIs mit Curl
> * Überprüfen des Clusterstatus

## <a name="prerequisites"></a>Voraussetzungen

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. In den Beispielen dieses Artikels wird die Bash-Shell unter Windows 10 für die cURL-Befehle verwendet. Die Installationsschritte finden Sie unter [Windows Subsystem for Linux Installation Guide for Windows 10](/windows/wsl/install-win10) (Windows-Subsystem für Linux: Installationshandbuch für Windows 10).  Es funktionieren auch andere [Unix-Shells](https://www.gnu.org/software/bash/).  Die cURL-Beispiele können mit einigen geringfügigen Änderungen auch an einer Windows-Eingabeaufforderung verwendet werden.  Alternativ dazu können Sie das Windows PowerShell-Cmdlet [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) verwenden.

## <a name="create-apache-hbase-cluster"></a>Erstellen eines Apache HBase-Clusters

Im folgenden Verfahren wird eine Azure Resource Manager-Vorlage verwendet, um einen HBase-Cluster zu erstellen. Mit der Vorlage wird auch das abhängige Azure Storage-Standardkonto erstellt. Informationen zu den Parametern, die in diesem Verfahren und in anderen Verfahren zur Clustererstellung verwendet werden, finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Wählen Sie die folgende Abbildung aus, um die Vorlage im Azure-Portal zu öffnen. Die Vorlage finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Geben Sie im Dialogfeld **Benutzerdefinierte Bereitstellung** die folgenden Werte ein:

    |Eigenschaft |BESCHREIBUNG |
    |---|---|
    |Subscription|Wählen Sie Ihr Azure-Abonnement aus, das zum Erstellen des Clusters verwendet wird.|
    |Resource group|Erstellen Sie eine neue Azure Resource Management-Gruppe, oder verwenden Sie eine vorhandene Ressourcengruppe.|
    |Position|Geben Sie den Standort der Ressourcengruppe an. |
    |ClusterName|Geben Sie einen Namen für den HBase-Cluster ein.|
    |Clusteranmeldename und Kennwort|Der Standardanmeldename lautet **admin** .|
    |SSH-Benutzername und Kennwort|Der Standardbenutzername lautet **sshuser** .|

    Andere Parameter sind optional.  

    Jeder Cluster verfügt über eine Abhängigkeit von einem Azure Storage-Konto. Nach dem Löschen eines Clusters verbleiben die Daten im Speicherkonto. Zur Bildung des Standardnamens für das Speicherkonto des Clusters wird „store“ an den Clusternamen angehängt. Er ist im Variablenabschnitt der Vorlage hartcodiert.

3. Wählen Sie **Ich stimme den oben genannten Geschäftsbedingungen zu** , und wählen Sie anschließend **Kaufen** aus. Das Erstellen eines Clusters dauert ca. 20 Minuten.

Nachdem Sie den HBase-Cluster gelöscht haben, können Sie im gleichen Standardblobcontainer einen neuen HBase-Cluster erstellen. Der neue Cluster übernimmt die im vorherigen Cluster erstellten HBase-Tabellen. Es wird empfohlen, die HBase-Tabellen vor dem Löschen des Clusters zu deaktivieren, um Inkonsistenzen zu vermeiden.

## <a name="create-tables-and-insert-data"></a>Erstellen von Tabellen und Einfügen von Daten

Sie können SSH verwenden, um eine Verbindung mit Apache HBase-Clustern herzustellen, und dann mithilfe von [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) HBase-Tabellen erstellen, Daten einfügen und Daten abfragen.

Den meisten Benutzern werden die Daten im Tabellenformat angezeigt:

![HDInsight Apache HBase-Tabellendaten](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

In HBase (eine Implementierung von [Cloud BigTable](https://cloud.google.com/bigtable/)) sehen die gleichen Daten wie folgt aus:

![HDInsight Apache HBase-BigTable-Daten](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**So verwenden Sie die HBase-Shell**

1. Verwenden Sie zum Herstellen der Verbindung mit Ihrem HBase-Cluster `ssh`. Bearbeiten Sie den unten angegebenen Befehl, indem Sie `CLUSTERNAME` durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verwenden Sie den Befehl `hbase shell`, um die interaktive HBase-Shell zu starten. Geben Sie den folgenden Befehl in Ihrer SSH-Verbindung ein:

    ```bash
    hbase shell
    ```

1. Verwenden Sie den Befehl `create`, um eine HBase-Tabelle mit zwei Spaltenfamilien zu erstellen. Für die Tabellen- und Spaltennamen wird die Groß-/Kleinschreibung beachtet. Geben Sie den folgenden Befehl ein:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Verwenden Sie den Befehl `list`, um alle Tabellen in HBase aufzulisten. Geben Sie den folgenden Befehl ein:

    ```hbase
    list
    ```

1. Verwenden Sie den Befehl `put`, um Werte in einer angegebenen Spalte einer angegebenen Zeile in einer bestimmten Tabelle einzufügen. Geben Sie die folgenden Befehle ein:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Verwenden Sie den Befehl `scan`, um die Daten der Tabelle `Contacts` zu überprüfen und zurückzugeben. Geben Sie den folgenden Befehl ein:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Apache Hadoop HBase-Shell](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Verwenden Sie den Befehl `get`, um den Inhalt einer Zeile abzurufen. Geben Sie den folgenden Befehl ein:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Sie erhalten ähnliche Ergebnisse wie mit dem Befehl `scan`, da nur eine Zeile vorhanden ist.

    Weitere Informationen zum HBase-Tabellenschema finden Sie unter [Einführung in das Apache HBase-Schemadesign](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Beschreibungen weiterer HBase-Befehle finden Sie im [Apache HBase-Referenzhandbuch](https://hbase.apache.org/book.html#quickstart).

1. Verwenden Sie den Befehl `exit`, um die interaktive HBase-Shell zu beenden. Geben Sie den folgenden Befehl ein:

    ```hbaseshell
    exit
    ```

**So laden Sie Massendaten in die HBase-Kontakttabelle hoch**

HBase bietet mehrere Methoden zum Laden von Daten in Tabellen.  Weitere Informationen finden Sie unter [Laden von Massendaten](https://hbase.apache.org/book.html#arch.bulk.load).

Eine Datei mit Beispieldaten finden Sie in einem öffentlichen Blobcontainer: `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Diese Datendatei hat folgenden Inhalt:

`8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.`

`16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz`

`4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta`

`16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.`

`3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive`

`3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle`

`10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street`

`4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street`

`4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.`

`16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive`

Sie haben die Option, eine Textdatei zu erstellen und die Datei in Ihr eigenes Speicherkonto hochzuladen. Anweisungen hierzu finden Sie unter [Hochladen von Daten für Apache Hadoop-Aufträge in HDInsight](../hdinsight-upload-data.md).

In der folgenden Prozedur wird die soeben erstellte HBase-Tabelle `Contacts` verwendet.

1. Führen Sie über Ihre geöffnete SSH-Verbindung den folgenden Befehl aus, um die Datendatei in das StoreFiles-Format zu konvertieren und unter dem mit `Dimporttsv.bulk.output` angegebenen relativen Pfad zu speichern.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Führen Sie den folgenden Befehl aus, um die Daten aus `/example/data/storeDataFileOutput` in der HBase-Tabelle hochzuladen:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Sie können die HBase-Shell öffnen und den Tabelleninhalt mit dem Befehl `scan` auflisten.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Verwenden von Apache Hive zum Abfragen von Apache HBase

Sie können Daten in HBase-Tabellen mithilfe von [Apache Hive](https://hive.apache.org/) abfragen. In diesem Abschnitt erstellen Sie eine Ihrer HBase-Tabelle zugeordnete Hive-Tabelle, mit der Sie die Daten Ihrer HBase-Tabelle abfragen.

1. Verwenden Sie über Ihre geöffnete SSH-Verbindung den folgenden Befehl, um Beeline zu starten:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Weitere Informationen zu Beeline finden Sie unter [Verwenden von Hive mit Hadoop in HDInsight über Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Führen Sie das folgende [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)-Skript aus, um eine der HBase-Tabelle zugeordnete Hive-Tabelle zu erstellen. Stellen Sie vor Ausführung dieser Anweisung sicher, dass Sie mithilfe der HBase-Shell die zuvor in diesem Artikel erwähnte Beispieltabelle erstellt haben.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Führen Sie das folgende HiveQL-Skript aus, um die Daten in der HBase-Tabelle abzufragen:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Verwenden Sie `!exit`, um Beeline zu beenden.

1. Verwenden Sie zum Beenden Ihrer SSH-Verbindung den Befehl `exit`.

### <a name="separate-hive-and-hbase-clusters"></a>Separate Hive- und HBase-Cluster

Die Hive-Abfrage für den Zugriff auf HBase-Daten muss nicht aus dem HBase-Cluster ausgeführt werden. Alle Cluster, die über Hive verfügen (einschließlich Spark, Hadoop, HBase oder Interactive Query), können zum Abfragen von HBase-Daten verwendet werden, sofern die folgenden Schritte ausgeführt wurden:

1. Beide Cluster müssen an dasselbe virtuelle Netzwerk und Subnetz angefügt werden.
2. Kopieren Sie `/usr/hdp/$(hdp-select --version)/hbase/conf/hbase-site.xml` von den Hauptknoten des HBase-Clusters auf die Hauptknoten des Hive-Clusters.

### <a name="secure-clusters"></a>Sichere Cluster

HBase-Daten können auch über Hive abgefragt werden, indem HBase mit aktiviertem Enterprise-Sicherheitspaket (ESP) verwendet wird: 

1. Bei Verwendung eines Musters mit mehreren Clustern muss für beide Cluster ESP aktiviert sein. 
2. Stellen Sie sicher, dass dem Benutzer `hive` Berechtigungen zum Zugreifen auf die HBase-Daten über das Apache Ranger-Plug-In von HBase gewährt werden, um für Hive das Abfragen der HBase-Daten zu ermöglichen.
3. Bei Verwendung von separaten ESP-fähigen Clustern muss der Inhalt von `/etc/hosts` von den Hauptknoten des HBase-Clusters an den Ordner `/etc/hosts` der Hauptknoten des Hive-Clusters angefügt werden. 
> [!NOTE]
> Nachdem beide Cluster skaliert wurden, muss `/etc/hosts` erneut angefügt werden.

## <a name="use-hbase-rest-apis-using-curl"></a>Verwenden der HBase-REST-APIs mit Curl

Die REST-API wird durch [Standardauthentifizierung](https://en.wikipedia.org/wiki/Basic_access_authentication)gesichert. Sie sollten Anforderungen immer über HTTPS (Secure HTTP) stellen, um sicherzustellen, dass Ihre Anmeldeinformationen sicher an den Server gesendet werden.

1. Fügen Sie das folgende benutzerdefinierte Startskript im Abschnitt **Skriptaktion** hinzu, um HBase-REST-APIs im HDInsight-Cluster zu aktivieren. Sie können das Startskript beim Erstellen des Clusters oder nach Abschluss des Vorgangs hinzufügen. Wählen Sie unter **Knotentyp** die Option **Regionsserver** aus, um sicherzustellen, dass das Skript nur auf HBase-Regionsservern ausgeführt wird.


    ```bash
    #! /bin/bash

    THIS_MACHINE=`hostname`

    if [[ $THIS_MACHINE != wn* ]]
    then
        printf 'Script to be executed only on worker nodes'
        exit 0
    fi

    RESULT=`pgrep -f RESTServer`
    if [[ -z $RESULT ]]
    then
        echo "Applying mitigation; starting REST Server"
        sudo python /usr/lib/python2.7/dist-packages/hdinsight_hbrest/HbaseRestAgent.py
    else
        echo "Rest server already running"
        exit 0
    fi
    ```

1. Legen Sie die Umgebungsvariable fest, um für Benutzerfreundlichkeit zu sorgen. Bearbeiten Sie die unten angegebenen Befehle, indem Sie `MYPASSWORD` durch das Kennwort für die Anmeldung am Cluster ersetzen. Ersetzen Sie `MYCLUSTERNAME` durch den Namen Ihres HBase-Clusters. Geben Sie anschließend die Befehle ein.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Verwenden Sie den folgenden Befehl, um die vorhandenen HBase-Tabellen aufzulisten:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Verwenden Sie den folgenden Befehl, um eine neue HBase-Tabelle mit zwei Spaltenfamilien zu erstellen:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Das Schema wird im JSON-Format bereitgestellt.
1. Fügen Sie mit dem folgenden Befehl Daten ein:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Codieren Sie die im -d-Switch angegebenen Werte mit Base64. Im Beispiel:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Persönlich: Name
   * Sm9obiBEb2xl: John Dole

     Mit [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) können Sie mehrere Werte (Batchwerte) einfügen.

1. Rufen Sie mit dem folgenden Befehl eine Zeile ab:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Weitere Informationen zu HBase-REST finden Sie im [Referenzleitfaden zu Apache HBase](https://hbase.apache.org/book.html#_rest).

> [!NOTE]  
> Thrift wird von HBase in HDInsight nicht unterstützt.
>
> Wenn Sie Curl oder eine andere REST-Kommunikation mit WebHCat verwenden, müssen Sie die Anforderungen authentifizieren, indem Sie den Benutzernamen und das Kennwort des Administrators des HDInsight-Clusters bereitstellen. Sie müssen auch den Clusternamen als Teil des URIs (Uniform Resource Identifier) verwenden, um die Anforderungen an den Server zu senden.
>
> `curl -u <UserName>:<Password> \`
>
> `-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status`
>
> Sie sollten eine Antwort empfangen, die in etwa der folgenden entspricht:
>
> `{"status":"ok","version":"v1"}`

## <a name="check-cluster-status"></a>Überprüfen des Clusterstatus

HBase in HDInsight wird mit einer Web-Benutzeroberfläche ausgeliefert, über die Cluster überwacht werden können. In dieser Web-Benutzeroberfläche können Sie Statistiken und Informationen zu Regionen anfordern.

**So greifen Sie auf die HBase Master-Benutzeroberfläche zu**

1. Melden Sie sich unter `https://CLUSTERNAME.azurehdinsight.net` bei der Benutzeroberfläche von Ambari Web an, wobei `CLUSTERNAME` der Name Ihres HBase-Clusters ist.

1. Wählen Sie im linken Menü **HBase** aus.

1. Klicken Sie am oberen Rand der Seite auf **Quicklinks** , zeigen Sie auf den aktiven Zookeeper-Knotenlink, und klicken Sie anschließend auf **HBase Master-Benutzeroberfläche** .  Die Benutzeroberfläche wird in einer anderen Browserregisterkarte geöffnet:

   ![Benutzeroberfläche von HDInsight Apache HBase HMaster](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   Die HBase Master-Benutzeroberfläche enthält folgende Abschnitte:

   - Regionsserver
   - Backup Master
   - Tabellen
   - Tasks
   - Softwareattribute

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es wird empfohlen, die HBase-Tabellen vor dem Löschen des Clusters zu deaktivieren, um Inkonsistenzen zu vermeiden. Sie können den HBase-Befehl `disable 'Contacts'` verwenden. Wenn Sie diese Anwendung nicht mehr benötigen, gehen Sie wie folgt vor, um den erstellten HBase-Cluster zu löschen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Geben Sie oben im **Suchfeld** den Suchbegriff **HDInsight** ein.
1. Wählen Sie unter **Dienste** die Option **HDInsight-Cluster** aus.
1. Klicken Sie in der daraufhin angezeigten Liste mit den HDInsight-Clustern neben dem Cluster, den Sie für dieses Tutorial erstellt haben, auf die Auslassungspunkte ( **...** ).
1. Klicken Sie auf **Löschen** . Klicken Sie auf **Ja** .

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie einen Apache HBase-Cluster erstellen. Sie haben auch gelernt, wie Sie mit der HBase-Shell Tabellen erstellen und die Daten in diesen Tabellen anzeigen. Darüber hinaus wissen Sie jetzt, wie Sie eine Hive-Abfrage mit Daten in HBase-Tabellen verwenden. Außerdem haben Sie gelernt, wie Sie die C#-REST-APIs für HBase verwenden, um eine HBase-Tabelle zu erstellen und Daten aus dieser Tabelle abzurufen. Weitere Informationen finden Sie unter:

> [!div class="nextstepaction"]
> [HDInsight HBase overview (Übersicht über HDInsight HBase)](./apache-hbase-overview.md)