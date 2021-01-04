---
title: 'Verhindern der Übersetzung von Inhalten: Translator'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie mit Translator die Übersetzung von Inhalten verhindern. Mit Translator können Sie Inhalte kennzeichnen, die nicht übersetzt werden sollen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: bf8923c1090669caa46ef51a26418933b1cda023
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563431"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Verhindern der Übersetzung von Inhalten mit Translator

Mit Translator können Sie Inhalte kennzeichnen, die nicht übersetzt werden sollen. Sie können beispielsweise Code, einen Markennamen oder einzelne Wörter/Ausdrücke markieren, deren Übersetzung nicht sinnvoll ist.

## <a name="methods-for-preventing-translation"></a>Methoden zum Verhindern der Übersetzung

1. Kennzeichnen Sie Ihren Inhalt mit `notranslate`. Dies funktioniert prinzipbedingt nur, wenn der Eingabetexttyp auf HTML festgelegt ist.

   Beispiel:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Kennzeichnen Sie Ihren Inhalt mit `translate="no"`. Dies funktioniert nur, wenn textType für die Eingabe auf HTML festgelegt ist.

   Beispiel:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. Verwenden Sie das [dynamische Wörterbuch](dynamic-dictionary.md), um eine bestimmte Übersetzung vorzuschreiben.

4. Übergeben Sie die Zeichenfolge nicht zur Übersetzung an Translator.

5. Benutzerdefinierter Translator: Verwenden Sie ein [Wörterbuch in „Benutzerdefinierter Translator“](custom-translator/what-is-dictionary.md), um die Übersetzung eines Ausdrucks mit einer Wahrscheinlichkeit von 100% vorzuschreiben.


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Translator 3.0: Translate](reference/v3-0-translate.md)
