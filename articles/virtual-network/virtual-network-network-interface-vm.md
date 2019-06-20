---
title: 'Virtuelle Azure-Computer: Hinzufügen oder Entfernen von Netzwerkschnittstellenkarten | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Netzwerkschnittstellen zu virtuellen Computern hinzufügen oder aus diesen entfernen können.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: kumud
ms.openlocfilehash: 23e46290af6bdb4c217d8fa0cd836673652fc81d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64701377"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Hinzufügen von Netzwerkschnittstellen zu virtuellen Computern oder Entfernen von Netzwerkschnittstellen aus diesen

Erfahren Sie, wie eine vorhandene Netzwerkschnittstellenkarte hinzugefügt wird, wenn Sie einen virtuellen Azure-Computer erstellen, und wie Netzwerkschnittstellen einem virtuellen Computer mit dem Zustand „Beendet (Zuordnung aufgehoben)“ hinzugefügt oder von diesem entfernt werden. Eine Netzwerkschnittstelle ermöglicht einem virtuellen Azure-Computer die Kommunikation mit dem Internet, Azure und lokalen Ressourcen. Ein virtueller Computer kann eine oder mehrere Netzwerkschnittstellen haben. 

Weitere Informationen zum Hinzufügen, Ändern oder Entfernen von IP-Adressen für eine Netzwerkschnittstelle finden Sie unter [Verwalten von IP-Adressen für Netzwerkschnittstellen](virtual-network-network-interface-addresses.md). Informationen zum Erstellen, Ändern oder Löschen von Netzwerkschnittstellen finden Sie unter [Verwalten von Netzwerkschnittstellen](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Führen Sie zuerst die folgenden Aufgaben aus, ehe Sie die Schritte in den Abschnitten dieses Artikels durchführen:

- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/free) registrieren.
- Öffnen Sie bei Verwendung des Portals https://portal.azure.com, und melden Sie sich mit Ihrem Azure-Konto an.
- Wenn Sie PowerShell-Befehle zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder durch Ausführen von PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Für dieses Tutorial ist das Azure PowerShell-Modul Version 1.0.0 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.
- Wenn Sie Befehle der Azure-Befehlszeilenschnittstelle (CLI) zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder durch Ausführen der CLI auf Ihrem Computer aus. Für dieses Tutorial ist mindestens Version 2.0.26 der Azure CLI erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Hinzufügen vorhandener Netzwerkschnittstellen zu einer neuen VM

Beim Erstellen eines virtuellen Computers im Portal wird eine Netzwerkschnittstelle mit Standardeinstellungen generiert, die dem virtuellen Computer für Sie hinzugefügt wird. Über das Azure-Portal ist es nicht möglich, vorhandene Netzwerkschnittstellen an einen neuen virtuellen Computer anzufügen oder einen virtuellen Computer mit mehreren Netzwerkschnittstellen zu erstellen. Sie können beides über die Befehlszeilenschnittstelle oder mit PowerShell erreichen, sollten sich dazu aber mit den [Einschränkungen](#constraints) vertraut machen. Wenn Sie einen virtuellen Computer mit mehreren Netzwerkschnittstellen erstellen, müssen Sie auch das Betriebssystem für die ordnungsgemäße Verwendung der Schnittstellen nach der Erstellung des virtuellen Computers konfigurieren. Erfahren Sie, wie Sie [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) oder [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) für mehrere Netzwerkschnittstellen konfigurieren.

### <a name="commands"></a>Befehle

Erstellen Sie vor dem Erstellen des virtuellen Computers mithilfe der Schritte unter [Erstellen einer Netzwerkschnittstelle](virtual-network-network-interface.md#create-a-network-interface) eine Netzwerkschnittstelle.

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Hinzufügen einer Netzwerkschnittstelle zu einem vorhandenen virtuellen Computer

1. Melden Sie sich beim Azure-Portal an.
2. Geben Sie im Suchfeld im oberen Bereich des Portals den Namen des virtuellen Computers ein, dem Sie die Netzwerkschnittstelle hinzufügen möchten, oder suchen Sie durch Auswählen von **Alle Dienste** und dann **Virtuelle Computer** nach dem virtuellen Computer. Wenn Sie den virtuellen Computer gefunden haben, wählen Sie ihn aus. Der virtuelle Computer muss die Anzahl an Netzwerkschnittstellen, die Sie hinzufügen möchten, unterstützen. Weitere Informationen dazu, wie viele Netzwerkschnittstellen jede VM-Größe unterstützt, finden Sie unter [Größen für virtuelle Linux-Computer in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Größen für virtuelle Windows-Computer in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Übersicht** aus. Wählen Sie **Beenden** aus, und warten Sie, bis der **Status** des virtuellen Computers in **Beendet (Zuordnung aufgehoben)** geändert wird.
4. Wählen Sie unter **EINSTELLUNGEN** die Option **Netzwerk** aus.
5. Wählen Sie **Netzwerkschnittstelle anfügen** aus. Wählen Sie in der Liste der Netzwerkschnittstellen, die derzeit an keinen anderen virtuellen Computer angefügt sind, diejenige aus, die Sie anfügen möchten.

   >[!NOTE]
   >Für die ausgewählte Netzwerkschnittstelle darf kein beschleunigter Netzwerkbetrieb aktiviert sein, es darf keine IPv6-Adresse zugewiesen sein, sie muss sich im selben virtuellen Netzwerk befinden, in dem sich die derzeit an den virtuellen Computer angefügte Netzwerkschnittstelle befindet.

   Wenn keine Netzwerkschnittstelle vorhanden ist, müssen Sie zuerst eine erstellen. Wählen Sie dazu **Netzwerkschnittstelle erstellen** aus. Weitere Informationen zum Erstellen einer Netzwerkschnittstelle finden Sie unter [Erstellen einer Netzwerkschnittstelle](virtual-network-network-interface.md#create-a-network-interface). Weitere Informationen zu zusätzlichen Einschränkungen beim Hinzufügen von Netzwerkschnittstellen zu virtuellen Computern finden Sie unter [Einschränkungen](#constraints).

6. Klicken Sie auf **OK**.
7. Wählen Sie unter **EINSTELLUNGEN** die Option **Übersicht** aus, und wählen Sie dann **Starten** aus, um den virtuellen Computer zu starten.
8. Konfigurieren Sie das VM-Betriebssystem für die ordnungsgemäße Verwendung mehrerer Netzwerkschnittstellen. Erfahren Sie, wie Sie [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) oder [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) für mehrere Netzwerkschnittstellen konfigurieren.

### <a name="commands"></a>Befehle
|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (Verweis) or [detaillierte Schritte](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (Verweis) oder [detaillierte Schritte](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Anzeigen der Netzwerkschnittstellen für einen virtuellen Computer

Sie können die derzeit zu einem virtuellen Computer hinzugefügten Netzwerkschnittstellen anzeigen, um mehr über die Konfiguration der einzelnen Netzwerkschnittstellen und die diesen zugewiesenen IP-Adressen zu erfahren. 

1. Melden Sie sich mit einem Konto, dem für Ihr Abonnement eine der Rollen „Besitzer“, „Mitwirkender“ oder „Netzwerkmitwirkender“ zugewiesen ist, beim [Azure-Portal](https://portal.azure.com) an. Weitere Informationen zum Zuweisen von Rollen zu Konten finden Sie unter [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text **Ressourcen suchen** die Zeichenfolge **Virtuelle Computer** ein. Wenn in den Suchergebnissen **Virtuelle Computer** angezeigt wird, wählen Sie diesen Eintrag aus.
3. Wählen Sie den Namen des virtuellen Computers aus, dessen Netzwerkschnittstellen Sie anzeigen möchten.
4. Wählen Sie im Abschnitt **EINSTELLUNGEN** für den ausgewählten virtuellen Computer die Option **Netzwerk** aus. Weitere Informationen zu Netzwerkschnittstellen-Einstellungen finden Sie im Artikel [Verwalten von Netzwerkschnittstellen](virtual-network-network-interface.md). Weitere Informationen zum Hinzufügen, Ändern oder Entfernen der einer Netzwerkschnittstelle zugewiesenen IP-Adressen finden Sie unter [Verwalten von IP-Adressen für Netzwerkschnittstellen](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Befehle

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Entfernen einer Netzwerkschnittstelle von einem virtuellen Computer

1. Melden Sie sich beim Azure-Portal an.
2. Suchen Sie im Suchfeld im oberen Bereich des Portals nach dem Namen des virtuellen Computers, von dem Sie die Netzwerkschnittstelle entfernen (trennen) möchten, oder suchen Sie durch Auswählen von **Alle Dienste** und dann **Virtuelle Computer** nach dem virtuellen Computer. Wenn Sie den virtuellen Computer gefunden haben, wählen Sie ihn aus.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Übersicht** und dann **Beenden** aus. Warten Sie, bis der **Status** des virtuellen Computers in **Beendet (Zuordnung aufgehoben)** geändert wird.
4. Wählen Sie unter **EINSTELLUNGEN** die Option **Netzwerk** aus.
5. Wählen Sie **Netzwerkschnittstelle trennen** aus. Wählen Sie in der Liste der Netzwerkschnittstellen, die derzeit an den virtuellen Computer angefügt sind, die Netzwerkschnittstelle aus, die Sie trennen möchten.

   >[!NOTE]
   >Wenn nur eine Netzwerkschnittstelle aufgeführt ist, können Sie diese nicht trennen, da an einen virtuellen Computer immer mindestens eine Netzwerkschnittstelle angefügt sein muss.
6. Klicken Sie auf **OK**.

### <a name="commands"></a>Befehle

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (Verweis) or [detaillierte Schritte](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (Verweis) oder [detaillierte Schritte](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Einschränkungen

- An einen virtuellen Computer muss mindestens eine Netzwerkschnittstelle angefügt sein.
- Es können an einen virtuellen Computer nur so viele Netzwerkschnittstellen angefügt sein, wie die VM-Größe unterstützt. Weitere Informationen dazu, wie viele Netzwerkschnittstellen jede VM-Größe unterstützt, finden Sie unter [Größen für virtuelle Linux-Computer in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Größen für virtuelle Windows-Computer in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Alle Größen unterstützen mindestens zwei Netzwerkschnittstellen.
- Die Netzwerkschnittstellen, die Sie zu einem virtuellen Computer hinzufügen, können derzeit zu keinem anderen virtuellen Computer hinzugefügt werden. Weitere Informationen zum Erstellen von Netzwerkschnittstellen finden Sie unter [Erstellen einer Netzwerkschnittstelle](virtual-network-network-interface.md#create-a-network-interface).
- In der Vergangenheit konnten Netzwerkschnittstellen nur zu virtuellen Computern hinzugefügt werden, die mehrere Netzwerkschnittstellen unterstützten und mit mindestens zwei Netzwerkschnittstellen erstellt wurden. Es konnte keine Netzwerkschnittstelle zu einem virtuellen Computer hinzugefügt werden, der mit einer Netzwerkschnittstelle erstellt wurde, auch wenn die Größe des virtuellen Computers mehrere Netzwerkschnittstellen unterstützte. Im Gegenzug konnten Sie nur Netzwerkschnittstellen von einem virtuellen Computer mit mindestens drei Netzwerkschnittstellen entfernen, da virtuelle Computer, die mit zwei Netzwerkschnittstellen erstellt wurden, immer mindestens zwei Netzwerkschnittstellen haben mussten. Keine dieser Einschränkungen trifft noch zu. Sie können nun einen virtuellen Computer mit einer beliebigen Anzahl von Netzwerkschnittstellen (bis zu der von der VM-Größe unterstützten Anzahl) erstellen.
- Standardmäßig wird die erste an einen virtuellen Computer angefügte Netzwerkschnittstelle als *primäre* Netzwerkschnittstelle definiert. Alle anderen Netzwerkschnittstellen auf dem virtuellen Computer sind *sekundäre* Netzwerkschnittstellen.
- Sie können festlegen, an welche Netzwerkschnittstelle ausgehender Datenverkehr gesendet werden soll. Standardmäßig wird sämtlicher ausgehender Datenverkehr des virtuellen Computers jedoch über die IP-Adresse gesendet, die der primären IP-Konfiguration der primären Netzwerkschnittstelle zugewiesen ist.
- In der Vergangenheit mussten alle virtuellen Computer in der gleichen Verfügbarkeitsgruppe über eine einzelne Netzwerkschnittstelle (oder über mehrere Netzwerkschnittstellen) verfügen. In einer Verfügbarkeitsgruppe können nun VMs mit bis zu der von der VM-Größe unterstützten Anzahl an Netzwerkschnittstellen vertreten sein. Sie können eine VM erst dann einer Verfügbarkeitsgruppe hinzufügen, nachdem sie erstellt wurde. Weitere Informationen zu Verfügbarkeitsgruppen finden Sie unter [Verwalten der Verfügbarkeit von VMs in Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Netzwerkschnittstellen auf demselben virtuellen Computer können zwar mit unterschiedlichen Subnetzen in einem virtuellen Netzwerk verbunden sein, sie müssen jedoch alle mit demselben virtuellen Netzwerk verbunden sein.
- Sie können eine beliebige IP-Adresse für eine beliebige IP-Konfiguration einer beliebigen primären oder sekundären Netzwerkschnittstelle einem Back-End-Pool von Azure Load Balancer hinzufügen. Bisher konnte nur die primäre IP-Adresse für die primäre Netzwerkschnittstelle einem Back-End-Pool hinzugefügt werden. Weitere Informationen zu IP-Adressen und -Konfigurationen finden Sie im Artikel [Hinzufügen, Ändern oder Entfernen von IP-Adressen](virtual-network-network-interface-addresses.md).
- Beim Löschen eines virtuellen Computers werden seine Netzwerkschnittstellen nicht gelöscht. Wenn Sie einen virtuellen Computer löschen, werden die Netzwerkschnittstellen von dem virtuellen Computer getrennt. Sie können die Netzwerkschnittstellen an andere virtuelle Computer anfügen oder löschen.
- Wenn einer Netzwerkschnittstelle private IPv6-Adressen zugewiesen sind, müssen Sie sie an einen virtuellen Computer anfügen, wenn Sie diesen erstellen. Sie können einem virtuellen Computer keine Netzwerkschnittstelle mit einer zugewiesenen IPv6-Adresse mehr hinzufügen, nachdem Sie diesen erstellt haben. Wenn Sie beim Erstellen eines virtuellen Computers eine Netzwerkschnittstelle mit einer zugewiesenen privaten IPv6-Adresse hinzufügen, können Sie dem virtuellen Computer nur diese Netzwerkschnittstelle hinzufügen, unabhängig davon, wie viele Netzwerkschnittstellen die VM-Größe unterstützt. Weitere Informationen zum Zuweisen von IP-Adressen zu Netzwerkschnittstellen finden Sie unter [Verwalten von IP-Adressen für Netzwerkschnittstellen](virtual-network-network-interface-addresses.md).
- Wie bei IPv6 können Sie eine Netzwerkschnittstelle, auf der beschleunigter Netzwerkbetrieb aktiviert ist, nicht an einen virtuellen Computer anfügen, nachdem Sie diesen erstellt haben. Um beschleunigten Netzwerkbetrieb nutzen zu können, müssen Sie außerdem auch Schritte innerhalb des VM-Betriebssystems ausführen. Weitere Informationen zum beschleunigten Netzwerkbetrieb und weiteren Einschränkungen bei dessen Verwendung finden Sie in den entsprechenden Abschnitten für virtuelle [Windows](create-vm-accelerated-networking-powershell.md)- oder [Linux](create-vm-accelerated-networking-cli.md)-Computer.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie, wie Sie virtuelle Computer mit mehreren Netzwerkschnittstellen oder IP-Adressen erstellen:

|Aufgabe|Tool|
|---|---|
|Erstellen eines virtuellen Computers mit mehreren Netzwerkschnittstellenkarten (NICs)|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Erstellen eines virtuellen Computers mit einer NIC und mehreren IPv4-Adressen|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Erstellen eines virtuellen Computers mit einer NIC und einer IPv6-Adresse (mit Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-Vorlage](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
