---
title: Anleitung für die automatische Optimierung von E-Mail-Benachrichtigungen – Azure SQL-Datenbank | Microsoft-Dokumentation
description: Aktivieren Sie E-Mail-Benachrichtigungen zur automatischen Optimierung der Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 2af8ef7d29d1ac506ddca654544bc938758aa0d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66149860"
---
# <a name="email-notifications-for-automatic-tuning"></a>E-Mail-Benachrichtigungen zur automatischen Optimierung

Die Optimierungsempfehlungen für SQL-Datenbank werden mit dem Feature [Automatische Optimierung](sql-database-automatic-tuning.md) von Azure SQL-Datenbank generiert. Diese Lösung überwacht und analysiert kontinuierlich die Workloads von SQL-Datenbanken und stellt Optimierungsempfehlungen für jede einzelne Datenbank im Hinblick auf Indexerstellung, Indexlöschung und Optimierung von Abfrageausführungsplänen bereit.

Sie können die Empfehlungen zur automatischen Optimierung von SQL-Datenbank im [Azure-Portal](sql-database-advisor-portal.md) anzeigen oder mit [REST-API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor)-Aufrufen oder [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)- und [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)-Befehlen abrufen. In diesem Artikel wird ein PowerShell-Skript zum Abrufen der Empfehlungen zur automatischen Optimierung verwendet.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von der Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und in den AzureRm-Modulen sind im Wesentlichen identisch.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatisieren von E-Mail-Benachrichtigungen für Empfehlungen zur automatischen Optimierung

Die folgende Lösung automatisiert das Senden von E-Mail-Benachrichtigungen, die Empfehlungen der automatischen Optimierung enthalten. Die beschriebene Lösung umfasst die automatische Ausführung eines PowerShell-Skripts zum Abrufen von Optimierungsempfehlungen mithilfe von [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) und die Automatisierung der Planung der E-Mail-Übermittlung unter Verwendung von [Microsoft Flow ](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Erstellen eines Azure Automation-Kontos

Um Azure Automation verwenden zu können, müssen Sie zunächst ein Automation-Konto erstellen und dieses mit Azure-Ressourcen konfigurieren, um damit das PowerShell-Skript auszuführen. Weitere Informationen zu Azure Automation und den enthaltenen Funktionen finden Sie unter [Erste Schritte mit Azure Automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Führen Sie diese Schritte aus, um ein Azure Automation-Konto zu erstellen, indem Sie im Marketplace die Automation-App auswählen und konfigurieren:

- Melden Sie sich beim Azure-Portal an.
- Klicken Sie in der linken oberen Ecke auf **+ Ressource erstellen**.
- Suchen Sie nach **Automation** (drücken Sie die EINGABETASTE).
- Klicken Sie in den Suchergebnissen auf die Automation-App.

![Hinzufügen von Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Klicken Sie innerhalb des Bereichs „Erstellen eines Automation-Kontos“ auf **Erstellen**.
- Geben Sie die erforderliche Informationen an: Geben Sie einen Namen für dieses Automation-Konto ein, und wählen Sie Ihre Azure-Abonnement-ID und die Azure-Ressourcen, die für die Ausführung des PowerShell-Skripts verwendet werden sollen, aus.
- Wählen Sie für **Ausführendes Azure-Konto erstellen** die Option **Ja** aus, um den Typ des Kontos, unter dem das PowerShell-Skript mithilfe von Azure Automation ausgeführt wird, zu konfigurieren. Weitere Informationen zu den Kontotypen finden Sie unter [Ausführendes Konto](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
- Schließen Sie die Erstellung des Automation-Kontos durch Klicken auf **Erstellen** ab.

> [!TIP]
> Notieren Sie den Azure Automation-Kontonamen, die Abonnement-ID und die Ressourcen (z.B. durch Kopieren und Einfügen in einen Editor) genau wie bei der Eingabe während des Erstellens der Automation-App. Sie benötigen diese Informationen später.
>

Wenn Sie über mehrere Azure-Abonnements verfügen, für die Sie die gleiche Automatisierung einrichten möchten, müssen Sie diesen Vorgang für Ihre anderen Abonnements wiederholen.

## <a name="update-azure-automation-modules"></a>Aktualisieren von Azure Automation-Modulen

Das PowerShell-Skript zum Abrufen der Empfehlungen zur automatischen Optimierung verwendet die Befehle [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) und [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction), für die ein Update der Azure-Module auf Version 4 oder höher erforderlich ist.

Befolgen Sie die folgenden Schritte zum Aktualisieren von Azure PowerShell-Modulen:

- Wählen Sie im Automation-App-Bereich im linken Menü die Option **Module** aus (scrollen Sie nach unten, das Menü befindet sich unter „Freigegebene Ressourcen“).
- Klicken Sie im Bereich „Module“ oben auf **Azure-Module aktualisieren**, und warten Sie, bis die Meldung „Die Azure-Module wurden aktualisiert“ angezeigt wird. Dieser Vorgang kann einige Minuten dauern.

![Aktualisieren von Azure Automation-Modulen](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

## <a name="create-azure-automation-runbook"></a>Erstellen eines Azure Automation-Runbooks

Der nächste Schritt ist das Erstellen eines Runbooks in Azure Automation, in dem sich das PowerShell-Skript für den Abruf von Optimierungsempfehlungen befindet.

Führen Sie diese Schritte aus, um ein neues Azure Automation-Runbook zu erstellen:

- Greifen Sie auf das Azure Automation-Konto zu, das Sie im vorherigen Schritt erstellt haben.
- Klicken Sie im Bereich des Automation-Kontos auf der linken Seite auf das Menüelement **Runbooks**, um ein neues Azure Automation-Runbook mit dem PowerShell-Skript zu erstellen. Weitere Informationen zum Erstellen von Automation-Runbooks finden Sie unter [Erstellen eines neuen Runbooks](../automation/manage-runbooks.md#create-a-runbook).
- Um ein neues Runbook hinzuzufügen, klicken Sie auf die Menüoption **+ Runbook hinzufügen** und dann auf **Schnellerfassung – Neues Runbook erstellen**.
- Geben Sie im Bereich „Runbook“ den Namen Ihres Runbooks ein (in diesem Beispiel wird **AutomaticTuningEmailAutomation** verwendet). Wählen Sie als Typ des Runbooks **PowerShell** aus, und geben Sie eine Beschreibung mit dem Zweck dieses Runbooks an.
- Klicken Sie auf die Schaltfläche **Erstellen**, um die Erstellung des neuen Runbooks abzuschließen.

![Hinzufügen eines Azure Automation-Runbooks](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Führen Sie diese Schritte aus, um ein PowerShell-Skript in das erstellte Runbook zu laden:

- Wählen Sie im Bereich **PowerShell-Runbook bearbeiten** in der Menüstruktur **RUNBOOKS** aus, und erweitern Sie die Ansicht, bis der Name Ihres Runbooks angezeigt wird (in diesem Beispiel  **AutomaticTuningEmailAutomation**). Wählen Sie dieses Runbook aus.
- Fügen Sie den folgenden PowerShell-Skriptcode durch Kopieren und Einfügen in der ersten Zeile von „PowerShell-Runbook bearbeiten“ (beginnt mit der Zahl 1) ein. Dieses PowerShell-Skript wird unverändert bereitgestellt, um Ihnen den Einstieg zu erleichtern. Passen Sie das Skript an Ihre Anforderungen an.

Sie müssen im Header des bereitgestellten PowerShell-Skripts `<SUBSCRIPTION_ID_WITH_DATABASES>` durch Ihre Azure-Abonnement-ID ersetzen. Informationen zum Abrufen der Azure-Abonnement-ID finden Sie unter [Abrufen Ihrer Azure-Abonnement-GUID](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

Bei mehreren Abonnements können Sie diese durch Trennzeichen getrennt in der $subscriptions-Eigenschaft im Header des Skripts hinzufügen.

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Klicken Sie auf die Schaltfläche **Speichern** in der rechten oberen Ecke, um das Skript zu speichern. Wenn Sie mit dem Skript zufrieden sind, klicken Sie auf die Schaltfläche **Veröffentlichen**, um dieses Runbook zu veröffentlichen.

Sie können im Hauptbereich des Runbooks auf die Schaltfläche **Starten** klicken, um das Skript zu **testen**. Klicken Sie auf **Ausgabe**, um die Ergebnisse der Skriptausführung anzuzeigen. Diese Ausgabe wird der Inhalt Ihrer E-Mail sein. Eine Beispielausgabe vom Skript sehen Sie im folgenden Screenshot.

![Ausführung der Empfehlungen für die automatische Optimierung mit Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Passen Sie den Inhalt unbedingt an, indem Sie das PowerShell-Skript Ihrem Bedarf entsprechend ändern.

Mit den obigen Schritten wird das PowerShell-Skript zum Abrufen von Empfehlungen zur automatischen Optimierung in Azure Automation geladen. Im nächsten Schritt automatisieren und planen Sie den Auftrag zur E-Mail-Übermittlung.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatisieren der E-Mail-Aufträge mit Microsoft Flow

Zum Abschließen der Lösung erstellen Sie im letzten Schritt einen Automation-Flow in Microsoft Flow bestehend aus drei Aktionen (Aufträgen):

1. **Azure Automation – Auftrag erstellen:** zum Ausführen des PowerShell-Skripts, mit dem die Empfehlungen zur automatischen Optimierung im Azure Automation-Runbook abgerufen werden
2. **Azure Automation – Auftragsausgabe abrufen:** zum Abrufen der Ausgabe des ausgeführten PowerShell-Skripts
3. **Office 365 Outlook – E-Mail senden:** zum Senden einer E-Mail-Nachricht. E-Mails werden mit dem Office 365-Konto der Person gesendet, die den Flow erstellt hat.

Weitere Informationen zu den Funktionen von Microsoft Flow finden Sie unter [Erste Schritte mit Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Voraussetzung für diesen Schritt ist die Registrierung und Anmeldung beim [Microsoft Flow](https://flow.microsoft.com)-Konto. Führen Sie in der Lösung die folgenden Schritte aus, um einen **neuen Flow** einzurichten:

- Wählen Sie das Menüelement **Meine Flows** aus.
- Wählen Sie in „Meine Flows“ oben auf der Seite den Link **+ Ohne Vorlage erstellen** aus.
- Klicken Sie am unteren Rand der Seite auf den Link **Search for hundreds of connectors and triggers** (Hunderte von Connectors und Triggern suchen).
- Geben Sie im Suchfeld **Serie** ein, und wählen Sie in den Suchergebnissen **Zeitplan – Serie** aus, um die Ausführung des Auftrags zur E-Mail-Übermittlung zu planen.
- Wählen Sie im Bereich „Serie“ im Feld „Häufigkeit“ die geplante Häufigkeit (z.B. automatisierte E-Mail pro Minute, Stunde, Tag, Woche usw. senden) für die Ausführung dieses Flows aus.

Im nächsten Schritt werden dem neu erstellten Flow drei Aufträge (Erstellen, Abrufen der Ausgabe und Senden der E-Mail) hinzugefügt. Führen Sie zum Hinzufügen der erforderlichen Aufträge zum Flow die folgenden Schritte aus:

1. Erstellen Sie die Aktion für das Ausführen des PowerShell-Skripts zum Abrufen von Optimierungsempfehlungen.

   - Wählen Sie **+ Neuer Schritt** und dann im Bereich des Flows „Serie“ die Option **Aktion hinzufügen** aus.
   - Geben Sie im Suchfeld **Automation** ein, und wählen Sie in den Suchergebnissen **Azure Automation – Auftrag erstellen** aus.
   - Konfigurieren Sie im Bereich „Auftrag erstellen“ die Auftragseigenschaften. Bei dieser Konfiguration benötigen Sie Details Ihrer Azure-Abonnement-ID, der Ressourcengruppe und des Automation-Kontos, die Sie im Bereich **Automation-Konto** zuvor **notiert haben**. Weitere Informationen zu den in diesem Abschnitt verfügbaren Optionen finden Sie unter [Azure Automation – Auftrag erstellen](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Schließen Sie diese Aktion ab, indem Sie auf **Flow speichern** klicken.

2. Erstellen Sie eine Aktion, um die Ausgabe des ausgeführten PowerShell-Skripts abzurufen.

   - Wählen Sie **+ Neuer Schritt** und dann im Bereich des Flows „Serie“ die Option **Aktion hinzufügen** aus.
   - Geben Sie im Suchfeld **Automation** ein, und wählen Sie in den Suchergebnissen **Azure Automation – Auftragsausgabe abrufen** aus. Weitere Informationen zu den in diesem Abschnitt verfügbaren Optionen finden Sie unter [Azure Automation – Auftragsausgabe abrufen](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Geben Sie in die erforderlichen Felder (ähnlich wie bei der vorherigen Auftragserstellung) Ihre Azure-Abonnement-ID, die Ressourcengruppe und ein Automation-Konto (wie im Bereich „Automation-Konto“ eingegeben) ein.
   - Klicken Sie in das Feld **Auftrags-ID**, um das Menü **Dynamischer Inhalt** anzuzeigen. Wählen Sie in diesem Menü die Option **Auftrags-ID** aus.
   - Schließen Sie diese Aktion ab, indem Sie auf **Flow speichern** klicken.

3. Erstellen Sie eine Aktion zum Versenden von E-Mail-Nachrichten mithilfe der Office 365-Integration.

   - Wählen Sie **+ Neuer Schritt** und dann im Bereich des Flows „Serie“ die Option **Aktion hinzufügen** aus.
   - Geben Sie im Suchfeld **E-Mail senden** ein, und wählen Sie in den Suchergebnissen **Office 365 Outlook – E-Mail senden** aus.
   - Geben Sie im Feld **An** die E-Mail-Adresse ein, an die Sie die Benachrichtigungs-E-Mail senden möchten.
   - Geben Sie im Feld **Betreff** den Betreff der E-Mail ein, z.B. „E-Mail-Benachrichtigung mit Empfehlungen zur automatischen Optimierung“.
   - Klicken Sie in das Feld **Text**, um das Menü **Dynamischer Inhalt** anzuzeigen. Wählen Sie in diesem Menü unter **Auftragsausgabe abrufen** die Option **Inhalt** aus.
   - Schließen Sie diese Aktion ab, indem Sie auf **Flow speichern** klicken.

> [!TIP]
> Um automatisierte E-Mail-Nachrichten an verschiedene Empfänger zu senden, erstellen Sie separate Flows. Ändern Sie in diesen zusätzlichen Flows die E-Mail-Adresse des Empfängers im Feld „An“ und die Betreffzeile der E-Mail im Feld „Betreff“. Durch das Erstellen neuer Runbooks in Azure Automation mit angepassten PowerShell-Skripts (z.B. durch Ändern der Azure-Abonnement-ID) können Sie weitere angepasste Automatisierungsszenarien erstellen. So können Sie z.B. E-Mails mit Empfehlungen zur automatischen Optimierung an verschiedene Empfänger für unterschiedliche Abonnements senden.
>

Die oben durchgeführten Schritte dienen dem Konfigurieren des Workflows für den Auftrag zur E-Mail-Übermittlung. Der gesamte Flow – bestehend aus drei erstellten Aktionen – wird in der folgenden Abbildung dargestellt.

![Flow für E-Mail-Benachrichtigungen zur automatischen Optimierung](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Um den Datenfluss zu testen, klicken Sie im Flowbereich in der rechten oberen Ecke auf **Jetzt ausführen**.

Statistiken zur Ausführung der automatisierten Aufträge zeigen den Erfolg der gesendeten E-Mail-Benachrichtigungen. Sie können im Analysebereich des Flows angezeigt werden.

![Flowausführung für E-Mail-Benachrichtigungen zur automatischen Optimierung](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Die Flowanalyse ist hilfreich für die Überwachung des Erfolgs von Auftragsausführungen und um mögliche Probleme zu erkennen.  Bei der Problembehandlung können Sie auch das Ausführungsprotokoll des PowerShell-Skripts untersuchen, auf das Sie über die Azure Automation-App zugreifen können.

Die finale Ausgabe der automatisierten E-Mail sieht in etwa wie die folgende E-Mail aus, die nach dem Erstellen und Ausführen dieser Lösung empfangen wurde:

![Beispiel für die E-Mail-Ausgabe bei E-Mail-Benachrichtigungen zur automatischen Optimierung](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Durch Anpassen des PowerShell-Skripts können Sie die Ausgabe und die Formatierung der automatisierten E-Mails an Ihre Bedürfnisse anpassen.

Sie können die Lösung weiter anpassen, um E-Mail-Benachrichtigungen basierend auf einem bestimmten Optimierungsereignis zu erstellen, die abhängig von Ihren eigenen Szenarien an mehrere Empfänger für unterschiedliche Abonnements oder für Datenbanken gesendet werden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr darüber, wie die automatische Optimierung dabei hilft, die Datenbankleistung zu steigern: [Automatische Optimierung in Azure SQL-Datenbank](sql-database-automatic-tuning.md).
- Informationen zum Aktivieren der automatischen Optimierung in Azure SQL-Datenbank und zum Verwalten Ihrer Workload finden Sie unter [Aktivieren der automatischen Optimierung](sql-database-automatic-tuning-enable.md).
- Informationen zum manuellen Überprüfen und Anwenden von Empfehlungen der automatischen Optimierung finden Sie unter [Suchen und Anwenden von Empfehlungen zur Leistung](sql-database-advisor-portal.md).
