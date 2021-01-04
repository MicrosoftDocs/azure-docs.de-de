---
title: 'Tutorial: Debuggen von APIs in Azure API Management mithilfe der Anforderungsablaufverfolgung'
description: In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie die Ablaufverfolgung aktivieren und Anforderungsverarbeitungsschritte in Azure API Management überprüfen.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 531e346569b85ababc382f997fd7764a92b3d05f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542313"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>Tutorial: Debuggen von APIs mit der Anforderungsablaufverfolgung

In diesem Tutorial wird beschrieben, wie Sie die Anforderungsverarbeitung in Azure API Management überprüfen (verfolgen), um das Debuggen und Beheben von Problemen mit Ihrer API zu vereinfachen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verfolgen eines Beispielaufrufs
> * Überprüfen der Schritte zur Anforderungsverarbeitung

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="API-Inspektor":::

## <a name="prerequisites"></a>Voraussetzungen

+ Machen Sie sich mit der [Azure API Management-Terminologie](api-management-terminology.md) vertraut.
+ Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ Absolvieren Sie das folgende Tutorial: [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md).

## <a name="verify-allow-tracing-setting"></a>Überprüfen der Einstellung „Ablaufverfolgung zulassen“ 

Die Einstellung **Ablaufverfolgung zulassen** muss für das Abonnement aktiviert werden, das für Ihre API verwendet wird. Wenn Sie das integrierte Abonnement mit allen Zugriffsberechtigungen verwenden, ist die Einstellung standardmäßig aktiviert. Navigieren Sie zur Überprüfung im Portal zu ihrer API Management-Instanz, und wählen Sie **Abonnements** aus.

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="Zulassen der Ablaufverfolgung für ein Abonnement":::

## <a name="trace-a-call"></a>Verfolgen eines Aufrufs

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer API Management-Instanz.
1. Klicken Sie auf **APIs**.
1. Wählen Sie in der API-Liste **Demo Conference API** aus.
1. Wählen Sie die Registerkarte **Testen** aus.
1. Wählen Sie den Vorgang **GetSpeakers** aus.
1. Vergewissern Sie sich, dass der HTTP-Anforderungsheader **Ocp-Apim-Trace: True** und einen gültigen Wert für **Ocp-Apim-Subscription-Key** enthält. Ist dies nicht der Fall, wählen Sie **+ Header hinzufügen** aus, um den Header hinzuzufügen.
1. Wählen Sie **Senden** aus, um einen API-Aufruf durchzuführen.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="Konfigurieren der API-Ablaufverfolgung":::

> [!TIP]
> Wird **Ocp-Apim-Subscription-Key** in der HTTP-Anforderung nicht automatisch aufgefüllt, können Sie den Wert im Portal abrufen. Wählen Sie **Abonnements** aus, und öffnen Sie das Kontextmenü ( **...** ) für Ihr Abonnement. Wählen Sie **Geheimnisse anzeigen/ausblenden** aus. Sie können Schlüssel bei Bedarf auch erneut generieren. Fügen Sie dem Header dann einen Schlüssel hinzu.

## <a name="review-trace-information"></a>Überprüfen der Ablaufverfolgungsinformationen

1. Navigieren Sie nach Abschluss des Aufrufs in der **HTTP-Antwort** zur Registerkarte **Ablaufverfolgung**.
1. Wählen Sie einen der folgenden Links aus, um ausführliche Informationen zur Ablaufverfolgung anzuzeigen: **Eingehend**, **Back-End**, **Ausgehend**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="Überprüfen der Antwortablaufverfolgung":::

    * **Eingehend**: Hier werden die ursprüngliche Anforderung, die API Management vom Aufrufer empfangen hat, sowie die auf die Anforderung angewendeten Richtlinien angezeigt. Wenn Sie beispielsweise unter [Tutorial: Transformieren und Schützen Ihrer API](transform-api.md) Richtlinien hinzugefügt haben, werden diese hier angezeigt.

    * **Back-End**: Hier werden die Anforderungen, die API Management an das API-Back-End gesendet hat, sowie die erhaltene Antwort angezeigt.

    * **Ausgehend**: Hier werden die Richtlinien angezeigt, die auf die Antwort angewendet werden, bevor sie zurück an den Aufrufer gesendet wird.

    > [!TIP]
    > Jeder Schritt zeigt zudem die Zeit an, die verstrichen ist, seit die Anforderung von API Management empfangen wurde.

1. Auf der Registerkarte **Nachricht** wird vom Header **ocp-apim-trace-location** der Speicherort der in Azure-Blobspeicher gespeicherten Ablaufverfolgungsdaten angezeigt. Navigieren Sie bei Bedarf zu diesem Speicherort, um die Ablaufverfolgung abzurufen.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Ablaufverfolgungsspeicherort in Azure Storage":::
## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Verfolgen eines Beispielaufrufs
> * Überprüfen der Schritte zur Anforderungsverarbeitung

Fahren Sie mit dem nächsten Tutorial fort:

> [!div class="nextstepaction"]
> [Gefahrloses Vornehmen geringfügiger Änderungen mithilfe von Revisionen](api-management-get-started-revise-api.md)
