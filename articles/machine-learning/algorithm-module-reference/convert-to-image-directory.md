---
title: Konvertieren in ein Bildverzeichnis
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie das Modul „Convert to Image Directory“ (In Bildverzeichnis konvertieren) verwenden, um ein Dataset in das Bildverzeichnisformat zu konvertieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: 1489ce74da2ecff5212feb5a1a2e3c9151b73424
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555582"
---
# <a name="convert-to-image-directory"></a>Konvertieren in ein Bildverzeichnis

In diesem Artikel erfahren Sie, wie Sie das Modul „Convert to Image Directory“ (In Bildverzeichnis konvertieren) verwenden, um ein Bilddataset in den Datentyp *Bildverzeichnis* zu konvertieren. Hierbei handelt es sich um ein standardisiertes Datenformat für bildbezogene Aufgaben wie etwa die Bildklassifizierung im Azure Machine Learning-Designer.

## <a name="how-to-use-convert-to-image-directory"></a>Verwenden von „Convert to Image Directory“ (In Bildverzeichnis konvertieren)  

1. Bereiten Sie zunächst Ihren Bilddataset vor. 

    Für beaufsichtigtes Lernen müssen Sie die Bezeichnung des Trainingsdatasets angeben. Die Bilddatasetdatei sollte die folgende Struktur aufweisen:
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    Im Bilddatasetordner gibt es mehrere Unterordner. Jeder Unterordner enthält Bilder jeweils einer Kategorie. Die Namen von Unterordnern werden als Bezeichnungen für Aufgaben wie die Bildklassifizierung betrachtet. Weitere Informationen finden Sie unter [Torchvision-Datasets](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder).

    > [!WARNING]
    > Derzeit werden von der Datenbeschriftung exportierte Datasets im Designer nicht unterstützt.

    Bilder mit diesen Erweiterungen (in Kleinbuchstaben) werden unterstützt: „.jpg“, „.jpeg“, „.png“, „.ppm“, „.bmp“, „.pgm“, „.tif“, „.tiff“, „.webp“. Sie können auch mehrere Arten von Bildern in einem Ordner speichern. Es ist nicht notwendig, in jedem Kategorieordner die gleiche Anzahl von Bildern zu enthalten.

    Sie können entweder den Ordner oder eine komprimierte Datei mit den Erweiterungen „.zip“, „.tar“, „.gz“ und „.bz2“ verwenden. **Komprimierte Dateien werden für eine bessere Leistung empfohlen.** 
    
    ![Bildbeispieldataset](./media/module/image-sample-dataset.png)

    > [!NOTE]
    > Für Rückschlüsse darf der Bilddatasetordner nur nicht klassifizierte Bilder enthalten.

1. [Registrieren Sie das Bilddataset als Dateidataset](../how-to-create-register-datasets.md) in Ihrem Arbeitsbereich, da die Eingabe des Moduls „Convert to Image Directory“ (In Bildverzeichnis konvertieren) ein **Dateidataset** sein muss.

1. Fügen Sie das registrierte Bilddataset zur Canvas hinzu. Sie finden Ihr registriertes Dataset in der Kategorie **Datasets** in der Modulliste im linken Teil der Canvas. Derzeit unterstützt der Designer die Visualisierung von Bilddatasets nicht.

    > [!WARNING]
    > Bilddatasets können **nicht** mithilfe des Moduls **Daten importieren** importiert werden, da das Modul **Daten importieren** Daten als DataFrame-Verzeichnis ausgibt und die Daten dann nur die Dateipfadzeichenfolge enthalten.

1. Fügen Sie der Canvas das Modul **Convert to Image Directory** (In Bildverzeichnis konvertieren) hinzu. Dieses Modul befindet sich in der Modulliste in der Kategorie „Maschinelles Sehen/Transformation von Bilddaten“. Verbinden Sie es mit dem Bilddataset.
    
3.  Übermitteln Sie die Pipeline. Dieses Modul kann sowohl auf einer GPU als auch auf einer CPU ausgeführt werden.

## <a name="results"></a>Ergebnisse

Das Modul **Convert to Image Directory** (In Bildverzeichnis konvertieren) gibt Daten im **Bildverzeichnisformat** aus, und die Daten können mit anderen bildbezogenen Modulen verbunden werden, die ebenfalls das Bildverzeichnisformat als Eingabeportformat haben.

![Konvertieren in Bildverzeichnisausgabe](./media/module/convert-to-image-directory-output.png)

## <a name="technical-notes"></a>Technische Hinweise 

###  <a name="expected-inputs"></a>Erwartete Eingaben  

| Name          | type                  | Beschreibung   |
| ------------- | --------------------- | ------------- |
| Eingabedataset | AnyDirectory, ZipFile | Eingabedataset |

###  <a name="output"></a>Output  

| Name                   | type           | Beschreibung            |
| ---------------------- | -------------- | ---------------------- |
| Ausgabe-Bildverzeichnis | ImageDirectory | Ausgabe-Bildverzeichnis |

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an.