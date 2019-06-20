---
title: Zurücksetzen von Remotedesktopdienste oder dem zugehörigen Administratorkennwort auf einer Windows-VM | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Kennwort für ein Konto oder Remotedesktopdienste auf einem virtuellen Windows-Computer mit dem Azure-Portal oder Azure PowerShell zurücksetzen.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 0a12cbabc28640283f5a28eb7a83c7d7717e0882
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60921209"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Zurücksetzen von Remotedesktopdienste oder dem zugehörigen Administratorkennwort auf einer Windows-VM
Wenn Sie keine Verbindung mit einem virtuellen Windows-Computer herstellen können, können Sie Ihr lokales Administratorkennwort oder die Konfiguration von Remotedesktopdienste zurücksetzen (für Windows-Domänencontroller nicht unterstützt). Verwenden Sie zum Zurücksetzen des Kennworts entweder das Azure-Portal oder die VM-Zugriffserweiterung in Azure PowerShell. Setzen Sie das Kennwort für diesen lokalen Administrator zurück, nachdem Sie sich am virtuellen Computer angemeldet haben.  
Wenn Sie PowerShell verwenden, sollten Sie sicherstellen, dass das [neueste PowerShell-Modul installiert und konfiguriert](/powershell/azure/overview) ist und Sie bei Ihrem Azure-Abonnement angemeldet sind. Sie können diese Schritte auch für virtuelle Computer durchführen, die mit dem [klassischen Bereitstellungsmodell](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp) erstellt wurden.

Sie können Remotedesktopdienste und die Anmeldeinformationen wie folgt zurücksetzen:

- [Zurücksetzen über das Azure-Portal](#reset-by-using-the-azure-portal)

- [Zurücksetzen mit der VMAccess-Erweiterung und PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Zurücksetzen über das Azure-Portal

Melden Sie sich zuerst am [Azure-Portal](https://portal.azure.com) an, und wählen Sie im Menü auf der linken Seite dann die Option **Virtuelle Computer**. 

### <a name="reset-the-local-administrator-account-password"></a>**Zurücksetzen des Kennworts eines lokalen Administratorkontos**

1. Wählen Sie Ihre Windows-VM und dann unter **Support + Problembehandlung** die Option **Kennwort zurücksetzen**. Das Fenster **Kennwort zurücksetzen** wird angezeigt.

2. Wählen Sie **Kennwort zurücksetzen**, geben Sie einen Benutzernamen und ein Kennwort ein, und wählen Sie dann **Aktualisieren**. 

3. Versuchen Sie erneut, eine Verbindung mit Ihrem virtuellen Computer herzustellen.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Zurücksetzen der Konfiguration für Remotedesktopdienste**

Mit diesem Prozess wird der Remotedesktopdienst auf der VM aktiviert, und es wird eine Firewallregel für den RDP-Standardport 3389 erstellt.

1. Wählen Sie Ihre Windows-VM und dann unter **Support + Problembehandlung** die Option **Kennwort zurücksetzen**. Das Fenster **Kennwort zurücksetzen** wird angezeigt. 

2. Wählen Sie **Reset configuration only** (Nur Konfiguration zurücksetzen) und dann **Aktualisieren**. 

3. Versuchen Sie erneut, eine Verbindung mit Ihrem virtuellen Computer herzustellen.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Zurücksetzen mit der VMAccess-Erweiterung und PowerShell

Stellen Sie zunächst mit dem Cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) sicher, dass das [neueste PowerShell-Modul installiert und konfiguriert ist](/powershell/azure/overview) und Sie bei Ihrem Azure-Abonnement angemeldet sind.

### <a name="reset-the-local-administrator-account-password"></a>**Zurücksetzen des Kennworts eines lokalen Administratorkontos**

- Sie setzen das Administratorkennwort oder den Benutzernamen mithilfe des PowerShell-Cmdlets [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) zurück. Die Einstellung `typeHandlerVersion` muss 2.0 oder höher lauten, da Version 1 veraltet ist. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Wenn Sie einen anderen Namen als das aktuelle lokale Administratorkonto auf dem virtuellen Computer eingeben, fügt die VMAccess-Erweiterung ein lokales Administratorkonto mit diesem Namen hinzu und weist dem Konto das angegebene Kennwort zu. Wenn das lokale Administratorkonto auf Ihrem virtuellen Computer vorhanden ist, setzt die VMAccess-Erweiterung das Kennwort zurück. Wenn das Konto deaktiviert ist, wird es von der VMAccess-Erweiterung aktiviert.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Zurücksetzen der Konfiguration für Remotedesktopdienste**

1. Sie setzen den Remotezugriff auf Ihren virtuellen Computer mit dem PowerShell-Cmdlet [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) zurück. Im folgenden Beispiel wird die Zugriffserweiterung `myVMAccess` in der VM `myVM` in der Ressourcengruppe `myResourceGroup` zurückgesetzt:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > Zu jedem Zeitpunkt kann eine VM nur einen einzelnen VM-Zugriffs-Agent haben. Verwenden Sie die Option `-ForceRerun`, um die Eigenschaften für den VM-Zugriffs-Agent festzulegen. Stellen Sie bei Verwendung von `-ForceRerun` sicher, dass Sie den gleichen Namen für den VM-Zugriffs-Agent verwenden, den Sie ggf. bereits in vorherigen Befehlen genutzt haben.

1. Wenn Sie weiterhin nicht remote auf den virtuellen Computer zugreifen können, helfen Ihnen die Informationen unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) weiter. Wenn die Verbindung zum Windows-Domänencontroller getrennt wird, müssen Sie sie aus einer Domänencontrollersicherung wiederherstellen.

## <a name="next-steps"></a>Nächste Schritte

- Falls die Erweiterung für den Zugriff auf virtuelle Computer nicht reagiert und Sie das Kennwort nicht zurücksetzen können, können Sie [das lokale Windows-Kennwort offline zurücksetzen](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Dies ist eine anspruchsvollere Methode, bei der Sie die virtuelle Festplatte des problematischen virtuellen Computers mit einem anderen virtuellen Computer verbinden müssen. Führen Sie zunächst die in diesem Artikel dokumentierten Schritte aus. Die Methode zur Offlinezurücksetzung des Kennworts sollte nur genutzt werden, wenn diese Schritte nicht funktionieren.

- [Informationen zu Azure-VM-Erweiterungen und -Features](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

- [Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx)

- [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

