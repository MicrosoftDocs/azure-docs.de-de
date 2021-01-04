---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94574564"
---
> [!IMPORTANT]
> Die Cosmos DB-Instanz wird zusammen mit allen benötigten Ressourcen in einer von Microsoft verwalteten Ressourcengruppe in __Ihrem Abonnement__ erstellt. Dies bedeutet, dass Ihnen diese Cosmos DB-Instanz in Rechnung gestellt wird. Die verwaltete Ressourcengruppe wird im Format `<AML Workspace Resource Group Name><GUID>` benannt. Wenn im Azure Machine Learning-Arbeitsbereich ein privater Endpunkt verwendet wird, wird auch ein virtuelles Netzwerk für die Cosmos DB-Instanz erstellt. Dieses VNet wird zum Sichern der Kommunikation zwischen Cosmos DB und Azure Machine Learning verwendet.
> 
> * __Löschen Sie weder die Ressourcengruppe__, die diese Cosmos DB-Instanz enthält, noch Ressourcen, die automatisch in dieser Gruppe erstellt wurden. Wenn Sie die Ressourcengruppe, die Cosmos DB-Instanz usw. löschen müssen, müssen Sie den Azure Machine Learning-Arbeitsbereich löschen, der diese verwendet. Die Ressourcengruppe, die Cosmos DB-Instanz und andere automatisch erstellte Ressourcen werden gelöscht, wenn der zugehörige Arbeitsbereich gelöscht wird.
> * Die standardmäßigen [__Anforderungseinheiten__](../articles/cosmos-db/request-units.md) für dieses Cosmos-DB-Konto sind auf __8000__ festgelegt.
> * Sie __können kein eigenes VNet für die Verwendung mit der erstellten Cosmos DB-Instanz bereitstellen__. Außerdem __können Sie das virtuelle Netzwerk nicht ändern__. Beispielsweise können Sie den verwendeten IP-Adressbereich nicht ändern.