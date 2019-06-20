---
title: include file
description: include file
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 97387e24d5b55c1438a69da1a1fd0a9bc1720e47
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178250"
---
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

1. Wählen Sie im Dialogfeld **Veröffentlichen** die Option **Microsoft Azure App Service** und dann **Neu erstellen** und **Veröffentlichen** aus.

   ![Auswählen eines Veröffentlichungsziels](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Verwenden Sie im Dialogfeld **App Service erstellen** die Hostingeinstellungen wie in der Tabelle unter der Abbildung angegeben:

    ![Dialogfeld „App Service erstellen“](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Einstellung      | Empfohlener Wert  | BESCHREIBUNG                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **App-Name** | Global eindeutiger Name | Name, der Ihre neue Funktions-App eindeutig identifiziert. |
    | **Abonnement** | Auswählen Ihres Abonnements | Das zu verwendende Azure-Abonnement. |
    | **[Ressourcengruppe](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Name der Ressourcengruppe, in der die Funktions-App erstellt wird. Wählen Sie **Neu** aus, um eine neue Ressourcengruppe zu erstellen.|
    | **[Hostingplan](../articles/app-service/overview-hosting-plans.md)** | App Service-Plan | Ein [App Service-Plan](../articles/app-service/overview-hosting-plans.md) gibt den Standort, die Größe und die Funktionen der Webserverfarm an, die Ihre App hostet. Beim Hosten mehrerer Apps können Sie Geld sparen, indem Sie die Web-Apps für die gemeinsame Verwendung eines einzelnen App Service-Plans konfigurieren. App Service-Pläne definieren Region, Instanzgröße, Skalierung und SKU (Free, Shared, Basic, Standard oder Premium). Wählen Sie **New** aus, um einen neuen App Service-Plan zu erstellen. |

1. Klicken Sie auf **Erstellen**, um einen WebJob und zugehörige Ressourcen in Azure mit diesen Einstellungen zu erstellen und Ihren Projektcode bereitzustellen.