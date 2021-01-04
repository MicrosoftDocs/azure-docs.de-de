---
title: 'Schnellstart: Erstellen der ersten Instanz der benutzerdefinierten Bing-Suche'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung zum Erstellen einer Instanz der benutzerdefinierten Bing-Suche, mit der von Ihnen definierte Domänen und Webseiten durchsucht werden können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 726a60d611abc392bc1a4629c5088ca4c6b703d9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338332"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Schnellstart: Erstellen Ihrer ersten Instanz der benutzerdefinierten Bing-Suche

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Zum Verwenden einer benutzerdefinierten Bing-Suche müssen Sie eine Instanz der benutzerdefinierten Suche erstellen, mit der Ihre Ansicht bzw. das Segment des Webs definiert wird. Diese Instanz enthält die öffentlichen Domänen, Websites und Webseiten, die Sie durchsuchen möchten, sowie alle Rangfolgenanpassungen, die Sie ggf. durchführen. 

Verwenden Sie zum Erstellen der Instanz das [Portal für die benutzerdefinierte Bing-Suche](https://customsearch.ai). 

![Abbildung: Portal für die benutzerdefinierte Bing-Suche](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Erstellen einer Instanz für die benutzerdefinierte Suche

Erstellen Sie wie folgt eine Instanz für die benutzerdefinierte Bing-Suche:

1. Klicken Sie auf der Webseite mit dem [Portal für die benutzerdefinierte Bing-Suche](https://customsearch.ai) auf **Get Started** (Jetzt einsteigen), und melden Sie sich an Ihrem Microsoft-Konto an.

2. Klicken Sie auf **Neue Instanz**, und geben Sie einen aussagekräftigen Namen ein. Sie können den Namen Ihrer Instanz jederzeit ändern.
 
3. Geben Sie auf der **Suchoberfläche** auf der Registerkarte **Aktiv** die URL für mindestens eine Website ein, die Sie in Ihre Suche einbeziehen möchten. 

    > [!NOTE]
    > Instanzen der benutzerdefinierten Bing-Suche geben nur Ergebnisse für Domänen sowie für Webseiten zurück, die öffentlich sind und von Bing indiziert wurden.

4. Sie können im Portal für die benutzerdefinierte Bing-Suche auf der rechten Seite eine Abfrage eingeben und die Suchergebnisse untersuchen, die von Ihrer Suchinstanz zurückgegeben werden. Wenn keine Ergebnisse zurückgegeben werden, können Sie versuchen, eine andere URL einzugeben.  

5. Klicken Sie auf **Veröffentlichen**, um Ihre Änderungen an der Produktionsumgebung zu veröffentlichen, und aktualisieren Sie die Endpunkte der Instanz.

6.  Klicken Sie auf die Registerkarte **Produktion** unter **Endpunkte**, und kopieren Sie Ihre **Benutzerdefinierte Konfigurations-ID**. Sie benötigen diese ID zum Aufrufen der API für die benutzerdefinierte Bing-Suche, indem Sie sie in Ihren Aufrufen an den Abfrageparameter `customconfig=` anfügen.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Aufrufen eines Endpunkts für die benutzerdefinierte Bing-Suche](./call-endpoint-csharp.md)