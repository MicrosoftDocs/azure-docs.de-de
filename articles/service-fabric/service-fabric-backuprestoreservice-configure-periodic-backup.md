---
title: Grundlegendes zur Konfiguration der regelmäßigen Sicherung
description: Verwenden Sie das Service Fabric-Feature für regelmäßige Sicherungen und Wiederherstellungen, um regelmäßige Sicherungen Ihrer zuverlässigen zustandsbehafteten Dienste oder Reliable Actors zu konfigurieren.
ms.topic: article
ms.date: 2/01/2019
ms.openlocfilehash: 2607502af44b178131820d78f23bcdf4e32454a0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018884"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Grundlegendes zur Konfiguration der regelmäßigen Sicherung in Azure Service Fabric

Die Konfiguration der regelmäßigen Sicherung Ihrer zustandsbehafteten zuverlässigen Dienste oder Reliable Actors besteht aus den folgenden Schritten:

1. **Erstellen von Sicherungsrichtlinien**: In diesem Schritt werden je nach Anforderung eine oder mehrere Sicherungsrichtlinien erstellt.

2. **Aktivieren einer Sicherung**: In diesem Schritt ordnen Sie die in **Schritt 1** erstellten Sicherungsrichtlinien den erforderlichen Entitäten, der _Anwendung_, dem _Dienst_ oder einer _Partition_ zu.

## <a name="create-backup-policy"></a>Sicherungsrichtlinie erstellen

Eine Sicherungsrichtlinie besteht aus den folgenden Konfigurationen:

* **Automatische Wiederherstellung bei Datenverlust**: Gibt an, ob die Wiederherstellung automatisch mit der neuesten verfügbaren Sicherung ausgelöst werden soll, falls es auf der Partition zu einem Datenverlust kommt.
> [!NOTE]
> Es wird empfohlen, die automatische Wiederherstellung bei Produktionsclustern NICHT festzulegen.
>

* **Maximale inkrementelle Sicherungen**: Definiert die maximale Anzahl von inkrementellen Sicherungen, die zwischen zwei vollständigen Sicherungen durchgeführt werden sollen. Die maximale Anzahl von inkrementellen Sicherungen gibt die obere Grenze an. Eine vollständige Sicherung kann durchgeführt werden, bevor die angegebene Anzahl inkrementeller Sicherungen unter einer der folgenden Bedingungen abgeschlossen ist.

    1. Vom Replikat wurde nie eine vollständige Sicherung erstellt, seit es als primäres Replikat festgelegt wurde.

    2. Einige der Protokolldatensätze seit der letzten Sicherung wurden abgeschnitten.

    3. Das Replikat hat den Grenzwert „MaxAccumulatedBackupLogSizeInMB“ überschritten.

* **Sicherungszeitplan**: Die Zeit oder Häufigkeit, zu der regelmäßige Sicherungen erstellt werden sollen. Sicherungen lassen sich so planen, dass sie in einem bestimmten Intervall oder täglich bzw. wöchentlich zu einer bestimmten Zeit wiederholt werden.

    1. **Häufigkeitsbasierter Sicherungszeitplan**: Dieser Zeitplantyp sollte verwendet werden, wenn eine Datensicherung in festen Intervallen erforderlich ist. Das gewünschte Zeitintervall zwischen zwei aufeinanderfolgenden Sicherungen wird im ISO8601-Format definiert. Ein häufigkeitsbasierter Sicherungszeitplan kann ein Intervall minutengenau auflösen.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Zeitbasierter Sicherungszeitplan**: Dieser Zeitplantyp sollte verwendet werden, wenn eine Datensicherung zu einer bestimmten Zeit am Tag oder in der Woche erforderlich ist. Die Häufigkeit für diesen Zeitplantyp kann entweder täglich oder wöchentlich sein.
        1. **_Täglicher_ zeitbasierter Sicherungszeitplan**: Dieser Zeitplantyp sollte verwendet werden, wenn die Datensicherung zu bestimmten Tageszeiten erfolgen muss. Um diese festzulegen, setzen Sie `ScheduleFrequencyType` auf _Täglich_; und setzen Sie `RunTimes` im ISO8601-Format auf die Liste der gewünschten Uhrzeit während des Tages, wobei das mit der Uhrzeit angegebene Datum ignoriert wird. Zum Beispiel steht `0001-01-01T18:00:00` für täglich um _18:00 Uhr_, wobei der Datumsteil _0001-01-01_ ignoriert wird. Das folgende Beispiel veranschaulicht die Konfiguration zum Auslösen der täglichen Sicherung um _9:00 Uhr_ und _18:00 Uhr_.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **_Wöchentlicher_ zeitbasierter Sicherungszeitplan**: Dieser Zeitplantyp sollte verwendet werden, wenn die Datensicherung zu bestimmten Tageszeiten erfolgen muss. Um diese festzulegen, setzen Sie `ScheduleFrequencyType` auf _Wöchentlich_; setzen Sie `RunDays` auf die Liste der Tage in einer Woche, an denen die Sicherung ausgelöst werden soll, und setzen Sie `RunTimes` im ISO8601-Format auf die Liste der gewünschten Uhrzeit während des Tages, wobei das mit der Uhrzeit angegebene Datum ignoriert wird. Liste der Tage einer Woche, an denen die regelmäßige Sicherung ausgelöst werden soll. Das folgende Beispiel zeigt die Konfiguration zum Auslösen der täglichen Sicherung um _9:00 Uhr_ und _18:00 Uhr_ von montags bis freitags.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Sicherungsspeicher**: Gibt den Speicherort zum Hochladen von Sicherungen an. Als Speicher kann entweder ein Azure-Blobspeicher oder eine Dateifreigabe verwendet werden.
    1. **Azure-Blobspeicher**: Dieser Speichertyp sollte gewählt werden, wenn die generierten Sicherungen in Azure gespeichert werden müssen. Sowohl _eigenständige_ als auch _auf Azure basierende_ Cluster können diesen Speichertyp verwenden. Die Beschreibung für diesen Speichertyp erfordert die Verbindungszeichenfolge und den Namen des Containers, in den die Sicherungen hochgeladen werden sollen. Wenn der Container mit dem angegebenen Namen nicht verfügbar ist, wird er beim Hochladen einer Sicherung erstellt.

        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

        > [!NOTE]
        > Sicherungswiederherstellungsdienst funktioniert nicht mit Azure Storage v1
        >

    2. **Dateifreigabe**: Dieser Speichertyp sollte für _eigenständige_ Cluster ausgewählt werden, wenn die Datensicherung lokal gespeichert werden muss. Die Beschreibung für diesen Speichertyp erfordert einen Dateifreigabepfad, in den Sicherungen hochgeladen werden müssen. Über eine der folgenden Optionen kann der Zugriff auf die Dateifreigabe konfiguriert werden:
        1. _Integrierte Windows-Authentifizierung_, wobei der Zugriff auf die Dateifreigabe für alle Computer des Service Fabric-Clusters gewährt wird. Legen Sie in diesem Fall die folgenden Felder fest, um auf einer _Dateifreigabe_ basierenden Sicherungsspeicher zu konfigurieren.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Schützen der Dateifreigabe mit Benutzername und Kennwort_, wobei der Zugriff auf die Dateifreigabe bestimmten Benutzern gewährt wird. Die Speicherspezifikation der Dateifreigabe bietet auch die Möglichkeit, einen sekundären Benutzernamen und ein sekundäres Kennwort anzugeben. So werden die Anmeldeinformationen für den Fall bereitgestellt, dass bei der Authentifizierung mit dem primären Benutzernamen und dem primären Kennwort ein Fehler auftritt. Legen Sie in diesem Fall die folgenden Felder fest, um auf einer _Dateifreigabe_ basierenden Sicherungsspeicher zu konfigurieren.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Stellen Sie sicher, dass die Speicherzuverlässigkeit die Anforderungen an die Zuverlässigkeit von Sicherungsdaten erfüllt oder übertrifft.
>

* **Aufbewahrungsrichtlinie**: Gibt die Richtlinie zum Aufbewahren von Sicherungen im konfigurierten Speicher an. Es wird nur die Aufbewahrungsrichtlinie „Basic“ unterstützt.
    1. **Aufbewahrungsrichtlinie „Basic“** : Diese Aufbewahrungsrichtlinie ermöglicht die Sicherstellung einer optimalen Speicherauslastung, indem Sicherungsdateien entfernt werden, die nicht mehr benötigt werden. Über `RetentionDuration` kann die Zeitspanne festgelegt werden, für die Sicherungen im Speicher beibehalten werden müssen. `MinimumNumberOfBackups` ist ein optionaler Parameter, mit dem sichergestellt werden kann, dass unabhängig von `RetentionDuration` immer die angegebene Anzahl von Sicherungen beibehalten wird. Das nachstehende Beispiel zeigt eine Konfiguration, bei der Sicherungen für _10_ Tage beibehalten werden und die Anzahl von Sicherungen nicht unter _20_ fallen darf.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Aktivieren der regelmäßigen Sicherung
Nach der Definition der Sicherungsrichtlinie zur Erfüllung der Datensicherungsanforderungen sollte die Sicherungsrichtlinie entweder mit einer _Anwendung_, einem _Dienst_ oder einer _Partition_ verknüpft werden.

> [!NOTE]
> Vergewissern Sie sich vor dem Aktivieren der Sicherung, dass keine Anwendungsupgrades ausgeführt werden.
>

### <a name="hierarchical-propagation-of-backup-policy"></a>Hierarchische Weitergabe einer Sicherungsrichtlinie
In Service Fabric besteht eine hierarchische Beziehung zwischen Anwendung, Dienst und Partitionen, wie im [Anwendungsmodell](./service-fabric-application-model.md) erläutert. Sicherungsrichtlinien können entweder mit einer _Anwendung_, einem _Dienst_ oder einer _Partition_ in der Hierarchie verknüpft werden. Sicherungsrichtlinien werden hierarchisch an die nächste Ebene weitergegeben. Angenommen, es gibt nur eine Sicherungsrichtlinie, die mit einer _Anwendung_ verknüpft ist, dann werden alle zustandsbehafteten Partitionen, die zu sämtlichen _zustandsbehafteten zuverlässigen Dienste_ und _Reliable Actors_ der _Anwendung_ gehören, gemäß der Sicherungsrichtlinie gesichert. Oder, wenn die Sicherungsrichtlinie mit einem _zustandsbehafteten zuverlässigen Dienst_ verknüpft ist, werden alle zugehörigen Partitionen mit der Sicherungsrichtlinie gesichert.

### <a name="overriding-backup-policy"></a>Überschreiben einer Sicherungsrichtlinie
Es kann ein Szenario geben, in dem für alle Dienste der Anwendung eine Datensicherung mit demselben Sicherungszeitplan benötigt wird, außer für bestimmte Dienste, bei denen eine häufigere Datensicherung oder eine Sicherung auf einem anderen Speicherkonto oder einer anderen Dateifreigabe erforderlich ist. Für derartige Szenarien bietet der Dienst für Sicherungswiederherstellungen die Möglichkeit, weitergegebene Richtlinien im Dienst- und Partitionsumfang zu überschreiben. Wenn die Sicherungsrichtlinie mit einem _Dienst_ oder einer _Partition_ verknüpft ist, überschreibt sie die weitergegebene Sicherungsrichtlinie, falls vorhanden.

### <a name="example"></a>Beispiel

In diesem Beispiel wird ein Setup mit zwei Anwendungen verwendet, _MyApp_A_ und _MyApp_B_. Anwendung _MyApp_A_ umfasst zwei zustandsbehaftete zuverlässige Dienste, _SvcA1_ & _SvcA3_, und einen Reliable Actor-Dienst, _ActorA2_. _SvcA1_ enthält drei Partitionen, während _ActorA2_ und _SvcA3_ jeweils zwei Partitionen enthalten.  Anwendung _MyApp_B_ umfasst drei zustandsbehaftete zuverlässige Dienste, _SvcB1_, _SvcB2_ und _SvcB3_. _SvcB1_ und _SvcB2_ enthalten jeweils zwei Partitionen, während _SvcB3_ drei Partitionen enthält.

Angenommen es bestehen die folgenden Datensicherungsanforderungen dieser Anwendungen:

1. MyApp_A
    1. Täglich muss eine Datensicherung für alle Partitionen aller _zustandsbehafteter zuverlässiger Dienste_ und _Reliable Actors_ erstellt werden, die zur Anwendung gehören. Die Sicherungsdaten müssen an den Speicherort _BackupStore1_ hochgeladen werden.

    2. Für einen der Dienste, _SvcA3_, ist eine stündliche Datensicherung erforderlich.

    3. Die Datengröße in der Partition _SvcA1_P2_ ist größer als erwartet, und die Sicherungsdaten sollten an einem anderen Speicherort, _BackupStore2_, gespeichert werden.

2. MyApp_B
    1. Jeden Sonntag um 8:00 Uhr muss eine Datensicherung für alle Partitionen des _SvcB1_-Diensts erstellt werden. Die Sicherungsdaten müssen an den Speicherort _BackupStore1_ hochgeladen werden.

    2. Täglich um 8:00 Uhr muss eine Datensicherung für die Partition _SvcB2_P1_ erstellt werden. Die Sicherungsdaten müssen an den Speicherort _BackupStore1_ hochgeladen werden.

Um diese Datensicherungsanforderungen zu erfüllen, werden die Sicherungsrichtlinien „BP_1“ bis „BP_5“ erstellt und die Sicherungen wie folgt aktiviert.
1. MyApp_A
    1. Erstellen Sie die Sicherungsrichtlinie _BP_1_ mit einem häufigkeitsbasierten Sicherungszeitplan, für den die Häufigkeit auf „alle 24 Stunden“ gesetzt wird, und konfigurieren Sie den Sicherungsspeicher so, dass dieser den Speicherort _BackupStore1_ verwendet. Aktivieren Sie diese Richtlinie mithilfe der API [Anwendungssicherung aktivieren](/rest/api/servicefabric/sfclient-api-enableapplicationbackup) für Anwendung _MyApp_A_. Dadurch können Datensicherungen mit der Sicherungsrichtlinie _BP_1_ für alle Partitionen der _zustandsbehafteten zuverlässigen Dienste_ und _Reliable Actors_ erstellt werden, die zur Anwendung _MyApp_A_ gehören.

    2. Erstellen Sie die Sicherungsrichtlinie _BP_2_ mit einem häufigkeitsbasierten Sicherungszeitplan, für den die Häufigkeit auf „jede Stunde“ gesetzt wird, und konfigurieren Sie den Sicherungsspeicher so, dass dieser den Speicherort _BackupStore1_ verwendet. Aktivieren Sie die Richtlinie mit der API [Dienstsicherung aktivieren](/rest/api/servicefabric/sfclient-api-enableservicebackup) für den Dienst _SvcA3_. Dadurch wird die weitergegebene Richtlinie _BP_1_ durch die explizit aktivierte Sicherungsrichtlinie _BP_2_ für alle Partitionen des Diensts _SvcA3_ überschrieben. Infolgedessen werden für diese Partitionen Datensicherungen mit der Sicherungsrichtlinie _BP_2_ durchgeführt.

    3. Erstellen Sie die Sicherungsrichtlinie _BP_3_ mit einem häufigkeitsbasierten Sicherungszeitplan, für den die Häufigkeit auf „alle 24 Stunden“ gesetzt wird, und konfigurieren Sie den Sicherungsspeicher so, dass dieser den Speicherort _BackupStore2_ verwendet. Aktivieren Sie die Richtlinie mit der API [Partitionssicherung aktivieren](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) für die Partition _SvcA1_P2_. Dadurch wird die weitergegebene Richtlinie _BP_1_ durch die explizit aktivierte Sicherungsrichtlinie _BP_3_ für die Partition _SvcA1_P2_ überschrieben.

2. MyApp_B
    1. Erstellen Sie die Sicherungsrichtlinie _BP_4_ mit einem zeitbasierten Sicherungszeitplan, für den die Häufigkeit auf „Wöchentlich“, der Ausführungstag auf „Sonntag“ und die Ausführungszeit auf „8:00 Uhr“ gesetzt wird. Konfigurieren Sie den Sicherungsspeicher so, dass dieser den Speicherort _BackupStore1_ verwendet. Aktivieren Sie die Richtlinie mit der API [Dienstsicherung aktivieren](/rest/api/servicefabric/sfclient-api-enableservicebackup) für den Dienst _SvcB1_. Dadurch können Datensicherungen mit der Sicherungsrichtlinie _BP_4_ für alle Partitionen des Diensts _SvcB1_ erstellt werden.

    2. Erstellen Sie die Sicherungsrichtlinie _BP_5_ mit einem zeitbasierten Sicherungszeitplan, für den die Häufigkeit auf „Täglich“ und die Ausführungszeit auf „8:00 Uhr“ gesetzt wird. Konfigurieren Sie den Sicherungsspeicher so, dass dieser den Speicherort _BackupStore1_ verwendet. Aktivieren Sie die Richtlinie mit der API [Partitionssicherung aktivieren](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) für die Partition _SvcB2_P1_. Dadurch können Datensicherungen mit der Sicherungsrichtlinie _BP_5_ für die Partition _SvcB2_P1_ erstellt werden.

Das folgende Diagramm zeigt explizit aktivierte Sicherungsrichtlinien und weitergegebene Sicherungsrichtlinien.

![Service Fabric-Anwendungshierarchie][0]

## <a name="disable-backup"></a>Deaktivieren einer Sicherung
Sicherungsrichtlinien können deaktiviert werden, wenn keine Notwendigkeit besteht, Daten zu sichern. Die für eine _Anwendung_ aktivierte Sicherungsrichtlinie kann nur für dieselbe _Anwendung_ mit der API [Anwendungssicherung deaktivieren](/rest/api/servicefabric/sfclient-api-disableapplicationbackup) deaktiviert werden. Die für einen _Dienst_ aktivierte Sicherungsrichtlinie kann für denselben _Dienst_ mit der API [Dienstsicherung deaktivieren](/rest/api/servicefabric/sfclient-api-disableservicebackup) deaktiviert werden. Und schließlich kann die für eine _Partition_ aktivierte Sicherungsrichtlinie nur für dieselbe _Partition_ mit der API [Partitionssicherung deaktivieren](/rest/api/servicefabric/sfclient-api-disablepartitionbackup) deaktiviert werden.

* Durch das Deaktivieren der Sicherungsrichtlinie für eine _Anwendung_ werden alle regelmäßigen Datensicherungen beendet, die aufgrund der Weitergabe der Sicherungsrichtlinie an zustandsbehaftete zuverlässige Dienstpartitionen oder Reliable Actor-Partitionen durchgeführt werden.

* Durch das Deaktivieren der Sicherungsrichtlinie für einen _Dienst_ werden alle regelmäßigen Datensicherungen beendet, die aufgrund der Weitergabe der Sicherungsrichtlinie an die Partitionen des _Diensts_ durchgeführt werden.

* Durch das Deaktivieren der Sicherungsrichtlinie für eine _Partition_ werden alle regelmäßigen Datensicherungen beendet, die aufgrund der Sicherungsrichtlinie auf Partitionsebene durchgeführt werden.

* Wenn die Sicherung für eine Entität (Anwendung/Dienst/Partition) deaktiviert wird, kann `CleanBackup` auf _true_ festgelegt werden, um alle Sicherungen im konfigurierten Speicher zu löschen.
    ```json
    {
        "CleanBackup": true 
    }
    ```
> [!NOTE]
> Vergewissern Sie sich vor dem Deaktivieren der Sicherung, dass keine Anwendungsupgrades ausgeführt werden.
>

## <a name="suspend--resume-backup"></a>Anhalten und Fortsetzen einer Sicherung
Bestimmte Situationen können ein vorübergehendes Anhalten der regelmäßigen Datensicherung erfordern. In einer solchen Situation kann, je nach Anforderung, die API zum Anhalten von Sicherungen für eine _Anwendung_, einen _Dienst_ oder eine _Partition_ verwendet werden. Das Aussetzen der regelmäßigen Sicherung ist ab dem Zeitpunkt der Anwendung über die Teilstruktur der Anwendungshierarchie transitiv. 

* Wenn zum Anhalten für eine _Anwendung_ die API [Anwendungssicherung anhalten](/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) verwendet wird, werden die regelmäßigen Datensicherungen für alle Dienste und Partitionen unter dieser Anwendung ausgesetzt.

* Wenn zum Anhalten für einen _Diensts_ die API [Dienstsicherung anhalten](/rest/api/servicefabric/sfclient-api-suspendservicebackup) verwendet wird, werden die regelmäßigen Datensicherungen für alle Partitionen unter diesem Dienst ausgesetzt.

* Wenn zum Anhalten für eine _Partition_ die API [Partitionssicherung anhalten](/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) verwendet wird, werden die regelmäßigen Datensicherungen für die Partitionen unter diesem Dienst ausgesetzt.

Sobald das Anhalten nicht mehr erforderlich ist, kann die regelmäßige Datensicherung über die entsprechende API zum Fortsetzen der Sicherung wiederhergestellt werden. Die regelmäßige Sicherung muss bei derselben _Anwendung_, demselben _Dienst_ bzw. derselben _Partition_ fortgesetzt werden, für die bzw. den sie angehalten wurde.

* Verwenden Sie die API [Anwendungssicherung fortsetzen](/rest/api/servicefabric/sfclient-api-resumeapplicationbackup), um die regelmäßige Sicherung für eine _Anwendung_ wieder fortzusetzen, nachdem diese angehalten wurde. 

* Verwenden Sie die API [Dienstsicherung fortsetzen](/rest/api/servicefabric/sfclient-api-resumeservicebackup), um die regelmäßige Sicherung für einen _Dienst_ wieder fortzusetzen, nachdem diese angehalten wurde.

* Verwenden Sie die API [Partitionssicherung fortsetzen](/rest/api/servicefabric/sfclient-api-resumepartitionbackup), um die regelmäßige Sicherung für eine _Partition_ wieder fortzusetzen, nachdem diese angehalten wurde.

### <a name="difference-between-suspend-and-disable-backups"></a>Unterschied zwischen dem Anhalten und dem Deaktivieren von Sicherungen
Sicherungen sollten deaktiviert werden, wenn Sicherungen für eine bestimmte Anwendung, einen bestimmten Dienst oder eine bestimmte Partition nicht mehr erforderlich sind. Eine Anforderung zum Deaktivieren der Sicherung kann aufgerufen werden, wenn der Parameter zum Bereinigen von Sicherungen TRUE ist. Das bedeutet, dass alle vorhandenen Sicherungen ebenfalls gelöscht werden. Das Anhalten von Sicherungen wird hingegen in Szenarien eingesetzt, in denen Sicherungen vorübergehend deaktiviert werden sollen, wenn beispielsweise der lokale Datenträger voll ist oder beim Hochladen einer Sicherung aufgrund eines bekannten Netzwerkproblems ein Fehler auftritt usw. 

Während die Deaktivierung nur auf einer Ebene aufgerufen werden kann, die zuvor explizit für die Sicherung aktiviert wurde, kann das Anhalten der Sicherung auf jeder beliebigen Ebene angewendet werden, die zurzeit für die Sicherung aktiviert ist – entweder direkt oder durch Vererbung/Hierarchie. Wenn die Sicherung zum Beispiel auf Anwendungsebene aktiviert ist, kann die Deaktivierung nur auf Anwendungsebene aufgerufen werden. Das Anhalten der Sicherung kann jedoch für die Anwendung sowie für jeden Dienst oder jede Partition unter dieser Anwendung aufgerufen werden. 

## <a name="auto-restore-on-data-loss"></a>Automatische Wiederherstellung bei Datenverlust
Durch unerwartete Ausfälle können auf der Dienstpartition befindliche Daten verloren gehen. Beispielsweise wird die Festplatte für zwei von drei Replikaten einer Partition (einschließlich des primären Replikats) beschädigt oder gelöscht.

Wenn Service Fabric einen Datenverlust auf der Partition erkennt, wird die Schnittstellenmethode `OnDataLossAsync` für die Partition aufgerufen und es wird erwartet, dass für die Partition die erforderliche Aktion zum Beheben des Datenverlusts ausgeführt wird. Wenn in dieser Situation die effektive Sicherungsrichtlinie für die Partition das `AutoRestoreOnDataLoss`-Flag auf `true` gesetzt hat, wird die Wiederherstellung automatisch unter Verwendung der letzten verfügbaren Sicherung für diese Partition ausgelöst.

> [!NOTE]
> Es wird empfohlen, die automatische Wiederherstellung bei Produktionsclustern NICHT festzulegen.
>

## <a name="get-backup-configuration"></a>Abrufen der Sicherungskonfiguration
Separate APIs werden zur Verfügung gestellt, um Informationen zur Sicherungskonfiguration für eine _Anwendung_, einen _Dienst_ oder eine _Partition_ abzurufen. Zu diesen APIs gehören [Informationen zur Sicherungskonfiguration für eine Anwendung abrufen](/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [Informationen zur Sicherungskonfiguration für einen Dienst abrufen](/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo) und [Informationen zur Sicherungskonfiguration für eine Partition abrufen](/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo). Diese APIs geben in erster Linie die geltende Sicherungsrichtlinie, den Anwendungsumfang der Sicherungsrichtlinie und die Details zum Anhalten von Sicherungen zurück. Im Folgenden finden Sie eine kurze Beschreibung der zurückgegebenen Ergebnisse dieser APIs.

- Informationen zur Sicherungskonfiguration für eine Anwendung: Enthält die Details der für die Anwendung geltende Sicherungsrichtlinie und alle überschriebenen Richtlinien für Dienste und Partitionen, die zu der Anwendung gehören. Dazu zählen auch Informationen über das Anhalten regelmäßiger Sicherungen für die Anwendung und deren Dienste sowie für Partitionen.

- Informationen zur Sicherungskonfiguration für einen Dienst: Enthält die Details der für den Dienst geltenden Sicherungsrichtlinie und den Umfang, in dem diese Richtlinie angewendet wurde, sowie alle überschriebenen Richtlinien für die zugehörigen Partitionen. Dazu zählen auch Informationen über das Anhalten regelmäßiger Sicherungen für den Dienst und dessen Partitionen.

- Informationen zur Sicherungskonfiguration für eine Partition: Enthält die Details der für die Partition geltenden Sicherungsrichtlinie und den Umfang, in dem diese Richtlinie angewendet wurde. Dazu zählen auch Informationen über das Anhalten regelmäßiger Sicherungen für die Partitionen.

## <a name="list-available-backups"></a>Liste der verfügbaren Sicherungen

Mithilfe der API zum Abrufen einer Liste von Sicherungen können Sie verfügbare Sicherungen auflisten. Das Ergebnis des API-Aufrufs enthält Informationen zu allen Sicherungen, die auf dem Sicherungsspeicher verfügbar sind, der in der entsprechenden Sicherungsrichtlinie konfiguriert ist. Verschiedene Varianten dieser API stehen zur Verfügung, um verfügbare Sicherungen einer Anwendung, eines Dienstes oder einer Partition aufzulisten. Mit diesen APIs lassen sich die _letzten_ verfügbaren Sicherungen aller entsprechenden Partitionen abrufen oder Sicherungen anhand von _Startdatum_ und _Enddatum_ filtern.

Zudem unterstützen diese APIs das Paginieren der Ergebnisse. Wenn der Parameter _MaxResults_ auf eine positive ganze Zahl ungleich Null gesetzt ist, gibt die API maximal die durch _MaxResults_ festgelegte Anzahl an Elementen mit Sicherungsinformationen zurück. Sollte die Anzahl der Elemente mit Sicherungsinformationen den Wert _MaxResults_ übersteigen, wird ein Fortsetzungstoken zurückgegeben. Zulässige Parameter für Fortsetzungstoken können dazu verwendet werden, den nächsten Satz von Ergebnissen abzurufen. Wenn ein zulässiger Wert des Fortsetzungstokens an den nächsten Aufruf der API übergeben wird, gibt die API den nächsten Satz von Ergebnissen zurück. Werden alle verfügbaren Ergebnisse zurückgegeben, ist kein Fortsetzungstoken in der Antwort enthalten.

Im Folgenden finden Sie kurze Informationen zu den unterstützten Varianten.

- [Liste der Anwendungssicherungen abrufen](/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): Gibt eine Liste der verfügbaren Sicherungen für jede Partition zurück, die zu einer bestimmten Service Fabric-Anwendung gehört.

- [Liste der Dienstsicherungen abrufen](/rest/api/servicefabric/sfclient-api-getservicebackuplist): Gibt eine Liste der verfügbaren Sicherungen für jede Partition zurück, die zu einem bestimmten Service Fabric-Dienst gehört.
 
- [Liste der Partitionssicherungen abrufen](/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): Gibt eine Liste der verfügbaren Sicherungen für die angegebene Partition zurück.

## <a name="next-steps"></a>Nächste Schritte
- [REST-API-Referenz zu Sicherung/Wiederherstellung](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
