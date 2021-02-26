---
title: Data Wrangling-Funktionen in Azure Data Factory
description: Übersicht über die verfügbaren Data Wrangling-Funktionen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 659f6527d43e1b45a11fddf774050ca6d42bfe12
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896662"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Transformationsfunktionen in Power Query für Data Wrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Data Wrangling in Azure Data Factory ermöglicht codefreie agile Datenaufbereitung und Data Wrangling für die Cloud durch Übersetzen von Power Query ```M```-Skripts in Datenflussskripts. ADF wird in [Power Query Online](/powerquery-m/power-query-m-reference) integriert und stellt Power Query ```M```-Funktionen für Data Wrangling über die Spark-Ausführung mithilfe der Datenfluss-Spark-Infrastruktur bereit. 

> [!NOTE]
> Power Query in ADF ist zurzeit in der öffentlichen Vorschau verfügbar.

Derzeit werden nicht alle Power Query M-Funktionen für Data Wrangling unterstützt, obwohl sie während der Erstellung verfügbar sind. Beim Erstellen Ihrer Mash-Ups wird die folgende Fehlermeldung angezeigt, wenn eine Funktion nicht unterstützt wird:

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

Die unterstützten Power Query M-Funktionen sind im Folgenden aufgeführt.

## <a name="column-management"></a>Spaltenverwaltung

* Auswahl: [Table.SelectColumns](/powerquery-m/table-selectcolumns)
* Entfernung: [Table.RemoveColumns](/powerquery-m/table-removecolumns)
* Umbenennung: [Table.RenameColumns](/powerquery-m/table-renamecolumns), [Table.PrefixColumns](/powerquery-m/table-prefixcolumns), [Table.TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Neuanordnung: [Table.ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Zeilenfilterung

Verwenden Sie die M-Funktion [Table.SelectRows](/powerquery-m/table-selectrows), um Zeilen nach den folgenden Bedingungen zu filtern:

* Gleichheit und Ungleichheit
* Vergleiche von Zahlen, Text und Datumsangaben (jedoch nicht von Datum/Uhrzeit)
* Zahlenangaben, z. B. [Number.IsEven](/powerquery-m/number-iseven)/[Odd](/powerquery-m/number-iseven)
* Texteingrenzung unter Verwendung von [Text.Contains](/powerquery-m/text-contains), [Text.StartsWith](/powerquery-m/text-startswith) oder [Text.EndsWith](/powerquery-m/text-endswith)
* Datumsbereiche einschließlich aller [Datumsfunktionen](/powerquery-m/date-functions) mit „IsIn“) 
* Kombinationen dieser Bedingungen unter Verwendung der Bedingungen „and“, „or“ oder „not“

## <a name="adding-and-transforming-columns"></a>Hinzufügen und Transformieren von Spalten

Spalten lassen sich mit den folgenden M-Funktionen hinzufügen oder transformieren: [Table.AddColumn](/powerquery-m/table-addcolumn), [Table.TransformColumns](/powerquery-m/table-transformcolumns), [Table.ReplaceValue](/powerquery-m/table-replacevalue), [Table.DuplicateColumn](/powerquery-m/table-duplicatecolumn). Nachfolgend sind die unterstützten Transformationsfunktionen aufgeführt.

* Numerische Arithmetik
* Textverkettung
* Arithmetik für Datum und Uhrzeit (arithmetische Operatoren [Date.AddDays](/powerquery-m/date-adddays), [Date.AddMonths](/powerquery-m/date-addmonths), [Date.AddQuarters](/powerquery-m/date-addquarters), [Date.AddWeeks](/powerquery-m/date-addweeks), [Date.AddYears](/powerquery-m/date-addyears))
* Dauern können für die Arithmetik für Datum und Uhrzeit verwendet werden, müssen jedoch vor dem Schreiben in eine Senke in einen anderen Typ transformiert werden (arithmetische Operatoren [#duration](/powerquery-m/sharpduration), [Duration.Days](/powerquery-m/duration-days), [Duration.Hours](/powerquery-m/duration-hours), [Duration.Minutes](/powerquery-m/duration-minutes), [Duration.Seconds](/powerquery-m/duration-seconds), [Duration.TotalDays](/powerquery-m/duration-totaldays), [Duration.TotalHours](/powerquery-m/duration-totalhours), [Duration.TotalMinutes](/powerquery-m/duration-totalminutes), [Duration.TotalSeconds](/powerquery-m/duration-totalseconds))    
* Großteil der standardmäßigen, wissenschaftlichen und trigonometrischen numerischen Funktionen (alle Funktionen unter [Vorgänge](/powerquery-m/number-functions#operations), [Rundung](/powerquery-m/number-functions#rounding) und [Trigonometrie](/powerquery-m/number-functions#trigonometry), *mit Ausnahme von* Number.Factorial, Number.Permutations und Number.Combinations)
* Ersetzung ([Replacer.ReplaceText](/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](/powerquery-m/replacer-replacevalue), [Text.Replace](/powerquery-m/text-replace), [Text.Remove](/powerquery-m/text-remove))
* Textextraktion in Bezug auf die Position ([Text.PositionOf](/powerquery-m/text-positionof), [Text.Length](/powerquery-m/text-length), [Text.Start](/powerquery-m/text-start), [Text.End](/powerquery-m/text-end), [Text.Middle](/powerquery-m/text-middle), [Text.ReplaceRange](/powerquery-m/text-replacerange), [Text.RemoveRange](/powerquery-m/text-removerange))
* Grundlegende Textformatierung ([Text.Lower](/powerquery-m/text-lower), [Text.Upper](/powerquery-m/text-upper), [Text.Trim](/powerquery-m/text-trim)/[Start](/powerquery-m/text-trimstart)/[End](/powerquery-m/text-trimend), [Text.PadStart](/powerquery-m/text-padstart)/[End](/powerquery-m/text-padend), [Text.Reverse](/powerquery-m/text-reverse))
* Datum/Uhrzeit-Funktionen ([Date.Day](/powerquery-m/date-day), [Date.Month](/powerquery-m/date-month), [Date.Year](/powerquery-m/date-year) [Time.Hour](/powerquery-m/time-hour), [Time.Minute](/powerquery-m/time-minute), [Time.Second](/powerquery-m/time-second), [Date.DayOfWeek](/powerquery-m/date-dayofweek), [Date.DayOfYear](/powerquery-m/date-dayofyear), [Date.DaysInMonth](/powerquery-m/date-daysinmonth))
* If-Ausdrücke (Verzweigungen müssen jedoch übereinstimmende Typen aufweisen)
* Zeilenfilter als logische Spalte
* number-, text-, logical-, date- und datetime-Konstanten

<a name="mergingjoining-tables"></a>Zusammenführen und Verknüpfen von Tabellen
----------------------
* Power Query generiert eine geschachtelte Verknüpfung (Table.NestedJoin; Benutzer können auch manuell [Table.AddJoinColumn](/powerquery-m/table-addjoincolumn) schreiben).
    Benutzer müssen dann die geschachtelte Verknüpfungsspalte in eine nicht geschachtelten Verknüpfung erweitern (Table.ExpandTableColumn, wird in keinem anderen Kontext unterstützt).
* Die M-Funktion [Table.Join](/powerquery-m/table-join) kann direkt geschrieben werden, um einen zusätzlichen Erweiterungsschritt zu vermeiden, der Benutzer muss jedoch sicherstellen, dass in den verknüpften Tabellen keine doppelten Spaltennamen vorhanden sind.
* Unterstützte Verknüpfungsarten:   [Inner](/powerquery-m/joinkind-inner), [LeftOuter](/powerquery-m/joinkind-leftouter), [RightOuter](/powerquery-m/joinkind-rightouter), [FullOuter](/powerquery-m/joinkind-fullouter)
* [Value.Equals](/powerquery-m/value-equals) sowie [Value.NullableEquals](/powerquery-m/value-nullableequals) werden als Vergleichsfunktionen für Schlüsselgleichheit unterstützt.

## <a name="group-by"></a>Gruppieren nach

Verwenden Sie [Table.Group](/powerquery-m/table-group), um Werte zu aggregieren.
* Muss mit einer Aggregationsfunktion verwendet werden.
* Unterstützte Aggregationsfunktionen:   [List.Sum](/powerquery-m/list-sum), [List.Count](/powerquery-m/list-count), [List.Average](/powerquery-m/list-average), [List.Min](/powerquery-m/list-min), [List.Max](/powerquery-m/list-max), [List.StandardDeviation](/powerquery-m/list-standarddeviation), [List.First](/powerquery-m/list-first), [List.Last](/powerquery-m/list-last)

## <a name="sorting"></a>Sortierung

Verwenden Sie [Table.Sort](/powerquery-m/table-sort), um Werte zu sortieren.

## <a name="reducing-rows"></a>Verringern von Zeilen

„Erste Zeilen beibehalten“ und „Erste Zeilen entfernen“, „Zeilenbereich beibehalten“ (entsprechende M-Funktionen unterstützen nur die Anzahl und keine Bedingungen: [Table.FirstN](/powerquery-m/table-firstn), [Table.Skip](/powerquery-m/table-skip), [Table.RemoveFirstN](/powerquery-m/table-removefirstn), [Table.Range](/powerquery-m/table-range), [Table.MinN](/powerquery-m/table-minn), [Table.MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Bekannte nicht unterstützte Funktionen

| Funktion | Status |
| -- | -- |
| Table.PromoteHeaders | Wird nicht unterstützt. Dasselbe Ergebnis kann erzielt werden, indem „Erste Zeile als Header verwenden“ im Dataset festgelegt wird. |
| Table.CombineColumns | Dies ist ein gängiges Szenario, das nicht direkt unterstützt wird. Es kann jedoch durch Hinzufügen einer neuen Spalte umgesetzt werden, in der zwei angegebene Spalten verkettet sind.  Beispiel: Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | Diese Funktion wird in den meisten Fällen unterstützt. Die folgenden Szenarien werden nicht unterstützt: Transformieren des Typs „string“ in „currency“, Transformieren des Typs „string“ in „time“, Transformieren des Typs „string“ in „Percentage“. |
| Table.NestedJoin | Wenn Sie nur einen Join durchführen, führt dies zu einem Überprüfungsfehler. Die Spalten müssen erweitert werden, damit der Vorgang funktioniert. |
| Table.Distinct | Das Entfernen doppelter Zeilen wird nicht unterstützt. |
| Table.RemoveLastN | Das Entfernen der unteren Zeilen wird nicht unterstützt. |
| Table.RowCount | Nicht unterstützt, kann jedoch durch Hinzufügen einer benutzerdefinierten Spalte mit dem Wert „1“ und anschließendes Aggregieren dieser Spalte mit „List.Sum“ erreicht werden. Table.Group wird unterstützt. | 
| Fehlerbehandlung auf Zeilenebene | Die Fehlerbehandlung auf Zeilenebene wird derzeit nicht unterstützt. Wenn Sie z. B. nicht numerische Werte aus einer Spalte herausfiltern möchten, können Sie u. a. die Textspalte in eine Zahl transformieren. Jede Zelle, die nicht transformiert werden kann, weist einen Fehlerstatus auf und muss gefiltert werden. Dieses Szenario ist bei M-Funktionen mit horizontaler Skalierung nicht möglich. |
| Table.Transpose | Nicht unterstützt |
| Table.Pivot | Nicht unterstützt |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [eine Data Wrangling-Power Query in ADF](wrangling-tutorial.md) erstellen.
