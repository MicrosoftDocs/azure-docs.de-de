---
title: Zurücksetzen der Netzwerkschnittstelle für virtuelle Windows-Computer in Azure | Microsoft-Dokumentation
description: Zeigt, wie Sie die Netzwerkschnittstelle für virtuelle Windows-Computer in Azure zurücksetzen
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 6c4e507df0f112934979d4e59778b667743cf623
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022903"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Die Netzwerkschnittstelle für den virtuellen Windows-Computer in Azure zurücksetzen 

Dieser Artikel zeigt, wie Sie die Netzwerkschnittstelle für Azure-Windows-VMs zurücksetzen, um Probleme zu lösen, wenn Sie nach einer der folgenden Aktionen keine Verbindung mit einem virtuellen Microsoft Azure-Windows-Computer (VM) mehr herstellen können:

* Sie haben die Standard-Netzwerkschnittstelle (NIC) deaktiviert. 
* Sie haben manuell eine statische IP-Adresse für die NIC festgelegt. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Zurücksetzen der Netzwerkschnittstelle

### <a name="for-vms-deployed-in-resource-group-model"></a>Für im Ressourcengruppenmodell bereitgestellte virtuelle Computer

1.  Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com).
2.  Wählen Sie den betroffenen virtuellen Computer aus.
3.  Wählen Sie **Netzwerk** und dann die Netzwerkschnittstelle des virtuellen Computers aus.

    ![Position der Netzwerkschnittstellenkarte](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Wählen Sie die **IP-Konfigurationen** aus.
5.  Wählen Sie die IP aus. 
6.  Wenn die **Private IP-Zuweisung** nicht **Statisch** ist, ändern Sie diese in **Statisch**.
7.  Ändern Sie die **IP-Adresse** zu einer anderen IP-Adresse, die im Subnetz verfügbar ist.
8. Der virtuelle Computer wird neu gestartet, um die neue Netzwerkkarte mit dem System zu initialisieren.
9.  Versuchen Sie RDP auf Ihrem Computer auszuführen. Wenn Sie möchten, können Sie bei erfolgreicher Ausführung die private IP-Adresse zurück zur ursprünglichen ändern. Andernfalls können Sie sie speichern. 

#### <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell

1. Sicherstellen, dass die [neueste Azure PowerShell-Version](/powershell/azure/) installiert ist
2. Öffnen Sie eine Azure PowerShell Sitzung mit erhöhten Rechten („Als Administrator ausführen“). Führen Sie die folgenden Befehle aus:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Versuchen Sie RDP auf Ihrem Computer auszuführen.  Wenn Sie möchten, können Sie bei erfolgreicher Ausführung die private IP-Adresse zurück zur ursprünglichen ändern. Andernfalls können Sie sie speichern.

### <a name="for-classic-vms"></a>Für klassische virtuelle Computer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Um die Netzwerkschnittstelle zurückzusetzen, gehen Sie folgendermaßen vor:

#### <a name="use-azure-portal"></a>Verwenden des Azure-Portals

1.  Öffnen Sie das [Azure-Portal]( https://ms.portal.azure.com).
2.  Wählen Sie **Virtuelle Computer (klassisch)** aus.
3.  Wählen Sie den betroffenen virtuellen Computer aus.
4.  Wählen Sie die **IP-Adressen** aus.
5.  Wenn die **Private IP-Zuweisung** nicht **Statisch** ist, ändern Sie diese in **Statisch**.
6.  Ändern Sie die **IP-Adresse** zu einer anderen IP-Adresse, die im Subnetz verfügbar ist.
7.  Wählen Sie **Speichern** aus.
8.  Der virtuelle Computer wird neu gestartet, um die neue Netzwerkkarte mit dem System zu initialisieren.
9.  Versuchen Sie RDP auf Ihrem Computer auszuführen. Auf Wunsch können Sie bei erfolgreicher Ausführung die private IP-Adresse wieder auf die ursprüngliche zurücksetzen.  

#### <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell

1. Stellen Sie sicher, dass Sie die [neueste Azure PowerShell-Version](/powershell/azure/) installiert haben.
2. Öffnen Sie eine Azure PowerShell Sitzung mit erhöhten Rechten („Als Administrator ausführen“). Führen Sie die folgenden Befehle aus:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Versuchen Sie RDP auf Ihrem Computer auszuführen. Wenn Sie möchten, können Sie bei erfolgreicher Ausführung die private IP-Adresse zurück zur ursprünglichen ändern. Andernfalls können Sie sie speichern. 

## <a name="delete-the-unavailable-nics"></a>Löschen Sie die nicht verfügbaren NICs
Nachdem Sie eine Remotedesktopverbindung mit dem Computer ausführen können, müssen Sie die alten NICs löschen, um potenzielle Probleme zu vermeiden:

1.  Öffnen Sie den Geräte-Manager.
2.  Wählen Sie die **Ansicht** > **Ausgeblendete Geräte anzeigen** aus.
3.  Wählen Sie die **Netzwerkadapter** aus. 
4.  Suchen Sie nach Adaptern mit dem Namen „Microsoft Hyper-V-Netzwerkadapter“.
5.  Möglicherweise sehen Sie einen nicht verfügbaren, abgeblendeten Adapter. Klicken Sie mit der rechten Maustaste auf den Adapter, und klicken Sie dann auf Deinstallieren.

    ![die Abbildung der Netzwerkschnittstellenkarte](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Deinstallieren Sie nur die nicht verfügbaren Adapter mit dem Namen „Microsoft Hyper-V-Netzwerkadapter“. Wenn Sie einen der anderen ausgeblendeten Adapter deinstallieren, können zusätzliche Probleme auftreten.
    >
    >

6.  Nun sollten nur noch verfügbare Adapter im System vorhanden sein.
