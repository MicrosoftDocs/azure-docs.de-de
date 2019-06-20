---
title: Einrichten von Warnungen und Benachrichtigungen über das Azure-Portal | Microsoft-Dokumentation
description: Verwenden Sie das Azure-Portal, um SQL-Datenbankwarnungen zu erstellen, die Benachrichtigungen oder eine Automatisierung auslösen, wenn die angegebenen Bedingungen erfüllt sind.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: 93337e39a117c1f8d38f24dc416ff8ae95513a34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035970"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Erstellen von Warnungen für Azure SQL-Datenbank und Data Warehouse über das Azure-Portal

## <a name="overview"></a>Übersicht
In diesem Artikel erfahren Sie, wie Sie mit dem Azure-Portal Azure SQL-Datenbank- und Data Warehouse-Warnungen einrichten können. Warnungen können Ihnen eine E-Mail senden oder einen Webhook aufrufen, wenn bei einer bestimmten Metrik (beispielsweise bei der Datenbankgröße oder bei der CPU-Auslastung) der Schwellenwert erreicht wird. Dieser Artikel nennt auch bewährte Methoden für das Festlegen von Warnungszeiträumen.    

> [!IMPORTANT]
> Dieses Feature steht in einer verwalteten Instanz noch nicht zur Verfügung. Als Alternative können Sie den SQL-Agent verwenden, um E-Mail-Warnungen für einige Metriken auf der Grundlage [dynamischer Verwaltungssichten](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) zu senden.

Sie können auf der Grundlage von Überwachungsmetriken für Ihre Azure-Services oder von Ereignissen, die bei diesen auftreten, eine Warnung empfangen.

* **Metrikwerte** : Die Warnung wird ausgelöst, wenn der Wert einer angegebenen Metrik einen von Ihnen festgelegten Schwellenwert in beliebiger Richtung überschreitet. Das Auslösen erfolgt sowohl, wenn die Bedingung erstmals erfüllt wird, als auch danach, wenn diese Bedingung nicht mehr erfüllt wird.    
* **Aktivitätsprotokollereignisse** : Eine Warnung kann für *jedes* Ereignis oder nur dann ausgelöst werden, wenn eine bestimmte Anzahl von Ereignissen erfolgt ist.

Sie können konfigurieren, dass bei einer Warnung Folgendes erfolgt, wenn sie ausgelöst wird:

* Senden von E-Mail-Benachrichtigungen an den Dienstadministrator und Co-Administratoren
* Senden von E-Mal an weitere von Ihnen angegebene Adressen
* Aufrufen eines Webhooks

Sie haben folgende Möglichkeiten zum Konfigurieren von Warnregeln und Abrufen zugehöriger Informationen:

* [Azure-Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Befehlszeilenschnittstelle (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor-REST-API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Erstellen einer Warnungsregel anhand einer Metrik mit dem Azure-Portal
1. Suchen Sie im [Portal](https://portal.azure.com/)die Ressource, die Sie überwachen möchten, und wählen Sie sie aus.
2. Wählen Sie im Abschnitt „ÜBERWACHUNG“ die Option **Warnungen (klassisch)** aus. Text und Symbol können je nach Ressource geringfügig variieren.  
   
     ![Überwachung](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **NUR SQL DW**: Klicken Sie auf den Graphen **DWU-Nutzung**. Wählen Sie **Klassische Warnungen anzeigen** aus.

3. Wählen Sie die Schaltfläche **Metrikwarnung hinzufügen (klassisch)** aus, und füllen Sie die Felder aus.
   
    ![Warnung hinzufügen](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **Benennen** Sie Ihre Warnungsregel, und wählen Sie eine **Beschreibung** aus, die auch in Benachrichtigungs-E-Mails angezeigt wird.
5. Wählen Sie die **Metrik** aus, die Sie überwachen möchten, und dann je einen Wert für **Bedingung** und **Schwellenwert** für die Metrik aus. Wählen Sie auch den **Zeitraum** der Metrikregel aus, der erfüllt sein muss, ehe die Warnung ausgelöst wird. Wenn Sie z.B. den Zeitraum „PT5M“ wählen, und die Warnung nach einer CPU-Auslastung von über 80 % sucht, wird die Warnung ausgelöst, wenn die **durchschnittliche** CPU-Auslastung 5 Minuten über 80 % lag. Nachdem der erste Trigger ausgelöst wurde, erfolgt ein erneutes Auslösen, wenn die durchschnittliche CPU-Auslastung 5 Minuten unter 80 % bleibt. Die CPU-Messung erfolgt minütlich. In der folgenden Tabelle finden Sie unterstützte Zeitfenster und den Aggregationstyp für die einzelnen Warnungen – nicht alle Warnungen verwenden den durchschnittlichen Wert.   
6. Aktivieren Sie **E-Mail-Besitzer...** , wenn Sie möchten, dass Administratoren und Co-Administratoren per E-Mail benachrichtigt werden, wenn die Warnung ausgelöst wird.
7. Wenn Sie möchten, dass bei Auslösen der Warnung eine Benachrichtigung an weitere E-Mail-Adressen gesendet wird, fügen Sie diese dem Feld **Zusätzliche Administrator-E-Mail-Adresse** hinzu. Trennen Sie mehrere E-Mail-Adressen durch Semikolons: *email\@contoso.com;email2\@contoso.com*.
8. Fügen Sie in einen gültigen URI in das Feld **Webhook** ein, wenn dieser bei Auslösen der Warnung aufgerufen werden soll.
9. Wählen Sie **OK** aus, wenn das Erstellen der Warnung abgeschlossen ist.   

Innerhalb weniger Minuten wird die Warnung aktiv und wie oben beschrieben ausgelöst.

## <a name="managing-your-alerts"></a>Verwalten von Warnungen
Nachdem Sie eine Warnung erstellt haben, können Sie sie auswählen und:

* ein Diagramm einblenden, das den Schwellenwert der Metrik und die tatsächlichen Werte vom Vortag zeigt.
* bearbeiten oder löschen.
* sie **deaktivieren** oder **aktivieren**, wenn Sie den Empfang von Benachrichtigungen zu dieser Warnung vorübergehend beenden oder fortsetzen möchten.


## <a name="sql-database-alert-values"></a>Werte für SQL-Datenbankwarnungen

| Ressourcentyp | Metrikname | Anzeigename | Aggregationstyp | Mindestzeitfenster für Warnungen|
| --- | --- | --- | --- | --- |
| SQL database | cpu_percent | CPU-Prozentsatz | Durchschnitt | 5 Minuten |
| SQL database | physical_data_read_percent | E/A-Prozentsatz für Daten | Durchschnitt | 5 Minuten |
| SQL database | log_write_percent | E/A-Prozentsatz für Protokoll | Durchschnitt | 5 Minuten |
| SQL database | dtu_consumption_percent | DTU-Prozentsatz | Durchschnitt | 5 Minuten |
| SQL database | storage | Datenbankgröße gesamt | Maximum | 30 Minuten |
| SQL database | connection_successful | Erfolgreiche Verbindungen | Gesamt | 10 Minuten |
| SQL database | connection_failed | Verbindungsfehler | Gesamt | 10 Minuten |
| SQL database | blocked_by_firewall | Von der Firewall blockiert | Gesamt | 10 Minuten |
| SQL database | deadlock | Deadlocks | Gesamt | 10 Minuten |
| SQL database | storage_percent | Datenbankgröße als Prozentsatz | Maximum | 30 Minuten |
| SQL database | xtp_storage_percent | In-Memory-OLTP-Speicher in Prozent (Vorschau) | Durchschnitt | 5 Minuten |
| SQL database | workers_percent | Worker in Prozent | Durchschnitt | 5 Minuten |
| SQL database | sessions_percent | Sitzungen in Prozent | Durchschnitt | 5 Minuten |
| SQL database | dtu_limit | DTU-Grenzwert | Durchschnitt | 5 Minuten |
| SQL database | dtu_used | DTU-Verbrauch | Durchschnitt | 5 Minuten |
||||||
| Pool für elastische Datenbanken | cpu_percent | CPU-Prozentsatz | Durchschnitt | 10 Minuten |
| Pool für elastische Datenbanken | physical_data_read_percent | E/A-Prozentsatz für Daten | Durchschnitt | 10 Minuten |
| Pool für elastische Datenbanken | log_write_percent | E/A-Prozentsatz für Protokoll | Durchschnitt | 10 Minuten |
| Pool für elastische Datenbanken | dtu_consumption_percent | DTU-Prozentsatz | Durchschnitt | 10 Minuten |
| Pool für elastische Datenbanken | storage_percent | Speicher in Prozent | Durchschnitt | 10 Minuten |
| Pool für elastische Datenbanken | workers_percent | Worker in Prozent | Durchschnitt | 10 Minuten |
| Pool für elastische Datenbanken | eDTU_limit | eDTU-Grenzwert | Durchschnitt | 10 Minuten |
| Pool für elastische Datenbanken | storage_limit | Speicherbegrenzung | Durchschnitt | 10 Minuten |
| Pool für elastische Datenbanken | eDTU_used | eDTU-Verbrauch | Durchschnitt | 10 Minuten |
| Pool für elastische Datenbanken | storage_used | Verwendeter Speicher | Durchschnitt | 10 Minuten |
||||||               
| SQL Data Warehouse | cpu_percent | CPU-Prozentsatz | Durchschnitt | 10 Minuten |
| SQL Data Warehouse | physical_data_read_percent | E/A-Prozentsatz für Daten | Durchschnitt | 10 Minuten |
| SQL Data Warehouse | connection_successful | Erfolgreiche Verbindungen | Gesamt | 10 Minuten |
| SQL Data Warehouse | connection_failed | Verbindungsfehler | Gesamt | 10 Minuten |
| SQL Data Warehouse | blocked_by_firewall | Von der Firewall blockiert | Gesamt | 10 Minuten |
| SQL Data Warehouse | service_level_objective | Diensttarif der Datenbank | Gesamt | 10 Minuten |
| SQL Data Warehouse | dwu_limit | DWU-Grenzwert | Maximum | 10 Minuten |
| SQL Data Warehouse | dwu_consumption_percent | DWU in Prozent | Durchschnitt | 10 Minuten |
| SQL Data Warehouse | dwu_used | DWU-Verbrauch | Durchschnitt | 10 Minuten |
||||||


## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über die Azure-Überwachung](../monitoring-and-diagnostics/monitoring-overview.md) , einschließlich der Typen von Informationen, die Sie sammeln und überwachen können.
* Erfahren Sie mehr über das [Konfigurieren von Webhooks in Warnungen](../azure-monitor/platform/alerts-webhooks.md).
* Verschaffen Sie sich einen [Überblick über Diagnoseprotokolle](../azure-monitor/platform/diagnostic-logs-overview.md), um detaillierte Hochfrequenzmetriken für Ihren Dienst zu erfassen.
* Verschaffen Sie sich einen Überblick über das [Sammeln von Dienstmetriken](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.
