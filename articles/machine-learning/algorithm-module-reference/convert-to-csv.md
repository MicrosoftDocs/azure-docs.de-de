---
title: 'Convert to CSV: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul Convert to CSV im Azure Machine Learning-Designer ein Dataset in eine CSV-Datei konvertieren, die später wiederverwendet werden kann.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: cc58689e30e9b03e490c0871f3decd50372371fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421922"
---
# <a name="convert-to-csv-module"></a>Modul „Convert to CSV (Konvertieren in das CSV-Format)

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Nutzen Sie dieses Modul, um ein Dataset in ein CSV-Format konvertieren, das heruntergeladen, exportiert oder mit R- oder Python-Skriptmodulen gemeinsam genutzt werden kann.

### <a name="more-about-the-csv-format"></a>Weitere Informationen zum CSV-Format 

Das CSV-Format (Comma-Separated Values) ist ein Dateiformat, das von vielen externen Tools für maschinelles Lernen verwendet wird. CSV ist ein gängiges Austauschformat bei der Arbeit mit Open-Source-Sprachen wie R oder Python.

Auch wenn Sie den größten Teil der Arbeit in Azure Machine Learning erledigen, gibt es Situationen, in denen es sinnvoll sein kann, Ihr Dataset in das CSV-Format zu konvertieren, um es in externen Tools zu verwenden. Beispiel:

+ Laden Sie die CSV-Datei herunter, um sie in Excel zu öffnen, oder importieren Sie sie in eine relationale Datenbank.  
+ Speichern Sie die CSV-Datei im Cloudspeicher, und verbinden Sie sie mit Power BI, um Visualisierungen zu erstellen.  
+ Verwenden Sie das CSV-Format, um Daten zur Verwendung in R und Python aufzubereiten. 

Wenn Sie einen Datensatz in das CSV-Format konvertieren, wird die CSV-Datei in Ihrem Azure ML-Arbeitsbereich gespeichert. Sie können ein Azure-Speicherhilfsprogramm verwenden, um die Datei direkt zu öffnen und zu verwenden. Sie können auch auf die CSV-Datei im Designer zugreifen, indem Sie das Modul **Convert to CSV** (In CSV konvertieren) und dann das Histogrammsymbol unter der Registerkarte **Ausgaben** im rechten Fensterbereich auswählen, um die Ausgabe anzuzeigen. Sie können die CSV-Datei aus dem Ergebnisordner in ein lokales Verzeichnis herunterladen.  

## <a name="how-to-configure-convert-to-csv"></a>Konfigurieren von „Convert to CSV“


1.  Fügen Sie Ihrer Pipeline das Modul „Convert to CSV“ (In CSV konvertieren) hinzu. Das Modul befindet sich im Designer in der Gruppe **Datentransformation**. 

2. Verbinden Sie es mit einem beliebigen Modul, das ein Dataset ausgibt.   
  
3.  Übermitteln Sie die Pipeline.

### <a name="results"></a>Ergebnisse
  

Wählen Sie die Registerkarte **Ausgaben** im rechten Bereich von **Convert to CSV** (In CSV konvertieren) aus, und wählen Sie dann eines dieser Symbole unter den **Portausgaben** aus.  

+ **Dataset registrieren**: Wählen Sie das Symbol aus, und speichern Sie die CSV-Datei als separates Dataset in den Azure ML-Arbeitsbereich zurück. Sie finden das Dataset als Modul in der Modulstruktur unter dem Abschnitt **My Datasets** (Meine Datasets).

 + **Ausgabe anzeigen**: Wählen Sie das Augensymbol aus, und folgen Sie den Anweisungen, um den Ordner **Results_dataset** zu durchsuchen und die Datei „data.csv“ herunterzuladen.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 