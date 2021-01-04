---
title: Hinzufügen der Zwischenspeicherung zum Verbessern der Leistung in Azure API Management | Microsoft Docs
description: Erfahren Sie, wie Sie Latenz, Bandbreitennutzung und Webdienstauslastung für API Management-Dienstaufrufe verbessern.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 732abed830afdb759ed52fd933673edd8e5cade6
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638732"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Hinzufügen der Zwischenspeicherung zum Verbessern der Leistung in Azure API Management

APIs und Vorgänge in API Management können mit dem Zwischenspeichern von Antworten konfiguriert werden. Das Zwischenspeichern von Antworten kann die Latenzzeit für API-Aufrufer und die Back-End-Auslastung für API-Anbieter bedeutend reduzieren.

> [!IMPORTANT]
> Der integrierte Cache ist flüchtig und wird von allen Einheiten in derselben Region desselben API Management-Diensts gemeinsam genutzt.

Ausführlichere Informationen zum Zwischenspeichern finden Sie unter [Cacherichtlinien für API Management](api-management-caching-policies.md) sowie unter [Benutzerdefiniertes Caching in Azure API Management](api-management-sample-cache-by-key.md).

![Zwischenspeicherungsrichtlinien](media/api-management-howto-cache/cache-policies.png)

Sie lernen Folgendes:

> [!div class="checklist"]
> * Hinzufügen des Zwischenspeicherns von Antworten für Ihre API
> * Überprüfen des Zwischenspeicherns in Aktion

## <a name="availability"></a>Verfügbarkeit

> [!NOTE]
> Im Tarif **Consumption** von Azure API Management ist kein interner Cache verfügbar. Sie können stattdessen [eine externe Azure Cache for Redis-Instanz verwenden](api-management-howto-cache-external.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

+ [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ [Importieren und Veröffentlichen einer API](import-and-publish.md)

## <a name="add-the-caching-policies"></a><a name="caching-policies"> </a>Hinzufügen der Zwischenspeicherungsrichtlinien

Mit den Zwischenspeicherungsrichtlinien in diesem Beispiel gibt die erste Anforderung an den Vorgang **GetSpeakers** eine Antwort vom Back-End-Dienst zurück. Diese Antwort wird zwischengespeichert und erhält einen Schlüssel, der auf den angegebenen Headern und Abfrageparametern basiert. Bei nachfolgenden Aufrufen der Operation mit denselben Parametern wird die zwischengespeicherte Antwort zurückgegeben, bis das Ablaufintervall abgelaufen ist.

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Navigieren Sie zu Ihrer APIM-Instanz.
3. Klicken Sie auf die Registerkarte **API**.
4. Klicken Sie in der API-Liste auf **Demo Conference API**.
5. Wählen Sie **GetSpeakers** aus.
6. Klicken Sie im oberen Seitenbereich auf die Registerkarte **Entwurf**.
7. Klicken Sie im Abschnitt **Eingehende Verarbeitung** auf das Symbol **</>** .

    ![Code-Editor](media/api-management-howto-cache/code-editor.png)

8. Fügen Sie im Element für den **Eingang** die folgende Richtlinie hinzu:

   ```
   <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
       <vary-by-header>Accept</vary-by-header>
       <vary-by-header>Accept-Charset</vary-by-header>
       <vary-by-header>Authorization</vary-by-header>
   </cache-lookup>
   ```

9. Fügen Sie im Element für den **Ausgang** die folgende Richtlinie hinzu:

   ```
   <cache-store duration="20" />
   ```

    **Dauer** gibt das Ablaufintervall der gespeicherten Antworten an. In diesem Beispiel beträgt das Intervall **20** Sekunden.

> [!TIP]
> Wenn Sie wie unter [Verwenden einer externen Azure Cache for Redis-Instanz in Azure API Management](api-management-howto-cache-external.md) beschrieben einen externen Cache verwenden, empfiehlt es sich ggf., das Attribut `caching-type` der Cachingrichtlinien anzugeben. Ausführlichere Informationen finden Sie unter [Cacherichtlinien für API Management](api-management-caching-policies.md).

## <a name="call-an-operation-and-test-the-caching"></a><a name="test-operation"> </a>Aufrufen einer Operation und Testen der Zwischenspeicherung
Um die Zwischenspeicherung in Aktion zu sehen, können Sie den Vorgang über das Entwicklerportal aufrufen.

1. Navigieren Sie im Azure-Portal zu Ihrer APIM-Instanz.
2. Klicken Sie auf die Registerkarte **APIs**.
3. Wählen Sie die API aus, der Sie Zwischenspeicherungsrichtlinien hinzugefügt haben.
4. Wählen Sie den Vorgang **GetSpeakers** aus.
5. Klicken Sie im Menü rechts oben auf die Registerkarte **Test**.
6. Klicken Sie auf **Senden**.

## <a name="next-steps"></a><a name="next-steps"> </a>Nächste Schritte
* Weitere Informationen zu Richtlinien für die Zwischenspeicherung finden Sie unter [Richtlinien für die Zwischenspeicherung][Caching policies] in der [Richtlinienreferenz für API Management][API Management policy reference].
* Informationen zum Zwischenspeichern von Elementen nach Schlüssel mithilfe von Richtlinienausdrücken finden Sie unter [Benutzerdefiniertes Zwischenspeichern in Azure API Management](api-management-sample-cache-by-key.md).
* Weitere Informationen zur Verwendung einer externen Azure Cache for Redis-Instanz finden Sie unter [Verwenden einer externen Azure Cache for Redis-Instanz in Azure API Management](api-management-howto-cache-external.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
