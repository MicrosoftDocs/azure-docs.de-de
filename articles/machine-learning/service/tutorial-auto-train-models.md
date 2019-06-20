---
title: 'Regressionsmodelltutorial: Automatisiertes maschinelles Lernen'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie ein Machine Learning-Modell mithilfe des automatisierten maschinellen Lernens erstellen. Azure Machine Learning kann die Datenvorverarbeitung, Algorithmusauswahl und Hyperparameterauswahl automatisiert für Sie ausführen. Das endgültige Modell wird dann mit Azure Machine Learning Service bereitgestellt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: trbye
ms.date: 04/11/2019
ms.custom: seodec18
ms.openlocfilehash: 907383c057c0c1eebee6550a0d1e9c75dd88513c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417293"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>Tutorial: Erstellen Ihres Regressionsmodells mit automatisiertem Machine Learning

Dieses Tutorial ist der **zweite Teil einer zweiteiligen Reihe**. Im vorherigen Tutorial haben Sie [die New Yorker Taxidaten für die Regressionsmodellierung vorbereitet](tutorial-data-prep.md).

Nun können Sie damit beginnen, Ihr Modell mithilfe von Azure Machine Learning Service zu erstellen. In diesem Teil des Tutorials verwenden Sie die vorbereiteten Daten, um automatisch ein Regressionsmodell für die Vorhersage von Taxifahrtkosten zu generieren. Mithilfe der Funktionen für automatisiertes maschinelles Lernen des Diensts definieren Sie Ihre Ziele und Einschränkungen für das maschinelle Lernen. Sie starten den automatisierten Prozess für maschinelles Lernen, und anschließend lassen Sie die Algorithmusauswahl und Hyperparameteroptimierung vom Dienst vornehmen. Beim automatisierten maschinellen Lernen werden viele Kombinationen von Algorithmen und Hyperparametern durchlaufen, bis das beste Modell für Ihr Kriterium gefunden wird.

![Flussdiagramm](./media/tutorial-auto-train-models/flow2.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten einer Python-Umgebung und Importieren der SDK-Pakete
> * Konfigurieren eines Azure Machine Learning Service-Arbeitsbereichs
> * Automatisches Trainieren eines Regressionsmodells
> * Lokales Ausführen des Modells mit benutzerdefinierten Parametern
> * Untersuchen der Ergebnisse

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie noch heute die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

>[!NOTE]
> Der Code in diesem Artikel wurde mit Version 1.0.39 des Azure Machine Learning SDK getestet.

## <a name="prerequisites"></a>Voraussetzungen

Fahren Sie mit dem Abschnitt [Einrichten Ihrer Entwicklungsumgebung](#start) fort, um mehr zu den Schritten mit Notebooks zu erfahren, oder rufen Sie anhand der nachfolgenden Anweisungen das Notebook ab, und führen Sie es unter Azure Notebooks oder auf Ihrem eigenen Notebook-Server aus. Für die Ausführung des Notebooks ist Folgendes erforderlich:

* [Tutorial zur Datenvorbereitung](tutorial-data-prep.md) ausgeführt
* Ein Python 3.6-Notebook-Server mit folgenden Komponenten:
    * Das Azure Machine Learning SDK für Python mit den Extras `automl` und `notebooks`
    * `matplotlib`
* Das Tutorial-Notebook
* Ein Arbeitsbereich für maschinelles Lernen
* Die Konfigurationsdatei für den Arbeitsbereich muss sich im gleichen Verzeichnis wie das Notebook befinden.

Wie Sie diese erforderlichen Komponenten beziehen, erfahren Sie in den jeweiligen Abschnitten:

* Verwenden eines [cloudbasierten Notebook-Servers in Ihrem Arbeitsbereich](#azure) 
* Verwenden [Ihres eigenen Notebook-Servers](#server)

### <a name="azure"></a>Verwenden eines cloudbasierten Notebook-Servers in Ihrem Arbeitsbereich

Die ersten Schritte mit Ihrem eigenen cloudbasierten Notebook-Server sind ganz einfach. Das [Azure Machine Learning SDK für Python](https://aka.ms/aml-sdk) wird für Sie installiert und konfiguriert, wenn Sie diese Cloudressource erstellen.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Führen Sie nach dem Starten der Notebook-Webseite das Notebook **tutorials/regression-part2-automated-ml.ipynb** aus.

### <a name="server"></a>Verwenden Ihres eigenen Jupyter Notebook-Servers

Führen Sie diese Schritte aus, um auf Ihrem Computer einen lokalen Jupyter Notebook-Server zu erstellen.  Installieren Sie `matplotlib` sowie die Zusatzkomponenten `automl` und `notebooks` in Ihrer Umgebung.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Führen Sie nach den Schritten das Notebook **tutorials/regression-part2-automated-ml.ipynb** aus.

## <a name="start"></a>Einrichten Ihrer Entwicklungsumgebung

Das gesamte Setup für Ihre Entwicklungsarbeit kann in einem Python Notebook erfolgen. Die Einrichtung umfasst die folgenden Aktionen:

* Installieren des SDKs
* Importieren von Python-Paketen
* Konfigurieren des Arbeitsbereichs

### <a name="install-and-import-packages"></a>Installieren und Importieren von Paketen

Wenn Sie das Tutorial in Ihrer eigenen Python-Umgebung durchführen, verwenden Sie den folgenden Befehl, um die erforderlichen Pakete zu installieren:

```shell
pip install azureml-sdk[automl,notebooks] matplotlib
```

Importieren Sie die Python-Pakete, die Sie in diesem Tutorial benötigen:

```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
import logging
import os
```

### <a name="configure-workspace"></a>Konfigurieren des Arbeitsbereichs

Erstellen Sie ein Arbeitsbereichsobjekt aus dem vorhandenen Arbeitsbereich. Ein [Arbeitsbereich](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) ist eine Klasse, die Informationen zu Ihrem Azure-Abonnement und Ihren Azure-Ressourcen akzeptiert. Außerdem erstellt der Arbeitsbereich eine Cloudressource zur Überwachung und Nachverfolgung Ihrer Modellausführungen.

`Workspace.from_config()` liest die Datei **config.JSON** und lädt die Details in ein Objekt namens `ws`.  `ws` wird im restlichen Code in diesem Tutorial verwendet.

Nachdem Sie ein Arbeitsbereichsobjekt erstellt haben, geben Sie einen Namen für das Experiment an. Erstellen und registrieren Sie ein lokales Verzeichnis im Arbeitsbereich. Der Verlauf aller Ausführungen wird unter dem angegebenen Experiment und im [Azure-Portal](https://portal.azure.com) aufgezeichnet.


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>Erkunden von Daten

Verwenden Sie das im vorherigen Tutorial erstellte Datenflussobjekt. Im ersten Teil dieses Tutorials wurden die New Yorker Taxidaten bereinigt, um sie in einem Machine Learning-Modell verwenden zu können. Nun verwenden Sie verschiedene Features aus dem Dataset und ermöglichen es einem automatisierten Modell, Beziehungen zwischen den Features und dem Preis einer Taxifahrt herzustellen. Öffnen Sie den Datenfluss, führen Sie ihn aus, und überprüfen Sie die Ergebnisse:


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = dprep.Dataflow.open(file_path)
dflow_prepared.get_profile()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>max</th>
      <th>Count</th>
      <th>Fehlt (Anzahl)</th>
      <th>Fehlt nicht (Anzahl)</th>
      <th>Fehlt (Prozent)</th>
      <th>Fehler (Anzahl)</th>
      <th>Leer (Anzahl)</th>
      <th>0,1 % Quantil</th>
      <th>1 % Quantil</th>
      <th>5 % Quantil</th>
      <th>25 % Quantil</th>
      <th>50 % Quantil</th>
      <th>75 % Quantil</th>
      <th>95 % Quantil</th>
      <th>99 % Quantil</th>
      <th>99,9 % Quantil</th>
      <th>Mittelwert</th>
      <th>Standardabweichung</th>
      <th>Variance</th>
      <th>Schiefe</th>
      <th>Kurtosis</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>vendor</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>Freitag</td>
      <td>Wednesday</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2,90047</td>
      <td>2,69355</td>
      <td>9,72889</td>
      <td>16</td>
      <td>19,3713</td>
      <td>22,6974</td>
      <td>23</td>
      <td>23</td>
      <td>14,2731</td>
      <td>6,59242</td>
      <td>43,46</td>
      <td>-0,693723</td>
      <td>-0,570403</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>-4,99701</td>
      <td>-4,95833</td>
      <td>-14,1528</td>
      <td>29,3832</td>
      <td>44,6825</td>
      <td>56,4444</td>
      <td>58,9909</td>
      <td>59</td>
      <td>29,427</td>
      <td>17,4333</td>
      <td>303,921</td>
      <td>0,0120999</td>
      <td>-1,20981</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5,28131</td>
      <td>5</td>
      <td>14,7832</td>
      <td>29,9293</td>
      <td>44,725</td>
      <td>56,7573</td>
      <td>59</td>
      <td>59</td>
      <td>29,7443</td>
      <td>17,3595</td>
      <td>301,351</td>
      <td>-0,0252399</td>
      <td>-1,19616</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>Freitag</td>
      <td>Wednesday</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2,57153</td>
      <td>2</td>
      <td>9,58795</td>
      <td>15,9994</td>
      <td>19,6184</td>
      <td>22,8317</td>
      <td>23</td>
      <td>23</td>
      <td>14,2105</td>
      <td>6,71093</td>
      <td>45,0365</td>
      <td>-0,687292</td>
      <td>-0,61951</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5,44383</td>
      <td>4,84694</td>
      <td>14,1036</td>
      <td>28,8365</td>
      <td>44,3102</td>
      <td>56.6892</td>
      <td>59</td>
      <td>59</td>
      <td>29,2907</td>
      <td>17,4108</td>
      <td>303,136</td>
      <td>0,0222514</td>
      <td>-1,2181</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5,07801</td>
      <td>5</td>
      <td>14,5751</td>
      <td>29,5972</td>
      <td>45,4649</td>
      <td>56,2729</td>
      <td>59</td>
      <td>59</td>
      <td>29,772</td>
      <td>17,5337</td>
      <td>307,429</td>
      <td>-0,0212575</td>
      <td>-1,226</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>J</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,0781</td>
      <td>-73,7459</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74,0578</td>
      <td>-73,9639</td>
      <td>-73,9656</td>
      <td>-73,9508</td>
      <td>-73,9255</td>
      <td>-73,8529</td>
      <td>-73,8302</td>
      <td>-73,8238</td>
      <td>-73,7697</td>
      <td>-73,9123</td>
      <td>0,0503757</td>
      <td>0,00253771</td>
      <td>0,352172</td>
      <td>-0,923743</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5755</td>
      <td>40.8799</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40,632</td>
      <td>40,7117</td>
      <td>40,7115</td>
      <td>40,7213</td>
      <td>40,7565</td>
      <td>40,8058</td>
      <td>40,8478</td>
      <td>40.8676</td>
      <td>40,8778</td>
      <td>40,7649</td>
      <td>0,0494674</td>
      <td>0,00244702</td>
      <td>0,205972</td>
      <td>-0,777945</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0857</td>
      <td>-73.7209</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74,0775</td>
      <td>-73,9875</td>
      <td>-73,9882</td>
      <td>-73.9638</td>
      <td>-73,935</td>
      <td>-73,8755</td>
      <td>-73,8125</td>
      <td>-73,7759</td>
      <td>-73,7327</td>
      <td>-73,9202</td>
      <td>0,0584627</td>
      <td>0,00341789</td>
      <td>0,623622</td>
      <td>-0,262603</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5835</td>
      <td>40.8797</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40,5973</td>
      <td>40,6928</td>
      <td>40,6911</td>
      <td>40,7226</td>
      <td>40,7567</td>
      <td>40,7918</td>
      <td>40,8495</td>
      <td>40.868</td>
      <td>40,8787</td>
      <td>40,7583</td>
      <td>0,0517399</td>
      <td>0,00267701</td>
      <td>0,0390404</td>
      <td>-0,203525</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2,39249</td>
      <td>1,83197</td>
      <td>3,3561</td>
      <td>0,763144</td>
      <td>-1,23467</td>
    </tr>
    <tr>
      <th>distance</th>
      <td>FieldType.DECIMAL</td>
      <td>0.01</td>
      <td>32,34</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0108744</td>
      <td>0,743898</td>
      <td>0,738194</td>
      <td>1,243</td>
      <td>2,40168</td>
      <td>4,74478</td>
      <td>10,5136</td>
      <td>14,9011</td>
      <td>21,8035</td>
      <td>3,5447</td>
      <td>3,2943</td>
      <td>10,8524</td>
      <td>1,91556</td>
      <td>4,99898</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0,1</td>
      <td>88</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>6.148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2,33837</td>
      <td>5,00491</td>
      <td>5</td>
      <td>6,93129</td>
      <td>10,524</td>
      <td>17,4811</td>
      <td>33,2343</td>
      <td>50,0093</td>
      <td>63,1753</td>
      <td>13,6843</td>
      <td>9,66571</td>
      <td>93,426</td>
      <td>1,78518</td>
      <td>4,13972</td>
    </tr>
  </tbody>
</table>

Um die Daten für das Experiment vorzubereiten, fügen Sie Spalten als Merkmale für die Modellerstellung zu `dflow_x` hinzu. Sie definieren `dflow_y` als den Vorhersagewert **cost** (Kosten):

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday','pickup_hour', 'distance','passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>Aufteilen der Daten in Trainings- und Testsätze

Als Nächstes teilen Sie die Daten mithilfe der Funktion `train_test_split` in der Bibliothek `sklearn` in Trainings- und Testsätze auf. Diese Funktion teilt die Daten in zwei Datasets auf: das x-Dataset mit den **Merkmalen** zum Trainieren des Modells und das y-Dataset mit den **vorherzusagenden Werte** zum Durchführen von Tests. Der Parameter `test_size` bestimmt den Prozentsatz der Daten, die zu Testzwecken verwendet werden sollen. Der Parameter `random_state` legt einen Seed für den Zufallsgenerator fest, damit die Aufteilung zwischen Trainings- und Testdaten immer deterministisch ist:

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

Der Zweck dieses Schritts besteht darin, Datenpunkte zum Testen des fertigen Modells zu erhalten, mit denen das Modell nicht trainiert wurde. Anhand dieser Datenpunkte kann dann die tatsächliche Genauigkeit beurteilt werden. Anders ausgedrückt: Ein gut trainiertes Modell muss zutreffende Vorhersagen auf der Grundlage von Daten generieren können, die dem Modell noch nicht bekannt waren. Sie haben nun die notwendigen Pakete und Daten, um Ihr Modell automatisch zu trainieren.

## <a name="automatically-train-a-model"></a>Automatisches Trainieren eines Modells

Führen Sie die folgenden Schritte aus, um ein Modell automatisch zu trainieren:
1. Definieren Sie Einstellungen für die Experimentausführung. Fügen Sie Ihre Trainingsdaten an die Konfiguration an, und ändern Sie Einstellungen, die den Trainingsprozess steuern.
1. Übermitteln Sie das Experiment zur Modelloptimierung. Nach Übermittlung des Experiments durchläuft der Prozess verschiedene Machine Learning-Algorithmen und Hyperparametereinstellungen unter Berücksichtigung der von Ihnen definierten Einschränkungen. Er optimiert eine Genauigkeitsmetrik, um das am besten geeignete Modell auszuwählen.

### <a name="define-settings-for-autogeneration-and-tuning"></a>Definieren von Einstellungen für die automatische Generierung und Optimierung

Definieren Sie die Experimentparameter und Modelleinstellungen für die automatische Generierung und Optimierung. Die vollständige Liste mit den Einstellungen finden Sie [hier](how-to-configure-auto-train.md). Mit diesen Standardeinstellungen dauert die Übermittlung des Experiments etwa zehn bis 15 Minuten. Durch Verringern von `iterations` oder `iteration_timeout_minutes` können Sie die Laufzeit jedoch verkürzen.


|Eigenschaft| Wert in diesem Tutorial |BESCHREIBUNG|
|----|----|---|
|**iteration_timeout_minutes**|10|Zeitlimit in Minuten für jede Iteration. Verringern Sie diesen Wert, um die Gesamtlaufzeit zu verringern.|
|**iterations**|30|Anzahl von Iterationen. In jeder Iteration wird ein neues Machine Learning-Modell mit Ihren Daten trainiert. Hierbei handelt es sich um den primären Wert, der sich auf die Gesamtlaufzeit auswirkt.|
|**primary_metric**| spearman_correlation | Metrik, die Sie optimieren möchten. Das am besten geeignete Modell wird basierend auf dieser Metrik ausgewählt.|
|**preprocess**| True | Bei Verwendung von **True** kann das Experiment die Eingabedaten vorverarbeiten und beispielsweise fehlende Daten behandeln, Text in numerische Daten umwandeln und Ähnliches.|
|**verbosity**| logging.INFO | Steuert den Protokollierungsgrad.|
|**n_cross_validations**|5|Anzahl von Kreuzvalidierungsaufteilungen, die ausgeführt werden sollen, wenn keine Überprüfungsdaten angegeben sind.|



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```

Verwenden Sie Ihre definierten Trainingseinstellungen als Parameter für ein Objekt vom Typ `AutoMLConfig`. Geben Sie außerdem Ihre Trainingsdaten und die Art des Modells (in diesem Fall: `regression`) an.

```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>Trainieren des automatischen Regressionsmodells

Starten Sie das Experiment, und führen Sie es lokal aus. Übergeben Sie das definierte `automated_ml_config`-Objekt an das Experiment. Legen Sie die Ausgabe auf `True` fest, um den Fortschritt während des Experiments anzuzeigen:


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

Aktualisierungen werden während der Experimentausführung live in der Ausgabe angezeigt. Für die einzelnen Iterationen werden jeweils die Art des Modells, die Ausführungsdauer und die Trainingsgenauigkeit angezeigt. Im Feld `BEST` wird die beste Trainingsbewertung auf der Grundlage Ihres Metriktyps nachverfolgt.

    Parent Run ID: AutoML_02778de3-3696-46e9-a71b-521c8fca0651
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:08       0.9447    0.9447
             1   StandardScalerWrapper GradientBoosting         0:00:09       0.9536    0.9536
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.8580    0.9536
             3   StandardScalerWrapper RandomForest             0:00:08       0.9147    0.9536
             4   StandardScalerWrapper ExtremeRandomTrees       0:00:45       0.9398    0.9536
             5   MaxAbsScaler LightGBM                          0:00:08       0.9562    0.9562
             6   StandardScalerWrapper ExtremeRandomTrees       0:00:27       0.8282    0.9562
             7   StandardScalerWrapper LightGBM                 0:00:07       0.9421    0.9562
             8   MaxAbsScaler DecisionTree                      0:00:08       0.9526    0.9562
             9   MaxAbsScaler RandomForest                      0:00:09       0.9355    0.9562
            10   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            11   MaxAbsScaler LightGBM                          0:00:09       0.9553    0.9602
            12   MaxAbsScaler DecisionTree                      0:00:07       0.9484    0.9602
            13   MaxAbsScaler LightGBM                          0:00:08       0.9540    0.9602
            14   MaxAbsScaler RandomForest                      0:00:10       0.9365    0.9602
            15   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:49       0.9171    0.9602
            17   SparseNormalizer LightGBM                      0:00:08       0.9191    0.9602
            18   MaxAbsScaler DecisionTree                      0:00:08       0.9402    0.9602
            19   StandardScalerWrapper ElasticNet               0:00:08       0.9603    0.9603
            20   MaxAbsScaler DecisionTree                      0:00:08       0.9513    0.9603
            21   MaxAbsScaler SGD                               0:00:08       0.9603    0.9603
            22   MaxAbsScaler SGD                               0:00:10       0.9602    0.9603
            23   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            24   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            25   MaxAbsScaler SGD                               0:00:09       0.9603    0.9603
            26   TruncatedSVDWrapper ElasticNet                 0:00:09       0.9602    0.9603
            27   MaxAbsScaler SGD                               0:00:12       0.9413    0.9603
            28   StandardScalerWrapper ElasticNet               0:00:07       0.9603    0.9603
            29    Ensemble                                      0:00:38       0.9622    0.9622

## <a name="explore-the-results"></a>Untersuchen der Ergebnisse

Untersuchen Sie die Ergebnisse des automatischen Trainings mit einem Jupyter-Widget oder anhand des Experimentverlaufs.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>Option 1: Hinzufügen eines Jupyter-Widgets zum Anzeigen von Ergebnissen

Falls Sie ein Jupyter Notebook nutzen, verwenden Sie dieses Jupyter Notebook-Widget, um einen Graphen und eine Tabelle mit allen Ergebnissen anzuzeigen:


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Ausführungsdetails des Jupyter-Widgets](./media/tutorial-auto-train-models/automl-dash-output.png)
![Jupyter-Widget-Plot](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>Option 2: Abrufen und Untersuchen aller Ausführungsiterationen in Python

Sie können auch den Verlauf der einzelnen Experimente abrufen und die individuellen Metriken für die einzelnen Iterationsausführungen untersuchen. Wenn Sie RMSE (root_mean_squared_error) für die einzelnen Modellausführungen betrachten, sehen Sie, dass die vorhergesagten Preise für Taxifahrten bei den meisten Iterationen innerhalb eines angemessenen Bereichs (3–4 USD) liegen.

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0,811037</td>
      <td>0,880553</td>
      <td>0,398582</td>
      <td>0,776040</td>
      <td>0,663869</td>
      <td>0,875911</td>
      <td>0,115632</td>
      <td>0,586905</td>
      <td>0,851911</td>
      <td>0,793964</td>
      <td>...</td>
      <td>0,850023</td>
      <td>0,883603</td>
      <td>0,883704</td>
      <td>0,880797</td>
      <td>0,881564</td>
      <td>0,883708</td>
      <td>0,881826</td>
      <td>0,585377</td>
      <td>0,883123</td>
      <td>0,886817</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>2,189444</td>
      <td>1,500412</td>
      <td>5,480531</td>
      <td>2,626316</td>
      <td>2,973026</td>
      <td>1,550199</td>
      <td>6,383868</td>
      <td>4,414241</td>
      <td>1,743328</td>
      <td>2,294601</td>
      <td>...</td>
      <td>1,797402</td>
      <td>1,415815</td>
      <td>1,418167</td>
      <td>1,578617</td>
      <td>1,559427</td>
      <td>1,413042</td>
      <td>1,551698</td>
      <td>4,069196</td>
      <td>1,505795</td>
      <td>1,430957</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>1,438417</td>
      <td>0,850899</td>
      <td>4,579662</td>
      <td>1,765210</td>
      <td>1,594600</td>
      <td>0,869883</td>
      <td>4,266450</td>
      <td>3,627355</td>
      <td>0,954992</td>
      <td>1,361014</td>
      <td>...</td>
      <td>0,973634</td>
      <td>0,774814</td>
      <td>0,797269</td>
      <td>1,147234</td>
      <td>1,116424</td>
      <td>0,783958</td>
      <td>1,098464</td>
      <td>2,709027</td>
      <td>1,003728</td>
      <td>0,851724</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0,024908</td>
      <td>0,017070</td>
      <td>0,062350</td>
      <td>0,029878</td>
      <td>0,033823</td>
      <td>0,017636</td>
      <td>0,072626</td>
      <td>0,050219</td>
      <td>0,019833</td>
      <td>0,026105</td>
      <td>...</td>
      <td>0,020448</td>
      <td>0,016107</td>
      <td>0,016134</td>
      <td>0,017959</td>
      <td>0,017741</td>
      <td>0,016076</td>
      <td>0,017653</td>
      <td>0,046293</td>
      <td>0,017131</td>
      <td>0,016279</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0,016364</td>
      <td>0,009680</td>
      <td>0,052101</td>
      <td>0,020082</td>
      <td>0,018141</td>
      <td>0,009896</td>
      <td>0,048538</td>
      <td>0,041267</td>
      <td>0,010865</td>
      <td>0,015484</td>
      <td>...</td>
      <td>0,011077</td>
      <td>0,008815</td>
      <td>0,009070</td>
      <td>0,013052</td>
      <td>0,012701</td>
      <td>0,008919</td>
      <td>0,012497</td>
      <td>0,030819</td>
      <td>0,011419</td>
      <td>0,009690</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0,047968</td>
      <td>0,037882</td>
      <td>0,085572</td>
      <td>0,052282</td>
      <td>0,065809</td>
      <td>0,038664</td>
      <td>0,109401</td>
      <td>0,071104</td>
      <td>0,042294</td>
      <td>0,049967</td>
      <td>...</td>
      <td>0,042565</td>
      <td>0,037685</td>
      <td>0,037557</td>
      <td>0,037643</td>
      <td>0,037513</td>
      <td>0,037560</td>
      <td>0,037465</td>
      <td>0,072077</td>
      <td>0,037249</td>
      <td>0,036716</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0,055353</td>
      <td>0,045000</td>
      <td>0,110219</td>
      <td>0,065633</td>
      <td>0,063589</td>
      <td>0,044412</td>
      <td>0,123433</td>
      <td>0,092312</td>
      <td>0,046130</td>
      <td>0,055243</td>
      <td>...</td>
      <td>0,046540</td>
      <td>0,041804</td>
      <td>0,041771</td>
      <td>0,045175</td>
      <td>0,044628</td>
      <td>0,041617</td>
      <td>0,044405</td>
      <td>0,079651</td>
      <td>0,042799</td>
      <td>0,041530</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0,810900</td>
      <td>0,880328</td>
      <td>0,398076</td>
      <td>0,775957</td>
      <td>0,642812</td>
      <td>0,875719</td>
      <td>0,021603</td>
      <td>0,586514</td>
      <td>0,851767</td>
      <td>0,793671</td>
      <td>...</td>
      <td>0,849809</td>
      <td>0,880142</td>
      <td>0,880952</td>
      <td>0,880586</td>
      <td>0,881347</td>
      <td>0,880887</td>
      <td>0,881613</td>
      <td>0,548121</td>
      <td>0,882883</td>
      <td>0,886321</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>4,216362</td>
      <td>3,329810</td>
      <td>7,521765</td>
      <td>4,595604</td>
      <td>5,784601</td>
      <td>3,398540</td>
      <td>9,616354</td>
      <td>6,250011</td>
      <td>3,717661</td>
      <td>4,392072</td>
      <td>...</td>
      <td>3,741447</td>
      <td>3,312533</td>
      <td>3,301242</td>
      <td>3,308795</td>
      <td>3,297389</td>
      <td>3,301485</td>
      <td>3,293182</td>
      <td>6,335581</td>
      <td>3,274209</td>
      <td>3,227365</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0,243184</td>
      <td>0,197702</td>
      <td>0,484227</td>
      <td>0,288349</td>
      <td>0,279367</td>
      <td>0,195116</td>
      <td>0,542281</td>
      <td>0,405559</td>
      <td>0,202666</td>
      <td>0,242702</td>
      <td>...</td>
      <td>0,204464</td>
      <td>0,183658</td>
      <td>0,183514</td>
      <td>0,198468</td>
      <td>0,196067</td>
      <td>0,182836</td>
      <td>0,195087</td>
      <td>0,349935</td>
      <td>0,188031</td>
      <td>0,182455</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0,944743</td>
      <td>0,953618</td>
      <td>0,857965</td>
      <td>0,914703</td>
      <td>0,939846</td>
      <td>0,956159</td>
      <td>0,828187</td>
      <td>0,942069</td>
      <td>0,952581</td>
      <td>0,935477</td>
      <td>...</td>
      <td>0,951287</td>
      <td>0,960335</td>
      <td>0,960195</td>
      <td>0,960279</td>
      <td>0,960288</td>
      <td>0,960323</td>
      <td>0,960161</td>
      <td>0,941254</td>
      <td>0,960293</td>
      <td>0,962158</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0,944743</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>...</td>
      <td>0,960303</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,962158</td>
    </tr>
  </tbody>
</table>
<p>12 Zeilen x 30 Spalten</p>
</div>

## <a name="retrieve-the-best-model"></a>Abrufen des besten Modells

Wählen Sie aus den Iterationen die beste Pipeline aus. Die Methode `get_output` für `automl_classifier` gibt die beste Ausführung und das angepasste Modell für den letzten passenden Aufruf zurück. Mithilfe der Überladungen für `get_output` können Sie die beste Ausführung und das am besten geeignete Modell für jede protokollierte Metrik oder eine bestimmte Iteration abrufen:

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>Testen der Genauigkeit des besten Modells

Verwenden Sie das beste Modell, um Vorhersagen für das Testdataset auszuführen und Preise für Taxifahrten vorauszusagen. Die Funktion `predict` verwendet das beste Modell und sagt die Werte von y (**Fahrtkosten**) aus dem Dataset `x_test` voraus. Geben Sie die ersten zehn vorhergesagten Kostenwerte aus `y_predict` aus:

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Erstellen Sie ein Punktdiagramm, um den Vergleich der vorhergesagten Kostenwerte mit den tatsächlichen Kostenwerten zu visualisieren. Im folgenden Code wird das Merkmal `distance` als X-Achse verwendet und die Fahrtkosten `cost` als Y-Achse. Um die Varianz der vorhergesagten Kosten bei jedem Fahrtstreckenwert zu vergleichen, werden die ersten 100 vorhergesagten und tatsächlichen Kostenwerte als separate Reihen erstellt. Die Untersuchung des Diagramms zeigt, dass die Beziehung Entfernung/Kosten nahezu linear ist und die vorhergesagten Kostenwerte in den meisten Fällen sehr nahe bei den tatsächlichen Kostenwerten für die gleiche Entfernung liegen.

```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100], s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100], s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![Vorhersagepunktdiagramm](./media/tutorial-auto-train-models/automl-scatter-plot.png)

Berechnen Sie den `root mean squared error` der Ergebnisse. Verwenden Sie den Datenrahmen `y_test`. Konvertieren Sie ihn in eine Liste, die Sie mit den vorhergesagten Werten vergleichen. Die Funktion `mean_squared_error` akzeptiert zwei Wertarrays und berechnet den durchschnittlichen quadratischen Fehler zwischen den Arrays. Die Quadratwurzel des Ergebnisses ergibt einen Fehler in der gleichen Maßeinheit wie die y-Variable (**Kosten**). Dieser Fehler gibt Aufschluss darüber, wie weit die Vorhersagen des Preises für Taxifahrten ungefähr vom tatsächlichen Preis entfernt sind:

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

Führen Sie den folgenden Code aus, um den mittleren absoluten Fehler in Prozent (Mean Absolute Percent Error, MAPE) anhand der vollständigen Datasets `y_actual` und `y_predict` zu berechnen. Diese Metrik berechnet eine absolute Differenz zwischen jedem vorhergesagten und tatsächlichen Wert und addiert alle Differenzen. Die Summe wird dann als Prozentsatz der gesamten tatsächlichen Werte ausgedrückt:

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.10545153869569586

    Model Accuracy:
    0.8945484613043041

Anhand der endgültigen Metriken für die Vorhersagegenauigkeit sehen Sie, dass das Modell die Preise von Taxifahrten auf der Grundlage der Features des Datasets ziemlich gut vorhersagen kann (in der Regel mit einer Genauigkeit von +/-3,00 USD). Der traditionelle Modellentwicklungsprozess für das maschinelle Lernen ist sehr ressourcenintensiv und erfordert erhebliches Fachwissen und großen Zeitaufwand, um die Ergebnisse von Dutzenden von Modellen auszuführen und zu vergleichen. Mit automatisiertem maschinellem Lernen können Sie schnell zahlreiche verschiedene Modelle für Ihr Szenario testen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial zum automatisierten maschinellen Lernen haben Sie folgende Aufgaben ausgeführt:

> [!div class="checklist"]
> * Konfigurieren eines Arbeitsbereichs und Vorbereiten der Daten für ein Experiment
> * Lokales Trainieren mit einem automatisierten Regressionsmodell und benutzerdefinierten Parametern
> * Untersuchen und Überprüfen der Trainingsergebnisse

Als Nächstes können Sie [Ihr Modell mit Azure Machine Learning bereitstellen](tutorial-deploy-models-with-aml.md).
