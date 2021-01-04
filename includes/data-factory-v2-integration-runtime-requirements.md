---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023178"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Wenn sich Ihr Datenspeicher in einem lokalen Netzwerk, in einem virtuellen Azure-Netzwerk oder in einer virtuellen privaten Amazon-Cloud befindet, müssen Sie eine [selbstgehostete Integration Runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) konfigurieren, um eine Verbindung herzustellen.

Handelt es sich bei Ihrem Datenspeicher um einen verwalteten Clouddatendienst, können Sie alternativ Azure Integration Runtime verwenden. Ist der Zugriff auf IP-Adressen beschränkt, die in den Firewallregeln genehmigt sind, können Sie [Azure Integration Runtime-IPs](../articles/data-factory/azure-integration-runtime-ip-addresses.md) zur Positivliste hinzufügen. 

Weitere Informationen zu den von Data Factory unterstützten Netzwerksicherheitsmechanismen und -optionen finden Sie unter [Datenzugriffsstrategien](../articles/data-factory/data-access-strategies.md).
