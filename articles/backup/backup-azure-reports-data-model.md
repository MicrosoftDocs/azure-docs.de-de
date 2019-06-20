---
title: Datenmodell für Azure Backup
description: In diesem Artikel werden die Details des Power BI-Datenmodells für Azure Backup-Berichte vorgestellt.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: adigan
ms.openlocfilehash: c6160570644da108ba713e8229b38f9587495c92
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337577"
---
# <a name="data-model-for-azure-backup-reports"></a>Datenmodell für Azure Backup-Berichte
In diesem Artikel wird das Power BI-Datenmodell beschrieben, das zum Erstellen von Azure Backup-Berichten verwendet wird. Mit diesem Datenmodell können Sie vorhandene Berichte basierend auf entsprechenden Feldern filtern und, was noch wichtiger ist, eigene Berichte mithilfe der Tabellen und Felder im Modell erstellen. 

## <a name="creating-new-reports-in-power-bi"></a>Erstellen neuer Berichte in Power BI
Power BI bietet Anpassungsfunktionen, mit deren Hilfe Sie [Berichte mithilfe des Datenmodells erstellen](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/) können.

## <a name="using-azure-backup-data-model"></a>Verwenden des Azure Backup-Datenmodells
Sie können die folgenden Felder im Datenmodell nutzen, um Berichte zu erstellen und vorhandene Berichte anzupassen.

### <a name="alert"></a>Warnung
Diese Tabelle enthält grundlegende Felder und Aggregationen für verschiedene warnungsbezogene Felder.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Ganze Zahl |Anzahl der Warnungen, die im gewählten Zeitraum erstellt wurden |
| %ActiveAlertsCreatedInPeriod |Prozentsatz |Prozentsatz aktiver Warnungen im gewählten Zeitraum |
| %CriticalAlertsCreatedInPeriod |Prozentsatz |Prozentsatz kritischer Warnungen im gewählten Zeitraum |
| AlertOccurrenceDate |Date |Datum der Erstellung der Warnung |
| AlertSeverity |Text |Schweregrad der Warnung, z.B. Kritisch |
| AlertStatus |Text |Status der Warnung, z.B. Aktiv |
| AlertType |Text |Typ der generierten Warnung, z.B. Sicherung |
| AlertUniqueId |Text |Eindeutige ID der generierten Warnung |
| AsOnDateTime |Date/Time |Uhrzeit der letzten Aktualisierung der ausgewählten Zeile |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Dezimalzahl |Durchschnittliche Zeit (in Minuten) zum Beheben der Warnung für den gewählten Zeitraum |
| EntityState |Text |Aktueller Status des Warnungsobjekts, z.B. Aktiv, Gelöscht |

### <a name="backup-item"></a>Sicherungselement
Diese Tabelle enthält grundlegende Felder und Aggregationen für verschiedene auf Sicherungselemente bezogene Felder.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| #BackupItems |Ganze Zahl |Anzahl der Sicherungselemente |
| #UnprotectedBackupItems |Ganze Zahl |Anzahl der Sicherungselemente, deren Schutz beendet wurde oder die für Sicherungen konfiguriert sind, aber Sicherungen nicht gestartet wurden|
| AsOnDateTime |Date/Time |Uhrzeit der letzten Aktualisierung der ausgewählten Zeile |
| BackupItemFriendlyName |Text |Anzeigename des Sicherungselements |
| BackupItemId |Text |ID des Sicherungselements |
| BackupItemName |Text |Name des Sicherungselements |
| BackupItemType |Text |Typ des Sicherungselements, z.B. VM, Dateiordner |
| EntityState |Text |Aktueller Status des Sicherungselementobjekts, z.B. Aktiv, Gelöscht |
| LastBackupDateTime |Date/Time |Zeitpunkt der letzten Sicherung des ausgewählten Sicherungselements |
| LastBackupState |Text |Status der letzten Sicherung des ausgewählten Sicherungselements, z.B. Erfolgreich, Fehler |
| LastSuccessfulBackupDateTime |Date/Time |Zeitpunkt der letzten erfolgreichen Sicherung des ausgewählten Sicherungselements |
| ProtectionState |Text |Aktueller Schutzstatus des Sicherungselements, z.B. Geschützt, Schutz beendet |

### <a name="calendar"></a>Kalender
Diese Tabelle enthält Details zu kalenderbezogenen Feldern.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| Date |Date |Zum Filtern von Daten ausgewähltes Datum |
| DateKey |Text |Eindeutiger Schlüssel jedes Datumselements |
| DayDiff |Dezimalzahl |Differenz in Form von Tagen für das Filtern von Daten. Beispiel: 0 gibt die Daten des aktuellen Tages, -1 die Daten des Vortages an. 0 und -1 geben Daten des aktuellen und des Vortages an.  |
| Month (Monat) |Text |Der gewählte Monat des Jahres zum Filtern von Daten. Der Monat beginnt am 1. und endet am 31. Tag. |
| MonthDate | Date |Zum Filtern von Daten ausgewähltes Datum im Monat, an dem der Monat endet |
| MonthDiff |Dezimalzahl |Differenz in Form von Monaten für das Filtern von Daten. Beispiel: 0 gibt die Daten des aktuellen Monats, -1 die Daten des Vormonats an. 0 und -1 geben Daten des aktuellen und des Vormonats an. |
| Woche |Text |Zum Filtern von Daten ausgewählte Woche. Die Woche beginnt am Sonntag und endet am Samstag. |
| WeekDate |Date |Zum Filtern von Daten ausgewähltes Datum in der Woche, an dem die Woche endet |
| WeekDiff |Dezimalzahl |Differenz in Form von Wochen für das Filtern von Daten. Beispiel: 0 gibt die Daten der aktuellen Woche, -1 die Daten der Vorwoche an. 0 und -1 geben Daten der aktuellen und der Vorwoche an. |
| Year (Jahr) |Text |Zum Filtern von Daten ausgewähltes Kalenderjahr |
| YearDate |Date |Zum Filtern von Daten ausgewähltes Datum im Jahr, an dem das Jahr endet |

### <a name="job"></a>Auftrag
Diese Tabelle enthält grundlegende Felder und Aggregationen für verschiedene auftragsbezogene Felder.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| #JobsCreatedInPeriod |Ganze Zahl |Anzahl der Aufträge, die im gewählten Zeitraum erstellt wurden |
| %FailuresForJobsCreatedInPeriod |Prozentsatz |Prozentsatz der gesamten Auftragsfehler im ausgewählten Zeitraum |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Dezimalzahl |80\. Quantilwert der übertragenen Daten (in MB) für **Sicherungsaufträge**, die im ausgewählten Zeitraum erstellt wurden |
| AsOnDateTime |Date/Time |Uhrzeit der letzten Aktualisierung der ausgewählten Zeile |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Dezimalzahl |Durchschnittliche Zeit in Minuten für **abgeschlossene Sicherungsaufträge**, die im ausgewählten Zeitraum erstellt wurden |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Dezimalzahl |Durchschnittliche Zeit in Minuten für **abgeschlossene Wiederherstellungsaufträge**, die im ausgewählten Zeitraum erstellt wurden |
| BackupStorageDestination |Text |Ziel des Sicherungsspeichers, z.B. Cloud, Datenträger  |
| EntityState |Text |Aktueller Status des Auftragsobjekts, z.B. Aktiv, Gelöscht |
| JobFailureCode |Text |Zeichenfolge mit dem Fehlercode zum Angeben des Grunds des Auftragsfehlers |
| JobOperation |Text |Vorgang, für den Auftrag ausgeführt wird, z.B. Sicherung, Wiederherstellung, Sicherungskonfiguration |
| JobStartDate |Date |Datum des Starts des Auftrags |
| JobStartTime |Time |Uhrzeit des Starts des Auftrags |
| Auftragsstatus |Text |Status des beendeten Auftrags, z.B. Abgeschlossen, Fehler |
| JobUniqueId |Text |Eindeutige ID zur Bezeichnung des Auftrags |

### <a name="policy"></a>Richtlinie
Diese Tabelle enthält grundlegende Felder und Aggregationen für verschiedene richtlinienbezogene Felder.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| #Policies |Ganze Zahl |Anzahl der Sicherungsrichtlinien im System |
| #PoliciesInUse |Ganze Zahl |Anzahl der Richtlinien, die derzeit für das Konfigurieren von Sicherungen verwendet werden |
| AsOnDateTime |Date/Time |Uhrzeit der letzten Aktualisierung der ausgewählten Zeile |
| BackupDaysOfTheWeek |Text |Tag der Woche, für den Sicherungen geplant sind |
| BackupFrequency |Text |Häufigkeit, mit der Sicherungen erfolgen, z. B. täglich, wöchentlich |
| BackupTimes |Text |Datum und Uhrzeit geplanter Sicherungen |
| DailyRetentionDuration |Ganze Zahl |Gesamte Beibehaltungsdauer in Tagen für konfigurierte Sicherungen |
| DailyRetentionTimes |Text |Konfiguration von Datum und Uhrzeit der täglichen Beibehaltung |
| EntityState |Text |Aktueller Status des Richtlinienobjekts, z.B. Aktiv, Gelöscht |
| MonthlyRetentionDaysOfTheMonth |Text |Datumsangaben des Monats, die für die monatliche Beibehaltung ausgewählt sind |
| MonthlyRetentionDaysOfTheWeek |Text |Tage der Woche, die für die monatliche Beibehaltung ausgewählt sind |
| MonthlyRetentionDuration |Dezimalzahl |Gesamte Beibehaltungsdauer in Monaten für konfigurierte Sicherungen |
| MonthlyRetentionFormat |Text |Typ der Konfiguration für die monatliche Beibehaltung, z.B. täglich für tagesbasiert, wöchentlich für wochenbasiert |
| MonthlyRetentionTimes |Text |Konfiguration von Datum und Uhrzeit der monatlichen Beibehaltung |
| MonthlyRetentionWeeksOfTheMonth |Text |Wochen des Monats, für die die monatliche Beibehaltung konfiguriert ist, z.B. Erste, Letzte usw. |
| PolicyName |Text |Name der definierten Richtlinie |
| PolicyUniqueId |Text |Eindeutige ID zur Bezeichnung der Richtlinie |
| RetentionType |Text |Typ der Aufbewahrungsrichtlinie, z.B. Täglich, Wöchentlich, Monatlich, Jährlich |
| WeeklyRetentionDaysOfTheWeek |Text |Tage der Woche, die für die wöchentliche Beibehaltung ausgewählt sind |
| WeeklyRetentionDuration |Dezimalzahl |Gesamte wöchentliche Beibehaltungsdauer in Wochen für konfigurierte Sicherungen |
| WeeklyRetentionTimes |Text |Konfiguration von Datum und Uhrzeit der wöchentlichen Beibehaltung |
| YearlyRetentionDaysOfTheMonth |Text |Datumsangaben des Monats, die für die jährliche Beibehaltung ausgewählt sind |
| YearlyRetentionDaysOfTheWeek |Text |Tage der Woche, die für die jährliche Beibehaltung ausgewählt sind |
| YearlyRetentionDuration |Dezimalzahl |Gesamte Beibehaltungsdauer in Jahren für konfigurierte Sicherungen |
| YearlyRetentionFormat |Text |Typ der Konfiguration für die jährliche Beibehaltung, z.B. täglich für tagesbasiert, wöchentlich für wochenbasiert |
| YearlyRetentionMonthsOfTheYear |Text |Monate des Jahres, die für die jährliche Beibehaltung ausgewählt sind |
| YearlyRetentionTimes |Text |Konfiguration von Datum und Uhrzeit der jährlichen Beibehaltung |
| YearlyRetentionWeeksOfTheMonth |Text |Wochen des Monats, für die die jährliche Beibehaltung konfiguriert ist, z.B. Erste, Letzte usw. |

### <a name="protected-server"></a>Geschützter Server
Diese Tabelle enthält grundlegende Felder und Aggregationen für verschiedene Felder, die sich auf geschützte Server beziehen.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| #ProtectedServers |Ganze Zahl |Anzahl geschützter Server |
| AsOnDateTime |Date/Time |Uhrzeit der letzten Aktualisierung der ausgewählten Zeile |
| AzureBackupAgentOSType |Text |Betriebssystemtyp des Azure Backup-Agents |
| AzureBackupAgentOSVersion |Text |Betriebssystemversion des Azure Backup-Agents |
| AzureBackupAgentUpdateDate |Text |Datum, an dem der Agent-Backup-Agent aktualisiert wurde |
| AzureBackupAgentVersion |Text |Nummer der Backup-Agent-Version |
| BackupManagementType |Text |Anbietertyp für die Sicherung, z.B. IaaSVM, FileFolder |
| EntityState |Text |Aktueller Status des Objekts des geschützten Servers, z.B. Aktiv, Gelöscht |
| ProtectedServerFriendlyName |Text |Anzeigename des geschützten Servers |
| ProtectedServerName |Text |Name des geschützten Servers |
| ProtectedServerType |Text |Typ des geschützten Servers, der gesichert wird, z.B. IaaSVMContainer |
| ProtectedServerName |Text |Name des geschützten Servers, zu dem das Sicherungselement gehört |
| RegisteredContainerId |Text |ID des Containers, der für die Sicherung registriert ist |

### <a name="storage"></a>Storage
Diese Tabelle enthält grundlegende Felder und Aggregationen für verschiedene speicherbezogene Felder.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| #ProtectedInstances |Dezimalzahl |Anzahl der geschützten Instanzen, die zum Berechnen von Front-End-Speicher in der Abrechnung verwendet werden. Die Berechnung erfolgt basierend auf dem letzten Wert im ausgewählten Zeitraum. |
| AsOnDateTime |Date/Time |Uhrzeit der letzten Aktualisierung der ausgewählten Zeile |
| CloudStorageInMB |Dezimalzahl |Von Sicherungen belegter Sicherungsspeicher in der Cloud. Die Berechnung erfolgt basierend auf dem letzten Wert im ausgewählten Zeitraum. |
| EntityState |Text |Aktueller Status des Speicherobjekts, z.B. Aktiv, Gelöscht |
| LastUpdatedDate |Date |Datum, an dem ausgewählte Zeile zuletzt aktualisiert wurde |

### <a name="time"></a>Time
Diese Tabelle enthält Details zu uhrzeitbezogenen Feldern.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| Hour |Time |Stunde des Tages, z.B. 13:00:00 Uhr |
| HourNumber |Dezimalzahl |Nummer der Stunde des Tages, z.B. 13:00 |
| Minute |Dezimalzahl |Minute der Stunde |
| PeriodOfTheDay |Text |Zeitfenster des Tages, z.B. 12-15 Uhr |
| Time |Time |Stunde des Tages, z.B. 12:00:01 Uhr |
| TimeKey |Text |Schlüsselwert zum Darstellen der Zeit |

### <a name="vault"></a>Tresor
Diese Tabelle enthält grundlegende Felder und Aggregationen für verschiedene tresorbezogene Felder.

| Feld | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| #Vaults |Ganze Zahl |Anzahl der Tresore |
| AsOnDateTime |Date/Time |Uhrzeit der letzten Aktualisierung der ausgewählten Zeile |
| AzureDataCenter |Text |Rechenzentrum, in dem sich der Tresor befindet |
| EntityState |Text |Aktueller Status des Tresorobjekts, z.B. Aktiv, Gelöscht |
| StorageReplicationType |Text |Typ der Speicherreplikation für den Tresor, z.B. Georedundant |
| SubscriptionId |Text |Abonnement-ID des Kunden, die zur Erstellung von Berichten ausgewählt ist |
| VaultName |Text |Name des Tresors |
| VaultTags |Text |Dem Tresor zugeordnete Tags |

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie das Datenmodell zum Erstellen von Azure Backup-Berichten überprüft haben, finden Sie in den folgenden Artikeln weitere ausführliche Informationen zum Erstellen und Anzeigen von Berichten in Power BI.

* [Erstellen von Berichten in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtern von Berichten in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
