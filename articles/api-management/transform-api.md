---
title: 'Tutorial: Transformieren und Schützen Ihrer API in Azure API Management | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie Ihre API in API Management mit Richtlinien für Transformation und Drosselung (Einschränkung der Aufrufhäufigkeit) schützen.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 979bdaa1e0dac4f45a321abda2a208f46983f9cd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010230"
---
# <a name="tutorial-transform-and-protect-your-api"></a>Tutorial: Transformieren und Schützen Ihrer API

Das Tutorial zeigt, wie Sie Ihre API so transformieren, dass keine Informationen zum privaten Back-End offengelegt werden. Beispielsweise möchten Sie vielleicht die Informationen zum Technologiestapel ausblenden, der auf dem Back-End ausgeführt wird. Sie können auch die ursprünglich im Textkörper der HTTP-Antwort der API angezeigten URLs ausblenden und stattdessen eine Umleitung an das APIM-Gateway durchführen.

In diesem Tutorial wird außerdem veranschaulicht, wie einfach Sie Ihre Back-End-API schützen können, indem Sie ein Aufruflimit mithilfe von Azure API Management konfigurieren. Beispielsweise können Sie die Häufigkeit von API-Aufrufen begrenzen, damit die API von Entwicklern nicht übermäßig genutzt wird. Weitere Informationen finden Sie unter [API Management-Richtlinien](api-management-policies.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> -   Transformieren einer API zum Entfernen von Antwortheadern
> -   Ersetzen der ursprünglichen URLs im Textkörper der API-Antwort durch APIM-Gateway-URLs
> -   Schützen einer API durch das Hinzufügen einer Richtlinie für ein Aufruflimit (Drosselung)
> -   Testen der Transformationen

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="Richtlinien im Portal":::

## <a name="prerequisites"></a>Voraussetzungen

-   Machen Sie sich mit der [Azure API Management-Terminologie](api-management-terminology.md) vertraut.
-   Machen Sie sich mit dem [Konzept von Richtlinien in Azure API Management](api-management-howto-policies.md) vertraut.
-   Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
-   Absolvieren Sie außerdem das folgende Tutorial: [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Transformieren einer API zum Entfernen von Antwortheadern

In diesem Abschnitt wird gezeigt, wie Sie die HTTP-Header ausblenden, die für Ihre Benutzer nicht angezeigt werden sollen. Dieses Beispiel zeigt, wie Sie die folgenden Header in der HTTP-Antwort löschen:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Testen der ursprünglichen Antwort

So zeigen Sie die ursprüngliche Antwort an

1. Wählen Sie in der API Management-Dienstinstanz **APIs** aus.
1. Wählen Sie in der API-Liste **Demo Conference API** aus.
1. Wählen Sie oben auf der Seite die Registerkarte **Testen** aus.
1. Wählen Sie den Vorgang **GetSpeakers** und dann **Senden** aus.

Die ursprüngliche Antwort sollte etwa wie folgt aussehen:

:::image type="content" source="media/transform-api/original-response.png" alt-text="Ursprüngliche API-Antwort":::

Wie Sie sehen, enthält die Antwort die Header **X-AspNet-Version** und **X-Powered-By**.

### <a name="set-the-transformation-policy"></a>Festlegen der Transformationsrichtlinie

1. Wählen Sie **Demo Conference API** > **Entwurf** > **Alle Vorgänge** aus.
4. Wählen Sie im Abschnitt **Ausgehende Verarbeitung** das Code-Editor-Symbol ( **</>** ) aus.

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="Zu ausgehender Richtlinie navigieren" border="false":::

1. Positionieren Sie den Cursor im Element **&lt;outbound&gt;** , und wählen Sie in der oberen rechten Ecke die Schaltfläche **Codeausschnitte anzeigen** aus.
1. Wählen Sie im rechten Fenster unter **Transformationsrichtlinien** zweimal die Option **HTTP-Header festlegen** aus (um zwei Richtlinienausschnitte einzufügen).

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="Richtlinie für HTTP-Header festlegen":::

1. Ändern Sie Ihren **\<outbound>** -Code, sodass er folgendermaßen aussieht:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="HTTP-Header setzen":::

1. Wählen Sie **Speichern** aus.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Ersetzen der ursprünglichen URLs im Textkörper der API-Antwort durch APIM-Gateway-URLs

In diesem Abschnitt wird gezeigt, wie Sie die ursprünglich im Textkörper der HTTP-Antwort der API angezeigten URLs ausblenden und stattdessen eine Umleitung an das APIM-Gateway durchführen.

### <a name="test-the-original-response"></a>Testen der ursprünglichen Antwort

So zeigen Sie die ursprüngliche Antwort an

1. Wählen Sie **Demo Conference API** > **Testen** aus.
1. Wählen Sie den Vorgang **GetSpeakers** und dann **Senden** aus.

    Wie Sie sehen, enthält die Antwort die ursprünglichen Back-End-URLs:

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="Ursprüngliche URLs in der Antwort":::


### <a name="set-the-transformation-policy"></a>Festlegen der Transformationsrichtlinie

1.  Wählen Sie **Demo Conference API** > **Alle Vorgänge** > **Entwurf** aus.
1.  Wählen Sie im Abschnitt **Ausgehende Verarbeitung** das Code-Editor-Symbol ( **</>** ) aus.
1.  Positionieren Sie den Cursor im Element **&lt;outbound&gt;** , und wählen Sie in der oberen rechten Ecke die Schaltfläche **Codeausschnitte anzeigen** aus.
1.  Wählen Sie im rechten Fenster unter **Transformationsrichtlinien** die Option **URLs in Inhalt maskieren** aus. 
1.  Wählen Sie **Speichern** aus.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Schützen einer API durch das Hinzufügen einer Richtlinie für ein Aufruflimit (Drosselung)

In diesem Abschnitt wird gezeigt, wie Sie Ihre Back-End-API schützen, indem Sie Aufruflimits konfigurieren. Beispielsweise können Sie die Häufigkeit von API-Aufrufen begrenzen, damit die API von Entwicklern nicht übermäßig genutzt wird. In diesem Beispiel wird das Limit für jede Abonnement-ID auf 3 Aufrufe pro 15 Sekunden festgelegt. Nach 15 Sekunden kann ein Entwickler die API erneut aufrufen.

1.  Wählen Sie **Demo Conference API** > **Alle Vorgänge** > **Entwurf** aus.
1.  Wählen Sie im Abschnitt **Eingehende Verarbeitung** das Code-Editor-Symbol ( **</>** ) aus.
1.  Positionieren Sie den Cursor im Element **&lt;inbound&gt;** , und wählen Sie in der oberen rechten Ecke die Schaltfläche **Codeausschnitte anzeigen** aus.

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="Festlegen der Richtlinie für eingehenden Datenverkehr" border="false":::

1.  Wählen Sie im rechten Fenster unter **Richtlinien für die Zugriffsbeschränkung** die Option **+ Aufrufrate pro Schlüssel einschränken**.
1.  Ändern Sie Ihren **rate-limit-by-key**-Code (im **\<inbound\>** -Element) folgendermaßen ab:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Testen der Transformationen

Zu diesem Zeitpunkt sieht Ihre Richtlinie in etwa wie folgt aus, wenn Sie den Code im Code-Editor betrachten:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

Im verbleibenden Teil dieses Abschnitts werden die Richtlinientransformationen getestet, die Sie in diesem Artikel festgelegt haben.

### <a name="test-the-stripped-response-headers"></a>Testen der entfernten Antwortheader

1. Wählen Sie **Demo Conference API** > **Testen** aus.
1. Wählen Sie den Vorgang **GetSpeakers** und dann **Senden** aus.

    Wie Sie sehen, wurden die Header entfernt:

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="Entfernte Antwortheader":::

### <a name="test-the-replaced-url"></a>Testen der ersetzten URL

1. Wählen Sie **Demo Conference API** > **Testen** aus.
1. Wählen Sie den Vorgang **GetSpeakers** und dann **Senden** aus.

    Wie Sie sehen, wurde die URL ersetzt.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="Ersetzte URL":::

### <a name="test-the-rate-limit-throttling"></a>Testen des Aufruflimits (Drosselung)

1. Wählen Sie **Demo Conference API** > **Testen** aus.
1. Wählen Sie den Vorgang **GetSpeakers** aus. Wählen Sie dreimal hintereinander **Senden** aus.

    Nachdem Sie die Anforderung dreimal gesendet haben, erhalten Sie die Antwort **429 Zu viele Anforderungen**.

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="Zu viele Anforderungen":::

1. Warten Sie etwa 15 Sekunden, und wählen Sie erneut **Senden** aus. Jetzt sollten Sie die Antwort **200 OK** erhalten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
>
> -   Transformieren einer API zum Entfernen von Antwortheadern
> -   Ersetzen der ursprünglichen URLs im Textkörper der API-Antwort durch APIM-Gateway-URLs
> -   Schützen einer API durch das Hinzufügen einer Richtlinie für ein Aufruflimit (Drosselung)
> -   Testen der Transformationen

Fahren Sie mit dem nächsten Tutorial fort:

> [!div class="nextstepaction"]
> [Überwachen Ihrer API](api-management-howto-use-azure-monitor.md)
