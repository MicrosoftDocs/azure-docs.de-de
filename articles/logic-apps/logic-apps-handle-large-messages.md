---
title: Verarbeiten von großen Nachrichten in Azure Logic Apps | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie große Nachrichten durch Blockerstellung (Segmentierung) in Azure Logic Apps verarbeiten können
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 4/27/2018
ms.author: shhurst
ms.openlocfilehash: 5aa5ea2a39a0fb9f969e965fed14063522197cda
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303770"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Verarbeiten von großen Nachrichten durch Blockerstellung in Azure Logic Apps

Beim Verarbeiten von Nachrichten begrenzt Logic Apps Nachrichteninhalt auf eine maximale Größe. Dieser Grenzwert ermöglicht ein Verringern des Mehraufwands, der durch das Speichern und Verarbeiten von großen Nachrichten verursacht wird. Sollen Nachrichten verarbeitet werden, die größer sind, als dieser Grenzwert angibt, kann Logic Apps eine große Nachricht durch *Blockerstellung* in kleinere Nachrichten aufteilen. Auf diese Weise können Sie weiterhin große Dateien unter bestimmten Bedingungen mit Logic Apps übertragen. Bei der Kommunikation mit anderen Diensten über Connectors oder HTTP kann Logic Apps große Nachrichten verarbeiten, jedoch *nur* in Blöcken. Diese Bedingung bedeutet, dass auch Connectors Blockerstellung unterstützen müssen oder der zugrunde liegende HTTP-Nachrichtenaustausch zwischen Logic Apps und diesen Diensten Blockerstellung verwenden muss.

In diesem Artikel wird gezeigt, wie Sie die Blockerstellung für Aktionen im Zusammenhang mit Nachrichten einrichten, deren Größe den Grenzwert übersteigt. Logik-App-Trigger unterstützen die Blockerstellung aufgrund des Mehraufwands für den Austausch mehrerer Nachrichten nicht. 

## <a name="what-makes-messages-large"></a>Was macht Nachrichten „groß“?

Nachrichten sind „groß“ in Bezug auf den Dienst, von dem diese Nachrichten verarbeitet werden. Die genaue Größenbeschränkung für große Nachrichten ist zwischen Logic Apps und Connectors unterschiedlich. Sowohl Logic Apps als auch Connectors können große Nachrichten nicht direkt verarbeiten, weshalb diese in Blöcke aufgeteilt werden müssen. Informationen zur Logic Apps-Größenbeschränkung für Nachrichten finden Sie unter [Grenzwerte und Konfigurationsinformationen für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md).
Informationen zu der Nachrichtengrößenbeschränkung für jeden Connector finden Sie in den [speziellen technischen Details der Connectors](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Logic Apps-Verarbeitung von Nachrichten, die in Blöcke aufgeteilt sind

Logic Apps kann Ausgaben von Nachrichten, die wegen der Überschreitung der Nachrichtengrößenbeschränkung in Blöcke aufgeteilt wurden, nicht direkt verwenden. Nur Aktionen, die Blockerstellung unterstützen, können auf die Nachrichteninhalte in diesen Ausgaben zugreifen. Daher muss eine Aktion, die große Nachrichten verarbeitet, *eines* dieser Kriterien erfüllen:

* Systemeigene Unterstützung für Blockerstellung, wenn diese Aktion zu einem Connector gehört. 
* In der Laufzeitkonfiguration dieser Aktion muss die Unterstützung von Blockerstellung aktiviert sein. 

Andernfalls erhalten Sie einen Laufzeitfehler, wenn Sie versuchen, auf große Inhaltsausgaben zuzugreifen. Informationen dazu, wie Blockerstellung aktiviert wird, finden Sie unter [Einrichten der Blockerstellungsunterstützung](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Connector-Verarbeitung von Nachrichten, die in Blöcke aufgeteilt sind

Dienste, die mit Logic Apps kommunizieren, haben möglicherweise eigene Nachrichtengrößenbeschränkungen. Diese Beschränkungen sind häufig kleiner als die Logic Apps-Beschränkung. Nehmen Sie beispielsweise an, dass ein Connector, der Blockerstellung unterstützt, eine 30-MB-Nachricht als groß einstuft, während Logic Apps dies nicht tut. Um dieser Beschränkung des Connectors zu genügen, teilt Logic Apps jede Nachricht, die größer als 30 MB ist, in kleinere Blöcke auf.

Für Connectors, die Blockerstellung unterstützen, ist das zugrunde liegende Blockerstellungsprotokolls für Endbenutzer nicht sichtbar. Allerdings unterstützen nicht alle Connectors Blockerstellung, sodass diese Connectors Laufzeitfehler generieren, wenn eingehende Nachrichten die Größenbeschränkungen des jeweiligen Connectors überschreiten.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Einrichten von Blockerstellung über HTTP

In generischen HTTP-Szenarien können Sie herunter- oder hochzuladende Objekte mit großen Inhalten über HTTP aufteilen, sodass Ihre Logik-App und ein Endpunkt große Nachrichten austauschen können. Allerdings müssen Sie Nachrichten so in Blöcke aufteilen, wie dies von Logic Apps erwartet wird. 

Ist für einen Endpunkt Blockerstellung für Downloads oder Uploads aktiviert, werden große Nachrichten von den HTTP-Aktionen in Ihrer Logik-App automatisch in Blöcke aufgeteilt. Andernfalls müssen Sie Blockerstellungsunterstützung auf dem Endpunkt einrichten. Wenn Sie den Endpunkt oder Connector nicht besitzen oder steuern, haben Sie möglicherweise keine Möglichkeit, Blockerstellung einzurichten.

Außerdem müssen Sie, wenn eine HTTP-Aktion Blockerstellung noch nicht unterstützt, Blockerstellung auch in `runTimeConfiguration`-Eigenschaft der Aktion einrichten. Sie können diese Eigenschaft innerhalb der Aktion festlegen, entweder direkt im Codeansicht-Editor, wie dies weiter unten beschrieben ist, oder im Designer für Logik-Apps, wie dies hier beschrieben ist:

1. Wählen Sie in der oberen rechten Ecke der HTTP-Aktion die Ellipsenschaltfläche ( **...** ) aus, und wählen Sie dann **Einstellungen** aus.

   ![Menü „Einstellungen“ für die Aktion öffnen](./media/logic-apps-handle-large-messages/http-settings.png)

2. Legen Sie unter **Inhaltsübertragung** die Option **Segmentierung zulassen** auf **Ein** fest.

   ![Aktivieren von Segmentierung (Blockerstellung)](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Setzen Sie das Einrichten der Segmentierung (Blockerstellung) für Downloads oder Uploads fort, indem Sie die Schritte in den folgenden Abschnitten ausführen.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Herunterladen von Inhalten in Blöcken

Viele Endpunkte senden große Nachrichten, wenn diese über eine HTTP-GET-Anforderung heruntergeladen werden, automatisch in Blöcken. Damit in Blöcken vorliegende (segmentierte) Nachrichten von einem Endpunkt über HTTP heruntergeladen werden können, muss der Endpunkt partielle Inhaltsanforderungen oder *segmentierte Downloads* unterstützen. Wenn Ihre Logik-App eine HTTP-GET-Anforderung zum Herunterladen von Inhalt an einen Endpunkt sendet und der Endpunkt mit dem Statuscode „206“ antwortet, enthält die Antwort segmentierten Inhalt. Logic Apps kann nicht steuern, ob ein Endpunkt partielle Anforderungen unterstützt. Wenn Ihre Logik-App aber die erste „206“-Antwort empfangen hat, sendet sie automatisch mehrere Anforderungen, um den gesamten Inhalt herunterzuladen.

Um zu überprüfen, ob ein Endpunkt partiellen Inhalt unterstützen kann, senden Sie eine HEAD-Anforderung. Über diese Anforderung können Sie ermitteln, ob die Antwort den `Accept-Ranges`-Header enthält. Auf diese Weise können Sie, wenn der Endpunkt segmentierte Downloads unterstützt, aber keinen segmentierten Inhalt sendet, diese Option *vorschlagen*, indem Sie den `Range`-Header in Ihrer HTTP-GET-Anforderung festlegen. 

In den folgenden Schritten ist die Vorgehensweise ausführlich beschrieben, in der Logic Apps segmentierten Inhalt von einem Endpunkt in Ihre Logik-App herunterlädt:

1. Ihre Logik-App sendet eine HTTP-GET-Anforderung an den Endpunkt.

   Der Anforderungsheader kann optional ein `Range`-Feld enthalten, in dem ein Bytebereich für das Anfordern von Inhaltsblöcken beschrieben ist.

2. Der Endpunkt antwortet mit dem Statuscode „206“ und einem HTTP-Nachrichtentext.

    Details über den Inhalt in diesem Block (Segment) werden im `Content-Range`-Header der Antwort angezeigt. Dazu gehören auch die Informationen, anhand denen Logic Apps den Anfang und das Ende des Blocks sowie die Gesamtgröße ermittelt, die der Inhalt vor der Blockerstellung hatte.

3. Ihre Logik-App sendet automatisch entsprechende weitere HTTP-GET-Anforderungen.

    Ihre Logik-App sendet weitere GET-Anforderungen, bis der gesamte Inhalt abgerufen ist.

Zum Beispiel ist in dieser Aktionsdefinition eine HTTP GET-Anforderung zu sehen, in der der `Range`-Header festgelegt wird. Im Header wird *vorgeschlagen*, dass der Endpunkt mit segmentiertem Inhalt antworten soll:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

Die GET-Anforderung legt den „Range“-Header auf „bytes=0-1023“ fest, was der Bereich für Bytes ist. Wenn der Endpunkt Anforderungen zu partiellem Inhalt unterstützt, antwortet er mit einem Inhaltsblock aus dem angeforderten Bereich. Je nach Endpunkt kann das genaue Format für das „Range“-Headerfeld unterschiedlich sein.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Hochladen von Inhalten in Blöcken

Damit in einer HTTP-Aktion segmentierter Inhalt hochgeladen werden kann, muss Blockerstellungsunterstützung für die Aktion über deren `runtimeConfiguration`-Eigenschaft aktiviert sein. Diese Einstellung versetzt die Aktion in die Lage, das Blockerstellungsprotokoll zu starten. Ihre Logik-App kann dann eine erste POST- oder PUT-Nachricht an den Zielendpunkt senden. Nachdem der Endpunkt mit einer vorgeschlagenen Blockgröße (Segmentgröße) geantwortet hat, sendet Ihre Logik-App im Anschluss HTTP-PATCH-Anforderungen, die die Inhaltsblöcke enthalten.

In den folgenden Schritten ist die Vorgehensweise ausführlich beschrieben, in der Logic Apps segmentierten Inhalt von Ihrer Logik-App in einen Endpunkt hochlädt:

1. Ihre Logik-App sendet eine erste HTTP-POST- oder -PUT-Anforderung mit einem leeren Nachrichtentext. Die Anforderungsheader enthält diese Informationen zu dem Inhalt, den Ihre Logik-App in Blöcken hochladen möchte:

   | Logic Apps-Feld für Anforderungsheader | Wert | Type | BESCHREIBUNG |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | chunked | String | Gibt an, dass der Inhalt in Blöcken (Segmenten) hochgeladen wird |
   | **x-ms-content-length** | <*Inhaltslänge*> | Integer | Der Gesamtgröße des Inhalts in Bytes vor der Blockerstellung |
   ||||

2. Der Endpunkt antwortet mit dem Erfolgsstatuscode „200“ und diesen optionalen Informationen:

   | Endpunktfeld für Antwortheader | Type | Erforderlich | BESCHREIBUNG |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Integer | Nein | Der vorgeschlagene Blockgröße in Bytes |
   | **Location** | String | Nein | Die URL-Adresse, an die die HTTP-PATCH-Nachrichten gesendet werden sollen |
   ||||

3. Ihre Logik-App erstellt und sendet nacheinander HTTP-PATCH-Nachrichten, wobei jede Nachricht diese Informationen enthält:

   * Einen Inhaltsblock, dessen Größe dem Wert von **x-ms-chunk-size** oder einem intern berechneten Wert entspricht, bis der gesamte Inhalt entsprechend **x-ms-content-length** sequenziell hochgeladen ist

   * Im Header sind diese Details zu dem Inhaltsblock enthalten, der in der jeweiligen PATCH-Nachricht gesendet wurde:

     | Logic Apps-Feld für Anforderungsheader | Wert | Type | BESCHREIBUNG |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*Bereich*> | String | Der Bytebereich für den aktuellen Inhaltsblock, einschließlich des Anfangswerts, des Endwerts und der Gesamtgröße des Inhalts, zum Beispiel: „bytes=0-1023/10100“ |
     | **Content-Type** | <*Inhaltstyp*> | String | Der Typ des segmentierten (in Blöcke aufgeteilten) Inhalts |
     | **Content-Length** | <*Inhaltslänge*> | String | Die Länge des aktuellen Blocks in Bytes |
     |||||

4. Nach jeder PATCH-Anforderung bestätigt der Endpunkt den Empfang des entsprechenden Blocks durch Antworten mit dem Statuscode „200“.

In dieser Aktionsdefinition ist z. B. eine HTTP-POST-Anforderung gezeigt, in der segmentierter Inhalt an einen Endpunkt hochgeladen wird. In der `runTimeConfiguration`-Eigenschaft der Aktion wird die `contentTransfer`-Eigenschaft `transferMode` auf `chunked` festgelegt:

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```