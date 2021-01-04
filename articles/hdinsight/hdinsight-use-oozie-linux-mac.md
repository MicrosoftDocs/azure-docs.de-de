---
title: Verwenden von Hadoop Oozie-Workflows in Linux-basiertem Azure HDInsight
description: Verwenden von Hadoop Oozie in Linux-basiertem HDInsight Erfahren Sie, wie Sie einen Oozie-Workflow definieren und einen Oozie-Auftrag übermitteln können.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 7b0d3ac4775ca057856c28ab42197bb734f149d6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534939"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Verwenden von Apache Oozie mit Apache Hadoop zum Definieren und Ausführen eines Workflows in Linux-basiertem Azure HDInsight

Hier erfahren Sie, wie Sie Apache Oozie mit Apache Hadoop in Azure HDInsight verwenden. Oozie ist ein Workflow- und Koordinationssystem zur Verwaltung von Hadoop-Aufträgen. Oozie ist in den Hadoop-Stack integriert und unterstützt die folgenden Aufträge:

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Sie können Oozie auch dazu verwenden, bestimmte Aufträge für ein System zu planen, beispielsweise Java-Programme oder Shellskripts.

> [!NOTE]  
> Eine weitere Option zum Definieren von Workflows mit HDInsight ist die Verwendung von Azure Data Factory. Weitere Informationen zu Data Factory finden Sie unter [Verwenden von Apache Pig und Apache Hive mit Data Factory](../data-factory/transform-data.md). Informationen zur Verwendung von Oozie in Clustern mit dem Enterprise-Sicherheitspaket finden Sie unter [Ausführen von Apache Oozie in in die Domäne eingebundenen HDInsight Hadoop-Clustern](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).

## <a name="prerequisites"></a>Voraussetzungen

* **Einen Hadoop-Cluster in HDInsight** . Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **SSH-Client** . Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Apache Hadoop) per SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Eine Azure SQL-Datenbank** .  Weitere Informationen finden Sie unter [Erstellen einer Datenbank in Azure SQL-Datenbank im Azure-Portal](../azure-sql/database/single-database-create-quickstart.md).  Der Artikel verwendet eine Datenbank namens **oozietest** .

* Das URI-Schema für Ihren primären Clusterspeicher. `wasb://` für Azure Storage, `abfs://` für Azure Data Lake Storage Gen2 oder `adl://` für Azure Data Lake Storage Gen1. Wenn die sichere Übertragung für Azure Storage aktiviert ist, lautet der URI `wasbs://`. Siehe auch [Vorschreiben einer sicheren Übertragung in Azure Storage](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Beispielworkflow

Der in diesem Dokument verwendeten Workflows weist zwei Aktionen auf. Aktionen sind Definitionen von Aufgaben, z.B. das Ausführen von Hive, Sqoop, MapReduce oder anderen Prozessen:

![HDInsight – Oozie-Workflowdiagramm](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Eine Hive-Aktion führt ein HiveQL-Skript zum Extrahieren von Datensätzen aus der in HDInsight enthaltenen Tabelle `hivesampletable` aus. Jede Datenzeile beschreibt einen Besuch eines bestimmten Mobilgeräts. Das Format des Eintrags sieht ähnlich wie der folgende Text aus:

    ```output
    8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
    23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
    23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1
    ```

    Das in diesem Dokument verwendete Hive-Skript zählt die gesamten Besuche jeder Plattform (z.B. Android oder iPhone) und speichert die Werte in einer neuen Hive-Tabelle.

    Weitere Informationen zu Hive finden Sie unter „[Verwenden von Apache Hive mit HDInsight][hdinsight-use-hive]“.

2. Die Sqoop-Aktion exportiert den Inhalt der neuen Hive-Tabelle in eine Tabelle, die in Azure SQL-Datenbank erstellt wurde. Weitere Informationen zu Sqoop finden Sie unter [Verwenden von Sqoop mit Hadoop in HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md).

> [!NOTE]  
> Informationen zu den unterstützten Oozie-Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen](hdinsight-component-versioning.md).

## <a name="create-the-working-directory"></a>Erstellen des Arbeitsverzeichnisses

Oozie erwartet, dass die für einen Auftrag erforderlichen Ressourcen im selben Verzeichnis gespeichert werden. In diesem Beispiel wird `wasbs:///tutorials/useoozie` verwendet. Erstellen Sie das Verzeichnis, und führen Sie die folgenden Schritte aus:

1. Bearbeiten Sie den Code unten so, dass `sshuser` durch den SSH-Benutzername für den Cluster ersetzt wird, und ersetzen Sie `CLUSTERNAME` durch den Namen des Clusters.  Geben Sie dann den Code ein, um [mithilfe von SSH](hdinsight-hadoop-linux-use-ssh-unix.md) eine Verbindung mit dem HDInsight-Cluster herzustellen.  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Verwenden Sie zum Erstellen des Verzeichnisses den folgenden Befehl:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > Der Parameter `-p` bewirkt, dass alle Verzeichnisse im Pfad erstellt werden. Das Verzeichnis `data` dient zum Speichern der Daten, die vom Skript `useooziewf.hql` verwendet werden.

3. Bearbeiten Sie den Code unten so, dass `sshuser` durch Ihren SSH-Benutzername ersetzt wird.  Führen Sie den folgenden Befehl aus, damit Oozie die Identität Ihres Benutzerkontos annehmen kann:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > Sie können Fehler ignorieren, die besagen, dass der Benutzer bereits Mitglied der Gruppe `users` ist.

## <a name="add-a-database-driver"></a>Hinzufügen eines Datenbanktreibers

Dieser Workflow verwendet Sqoop zum Exportieren von Daten in die SQL-Datenbank. Deshalb müssen Sie eine Kopie des JDBC-Treibers bereitstellen, der für die Interaktion mit der SQL-Datenbank verwendet wird. Führen Sie den folgenden Befehl in der SSH-Sitzung aus, um den JDBC-Treiber in das Arbeitsverzeichnis zu kopieren:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Überprüfen Sie den tatsächlichen JDBC-Treiber, der unter dem Pfad `/usr/share/java/` verfügbar ist.

Wenn Ihr Workflow andere Ressourcen wie z.B. eine JAR-Datei mit einer MapReduce-Anwendung verwendet hat, müssen Sie diese Ressourcen ebenfalls hinzufügen.

## <a name="define-the-hive-query"></a>Definieren der Hive-Abfrage

Verwenden Sie die folgenden Schritte, um ein Skript der Hive-Abfragesprache (HiveQL) zu erstellen, das eine Abfrage definiert. Sie verwenden die Abfrage in einem Oozie-Workflow weiter unten in diesem Dokument.

1. Führen Sie über die SSH-Verbindung den folgenden Befehl aus, um eine Datei mit dem Namen `useooziewf.hql` zu erstellen:

    ```bash
    nano useooziewf.hql
    ```

1. Nachdem der GNU-Nano-Editor geöffnet wurde, verwenden Sie die folgende Abfrage als Inhalt der Datei:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Im Skript werden zwei Variablen verwendet:

   * `${hiveTableName}`: Enthält den Namen der zu erstellenden Tabelle.

   * `${hiveDataFolder}`: Enthält den Speicherort der Datendateien für die Tabelle.

     Die Workflowdefinitionsdatei („workflow.xml“ in diesem Artikel) übergibt diese Werte zur Laufzeit an das HiveQL-Skript.

1. Um die Datei zu speichern, drücken Sie **STRG+X** , geben Sie **J** ein, und drücken Sie auf die **EINGABETASTE** .  

1. Führen Sie den folgenden Befehl aus, um `useooziewf.hql` nach `wasbs:///tutorials/useoozie/useooziewf.hql` zu kopieren:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Mit diesem Befehl wird die Datei `useooziewf.hql` in dem mit Hadoop Distributed File System kompatiblen Speicher für den Cluster gespeichert.

## <a name="define-the-workflow"></a>Definieren des Workflows

Oozie-Workflowdefinitionen sind in der Sprache der Hadoop-Prozessdefinition (hPDL) geschrieben. Dabei handelt es sich um eine Sprache der XML-Prozessdefinition. Führen Sie zum Definieren des Workflows die folgenden Schritte aus:

1. Verwenden Sie die folgende Anweisung zum Erstellen und Bearbeiten einer neuen Datei:

    ```bash
    nano workflow.xml
    ```

2. Nachdem der Nano-Editor geöffnet wurde, verwenden Sie den folgenden XML-Code als Inhalt der Datei:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    Im Workflow sind zwei Aktionen definiert:

   * `RunHiveScript`: Diese Aktion ist die Startaktion und führt das Hive-Skript `useooziewf.hql` aus.

   * `RunSqoopExport`: Diese Aktion exportiert die vom Hive-Skript erstellten Daten mithilfe von Sqoop in die SQL-Datenbank. Diese Aktion wird nur ausgeführt, wenn die Aktion `RunHiveScript` erfolgreich war.

     Der Workflow weist mehrere Einträge auf, z.B. `${jobTracker}`. Sie ersetzen diese Einträge durch die Werte, die Sie in der Auftragsdefinition verwenden. Sie erstellen die Auftragsdefinition weiter unten in diesem Dokument.

     Beachten Sie auch den Eintrag `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` im Abschnitt „Sqoop“. Dieser Eintrag weist Oozie an, dieses Archiv für Sqoop zur Verfügung zu stellen, wenn diese Aktion ausgeführt wird.

3. Um die Datei zu speichern, drücken Sie **STRG+X** , geben Sie **J** ein, und drücken Sie auf die **EINGABETASTE** .  

4. Kopieren Sie mit folgendem Befehl die Datei `workflow.xml` nach `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Erstellen einer Tabelle

> [!NOTE]  
> Es gibt viele Möglichkeiten, zum Erstellen einer Tabelle eine Verbindung mit SQL Database herzustellen. Die folgenden Schritte verwenden [FreeTDS](https://www.freetds.org/) aus dem HDInsight-Cluster.

1. Verwenden Sie den folgenden Befehl, um FreeTDS im HDInsight-Cluster zu installieren:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Bearbeiten Sie den Code unten so, dass `<serverName>` durch den Namen Ihres [logischen SQL-Servers](../azure-sql/database/logical-servers.md) ersetzt wird und `<sqlLogin>` durch die Serveranmeldung.  Geben Sie den Befehl ein, um eine Verbindung mit der vorausgesetzten SQL-Datenbank herzustellen.  Geben Sie in der Eingabeaufforderung das Kennwort ein.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Sie erhalten dann eine Ausgabe wie etwa den folgenden Text:

    ```output
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to oozietest
    1>
    ```

3. Geben Sie bei der Eingabeaufforderung `1>` folgende Zeilen ein:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Nach Eingabe der Anweisung `GO` werden die vorherigen Anweisungen ausgewertet. Mithilfe dieser Anweisungen wird eine Tabelle mit dem Namen `mobiledata` erstellt, die vom Workflow verwendet wird.

    Um sicherzustellen, dass die Tabelle erstellt wurde, verwenden Sie die folgenden Befehle:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Ihnen sollte eine Ausgabe wie der folgende Text angezeigt werden:

    ```output
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    oozietest       dbo             mobiledata      BASE TABLE
    ```

4. Geben Sie zum Beenden des Dienstprogramms „tsql“ in die Eingabeaufforderung `1>` den Befehl `exit` ein.

## <a name="create-the-job-definition"></a>Erstellen der Auftragsdefinition

Die Auftragsdefinition beschreibt, wo sich die workflow.xml-Datei befindet. Sie beschreibt außerdem, wo sich andere vom Workflow verwendete Dateien (wie etwa `useooziewf.hql`) befinden. Sie definiert auch die Werte für Eigenschaften, die innerhalb des Workflows verwendet werden, und die zugeordneten Dateien.

1. Geben Sie den folgenden Befehl ein, um die vollständige Adresse des Standardspeichers abzurufen. Diese Adresse wird in der Konfigurationsdatei verwendet, die Sie im nächsten Schritt erstellen.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Die in der Rückgabe dieses Befehls enthaltenen Informationen sind ähnlich dem folgenden XML-Code:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Falls der HDInsight-Cluster Azure Storage als Standardspeicher verwendet, beginnt der Inhalt des Elements `<value>` mit `wasbs://`. Wenn stattdessen Azure Data Lake Storage Gen1 verwendet wird, beginnt er mit `adl://`. Wenn Azure Data Lake Storage Gen2 verwendet wird, beginnt er mit `abfs://`.

    Speichern Sie den Inhalt des Elements `<value>`. Dieser wird im nächsten Schritt verwendet.

2. Bearbeiten Sie das xml-Dokument unten folgendermaßen:

    |Platzhalterwert| Ersetzter Wert|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Wert aus Schritt 1|
    |admin| Ihr Anmeldename für den HDInsight-Cluster, falls Sie kein Administrator sind.|
    |serverName| Name des Azure SQL-Datenbank-Servers|
    |sqlLogin| Anmeldung des Azure SQL-Datenbank-Servers|
    |sqlPassword| Anmeldekennwort des Azure SQL-Datenbank-Servers|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    Die meisten der Informationen in dieser Datei dienen zum Auffüllen der Werte, die in der Datei „workflow.xml“ oder „ooziewf.hql“ (z.B. `${nameNode}`) verwendet werden.  Wenn der Pfad ein Pfad des Typs `wasbs` ist, müssen Sie den vollständigen Pfad verwenden. Verkürzen Sie ihn nicht bloß auf `wasbs:///`. Der Eintrag `oozie.wf.application.path` definiert, wo die Datei „workflow.xml“ zu finden ist. Diese Datei enthält den Workflow, der von diesem Auftrag ausgeführt wurde.

3. Verwenden Sie zum Erstellen der Konfiguration der Oozie-Auftragsdefinition den folgenden Befehl:

    ```bash
    nano job.xml
    ```

4. Nachdem der Nano-Editor geöffnet wurde, fügen Sie den bearbeiteten XML-Code als Inhalt der Datei ein.

5. Um die Datei zu speichern, drücken Sie **STRG+X** , geben Sie **J** ein, und drücken Sie auf die **EINGABETASTE** .

## <a name="submit-and-manage-the-job"></a>Übermitteln und Verwalten des Auftrags

Die folgenden Schritte verwenden den Oozie-Befehl zum Übermitteln und Verwalten von Oozie-Workflows im Cluster. Der Oozie-Befehl ist eine benutzerfreundliche Schnittstelle, die über die [Oozie-REST-API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)zur Verfügung steht.

> [!IMPORTANT]  
> Wenn Sie den Oozie-Befehl verwenden, müssen Sie den vollqualifizierten Domänennamen (FQDN) des HDInsight-Hauptknotens verwenden. Auf diesen FQDN kann nur innerhalb des Clusters zugegriffen werden. Wenn der Cluster sich in einem virtuellen Azure-Netzwerk befindet, ist der Zugriff von anderen Computern im selben Netzwerk möglich.

1. Geben Sie zum Abrufen der URL des Oozie-Diensts den folgenden Befehl an:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Die zurückgegebenen Informationen ähneln dem folgenden XML-Code:

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    Der Teil `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie` ist die mit dem Oozie-Befehl zu verwendende URL.

2. Bearbeiten Sie den Code so, dass die URL durch diejenige ersetzt wird, die Sie zuvor erhalten haben. Geben Sie Folgendes an, um eine Umgebungsvariable für die URL erstellen, damit Sie sie nicht für jeden Befehl eingeben müssen:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Geben Sie zum Übermitteln des Auftrags Folgendes an:

    ```bash
    oozie job -config job.xml -submit
    ```

    Mit diesem Befehl werden die Auftragsinformationen aus der Datei `job.xml` geladen und an Oozie übermittelt, ohne dass die Datei ausgeführt wird.

    Nach Abschluss des Befehls sollte die ID des Auftrags zurückgegeben werden, z.B. `0000005-150622124850154-oozie-oozi-W`. Diese ID wird verwendet, um den Auftrag zu verwalten.

4. Bearbeiten Sie den Code unten so, dass `<JOBID>` durch die ID ersetzt wird, die im vorherigen Schritt zurückgegeben wurde.  Verwenden Sie den folgenden Befehl, um den Status des Auftrags zu prüfen:

    ```bash
    oozie job -info <JOBID>
    ```

    Die Ausgabe sieht in etwa wie der folgende Text aus:

    ```output
    Job ID : 0000005-150622124850154-oozie-oozi-W
    ------------------------------------------------------------------------------------------------------------------------------------
    Workflow Name : useooziewf
    App Path      : wasb:///tutorials/useoozie
    Status        : PREP
    Run           : 0
    User          : USERNAME
    Group         : -
    Created       : 2015-06-22 15:06 GMT
    Started       : -
    Last Modified : 2015-06-22 15:06 GMT
    Ended         : -
    CoordAction ID: -
    ------------------------------------------------------------------------------------------------------------------------------------
    ```

    Dieser Auftrag hat den Status `PREP`. Dieser Status gibt an, dass der Auftrag erstellt, aber nicht gestartet wurde.

5. Bearbeiten Sie den Code unten so, dass `<JOBID>` durch die ID ersetzt wird, die zuvor zurückgegeben wurde.  Verwenden Sie den folgenden Befehl zum Starten des Auftrags:

    ```bash
    oozie job -start <JOBID>
    ```

    Wenn Sie nach diesem Befehl den Status überprüfen, lautet dieser „Wird ausgeführt“, und Informationen für die Aktionen innerhalb des Auftrags werden zurückgegeben.  Die Ausführung des Auftrags nimmt einige Minuten in Anspruch.

6. Bearbeiten Sie den Code unten so, dass `<serverName>` durch Ihren Servernamen ersetzt wird und `<sqlLogin>` durch die Serveranmeldung.  *Sobald die Aufgabe erfolgreich abgeschlossen wurde* , können Sie mit dem folgenden Befehl überprüfen, ob die Daten generiert wurden und ob die SQL-Datenbanktabelle exportiert wurde.  Geben Sie in der Eingabeaufforderung das Kennwort ein.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Geben Sie bei der Eingabeaufforderung `1>` folgende Abfrage ein:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Die zurückgegebenen Informationen ähneln dem folgenden Text:

    ```output
    deviceplatform  count
    Android 31591
    iPhone OS       22731
    proprietary development 3
    RIM OS  3464
    Unknown 213
    Windows Phone   1791
    (6 rows affected)
    ```

Weitere Informationen zum Oozie-Befehl finden Sie unter [Apache Oozie-Befehlszeilentool](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie-REST-API

Mit der Oozie-REST-API können Sie eigene Tools erstellen, die mit Oozie arbeiten. Die folgenden Informationen zur Verwendung der Oozie-REST-API sind HDInsight-spezifisch:

* **URI:** Unter `https://CLUSTERNAME.azurehdinsight.net/oozie` können Sie von außerhalb des Clusters auf die REST-API zugreifen.

* **Authentifizierung:** Verwenden Sie die API mit dem HTTP-Clusteradministratorkonto und -kennwort, um sich zu authentifizieren. Beispiel:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Weitere Informationen zur Verwendung der Oozie-REST-API finden Sie unter [Apache Oozie-Webdienste-API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie-Webbenutzeroberfläche

Die Oozie-Webbenutzeroberfläche bietet eine webbasierte Anzeige des Status von Oozie-Aufträgen im Cluster. In der Web-UI können Sie die folgenden Informationen anzeigen:

   * Auftragsstatus
   * Auftragsdefinition
   * Konfiguration
   * Ein Diagramm der im Auftrag enthaltenen Aktionen
   * Protokolle für den Auftrag

Sie können auch die Informationen zu den Aktionen innerhalb eines Auftrags anzeigen.

Um auf die Oozie-Webbenutzeroberfläche zuzugreifen, gehen Sie folgendermaßen vor:

1. Erstellen Sie einen SSH-Tunnel zum HDInsight-Cluster. Weitere Informationen finden Sie unter [Verwenden von SSH-Tunneling mit HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Nachdem Sie einen Tunnel erstellt haben, öffnen Sie mithilfe des URI `http://headnodehost:8080` die Ambari-Webbenutzeroberfläche in Ihrem Webbrowser.

3. Klicken Sie auf der linken Seite der Seite auf **Oozie** > **QuickLinks** > **Oozie Web UI** .

    ![Apache Ambari – Schritte in der Oozie-Webbenutzeroberfläche](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Die Oozie-Webbenutzeroberfläche zeigt standardmäßig aktive Workflowaufträge an. Klicken Sie zum Anzeigen aller Workflowaufträge auf **Alle Aufträge** .

    ![Workflowaufträge in der Oozie-Webkonsole](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Um weitere Informationen über einen Auftrag anzuzeigen, klicken Sie auf den Auftrag.

    ![HDInsight – Apache Oozie-Auftragsinformationen](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. Auf der Registerkarte **Auftragsinformationen** können Sie die grundlegenden Auftragsinformationen und die einzelnen Aktionen innerhalb des Auftrags anzeigen. Auf den Registerkarten am oberen Rand können Sie die **Auftragsdefinition** und **Auftragskonfiguration** anzeigen, auf das **Auftragsprotokoll** zugreifen oder einen gerichteten azyklischen Graph (Directed Acyclic Graph, DAG) unter **DAG des Auftrags** anzeigen.

   * **Auftragsprotokoll:** Wählen Sie die Schaltfläche **Get Logs** (Protokolle abrufen) aus, um alle Protokolle für den Auftrag abzurufen, oder filtern Sie die Protokolle mithilfe des Felds **Suchfilter eingeben** .

       ![HDInsight – Apache Oozie-Auftragsprotokoll](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Auftrags-DAG:** Der DAG ist eine grafische Übersicht über die Datenpfade, die im Workflow gewählt wurden.

       ![„HDInsight – Apache Oozie-Auftrags-DAG“](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Wenn Sie eine der Aktionen auf der Registerkarte **Auftragsinformationen** auswählen, werden Informationen zur Aktion eingeblendet. Wählen Sie z.B. die Aktion **RunSqoopExport** aus.

    ![HDInsight-Oozie-Auftrag – Aktionsinfo](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Sie können Details für die Aktion anzeigen, z. B. einen Link zur **Konsolen-URL** . Verwenden Sie diesen Link zum Anzeigen von JobTracker-Informationen für den Auftrag.

## <a name="schedule-jobs"></a>Planen von Aufträgen

Sie können den Koordinator verwenden, um den Start, das Ende und die Häufigkeit von Aufträgen anzugeben. Um einen Zeitplan für den Workflow zu definieren, führen Sie die folgenden Schritte aus:

1. Verwenden Sie den folgenden Befehl, um eine Datei namens **coordinator.xml** zu erstellen:

    ```bash
    nano coordinator.xml
    ```

    Verwenden Sie den folgenden XML-Code als Inhalt der Datei:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > Die `${...}`-Variablen werden zur Laufzeit durch Werte in der Auftragsdefinition ersetzt. Die Variablen heißen wie folgt:
    >
    > * `${coordFrequency}`: Die Zeit zwischen der Ausführung von Instanzen des Auftrags.
    > * `${coordStart}`: Die Startzeit des Auftrags.
    > * `${coordEnd}`: Die Endzeit des Auftrags.
    > * `${coordTimezone}`: Für Koordinatoraufträge wird eine feste Zeitzone ohne Sommerzeit verwendet (in der Regel in UTC). Diese Zeitzone wird als *Oozie-Verarbeitungszeitzone* bezeichnet.
    > * `${wfPath}`: Der Pfad der Datei „workflow.xml“.

2. Um die Datei zu speichern, drücken Sie **STRG+X** , geben Sie **J** ein, und drücken Sie auf die **EINGABETASTE** .

3. Verwenden Sie den folgenden Befehl, um die Datei in das Arbeitsverzeichnis dieses Auftrags zu kopieren:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Verwenden Sie zum Bearbeiten der zuvor erstellten `job.xml`-Datei den folgenden Befehl:

    ```bash
    nano job.xml
    ```

    Nehmen Sie die folgenden Änderungen vor:

   * Um Oozie anzuweisen, die Koordinatordatei statt des Workflows auszuführen, ändern Sie `<name>oozie.wf.application.path</name>` in `<name>oozie.coord.application.path</name>`.

   * Fügen Sie zum Festlegen der Variablen `workflowPath`, die vom Koordinator verwendet wird, den folgenden XML-Code hinzu:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Ersetzen Sie den Text `wasbs://mycontainer@mystorageaccount.blob.core.windows` durch den Wert, der in weiteren Einträgen in der Datei „job.xml“ verwendet wird.

   * Fügen Sie den folgenden XML-Code hinzu, um Start, Ende und Häufigkeit für den Koordinator festzulegen:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Diese Werte legen die Startzeit auf 12:00 Uhr am 10. Mai 2018 und die Endzeit auf den 12. Mai 2018 fest. Das Intervall für die Auftragsausführung ist auf „täglich“ festgelegt. Die Häufigkeit wird in Minuten angegeben. Daher gilt 24 Stunden x 60 Minuten = 1.440 Minuten. Schließlich wird die Zeitzone auf UTC festgelegt.

5. Um die Datei zu speichern, drücken Sie **STRG+X** , geben Sie **J** ein, und drücken Sie auf die **EINGABETASTE** .

6. Verwenden Sie den folgenden Befehl zum Übermitteln und Starten des Auftrags:

    ```bash
    oozie job -config job.xml -run
    ```

7. Wenn Sie zur Oozie-Webbenutzeroberfläche wechseln und die Registerkarte **Koordinatoraufträge** auswählen, werden Informationen ähnlich wie in der folgenden Abbildung angezeigt:

    ![Registerkarte für Koordinatoraufträge in der Oozie-Webkonsole](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    Der Eintrag **Next Materialization** (Nächste Materialisierung) enthält die nächste Ausführungszeit des Auftrags.

8. Wie beim vorherigen Workflowauftrag werden beim Auswählen des Auftragseintrags auf der Webbenutzeroberfläche Informationen zum Auftrag angezeigt:

    ![Apache Oozie-Koordinator – Auftragsinformationen](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > In dieser Abbildung werden nur erfolgreiche Ausführungen des Auftrags und nicht einzelne Aktionen innerhalb des geplanten Workflows angezeigt. Um die einzelnen Aktionen anzuzeigen, wählen Sie eine der **Aktionseinträge** aus.

    ![Oozie-Webkonsole – Registerkarte mit Auftragsinformationen](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie ein Oozie-Workflow definiert und Oozie-Auftrag ausgeführt wird. Weitere Informationen zum Arbeiten mit HDInsight finden Sie in den folgenden Artikeln:

* [Hochladen von Daten für Hadoop-Aufträge in HDInsight](hdinsight-upload-data.md)
* [Verwenden von Apache Sqoop mit Apache Hadoop in HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md)
* [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hadoop/hdinsight-use-hive.md)
* [Problembehandlung für Apache Oozie](./troubleshoot-oozie.md)