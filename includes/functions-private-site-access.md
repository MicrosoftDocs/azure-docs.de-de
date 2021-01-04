---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020987"
---
Ein [privater Azure-Endpunkt](../articles/private-link/private-endpoint-overview.md) ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem Dienst verbindet, der von Azure Private Link unterstützt wird.  Ein privater Endpunkt verwendet eine private IP-Adresse aus Ihrem virtuellen Netzwerk und macht den Dienst so in Ihrem virtuellen Netzwerk verfügbar.

Sie können einen privaten Endpunkt für ihre Funktionen verwenden, die in den Plänen [Premium](../articles/azure-functions/functions-premium-plan.md) und [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) gehostet werden.

Wenn Sie eine eingehende private Endpunktverbindung für Funktionen erstellen, benötigen Sie auch einen DNS-Eintrag, um die private Adresse aufzulösen.  Standardmäßig wird ein privater DNS-Eintrag für Sie erstellt, wenn Sie einen privaten Endpunkt mithilfe des Azure-Portals erstellen.

Weitere Informationen finden Sie unter [Verwenden privater Endpunkte für Web-Apps](../articles/app-service/networking/private-endpoint.md).