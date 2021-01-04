---
title: Aktivieren der Browserverbindung auf Azure DevTest Labs-VMs
description: DevTest Labs und Azure Bastion sind jetzt integriert. Als Besitzer des Labs können Sie den Zugriff auf alle Lab-VMs über einen Browser aktivieren.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6d9d631c79c22f1f713cfc4ee7cdd766a4ad8f06
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341171"
---
# <a name="enable-browser-connection-on-azure-devtest-labs-virtual-machines"></a>Aktivieren der Browserverbindung auf Azure DevTest Labs-VMs 
DevTest Labs und [Azure Bastion](../bastion/index.yml) sind integriert, sodass Sie über einen Browser eine Verbindung mit Ihren virtuellen Computern herstellen können. Zuerst müssen Sie die Browserverbindung auf Lab-VMs aktivieren.

Als Besitzer eines Labs können Sie den Zugriff auf alle Lab-VMs über einen Browser ermöglichen. Dazu sind kein zusätzlicher Client oder Agent und keine zusätzliche Software erforderlich. Azure Bastion bietet direkt im Azure-Portal sichere und nahtlose RDP- und SSH-Verbindungen mit Ihren virtuellen Computern über TLS. Beim Herstellen einer Verbindung über Azure Bastion benötigen Ihre virtuellen Computer keine öffentliche IP-Adresse. Weitere Informationen finden Sie unter [Was ist Azure Bastion?](../bastion/bastion-overview.md)


Dieser Artikel erläutert, wie Sie die Browserverbindung auf Lab-VMs aktivieren.

## <a name="prerequisites"></a>Voraussetzungen 
- Sie können einen Bastionhost im virtuellen Netzwerk Ihres bestehenden Labs bereitstellen **ODER** Ihr Lab mit einem mit Bastion konfigurierten virtuellen Netzwerk verbinden.
Informationen zum Bereitstellen eines Bastionhosts in einem virtuellen Netzwerk finden Sie unter [Erstellen eines Azure Bastion-Hosts](../bastion/tutorial-create-host-portal.md). Wenn Sie den Bastion-Host erstellen, wählen Sie das virtuelle Netzwerk des Labs aus. 
- Der Labbenutzer muss über eine Rolle **Leser** auf dem Bastionhost und in dem virtuellen Netzwerk verfügen, in dem Bastion konfiguriert ist. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Erstellen eines zweiten Subnetzes im virtuellen Bastion-Netzwerk
Zunächst müssen Sie ein zweites Subnetz im virtuellen Bastion-Netzwerk erstellen, da in AzureBastionSubnet keine Nicht-Bastion-Ressourcen erstellt werden können. Erstellen Sie ein weiteres Subnetz innerhalb des virtuellen Bastion-Netzwerks, wie in der folgenden Abbildung dargestellt:

![Zweites Subnetz in einem virtuellen Azure Bastion-Netzwerk](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Aktivieren der VM-Erstellung im Subnetz
Aktivieren Sie nun die Erstellung von VMs in diesem Subnetz über die folgenden Schritte: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsmenü die Option **Alle Dienste** aus. 
1. Wählen Sie in der Liste **DevTest Labs** aus. 
1. Wählen Sie in der Liste der Labs Ihr *Lab* aus. 

    > [!NOTE]
    > Azure Bastion ist jetzt in den folgenden Regionen allgemein verfügbar: USA, Westen; USA, Osten; Europa, Westen; USA, Süden-Mitte; Australien, Osten und Japan, Osten. Erstellen Sie also ein Lab in einer dieser Regionen, wenn sich ihr Lab nicht in einer davon befindet. 
    
1. Wählen Sie im Abschnitt **Einstellungen** die Option **Konfiguration und Richtlinien** aus. 
1. Wählen Sie **Virtuelle Netzwerke** aus.
1. Klicken Sie auf der Symbolleiste auf **Hinzufügen**. 
1. Wählen Sie das **virtuelle Netzwerk** aus, in dem der Bastionhost bereitgestellt ist. 
1. Wählen Sie das Subnetz für VMs aus, nicht das zuvor erstellte **AzureBastionSubnet**. Wenn das Subnetz nicht unten in der Liste angezeigt wird, schließen Sie die Seite, und öffnen Sie sie erneut. 

    ![Aktivieren der VM-Erstellung im Subnetz](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Wählen Sie in die Option **Beim Erstellen virtueller Computer verwenden** aus. 
1. Wählen Sie auf der Symbolleiste **Speichern** aus. 
1. Wenn Sie über ein altes virtuelles Netzwerk für das Lab verfügen, entfernen Sie es, indem Sie * *...* und **Entfernen** auswählen. 

## <a name="enable-browser-connection"></a>Aktivieren der Browserverbindung 

Sobald Sie über ein mit Bastion konfiguriertes virtuelles Netzwerk im Lab verfügen, können Sie als Besitzer des Labs die Browserverbindung auf Lab-VMs aktivieren.

Gehen Sie wie folgt vor, um die Browserverbindung auf Lab-VMs zu aktivieren:

1. Navigieren Sie im Azure-Portal zu Ihrem *Lab*.
1. Wählen Sie **Konfiguration und Richtlinien** aus.
1. Wählen Sie unter **Einstellungen** die Option **Browserverbindung** aus. Wenn diese Option nicht angezeigt wird, schließen Sie die Seite **Konfigurationsrichtlinien**, und öffnen Sie sie erneut. 

    ![Aktivieren der Browserverbindung](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Nächste Schritte
Im folgenden Artikel erfahren Sie, wie Sie mithilfe eines Browsers eine Verbindung mit Ihren virtuellen Computern herstellen: [Herstellen einer Verbindung mit Ihren virtuellen Computern über einen Browser](connect-virtual-machine-through-browser.md)