---
title: 'Export Data (Daten exportieren): Modulreferenz'
titleSuffix: Azure Machine Learning
description: Verwenden Sie das Modul Export Data im Azure Machine Learning-Designer, um Ergebnisse und Zwischendaten außerhalb von Azure Machine Learning zu speichern.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/28/2020
ms.openlocfilehash: c6e3d56958168cd279c98a4ba4c021c2362c2694
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421310"
---
# <a name="export-data-module"></a>Modul „Export Data“ (Daten exportieren)

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie dieses Modul, um Ergebnisse, Zwischendaten und Arbeitsdaten aus Ihren Pipelines in Cloudspeicherzielen zu speichern. 

Dieses Modul unterstützt das Exportieren von Daten in folgende Clouddatendiensten:

- Azure-Blobcontainer
- Azure-Dateifreigabe
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Azure SQL-Datenbank

Bevor Sie Ihre Daten exportieren können, müssen Sie einen Datenspeicher in Ihrem Azure Machine Learning-Arbeitsbereich registrieren. Weitere Informationen finden Sie unter [Zugreifen auf Daten in Azure Storage-Diensten](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Gewusst wie: Konfigurieren von „Export Data“

1. Fügen Sie das Modul **Export Data** zu Ihrer Pipeline im Designer hinzu. Sie finden dieses Modul in der Kategorie **Input and Output** (Eingabe und Ausgabe).

1. Verbinden Sie **Export Data** mit dem Modul, das die zu exportierenden Daten enthält.

1. Wählen Sie **Export Data** aus, um den Bereich **Properties** (Eigenschaften) zu öffnen.

1. Wählen Sie für **Datastore** (Datenspeicher) in der Dropdownliste einen vorhandenen Datenspeicher aus. Sie können auch einen neuen Datenspeicher erstellen. Wie dies funktioniert, erfahren Sie unter [Zugreifen auf Daten in Azure Storage-Diensten](../how-to-access-data.md).

    > [!NOTE]
    > Das Exportieren von Daten eines bestimmten Datentyps in eine SQL-Datenbankspalte, für die ein anderer Datentyp angegeben ist, wird nicht unterstützt. Die Zieltabelle muss nicht vorab existieren.

1. Das Kontrollkästchen **Ausgabe erneut generieren** legt fest, ob das Modul zum erneuten Generieren der Ausgabe zur Laufzeit ausgeführt werden soll. 

    Die Option ist standardmäßig deaktiviert. Dies bedeutet, dass das System die Ausgabe der letzten Ausführung wiederverwendet, um die Laufzeit zu verkürzen, wenn das Modul bereits mit denselben Parametern ausgeführt wurde. 

    Wenn sie ausgewählt ist, führt das System das Modul erneut aus, um die Ausgabe neu zu generieren.

1. Geben Sie den Pfad im Datenspeicher an, an dem sich die Daten befinden. Der Pfad ist ein relativer Pfad. Leere Pfade oder URL-Pfade sind nicht zulässig.


1. Wählen Sie für **File format** (Dateiformat) das Format aus, in dem die Daten gespeichert werden sollen.
 
1. Übermitteln Sie die Pipeline.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 
