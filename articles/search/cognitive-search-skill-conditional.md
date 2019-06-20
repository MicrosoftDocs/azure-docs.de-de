---
title: Die Qualifikation „Bedingt“ der kognitiven Suche (Azure Search) | Microsoft-Dokumentation
description: Die Funktion „Bedingt“ ermöglicht das Filtern, das Erstellen von Standardwerten und das Zusammenführen von Werten.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791518"
---
#   <a name="conditional-skill"></a>Qualifikation „Bedingt“

Die *Funktion „Bedingt“* ermöglicht Azure Search-Szenarios, die eine boolesche Operation erfordern, um zu bestimmen, welche Daten einer Ausgabe zugewiesen werden. Zu diesen Szenarios gehören die Filterung, die Zuweisung eines Standardwerts und das Zusammenführung von Daten auf der Grundlage einer Bedingung.

Der folgende Pseudocode veranschaulicht, wozu die Funktion „Bedingt“ dient:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Diese Funktion ist nicht an eine Azure Cognitive Services-API gebunden, und es fallen keine Kosten für die Nutzung an. Es wird jedoch empfohlen, dennoch eine [Cognitive Services-Ressource anzufügen](cognitive-search-attach-cognitive-services.md), um die Ressourcenoption „Free“ außer Kraft zu setzen, durch die Sie auf eine geringe Anzahl von Anreicherungen pro Tag beschränkt werden.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Ausgewertete Felder

Diese Qualifikation stellt einen Sonderfall dar, da ihre Eingaben ausgewertete Felder sind.

Die folgenden Elemente sind gültige Werte eines Ausdrucks:

-   Anmerkungspfade (Pfade in Ausdrücken müssen mit „$(“ und „)“ abgegrenzt werden.)
 <br/>
    Beispiele:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literale (Zeichenfolgen, Zahlen, TRUE, FALSE, NULL) <br/>
    Beispiele:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Ausdrücke, die Vergleichsoperatoren verwenden (==, !=, >=, >, <=, <) <br/>
    Beispiele:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Ausdrücke, die boolesche Operatoren verwenden (&&, ||, !, ^) <br/>
    Beispiele:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Ausdrücke, die numerische Operatoren verwenden (+, -, \*, /, %) <br/>
    Beispiele: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Da die Funktion „Bedingt“ die Auswertung unterstützt, können Sie diese in kleineren Transformationsszenarios verwenden. Ein Beispiel hierfür finden Sie in der [Funktionsdefinition 4](#transformation-example).

## <a name="skill-inputs"></a>Skilleingaben
Bei Eingaben muss die Groß-/Kleinschreibung beachtet werden.

| Eingabe   | BESCHREIBUNG |
|-------------|-------------|
| condition   | Diese Eingabe ist ein [ausgewertetes Feld](#evaluated-fields), das die auszuwertende Bedingung darstellt. Diese Bedingung sollte in einen booleschen Wert ausgewertet werden (*true* oder *false*).   <br/>  Beispiele: <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Diese Eingabe ist ein [ausgewertetes Feld](#evaluated-fields), das den zurückzugebenden Wert darstellt, wenn die Bedingung mit *true* ausgewertet wird. Konstante Zeichenfolgen sollten in einfachen Anführungszeichen (' ') zurückgegeben werden. <br/>Beispielwerte: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | Diese Eingabe ist ein [ausgewertetes Feld](#evaluated-fields), das den zurückzugebenden Wert darstellt, wenn die Bedingung mit *false* ausgewertet wird. <br/>Beispielwerte: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>Skillausgaben
Es gibt eine einzelne Ausgabe, die einfach „Ausgabe“ genannt wird. Diese gibt entweder den Wert von *whenFalse* zurück, wenn die Bedingung „false“ ist, oder sie gibt *whenTrue* zurück, wenn die Bedingung „true“ ist.

## <a name="examples"></a>Beispiele

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Beispieldefinition 1 einer Qualifikation: Filtern von Dokumenten, um nur französische Dokumente zurückzugeben

Die folgende Ausgabe gibt ein Array von Sätzen („/document/frenchSentences“) zurück, wenn die Sprache des Dokuments Französisch ist. Wenn die Sprache nicht Französisch ist, wird der Wert auf *null* festgelegt.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Wenn „/document/frenchSentences“ als *Kontext* einer anderen Funktion verwendet wird, wird diese nur ausgeführt, wenn „/document/frenchSentences“ nicht auf *null* festgelegt ist.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Beispieldefinition 2 einer Qualifikation: Festlegen eines Standardwerts für einen nicht vorhandenen Wert

Die folgende Ausgabe erstellt eine Anmerkung („/document/languageWithDefault“), die entweder auf die Sprache des Dokuments oder auf „es“ bei nicht festgelegter Sprache festgelegt wird.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Beispieldefinition 3 einer Qualifikation: Zusammenführen von Werten aus zwei Feldern in ein Feld

In diesem Beispiel haben einige Sätze eine *frenchSentiment*-Eigenschaft. Wenn die *frenchSentiment*-Eigenschaft „null“ ist, soll der Wert *englishSentiment* verwendet werden. Wir weisen die Ausgabe einem Member mit der Bezeichnung *sentiment* zu („/document/sentiment/*/sentiment“).

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Beispiel einer Transformation
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Beispieldefinition 4 einer Qualifikation: Datentransformation auf ein einzelnes Feld

In diesem Beispiel erhalten wir eine *Stimmung*, die zwischen 0 und 1 liegt. Nach der Transformation soll die Stimmung zwischen –1 und 1 liegen. Die Funktion „Bedingt“ kann für diese geringfügige Transformation verwendet werden.

In diesem Beispiel verwenden wir nicht den bedingten Aspekt der Funktion, da die Bedingung immer *true* ist.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Besondere Überlegungen
Einige Parameter werden ausgewertet. Daher müssen Sie das dokumentierte Muster besonders sorgfältig befolgen. Ausdrücke müssen mit einem Gleichheitszeichen („=“) beginnen. Ein Pfad muss durch „$(“ und „)“ begrenzt sein. Stellen Sie sicher, dass die Zeichenfolgen in einfache Anführungszeichen eingeschlossen werden (" und "). Dies hilft bei der Auswertung, zwischen Zeichenfolgen und tatsächlichen Pfaden und Operatoren zu unterscheiden. Stellen Sie auch sicher, dass Sie vor und nach Operatoren Leerzeichen verwenden (z. B. bedeutet ein „*“ in einem Pfad etwas anderes als Multiplizieren).


## <a name="next-steps"></a>Nächste Schritte

+ [Vordefinierte Skills](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
