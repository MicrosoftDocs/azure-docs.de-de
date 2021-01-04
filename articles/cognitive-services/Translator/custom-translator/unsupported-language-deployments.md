---
title: Bereitstellungen in nicht unterstützten Sprachen – Benutzerdefinierter Translator
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie nicht unterstützte Sprachpaare im benutzerdefinierten Translator von Azure Cognitive Services bereitstellen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 7498169c2c61b8337a90fbda082729f200f04635
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369034"
---
# <a name="unsupported-language-deployments"></a>Bereitstellungen in nicht unterstützten Sprachen

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Mit der bevorstehenden Einstellung des Microsoft Translator-Hubs wird Microsoft für alle derzeit über den Hub bereitgestellten Modelle die Bereitstellung aufheben. Viele von Ihnen verfügen über im Hub bereitgestellte Modelle, deren Sprachenpaare im benutzerdefinierten Translator nicht unterstützt werden.  Wir möchten nicht, dass die Benutzer in dieser Situation keine Möglichkeit haben, ihre Inhalte zu übersetzen.

Wir verfügen jetzt über einen Prozess, der es Ihnen ermöglicht, Ihre nicht unterstützten Modelle über den benutzerdefinierten Translator bereitzustellen.  Dieser Prozess ermöglicht es Ihnen, Inhalte weiterhin mit der neuesten V3-API zu übersetzen.  Diese Modelle werden gehostet, bis Sie die Bereitstellung wieder aufheben oder das Sprachenpaar im benutzerdefinierten Translator zur Verfügung gestellt wird.  In diesem Artikel wird die Bereitstellung von Modellen mit nicht unterstützten Sprachenpaaren erläutert.

## <a name="prerequisites"></a>Voraussetzungen

Damit sich Ihre Modelle für die Bereitstellung eignen, müssen sie die folgenden Kriterien erfüllen:
* Das Projekt, das das Modell enthält, muss mit dem Migrationstool vom Hub in den benutzerdefinierten Translator migriert worden sein.  Informationen zum Migrieren von Projekten und Arbeitsbereichen finden Sie [hier](how-to-migrate.md).
* Das Modell muss sich zum Zeitpunkt der Migration im Zustand „bereitgestellt“ befinden.  
* Das Sprachenpaar des Modells muss ein nicht unterstütztes Sprachenpaar im benutzerdefinierten Translator sein.  Sprachenpaare, bei denen eine Sprache für die Übersetzung in oder von Englisch unterstützt wird, das Paar selbst aber kein Englisch umfasst, sind Kandidaten für Bereitstellungen in nicht unterstützten Sprachen.  So wird z. B. ein Hubmodell für ein Sprachenpaar Französisch-Deutsch als nicht unterstütztes Sprachenpaar betrachtet, obwohl Französisch-Englisch und Englisch-Deutsch als unterstützte Sprachenpaare gelten.

## <a name="process"></a>Prozess
Nachdem Sie Modelle aus dem Hub migriert haben, die Kandidaten für die Bereitstellung sind, können Sie diese finden, indem Sie zur Seite **Einstellungen** für Ihren Arbeitsbereich wechseln und zum Seitenende scrollen, wo der Abschnitt **Nicht unterstützte Translator-Hub-Trainings** angezeigt wird.  Dieser Abschnitt wird nur angezeigt, wenn Sie über Projekte verfügen, die die oben genannten Voraussetzungen erfüllen.

![Screenshot: Hervorgehobener Abschnitt „Nicht unterstützte Translator-Hub-Trainings“](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Auf der Auswahlseite **Nicht unterstützte Translator-Hub-Trainings** enthält die Registerkarte für **nicht angeforderte Trainings** die für die Bereitstellung geeigneten Modelle.  Wählen Sie die Modelle aus, die Sie bereitstellen möchten, und übermitteln Sie eine Anforderung.   Vor Ablauf der Bereitstellungsfrist am 30. April können Sie beliebig viele Modelle für die Bereitstellung auswählen.
 
![Screenshot: Registerkarte für nicht angeforderte Trainings](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Nach der Übermittlung ist das Modell nicht mehr auf der Registerkarte für **nicht angeforderte Trainings** verfügbar, sondern wird auf der Registerkarte für **angeforderte Trainings** angezeigt.  Sie können Ihre angeforderten Trainings jederzeit anzeigen.

![So führen Sie die Migration aus dem Hub durch](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Wie geht es weiter?

Die von Ihnen für die Bereitstellung ausgewählten Modelle werden gespeichert, sobald der Hub außer Betrieb genommen wird und die Bereitstellung aller Modelle aufgehoben wird.  Sie haben bis zum 24. Mai Zeit, um Anforderungen für die Bereitstellung von nicht unterstützten Modellen zu übermitteln.  Wir werden diese Modelle am 15. Juni bereitstellen. Ab diesem Zeitpunkt werden sie über die Translator V3-API zugänglich sein.  Darüber hinaus werden sie bis zum 1. Juli über die V2-API verfügbar sein.  

Weitere Informationen zu wichtigen Terminen zur Einstellung des Hubs finden Sie [hier](https://www.microsoft.com/translator/business/hub/).
Nach der Bereitstellung fallen normale Hostinggebühren an.  Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).  

Im Gegensatz zu den Standardmodellen des benutzerdefinierten Translators werden Hubmodelle nur in einer einzelnen Region verfügbar sein, sodass keine Hostinggebühren für mehrere Regionen anfallen.  Nach der Bereitstellung können Sie für Ihr Hubmodell die Bereitstellung jederzeit über das migrierte Projekt für den benutzerdefinierten Translator wieder aufheben.

## <a name="next-steps"></a>Nächste Schritte

- [Trainieren eines Modells](how-to-train-model.md).
- Beginnen Sie mit der Verwendung Ihres bereitstellten benutzerdefinierten Übersetzungsmodells über die [Microsoft-Textübersetzungs-API (V3)](../reference/v3-0-translate.md?tabs=curl).