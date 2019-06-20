---
title: Ausdrücke und Funktionen in Azure Data Factory | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zu Ausdrücken und Funktionen, die Sie bei der Erstellung der Data Factory-Entitäten verwenden können.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 4c51974498539a0305312d6501bcfa9ebc3b2e88
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573555"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Ausdrücke und Funktionen in Azure Data Factory | Microsoft-Dokumentation
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [Aktuelle Version](control-flow-expression-language-functions.md)

Dieser Artikel enthält Informationen zu von Azure Data Factory unterstützten Ausdrücken und Funktionen. 

## <a name="introduction"></a>Einführung
Bei den JSON-Werten in der Definition kann es sich um Literalwerte oder um Ausdrücke handeln, die zur Laufzeit ausgewertet werden. Beispiel:  
  
```json
"name": "value"
```

 (oder)  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Ausdrücke  
Ausdrücke können an beliebiger Stelle in einem JSON-Zeichenfolgenwert verwendet werden und ergeben immer einen anderen JSON-Wert. Wenn ein JSON-Wert ein Ausdruck ist, wird der Text des Ausdrucks durch Entfernen des \@-Zeichens extrahiert. Falls Sie ein Zeichenfolgenliteral benötigen, das mit \@ beginnt, muss es wie folgt mit einem Escapezeichen versehen werden: \@\@. Die folgenden Beispiele veranschaulichen die Auswertung von Ausdrücken.  
  
|JSON-Wert|Ergebnis|  
|----------------|------------|  
|"parameters"|Die Zeichenfolge „parameters“ wird zurückgegeben.|  
|"parameters[1]"|Die Zeichenfolge „parameters[1]“ wird zurückgegeben.|  
|„\@\@“|Eine Zeichenfolge, die „\@“ enthält, wird zurückgegeben (ein Zeichen).|  
|" \@"|Eine Zeichenfolge, die „\@“ enthält, wird zurückgegeben (zwei Zeichen).|  
  
 Mit der *Zeichenfolgeninterpolation* können Ausdrücke auch innerhalb von Zeichenfolgen verwendet werden. Dabei werden die Ausdrücke in `@{ ... }` eingeschlossen. Beispiel: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Bei der Verwendung einer Zeichenfolgeninterpolation ist das Ergebnis immer eine Zeichenfolge. Angenommen, ich habe `myNumber` als `42` und `myString` als `foo` definiert:  
  
|JSON-Wert|Ergebnis|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Gibt `foo` als Zeichenfolge zurück.|  
|"\@{pipeline().parameters.myString}"| Gibt `foo` als Zeichenfolge zurück.|  
|"\@pipeline().parameters.myNumber"| Gibt `42` als *Zahl* zurück.|  
|"\@{pipeline().parameters.myNumber}"| Gibt `42` als *Zeichenfolge* zurück.|  
|"Answer is: @{pipeline().parameters.myNumber}"| Gibt die Zeichenfolge `Answer is: 42` zurück.|  
|"\@concat('Answer is: ', string(pipeline().parameters.myNumber))"| Gibt die Zeichenfolge `Answer is: 42` zurück.|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| Gibt die Zeichenfolge `Answer is: @{pipeline().parameters.myNumber}` zurück.|  
  
### <a name="examples"></a>Beispiele

#### <a name="a-dataset-with-a-parameter"></a>Ein Dataset mit einem Parameter
Im folgenden Beispiel nutzt „BlobDataset“ einen Parameter namens **path**. Der Wert wird unter Verwendung des folgenden Ausdrucks zum Festlegen eines Werts für die Eigenschaft **folderPath** verwendet: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

#### <a name="a-pipeline-with-a-parameter"></a>Eine Pipeline mit einem Parameter
Im folgenden Beispiel nutzt die Pipeline die Parameter **inputPath** und **outputPath**. Der Parameter **path** für das parametrisierte Blobdataset wird mithilfe der Werte dieser Parameter festgelegt. Die hier verwendete Syntax lautet `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
  
## <a name="functions"></a>Functions  
 Sie können Funktionen innerhalb von Ausdrücken aufrufen. Die folgenden Abschnitte enthalten Informationen zu den Funktionen, die in einem Ausdruck verwendet werden können.  

## <a name="string-functions"></a>Zeichenfolgenfunktionen  
 Die folgenden Funktionen gelten nur für Zeichenfolgen. Für Zeichenfolgen können auch einige Auflistungsfunktionen verwendet werden.  
  
|Funktionsname|BESCHREIBUNG|  
|-------------------|-----------------|  
|concat|Kombiniert eine beliebige Anzahl von Zeichenfolgen. Wenn parameter1 z.B. `foo,` lautet, würde der folgende Ausdruck `somevalue-foo-somevalue` zurückgeben :`concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Parameternummer:** 1 ... *n*<br /><br /> **Name**: Zeichenfolge *n*<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolgen, die zu einer einzelnen Zeichenfolge zusammengefasst werden sollen.|  
|substring|Gibt eine Teilmenge der Zeichen aus einer Zeichenfolge zurück. Der folgende Ausdruck:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> gibt beispielsweise Folgendes zurück:<br /><br /> `foo`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, aus der die Teilzeichenfolge abgerufen wird.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Startindex<br /><br /> **Beschreibung:** Erforderlich. Der Index für den Beginn der Teilzeichenfolge im Parameter 1.<br /><br /> **Parameternummer:** 3<br /><br /> **Name**: Länge<br /><br /> **Beschreibung:** Erforderlich. Die Länge der Teilzeichenfolge.|  
|replace|Ersetzt eine Zeichenfolge durch eine angegebene Zeichenfolge. Der Ausdruck:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> gibt beispielsweise Folgendes zurück:<br /><br /> `the new string`<br /><br /> **Parameternummer:** 1<br /><br /> **Name:** Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich.  Die Zeichenfolge, die nach dem Parameter 2 durchsucht und mit dem Parameter 3 aktualisiert wird, wenn der Parameter 2 im Parameter 1 gefunden wird.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Alte Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, die durch den Parameter 3 ersetzt werden soll, wenn im Parameter 1 eine Übereinstimmung gefunden wird.<br /><br /> **Parameternummer:** 3<br /><br /> **Name**: Neue Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, mit der die Zeichenfolge im Parameter 2 ersetzt werden soll, wenn im Parameter 1 eine Übereinstimmung gefunden wird.|  
|GUID| Generiert eine global eindeutige Zeichenfolge (GUID). Beispielsweise könnte die folgende Ausgabe generiert werden `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Format<br /><br /> **Beschreibung:** Optional. Ein einzelner Formatbezeichner, der angibt, [wie der Wert dieser GUID formatiert werden soll](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Mögliche Formatparameter sind „N“, „D“, „B“, „P“ und „X“. Sollte kein Format angegeben werden, wird „D“ verwendet.|  
|toLower|Konvertiert eine Zeichenfolge in Kleinbuchstaben. Das folgende Beispiel gibt `two by two is four` zurück: `toLower('Two by Two is Four')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, die in Kleinbuchstaben konvertiert werden soll. Sollte für ein Zeichen in der Zeichenfolge keine klein geschriebene Variante verfügbar sein, wird das Zeichen unverändert in die zurückgegebene Zeichenfolge eingefügt.|  
|toUpper|Konvertiert eine Zeichenfolge in Großbuchstaben. Der folgende Ausdruck gibt beispielsweise `TWO BY TWO IS FOUR` zurück: `toUpper('Two by Two is Four')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, die in Großbuchstaben konvertiert werden soll. Sollte für ein Zeichen in der Zeichenfolge keine groß geschriebene Variante verfügbar sein, wird das Zeichen unverändert in die zurückgegebene Zeichenfolge eingefügt.|  
|indexof|Sucht den Index eines Werts innerhalb einer Zeichenfolge ohne Berücksichtigung der Groß-/Kleinschreibung. Der folgende Ausdruck gibt beispielsweise `7` zurück: `indexof('hello, world.', 'world')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, die möglicherweise den Wert enthält.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Der Wert, für den der Index ermittelt werden soll.|  
|lastindexof|Sucht den letzten Index eines Werts innerhalb einer Zeichenfolge ohne Berücksichtigung der Groß-/Kleinschreibung. Der folgende Ausdruck gibt beispielsweise `3` zurück: `lastindexof('foofoo', 'foo')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, die möglicherweise den Wert enthält.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Der Wert, für den der Index ermittelt werden soll.|  
|startswith|Überprüft, ob die Zeichenfolge mit einem Wert beginnt (ohne Berücksichtigung der Groß-/Kleinschreibung). Der folgende Ausdruck gibt beispielsweise `true` zurück: `startswith('hello, world', 'hello')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, die möglicherweise den Wert enthält.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Der Wert, mit dem die Zeichenfolge möglicherweise beginnt.|  
|endswith|Überprüft, ob die Zeichenfolge mit einem Wert endet (ohne Berücksichtigung der Groß-/Kleinschreibung). Der folgende Ausdruck gibt beispielsweise `true` zurück: `endswith('hello, world', 'world')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, die möglicherweise den Wert enthält.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Der Wert, mit dem die Zeichenfolge möglicherweise endet.|  
|split|Teilt die Zeichenfolge mithilfe eines Trennzeichens auf. Der folgende Ausdruck gibt beispielsweise `["a", "b", "c"]` zurück: `split('a;b;c',';')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die aufzuteilende Zeichenfolge.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Das Trennzeichen.|  
  
  
## <a name="collection-functions"></a>Auflistungsfunktionen  
 Diese Funktionen werden für Auflistungen wie Arrays, Zeichenfolgen und manchmal für Wörterbücher ausgeführt.  
  
|Funktionsname|BESCHREIBUNG|  
|-------------------|-----------------|  
|contains|Gibt „true“ zurück, wenn das Wörterbuch einen Schlüssel, die Liste einen Wert oder die Zeichenfolge eine Teilzeichenfolge enthält. Der folgende Ausdruck gibt beispielsweise `true:``contains('abacaba','aca')` zurück<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Auflistung<br /><br /> **Beschreibung:** Erforderlich. Die Auflistung, in der gesucht werden soll.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Objekt suchen<br /><br /> **Beschreibung:** Erforderlich. Das Objekt, das innerhalb der **Auflistung** gesucht werden soll.|  
|length|Gibt die Anzahl von Elementen in einem Array oder in einer Zeichenfolge zurück. Der folgende Ausdruck gibt beispielsweise `3` zurück: `length('abc')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Sammlung<br /><br /> **Beschreibung:** Erforderlich. Die Auflistung, deren Länge abgerufen werden soll.|  
|empty|Gibt „true“ zurück, wenn das Objekt, das Array oder die Zeichenfolge leer ist. Der folgende Ausdruck gibt beispielsweise `true` zurück:<br /><br /> `empty('')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Sammlung<br /><br /> **Beschreibung:** Erforderlich. Die Auflistung, für die geprüft werden soll, ob sie leer ist.|  
|Schnittmenge|Gibt ein einzelnes Array oder Objekt zurück, für das gemeinsame Elemente zwischen Arrays oder Objekten übergeben wurden. Die folgende Funktion gibt beispielsweise `[1, 2]` zurück:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Bei den Parametern für die Funktion kann es sich entweder um eine Gruppe von Objekten oder um eine Gruppe von Arrays (aber nicht um eine Kombination aus beidem) handeln. Sollten zwei Objekte mit dem gleichen Namen vorhanden sein, wird jeweils das letzte Objekt in das endgültige Objekt aufgenommen.<br /><br /> **Parameternummer:** 1 ... *n*<br /><br /> **Name**: Sammlung *n*<br /><br /> **Beschreibung:** Erforderlich. Die auszuwertenden Auflistungen. Ein Objekt muss in allen übergebenen Auflistungen enthalten sein, um in das Ergebnis aufgenommen zu werden.|  
|union|Gibt ein einzelnes Array oder Objekt mit allen Elementen zurück, die sich im übergebenen Array oder Objekt befinden. Die folgende Funktion gibt beispielsweise `[1, 2, 3, 10, 101]:` zurück<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Bei den Parametern für die Funktion kann es sich entweder um eine Gruppe von Objekten oder um eine Gruppe von Arrays (aber nicht um eine Kombination aus beidem) handeln. Sollten in der endgültigen Ausgabe zwei Objekte mit dem gleichen Namen vorhanden sein, wird jeweils das letzte Objekt in das endgültige Objekt aufgenommen.<br /><br /> **Parameternummer:** 1 ... *n*<br /><br /> **Name**: Sammlung *n*<br /><br /> **Beschreibung:** Erforderlich. Die auszuwertenden Auflistungen. Ein Objekt, das in einer der Sammlungen enthalten ist, ist auch im Ergebnis enthalten.|  
|first|Gibt das erste Element des übergebenen Arrays oder der übergebenen Zeichenfolge zurück. Die folgende Funktion gibt beispielsweise `0` zurück:<br /><br /> `first([0,2,3])`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Sammlung<br /><br /> **Beschreibung:** Erforderlich. Die Auflistung, aus der das erste Objekt abgerufen werden soll.|  
|last|Gibt das letzte Element des übergebenen Arrays oder der übergebenen Zeichenfolge zurück. Die folgende Funktion gibt beispielsweise `3` zurück:<br /><br /> `last('0123')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Sammlung<br /><br /> **Beschreibung:** Erforderlich. Die Auflistung, aus der das letzte Objekt abgerufen werden soll.|  
|take|Gibt die ersten **Auflistung**-Elemente aus dem übergebenen Array oder aus der übergebenen Zeichenfolge zurück. Diese Funktion gibt beispielsweise `[1, 2]`zurück: `take([1, 2, 3, 4], 2)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Sammlung<br /><br /> **Beschreibung:** Erforderlich. Die Auflistung, aus der die ersten **Auflistung**-Objekte genommen werden sollen.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Count<br /><br /> **Beschreibung:** Erforderlich. Die Anzahl von Objekten, die aus der **Auflistung** abgerufen werden sollen. Der Wert muss eine positive ganze Zahl sein.|  
|skip|Gibt die Elemente im Array ab dem Index **Auflistung** zurück. Diese Funktion gibt beispielsweise `[3, 4]` zurück:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Sammlung<br /><br /> **Beschreibung:** Erforderlich. Die Auflistung, in der die ersten **Anzahl** Objekte übersprungen werden sollen.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Count<br /><br /> **Beschreibung:** Erforderlich. Die Anzahl von Objekten, die am Beginn der **Auflistung** entfernt werden sollen. Der Wert muss eine positive ganze Zahl sein.|  
  
## <a name="logical-functions"></a>Logische Funktionen  
 Diese Funktionen können innerhalb von Bedingungen zur Auswertung einer beliebigen Art von Logik verwendet werden.  
  
|Funktionsname|BESCHREIBUNG|  
|-------------------|-----------------|  
|equals|Gibt „true“ zurück, wenn zwei Werte gleich sind. Wenn parameter1 z.B. „foo“ lautet, würde der folgende Ausdruck `true` zurückgeben :`equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Objekt 1<br /><br /> **Beschreibung:** Erforderlich. Das Objekt, das mit **Objekt 2** verglichen werden soll.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Objekt 2<br /><br /> **Beschreibung:** Erforderlich. Das Objekt, das mit **Objekt 1** verglichen werden soll.|  
|less|Gibt „true“ zurück, wenn das erste Argument kleiner als das zweite Argument ist. Beachten Sie, dass nur Werte vom Typ „integer“, „float“ oder „string“ verwendet werden können. Der folgende Ausdruck gibt beispielsweise `true` zurück: `less(10,100)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Objekt 1<br /><br /> **Beschreibung:** Erforderlich. Das Objekt, für das geprüft werden soll, ob es kleiner als **Objekt 2** ist.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Objekt 2<br /><br /> **Beschreibung:** Erforderlich. Das Objekt, für das geprüft werden soll, ob es größer als **Objekt 1** ist.|  
|lessOrEquals|Gibt „true“ zurück, wenn das erste Argument im Vergleich zum zweiten Argument kleiner oder gleich groß ist. Beachten Sie, dass nur Werte vom Typ „integer“, „float“ oder „string“ verwendet werden können. Der folgende Ausdruck gibt beispielsweise `true` zurück: `lessOrEquals(10,10)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Objekt 1<br /><br /> **Beschreibung:** Erforderlich. Das Objekt, für das geprüft werden soll, ob es im Vergleich zu **Objekt 2** kleiner oder gleich groß ist.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Objekt 2<br /><br /> **Beschreibung:** Erforderlich. Das Objekt, für das geprüft werden soll, ob es im Vergleich zu **Objekt 1** größer oder gleich groß ist.|  
|greater|Gibt „true“ zurück, wenn das erste Argument größer als das zweite Argument ist. Beachten Sie, dass nur Werte vom Typ „integer“, „float“ oder „string“ verwendet werden können. Der folgende Ausdruck gibt beispielsweise `false` zurück: `greater(10,10)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Objekt 1<br /><br /> **Beschreibung:** Erforderlich. Das Objekt, für das geprüft werden soll, ob es größer als **Objekt 2** ist.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Objekt 2<br /><br /> **Beschreibung:** Erforderlich. Das Objekt, für das geprüft werden soll, ob es kleiner als **Objekt 1** ist.|  
|greaterOrEquals|Gibt „true“ zurück, wenn das erste Argument im Vergleich zum zweiten Argument größer oder gleich groß ist. Beachten Sie, dass nur Werte vom Typ „integer“, „float“ oder „string“ verwendet werden können. Der folgende Ausdruck gibt beispielsweise `false` zurück: `greaterOrEquals(10,100)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Objekt 1<br /><br /> **Beschreibung:** Erforderlich. Das Objekt, für das geprüft werden soll, ob es im Vergleich zu **Objekt 2** größer oder gleich groß ist.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Objekt 2<br /><br /> **Beschreibung:** Erforderlich. Das Objekt, für das geprüft werden soll, ob es im Vergleich zu **Objekt 1** kleiner oder gleich groß ist.|  
|and|Gibt TRUE zurück, wenn beide Parameter TRUE sind. Bei beiden Argumenten muss es sich um boolesche Werte handeln. Das Folgende gibt `false` zurück: `and(greater(1,10),equals(0,0))`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Boolescher Wert 1<br /><br /> **Beschreibung:** Erforderlich. Das erste Argument, das `true` sein muss.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Boolescher Wert 2<br /><br /> **Beschreibung:** Erforderlich. Das zweite Argument, das `true` sein muss.|  
|oder|Gibt TRUE zurück, wenn einer der Parameter TRUE ist. Bei beiden Argumenten muss es sich um boolesche Werte handeln. Das Folgende gibt `true` zurück: `or(greater(1,10),equals(0,0))`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Boolescher Wert 1<br /><br /> **Beschreibung:** Erforderlich. Das erste Argument, das möglicherweise `true` ist.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Boolescher Wert 2<br /><br /> **Beschreibung:** Erforderlich. Das zweite Argument, das möglicherweise `true` ist.|  
|not|Gibt TRUE zurück, wenn der Parameter `false` lautet. Bei beiden Argumenten muss es sich um boolesche Werte handeln. Das Folgende gibt `true` zurück: `not(contains('200 Success','Fail'))`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Boolean<br /><br /> **Beschreibung:** Gibt TRUE zurück, wenn der Parameter `false` lautet. Bei beiden Argumenten muss es sich um boolesche Werte handeln. Das Folgende gibt `true` zurück: `not(contains('200 Success','Fail'))`|  
|if|Macht die Rückgabe eines angegebenen Werts davon abhängig, ob der Ausdruck `true` oder `false` ergeben hat.  Das folgende Beispiel gibt `"yes"` zurück: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Ausdruck<br /><br /> **Beschreibung:** Erforderlich. Ein boolescher Wert, der bestimmt, welcher Wert vom Ausdruck zurückgegeben wird.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: True<br /><br /> **Beschreibung:** Erforderlich. Der zurückzugebende Wert, wenn der Ausdruck `true` ist.<br /><br /> **Parameternummer:** 3<br /><br /> **Name**: False<br /><br /> **Beschreibung:** Erforderlich. Der zurückzugebende Wert, wenn der Ausdruck `false` ist.|  
  
## <a name="conversion-functions"></a>Konvertierungsfunktionen  
 Diese Funktionen dienen zum Konvertieren zwischen den einzelnen nativen Typen in der Sprache:  
  
-   Zeichenfolge  
  
-   integer  
  
-   Gleitkommawert  
  
-   boolean  
  
-   Arrays  
  
-   Wörterbücher  
  
|Funktionsname|BESCHREIBUNG|  
|-------------------|-----------------|  
|int|Konvertiert den Parameter in eine ganze Zahl. Der folgende Ausdruck gibt beispielsweise 100 als Zahl (und nicht als Zeichenfolge) zurück: `int('100')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Wert<br /><br /> **Beschreibung:** Erforderlich. Der Wert, der in eine ganze Zahl konvertiert wird.|  
|Zeichenfolge|Konvertiert den Parameter in eine Zeichenfolge. Der folgende Ausdruck gibt beispielsweise `'10'` zurück:  `string(10)` Sie können ein Objekt auch in eine Zeichenfolge konvertieren, wenn etwa der **foo**-Parameter ein Objekt mit der Eigenschaft `bar : baz` ist, dann würde Folgendes zurückgegeben werden: `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Wert<br /><br /> **Beschreibung:** Erforderlich. Der Wert, der in eine Zeichenfolge konvertiert wird.|  
|json|Konvertiert den Parameter in einen JSON-Wert. Ist das Gegenteil von string(). Der folgende Ausdruck gibt beispielsweise `[1,2,3]` als Zahl (und nicht als Zeichenfolge) zurück:<br /><br /> `json('[1,2,3]')`<br /><br /> Analog dazu können Sie eine Zeichenfolge in ein Objekt konvertieren. Beispielsweise gibt `json('{"bar" : "baz"}')` Folgendes zurück:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, die in einen nativen Typwert konvertiert werden soll.<br /><br /> Die JSON-Funktion unterstützt auch XML-Eingaben. So kann beispielsweise der folgende Parameterwert in JSON konvertiert werden:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> wird in das folgende JSON-Format konvertiert werden:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|Gleitkommawert|Konvertiert das Parameterargument in eine Gleitkommazahl. Der folgende Ausdruck gibt beispielsweise `10.333` zurück: `float('10.333')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Wert<br /><br /> **Beschreibung:** Erforderlich. Der Wert, der in eine Gleitkommazahl konvertiert wird.|  
|bool|Konvertiert den Parameter in einen booleschen Wert. Der folgende Ausdruck gibt beispielsweise `false` zurück: `bool(0)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Wert<br /><br /> **Beschreibung:** Erforderlich. Der Wert, der in einen booleschen Wert konvertiert wird.|  
|coalesce|Gibt das erste Objekt aus den übergebenen Argumenten zurück, das nicht NULL ist. Hinweis: Eine leere Zeichenfolge ist nicht NULL. Wenn also beispielsweise die Parameter 1 und 2 nicht definiert sind, gibt die folgende Funktion `fallback` zurück: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Parameternummer:** 1 ... *n*<br /><br /> **Name**: Objekt*n*<br /><br /> **Beschreibung:** Erforderlich. Die Objekte, für die geprüft werden soll, ob sie `null` sind.|  
|base64|Rückkehr zur base64-Darstellung der Eingabezeichenfolge. Der folgende Ausdruck gibt beispielsweise `c29tZSBzdHJpbmc=` zurück: `base64('some string')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge 1<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, die als Base64-Darstellung codiert werden soll.|  
|base64ToBinary|Gibt eine binäre Darstellung einer Base64-codierten Zeichenfolge zurück. Der folgende Ausdruck gibt beispielsweise die binäre Darstellung von einigen Zeichenfolgen wieder: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Base64-codierte Zeichenfolge.|  
|base64ToString|Gibt eine Zeichenfolgendarstellung einer Base64-codierten Zeichenfolge zurück. Der folgende Ausdruck gibt beispielsweise eine Zeichenfolge zurück: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Base64-codierte Zeichenfolge.|  
|Binär|Gibt eine binäre Darstellung eines Werts zurück.  Der folgende Ausdruck gibt beispielsweise die binäre Darstellung von einigen Zeichenfolgen wieder: `binary(‘some string’).`.<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Wert<br /><br /> **Beschreibung:** Erforderlich. Der Wert, der in einen Binärwert konvertiert wird.|  
|dataUriToBinary|Gibt eine binäre Darstellung eines Daten-URI zurück. Der folgende Ausdruck gibt beispielsweise die binäre Darstellung von einigen Zeichenfolgen wieder: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`.<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Der Daten-URI, der in eine binäre Darstellung konvertiert werden soll.|  
|dataUriToString|Gibt eine Zeichenfolgendarstellung eines Daten-URI zurück. Der folgende Ausdruck gibt beispielsweise eine Zeichenfolge zurück: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`.<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br />**Beschreibung:** Erforderlich. Der Daten-URI, der in eine Zeichenfolgendarstellung konvertiert werden soll.|  
|dataUri|Gibt einen Daten-URI eines Werts zurück. Der folgende Ausdruck gibt beispielsweise Daten zurück: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Parameternummer:** 1<br /><br />**Name**: Wert<br /><br />**Beschreibung:** Erforderlich. Der Wert, der in einen Daten-URI konvertiert werden soll.|  
|decodeBase64|Gibt eine Zeichenfolgendarstellung einer Base64-Eingabezeichenfolge zurück. Der folgende Ausdruck gibt beispielsweise `some string` zurück: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Gibt eine Zeichenfolgendarstellung einer Base64-Eingabezeichenfolge zurück.|  
|encodeUriComponent|Versieht die übergebene Zeichenfolge mit URL-Escapezeichen. Der folgende Ausdruck gibt beispielsweise `You+Are%3ACool%2FAwesome` zurück: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, in der für URLs ungeeignete Zeichen mit Escapezeichen versehen werden sollen.|  
|decodeUriComponent|Entfernt URL-Escapezeichen aus der übergebenen Zeichenfolge. Der folgende Ausdruck gibt beispielsweise `You Are:Cool/Awesome` zurück: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Die Zeichenfolge, in der für URLs ungeeignete Zeichen decodiert werden sollen.|  
|decodeDataUri|Gibt eine binäre Darstellung einer Eingabezeichenfolge des Daten-URI zurück. Der folgende Ausdruck gibt beispielsweise die binäre Darstellung von einigen Zeichenfolgen von `some string` wieder: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br /> **Beschreibung:** Erforderlich. Der Daten-URI, der in eine binäre Darstellung konvertiert werden soll.|  
|uriComponent|Gibt eine URI-codierte Darstellung eines Werts zurück. Der folgende Ausdruck gibt beispielsweise `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')` zurück<br /><br /> Parameterdetails: Nummer: 1, Name: Zeichenfolge, Beschreibung: Erforderlich. Die Zeichenfolge, die als URI codiert werden soll.|  
|uriComponentToBinary|Gibt eine binäre Darstellung einer URI-codierten Zeichenfolge zurück. Der folgende Ausdruck gibt beispielsweise die binäre Darstellung von `You Are:Cool/Awesome` wieder: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeichenfolge<br /><br />**Beschreibung:** Erforderlich. Die URI-codierte Zeichenfolge.|  
|uriComponentToString|Gibt eine Zeichenfolgendarstellung einer URI-codierten Zeichenfolge zurück. Der folgende Ausdruck gibt beispielsweise `You Are:Cool/Awesome` zurück: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameternummer:** 1<br /><br />**Name**: Zeichenfolge<br /><br />**Beschreibung:** Erforderlich. Die URI-codierte Zeichenfolge.|  
|Xml|Gibt eine XML-Darstellung des Werts zurück. Der folgende Ausdruck gibt beispielsweise einen XML-Inhalt zurück, der durch `'\<name>Alan\</name>'` dargestellt wird: `xml('\<name>Alan\</name>')`. Die XML-Funktion unterstützt auch JSON-Objekteingaben. Der Parameter `{ "abc": "xyz" }` wird beispielsweise in XML-Inhalt konvertiert:`\<abc>xyz\</abc>`<br /><br /> **Parameternummer:** 1<br /><br />**Name**: Wert<br /><br />**Beschreibung:** Erforderlich. Der Wert, der in XML konvertiert werden soll.|  
|xpath|Gibt ein Array mit XML-Knoten zurück, die dem xpath-Ausdruck eines Werts entsprechen, zu dem der xpath-Ausdruck ausgewertet wird.<br /><br />  **Beispiel 1**<br /><br /> Angenommen, der Wert des Parameters „p1“ ist eine Zeichenfolgendarstellung des folgenden XML-Codes:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Der Code `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> würde Folgendes zurückgeben:<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> während<br /><br /> 2. Der Code `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> würde Folgendes zurückgeben:<br /><br /> `13`<br /><br /> <br /><br /> **Beispiel 2**<br /><br /> Angenommen, der folgende XML-Inhalt wird verwendet:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Der Code `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> oder<br /><br /> 2. Der Code `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> gibt Folgendes zurück:<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> and<br /><br /> 3. Der Code `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> gibt Folgendes zurück:<br /><br /> ``bar``<br /><br /> **Parameternummer:** 1<br /><br />**Name**: Xml<br /><br />**Beschreibung:** Erforderlich. Der XML-Code, für den der XPath-Ausdruck ausgewertet werden soll.<br /><br /> **Parameternummer:** 2<br /><br />**Name**: XPath<br /><br />**Beschreibung:** Erforderlich. Der auszuwertende XPath-Ausdruck.|  
|Array|Konvertiert den Parameter in ein Array.  Der folgende Ausdruck gibt beispielsweise `["abc"]` zurück: `array('abc')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Wert<br /><br /> **Beschreibung:** Erforderlich. Der Wert, der in ein Array konvertiert wird.|
|createArray|Erstellt ein Array auf der Grundlage der Parameter.  Der folgende Ausdruck gibt beispielsweise `["a", "c"]` zurück: `createArray('a', 'c')`<br /><br /> **Parameternummer:** 1 ... n<br /><br /> **Name**: Beliebiges n<br /><br /> **Beschreibung:** Erforderlich. Die Werte, die zu einem Array zusammengefasst werden sollen.|

## <a name="math-functions"></a>Mathematische Funktionen  
 Diese Funktionen können für folgende Arten von Zahlen verwendet werden: **ganze Zahlen** und **Gleitkommazahlen**.  
  
|Funktionsname|BESCHREIBUNG|  
|-------------------|-----------------|  
|Hinzufügen|Gibt das Ergebnis der Addition zweier Zahlen zurück. Die folgende Funktion gibt beispielsweise `20.333` zurück: `add(10,10.333)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Summand 1<br /><br /> **Beschreibung:** Erforderlich. Die Zahl, die zu **Summand 2** addiert werden soll.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Summand 2<br /><br /> **Beschreibung:** Erforderlich. Die Zahl, die zu **Summand 1** addiert werden soll.|  
|sub|Gibt das Ergebnis der Subtraktion zweier Zahlen zurück. Die folgende Funktion gibt beispielsweise `-0.333` zurück:<br /><br /> `sub(10,10.333)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Minuend<br /><br /> **Beschreibung:** Erforderlich. Die Zahl, von der **Subtrahend** abgezogen wird.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Subtrahend<br /><br /> **Beschreibung:** Erforderlich. Die Zahl, die von **Minuend** abgezogen werden soll.|  
|mul|Gibt das Ergebnis der Multiplikation zweier Zahlen zurück. Das folgende Beispiel gibt `103.33` zurück:<br /><br /> `mul(10,10.333)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Multiplikand 1<br /><br /> **Beschreibung:** Erforderlich. Die Zahl, mit der **Multiplikand 2** multipliziert werden soll.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Multiplikand 2<br /><br /> **Beschreibung:** Erforderlich. Die Zahl, mit der **Multiplikand 1** multipliziert werden soll.|  
|div|Gibt das Ergebnis der Division zweier Zahlen zurück. Das folgende Beispiel gibt `1.0333` zurück:<br /><br /> `div(10.333,10)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Dividend<br /><br /> **Beschreibung:** Erforderlich. Die Zahl, die durch den **Divisor** dividiert werden soll.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Divisor<br /><br /> **Beschreibung:** Erforderlich. Die Zahl, durch die der **Dividend** dividiert werden soll.|  
|mod|Gibt das Ergebnis des Rests nach der Division zweier Zahlen zurück (Modulo). Der folgende Ausdruck gibt beispielsweise `2` zurück:<br /><br /> `mod(10,4)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Dividend<br /><br /> **Beschreibung:** Erforderlich. Die Zahl, die durch den **Divisor** dividiert werden soll.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Divisor<br /><br /> **Beschreibung:** Erforderlich. Die Zahl, durch die der **Dividend** dividiert werden soll. Nach der Division wird der Rest verwendet.|  
|Min|Diese Funktion kann auf zwei Arten aufgerufen werden: `min([0,1,2])` Min wird hier ein Array. Dieser Ausdruck gibt `0` zurück. Die Funktion kann aber auch eine kommagetrennte Liste mit Werten verwenden:  `min(0,1,2)` In diesem Fall wird ebenfalls 0 zurückgegeben. Hinweis: Als Werte sind nur Zahlen zulässig. Wenn der Parameter also ein Array ist, darf dieses ausschließlich Zahlen enthalten.<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Auflistung oder Wert<br /><br /> **Beschreibung:** Erforderlich. Entweder ein Array mit Werten, um den kleinsten Wert zu ermitteln, oder der erste Wert einer Gruppe.<br /><br /> **Parameternummer:** 2 ... *n*<br /><br /> **Name**: Wert *n*<br /><br /> **Beschreibung:** Optional. Falls es sich bei dem ersten Parameter um einen Wert handelt, können Sie zusätzliche Werte übergeben. In diesem Fall wird der kleinste aller übergebenen Werte zurückgegeben.|  
|max|Diese Funktion kann auf zwei Arten aufgerufen werden: `max([0,1,2])`<br /><br /> Max wird hier ein Array. Dieser Ausdruck gibt `2` zurück. Die Funktion kann aber auch eine kommagetrennte Liste mit Werten verwenden:  `max(0,1,2)` Die Funktion gibt 2 zurück. Hinweis: Als Werte sind nur Zahlen zulässig. Wenn der Parameter also ein Array ist, darf dieses ausschließlich Zahlen enthalten.<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Auflistung oder Wert<br /><br /> **Beschreibung:** Erforderlich. Entweder ein Array mit Werten, um den größten Wert zu ermitteln, oder der erste Wert einer Gruppe.<br /><br /> **Parameternummer:** 2 ... *n*<br /><br /> **Name**: Wert *n*<br /><br /> **Beschreibung:** Optional. Falls es sich bei dem ersten Parameter um einen Wert handelt, können Sie zusätzliche Werte übergeben. In diesem Fall wird der größte aller übergebenen Werte zurückgegeben.|  
|range| Generiert ein Array von Ganzzahlen, beginnend mit einer bestimmten Anzahl, und Sie definieren die Länge des zurückgegebenen Arrays. Die folgende Funktion gibt beispielsweise `[3,4,5,6]` zurück:<br /><br /> `range(3,4)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Startindex<br /><br /> **Beschreibung:** Erforderlich. Die erste ganze Zahl im Array.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Count<br /><br /> **Beschreibung:** Erforderlich. Die Anzahl von ganzen Zahlen im Array.|  
|rand| Generiert nach dem Zufallsprinzip eine ganze Zahl aus dem angegebenen Bereich (einschließlich Anfang und Ende). Dies könnte z.B. `42` zurückgeben:<br /><br /> `rand(-1000,1000)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Minimum<br /><br /> **Beschreibung:** Erforderlich. Die kleinste ganze Zahl, die zurückgegeben werden kann.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Maximum<br /><br /> **Beschreibung:** Erforderlich. Die größte ganze Zahl, die zurückgegeben werden kann.|  
  
## <a name="date-functions"></a>Datumsfunktionen  
  
|Funktionsname|BESCHREIBUNG|  
|-------------------|-----------------|  
|utcnow|Gibt den aktuellen Zeitstempel als Zeichenfolge zurück. Beispiel: `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Format<br /><br /> **Beschreibung:** Optional. Entweder ein [Formatbezeichner aus einem einzelnen Zeichen](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) oder ein [benutzerdefiniertes Formatmuster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx), mit dem die gewünschte Formatierung für den Wert des Zeitstempels angegeben wird. Ohne Formatangabe wird das ISO 8601-Format („o“) verwendet.|  
|addseconds|Addiert eine ganzzahlige Anzahl von Sekunden zu einem übergebenen Zeichenfolgenzeitstempel. Die Sekundenanzahl kann positiv oder negativ sein. Sofern kein Formatbezeichner angegeben wird, ist das Ergebnis standardmäßig eine Zeichenfolge im ISO 8601-Format („o“). Beispiel: `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeitstempel<br /><br /> **Beschreibung:** Erforderlich. Eine Zeichenfolge mit der Uhrzeit.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Sekunden<br /><br /> **Beschreibung:** Erforderlich. Die Anzahl zu addierender Sekunden. Bei einem negativen Wert werden die Sekunden subtrahiert.<br /><br /> **Parameternummer:** 3<br /><br /> **Name**: Format<br /><br /> **Beschreibung:** Optional. Entweder ein [Formatbezeichner aus einem einzelnen Zeichen](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) oder ein [benutzerdefiniertes Formatmuster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx), mit dem die gewünschte Formatierung für den Wert des Zeitstempels angegeben wird. Ohne Formatangabe wird das ISO 8601-Format („o“) verwendet.|  
|addminutes|Addiert eine ganzzahlige Anzahl von Minuten zu einem übergebenen Zeichenfolgenzeitstempel. Die Minutenanzahl kann positiv oder negativ sein. Sofern kein Formatbezeichner angegeben wird, ist das Ergebnis standardmäßig eine Zeichenfolge im ISO 8601-Format („o“). Beispiel: `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeitstempel<br /><br /> **Beschreibung:** Erforderlich. Eine Zeichenfolge mit der Uhrzeit.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Minuten<br /><br /> **Beschreibung:** Erforderlich. Die Anzahl zu addierender Minuten. Bei einem negativen Wert werden die Minuten subtrahiert.<br /><br /> **Parameternummer:** 3<br /><br /> **Name**: Format<br /><br /> **Beschreibung:** Optional. Entweder ein [Formatbezeichner aus einem einzelnen Zeichen](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) oder ein [benutzerdefiniertes Formatmuster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx), mit dem die gewünschte Formatierung für den Wert des Zeitstempels angegeben wird. Ohne Formatangabe wird das ISO 8601-Format („o“) verwendet.|  
|addhours|Addiert eine ganzzahlige Anzahl von Stunden zu einem übergebenen Zeichenfolgenzeitstempel. Die Stundenanzahl kann positiv oder negativ sein. Sofern kein Formatbezeichner angegeben wird, ist das Ergebnis standardmäßig eine Zeichenfolge im ISO 8601-Format („o“). Beispiel: `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeitstempel<br /><br /> **Beschreibung:** Erforderlich. Eine Zeichenfolge mit der Uhrzeit.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Stunden<br /><br /> **Beschreibung:** Erforderlich. Die Anzahl zu addierender Stunden. Bei einem negativen Wert werden die Stunden subtrahiert.<br /><br /> **Parameternummer:** 3<br /><br /> **Name**: Format<br /><br /> **Beschreibung:** Optional. Entweder ein [Formatbezeichner aus einem einzelnen Zeichen](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) oder ein [benutzerdefiniertes Formatmuster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx), mit dem die gewünschte Formatierung für den Wert des Zeitstempels angegeben wird. Ohne Formatangabe wird das ISO 8601-Format („o“) verwendet.|  
|adddays|Addiert eine ganzzahlige Anzahl von Tagen zu einem übergebenen Zeichenfolgenzeitstempel. Die Tagesanzahl kann positiv oder negativ sein. Sofern kein Formatbezeichner angegeben wird, ist das Ergebnis standardmäßig eine Zeichenfolge im ISO 8601-Format („o“). Beispiel: `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Zeitstempel<br /><br /> **Beschreibung:** Erforderlich. Eine Zeichenfolge mit der Uhrzeit.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Days<br /><br /> **Beschreibung:** Erforderlich. Die Anzahl zu addierender Tage. Bei einem negativen Wert werden die Tage subtrahiert.<br /><br /> **Parameternummer:** 3<br /><br /> **Name**: Format<br /><br /> **Beschreibung:** Optional. Entweder ein [Formatbezeichner aus einem einzelnen Zeichen](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) oder ein [benutzerdefiniertes Formatmuster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx), mit dem die gewünschte Formatierung für den Wert des Zeitstempels angegeben wird. Ohne Formatangabe wird das ISO 8601-Format („o“) verwendet.|  
|formatDateTime|Gibt eine Zeichenfolge im Datumsformat zurück. Sofern kein Formatbezeichner angegeben wird, ist das Ergebnis standardmäßig eine Zeichenfolge im ISO 8601-Format („o“). Beispiel: `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Parameternummer:** 1<br /><br /> **Name**: Datum<br /><br /> **Beschreibung:** Erforderlich. Eine Zeichenfolge mit dem Datum.<br /><br /> **Parameternummer:** 2<br /><br /> **Name**: Format<br /><br /> **Beschreibung:** Optional. Entweder ein [Formatbezeichner aus einem einzelnen Zeichen](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) oder ein [benutzerdefiniertes Formatmuster](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx), mit dem die gewünschte Formatierung für den Wert des Zeitstempels angegeben wird. Ohne Formatangabe wird das ISO 8601-Format („o“) verwendet.|  

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Systemvariablen, die Sie in Ausdrücken verwenden können, finden Sie unter [Systemvariablen](control-flow-system-variables.md).
