---
title: 'Python-Skript ausführen: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie das Modul „Python-Skript ausführen“ in Azure Machine Learning Service zum Ausführen von Python-Code verwendet wird.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6e61ed5a18e69b107b78bf2042de21d14cd6beb5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027756"
---
# <a name="execute-python-script-module"></a>Execute Python Script-Modul

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für den Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul zum Ausführen von Python-Code. Weitere Informationen zur Architektur und den Entwurfsprinzipien von Python finden Sie [im folgenden Artikel.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Mit Python können Sie Aufgaben ausführen, die aktuell von vorhandenen Modulen nicht unterstützt werden, beispielsweise:

+ Visualisieren von Daten mit `matplotlib`
+ Verwenden von Python-Bibliotheken zum Auflisten von Datasets und Modellen in Ihrem Arbeitsbereich
+ Lesen, Laden und Bearbeiten von Daten aus Quellen, die vom Modul [Daten importieren](./import-data.md) nicht unterstützt werden
+ Ausführen eigenen Deep Learning-Codes 


Azure Machine Learning verwendet die Anaconda-Distribution von Python, die viele gängige Hilfsprogramme für die Datenverarbeitung enthält. Wir führen Updates der Anaconda-Version automatisch aus. Die aktuelle Version ist:
 -  Anaconda 4.5+-Distribution für Python 3.6 

Dies sind die vorinstallierten Pakete:
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas==0.24.2
- Pillow==6.0.0
- pip==19.0.3
- pyarrow==0.12.1
- pycparser==2.19
- pycryptodomex==3.7.3
- pyrsistent==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- requests==2.21.0
- scikit-learn==0.20.3
- scipy==1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- wheel==0.33.1 

 Um weitere Pakete zu installieren, die nicht in der Liste der vorinstallierten Pakete enthalten sind, beispielsweise *scikit-misc*, fügen Sie Ihrem Skript den folgenden Code hinzu: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Verwendung

Das Modul **Execute Python Script** enthält Python-Beispielcode, den Sie als Ausgangspunkt verwenden können. Um das Modul **Python-Skript ausführen** zu konfigurieren, geben Sie einen Satz von Eingaben und auszuführenden Python-Code im Textfeld **Pytho-Skript** ein.

1. Fügen Sie Ihrem Experiment das Modul **Python-Skript ausführen** hinzu.

2. Fügen Sie in **Dataset1** alle Datasets aus der Schnittstelle hinzu, die Sie als Eingabe verwenden möchten. Verweisen Sie auf dieses Dataset in Ihrem Python-Skript als **DataFrame1**.

    Die Verwendung eines Datasets ist optional, wenn Sie Daten mithilfe von Python generieren oder Python-Code zum direkten Importieren der Daten in das Modul verwenden möchten.

    Dieses Modul unterstützt das Hinzufügen eines zweiten Datensets in **Dataset2**. Verweisen Sie auf das zweite Dataset in ihrem Python-Skript als „DataFrame2“.

    In Azure Machine Learning gespeicherte Datasets werden automatisch in **Pandas** data.frames konvertiert, wenn sie mit diesem Modul geladen werden.

    ![Eingabezuordnung zum Ausführen von Python-Code](media/module/python-module.png)

4. Um neue Python-Pakete oder Code einzuschließen, fügen Sie die ZIP-Datei, die diese benutzerdefinierten Ressourcen enthält, in **Script bundle** (Skriptbundle) hinzu. Die Eingabe in **Script bundle** muss eine ZIP-Datei sein, die bereits in Ihren Arbeitsbereich hochgeladen wurde. 

    Während der Ausführung des Experiments kann jede Datei verwendet werden, die im hochgeladenen ZIP-Archiv enthalten ist. Wenn das Archiv eine Verzeichnisstruktur enthält, bleibt die Struktur erhalten, Sie müssen dem Pfad aber ein Verzeichnis mit dem Namen **src** voranstellen.

5. Geben oder fügen Sie in das Textfeld **Python script** (Python-Skript) ein gültiges Python-Skript ein.

    Das Textfeld **Python script** (Python-Skript) ist mit einigen Anweisungen in Kommentaren und Beispielcode für den Datenzugriff und die Datenausgabe voraufgefüllt. **Sie müssen diesen Code bearbeiten oder ersetzen.** Achten Sie darauf, die Python-Konventionen zu Einzug und Schreibweise zu beachten.

    + Das Skript muss eine Funktion mit dem Namen `azureml_main` als Einstiegspunkt für dieses Modul enthalten.
    + Die Einstiegspunktfunktion kann bis zu zwei Eingabeargumente enthalten: `Param<dataframe1>` und `Param<dataframe2>`
    + ZIP-Dateien, die mit dem dritten Eingabeport verbunden sind, werden entzippt und im Verzeichnis `.\Script Bundle` gespeichert, das dem Python-`sys.path` ebenfalls hinzugefügt wird. 

    Wenn Ihre ZIP-Datei `mymodule.py` enthält, importieren Sie sie daher mit `import mymodule`.

    + Zwei Datasets können an die Schnittstelle zurückgegeben werden, dabei muss es sich um eine Sequenz vom Typ `pandas.DataFrame` handeln. Sie können weitere Ausgaben in Ihrem Python-Code erstellen und sie direkt in den Azure-Speicher schreiben.

6. Führen Sie das Experiment aus, oder wählen Sie das Modul aus, und klicken Sie auf **Run selected** (Auswahl ausführen), um nur das Python-Skript auszuführen.

    Alle Daten und der gesamte Code werden in einen virtuellen Computer geladen und unter Verwendung der angegebenen Python-Umgebung ausgeführt.

## <a name="results"></a>Ergebnisse

Die Ergebnisse aller Berechnungen, die vom eingebetteten Python-Code ausgeführt werden, müssen als ein pandas.DataFrame bereitgestellt werden, der automatisch in das Datasetformat von Azure Machine Learning konvertiert wird, sodass Sie die Ergebnisse zusammen mit anderen Modulen im Experiment verwenden können.

Das Modul gibt zwei Datasets zurück:  
  
+ **Results Dataset 1** (Ergebnisdataset 1), durch den ersten zurückgegebenen pandas-Datenrahmen im Python-Skript definiert

+ **Results Dataset 2** (Ergebnisdataset 2), durch den zweiten zurückgegebenen pandas-Datenrahmen im Python-Skript definiert


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 