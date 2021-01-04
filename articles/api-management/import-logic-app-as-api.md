---
title: Importieren einer Logik-App als eine API mit dem Azure-Portal | Microsoft-Dokumentation
description: Dieser Artikel veranschaulicht, wie Sie API Management (APIM) verwenden, um Logik-Apps als API zu importieren.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 0dd460bce01b86c72d0b8fd70351fdcc85c69bd4
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146959"
---
# <a name="import-a-logic-app-as-an-api"></a>Importieren einer Logik-App als API

Dieser Artikel zeigt, wie Sie eine Logik-App als eine API importieren und die importierte API testen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> -   Importieren einer Logik-App als API
> -   Testen der API im Azure-Portal
> -   Testen der API im Entwicklerportal

## <a name="prerequisites"></a>Voraussetzungen

-   Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
-   Stellen Sie sicher, dass Ihr Abonnement eine Logik-App enthält, die einen HTTP-Endpunkt verfügbar macht. Weitere Informationen finden Sie unter [Aufrufen, Auslösen oder Schachteln von Workflows mit HTTP-Endpunkten in Logik-Apps](../logic-apps/logic-apps-http-endpoint.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importieren und Veröffentlichen einer Back-End-API

1. Navigieren Sie im Azure-Portal zu Ihrem API Management-Dienst, und wählen Sie im Menü **APIs** aus.
2. Wählen Sie **Logik-App** in der Liste **Neue API hinzufügen** aus.

    ![Logik-App](./media/import-logic-app-as-api/logic-app-api.png)

3. Klicken Sie auf **Durchsuchen** , um die Liste der Logik-Apps mit HTTP-Trigger in Ihrem Abonnement anzuzeigen. (Beachten Sie, dass Logik-Apps ohne HTTP-Trigger nicht in der Liste aufgeführt sind.)
4. Wählen Sie die App aus. API Management sucht den Swagger, der der ausgewählten App zugeordnet ist, ruft ihn ab und importiert ihn.
5. Fügen Sie ein API-URL-Suffix hinzu. Das Suffix ist ein Name, der diese spezifische API in dieser API Management-Instanz identifiziert. Es muss in dieser API Management-Instanz eindeutig sein.
6. Veröffentlichen Sie die API, indem Sie sie einem Produkt zuordnen. In diesem Fall wird das Produkt „ _Unlimited_ “ verwendet. Wenn Sie möchten, dass die API veröffentlicht wird und dann Entwicklern zur Verfügung steht, fügen Sie sie einem Produkt hinzu. Sie können dies während der Erstellung der API vornehmen oder später festlegen.

    Bei Produkten handelt es sich um API-Zuordnungen. Sie können eine Reihe von APIs einfügen und sie Entwicklern über das Entwicklerportal zur Verfügung stellen. Entwickler müssen ein Produkt zunächst abonnieren, um Zugriff auf die API zu erhalten. Wenn sie ein Produkt abonnieren, erhalten sie einen Abonnementschlüssel, der für jede API in diesem Produkt gilt. Wenn Sie die API Management-Instanz erstellt haben, sind Sie bereits Administrator und haben dadurch standardmäßig alle Produkte abonniert.

    Standardmäßig enthält jede API Management-Instanz zwei Beispielprodukte:

    - **Starter**
    - **Unbegrenzt**

7. Geben Sie weitere API-Einstellungen ein. Sie können die Werte während der Erstellung festlegen oder später über die Registerkarte **Einstellungen** konfigurieren. Die Einstellungen werden im Tutorial [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md#import-and-publish-a-backend-api) erläutert.
8. Klicken Sie auf **Erstellen**.

## <a name="test-the-api-in-the-azure-portal"></a>Testen der API im Azure-Portal

Vorgänge können direkt aus dem Azure-Portal aufgerufen werden. Dies ist ein einfacher Weg, die Vorgänge einer API anzuzeigen und zu testen.

1. Wählen Sie die API aus, die Sie im vorherigen Schritt erstellt haben.
2. Wählen Sie die Registerkarte **Testen** aus.
3. Wählen Sie einen Vorgang aus.

    Die Seite zeigt Felder für Abfrageparameter und Felder für die Header. Einer der Header ist „Ocp-Apim-Subscription-Key“. Er steht für den Abonnementschlüssel des Produkts, das dieser API zugeordnet ist. Wenn Sie die API Management-Instanz erstellt haben, sind Sie bereits Administrator, sodass der Schlüssel automatisch eingetragen wird.

4. Klicken Sie auf **Senden**.

    Das Back-End antwortet mit **200 OK** und einigen Daten.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Jede Logik-App enthält den Vorgang **manual-invoke**. Wenn Sie Ihre API aus mehreren Logik-Apps zusammensetzen möchten, müssen Sie die Funktion umbenennen, damit keine Namenskonflikte auftreten.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)
