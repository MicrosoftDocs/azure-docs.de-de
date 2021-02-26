---
title: 'ML Studio (Classic): Ausführen von Python-Skripts – Azure'
description: Erfahren Sie, wie Sie das Modul „Execute Python Script“ nutzen können, um Python-Code in Ihren (klassischen) Azure Machine Learning Studio-Experimenten und -Webdiensten zu verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: devx-track-python, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 374589212b230e6b3ce0abcadbad8aa3eb7271fc
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519982"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Ausführen von Python-Machine Learning-Skripts in Azure Machine Learning Studio (klassisch)

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Python ist ein wertvolles Tool im Werkzeugkasten vieler Datenanalysten. Es wird in jeder Phase typischer Workflows beim maschinellen Lernen verwendet, einschließlich des Durchsuchens von Daten, der Featureextraktion, des Modelltrainings und der Modellvalidierung sowie der Bereitstellung.

In diesem Artikel wird beschrieben, wie Sie das Modul „Execute Python Script“ nutzen können, um Python-Code in Ihren (klassischen) Azure Machine Learning Studio-Experimenten und -Webdiensten zu verwenden.

## <a name="using-the-execute-python-script-module"></a>Verwenden des Execute Python Script-Moduls

Die primäre Schnittstelle zu Python in Studio (klassisch) stellt das Modul [Execute Python Script][execute-python-script] dar. Es nimmt bis zu drei Eingaben entgegen und erzeugt bis zu zwei Ausgaben, ähnlich dem Modul [Execute R Script][execute-r-script]. Python-Code wird in das Parameterfeld durch eine besonders benannte Einstiegspunktfunktion mit dem Namen `azureml_main` eingegeben.

![Execute Python Script-Modul](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Python-Beispielcode im Modulparameterfeld](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Eingabeparameter

Eingaben in das Python-Modul werden als Pandas-Datenrahmen verfügbar gemacht. Die `azureml_main`-Funktion akzeptiert bis zu zwei optionale Pandas-Datenrahmen als Parameter.

Die Zuordnung zwischen Eingangsports und Funktionsparametern erfolgt nach Position:

- Der erste verbundene Eingangsport wird dem ersten Parameter der Funktion zugeordnet.
- Der zweite Eingangsport (sofern verbunden) wird dem zweiten Parameter der Funktion zugeordnet.
- Die dritte Eingabe wird zum [Importieren zusätzlicher Python-Module ](#import-modules) verwendet.

Eine ausführlichere Semantik zur Zuordnung der Eingangsports zu Parametern der `azureml_main`-Funktion wird unten dargestellt.

![Tabelle der Eingabeportkonfigurationen und der resultierenden Python-Signatur](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Ausgaberückgabewerte

Die `azureml_main`-Funktion muss einen einzelnen Pandas-Datenrahmen, verpackt in einer Python-[Sequenz](https://docs.python.org/2/c-api/sequence.html) zurückgeben, beispielsweise in einem Tupel, einer Liste oder einem NumPy-Array. Das erste Element dieser Sequenz wird an den ersten Ausgabeport des Moduls zurückgegeben. Der zweite Ausgabeport des Moduls wird für [Visualisierungen](#visualizations) verwendet und erfordert keinen Rückgabewert. Dieses Schema ist unten dargestellt.

![Zuordnung von Eingabeports zu Parametern und von Rückgabewerten zu Ausgabeports](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Übersetzung von Eingabe- und Ausgabedatentypen

Studio-Datasets sind nicht das gleiche wie Panda-Datenrahmen. Das hat zur Folge, dass Eingabedatasets in Studio (klassisch) in Pandas-Datenrahmen konvertiert werden, und Ausgaberatenrahmen werden in (klassische) Studio-Datasets zurückkonvertiert. Während dieses Konvertierungsvorgangs werden außerdem die folgenden Übersetzungen ausgeführt:

 **Python-Datentyp** | **Studio-Übersetzungsvorgang** |
| --- | --- |
| Zeichenfolgen und numerische Werte| Wie vorliegend übersetzt |
| Pandas ‚N/V‘ | Als ‚Fehlender Wert‘ übersetzt |
| Indexvektoren | Nicht unterstützt* |
| Nicht als Zeichenfolgen vorliegende Spaltennamen | `str` für Spaltennamen aufrufen |
| Doppelte Spaltennamen | Numerisches Suffix anfügen: (1), (2), (3) usw.

**Alle Eingabedatenrahmen in der Python-Funktion haben stets einen numerischen 64-Bit-Index von 0 bis zur Anzahl der Zeilen minus 1*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Importieren vorhandener Python-Skriptmodule

Das zum Ausführen von Python-Code verwendete Back-End basiert auf [Anaconda](https://www.anaconda.com/distribution/), einer weitverbreiteten wissenschaftlichen Python-Distribution. Sie wird mit knapp 200 der gängigsten Python-Pakete geliefert, die für datenorientierte Workloads verwendet werden. Studio (klassisch) bietet aktuell keine Unterstützung für Paketverwaltungssystem wie Pip oder Conda zum Installieren und Verwalten externer Bibliotheken.  Wenn die Notwendigkeit besteht, zusätzliche Bibliotheken einzubinden, verwenden Sie das folgende Szenario als Richtschnur.

Ein häufiger Anwendungsfall besteht in der Einbeziehung vorhandener Python-Skripts in Studio-Experimenten (klassisch). Das Modul [Execute Python Script][execute-python-script] nimmt eine ZIP-Datei mit Python-Modulen am dritten Eingabeport entgegen. Die Datei wird zur Laufzeit vom Ausführungs-Framework entpackt, und die Inhalte werden dem Bibliothekspfad des Python-Interpreters hinzugefügt. Die `azureml_main` -Einstiegspunktfunktion kann diese Module anschließend direkt importieren. 

Ein Beispiel wäre etwa die Datei „Hello.py“ mit einer einfachen Funktion vom Typ „Hello, World“.

![Benutzerdefinierte Funktion in der Datei „Hello.py“](./media/execute-python-scripts/figure4.png)

Als Nächstes erstellen wir die Datei „Hello.zip“, die „Hello.py“ enthält:

![ZIP-Datei mit benutzerdefiniertem Python-Code](./media/execute-python-scripts/figure5.png)

Laden Sie ZIP-Datei als Dataset in Studio (klassisch) hoch. Erstellen und führen Sie anschließend ein Experiment aus, das den Python-Code in der Datei „Hello.zip“ verwendet, indem Sie ihn mit dem dritten Eingabeport des **Execute Python Script**-Moduls verbinden, wie in der folgenden Abbildung dargestellt.

![Beispielexperiment mit „Hello.zip“ als Eingabe eines Execute Python Script-Moduls](./media/execute-python-scripts/figure6a.png)

![Als ZIP-Datei hochgeladener benutzerdefinierter Python-Code](./media/execute-python-scripts/figure6b.png)

Die Modulausgabe zeigt, dass die ZIP-Datei entpackt und die Funktion `print_hello` ausgeführt wurde.

![Modulausgabe, die eine benutzerdefinierte Funktion darstellt](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Zugreifen auf Azure Storage Blobs

Auf Daten, die in einem Azure Blob Storage-Konto gespeichert sind, können Sie mit diesen Schritten zugreifen:

1. Laden Sie das [Azure Blob Storage-Paket für Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) lokal herunter.
1. Laden Sie die ZIP-Datei als Dataset in Ihren (klassischen) Studio-Arbeitsbereich hoch.
1. Erstellen Sie mit `protocol='http'` Ihr BlobService-Objekt.

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Deaktivieren Sie auf der Einstellungsregisterkarte **Konfiguration** die Option **Sichere Übertragung erforderlich**

![Deaktivieren von „Sichere Übertragung erforderlich“ im Azure-Portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Operationalisieren von Python-Skripts

Alle [Execute Python Script][execute-python-script]-Module in einem Bewertungsexperiment werden bei der Veröffentlichung als Webdienst aufgerufen. Die Abbildung unten zeigt beispielsweise ein Bewertungsexperiment, das den Code zum Auswerten eines einzelnen Python-Ausdrucks enthält.

![Studio-Arbeitsbereich für einen Webdienst](./media/execute-python-scripts/figure3a.png)

![Python Pandas-Ausdruck](./media/execute-python-scripts/python-script-with-python-pandas.png)

Ein aus diesem Experiment erstellter Webdienst würde die folgenden Aktionen ausführen:

1. Annehmen eines Python-Ausdrucks als Eingabe (in Form einer Zeichenfolge)
1. Senden des Python-Ausdrucks an den Python-Interpreter
1. Rückgabe einer Tabelle, die sowohl den Ausdruck als auch das bewertete Ergebnis enthält.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Arbeiten mit Visualisierungen

Mit MatplotLib erstellte Plots können vom Modul [Execute Python Script][execute-python-script] zurückgegeben werden. Allerdings werden Plots nicht, wie bei R, automatisch in Bilder umgeleitet. Daher muss der Benutzer Plots explizit als PNG-Dateien speichern.

Um Bilder aus MatplotLib zu generieren, müssen Sie die folgenden Schritte ausführen:

1. Stellen Sie das Back-End vom Qt-basierten Standardrenderer auf „AGG“ um.
1. Erstellen Sie ein neues Abbildungsobjekt.
1. Rufen Sie die Achse ab, und generieren Sie alle zugehörigen Plots.
1. Speichern Sie die Abbildung als PNG-Datei.

Dieser Prozess wird in den folgenden Abbildungen dargestellt, in denen mithilfe der „scatter_matrix“-Funktion in Pandas eine Punktdiagramm-Matrix erstellt wird.

![Code zum Speichern von MatplotLib-Abbildungen in Bildern](./media/execute-python-scripts/figure-v1-8.png)

![Klicken Sie auf einem Execute Python Script-Modul auf „Visualisieren“, um die Abbildungen anzuzeigen](./media/execute-python-scripts/figure-v2-9a.png)

![Visualisieren von Plots für ein Beispielexperiment mithilfe von Python-Code](./media/execute-python-scripts/figure-v2-9b.png)

Es ist möglich, durch Speichern in verschiedenen Bilddateien mehrere Abbildungen zurückzugeben. Die Studio-Runtime (klassisch) nimmt alle Images auf und verkettet sie zum Zweck der Visualisierung.

## <a name="advanced-examples"></a>Fortgeschrittene Beispiele

Die in Studio (klassisch) installierte Anaconda-Umgebung enthält gängige Pakete wie NumPy, SciPy und Scikits-Learn. Diese Pakete können effektiv für die Datenverarbeitung in einer Machine Learning-Pipeline verwendet werden.

Als Beispiel veranschaulichen der folgende Versuch und das Skript die Verwendung von Ensemble-Learnern in Scikits-Learn, um Featurewichtigkeitsbewertungen für ein Dataset zu berechnen. Anhand der Ergebnisse kann dann eine überwachte Featureauswahl durchgeführt werden, bevor diese in ein anderes ML-Modell aufgenommen werden.

Dies ist die Python-Funktion zum Berechnen der Wichtigkeitsbewertungen und zum Sortieren der Features anhand der Ergebnisse:

![Funktion zum Klassifizieren von Features nach Bewertungen](./media/execute-python-scripts/figure8.png)

Im folgenden Experiment werden dann die Wichtigkeitsbewertungen der Features berechnet und im Dataset „Pima Indian Diabetes“ in Azure Machine Learning Studio (Classic) ausgegeben:

![Versuch zum Klassifizieren von Features im Dataset „Pima Indian Diabetes“ mithilfe von Python](./media/execute-python-scripts/figure9a.png)

![Visualisierung der Ausgabe des Execute Python Script-Moduls](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Einschränkungen

Für das Modul [Execute Python Script][execute-python-script] gelten derzeit folgende Einschränkungen:

### <a name="sandboxed-execution"></a>Sandbox-Ausführung

Die Python-Laufzeit befindet sich derzeit im Sandbox-Modus und ermöglicht keinen dauerhaften Zugriff auf das Netzwerk oder auf das lokale Dateisystem. Alle lokal gespeicherten Dateien sind isoliert und werden nach Abschluss des Moduls gelöscht. Auf dem Computer, auf dem er ausgeführt wird, kann der Python-Code nur auf das aktuelle Verzeichnis und die zugehörigen Unterverzeichnisse zugreifen.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Keine hoch entwickelte Unterstützung für Entwicklung und Debuggen

IDE-Features wie Intellisense und Debuggen werden vom Python-Modul derzeit nicht unterstützt. Sollte zur Laufzeit ein Fehler des Moduls auftreten, ist die komplette Python-Stapelüberwachung verfügbar. Diese muss jedoch im Ausgabeprotokoll für das Modul angezeigt werden. Derzeit wird empfohlen, dass Sie Python-Skripts in einer Umgebung wie IPython entwickeln und debuggen und den Code anschließend in das Modul importieren.

### <a name="single-data-frame-output"></a>Ausgabe in einem einzelnen Datenrahmen

Der Python-Einstiegspunkt kann nur einen einzelnen Datenrahmen als Ausgabe zurückgeben. Derzeit ist es nicht möglich, beliebige Python-Objekte wie z. B. trainierte Modelle direkt an die (klassische) Studio-Runtime zurückzugeben. Wie beim Modul [Execute R Script][execute-r-script], das die gleiche Einschränkung aufweist, ist es in vielen Fällen möglich, Objekte in ein Bytearray einzubetten und dieses innerhalb eines Datenrahmens zurückzugeben.

### <a name="inability-to-customize-python-installation"></a>Keine Möglichkeit zum Anpassen der Python-Installation

Derzeit besteht die einzige Möglichkeit zum Hinzufügen benutzerdefinierter Python-Module über den weiter oben beschriebenen ZIP-Dateimechanismus. Während dies bei kleinen Modulen machbar ist, ist dieser Ansatz für große Module (vor allem Module mit systemeigenen DLLs) oder eine große Anzahl von Modulen umständlich.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie im [Python Developer Center](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: /azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script