---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178761"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Angeben der Strukturdefinition für rechteckige Datasets
Der Abschnitt „structure“ in der JSON von Datasets ist ein **optionaler** Abschnitt für rechteckige Tabellen (mit Zeilen und Spalten) und enthält eine Auflistung der Spalten der Tabelle. Sie verwenden den Abschnitt "structure" entweder zum Angeben von Typinformationen für Typumwandlungen oder für Spaltenzuordnungen. In den folgenden Abschnitten werden diese Features ausführlich beschrieben. 

Jede Spalte enthält die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| name |Name der Spalte. |Ja |
| type |Datentyp der Spalte. Im nachstehenden Abschnitt "Typkonvertierungen" finden Sie Details zur Angabe von Typinformationen. |Nein |
| culture |Zu verwendendes .NET-basiertes Gebietsschema, wenn "type" angegeben ist und den .NET-Typ "Datetime" oder "Datetimeoffset" hat. Die Standardeinstellung ist "en-us". |Nein |
| format |Zu verwendende Formatzeichenfolge, wenn "type" angegeben ist und den .NET-Typ "Datetime" oder "Datetimeoffset" hat. |Nein |

Das folgende Beispiel zeigt den Abschnitt "structure" der JSON für eine Tabelle mit den drei Spalten "userid", "name" und "lastlogindate".

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Befolgen Sie die folgenden Angaben dazu, wann der Abschnitt **structure** mit welchen Informationen verwendet werden sollte.

* **Für strukturierte Datenquellen**, die Datenschema- und Typinformationen neben den Daten selbst speichern (wie etwa SQL Server, Oracle, Azure-Tabelle usw.), sollten Sie den Abschnitt „structure“ nur angeben, wenn bestimmte Quellspalten bestimmten Zielspalten in der Senke zugewiesen werden sollen und deren Namen nicht identisch sind (Einzelheiten finden Sie im nachfolgenden Abschnitt zur Spaltenzuordnung). 
  
    Wie bereits erwähnt, ist die Information "type" im Abschnitt "structure" optional. Für strukturierte Datenquellen steht die Information "type" bereits als Teil der Datasetdefinition im Datenspeicher zur Verfügung, weshalb Sie Typinformationen nicht dem Abschnitt "structure" hinzufügen müssen.
* **Für das Schema von Lesedatenquellen (insbesondere Azure-Blob)** können Sie Daten speichern, ohne Schema- oder Typinformationen mit den Daten zu speichern. Bei diesen Typen von Datenquellen sollten Sie den Abschnitt "structure" in den beiden folgenden Fällen hinzufügen:
  * Sie wünschen eine Spaltenzuordnung.
  * Wenn das Dataset eine Quelle in einer Kopieraktivität ist, können Sie Typinformationen in "structure" bereitstellen. Data Factory verwendet diese Typinformationen für die Konvertierung in systemeigene Typen für die Senke. Weitere Informationen finden Sie im Artikel [Verschieben von Daten in einen und aus einem Azure-Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md).

### <a name="supported-net-based-types"></a>Unterstützte .NET-basierte Typen
Data Factory unterstützt die folgenden CLS-konformen auf .NET basierenden Typwerte für die Bereitstellung von Typinformationen in "structure" für das Schema von Lesedatenquellen wie Azure-Blob.

* Int16
* Int32 
* Int64
* Single
* Double
* Decimal
* Byte[]
* Bool
* string 
* Guid
* Datetime
* Datetimeoffset
* Timespan 

Für "Datetime" und "Datetimeoffset" können Sie optional auch die Zeichenfolgen "culture" und "format" angeben, um das Analysieren Ihrer benutzerdefinierten "Datetime"-Zeichenfolge zu erleichtern. Nachstehend sehen Sie ein Beispiel einer Typumwandlung.

