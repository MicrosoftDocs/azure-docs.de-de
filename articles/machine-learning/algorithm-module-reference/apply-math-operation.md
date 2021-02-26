---
title: Anwenden einer mathematischen Operation
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie das Modul zum Anwenden einer mathematischen Operation in Azure Machine Learning verwenden können, um eine mathematische Operation auf Spaltenwerte in einer Pipeline anzuwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 3b2925a532d722598ccf16c001c9e2591aed1f2b
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584989"
---
# <a name="apply-math-operation"></a>Anwenden einer mathematischen Operation

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie die das Modul zum Anwenden einer mathematischen Operation zum Erstellen von Berechnungen, die auf numerische Spalten im Eingabedataset angewendet werden. 

Zu den mathematischen Operationen zählen arithmetische Funktionen, trigonometrische Funktionen, Rundungsfunktionen und spezielle Data Science-Funktionen wie Gamma- und Fehlerfunktionen.  

Nachdem Sie eine Operation definiert und die Pipeline gestartet haben, werden die Werte zu Ihrem Dataset hinzugefügt. Je nachdem, wie Sie das Modul konfigurieren, können Sie folgende Aktionen ausführen:

+ Fügen Sie die Ergebnisse an Ihr Dataset an (hilfreich beim Überprüfen des Ergebnisses der Operation).
+ Sie können Spaltenwerte durch die neuen berechneten Werte ersetzen.
+ Sie können eine neue Spalte für Ergebnisse generieren und nicht die Originaldaten anzeigen. 

Suchen Sie in den folgenden Kategorien nach der benötigten Operation:  

- [Grundlegend](#basic-math-operations)  
  
     Mit den Funktionen der Kategorie **Grundlegende Funktionen** kann ein einzelner Wert oder eine Wertspalte bearbeitet werden. So können Sie beispielsweise den absoluten Wert aller Zahlen in einer Spalte ermitteln oder die Quadratwurzel jedes Wertes in einer Spalte berechnen.  
  
-   [Vergleichen](#comparison-operations)  
  
      Die Funktionen in der Kategorie **Vergleichen** werden alle für Vergleiche verwendet: Sie können die Werte in zwei Spalten paarweise oder jeden Wert in einer Spalte mit einer bestimmten Konstanten vergleichen. Mit dem Vergleich von Spalten können Sie beispielsweise feststellen, ob die Werte in zwei Datasets gleich sind. Oder Sie verwenden eine Konstante, wie beispielsweise einen maximal zulässigen Wert, um Ausreißer in einer numerischen Spalte zu finden.  
  
-   [Vorgänge](#arithmetic-operations)  
  
     Die Kategorie **Operationen** umfasst grundlegende mathematische Funktionen: Addition, Subtraktion, Multiplikation und Division. Sie können entweder mit Spalten oder Konstanten arbeiten. Sie können beispielsweise den Wert in Spalte A mit dem Wert in Spalte B addieren. Oder Sie können von jedem Wert in Spalte A eine Konstante, wie beispielsweise einen zuvor berechneten Mittelwert, subtrahieren.  
  
-   [Runden](#rounding-operations)  
  
     Die Kategorie **Runden** enthält eine Vielzahl von Funktionen zur Ausführung von Operationen wie Runden, Aufrundungs- und Abrundungsfunktion und Kürzen auf verschiedene Genauigkeitsstufen. Sie können die Genauigkeitsstufe sowohl für Dezimal- als auch für Ganzzahlen angeben.  
  
-   [Sonderfunktionen](#special-math-functions)  
  
     Die Kategorie **Sonderfunktionen** beinhaltet mathematische Funktionen, die vor allem im Bereich Data Science verwendet werden, wie elliptische Integrale und die Gaußsche Fehlerfunktion.  
  
-   [Trigonometrische Funktionen](#trigonometric-functions)  
  
     Die Kategorie **Trigonometrische Funktionen** enthält alle trigonometrischen Standardfunktionen. So können Sie beispielsweise das Bogenmaß in Grad konvertieren oder Funktionen wie Tangens entweder als Bogenmaß oder in Grad berechnen.
     Diese Funktionen sind unär, d. h. sie verwenden eine einzige Wertspalte als Eingabe, wenden die trigonometrische Funktion an und geben als Ergebnis eine Wertspalte zurück. Stellen Sie sicher, dass die Eingabespalte den richtigen Typ aufweist und den richtigen Typ von Werten für die angegebene Operation enthält.   

## <a name="how-to-configure-apply-math-operation"></a>So konfigurieren Sie das Modul zum Anwenden einer mathematischen Operation  

Für das Modul zum **Anwenden einer mathematischen Operation** ist ein Dataset erforderlich, dass mindestens eine Spalte mit ausschließlich Zahlen enthält. Die Zahlen können diskret oder kontinuierlich sein, müssen aber ein numerischer Datentyp und keine Zeichenfolge sein.

Sie können die gleiche Operation auf mehrere numerische Spalten anwenden, wobei jedoch alle Spalten zum selben Dataset gehören müssen. 

Jede Instanz dieses Moduls kann jeweils nur einen Operationstyp ausführen. Zum Auszuführen komplexer mathematischer Operationen müssen Sie möglicherweise mehrere Instanzen des Moduls zum **Anwenden einer mathematischen Operation** miteinander verketten.  
  
1.  Fügen Sie das Modul zum **Anwenden einer mathematischen Operation** Ihrer Pipeline hinzu.

1. Stellen Sie eine Verbindung mit einem Dataset her, das mindestens eine numerische Spalte enthält.  

1.  Wählen Sie mindestens eine Quellspalte aus, für die die Berechnung durchgeführt werden soll.   
  
    - Jede ausgewählte Spalte muss ein numerischer Datentyp sein. 
    - Der Datenbereich muss für die ausgewählte mathematische Operation gültig sein. Andernfalls wird ein Fehler oder ein NaN-Ergebnis (Not a Number, Keine Zahl) angezeigt. Beispielsweise ist Ln(-1.0) eine ungültige Operation und führt zum Wert `NaN`.
  
1.  Wählen Sie **Kategorie** aus, um den **Typ** der auszuführenden mathematischen Operation auszuwählen.
    
1. Wählen Sie eine bestimmte Operation aus der Liste in dieser Kategorie aus.
  
1.  Legen Sie zusätzliche Parameter fest, die für den jeweiligen Operationstyp erforderlich sind.  
  
1.  Verwenden Sie die Option **Output mode**, um anzugeben, wie die mathematische Operation generiert werden soll: 

    - **Append**. Alle als Eingaben verwendeten Spalten sind im ausgegebenen Dataset enthalten, und es wird eine zusätzliche Spalte angefügt, die die Ergebnisse der mathematischen Operation enthält.
    - **Inplace**. Die Werte in den als Eingaben verwendeten Spalten werden durch die neuen berechneten Werte ersetzt. 
    - **ResultOnly**. Es wird eine einzelne Spalte mit den Ergebnissen der mathematischen Operation zurückgegeben.
  
1.  Übermitteln Sie die Pipeline.  
  
## <a name="results"></a>Ergebnisse

Wenn Sie die Ergebnisse mit den Optionen **Append** oder **ResultOnly** generieren, werden in den Spaltenüberschriften des zurückgegebenen Datasets die Operation und die verwendeten Spalten angegeben. Wenn Sie z. B. zwei Spalten mit dem Operator **Equals** vergleichen, sehen die Ergebnisse wie folgt aus:  
  
-   **Equals(Col2_Col1)**, gibt an, dass Sie Col2 mit Col1 überprüft haben.  
-   **Equals(Col2_$10)**, gibt an, dass Sie Spalte 2 mit der Konstanten 10 verglichen haben.  

Auch wenn Sie die Option **In place** verwenden, werden die Quelldaten weder gelöscht noch geändert. Die Spalte im ursprünglichen Dataset ist weiterhin im Designer verfügbar. Um die ursprünglichen Daten anzuzeigen, können Sie das Modul [Spalten hinzufügen](add-columns.md) anschließen und es mit der Ausgabe des Moduls zum **Anwenden einer mathematischen Operation** verbinden.  
    
## <a name="basic-math-operations"></a>Grundlegende mathematische Operationen 

Die Funktionen in der Kategorie **Grundlegende Funktionen** verwenden in der Regel einen Einzelwert aus einer Spalte, führen die vordefinierte Operation aus und geben einen Einzelwert zurück. Für einige Funktionen können Sie eine Konstante oder einen Spaltensatz als zweites Argument angeben.  
  
 Azure Machine Learning unterstützt die folgenden Funktionen in der Kategorie **Grundlegende Funktionen**:  

### <a name="abs"></a>Abs

Gibt den absoluten Wert der ausgewählten Spalte zurück.  
  
### <a name="atan2"></a>Atan2

Gibt den umgekehrten Tangens des vierten Quadranten zurück.  

Wählen Sie die Spalten aus, die die Punktkoordinaten enthalten. Für das zweite Argument, das der x-Koordinate entspricht, können Sie ebenfalls eine Konstante angeben.  

Entspricht der ATAN2-Funktion in MATLAB.  

### <a name="conj"></a>Conj

Gibt die Konjugation für die Werte in der ausgewählten Spalte zurück.  

### <a name="cuberoot"></a>CubeRoot

Gibt die Kubikwurzel für die Werte in der ausgewählten Spalte zurück.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Gibt die Doppelfakultät für die Werte in der ausgewählten Spalte zurück. Die Doppelfakultät ist eine Erweiterung der normalen Fakultätsfunktion, und sie wird als `x!!` bezeichnet.  

### <a name="eps"></a>Eps

Gibt die Größe des Abstands zwischen dem aktuellen Wert und der nächsthöheren Zahl mit doppelter Genauigkeit zurück. Entspricht der EPS-Funktion in MATLAB.  
  
### <a name="exp"></a>Exp

Potenziert die Basis e mit dem Wert in der ausgewählten Spalte. Diese Funktion entspricht der EXP-Funktion in Excel.  

### <a name="exp2"></a>Exp2

Gibt das Exponential der Basis 2 der Argumente zurück und löst nach y = x * 2<sup>t</sup> auf, wobei „t“ eine Wertspalte mit Exponenten ist.  

Wählen Sie unter **Spaltensatz** die Spalte aus, die die Exponentenwerte t enthält.

Für **Exp2** können Sie ein zweites Argument x angeben, das entweder eine Konstante oder eine andere Wertspalte sein kann. Geben Sie in **Zweiter Argumenttyp** an, ob der Multiplikator x als Konstante oder als ein Wert in einer Spalte angegeben wird.  

Wenn Sie beispielsweise eine Spalte mit den Werten {0,1,2,3,4,5} sowohl für den Multiplikator als auch für den Exponenten auswählen, gibt die Funktion {0, 2, 8, 24, 64 160) zurück.  

### <a name="expminus1"></a>ExpMinus1 

Gibt den negativen Exponenten für Werte in der ausgewählten Spalte zurück.  

### <a name="factorial"></a>Fakultät
Gibt die Fakultät für Werte in der ausgewählten Spalte zurück.  

### <a name="hypotenuse"></a>Hypotenuse
Berechnet die Hypotenuse für ein Dreieck, in dem die Länge einer Seite als Wertspalte und die Länge der zweiten Seite entweder als Konstante oder als zwei Spalten angegeben wird.  

### <a name="ln"></a>Ln

Gibt den natürlichen Logarithmus für die Werte in der ausgewählten Spalte zurück.  

### <a name="lnplus1"></a>LnPlus1

Gibt den natürlichen Logarithmus plus 1 für die Werte in der ausgewählten Spalte zurück.  

### <a name="log"></a>Log

Gibt den Logarithmus der Werte in der ausgewählten Spalte bezogen auf die angegebene Basis zurück.  

Sie können die Basis (das zweite Argument) entweder als Konstante oder durch Auswahl einer anderen Wertspalte angeben.  

### <a name="log10"></a>Log10

Gibt den Logarithmuswert der Basis 10 für die ausgewählte Spalte zurück.  

### <a name="log2"></a>Log2

Gibt den Logarithmuswert der Basis 2 für die ausgewählte Spalte zurück.  

### <a name="nthroot"></a>NthRoot
Gibt die n-te Wurzel des Werts unter Verwendung eines von Ihnen angegebenen n-Werts zurück.  

Wählen Sie mithilfe der Option **ColumnSet** die Spalten aus, für die die Wurzel berechnet werden soll.  

Wählen Sie in **Zweiter Argumenttyp** eine andere Spalte aus, die die Wurzel enthält, oder geben Sie eine Konstante an, die als Wurzel verwendet werden soll.  

Wenn das zweite Argument eine Spalte ist, wird jeder Wert in der Spalte als Wert von n für die entsprechende Zeile verwendet. Wenn das zweite Argument eine Konstante ist, geben Sie den Wert für n im Textfeld **Zweites Argument** ein.
### <a name="pow"></a>Pow

Berechnet die Potenz der Basis X für jeden Wert in der ausgewählten Spalte mit Y.  

Wählen Sie zunächst mit der Option **ColumnSet** die Spalten aus, die die **Basis** enthalten. Diese muss eine Gleitkommazahl sein.  

Wählen Sie in **Zweiter Argumenttyp** eine Spalte aus, die den Exponenten enthält, oder geben Sie eine Konstante an, die als Exponent verwendet werden soll.  

Wenn das zweite Argument eine Spalte ist, wird jeder Wert in der Spalte als Exponent für die entsprechende Zeile verwendet. Wenn das zweite Argument eine Konstante ist, geben Sie den Wert für den Exponenten im Textfeld **Zweites Argument** ein.  

### <a name="sqrt"></a>Sqrt

Gibt die Quadratwurzel für die Werte in der ausgewählten Spalte zurück.  

### <a name="sqrtpi"></a>SqrtPi

Jeder Wert in der ausgewählten Spalte wird mit Pi multipliziert, anschließend wird die Quadratwurzel des Ergebnisses zurückgegeben.  

### <a name="square"></a>Square

Quadriert die Werte in der ausgewählten Spalte.  

## <a name="comparison-operations"></a>Vergleichsoperationen  

Sie können jederzeit die Vergleichsfunktionen im Azure Machine Learning-Designer verwenden, wenn Sie zwei Wertsätze miteinander vergleichen müssen. Beispielsweise müssen Sie möglicherweise in einer Pipeline die folgenden Vergleichsoperationen durchführen:  

- Bewerten einer Spalte in einem Wahrscheinlichkeitsmodell anhand eines Schwellenwerts.
- Bestimmen, ob zwei Resultsets gleich sind. Fügen Sie für jede abweichende Zeile ein FALSE-Flag hinzu, das für die weitere Verarbeitung oder Filterung verwendet werden kann.  

### <a name="equalto"></a>EqualTo

Gibt „True“ zurück, wenn die beiden Werte gleich sind.  

### <a name="greaterthan"></a>GreaterThan

Gibt „True“ zurück, wenn die Werte in **Spaltensatz** größer als die angegebene Konstante oder größer als die entsprechenden Werte in der Vergleichsspalte sind.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Gibt „True“ zurück, wenn die Werte in **Spaltensatz** größer oder gleich der angegebenen Konstanten oder größer oder gleich den entsprechenden Werten in der Vergleichsspalte sind.  

### <a name="lessthan"></a>LessThan

Gibt „True“ zurück, wenn die Werte in **Spaltensatz** kleiner als die angegebene Konstante oder kleiner als die entsprechenden Werte in der Vergleichsspalte sind.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Gibt „True“ zurück, wenn die Werte in **Spaltensatz** kleiner oder gleich der angegebenen Konstanten oder kleiner oder gleich den entsprechenden Werten in der Vergleichsspalte sind.  

### <a name="notequalto"></a>NotEqualTo

Gibt "True" zurück, wenn die Werte in **Column set** ungleich zu der Konstante oder der Vergleichsspalte sind, andernfalls wird "False" zurückgegeben.  

### <a name="pairmax"></a>PairMax

Gibt den jeweils größeren Wert zurück – den Wert in **Column set** oder den Wert in der Konstante oder der Vergleichsspalte.  

### <a name="pairmin"></a>PairMin

Gibt den kleineren Wert zurück – den Wert in **Spaltensatz** oder den Wert der Konstanten oder in der Vergleichsspalte.  
  
##  <a name="arithmetic-operations"></a>Arithmetische Operationen   

Umfasst die grundlegenden arithmetischen Operationen: Addition und Subtraktion, Division und Multiplikation.  Da die meisten Operationen binär sind und zwei Zahlen erfordern, wählen Sie zuerst die Operation und dann die Spalte oder die Zahlen, die im ersten und zweiten Argument verwendet werden sollen.

Die Reihenfolge für Division und Subtraktion lautet wie folgt: 
- Subtract(Arg1_Arg2) = Arg1 - Arg 2
- Divide(Arg1_Arg2) =  Arg1 / Arg 2

Die folgende Tabelle zeigt einige Beispiele
Vorgang|Num1|Num2|Ergebnisspalte|Ergebniswert|
----|----|----|----|----
|Addition|1|5|Add(Num2_Num1)| 6|
|Multiplikation|1|5|Multiple(Num2_Num1)|5|
|Subtraktion|5|1|Subtract(Num2_Num1)|4|
|Subtraktion|0|1|Subtract(Num2_Num1)|-1|
|Division|5|1|Divide(Num2_Num1)|5|
|Division|1|0|Divide(Num2_Num1)|Unendlich|

### <a name="add"></a>Hinzufügen

Geben Sie mit **Spaltensatz** die Quellspalten an, und addieren Sie dann diese Werte mit einer Zahl, die in **Zweites Argument** angegeben ist.  

Um die Werte in zwei Spalten zu addieren, wählen Sie eine oder mehrere Spalten mithilfe von **Spaltensatz** und anschließend eine zweite Spalte mithilfe von **Zweites Argument**.  

### <a name="divide"></a>Dividieren

Dividiert die Werte in **Spaltensatz** durch eine Konstante oder durch die in **Zweites Argument** definierten Spaltenwerte.  Anders ausgedrückt: Sie wählen zuerst den Divisor und dann die Dividende aus. Der Ausgabewert ist der Quotient.

### <a name="multiply"></a>Multiplizieren

Dividiert die Werte in **Column set** durch die angegebene Konstante bzw. die angegebenen Spaltenwerte.  

### <a name="subtract"></a>Subtrahieren

Geben Sie die Wertspalte an, für die die Operation durchgeführt werden soll (der *Minuenden*), indem Sie mit der Option **Spaltensatz** eine andere Spalte auswählen. Geben Sie dann über die Dropdownliste **Zweites Argument** die zu subtrahierende Zahl (den *Subtrahenden*) an. Sie können entweder eine Konstante oder eine Wertspalte auswählen.

##  <a name="rounding-operations"></a>Rundungsoperationen 

Azure Machine Learning-Designer unterstützt eine Vielzahl von Rundungsoperationen. Bei vielen Operationen müssen Sie angeben, wie genau gerundet werden soll. Sie können entweder eine statische Genauigkeit verwenden, die als Konstante angegeben ist, oder Sie können einen dynamischen Genauigkeitswert anwenden, der aus einer Wertspalte abgerufen wird.  

- Wenn Sie eine Konstante verwenden, legen Sie den **Genauigkeitstyp** auf **Konstante** fest, und geben Sie dann die Anzahl der Stellen als Ganzzahl in das Textfeld **Konstante Genauigkeit** ein. Wenn Sie keine Ganzzahl eingeben, gibt das Modul zwar keinen Fehler aus, aber es können unerwartete Ergebnisse angezeigt werden.  

- Um für jede Zeile im Dataset einen anderen Genauigkeitswert zu verwenden, legen Sie für **Genauigkeitstyp****ColumnSet** fest, und wählen Sie dann die Spalte aus, die die entsprechenden Genauigkeitswerte enthält.  

### <a name="ceiling"></a>Ceiling

Gibt die Obergrenze für die Werte in **Column set** zurück.  

### <a name="ceilingpower2"></a>CeilingPower2

Gibt die quadrierte Obergrenze für die Werte in **Column set** zurück.  

### <a name="floor"></a>Etage

Gibt die Untergrenze für die Werte in **Column set** mit der angegebenen Genauigkeit zurück.  

### <a name="mod"></a>Mod

Gibt die Nachkommastellen der Werte in **Spaltensatz** entsprechend der angegebenen Genauigkeit zurück.  

### <a name="quotient"></a>Quotient

Gibt den Bruchteil der Werte in **Column set** mit der angegebenen Genauigkeit zurück.  

### <a name="remainder"></a>Rest

Gibt den Rest für die Werte in **Column set** zurück.  

### <a name="rounddigits"></a>RoundDigits

Gibt die Werte in **Column set** nach der 4/5-Regel auf die angegebene Anzahl von Stellen gerundet zurück.  

### <a name="rounddown"></a>RoundDown

Gibt die Werte in **Column set** auf die angegebene Anzahl von Stellen abgerundet zurück.  

### <a name="roundup"></a>RoundUp

Gibt die Werte in **Column set** auf die angegebene Anzahl von Stellen aufgerundet zurück.  

### <a name="toeven"></a>ToEven

Gibt die Werte in **Column set** auf die nächste ganze gerade Zahl gerundet zurück.  

### <a name="toodd"></a>ToOdd

Gibt die Werte in **Column set** auf die nächste ganze ungerade Zahl gerundet zurück.  

### <a name="truncate"></a>Truncate

Schneidet die Werte in **Column set** ab, indem alle Ziffern entfernt werden, die für die angegebene Genauigkeit unzulässig sind.  
  
## <a name="special-math-functions"></a>Mathematische Sonderfunktionen

Diese Kategorie enthält mathematische Sonderfunktionen, die häufig in Data Science verwendet werden. Sofern nicht anders angegeben, ist die Funktion unär und gibt die spezifizierte Berechnung für jeden Wert in der oder den ausgewählten Spalten zurück.  

### <a name="beta"></a>Beta

Gibt den Wert der Eulerschen Betafunktion zurück.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
Gibt den Wert des unvollständigen elliptischen Integrals zurück.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Gibt den Wert des vollständigen elliptischen Integrals (K) zurück.  

### <a name="erf"></a>Erf

Gibt den Wert der Fehlerfunktion zurück.  

Die Fehlerfunktion (auch Gaußsche Fehlerfunktion genannt) ist eine Sonderfunktion der Sigmoidfunktion, die in der Wahrscheinlichkeit zur Beschreibung der Diffusion verwendet wird.  

### <a name="erfc"></a>Erfc

Gibt den Wert der komplementären Fehlerfunktion zurück.  

`Erfc` ist als „1 – erf(x)“ definiert.  

### <a name="erfscaled"></a>ErfScaled

Gibt den Wert der skalierten Fehlerfunktion zurück.  

Die skalierte Version der Fehlerfunktion kann zur Vermeidung eines arithmetischen Unterlaufs verwendet werden.  

### <a name="erfinverse"></a>Erfinverse

Gibt den Wert der umgekehrten `erf`-Funktion zurück.  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

Gibt den Wert der Integralexponentialfunktion Ei zurück.  

### <a name="gamma"></a>Gamma

Gibt den Wert der Gammafunktion zurück.  

### <a name="gammaln"></a>GammaLn

Gibt den natürlichen Logarithmus der Gammafunktion zurück.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Gibt den Wert der regularisierten, unvollständigen Gammafunktion zurück.  

Diese Funktion verwendet ein zweites Argument, das entweder als Konstante oder als Wertspalte angegeben werden kann.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Gibt den Wert der umgekehrten, regularisierten, unvollständigen Gammafunktion zurück.  

Diese Funktion verwendet ein zweites Argument, das entweder als Konstante oder als Wertspalte angegeben werden kann.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Gibt den Wert der regularisierten, unvollständigen Gammafunktion zurück.  

Diese Funktion verwendet ein zweites Argument, das entweder als Konstante oder als Wertspalte angegeben werden kann.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Gibt den Wert der umgekehrten, generalisierten, regularisierten, unvollständigen Gammafunktion zurück.

Diese Funktion verwendet ein zweites Argument, das entweder als Konstante oder als Wertspalte angegeben werden kann.  

### <a name="polygamma"></a>PolyGamma

Gibt den Wert der Polygammafunktion zurück.  

Diese Funktion verwendet ein zweites Argument, das entweder als Konstante oder als Wertspalte angegeben werden kann.

##  <a name="trigonometric-functions"></a>Trigonometrische Funktionen 

Diese Kategorie umfasst die meisten wichtigen trigonometrischen und umgekehrten trigonometrischen Funktionen. Alle trigonometrischen Funktionen sind unär und erfordern keine zusätzlichen Argumente.  

### <a name="acos"></a>Acos

Berechnet den Arkuskosinus für die Spaltenwerte.  

### <a name="acosdegree"></a>AcosDegree

Berechnet den Arkuskosinus für die Spaltenwerte in Grad.  

### <a name="acosh"></a>Acosh

Berechnet den hyperbolischen Arkuskosinus für die Spaltenwerte.  

### <a name="acot"></a>Acot

Berechnet den Arkuskotangens für die Spaltenwerte.  

### <a name="acotdegrees"></a>AcotDegrees

Berechnet den Arkuskotangens für die Spaltenwerte in Grad.  

### <a name="acoth"></a>Acoth

Berechnet den hyperbolischen Arkuskotangens für die Spaltenwerte.  

### <a name="acsc"></a>Acsc

Berechnet den Arkuskosekans für die Spaltenwerte.  

### <a name="acscdegrees"></a>AcscDegrees

Berechnet den Arkuskosekans für die Spaltenwerte in Grad.  
### <a name="asec"></a>Asec

Berechnet den Arkussekans für die Spaltenwerte.  

### <a name="asecdegrees"></a>AsecDegrees

Berechnet den Arkussekans für die Spaltenwerte in Grad.  

### <a name="asech"></a>Asech

Berechnet den hyperbolischen Arkussekans für die Spaltenwerte.  

### <a name="asin"></a>Asin

Berechnet den Arkussinus für die Spaltenwerte.  

### <a name="asindegrees"></a>AsinDegrees

Berechnet den Arkussinus für die Spaltenwerte in Grad.  

### <a name="asinh"></a>Asinh

Berechnet den hyperbolischen Arkussinus für die Spaltenwerte.  

### <a name="atan"></a>Atan

Berechnet den Arkustangens für die Spaltenwerte.  

### <a name="atandegrees"></a>AtanDegrees

Berechnet den Arkustangens für die Spaltenwerte in Grad.  

### <a name="atanh"></a>Atanh

Berechnet den hyperbolischen Arkustangens für die Spaltenwerte.  

### <a name="cos"></a>Cos

Berechnet den Kosinus für die Spaltenwerte.  

### <a name="cosdegrees"></a>CosDegrees

Berechnet den Kosinus für die Spaltenwerte in Grad.  

### <a name="cosh"></a>Cosh

Berechnet den hyperbolischen Kosinus für die Spaltenwerte.  

### <a name="cot"></a>Cot

Berechnet den Kotangens für die Spaltenwerte.  

### <a name="cotdegrees"></a>CotDegrees

Berechnet den Kotangens für die Spaltenwerte in Grad.  

### <a name="coth"></a>Coth
Berechnet den hyperbolischen Kotangens für die Spaltenwerte.  

### <a name="csc"></a>Csc

Berechnet den Kosekans für die Spaltenwerte.  

### <a name="cscdegrees"></a>CscDegrees

Berechnet den Kosekans für die Spaltenwerte in Grad.  

### <a name="csch"></a>Csch

Berechnet den hyperbolischen Kosekans für die Spaltenwerte.  

### <a name="degreestoradians"></a>DegreesToRadians

Konvertiert Grade in Bogenmaße.  

### <a name="sec"></a>Sec

Berechnet den Sekans für die Spaltenwerte.  

### <a name="asecdegrees"></a>aSecDegrees

Berechnet den Sekans für die Spaltenwerte in Grad.  

### <a name="asech"></a>aSech

Berechnet den hyperbolischen Sekans für die Spaltenwerte.  

### <a name="sign"></a>Signieren

Gibt das Vorzeichen der Spaltenwerte zurück.  

### <a name="sin"></a>Sin

Berechnet den Sinus für die Spaltenwerte.  

### <a name="sinc"></a>Sinc

Berechnet den Sinus-Kosinus-Wert für die Spaltenwerte.  

### <a name="sindegrees"></a>SinDegrees

Berechnet den Sinus für die Spaltenwerte in Grad.  

### <a name="sinh"></a>Sinh

Berechnet den hyperbolischen Sinus für die Spaltenwerte.  

### <a name="tan"></a>Tan

Berechnet den Tangens für die Spaltenwerte.  

### <a name="tandegrees"></a>TanDegrees

Berechnet den Tangens für das Argument in Grad.  

### <a name="tanh"></a>Tanh

Berechnet den hyperbolischen Tangens für die Spaltenwerte.  
  
## <a name="technical-notes"></a>Technische Hinweise

Gehen Sie vorsichtig vor, wenn Sie mehr als eine Spalte als zweiten Operator auswählen. Die Ergebnisse sind bei einer einfachen Operation leicht verständlich, wie z. B. das Addieren einer Konstanten zu allen Spalten. 

Nehmen wir an, Ihr Dataset besteht aus mehreren Spalten und Sie addieren das Dataset mit sich selbst. In den Ergebnissen wird jede Spalte wie folgt mit sich selbst addiert:  
  
|Num1|Num2|Num3|Add(Num1_Num1)|Add(Num2_Num2)|Add(Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Wenn Sie komplexere Berechnungen durchführen müssen, können Sie mehrere Instanzen von des Moduls zum **Anwenden einer mathematischen Operation** miteinander verketten. Sie können z. B. zwei Spalten mit einer Instanz von **Apply Math Operation** hinzufügen und dann eine andere Instanz von **Apply Math Operation** verwenden, um die Summe durch eine Konstante zu dividieren und so den Mittelwert zu erhalten.  
  
Alternativ können Sie auch eines der folgenden Module verwenden, um alle Berechnungen gleichzeitig mit SQL-, R- oder Python-Skripts durchzuführen:
 
+ [Ausführen von R-Skripts](execute-r-script.md)
+ [Ausführen von Python-Skripts](execute-python-script.md)
+ [Anwenden der SQL-Transformation](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 
