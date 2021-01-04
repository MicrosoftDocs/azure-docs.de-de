---
title: Importieren einer SOAP-API mithilfe des Azure-Portals | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine XML-Standarddarstellung einer SOAP-API importieren und die API dann im Azure- und im Entwicklerportal testen.
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
ms.openlocfilehash: 81ded79ee72fb7c2d89898595602cb3e6d7ae5e2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011063"
---
# <a name="import-soap-api"></a>Importieren einer SOAP-API

Dieser Artikel zeigt, wie Sie eine standardmäßige XML-Darstellung einer SOAP-API importieren. Der Artikel zeigt außerdem, wie Sie die API Management-API testen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Importieren einer SOAP-API
> * Testen der API im Azure-Portal
> * Testen der API im Entwicklerportal

## <a name="prerequisites"></a>Voraussetzungen

Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importieren und Veröffentlichen einer Back-End-API

1. Navigieren Sie im Azure-Portal zu Ihrem API Management-Dienst, und wählen Sie im Menü **APIs** aus.
2. Wählen Sie **WSDL** in der Liste **Neue API hinzufügen** aus.

    ![SOAP-API](./media/import-soap-api/wsdl-api.png)
3. Geben Sie in **WSDL-Spezifikation** die URL Ihrer SOAP-API ein.
4. Das Optionsfeld **SOAP-Passthrough** ist standardmäßig aktiviert. Mit dieser Auswahl wird die API als SOAP verfügbar gemacht. Der Consumer muss die SOAP-Regeln verwenden. Wenn Sie die API „korrigieren“ möchten, führen Sie die Schritte in [Importieren einer SOAP-API und Konvertieren dieser in REST](restify-soap-api.md) aus.

    ![Screenshot, der das Dialogfeld „Aus W S D L erstellen“ zeigt, in dem Sie eine W S D L-Spezifikation eingeben können.](./media/import-soap-api/pass-through.png)
5. Drücken Sie auf die Registerkarte.

    Die folgenden Felder werden mit den Informationen aus der SOAP-API aufgefüllt: Anzeigename, Name, Beschreibung.
6. Fügen Sie ein API-URL-Suffix hinzu. Das Suffix ist ein Name, der diese spezifische API in dieser API Management-Instanz identifiziert. Es muss in dieser API Management-Instanz eindeutig sein.
7. Veröffentlichen Sie die API, indem Sie sie einem Produkt zuordnen. In diesem Fall wird das Produkt „*Unlimited*“ verwendet.  Wenn Sie möchten, dass die API veröffentlicht wird und dann Entwicklern zur Verfügung steht, fügen Sie sie einem Produkt hinzu. Sie können dies während der Erstellung der API vornehmen oder später festlegen.

    Bei Produkten handelt es sich um API-Zuordnungen. Sie können eine Reihe von APIs einfügen und sie Entwicklern über das Entwicklerportal zur Verfügung stellen. Entwickler müssen ein Produkt zunächst abonnieren, um Zugriff auf die API zu erhalten. Wenn sie ein Produkt abonnieren, erhalten sie einen Abonnementschlüssel, der für jede API in diesem Produkt gilt. Wenn Sie die API Management-Instanz erstellt haben, sind Sie bereits Administrator und haben dadurch standardmäßig alle Produkte abonniert.

    Standardmäßig enthält jede API Management-Instanz zwei Beispielprodukte:

    * **Starter**
    * **Unbegrenzt**   
8. Geben Sie weitere API-Einstellungen ein. Sie können die Werte während der Erstellung festlegen oder später über die Registerkarte **Einstellungen** konfigurieren. Die Einstellungen werden im Tutorial [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md#import-and-publish-a-backend-api) erläutert.
9. Klicken Sie auf **Erstellen**.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Testen der neuen API im Verwaltungsportal

Vorgänge können direkt aus dem Verwaltungsportal aufgerufen werden. Dies ist ein einfacher Weg, die Vorgänge einer API anzuzeigen und zu testen.  

1. Wählen Sie die API aus, die Sie im vorherigen Schritt erstellt haben.
2. Wählen Sie die Registerkarte **Testen** aus.
3. Wählen Sie einen Vorgang aus.

    Die Seite zeigt Felder für Abfrageparameter und Felder für die Header. Einer der Header ist „Ocp-Apim-Subscription-Key“. Er steht für den Abonnementschlüssel des Produkts, das dieser API zugeordnet ist. Wenn Sie die API Management-Instanz erstellt haben, sind Sie bereits Administrator, sodass der Schlüssel automatisch eingetragen wird. 
1. Klicken Sie auf **Senden**.

    Das Back-End antwortet mit **200 OK** und einigen Daten.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)