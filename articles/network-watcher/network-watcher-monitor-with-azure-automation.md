---
title: Problembehandlung und Überwachung von VPN-Gateways – Azure Automation
titleSuffix: Azure Network Watcher
description: In diesem Artikel wird beschrieben, wie die Diagnose für die lokale Verbindung mit Azure Automation und Network Watcher durchgeführt wird.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: af671996722524de9af1a90ae8dfde27f814c8c2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011811"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Überwachen von VPN Gateways mit der Problembehandlung von Network Watcher

Das Gewinnen von eingehenden Erkenntnissen zu Ihrer Netzwerkleistung ist von entscheidender Bedeutung, damit Sie für Ihre Kunden zuverlässige Dienste bereitstellen können. Daher ist es wichtig, Netzwerkausfälle schnell zu erkennen und Korrekturmaßnahmen einzuleiten, um eine Lösung für die Ausfallsituation zu finden. Mit Azure Automation können Sie eine Aufgabe auf programmgesteuerte Weise mithilfe von Runbooks implementieren und ausführen. Azure Automation schafft die Voraussetzungen für die Durchführung einer fortlaufenden und proaktiven Netzwerküberwachung und die Verwendung von Warnungen.

## <a name="scenario"></a>Szenario

Das Szenario in der folgenden Abbildung ist eine Multi-Tier-Anwendung mit lokaler Verbindung, die über ein VPN Gateway und einen Tunnel hergestellt wird. Zur Erzielung einer hohen Anwendungsleistung muss sichergestellt werden, dass das VPN Gateway betriebsbereit ist und ausgeführt wird.

Ein Runbook wird mit einem Skript erstellt, um den Verbindungsstatus des VPN-Tunnels zu überprüfen, indem die API für die Ressourcenproblembehandlung verwendet wird. Wenn der Status nicht fehlerfrei ist, wird eine E-Mail-Benachrichtigung an die Administratoren gesendet.

![Beispielszenario][scenario]

Dieses Szenario umfasst Folgendes:

- Erstellen eines Runbooks zum Aufrufen des `Start-AzureRmNetworkWatcherResourceTroubleshooting`-Cmdlets für die Problembehandlung in Bezug auf den Verbindungsstatus
- Verknüpfen eines Zeitplans mit dem Runbook

## <a name="before-you-begin"></a>Voraussetzungen

Vor dem Starten dieses Szenarios müssen die folgenden Voraussetzungen erfüllt sein:

- Azure Automation-Konto in Azure Stellen Sie sicher, dass das Automation-Konto über die neuesten Module und über das AzureRM.Network-Modul verfügt. Das AzureRM.Network-Modul ist im Modulkatalog verfügbar, falls Sie es ihrem Automation-Konto noch hinzufügen müssen.
- In Azure Automation muss ein Satz mit Anmeldeinformationen konfiguriert sein. Weitere Informationen finden Sie im Artikel zur [Azure Automation-Sicherheit](../automation/automation-security-overview.md).
- Gültiger SMTP-Server (Microsoft 365, Ihre lokale E-Mail-Anwendung oder Ähnliches) und definierte Anmeldeinformationen in Azure Automation
- Konfiguriertes Virtual Network-Gateway in Azure
- Ein bestehendes Speicherkonto mit einem bestehenden Container, in dem die Protokolle gespeichert werden.

> [!NOTE]
> Die in der obigen Abbildung dargestellte Infrastruktur dient nur Informationszwecken und umfasst nicht die in diesem Artikel enthaltenen Schritte.

### <a name="create-the-runbook"></a>Erstellen des Runbooks

Der erste Schritt zum Konfigurieren des Beispiels ist die Erstellung des Runbooks. In diesem Beispiel wird ein ausführendes Konto verwendet. Informationen zu ausführenden Konten finden Sie unter [Authentifizieren von Runbooks mit der Azure-Option „Ausführendes Konto“](../automation/manage-runas-account.md).

### <a name="step-1"></a>Schritt 1

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Azure Automation, und klicken Sie auf **Runbooks**.

![Automation-Konto – Übersicht][1]

### <a name="step-2"></a>Schritt 2

Klicken Sie auf **Runbook hinzufügen**, um den Erstellungsprozess des Runbooks zu starten.

![Blatt „Runbooks“][2]

### <a name="step-3"></a>Schritt 3

Klicken Sie unter **Schnellerstellung** auf **Neues Runbook erstellen**, um das Runbook zu erstellen.

![Blatt „Runbook hinzufügen“][3]

### <a name="step-4"></a>Schritt 4

In diesem Schritt geben wir dem Runbook einen Namen. Im Beispiel ist dies **Get-VPNGatewayStatus**. Es ist wichtig, dem Runbook einen beschreibenden Namen zu geben, und wir raten Ihnen zu einem Namen, der den PowerShell-Benennungsstandards entspricht. Der Runbooktyp für dieses Beispiel ist **PowerShell**, und die anderen Optionen sind „Grafisch“, „PowerShell-Workflow“ und „Grafischer PowerShell-Workflow“.

![Blatt „Runbook“][4]

### <a name="step-5"></a>Schritt 5

In diesem Schritt wird das Runbook erstellt. Das folgende Codebeispiel enthält den gesamten Code, der für das Beispiel benötigt wird. Die Elemente im Code, die \<value\> enthalten, müssen durch die Werte aus Ihrem Abonnement ersetzt werden.

Verwenden Sie den folgenden Code, und klicken Sie auf **Speichern**.

```powershell
# Set these variables to the proper values for your environment
$automationCredential = "<work or school account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for work or school account
$cred = Get-AutomationPSCredential -Name $automationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>Schritt 6

Nach dem Speichern des Runbooks muss ein Zeitplan damit verknüpft werden, um den Start des Runbooks zu automatisieren. Klicken Sie zum Starten des Prozesses auf **Zeitplan**.

![Schritt 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Verknüpfen eines Zeitplans mit dem Runbook

Es muss ein neuer Zeitplan erstellt werden. Klicken Sie auf **Zeitplan mit Runbook verknüpfen**.

![Schritt 7][7]

### <a name="step-1"></a>Schritt 1

Klicken Sie auf dem Blatt **Zeitplan** auf **Neuen Zeitplan erstellen**.

![Schritt 8][8]

### <a name="step-2"></a>Schritt 2

Geben Sie auf dem Blatt **Neuer Zeitplan** die Zeitplaninformationen ein. Die Werte, die festgelegt werden können, sind in der folgenden Liste aufgeführt:

- **Name**: Der Anzeigename des Zeitplans.
- **Beschreibung**: Eine Beschreibung des Zeitplans.
- **Startet**: Dieser Wert ist eine Kombination aus Datum, Uhrzeit und Zeitzone, um den Zeitpunkt für das Auslösen des Zeitplans anzugeben.
- **Wiederholung**: Mit diesem Wert wird die Wiederholung für den Zeitplan bestimmt.  Gültige Werte sind **Einmal** oder **Wiederholt**.
- **Wiederholen alle**: Das Wiederholungsintervall des Zeitplans in Stunden, Tagen, Wochen oder Monaten.
- **Ablaufdatum festlegen**: Mit diesem Wert wird bestimmt, ob der Zeitplan ablaufen soll. Sie können **Ja** oder **Nein** wählen. Wenn Sie „Ja“ wählen, müssen Sie ein gültiges Datum und eine Uhrzeit angeben.

> [!NOTE]
> Wenn Sie ein Runbook häufiger als jede Stunde ausführen müssen, müssen mehrere Zeitpläne mit unterschiedlichen Intervallen erstellt werden (also 15, 30 oder 45 Minuten nach der vollen Stunde).

![Schritt 9][9]

### <a name="step-3"></a>Schritt 3

Klicken Sie auf „Speichern“, um den Zeitplan im Runbook zu speichern.

![Schritt 10][10]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich darüber informiert haben, wie Sie die Network Watcher-Problembehandlung in Azure Automation integrieren können, helfen Ihnen die Informationen zum Auslösen von Paketerfassungen bei VM-Warnungen unter [Verwenden der Paketerfassung für die proaktive Netzwerküberwachung mit Azure Functions](network-watcher-alert-triggered-packet-capture.md) weiter.

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png