---
title: Konvertieren in Indikatorwerte
titleSuffix: Azure Machine Learning
description: Konvertieren Sie mit dem Convert to Indicator Values-Modul im Azure Machine Learning-Designer Kategoriespalten in eine Reihe von binären Indikatorspalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 81b3c113f46428327842c1555fdd1934e9ae8762
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420851"
---
# <a name="convert-to-indicator-values"></a>Konvertieren in Indikatorwerte
In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Konvertieren Sie mit dem Modul **Convert to Indicator Values** im Azure Machine Learning-Designer Spalten mit Kategoriewerten in eine Reihe von binären Indikatorspalten.  

Dieses Modul gibt außerdem eine Definition der Transformation aus, die zum Konvertieren in Indikatorwerte verwendet wird. Sie können diese Transformation bei anderen Datasets mit demselben Schema wiederverwenden, indem Sie das Modul [Apply Transformation](apply-transformation.md) einsetzen.

## <a name="how-to-configure-convert-to-indicator-values"></a>Konfigurieren von Convert to Indicator Values

1.  Suchen Sie das Modul **Convert to Indicator Values**, und ziehen Sie es in den Pipelineentwurf. Sie finden dieses Modul in der Kategorie **Data Transformation**.
    > [!NOTE]
    > Sie können das Modul [Edit Metadata](edit-metadata.md) vor dem Modul **Convert to Indiciator Values** verwenden, um die Zielspalten als Kategorie zu markieren.

1. Verbinden Sie das Modul **Convert to Indicator Values** mit dem Dataset, das die zu konvertierenden Spalten enthält. 

1. Verwenden Sie **Spalte bearbeiten**, um mindestens eine Kategoriespalte auszuwählen.

1. Wählen Sie die Option **Overwrite categorical columns** (Kategoriespalten überschreiben) aus, wenn Sie **nur** die neuen booleschen Spalten ausgeben möchten. Diese Option ist standardmäßig deaktiviert.
    

    > [!TIP]
    >  Wenn Sie die Option zum Überschreiben auswählen, wird die Quellspalte nicht gelöscht oder geändert. Stattdessen werden die neuen Spalten generiert und im Ausgabedataset dargestellt, und die Quellspalte bleibt im Arbeitsbereich verfügbar. Wenn Sie die ursprünglichen Daten anzeigen müssen, können Sie jederzeit das Modul [Add Columns](add-columns.md) verwenden, um die Quellspalte wieder hinzuzufügen.

1. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Angenommen, eine Ihrer Spalten enthält Scores, die angeben, ob die Wahrscheinlichkeit eines Servers für einen Fehler hoch, mittel oder niedrig ist.  

| Server-ID | Fehlerscore |
| --------- | ------------- |
| 10301     | Niedrig           |
| 10302     | Medium        |
| 10303     | High          |

Wenn Sie **Convert to Indicator Values** anwenden, konvertiert der Designer eine einzelne Spalte von Bezeichnungen in mehrere Spalten, die boolesche Werte enthalten:  

| Server-ID | Fehlerscore – niedrig | Fehlerscore – mittel | Fehlerscore – hoch |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Die Konvertierung funktioniert folgendermaßen:  

-   Die Spalte **Failure score** (Fehlerscore), die das Risiko beschreibt, kann nur drei mögliche Werte (hoch, mittel und niedrig) und keine fehlenden Werte aufweisen. Daher werden genau drei neue Spalten erstellt.  

-   Die neuen Indikatorspalten werden basierend auf den Spaltenüberschriften und Werten der Quellspalte nach diesem Muster benannt: *\<source column>- \<data value>* .  

-   Es sollte in genau einer Indikatorspalte eine 1 und in allen anderen Indikatorspalten eine 0 enthalten sein, da jeder Server nur eine Risikobewertung aufweisen kann.  

Sie können jetzt die drei Indikatorspalten als Features in einem Machine Learning-Modell verwenden.

Das Modul gibt zwei Ausgaben zurück:

- **Ergebnisdataset**: Ein Dataset mit konvertierten Indikatorwertspalten. Spalten, die nicht zur Bereinigung ausgewählt wurden, werden auch „per Pass-Through übergeben“.
- **Transformation der Indikatorwerte:** Eine für die Konvertierung in Indikatorwerte verwendete Datentransformation, die in Ihrem Arbeitsbereich gespeichert und später auf neue Daten angewandt werden kann.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Anwenden eines gespeicherten Indikatorwertvorgangs auf neue Daten

Wenn Sie häufig Indikatorwertvorgänge wiederholen müssen, können Sie die Schritte zur Datenbearbeitung als *Transformation* speichern, um sie mit demselben Dataset wiederzuverwenden. Dies ist hilfreich, wenn Sie häufig Daten mit demselben Schema erneut importieren und dann bereinigen müssen.

1. Fügen Sie das Modul [Apply Transformation](apply-transformation.md) (Transformation anwenden) Ihrer Pipeline hinzu.

1. Fügen Sie dann das zu bereinigende Dataset hinzu, und verbinden Sie es mit dem rechten Eingangsport.

1. Erweitern Sie die Gruppe **Datentransformation** im linken Bereich des Designers. Suchen Sie nach der gespeicherten Transformation, und ziehen Sie sie in die Pipeline.

1. Verbinden Sie die gespeicherte Transformation mit dem linken Eingangsport von [Apply Transformation](apply-transformation.md).

   Wenn Sie eine gespeicherte Transformation anwenden, können Sie nicht auswählen, welche Spalten transformiert werden sollen. Dies liegt daran, dass die Transformation definiert wurde und automatisch für die im ursprünglichen Vorgang angegebenen Datentypen gilt.

1. Übermitteln Sie die Pipeline.
 
## <a name="technical-notes"></a>Technische Hinweise  

Dieser Abschnitt enthält Implementierungsdetails, Tipps und Antworten auf häufig gestellte Fragen.

### <a name="usage-tips"></a>Verwendungstipps

-   Nur Spalten, die als Kategorie markiert sind, können in Indikatorspalten konvertiert werden. Wenn der folgende Fehler angezeigt wird, ist wahrscheinlich eine der ausgewählten Spalten keine Kategorie:  

     Fehler 0056: Die Spalte namens \<column name> befindet sich nicht in einer zulässigen Kategorie.  

     Standardmäßig werden die meisten Zeichenfolgenspalten als Zeichenfolgenfeatures behandelt, sodass Sie sie explizit mithilfe von [Edit Metadata](edit-metadata.md) als Kategorie markieren müssen.  

-   Die Anzahl von Spalten, die Sie in Indikatorspalten konvertieren können, ist nicht beschränkt. Da jedoch jede Spalte mit Werten mehrere Indikatorspalten ergeben kann, empfiehlt es sich, jeweils nur einige Spalten auf einmal zu konvertieren und zu überprüfen.  

-   Wenn in der Spalte Werte fehlen, wird für die fehlende Kategorie eine separate Indikatorspalte mit dem folgenden Namen erstellt: *\<source column>- Missing*.  

-   Wenn die Spalte, die Sie in Indikatorwerte konvertieren, Zahlen enthält, muss diese wie jede andere Featurespalte als Kategorie markiert werden. Anschließend werden die Zahlen als diskrete Werte behandelt. Wenn beispielsweise eine numerische Spalte mit Verbrauchswerten in MPG (Miles per Gallon) zwischen 25 und 30 vorhanden ist, wird für jeden diskreten Wert eine neue Indikatorspalte erstellt:  

    | Make       | Highway mpg -25 | Highway mpg -26 | Highway mpg -27 | Highway mpg -28 | Highway mpg -29 | Highway mpg -30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Contoso – Autos | 0               | 0               | 0               | 0               | 0               | 1               |

- Um zu vermeiden, dass dem Dataset zu viele Dimensionen hinzugefügt werden, wird empfohlen, zuerst die Anzahl der Werte in der Spalte zu überprüfen und die Daten entsprechend einzuordnen oder zu quantifizieren.  


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 
