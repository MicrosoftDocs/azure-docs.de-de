---
title: Webhook-Aktivität in Azure Data Factory | Microsoft-Dokumentation
description: Die Webhook-Aktivität setzt die Ausführung der Pipeline erst fort, nachdem sie das angefügte Dataset mit bestimmten vom Benutzer angegebenen Kriterien überprüft hat.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 6ec43b06ce266b9ceaddb5dd21cbf52f509d6596
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764304"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-Aktivität in Azure Data Factory
Mithilfe einer Webhook-Aktivität können Sie die Ausführung von Pipelines über Ihren benutzerdefinierten Code steuern. Mithilfe der Webhook-Aktivität können Kunden einen Endpunkt aufrufen und eine Rückruf-URL übergeben. Die Pipelineausführung wartet, bis der Rückruf aufgerufen wurde, bevor sie mit der nächsten Aktivität fortfährt.

## <a name="syntax"></a>Syntax

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Typeigenschaften



Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
name | Name der Webhook-Aktivität | string | Ja |
type | Muss auf **WebHook** festgelegt werden. | string | Ja |
method | REST-API-Methode für den Zielendpunkt. | Eine Zeichenfolge. Unterstützte Typen: ‚POST‘ | Ja |
url | Zielendpunkt und Pfad | Zeichenfolge (oder Ausdruck mit resultType der Zeichenfolge). | Ja |
headers | Header, die in der Anforderung gesendet werden. So legen Sie beispielsweise die Sprache und den Typ für eine Anforderung fest: „headers“: { „Accept-Language“: „en-us“, „Content-Type“: „application/json“ }. | Zeichenfolge (oder ein Ausdruck mit resultType der Zeichenfolge) | Ja, der Content-Type-Header ist erforderlich. "headers":{ "Content-Type":"application/json"} |
body | Stellt die an den Endpunkt gesendete Nutzlast dar. | Der an den Rückruf-URI zurückgegebene Textkörper sollte ein gültiges JSON-Objekt sein. Weitere Informationen finden Sie in den Details zum Schema der Anforderungsnutzlast im Abschnitt [Schema der Anforderungsnutzlast](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | Ja |
authentication | Die zum Aufrufen des Endpunkts verwendete Authentifizierungsmethode. Unterstützte Typen sind „Basic“ oder „ClientCertificate“. Weitere Informationen finden Sie im Abschnitt [Authentifizierung](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Wenn keine Authentifizierung erforderlich ist, schließen Sie diese Eigenschaft aus. | Zeichenfolge (oder ein Ausdruck mit resultType der Zeichenfolge) | Nein |
timeout | Legt fest, wie lange die Aktivität darauf warten soll, dass &#39;callBackUri&#39; aufgerufen wird. Legt fest, wie lange die Aktivität darauf warten soll, dass „callBackUri“ aufgerufen wird. Der Standardwert ist 10 Minuten („00:10:00“). Das Format ist „Timespan“, d.h. „d.hh:mm:ss“. | string | Nein |

## <a name="additional-notes"></a>Zusätzliche Hinweise

Azure Data Factory übergibt die zusätzliche Eigenschaft „callBackUri“ im Textkörper an den URL-Endpunkt und erwartet, dass dieser URI vor dem angegebenen Timeoutwert aufgerufen wird. Wenn der URI nicht aufgerufen wird, schlägt die Aktivität mit dem Status „TimedOut“ fehl.

Die Webhook-Aktivität selbst schlägt nur fehl, wenn beim Aufruf des benutzerdefinierten Endpunkts ein Fehler auftritt. Im Textkörper des Rückrufs kann jede beliebige Fehlermeldung hinzugefügt und in einer nachfolgenden Aktivität verwendet werden.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter anderen Ablaufsteuerungsaktivitäten, die von Data Factory unterstützt werden:

- [Aktivität „If Condition“](control-flow-if-condition-activity.md)
- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
- [Until-Aktivität](control-flow-until-activity.md)
