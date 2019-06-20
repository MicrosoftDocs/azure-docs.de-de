---
title: Überwachen der Azure SQL-Datensynchronisierung mit Azure Monitor-Protokollen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Azure SQL-Datensynchronisierung mithilfe von Azure Monitor-Protokollen überwachen
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 6e94aac47ce5b45e700e2413d2e86d5f36596348
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614954"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Überwachen der SQL-Datensynchronisierung mit Azure Monitor-Protokollen 

Zur Überprüfung des Aktivitätsprotokolls der SQL-Datensynchronisierung sowie zur Erkennung von Fehlern und Warnungen mussten Sie bislang die SQL-Datensynchronisierung manuell im Azure-Portal überprüfen oder hierfür PowerShell oder die REST-API verwenden. Mit den Schritten in diesem Artikel können Sie eine benutzerdefinierte Lösung konfigurieren, die die Überwachung der Datensynchronisierung verbessert. Diese Lösung kann an Ihr individuelles Szenario angepasst werden.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Eine Übersicht über die SQL-Datensynchronisierung finden Sie unter [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](sql-database-sync-data.md).

> [!IMPORTANT]
> Die Azure SQL-Datensynchronisierung unterstützt zurzeit **keine** verwalteten Azure SQL-Datenbank-Instanzen.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Überwachungsdashboard für alle Synchronisierungsgruppen 

Bei der Suche nach Problemen müssen Sie nun nicht mehr die Protokolle der einzelnen Synchronisierungsgruppen durchgehen. Mit einer benutzerdefinierten Azure Monitor-Ansicht können Sie alle Synchronisierungsgruppen aus all Ihren Abonnements zentral überwachen. Diese Ansicht enthält die Informationen, die für Kunden der SQL-Datensynchronisierung relevant sind.

![Dashboard zur Überwachung der Datensynchronisierung](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatische E-Mail-Benachrichtigungen

Die Protokolle müssen nicht mehr manuell im Azure-Portal, über PowerShell oder mithilfe der REST-API geprüft werden. Mit [Azure Monitor-Protokollen](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) können Sie Warnungen erstellen, die direkt an die E-Mail-Adressen der Benutzer gesendet werden, die beim Auftreten eines Fehlers informiert werden müssen.

![E-Mail-Benachrichtigungen für die Datensynchronisierung](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Wie richten Sie diese Überwachungsfunktionen ein? 

Mit den folgenden Schritten implementieren Sie in weniger als einer Stunde eine benutzerdefinierte Überwachungslösung mit Azure Monitor-Protokollen für die SQL-Datensynchronisierung:

Sie müssen drei Komponenten konfigurieren:

-   Ein PowerShell-Runbook zur Übertragung der Protokolldaten der SQL-Datensynchronisierung an Azure Monitor-Protokolle

-   Eine Azure Monitor-Warnung für E-Mail-Benachrichtigungen

-   Eine Azure Monitor-Ansicht für die Überwachung

### <a name="samples-to-download"></a>Beispiele zum Herunterladen

Laden Sie die beiden folgenden Beispiele herunter:

-   [PowerShell-Runbook für das Protokoll der Datensynchronisierung](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Azure Monitor-Ansicht für die Datensynchronisierung](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Voraussetzungen

Folgendes muss bereits eingerichtet sein:

-   Ein Azure Automation-Konto

-   Log Analytics-Arbeitsbereich

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell-Runbook zum Abrufen des Protokolls der SQL-Datensynchronisierung 

Verwenden Sie ein in Azure Automation gehostetes PowerShell-Runbook, um die Protokolldaten der SQL-Datensynchronisierung per Pullvorgang abzurufen und an Azure Monitor-Protokolle zu senden. Ein Beispielskript steht zur Verfügung. Sie benötigen ein Azure Automation-Konto. Anschließend müssen Sie ein Runbook erstellen und dessen Ausführung planen. 

### <a name="create-a-runbook"></a>Erstellen eines Runbooks

Weitere Informationen zum Erstellen eines Runbooks finden Sie unter [Mein erstes PowerShell-Runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Klicken Sie unter Ihrem Azure Automation-Konto unter „Prozessautomatisierung“ auf die Registerkarte **Runbooks**.

2.  Klicken Sie in der linken oberen Ecke der Seite „Runbooks“ auf **Runbook hinzufügen**.

3.  Klicken Sie auf **Vorhandenes Runbook importieren**.

4.  Verwenden Sie unter **Runbookdatei** die angegebene Datei `DataSyncLogPowerShellRunbook`. Legen Sie **Runbooktyp** auf `PowerShell` fest. Benennen Sie das Runbook.

5.  Klicken Sie auf **Erstellen**. Sie verfügen nun über ein Runbook.

6.  Klicken Sie unter Ihrem Azure Automation-Konto unter „Freigegebene Ressourcen“ auf die Registerkarte **Variablen**.

7.  Klicken Sie auf der Seite „Variablen“ auf **Variable hinzufügen**. Erstellen Sie eine Variable, um die letzte Ausführungszeit für das Runbook zu speichern. Wenn Sie über mehrere Runbooks verfügen, benötigen Sie für jedes Runbook eine eigene Variable.

8.  Legen Sie den Namen der Variablen auf `DataSyncLogLastUpdatedTime` und den Typ auf „DateTime“ fest.

9.  Wählen Sie das Runbook aus, und klicken Sie am oberen Seitenrand auf die Schaltfläche „Bearbeiten“.

10. Nehmen Sie die erforderlichen Änderungen für Ihr Konto und die Konfiguration der SQL-Datensynchronisierung vor. (Ausführlichere Informationen finden Sie im Beispielskript.)

    1.  Azure-Informationen

    2.  Informationen zur Synchronisierungsgruppe

    3.  Informationen in Azure Monitor-Protokollen Diese Informationen finden Sie im Azure-Portal unter „Einstellungen“ > „Verbundene Quellen“. Weitere Informationen zum Senden von Daten an Azure Monitor-Protokolle finden Sie unter [Senden von Daten an Azure Monitor-Protokolle mit der HTTP-Datensammler-API (Vorschauversion)](../azure-monitor/platform/data-collector-api.md).

11. Führen Sie das Runbook im Testbereich aus. Vergewissern Sie sich, dass es erfolgreich ausgeführt wurde.

    Sollten Fehler aufgetreten sein, vergewissern Sie sich, dass das neueste PowerShell-Modul installiert ist. Sie können das neueste PowerShell-Modul im **Modulkatalog** in Ihren Automation-Konto installieren.

12. Klicken Sie auf **Veröffentlichen**.

### <a name="schedule-the-runbook"></a>Planen des Runbooks

So planen Sie das Runbook:

1.  Klicken Sie unter dem Runbook unter „Ressourcen“ auf die Registerkarte **Zeitpläne**.

2.  Klicken Sie auf der Seite „Zeitpläne“ auf **Zeitplan hinzufügen**.

3.  Klicken Sie auf **Zeitplan mit Runbook verknüpfen**.

4.  Klicken Sie anschließend auf **Neuen Zeitplan erstellen**.

5.  Legen Sie **Serie** auf „Serie“ fest, und geben Sie das gewünschte Intervall an. Verwenden Sie stets dasselbe Intervall: hier, im Skript und in Azure Monitor-Protokollen.

6.  Klicken Sie auf **Erstellen**.

### <a name="check-the-automation"></a>Überprüfen der Automatisierung

Um zu prüfen, ob die Automatisierung erwartungsgemäß funktioniert, suchen Sie in der **Übersicht** für Ihr Automation-Konto unter **Überwachung** nach der Ansicht **Auftragsstatistik**. Heften Sie diese Ansicht zur einfacheren Betrachtung an Ihr Dashboard an. Erfolgreiche Ausführungen des Runbooks werden als „Abgeschlossen“ angezeigt, nicht erfolgreiche als „Fehler“.

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Erstellen einer Azure Monitor-Leserwarnung für E-Mail-Benachrichtigungen

Gehen Sie wie im Anschluss beschrieben vor, um eine auf Azure Monitor-Protokollen basierende Warnung zu erstellen. Hierzu müssen Azure Monitor-Protokolle bereits mit einem Log Analytics-Arbeitsbereich verknüpft sein.

1.  Wählen Sie im Azure-Portal die Option **Protokollsuche**.

2.  Erstellen Sie eine Abfrage, um die Fehler und Warnungen innerhalb des gewählten Intervalls nach Synchronisierungsgruppe auszuwählen. Beispiel:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Klicken Sie nach Ausführung der Abfrage auf das Glockensymbol mit dem Text **Warnung**.

4.  Wählen Sie unter **Warnung generieren basierend auf** die Option **Metrische Maßeinheit** aus.

    1.  Legen Sie den Aggregatwert auf **Größer als** fest.

    2.  Geben Sie nach **Größer als** den Schwellenwert ein, nach dessen Überschreitung Warnungen ausgegeben werden sollen. Bei der Datensynchronisierung ist mit vorübergehenden Fehlern zu rechnen. Um Rauschen zu vermindern, legen Sie den Schwellenwert auf 5 fest.

5.  Legen Sie unter **Aktionen** die Option **E-Mail-Benachrichtigung** auf „Ja“ fest. Geben Sie die gewünschten E-Mail-Empfänger ein.

6.  Klicken Sie auf **Speichern**. Die angegebenen Empfänger werden nun per E-Mail benachrichtigt, wenn Fehler auftreten.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Erstellen einer Azure Monitor-Ansicht für die Überwachung

In diesem Schritt erstellen Sie eine Azure Monitor-Ansicht für die visuelle Überwachung aller angegebenen Synchronisierungsgruppen. Die Ansicht umfasst mehrere Komponenten:

-   Eine Übersichtskachel mit der Anzahl von Fehlern, erfolgreichen Vorgängen und Warnungen aller Synchronisierungsgruppen.

-   Eine Kachel für alle Synchronisierungsgruppen mit der Anzahl von Fehlern und Warnungen pro Synchronisierungsgruppe. Gruppen ohne Probleme werden auf dieser Kachel nicht angezeigt.

-   Eine Kachel für jede Synchronisierungsgruppe mit der Anzahl von Fehlern, erfolgreichen Vorgängen und Warnungen sowie mit den neuesten Fehlermeldungen.

Gehen Sie zum Konfigurieren der Azure Monitor-Ansicht wie folgt vor:

1.  Wählen Sie links auf der Startseite des Log Analytics-Arbeitsbereichs das Pluszeichen aus, um den **Ansicht-Designer** zu öffnen.

2.  Klicken Sie auf der oberen Leiste des Ansicht-Designers auf **Importieren**. Wählen Sie die Beispieldatei „DataSyncLogOMSView“ aus.

3.  Die Beispielansicht ermöglicht die Verwaltung von zwei Synchronisierungsgruppen. Bearbeiten Sie diese Ansicht, um sie an Ihr Szenario anzupassen. Klicken Sie auf **Bearbeiten**, und nehmen Sie die folgenden Änderungen vor:

    1.  Erstellen Sie nach Bedarf neue Objekte vom Typ „Ring und Liste“.

    2.  Aktualisieren Sie die Abfragen der einzelnen Kacheln mit Ihren Informationen.

        1.  Legen Sie für jede Kachel das Intervall „TimeStamp_t“ wie gewünscht fest.

        2.  Aktualisieren Sie auf den Kacheln für die einzelnen Synchronisierungsgruppen die Namen der Synchronisierungsgruppen.

    3.  Aktualisieren Sie den Titel der einzelnen Kacheln nach Bedarf.

4.  Klicken Sie abschließend auf **Speichern**. Die Ansicht ist nun bereit.

## <a name="cost-of-this-solution"></a>Kosten dieser Lösung

In den meisten Fällen ist diese Lösung kostenlos.

**Azure Automation:** Je nach Verwendung fallen unter Umständen Kosten im Zusammenhang mit dem Azure Automation-Konto an. Pro Monat sind jeweils die ersten 500 Minuten der Auftragsausführungszeit kostenlos. In den meisten Fällen kommt diese Lösung mit weniger als 500 Minuten pro Monat aus. Verwenden Sie zur Vermeidung von Gebühren ein Runbook-Ausführungsintervall von mindestens zwei Stunden. Weitere Informationen finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).

**Azure Monitor-Protokolle:** Je nach Verwendung fallen unter Umständen Kosten für Azure Monitor-Protokolle an. Im Free-Tarif sind 500 MB an erfassten Daten pro Tag enthalten. In den meisten Fällen erfasst diese Lösung weniger als 500 MB pro Tag. Filtern Sie ggf. nur nach Fehlern, um den Verbrauch zu senken. Ein entsprechender Filter ist im Runbook enthalten. Sollten Sie pro Tag mehr als 500 MB benötigen, führen Sie ein Upgrade auf den kostenpflichtigen Tarif durch, um zu vermeiden, dass die Analyse bei Erreichen des Limits beendet wird. Weitere Informationen finden Sie unter [Preise für Azure Monitor-Protokolle](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Codebeispiele

Die in diesem Artikel beschriebenen Codebeispiele können Sie unter den folgenden Links herunterladen:

-   [PowerShell-Runbook für das Protokoll der Datensynchronisierung](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Azure Monitor-Ansicht für die Datensynchronisierung](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur SQL-Datensynchronisierung finden Sie unter:

-   Übersicht: [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](sql-database-sync-data.md)
-   Einrichten der Datensynchronisierung
    - Im Portal – [Tutorial: Einrichten der SQL-Datensynchronisierung zum Synchronisieren von Daten zwischen Azure SQL-Datenbank und lokalem SQL Server](sql-database-get-started-sql-data-sync.md)
    - Mit PowerShell
        -  [Verwenden von PowerShell zum Synchronisieren zwischen mehreren Azure SQL-Datenbanken](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Verwenden von PowerShell zum Synchronisieren zwischen einer Azure SQL-Datenbank und einer lokalen SQL Server-Datenbank](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Datensynchronisierungs-Agent: [Datensynchronisierungs-Agent für die Azure SQL-Datensynchronisierung](sql-database-data-sync-agent.md)
-   Bewährte Methoden: [Bewährte Methoden für die Azure SQL-Datensynchronisierung](sql-database-best-practices-data-sync.md)
-   Problembehandlung: [Behandeln von Problemen mit der Azure SQL-Datensynchronisierung](sql-database-troubleshoot-data-sync.md)
-   Aktualisieren des Synchronisierungsschemas
    -   Mit Transact-SQL: [Automatisieren der Replikation von Schemaänderungen in der Azure SQL-Datensynchronisierung](sql-database-update-sync-schema.md)
    -   Mit PowerShell: [Verwenden von PowerShell zum Aktualisieren des Synchronisierungsschemas in einer bestehenden Synchronisierungsgruppe](scripts/sql-database-sync-update-schema.md)

Weitere Informationen zu SQL-Datenbank finden Sie unter:

-   [Übersicht über die SQL-Datenbank](sql-database-technical-overview.md)
-   [Datenbank-Lebenszyklusverwaltung](https://msdn.microsoft.com/library/jj907294.aspx)
