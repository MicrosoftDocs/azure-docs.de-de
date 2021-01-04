---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 6147dff71ff8c93c8c46b5921b37db459ea73e4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026064"
---
> [!TIP]
> Nach der Anmeldung sehen Sie eine Liste der mit Ihrem Azure-Konto verknüpften Abonnements. Die Abonnementinformation mit `isDefault: true` ist das derzeit aktivierte Abonnement für Azure CLI-Befehle. Dieses Abonnement muss dasselbe sein, das Ihren Azure Machine Learning-Arbeitsbereich enthält. Sie finden die Abonnement-ID im [Azure-Portal](https://portal.azure.com), indem Sie zur Übersichtsseite Ihres Arbeitsbereichs navigieren. Sie können das SDK auch verwenden, um die Abonnement-ID aus dem Arbeitsbereichsobjekt abzurufen. Beispiel: `Workspace.from_config().subscription_id`.
> 
> Um ein anderes Abonnement auszuwählen, verwenden Sie den Befehl `az account set -s <subscription name or ID>`, und geben Sie den Namen oder die ID des Abonnements an, zu dem Sie wechseln möchten. Weitere Informationen zur Abonnementauswahl finden Sie unter [Verwenden mehrerer Azure-Abonnements](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).