---
title: Anleitung für die automatische Optimierung von E-Mail-Benachrichtigungen
description: Aktivieren Sie E-Mail-Benachrichtigungen zur automatischen Optimierung der Azure SQL-Datenbank.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/03/2019
ms.openlocfilehash: a373a28a180b2a6c72f6a291b9d1437a2e88d9ff
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500953"
---
# <a name="email-notifications-for-automatic-tuning"></a>E-Mail-Benachrichtigungen zur automatischen Optimierung
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Die Optimierungsempfehlungen für Azure SQL-Datenbank werden mit dem Feature [Automatische Optimierung](automatic-tuning-overview.md) von Azure SQL-Datenbank generiert. Diese Lösung führt eine kontinuierliche Überwachung und Analyse der Workloads von Datenbanken durch und stellt Optimierungsempfehlungen für jede einzelne Datenbank im Hinblick auf Indexerstellung, Indexlöschung und Optimierung von Abfrageausführungsplänen bereit.

Sie können die Empfehlungen zur automatischen Optimierung von Azure SQL-Datenbank im [Azure-Portal](database-advisor-find-recommendations-portal.md) anzeigen oder mit [REST-API](/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor)-Aufrufen oder [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)- und [PowerShell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction)-Befehlen abrufen. In diesem Artikel wird ein PowerShell-Skript zum Abrufen der Empfehlungen zur automatischen Optimierung verwendet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatisieren von E-Mail-Benachrichtigungen für Empfehlungen zur automatischen Optimierung

Mit der folgenden Lösung wird das Senden von E-Mail-Benachrichtigungen automatisiert, die Empfehlungen zur automatischen Optimierung enthalten. Die beschriebene Lösung umfasst die automatische Ausführung eines PowerShell-Skripts zum Abrufen von Optimierungsempfehlungen mithilfe von [Azure Automation](../../automation/automation-intro.md) und die Automatisierung der Planung der E-Mail-Übermittlung unter Verwendung von [Microsoft Flow ](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Erstellen eines Azure Automation-Kontos

Um Azure Automation verwenden zu können, müssen Sie zunächst ein Automation-Konto erstellen und dieses mit Azure-Ressourcen konfigurieren, um damit das PowerShell-Skript auszuführen. Weitere Informationen zu Azure Automation und den enthaltenen Funktionen finden Sie unter [Erste Schritte mit Azure Automation](../../automation/index.yml).

Führen Sie diese Schritte aus, um ein Azure Automation-Konto zu erstellen, indem Sie im Azure Marketplace eine Automation-App auswählen und konfigurieren:

1. Melden Sie sich im Azure-Portal an.
1. Klicken Sie in der linken oberen Ecke auf **+ Ressource erstellen**.
1. Suchen Sie nach **Automation** (drücken Sie die EINGABETASTE).
1. Klicken Sie in den Suchergebnissen auf die Automation-App.

    ![Hinzufügen von Azure Automation](./media/automatic-tuning-email-notifications-configure/howto-email-01.png)

1. Klicken Sie innerhalb des Bereichs „Automation-Konto erstellen“ auf **Erstellen**.
1. Geben Sie die erforderlichen Informationen an: Geben Sie einen Namen für dieses Automation-Konto ein, und wählen Sie Ihre Azure-Abonnement-ID und die Azure-Ressourcen aus, die für die Ausführung des PowerShell-Skripts verwendet werden sollen.
1. Wählen Sie für **Ausführendes Azure-Konto erstellen** die Option **Ja** aus, um den Typ des Kontos, unter dem das PowerShell-Skript mithilfe von Azure Automation ausgeführt wird, zu konfigurieren. Weitere Informationen zu den Kontotypen finden Sie unter [Ausführendes Konto](../../automation/manage-runas-account.md).
1. Klicken Sie auf **Erstellen**, um die Erstellung des Automation-Kontos abzuschließen.

> [!TIP]
> Notieren Sie den Azure Automation-Kontonamen, die Abonnement-ID und die Ressourcen (z.B. durch Kopieren und Einfügen in einen Editor) genau wie bei der Eingabe während des Erstellens der Automation-App. Sie benötigen diese Informationen später.

Wenn Sie über mehrere Azure-Abonnements verfügen, für die Sie die gleiche Automatisierung einrichten möchten, müssen Sie diesen Vorgang für Ihre anderen Abonnements wiederholen.

## <a name="update-azure-automation-modules"></a>Aktualisieren von Azure Automation-Modulen

Das PowerShell-Skript zum Abrufen der Empfehlungen zur automatischen Optimierung verwendet die Befehle [Get-AzResource](/powershell/module/az.Resources/Get-azResource) und [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction), für die mindestens Version 4 des Azure-Moduls erforderlich ist.

- Falls Sie Ihre Azure-Module aktualisieren müssen, helfen Ihnen die Informationen unter [Unterstützung für Az-Module in Azure Automation](../../automation/shared-resources/modules.md) weiter.

## <a name="create-azure-automation-runbook"></a>Erstellen eines Azure Automation-Runbooks

Der nächste Schritt ist das Erstellen eines Runbooks in Azure Automation, in dem sich das PowerShell-Skript für den Abruf von Optimierungsempfehlungen befindet.

Führen Sie diese Schritte aus, um ein neues Azure Automation-Runbook zu erstellen:

1. Greifen Sie auf das Azure Automation-Konto zu, das Sie im vorherigen Schritt erstellt haben.
1. Klicken Sie im Bereich des Automation-Kontos auf der linken Seite auf das Menüelement **Runbooks**, um ein neues Azure Automation-Runbook mit dem PowerShell-Skript zu erstellen. Weitere Informationen zum Erstellen von Automation-Runbooks finden Sie unter [Erstellen eines Runbooks](../../automation/manage-runbooks.md#create-a-runbook).
1. Klicken Sie auf die Menüoption **+ Runbook hinzufügen** und dann auf **Schnelles Erstellen – Neues Runbook erstellen**, um ein neues Runbook hinzuzufügen.
1. Geben Sie im Bereich „Runbook“ den Namen Ihres Runbooks ein (in diesem Beispiel wird **AutomaticTuningEmailAutomation** verwendet). Wählen Sie als Typ für das Runbook **PowerShell** aus, und geben Sie eine Beschreibung mit dem Zweck dieses Runbooks an.
1. Klicken Sie auf die Schaltfläche **Erstellen**, um die Erstellung des neuen Runbooks abzuschließen.

    ![Hinzufügen eines Azure Automation-Runbooks](./media/automatic-tuning-email-notifications-configure/howto-email-03.png)

Führen Sie diese Schritte aus, um ein PowerShell-Skript in das erstellte Runbook zu laden:

1. Klicken Sie im Bereich **PowerShell-Runbook bearbeiten** in der Menüstruktur auf **RUNBOOKS**, und erweitern Sie die Ansicht, bis der Name Ihres Runbooks angezeigt wird (in diesem Beispiel  **AutomaticTuningEmailAutomation**). Wählen Sie dieses Runbook aus.
1. Fügen Sie den folgenden PowerShell-Skriptcode durch Kopieren und Einfügen in der ersten Zeile von „PowerShell-Runbook bearbeiten“ (beginnt mit der Zahl 1) ein. Dieses PowerShell-Skript wird unverändert bereitgestellt, um Ihnen den Einstieg zu erleichtern. Passen Sie das Skript an Ihre Anforderungen an.

Sie müssen im Header des bereitgestellten PowerShell-Skripts `<SUBSCRIPTION_ID_WITH_DATABASES>` durch Ihre Azure-Abonnement-ID ersetzen. Informationen zum Abrufen der Azure-Abonnement-ID finden Sie unter [Abrufen Ihrer Azure-Abonnement-GUID](/archive/blogs/mschray/getting-your-azure-subscription-guid-new-portal).

Bei mehreren Abonnements können Sie diese durch Kommas getrennt in der „$subscriptions“-Eigenschaft im Header des Skripts hinzufügen.

```powershell
# PowerShell script to retrieve Azure SQL Database automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
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

                # Loop through all automatic tuning recommendation types
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

![Ausführung der Empfehlungen für die automatische Optimierung mit Azure Automation](./media/automatic-tuning-email-notifications-configure/howto-email-04.png)

Passen Sie den Inhalt unbedingt an, indem Sie das PowerShell-Skript Ihrem Bedarf entsprechend ändern.

Mit den obigen Schritten wird das PowerShell-Skript zum Abrufen von Empfehlungen zur automatischen Optimierung in Azure Automation geladen. Im nächsten Schritt automatisieren und planen Sie den Auftrag zur E-Mail-Übermittlung.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatisieren der E-Mail-Aufträge mit Microsoft Flow

Zum Abschließen der Lösung erstellen Sie im letzten Schritt einen Automation-Flow in Microsoft Flow bestehend aus drei Aktionen (Aufträgen):

- **Azure Automation – Auftrag erstellen:** zum Ausführen des PowerShell-Skripts, mit dem die Empfehlungen zur automatischen Optimierung im Azure Automation-Runbook abgerufen werden.
- **Azure Automation – Auftragsausgabe abrufen:** zum Abrufen der Ausgabe des ausgeführten PowerShell-Skripts.
- **Office 365 Outlook – E-Mail senden:** zum Senden einer E-Mail E-Mails werden mit dem Geschäfts-, Schul- oder Unikonto der Person gesendet, die den Flow erstellt hat.

Weitere Informationen zu den Funktionen von Microsoft Flow finden Sie unter [Erste Schritte mit Microsoft Flow](/flow/getting-started).

Voraussetzung für diesen Schritt ist die Registrierung und Anmeldung bei einem [Microsoft Flow](https://flow.microsoft.com)-Konto. Führen Sie in der Lösung die folgenden Schritte aus, um einen **neuen Flow** einzurichten:

1. Klicken Sie auf das Menüelement **Meine Flows**.
1. Klicken Sie in „Meine Flows“ oben auf der Seite auf den Link **+ Ohne Vorlage erstellen**.
1. Klicken Sie unten auf der Seite auf den Link zur **Suche nach Hunderten von Connectors und Triggern**.
1. Geben Sie in das Suchfeld **Wiederholung** ein, und wählen Sie in den Suchergebnissen **Zeitplan: Wiederholung** aus, um die Ausführung des Auftrags zur E-Mail-Übermittlung zu planen.
1. Wählen Sie im Bereich „Serie“ im Feld „Häufigkeit“ die geplante Häufigkeit (z.B. automatisierte E-Mail pro Minute, Stunde, Tag, Woche usw. senden) für die Ausführung dieses Flows aus.

Im nächsten Schritt werden dem neu erstellten Flow drei Aufträge (Erstellen, Abrufen der Ausgabe und Senden der E-Mail) hinzugefügt. Führen Sie zum Hinzufügen der erforderlichen Aufträge zum Flow die folgenden Schritte aus:

1. Erstellen Sie die Aktion für das Ausführen des PowerShell-Skripts zum Abrufen von Optimierungsempfehlungen.

   - Klicken Sie auf **+ Neuer Schritt** und dann im Bereich „Wiederholung“ des Flows auf die Option **Aktion hinzufügen**.
   - Geben Sie in das Suchfeld **Automation** ein, und wählen Sie in den Suchergebnissen **Azure Automation – Auftrag erstellen** aus.
   - Konfigurieren Sie im Bereich „Auftrag erstellen“ die Auftragseigenschaften. Bei dieser Konfiguration benötigen Sie Details Ihrer Azure-Abonnement-ID, der Ressourcengruppe und des Automation-Kontos, die Sie im Bereich **Automation-Konto** zuvor **notiert haben**. Weitere Informationen zu den in diesem Abschnitt verfügbaren Optionen finden Sie unter [Azure Automation – Auftrag erstellen](/connectors/azureautomation/#create-job).
   - Schließen Sie das Erstellen der Aktion ab, indem Sie auf **Flow speichern** klicken.

2. Erstellen Sie eine Aktion, um die Ausgabe des ausgeführten PowerShell-Skripts abzurufen.

   - Klicken Sie auf **+ Neuer Schritt** und dann im Bereich „Wiederholung“ des Flows auf die Option **Aktion hinzufügen**.
   - Geben Sie in das Suchfeld **Automation** ein, und wählen Sie in den Suchergebnissen **Azure Automation – Auftragsausgabe abrufen** aus. Weitere Informationen zu den in diesem Abschnitt verfügbaren Optionen finden Sie unter [Azure Automation – Auftragsausgabe abrufen](/connectors/azureautomation/#get-job-output).
   - Geben Sie in die erforderlichen Felder (ähnlich wie bei der vorherigen Auftragserstellung) Ihre Azure-Abonnement-ID, die Ressourcengruppe und ein Automation-Konto (wie im Bereich „Automation-Konto“ eingegeben) ein.
   - Klicken Sie in das Feld **Auftrags-ID**, um das Menü **Dynamischer Inhalt** anzuzeigen. Klicken Sie in diesem Menü auf die Option **Auftrags-ID**.
   - Schließen Sie das Erstellen der Aktion ab, indem Sie auf **Flow speichern** klicken.

3. Erstellen einer Aktion zum Versenden von E-Mail-Nachrichten mithilfe der Office 365-Integration

   - Klicken Sie auf **+ Neuer Schritt** und dann im Bereich „Wiederholung“ des Flows auf die Option **Aktion hinzufügen**.
   - Geben Sie in das Suchfeld **E-Mail senden** ein, und wählen Sie in den Suchergebnissen **Office 365 Outlook – E-Mail senden** aus.
   - Geben Sie in das Feld **An** die E-Mail-Adresse ein, an die Sie die Benachrichtigungs-E-Mail senden möchten.
   - Geben Sie in das Feld **Betreff** den Betreff der E-Mail ein, z. B. „E-Mail-Benachrichtigung mit Empfehlungen zur automatischen Optimierung“.
   - Klicken Sie in das Feld **Text**, um das Menü **Dynamischer Inhalt** anzuzeigen. Wählen Sie in diesem Menü unter **Auftragsausgabe abrufen** die Option **Inhalt** aus.
   - Schließen Sie das Erstellen der Aktion ab, indem Sie auf **Flow speichern** klicken.

> [!TIP]
> Um automatisierte E-Mail-Nachrichten an verschiedene Empfänger zu senden, erstellen Sie separate Flows. Ändern Sie in diesen zusätzlichen Flows die E-Mail-Adresse des Empfängers im Feld „An“ und die Betreffzeile der E-Mail im Feld „Betreff“. Die Erstellung neuer Runbooks in Azure Automation mit angepassten PowerShell-Skripts (z. B. durch Ändern der Azure-Abonnement-ID) ermöglicht eine weitere Anpassung von Automatisierungsszenarien. Sie können beispielsweise E-Mails mit Empfehlungen zur automatischen Optimierung an verschiedene Empfänger für unterschiedliche Abonnements senden.

Die oben durchgeführten Schritte dienen dem Konfigurieren des Workflows für den Auftrag zur E-Mail-Übermittlung. Der gesamte Flow – bestehend aus drei erstellten Aktionen – wird in der folgenden Abbildung dargestellt.

![Flow für E-Mail-Benachrichtigungen zur automatischen Optimierung](./media/automatic-tuning-email-notifications-configure/howto-email-05.png)

Klicken Sie im Flowbereich in der rechten oberen Ecke auf **Jetzt ausführen**, um den Flow zu testen.

Statistiken zur Ausführung der automatisierten Aufträge zeigen den Erfolg der gesendeten E-Mail-Benachrichtigungen. Sie können im Analysebereich des Flows angezeigt werden.

![Flowausführung für E-Mail-Benachrichtigungen zur automatischen Optimierung](./media/automatic-tuning-email-notifications-configure/howto-email-06.png)

Der Bereich für die Flowanalyse ist hilfreich, um den erfolgreichen Ablauf für Auftragsausführungen zu überwachen und mögliche Probleme zu erkennen.  Bei der Problembehandlung können Sie auch das Ausführungsprotokoll des PowerShell-Skripts untersuchen, auf das Sie über die Azure Automation-App zugreifen können.

Die finale Ausgabe der automatisierten E-Mail sieht in etwa wie die folgende E-Mail aus, die nach dem Erstellen und Ausführen dieser Lösung empfangen wurde:

![Beispiel für die E-Mail-Ausgabe bei E-Mail-Benachrichtigungen zur automatischen Optimierung](./media/automatic-tuning-email-notifications-configure/howto-email-07.png)

Durch Anpassen des PowerShell-Skripts können Sie die Ausgabe und die Formatierung der automatisierten E-Mails an Ihre Bedürfnisse anpassen.

Sie können die Lösung weiter anpassen, um E-Mail-Benachrichtigungen basierend auf einem bestimmten Optimierungsereignis zu erstellen, die abhängig von Ihren eigenen Szenarien an mehrere Empfänger für unterschiedliche Abonnements oder für Datenbanken gesendet werden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr darüber, wie die automatische Optimierung dabei hilft, die Datenbankleistung zu steigern: [Automatische Optimierung in Azure SQL-Datenbank](automatic-tuning-overview.md).
- Informationen zum Aktivieren der automatischen Optimierung in Azure SQL-Datenbank und zum Verwalten Ihrer Workload finden Sie unter [Aktivieren der automatischen Optimierung](automatic-tuning-enable.md).
- Informationen zum manuellen Überprüfen und Anwenden von Empfehlungen der automatischen Optimierung finden Sie unter [Suchen und Anwenden von Empfehlungen zur Leistung](database-advisor-find-recommendations-portal.md).