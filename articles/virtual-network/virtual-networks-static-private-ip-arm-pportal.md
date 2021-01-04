---
title: Konfigurieren privater IP-Adressen für virtuelle Computer – Azure-Portal
description: Erfahren Sie, wie Sie private IP-Adressen für virtuelle Computer über das Azure-Portal konfigurieren.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015923"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Konfigurieren einer privaten IP-Adresse für eine VM im Azure-Portal

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Für die folgenden Beispielschritte wird vorausgesetzt, dass bereits eine einfache Umgebung erstellt wurde. Wenn Sie die in diesem Dokument aufgeführten Schritte so wie beschrieben ausführen möchten, müssen Sie zunächst [ein virtuelles Netzwerk erstellen](quick-create-portal.md#create-a-virtual-network). Verwenden Sie in Schritt 3 jedoch stattdessen diese Werte:

| Einstellung | Wert |
| ------- | ----- |
| Name | *TestVNet* |
| Adressraum | *192.168.0.0/16* |
| Ressourcengruppe | **TestRG** (wählen Sie bei Bedarf **Neu erstellen** aus, um sie zu erstellen) |
| Subnetzname | *FrontEnd* |
| Subnetzadressbereich | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Erstellen einer VM zum Testen statischer privater IP-Adressen
Wenn Sie eine VM im Resource Manager-Bereitstellungsmodus erstellen, können Sie im Azure-Portal keine statische private IP-Adresse festlegen. Stattdessen erstellen Sie zunächst die VM. Anschließend können Sie deren private IP-Adresse als statisch festlegen.

Führen Sie die folgenden Schritte aus, um im virtuellen Netzwerk *TestVNet* im Subnetz *FrontEnd* die VM *DNS01* zu erstellen:

1. Wählen Sie im Menü [Azure-Portal](https://portal.azure.com) die Option **Ressource erstellen** aus.

    ![Erstellen einer Ressource, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Wählen Sie **Compute** > **Virtueller Computer** aus.

    ![Erstellen einer VM, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. Geben Sie in **Grundlagen** Werte für die Elemente ein, die in der folgenden Tabelle beschrieben werden. Wählen Sie dann **Weiter&nbsp;:&nbsp;Datenträger** und dann **Weiter&nbsp;:&nbsp;Netzwerk** aus.

    | Element | Wert |
    | --- | --- |
    | **Abonnement** | Ihr aktuelles Abonnement |
    | **Ressourcengruppe** | **TestRG** (in Dropdownliste auswählen) |
    | **Name des virtuellen Computers** | *DNS01* |
    | **Region** | **USA, Osten** |
    | **Image** | **Windows Server 2019 Datacenter** |
    | **Größe** | **VM-Größe**: **B1ls**, **Angebot**: **Standard** |
    | **Benutzername** | Der Benutzername Ihres Administratorkontos |
    | **Kennwort** | Das Kennwort des Benutzernamens Ihres Administratorkontos |
    | **Kennwort bestätigen** | Kennwort erneut eingeben |

    ![Registerkarte „Grundlagen“, VM erstellen, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. Geben Sie in **Netzwerk** Werte für die Elemente ein, die in der folgenden Tabelle beschrieben sind, und wählen Sie dann **Weiter** aus.

    | Element | Wert |
    | --- | --- |
    | **Virtuelles Netzwerk** | **TestVNet** |
    | **Subnetz** | **FrontEnd** |

    ![Registerkarte „Netzwerk“, VM erstellen, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. Wählen Sie in **Verwaltung** unter **Diagnosespeicherkonto** die Option **vnetstorage** aus. Wenn dieses Speicherkonto nicht in der Liste angezeigt wird, wählen Sie **Neu erstellen** aus. Geben Sie *vnetstorage* in **Name** ein, und wählen Sie **OK** aus. Wählen Sie **Überprüfen&nbsp;+&nbsp;erstellen** aus.

    ![Registerkarte „Verwaltung“, VM erstellen, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. Prüfen Sie in **Überprüfen + erstellen** die Informationen in der Übersicht, und wählen Sie dann **Erstellen** aus.

    ![Registerkarte „Überprüfen + erstellen“, VM erstellen, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Nachdem die VM erstellt wurde, wird die folgende Meldung angezeigt.

![Meldung zum Abschluss der Bereitstellung, VM erstellen, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Abrufen von Informationen zur privaten IP-Adresse einer VM
So zeigen Sie Informationen zur privaten IP-Adresse Ihrer neuen VM an

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer VM. Suchen Sie nach **Virtuelle Computer**, und wählen Sie diese Option aus.

    ![Virtuelle Computer, Feld „Suchen“, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Wählen Sie den Namen Ihrer neuen VM aus (**DNS01**).

    ![Liste der virtuellen Computer, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Wählen Sie **Netzwerk** und dann die einzige aufgeführte Netzwerkschnittstelle aus.

    ![Netzwerkschnittstelle, Netzwerk, virtueller Computer, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Wählen Sie **IP-Konfigurationen** und dann die in der Tabelle aufgeführte IP-Konfiguration aus.

    ![IP-Konfiguration, Netzwerkschnittstelle, Netzwerk, virtueller Computer, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. Beachten Sie in **Einstellungen für private IP-Adressen** unter dem virtuellen Netzwerk/Subnetz **TestVNet/FrontEnd** den Wert **Zuweisung** (**Dynamisch** oder **Statisch**) und die **IP-Adresse**.

    ![Dynamische oder statische Zuweisung, Einstellungen der alten, privaten IP-Adresse, IP-Konfiguration, Netzwerkschnittstelle, Netzwerk, virtueller Computer, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Hinzufügen einer statischen privaten IP-Adresse zu einem vorhandenen virtuellen Computer
So fügen Sie Ihrer neuen VM eine statische private IP-Adresse hinzu

1. Legen Sie auf der Seite „IP-Konfiguration“ die Zuweisung Ihrer privaten IP-Adresse auf **Statisch** fest.
2. Ändern Sie Ihre private **IP-Adresse** in *192.168.1.101*, und wählen Sie **Speichern** aus.
   
    ![Dynamische oder statische Zuweisung, Einstellungen der neuen, privaten IP-Adresse, IP-Konfiguration, Netzwerkschnittstelle, Netzwerk, virtueller Computer, Azure-Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Wenn Sie nach Auswählen von **Speichern** feststellen, dass die Zuweisung immer noch auf **Dynamisch** festgelegt ist, wird die eingegebene IP-Adresse bereits verwendet. Probieren Sie eine andere IP-Adresse.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Entfernen einer statischen privaten IP-Adresse von einem virtuellen Computer
So entfernen Sie die statische private IP-Adresse Ihrer VM

Legen Sie auf der Seite „IP-Konfiguration“ die Zuweisung Ihrer privaten IP-Adresse auf **Dynamisch** fest, und wählen Sie dann **Speichern** aus.

## <a name="set-ip-addresses-within-the-operating-system"></a>Festlegen von IP-Adressen innerhalb des Betriebssystems

Im Betriebssystem einer VM dürfen Sie die *private* IP-Adresse, die der Azure-VM zugewiesen ist, nicht statisch zuweisen. Nehmen Sie die statische Zuweisung einer privaten IP-Adresse nur dann vor, wenn es notwendig ist, wie z. B. beim [Zuweisen vieler IP-Adressen zu VMs](virtual-network-multiple-ip-addresses-portal.md). Wenn Sie die private IP-Adresse innerhalb des Betriebssystems manuell festlegen, stellen Sie sicher, dass sie mit der privaten IP-Adresse übereinstimmt, die der Azure-[Netzwerkschnittstelle](virtual-network-network-interface-addresses.md#change-ip-address-settings) zugewiesen ist. Andernfalls kann Konnektivität mit der VM verloren gehen. Erfahren Sie mehr über Einstellungen für [private IP-Adressen](virtual-network-network-interface-addresses.md#private).

Außerdem darf die *öffentliche* IP-Adresse, die einem virtuellen Azure-Computer innerhalb des Betriebssystems des virtuellen Computers zugewiesen wurde, niemals manuell zugewiesen werden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Verwaltung von [privaten IP-Adressen](virtual-network-network-interface-addresses.md).
