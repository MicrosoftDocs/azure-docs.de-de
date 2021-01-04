---
title: Häufig gestellte Fragen (FAQ) – Bing-Bildersuche-API
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu Konzepten, Code und Szenarien der Bing-Bildersuche-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e8502b1d01e0f3fbf5d42e924511cc978690bce4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342191"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Häufig gestellte Fragen (FAQ) zur Bing-Bildersuche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

In diesem Artikel finden Sie Antworten zu häufig gestellten Fragen zu Konzepten, Code und Szenarios der Bing-Bildersuche-API für Microsoft Cognitive Services in Azure.

## <a name="response-headers-in-javascript"></a>Antwortheader in JavaScript

Die folgenden Header können als Antworten der Bing-Bildersuche-API auftreten.

| attribute           | BESCHREIBUNG   |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |Die eindeutige ID, die Bing dem Benutzer zugewiesen hat |
| `BingAPIs-Market`   |Der Markt, der zur Erfüllung der Anforderung verwendet wurde |
| `BingAPIs-TraceId`  |Der Protokolleintrag auf den Bing-API-Server für diese Anforderung (für Unterstützung) |

Es ist besonders wichtig, die Client-ID beizubehalten und mit nachfolgenden Anforderungen zurückzugeben. Wenn Sie dies tun, verwendet die Suche den vergangenen Kontext in der Suchergebnisrangfolge und bietet auch eine konsistente Benutzererfahrung.

Wenn Sie jedoch die Bing-Bildersuche-API von JavaScript aus aufrufen, kann es sein, dass die in Ihrem Browser integrierten Sicherheitsfunktionen (CORS) unterbinden, dass Sie auf die Werte dieser Header zuzugreifen können.

Um Zugriff auf die Header zu erhalten, können Sie die Bing-Bildersuche-API über einen CORS-Proxy anfordern. In der Antwort eines solchen Proxys befindet sich ein `Access-Control-Expose-Headers`-Header, mit dem die Antwortheader gefiltert und für JavaScript verfügbar gemacht werden.

Die Installation eines CORS-Proxys, mit dem die [Tutorial-App](tutorial-bing-image-search-single-page-app.md) auf die optionalen Clientheader zugreifen kann, ist schnell und unkompliziert. [Installieren Sie Node.js](https://nodejs.org/en/download/), falls Sie dies noch nicht getan haben. Geben Sie dann an einer Eingabeaufforderung den folgenden Befehl ein.

```console
npm install -g cors-proxy-server
```

Passen Sie den Endpunkt der Bing-Bildersuche-API in der HTML-Datei wie folgt an:
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Starten Sie abschließend den CORS-Proxy mit folgendem Befehl:

```console
cors-proxy-server
```

Lassen Sie das Fenster während der Nutzung der Tutorial-App geöffnet. Wenn Sie das Fenster schließen, wird auch die Ausführung des Proxys beendet. Im Bereich mit den erweiterbaren HTTP-Headern unter den Suchergebnissen wird nun u.a. der `X-MSEdge-ClientID`-Header angezeigt. Hier können Sie überprüfen, ob dieser für alle Anforderungen identisch ist.

## <a name="response-headers-in-production"></a>Antwortheader in einer Produktionsumgebung

Der in der vorherigen Antwort erläuterte Ansatz mit CORS-Proxy ist für Entwicklung, Test und Lernen geeignet.

In einer Produktionsumgebung sollten Sie jedoch ein serverseitiges Skript auf derselben Domäne wie die Webseite hosten, die die Bing-Websuche-API verwendet. Dieses Skript sollte die API-Aufrufe auf Anforderung des JavaScript auf der Webseite ausführen und alle Ergebnisse, einschließlich der Header, an den Client zurückgeben. Da die beiden Ressourcen (Seite und Skript) einen gemeinsamen Ursprung haben, wird CORS nicht aktiviert, und die speziellen Header sind für das JavaScript auf der Webseite zugänglich.

Dieser Ansatz schützt auch Ihren API-Schlüssel vor der Offenlegung, da dieser nur vom serverseitigen Skript benötigt wird. Das Skript kann eine andere Methode (z.B. den HTTP-Verweiser) verwenden, um sicherzustellen, dass die Anforderung autorisiert ist.

## <a name="next-steps"></a>Nächste Schritte

Haben Sie eine Frage zu einem fehlenden Feature bzw. einer fehlenden Funktion? Sie können gerne auf der [UserVoice-Website](https://cognitive.uservoice.com/forums/555907-bing-search) eine entsprechende Anforderung erstellen oder dafür abstimmen.

## <a name="see-also"></a>Weitere Informationen

 [Stack Overflow: Cognitive Services](https://stackoverflow.com/questions/tagged/bing-api)