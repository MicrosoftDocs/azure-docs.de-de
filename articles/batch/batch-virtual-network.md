---
title: Bereitstellen eines Pools in einem virtuellen Netzwerk – Azure Batch | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Batch-Pool in einem virtuellen Netzwerk erstellen, damit Computeknoten sicher mit anderen VMs (z. B. Dateiserver) im Netzwerk kommunizieren können.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 2583e7e218e765e0d7745978582e19a5a4fe17ce
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550200"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Erstellen eines Azure Batch-Pools in einem virtuellen Netzwerk

Beim Erstellen eines Azure Batch-Pools können Sie einen Pool in einem von Ihnen angegebenen Subnetz eines [virtuellen Azure-Netzwerks](../virtual-network/virtual-networks-overview.md) (VNET) bereitstellen. In diesem Artikel wird erklärt, wie Sie einen Batch-Pool in einem VNET einrichten. 

## <a name="why-use-a-vnet"></a>Gründe für die Verwendung eines VNETs

Ein Azure Batch-Pool verfügt über Einstellung, durch die Computeknoten miteinander kommunizieren können, z.B. zum Ausführen von Tasks mit mehreren Instanzen. Für diese Einstellungen ist kein separates VNET erforderlich. Allerdings können die Knoten standardmäßig nicht mit VMs kommunizieren, die Teil des Batch-Pools sind, wie etwa Lizenzserver und Dateiserver. Um die sichere Kommunikation zwischen Computeknoten und VMs oder lokalen Netzwerken zu ermöglichen, können Sie den Pool in einem Subnetz eines Azure VNETs bereitstellen. 

## <a name="prerequisites"></a>Voraussetzungen

* **Authentifizierung**. Zur Verwendung eines Azure VNETs muss die Batch-Client-API die Azure Active Directory-Authentifizierung verwenden. Die Azure Batch-Unterstützung für Azure AD ist unter [Authentifizieren von Lösungen des Azure Batch-Diensts mit Active Directory](batch-aad-auth.md) dokumentiert. 

* **Ein Azure VNET** Im folgenden Abschnitt finden Sie Informationen zu den Anforderungen und der Konfiguration des VNET. Um ein VNET mit mindestens einem Subnetz vorzubereiten, können Sie das Azure-Portal, Azure PowerShell, die Azure-Befehlszeilenschnittstelle (CLI) oder andere Methoden verwenden.  
  * Um ein auf Azure Resource Manager basiertes VNET zu erstellen, lesen Sie die Informationen unter [Create, change, or delete a virtual network](../virtual-network/manage-virtual-network.md#create-a-virtual-network) (Erstellen, Ändern oder Löschen eines virtuellen Netzwerks). Ein auf Resource Manager basierendes VNET wird für neue Bereitstellungen empfohlen und wird nur für Pools in der Konfiguration des virtuellen Computers unterstützt.
  * Um ein klassischen VNET zu erstellen, können Sie sich unter [Erstellen eines (klassischen) virtuellen Netzwerks mit mehreren Subnetzen](../virtual-network/create-virtual-network-classic.md) informieren. Ein klassisches VNET wird nur für Pools in der Cloud Services-Konfiguration unterstützt.

## <a name="vnet-requirements"></a>VNET-Anforderungen

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Erstellen eines Pools mit einem VNET im Portal

Wenn Sie Ihr VNET erstellt und einem Subnetz zugewiesen haben, können Sie einen Batch-Pool mit diesem VNET erstellen. Führen Sie die folgenden Schritte aus, um einen Pool im Azure-Portal zu erstellen: 

1. Navigieren Sie im Azure-Portal zu Ihrem Batch-Konto. Das Konto muss sich im gleichen Abonnement und der gleichen Region wie die Ressourcengruppe befinden, die das VNET enthält, das Sie verwenden möchten. 
2. Wählen Sie links im Fenster **Einstellungen** die Menüoption **Pools** aus.
3. Wählen Sie im Fenster **Pools** den Befehl **Hinzufügen** aus.
4. Wählen Sie im Fenster **Pool hinzufügen** die Option, die Sie verwenden möchten, in der Dropdownliste **Imagetyp** aus. 
5. Wählen Sie **Verleger/Angebot/SKU** für Ihr benutzerdefiniertes Image aus.
6. Geben Sie die übrigen erforderlichen Einstellungen an, einschließlich **Knotengröße**, **Ziel für dedizierte Knoten** und **Knoten mit niedriger Priorität**, sowie alle gewünschten optionalen Einstellungen.
7. Wählen Sie unter **Virtuelles Netzwerk** das virtuelle Netzwerk und Subnetz aus, die Sie verwenden möchten.
  
   ![„Pool hinzufügen“ mit virtuellem Netzwerk](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Benutzerdefinierte Routen für erzwungenes Tunneln

Ihr Unternehmen erfordert möglicherweise zu Überprüfungs- und Protokollierungszwecken das (erzwungene) Weiterleiten von Internetdatenverkehr vom Subnetz zurück zu ihrem lokalen Standort. Möglicherweise ist die Tunnelerzwingung für das Subnetz in Ihrem VNET aktiviert. 

Um sicherzustellen, dass die Computeknoten Ihres Azure Batch-Pools in einem VNET funktionieren, in dem die Tunnelerzwingung aktiviert ist, müssen Sie folgende [benutzerdefinierte Routen](../virtual-network/virtual-networks-udr-overview.md) für dieses Subnetz hinzufügen:

* Der Batch-Dienst muss für die zeitliche Planung von Tasks mit den Computeknoten des Pools kommunizieren. Um diese Kommunikation zu ermöglichen, fügen Sie eine benutzerdefinierte für jede IP-Adresse hinzu, die vom Batch-Dienst in der Region Ihres Batch-Kontos verwendet werden. Wenden Sie sich an den Azure-Support, um die Liste der IP-Adressen des Batch-Diensts zu erhalten.

* Stellen Sie sicher, dass ausgehender Datenverkehr an Azure Storage (also URLs im Format `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` und `<account>.blob.core.windows.net`) nicht über Ihr lokales Netzwerkgerät blockiert wird.

Wenn Sie eine benutzerdefinierte Route hinzufügen, definieren Sie die Route für jedes zugehörige Batch-IP-Adresspräfix und legen **Typ des nächsten Hops** auf **Internet** fest. Siehe folgendes Beispiel:

![Benutzerdefinierte Route](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Nächste Schritte

- Eine detaillierte Übersicht über Batch finden Sie unter [Entwickeln von parallelen Computelösungen in größerem Umfang mit Batch](batch-api-basics.md).
- Weitere Informationen zum Erstellen einer benutzerdefinierten Route finden Sie unter [Erstellen einer benutzerdefinierten Route – Azure-Portal](../virtual-network/tutorial-create-route-table-portal.md).
