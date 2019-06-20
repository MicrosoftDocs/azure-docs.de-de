---
title: Azure Monitor-Protokolldatenmodell für Azure Backup
description: In diesem Artikel werden die Details des Azure Monitor-Protokolldatenmodells für Azure Backup-Daten vorgestellt.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: dd4dad2cc3e541d3b6866c02341161dc1d9e1e6c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61234920"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics-Datenmodell für Azure Backup-Daten

Verwenden Sie das Log Analytics-Datenmodell, um benutzerdefinierte Warnungen in Log Analytics zu erstellen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Verwenden des Azure Backup-Datenmodells

Sie können die folgenden im Rahmen des Datenmodells bereitgestellten Felder verwenden, um visuelle Elemente, benutzerdefinierte Abfragen und ein Dashboard gemäß Ihren Anforderungen zu erstellen.

### <a name="alert"></a>Warnung

Diese Tabelle enthält Details zu warnungsbezogenen Feldern.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| AlertUniqueId_s |Text |Eindeutiger Bezeichner der generierten Warnung |
| AlertType_s |Text |Typ der Warnung, z.B. Sicherung |
| AlertStatus_s |Text |Status der Warnung, z.B. „Aktiv“ |
| AlertOccurrenceDateTime_s |Date/Time |Datum und Uhrzeit der Warnungserstellung |
| AlertSeverity_s |Text |Schweregrad der Warnung, z.B. „Kritisch“ |
|AlertTimeToResolveInMinutes_s    | Number        |Zeit, die benötigt wurde, um eine Warnung zu klären. Leer für aktive Warnungen.         |
|AlertConsolidationStatus_s   |Text         |Erkennen, ob die Warnung eine konsolidierte Warnung ist oder nicht         |
|CountOfAlertsConsolidated_s     |Number         |Anzahl konsolidierter Warnungen, wenn es sich um eine konsolidierten Warnung handelt          |
|AlertRaisedOn_s     |Text         |Entitätstyp, aufgrund dessen die Warnung ausgelöst wurde         |
|AlertCode_s     |Text         |Code, um einen Warnungstyp eindeutig zu bestimmen         |
|RecommendedAction_s   |Text         |Empfohlene Aktion, um eine Warnung zu klären         |
| EventName_s |Text |Name der Veranstaltung. Immer „AzureBackupCentralReport“ |
| BackupItemUniqueId_s |Text |Eindeutiger Bezeichner des Sicherungselements, das der Warnung zugeordnet ist |
| SchemaVersion_s |Text |Aktuelle Version des Schemas, z. B. **V2** |
| State_s |Text |Aktueller Status des Warnungsobjekts, z.B. „Aktiv“, „Gelöscht“ |
| BackupManagementType_s |Text |Anbietertyp für die Sicherung, zu der diese Warnung gehört, z.B. IaaSVM, FileFolder |
| OperationName |Text |Name des aktuellen Vorgangs, z.B. Warnung |
| Category (Kategorie) |Text |Kategorie der Diagnosedaten, die mithilfe von Push in Azure Monitor-Protokolle übertragen werden Immer „AzureBackupReport“ |
| Resource |Text |Die Ressource, für die Daten erfasst werden; zeigt den Recovery Services-Tresornamen an |
| ProtectedServerUniqueId_s |Text |Eindeutiger Bezeichner des geschützten Servers, der der Warnung zugeordnet ist |
| VaultUniqueId_s |Text |Eindeutiger Bezeichner des geschützten Tresors, der der Warnung zugeordnet ist |
| SourceSystem |Text |Quellsystem der aktuellen Daten: Azure |
| resourceId |Text |Eindeutiger Bezeichner der Ressource, zu der Daten gesammelt werden. Beispiel: eine Ressourcen-ID des Recovery Services-Tresors |
| SubscriptionId |Text |Abonnementbezeichner der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceGroup |Text |Ressourcengruppe der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceProvider |Text |Ressourcenanbieter, für den Daten gesammelt werden. Beispiel: Microsoft.RecoveryServices |
| ResourceType |Text |Ressourcentyp, für den Daten gesammelt werden. Beispiel: Tresore |

### <a name="backupitem"></a>BackupItem

Diese Tabelle enthält Details zu Feldern in Bezug auf Sicherungselemente.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| EventName_s |Text |Name der Veranstaltung. Immer „AzureBackupCentralReport“ |  
| BackupItemUniqueId_s |Text |Eindeutiger Bezeichner des Sicherungselements |
| BackupItemId_s |Text |Bezeichner des Sicherungselements |
| BackupItemName_s |Text |Name des Sicherungselements |
| BackupItemFriendlyName_s |Text |Anzeigename des Sicherungselements |
| BackupItemType_s |Text |Typ des Sicherungselements, z.B. VM, FileFolder |
| ProtectionState_s |Text |Aktueller Schutzstatus des Sicherungselements, z.B. „Geschützt“, „Schutz beendet“ |
| ProtectionGroupName_s |Text | Name der Schutzgruppe, in der das Sicherungselement geschützt wird, (falls zutreffend) für System Center Data Protection Manager (SC DPM) und Microsoft Azure Backup Server (MABS)|
| SecondaryBackupProtectionState_s |Text |Gibt an, ob der sekundäre Schutz für das Sicherungselement aktiviert ist|
| SchemaVersion_s |Text |Version des Schemas, z. B. **V2** |
| State_s |Text |Status des Sicherungselementobjekts, z.B. „Aktiv“ oder „Gelöscht“ |
| BackupManagementType_s |Text |Anbietertyp für die Sicherung, zu der dieses Sicherungselement gehört, z.B. IaaSVM, FileFolder |
| OperationName |Text |Name des Vorgangs, z.B. „BackupItem“ |
| Category (Kategorie) |Text |Kategorie der Diagnosedaten, die mithilfe von Push in Azure Monitor-Protokolle übertragen werden Immer „AzureBackupReport“ |
| Resource |Text |Ressource, für die Daten erfasst werden; z.B. der Name des Recovery Services-Tresors |
| SourceSystem |Text |Quellsystem der aktuellen Daten: Azure |
| resourceId |Text |Ressourcen-ID, für die Daten erfasst werden; z.B. die Ressourcen-ID des Recovery Services-Tresors |
| SubscriptionId |Text |Abonnementbezeichner der Ressource (z.B. Recovery Services-Tresor) für Daten, die erfasst werden |
| ResourceGroup |Text |Ressourcengruppe der Ressource (z.B. Recovery Services-Tresor) für Daten, die erfasst werden |
| ResourceProvider |Text |Ressourcenanbieter für Daten, die erfasst werden; z.B. „Microsoft.RecoveryServices“ |
| ResourceType |Text |Typ der Ressource für Daten, die erfasst werden; z.B. Tresore |

### <a name="backupitemassociation"></a>BackupItemAssociation

Diese Tabelle enthält Details zur Zuordnung von Sicherungselementen zu verschiedenen Entitäten.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| EventName_s |Text |Dieses Feld stellt den Namen des Ereignisses dar, es lautet immer „AzureBackupCentralReport“ |  
| BackupItemUniqueId_s |Text |Eindeutige ID des Sicherungselements |
| SchemaVersion_s |Text |Dieses Feld gibt die aktuelle Version des Schemas an: **V2** |
| State_s |Text |Aktueller Status des Zuordnungsobjekts für das Sicherungselement, z.B. „Aktiv“, „Gelöscht“ |
| BackupManagementType_s |Text |Anbietertyp für den Server zur Durchführung des Sicherungsjobs, z.B. IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Front-End-Größe des Sicherungselements |
| BackupManagementServerUniqueId_s |Text | Feld, um den Server für die Sicherungsverwaltung, durch den das Sicherungselement geschützt wird, eindeutig zu bestimmen (wenn zutreffend) |
| Category (Kategorie) |Text |Dieses Feld repräsentiert die Kategorie der Diagnosedaten, die an Log Analytics übermittelt werden: AzureBackupReport |
| OperationName |Text |Dieses Feld repräsentiert den Namen des aktuellen Vorgangs: BackupItemAssociation |
| Resource |Text |Die Ressource, für die Daten erfasst werden; zeigt den Recovery Services-Tresornamen an |
| PolicyUniqueId_g |Text |Eindeutiger Bezeichner der Richtlinie, die dem Sicherungselement zugeordnet ist |
| ProtectedServerUniqueId_s |Text |Eindeutiger Bezeichner des geschützten Servers, der dem Sicherungselement zugeordnet ist |
| VaultUniqueId_s |Text |Eindeutiger Bezeichner des Tresors, der das Sicherungselement enthält |
| SourceSystem |Text |Quellsystem der aktuellen Daten: Azure |
| resourceId |Text |Ressourcenbezeichner der Daten, die erfasst werden. Beispiel: Ressourcen-ID des Recovery Services-Tresors |
| SubscriptionId |Text |Abonnementbezeichner der Ressource (z.B. Recovery Services-Tresor), für die Daten gesammelt werden |
| ResourceGroup |Text |Ressourcengruppe der Ressource (z.B. Recovery Services-Tresor), für die Daten gesammelt werden |
| ResourceProvider |Text |Ressourcenanbieter für Daten, die erfasst werden; z.B. „Microsoft.RecoveryServices“ |
| ResourceType |Text |Typ der Ressource für Daten, die erfasst werden; z.B. Tresore |

### <a name="backupmanagementserver"></a>BackupManagementServer

Diese Tabelle enthält Details zur Zuordnung von Sicherungselementen zu verschiedenen Entitäten.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Name des Servers für die Sicherungsverwaltung        |
|AzureBackupAgentVersion_s     |Text         |Version des Azure Backup-Agenten auf dem Sicherungsverwaltungsserver          |
|BackupManagementServerVersion_s     |Text         |Version des Servers für die Sicherungsverwaltung|
|BackupManagementServerOSVersion_s    |Text            |Betriebssystemversion des Servers für die Sicherungsverwaltung|
|BackupManagementServerType_s     |Text         |Typ des Sicherungsverwaltungsserver, z. B. MABS oder SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Feld, um den Sicherungsverwaltungsserver eindeutig zu bestimmen       |
| SourceSystem |Text |Quellsystem der aktuellen Daten: Azure |
| resourceId |Text |Ressourcenbezeichner der Daten, die erfasst werden. Beispiel: Ressourcen-ID des Recovery Services-Tresors |
| SubscriptionId |Text |Abonnementbezeichner der Ressource (z.B. Recovery Services-Tresor), für die Daten gesammelt werden |
| ResourceGroup |Text |Ressourcengruppe der Ressource (z.B. Recovery Services-Tresor), für die Daten gesammelt werden |
| ResourceProvider |Text |Ressourcenanbieter für Daten, die erfasst werden; z.B. „Microsoft.RecoveryServices“ |
| ResourceType |Text |Typ der Ressource für Daten, die erfasst werden; z.B. Tresore |

### <a name="job"></a>Auftrag

Diese Tabelle enthält Details zu auftragsbezogenen Feldern.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| EventName_s |Text |Name der Veranstaltung. Immer „AzureBackupCentralReport“ |
| BackupItemUniqueId_s |Text |Eindeutiger Bezeichner des Sicherungselements |
| SchemaVersion_s |Text |Version des Schemas, z. B. **V2** |
| State_s |Text |Aktueller Status des Auftragsobjekts, z.B. „Aktiv“, „Gelöscht“ |
| BackupManagementType_s |Text |Anbietertyp für den Server zur Durchführung des Sicherungsjobs, z.B. IaaSVM, FileFolder |
| OperationName |Text |Dieses Feld repräsentiert den Namen des aktuellen Vorgangs: Auftrag |
| Category (Kategorie) |Text |Dieses Feld repräsentiert die Kategorie der Diagnosedaten, die an Azure Monitor-Protokolle übermittelt werden: AzureBackupReport |
| Resource |Text |Die Ressource, für die Daten erfasst werden; zeigt den Recovery Services-Tresornamen an |
| ProtectedServerUniqueId_s |Text |Eindeutiger Bezeichner des geschützten Servers, der dem Job zugeordnet ist |
| ProtectedContainerUniqueId_s |Text | Eindeutige ID, um den geschützten Container zu bestimmen, in dem der Auftrag ausgeführt wird |
| VaultUniqueId_s |Text |Eindeutiger Bezeichner des geschützten Tresors |
| JobOperation_s |Text |Vorgang, für den Auftrag ausgeführt wird, z.B. Sicherung, Wiederherstellung, Sicherungskonfiguration |
| JobStatus_s |Text |Status des beendeten Auftrags, z.B. „Abgeschlossen“, „Fehler“ |
| JobFailureCode_s |Text |Zeichenfolge mit dem Fehlercode zum Angeben des Grunds des Auftragsfehlers |
| JobStartDateTime_s |Date/Time |Datum und Uhrzeit des Starts des Auftrags |
| BackupStorageDestination_s |Text |Ziel des Sicherungsspeichers, z.B. Cloud, Datenträger  |
| AdHocOrScheduledJob_s |Text | Feld, um anzugeben, ob der Auftrag „Ad-hoc“ oder „Geplant“ ist |
| JobDurationInSecs_s | Number |Gesamtdauer des Auftrags in Sekunden |
| DataTransferredInMB_s | Number |Für diesen Auftrag übertragene Daten in MB|
| JobUniqueId_g |Text |Eindeutige ID zur Bezeichnung des Auftrags |
| RecoveryJobDestination_s |Text | Ziel eines Wiederherstellungsauftrags, an dem die Daten wiederhergestellt werden |
| RecoveryJobRPDateTime_s |DateTime | Datum und Uhrzeit, wann der Wiederherstellungspunkt, der wiederhergestellt wird, erstellt wurde |
| RecoveryJobRPLocation_s |Text | Ort, an dem der Wiederherstellungspunkt, der wiederhergestellt wird, gespeichert wurde|
| SourceSystem |Text |Quellsystem der aktuellen Daten: Azure |
| resourceId |Text |Ressourcenbezeichner der Daten, die erfasst werden. Beispiel: Ressourcen-ID des Recovery Services-Tresors|
| SubscriptionId |Text |Abonnementbezeichner der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceGroup |Text |Ressourcengruppe der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceProvider |Text |Ressourcenanbieter, für den Daten gesammelt werden. Beispiel: Microsoft.RecoveryServices |
| ResourceType |Text |Ressourcentyp, für den Daten gesammelt werden. Beispiel: Tresore |

### <a name="policy"></a>Richtlinie

Diese Tabelle enthält Details zu richtlinienbezogenen Feldern.

| Feld | Datentyp | Anwendbare Versionen | BESCHREIBUNG |
| --- | --- | --- | --- |
| EventName_s |Text ||Dieses Feld stellt den Namen des Ereignisses dar, es lautet immer „AzureBackupCentralReport“ |
| SchemaVersion_s |Text ||Dieses Feld gibt die aktuelle Version des Schemas an: **V2** |
| State_s |Text ||Aktueller Status des Richtlinienobjekts, z.B. „Aktiv“, „Gelöscht“ |
| BackupManagementType_s |Text ||Anbietertyp für den Server zur Durchführung des Sicherungsjobs, z.B. IaaSVM, FileFolder |
| OperationName |Text ||Dieses Feld repräsentiert den Namen des aktuellen Vorgangs: Richtlinie |
| Category (Kategorie) |Text ||Dieses Feld repräsentiert die Kategorie der Diagnosedaten, die an Azure Monitor-Protokolle übermittelt werden: AzureBackupReport |
| Resource |Text ||Die Ressource, für die Daten erfasst werden; zeigt den Recovery Services-Tresornamen an |
| PolicyUniqueId_g |Text ||Eindeutige ID zur Bezeichnung der Richtlinie |
| PolicyName_s |Text ||Name der definierten Richtlinie |
| BackupFrequency_s |Text ||Häufigkeit, mit der Sicherungen erfolgen, z.B. täglich, wöchentlich |
| BackupTimes_s |Text ||Datum und Uhrzeit geplanter Sicherungen |
| BackupDaysOfTheWeek_s |Text ||Tag der Woche, für den Sicherungen geplant sind |
| RetentionDuration_s |Ganze Zahl ||Beibehaltungsdauer für konfigurierte Sicherungen |
| DailyRetentionDuration_s |Ganze Zahl ||Gesamte Beibehaltungsdauer in Tagen für konfigurierte Sicherungen |
| DailyRetentionTimes_s |Text ||Konfiguration von Datum und Uhrzeit der täglichen Beibehaltung |
| WeeklyRetentionDuration_s |Dezimalzahl ||Gesamte wöchentliche Beibehaltungsdauer in Wochen für konfigurierte Sicherungen |
| WeeklyRetentionTimes_s |Text ||Konfiguration von Datum und Uhrzeit der wöchentlichen Beibehaltung |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Tage der Woche, die für die wöchentliche Beibehaltung ausgewählt sind |
| MonthlyRetentionDuration_s |Dezimalzahl ||Gesamte Beibehaltungsdauer in Monaten für konfigurierte Sicherungen |
| MonthlyRetentionTimes_s |Text ||Konfiguration von Datum und Uhrzeit der monatlichen Beibehaltung |
| MonthlyRetentionFormat_s |Text ||Typ der Konfiguration für die monatliche Beibehaltung, z.B. „Täglich“ für tagesbasiert, „Wöchentlich“ für wochenbasiert |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Tage der Woche, die für die monatliche Beibehaltung ausgewählt sind |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Wochen des Monats, in denen die monatliche Beibehaltung konfiguriert ist, z.B. Erste, Letzte usw. |
| YearlyRetentionDuration_s |Dezimalzahl ||Gesamte Beibehaltungsdauer in Jahren für konfigurierte Sicherungen |
| YearlyRetentionTimes_s |Text ||Konfiguration von Datum und Uhrzeit der jährlichen Beibehaltung |
| YearlyRetentionMonthsOfTheYear_s |Text ||Monate des Jahres, die für die jährliche Beibehaltung ausgewählt sind |
| YearlyRetentionFormat_s |Text ||Typ der Konfiguration für die jährliche Beibehaltung, z.B. „Täglich“ für tagesbasiert, „Wöchentlich“ für wochenbasiert | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Datumsangaben des Monats, die für die jährliche Beibehaltung ausgewählt sind |
| SynchronisationFrequencyPerDay_s |Ganze Zahl |V2|Anzahl der Male, die eine Dateisicherung pro Tag mit SC DPM und MABS synchronisiert wird |
| DiffBackupFormat_s |Text |V2|Format für differenzielle Sicherungen für SQL in Sicherungen von Azure-VMs |
| DiffBackupTime_s |Time |V2|Zeit für differenzielle Sicherungen für SQL im Azure Backup-Dienst für VMs|
| DiffBackupRetentionDuration_s |Dezimalzahl |V2|Aufbewahrungsdauer für differenzielle Sicherungen für SQL im Azure Backup-Dienst für VMs|
| LogBackupFrequency_s |Dezimalzahl |V2|Häufigkeit von Protokollsicherungen für SQL|
| LogBackupRetentionDuration_s |Dezimalzahl |V2|Aufbewahrungsdauer für Protokollsicherungen für SQL im Azure Backup-Dienst für VMs|
| DiffBackupDaysofTheWeek_s |Text |V2|Wochentage für differenzielle Sicherungen für SQL im Azure Backup-Dienst für VMs|
| SourceSystem |Text ||Quellsystem der aktuellen Daten: Azure |
| resourceId |Text ||Ressourcenbezeichner der Daten, die erfasst werden. Beispiel: Ressourcen-ID des Recovery Services-Tresors |
| SubscriptionId |Text ||Abonnementbezeichner der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceGroup |Text ||Ressourcengruppe der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceProvider |Text ||Ressourcenanbieter, für den Daten gesammelt werden. Beispiel: Microsoft.RecoveryServices |
| ResourceType |Text ||Ressourcentyp, für den Daten gesammelt werden. Beispiel: Tresore |

### <a name="policyassociation"></a>PolicyAssociation

Diese Tabelle enthält Details zur Zuordnung von Richtlinien zu verschiedenen Entitäten.

| Feld | Datentyp | Anwendbare Versionen | BESCHREIBUNG |
| --- | --- | --- | --- |
| EventName_s |Text ||Dieses Feld stellt den Namen des Ereignisses dar, es lautet immer „AzureBackupCentralReport“ |
| SchemaVersion_s |Text ||Dieses Feld gibt die aktuelle Version des Schemas an: **V2** |
| State_s |Text ||Aktueller Status des Richtlinienobjekts, z.B. „Aktiv“, „Gelöscht“ |
| BackupManagementType_s |Text ||Anbietertyp für den Server zur Durchführung des Sicherungsjobs, z.B. IaaSVM, FileFolder |
| OperationName |Text ||Dieses Feld repräsentiert den Namen des aktuellen Vorgangs: PolicyAssociation |
| Category (Kategorie) |Text ||Dieses Feld repräsentiert die Kategorie der Diagnosedaten, die an Azure Monitor-Protokolle übermittelt werden: AzureBackupReport |
| Resource |Text ||Die Ressource, für die Daten erfasst werden; zeigt den Recovery Services-Tresornamen an |
| PolicyUniqueId_g |Text ||Eindeutige ID zur Bezeichnung der Richtlinie |
| VaultUniqueId_s |Text ||Eindeutige ID des Tresors, zu dem diese Richtlinie gehört |
| BackupManagementServerUniqueId_s |Text |V2 |Feld, um den Server für die Sicherungsverwaltung, durch den das Sicherungselement geschützt wird, eindeutig zu bestimmen (wenn zutreffend)        |
| SourceSystem |Text ||Quellsystem der aktuellen Daten: Azure |
| resourceId |Text ||Ressourcenbezeichner der Daten, die erfasst werden. Beispiel: Ressourcen-ID des Recovery Services-Tresors |
| SubscriptionId |Text ||Abonnementbezeichner der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceGroup |Text ||Ressourcengruppe der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceProvider |Text ||Ressourcenanbieter, für den Daten gesammelt werden. Beispiel: Microsoft.RecoveryServices |
| ResourceType |Text ||Ressourcentyp, für den Daten gesammelt werden. Beispiel: Tresore |

### <a name="protected-container"></a>Geschützte Container

Diese Tabelle enthält grundlegende Felder zu geschützten Containern (ehemals „ProtectedServer“ in Version 1)

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Feld, mit dem ein geschützter Container eindeutig bestimmt wird |
| ProtectedContainerOSType_s |Text |Betriebssystemtyp des geschützten Containers |
| ProtectedContainerOSVersion_s |Text |Betriebssystemversion des geschützten Containers |
| AgentVersion_s |Text |Versionsnummer der Agentsicherung oder des Schutzagenten (im Fall von SC DPM und MABS) |
| BackupManagementType_s |Text |Anbietertyp für die Sicherung, z.B. IaaSVM, FileFolder |
| EntityState_s |Text |Aktueller Status des Objekts des geschützten Servers, z.B. Aktiv, Gelöscht |
| ProtectedContainerFriendlyName_s |Text |Anzeigename des geschützten Servers |
| ProtectedContainerName_s |Text |Name des geschützten Containers |
| ProtectedContainerWorkloadType_s |Text |Typ des geschützten Containers, der gesichert wird, z. B. IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Gibt an, ob der geschützte Container ein lokaler Container ist oder sich in Azure befindet |
| ProtectedContainerType_s |Text |Gibt an, ob der geschützte Container ein Server oder ein Container ist |

### <a name="storage"></a>Storage

Diese Tabelle enthält Details zu speicherbezogenen Feldern.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| CloudStorageInBytes_s |Dezimalzahl |Von Sicherungen belegter Sicherungsspeicher in der Cloud, berechnet anhand des letzten Werts |
| ProtectedInstances_s |Dezimalzahl |Anzahl der geschützten Instanzen, die zum Berechnen von Front-End-Speicher in der Abrechnung verwendet werden, berechnet anhand des letzten Werts |
| EventName_s |Text |Dieses Feld stellt den Namen des Ereignisses dar, es lautet immer „AzureBackupCentralReport“ |
| SchemaVersion_s |Text |Dieses Feld gibt die aktuelle Version des Schemas an: **V2** |
| State_s |Text |Aktueller Status des Speicherobjekts, z.B. „Aktiv“, „Gelöscht“ |
| BackupManagementType_s |Text |Anbietertyp für den Server zur Durchführung des Sicherungsjobs, z.B. IaaSVM, FileFolder |
| OperationName |Text |Dieses Feld repräsentiert den Namen des aktuellen Vorgangs: Speicher |
| Category (Kategorie) |Text |Dieses Feld repräsentiert die Kategorie der Diagnosedaten, die an Azure Monitor-Protokolle übermittelt werden: AzureBackupReport |
| Resource |Text |Die Ressource, für die Daten erfasst werden; zeigt den Recovery Services-Tresornamen an |
| ProtectedServerUniqueId_s |Text |Eindeutige ID des geschützten Servers, für den der Speicher berechnet wird |
| VaultUniqueId_s |Text |Eindeutige ID des Tresors, für den der Speicher berechnet wird |
| SourceSystem |Text |Quellsystem der aktuellen Daten: Azure |
| resourceId |Text |Ressourcenbezeichner der Daten, die erfasst werden. Beispiel: Ressourcen-ID des Recovery Services-Tresors |
| SubscriptionId |Text |Abonnementbezeichner der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceGroup |Text |Ressourcengruppe der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceProvider |Text |Ressourcenanbieter, für den Daten gesammelt werden. Beispiel: Microsoft.RecoveryServices |
| ResourceType |Text |Ressourcentyp, für den Daten gesammelt werden. Beispiel: Tresore |

### <a name="storageassociation"></a>StorageAssociation

In dieser Tabelle sind grundlegende speicherbezogene Felder enthalten, die Speicher mit anderen Entitäten verbinden

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Eindeutige ID, mithilfe derer die Speicherentität bestimmt wird |
| SchemaVersion_s |Text |Dieses Feld gibt die aktuelle Version des Schemas an: **V2** |
| BackupItemUniqueId_s |Text |Eindeutige ID, mithilfe derer das Sicherungselement bestimmt wird, das mit der Speicherentität verbunden ist |
| BackupManagementServerUniqueId_s |Text |Eindeutige ID, mithilfe derer der Sicherungsverwaltungsserver bestimmt wird, der mit der Speicherentität verbunden ist|
| VaultUniqueId_s |Text |Eindeutige ID, mithilfe derer der Tresor bestimmt wird, der mit der Speicherentität verbunden ist|
| StorageConsumedInMBs_s |Number|Speichergröße, die vom entsprechenden Sicherungselement im entsprechenden Speicher benötigt wird |
| StorageAllocatedInMBs_s |Number |Speichergröße, die dem entsprechenden Sicherungselement im entsprechenden Speicher des Typs „Datenträger“ zugewiesen wird|

### <a name="vault"></a>Tresor

Diese Tabelle enthält Details zu tresorbezogenen Feldern.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| EventName_s |Text |Dieses Feld stellt den Namen des Ereignisses dar, es lautet immer „AzureBackupCentralReport“ |
| SchemaVersion_s |Text |Dieses Feld gibt die aktuelle Version des Schemas an: **V2** |
| State_s |Text |Aktueller Status des Tresorobjekts, z.B. „Aktiv“, „Gelöscht“ |
| OperationName |Text |Dieses Feld repräsentiert den Namen des aktuellen Vorgangs: Tresor |
| Category (Kategorie) |Text |Dieses Feld repräsentiert die Kategorie der Diagnosedaten, die an Azure Monitor-Protokolle übermittelt werden: AzureBackupReport |
| Resource |Text |Die Ressource, für die Daten erfasst werden; zeigt den Recovery Services-Tresornamen an |
| VaultUniqueId_s |Text |Eindeutige ID des Tresors |
| VaultName_s |Text |Name des Tresors |
| AzureDataCenter_s |Text |Rechenzentrum, in dem sich der Tresor befindet |
| StorageReplicationType_s |Text |Typ der Speicherreplikation für den Tresor, z.B. GeoRedundant |
| SourceSystem |Text |Quellsystem der aktuellen Daten: Azure |
| resourceId |Text |Ressourcenbezeichner der Daten, die erfasst werden. Beispiel: Ressourcen-ID des Recovery Services-Tresors |
| SubscriptionId |Text |Abonnementbezeichner der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceGroup |Text |Ressourcengruppe der Ressource (z.B. Recovery Services-Tresor), zu der Daten gesammelt werden |
| ResourceProvider |Text |Ressourcenanbieter, für den Daten gesammelt werden. Beispiel: Microsoft.RecoveryServices |
| ResourceType |Text |Ressourcentyp, für den Daten gesammelt werden. Beispiel: Tresore |

### <a name="backup-management-server"></a>Server für die Sicherungsverwaltung

In dieser Tabelle sind grundlegende Felder zum Sicherungsverwaltungsserver enthalten

|Feld  |Datentyp  | BESCHREIBUNG  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |Name des Servers für die Sicherungsverwaltung        |
|AzureBackupAgentVersion_s     |Text         |Version des Azure Backup-Agenten auf dem Sicherungsverwaltungsserver          |
|BackupManagmentServerVersion_s     |Text         |Version des Servers für die Sicherungsverwaltung|
|BackupManagmentServerOSVersion_s     |Text            |Betriebssystemversion des Servers für die Sicherungsverwaltung|
|BackupManagementServerType_s     |Text         |Typ des Sicherungsverwaltungsserver, z. B. MABS oder SC DPM|
|BackupManagmentServerUniqueId_s     |Text         |Feld, um den Sicherungsverwaltungsserver eindeutig zu bestimmen       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

In dieser Tabelle sind die Workload(s) angegeben, mit denen ein Volumen verbunden ist.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| StorageUniqueId_s |Text |Eindeutige ID, mithilfe derer die Speicherentität bestimmt wird |
| BackupItemType_s |Text |Die Workloads, für die dieses Volumen der bevorzugte Speicher ist|

### <a name="protectedinstance"></a>ProtectedInstance

Diese Tabelle enthält grundlegende Felder zu geschützten Instanzen.

| Feld | Datentyp |Anwendbare Versionen | BESCHREIBUNG |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |V2|Eindeutige ID, mithilfe derer das Sicherungselement für VMs bestimmt wird, die mithilfe von DPM, MABS gesichert wurden|
| ProtectedContainerUniqueId_s |Text |V2|Eindeutige ID, mithilfe derer der geschützte Container für alles außer VMs bestimmt wird, die mithilfe von DPM, MABS geschützt wurden|
| ProtectedInstanceCount_s |Text |V2|Anzahl geschützter Instanzen für das zugeordnete Sicherungselement oder den geschützten Container an diesem Datum und zu dieser Uhrzeit|

### <a name="recoverypoint"></a>RecoveryPoint

Diese Tabelle enthält grundlegende Felder zum Wiederherstellungspunkt

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Eindeutige ID, mithilfe derer das Sicherungselement für VMs bestimmt wird, die mithilfe von DPM, MABS gesichert wurden|
| OldestRecoveryPointTime_s |Text |Datum und Uhrzeit des ältesten Wiederherstellungspunkts des Sicherungselements|
| OldestRecoveryPointLocation_s |Text |Speicherort des ältesten Wiederherstellungspunkts des Sicherungselements|
| LatestRecoveryPointTime_s |Text |Datum und Uhrzeit des aktuellsten Wiederherstellungspunkts des Sicherungselements|
| LatestRecoveryPointLocation_s |Text |Speicherort des aktuellsten Wiederherstellungspunkts des Sicherungselements|

## <a name="next-steps"></a>Nächste Schritte

Sobald Sie das Datenmodell geprüft haben, können Sie in Azure Monitor-Protokollen damit beginnen [benutzerdefinierte Abfragen zu erstellen](../azure-monitor/learn/tutorial-logs-dashboards.md), um Ihr eigenes Dashboard zu erstellen.
