---
title: Sammeln von Log Analytics-Daten mit einem Runbook in Azure Automation | Microsoft-Dokumentation
description: In diesem Tutorial wird Schritt für Schritt erläutert, wie Sie ein Runbook in Azure Automation erstellen, um Daten im Repository für die Analyse durch Log Analytics zu sammeln.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 67378a5911e5bd83888342aa3773f7f5ed4ccf29
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60454105"
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Sammeln von Daten in Log Analytics mit einem Azure Automation-Runbook

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Sie können in Log Analytics eine erhebliche Menge an Daten aus unterschiedlichen Quellen sammeln, einschließlich [Datenquellen](../../azure-monitor/platform/agent-data-sources.md) auf Agents sowie [von Azure gesammelte Daten](../../azure-monitor/platform/collect-azure-metrics-logs.md). Es gibt jedoch Fälle, in denen Sie Daten sammeln müssen, auf die Sie über diese Standardquellen keinen Zugriff erhalten. Sie können in diesen Situationen die [HTTP-Datensammler-API](../../azure-monitor/platform/data-collector-api.md) verwenden, um Daten von einem REST-API-Client an Log Analytics zu senden. Eine gängige Methode zum Durchführen dieser Datensammlung stellt die Verwendung eines Runbooks in Azure Automation dar.

Dieses Tutorial führt Sie durch das Erstellen und Planen eines Runbooks in Azure Automation zum Schreiben von Daten in Log Analytics.

## <a name="prerequisites"></a>Voraussetzungen
Dieses Szenario erfordert die Konfiguration der folgenden Ressourcen in Ihrem Azure-Abonnement. Beide können kostenlose Konten sein.

- [Log Analytics-Arbeitsbereich](../../azure-monitor/learn/quick-create-workspace.md)
- [Azure Automation-Konto](../..//automation/automation-quickstart-create-account.md)

## <a name="overview-of-scenario"></a>Übersicht über das Szenario
Für dieses Tutorial schreiben Sie ein Runbook, das Informationen zu Automation-Aufträgen sammelt. Runbooks werden in Azure Automation mit PowerShell implementiert. Deshalb beginnen Sie mit dem Schreiben und Testen eines Skripts im Azure Automation-Editor. Nachdem Sie überprüft haben, ob die gewünschten Informationen gesammelt werden, schreiben Sie die Daten nach Log Analytics und überprüfen den benutzerdefinierten Datentyp. Zum Schluss erstellen Sie einen Zeitplan zum Starten des Runbooks in regelmäßigen Abständen.

> [!NOTE]
> Sie können Azure Automation für das Senden von Informationen an Log Analytics ohne dieses Runbook konfigurieren. Dieses Szenario wird hauptsächlich verwendet, um das Tutorial zu unterstützen. Es wird empfohlen, dass Sie die Daten an einen Testarbeitsbereich senden.

## <a name="1-install-data-collector-api-module"></a>1. Installieren des Datensammler-API-Moduls
Jede [Anforderung von der HTTP-Datensammler-API](../../azure-monitor/platform/data-collector-api.md#create-a-request) muss ordnungsgemäß formatiert sein und einen Autorisierungsheader enthalten. Sie können dies in Ihrem Runbook durchführen, aber Sie können auch den erforderlichen Code reduzieren, indem Sie ein Modul verwenden, das diesen Prozess vereinfacht. Ein Modul, das Sie verwenden können, ist das [OMSIngestionAPI-Modul](https://www.powershellgallery.com/packages/OMSIngestionAPI) im PowerShell-Katalog.

Damit Sie ein [Modul](../../automation/automation-integration-modules.md) in einem Runbook verwenden können, muss es unter Ihrem Automation-Konto installiert sein.  Jedes Runbook in demselben Konto kann dann die Funktionen im Modul verwenden. Sie können ein neues Modul installieren, indem Sie in Ihrem Automation-Konto **Ressourcen** > **Module** > **Modul hinzufügen** auswählen.

Der PowerShell-Katalog bietet jedoch eine schnelle Möglichkeit, ein Modul direkt in Ihrem Automation-Konto bereitzustellen. Sie sollten diese Option für dieses Tutorial verwenden.

![OMSIngestionAPI-Modul](media/runbook-datacollect/OMSIngestionAPI.png)

1. Navigieren Sie zum [PowerShell-Katalog](https://www.powershellgallery.com/).
2. Suchen Sie nach **OMSIngestionAPI**.
3. Klicken Sie auf die Schaltfläche **In Azure Automation bereitstellen**.
4. Wählen Sie Ihr Automation-Konto aus, und klicken Sie auf **OK**, um das Modul zu installieren.

## <a name="2-create-automation-variables"></a>2. Erstellen von Automation-Variablen
[Automation-Variablen](../../automation/automation-variables.md) enthalten Werte, die von allen Runbooks in Ihrem Automation-Konto verwendet werden können. Sie machen die Runbooks flexibler, da Sie diese Werte ändern können, ohne das eigentliche Runbook zu bearbeiten. Jede Anforderung von der HTTP-Datensammler-API erfordert die ID und den Schlüssel des Log Analytics-Arbeitsbereichs. Für das Speichern solcher Informationen eignen sich besonders variable Ressourcen.

![Variables](media/runbook-datacollect/variables.png)

1. Navigieren Sie im Azure-Portal zu Ihrem Automation-Konto.
2. Wählen Sie unter **Freigegebene Ressourcen** die Option **Variablen** aus.
2. Klicken Sie auf **Variable hinzufügen**, und erstellen Sie die beiden Variablen in der folgenden Tabelle.

| Eigenschaft | Wert der Arbeitsbereichs-ID | Wert des Arbeitsbereichsschlüssels |
|:--|:--|:--|
| NAME | WorkspaceId | WorkspaceKey |
| Type | string | string |
| Wert | Fügen Sie die Arbeitsbereichs-ID Ihres Log Analytics-Arbeitsbereichs ein. | Fügen Sie den primären oder sekundären Schlüssel Ihres Log Analytics-Arbeitsbereichs ein. |
| Verschlüsselt | Nein | Ja |

## <a name="3-create-runbook"></a>3. Runbook erstellen

Azure Automation bietet im Portal einen Editor, mit dem Sie Ihr Runbook bearbeiten und testen können. Sie haben die Möglichkeit, den Skript-Editor für die direkte Arbeit mit [PowerShell](../../automation/automation-edit-textual-runbook.md) zu verwenden oder ein [grafisches Runbook zu erstellen](../../automation/automation-graphical-authoring-intro.md). In diesem Tutorial arbeiten Sie mit einem PowerShell-Skript.

![Runbook bearbeiten](media/runbook-datacollect/edit-runbook.png)

1. Navigieren Sie zu Ihrem Automation-Konto.
2. Klicken Sie auf **Runbooks** > **Runbook hinzufügen** > **Neues Runbook erstellen**.
3. Geben Sie als Runbooknamen **Collect-Automation-jobs** ein. Wählen Sie als Runbooktyp **PowerShell** aus.
4. Klicken Sie auf **Erstellen**, um das Runbook zu erstellen und den Editor zu öffnen.
5. Kopieren Sie den folgenden Code, und fügen Sie ihn in das Runbook ein. Erläuterungen zum Code finden Sie in den Kommentaren im Skript.
    ```
    # Get information required for the automation account from parameter values when the runbook is started.
    Param
    (
        [Parameter(Mandatory = $True)]
        [string]$resourceGroupName,
        [Parameter(Mandatory = $True)]
        [string]$automationAccountName
    )
    
    # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
    # Code copied from the runbook AzureAutomationTutorial.
    $connectionName = "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName
    Connect-AzAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    
    # Set the $VerbosePreference variable so that we get verbose output in test environment.
    $VerbosePreference = "Continue"
    
    # Get information required for Log Analytics workspace from Automation variables.
    $customerId = Get-AutomationVariable -Name 'WorkspaceID'
    $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
    
    # Set the name of the record type.
    $logType = "AutomationJob"
    
    # Get the jobs from the past hour.
    $jobs = Get-AzAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
    
    if ($jobs -ne $null) {
        # Convert the job data to json
        $body = $jobs | ConvertTo-Json
    
        # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
        Write-Verbose $body
    
        # Send the data to Log Analytics.
        Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
    }
    ```

## <a name="4-test-runbook"></a>4. Testen des Runbooks
Azure Automation umfasst eine Umgebung zum [Testen Ihres Runbooks](../../automation/automation-testing-runbook.md) vor der Veröffentlichung. Sie können die vom Runbook gesammelten Daten analysieren und überprüfen, ob es wie erwartet nach Log Analytics schreibt, bevor Sie es für die Produktion veröffentlichen.

![Testen des Runbooks](media/runbook-datacollect/test-runbook.png)

1. Klicken Sie auf **Speichern**, um das Runbook zu speichern.
1. Klicken Sie auf **Testbereich**, um das Runbook in der Testumgebung zu öffnen.
1. Da Ihr Runbook über Parameter verfügt, werden Sie aufgefordert, Werte für diese einzugeben. Geben Sie den Namen der Ressourcengruppe und das Automation-Konto ein, von der bzw. dem Sie Auftragsinformationen sammeln möchten.
1. Klicken Sie auf **Starten**, um das Runbook zu starten.
1. Das Runbook wird mit dem Status **In Warteschlange** gestartet, bevor es in **Wird ausgeführt** wechselt.
1. Das Runbook sollte eine ausführliche Ausgabe mit den gesammelten Aufträgen im JSON-Format anzeigen. Wenn keine Aufträge aufgeführt werden, wurden möglicherweise in der letzten Stunde keine Aufträge in dem Automation-Konto erstellt. Starten Sie ein beliebiges Runbook im Automation-Konto, und führen Sie den Test erneut aus.
1. Stellen Sie sicher, dass die Ausgabe keine Fehler im POST-Befehl an Log Analytics anzeigt. Es wird eine Meldung ähnlich der folgenden angezeigt.

    ![POST-Ausgabe](media/runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Überprüfen Sie die Datensätze in Log Analytics
Sobald das Runbook im Test abgeschlossen wurde und Sie den erfolgreichen Empfang der Ausgabe überprüft haben, können Sie überprüfen, ob die Datensätze mit einer [Protokollsuche in Log Analytics](../../azure-monitor/log-query/log-query-overview.md) erstellt wurden.

![Protokollausgabe](media/runbook-datacollect/log-output.png)

1. Wählen Sie im Azure-Portal Ihren Log Analytics-Arbeitsbereich aus.
2. Klicken Sie auf **Protokollsuche**.
3. Geben Sie den folgenden Befehl `Type=AutomationJob_CL` ein, und klicken Sie auf die Schaltfläche „Suchen“. Beachten Sie, dass der Datensatztyp die Zeichenfolge „_CL“ enthält, die nicht im Skript angegeben wurde. Dieses Suffix wird automatisch an den Protokolltyp angefügt, um anzugeben, dass es sich um einen benutzerdefinierten Protokolltyp handelt.
4. Es sollte mindestens ein Datensatz zurückgegeben und damit bestätigt werden, dass das Runbook wie erwartet funktioniert.

## <a name="6-publish-the-runbook"></a>6. Veröffentlichen des Runbooks
Nachdem Sie überprüft haben, dass das Runbook ordnungsgemäß funktioniert, müssen Sie es veröffentlichen, damit Sie es in der Produktionsumgebung ausführen können. Sie können das Runbook weiterhin bearbeiten und testen, ohne die veröffentlichte Version zu ändern.

![Runbook veröffentlichen](media/runbook-datacollect/publish-runbook.png)

1. Navigieren Sie zurück zu Ihrem Automation-Konto.
2. Klicken Sie auf **Runbooks**, und wählen Sie **Collect-Automation-jobs** aus.
3. Klicken Sie auf **Bearbeiten** und dann auf **Veröffentlichen**.
4. Klicken Sie auf **Ja**, wenn Sie bestätigen sollen, dass die zuvor veröffentlichte Version überschrieben werden soll.

## <a name="7-set-logging-options"></a>7. Festlegen von Protokollierungsoptionen
Für den Test konnten Sie die [ausführliche Ausgabe](../../automation/automation-runbook-output-and-messages.md#message-streams) anzeigen, da Sie die Variable $VerbosePreference im Skript festgelegt hatten. Für die Produktion müssen Sie die Protokollierungseigenschaften für das Runbook festlegen, wenn Sie eine ausführlichen Ausgabe anzeigen möchten. Für das Runbook in diesem Tutorial werden damit die JSON-Daten angezeigt, die an Log Analytics gesendet werden.

![Protokollierung und Nachverfolgung](media/runbook-datacollect/logging.png)

1. Wählen Sie in den Eigenschaften für Ihr Runbook unter **Runbookeinstellungen** die Option **Protokollierung und Nachverfolgung** aus.
2. Ändern Sie die Einstellung für **Ausführliche Datensätze protokollieren** in **Ein**.
3. Klicken Sie auf **Speichern**.

## <a name="8-schedule-runbook"></a>8. Planen des Runbooks
Die gängigste Methode zum Starten eines Runbooks, das Überwachungsdaten sammelt, stellt die Planung für die automatische Ausführung dar. Erstellen Sie dazu einen [Zeitplan in Azure Automation](../../automation/automation-schedules.md), und fügen Sie diesen an Ihr Runbook an.

![Planen des Runbooks](media/runbook-datacollect/schedule-runbook.png)

1. Wählen Sie in den Eigenschaften für Ihr Runbook unter **Ressourcen** die Option **Zeitpläne** aus.
2. Klicken Sie auf **Zeitplan hinzufügen** > **Zeitplan mit Runbook verknüpfen** > **Neuen Zeitplan erstellen**.
5. Geben Sie die folgenden Werte für den Zeitplan ein, und klicken Sie auf **Erstellen**.

| Eigenschaft | Wert |
|:--|:--|
| NAME | AutomationJobs-Hourly |
| Startet | Wählen Sie eine Zeit aus, die mindestens 5 Minuten in der Zukunft liegt. |
| Serie | Serie |
| Wiederholen alle | 1 Stunde |
| Ablaufdatum festlegen | Nein |

Nachdem der Zeitplan erstellt wurde, müssen Sie die Parameterwerte festlegen, die jedes Mal verwendet werden, wenn dieser Zeitplan das Runbook startet.

1. Klicken Sie auf **Parameter und Ausführungseinstellungen konfigurieren**.
1. Geben Sie Werte für **ResourceGroupName** und **AutomationAccountName** ein.
1. Klicken Sie auf **OK**.

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Überprüfen des Starts des Runbooks gemäß Zeitplan
Bei jedem Start eines Runbooks [wird ein Auftrag erstellt](../../automation/automation-runbook-execution.md), und die Ausgabe wird protokolliert. Dies sind genau die Aufträge, die das Runbook sammelt. Sie können überprüfen, ob das Runbook wie erwartet gestartet wird, indem Sie die Aufträge für das Runbook nach Ablauf die Startzeit des Zeitplans überprüfen.

![Aufträge](media/runbook-datacollect/jobs.png)

1. Wählen Sie in den Eigenschaften für Ihr Runbook unter **Ressourcen** die Option **Aufträge** aus.
2. Es sollte eine Liste der Aufträge für jeden Start des Runbook angezeigt werden.
3. Klicken Sie auf einen der Aufträge, um die zugehörigen Details anzuzeigen.
4. Klicken Sie auf **Alle Protokolle**, um die Protokolle und die Ausgabe des Runbooks anzuzeigen.
5. Scrollen Sie nach unten, um einen Eintrag ähnlich der folgenden Abbildung zu suchen.<br>![Ausführlich](media/runbook-datacollect/verbose.png)
6. Klicken Sie auf diesen Eintrag, um die ausführlichen JSON-Daten anzuzeigen, die an Log Analytics gesendet wurden.

## <a name="next-steps"></a>Nächste Schritte
- Verwenden Sie den [Ansicht-Designer](../../azure-monitor/platform/view-designer.md) zum Erstellen einer Ansicht zum Anzeigen der Daten, die Sie im Log Analytics-Repository gesammelt haben.
- Packen Sie Ihr Runbook in einer [Verwaltungslösung](../../azure-monitor/insights/solutions-creating.md), um sie an Kunden zu verteilen.
- Erfahren Sie mehr über [Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- Erfahren Sie mehr über [Azure Automation](https://docs.microsoft.com/azure/automation/).
- Erfahren Sie mehr über die [HTTP-Datensammler-API](../../azure-monitor/platform/data-collector-api.md).
