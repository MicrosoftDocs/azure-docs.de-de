---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: 6e9f349f2fd90fdb12d8d26ca904e504b75d9db2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445206"
---
* Wenn Sie einen neuen Arbeitsbereich erstellen, können Sie die vom Arbeitsbereich benötigten Dienste automatisch erstellen oder vorhandene Dienste verwenden. Wenn Sie __vorhandene Dienste aus einem anderen Azure-Abonnement__ als dem des Arbeitsbereichs verwenden möchten, müssen Sie den Azure Machine Learning-Namespace in dem Abonnement mit diesen Dienst registrieren. Wenn Sie z. B. einen Arbeitsbereich in Abonnement A erstellen, der ein Speicherkonto aus Abonnement B verwendet, muss der Azure Machine Learning-Namespace in Abonnement B registriert werden, damit Sie das Speicherkonto mit dem Arbeitsbereich verwenden können.

    Der Ressourcenanbieter für Azure Machine Learning ist __Microsoft.MachineLearningService__. Informationen zum Überprüfen und Durchführen der Registrierung finden Sie im Artikel [Azure-Ressourcenanbieter und -typen](../articles/azure-resource-manager/management/resource-providers-and-types.md).

    > [!IMPORTANT]
    > Dies betrifft für Ressourcen, die beim Erstellen des Arbeitsbereichs bereitgestellt werden, also Azure Storage-Konten, Azure Container Registry, Azure Key Vault und Application Insights.