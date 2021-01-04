---
title: Definieren von Vorschlägen für die benutzerdefinierte Vorschlagssuche | Benutzerdefinierte Bing-Suche
titleSuffix: Azure Cognitive Services
description: Die benutzerdefinierte Vorschlagssuche gibt eine Liste der für Ihre Suche relevanten vorgeschlagenen Suchabfragezeichenfolgen zurück.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 986a7c8f1282b76c2ae94eebcc2c31dc3b3e7cad
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352013"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurieren der benutzerdefinierten Vorschlagssuche

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die benutzerdefinierte Vorschlagssuche gibt eine Liste der für Ihre Suche relevanten vorgeschlagenen Suchabfragezeichenfolgen zurück. Die vorgeschlagenen Abfragezeichenfolgen basieren auf einer partiellen Abfragezeichenfolge, die der Benutzer in das Suchfeld eingibt. Die Liste enthält maximal 10 Vorschläge. 

Sie bestimmen, ob nur benutzerdefinierte Vorschläge zurückgegeben oder auch Bing-Vorschläge eingeschlossen werden. Wenn Sie Bing-Vorschläge einschließen, werden benutzerdefinierte Vorschläge vor den Bing-Vorschlägen angezeigt. Wenn Sie genügend relevante Vorschläge angeben, ist es möglich, dass die zurückgegebene Liste mit Vorschlägen keine Bing-Vorschläge enthält. Bing-Vorschläge beziehen sich immer auf den Kontext der benutzerdefinierten Suchinstanz. 

Um die Vorschläge zu Suchabfragen für Ihre Instanz zu konfigurieren, klicken Sie auf die Registerkarte **Vorschlagssuche**.  

> [!NOTE]
> Um dieses Feature verwenden zu können, müssen Sie die benutzerdefinierte Suche im entsprechenden Tarif abonnieren (siehe [Preise](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Es kann bis zu 24 Stunden dauern, bis Vorschläge am aktiven Endpunkt (API oder gehostete Benutzeroberfläche) verfügbar werden.

## <a name="enable-bing-suggestions"></a>Aktivieren der Bing-Vorschläge

Schalten Sie den Schieberegler für **Automatische Bing-Vorschläge** auf die Position EIN um, um Bing-Vorschläge zu aktivieren. Der Schieberegler wird Blau angezeigt.

## <a name="add-your-own-suggestions"></a>Hinzufügen eigener Vorschläge

Ihre eigenen Vorschläge für Abfragezeichenfolgen fügen Sie der Liste mit **benutzerdefinierten Vorschlägen** hinzu. Nachdem Sie der Liste einen Vorschlag hinzugefügt haben, drücken Sie die EINGABETASTE, oder klicken Sie auf das Symbol **+** . Sie können die Vorschläge in einer beliebigen Sprache angeben. Sie können maximal 5.000 Vorschläge für Abfragezeichenfolgen hinzufügen.

## <a name="upload-suggestions"></a>Hochladen von Vorschlägen

Sie können optional auch eine Liste mit Vorschlägen aus einer Datei hochladen. Die Datei darf pro Zeile jeweils nur eine Zeichenfolge zur Suchabfrage enthalten. Um die Datei hochzuladen, klicken Sie auf das Symbol für den Upload und wählen die Datei aus. Der Dienst extrahiert die Vorschläge aus der Datei und fügt sie der Liste hinzu.

## <a name="remove-suggestions"></a>Entfernen von Vorschlägen

Wenn Sie einen Vorschlag für eine Abfragezeichenfolge entfernen möchten, klicken Sie neben diesem auf das Symbol zum Entfernen.

## <a name="block-suggestions"></a>Blockieren von Vorschlägen

Wenn Sie Bing-Vorschläge einschließen, können Sie eine Liste von Abfragezeichenfolgen für die Suche hinzufügen, die Bing nicht zurückgeben soll. Wenn Sie blockierte Abfragezeichenfolgen hinzufügen möchten, klicken Sie auf **Show blocked suggestions** (Blockierte Vorschläge anzeigen). Fügen Sie die Abfragezeichenfolge der Liste hinzu, und drücken Sie die EINGABETASTE, oder klicken Sie auf das Symbol **+** . Sie können maximal 50 blockierte Abfragezeichenfolgen hinzufügen.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Es kann bis zu 24 Stunden dauern, bis Änderungen an der Konfiguration der benutzerdefinierten Vorschlagssuche wirksam werden.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Aktivieren der Vorschlagssuche auf gehosteter Benutzeroberfläche

Um Vorschläge für Abfragezeichenfolgen für Ihre gehostete Benutzeroberfläche zu aktivieren, klicken Sie auf **Gehostete Benutzeroberfläche**. Scrollen Sie nach unten zum Abschnitt **Zusätzliche Konfiguration**. Wählen Sie unter **Websuche** für **Enable autosuggest** (Vorschlagssuche aktivieren) die Option **Ein** aus. Um die Vorschlagssuche zu aktivieren, müssen Sie ein Layout auswählen, das ein Suchfeld enthält.


## <a name="calling-the-autosuggest-api"></a>Aufrufen der Vorschlagssuche-API

Um mithilfe der API für die benutzerdefinierte Bing-Suche vorgeschlagene Abfragezeichenfolgen zu erhalten, senden Sie eine `GET`-Anforderung an den folgenden Endpunkt.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Die Antwort enthält eine Liste mit `SearchAction`-Objekten, in denen die vorgeschlagenen Abfragezeichenfolgen enthalten sind.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Jeder Vorschlag enthält die Felder `displayText` und `query`. Das Feld `displayText` enthält die vorgeschlagene Abfragezeichenfolge, die Sie zum Auffüllen der Dropdownliste des Suchfelds verwenden.

Wenn der Benutzer in der Dropdownliste eine vorgeschlagene Abfragezeichenfolge auswählt, können Sie die Abfragezeichenfolge im Feld `query` verwenden, wenn Sie die [API für die benutzerdefinierte Bing-Suche](overview.md) aufrufen.


## <a name="next-steps"></a>Nächste Schritte

- [Abrufen von benutzerdefinierten Vorschlägen]()
- [Suchen der benutzerdefinierten Instanz](./search-your-custom-view.md)
- [Konfigurieren und Verwenden der benutzerdefinierten gehosteten Benutzeroberfläche](./hosted-ui.md)