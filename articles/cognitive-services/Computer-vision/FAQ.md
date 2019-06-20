---
title: 'Häufig gestellte Fragen: maschinelles Sehen'
titlesuffix: Azure Cognitive Services
description: Erhalten Sie Antworten auf häufig gestellte Fragen zur Maschinelles Sehen-API in Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 579a47e70f292222914723606d032737b98822bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60498779"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Häufig gestellte Fragen zur Maschinelles Sehen-API

> [!TIP]
> Wenn Sie in diesen FAQs keine Antwort auf Ihre Frage finden, können Sie die Fragen unter [Stack Overflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) und an die Community für die Maschinelles Sehen-API stellen oder wenden Sie sich an die [Hilfe und den Support bei UserVoice](https://cognitive.uservoice.com/).

-----

**Frage:** *Kann ich die Maschinelles Sehen-API für die Verwendung benutzerdefinierter Tags trainieren?  Zum Beispiel möchte ich Bilder von Katzenrassen einfügen, um die KI zu „trainieren“ und dann auf eine KI-Anforderung hin den Zuchtwert zu erhalten.*

**Antwort:** Diese Funktion ist derzeit nicht verfügbar. Unsere Techniker arbeiten jedoch daran, diese Funktionalität bald für maschinelles Sehen zu ermöglichen.

-----

**Frage:** *Kann maschinelles Sehen lokal ohne eine Internetverbindung genutzt werden?*

**Antwort:** Wir bieten derzeit keine lokale Lösung an.

-----

**Frage:** *Kann maschinelles Sehen zum Lesen von Kfz-Kennzeichen verwendet werden?*

**Antwort:** Die Maschinelles Sehen-API bietet eine gute Texterkennung mit OCR, aber sie ist derzeit nicht für Kfz-Kennzeichen optimiert. Wir sind ständig bemüht, unseren Dienst zu verbessern und haben OCR zur automatischen Nummernschilderkennung in unsere Liste der Funktionswünsche aufgenommen.

-----

**Frage:** *Welche Arten von Schreiboberflächen werden für die Handschrifterkennung unterstützt?*

**Antwort:** Die Technologie funktioniert mit verschiedenen Oberflächen, einschließlich Whiteboards, weißem Papier und gelben Haftnotizen.

-----

**Frage:** *Wie lange dauert der Vorgang der Handschrifterkennung?*

**Antwort:** Die Dauer ist abhängig von der Länge des Texts. Bei längeren Texten kann der Vorgang einige Sekunden dauern. Daher müssen Sie nach Abschluss der Operation „Handschriftlichen Text erkennen“ möglicherweise warten, bevor Sie die Ergebnisse mit der Operation „Ergebnisse für die Operation für handschriftlichen Text holen“ abrufen können.

-----

**Frage:** *Wie geht die Handschrifterkennungstechnologie mit Text um, der mit einem Auslassungszeichen in der Mitte einer Zeile eingefügt wurde?*

**Antwort:** Dieser Text wird von der Handschrifterkennung als separate Zeile zurückgegeben.

-----

**Frage:** *Wie geht die Handschrifterkennungstechnologie mit per Hand durchgestrichenen Wörtern oder Zeilen um?*

**Antwort:** Wenn die Wörter mehrmals durchgestrichen werden, um sie unkenntlich zu machen, werden sie von der Handschrifterkennung nicht erkannt. Wenn die Wörter jedoch mit einer einzigen Zeile durchgestrichen werden, wird diese Durchstreichung als Rauschen behandelt, und die Wörter werden trotzdem von der Handschrifterkennung erfasst.

-----

**Frage:** *Welche Textausrichtungen werden für die Handschrifterkennung unterstützt?*

**Antwort:** Text, der in einem Winkel von bis zu 30 Grad bis 40 Grad ausgerichtet ist, kann von der Handschrifterkennung erfasst werden.

-----
