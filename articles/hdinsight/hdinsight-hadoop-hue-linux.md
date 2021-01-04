---
title: Hue mit Hadoop in Linux-basierten HDInsight-Clustern – Azure
description: Erfahren Sie, wie Sie Hue auf HDInsight-Clustern installieren und die Anfragen mittels Tunneln an Hue weiterleiten. Verwenden Sie Hue, um Speicher zu durchsuchen und Hive oder Pig auszuführen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: e80df5d1c3d2b2195e76622964406cc65c933a63
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546193"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installieren und Verwenden von Hue in HDInsight Hadoop-Clustern

Erfahren Sie, wie Sie Hue auf HDInsight-Clustern installieren und die Anfragen mittels Tunneln an Hue weiterleiten.

## <a name="what-is-hue"></a>Was ist Hue?

Bei Hue handelt es sich um einen Satz von Webanwendungen zur Interaktion mit einem Apache Hadoop-Cluster. Mit Hue können Sie den mit einem Hadoop-Cluster verknüpften Speicher (WASB bei HDInsight-Clustern) durchsuchen, Hive-Aufträge und Pig-Skripts ausführen usw. Die folgenden Komponenten sind mit Hue-Installationen in einem HDInsight Hadoop-Cluster verfügbar.

* Beeswax Hive Editor
* Apache Pig
* Metastore Manager
* Apache Oozie
* FileBrowser (Kommunikation mit dem WASB-Standardcontainer)
* Job Browser

> [!WARNING]  
> Komponenten, die mit dem HDInsight-Cluster bereitgestellt werden, werden vollständig unterstützt, und Microsoft Support hilft Ihnen, Probleme im Zusammenhang mit diesen Komponenten zu isolieren und zu beheben.
>
> Für benutzerdefinierte Komponenten steht kommerziell angemessener Support für eine weiterführende Behebung des Problems zur Verfügung. Auf diese Weise kann das Problem behoben werden, ODER Sie werden aufgefordert, verfügbare Kanäle für Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse für diese Technologien verfügen. So können z. B. viele Communitywebsites verwendet werden, wie: [Frageseite von Microsoft Q&A (Fragen und Antworten) für HDInsight](/answers/topics/azure-hdinsight.html), [https://stackoverflow.com](https://stackoverflow.com). Für Apache-Projekte gibt es auch Projektwebsites auf [https://apache.org](https://apache.org). Beispiel: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Installation von Hue mithilfe von Skriptaktionen

Verwenden Sie die Informationen in der nachstehenden Tabelle für die Skriptaktion. Genauere Informationen zur Verwendung von Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

> [!NOTE]  
> Um Hue auf HDInsight-Clustern zu installieren, ist die empfohlene Hauptknotengröße mindestens A4 (8 Kerne, 14 GB Arbeitsspeicher).

|Eigenschaft |Wert |
|---|---|
|Skripttyp:|--Benutzerdefiniert|
|Name|Installieren von Hue|
|Bash-Skript-URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Knotentyp(en):|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Verwenden von Hue mit HDInsight-Clustern

Auf regulären Clustern können Sie nur ein einziges Benutzerkonto mit Hue haben. Aktivieren Sie für den Mehrbenutzerzugriff das [Enterprise-Sicherheitspaket](./domain-joined/hdinsight-security-overview.md) auf dem Cluster. SSH-Tunneling ist die einzige Möglichkeit für den Zugriff auf Hue auf dem Cluster, sobald es ausgeführt wird. Durch das Tunneln über SSH wird der Datenverkehr direkt an den Hauptknoten des Clusters geleitet, auf dem Hue ausgeführt wird. Führen Sie nach Abschluss der Clusterbereitstellung die folgenden Schritte zum Verwenden von Hue in einem HDInsight-Cluster aus.

> [!NOTE]  
> Es wird empfohlen, im Firefox-Webbrowser die nachstehenden Anleitungen zu befolgen.

1. Nutzen Sie die Informationen unter [Verwenden von SSH-Tunneling zum Zugriff auf die Apache Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md), um einen SSH-Tunnel von Ihrem Clientsystem an das HDInsight-Cluster zu erstellen und anschließend Ihren Webbrowser für die Verwendung des SSH-Tunnels als Proxy zu konfigurieren.

1. Verwenden Sie einen [ssh-Befehl](./hdinsight-hadoop-linux-use-ssh-unix.md) zum Herstellen der Verbindung mit dem Cluster. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verwenden Sie nach erfolgter Verbindungsherstellung den folgenden Befehl, um den vollqualifizierten Domänennamen des primären Hauptknotens abzurufen:

    ```bash
    hostname -f
    ```

    Die Ausgabe dieses Befehls sieht in etwa wie folgt aus:

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    Dies ist der Hostname des primären Hauptknotens, an dem sich die Hue-Website befindet.

1. Öffnen Sie im Browser das Hue-Portal unter `http://HOSTNAME:8888`. Ersetzen Sie „HOSTNAME“ durch den Namen, den Sie im vorherigen Schritt abgerufen haben.

   > [!NOTE]  
   > Wenn Sie sich zum ersten Mal anmelden, werden Sie aufgefordert, ein Konto für die Anmeldung beim Hue-Portal zu erstellen. Die hier angegebenen Anmeldeinformationen gelten nur für das Portal und beziehen sich nicht auf die Administrator- oder SSH-Benutzeranmeldeinformationen, die Sie beim Bereitstellen des Clusters angegeben haben.

    ![HDInsight – Hue-Portal – Anmeldefenster](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Angeben von Anmeldeinformationen für das Hue-Portal")

### <a name="run-a-hive-query"></a>Ausführen einer Hive-Abfrage

1. Wählen Sie im Hue-Portal **Query Editors** (Abfrage-Editoren) und dann **Hive** aus, um den Hive-Editor zu öffnen.

    ![HDInsight – Hue-Portal – Hive-Editor verwenden](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Verwenden von Hive")

2. Auf der Registerkarte **Assist** (Hilfe) sollte unter **Database** (Datenbank) der Eintrag **hivesampletable** angezeigt werden. Dies ist eine Beispieltabelle, die mit allen Hadoop-Clustern für HDInsight geliefert wird. Geben Sie eine Beispielabfrage im rechten Bereich ein. Die Ausgabe wird auf der Registerkarte **Results** (Ergebnisse) im unteren Bereich angezeigt (siehe Bildschirmaufnahme).

    ![HDInsight – Hue-Portal – Hive-Abfrage](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Ausführen einer Hive-Abfrage")

    Zudem können Sie über die Registerkarte **Chart** eine visuelle Darstellung des Ergebnisses anzeigen.

### <a name="browse-the-cluster-storage"></a>Durchsuchen des Clusterspeichers

1. Wählen Sie im Hue-Portal oben rechts auf der Menüleiste die Option **File Browser** (Dateibrowser) aus.
2. Standardmäßig wird der Dateibrowser im Verzeichnis **/user/myuser** geöffnet. Wählen Sie in dem Pfad den Schrägstrich direkt vor dem Benutzerverzeichnis aus, um zum Stammverzeichnis des Azure-Speichercontainers zu wechseln, der dem Cluster zugeordnet ist.

    ![HDInsight Portal – Hue-Portal – Dateibrowser](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Verwenden des Dateibrowsers")

3. Klicken Sie mit der rechten Maustaste auf eine Datei oder einen Ordner, um die verfügbaren Vorgänge anzuzeigen. Verwenden Sie die Schaltfläche **Upload** in der rechten Ecke, um Dateien in das aktuelle Verzeichnis hochzuladen. Über die Schaltfläche **New** können Sie neue Dateien oder Verzeichnisse erstellen.

> [!NOTE]  
> Im Hue-Dateibrowser können nur die Inhalte des Standardcontainers angezeigt werden, der dem HDInsight-Cluster zugeordnet ist. Auf alle anderen Speicherkonten und Container, die Sie eventuell mit dem Cluster verknüpft haben, können Sie mit dem Dateibrowser nicht zugreifen. Die zusätzlichen mit dem Cluster verknüpften Container sind jedoch für Hive-Aufträge immer verfügbar. Wenn Sie beispielsweise im Hive-Editor den Befehl `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` eingeben, werden auch die Inhalte der zusätzlichen Container angezeigt. In diesem Befehl ist **newcontainer** nicht der mit einem Cluster verknüpfte Standardcontainer.

## <a name="important-considerations"></a>Wichtige Hinweise

1. Das für die Hue-Installation verwendete Skript installiert Hue nur auf dem primären Hauptknoten des Clusters.

1. Während der Installation werden mehrere Hadoop-Dienste (HDFS, YARN, MR2, Oozie) zum Aktualisieren der Konfiguration neu gestartet. Nach Abschluss der Installation von Hue mit dem Skript kann es einige Zeit dauern, bis andere Hadoop-Dienste gestartet werden. Dies kann anfänglich die Leistung von Hue beeinträchtigen. Nachdem alle Dienste gestartet wurden, ist Hue voll funktionsfähig.

1. Hue kann keine Apache Tez-Aufträge verarbeiten, wobei es sich um die aktuelle Standardeinstellung für Hive handelt. Wenn Sie MapReduce als Ausführungs-Engine für Hive verwenden möchten, müssen Sie das Skript so aktualisieren, dass der folgende Befehl im Skript verwendet wird:

   `set hive.execution.engine=mr;`

1. Bei Linux-Clustern können Sie Ihre Dienste auf dem primären Hauptknoten und Resource Manager auf dem sekundären Knoten ausführen. Dieses Szenario kann zu Fehlern führen (siehe unten), wenn mithilfe von Hue Details zu ausgeführten Aufträgen im Cluster angezeigt werden sollen. Nach Abschluss des Auftrags können Sie die Auftragsdetails jedoch anzeigen.

   ![Hue-Portal – Fehler – Beispielnachricht](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Fehler im Hue-Portal")

   Dies ist auf ein bekanntes Problem zurückzuführen. Zur Umgehung dieses Problems können Sie Ambari so konfigurieren, dass die aktive Resource Manager-Instanz auch auf dem primären Hauptknoten ausgeführt wird.

1. Hue verarbeitet WebHDFS, während HDInsight-Cluster Azure Storage mit `wasbs://` verwenden. Daher installiert das mit der Skriptaktion verwendete benutzerdefinierte Skript WebWasb, einen WebHDFS-kompatiblen Dienst für die Kommunikation mit WASB. Auch wenn im Hue-Portal an bestimmten Stellen HDFS angegeben ist (etwa beim Bewegen des Mauszeigers über den **Dateibrowser** ), sollte dies als WASB interpretiert werden.

## <a name="next-steps"></a>Nächste Schritte

[Installieren und Verwenden von R in HDInsight-Clustern](./r-server/r-server-overview.md). Verwenden Sie die Clusteranpassung, um R in HDInsight Hadoop-Clustern zu installieren. R ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. Sie bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten.