---
title: Beschneiden von Werten
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mit dem Modul „Clip Values“ in Azure Machine Learning Ausreißer erkennen und ihre Werte beschneiden oder ersetzen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 99fb41542dff28997438881abad71da11e927a78
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014889"
---
# <a name="clip-values"></a>Beschneiden von Werten

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie das Modul Clip Values, um Datenwerte, die oberhalb oder unterhalb eines angegebenen Schwellenwerts liegen, zu identifizieren und optional durch einen Mittelwert, eine Konstante oder einen anderen Ersatzwert zu ersetzen.  

Sie verbinden das Modul mit einem Dataset, das die zu beschneidenden Zahlen enthält, wählen die Spalten für die Bearbeitung aus und legen dann einen Schwellenwert oder Wertebereich sowie eine Ersetzungsmethode fest. Das Modul kann nur entweder die Ergebnisse oder die geänderten Werte an das ursprüngliche Dataset angefügt ausgeben.

## <a name="how-to-configure-clip-values"></a>Konfigurieren von Clip Values

Ermitteln Sie zunächst die Spalten, die Sie beschneiden möchten, und die zu verwendende Methode. Es wird empfohlen, Beschneidungsmethoden zuerst an einer kleinen Teilmenge der Daten zu testen.

Das Modul wendet die gleichen Kriterien und die Ersetzungsmethode auf **alle** Spalten an, die Sie in die Auswahl einschließen. Achten Sie daher darauf, Spalten auszuschließen, die Sie nicht ändern möchten.

Wenn Sie auf einige Spalten Beschneidungsmethoden oder andere Kriterien anwenden möchten, müssen Sie für jede Gruppe ähnlicher Spalten eine neue Instanz von **Clip Values** verwenden.

1.  Fügen Sie das Modul **Clip Values** Ihrer Pipeline hinzu, und verbinden Sie es mit dem Dataset, das Sie ändern möchten. Sie finden dieses Modul unter **Data Transformation** (Datentransformation) in der Kategorie **Scale and Reduce** (Skalieren und verringern). 
  
1.  Wählen Sie in der **Liste der Spalten** mithilfe der Spaltenauswahl die Spalten aus, auf die **Clip Values** angewandt werden soll.  
  
1.  Wählen Sie für **Set of thresholds** (Satz von Schwellenwerten) in der Dropdownliste eine der folgenden Optionen aus. Diese Optionen legen fest, wie die Ober- und Untergrenze für zulässige Werte bzw. Werte, die beschnitten werden müssen, festgelegt werden.  
  
    - **ClipPeaks:** Wenn Sie Werte nach Spitzenwerten beschneiden, geben Sie nur eine Obergrenze an. Werte, die größer als dieser Grenzwert sind, werden ersetzt.
  
    -  **ClipSubpeaks:** Wenn Sie Werte nach Unterspitzenwerten beschneiden, geben Sie nur eine Untergrenze an. Werte, die kleiner als dieser Grenzwert sind, werden ersetzt.  
  
    - **ClipPeaksAndSubpeaks:** Wenn Sie Werte nach Spitzen- und Unterspitzenwerten beschneiden, können Sie die Ober- und die Untergrenze angeben. Werte, die außerhalb dieses Bereichs liegen, werden ersetzt. Werte, die den Grenzwerten entsprechen, werden nicht geändert.
  
1.  Abhängig von Ihrer Auswahl im vorherigen Schritt können Sie die folgenden Schwellenwerte festlegen: 

    + **Unterer Schwellenwert:** Wird nur angezeigt, wenn Sie **ClipSubpeaks** auswählen.
    + **Oberer Schwellenwert:** Wird nur angezeigt, wenn Sie **ClipPeaks** auswählen.
    + **Schwellenwert**: Wird nur angezeigt, wenn Sie **ClipPeaksAndSubPeaks** auswählen.

    Wählen Sie für jeden Schwellenwert **Konstante** oder **Perzentil** aus.

1. Wenn Sie **Konstante** auswählen, geben Sie den maximalen oder minimalen Wert in das Textfeld ein. Beispiel: Sie wissen, dass der Wert 999 als Platzhalterwert verwendet wurde. Sie können **Konstante** für den oberen Schwellenwert auswählen und unter **Constant value for upper threshold** (Konstanter Wert für oberen Schwellenwert) den Wert „999“ eingeben.
  
1. Wenn Sie **Perzentil** auswählen, schränken Sie die Spaltenwerte auf einen Perzentilbereich ein. 

    Beispiel: Sie möchten nur die Werte im Perzentilbereich von 10–80 behalten und alle anderen ersetzen. Wählen Sie **Perzentil** aus, und geben Sie dann „10“ unter **Percentile value for lower threshold** (Perzentilwert für unteren Schwellenwert) und „80“ unter **Percentile value for upper threshold** (Perzentilwert für oberen Schwellenwert) ein. 

    Einige Beispiele zur Verwendung von Perzentilbereichen finden Sie im Abschnitt zu [Perzentilen](#examples-for-clipping-using-percentiles).  
  
1.  Definieren Sie einen Ersatzwert.

    Zahlen, die genau mit den angegebenen Grenzwerten übereinstimmen, werden als innerhalb des zulässigen Wertebereichs angesehen und daher nicht ersetzt. Alle Zahlen, die außerhalb des angegebenen Bereichs liegen, werden durch den Ersatzwert ersetzt. 
  
    + **Ersatzwert für Spitzenwerte:** Definiert den Wert, der für alle Spaltenwerte, die größer als der angegebene Schwellenwert sind, ersetzt werden soll.  
    + **Ersatzwert für Unterspitzenwerte:** Definiert den Wert, der für alle Spaltenwerte, die kleiner als der angegebene Schwellenwert sind, ersetzt werden soll.  
    + Wenn Sie die Option **ClipPeaksAndSubpeaks** verwenden, können Sie separate Ersatzwerte für die oberen und unteren abgeschnittenen Werte angeben.  

    Die folgenden Ersatzwerte werden unterstützt:  
  
    -   **Schwellenwert**: Ersetzt beschnittene Werte durch den angegebenen Schwellenwert.  
  
    -   **Mittelwert:** Ersetzt beschnittene Werte durch den Mittelwert der Spaltenwerte. Der Mittelwert wird berechnet, bevor Werte abgeschnitten werden.  
  
    -   **Median:** Ersetzt beschnittene Werte durch den Medianwert der Spaltenwerte. Der Median wird berechnet, bevor Werte abgeschnitten werden.   
  
    -   **Fehlend:** Ersetzt beschnittene Werte durch einen fehlenden (leeren) Wert.  
  
1.  **Indikatorspalten hinzufügen:** Wählen Sie diese Option aus, wenn Sie eine neue Spalte generieren möchten, die Aufschluss darüber gibt, ob der angegebene Beschneidungsvorgang auf die Daten in dieser Zeile angewandt wurde. Diese Option ist nützlich, wenn Sie einen neuen Satz von Beschneidungs- und Ersetzungswerten testen.  
  
1. **Flag für Überschreiben:** Gibt an, wie die neuen Werte generiert werden sollen. Standardmäßig erstellt **Clip Values** eine neue Spalte mit den am gewünschten Schwellenwert abgeschnittenen Spitzenwerten. Neue Werte überschreiben die ursprüngliche Spalte.  
  
    Deaktivieren Sie diese Option, um die ursprüngliche Spalte beizubehalten und eine neue Spalte mit den beschnittenen Werten hinzuzufügen.  
  
1.  Übermitteln Sie die Pipeline.  
  
    Klicken Sie mit der rechten Maustaste auf das Modul **Clip Values**, und wählen Sie **Visualisieren** aus, oder wählen Sie das Modul aus, und wechseln Sie im rechten Bereich zur Registerkarte **Ausgaben**. Klicken Sie auf das Histogrammsymbol in den **Portausgaben**, um die Werte zu überprüfen und sicherzustellen, dass der Clippingvorgang Ihren Erwartungen entspricht.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Beispiele für das Beschneiden mithilfe von Perzentilen

Um zu verstehen, wie das Beschneiden nach Perzentilen funktioniert, stellen Sie sich ein Dataset mit 10 Zeilen vor, die jeweils eine Instanz der Werte 1 bis 10 aufweisen.  
  
- Wenn Sie ein Perzentil als oberen Schwellenwert verwenden, müssen bei dem Wert für das 90. Perzentil 90 % aller Werte im Dataset kleiner als dieser Wert sein.  
  
- Wenn Sie ein Perzentil als unteren Schwellenwert verwenden, müssen bei dem Wert für das 10. Perzentil 10 % aller Werte im Dataset kleiner als dieser Wert sein.  
  
1.  Wählen Sie für **Set of thresholds** (Satz von Schwellenwerten) die Option **ClipPeaksAndSubPeaks** aus.  
  
1.  Wählen Sie für **Upper threshold** (Oberer Schwellenwert) die Option **Percentile** (Perzentil) aus, und geben Sie für **Percentile number** (Perzentilwert) den Wert „90“ ein.  
  
1.  Wählen Sie für **Upper substitute value** (Oberer Ersatzwert) die Option **Missing Value** (Fehlender Wert) aus.  
  
1.  Wählen Sie für **Lower threshold** (Unterer Schwellenwert) die Option **Percentile** (Perzentil) aus, und geben Sie für **Percentile number** (Perzentilwert) den Wert „10“ ein.  
  
1.  Wählen Sie für **Lower substitute value** (Unterer Ersatzwert) die Option **Missing Value** (Fehlender Wert) aus.  
  
1.  Deaktivieren Sie die Option **Flag für Überschreiben**, und aktivieren Sie die Option **Add indicator column** (Indikatorspalte hinzufügen).  
  
Probieren Sie nun dieselbe Pipeline mit 60 als oberem Perzentilschwellenwert und 30 als unterem Perzentilschwellenwert aus, und verwenden Sie den Schwellenwert als Ersatzwert. In der folgenden Tabelle werden die beiden Ergebnisse verglichen:  
  
1.  Ersetzen durch fehlend; oberer Schwellenwert = 90; unterer Schwellenwert = 20  
  
1.  Ersetzen durch Schwellenwert; Oberer Perzentilwert = 60; unterer Perzentilwert = 40  
  
|Ursprüngliche Daten|Ersetzen durch fehlend|Ersetzen durch Schwellenwert|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, FALSE<br /><br /> 4, FALSE<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, FALSE<br /><br /> 8, FALSE<br /><br /> 9, FALSE<br /><br /> TRUE|4, TRUE<br /><br /> 4, TRUE<br /><br /> 4, TRUE<br /><br /> 4, TRUE<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE| 
 
## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 
