---
title: 'Init Image Transformation: Bildtransformation'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie das Init Image Transformation-Modul im Azure Machine Learning-Designer verwenden, um eine Bildtransformation zu initialisieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fc0eb196ed24e413c35d64f0571ff29dc3725032
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421276"
---
# <a name="init-image-transformation"></a>Initialisieren einer Bildtransformation

In diesem Artikel wird beschrieben, wie das **Init Image Transformation**-Modul im Azure Machine Learning-Designer verwendet wird, um eine Bildtransformation zu initialisieren, um damit anzugeben, wie ein Bild transformiert werden soll.

## <a name="how-to-configure-init-image-transformation"></a>Konfigurieren von Init Image Transformation

1.  Fügen Sie das Modul **Init Image Transformation** im Designer Ihrer Pipeline hinzu. 

2.  Geben Sie für **Resize** an, ob die Größe des PIL-Eingabebilds gemäß der Angabe geändert werden soll. Wenn Sie ‚True‘ wählen, können Sie die gewünschte Größe des Ausgabebilds in **Size** angeben, der Standardwert ist 256. 

3.  Geben Sie für **Center crop** an, ob das angegebene PIL-Bild in der Mitte zugeschnitten werden soll. Wenn Sie ‚True‘ wählen, können Sie die gewünschte Ausgabebildgröße des Zuschnitts in **Crop size** angeben, der Standardwert ist 224.  

4.  Geben Sie in **Pad** an, ob das angegebene PIL-Bild auf allen Seiten mit dem Auffüllwert 0 aufgefüllt werden soll. Wenn Sie ‚True‘ wählen, können Sie die Auffüllung für jede Kante (wie viele Pixel hinzugefügt werden sollen) in **Padding** angeben.

5.  Geben Sie in **Color jitter** an, ob Helligkeit, Kontrast und Sättigung eines Bilds zufällig geändert werden sollen.

6.  Geben Sie in **Grayscale** an, ob das Bild in Graustufen konvertiert werden soll.

7.  Geben Sie in **Random resized crop** an, ob das angegebene PIL-Bild auf eine zufällige Größe und ein zufälliges Seitenverhältnis zugeschnitten werden soll. Es wird ein Zuschnitt zufälliger Größe (Bereich von 0,08 bis 1,0) gegenüber der Originalgröße und einem zufälligen Seitenverhältnis (Bereich von 3/4 bis 4/3) gegenüber dem ursprünglichen Seitenverhältnis erstellt. Die Größe dieses Zuschnitts wird schließlich auf die angegebene Größe geändert.
    Dies wird häufig beim Trainieren des Inception-Netzwerks verwendet. Wenn Sie ‚True‘ wählen, können Sie die erwartete Ausgabegröße jeder Kante in **Random size** angeben, der Standardwert ist 256.

8.  Geben Sie für **Random crop** an, ob das angegebene PIL-Bild an einer zufälligen Stelle zugeschnitten werden soll. Wenn Sie ‚True‘ auswählen, können Sie die gewünschte Ausgabegröße des Zuschnitts in **Random crop size** angeben, der Standardwert ist 224.

9.  Geben Sie für **Random horizontal flip** an, ob das angegebene PIL-Bild zufällig mit einer Wahrscheinlichkeit von 0,5 horizontal gekippt werden soll.

10.  Geben Sie für **Random vertical flip** an, ob das angegebene PIL-Bild zufällig mit einer Wahrscheinlichkeit von 0,5 vertikal gekippt werden soll.

11.  Geben Sie in **Random rotation** an, ob das Bild um einen Winkel gedreht werden soll. Wenn Sie ‚True‘ wählen, können Sie einen Gradbereich angeben, indem Sie **Random rotation degrees** festlegen, was (Grad -, Grad +) bedeutet, der Standardwert ist 0.

12.  Geben Sie in **Random affine** an, ob eine zufällige affine Transformation des Bilds erfolgen soll, ohne Veränderung der Bildmitte. Wenn Sie ‚True‘ wählen, können Sie einen Gradbereich angeben, den Sie in **Random rotation degrees** auswählen können, was (Grad -, Grad +) bedeutet, der Standardwert ist 0.

13.  Geben Sie in **Random grayscale** an, ob das Bild zufällig in Graustufen konvertiert werden soll, mit einer Wahrscheinlichkeit von 0,1.

14.  Geben Sie in **Random perspective** an, ob mit einer Wahrscheinlichkeit von 0,5 eine zufällige Perspektivtransformation des angegebenen PIL-Bilds durchgeführt werden soll.


16.  Stellen Sie eine Verbindung mit dem Modul [Apply Image Transformation](apply-image-transformation.md) her, um die oben angegebene Transformation auf das Eingabe-Bilddataset anzuwenden.

17. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nach dem Abschluss der Transformation finden Sie die transformierten Bilder in der Ausgabe des Moduls [Apply Image Transformation](apply-image-transformation.md).


## <a name="technical-notes"></a>Technische Hinweise  

Weitere Informationen zur Bildtransformation finden Sie unter [https://pytorch.org/docs/stable/torchvision/transforms.html](https://pytorch.org/docs/stable/torchvision/transforms.html).

###  <a name="module-parameters"></a>Modulparameter  

| Name                    | Range   | type    | Standard | BESCHREIBUNG                              |
| ----------------------- | ------- | ------- | ------- | ---------------------------------------- |
| Größe ändern                  | Any     | Boolean | True    | Ändern der Größe des PIL-Eingabebilds auf die angegebene Größe |
| Size                    | >=1     | Integer | 256     | Geben Sie die gewünschte Ausgabegröße an.          |
| Center crop             | Any     | Boolean | True    | Schneidet das angegebene PIL-Bild in der Mitte zu  |
| Crop size               | >=1     | Integer | 224     | Geben Sie die gewünschte Ausgabegröße des Zuschnitts an |
| Pad                     | Any     | Boolean | False   | Auffüllen des angegebenen PIL-Bilds auf allen Seiten mit dem angegebenen Wert von „pad“ |
| Auffüllen                 | >=0     | Integer | 0       | Auffüllen an jeder Kante                   |
| Color jitter            | Any     | Boolean | False   | Helligkeit, Kontrast und Sättigung eines Bilds zufällig ändern |
| Grayscale               | Any     | Boolean | False   | Konvertieren eines Bilds in Graustufen               |
| Random resized crop     | Any     | Boolean | False   | Schneidet das angegebene PIL-Bild auf eine zufällige Größe und ein zufälliges Seitenverhältnis zu |
| Random size             | >=1     | Integer | 256     | Erwartete Ausgabegröße jeder Kante        |
| Random crop             | Any     | Boolean | False   | Beschneidet das angegebene PIL-Bild an einer zufälligen Stelle |
| Random crop size        | >=1     | Integer | 224     | Gewünschte Ausgabegröße des Zuschnitts          |
| Random horizontal flip  | Any     | Boolean | True    | Angegebenes PIL-Bild mit einer angegebenen Wahrscheinlichkeit zufällig horizontal kippen |
| Random vertical flip    | Any     | Boolean | False   | Angegebenes PIL-Bild mit einer angegebenen Wahrscheinlichkeit zufällig vertikal kippen |
| Random rotation         | Any     | Boolean | False   | Drehen des Bilds um einen Winkel                |
| Random rotation degrees | [0,180] | Integer | 0       | Gradbereich zur Auswahl          |
| Random affine           | Any     | Boolean | False   | Zufällige affine Transformation des Bilds ohne Veränderung der Bildmitte |
| Random affine degrees   | [0,180] | Integer | 0       | Gradbereich zur Auswahl          |
| Random grayscale        | Any     | Boolean | False   | Bild mit einer Wahrscheinlichkeit von 0,1 zufällig in Graustufen konvertieren |
| Random perspective      | Any     | Boolean | False   | Führt mit einer Wahrscheinlichkeit von 0,5 eine zufällige Perspektivtransformation des angegebenen PIL-Bilds durch |
| Random erasing          | Any     | Boolean | False   | Wählt mit einer Wahrscheinlichkeit von 0,5 einen rechteckigen Bereich in einem Bild aus und löscht dessen Pixel |

###  <a name="output"></a>Output  

| Name                        | type                    | BESCHREIBUNG                              |
| --------------------------- | ----------------------- | ---------------------------------------- |
| Ausgabebildtransformation | TransformationDirectory | Ausgabebildtransformation, die mit dem Modul **Apply Image Transformation** verbunden werden kann. |

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 