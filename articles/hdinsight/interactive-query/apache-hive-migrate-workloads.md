---
title: Migrieren von Azure HDInsight 3.6-Hive-Workloads zu HDInsight 4.0
description: Erfahren Sie, wie Sie Apache Hive-Workloads in HDInsight 3.6 zu HDInsight 4.0 migrieren.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: bcc0faa8fdbd61ab3e3e0886256f7c796e5a98e2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011505"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrieren von Azure HDInsight 3.6-Hive-Workloads zu HDInsight 4.0

In diesem Dokument erfahren Sie, wie Sie Apache Hive- und LLAP-Workloads in HDInsight 3.6 zu HDInsight 4.0 migrieren. HDInsight 4.0 bietet neuere Hive- und LLAP-Features, beispielsweise materialisierte Sichten und die Zwischenspeicherung von Abfrageergebnissen. Wenn Sie Ihre Workloads zu HDInsight 4.0 migrieren, können Sie viele neuere Funktionen von Hive 3 nutzen, die nicht in HDInsight 3.6 verfügbar sind.

In diesem Artikel werden die folgenden Themen behandelt:

* Migration von Hive-Metadaten zu HDInsight 4.0
* Sichere Migration von ACID- und Nicht-ACID-Tabellen
* Beibehaltung von Hive-Sicherheitsrichtlinien in verschiedenen HDInsight-Versionen
* Abfrageausführung und Debuggen von HDInsight 3.6 in HDInsight 4.0

Ein Vorteil von Hive ist die Möglichkeit, Metadaten in eine externe Datenbank zu exportieren (bezeichnet als Hive-Metastore). Der **Hive-Metastore** ist für das Speichern von Tabellenstatistiken zuständig, einschließlich des Tabellenspeicherorts, der Spaltennamen und der Indexinformationen der Tabelle. HDInsight 3.6 und HDInsight 4.0 erfordern unterschiedliche Schemas für den Metastore. Die gemeinsame Nutzung eines einzelnen Metastores ist daher nicht möglich. Die empfohlene Vorgehensweise zum sicheren Aktualisieren des Hive-Metastores besteht darin, anstelle des Originals in der aktuellen Produktionsumgebung eine Kopie zu aktualisieren. Für dieses Dokument müssen der ursprüngliche und der neue Cluster Zugriff auf dasselbe Speicherkonto haben. Die Datenmigration zu einer anderen Region wird daher nicht behandelt.

## <a name="migrate-from-external-metastore"></a>Migrieren von einem externen Metastore

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. Ausführen von Hauptkomprimierung für ACID-Tabellen in HDInsight 3.6

ACID-Deltas werden für ACID-Tabellen in HDInsight 3.6 und HDInsight 4.0 unterschiedlich interpretiert. Die einzige erforderliche Aktion vor der Migration ist das Ausführen einer „MAJOR“-Komprimierung für jede ACID-Tabelle im 3.6-Cluster. Ausführliche Informationen zur Komprimierung finden Sie im [Handbuch zur Hive-Sprache](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact).

### <a name="2-copy-sql-database"></a>2. Kopieren der SQL-Datenbank
Erstellen Sie eine neue Kopie Ihres externen Metastores. Wenn Sie einen externen Metastore verwenden, besteht eine sichere und einfache Methode zum Erstellen einer Kopie des Metastores in der [Wiederherstellung der Datenbank](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore) mit einem anderen Namen unter Verwendung der `RESTORE`-Funktion.  Weitere Informationen zum Anfügen eines externen Metastores an einen HDInsight-Cluster finden Sie unter [Verwenden von externen Metadatenspeichern in Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

### <a name="3-upgrade-metastore-schema"></a>3. Aktualisieren des Metastoreschemas
Nachdem das **Kopieren** des Metastores abgeschlossen ist, führen Sie ein Schemaskript für die Aktualisierung unter [Skriptaktion](../hdinsight-hadoop-customize-cluster-linux.md) auf dem vorhandenen HDInsight 3.6-Cluster aus, um den neuen Metastore auf das Hive 3-Schema zu aktualisieren. (Für diesen Schritt muss der neue Metastore nicht mit einem Cluster verbunden sein.) Dadurch kann die Datenbank als HDInsight 4.0-Metastore angefügt werden.

Verwenden Sie die Werte in der Tabelle weiter unten. Ersetzen Sie `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` durch die entsprechenden Werte für die **Kopie** des Hive-Metastores, getrennt durch Leerzeichen. Lassen Sie bei der Angabe des SQL-Servernamens „.database.windows.net“ weg.

|Eigenschaft | Wert |
|---|---|
|Skripttyp|--Benutzerdefiniert|
|Name|Hive-Upgrade|
|Bash-Skript-URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Knotentyp(en)|Head|
|Parameter|SQLSERVERNAME DATABASENAME USERNAME PASSWORD|

> [!Warning]  
> Das Upgrade zum Konvertieren des HDInsight 3.6-Metadatenschemas in das HDInsight 4.0-Schema kann nicht rückgängig gemacht.

Sie können das Upgrade überprüfen, indem Sie die folgende SQL-Abfrage für die Datenbank ausführen:

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. Bereitstellen eines neuen HDInsight 4.0-Clusters

1. Geben Sie den aktualisierten Metastore als Hive-Metastore des neuen Clusters an.

1. Auf die eigentlichen Daten in den Tabellen kann jedoch erst zugegriffen werden, wenn der Cluster Zugriff auf die erforderlichen Speicherkonten hat.
Stellen Sie sicher, dass die Speicherkonten der Hive-Tabellen im HDInsight 3.6-Cluster als primäre oder sekundäre Speicherkonten des neuen HDInsight 4.0-Clusters angegeben sind.
Weitere Informationen zum Hinzufügen von Speicherkonten zu HDInsight-Clustern finden Sie unter [Hinzufügen zusätzlicher Speicherkonten zu HDInsight](../hdinsight-hadoop-add-storage.md).

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. Vervollständigen der Migration mit einem Tool nach dem Upgrade in HDInsight 4.0

Verwaltete Tabellen müssen in HDInsight 4.0 standardmäßig ACID-kompatibel sein. Führen Sie nach Abschluss der Metastoremigration ein Tool nach dem Upgrade aus, um zuvor nicht per ACID verwaltete Tabellen mit dem HDInsight 4.0-Cluster kompatibel zu machen. Dieses Tool führt die folgende Konvertierung aus:

|3.6 |4,0 |
|---|---|
|Externe Tabellen|Externe Tabellen|
|Nicht per ACID-verwaltete Tabellen|Externe Tabellen mit der Eigenschaft „external.table.purge=true“|
|ACID-verwaltete Tabellen|ACID-verwaltete Tabellen|

Führen Sie das Hive-Tool nach dem Upgrade im HDInsight 4.0-Cluster mithilfe der SSH-Shell aus:

1. Stellen Sie über SSH eine Verbindung mit Ihrem Clusterhauptknoten her. Anweisungen hierzu finden Sie unter [Herstellen einer Verbindung mit HDInsight per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
1. Öffnen Sie als Hive-Benutzer eine Anmelde-Shell, indem Sie `sudo su - hive` ausführen.
1. Führen Sie den folgenden Befehl über die Shell aus.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

Nach Abschluss der Ausführung des Tools ist Ihr Hive-Warehouse bereit für HDInsight 4.0.

## <a name="migrate-from-internal-metastore"></a>Migrieren von einem internen Metastore

Wenn Ihr HDInsight 3.6-Cluster einen internen Hive-Metastore verwendet, befolgen Sie die nachstehenden Schritte, um ein Skript auszuführen, das Hive-Abfragen zum Exportieren von Objektdefinitionen aus dem Metastore generiert.

In den HDInsight 3.6- und 4.0-Clustern muss dasselbe Speicherkonto verwendet werden.

> [!NOTE]
>
> * Im Fall von ACID-Tabellen wird unterhalb der Tabelle eine neue Kopie der Daten erstellt.
>
> * Dieses Skript unterstützt nur die Migration von Hive-Datenbanken, -Tabellen und -Partitionen. Andere Metadatenobjekte wie Sichten, benutzerdefinierte Funktionen (UDF) und Tabelleneinschränkungen müssen manuell kopiert werden.
>
> * Nach Abschluss dieses Skripts wird davon ausgegangen, dass der alte Cluster nicht mehr für den Zugriff auf Tabellen oder Datenbanken verwendet wird, auf die im Skript verwiesen wird.
>
> * Alle verwalteten Tabellen werden in HDInsight 4.0 transaktional. Optional können Sie die Tabelle nicht transaktional halten, indem Sie die Daten in eine externe Tabelle mit der Eigenschaft „external.table.purge=true“ exportieren. Beispiel:
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. Stellen Sie mit einem [Secure Shell-Client (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md) eine Verbindung mit dem HDInsight 3.6-Cluster her.

1. Laden Sie in der geöffneten SSH-Sitzung die folgende Skriptdatei herunter, um eine Datei mit dem Namen **alltables.hql** zu generieren.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * Führen Sie für einen regulären HDInsight-Cluster ohne ESP einfach `exporthive_hdi_3_6.sh` aus.

    * Verwenden Sie für einen Cluster mit ESP kinit, und ändern Sie die Argumente in Beeline: Führen Sie Folgendes aus, und definieren Sie USER und DOMAIN für Azure AD-Benutzer mit vollständigen Hive-Berechtigungen.

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. Beenden Sie Ihre SSH-Sitzung. Geben Sie dann einen SCP-Befehl ein, um **alltables.hql** lokal herunterzuladen.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Laden Sie **alltables.hql** in den *neuen* HDInsight-Cluster hoch.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Stellen Sie dann mithilfe von SSH eine Verbindung mit dem *neuen* HDInsight 4.0-Cluster her. Führen Sie in einer SSH-Sitzung in diesem Cluster den folgenden Befehl aus:

    Ohne ESP:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    Mit ESP:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

Das Tool nach dem Upgrade für die Migration mit externem Metastore kann hier nicht verwendet werden, da nicht per ACID verwaltete Tabellen aus HDInsight 3.6 in HDInsight 4.0 in ACID-verwaltete Tabellen konvertiert werden.

> [!Important]  
> Auf verwaltete Tabellen in HDInsight 4.0 (einschließlich Tabellen, die von 3.6 migriert wurden) sollten keine anderen Dienste oder Anwendungen zugreifen, auch keine HDInsight 3.6-Cluster.

## <a name="secure-hive-across-hdinsight-versions"></a>Sichern von Hive in verschiedenen HDInsight-Versionen

Seit HDInsight 3.6 ist HDInsight in Azure Active Directory und das HDInsight-Enterprise-Sicherheitspaket (ESP) integriert. Das Enterprise-Sicherheitspaket verwendet Kerberos und Apache Ranger, um die Berechtigungen bestimmter Ressourcen im Cluster zu verwalten. Für Hive in HDInsight 3.6 bereitgestellte Ranger-Richtlinien können wie folgt zu HDInsight 4.0 migriert werden:

1. Navigieren Sie in Ihrem HDInsight 3.6-Cluster zum Bereich „Ranger Service Manager“.
2. Navigieren Sie zur Richtlinie namens **HIVE**, und exportieren Sie die Richtlinie in eine JSON-Datei.
3. Stellen Sie sicher, dass alle Benutzer, auf die in der JSON-Datei mit der exportierten Richtlinie verwiesen wird, im neuen Cluster vorhanden sind. Wenn in der JSON-Datei mit der Richtlinie auf einen Benutzer verwiesen wird, der nicht im neuen Cluster vorhanden ist, fügen Sie den Benutzer dem neuen Cluster hinzu, oder entfernen Sie den Verweis aus der Richtlinie.
4. Navigieren Sie in Ihrem HDInsight 4.0-Cluster zum Bereich **Ranger Service Manager**.
5. Navigieren Sie zur Richtlinie namens **HIVE**, und importieren Sie die in Schritt 2 erstellte JSON-Datei mit der Ranger-Richtlinie.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Überprüfen der Kompatibilität und Ändern von Codes nach Bedarf in der Test-App

Wenn Sie Workloads wie z.B. vorhandene Programme und Abfragen migrieren, überprüfen Sie die Versionshinweise und die Dokumentation auf Änderungen, und übernehmen Sie die Änderungen bei Bedarf. Wenn Ihr HDInsight 3.6-Cluster einen freigegebenen Spark- und Hive-Metastore verwendet, ist eine [zusätzliche Konfiguration mit Hive Warehouse Connector](./apache-hive-warehouse-connector.md) erforderlich.

## <a name="deploy-new-app-for-production"></a>Bereitstellen der neuen App für die Produktion

Um zum neuen Cluster zu wechseln, können Sie z.B. eine neue Clientanwendung installieren und als neue Produktionsumgebung verwenden oder ein Upgrade Ihrer vorhandenen Clientanwendung durchführen und zu HDInsight 4.0 wechseln.

## <a name="switch-hdinsight-40-to-the-production"></a>Wechseln von HDInsight 4.0 zur Produktionsversion

Wenn beim Testen im Metastore Unterschiede aufgetreten sind, müssen Sie die Änderungen unmittelbar vor der Umstellung aktualisieren. In diesem Fall können Sie den Metastore exportieren und importieren und dann das Upgrade erneut ausführen.

## <a name="remove-the-old-production"></a>Entfernen der alten Produktionsversion

Nachdem Sie sich vergewissert haben, dass das Release vollständig und betriebsbereit ist, können Sie Version 3.6 und den vorherigen Metastore entfernen. Stellen Sie sicher, dass alles migriert wurde, bevor Sie die Umgebung löschen.

## <a name="query-execution-across-hdinsight-versions"></a>Abfrageausführung in verschiedenen HDInsight-Versionen

Zum Ausführen und Debuggen von Hive-/LLAP-Abfragen in einem HDInsight 3.6-Cluster stehen zwei Möglichkeiten zur Verfügung. HiveCLI bietet eine Befehlszeilenumgebung, und die [Tez-Ansicht/Hive-Ansicht](../hadoop/apache-hadoop-use-hive-ambari-view.md) stellt einen Workflow mit grafischer Benutzeroberfläche (Graphical User Interface, GUI) bereit.

In HDInsight 4.0 wurde HiveCLI durch Beeline ersetzt. Die Tez-Ansicht/Hive-Ansicht stellt einen Workflow mit grafischer Benutzeroberfläche bereit. HiveCLI ist ein Thrift-Client für Hive-Server 1, und Beeline ist ein JDBC-Client, der Zugriff auf Hive-Server 2 bietet. Beeline kann auch zum Herstellen einer Verbindung mit einem beliebigen anderen JDBC-kompatiblen Datenbankendpunkt verwendet werden. Beeline ist in HDInsight 4.0 standardmäßig verfügbar und erfordert keinerlei Installation.

## <a name="next-steps"></a>Nächste Schritte

* [Ankündigung von HDInsight 4.0](../hdinsight-version-release.md)
* [HDInsight 4.0 deep dive](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/) (Ausführliche Informationen zu HDInsight 4.0)
* [Hive 3-ACID-Tabellen](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)