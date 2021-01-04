---
title: Verwaltetes virtuelles Netzwerk und verwaltete private Endpunkte
description: Erfahren Sie mehr über das verwaltete virtuelle Netzwerk und verwaltete private Endpunkte in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: 81d82bccd6b6bd97b84df5269dd59ffac4903370
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980359"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Verwaltetes virtuelles Netzwerk in Azure Data Factory (Vorschauversion)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden das verwaltete virtuelle Netzwerk und verwaltete private Endpunkte in Azure Data Factory erläutert.


## <a name="managed-virtual-network"></a>Verwaltetes virtuelles Netzwerk

Wenn Sie eine Azure Integration Runtime in einem verwalteten virtuellen Azure Data Factory-Netzwerk (VNET) erstellen, wird die Integration Runtime mit dem verwalteten virtuellen Netzwerk bereitgestellt und nutzt private Endpunkte, um sichere Verbindungen mit unterstützten Datenspeichern herzustellen. 

Durch das Erstellen einer Azure IR in einem verwaltetem virtuellen Netzwerk wird sichergestellt, dass der Datenintegrationsprozess isoliert und sicher ist. 

Vorteile der Verwendung eines verwalteten virtuellen Netzwerks:

- Mit einem verwalteten virtuellen Netzwerk können Sie die Verwaltung des virtuellen Netzwerks an Azure Data Factory auslagern. Es ist nicht erforderlich, ein Subnetz für die Azure Integration Runtime zu erstellen, das letztendlich viele private IP-Adressen Ihres virtuellen Netzwerks verwenden könnte, sodass eine vorherige Planung der Netzwerkinfrastruktur erforderlich wäre. 
- Für sichere Datenintegrationen sind keine tief greifenden Azure-Netzwerkkenntnisse erforderlich. Die ersten Schritte mit sicherem ETL sind für Datentechniker im Vergleich viel einfacher. 
- Das verwaltete virtuelle Netzwerk schützt zusammen mit verwalteten privaten Endpunkten vor Datenexfiltration. 

> [!IMPORTANT]
>Derzeit wird das verwaltete VNET nur in derselben Region wie Azure Data Factory unterstützt.
 

![Architektur des verwalteten virtuellen ADF-Netzwerks](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Verwaltete private Endpunkte

Verwaltete private Endpunkte sind private Endpunkte, die im verwalteten virtuellen Netzwerk von Azure Data Factory erstellt werden und einen privaten Link zu Azure-Ressourcen herstellen. Azure Data Factory verwaltet diese privaten Endpunkte für Sie. 

![Neuer verwalteter privater Endpunkt](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory unterstützt private Links. Der private Link ermöglicht Ihnen den Zugriff auf Azure-Dienste (PaaS, z. B. Azure Storage, Azure Cosmos DB, Azure Synapse Analytics (ehemals SQL Data Warehouse)).

Bei der Verwendung eines privaten Links wird der Datenverkehr zwischen Ihrem Datenspeicher und dem verwalteten virtuellen Netzwerk vollständig über das Microsoft-Backbone-Netzwerk übertragen. Private Link schützt vor einer möglichen Datenexfiltration. Ein privater Link zu einer Ressource wird durch die Erstellung eines privaten Endpunkts eingerichtet.

Der private Endpunkt nutzt eine private IP-Adresse im verwalteten virtuellen Netzwerk, um den Dienst effektiv darin einzubinden. Private Endpunkte werden einer bestimmten Ressource in Azure und nicht dem gesamten Dienst zugeordnet. Kunden können die Konnektivität auf eine bestimmte Ressource beschränken, die von Ihrer Organisation genehmigt wurde. Weitere Informationen finden Sie in der [Dokumentation zu Private Link](../private-link/index.yml).

> [!NOTE]
> Es wird empfohlen, verwaltete private Endpunkte zu erstellen, um eine Verbindung mit all Ihren Azure-Datenquellen herzustellen. 
 
> [!WARNING]
> Wenn für einen PaaS-Datenspeicher (Blob, ADLS Gen2, Azure Synapse Analytics) bereits ein privater Endpunkt erstellt wurde, kann ADF nur mithilfe des verwalteten privaten Endpunkts darauf zugreifen, selbst wenn er den Zugriff aus allen Netzwerken zulässt. Stellen Sie in solchen Szenarien sicher, einen privaten Endpunkt zu erstellen. 

Wenn Sie einen verwalteten privaten Endpunkt in Azure Data Factory erstellen, wird eine Verbindung mit einem privaten Endpunkt mit dem Status „Ausstehend“ erstellt. Ein Genehmigungsworkflow wird ausgelöst. Der Besitzer der Ressource für private Links ist für die Genehmigung oder Ablehnung der Verbindung verantwortlich.

![Verwalten eines privaten Endpunkts](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Wenn der Besitzer die Verbindung genehmigt, wird der private Link eingerichtet. Andernfalls wird der private Link nicht eingerichtet. In beiden Fällen wird der verwaltete private Endpunkt mit dem Status der Verbindung aktualisiert.

![Genehmigter verwalteter privater Endpunkt](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Nur ein verwalteter privater Endpunkt im genehmigten Zustand kann Datenverkehr an eine angegebene Ressource für private Links senden.

## <a name="interactive-authoring"></a>Interaktive Erstellung
Interaktive Erstellungsfunktionen werden beispielsweise für Testverbindungen, das Durchsuchen von Ordner- und Tabellenlisten, das Abrufen von Schemas und die Vorschau von Daten verwendet. Sie können die interaktive Erstellung aktivieren, wenn Sie eine Azure Integration Runtime erstellen, die sich in einem per ADF verwalteten virtuellen Netzwerk befindet. Der Back-End-Dienst weist vorab Computeressourcen für die interaktive Erstellung zu. Andernfalls werden Computeressourcen jedes Mal zugewiesen, wenn eine interaktive ausgeführt wird. Dies nimmt mehr Zeit in Anspruch. Die Gültigkeitsdauer für die interaktive Erstellung beträgt 60 Minuten. Das bedeutet, dass das Feature 60 Minuten nach dem letzten interaktiven Erstellungsvorgang deaktiviert wird.

![Interaktive Erstellung](./media/managed-vnet/interactive-authoring.png)

## <a name="limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme
### <a name="supported-data-sources"></a>Unterstützte Datenquellen
Die nachstehenden Datenquellen werden für eine Verbindung über einen privaten Link aus einem verwalteten virtuellen ADF-Netzwerk unterstützt.
- Azure Blob Storage
- Azure-Tabellenspeicher
- Azure Files
- Azure Data Lake Gen2
- Azure SQL-Datenbank (ohne Azure SQL Managed Instance)
- Azure Synapse Analytics (ehemals SQL Data Warehouse)
- Azure Cosmos DB SQL
- Azure-Schlüsseltresor
- Azure Private Link-Dienst
- Azure Search
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Verwaltetes virtuelles Netzwerk in Azure Data Factory ist in den folgenden Azure-Regionen verfügbar:
- East US
- USA (Ost) 2
- USA, Westen-Mitte
- USA (Westen)
- USA, Westen 2
- USA Süd Mitte
- USA (Mitte)
- Nordeuropa
- Europa, Westen
- UK, Süden
- Asien, Südosten
- Australien (Osten)
- Australien, Südosten

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Ausgehende Kommunikation über einen öffentlichen Endpunkt von einem verwaltetem virtuellen ADF-Netzwerk
- Nur Port 443 wird für die ausgehende Kommunikation geöffnet.
- Azure Storage und Azure Data Lake Gen2 werden für eine Verbindung über einen öffentlichen Endpunkt von einem verwalteten virtuellen ADF-Netzwerk nicht unterstützt.

### <a name="linked-service-creation-of-azure-key-vault"></a>Erstellung eines verknüpften Diensts von Azure Key Vault 
- Wenn Sie einen verknüpften Dienst für Azure Key Vault erstellen, gibt es keinen Verweis auf Azure Integration Runtime. Daher können Sie während der Erstellung eines verknüpften Diensts von Azure Key Vault keinen privaten Endpunkt erstellen. Wenn Sie jedoch einen verknüpften Dienst für Datenspeicher erstellen, der auf den verknüpften Dienst von Azure Key Vault verweist, und dieser verknüpfte Dienst auf Azure Integration Runtime mit aktiviertem verwalteten virtuellen Netzwerk verweist, können Sie während der Erstellung einen privaten Endpunkt für den verknüpften Dienst von Azure Key Vault erstellen. 
- Bei dem Vorgang **Verbindung testen** für den verknüpften Dienst von Azure Key Vault wird nur das URL-Format überprüft. Es werden dabei jedoch keine Netzwerkvorgänge ausgeführt.

## <a name="next-steps"></a>Nächste Schritte

- Tutorial: [Erstellen einer Kopierpipeline mithilfe eines verwalteten virtuellen Netzwerks und privater Endpunkte](tutorial-copy-data-portal-private.md) 
- Tutorial: [Erstellen einer Pipeline für den Zuordnungsdatenfluss mithilfe eines verwalteten virtuellen Netzwerks und privater Endpunkte](tutorial-data-flow-private.md)