---
title: Aufrufen einer API in einer App – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Hier wird beschrieben, wie Sie die Custom Decision Service-APIs über eine Smartphone-App aufrufen.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ms.openlocfilehash: 0e5c99aae61fb927ea7f101bab74d661a747f88b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60511547"
---
# <a name="call-api-from-an-app"></a>Aufrufen einer API in einer App

Aufrufen der Azure Custom Decision Service-APIs über eine Smartphone-App. In diesem Artikel werden einige einfache Optionen vorgestellt.

Achten Sie darauf, dass Sie Ihre Anwendung [zuerst registrieren](custom-decision-service-get-started-register.md).

Es gibt zwei API-Aufrufe, die Sie über Ihre Smartphone-App an Custom Decision Service durchführen: ein Aufruf der Rangfolge-API zum Abrufen einer Rangfolge Ihres Inhalts und ein Aufruf der Relevanz-API um eine Bewertung abzugeben. Hier sehen Sie die Beispielaufrufe in [cURL](https://en.wikipedia.org/wiki/CURL).

Weitere Informationen finden Sie in der [API](custom-decision-service-api-reference.md)-Referenz.

Rufen Sie zunächst die Rangfolge-API auf. Erstellen Sie die Datei `<request.json>`, die die Aktionssatz-ID aufweist. Diese ID ist der Name des entsprechenden RSS- oder Atom-Feeds, die Sie im Portal eingegeben haben:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Viele Aktionssätze können wie folgt angegeben werden:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Diese JSON-Datei wird dann im Rahmen einer Rangfolgeanforderung übermittelt:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Hier ist `<appId>` der Name Ihrer Anwendung, die im Portal registriert wurde. Sie sollten eine geordnete Menge an Inhaltselementen erhalten, die Sie in Ihrer Anwendung rendern können. Eine Beispielrückgabe kann z.B. wie folgt aussehen:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

Der erste Teil der Rückgabe besteht aus einer Liste geordneter Aktionen, die von der jeweiligen Aktions-ID angegeben werden. Für einen Artikel ist die Aktions-ID eine URL. Die gesamte Anforderung hat eine eindeutige `<eventId>`, die vom System erstellt wird.

Später können Sie angeben, ob es einen Klick auf das erste Inhaltselement dieses Ereignisses, `<actionId3>`, gab. Dann können Sie die Relevanz dieser `<eventId>` an Custom Decision Service über die Relevanz-API melden. Eine derartige Anforderung könnte z.B. wie folgt aussehen:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Zuletzt müssen Sie noch die Aktionssatz-API angeben, die eine Liste von Artikeln (Aktionen) zurückgibt, die von Custom Decision Service beachtet werden müssen. Implementieren Sie diese API wie folgt als RSS-Feed:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Hier beschreibt jedes `<item>`-Element auf oberster Ebene einen Artikel. Der `<link>` ist verpflichtend und wird von Custom Decision Service als Aktions-ID verwendet. Geben Sie `<date>` (im Standard-RSS-Format) an, wenn es mehr als 15 Artikel gibt. Es werden die 15 aktuellsten Artikel verwendet. Der `<title>` ist optional und wird verwendet, um textgebundene Features für den Artikel zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

* Abschließen Sie eines [Tutorials](custom-decision-service-tutorial-news.md), um ein tiefergehendes Beispiel kennenzulernen.
* Weitere Informationen zu den bereitgestellten Funktionen finden Sie in der [API-Referenz](custom-decision-service-api-reference.md).
