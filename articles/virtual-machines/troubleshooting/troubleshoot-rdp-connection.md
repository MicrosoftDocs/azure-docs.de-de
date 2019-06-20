---
title: Keine RDP-Verbindung mit einem virtuellen Windows-Computer in Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Probleme beim Herstellen einer Remotedesktopverbindung mit Ihrem virtuellen Windows-Computer in Azure behandeln.
keywords: Remotedesktop-Fehler,Remotedesktop-Verbindungsfehler,Verbindung mit virtuellem Computer nicht möglich,Remotedesktop-Problembehandlung
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: roiyz
ms.openlocfilehash: 50adab1eaa199473a8da857d38c3a08c424c677a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64728939"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Behandeln von Problemen bei Remotedesktopverbindungen mit einem virtuellen Azure-Computer
Eine Verbindung über das Remotedesktopprotokoll (RDP) mit Ihrem Windows-basierten virtuellen Azure-Computer (VM, Virtual Machine) kann aus verschiedenen Gründen fehlschlagen, sodass Sie nicht auf Ihren virtuellen Computer zugreifen können. Das Problem kann mit dem Remotedesktopdienst auf dem virtuellen Computer, der Netzwerkverbindung oder dem Remotedesktopclient auf Ihrem Hostcomputer zusammenhängen. Dieser Artikel führt Sie durch einige der am häufigsten verwendeten Methoden, um die RDP-Verbindungsprobleme zu beheben. 

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Azure-Experten im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/)Fragen stellen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Schritte zur schnellen Problembehandlung
Versuchen Sie nach jedem Problembehandlungsschritt, die Verbindung mit dem virtuellen Computer erneut herzustellen:

1. Setzen Sie die Remotedesktopkonfiguration zurück.
2. Überprüfen Sie die Netzwerksicherheitsgruppen-Regeln/Cloud Services-Endpunkte.
3. Überprüfen Sie die Konsolenprotokolle des virtuellen Computers.
4. Setzen Sie die NIC für den virtuellen Computer zurück.
5. Überprüfen Sie die Ressourcenintegrität des virtuellen Computers.
6. Setzen Sie Ihr Kennwort für den virtuellen Computer zurück.
7. Starten Sie den virtuellen Computer neu.
8. Stellen Sie den virtuellen Computer erneut bereit.

Lesen Sie weiter, falls Sie ausführlichere Schritte und Erläuterungen benötigen. Vergewissern Sie sich, dass lokale Netzwerkgeräte wie Router und Firewalls den ausgehenden TCP-Port 3389 nicht blockieren (siehe den Hinweis unter [Detaillierte RDP Problembehandlungsszenarien](detailed-troubleshoot-rdp.md)).

> [!TIP]
> Falls die Schaltfläche **Verbinden** für Ihren virtuellen Computer im Portal abgeblendet ist und keine [ExpressRoute](../../expressroute/expressroute-introduction.md)- oder [Site-to-Site-VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)-Verbindung mit Azure besteht, müssen Sie eine öffentliche IP-Adresse erstellen und dem virtuellen Computer zuweisen, um RDP verwenden zu können. Lesen Sie mehr über [öffentliche IP-Adressen in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Behandlungsmöglichkeiten für RDP-Probleme
Probleme mit virtuellen Computern, die unter Verwendung des Resource Manager-Bereitstellungsmodells erstellt wurden, können auf folgende Arten behandelt werden:

* Azure-Portal: Ermöglicht das schnelle Zurücksetzen der RDP-Konfiguration oder Benutzeranmeldeinformationen, falls die Azure-Tools nicht installiert sind.
* Azure PowerShell: Benutzer, die mit der PowerShell-Eingabeaufforderung vertraut sind, können RDP-Konfiguration oder Benutzeranmeldeinformationen schnell mithilfe der Azure PowerShell-Cmdlets zurücksetzen.

Schritte zum Behandeln von Problemen mit virtuellen Computern, die unter Verwendung des klassischen Bereitstellungsmodells erstellt wurden, finden Sie [hier](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Behandeln von Problemen über das Azure-Portal
Versuchen Sie nach jedem Problembehandlungsschritt, die Verbindung mit dem virtuellen Computer erneut herzustellen. Sollte sich immer noch keine Verbindung herstellen lassen, versuchen Sie es mit dem nächsten Schritt.

1. **Setzen Sie Ihre RDP-Verbindung zurück.** In diesem Problembehandlungsschritt wird die RDP-Konfiguration zurückgesetzt. Dies kann beispielsweise hilfreich sein, wenn Remoteverbindungen deaktiviert sind oder RDP aufgrund von Windows-Firewallregeln blockiert wird.
   
    Wählen Sie im Azure-Portal Ihren virtuellen Computer aus. Scrollen Sie im Einstellungsbereich nach unten zum Abschnitt **Support und Problembehandlung**. Klicken Sie auf die Schaltfläche **Kennwort zurücksetzen**. Legen Sie den **Modus** auf **Reset configuration only** (Nur Konfiguration zurücksetzen) fest, und klicken Sie anschließend auf die Schaltfläche **Aktualisieren**:
   
    ![Zurücksetzen der RDP-Konfiguration im Azure-Portal](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Überprüfen Sie die Netzwerksicherheitsgruppen-Regeln.** Verwenden Sie den Ansatz [Überprüfen des IP-Flusses](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md), um zu bestätigen, dass eine Regel in einer Netzwerksicherheitsgruppe den Datenverkehr an eine VM oder von einer VM blockiert. Sie können auch die aktiven Sicherheitsgruppenregeln überprüfen, um sicherzustellen, dass die NSG-Regel „Zulassen“ für eingehende Verbindungen vorhanden ist und für den RDP-Port (standardmäßig 3389) Priorität hat. Weitere Informationen finden Sie unter [Verwenden von effektiven Sicherheitsregeln zur Problembehandlung des Datenverkehrsflusses auf virtuellen Computern](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Überprüfen Sie die Startdiagnose des virtuellen Computers.** In diesem Problembehandlungsschritt wird anhand der Konsolenprotokolle des virtuellen Computers ermittelt, ob der virtuelle Computer ein Problem meldet. Da die Startdiagnose nicht bei allen virtuellen Computern aktiviert ist, handelt es sich hierbei ggf. um einen optionalen Problembehandlungsschritt.
   
    Spezifische Problembehandlungsschritte werden in diesem Artikel nicht behandelt, deuten aber möglicherweise auf ein größeres Problem hin, das die RDP-Verbindung beeinträchtigt. Weitere Informationen zum Überprüfen der Konsolenprotokolle des virtuellen Computers sowie einen Screenshot finden Sie unter [Boot Diagnostics for Virtual Machines v2](boot-diagnostics.md) (Startdiagnose für virtuelle Computer v2).

4. **Setzen Sie die NIC für den virtuellen Computer zurück**. Weitere Informationen finden Sie unter [How to reset NIC for Azure Windows VM (Zurücksetzen der NIC für die Azure-Windows-VM)](../windows/reset-network-interface.md).
5. **Überprüfen Sie die Ressourcenintegrität des virtuellen Computers.** In diesem Problembehandlungsschritt wird überprüft, ob bekannte Probleme für die Azure-Plattform vorliegen, die möglicherweise die Verbindung mit dem virtuellen Computer beeinträchtigen.
   
    Wählen Sie im Azure-Portal Ihren virtuellen Computer aus. Scrollen Sie im Einstellungsbereich nach unten zum Abschnitt **Support und Problembehandlung**. Klicken Sie auf die Schaltfläche **Ressourcenintegrität**. Fehlerfreie virtuelle Computer haben den Status **Verfügbar**:
   
    ![Überprüfen der Ressourcenintegrität des virtuellen Computers im Azure-Portal](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Setzen Sie die Benutzeranmeldeinformationen zurück.** In diesem Problembehandlungsschritt wird das Kennwort für ein lokales Administratorkonto zurückgesetzt, falls Sie unsicher sind oder die Anmeldeinformationen vergessen haben.  Sobald Sie sich beim virtuellen Computer angemeldet haben, müssen Sie das Kennwort für diesen Benutzer zurücksetzen.
   
    Wählen Sie im Azure-Portal Ihren virtuellen Computer aus. Scrollen Sie im Einstellungsbereich nach unten zum Abschnitt **Support und Problembehandlung**. Klicken Sie auf die Schaltfläche **Kennwort zurücksetzen**. Vergewissern Sie sich, dass der **Modus** auf **Kennwort zurücksetzen** festgelegt ist, und geben Sie anschließend Ihren Benutzernamen und ein neues Kennwort ein. Klicken Sie abschließend auf die Schaltfläche **Aktualisieren**:
   
    ![Zurücksetzen der Benutzeranmeldeinformationen im Azure-Portal](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Starten Sie den virtuellen Computer neu.** Dieser Problembehandlungsschritt dient zum Beheben von Problemen, die auf den eigentlichen virtuellen Computer zurückzuführen sind.
   
    Wählen Sie im Azure-Portal Ihren virtuellen Computer aus, und klicken Sie auf die Registerkarte **Übersicht**. Klicken Sie auf die Schaltfläche **Neu starten**:
   
    ![Neustarten des virtuellen Computers im Azure-Portal](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Stellen Sie den virtuellen Computer erneut bereit.** In diesem Problembehandlungsschritt wird Ihr virtueller Computer auf einen anderen Host innerhalb von Azure neu bereitgestellt, um mögliche Plattform- oder Netzwerkprobleme zu beheben.
   
    Wählen Sie im Azure-Portal Ihren virtuellen Computer aus. Scrollen Sie im Einstellungsbereich nach unten zum Abschnitt **Support und Problembehandlung**. Klicken Sie auf die Schaltfläche **Erneut bereitstellen** und anschließend auf **Erneut bereitstellen**:
   
    ![Erneutes Bereitstellen des virtuellen Computers im Azure-Portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Nach Beendigung dieses Vorgangs gehen kurzlebige Datenträgerdaten verloren, und dynamische IP-Adressen, die dem virtuellen Computer zugeordnet sind, werden aktualisiert.

9. **Überprüfen Sie das Routing**. Vergewissern Sie sich mit der Funktion [Nächster Hop](../../network-watcher/network-watcher-check-next-hop-portal.md) von Network Watcher, dass eine Route nicht das Weiterleiten des Datenverkehrs an einen bzw. von einem virtuellen Computer verhindert. Sie können auch die effektiven Routen überprüfen, um alle effektiven Routen für eine Netzwerkschnittstelle anzuzeigen. Weitere Informationen finden Sie unter [Problembehandlung bei Routen mit Azure PowerShell](../../virtual-network/diagnose-network-routing-problem.md).

10. Stellen Sie sicher, dass alle lokalen Firewalls oder Firewalls auf dem Computer ausgehenden Datenverkehr an TCP-Port 3389 für Azure zulassen.

Sollten weiterhin RDP-Probleme auftreten, können Sie eine [Supportanfrage stellen](https://azure.microsoft.com/support/options/) oder sich eine [ausführlichere Darstellung der Konzepte und Schritte für die RDP-Problembehandlung](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ansehen.

## <a name="troubleshoot-using-azure-powershell"></a>Behandeln von Problemen mit Azure PowerShell
Sofern dies noch nicht erfolgt ist, [installieren und konfigurieren Sie die neueste Version von Azure PowerShell](/powershell/azure/overview).

In den folgenden Beispielen werden Variablen wie `myResourceGroup`, `myVM` und `myVMAccessExtension` verwendet. Ersetzen Sie diese Variablennamen und -orte durch Ihre eigenen Werte.

> [!NOTE]
> Zum Zurücksetzen der Benutzeranmeldeinformationen und der RDP-Konfiguration wird das PowerShell-Cmdlet [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) verwendet. In den folgenden Beispielen wird im Rahmen des Prozesses der Name `myVMAccessExtension` angegeben. Wenn Sie zuvor VMAccessAgent verwendet haben, können Sie den Namen der vorhandenen Erweiterung mithilfe von `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` abrufen, um die Eigenschaften des virtuellen Computers zu überprüfen. Der Name wird in der Ausgabe im Abschnitt „Erweiterungen“ angezeigt.

Versuchen Sie nach jedem Problembehandlungsschritt, die Verbindung mit dem virtuellen Computer erneut herzustellen. Sollte sich immer noch keine Verbindung herstellen lassen, versuchen Sie es mit dem nächsten Schritt.

1. **Setzen Sie Ihre RDP-Verbindung zurück.** In diesem Problembehandlungsschritt wird die RDP-Konfiguration zurückgesetzt. Dies kann beispielsweise hilfreich sein, wenn Remoteverbindungen deaktiviert sind oder RDP aufgrund von Windows-Firewallregeln blockiert wird.
   
    Im folgenden Beispiel wird die RDP-Verbindung auf einem virtuellen Computer namens `myVM` am Standort `WestUS` in der Ressourcengruppe namens `myResourceGroup` zurückgesetzt:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Überprüfen Sie die Netzwerksicherheitsgruppen-Regeln.** In diesem Problembehandlungsschritt wird überprüft, ob Ihre Netzwerksicherheitsgruppe eine Regel enthält, die RDP-Datenverkehr zulässt. Der Standardport für RDP ist der TCP-Port 3389. Unter Umständen wird bei der Erstellung Ihres virtuellen Computers nicht automatisch eine Regel zum Zulassen von RDP-Datenverkehr erstellt.
   
    Weisen Sie zunächst alle Konfigurationsdaten für Ihre Netzwerksicherheitsgruppe der Variablen `$rules` zu. Im folgenden Beispiel werden Informationen zur Netzwerksicherheitsgruppe namens `myNetworkSecurityGroup` in der Ressourcengruppe namens `myResourceGroup` abgerufen:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Sehen Sie sich nun die Regeln an, die für diese Netzwerksicherheitsgruppe konfiguriert sind. Vergewissern Sie sich, dass eine Regel vorhanden ist, die den TCP-Port 3389 für eingehende Verbindungen zulässt:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    Das folgende Beispiel zeigt eine gültige Sicherheitsregel, die RDP-Datenverkehr zulässt. Wie Sie sehen, sind `Protocol`, `DestinationPortRange`, `Access` und `Direction` ordnungsgemäß konfiguriert:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Sollte keine Regel vorhanden sein, die RDP-Datenverkehr zulässt, [erstellen Sie eine Netzwerksicherheitsgruppen-Regel](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Lassen Sie den TCP-Port 3389 zu.
3. **Setzen Sie die Benutzeranmeldeinformationen zurück.** In diesem Problembehandlungsschritt wird das Kennwort des lokalen Administratorkontos zurückgesetzt, das Sie angeben, falls Sie sich bei den Anmeldeinformationen unsicher sind oder die Anmeldeinformationen vergessen haben.
   
    Geben Sie zunächst den Benutzernamen und ein neues Kennwort an, indem Sie der Variablen `$cred` Anmeldeinformationen zuweisen:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Aktualisieren Sie nun die Anmeldeinformationen auf Ihrem virtuellen Computer. Im folgenden Beispiel werden die Anmeldeinformationen auf einem virtuellen Computer namens `myVM` am Standort `WestUS` in der Ressourcengruppe namens `myResourceGroup` aktualisiert:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Starten Sie den virtuellen Computer neu.** Dieser Problembehandlungsschritt dient zum Beheben von Problemen, die auf den eigentlichen virtuellen Computer zurückzuführen sind.
   
    Im folgenden Beispiel wird der virtuelle Computer namens `myVM` in der Ressourcengruppe namens `myResourceGroup` neu gestartet:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Stellen Sie den virtuellen Computer erneut bereit.** In diesem Problembehandlungsschritt wird Ihr virtueller Computer auf einen anderen Host innerhalb von Azure neu bereitgestellt, um mögliche Plattform- oder Netzwerkprobleme zu beheben.
   
    Im folgenden Beispiel wird der virtuelle Computer namens `myVM` am Standort `WestUS` in der Ressourcengruppe namens `myResourceGroup` erneut bereitgestellt:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Überprüfen Sie das Routing**. Vergewissern Sie sich mit der Funktion [Nächster Hop](../../network-watcher/network-watcher-check-next-hop-portal.md) von Network Watcher, dass eine Route nicht das Weiterleiten des Datenverkehrs an einen bzw. von einem virtuellen Computer verhindert. Sie können auch die effektiven Routen überprüfen, um alle effektiven Routen für eine Netzwerkschnittstelle anzuzeigen. Weitere Informationen finden Sie unter [Problembehandlung bei Routen mit Azure PowerShell](../../virtual-network/diagnose-network-routing-problem.md).

7. Stellen Sie sicher, dass alle lokalen Firewalls oder Firewalls auf dem Computer ausgehenden Datenverkehr an TCP-Port 3389 für Azure zulassen.

Sollten weiterhin RDP-Probleme auftreten, können Sie eine [Supportanfrage stellen](https://azure.microsoft.com/support/options/) oder sich eine [ausführlichere Darstellung der Konzepte und Schritte für die RDP-Problembehandlung](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ansehen.

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Behandeln von Problemen mit virtuellen Computern, die unter Verwendung des klassischen Bereitstellungsmodells erstellt wurden
Versuchen Sie nach jedem Problembehandlungsschritt, die Verbindung mit dem virtuellen Computer erneut herzustellen.

1. **Setzen Sie Ihre RDP-Verbindung zurück.** In diesem Problembehandlungsschritt wird die RDP-Konfiguration zurückgesetzt. Dies kann beispielsweise hilfreich sein, wenn Remoteverbindungen deaktiviert sind oder RDP aufgrund von Windows-Firewallregeln blockiert wird.
   
    Wählen Sie im Azure-Portal Ihren virtuellen Computer aus. Klicken Sie auf die Schaltfläche **...Mehr** und anschließend auf **Remotezugriff zurücksetzen**:
   
    ![Zurücksetzen der RDP-Konfiguration im Azure-Portal](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Überprüfen Sie die Cloud Services-Endpunkte.** In diesem Problembehandlungsschritt wird überprüft, ob Ihre Cloud Services-Instanz Endpunkte enthält, die RDP-Datenverkehr zulassen. Der Standardport für RDP ist der TCP-Port 3389. Unter Umständen wird bei der Erstellung Ihres virtuellen Computers nicht automatisch eine Regel zum Zulassen von RDP-Datenverkehr erstellt.
   
   Wählen Sie im Azure-Portal Ihren virtuellen Computer aus. Klicken Sie auf die Schaltfläche **Endpunkte**, um die Endpunkte anzuzeigen, die derzeit für Ihren virtuellen Computer konfiguriert sind. Vergewissern Sie sich, dass Endpunkte vorhanden sind, die RDP-Datenverkehr über den TCP-Port 3389 zulassen.
   
   Das folgende Beispiel zeigt gültige Endpunkte, die RDP-Datenverkehr zulassen:
   
   ![Überprüfen der Cloud Services-Endpunkte im Azure-Portal](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Sollte kein Endpunkt vorhanden sein, der RDP-Datenverkehr zulässt, [erstellen Sie einen Cloud Services-Endpunkt](../windows/classic/setup-endpoints.md). Lassen Sie TCP für den privaten Port 3389 zu.
3. **Überprüfen Sie die Startdiagnose des virtuellen Computers.** In diesem Problembehandlungsschritt wird anhand der Konsolenprotokolle des virtuellen Computers ermittelt, ob der virtuelle Computer ein Problem meldet. Da die Startdiagnose nicht bei allen virtuellen Computern aktiviert ist, handelt es sich hierbei ggf. um einen optionalen Problembehandlungsschritt.
   
    Spezifische Problembehandlungsschritte werden in diesem Artikel nicht behandelt, deuten aber möglicherweise auf ein größeres Problem hin, das die RDP-Verbindung beeinträchtigt. Weitere Informationen zum Überprüfen der Konsolenprotokolle des virtuellen Computers sowie einen Screenshot finden Sie unter [Boot Diagnostics for Virtual Machines v2](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) (Startdiagnose für virtuelle Computer v2).
4. **Überprüfen Sie die Ressourcenintegrität des virtuellen Computers.** In diesem Problembehandlungsschritt wird überprüft, ob bekannte Probleme für die Azure-Plattform vorliegen, die möglicherweise die Verbindung mit dem virtuellen Computer beeinträchtigen.
   
    Wählen Sie im Azure-Portal Ihren virtuellen Computer aus. Scrollen Sie im Einstellungsbereich nach unten zum Abschnitt **Support und Problembehandlung**. Klicken Sie auf die Schaltfläche **Ressourcenintegrität**. Fehlerfreie virtuelle Computer haben den Status **Verfügbar**:
   
    ![Überprüfen der Ressourcenintegrität des virtuellen Computers im Azure-Portal](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Setzen Sie die Benutzeranmeldeinformationen zurück.** In diesem Problembehandlungsschritt wird das Kennwort des lokalen Administratorkontos zurückgesetzt, das Sie angeben, falls Sie sich bei den Anmeldeinformationen unsicher sind oder die Anmeldeinformationen vergessen haben.  Sobald Sie sich beim virtuellen Computer angemeldet haben, müssen Sie das Kennwort für diesen Benutzer zurücksetzen.
   
    Wählen Sie im Azure-Portal Ihren virtuellen Computer aus. Scrollen Sie im Einstellungsbereich nach unten zum Abschnitt **Support und Problembehandlung**. Klicken Sie auf die Schaltfläche **Kennwort zurücksetzen**. Geben Sie Ihren Benutzernamen und ein neues Kennwort ein. Klicken Sie abschließend auf die Schaltfläche **Speichern**:
   
    ![Zurücksetzen der Benutzeranmeldeinformationen im Azure-Portal](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Starten Sie den virtuellen Computer neu.** Dieser Problembehandlungsschritt dient zum Beheben von Problemen, die auf den eigentlichen virtuellen Computer zurückzuführen sind.
   
    Wählen Sie im Azure-Portal Ihren virtuellen Computer aus, und klicken Sie auf die Registerkarte **Übersicht**. Klicken Sie auf die Schaltfläche **Neu starten**:
   
    ![Neustarten des virtuellen Computers im Azure-Portal](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Stellen Sie sicher, dass alle lokalen Firewalls oder Firewalls auf dem Computer ausgehenden Datenverkehr an TCP-Port 3389 für Azure zulassen.

Sollten weiterhin RDP-Probleme auftreten, können Sie eine [Supportanfrage stellen](https://azure.microsoft.com/support/options/) oder sich eine [ausführlichere Darstellung der Konzepte und Schritte für die RDP-Problembehandlung](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ansehen.

## <a name="troubleshoot-specific-rdp-errors"></a>Behandeln spezifischer RDP-Fehler
Möglicherweise wird beim Herstellen der RDP-Verbindung mit Ihrem virtuellen Computer eine spezifische Fehlermeldung angezeigt. Die häufigsten Fehlermeldungen sind:

* [Die Verbindung mit der Remotesitzung wurde getrennt, da keine Lizenzserver für Remotedesktop vorhanden sind](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Remotedesktop kann den Computer „Name“ nicht finden.](troubleshoot-specific-rdp-errors.md#rdpname)
* [Authentifizierungsfehler. Die lokale Sicherheitsautorität (LSA) ist nicht erreichbar](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Windows-Sicherheitsfehler: Ihre Anmeldeinformationen haben nicht funktioniert](troubleshoot-specific-rdp-errors.md#wincred).
* [Dieser Computer kann keine Verbindung mit dem Remotecomputer herstellen.](troubleshoot-specific-rdp-errors.md#rdpconnect)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Wenn keiner dieser Fehler auftritt und Sie trotzdem keine Verbindung mit dem virtuellen Computer per Remotedesktop herstellen können, können Sie im [ausführlichen Handbuch zur Problembehandlung für Remotedesktop](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)nachschlagen.
* Informationen zum Behandeln von Problemen beim Zugreifen auf Anwendungen, die auf einem virtuellen Computer ausgeführt werden, finden Sie unter [Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Informationen zu Problemen beim Herstellen einer SSH-Verbindung (Secure Shell) mit einem virtuellen Linux-Computer in Azure finden Sie unter [Behandeln von Problemen, Fehlern oder Ablehnungen im Zusammenhang mit der SSH-Verbindung mit einem virtuellen Azure Linux-Computer](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


