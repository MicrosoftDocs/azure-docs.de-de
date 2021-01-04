---
title: Zugreifen auf Datasets mit der Python-Clientbibliothek – Team Data Science-Prozess
description: Installieren und verwenden Sie die Python-Clientbibliothek und verwalten Sie Azure Machine Learning-Daten sicher aus einer lokalen Python-Umgebung.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 497b8f5598cf7aa7720f47863d465f5e29789b07
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321945"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Zugriff auf Datasets mit Python mithilfe der Azure Machine Learning Python-Clientbibliothek
Die Vorschau von Microsoft Azure Machine Learning Python-Clientbibliothek kann einen sicheren Zugriff auf Ihre Azure Machine Learning-Datasets aus einer lokalen Python-Umgebung aktivieren und ermöglicht die Erstellung und Verwaltung von Datasets im Arbeitsbereich.

Dieses Thema enthält Anweisungen zu:

* Installation der Machine Learning Python-Clientbibliothek
* Zugriff auf und Hochladen von Datasets, einschließlich Anweisungen zur Autorisierung des Zugriffs auf Azure Machine Learning Datasets aus Ihrer lokalen Python-Umgebung
* Zugriff auf Zwischen-Datasets von Experimenten
* Verwendung der Python-Clientbibliothek zum Auflisten von Datasets, Zugriff auf Metadaten, Lesen des Inhalts eines Datasets, Erstellen neuer Datasets und Aktualisieren vorhandener Datasets

## <a name="prerequisites"></a><a name="prerequisites"></a>Voraussetzungen
Die Python-Clientbibliothek wurde in den folgenden Umgebungen getestet:

* Windows, Mac und Linux
* Python 2.7, 3.3 und 3.4

Es besteht eine Abhängigkeit von folgenden Paketen:

* requests
* Python-dateutil
* pandas

Es empfiehlt sich, eine Python-Distribution wie z.B. [Anaconda](https://www.anaconda.com/) oder [Canopy](https://store.enthought.com/downloads/) zu verwenden, die aus Python, IPython stammt und mit den drei oben aufgeführten Paketen geliefert und installiert wird. Obwohl IPython nicht unbedingt erforderlich ist, ist es eine optimale Umgebung für die interaktive Bearbeitung und Visualisierung von Daten.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Installation der Azure Machine Learning Python-Clientbibliothek
Installieren Sie die Azure Machine Learning Python-Clientbibliothek zum Ausführen der Aufgaben in diesem Thema. Diese Bibliothek ist über [Python Package Index](https://pypi.python.org/pypi/azureml) verfügbar. Um es in Ihrer Python-Umgebung zu installieren, führen Sie den folgenden Befehl Ihrer lokalen Python-Umgebung aus:

```console
pip install azureml
```

Alternativ können Sie von den Quellen auf [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python) herunterladen und installieren.

```console
python setup.py install
```

Wenn Sie Git auf Ihrem Computer installiert haben, können Sie mit Pip direkt vom Git-Repository installieren:

```console
pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git
```

## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Verwenden von Codeausschnitten, um auf Datasets zuzugreifen
Mit der Python-Clientbibliothek erhalten Sie programmgesteuerten Zugriff auf Ihre vorhandenen Datasets aus durchgeführten Experimenten.

Über die Studio Weboberfläche von Azure Machine Learning Studio (klassisch) können Sie Codeausschnitte generieren, die alle notwendigen Informationen zum Herunterladen und Deserialisieren von Datasets als Pandas DataFrame-Objekte auf Ihrem lokalen Computer enthalten.

### <a name="security-for-data-access"></a><a name="security"></a>Sicherheit für den Datenzugriff
Die von Azure Machine Learning Studio (klassisch) bereitgestellten Codeausschnitte zur Verwendung mit Python-Clientbibliotheken enthalten Ihre Arbeitsbereichs-ID und Ihr Authentifizierungstoken. Diese bieten vollständigen Zugriff auf den Arbeitsbereich und müssen wie ein Kennwort geschützt werden.

Aus Sicherheitsgründen ist die Codeausschnitt-Funktionalität nur für Benutzer verfügbar, deren Rolle als **Besitzer** für den Arbeitsbereich eingestellt ist. Die Rolle wird in Azure Machine Learning Studio (klassisch) auf der Seite **BENUTZER** unter **Einstellungen** angezeigt.

![Screenshot mit den Einstellungen auf der Seite „USERS“ (Benutzer) von Azure Machine Learning Studio][security]

Wenn Ihre Rolle nicht als **Besitzer** festgelegt ist, können Sie darum bitten, erneut als Besitzer eingeladen zu werden, oder den Besitzer des Arbeitsbereichs darum bitten, Ihnen den Codeausschnitt zu stellen.

Um das Autorisierungstoken zu erhalten, können Sie eine der folgenden Optionen auswählen:

* Fragen Sie bei einem Besitzer nach einem Token. Besitzer können auf ihr Autorisierungstoken auf der Seite „Einstellungen“ ihres Arbeitsbereichs in Azure Machine Learning Studio (klassisch) zugreifen. Wählen Sie im linken Bereich **Einstellungen** , und klicken Sie auf **AUTORISIERUNGSTOKEN** , um die primären und sekundären Token zu sehen. Im Codebeispiel können zwar entweder das primäre oder das sekundäre Autorisierungstoken verwendet werden, es empfiehlt sich jedoch, dass Besitzer nur das sekundäre Autorisierungstoken freigeben.

   ![Autorisierungstoken](./media/python-data-access/ml-python-access-settings-tokens.png)

* Fordern Sie an, dass Sie höher gestuft werden, um die Rolle „Besitzer“ zu erhalten: Ein aktueller Besitzer des Arbeitsbereichs muss Sie zuerst aus dem Arbeitsbereich entfernen und dann erneut als Besitzer einladen.

Wenn Entwickler die Arbeitsplatz-ID und das Autorisierungstoken abgerufen haben, können sie unabhängig von ihrer Rolle mithilfe des Codeausschnitts auf den Arbeitsbereich zugreifen.

Autorisierungstoken werden auf der Seite **AUTORISIERUNGSTOKEN** unter **EINSTELLUNGEN** verwaltet. Sie können sie erneut generieren, aber dieses Verfahren hebt den Zugriff auf das vorherige Token auf.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Zugriff auf Datasets aus einer lokalen Python-Anwendung
1. Klicken Sie in Machine Learning Studio (klassisch) auf der Navigationsleiste auf der linken Seite auf **DATASETS**.
2. Wählen Sie das Dataset, auf das Sie zugreifen möchten. Wählen Sie eines der Datasets aus der Liste **MEINE DATASETS** oder aus der Liste **BEISPIELE** aus.
3. Klicken Sie auf der unteren Symbolleiste auf **Datenzugriffscode generieren**. Diese Schaltfläche ist deaktiviert, wenn die Daten ein mit der Python-Clientbibliothek nicht kompatibles Format haben.
   
    ![Screenshot mit Datasets mit der Schaltfläche US GENERATE DATA ACCESS CODE („Datei für den Datenzugriffscode generieren“)][datasets]
4. Wählen Sie den Codeausschnitt aus dem Fenster, das angezeigt wird und kopieren Sie ihn in die Zwischenablage.
   
    ![Schaltfläche „Generate data access code“ (Datei für den Datenzugriffscode generieren)][dataset-access-code]
5. Fügen Sie den Code in das Notebook Ihrer lokalen Python-Anwendung ein.
   
    ![Einfügen von Code in das Notebook][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Zugriff auf Zwischen-Datasets von Machine Learning-Experimenten
Nachdem ein Experiment in Machine Learning Studio (klassisch) ausgeführt wurde, ist es möglich, von den Ausgabeknoten der Module auf die Zwischen-Datasets zuzugreifen. Zwischen-Datasets sind Daten, die für Zwischenschritte erstellt und verwendet wurden, wenn ein Modell-Werkzeug ausgeführt wurde.

Auf Zwischen-Datasets kann zugegriffen werden, solange das Datenformat mit der Python-Clientbibliothek kompatibel ist.

Die folgenden Formate werden unterstützt (Konstanten für diese Formate sind der `azureml.DataTypeIds`-Klasse):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Sie können das Format bestimmen, indem Sie mit dem Mauszeiger über einen Modulausgabeknoten fahren. Er wird zusammen mit den Knotennamen in einer QuickInfo angezeigt.

Einige der Module, wie z. B. das Modul [Split][split] geben ein Format mit dem Namen `Dataset` aus, das von der Python-Clientbibliothek nicht unterstützt wird.

![Dataset-Format][dataset-format]

Sie müssen ein Konvertierungsmodul wie z. B. [Convert to CSV][convert-to-csv] verwenden, um eine Ausgabe in einem unterstützten Format abzurufen.

![GenericCSV-Format][csv-format]

Die folgenden Schritte zeigen ein Beispiel, das ein Experiment erstellt, dieses ausführt und auf das Zwischen-Dataset zugreift.

1. Erstellen eines neuen Experiments.
2. Fügen Sie ein Modul **Binäres Klassifizierungsdataset der Einkommenserhebung für Erwachsene** ein.
3. Einfügen eines Moduls [Split][split] und Verbinden seines Eingangs mit dem Ausgang des Datasetmoduls.
4. Einfügen eines Moduls [Convert to CSV][convert-to-csv] und Verbinden seines Eingangs mit einem Ausgang des Moduls [Split][split].
5. Speichern Sie das Experiment, führen Sie es aus und warten Sie, bis der Auftrag abgeschlossen ist.
6. Klicken Sie auf den Ausgangsknoten des Moduls [Convert to CSV][convert-to-csv].
7. Wenn das Kontextmenü angezeigt wird, wählen Sie **Datenzugriffscode generieren**.
   
    ![Kontextmenü][experiment]
8. Wählen Sie im eingeblendeten Fenster den Codeausschnitt aus, und kopieren Sie ihn in die Zwischenablage.
   
    ![Generieren eines Zugriffscodes über das Kontextmenü][intermediate-dataset-access-code]
9. Fügen Sie den Code in Ihrem Notizbuch ein.
   
    ![Einfügen von Code in ein Notebook][ipython-intermediate-dataset]
10. Sie können die Daten mithilfe von Matplotlib visualisieren. Dies zeigt ein Histogramm für die Spalte „Alter“:
    
    ![Histogramm][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Verwenden Sie die Machine Learning Python-Clientbibliothek, um auf Datasets zuzugreifen, diese zu lesen, zu erstellen und zu verwalten.
### <a name="workspace"></a>Arbeitsbereich
Der Arbeitsbereich ist der Einstiegspunkt für die Python-Clientbibliothek. Geben Sie zum Erstellen einer Instanz die Klasse `Workspace` mit Ihrer Arbeitsbereich-ID und Ihrem Autorisierungstoken an:

```python
ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
               authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')
```

### <a name="enumerate-datasets"></a>Auflisten von Datasets
Um alle Datasets in einem bestimmten Arbeitsbereich aufzulisten:

```python
for ds in ws.datasets:
    print(ds.name)
```

Um nur die von Benutzern erstellten Datasets aufzulisten:

```python
for ds in ws.user_datasets:
    print(ds.name)
```

Um nur die Beispiel-Datasets aufzulisten:

```python
for ds in ws.example_datasets:
    print(ds.name)
```

Sie können auf ein Dataset nach Namen zugreifen (es wird zwischen Groß-und Kleinschreibung unterschieden):

```python
ds = ws.datasets['my dataset name']
```

Oder Sie können über Index darauf zugreifen:

```python
ds = ws.datasets[0]
```

### <a name="metadata"></a>Metadaten
Datasets enthalten zusätzlich zum Inhalt Metadaten. (Zwischen-Datasets sind eine Ausnahme dieser Regel und verfügen nicht über Metadaten.)

Einige Metadatenwerte werden bei der Erstellung vom Benutzer zugewiesen:

* `print(ds.name)`
* `print(ds.description)`
* `print(ds.family_id)`
* `print(ds.data_type_id)`

Andere sind Werte, die von Azure ML zugewiesen werden:

* `print(ds.id)`
* `print(ds.created_date)`
* `print(ds.size)`

Siehe `SourceDataset` -Klasse für weitere Informationen zu verfügbaren Metadaten.

### <a name="read-contents"></a>Inhalte lesen
Die von Machine Learning Studio (klassisch) automatisch bereitgestellten Codeausschnitte werden automatisch herunterladen und deserialisieren das Dataset zu einem Pandas DataFrame-Objekt. Dies erfolgt mit der `to_dataframe`-Methode:

```python
frame = ds.to_dataframe()
```

Wenn Sie lieber die Rohdaten herunterladen wollen, um die Deserialisierung selbst auszuführen, besteht diese Option. Im Moment ist dies die einzige Option für Formate wie z. B. „ARFF“, die nicht von der Python-Clientbibliothek deserialisiert werden können.

Um die Inhalte als Text zu lesen:

```python
text_data = ds.read_as_text()
```

Um die Inhalte als binäre Informationen zu lesen:

```python
binary_data = ds.read_as_binary()
```

Sie können auch einfach einen Stream zu den Inhalten öffnen:

```python
with ds.open() as file:
    binary_data_chunk = file.read(1000)
```

### <a name="create-a-new-dataset"></a>Erstellen eines neuen Datasets
Mit der Python-Clientbibliothek können Sie Datasets aus Ihrem Python-Programm hochladen. Diese Datasets stehen dann in Ihrem Arbeitsbereich zur Verfügung.

Wenn Sie Ihre Daten in Pandas DataFrame haben, verwenden Sie den folgenden Code:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_dataframe(
    dataframe=frame,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Wenn Ihre Daten bereits serialisiert sind, können Sie Folgendes verwenden:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_raw_data(
    raw_data=raw_data,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Die Python-Clientbibliothek kann einen Pandas DataFrame in die folgenden Formate serialisieren (Konstanten dafür sind in der `azureml.DataTypeIds`-Klasse):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Aktualisieren eines vorhandenen Datasets
Wenn Sie versuchen, ein neues Dataset mit einem Namen hochzuladen, der mit einem vorhandenen Dataset übereinstimmt, erhalten Sie einen Konfliktfehler.

Um ein vorhandenes Dataset zu aktualisieren, müssen Sie zuerst einen Verweis auf das vorhandene Dataset abrufen:

```python
dataset = ws.datasets['existing dataset']

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Verwenden Sie dann `update_from_dataframe` zur Serialisierung und ersetzen Sie den Inhalt des Datasets auf Azure:

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(frame2)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Wenn Sie die Daten in ein anderes Format serialisieren möchten, geben Sie einen Wert für den optionalen `data_type_id` Parameter an.

```python
from azureml import DataTypeIds

dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    data_type_id=DataTypeIds.GenericTSV,
)

print(dataset.data_type_id) # 'GenericTSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Sie können optional eine neue Beschreibung festlegen, indem Sie einen Wert für den `description` Parameter angeben.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    description='data up to feb 2015',
)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to feb 2015'
```

Sie können optional einen neuen Namen festlegen, indem Sie einen Wert für den `name` Parameter angeben. Von nun an rufen Sie das Dataset nur mithilfe des neuen Namens ab. Der folgende Code aktualisiert Daten, Namen und Beschreibung.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    name='existing dataset v2',
    description='data up to feb 2015',
)

print(dataset.data_type_id)                    # 'GenericCSV'
print(dataset.name)                            # 'existing dataset v2'
print(dataset.description)                     # 'data up to feb 2015'

print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
print(ws.datasets['existing dataset'].name)    # IndexError
```

Die Parameter `data_type_id`, `name` und `description` sind optional und werden standardmäßig auf ihren vorherigen Wert festgelegt. Der Parameter `dataframe` ist immer erforderlich.

Wenn Ihre Daten bereits serialisiert sind, verwenden Sie `update_from_raw_data` statt `update_from_dataframe`. Wenn Sie `raw_data` anstelle von `dataframe` übergeben, funktioniert dies auf ähnliche Weise.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: /azure/machine-learning/studio-module-reference/convert-to-csv
[split]: /azure/machine-learning/studio-module-reference/split-data