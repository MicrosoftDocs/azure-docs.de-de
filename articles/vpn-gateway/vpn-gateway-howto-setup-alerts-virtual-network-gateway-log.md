---
title: 'Azure-VPN Gateway: Konfigurieren von Warnungen für Diagnoseressourcenprotokollereignisse'
description: Hier erfahren Sie, wie Sie mit Azure Monitor Log Analytics Warnungen einrichten, die auf Ressourcenprotokollereignissen von Azure VPN Gateway basieren.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: alzam
ms.openlocfilehash: 1f10799ab32b6cb26a8b3b5b343f65b960d71357
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007000"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>Einrichten von Warnungen für Ressourcenprotokollereignisse von VPN Gateway

In diesem Artikel erfahren Sie, wie Sie mit Azure Monitor Log Analytics Warnungen einrichten, die auf Ressourcenprotokollereignissen von Azure VPN Gateway basieren. 

Die folgenden Ressourcenprotokolle sind in Azure verfügbar:

|***Name** _ | _*_Beschreibung_*_ |
|---        | ---               |
|GatewayDiagnosticLog | Dieses Protokoll enthält Ressourcenprotokolle für Konfigurationsereignisse, wichtige Änderungen und Wartungsereignisse, die das Gateway betreffen. |
|TunnelDiagnosticLog | Enthält Ereignisse, die die Änderung des Tunnelzustands betreffen. Ereignisse im Zusammenhang mit der Herstellung und Trennung der Tunnelverbindung beinhalten ggf. eine Zusammenfassung, in der die Ursache der Zustandsänderung beschrieben wird. |
|RouteDiagnosticLog | Enthält Änderungen an statische Routen und BGP-Ereignisse, die am Gateway auftreten. |
|IKEDiagnosticLog | Enthält IKE-Kontrollnachrichten und -ereignisse, die am Gateway erfasst werden. |
|P2SDiagnosticLog | Enthält Point-to-Site-Kontrollnachrichten und -ereignisse, die am Gateway erfasst werden. Informationen zur Verbindungsquelle werden nur für IKEv2- und OpenVPN-Verbindungen bereitgestellt. |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Einrichten von Warnungen im Azure-Portal

Mit den folgenden Beispielschritten wird eine Warnung für ein Trennungsereignis mit einem Site-to-Site-VPN-Tunnel erstellt:


1. Suchen Sie im Azure-Portal unter **Alle Dienste** nach _ *Log Analytics**, und wählen Sie **Log Analytics-Arbeitsbereiche** aus.

   ![Auswahl für den Wechsel zu Log Analytics-Arbeitsbereichen](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Erstellen")

2. Wählen Sie **Erstellen** auf der Seite **Log Analytics** aus.

   ![Log Analytics-Seite mit Schaltfläche „Erstellen“](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Select")

3. Wählen Sie **Neu erstellen** aus und geben Sie die Details ein.

   ![Details zum Erstellen eines Log Analytics-Arbeitsbereichs](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Select")

4. Suchen Sie Ihr VPN Gateway auf dem Blatt **Überwachen** > **Diagnoseeinstellungen**.

   ![Auswahl zum Suchen des VPN-Gateways in den Diagnoseeinstellungen](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Select")

5. Doppelklicken Sie zum Aktivieren der Diagnose auf das Gateway, und wählen Sie dann **Diagnose aktivieren** aus.

   ![Auswahl zum Aktivieren der Diagnose](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Select")

6. Geben Sie die Details ein und stellen Sie sicher, dass **An Log Analytics senden** und **TunnelDiagnosticLog** ausgewählt sind. Wählen Sie den Log Analytics-Arbeitsbereich aus, den Sie in Schritt 3 erstellt haben.

   ![Aktivierte Kontrollkästchen](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Select")

   > [!NOTE]
   > Es kann einige Stunden dauern, bis die Daten erstmalig angezeigt werden.

7. Wechseln Sie zur Übersicht für die virtuelle Netzwerkgatewayressource, und wählen Sie **Warnungen** auf der Registerkarte **Überwachung** aus. Erstellen Sie dann eine neue Warnungsregel, oder bearbeiten Sie eine bestehende Warnungsregel.

   ![Auswahl zum Erstellen einer neuen Warnungsregel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")

   ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")
8. Wählen Sie den Log Analytics-Arbeitsbereich und die Ressource aus.

   ![Auswahl für Arbeitsbereich und Ressource](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Select")

9. Wählen Sie **Benutzerdefinierte Protokollsuche** als Signallogik unter **Bedingung hinzufügen** aus.

   ![Auswahl für eine benutzerdefinierte Protokollsuche](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Select")

10. Geben Sie im Textfeld **Suchabfrage** die folgende Abfrage ein. Ersetzen Sie die Werte in „<>“ und „TimeGenerated“ entsprechend.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Legen Sie den Schwellwert auf 0 fest und wählen Sie **Fertig** aus.

    ![Eingeben einer Abfrage und Auswählen eines Schwellenwerts](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Select")

11. Wählen Sie auf der Seite **Regel erstellen** **Neu erstellen** unter dem Abschnitt **AKTIONSGRUPPEN** aus. Geben Sie die Details ein und wählen Sie **OK** aus.

    ![Details für eine neue Aktionsgruppe](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Select")

12. Geben Sie auf der Seite **Regel erstellen** die Details für **Aktionen anpassen** ein und stellen Sie sicher, dass der richtige Name im Abschnitt **AKTIONSGRUPPENNAME** angezeigt wird. Wählen Sie **Warnungsregel erstellen** aus, um die Regel zu erstellen.

    ![Auswahl zum Erstellen einer Regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Select")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Einrichten von Warnungen mithilfe von PowerShell

Mit den folgenden Beispielschritten wird eine Warnung für ein Trennungsereignis erstellt, das einen Site-to-Site-VPN-Tunnel umfasst.

1. Erstellen Sie einen Log Analytics-Arbeitsbereich:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Aktivieren Sie die Diagnose für das VPN-Gateway:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Erstellen Sie eine Aktionsgruppe.

   Mit diesem Code wird eine Aktionsgruppe erstellt, die eine E-Mail-Benachrichtigung sendet, wenn eine Warnung ausgelöst wird:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Erstellen Sie eine Warnungsregel basierend auf einer benutzerdefinierten Protokollsuche:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren von Warnungen zu Tunnelmetriken finden Sie unter [Einrichten von Warnungen zu VPN Gateway-Metriken](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
