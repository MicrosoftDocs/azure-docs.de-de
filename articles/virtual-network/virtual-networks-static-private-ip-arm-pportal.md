---
title: Konfigurieren von privaten IP-Adressen für virtuelle Computer – Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie private IP-Adressen für virtuelle Computer über das Azure-Portal konfigurieren.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: kumud
ms.openlocfilehash: 31aeab946b9ad740e2f56eb1ecaafd3e76cc42b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64723794"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Konfigurieren von privaten IP-Adressen für einen virtuellen Computer über das Azure-Portal

> [!div class="op_single_selector"]
> * [Azure-Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure-Befehlszeilenschnittstelle](virtual-networks-static-private-ip-arm-cli.md)
> * [Azure-Portal (klassisch)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (klassisch)](virtual-networks-static-private-ip-classic-ps.md)
> * [Azure CLI (klassisch)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie können [eine statische private IP-Adresse auch im klassischen Bereitstellungsmodell verwalten](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Für die folgenden Schritte wird vorausgesetzt, dass bereits eine einfache Umgebung erstellt wurde. Wenn Sie die in diesem Dokument aufgeführten Schritte ohne Veränderungen ausführen möchten, erstellen Sie zunächst eine Testumgebung, wie unter [Erstellen eines virtuellen Netzwerks](quick-create-portal.md) beschrieben.

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Erstellen einer VM zum Testen statischer privater IP-Adressen
Im Azure-Portal ist es nicht möglich, beim Erstellen eines virtuellen Computers im Ressourcen-Manager-Bereitstellungsmodus eine statische private IP-Adresse festzulegen. Sie müssen den virtuellen Computer zuerst erstellen und dann seine private IP-Adresse als statisch festlegen.

Führen Sie die folgenden Schritte aus, um im VNET *TestVNet* im Subnetz *FrontEnd* den virtuellen Computer *DNS01* zu erstellen:

1. Navigieren Sie in einem Browser zu https://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **Ressource erstellen** > **Compute** > **Windows Server 2012 R2 Datacenter**. Beachten Sie, dass in der Liste **Bereitstellungsmodell auswählen** bereits **Resource Manager** angezeigt wird, und klicken Sie anschließend auf **Erstellen**, wie in der folgenden Abbildung gezeigt.
   
    ![Erstellen einer VM im Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. Geben Sie im Bereich **Grundlagen** den Namen des zu erstellenden virtuellen Computers (in diesem Szenario*DNS01*), das lokale Administratorkonto und das Kennwort ein, wie in der folgenden Abbildung gezeigt.
   
    ![Bereich „Grundlagen“](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Stellen Sie sicher, dass unter **Speicherort** die Region *USA, Mitte* ausgewählt ist. Klicken Sie anschließend **unter Ressourcengruppe** auf **Vorhandene auswählen**, klicken Sie erneut auf **Ressourcengruppe**, klicken Sie auf *TestRG* und dann auf **OK**.
   
    ![Bereich „Grundlagen“](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. Wählen Sie im Bereich **Größe auswählen** die Option **A1 Standard** aus, und klicken Sie anschließend auf **Auswählen**.
   
    ![Bereich „Größe auswählen“](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. Vergewissern Sie sich im Bereich **Einstellungen**, dass die Eigenschaften auf die folgenden Werte festgelegt sind, und klicken Sie dann auf **OK**.
   
    -**Speicherkonto**: *vnetstorage*
   
   * **Netzwerk:** *TestVNet*
   * **Subnetz**: *FrontEnd*
     
     ![Bereich „Größe auswählen“](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. Klicken Sie im Bereich **Zusammenfassung** auf **OK**. Beachten Sie die folgende Kachel, die im Dashboard angezeigt wird.
   
    ![Erstellen einer VM im Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Es wird davon abgeraten, die private IP-Adresse, die dem virtuellen Azure-Computer innerhalb des Betriebssystems einer VM zugewiesen ist, statisch zuzuweisen, sofern dies nicht erforderlich ist (z.B. beim [Zuweisen mehrerer IP-Adressen zu einer Windows-VM](virtual-network-multiple-ip-addresses-portal.md)). Wenn Sie die private IP-Adresse innerhalb des Betriebssystems manuell festlegen, sollten Sie sicherstellen, dass es sich um dieselbe Adresse wie die private IP-Adresse handelt, die der Azure-[Netzwerkschnittstelle](virtual-network-network-interface-addresses.md#change-ip-address-settings) zugewiesen ist. Andernfalls kann die Konnektivität mit dem virtuellen Computer verloren gehen. Erfahren Sie mehr über Einstellungen für [private IP-Adressen](virtual-network-network-interface-addresses.md#private). Die öffentliche IP-Adresse sollte niemals manuell einem virtuellen Azure-Computer innerhalb des Betriebssystems des virtuellen Computers zugewiesen werden.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Abrufen der Informationen zur statischen privaten IP-Adresse für einen virtuellen Computer
Um die Informationen zur statischen privaten IP-Adresse für den virtuellen Computer anzuzeigen, der mit den obigen Schritten erstellt wurde, führen Sie die folgenden Schritte aus.

1. Klicken Sie im Azure-Portal auf **ALLE DURCHSUCHEN** > **Virtuelle Computer** > **DNS01** > **Alle Einstellungen** > **Netzwerkschnittstellen**, und klicken Sie dann auf die einzige aufgeführte Netzwerkschnittstelle.
   
    ![Bereitstellen der VM-Kachel](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. Klicken Sie im Bereich **Netzwerkschnittstelle** auf **Alle Einstellungen** > **IP-Adressen**, und beachten Sie die Werte unter **Zuweisung** und **IP-Adresse**.
   
    ![Bereitstellen der VM-Kachel](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Hinzufügen einer statischen privaten IP-Adresse zu einem vorhandenen virtuellen Computer
Führen Sie die folgenden Schritte aus, um dem virtuellen Computer, der mit dem obigen Skript erstellt wurde, eine statische private IP-Adresse hinzuzufügen:

1. Klicken Sie im oben gezeigten Bereich **IP-Adressen** unter **Zuweisung** auf **Statisch**.
2. Geben Sie im Feld *IP-Adresse* den Wert **192.168.1.101** ein, und klicken Sie anschließend auf **Speichern**.
   
    ![Erstellen einer VM im Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Wenn Sie nach dem Klicken auf **Speichern** feststellen, dass für die Zuweisung immer noch **Dynamisch** angezeigt wird, bedeutet dies, dass die eingegebene IP-Adresse bereits verwendet wird. Versuchen Sie, eine andere IP-Adresse zu verwenden.
> 
> 

Es wird davon abgeraten, die private IP-Adresse, die dem virtuellen Azure-Computer innerhalb des Betriebssystems einer VM zugewiesen ist, statisch zuzuweisen, sofern dies nicht erforderlich ist (z.B. beim [Zuweisen mehrerer IP-Adressen zu einer Windows-VM](virtual-network-multiple-ip-addresses-portal.md)). Wenn Sie die private IP-Adresse innerhalb des Betriebssystems manuell festlegen, sollten Sie sicherstellen, dass es sich um dieselbe Adresse wie die private IP-Adresse handelt, die der Azure-[Netzwerkschnittstelle](virtual-network-network-interface-addresses.md#change-ip-address-settings) zugewiesen ist. Andernfalls kann die Konnektivität mit dem virtuellen Computer verloren gehen. Erfahren Sie mehr über Einstellungen für [private IP-Adressen](virtual-network-network-interface-addresses.md#private). Die öffentliche IP-Adresse sollte niemals manuell einem virtuellen Azure-Computer innerhalb des Betriebssystems des virtuellen Computers zugewiesen werden.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Entfernen einer statischen privaten IP-Adresse von einem virtuellen Computer
Um die statische private IP-Adresse für den zuvor erstellten virtuellen Computer zu entfernen, führen Sie den folgenden Schritt aus:

Klicken Sie im oben dargestellten Bereich **IP-Adressen** unter **Zuweisung** auf **Dynamisch**, und klicken Sie dann auf **Speichern**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Festlegen von IP-Adressen innerhalb des Betriebssystems

Es wird davon abgeraten, die private IP-Adresse, die dem virtuellen Azure-Computer innerhalb des Betriebssystems einer VM zugewiesen ist, statisch zuzuweisen sofern nicht erforderlich, wie es etwa beim [Zuweisen mehrerer IP-Adressen zu einer Windows-VM](virtual-network-multiple-ip-addresses-portal.md) der Fall ist. Wenn Sie die private IP-Adresse innerhalb des Betriebssystems manuell festlegen, sollten Sie sicherstellen, dass es sich um dieselbe Adresse wie die private IP-Adresse handelt, die der Azure-[Netzwerkschnittstelle](virtual-network-network-interface-addresses.md#change-ip-address-settings) zugewiesen ist. Andernfalls kann die Konnektivität mit dem virtuellen Computer verloren gehen. Erfahren Sie mehr über Einstellungen für [private IP-Adressen](virtual-network-network-interface-addresses.md#private). Die öffentliche IP-Adresse sollte niemals manuell einem virtuellen Azure-Computer innerhalb des Betriebssystems des virtuellen Computers zugewiesen werden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Verwaltung von [privaten IP-Adressen](virtual-network-network-interface-addresses.md).

