---
title: Herstellen einer Verbindung mit einer Synapse Studio-Instanz mit privaten Verbindungen
description: In diesem Artikel wird beschrieben, wie Sie über private Verbindungen eine Verbindung mit Ihrer Azure Synapse Studio-Instanz herstellen.
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 2613a4fd931ad49a4f40a4221ea20e8c25f185fe
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501327"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Herstellen einer Verbindung mit Azure Synapse Studio mit Azure Private Link-Hubs 

In diesem Artikel wird beschrieben, wie Private Link-Hubs von Azure Synapse Analytics verwendet werden, um eine sichere Verbindung mit Synapse Studio herzustellen. 

## <a name="azure-private-link-hubs"></a>Azure Private Link-Hubs 
Sie können private Verbindungen (Private Links) verwenden, um aus Ihrem virtuellen Azure-Netzwerk sichere Verbindungen mit Azure Synapse Studio herzustellen. Bei Private Link-Hubs (also Hubs für private Verbindungen) von Azure Synapse Analytics handelt es sich um Azure-Ressourcen, die als Connectors zwischen Ihrem geschützten Netzwerk und der Synapse Studio-Weboberfläche dienen. 

Sie müssen zwei Schritte ausführen, um über private Verbindungen eine Verbindung mit Synapse Studio herzustellen. Zunächst müssen Sie eine Private Link-Hub-Ressource erstellen. Anschließend müssen Sie einen privaten Endpunkt erstellen, der über eine Verbindung zwischen Ihrem virtuellen Azure-Netzwerk und diesem Private Link-Hub verfügt. Sie können private Endpunkte dann für die sichere Kommunikation mit Synapse Studio verwenden. Sie müssen die privaten Endpunkte mit Ihrer DNS-Lösung integrieren. Dies kann entweder Ihre lokale Lösung oder „Privates DNS“ von Azure sein. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Azure Private Link-Hubs und Azure Synapse Studio
Sie können eine einzelne Private Link-Hub-Ressource von Azure Synapse verwenden, um für Ihre gesamten Azure Synapse Analytics-Arbeitsbereiche mit Azure Synapse Studio private Verbindungen herzustellen. Die Arbeitsbereiche müssen sich nicht in derselben Region wie der Private Link-Hub von Azure Synapse befinden. Die Private Link-Hub-Ressource von Azure Synapse kann auch für Verbindungen mit Synapse-Arbeitsbereichen in unterschiedlichen Abonnements oder Azure AD-Mandanten verwendet werden.

Sie können Ihren Private Link-Hub erstellen, indem Sie im Azure-Portal nach *Synapse Private Link-Hubs* suchen und unter „Dienste“ die Option **Azure Synapse Analytics (Private Link-Hubs)** auswählen. Führen Sie die Schritte zum [Herstellen einer Verbindung mit Arbeitsbereichsressourcen über ein eingeschränktes Netzwerk](./how-to-connect-to-workspace-from-restricted-network.md) aus, um weitere Informationen zu erhalten.

>[!NOTE]
>Mit Private Link-Hubs kann der statische Inhalt von Synapse Studio über private Verbindungen sicher geladen werden. Sie müssen **separate private Endpunkte** für die Ressourcen erstellen, mit denen Sie im Arbeitsbereich Verbindungen herstellen möchten, z. B. bereitgestellte/dedizierte SQL-Pools oder Spark-Pools. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Azure Private Link-Hubs und virtuelles Azure-Netzwerk
Sie müssen Ihr virtuelles Azure-Netzwerk mit der Private Link-Hub-Ressource von Synapse verbinden, um die End-to-End-Verbindung mit Synapse Studio zu schützen. Hierfür müssen Sie einen privaten Endpunkt erstellen, der über eine Verbindung zwischen Ihrem virtuellen Netzwerk und dem von Ihnen erstellten Private Link-Hub verfügt. Sie können das Azure-Portal für Ihren Private Link-Hub verwenden und zum Abschnitt „Privater Endpunkt“ navigieren. Wählen Sie die Option „+ Privater Endpunkt“ aus, um einen neuen privaten Endpunkt zu erstellen, über den eine Verbindung mit Ihrem Private Link-Hub hergestellt wird.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="Erstellen einer Verbindung von einem privaten Endpunkt mit einem Private Link-Hub":::

Wählen Sie auf der Registerkarte „Ressource“ den Ressourcentyp „Microsoft.Synapse/privateLinkHubs“ aus. :::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="Erstellen einer Verbindung von einem privaten Endpunkt mit einem Private Link-Hub":::

Wählen Sie auf der Registerkarte „Konfiguration“ unter „Private DNS-Zonen“ die Option „privatelink.azuresynapse.net“ aus, wenn Sie die Integration mit Ihrem virtuellen Netzwerk und der privaten DNS-Zone durchführen.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="Erstellen einer Verbindung von einem privaten Endpunkt mit einem Private Link-Hub":::

## <a name="next-steps"></a>Nächste Schritte

[Herstellen einer Verbindung mit Arbeitsbereichsressourcen über ein eingeschränktes Netzwerk](./how-to-connect-to-workspace-from-restricted-network.md)

Weitere Informationen zu [verwalteten virtuellen Arbeitsbereichsnetzwerken](./synapse-workspace-managed-vnet.md)

Weitere Informationen zu [Verwalteten privaten Endpunkten](./synapse-workspace-managed-private-endpoints.md).

[Erstellen Sie verwaltete private Endpunkte für Ihre Datenquellen.](./how-to-create-managed-private-endpoints.md)

[Herstellen einer Verbindung mit Ihrem Azure Synapse-Arbeitsbereich mit privaten Links](./how-to-connect-to-workspace-with-private-links.md)

