---
title: Neuerungen im Release
titleSuffix: Azure Machine Learning
description: Erfahren Sie mehr über die neuesten Updates für Azure Machine Learning und Python SDKs für maschinelles Lernen und Datenaufbereitung.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 09/10/2020
ms.openlocfilehash: a01aab13b87398b7d27af02fa84dd7fadf7b8345
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430996"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning: Anmerkungen zu dieser Version

In diesem Artikel erhalten Sie Informationen zu Azure Machine Learning-Versionen.  Den vollständigen SDK-Referenzinhalt finden Sie auf der Hauptseite der Referenz zum [**Azure Machine Learning SDK für Python**](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

 ## <a name="2021-01-31"></a>31.01.2021
### <a name="azure-machine-learning-studio-notebooks-experience-january-update"></a>Oberfläche von Azure Machine Learning Studio Notebooks (Aktualisierung vom Januar)
+ **Neue Features**
  + Nativer Markdown-Editor in Azure Machine Learning: Benutzer können jetzt Markdowndateien nativ in Azure Machine Learning Studio rendern und bearbeiten.
  + [Ausführungsschaltfläche für Skripts („.py“, „.R“ und „.sh“)](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#run-a-notebook-or-python-script): Benutzer können jetzt mühelos Python-, R- und Bash-Skripts in Azure Machine Learning ausführen.
  + [Variablen-Explorer](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#explore-variables-in-the-notebook): Erkunden Sie den Inhalt von Variablen und Datenrahmen in einem Popupbereich. Benutzer können problemlos den Datentyp, die Größe und den Inhalt überprüfen.
  + [Inhaltsverzeichnis](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#navigate-with-a-toc): Navigieren Sie zu Abschnitten Ihres Notebooks (angegeben durch Markdown-Header).
  + Exportieren Ihres Notebooks als LaTeX-/HTML-/PY-Datei: Exportieren Sie Notebook-Dateien als LaTeX-, HTML- oder PY-Datei, um eine problemlose Weitergabe zu ermöglichen.
  + IntelliCode: Machine Learning-gestützte Ergebnisse sorgen für eine verbesserte [intelligente automatische Vervollständigung](https://docs.microsoft.com/visualstudio/intellicode/overview).

+ **Fehlerbehebungen und Verbesserungen**
  + Verbesserte Seitenladezeiten
  + Verbesserte Leistung 
  + Verbesserte Geschwindigkeit und Kernelzuverlässigkeit
  
 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Azure Machine Learning SDK für Python v1.21.0
+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Korrektur des CLI-Hilfetexts bei Verwendung von AmlCompute mit UserAssigned-Identität
  + **azureml-contrib-automl-dnn-vision**
    + Schaltflächen zum Bereitstellen und Herunterladen werden für Ausführungen des AutoML-Sehens angezeigt, und Modelle können auf ähnliche Weise wie bei anderen AutoML-Ausführungen bereitgestellt und heruntergeladen werden. Es sind zwei neue Dateien („scoring_file_v_1_0_0.py“ und „conda_env_v_1_0_0.yml“) vorhanden, die ein Skript zum Ausführen von Rückschlüssen bzw. eine YML-Datei zum Neuerstellen der Conda-Umgebung enthalten. Die Datei „model.pth“ wurde ebenfalls umbenannt und weist nun die Erweiterung „.pt“ auf.
  + **azureml-core**
    + MSI-Unterstützung für azure-cli-ml
    + Unterstützung für benutzerseitig zugewiesene verwaltete Identität.
    + Dank dieser Änderung sollten Kunden eine benutzerseitig zugewiesene Identität angeben können, mit der der Schlüssel für die Verschlüsselung im Ruhezustand aus dem Schlüsseltresor des Kunden abgerufen werden kann.
    +  „fix row_count=0“ für das Profil sehr großer Dateien: Fehlerbehebung bei „double“-Konvertierung für getrennte Werte mit Leerzeichenauffüllung
    + Experimentelles Flag für Ausgabedataset (GA) entfernt
    + Dokumentation zum Abrufen einer bestimmten Version eines Modells aktualisiert
    + Aktualisierung des Arbeitsbereichs für Zugriff im gemischten Modus bei einer privaten Verbindung
    + Fehlerbehebung: Zusätzliche Registrierung in Datenspeicher für das Feature zum Fortsetzen der Ausführung entfernt
    + CLI/SDK-Unterstützung für die Aktualisierung der primären benutzerseitigen Identität des Arbeitsbereichs hinzugefügt
  + **azureml-interpret**
    + „azureml-interpret“ wurde auf „interpret-community 0.16.0“ aktualisiert
    + Arbeitsspeicheroptimierungen für Erklärungsclient in „azureml-interpret“
  + **azureml-train-automl-runtime**
    + Streaming für ADB-Ausführungen aktiviert
  + **azureml-train-core**
    + Fehlerbehebung: Zusätzliche Registrierung in Datenspeicher für das Feature zum Fortsetzen der Ausführung entfernt
  + **azureml-widgets**
    + Für Kunden sollten bei Verwendung des Widgets keine Änderungen an der vorhandenen Visualisierung von Ausführungsdaten sichtbar sein. Darüber hinaus ist jetzt Unterstützung vorhanden, wenn Kunden sich für die optionale Nutzung von bedingten Hyperparametern entscheiden.
    + Das Widget für die Benutzerausführung enthält jetzt eine ausführliche Beschreibung, warum sich eine Ausführung in der Warteschlange befindet.


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Azure Machine Learning SDK für Python v1.20.0
+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + framework_version in OptimizationConfig hinzugefügt. Dies wird verwendet, wenn das Modell mit dem Framework MULTI registriert wird.
  + **azureml-contrib-optimization**
    + framework_version in OptimizationConfig hinzugefügt. Dies wird verwendet, wenn das Modell mit dem Framework MULTI registriert wird.
  + **azureml-pipeline-steps**
    + CommandStep eingeführt, der einen Befehl zur Verarbeitung übernimmt. Der Befehl kann ausführbare Dateien, Shellbefehle, Skripts usw. einschließen.
  + **azureml-core**
    + Die Arbeitsbereichs Erstellung unterstützt nun benutzerseitig zugewiesene Identitäten. Hinzufügen von UAI-Unterstützung über SDK/Befehlszeilenschnittstelle
    + Problem bei service.reload() behoben, um Änderungen an „score.py“ in der lokalen Bereitstellung zu übernehmen.
    + `run.get_details()` verfügt über das zusätzliche Feld „submittedBy“, das den Namen des Autors für diese Ausführung anzeigt.
    + Die Dokumentation zur Model.register-Methode wurde um Informationen dazu ergänzt, wie das Modell direkt aus der Ausführung registriert werden kann.
    + Problem mit der Verarbeitung von Änderungen des IoT-Server-Verbindungsstatus behoben.
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Azure Machine Learning Studio Notebooks-Oberfläche (Aktualisierung vom Dezember)
+ **Neue Features**
  + Suche des Benutzers nach Dateinamen. Benutzer können jetzt alle in einem Arbeitsbereich gespeicherten Dateien durchsuchen.
  + Parallele Markdown-Anzeige pro Notebookzelle. Die Benutzer können jetzt in einer Notebookzelle gerendertes Markdown und die Markdownsyntax nebeneinander anzeigen.
  + Zellenstatusleiste. Auf der Statusleiste werden der Status einer Codezelle angezeigt und angegeben, ob eine Zelle erfolgreich ausgeführt wurde und wie lange die Ausführung gedauert hat. 
   
+ **Fehlerbehebungen und Verbesserungen**
  + Verbesserte Seitenladezeiten
  + Verbesserte Leistung 
  + Verbesserte Geschwindigkeit und Kernelzuverlässigkeit

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Azure Machine Learning SDK für Python v1.19.0
+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + Die experimentelle Unterstützung für Testdaten wurde zu AutoMLStep hinzugefügt.
    + Die anfängliche Kernimplementierung des Features für die Testsatzerfassung wurde hinzugefügt.
    + Verweise auf „sklearn.externals.joblib“ wurden verschoben, um eine direkte Abhängigkeit von „joblib“ zu erreichen.
    + Es wurde der neue AutoML-Aufgabentyp „image-instance-segmentation“ eingeführt.
  + **azureml-automl-runtime**
    + Die anfängliche Kernimplementierung des Features für die Testsatzerfassung wurde hinzugefügt.
    + Wenn alle Zeichenfolgen in einer Textspalte eine Länge von genau einem Zeichen aufweisen, funktioniert der TFIDF-Word-Gram-Featurizer nicht, da sein Tokenizer die Zeichenfolgen mit weniger als zwei Zeichen ignoriert. Die aktuelle Codeänderung ermöglicht es AutoML, diesen Anwendungsfall zu behandeln.
    + Es wurde der neue AutoML-Aufgabentyp „image-instance-segmentation“ eingeführt.
  + **azureml-contrib-automl-dnn-nlp**
    + Anfänglicher PR für das neue dnn-nlp-Paket.
  + **azureml-contrib-automl-dnn-vision**
    + Es wurde der neue AutoML-Aufgabentyp „image-instance-segmentation“ eingeführt.
  + **azureml-contrib-automl-pipeline-steps**
    + Dieses neue Paket ist für das Erstellen von Schritten verantwortlich, die für viele Szenarien zum Trainieren/Rückschließen von Modellen erforderlich sind. Außerdem wird der Code für Training/Rückschlüsse in das Paket „azureml.train.automl.runtime“ verschoben, sodass alle zukünftigen Fehlerbehebungen automatisch über kuratierte Umgebungsversionen zur Verfügung stehen würden.
  + **azureml-contrib-dataset**
    + Es wurde der neue AutoML-Aufgabentyp „image-instance-segmentation“ eingeführt.
  + **azureml-core**
    + Die anfängliche Kernimplementierung des Features für die Testsatzerfassung wurde hinzugefügt.
    + Die XREF-Warnungen für Dokumentationen im Paket „azureml-core“ wurden behoben.
    + Fehlerbehebungen für Dokumentzeichenfolgen für das Feature „Befehlsunterstützung“ im SDK
    + Die Befehlseigenschaft wurde zu RunConfiguration hinzugefügt. Das Feature ermöglicht es Benutzern, einen eigentlichen Befehl oder ausführbare Dateien auf der Compute-Instanz über das AzureML SDK auszuführen.
    + Benutzer können ein leeres Experiment mit der ID dieses Experiments löschen.
  + **azureml-dataprep**
    + Die Datasetunterstützung für Spark wurde hinzugefügt, die mit Scala 2.12 erstellt wurden. Dies ergänzt die bestehende 2.11-Unterstützung.
  + **azureml-mlflow**
    + AzureML-MLflow fügt Absicherungen in Remoteskripts hinzu, um ein vorzeitiges Beenden von übermittelten Ausführungen zu vermeiden.
  + **azureml-pipeline-core**
    + Es wurde ein Fehler beim Festlegen einer Standardpipeline für einen über die Benutzeroberfläche erstellten Pipelineendpunkt behoben.
  + **azureml-pipeline-steps**
    + Die experimentelle Unterstützung für Testdaten wurde zu AutoMLStep hinzugefügt.
  + **azureml-tensorboard**
    + Die XREF-Warnungen für Dokumentationen im Paket „azureml-core“ wurden behoben.
  + **azureml-train-automl-client**
    + Die experimentelle Unterstützung für Testdaten wurde zu AutoMLStep hinzugefügt.
    + Die anfängliche Kernimplementierung des Features für die Testsatzerfassung wurde hinzugefügt.
    + Es wurde der neue AutoML-Aufgabentyp „image-instance-segmentation“ eingeführt.
  + **azureml-train-automl-runtime**
    + Die anfängliche Kernimplementierung des Features für die Testsatzerfassung wurde hinzugefügt.
    + Es wurde das Berechnungsergebnis der unformatierten Erklärungen für das beste AutoML-Modell korrigiert, wenn die AutoML-Modelle mithilfe der Einstellung „validation_size“ trainiert werden.
    + Verweise auf „sklearn.externals.joblib“ wurden verschoben, um eine direkte Abhängigkeit von „joblib“ zu erreichen.
  + **azureml-train-core**
    + „HyperDriveRun.get_children_sorted_by_primary_metric()“ sollte jetzt schneller abgeschlossen werden.
    + Die Fehlerbehandlung im Hyperdrive SDK wurde verbessert.
    +  Alle Schätzerklassen wurden zugunsten der Verwendung von „ScriptRunConfig“ zur Konfiguration von Experimentausführungen als veraltet markiert. Zu den veralteten Klassen gehören:
        + MMLBaseEstimator
        + Estimator
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + Die Verwendung von „Nccl“ und „Gloo“ als gültige Eingabetypen für Schätzerklassen wurde zugunsten der Verwendung von „PyTorchConfiguration“ mit „ScriptRunConfig“ als veraltet markiert.
    + Die Verwendung von „Mpi“ als gültige Eingabetypen für Schätzerklassen wurde zugunsten der Verwendung von „MpiConfiguration“ mit „ScriptRunConfig“ als veraltet markiert.
    + Die Befehlseigenschaft wurde zu RunConfiguration hinzugefügt. Das Feature ermöglicht es Benutzern, einen eigentlichen Befehl oder ausführbare Dateien auf der Compute-Instanz über das AzureML SDK auszuführen.

    +  Alle Schätzerklassen wurden zugunsten der Verwendung von „ScriptRunConfig“ zur Konfiguration von Experimentausführungen als veraltet markiert. Zu den veralteten Klassen gehören: + MMLBaseEstimator + Estimator + PyTorch + TensorFlow + Chainer + SKLearn
    + Die Verwendung von „Nccl“ und „Gloo“ als gültiger Eingabetyp für Schätzerklassen wurde zugunsten der Verwendung von „PyTorchConfiguration“ mit „ScriptRunConfig“ als veraltet markiert. 
    + Die Verwendung von „Mpi“ als gültiger Eingabetyp für Schätzerklassen wurde zugunsten der Verwendung von „MpiConfiguration“ mit „ScriptRunConfig“ als veraltet markiert.

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Azure Machine Learning Studio Notebooks-Oberfläche (Aktualisierung vom November)
+ **Neue Features**
   + Natives Terminal. Benutzer haben jetzt über das [integrierte Terminal](./how-to-run-jupyter-notebooks.md#terminal) Zugriff auf ein integriertes Terminal und auf Git-Vorgänge.
  + Ordnerduplikat 
  + Dropdownliste für Computekosten 
  + Pylance für Offlinecomputing 

+ **Fehlerbehebungen und Verbesserungen**
  + Verbesserte Seitenladezeiten
  + Verbesserte Leistung 
  + Verbesserte Geschwindigkeit und Kernelzuverlässigkeit
  + Upload großer Dateien. Sie können jetzt Dateien mit einer Größe von bis zu 95 MB hochladen.

## <a name="2020-11-09"></a>11.9.2020

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Azure Machine Learning SDK für Python v1.18.0
+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    +  Die Verarbeitung von kurzen Zeitreihen wurde verbessert, indem sie mit gaußschem Rauschen aufgefüllt werden dürfen.
  + **azureml-automl-runtime**
    + Wenn eine DateTime-Spalte einen OutOfBoundsDatetime-Wert aufweist, wird eine ConfigException-Ausnahme ausgelöst.
    + Die Verarbeitung von kurzen Zeitreihen wurde verbessert, indem sie mit gaußschem Rauschen aufgefüllt werden dürfen.
    + Es wird sichergestellt, dass jede Textspalte char-gram-Transformationen mit dem n-gram-Bereich auf Grundlage der Länge der Zeichenfolgen in der entsprechenden Textspalte verwenden kann.
    + Für auf dem lokalen Computer eines Benutzers ausgeführte AutoML-Experimente werden Rohfeatureerklärungen für den besten Modus bereitgestellt.
  + **azureml-core**
    + Anpinnen von Paketen: pyjwt wird verwendet, um das Pullen in fehlerhaften Versionen zukünftiger Veröffentlichungen zu verhindern.
    + Das Erstellen eines Experiments gibt das aktive oder zuletzt archivierte Experiment mit demselben Name zurück, wenn ein solches Experiment vorhanden ist, oder ein neues Experiment.
    + Das Aufrufen von get_experiment über seinen Namen gibt das aktive oder zuletzt archivierte Experiment mit demselben Namen zurück.
    + Benutzer können ein Experiment nicht umbenennen, während es reaktiviert wird.
    + Die Fehlermeldung wurde verbessert, sodass sie nun mögliche Fehlerbehebungen beinhaltet, wenn ein Dataset fehlerhaft an ein Experiment übergeben wurde, z. B. ScriptRunConfig. 
    + Die Dokumentation für `OutputDatasetConfig.register_on_complete` wurde verbessert, sodass das Verhalten eingeschlossen wird, das angewendet wird, wenn der Name bereits vorhanden ist.
    + Wenn Namen für Dataseteingabe und -ausgabe angegeben werden, die möglicherweise zu Konflikten mit allgemeinen Umgebungsvariablen führen könnten, führt dies nun zu einer Warnung.
    + Der Zweck des `grant_workspace_access`-Parameters beim Registrieren von Datenspeichern wurde neu bestimmt. Legen Sie ihn auf `True` fest, um auf Daten in einem virtuellen Netzwerk in Machine Learning Studio zuzugreifen.
      [Weitere Informationen](./how-to-enable-studio-virtual-network.md)
    + Die verknüpfte Dienst-API wurde optimiert. Anstatt eine Ressourcen-ID anzugeben, verfügen Sie über die drei eigenständigen Parameter „sub_id“, „rg“ und „name“, die in der Konfiguration definiert sind.
    + Damit Kunden Probleme mit beschädigten Token selbst lösen können, aktivieren Sie die Synchronisierung für Arbeitsbereichstoken als öffentliche Methode.
    + Diese Änderung ermöglicht es, dass eine leere Zeichenfolge als Wert für scrip_param verwendet werden kann.
  + **azureml-train-automl-client**
    +  Die Verarbeitung von kurzen Zeitreihen wurde verbessert, indem sie mit gaußschem Rauschen aufgefüllt werden dürfen.
  + **azureml-train-automl-runtime**
    + Wenn eine DateTime-Spalte einen OutOfBoundsDatetime-Wert aufweist, wird eine ConfigException-Ausnahme ausgelöst.
    + Es wurde Unterstützung für das Bereitstellen von Rohfeatureerklärungen für das beste Modell für auf dem lokalen Computer eines Benutzers ausgeführte AutoML-Experimente hinzugefügt.
    + Die Verarbeitung von kurzen Zeitreihen wurde verbessert, indem sie mit gaußschem Rauschen aufgefüllt werden dürfen.
  + **azureml-train-core**
    + Diese Änderung ermöglicht es, dass eine leere Zeichenfolge als Wert für scrip_param verwendet werden kann.
  + **azureml-train-restclients-hyperdrive**
    + Die README-Datei wurde geändert und bietet nun mehr Kontext.
  + **azureml-widgets**
    + Für das Widget wurde Unterstützung für Zeichenfolgen für Diagramme/parallele Koordinaten hinzugefügt.

## <a name="2020-11-05"></a>5\.11.2020

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>Datenbeschriftungen für die Segmentierung von Bildinstanzen (Polygonanmerkungen) (Vorschau)

Der Projekttyp für die Segmentierung von Bildinstanzen (Polygonanmerkungen) bei der Datenbeschriftung ist ab sofort verfügbar. Benutzer können also Polygone zeichnen und mit Anmerkungen um die Kontur der Objekte herum auf Bildern versehen. Benutzer können den einzelnen Objekten eine Klasse und ein Polygon zuweisen, das für ein Bild relevant ist.

Weitere Informationen zum [Taggen von Bildern in einem Bezeichnungsprojekt](how-to-label-images.md)



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Azure Machine Learning SDK für Python v1.17.0
+ **Neue Beispiele**
  + Ein neues von der Community betreutes Repository von Beispielen ist verfügbar unter https://github.com/Azure/azureml-examples
+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + Es wurde ein Problem behoben, bei dem „get_output“ einen XGBoostError auslösen konnte.
  + **azureml-automl-runtime**
    + Zeit-/Kalenderbasierte Features, die mit AutoML erstellt wurden, weisen jetzt das Präfix auf.
    + Ein IndexError, der während des Trainings von StackEnsemble für Klassifizierungsdatasets mit einer großen Anzahl von Klassen und aktivierter Subsampling-Funktion auftrat, wurde behoben.
    + Es wurde ein Problem behoben, bei dem die Vorhersagen von VotingRegressor nach der Neuanpassung des Modells ungenau sein konnten.
  + **azureml-core**
    + Weitere Details über die Beziehung zwischen der AKS-Bereitstellungskonfiguration und den Azure Kubernetes Service-Konzepten wurden hinzugefügt.
    + Unterstützung für Umgebungsclientbezeichnungen. Benutzer können Umgebungen beschriften und über Bezeichnungen darauf verweisen.
  + **azureml-dataprep**
    + Bessere Fehlermeldung bei der Verwendung von derzeit nicht unterstütztem Spark mit Scala 2.12.
  + **azureml-explain-model**
    + Das Paket „azureml-explain-model“ ist offiziell veraltet.
  + **azureml-mlflow**
    + Ein Fehler in „mlflow.projects.run“ für das azureml-Back-End, bei dem der Zustand „Ausführung wird abgeschlossen“ nicht richtig behandelt wurde, wurde behoben.
  + **azureml-pipeline-core**
    + Unterstützung zum Erstellen, Auflisten und Abrufen von Pipelineplänen auf der Basis eines Pipelineendpunkts wurde hinzugefügt.
    +  Die Dokumentation von „PipelineData.as_dataset“ mit einem ungültigen Verwendungsbeispiel wurde verbessert. Die Verwendung von „PipelineData.as_dataset“ führt jetzt zum Auslösen von „ValueException“.
    + Das HyperDriveStep-Pipelinenotebook wurde geändert, um das beste Modell innerhalb eines „PipelineSteps“ direkt nach der HyperDriveStep-Ausführung zu registrieren.
  + **azureml-pipeline-steps**
    + Das HyperDriveStep-Pipelinenotebook wurde geändert, um das beste Modell innerhalb eines „PipelineSteps“ direkt nach der HyperDriveStep-Ausführung zu registrieren.
  + **azureml-train-automl-client**
    + Es wurde ein Problem behoben, bei dem „get_output“ einen XGBoostError auslösen konnte.

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Azure Machine Learning Studio Notebooks-Oberfläche (Aktualisierung vom Oktober)
+ **Neue Features**
  + [Volle Unterstützung für virtuelle Netzwerke](./how-to-enable-studio-virtual-network.md)
  + [Fokusmodus](./how-to-run-jupyter-notebooks.md#focus-mode)
  + Notebooks speichern Strg-S
  + Zeilennummern

+ **Fehlerbehebungen und Verbesserungen**
  + Verbesserung der Geschwindigkeit und Kernelzuverlässigkeit
  + Updates der Jupyter Widget-Benutzeroberfläche

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Azure Machine Learning SDK für Python v1.16.0
+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + AKSWebservice und AKSEndpoints unterstützen jetzt CPU- und Speicherressourceneinschränkungen auf Podebene. Sie können diese optionalen Grenzwerte verwenden, indem Sie die Flags `--cpu-cores-limit` und `--memory-gb-limit` in anwendbaren CLI-Aufrufen festlegen.
  + **azureml-core**
    + Hauptversionen von direkten Abhängigkeiten von azureml-core wurden angeheftet.
    + AKSWebservice und AKSEndpoints unterstützen jetzt CPU- und Speicherressourceneinschränkungen auf Podebene. Weitere Informationen zu Kubernetes-Ressourcen und -Grenzwerten finden Sie [hier](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits).
    + Run.log_table wurde aktualisiert, um die Protokollierung einzelner Zeilen zu gestatten.
    + Die statische Methode `Run.get(workspace, run_id)` wurde hinzugefügt, um eine Ausführung nur mithilfe eines Arbeitsbereichs abzurufen. 
    + Die Instanzmethode `Workspace.get_run(run_id)` wurde hinzugefügt, um eine Ausführung innerhalb des Arbeitsbereichs abzurufen.
    + Die Befehlseigenschaft wurde in der Ausführungskonfiguration eingeführt, die es Benutzern ermöglicht, Befehle anstelle von Skripts und Argumenten zu übermitteln.
  + **azureml-interpret**
    + Verhalten von Erklärungsclient is_raw flag wurde in azureml-interpret behoben.
  + **azureml-sdk**
    + `azureml-sdk` unterstützen Python 3.8 offiziell.
  + **azureml-train-core**
    + Kuratierte TensorFlow 2.3-Umgebung wurde hinzugefügt.
    + Die Befehlseigenschaft wurde in der Ausführungskonfiguration eingeführt, die es Benutzern ermöglicht, Befehle anstelle von Skripts und Argumenten zu übermitteln.
  + **azureml-widgets**
    + Neu gestaltete Schnittstelle für das Widget zur Skriptausführung.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK für Python v1.15.0
+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-contrib-interpret**
    + LIME-Erklärmodul aus azureml-contrib-interpret in interpret-community-Paket verschoben und Image-Erklärmodul aus azureml-contrib-interpret-Paket entfernt
    + Visualisierungsdashboard aus azureml-contrib-interpret-Paket entfernt, Erklärungsclient in azureml-interpret-Paket verschoben und in azureml-contrib-interpret-Paket als veraltet markiert und Notebooks aktualisiert, um die verbesserte API widerzuspiegeln
    + Korrektur der pypi-Paketbeschreibungen für azureml-interpret, azureml-explain-model, azureml-contrib-interpret und azureml-tensorboard
  + **azureml-contrib-notebook**
    + nbcovert-Abhängigkeit an < 6 anheften, damit papermill 1. x weiterhin funktioniert.
  + **azureml-core**
    + Parameter wurden TensorflowConfiguration und dem MpiConfiguration-Konstruktor hinzugefügt, um eine optimierte Initialisierung der Klassenattribute zu ermöglichen, ohne dass der Benutzer jedes einzelne Attribut festlegen muss. Es wurde eine PyTorchConfiguration-Klasse zum Konfigurieren verteilter PyTorch-Aufträge in ScriptRunConfig hinzugefügt.
    + Heften Sie die Version von azure-mgmt-resource an, um den Authentifizierungsfehler zu beheben.
    + Unterstützung von Triton-Bereitstellung ohne Code
    + In Run.start_logging() angegebenen Ausgabeverzeichnisse werden nun nachverfolgt, wenn die Ausführung in interaktiven Szenarien verwendet wird. Die nachverfolgten Dateien sind in ML Studio nach dem Aufruf von Run.complete() sichtbar.
    + Dateicodierung kann jetzt während der Dataseterstellung mit `Dataset.Tabular.from_delimited_files` und `Dataset.Tabular.from_json_lines_files` angegeben werden, indem das `encoding`-Argument übergeben wird. Die unterstützten Codierungen sind „utf8“, „iso88591“, „latin1“, „ascii“, „utf16“, „utf32“, „utf8bom“ und „windows1252“.
    + Fehlerbehebung, wenn das Umgebungsobjekt nicht an den ScriptRunConfig-Konstruktor übergeben wird.
    + Run.cancel() wurde aktualisiert, um das Abbrechen einer lokalen Ausführung von einem anderen Computer zuzulassen.
  + **azureml-dataprep**
    +  Probleme durch Timeout beim Einbinden von Datasets behoben.
  + **azureml-explain-model**
    + Korrektur der pypi-Paketbeschreibungen für azureml-interpret, azureml-explain-model, azureml-contrib-interpret und azureml-tensorboard
  + **azureml-interpret**
    + Visualisierungsdashboard aus azureml-contrib-interpret-Paket entfernt, Erklärungsclient in azureml-interpret-Paket verschoben und in azureml-contrib-interpret-Paket als veraltet markiert und Notebooks aktualisiert, um die verbesserte API widerzuspiegeln
    + azureml-interpret-Paket als Abhängigkeit von interpret-community 0.15.0 aktualisiert.
    + Korrektur der pypi-Paketbeschreibungen für azureml-interpret, azureml-explain-model, azureml-contrib-interpret und azureml-tensorboard
  + **azureml-pipeline-core**
    +  Pipelineproblem mit `OutputFileDatasetConfig` behoben, bei dem das System ggf. nicht mehr reagiert, wenn `register_on_complete` mit dem `name`-Parameter aufgerufen wird, der auf einen bereits vorhandenen Datasetnamen festgelegt ist.
  + **azureml-pipeline-steps**
    + Veraltete Databricks-Notebooks entfernt.
  + **azureml-tensorboard**
    + Korrektur der pypi-Paketbeschreibungen für azureml-interpret, azureml-explain-model, azureml-contrib-interpret und azureml-tensorboard
  + **azureml-train-automl-runtime**
    + Visualisierungsdashboard aus azureml-contrib-interpret-Paket entfernt, Erklärungsclient in azureml-interpret-Paket verschoben und in azureml-contrib-interpret-Paket als veraltet markiert und Notebooks aktualisiert, um die verbesserte API widerzuspiegeln
  + **azureml-widgets**
    + Visualisierungsdashboard aus azureml-contrib-interpret-Paket entfernt, Erklärungsclient in azureml-interpret-Paket verschoben und in azureml-contrib-interpret-Paket als veraltet markiert und Notebooks aktualisiert, um die verbesserte API widerzuspiegeln

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK für Python v1.14.0
+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Die Rasterprofilerstellung wurde aus dem SDK entfernt und wird nicht mehr unterstützt.
  + **azureml-accel-models**
    + Das Paket azureml-accel-models unterstützt jetzt Tensorflow 2.x
  + **azureml-automl-core**
    + Fehlerbehandlung in get_output für Fälle hinzugefügt, in denen lokale Versionen von pandas/sklearn nicht den beim Training verwendeten Versionen entsprechen
  + **azureml-automl-runtime**
    + Es wurde ein Fehler behoben, bei dem AutoArima-Iterationen mit einer PredictionException und der folgenden Meldung fehlschlagen: „Bei der Vorhersage ist ein Fehler ohne Meldung aufgetreten.“
  + **azureml-cli-common**
    + Die Rasterprofilerstellung wurde aus dem SDK entfernt und wird nicht mehr unterstützt.
  + **azureml-contrib-server**
    + Beschreibung des Pakets für die pypi-Übersichtsseite aktualisiert.
  + **azureml-core**
    + Die Rasterprofilerstellung wurde aus dem SDK entfernt und wird nicht mehr unterstützt.
    + Die Anzahl der Fehlermeldungen wurde verringert, wenn der Arbeitsbereich nicht abgerufen werden konnte.
    + Es wird keine Warnung angezeigt, wenn Metadaten nicht abgerufen werden können
    + Neuer Kusto-Schritt und neues Kusto-Computeziel.
    + Dokument für den SKU-Parameter aktualisiert. SKU in der Arbeitsbereichsupdate-Funktionalität in der CLI und im SDK entfernt.
    + Beschreibung des Pakets für die pypi-Übersichtsseite aktualisiert.
    + Dokumentation für AzureML-Umgebungen aktualisiert.
    + Vom Dienst verwaltete Ressourceneinstellungen für den AML-Arbeitsbereich im SDK wurden verfügbar gemacht.
  + **azureml-dataprep**
    + Ausführungsberechtigung für Dateien für Einbinden von Datasets aktiviert.
  + **azureml-mlflow**
    + AzureML MLflow-Dokumentation und Notebook-Beispiele aktualisiert. 
    + Neue Unterstützung für MLflow-Projekte mit AzureML-Backend
    + Unterstützung für MLflow-Modellregistrierung
    + Azure RBAC-Unterstützung für AzureML-MLflow-Vorgänge hinzugefügt. 
    
  + **azureml-pipeline-core**
    + Dokumentation zu den PipelineOutputFileDataset.parse_*-Methoden verbessert.
    + Neuer Kusto-Schritt und neues Kusto-Computeziel.
    + Swaggerurl-Eigenschaft für pipeline-endpoint-Entität bereitgestellt, über die der Benutzer die Schemadefinition für den veröffentlichten Pipelineendpunkt anzeigen kann.
  + **azureml-pipeline-steps**
    + Neuer Kusto-Schritt und neues Kusto-Computeziel.
  + **azureml-telemetry**
    + Beschreibung des Pakets für die pypi-Übersichtsseite aktualisiert.
  + **azureml-train**
    + Beschreibung des Pakets für die pypi-Übersichtsseite aktualisiert.
  + **azureml-train-automl-client**
    + Fehlerbehandlung in get_output für Fälle hinzugefügt, in denen lokale Versionen von pandas/sklearn nicht den beim Training verwendeten Versionen entsprechen
  + **azureml-train-core**
    + Beschreibung des Pakets für die pypi-Übersichtsseite aktualisiert.
    
## <a name="2020-08-31"></a>31.08.2020

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK für Python v1.13.0
+ **Vorschaufunktionen**
  + **azureml-core** Mit der neuen Ausgabedatasets-Funktion können Sie in den Cloudspeicher, einschließlich Blob, ADLS Gen 1, ADLS Gen 2 und FileShare, zurückschreiben. Sie können konfigurieren, wo Daten ausgegeben werden sollen, wie Daten ausgegeben werden (per Einbindung oder Upload) und ob die Ausgabedaten für die zukünftige Wiederverwendung und Freigabe registriert werden soll. Außerdem ist es möglich, Zwischendaten zwischen den Pipelineschritten reibungslos zu übergeben. Dies ermöglicht Reproduzierbarkeit und Freigabe, verhindert die Duplizierung von Daten und führt zu Kosteneffizienz und Produktivitätssteigerungen. [Informationen zur Verwendung](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py)
    
+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + Die Datei „validated_{platform}_requirements.txt“ wurde hinzugefügt, um alle PIP-Abhängigkeiten für AutoML anzuheften.
    + Diese Version unterstützt Modelle, die größer als 4 GB sind.
    + Aktualisierte AutoML-Abhängigkeiten: `scikit-learn` (jetzt 0.22.1), `pandas` (jetzt 0.25.1), `numpy` (jetzt 1.18.2).
  + **azureml-automl-runtime**
    + Legen Sie Horovod für Text-DNN so fest, dass immer FP16-Komprimierung verwendet wird.
    + Diese Version unterstützt Modelle, die größer als 4 GB sind.
    + ImportError: cannot import name `RollingOriginValidator` (Importfehler: Der Name `RollingOriginValidator` kann nicht importiert werden.)
    + Aktualisierte AutoML-Abhängigkeiten: `scikit-learn` (jetzt 0.22.1), `pandas` (jetzt 0.25.1), `numpy` (jetzt 1.18.2).
  + **azureml-contrib-automl-dnn-forecasting**
    + Aktualisierte AutoML-Abhängigkeiten: `scikit-learn` (jetzt 0.22.1), `pandas` (jetzt 0.25.1), `numpy` (jetzt 1.18.2).
  + **azureml-contrib-fairness**
    + Geben Sie eine kurze Beschreibung für „azureml-contrib-fairness“ an.
  + **azureml-contrib-pipeline-steps**
    + Es wurde eine Meldung hinzugefügt, die darauf hinweist, dass dieses Paket veraltet ist und der Benutzer stattdessen „azureml-pipeline-steps“ verwenden soll.
  + **azureml-core**
    + Der Befehl zum Auflisten von Schlüsseln für den Arbeitsbereich wurde hinzugefügt.
    + Parameter zum Hinzufügen von Tags im Arbeitsbereich-SDK und der CLI.
    + Ein Fehler wurde behoben, aufgrund dessen beim Übermitteln einer untergeordneten Ausführung mit einem Dataset ein Fehler aufgrund von `TypeError: can't pickle _thread.RLock objects` auftritt.
    + Hinzufügen von „page_count default/documentation“ für „Model list()“.
    + Ändern von CLI und SDK, um den adbworkspace-Parameter zu übernehmen und Hinzufügen von Workspace-adb lin/Unlink Runner.
    + Beheben eines Fehlers in „Dataset.update“, der dazu geführt hat, dass die aktuelle Datasetversion aktualisiert wurde, und nicht die Version, für die die Datasetaktualisierung aufgerufen wurde. 
    + Beheben eines Fehlers in „Dataset.get_by_name“, aufgrund dessen die Tags für die neueste Datasetversion des Datasets anzeigt werden, auch wenn eine bestimmte ältere Version abgerufen wurde.
  + **azureml-interpret**
    + Wahrscheinlichkeitsausgaben zu SHAP Scoring Explainers in „azureml-interpret“ wurden hinzugefügt, basierend auf dem Parameter „shap_values_output“ vom ursprünglichen Explainer.
  + **azureml-pipeline-core**
    + Die Dokumentation von `PipelineOutputAbstractDataset.register` wurde verbessert.
  + **azureml-train-automl-client**
    + Aktualisierte AutoML-Abhängigkeiten: `scikit-learn` (jetzt 0.22.1), `pandas` (jetzt 0.25.1), `numpy` (jetzt 1.18.2).
  + **azureml-train-automl-runtime**
    + Aktualisierte AutoML-Abhängigkeiten: `scikit-learn` (jetzt 0.22.1), `pandas` (jetzt 0.25.1), `numpy` (jetzt 1.18.2).
  + **azureml-train-core**
    + Benutzer müssen jetzt ein gültiges „hyperparameter_sampling“-Argument bereitstellen, wenn Sie HyperDriveConfig erstellen. Außerdem wurde die Dokumentation für „HyperDriveRunConfig“ bearbeitet, um Benutzer über die Veraltung von „HyperDriveRunConfig“ zu informieren.
    + Wiederherstellung der PyTorch-Standardversion auf 1.4.
    + Hinzufügen von PyTorch 1.6- und TensorFlow 2.2-Images und der zusammengestellten Umgebung.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Azure Machine Learning Studio Notebooks-Oberfläche (Aktualisierung vom August)
+ **Neue Features**
  + Neue Landing Page „Erste Schritte“ 
  
+ **Vorschaufunktionen**
    + Funktion „Gather“ (Sammeln) in Notebooks. Mit der Funktion [Gather](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) (Sammeln) können Benutzer jetzt problemlos Notebooks bereinigen. Sie verwendet eine automatisierte Abhängigkeitsanalyse Ihres Notebooks und stellt sicher, dass der wesentliche Code beibehalten wird, aber alle irrelevanten Elemente entfernt werden.

+ **Fehlerbehebungen und Verbesserungen**
  + Verbesserung der Geschwindigkeit und Zuverlässigkeit
  + Fehler im dunklen Modus behoben
  + Fehler beim Ausgabebildlauf
  + Die Beispielsuche durchsucht jetzt den gesamten Inhalt aller Dateien im Azure Machine Learning-Beispielnotebookrepository.
  + Mehrzeilige R-Zellen können nun ausgeführt werden.
  + „I trust contents of this file“ (Ich vertraue dem Inhalt dieser Datei) wird jetzt nach dem ersten Mal automatisch aktiviert.
  + Verbessertes Konfliktlösungs-Dialogfeld mit der neuen Option „Kopie erstellen“
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK für Python v1.12.0

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Fügen Sie die Parameter image_name und image_label zu Model.package() hinzu, um das Umbenennen des erstellten Paketimages zu ermöglichen.
  + **azureml-automl-core**
    + AutoML löst einen neuen Fehlercode von dataprep aus, wenn Inhalt während des Lesens geändert wird.
  + **azureml-automl-runtime**
    + Es wurden Warnungen für den Benutzer hinzugefügt, wenn Daten fehlende Werte enthalten, die Featureisierung aber deaktiviert ist.
    + Es wurden Fehler bei der Ausführung untergeordneter Elemente behoben, wenn Daten „nan“ enthalten und die Featurisierung deaktiviert ist.
    + AutoML löst einen neuen Fehlercode von dataprep aus, wenn Inhalt während des Lesens geändert wird.
    + Die Normalisierung für die Vorhersage von Metriken, die nach Intervall auftreten, wurde aktualisiert.
    + Die Berechnung von Vorhersagequantilen wurde verbessert, wenn Rückblickfeatures deaktiviert sind.
    + Die Behandlung der booleschen Sparsematrix bei der Berechnung von Erklärungen nach AutoML wurde behoben.
  + **azureml-core**
    + Eine neue `run.get_detailed_status()`-Methode zeigt jetzt die detaillierte Erklärung des aktuellen Ausführungsstatus an. Derzeit wird nur eine Erklärung für den `Queued`-Status angezeigt.
    + Fügen Sie die Parameter image_name und image_label zu Model.package() hinzu, um das Umbenennen des erstellten Paketimages zu ermöglichen.
    + Neue `set_pip_requirements()`-Methode, um den gesamten Abschnitt „pip“ in [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?preserve-view=true&view=azure-ml-py) auf einmal festzulegen.
    + Die Registrierung von Anmeldeinformationen ohne ADLS Gen2-Datenspeicher wurde aktiviert.
    + Die Fehlermeldung beim Versuch, einen falschen Datasettyp herunterzuladen oder einzubinden, wurde verbessert.
    + Das Beispielnotebook für Zeitreihen-Datasetfilter wurde mit weiteren Beispielen von „partition_timestamp“ aktualisiert, die eine Filteroptimierung bieten.
    + Das SDK und die CLI wurden geändert, um „subscriptionId“, „resourceGroup“, „workspaceName“, „peConnectionName“ anstelle von „ArmResourceId“ als Parameter zu akzeptieren, wenn private Endpunktverbindungen gelöscht werden.
    + Experimenteller Decorator zeigt zur leichteren Identifizierung den Klassennamen an.
    + Beschreibungen für die Ressourcen innerhalb von Modellen werden nicht mehr automatisch auf der Grundlage einer Ausführung generiert.
  + **azureml-datadrift**
    + Markiert „create_from_model“-API in DataDriftDetector als veraltet.
  + **azureml-dataprep**
    + Die Fehlermeldung beim Versuch, einen falschen Datasettyp herunterzuladen oder einzubinden, wurde verbessert.
  + **azureml-pipeline-core**
    + Fehler bei der Deserialisierung von Pipelinegraphen, die registrierte Datasets enthalten, wurde behoben.
  + **azureml-pipeline-steps**
    + RScriptStep unterstützt RSection aus azureml.core.environment.
    + Der Parameter „passthru_automl_config“ wurde aus der öffentlichen `AutoMLStep`-API entfernt und in einen rein internen Parameter konvertiert.
  + **azureml-train-automl-client**
    + Die lokalen asynchronen, verwalteten Umgebungsausführungen wurden aus AutoML entfernt. Alle lokalen Ausführungen werden in der Umgebung ausgeführt, in der die Ausführung gestartet wurde.
    + Probleme mit der Momentaufnahme bei der Übermittlung von AutoML-Ausführungen ohne vom Benutzer bereitgestellte Skripts wurde behoben.
    + Es wurden Fehler bei der Ausführung untergeordneter Elemente behoben, wenn Daten „nan“ enthalten und die Featurisierung deaktiviert ist.
  + **azureml-train-automl-runtime**
    + AutoML löst einen neuen Fehlercode von dataprep aus, wenn Inhalt während des Lesens geändert wird.
    + Probleme mit der Momentaufnahme bei der Übermittlung von AutoML-Ausführungen ohne vom Benutzer bereitgestellte Skripts wurde behoben.
    + Es wurden Fehler bei der Ausführung untergeordneter Elemente behoben, wenn Daten „nan“ enthalten und die Featurisierung deaktiviert ist.
  + **azureml-train-core**
    + Unterstützung für die Angabe von pip-Optionen (z. B. --extra-index-url) in der pip-Anforderungsdatei, die an einen [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator?preserve-view=true&view=azure-ml-py) bis `pip_requirements_file` Parameter übergeben wird, wurde hinzugefügt.


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK für Python v1.11.0

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Modellframework und nicht im ausgeführten Objekt übergebenes Modellframework wurde im Modellregistrierungspfad der CLI behoben.
    + AmlCompute-Befehl der CLI zum Anzeigen der Identität wurde behoben, um die Mandanten-ID und die Prinzipal-ID anzuzeigen. 
  + **azureml-train-automl-client**
    + Get_best_child () wurde zu AutoMLRun hinzugefügt, um die beste Ausführung des untergeordneten Elements für eine AutoML-Ausführung abzurufen, ohne das zugehörige Modell herunterzuladen.
    + ModelProxy-Objekt wurde hinzugefügt, mit dem Vorhersagen oder Prognosen in einer entfernten Trainingsumgebung ausgeführt werden können, ohne dass das Modell lokal heruntergeladen werden muss.
    + Unbehandelte Ausnahmen in AutoML verweisen jetzt auf eine HTTP-Seite mit bekannten Problemen, auf der weitere Informationen zu den Fehlern zu finden sind.
  + **azureml-core**
    + Modellnamen können 255 Zeichen lang sein.
    + Der Typ des Rückgabeobjekts für „Environment.get_image_details()“ wurde geändert. Die `DockerImageDetails`-Klasse hat `dict` ersetzt. Imagedetails sind in den neuen Klasseneigenschaften verfügbar. Änderungen sind abwärtskompatibel.
    + Fehler für „Environment.from_pip_requirements()“ wurde behoben, um die Struktur der Abhängigkeiten zu erhalten.
    + Es wurde ein Problem behoben, bei dem bei log_list ein Fehler aufgetreten ist, wenn ein Int- und Double-Typ in derselben Liste enthalten war.
    + Beachten Sie bei der Aktivierung einer privaten Verknüpfung für einen bestehenden Arbeitsbereich, dass wenn dem Arbeitsbereich Computeziele zugeordnet sind, diese Ziele nicht funktionieren, wenn sie sich nicht hinter demselben virtuellen Netzwerk wie der private Endpunkt des Arbeitsbereichs befinden.
    + Bei der Verwendung von Datasets in Experimenten wurde `as_named_input` als „optional“ festgelegt sowie `as_mount` und `as_download` zu `FileDataset` hinzugefügt. Der Name der Eingabe wird automatisch generiert, wenn `as_mount` oder `as_download` aufgerufen wird.
  + **azureml-automl-core**
    + Unbehandelte Ausnahmen in AutoML verweisen jetzt auf eine HTTP-Seite mit bekannten Problemen, auf der weitere Informationen zu den Fehlern zu finden sind.
    + Get_best_child () wurde zu AutoMLRun hinzugefügt, um die beste Ausführung des untergeordneten Elements für eine AutoML-Ausführung abzurufen, ohne das zugehörige Modell herunterzuladen.
    + ModelProxy-Objekt wurde hinzugefügt, mit dem Vorhersagen oder Prognosen in einer entfernten Trainingsumgebung ausgeführt werden können, ohne dass das Modell lokal heruntergeladen werden muss.
  + **azureml-pipeline-steps**
    + Die Flags `enable_default_model_output` und `enable_default_metrics_output` wurden zu `AutoMLStep` hinzugefügt. Diese Flags können zum Aktivieren/Deaktivieren der Standardausgaben verwendet werden.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK für Python v1.10.0

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + Wenn bei Verwendung von AutoML ein Pfad an das AutoMLConfig-Objekt übergeben wird, der nicht bereits vorhanden ist, wird er automatisch erstellt.
    + Benutzer können jetzt eine Zeitreihenhäufigkeit für Vorhersageaufgaben angeben, indem sie den Parameter `freq` verwenden.
  + **azureml-automl-runtime**
    + Wenn bei Verwendung von AutoML ein Pfad an das AutoMLConfig-Objekt übergeben wird, der nicht bereits vorhanden ist, wird er automatisch erstellt.
    + Benutzer können jetzt eine Zeitreihenhäufigkeit für Vorhersageaufgaben angeben, indem sie den Parameter `freq` verwenden.
    + AutoML-Vorhersagen unterstützen jetzt die parallele Auswertung, was für den Anwendungsfall gilt, dass die Länge eines Test- oder Validierungssatzes länger als der eingegebene Horizont ist und der bekannte y_pred-Wert als Vorhersagekontext verwendet wird.
  + **azureml-core**
    + Warnmeldungen werden ausgegeben, wenn in einer Ausführung keine Dateien vom Datenspeicher heruntergeladen wurden.
    + Die Dokumentation für `skip_validation` wurde zu `Datastore.register_azure_sql_database method` hinzugefügt.
    + Benutzer müssen ein Upgrade auf SDK v1.10.0 oder höher durchführen, um einen automatisch genehmigten privaten Endpunkt zu erstellen. Dies umfasst die Notebook-Ressource, die hinter dem VNet verwendbar ist.
    + NotebookInfo wurde in der Antwort zum Abrufen des Arbeitsbereichs verfügbar gemacht.
    + Änderungen, die dafür sorgen, dass Aufrufe zum Auflisten und Abrufen von Computezielen bei einer Remoteausführung erfolgreich ausgeführt werden. SDK-Funktionen zum Abrufen von Computezielen und zum Auflisten von Computezielen von Arbeitsbereichen funktionieren jetzt auch bei Remoteausführungen.
    + Nachrichten zur Einstellung wurden zu den Klassenbeschreibungen für azureml.core.image-Klassen hinzugefügt.
    + Wenn bei der Erstellung des privaten Endpunkts des Arbeitsbereichs ein Fehler auftritt, wird eine Ausnahme ausgelöst und der Arbeitsbereich sowie die abhängigen Ressourcen werden bereinigt.
    + SKU-Upgrade für den Arbeitsbereich wird für die Aktualisierungsmethode für den Arbeitsbereich unterstützt.
  + **azureml-datadrift**
    + Update der matplotlib-Version von 3.0.2 auf 3.2.1 wurde zur Unterstützung von Python 3.8 durchgeführt.
  + **azureml-dataprep**
    + Unterstützung von Datenquellen mit Web-URL wurde mit der Anforderung `Range` oder `Head` hinzugefügt. 
    + Stabilität beim Einbinden und Herunterladen von Dateidatasets wurde verbessert.
  + **azureml-train-automl-client**
    + Es wurden Probleme behoben, die sich auf das Entfernen von `RequirementParseError` aus Einrichtungstools beziehen.
    + Verwenden Sie „docker“ anstelle von „conda“ für lokale Ausführungen, die über „compute_target='local'“ übermittelt werden.
    + Die auf der Konsole ausgegebene Iterationsdauer wurde korrigiert. Früher wurde die Iterationsdauer gelegentlich als „Endzeit der Ausführung minus Erstellungszeit der Ausführung“ ausgegeben. Sie wurde korrigiert und entspricht nun der Endzeit der Ausführung minus der Startzeit der Ausführung.
    + Wenn bei Verwendung von AutoML ein Pfad an das AutoMLConfig-Objekt übergeben wird, der nicht bereits vorhanden ist, wird er automatisch erstellt.
    + Benutzer können jetzt eine Zeitreihenhäufigkeit für Vorhersageaufgaben angeben, indem sie den Parameter `freq` verwenden.
  + **azureml-train-automl-runtime**
    + Konsolenausgabe wurde verbessert, wenn bei Erläuterungen des besten Modells Fehler aufgetreten sind.
    + Eingabeparameter in „blocked_models“ umbenannt, um einen sensiblen Ausdruck zu entfernen.
      + Eingabeparameter in „allowed_models“ umbenannt, um einen sensiblen Ausdruck zu entfernen.
    + Benutzer können jetzt eine Zeitreihenhäufigkeit für Vorhersageaufgaben angeben, indem sie den Parameter `freq` verwenden.

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK für Python v1.9.0

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + Get_model_path() wurde durch die Umgebungsvariable AZUREML_MODEL_DIR im automatisch generierten AutoML-Bewertungsskript ersetzt. Außerdem wurde Telemetrie hinzugefügt, um Fehler beim Ausführen von „init()“ nachzuverfolgen.
    + Die Möglichkeit zur Angabe von `enable_cache` als Teil von AutoMLConfig wurde entfernt.
    + Ein Fehler wurde behoben, der bei der Ausführung bestimmter Vorhersagen mit Dienstfehlern auftreten konnte.
    + Die Fehlerbehandlung für bestimmte Modelle während `get_output` wurde verbessert.
    + Ein Problem beim Aufruf von „fitted_model.fit(X, y)“ für die Klassifizierung mit einem y-Transformator wurde behoben.
    + Der angepasste Forward-Fill-Imputer wurde für Vorhersageaufgaben aktiviert.
    + Eine neue ForecastingParameters-Klasse wird anstelle von Vorhersageparametern in einem Wörterbuchformat verwendet.
    + Die automatische Erkennung von Zielverzögerungen wurde verbessert.
    + Die eingeschränkte Verfügbarkeit der verteilten Featurisierung mit BERT über mehrere Knoten und mehrere GPUs wurde hinzugefügt.
  + **azureml-automl-runtime**
    + Prophet führt jetzt anstelle der multiplikativen die additive Saisonalitätsmodellierung aus.
    + Das Problem, dass bei kurzen Körnungen, die von langen Körnungen abweichende Frequenzen aufweisen, Fehler bei der Ausführung auftreten, wurde behoben.
  + **azureml-contrib-automl-dnn-vision**
    + Sammeln von System-/GPU-Statistiken und Protokollieren von Durchschnittswerten für Training und Bewertung
  + **azureml-contrib-mir**
    + Die Unterstützung für das Flag „enable-app-insights“ wurde in ManagedInferencing hinzugefügt.
  + **azureml-core**
    + Ein Validierungsparameter für diese APIs, mit dem die Validierung übersprungen werden kann, wenn die Datenquelle vom aktuellen Compute aus nicht zugänglich ist.
      + TabularDataset.time_before(end_time, include_boundary=True, validate=True)
      + TabularDataset.time_after(start_time, include_boundary=True, validate=True)
      + TabularDataset.time_recent(time_delta, include_boundary=True, validate=True)
      + TabularDataset.time_between(start_time, end_time, include_boundary=True, validate=True)
    + Die Frameworkunterstützung wurde für die Modellliste hinzugefügt und das NCD-AutoML-Beispiel im Notebook hinzugefügt.
    + Für „Datastore.register_azure_blob_container“ und „Datastore.register_azure_file_share“ (nur Optionen, die SAS-Token unterstützen) haben wir die Dokumentzeichenfolgen für das `sas_token`-Feld aktualisiert, um die Anforderungen an die Mindestberechtigungen für typische Lese- und Schreibszenarien einzubeziehen.
    + Der Parameter „_with_auth“ wurde in „ws.get_mlflow_tracking_uri()“ eingestellt.
  + **azureml-mlflow**
    + Die Unterstützung für die Bereitstellung von lokalen „file://“-Modellen mit AzureML-MLflow wurde hinzugefügt.
    + Der Parameter „_with_auth“ wurde in „ws.get_mlflow_tracking_uri()“ eingestellt.
  + **azureml-opendatasets**
    + Kürzlich veröffentlichte Datasets für die Covid-19-Nachverfolgung sind jetzt mit dem SDK verfügbar.
  + **azureml-pipeline-core**
    + Die Warnung beim Abmelden, wenn „azureml-defaults“ nicht als Teil von „pip-dependency“ einbezogen ist, wurde hinzugefügt.
    + Das Rendering von Hinweisen wurde verbessert.
    + Die Unterstützung für Zeilenumbrüche in Anführungszeichen beim Analysieren von Dateien mit Trennzeichen in PipelineOutputFileDataset wurde hinzugefügt.
    + Die PipelineDataset-Klasse wurde eingestellt. Weitere Informationen finden Sie unter https://aka.ms/dataset-deprecation. Informationen zur Verwendung von Datasets mit Pipeline finden Sie unter https://aka.ms/pipeline-with-dataset.
  + **azureml-pipeline-steps**
    + Die Dokumentation zu „azureml-pipeline-steps“ wurde aktualisiert.
    +  Die Unterstützung wurde in `load_yaml()` (ParallelRunConfig) für Benutzer hinzugefügt, um Umgebungen inline mit dem Rest der Konfiguration oder in einer separaten Datei zu definieren.
  + **azureml-train-automl-client**.
    + Die Möglichkeit zur Angabe von `enable_cache` als Teil von AutoMLConfig wurde entfernt.
  + **azureml-train-automl-runtime**
    + Die eingeschränkte Verfügbarkeit der verteilten Featurisierung mit BERT über mehrere Knoten und mehrere GPUs wurde hinzugefügt.
    + Die Fehlerbehandlung für inkompatible Pakete in ADB-basierten automatisierten Machine Learning-Ausführungen wurde hinzugefügt.
  + **azureml-widgets**
    + Die Dokumentation zu „azureml-widgets“ wurde aktualisiert.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK für Python v1.8.0
  
  + **Vorschaufunktionen**
    + **azureml-contrib-fairness** Das `azureml-contrib-fairness`-Paket bietet die Integration zwischen dem Open Source-Paket zur Bewertung der Fairness und Vermeidung von Unfairness mit [Fairlearn](https://fairlearn.github.io) und Azure Machine Learning Studio. Insbesondere ermöglicht das Paket das Hochladen von Modelldashboards zur Bewertung der Fairness als Teil einer AzureML-Ausführung und die Anzeige in Azure Machine Learning Studio.

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Die Unterstützung für das Abrufen von Protokollen des Init-Containers wurde hinzugefügt.
    + Neue CLI-Befehle zur Verwaltung von ComputeInstance wurden hinzugefügt.
  + **azureml-automl-core**
    + Benutzer können jetzt die Stack-Ensemble-Iteration für Zeitreihenaufgaben mit einer Warnung vor einer möglichen Überanpassung aktivieren.
    + Ein neuer Typ von Benutzerausnahme wurde hinzugefügt, der ausgelöst wird, wenn der Inhalt des Cachespeichers manipuliert wurde.
  + **azureml-automl-runtime**
    + Class Balancing Sweeping ist nicht mehr aktiviert, wenn der Benutzer die Featurisierung deaktiviert.  
  + **azureml-contrib-itp**
    + Der CmAks-Computetyp wird unterstützt. Sie können Ihren eigenen AKS-Cluster an den Arbeitsbereich für den Trainingsauftrag anfügen.
  + **azureml-contrib-notebook**
    + Die Dokumentation für das Paket „azureml-contrib-notebook“ wurde verbessert.
  + **azureml-contrib-pipeline-steps**
    + Die Dokumentation für das Paket „azureml-contrib--pipeline-steps“ wurde verbessert.
  + **azureml-core**
    + Die Funktionen „set_connection“, „get_connection“, „list_connections“ und „delete_connection“ wurden hinzugefügt, damit der Kunde mit der Arbeitsbereichsverbindungsressource arbeiten kann.
    + Die Dokumentation für das Paket „azureml-coore/azureml.exceptions“ wurde verbessert.
    + Die Dokumentation für das Paket „azureml-core“ wurde aktualisiert.
    + Die Dokumentation für die Klasse „ComputeInstance“ wurde aktualisiert.
    + Die Dokumentation für das Paket „azureml-core/azureml.core.compute“ wurde verbessert.
    + Die Dokumentation für Klassen, die sich auf Webdienste beziehen, wurden in „azureml-core“ verbessert.
    + Die Unterstützung für vom Benutzer ausgewählten Datenspeicher zum Speichern von Profilerstellungsdaten wurde hinzugefügt.
    + Die Eigenschaften „expand“ und „page_count“ wurden für die Modelllisten-API hinzugefügt.
    + Ein Fehler, bei dem das Entfernen der Überschreibungseigenschaft dazu führte, dass die übermittelte Ausführung mit einem Deserialisierungsfehler fehlschlug, wurde behoben.
    + Die inkonsistente Ordnerstruktur beim Herunterladen oder Einbinden eines FileDataset, das auf eine einzelne Datei verweist, wurde behoben.
    + Das Laden eines Datasets von Parquet-Dateien in „_spark_dataframe“ ist jetzt schneller und unterstützt alle Parquet- und Spark SQL-Datentypen.
    + Die Unterstützung für das Abrufen von Protokollen des Init-Containers wurde hinzugefügt.
    + AutoML-Ausführungen werden jetzt als untergeordnete Ausführung des Schritts zur parallelen Ausführung markiert.
  + **azureml-datadrift**
    + Die Dokumentation für das Paket „azureml-contrib-notebook“ wurde verbessert.
  + **azureml-dataprep**
    + Das Laden eines Datasets von Parquet-Dateien in „_spark_dataframe“ ist jetzt schneller und unterstützt alle Parquet- und Spark SQL-Datentypen.
    + Die Arbeitsspeicherverwaltung bei OutOfMemory-Problemen für „to_pandas_dataframe“ wurde verbessert.
  + **azureml-interpret**
    + azureml-interpret wurde auf die Verwendung von interpret-community Version 0.12* aktualisiert.
  + **azureml-mlflow**
    + Die Dokumentation für „azureml-mlflow“ wurde verbessert.
    + Die Unterstützung für die AML-Modellregistrierung mit MLFlow wurde hinzugefügt.
  + **azureml-opendatasets**
    + Unterstützung für Python 3.8 hinzugefügt
  + **azureml-pipeline-core**
    + Die Dokumentation für `PipelineDataset` wurde aktualisiert, um zu verdeutlichen, dass es sich um eine interne Klasse handelt.
    + ParallelRunStep wurde aktualisiert, um mehrere Werte für ein Argument zu akzeptieren. Beispiel: "--group_column_names", "Col1", "Col2", "Col3".
    + Die Anforderung „passthru_automl_config“ für die Verwendung von Zwischendaten mit AutoMLStep in Pipelines wurde entfernt.
  + **azureml-pipeline-steps**
    + Die Dokumentation für das Paket „azureml-pipeline-steps“ wurde verbessert.
    + Die Anforderung „passthru_automl_config“ für die Verwendung von Zwischendaten mit AutoMLStep in Pipelines wurde entfernt.
  + **azureml-telemetry**
    + Die Dokumentation für „azureml-telemetry“ wurde verbessert.
  + **azureml-train-automl-client**
    + Es wurde ein Fehler behoben, bei dem ein zweimaliger Aufruf von `experiment.submit()` für ein `AutoMLConfig`-Objekt zu einem anderen Verhalten führte.
    + Benutzer können jetzt die Stack-Ensemble-Iteration für Zeitreihenaufgaben mit einer Warnung vor einer möglichen Überanpassung aktivieren.
    + Das AutoML-Ausführungsverhalten zum Auslösen von „UserErrorException“ wurde geändert, wenn der Dienst einen Benutzerfehler auslöst.
    + Ein Fehler, der dazu führte, dass „azureml_automl.log“ nicht generiert wurde oder Protokolle fehlten, wenn ein AutoML-Experiment auf einem entfernten Computeziel durchgeführt wurde, wurde behoben.
    + Bei Klassifizierungsdatasets mit unausgeglichenen Klassen wenden wir einen Gewichtsausgleich an, wenn das Sweeper-Feature feststellt, dass bei Unterstichprobendaten der Gewichtsausgleich die Leistung der Klassifizierungsaufgabe um einen bestimmten Schwellenwert verbessert.
    + AutoML-Ausführungen werden jetzt als untergeordnete Ausführung des Schritts zur parallelen Ausführung markiert.
  + **azureml-train-automl-runtime**
    + Das AutoML-Ausführungsverhalten zum Auslösen von „UserErrorException“ wurde geändert, wenn der Dienst einen Benutzerfehler auslöst.
    + AutoML-Ausführungen werden jetzt als untergeordnete Ausführung des Schritts zur parallelen Ausführung markiert.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK für Python v1.7.0

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Das Entfernen der Erstellung von Modellprofilen aus „mir contrib“ durch Bereinigen von CLI-Befehlen und Paketabhängigkeiten wurde abgeschlossen. Die Erstellung von Modellprofilen ist im Kern verfügbar.
    + Führt ein Upgrade der minimalen Azure CLI-Version auf 2.3.0 durch.
  + **azureml-automl-core**
    + Die Ausnahmemeldung beim Featurisierungsschritt „fit_transform()“ aufgrund von benutzerdefinierten Transformatorparametern wurde verbessert.
    + Die Unterstützung für mehrere Sprachen für Deep Learning-Transformatormodellen wie BERT beim automatisierten maschinellen Lernen wurde hinzugefügt.
    + Der veraltete Parameter „lag_length“ wurde aus der Dokumentation entfernt.
    + Die Dokumentation zu den Vorhersageparametern wurde verbessert. Der Parameter „lag_length“ wurde eingestellt.
  + **azureml-automl-runtime**
    + Ein Fehler, der ausgelöst wird, wenn eine der kategorischen Spalten in der Vorhersage/Testzeit leer ist, wurde behoben.
    + Die Ausführungsfehler, die auftreten, wenn die Rückblickfeatures aktiviert werden und die Daten kurze Körnungen enthalten, wurden behoben.
    + Das Problem mit der duplizierten Zeitindex-Fehlermeldung wurde behoben, das aufgetreten ist, wenn Verzögerungen oder rollierende Fenster auf „Auto“ festgelegt wurden.
    + Es wurde ein Problem mit Prophet- und Arima-Modellen für Datasets behoben, die die Rückblickfeatures enthalten.
    + Die Unterstützung von Datumsangaben vor 1677-09-21 oder nach 2262-04-11 in anderen Spalten als „Datum und Uhrzeit“ in den Vorhersageaufgaben wurde hinzugefügt. Verbesserte Fehlermeldungen.
    + Die Dokumentation zu den Vorhersageparametern wurde verbessert. Der Parameter „lag_length“ wurde eingestellt.
    + Die Ausnahmemeldung beim Featurisierungsschritt „fit_transform()“ aufgrund von benutzerdefinierten Transformatorparametern wurde verbessert.
    + Die Unterstützung für mehrere Sprachen für Deep Learning-Transformatormodellen wie BERT beim automatisierten maschinellen Lernen wurde hinzugefügt.
    + Cachevorgänge, die zu einigen OSErrors führen, lösen einen Benutzerfehler aus.
    + Es wurden Prüfungen hinzugefügt, um sicherzustellen, dass Trainings- und Überprüfungsdaten dieselbe Anzahl und Auswahl von Spalten aufweisen.
    + Ein Problem mit dem automatisch generierten AutoML-Bewertungsskript, das aufgetreten ist, wenn die Daten Anführungszeichen enthalten, wurde behoben.
    + Erläuterungen für AutoML Prophet und zusammengestellte Modelle, die das Prophet-Modell enthalten, wurden aktiviert.
    + Bei einem kürzlich aufgetretenen Kundenproblem wurde ein Livewebsite-Fehler ermittelt, bei dem wir Nachrichten beim Class-Balancing-Sweeping protokollieren, selbst wenn die Class-Balancing-Logik nicht ordnungsgemäß aktiviert ist. Diese Protokolle/Meldungen werden mit diesem PR entfernt.
  + **azureml-cli-common**
    + Das Entfernen der Erstellung von Modellprofilen aus „mir contrib“ durch Bereinigen von CLI-Befehlen und Paketabhängigkeiten wurde abgeschlossen. Die Erstellung von Modellprofilen ist im Kern verfügbar.
  + **azureml-contrib-reinforcementlearning**
    + Auslastungstesttool
  + **azureml-core**
    + Die Dokumentation für „Script_run_config.py“ wurde aktualisiert.
    + Ein Fehler beim Drucken der Ausgabe der ausgeführten submit-pipeline-CLI wurde behoben.
    + Die Dokumentation für „azureml-core/azureml.data“ wurde verbessert.
    + Behebt ein Problem beim Abrufen von Speicherkonten mit dem Befehl „hdfs getconf“.
    + Die Dokumentation für „register_azure_blob_container“ und „register_azure_file_share“ wurde verbessert.
  + **azureml-datadrift**
    + Die Implementierung zum Deaktivieren und Aktivieren von Datendriftüberwachungen wurde verbessert.
  + **azureml-interpret**
    + Entfernen Sie im Erklärungsclient NaNs oder Infs vor der JSON-Serialisierung beim Upload von Artefakten.
    + Aktualisieren Sie auf die neueste Version von interpret-community, um Arbeitsspeicherfehler für globale Erklärungen mit vielen Features und Klassen zu verbessern.
    + Fügen Sie den optionalen Parameter „true_ys“ zum Erläuterungsupload hinzu, um zusätzliche Features auf der Studio-Benutzeroberfläche zu aktivieren.
    + Die Leistung von „download_model_explanations()“ und „list_model_explanations()“ wurde verbessert.
    + Es wurden kleine Optimierungen an Notebooks vorgenommen, um beim Debuggen zu helfen.
  + **azureml-opendatasets**
    + azureml-opendatasets benötigt azureml-dataprep Version 1.4.0 oder höher. Die Warnung beim Erkennen einer niedrigeren Version wurde hinzugefügt.
  + **azureml-pipeline-core**
    + Diese Änderung ermöglicht es dem Benutzer, beim Aufruf von „module.Publish_python_script“ eine optionale „runconfig“ für „moduleVersion“ bereitzustellen.
    + Das Aktivieren eines Knotenkontos kann ein Pipelineparameter in ParallelRunStep in „azureml.pipeline.steps“ sein.
  + **azureml-pipeline-steps**
    + Diese Änderung ermöglicht es dem Benutzer, beim Aufruf von „module.Publish_python_script“ eine optionale „runconfig“ für „moduleVersion“ bereitzustellen.
  + **azureml-train-automl-client**
    + Die Unterstützung für mehrere Sprachen für Deep Learning-Transformatormodellen wie BERT beim automatisierten maschinellen Lernen wurde hinzugefügt.
    + Der veraltete Parameter „lag_length“ wurde aus der Dokumentation entfernt.
    + Die Dokumentation zu den Vorhersageparametern wurde verbessert. Der Parameter „lag_length“ wurde eingestellt.
  + **azureml-train-automl-runtime**
    + Erläuterungen für AutoML Prophet und zusammengestellte Modelle, die das Prophet-Modell enthalten, wurden aktiviert.
    + Die Dokumentation für „azureml-train-automl-*“-Pakete wurde aktualisiert.
  + **azureml-train-core**
    + Die TensorFlow-Version 2.1 bei der PyTorch-Schätzung wird nun unterstützt.
    + Es wurden Verbesserungen am Paket „azureml-train-core“ vorgenommen.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK für Python v1.6.0

+ **Neue Features**
  + **azureml-automl-runtime**
    + AutoML unterstützt Kundenprognosen jetzt über den vorgegebenen maximalen Horizont hinaus, ohne dass das Modell neu trainiert werden muss. Wenn das Prognoseziel ferner in der Zukunft liegt als der angegebene maximale Horizont, erstellt die forecast()-Funktion mit einem rekursiven Betriebsmodus dennoch gezielte Vorhersagen für den späteren Zeitpunkt. Die Abbildung der neuen Funktion finden Sie im Abschnitt „Forecasting farther than the maximum horizon“ (Vorhersagen über den maximalen Horizont hinaus) des Notebooks „forecasting-forecast-function“ in diesem [Ordner](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning).
  
  + **azureml-pipeline-steps**
    + ParallelRunStep ist nun herausgegeben worden und Teil des **azureml-pipeline-steps**-Pakets. Vorhandenes ParallelRunStep im **azureml-contrib-pipeline-steps**-Paket ist veraltet. Änderungen gegenüber der öffentlichen Vorschauversion:
      + Der optional konfigurierbare Parameter `run_max_try` zum Steuern der maximalen Anzahl von Aufrufen der Run-Methode für einen beliebigen Batch wurde hinzugefügt. Der Standardwert ist 3.
      + Es werden keine PipelineParameters mehr automatisch generiert. Die folgenden konfigurierbaren Werte können explizit als PipelineParameter festgelegt werden.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + Der Standardwert für process_count_per_node wird in 1 geändert. Der Benutzer sollte diesen Wert optimieren, um die Leistung zu verbessern. Als Vorgehensweise hat sich bewährt, die Anzahl der GPU- oder CPU-Knoten festzulegen.
      + ParallelRunStep fügt keine Pakete ein. Der Benutzer muss **azureml-core** und **azureml-dataprep[Pandas, fuse]** -Pakete in die Umgebungsdefinition einschließen. Wenn ein benutzerdefiniertes Docker-Image mit user_managed_dependencies verwendet wird, muss der Benutzer auf dem Image Conda installieren.
      
+ **Wichtige Änderungen**
  + **azureml-pipeline-steps**
    + Die Verwendung von „azureml. dprep. Dataflow“ als gültiger Typ der Eingabe für AutoMLConfig wurde als veraltet markiert.
  + **azureml-train-automl-client**
    + Die Verwendung von „azureml. dprep. Dataflow“ als gültiger Typ der Eingabe für AutoMLConfig wurde als veraltet markiert.

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + Es wurde der Fehler behoben, dass während `get_output` eine Warnung ausgegeben werden konnte, die den Benutzer zur Herabstufung des Clients aufgefordert hat.
    + Der Mac wurde dahingehend aktualisiert, dass er sich auf cudatoolkit=9.0 verlässt, da es noch nicht in Version 10 verfügbar ist.
    + Einschränkungen für prophet- und xgboost-Modelle beim Trainieren über Remotecompute wurden entfernt.
    + Verbesserte Protokollierung in AutoML
    + Die Fehlerbehandlung für die benutzerdefinierte Featurisierung in Prognosetasks wurde verbessert.
    + Es wurden Funktionen hinzugefügt, um Benutzern zu ermöglichen, isolierte Features zum Generieren von Prognosen einzuschließen.
    + Fehlermeldungen wurden dahingehend aktualisiert, dass Benutzerfehler ordnungsgemäß angezeigt werden.
    + Unterstützung für die Verwendung von cv_split_column_names mit training_data
    + Protokollierung der Ausnahmemeldung und Ablaufverfolgung wurden aktualisiert.
  + **azureml-automl-runtime**
    + Aktivieren der Schutzmaßnahmen für Imputation fehlender Werte in Vorhersagen.
    + Verbesserte Protokollierung in AutoML
    + Differenzierte Fehlerbehandlung für Ausnahmen bei der Datenvorbereitung wurde hinzugefügt.
    + Einschränkungen für phrophet- und xgboost-Modelle beim Trainieren über Remotecompute wurden entfernt.
    + In `azureml-train-automl-runtime` und `azureml-automl-runtime` wurden Abhängigkeiten für `pytorch`, `scipy` und `cudatoolkit` aktualisiert. Wir unterstützen jetzt `pytorch==1.4.0`, `scipy>=1.0.0,<=1.3.1` und `cudatoolkit==10.1.243`.
    + Die Fehlerbehandlung für die benutzerdefinierte Featurisierung in Prognosetasks wurde verbessert.
    + Der Häufigkeitserkennungsmechanismus für das Vorhersagendataset wurde verbessert.
    + Problem beim Prophet-Modelltraining für einige Datasets wurde behoben.
    + Die automatische Erkennung des maximalem Horizonts während der Vorhersage wurde verbessert.
    + Es wurden Funktionen hinzugefügt, um Benutzern zu ermöglichen, isolierte Features zum Generieren von Prognosen einzuschließen.
    +  In der Vorhersagefunktion wurden Funktionen hinzugefügt, um die Bereitstellung von Vorhersagen über den trainierten Horizont hinaus zu ermöglichen, ohne dass das Modell erneut trainiert werden muss.
    + Unterstützung für die Verwendung von cv_split_column_names mit training_data
  + **azureml-contrib-automl-dnn-forecasting**
    + Verbesserte Protokollierung in AutoML
  + **azureml-contrib-mir**
    + Unterstützung für Windows-Dienste in ManagedInferencing wurde hinzugefügt.
    + Alte MIR-Workflows wie z. B. Anfügen von MIR-Compute, SingleModelMirWebservice-Klasse wurden entfernt – Bereinigen der Modellprofilerstellung wurde im contrib-mir-Paket platziert.
  + **azureml-contrib-pipeline-steps**
    + Kleinere Korrektur der YAML-Unterstützung
    + ParallelRunStep wird in allgemeiner Verfügbarkeit herausgegeben – azureml.contrib.pipeline.steps wurde mit einem Veraltungshinweis versehen und zu azureml.pipeline.steps verschoben.
  + **azureml-contrib-reinforcementlearning**
    + RL-Auslastungstesttool
    + RL-Schätzer hat intelligente Standardwerte.
  + **azureml-core**
    + Alte MIR-Workflows wie z. B. Anfügen von MIR-Compute, SingleModelMirWebservice-Klasse wurden entfernt – Bereinigen der Modellprofilerstellung wurde im contrib-mir-Paket platziert.
    + Die Informationen, die Benutzer bei einem Profilerstellungsfehler erhalten, wurden korrigiert: die Anforderungs-ID wurde eingeschlossen, und die Nachricht wurde verständlicher formuliert. Neuer Profilerstellungsworkflow wurde Profilerstellungsrunnern hinzugefügt.
    + Fehlertext bei Datasetausführungsfehlern verbessert.
    + CLI-Unterstützung für Private Link im Arbeitsbereich wurde hinzugefügt.
    + `Dataset.Tabular.from_json_lines_files` wurde ein optionaler Parameter `invalid_lines` hinzugefügt, der angibt, wie Zeilen behandelt werden sollen, die ungültiges JSON-Format enthalten.
    + Wir werden die ausführungsbasierte Erstellung von Compute im nächsten Release als veraltet kennzeichnen. Sie sollten einen tatsächlichen Amlcompute-Cluster als dauerhaftes Computeziel erstellen und den Clusternamen in Ihrer Laufzeitkonfiguration als Computeziel verwenden. Siehe Beispielnotebook hier: aka.ms/amlcomputenb.
    + Fehlermeldungen bei Datasetausführungsfehlern verbessert.
  + **azureml-dataprep**
    + Es wurde eine Warnung erstellt, die PyArrow-Version expliziter zu aktualisieren.
    + Verbesserte Fehlerbehandlung und Rückmeldung bei einem Fehler beim Ausführen des Datenflusses.
  + **azureml-interpret**
    + Dokumentation wurde für azureml-interpret-Paket aktualisiert.
    + Interpretierbarkeitspakete und Notebooks wurden korrigiert, sodass sie mit dem neuesten sklearn-Update kompatibel sind.
  + **azureml-opendatasets**
    + „None“ (Keine) wird zurückgegeben, wenn keine Daten zurückgegeben werden.
    + Verbessern der Leistung von to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Schnellkorrektur für ParallelRunStep bei Unterbrechung des Ladens von YAML.
    + ParallelRunStep wird in allgemeiner Verfügbarkeit herausgegeben – azureml.contrib.pipeline.steps wurde mit einem Veraltungshinweis versehen und zu azureml.pipeline.steps verschoben – zu den neuen Features zählen: 1. Datasets als PipelineParameter 2. Neuer Parameter „run_max_retry 3“. Konfigurierbarer append_row-Ausgabedateiname
  + **azureml-pipeline-steps**
    + azureml.dprep.Dataflow wurde als gültiger Typ für Eingabedaten als veraltet markiert.
    + Schnellkorrektur für ParallelRunStep bei Unterbrechung des Ladens von YAML.
    + ParallelRunStep wird in allgemeiner Verfügbarkeit herausgegeben – azureml.contrib.pipeline.steps wurde mit einem Veraltungshinweis versehen und zu azureml.pipeline.steps verschoben – zu den neuen Features zählen:
      + Datasets als PipelineParameter
      + Neuer Parameter „run_max_retry“
      + Konfigurierbarer append_row-Ausgabedateiname
  + **azureml-telemetry**
    + Protokollierung der Ausnahmemeldung und Ablaufverfolgung wurden aktualisiert.
  + **azureml-train-automl-client**
    + Verbesserte Protokollierung in AutoML
    + Fehlermeldungen wurden dahingehend aktualisiert, dass Benutzerfehler ordnungsgemäß angezeigt werden.
    + Unterstützung für die Verwendung von cv_split_column_names mit training_data
    + azureml.dprep.Dataflow wurde als gültiger Typ für Eingabedaten als veraltet markiert.
    + Der Mac wurde dahingehend aktualisiert, dass er sich auf cudatoolkit=9.0 verlässt, da es noch nicht in Version 10 verfügbar ist.
    + Einschränkungen für phrophet- und xgboost-Modelle beim Trainieren über Remotecompute wurden entfernt.
    + In `azureml-train-automl-runtime` und `azureml-automl-runtime` wurden Abhängigkeiten für `pytorch`, `scipy` und `cudatoolkit` aktualisiert. Wir unterstützen jetzt `pytorch==1.4.0`, `scipy>=1.0.0,<=1.3.1` und `cudatoolkit==10.1.243`.
    + Es wurden Funktionen hinzugefügt, um Benutzern zu ermöglichen, isolierte Features zum Generieren von Prognosen einzuschließen.
  + **azureml-train-automl-runtime**
    + Verbesserte Protokollierung in AutoML
    + Differenzierte Fehlerbehandlung für Ausnahmen bei der Datenvorbereitung wurde hinzugefügt.
    + Einschränkungen für phrophet- und xgboost-Modelle beim Trainieren über Remotecompute wurden entfernt.
    + In `azureml-train-automl-runtime` und `azureml-automl-runtime` wurden Abhängigkeiten für `pytorch`, `scipy` und `cudatoolkit` aktualisiert. Wir unterstützen jetzt `pytorch==1.4.0`, `scipy>=1.0.0,<=1.3.1` und `cudatoolkit==10.1.243`.
    + Fehlermeldungen wurden dahingehend aktualisiert, dass Benutzerfehler ordnungsgemäß angezeigt werden.
    + Unterstützung für die Verwendung von cv_split_column_names mit training_data
  + **azureml-train-core**
    + Es wurde ein neuer Satz von HyperDrive-spezifischen Ausnahmen hinzugefügt. azureml.train.hyperdrive löst jetzt ausführliche Ausnahmen aus.
  + **azureml-widgets**
    + AzureML-Widgets werden nicht in JupyterLab angezeigt.
  

## <a name="2020-05-11"></a>11.05.2020

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Azure Machine Learning SDK für Python v1.5.0

+ **Neue Features**
  + **Vorschaufunktionen**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning bietet im Rahmen einer Vorschauversion Unterstützung für vertiefendes Erlernen mithilfe des [Ray](https://ray.io)-Frameworks. `ReinforcementLearningEstimator` ermöglicht das Training von Agents für das vertiefende Lernen für GPU- und CPU-Computeziele in Azure Machine Learning.

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + „azure-cli-ml“ behebt ein versehentlich übersehenes Problem mit einem Warnungsprotokoll aus einer vorangegangen Pull Request. Das Protokoll dient dem Debuggen und wurde versehentlich übersehen.
    + Die Fehlerbehebung besteht darin, Clients über Fehler in Teilen der Profilerstellung zu informieren.
  + **azureml-automl-core**
    + „azureml-automl-core“ beschleunigt das Prophet/AutoArima-Modell bei AutoML-Vorhersagen, indem parallele Anpassungen für die Zeitreihen ermöglicht werden, wenn Datasets über mehrere Zeitreihen verfügen. Es wird empfohlen, in AutoMLConfig „max_cores_per_iteration = –1“ festzulegen, d. h., dass alle verfügbaren CPU-Kerne verwendet werden, um von diesem neuen Feature profitieren zu können.
    + Es wurde ein KeyError beim Drucken von Schutzmaßnahmen in der Konsolenschnittstelle behoben.
    + Die Fehlermeldung für experimentation_timeout_hours wurde korrigiert.
    + Tensorflow-Modelle für AutoML wurden als veraltet gekennzeichnet.
  + **azureml-automl-runtime**
    + Die Fehlermeldung für experimentation_timeout_hours wurde korrigiert.
    + Es wurde ein Problem behoben, bei dem eine nicht klassifizierte Ausnahme beim Versuch auftrat, eine Deserialisierung im Cachespeicher durchzuführen.
    + „azureml-automl-core“ beschleunigt das Prophet/AutoArima-Modell bei AutoML-Vorhersagen, indem parallele Anpassungen für die Zeitreihen ermöglicht werden, wenn Datasets über mehrere Zeitreihen verfügen.
    + Es wurde ein Fehler bei Vorhersagen mit aktiviertem rollierenden Zeitfenster für Datasets behoben, bei dem Test/Vorhersage keine Körnungen aus dem Trainingsset enthielten.
    + Mit fehlenden Daten kann jetzt besser umgegangen werden.
    + Ein Fehler bei Vorhersageintervallen während Vorhersagen für Datasets wurde behoben. Dabei waren Zeitreihen enthalten, deren zeitliche Ausrichtung fehlerhaft war.
    + Eine bessere Validierung der Datenform für Vorhersageaufgaben wurde hinzugefügt.
    + Die Häufigkeit der Suche wurde optimiert.
    + Es wurde eine optimierte Fehlermeldung erstellt, wenn die Kreuzvalidierungsteilmengen für Vorhersageaufgaben nicht generiert werden können.
    + Die Konsolenschnittstelle für das ordnungsgemäße Drucken von Schutzmaßnahmen bei fehlenden Werten wurde optimiert.
    + Bei cv_split_indices-Eingaben in AutoMLConfig werden Datentypüberprüfungen erzwungen.
  + **azureml-cli-common**
    + Die Fehlerbehebung besteht darin, Clients über Fehler in Teilen der Profilerstellung zu informieren.
  + **azureml-contrib-mir**
    + Fügt die Klasse „azureml.contrib.mir.RevisionStatus“ hinzu, wo sich Informationen zu der aktuell bereitgestellten MIR-Revision und die aktuell vom Benutzer angegebene Version finden. Dieses Klasse ist im MirWebservice-Objekt unter dem Attribut „deployment_status“ enthalten.
    + Außerdem werden Updates auf Webdienste des Typs „MirWebservice“ und der untergeordneten Klasse „SingleModelMirWebservice“ ermöglicht.
  + **azureml-contrib-reinforcementlearning**
    + Es wurde Unterstützung für Ray 0.8.3 hinzugefügt.
    + AmlWindowsCompute unterstützt Azure Files nur als eingebundenen Speicher.
    + health_check_timeout wurde umbenannt in health_check_timeout_seconds.
    + Mehrere Beschreibungen von Klassen und Methoden wurden optimiert.
  + **azureml-core**
    + Ab sofort sind Konvertierungen von WASB zu Blob in den Azure Government- und China-Clouds möglich.
    + Es wurde ein Fehler behoben, sodass Benutzer mit Leseberechtigung die Befehle zum Ausführen der Befehlszeilenschnittstelle in Azure Machine Learning ausführen können, um Informationen zur Ausführung zu erhalten.
    + Eine unnötige Protokollierung während Remoteausführungen mit Eingabedatasets in Azure Machine Learning wurde entfernt.
    + RCranPackage unterstützt nun den Parameter „Version“ für die CRAN-Paketversion.
    + Die Fehlerbehebung besteht darin, Clients über Fehler in Teilen der Profilerstellung zu informieren.
    + Für azureml-core können Gleitkommazahlen nun im europäischen Stil verarbeitet werden.
    + Im Azure Machine Learning SDK stehen in Arbeitsbereichen nun Private Link-Features zur Verfügung.
    + Beim Erstellen eines TabularDataset-Elements mithilfe von `from_delimited_files` können Sie angeben, ob leere Werte als „Keine“ oder als leere Zeichenfolge geladen werden sollen. Dafür können Sie das boolesche Argument `empty_as_string` festlegen.
    + In Datasets können Gleitkommazahlen nun im europäischen Stil verarbeitet werden.
    + Es wurden optimierte Fehlermeldungen zu Fehlern bei eingebundenen Datasets hinzugefügt.
  + **azureml-datadrift**
    + Die Datendriftergebnisse bei Abfragen aus dem SDK wiesen einen Fehler auf, bei dem nicht zwischen minimalen, maximalen und mittleren Featuremetriken differenziert wurde, was zu doppelten Werten führte. Dieses Problem wurde behoben, indem das Ziel oder die Baseline als Präfix vor den Metriknamen gestellt wird. Zuvor lautete die Syntax wie folgt: duplicate min, max, mean. Sie wurde nun geändert in: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + Die Verarbeitung von Python-Umgebungen mit eingeschränkten Schreibberechtigungen wurde verbessert, indem dafür gesorgt wurde, dass .NET-Abhängigkeiten für die Datenübermittlung erforderlich sind.
    + Die Dataflowerstellung für Dateien mit vorangestellten leeren Datensätzen wurde verbessert.
    + Es wurden Fehlerbehandlungsoptionen für `to_partition_iterator` ähnlich wie bei `to_pandas_dataframe` hinzugefügt.
  + **azureml-interpret**
    + Die Längenbeschränkungen für Erklärungspfade wurden verringert, um die Wahrscheinlichkeit zu senken, dass die Begrenzung von Windows überschritten wird.
    + Es wurde eine Fehlerbehebung für platzsparende Erklärungen hinzugefügt, die mit Mimic Explainer mithilfe eines linearen Surrogatmodells erstellt wurden.
  + **azureml-opendatasets**
    + Es wurde ein Problem behoben, bei dem die MNIST-Spalten als Zeichenfolge analysiert wurden. Sie sollten jedoch als Integer analysiert werden.
  + **azureml-pipeline-core**
    + Hier wird es ermöglicht, Ausgaben erneut zu generieren, wenn ein Modul verwendet wird, das in einer ModuleStep-Klasse eingebettet ist.
  + **azureml-train-automl-client**
    + Tensorflow-Modelle für AutoML wurden als veraltet gekennzeichnet.
    + Ein Fehler wurde behoben, bei dem Benutzer nicht unterstützte Algorithmen im lokalen Modus auf die Zulassungsliste setzen konnten.
    + Die Dokumentation zu AutoMLConfig wurde optimiert.
    + Bei cv_split_indices-Eingaben in AutoMLConfig werden Datentypüberprüfungen erzwungen.
    + Es wurde ein Problem behoben, bei dem AutoML-Ausführungen bei show_output fehlschlugen.
  + **azureml-train-automl-runtime**
    + Es wurde ein Fehler bei Ensembleiterationen behoben, bei dem ein Timeout für Modelldownloads nicht ordnungsgemäß ausgelöst werden konnte.
  + **azureml-train-core**
    + Ein Rechtschreibfehler bei der Klasse „azureml.train.dnn.Nccl“ wurde behoben.
    + Die PyTorch-Version 1.5 bei der PyTorch-Schätzung wird nun unterstützt.
    + Es wurde ein Fehler behoben, bei dem ein Frameworkimage in der Azure Government-Region bei der Verwendung von Trainingsframeworkschätzern nicht abgerufen werden konnte.

  
## <a name="2020-05-04"></a>2020-05-04
**Neue Notebookfunktionen**

Sie haben jetzt die Möglichkeit, Machine Learning-Notebooks und -Dateien direkt in der Studioweboberfläche von Azure Machine Learning zu erstellen, zu bearbeiten und freizugeben. Sie können alle Klassen und Methoden verwenden, die im [Python-SDK für Azure Machine Learning](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) aus diesen Notebooks verfügbar sind. [Erste Schritte](./how-to-run-jupyter-notebooks.md)

**Neue Funktionen:**

+ Verbesserter Editor (Monaco-Editor), der von VS-Code verwendet wird 
+ Verbesserungen an Benutzeroberfläche/Funktionalität
+ Zellensymbolleiste
+ Neue Notebooksymbolleiste und Compute-Steuerelemente
+ Notebookstatusleiste 
+ Inline-Kernel-Wechsel
+ R-Unterstützung
+ Verbesserungen bei Barrierefreiheit und Lokalisierung
+ Befehlspalette
+ Weitere Tastenkombinationen
+ Automatisches Speichern
+ Verbesserte Leistung und Zuverlässigkeit

Greifen Sie von Studio aus auf die folgenden webbasierten Erstellungstools zu:
    
| Webbasiertes Tool  |     BESCHREIBUNG  |
|---|---|
| Azure ML Studio-Notebooks   |     Erstklassige Erstellung von Notebookdateien und Unterstützung aller Vorgänge im Azure ML Python-SDK. | 

## <a name="2020-04-27"></a>27.04.2020

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK für Python v1.4.0

+ **Neue Features**
  + AmlCompute-Clusters unterstützen jetzt zum Zeitpunkt der Bereitstellung die Einrichtung einer verwalteten Identität im Cluster. Geben Sie einfach an, ob Sie eine vom System zugewiesene Identität oder eine vom Benutzer zugewiesene Identität verwenden möchten, und geben Sie für eine vom Benutzer zugewiesenen Identität eine identityId an. Anschließend können Sie Berechtigungen für den Zugriff auf verschiedene Ressourcen wie Speicher oder ACR so einrichten, dass die Identität der Computerressource sicher auf die Daten zugreift. Der aktuell von AmlCompute verwendete tokenbasierte Ansatz wird in diesem Fall nicht länger verwendet. Weitere Informationen zu den Parametern finden Sie in unserer SDK-Referenz.
  

+ **Wichtige Änderungen**
  + AmlCompute-Cluster haben bisher eine Previewfunktion zur ausführungsbasierten Erstellung unterstützt, die in zwei Wochen eingestellt werden soll. Sie können weiterhin über die Amlcompute-Klasse persistente Computeziele erstellen, das Angeben des Bezeichners „amlcompute“ als Computeziel beim Ausführen der Konfiguration wird in naher Zukunft jedoch nicht unterstützt. 

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-runtime**
    + Aktivieren der Unterstützung für nicht hashbaren Typ beim Berechnen der Anzahl von eindeutigen Werten in einer Spalte.
  + **azureml-core**
    + Verbesserte Stabilität beim Lesen aus Azure Blob Storage unter Verwendung eines tabellarischen Datasets.
    + Verbesserte Dokumentation für den `grant_workspace_msi`-Parameter für `Datastore.register_azure_blob_store`.
    + Es wurde ein Fehler bei `datastore.upload` behoben, damit `src_dir`-Argumente, die auf `/` oder `\` enden, unterstützt werden.
    + Es wurde eine Fehlermeldung hinzugefügt, die eine Aktion erfordert und beim Versuch angezeigt wird, Daten in einen Azure Blob Storage-Datenspeicher hochzuladen, der weder über einen Zugriffsschlüssel noch über ein SAS-Token verfügt.
  + **azureml-interpret**
    + Es wurde eine Obergrenze für die Visualisierungsdaten bei hochgeladenen Erklärungen hinzugefügt.
  + **azureml-train-automl-client**
    + Explizite Überprüfung, ob die Parameter „label_column_name“ und „weight_column_name“ für AutoMLConfig vom Typ Zeichenfolge sind.
  + **azureml-contrib-pipeline-steps**
    + „ParallelRunStep“ unterstützt jetzt ein Dataset als Pipelineparameter. Der Benutzer kann eine Pipeline mit einem Beispieldataset erstellen und für eine neue Pipelineausführung ein anderes Eingabedataset desselben Typs (Datei oder tabellarisch) auswählen.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK für Python v1.3.0

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + Zusätzliche Telemetrie zu Vorgängen nach dem Training wurde hinzugefügt.
    + Automatisches ARIMA-Training durch CSS-Training (Conditional Sum of Squares, bedingte Quadratsumme) für Reihen über 100 wurde beschleunigt. Die verwendete Länge wird als Konstante ARIMA_TRIGGER_CSS_TRAINING_LENGTH in der TimeSeriesInternal-Klasse unter „/src/azureml-automl-core/azureml/automl/core/shared/constants.py“ gespeichert wird.
    + Die Benutzerprotokollierung von Vorhersageausführungen wurde verbessert. Nun werden weitere Informationen zur derzeit ausgeführten Phase im Protokoll aufgeführt.
    + target_rolling_window_size kann nicht mehr auf Werte unter 2 festgelegt werden.
  + **azureml-automl-runtime**
    + Die angezeigte Fehlermeldung, wenn doppelte Zeitstempel gefunden werden, wurde verbessert.
    + target_rolling_window_size kann nicht mehr auf Werte unter 2 festgelegt werden.
    + Der Fehler bei Verzögerungsimputation wurde korrigiert. Das Problem wurde durch die unzureichende erforderliche Anzahl von Beobachtungen für die saisonale Zerlegung einer Reihe verursacht. Die Daten mit herausgerechneter Saisonalität werden verwendet, um eine Funktion für partielle Autokorrelation (PACF) zu berechnen, mit der die Verzögerungsdauer ermittelt wird.
    + Die Anpassung der Spaltenzweck-Featureerstellung für Vorhersageaufgaben wurde durch Konfiguration der Featureerstellung ermöglicht. Es werden nun numerische und kategorische Werte als Spaltenzweck für Vorhersageaufgaben unterstützt.
    + Die Anpassung der Löschungsspalten-Featureerstellung für Vorhersageaufgaben wurde durch Konfiguration der Featureerstellung ermöglicht.
    + Die Imputationsanpassung für Vorhersageaufgaben wurde durch Konfiguration der Featureerstellung ermöglicht. Imputation von konstanten Werten für die Zielspalte sowie Imputation der Werte mean, median, most_frequent und constant für Trainingsdaten werden jetzt unterstützt.
  + **azureml-contrib-pipeline-steps**
    + Zeichenfolgen-Computenamen, die an ParallelRunConfig übergeben werden sollen, werden akzeptiert.
  + **azureml-core**
    +  Die API Environment.clone(new_name) wurde hinzugefügt, die eine Kopie des Umgebungsobjekts erstellt.
    +  Environment.docker.base_dockerfile akzeptiert einen Dateipfad. Wenn eine Datei aufgelöst werden kann, wird der Inhalt in die Umgebungseigenschaft base_dockerfile gelesen.
    + Sich gegenseitig ausschließende Werte für base_image und base_dockerfile werden automatisch zurückgesetzt, wenn der Benutzer manuell einen Wert in Environment.docker festlegt.
    + In RSection wurde das Flag user_managed hinzugefügt, das angibt, ob die Umgebung von einem Benutzer oder von AzureML verwaltet wird.
    + Dataset: Ein Fehler beim Herunterladen von Datasets, wenn der Dateipfad Unicode-Zeichen enthielt, wurde behoben.
    + Dataset: Der Cachingmechanismus zum Einbinden von Datasets wurde verbessert berücksichtigt nun die minimale Speicherplatzanforderung in Azure Machine Learning Compute. Dadurch wird vermieden, dass der Knoten unbrauchbar und der Auftrag daher abgebrochen wird.
    + Dataset: Es wurde ein Index für die Zeitreihenspalte hinzugefügt, wenn Sie auf ein Zeitreihendataset als Pandas-Dataframes zugreifen. Dadurch wird der Zugriff auf zeitreihenbasierten Datenzugriff beschleunigt.  Zuvor erhielt der Index den gleichen Namen wie die Zeitstempelspalte, sodass Benutzer nicht genau wussten, welches die eigentliche Zeitstempelspalte und welches der Index war. Wir geben dem Index nun keinen spezifischen Namen mehr, da er nicht als Spalte verwendet werden sollte. 
    + Dataset: Ein Problem bei der Datasetauthentifizierung in der Sovereign Cloud wurde behoben.
    + Dataset: Der Fehler bei `Dataset.to_spark_dataframe` für aus Azure PostgreSQL-Datenspeichern erstellte Datasets wurde behoben.
  + **azureml-interpret**
    + Es wurden globale Ergebnisse zur Visualisierung hinzugefügt, wenn die Werte der lokalen Wichtigkeit eine geringe Dichte aufweisen.
    + azureml-interpret wurde auf die Verwendung von interpret-community 0.9.* aktualisiert.
    + Ein Problem beim Herunterladen der Erklärung mit Auswertungsdaten mit geringer Dichte wurde behoben.
    + Unterstützung für das Sparseformat des Erklärungsobjekts in AutoML wurde hinzugefügt.
  + **azureml-pipeline-core**
    + ComputeInstance wird nun als Computeziel in Pipelines unterstützt.
  + **azureml-train-automl-client**
    + Zusätzliche Telemetrie zu Vorgängen nach dem Training wurde hinzugefügt.
    + Die Regression bei früher Beendigung wurde korrigiert.
    + azureml.dprep.Dataflow wurde als gültiger Typ für Eingabedaten als veraltet markiert.
    +  Das Standardtimeout für AutoML-Experimente wurde auf sechs Tage geändert.
  + **azureml-train-automl-runtime**
    + Zusätzliche Telemetrie zu Vorgängen nach dem Training wurde hinzugefügt.
    + End-to-End-Unterstützung für AutoML mit geringer Dichte wurde hinzugefügt.
  + **azureml-opendatasets**
    + Zusätzliche Telemetrie für den Dienstmonitor wurde hinzugefügt.
    + Front Door wurde für Blobs aktiviert, um die Stabilität zu verbessern. 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK für Python v1.2.0

+ **Wichtige Änderungen**
  + Unterstützung von Python 2.7 eingestellt

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Fügt „--subscription-id“ zu `az ml model/computetarget/service`-Befehlen in der CLI hinzu
    + Die Unterstützung für die Übergabe von kundenseitig verwalteten Schlüsseln (CMK) vault_url, key_name und key_version für die ACI-Bereitstellung wurde hinzugefügt.
  + **azureml-automl-core** 
    + Die benutzerdefinierte Imputation mit konstantem Wert sowohl für X- als auch für Y-Datenvorhersageaufgaben wurde ermöglicht.
    + Das Problem bei der Anzeige von Fehlermeldungen für den Benutzer wurde behoben.    
  + **azureml-automl-runtime**
    + Das Problem bei der Vorhersage von Datasets, die Intervalle mit nur einer Zeile enthalten, wurde behoben.
    + Der Speicherbedarf der Vorhersageaufgaben wurde verringert.
    + Es wurden bessere Fehlermeldungen hinzugefügt, wenn die Zeitspalte ein falsches Format aufweist.
    + Die benutzerdefinierte Imputation mit konstantem Wert sowohl für X- als auch für Y-Datenvorhersageaufgaben wurde ermöglicht.
  + **azureml-core**
    + Unterstützung für das Laden von ServicePrincipal aus Umgebungsvariablen wurde hinzugefügt: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID und AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Es wurde ein neuer Parameter `support_multi_line` zu `Dataset.Tabular.from_delimited_files` hinzugefügt: Standardmäßig (`support_multi_line=False`) werden alle Zeilenumbrüche, einschließlich derjenigen in Feldwerten in Anführungszeichen, als Datensatzumbruch interpretiert. Das Lesen von Daten auf diese Weise ist schneller und für die parallele Ausführung auf mehreren CPU-Kernen optimiert. Es kann jedoch dazu führen, dass im Hintergrund mehr Datensätze mit falsch ausgerichteten Feldwerten erzeugt werden. Dieser sollte auf `True` festgelegt werden, wenn bekannt ist, dass die durch Trennzeichen getrennten Dateien Zeilenumbrüche in Anführungszeichen enthalten.
    + Es wurde die Möglichkeit hinzugefügt, ADLS Gen2 in der Azure Machine Learning-CLI zu registrieren.
    + Der Parameter „fine_grain_timestamp“ wurde in „timestamp“ und der Parameter „coarse_grain_timestamp“ in „partition_timestamp“ für die Methode „with_timestamp_columns()“ in „TabularDataset“ umbenannt, um die Verwendung der Parameter besser widerzuspiegeln.
    + Die maximale Länge der Experimentnamen wurde auf 255 erhöht.
  + **azureml-interpret**
    + „azureml-interpret“ wurde auf „interpret-community 0.7.*“ aktualisiert.
  + **azureml-sdk**
    + Es wurde zu Abhängigkeiten mit Tilde in kompatibler Version für die Unterstützung des Patchens in Vorabversionen und stabilen Versionen gewechselt.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK für Python v1.1.5

+ **Eingestellte Unterstützung von Features**
  + **Python 2.7**
    + Letzte Version mit Unterstützung von Python 2.7

+ **Wichtige Änderungen**
  + **Semantic Versioning 2.0.0**
    + Ab Version 1.1 führt das Azure ML-Python-SDK Semantic Versioning 2.0.0 ein. Weitere Informationen finden Sie [hier](https://semver.org/). Alle nachfolgenden Versionen folgen dem neuen Nummerierungsschema und dem Vertrag von Semantic Versioning. 

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Ändern Sie den Endpunkt-CLI-Befehlsnamen aus Gründen der Konsistenz von „az ml endpoint aks“ in „az ml endpoint real time“.
    + Aktualisieren der CLI-Installationsanweisungen für eine stabile und experimentelle Branch-CLI
    + Die Profilerstellung für einzelne Instanzen wurde korrigiert, um eine Empfehlung abzugeben, und im Core SDK zur Verfügung gestellt.
  + **azureml-automl-core**
    + Hat den Batchmodusrückschluss (mehrere Zeilen gleichzeitig) für AutoML-ONNX-Modelle aktiviert.
    + Hat die Erkennung der Häufigkeit für die Datasets verbessert, denen Daten fehlen oder die unregelmäßige Datenpunkte enthalten.
    + Hat die Möglichkeit zum Entfernen von Datenpunkten hinzugefügt, die nicht mit der bestimmenden Häufigkeit übereinstimmen.
    + Die Eingabe des Konstruktors wurde dahingehend geändert, dass eine Liste von Optionen zur Anwendung der Imputationsoptionen für entsprechende Spalten verwendet wird.
    + Die Fehlerprotokollierung wurde verbessert.
  + **azureml-automl-runtime**
    + Hat das Problem mit dem Fehler behoben, der ausgelöst wurde, wenn die im Trainingssatz nicht vorhandene Komponente im Testsatz aufgetreten ist.
    + Hat die y_query-Anforderung während der Bewertung für den Vorhersagedienst entfernt.
    + Es wurde das Problem bei der Vorhersage behoben, wenn das Dataset eine kurze Körnung mit langen Zeitlücken enthält.
    + Es wurde ein Problem behoben, wenn der automatische maximale Horizont aktiviert ist und die Datumsspalte Datumsangaben in Form von Zeichenfolgen enthält. Ordnungsgemäße Konvertierung und Fehlermeldungen wurden hinzugefügt, wenn eine Konvertierung bis heute nicht möglich ist.
    + Verwendung von NumPy und SciPy nativ zum Serialisieren und Deserialisieren von Zwischendaten für FileCacheStore (für lokale AutoML-Ausführungen)
    + Es wurde ein Fehler behoben, bei dem fehlerhafte untergeordnete Ausführungen im Ausführungsstatus hängen bleiben konnten.
    + Schnellere Featurebereitstellung
    + Behebung von Fehlern bei der Häufigkeitsprüfung während der Bewertung. Bei Vorhersageaufgaben ist nun keine genaue Übereinstimmung der Häufigkeit zwischen Trainings- und Testdataset mehr erforderlich.
    + Die Eingabe des Konstruktors wurde dahingehend geändert, dass eine Liste von Optionen zur Anwendung der Imputationsoptionen für entsprechende Spalten verwendet wird.
    + Fehler im Zusammenhang mit der Auswahl des Verzögerungstyps wurden behoben.
    + Der nicht klassifizierte Fehler, der bei den Datasets auftrat, die Intervalle mit der einzelnen Zeile enthielten, wurde behoben.
    + Das Problem mit der geringen Geschwindigkeit bei der Häufigkeitserfassung wurde behoben.
    + Behebt einen Fehler in der AutoML-Ausnahmebehandlung, der dazu führte, dass der eigentliche Grund für den Trainingsfehler durch ein „AttributeError“ ersetzt wurde.
  + **azureml-cli-common**
    + Die Profilerstellung für einzelne Instanzen wurde korrigiert, um eine Empfehlung abzugeben, und im Core SDK zur Verfügung gestellt.
  + **azureml-contrib-mir**
    + Fügt Funktionalität in der MirWebservice-Klasse hinzu, um das Zugriffstoken abzurufen.
    + Standardmäßiges Verwenden der Tokenauthentifizierung für MirWebservice während des Aufrufs von MirWebservice.run() – Nur aktualisieren, wenn der Aufruf einen Fehler aufweist.
    + Für die Bereitstellung von MirWebservice sind jetzt die richtigen SKUs [Standard_DS2_v2, Standard_F16, Standard_A2_v2] anstelle von [Ds2v2, A2v2 und F16] erforderlich.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep wurde der optionale Parameter side_inputs hinzugefügt. Dieser Parameter kann zum Einbinden von Ordnern im Container verwendet werden. Die derzeit unterstützten Typen sind DataReference und PipelineData.
    + Parameter, die in ParallelRunConfig übergeben werden, können nun durch Übergabe von Pipelineparametern überschrieben werden. Neue unterstützte Pipelineparameter: aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count und aml_process_count_per_node waren bereits Teil eines früheren Releases).
  + **azureml-core**
    + Die bereitgestellten AzureML-Webdienste werden jetzt standardmäßig auf `INFO`-Protokollierung festgelegt. Dies kann durch das Festlegen der Umgebungsvariablen `AZUREML_LOG_LEVEL` im bereitgestellten Dienst gesteuert werden.
    + Das Python SDK verwendet den Ermittlungsdienst, um den „api“-Endpunkt anstelle von „pipelines“ zu verwenden.
    + Wechseln Sie bei allen SDK-Aufrufen zu den neuen Routen.
    + Das Routing von ModelManagementService-Aufrufen wurde in eine neue, einheitliche Struktur geändert.
      + Die Methode zur Aktualisierung des Arbeitsbereichs wurde öffentlich zugänglich gemacht.
      + Der image_build_compute-Parameter wurde der Methode für die Arbeitsbereichsaktualisierung hinzugefügt, um dem Benutzer die Computeaktualisierung für die Imageerstellung zu ermöglichen.
    + Dem alten Workflow für die Profilerstellung wurden Hinweise zur Veraltung hinzugefügt. CPU- und Arbeitsspeichergrenzwerte wurden für die Profilerstellung korrigiert.
    + RSection wurde als Teil der Umgebung zur Ausführung von R-Aufträgen hinzugefügt.
    + Die Überprüfung von `Dataset.mount` wurde hinzugefügt, um einen Fehler auszulösen, wenn die Quelle des Datasets nicht zugänglich ist oder keine Daten enthält.
    + `--grant-workspace-msi-access` wurde als zusätzlicher Parameter für die Datenspeicher-CLI zum Registrieren eines Azure-Blobcontainers hinzugefügt, sodass Sie nun einen Blobcontainer hinter einem VNET registrieren können.
    + Die Profilerstellung für einzelne Instanzen wurde korrigiert, um eine Empfehlung abzugeben, und im Core SDK zur Verfügung gestellt.
    + Es wurde das Problem in _deploy in „aks.py“ behoben.
    + Überprüft die Integrität von Modellen beim Upload, um Speicherausfälle ohne Fehlermeldung zu vermeiden.
    + Benutzer können jetzt beim Neugenerieren von Schlüsseln für Webdienste einen Wert für den Authentifizierungsschlüssel angeben.
    + Der Fehler, dass Großbuchstaben nicht als Eingabename für Datasets verwendet werden konnten, wurde behoben.
  + **azureml-defaults**
    + `azureml-dataprep` wird nun als Teil von `azureml-defaults` installiert. Es ist nicht mehr erforderlich, „data prep[fuse]“ manuell auf Computezielen zu installieren, um Datasets einzubinden.
  + **azureml-interpret**
    + „azureml-interpret“ wurde auf „interpret-community 0.6.*“ aktualisiert.
    + Nach dem Update hängt „azureml-interpret“ nun von „interpret-community 0.5.0“ ab.
    + „azureml-interpret“ wurden Ausnahmen im azureml-Stil hinzugefügt.
    + Die DeepScoringExplainer-Serialisierung für Keras-Modelle wurde korrigiert.
  + **azureml-mlflow**
    + Fügt Unterstützung für Sovereign Clouds zu azureml.mlflow hinzu.
  + **azureml-pipeline-core**
    + Das Notebook für die Pipeline-Batchbewertung verwendet nun ParallelRunStep.
    + Behebung eines Fehlers, bei dem PythonScriptStep-Ergebnisse trotz Ändern der Argumentliste inkorrekt wiederverwendet werden konnten
    + Es wurde die Möglichkeit hinzugefügt, den Spaltentyp beim Aufruf der „parse_*“-Methoden auf `PipelineOutputFileDataset` festzulegen.
  + **azureml-pipeline-steps**
    + Das Paket `AutoMLStep` wurde in das Paket `azureml-pipeline-steps` verschoben. `AutoMLStep` in `azureml-train-automl-runtime` wurde als veraltet markiert.
    + Hinzufügen eines Dokumentationsbeispiels für ein Dataset als PythonScriptStep-Eingabe
  + **azureml-tensorboard**
    + azureml-tensorboard wurde um die Unterstützung von tensorflow 2.0 aktualisiert.
    + Es wird die richtige Portnummer angezeigt, wenn ein benutzerdefinierter Tensorboard-Port in einer Compute-Instanz verwendet wird.
  + **azureml-train-automl-client**
    + Es wurde ein Problem behoben, bei dem bestimmte Pakete bei Remoteausführungen mit falschen Versionen installiert werden können.
    + Ein Problem von FeaturizationConfig beim Überschreiben, das die benutzerdefinierte Featureerstellungskonfiguration filtert, wurde behoben.
  + **azureml-train-automl-runtime**
    + Das Problem mit der Häufigkeitserfassung in den Remoteausführungen wurde behoben.
    + `AutoMLStep` wurde in das Paket `azureml-pipeline-steps` verschoben. `AutoMLStep` in `azureml-train-automl-runtime` wurde als veraltet markiert.
  + **azureml-train-core**
    + Unterstützung von PyTorch-Version 1.4 bei der PyTorch-Schätzung
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning-SDK für Python v1.1.2rc0 (Vorabversion)

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + Hat den Batchmodusrückschluss (mehrere Zeilen gleichzeitig) für AutoML-ONNX-Modelle aktiviert.
    + Hat die Erkennung der Häufigkeit für die Datasets verbessert, denen Daten fehlen oder die unregelmäßige Datenpunkte enthalten.
    + Hat die Möglichkeit zum Entfernen von Datenpunkten hinzugefügt, die nicht mit der bestimmenden Häufigkeit übereinstimmen.
  + **azureml-automl-runtime**
    + Hat das Problem mit dem Fehler behoben, der ausgelöst wurde, wenn die im Trainingssatz nicht vorhandene Komponente im Testsatz aufgetreten ist.
    + Hat die y_query-Anforderung während der Bewertung für den Vorhersagedienst entfernt.
  + **azureml-contrib-mir**
    + Fügt Funktionalität in der MirWebservice-Klasse hinzu, um das Zugriffstoken abzurufen.
  + **azureml-core**
    + Die bereitgestellten AzureML-Webdienste werden jetzt standardmäßig auf `INFO`-Protokollierung festgelegt. Dies kann durch das Festlegen der Umgebungsvariablen `AZUREML_LOG_LEVEL` im bereitgestellten Dienst gesteuert werden.
    + Korrigieren Sie die Iteration bei `Dataset.get_all`, um alle mit dem Arbeitsbereich registrierten Datasets zurückzugeben.
    + Verbessern Sie die Fehlermeldung, wenn ein ungültiger Typ an das `path`-Argument der APIs zur Datasetgenerierung übergeben wird.
    + Das Python SDK verwendet den Ermittlungsdienst, um den „api“-Endpunkt anstelle von „pipelines“ zu verwenden.
    + Wechseln Sie bei allen SDK-Aufrufen zu den neuen Routen.
    + Ändert das Routing von ModelManagementService-Aufrufen in eine neue, einheitliche Struktur.
      + Die Methode zur Aktualisierung des Arbeitsbereichs wurde öffentlich zugänglich gemacht.
      + Der image_build_compute-Parameter wurde der Methode für die Arbeitsbereichsaktualisierung hinzugefügt, um dem Benutzer die Computeaktualisierung für die Imageerstellung zu ermöglichen.
    +  Dem alten Workflow für die Profilerstellung wurden Hinweise zur Veraltung hinzugefügt. CPU- und Arbeitsspeichergrenzwerte wurden für die Profilerstellung korrigiert.
  + **azureml-interpret**
    + „azureml-interpret“ wurde auf „interpret-community 0.6.*“ aktualisiert.
  + **azureml-mlflow**
    + Fügt Unterstützung für Sovereign Clouds zu azureml.mlflow hinzu.
  + **azureml-pipeline-steps**
    + `AutoMLStep` wurde zu `azureml-pipeline-steps package` verschoben. `AutoMLStep` in `azureml-train-automl-runtime` wurde als veraltet markiert.
  + **azureml-train-automl-client**
    + Es wurde ein Problem behoben, bei dem bestimmte Pakete bei Remoteausführungen mit falschen Versionen installiert werden können.
  + **azureml-train-automl-runtime**
    + Das Problem mit der Häufigkeitserfassung in den Remoteausführungen wurde behoben.
    + `AutoMLStep` wurde zu `azureml-pipeline-steps package` verschoben. `AutoMLStep` in `azureml-train-automl-runtime` wurde als veraltet markiert.
  + **azureml-train-core**
    + `AutoMLStep` wurde zu `azureml-pipeline-steps package` verschoben. `AutoMLStep` in `azureml-train-automl-runtime` wurde als veraltet markiert.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning-SDK für Python v1.1.1rc0 (Vorabversion)

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Die Profilerstellung für einzelne Instanzen wurde korrigiert, um eine Empfehlung abzugeben, und im Core SDK zur Verfügung gestellt.
  + **azureml-automl-core**
    + Die Fehlerprotokollierung wurde verbessert.
  + **azureml-automl-runtime**
    + Es wurde das Problem bei der Vorhersage behoben, wenn das Dataset eine kurze Körnung mit langen Zeitlücken enthält.
    + Es wurde ein Problem behoben, wenn der automatische maximale Horizont aktiviert ist und die Datumsspalte Datumsangaben in Form von Zeichenfolgen enthält. Es wurden eine ordnungsgemäße Konvertierung und ein empfindlicher Fehler für den Fall, dass keine Konvertierung möglich ist, hinzugefügt.
    + Verwendung von NumPy und SciPy nativ zum Serialisieren und Deserialisieren von Zwischendaten für FileCacheStore (für lokale AutoML-Ausführungen)
    + Es wurde ein Fehler behoben, bei dem fehlerhafte untergeordnete Ausführungen im Ausführungsstatus hängen bleiben konnten.
  + **azureml-cli-common**
    + Die Profilerstellung für einzelne Instanzen wurde korrigiert, um eine Empfehlung abzugeben, und im Core SDK zur Verfügung gestellt.
  + **azureml-core**
    + `--grant-workspace-msi-access` wurde als zusätzlicher Parameter für die Datenspeicher-CLI zum Registrieren eines Azure-Blobcontainers hinzugefügt, sodass Sie nun einen Blobcontainer hinter einem VNET registrieren können.
    + Die Profilerstellung für einzelne Instanzen wurde korrigiert, um eine Empfehlung abzugeben, und im Core SDK zur Verfügung gestellt.
    + Es wurde das Problem in _deploy in „aks.py“ behoben.
    + Überprüft die Integrität von Modellen beim Upload, um Speicherausfälle ohne Fehlermeldung zu vermeiden.
  + **azureml-interpret**
    + azureml-interpret wurden Ausnahmen im azureml-Stil hinzugefügt.
    + Die DeepScoringExplainer-Serialisierung für Keras-Modelle wurde korrigiert.
  + **azureml-pipeline-core**
    + Das Notebook für die Pipeline-Batchbewertung verwendet nun ParallelRunStep.
  + **azureml-pipeline-steps**
    + `AutoMLStep` wurde in das Paket `azureml-pipeline-steps` verschoben. `AutoMLStep` in `azureml-train-automl-runtime` wurde als veraltet markiert.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep wurde der optionale Parameter side_inputs hinzugefügt. Dieser Parameter kann zum Einbinden von Ordnern im Container verwendet werden. Die derzeit unterstützten Typen sind DataReference und PipelineData.
  + **azureml-tensorboard**
    + azureml-tensorboard wurde um die Unterstützung von tensorflow 2.0 aktualisiert.
  + **azureml-train-automl-client**
    + Ein Problem von FeaturizationConfig beim Überschreiben, das die benutzerdefinierte Featureerstellungskonfiguration filtert, wurde behoben.
  + **azureml-train-automl-runtime**
    + `AutoMLStep` wurde in das Paket `azureml-pipeline-steps` verschoben. `AutoMLStep` in `azureml-train-automl-runtime` wurde als veraltet markiert.
  + **azureml-train-core**
    + Unterstützung von PyTorch-Version 1.4 bei der PyTorch-Schätzung
  
## <a name="2020-02-04"></a>4\.2.2020

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning-SDK für Python v1.1.0rc0 (Vorabversion)

+ **Wichtige Änderungen**
  + **Semantic Versioning 2.0.0**
    + Ab Version 1.1 führt das Azure ML-Python-SDK Semantic Versioning 2.0.0 ein. Weitere Informationen finden Sie [hier](https://semver.org/). Alle nachfolgenden Versionen folgen dem neuen Nummerierungsschema und dem Vertrag von Semantic Versioning. 
  
+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-runtime**
    + Schnellere Featurebereitstellung
    + Behebung von Fehlern bei der Häufigkeitsprüfung während der Bewertung. Bei Vorhersageaufgaben ist nun keine genaue Übereinstimmung der Häufigkeit zwischen Trainings- und Testdataset mehr erforderlich.
  + **azureml-core**
    + Benutzer können jetzt beim Neugenerieren von Schlüsseln für Webdienste einen Wert für den Authentifizierungsschlüssel angeben.
  + **azureml-interpret**
    + Nach dem Update hängt „azureml-interpret“ nun von „interpret-community 0.5.0“ ab.
  + **azureml-pipeline-core**
    + Behebung eines Fehlers, bei dem PythonScriptStep-Ergebnisse trotz Ändern der Argumentliste inkorrekt wiederverwendet werden konnten
  + **azureml-pipeline-steps**
    + Hinzufügen eines Dokumentationsbeispiels für ein Dataset als PythonScriptStep-Eingabe
  + **azureml-contrib-pipeline-steps**
    + Parameter, die in ParallelRunConfig übergeben werden, können nun durch Übergabe von Pipelineparametern überschrieben werden. Neue unterstützte Pipelineparameter: aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count und aml_process_count_per_node waren bereits Teil eines früheren Releases).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK für Python v1.0.85

+ **Neue Features**
  + **azureml-core**
    + Abrufen der aktuellen Kernnutzung und Kontingentbeschränkung für AmlCompute-Ressourcen in einem bestimmten Arbeitsbereich und Abonnement
  
  + **azureml-contrib-pipeline-steps**
    + Ermöglichen Sie dem Benutzer, ein Tabellendataset als Zwischenergebnis aus dem vorherigen Schritt an den parallel ausgeführten Schritt zu übergeben.

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-runtime**
    + Die Anforderung der Spalte „y_query“ in der Anforderung für den bereitgestellten Vorhersagedienst wurde entfernt. 
    + Das „y_query“ wurde aus dem Dienstanforderungsabschnitt für das Notebook „Orange Juice“ von Dominick entfernt.
    + Der Fehler, der die Vorhersage für die bereitgestellten Modelle verhinderte, die mit Datasets mit Datums- und Zeitspalten arbeiten, wurde behoben.
    + Der Matthews-Korrelationskoeffizient wurde als Klassifizierungsmetrik hinzugefügt, sowohl für die binäre als auch für die Multiklassenklassifizierung.
  + **azureml-contrib-interpret**
    + Text Explainer wurden von azureml-contrib-interpret als Texterläuterung entfernt und in das interpret-text-Repository verschoben, das demnächst veröffentlicht wird.
  + **azureml-core**
    + Dataset: Die Verwendung von Dateidatasets hängt nicht mehr davon ab, ob NumPy und Pandas in der Python-Umgebung installiert sind.
    + „LocalWebservice.wait_for_deployment()“ wurde geändert, um den Status des lokalen Docker-Containers zu prüfen, bevor versucht wird, seinen Integritätsendpunkt zu pingen, wodurch die Zeit, die zum Melden einer fehlerhaften Bereitstellung benötigt wird, erheblich reduziert wird.
    + Die Initialisierung einer internen Eigenschaft, die in „LocalWebservice.reload()“ verwendet wird, wenn das Dienstobjekt aus einer vorhandenen Bereitstellung mit dem „LocalWebservice()“-Konstruktor erstellt wird, wurde behoben.
    + Die Fehlermeldung wurde zur Verdeutlichung bearbeitet.
    + Eine neue Methode namens „get_access_token()“ wurde zu AksWebservice hinzugefügt, die das AksServiceAccessToken-Objekt zurückgibt, das das Zugriffstoken, die Aktualisierung nach Zeitstempel, den Ablauf bei Zeitstempel und den Tokentyp enthält. 
    + Bestehende „get_token()“-Methode in AksWebservice als veraltet markiert, da die neue Methode alle Informationen zurückgibt, die diese Methode zurückgibt.
    + Die Ausgabe des Befehls „az ml service get-access-token“ wurde geändert. Das Token wurde in „accessToken“ und „refreshBy“ in „refreshAfter“ umbenannt. Die Eigenschaften „expiryOn“ und „tokenType“ wurden hinzugefügt.
    + „get_active_runs“ wurde behoben.
  + **azureml-explain-model**
    + Shap wurde auf Version 0.33.0 und interpret-community auf 0.4 aktualisiert.*
  + **azureml-interpret**
    + Shap wurde auf Version 0.33.0 und interpret-community auf 0.4 aktualisiert.*
  + **azureml-train-automl-runtime**
    + Der Matthews-Korrelationskoeffizient wurde als Klassifizierungsmetrik hinzugefügt, sowohl für die binäre als auch für die Multiklassenklassifizierung.
    + Das Vorverarbeitungsflag wurde im Code als veraltet markiert und durch die Featurebereitstellung ersetzt. Die Featurebereitstellung ist standardmäßig aktiviert.

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK für Python, v1.0.83

+ **Neue Features**
  + Dataset: Zwei Optionen, `on_error` und `out_of_range_datetime`, für `to_pandas_dataframe` hinzugefügt, die fehlschlagen, wenn Daten Fehlerwerte aufweisen, statt mit `None` aufgefüllt zu werden.
  + Arbeitsbereich: Das Flag `hbi_workspace` für Arbeitsbereiche mit vertraulichen Daten wurde hinzugefügt, das eine weitere Verschlüsselung ermöglicht und eine erweiterte Diagnose für Arbeitsbereiche deaktiviert. Wir haben zudem die BYOK-Unterstützung (Bring Your Own Keys) für die zugehörige Cosmos DB-Instanz hinzugefügt, indem die Parameter `cmk_keyvault` und `resource_cmk_uri` beim Erstellen eines Arbeitsbereichs angegeben werden, wodurch eine Cosmos DB-Instanz in Ihrem Abonnement erstellt wird, während Sie Ihren Arbeitsbereich bereitstellen. [Weitere Informationen finden Sie hier.](./concept-data-encryption.md#azure-cosmos-db)

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-runtime**
    + Es wurde eine Regression korrigiert, die beim Ausführen von AutoML unter Python-Versionen unter 3.5.4 zum Auslösen eines TypeError-Fehlers geführt hat.
  + **azureml-core**
    + Fehler in `datastore.upload_files` behoben, bei dem kein relativer Pfad verwendet werden konnte, der nicht mit `./` begann.
    + Veraltungsnachrichten für alle Bildklassen-Codepfade hinzugefügt
    + Behobene Erstellung der Modellverwaltungs-URL für die Azure China 21Vianet-Region.
    + Problem behoben, bei dem Modelle, die source_dir verwenden, nicht für Azure Functions gepackt werden konnten.    
    + Option zu [Environment.build_local()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) hinzugefügt, die das Pushen eines Images in die AzureML-Arbeitsbereich-Containerregistrierung bewirkt
    + Das SDK wurde aktualisiert und verwendet die neue Tokenbibliothek in Azure Synapse in abwärtskompatibler Weise.
  + **azureml-interpret**
    + Es wurde ein Fehler behoben, bei dem „None“ zurückgegeben wurde, wenn keine Erläuterungen zum Download verfügbar waren. Löst nun eine Ausnahme aus, in Übereinstimmung mit dem Verhalten an anderen Stellen.
  + **azureml-pipeline-steps**
    + Das Übergeben von `DatasetConsumptionConfig`s an den `inputs`-Parameter von `Estimator` wurde untersagt, wenn der `Estimator` in einem `EstimatorStep` verwendet wird.
  + **azureml-sdk**
    + Dem azureml-sdk-Paket wurde ein AutoML-Client hinzugefügt, wodurch Remoteausführungen von AutoML übermittelt werden können, ohne dass das vollständige AutoML-Paket installiert wird.
  + **azureml-train-automl-client**
    + Korrigierte Ausrichtung bei der Konsolenausgabe für AutoML-Ausführungen
    + Es wurde ein Fehler behoben, durch den eine falsche Version von Pandas auf einem Remote-amlcompute installiert werden kann.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK für Python v1.0.81

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-contrib-interpret**
    + Aufschieben der Shap-Abhängigkeit aus „azureml-interpret“ auf „interpret-community“
  + **azureml-core**
    + Das Computeziel kann jetzt als Parameter der entsprechenden Bereitstellungskonfigurationsobjekte angegeben werden. Dabei handelt es sich spezifisch um den Namen des Computeziels, auf dem die Bereitstellung erfolgen soll, nicht um das SDK-Objekt.
    + Modell- und Dienstobjekten wurden CreatedBy-Informationen hinzugefügt. Der Zugriff kann über „.created_by“ erfolgen
    + Behobenes Problem bei ContainerImage.run(), das zu einer nicht ordnungsgemäßen Einrichtung des HTTP-Ports des Docker-Containers führte.
    + `azureml-dataprep` wurde für den CLI-Befehl `az ml dataset register` optional
    + Es wurde ein Fehler behoben, bei dem `TabularDataset.to_pandas_dataframe` fälschlicherweise auf einen alternativen Reader zurückgriff und eine Warnung ausgab.
  + **azureml-explain-model**
    + Aufschieben der Shap-Abhängigkeit aus „azureml-interpret“ auf „interpret-community“
  + **azureml-pipeline-core**
    + Der neue Pipelineschritt `NotebookRunnerStep` zur Ausführung eines lokalen Notebooks als Schritt in der Pipeline wurde hinzugefügt.
    + Die veralteten get_all-Funktionen für PublishedPipelines, Schedules und PipelineEndpoints wurden entfernt.
  + **azureml-train-automl-client**
    + Die Ausweisung von data_script als Eingabe für AutoML als veraltet wurde begonnen.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK für Python v1.0.79

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + Die zu protokollierende featurizationConfig wurde entfernt.
      + Protokollierung wurde auf nur noch „auto“/„off“/„customized“ (automatisch/aus/angepasst) aktualisiert.
  + **azureml-automl-runtime**
    + Unterstützung für pandas wurde hinzugefügt. Datenreihen und Pandas. Categorical zum Erkennen des Datentyps von Spalten. Bislang wurde nur numpy.ndarray unterstützt.
      + Verwandte Codeänderungen zum ordnungsgemäßen Verarbeiten des kategorischen dtype wurden hinzugefügt.
    + Die Vorhersagefunktions-Schnittstelle wurde verbessert: der y_pred-Parameter ist jetzt optional. \- Die docstrings wurden verbessert.
  + **azureml-contrib-dataset**
    + Ein Fehler wurde behoben, bei dem Datasets mit Bezeichnungen nicht eingebunden werden konnten.
  + **azureml-core**
    + Fehlerkorrektur für `Environment.from_existing_conda_environment(name, conda_environment_name)`. Der Benutzer kann eine Instanz der Umgebung erstellen, die ein exaktes Replikat der lokalen Umgebung ist.
    + Zeitreihenbezogene Datasets-Methoden wurden standardmäßig auf `include_boundary=True` geändert.
  + **azureml-train-automl-client**
    + Ein Problem wurde behoben, bei dem Validierungsergebnisse nicht gedruckt werden, wenn das Anzeigen von Ausgaben auf „false“ festgelegt ist.


## <a name="2019-11-25"></a>25.11.2019

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK für Python, Version 1.0.76

+ **Wichtige Änderungen**
  + Probleme beim Upgrade auf azureml-train-automl
    + Ein Upgrade von azureml-train-automl<1.0.76 auf azureml-train-automl>=1.0.76 kann teilweise Installationen bewirken, wodurch bei einigen AutoML-Importen Fehler auftreten. Führen Sie das Setupskript unter https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd, um dieses Problem zu beheben. Wenn Sie pip direkt verwenden, können Sie Folgendes ausführen:
      + „pip install --upgrade azureml-train-automl“
      + „pip install --ignore-installed azureml-train-automl-client“
    + oder Sie können die alte Version vor dem Upgrade deinstallieren:
      + „pip uninstall azureml-train-automl“
      + „pip install azureml-train-automl“

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-runtime**
    + AutoML berücksichtigt nun sowohl wahre als auch falsche Klassen, wenn gemittelte skalare Metriken für Binärklassifizierungsaufgaben berechnet werden.
    + Machine Learning- und Trainingscode in AzureML-AutoML-Core wurde in das neue Paket AzureML-AutoML-Runtime verschoben.
  + **azureml-contrib-dataset**
    + Beim Aufrufen von `to_pandas_dataframe` für ein bezeichnetes Dataset mit der Download-Option können Sie nun angeben, ob vorhandene Dateien überschrieben oder nicht überschrieben werden sollen.
    + Bei einem Aufruf von `keep_columns` oder `drop_columns`, der das Verwerfen einer Zeitreihen-, Bezeichnungs- oder Bildspalte bewirkt, werden die entsprechenden Funktionen für das Dataset ebenfalls verworfen.
    + Ein Problem mit dem PyTorch-Ladeprogramm für die Objekterkennungsaufgabe wurde behoben.
  + **azureml-contrib-interpret**
    + Das Erklärungsdashboard-Widget wurde aus azureml-contrib-interpret entfernt, das Paket verweist nun auf das neue in interpret_community.
    + Version von interpret-community wurde auf 0.2.0 aktualisiert.
  + **azureml-core**
    + Die Leistung von `workspace.datasets` wurde verbessert.
    + Die Möglichkeit zum Registrieren des Datenspeichers von Azure SQL-Datenbank mit Benutzername und Kennwortauthentifizierung wurde eingeführt.
    + Fix für das Laden von RunConfigurations aus relativen Pfaden.
    + Bei einem Aufruf von `keep_columns` oder `drop_columns`, der das Verwerfen einer Zeitreihenspalte bewirkt, werden die entsprechenden Funktionen für das Dataset ebenfalls verworfen.
  + **azureml-interpret**
    + Version von interpret-community wurde auf 0.2.0 aktualisiert.
  + **azureml-pipeline-steps**
    + Unterstützte Werte für `runconfig_pipeline_params` für Azure Machine Learning-Pipelineschritte wurden dokumentiert.
  + **azureml-pipeline-core**
    + Eine CLI-Option zum Herunterladen der Ausgabe im JSON-Format für Pipeline-Befehle wurde hinzugefügt.
  + **azureml-train-automl**
    + AzureML-Train-AutoML wurde in zwei Pakete aufgeteilt, ein Clientpaket AzureML-Train-AutoML-Client und ein ML-Training-Paket AzureML-Train-AutoML-Runtime.
  + **azureml-train-automl-client**
    + Es wurde ein Thin Client hinzugefügt, mit dem AutoML-Experimente übermittelt werden können, ohne dass lokal Machine Learning-Abhängigkeiten installiert werden müssen.
    + Die Protokollierung von automatisch erkannten Verzögerungen, rollierenden Zeitfenstern und maximalen Horizonten in den Remoteausführungen wurde korrigiert.
  + **azureml-train-automl-runtime**
    + Es wurde ein neues AutoML-Paket hinzugefügt, um Machine Learning- und Runtime-Komponenten vom Client zu isolieren.
  + **azureml-contrib-train-rl**
    + Unterstützung für vertiefendes Lernen in SDK wurde hinzugefügt.
    + AmlWindowsCompute-Unterstützung in RL SDK wurde hinzugefügt.


## <a name="2019-11-11"></a>11.11.2019

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK für Python, v1.0.74

  + **Vorschaufunktionen**
    + **azureml-contrib-dataset**
      + Nach dem Importieren von azureml-contrib-dataset können Sie `Dataset.Labeled.from_json_lines` anstelle von `._Labeled` aufrufen, um ein bezeichnetes Dataset zu erstellen.
      + Beim Aufrufen von `to_pandas_dataframe` für ein bezeichnetes Dataset mit der Download-Option können Sie nun angeben, ob vorhandene Dateien überschrieben oder nicht überschrieben werden sollen.
      + Bei einem Aufruf von `keep_columns` oder `drop_columns`, der das Verwerfen einer Zeitreihen-, Bezeichnungs- oder Bildspalte bewirkt, werden die entsprechenden Funktionen für das Dataset ebenfalls verworfen.
      + Es wurden Probleme mit dem PyTorch-Ladeprogramm beim Aufrufen von `dataset.to_torchvision()` behoben.

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + In der Vorschau-CLI wurde die Profilerstellung für Modelle eingeführt.
    + Breaking Change in Azure Storage, die einen Ausfall der AzureML-CLI bewirkte, wurde korrigiert.
    + Der MLC wurde ein Typ des Lastenausgleichs für AKS-Typen hinzugefügt.
  + **azureml-automl-core**
    + Das Problem mit der Erkennung des maximalem Horizonts in Zeitreihen mit fehlenden Werten und mehreren Körnungen wurde behoben.
    + Das Problem mit Ausfällen, welche die Generierung von Kreuzvalidierungsteilungen bewirkten, wurde behoben.
    + Diesen Abschnitt durch eine Meldung im Markdown-Format für Versionshinweise ersetzen: – Verarbeitung von kurzen Körnungen in den Vorhersagedatasets wurde verbessert.
    + Es wurde ein Problem behoben, bei dem einige Benutzerinformationen während der Protokollierung maskiert wurden. – Verbesserte Protokollierung der Fehler während der Ausführung von Vorhersagen.
    + psutil wurde der automatisch generierten YML-Bereitstellungsdatei als Conda-Abhängigkeit hinzugefügt.
  + **azureml-contrib-mir**
    + Breaking Change in Azure Storage, die einen Ausfall der AzureML-CLI bewirkte, wurde korrigiert.
  + **azureml-core**
    + Behebung eines Fehlers, der bewirkte, dass in Azure Functions bereitgestellte Modelle 500s produzieren.
    + Es wurde ein Problem behoben, bei dem die amlignore-Datei nicht auf Momentaufnahmen angewendet wurde.
    + Es wurde die neue API amlcompute.get_active_runs hinzugefügt, die einen Generator für die Ausführung und zwischengespeicherte Ausführungen für eine bestimmte amlcompute zurückgibt.
    + Der MLC wurde ein Typ des Lastenausgleichs für AKS-Typen hinzugefügt.
    + Der append_prefix-Bool-Parameter wurde download_files in run.py und download_artifacts_from_prefix in artifacts_client hinzugefügt. Dieses Flag wird verwendet, um den ursprünglichen Dateipfad so zu vereinfachen, dass dem output_directory nur der Datei- oder Ordnername hinzugefügt wird.
    + Deserialisierungsproblem für `run_config.yml` mit Datasetverwendung wurde behoben.
    + Bei einem Aufruf von `keep_columns` oder `drop_columns`, der das Verwerfen einer Zeitreihenspalte bewirkt, werden die entsprechenden Funktionen für das Dataset ebenfalls verworfen.
  + **azureml-interpret**
    + interpret-community wurde auf Version 0.1.0.3 aktualisiert.
  + **azureml-train-automl**
    + Es wurde ein Problem behoben, bei dem automl_step Validierungsprobleme möglicherweise nicht ausgibt.
    + Es wurde ein Problem behoben, sodass register_model selbst dann erfolgreich ausgeführt wird, wenn in der Umgebung des Modells Abhängigkeiten lokal fehlen.
    + Es wurde ein Problem behoben, bei dem einige Remoteausführung nicht Docker-fähig waren.
    + Es wurde die Protokollierung der Ausnahme eingeführt, die einen vorzeitigen Abbruch einer lokalen Ausführung bewirkte.
  + **azureml-train-core**
    + resume_from-Ausführungen werden bei der Berechnung der besten untergeordneten Ausführungen der automatisierten Hyperparameteroptimierung berücksichtigt.
  + **azureml-pipeline-core**
    + Die Parameterverarbeitung bei der Erstellung von Pipeline-Argumenten wurde korrigiert.
    + Es wurden eine Pipelinebeschreibung und ein Schritttyp-YAML-Parameter hinzugefügt.
    + Neues YAML-Format für den Pipelineschritt und Warnung für veraltetes Format hinzugefügt.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Weboberfläche

Die Landing Page für den Zusammenarbeitsbereich unter [https://ml.azure.com](https://ml.azure.com) wurde erweitert und wird nun als Azure Machine Learning Studio bezeichnet.

In Studio können Sie Azure Machine Learning-Ressoucen wie Datasets, Pipelines, Modelle, Endpunkte usw. trainieren, testen, bereitstellen und verwalten.

Greifen Sie von Studio aus auf die folgenden webbasierten Erstellungstools zu:

| Webbasiertes Tool | BESCHREIBUNG | 
|-|-|-|
| Notebook-VM (Vorschau) | Vollständig verwaltete, cloudbasierte Arbeitsstation | 
| [Automatisiertes maschinelles Lernen](tutorial-first-experiment-automated-ml.md) (Vorschauversion) | Codefreie Umgebung für die Automatisierung der Machine Learning-Modellentwicklung | 
| [Designer](concept-designer.md) | Machine Learning-Modellierungstool mit Drag & Drop, zuvor als grafische Benutzeroberfläche bezeichnet | 


### <a name="azure-machine-learning-designer-enhancements"></a>Verbesserungen am Azure Machine Learning-Designer

+ Zuvor als grafische Benutzeroberfläche bezeichnet 
+    11 neue [Module](algorithm-module-reference/module-reference.md) einschließlich Empfehlungs-, Klassifizierungs- und Trainings-Hilfsprogrammen für Funktionsentwicklung, Kreuzvalidierung, Datentransformation u. v. m.

### <a name="r-sdk"></a>R SDK 
 
Das [Azure Machine Learning-SDK für R](https://github.com/Azure/azureml-sdk-for-r) wird von Data Scientists und KI-Entwicklern zum Erstellen und Ausführen von Workflows zum maschinellen Lernen mit Azure Machine Learning verwendet.

Das Azure Machine Learning SDK für R verwendet das Paket `reticulate` zur Bindung an das Python-SDK. Durch die direkte Bindung an Python ermöglicht das SDK für R den Zugriff auf die im Python-SDK implementierten Kernobjekte und -methoden aus einer beliebigen R-Umgebung Ihrer Wahl.

Zu den Hauptfunktionen des SDK gehören:

+    Verwalten von Cloudressourcen zum Überwachen, Protokollieren und Organisieren von Experimenten mit maschinellem Lernen
+    Trainieren von Modellen mithilfe von Cloudressourcen, einschließlich GPU-beschleunigten Modelltrainings.
+    Bereitstellen Ihrer Modelle als Webdienste in Azure Container Instances (ACI) und Azure Kubernetes Service (AKS).

Eine vollständige Dokumentation finden Sie auf der [Website des Pakets](https://azure.github.io/azureml-sdk-for-r).

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning-Integration in Event Grid 

Azure Machine Learning ist jetzt ein Ressourcenanbieter für Event Grid, Sie können Machine Learning-Ereignisse über das Azure-Portal oder die Azure-CLI konfigurieren. Benutzer können Ereignisse für den Abschluss der Ausführung, die Modellregistrierung, die Modellimplementierung und erkannte Datendrift erstellen. Diese Ereignisse können an Ereignishandler weitergeleitet werden, deren Nutzung von Event Grid unterstützt wird. Weitere Informationen finden Sie im Artikel zum Machine Learning-Ereignis [Schema](../event-grid/event-schema-machine-learning.md) und im [Tutorial](how-to-use-event-grid.md).

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK für Python v1.0.72

+ **Neue Features**
  + Datasetüberwachungen wurden über das Paket [**azureml-datadrift**](/python/api/azureml-datadrift) hinzugefügt, sodass Zeitreihendatasets auf Datendrift oder andere statistische Änderungen im Zeitverlauf überwacht werden können. Warnungen und Ereignisse können ausgelöst werden, wenn eine Drift erkannt wird oder andere Bedingungen für die Daten erfüllt werden. Ausführliche Informationen finden Sie in der [Dokumentation](how-to-monitor-datasets.md).
  + Für Azure Machine Learning werden zwei neue Editionen (auch als „SKU“ bezeichnet) bekannt gegeben. Mit diesem Release können Sie nun einen Basic- oder Enterprise-Arbeitsbereich für Azure Machine Learning erstellen. Alle vorhandenen Arbeitsbereiche werden standardmäßig auf die Basic-Edition bezogen, und Sie können den Arbeitsbereich jederzeit im Azure-Portal oder in Studio aktualisieren. Im Azure-Portal können Sie einen Basic- oder einen Enterprise-Arbeitsbereich erstellen. Lesen Sie die [Dokumentation](./how-to-manage-workspace.md), um mehr zu erfahren. Im SDK kann die Edition Ihres Arbeitsbereichs mithilfe der sku-Eigenschaft Ihres Arbeitsbereichsobjekts ermittelt werden.
  + Wir haben auch Verbesserungen an Azure Machine Learning Compute vorgenommen. Sie können nun neben Diagnoseprotokollen zum Debuggen auch Metriken für Ihre Cluster (z. B. Gesamtknoten, ausgeführte Knoten, Gesamtkernkontingent) in Azure Monitor anzeigen. Außerdem können Sie derzeit ausgeführte oder in der Warteschlange befindliche Ausführungen in Ihrem Cluster anzeigen, z. B. die IP-Adressen der verschiedenen Knoten im Cluster. Sie können diese im Portal oder mit den entsprechenden Funktionen im SDK oder in der Befehlszeilenschnittstelle anzeigen.

  + **Vorschaufunktionen**
    + Wir veröffentlichen eine Vorschauunterstützung für die Datenträgerverschlüsselung Ihres lokalen SSD in Azure Machine Learning Compute. Eröffnen Sie ein technisches Supportticket, um Ihr Abonnement in die Zulassungsliste aufzunehmen und dieses Feature verwenden zu können.
    + Public Preview von Azure Machine Learning-Batchrückschlüssen. Azure Machine Learning-Batchrückschlüsse sind für große Rückschlussaufträge vorgesehen, die nicht zeitempfindlich sind. Batchrückschlüsse bieten kosteneffiziente Computeskalierung für Rückschlüsse mit beispiellosem Durchsatz für asynchrone Anwendungen. Sie sind für Fire-and-Forget-Rückschlüsse mit hohem Durchsatz für große Datensammlungen optimiert.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + Aktivierte Funktionen für bezeichnete Datasets
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Die Befehlszeilenschnittstelle unterstützt nun die Modellverpackung.
    + Dataset-Befehlszeilenschnittstelle wurde hinzugefügt. Weitere Informationen: `az ml dataset --help`
    + Es wurde Unterstützung für das Bereitstellen und Verpacken unterstützter Modelle (ONNX, scikit-learn und TensorFlow) ohne InferenceConfig-Instanz hinzugefügt.
    + Das Überschreibungsflag für die Dienstbereitstellung (ACI und AKS) wurde im SDK und der Befehlszeilenschnittstelle hinzugefügt. Wenn dies angegeben ist, wird der vorhandene Dienst überschrieben, wenn bereits ein Dienst mit dem Namen vorhanden ist. Ist der Dienst nicht vorhanden, wird ein neuer Dienst erstellt.
    + Modelle können mit zwei neuen Frameworks registriert werden, ONNX und TensorFlow. - Die Modellregistrierung akzeptiert Beispieleingabedaten, Beispielausgabedaten und eine Ressourcenkonfiguration für das Modell.
  + **azureml-automl-core**
    + Das Training einer Iteration wurde nur dann in einem untergeordneten Prozess ausgeführt, wenn Laufzeiteinschränkungen festgelegt wurden.
    + Es wurde eine Guardrail für Vorhersageaufgaben hinzugefügt, um zu überprüfen, ob ein angegebener Wert max_horizon auf dem angegebenen Computer ein Arbeitsspeicherproblem verursacht. In diesem Fall wird eine Guardrailmeldung angezeigt.
    + Es wurde Unterstützung für komplexe Häufigkeiten wie zwei Jahre und einen Monat hinzugefügt. Es wurde eine verständliche Fehlermeldung hinzugefügt, wenn die Häufigkeit nicht bestimmt werden kann.
    + azureml-default wurde automatisch generierten Conda-Umgebungen hinzugefügt, um den Fehler bei der Modellbereitstellung zu beheben.
    + Zwischendaten in der Azure Machine Learning-Pipeline können nun in tabellarische Datasets konvertiert und in `AutoMLStep` verwendet werden.
    + Es wurde ein Spaltenzweckupdate für Streaming implementiert.
    + Es wurde ein Transformatorparameterupdate für Imputer und HashOneHotEncoder für das Streaming implementiert.
    + Die aktuelle Datengröße und die mindestens erforderliche Datengröße wurden den Validierungsfehlermeldungen hinzugefügt.
    + Die mindestens erforderliche Datengröße für die Kreuzvalidierung wurde aktualisiert, sodass mindestens zwei Stichproben in den einzelnen Validierungsfolds sicherstellt sind.
  + **azureml-cli-common**
    + Die Befehlszeilenschnittstelle unterstützt nun die Modellverpackung.
    + Modelle können mit zwei neuen Frameworks registriert werden, ONNX und TensorFlow.
    + Die Modellregistrierung akzeptiert Beispieleingabedaten, Beispielausgabedaten und eine Ressourcenkonfiguration für das Modell.
  + **azureml-contrib-gbdt**
    + Der Releasekanal für das Notebook wurde korrigiert.
    + Es wurde eine Warnung für andere Computeziele als AmlCompute hinzugefügt, die nicht unterstützt werden.
    + Dem Paket azureml-contrib-gbdt wurde eine LightGMB-Schätzung hinzugefügt.
  + [**azureml-core**](/python/api/azureml-core)
    + Die Befehlszeilenschnittstelle unterstützt nun die Modellverpackung.
    + Es wurde eine Veraltungswarnung für veraltete Dataset-APIs hinzugefügt. Informationen finden Sie im Änderungshinweis zur Dataset-API unter https://aka.ms/tabular-dataset.
    + [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) wurde so geändert, dass beim Registrieren des Datasets der Registrierungsname und die Version zurückgegeben werden.
    + Ein Fehler wurde behoben, bei dem ScriptRunConfig nicht wiederholt mit einem Dataset als Argument zum Übermitteln der Experimentausführung verwendet werden konnte.
    + Während einer Ausführung abgerufene Datasets werden nachverfolgt und können auf der Seite mit den Ausführungsdetails oder durch Aufrufen von [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) nach Abschluss der Ausführung angezeigt werden.
    + Zwischendaten in der Azure Machine Learning-Pipeline können nun in tabellarische Datasets konvertiert und in [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) verwendet werden.
    + Es wurde Unterstützung für das Bereitstellen und Verpacken unterstützter Modelle (ONNX, scikit-learn und TensorFlow) ohne InferenceConfig-Instanz hinzugefügt.
    + Das Überschreibungsflag für die Dienstbereitstellung (ACI und AKS) wurde im SDK und der Befehlszeilenschnittstelle hinzugefügt. Wenn dies angegeben ist, wird der vorhandene Dienst überschrieben, wenn bereits ein Dienst mit dem Namen vorhanden ist. Ist der Dienst nicht vorhanden, wird ein neuer Dienst erstellt.
    +  Modelle können mit zwei neuen Frameworks registriert werden, ONNX und TensorFlow. Die Modellregistrierung akzeptiert Beispieleingabedaten, Beispielausgabedaten und eine Ressourcenkonfiguration für das Modell.
    + Es wurde ein neuer Datenspeicher für Azure Database for MySQL hinzugefügt. Ein Beispiel für die Verwendung von Azure Database for MySQL in DataTransferStep in Azure Machine Learning-Pipelines wurde hinzugefügt.
    + Es wurde zusätzliche Funktionalität zum Hinzufügen und Entfernen von Tags aus Experimenten hinzugefügt. Es wurde Funktionalität zum Entfernen von Tags aus Ausführungen hinzugefügt.
    + Das Überschreibungsflag für die Dienstbereitstellung (ACI und AKS) wurde im SDK und der Befehlszeilenschnittstelle hinzugefügt. Wenn dies angegeben ist, wird der vorhandene Dienst überschrieben, wenn bereits ein Dienst mit dem Namen vorhanden ist. Ist der Dienst nicht vorhanden, wird ein neuer Dienst erstellt.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + Wurde von `azureml-contrib-datadrift` in `azureml-datadrift` verschoben
    + Es wurde Unterstützung für die Überwachung von Zeitreihendatasets auf Drift und andere statistische Measures hinzugefügt.
    + Der [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29)-Klasse wurden die neuen Methoden `create_from_model()` und `create_from_dataset()` hinzugefügt. Die `create()`-Methode wird eingestellt.
    + Anpassungen an den Visualisierungen in Python und der Benutzeroberfläche in Azure Machine Learning Studio.
    + Für Datasetmonitore werden jetzt auch wöchentliche und monatliche Überwachungszeitpläne zusätzlich zu den täglichen unterstützt.
    + Der Abgleich von Datenmonitormetriken zum Analysieren von Verlaufsdaten für Datasetmonitore wird jetzt unterstützt.
    + Verschiedene Fehlerbehebungen
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + azureml-dataprep ist nicht mehr erforderlich, um eine Azure Machine Learning-Pipelineausführung über die Datei `yaml` der Pipeline zu übermitteln.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + azureml-default wurde automatisch generierten Conda-Umgebungen hinzugefügt, um den Fehler bei der Modellbereitstellung zu beheben.
    + Das AutoML-Remotetraining schließt jetzt azureml-defaults ein, um die Wiederverwendung von Trainingsumgebungen für Rückschlüsse zu ermöglichen.
  + **azureml-train-core**
    + In der [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch)-Schätzung wurde Unterstützung für PyTorch 1.3 hinzugefügt.

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Grafische Benutzeroberfläche (Vorschauversion)

+ Die grafische Benutzeroberfläche von Azure Machine Learning (Vorschauversion) wurde überarbeitet, damit sie in [Azure Machine Learning-Pipelines](concept-ml-pipelines.md) ausgeführt werden kann. Pipelines (früher als Experimente bezeichnet), die auf der grafischen Benutzeroberfläche erstellt werden, sind nun vollständig in die Kernoberfläche von Azure Machine Learning integriert.
  + Vereinheitlichte Verwaltungsoberfläche mit SDK-Ressourcen
  + Versionierung und Nachverfolgung von Modellen für grafische Benutzeroberflächen, Pipelines und Endpunkte
  + Neu gestaltete Benutzeroberfläche
  + Batchrückschluss-Bereitstellung wurde hinzugefügt.
  + Unterstützung von Azure Kubernetes Service (AKS) für Rückschlüsse von Computezielen wurde hinzugefügt.
  + Neuer Workflow für die Erstellung von Python-Schrittpipelines
  + Neue [Landing Page](https://ml.azure.com) für visuelle Erstellungstools

+ **Neue Module**
  + Apply math operation
  + Apply SQL transformation
  + Clip values
  + Summarize data
  + Import from SQL Database

## <a name="2019-10-14"></a>14.10.2019

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK für Python v1.0.69

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + Beschränkung der Modellerklärungen auf die beste Ausführung, anstatt die Erklärungen für jede Ausführung zu berechnen. Änderung dieses Verhaltens für die lokale Umgebung, Remoteumgebung und ADB.
    + Unterstützung für bedarfsgesteuerte Modellerklärungen für Benutzeroberfläche hinzugefügt
    + psutil wurde als Abhängigkeit von `automl` hinzugefügt, und psutil wurde als Conda-Abhängigkeit in amlcompute eingefügt.
    + Behebung eines Problems mit heuristischen Verzögerungen und Größen von rollierenden Zeitfenstern für die Vorhersagedatasets, von denen einige Serien zu Fehlern in Bezug auf die lineare Algebra führen können
      + Ausdruck für die heuristisch ermittelten Parameter in den Vorhersageläufen hinzugefügt.
  + **azureml-contrib-datadrift**
    + Schutz für die Erstellung von Ausgabemetriken hinzugefügt, falls die Abweichung auf Datasetebene nicht im ersten Abschnitt enthalten ist.
  + **azureml-contrib-interpret**
    + Paket „azureml-contrib-explain-model“ wurde in „azureml-contrib-interpret“ umbenannt
  + **azureml-core**
    + API zum Aufheben der Registrierung von Datasets hinzugefügt. `dataset.unregister_all_versions()`
    + Paket „azureml-contrib-explain-model“ wurde in „azureml-contrib-interpret“ umbenannt.
  + **[azureml-core](/python/api/azureml-core)**
    + API zum Aufheben der Registrierung von Datasets hinzugefügt. dataset.[unregister_all_versions()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Dataset-API zum Überprüfen des Zeitpunkts von Datenänderungen hinzugefügt. `dataset.data_changed_time`.
    + Nutzung von `FileDataset` und `TabularDataset` als Eingaben für `PythonScriptStep`, `EstimatorStep` und `HyperDriveStep` in Azure Machine Learning-Pipeline
    + Leistung von `FileDataset.mount` für Ordner mit einer großen Zahl von Dateien wurde verbessert
    + Neue Möglichkeit, [FileDataset](/python/api/azureml-core/azureml.data.filedataset) und [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) als Eingaben für [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep) und [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) in der Azure Machine Learning-Pipeline zu nutzen.
    + Die Leistung von FileDataset.[mount()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) für Ordner mit einer großen Anzahl von Dateien wurde verbessert.
    + URL wurde bekannten Fehlerempfehlungen in den Ausführungsdetails hinzugefügt.
    + Fehler in „run.get_metrics“ behoben, bei dem Anforderungen nicht erfolgreich waren, wenn eine Ausführung zu viele untergeordnete Elemente aufgewiesen hat
    + Ein Fehler in [run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) wurde behoben, bei dem Anforderungen nicht erfolgreich waren, wenn eine Ausführung zu viele untergeordnete Elemente aufwies.
    + Unterstützung für die Authentifizierung im Arcadia-Cluster hinzugefügt.
    + Beim Erstellen eines Experiment-Objekts wird das Experiment im Azure Machine Learning-Arbeitsbereich für die Nachverfolgung des Ausführungsverlaufs abgerufen bzw. erstellt. Die Experiment-ID und die archivierte Zeit werden bei der Erstellung in das Experimentobjekt eingefügt. Beispiel: experiment = Experiment(workspace, "New Experiment") experiment_id = experiment.id archive() und reactivate() sind Funktionen, die für ein Experiment aufgerufen werden können, um es in der Benutzeroberfläche auszublenden und wiederherzustellen, oder die in einem Aufruf zum Auflisten von Experimenten standardmäßig zurückgegeben werden können. Wenn ein neues Experiment unter dem gleichen Namen als archiviertes Experiment erstellt wird, können Sie das archivierte Experiment bei der Reaktivierung umbenennen, indem Sie einen neuen Namen übergeben. Es kann nur jeweils ein aktives Experiment gleichen Namens vorhanden sein. Beispiel: experiment1 = Experiment(workspace, "Active Experiment") experiment1.archive() # Erstellung eines neuen aktiven Experiments mit dem gleichen Namen wie das archivierte Experiment. experiment2. = Experiment(workspace, "Active Experiment") experiment1.reactivate(new_name="Previous Active Experiment") Für die statische „list()“-Methode für „Experiment“ können ein name-Filter und ein ViewType-Filter verwendet werden. ViewType-Werte sind „ACTIVE_ONLY“, „ARCHIVED_ONLY“ und „ALL“. Beispiel: archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + Unterstützung der Nutzung einer Umgebung für die Modellimplementierung und Dienstupdate.
  + **azureml-datadrift**
    + Das „show“-Attribut der „DataDriftDector“-Klasse verfügt nicht mehr über die Unterstützung des optionalen Arguments „with_details“. Das „show“-Attribut stellt nur den Datendriftkoeffizienten und den Beitrag zur Datenabweichung der Featurespalten dar.
    + Änderungen des Verhaltens von „get_output“ für das DataDriftDetector-Attribut:
      + Eingabeparameter „start_time“ und „end_time“ sind nicht mehr obligatorisch, sondern optional.
      + Die Eingabe der spezifischen Angabe für start_time bzw. end_time mit einer spezifischen run_id im selben Aufruf führt zu einer Wertfehlerausnahme, da sich dies gegenseitig ausschließt.
      + Wenn eine spezifische Eingabe für „start_time“ bzw. „end_time“ erfolgt, werden nur Ergebnisse von geplanten Ausführungen zurückgegeben.
      + Der Parameter „daily_latest_only“ ist veraltet.
    + Unterstützung des Abrufs von Datendriftausgaben, die auf Datasets basieren.
  + **azureml-explain-model**
    + Benennt das Paket „AzureML-explain-model“ in „AzureML-interpret“ um. Das alte Paket wird vorerst aus Gründen der Abwärtskompatibilität beibehalten.
    + Der `automl`-Fehler wurde behoben, bei dem unformatierte Erklärungen beim Download von ExplanationClient standardmäßig nicht als Regression, sondern als Klassifizierungsaufgabe festgelegt wurden.
    + Unterstützung hinzugefügt, damit `ScoringExplainer` direkt per `MimicWrapper` erstellt werden kann.
  + **azureml-pipeline-core**
    + Verbesserte Leistung bei Erstellung einer großen Pipeline
  + **azureml-train-core**
    + Unterstützung für TensorFlow 2.0 in TensorFlow Estimator hinzugefügt
  + **azureml-train-automl**
    + Beim Erstellen eines [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment)-Objekts wird das Experiment im Azure Machine Learning-Arbeitsbereich für die Nachverfolgung des Ausführungsverlaufs abgerufen bzw. erstellt. Die Experiment-ID und die archivierte Zeit werden bei der Erstellung in das Experimentobjekt eingefügt. Beispiel:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) und [reactivate()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) sind Funktionen, die für ein Experiment aufgerufen werden können, um es in der Benutzeroberfläche auszublenden und wiederherzustellen, oder die in einem Aufruf zum Auflisten von Experimenten standardmäßig zurückgegeben werden können. Wenn ein neues Experiment unter dem gleichen Namen als archiviertes Experiment erstellt wird, können Sie das archivierte Experiment bei der Reaktivierung umbenennen, indem Sie einen neuen Namen übergeben. Es kann nur jeweils ein aktives Experiment gleichen Namens vorhanden sein. Beispiel:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Die statische [list()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-)-Methode in einem Experiment kann einen Namensfilter und einen ViewType-Filter verwenden. Werte für ViewType sind „ACTIVE_ONLY“, „ARCHIVED_ONLY“ und „ALL“. Beispiel:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Unterstützung der Nutzung einer Umgebung für die Modellimplementierung und Dienstupdate.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + Das „show“-Attribut der [DataDriftDector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector)-Klasse verfügt nicht mehr über die Unterstützung des optionalen Arguments „with_details“. Das „show“-Attribut stellt nur den Datendriftkoeffizienten und den Beitrag zur Datenabweichung der Featurespalten dar.
    + Verhalten der DataDriftDetector-Funktion [get_output]python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) ändert sich:
      + Eingabeparameter „start_time“ und „end_time“ sind nicht mehr obligatorisch, sondern optional.
      + Die Eingabe der spezifischen Angabe für start_time bzw. end_time mit einer spezifischen run_id im selben Aufruf führt zu einer Wertfehlerausnahme, da sich dies gegenseitig ausschließt.
      + Wenn eine spezifische Eingabe für „start_time“ bzw. „end_time“ erfolgt, werden nur Ergebnisse von geplanten Ausführungen zurückgegeben.
      + Der Parameter „daily_latest_only“ ist veraltet.
    + Unterstützung des Abrufs von Datendriftausgaben, die auf Datasets basieren.
  + **azureml-explain-model**
    + Unterstützung für die direkte Erstellung von [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py&preserve-view=true) mit MimicWrapper wurde hinzugefügt.
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Die Leistung beim Erstellen einer großen Pipeline wurde verbessert.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + Unterstützung für TensorFlow 2.0 in der [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow)-Schätzung wurde hinzugefügt.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Für die übergeordnete Ausführung tritt kein Fehler mehr auf, wenn die Setupiteration fehlschlägt, weil dies von der Orchestrierung übernommen wird.
    + Unterstützung von „local-docker“ und „local-conda“ für AutoML-Experimente hinzugefügt.
    + Unterstützung von local-docker und local-conda für AutoML-Experimente wurde hinzugefügt.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Neue Weboberfläche (Vorschau) für Azure Machine Learning-Arbeitsbereiche

Die Registerkarte „Experiment“ im [Portal für neue Arbeitsbereiche](https://ml.azure.com) wurde aktualisiert, damit Datenanalysten Experimente auf leistungsfähigere Weise überwachen können. Sie können die folgenden Features erkunden:
+ Experimentmetadaten zum einfachen Filtern und Sortieren der Liste Ihrer Experimente
+ Vereinfachte und leistungsfähige Seiten für Experimentdetails, mit denen Sie Ihre Ausführungen visualisieren und vergleichen können
+ Neues Design zum Ausführen von Detailseiten zum Verstehen und Überwachen von Trainingsausführungen

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK für Python 1.0.65

  + **Neue Features**
    + Es wurden zusammengestellte Umgebungen hinzugefügt. Diese Umgebungen wurden mit Bibliotheken für gängige Aufgaben für maschinelles Lernen vorkonfiguriert und zur schnelleren Ausführung vorab als Docker-Images erstellt und zwischengespeichert. Sie werden standardmäßig in der Liste der Umgebungen des Arbeitsbereichs mit dem Präfix „AzureML“ angezeigt.
    + Es wurden zusammengestellte Umgebungen hinzugefügt. Diese Umgebungen wurden mit Bibliotheken für gängige Aufgaben für maschinelles Lernen vorkonfiguriert und zur schnelleren Ausführung vorab als Docker-Images erstellt und zwischengespeichert. Sie werden standardmäßig in der Liste der Umgebungen des [Arbeitsbereichs](/python/api/azureml-core/azureml.core.workspace%28class%29) mit dem Präfix „AzureML“ angezeigt.

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Unterstützung für die ONNX-Konvertierung für ADB und HDI wurde hinzugefügt.

+ **Vorschaufunktionen**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + BERT und BiLSTM werden jetzt als Textfeaturanpassungen unterstützt (nur Vorschauversion).
    + Featureanpassung für Parameter für Spaltenzweck und Transformationsparameter wird jetzt unterstützt (nur Vorschauversion).
    + Unformatierte Erklärungen werden jetzt unterstützt, wenn der Benutzer während des Trainings die Modellerklärung aktiviert (nur Vorschauversion).
    + Prophet wurde für `timeseries`-Prognosen als trainierbare Pipeline hinzugefügt (nur Vorschauversion).

  + **azureml-contrib-datadrift**
    + Pakete wurden von azureml-contrib-datadrift nach azureml-datadrift verschoben. Das Paket `contrib` wird in einem zukünftigen Release entfernt.

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + FeaturizationConfig wurde in AutoMLConfig und AutoMLBaseSettings eingeführt.
    + FeaturizationConfig wurde in [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) und AutoMLBaseSettings eingeführt.
      + Spaltenzweck kann für Featureeinordnung mit angegebenen Spalten- und Featuretypen außer Kraft gesetzt werden.
      + Transformationsparameter können außer Kraft gesetzt werden.
    + Für explain_model() und retrieve_model_explanations() wurde ein Hinweis zur Veraltung hinzugefügt.
    + Prophet wurde als trainingsfähige Pipeline hinzugefügt (nur Vorschauversion).
    + Für explain_model() und retrieve_model_explanations() wurde ein Hinweis zur Veraltung hinzugefügt.
    + Prophet wurde als trainierbare Pipeline hinzugefügt (nur Vorschauversion).
    + Unterstützung für die automatische Erkennung von Zielverzögerungen, der Größe rollierender Fenster und des maximalen Horizonts wurde hinzugefügt. Wenn einer der Werte target_lags, target_rolling_window_size oder max_horizon auf automatisch festgelegt ist, wird die Heuristik zum Abschätzen des Werts des entsprechenden Parameters basierend auf den Trainingsdaten angewandt.
    + Die Vorhersage wurde für den Fall korrigiert, dass das Dataset eine Spalte vom Typ „Körnung“ enthält, diese einen numerischen Typ aufweist und eine Lücke zwischen dem Trainings- und dem Testsatz besteht.
    + Die Fehlermeldung über den duplizierten Index in der Remoteausführung in Prognoseaufgaben wurde korrigiert.
    + Vorhersagen wurden für den Fall korrigiert, dass das Dataset eine Körnungsspalte enthält, diese einen numerischen Typ aufweist und zwischen dem Trainings- und dem Testsatz eine Lücke besteht.
    + Die Fehlermeldung über den duplizierten Index in der Remoteausführung in Prognoseaufgaben wurde korrigiert.
    + Eine Guardrail wurde zur Überprüfung hinzugefügt, ob ein Dataset unausgeglichen ist. Wenn dies der Fall ist, wird eine Guardrailnachricht in die Konsole geschrieben.
  + **azureml-core**
    + Die Möglichkeit zum Abrufen der SAS-URL in das Modell im Speicher über das Modellobjekt wurde hinzugefügt. Beispiel: model.get_sas_url()
    + `run.get_details()['datasets']` wurde eingeführt, um mit der übermittelten Ausführung verknüpfte Datasets abzurufen.
    + Die API `Dataset.Tabular.from_json_lines_files` wurde hinzugefügt, um ein TabularDataset aus JSON-Lines-Dateien zu erstellen. Weitere Informationen zu diesen Tabellendaten in den JSON Lines-Dateien für TabularDataset finden Sie in der Dokumentation in [diesem Artikel](how-to-create-register-datasets.md).
    + Der supported_vmsizes()-Funktion wurden zusätzliche VM-Größenfelder (Betriebssystemdatenträger, Anzahl von GPUs) hinzugefügt.
    + Der list_nodes()-Funktion wurden zusätzliche Felder hinzugefügt, um die Ausführung, die private und die öffentliche IP-Adresse, den Port usw. anzuzeigen.
    + Die Möglichkeit, bei der Clusterbereitstellung ein neues Feld über --remotelogin_port_public_access anzugeben, wurde hinzugefügt. Dieses kann je nachdem, ob der SSH-Port zum Zeitpunkt der Clustererstellung geöffnet oder geschlossen sein soll, aktiviert oder deaktiviert werden. Wenn keine Angabe erfolgt, öffnet oder schließt der Dienst den Port intelligent in Abhängigkeit davon, ob Sie den Cluster in einem VNET bereitstellen.
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + Die Möglichkeit zum Abrufen der SAS-URL in das Modell im Speicher über das Modellobjekt wurde hinzugefügt. Beispiel: model.[get_sas_url()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + run.[get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] wurde eingeführt, um mit der übermittelten Ausführung verknüpfte Datasets abzurufen.
    + Die API `Dataset.Tabular`.[from_json_lines_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) wurde hinzugefügt, um ein TabularDataset aus JSON-Lines-Dateien zu erstellen. Weitere Informationen zu diesen Tabellendaten in den JSON-Lines-Dateien für TabularDataset finden Sie in der Dokumentation unter https://aka.ms/azureml-data.
    + Der [supported_vmsizes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)-Funktion wurden zusätzliche VM-Größenfelder (Betriebssystemdatenträger, Anzahl von GPUs) hinzugefügt.
    + Der [list_nodes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--)-Funktion wurden zusätzliche Felder hinzugefügt, um die Ausführung, die private und die öffentliche IP-Adresse, den Port usw. anzuzeigen.
    + Die Möglichkeit, bei der [Clusterbereitstellung](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) ein neues Feld anzugeben, wurde hinzugefügt. Dieses kann je nachdem, ob der SSH-Port zum Zeitpunkt der Clustererstellung geöffnet oder geschlossen sein soll, aktiviert oder deaktiviert werden. Wenn keine Angabe erfolgt, öffnet oder schließt der Dienst den Port intelligent in Abhängigkeit davon, ob Sie den Cluster in einem VNET bereitstellen.
  + **azureml-explain-model**
    + Die Dokumentation für Erklärungsausgaben im Klassifizierungsszenario wurde verbessert.
    + Die Möglichkeit, die vorhergesagten y-Werte in der Erklärung für die Evaluierungsbeispiele hochzuladen, wurde hinzugefügt. Weitere nützliche Visualisierungen wurden entsperrt.
    + MimicWrapper wurde eine Erklärungseigenschaft hinzugefügt, um den zugrunde liegenden MimicExplainer-Wert abzurufen.
  + **azureml-pipeline-core**
    + Ein Notebook zum Beschreiben von Module, ModuleVersion und ModuleStep wurde hinzugefügt.
  + **azureml-pipeline-steps**
    + RScriptStep wurde zur Unterstützung von R-Skript-Ausführungen über die AML-Pipeline hinzugefügt.
    + Die Analyse von Metadatenparametern in AzureBatchStep wurde korrigiert. Dies war die Ursache der Fehlermeldung über eine „nicht angegebene Zuweisung für den SubscriptionId-Parameter“.
  + **azureml-train-automl**
    + training_data, validation_data, label_column_name und weight_column_name werden jetzt als Format für die Dateneingabe unterstützt.
    + Für explain_model() und retrieve_model_explanations() wurde ein Hinweis zur Veraltung hinzugefügt.
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Ein [Notebook](https://aka.ms/pl-modulestep) zum Beschreiben von [Module](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), [ModuleVersion und [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) wurde hinzugefügt.
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + [RScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) zur Unterstützung von R-Skript-Ausführungen über die AML-Pipeline wurde hinzugefügt.
    + Die Analyse von Metadatenparametern in AzureBatchStep wurde korrigiert. Dies war die Ursache der Fehlermeldung über eine nicht angegebene Zuweisung für den SubscriptionId-Parameter.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + training_data, validation_data, label_column_name und weight_column_name werden jetzt als Format für die Dateneingabe unterstützt.
    + Für [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) und [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-) wurde ein Hinweis zur Veraltung hinzugefügt.


## <a name="2019-09-16"></a>16.09.2019

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK für Python v1.0.62

+ **Neue Features**
  + Das Merkmal `timeseries` für TabularDataset wurde eingeführt. Dieses Merkmal ermöglicht eine einfache Zeitstempelfilterung für Daten in einem TabularDataset, wie z. B. das Verwenden aller Daten aus einem Zeitbereich oder der neuesten Daten.  Unter https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb finden Sie ein Beispielnotebook.
  + Training mit TabularDataset und FileDataset aktiviert. 

  + **azureml-train-core**
      + In der PyTorch-Schätzung wurde die Unterstützung von `Nccl` und `Gloo` hinzugefügt.

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + AutoML-Einstellung „lag_length“ und LaggingTransformer sind veraltet.
    + Validierung der Eingabedaten korrigiert, wenn sie in einem Dataflow-Format angegeben sind.
    + „it_pipeline.py“ so geändert, dass der JSON-Code für den Graphen generiert und in Artefakte hochgeladen wird.
    + Das Diagramm unter `userrun` wurde mithilfe von `Cytoscape` gerendert.
  + **azureml-core**
    + Überprüfen Sie die Ausnahmebehandlung im ADB-Code, und nehmen Sie Änderungen gemäß der neuen Fehlerbehandlung vor.
    + Automatische MSI-Authentifizierung für Notebook-VMs hinzugefügt.
    + Fehler korrigiert, aufgrund dessen beschädigte oder leere Modelle aufgrund fehlgeschlagener Wiederholungen hochgeladen werden konnten.
    + Fehler korrigiert, bei dem sich der `DataReference`-Name ändert, wenn sich der `DataReference`-Modus ändert (z. B. beim Aufrufen von `as_upload`, `as_download` oder `as_mount`).
    + `mount_point` und `target_path` für `FileDataset.mount` und `FileDataset.download` als optional festgelegt.
    + Ausnahme bei Nichtauffindbarkeit der Zeitstempelspalte wird ausgelöst, wenn die auf Zeitreihen bezogene API ohne Zuweisung einer differenzierten Zeitstempelspalte aufgerufen wird oder die zugewiesenen Zeitstempelspalten gelöscht werden.
    + Zeitserienspalten müssen einer Spalte zugewiesen werden, deren Typ „Datum“ ist, sonst wird eine Ausnahme erwartet.
    + Zeitreihenspalten, die die API „with_timestamp_columns“ zuweisen, können den Wert „None“ bei einem differenzierten/undifferenzierten Namen einer Zeitstempelspalte annehmen, wodurch zuvor zugewiesene Zeitstempelspalten gelöscht werden.
    + Eine Ausnahme wird ausgelöst, wenn entweder eine differenzierte oder undifferenzierte Zeitstempelspalte mit dem Hinweis für den Benutzer gelöscht wird, dass das Löschen entweder nach dem Ausschließen der Zeitstempelspalte aus der Löschliste oder dem Aufruf von with_time_stamp mit dem Wert „None“ zur Freigabe von Zeitstempelspalten erfolgen kann.
    + Eine Ausnahme wird ausgelöst, wenn entweder eine differenzierte oder undifferenzierte Zeitstempelspalte mit dem Hinweis nicht der Liste zur Beibehaltung von Spalten hinzugefügt wird, dass das Beibehalten entweder nach dem Ausschließen der Zeitstempelspalte aus der Liste zur Beibehaltung von Spalten oder dem Aufruf von with_time_stamp mit dem Wert „None“ zur Freigabe von Zeitstempelspalten erfolgen kann.
    + Protokollierung für die Größe eines registrierten Modells hinzugefügt.
  + **azureml-explain-model**
    + In der Konsole ausgegebene Warnung korrigiert, wenn das Python-Paket „packaging“ nicht installiert ist: „Sie verwenden eine ältere als die unterstützte Version von lightgbm. Aktualisieren Sie auf eine Version höher als 2.2.1.“
    + Erklärung zum Downloadmodell mit Sharding für globale Erklärungen mit vielen Features korrigiert.
    + Fehlende Initialisierungsbeispiele zur Erläuterung der Ausgabe von Mimic Explainer korrigiert.
    + Unveränderlicher Fehler bei festgelegten Eigenschaften korrigiert, wenn beim Hochladen mit dem Erklärungsclient zwei verschiedene Arten von Modellen verwendet wurden.
    + get_raw-Parameter wurde zu Bewertungs-Explainer .explain() hinzugefügt, sodass ein Bewertungs-Explainer sowohl bearbeitete als auch Rohwerte zurückgeben kann.
  + **azureml-train-automl**
    + Öffentliche APIs aus AutoML wurden zur Unterstützung von Erklärungen aus dem `automl` Explain SDK eingeführt. - Neuere Art der Unterstützung von AutoML-Erklärungen durch Entkopplung der AutoML-Featurisierung und Explain SDK. - Integrierte Roherklärungsunterstützung aus dem Azure ML Explain SDK für AutoML-Modelle.
    + azureml-defaults aus Remotetrainingsumgebungen entfernt.
    + Der Standardspeicherort des Caches von FileCacheStore wurde basierend auf AzureFileCacheStore geändert, um AutoML im Azure Databricks-Codepfad zu ermöglichen.
    + Validierung der Eingabedaten korrigiert, wenn sie in einem Dataflow-Format angegeben sind.
  + **azureml-train-core**
    + Veraltung von source_directory_data_store aufgehoben.
    + Möglichkeit zum Überschreiben installierter azureml-Paketversionen hinzugefügt.
    + Dockerfile-Unterstützung im `environment_definition`-parameter in Estimators hinzugefügt.
    + Verteilte Trainingsparameter in Estimators vereinfacht.

         ```python
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>09.09.2019

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Neue Weboberfläche (Vorschau) für Azure Machine Learning-Arbeitsbereiche
Die neue Weboberfläche ermöglicht Data Scientists und Datentechnikern, ihren End-to-End-Lebenszyklus für Machine Learning vom Vorbereiten und Visualisieren von Daten bis hin zu Trainings- und Bereitstellungsmodellen an einem einzigen Ort abzuschließen.

![Benutzeroberfläche des Azure Machine Learning-Arbeitsbereichs (Vorschau)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Wichtige Features:**

Mit dieser neuen Azure Machine Learning-Benutzeroberfläche können Sie nun folgende Aktionen ausführen:
+ Verwalten Ihrer Notebooks oder Verknüpfen mit Jupyter
+ [Ausführen automatisierter ML-Experimente](tutorial-first-experiment-automated-ml.md)
+ [Erstellen von Datasets aus lokalen Dateien, Datenspeichern und Webdateien](how-to-create-register-datasets.md)
+ Untersuchen und Vorbereiten von Datasets für die Modellerstellung
+ Überwachen der Datenabweichung für Ihre Modelle
+ Anzeigen aktueller Ressourcen aus einem Dashboard

Zum Zeitpunkt dieses Releases werden die folgenden Browser unterstützt: Chrome, Firefox, Safari und Microsoft Edge (Vorschau).

**Bekannte Probleme:**

1. Aktualisieren Sie Ihren Browser, wenn die Meldung „Leider ist ein Fehler aufgetreten. Fehler beim Laden von Blockdateien“ angezeigt wird, wenn die Bereitstellung ausgeführt wird.

1. Die Datei kann in Notebooks und Dateien nicht gelöscht oder umbenannt werden. Während der Public Preview können Sie die Jupyter-Benutzeroberfläche oder das Terminal in der Notebook-VM verwenden, um Aktualisierungsdateivorgänge auszuführen. Da es sich um ein eingebundenes Netzwerkdateisystem handelt, spiegeln sich alle Änderungen, die Sie an der Notebook-VM vornehmen, sofort im Notebook-Arbeitsbereich wider.

1. So stellen Sie eine SSH-Verbindung mit der Notebook-VM her:
   1. Suchen Sie die SSH-Schlüssel, die während der VM-Einrichtung erstellt wurden. Alternativ können Sie die Schlüssel im Azure Machine Learning-Arbeitsbereich suchen: Öffnen Sie die Registerkarte „Compute“, suchen Sie die Notebook-VM in der Liste, öffnen Sie deren Eigenschaften, und kopieren Sie die Schlüssel aus dem Dialogfeld.
   1. Importieren Sie diese öffentlichen und privaten SSH-Schlüssel auf Ihren lokalen Computer.
   1. Verwenden Sie sie, um eine SSH-Verbindung mit der Notebook-VM herzustellen.

## <a name="2019-09-03"></a>03.09.2019
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK für Python v1.0.60

+ **Neue Features**
  + FileDataset wurde eingeführt, das auf einzelne oder mehrere Dateien in Ihren Datenspeichern oder öffentlichen URLs verweist. Die Dateien können ein beliebiges Format aufweisen. Mit FileDataset haben Sie die Möglichkeit, die Dateien herunterzuladen oder in Ihrer Computeinstanz bereitzustellen. 
  + Hinzugefügte Pipeline-YAML-Unterstützung für PythonScript Step, Adla Step, Databricks Step, DataTransferStep und AzureBatch Step

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + AutoArima ist jetzt eine vorschlagbare Pipeline nur für die Vorschau.
    + Verbesserte Fehlerberichte für Vorhersagen.
    + Verbesserte Protokollierung durch Verwendung von benutzerdefinierten Ausnahmen anstelle von generischen Ausnahmen in den Vorhersagetasks.
    + Die Überprüfung wurde entfernt, dass max_concurrent_iterations kleiner als die Gesamtzahl von Iterationen ist.
    + AutoML-Modelle geben jetzt AutoMLExceptions zurück.
    + Dieses Release verbessert die Ausführungsleistung automatisierter lokaler Machine Learning-Ausführungen.
  + **azureml-core**
    + Dataset.get_all(workspace) wurde eingeführt und gibt ein Wörterbuch von `TabularDataset`- und `FileDataset`-Objekten zurück, die als Schlüssel ihren Registrierungsnamen verwenden.

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + `parition_format` wurde als Argument für `Dataset.Tabular.from_delimited_files` und `Dataset.Tabular.from_parquet.files` eingeführt. Die Partitionsinformationen für die einzelnen Datenpfade werden basierend auf dem angegebenen Format in Spalten extrahiert. „{column_name}“ erstellt eine Zeichenfolgenspalte, und „{column_name:yyyyyy/MM/dd/HH/mm/ss}' erstellt eine datetime-Spalte, wobei „yyyy“, „MM“, „dd“, „HH“, „mm“ und „ss“ verwendet werden, um Jahr, Monat, Tag, Stunde, Minute und Sekunde für den datetime-Typ zu extrahieren. Das partition_format sollte an der Position des ersten Partitionsschlüssels beginnen und bis zum Ende des Dateipfads reichen. Beispielpfad: „../USA/2019/01/01/data.csv“. Dabei wird die Partition durch das Land und das Datum festgelegt, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' erstellt die Zeichenfolgenspalte „Country“ mit dem Wert „USA“ und die datetime-Spalte „PartitionDate“ mit dem Wert „2019-01-01“.
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + `partition_format` wurde als Argument für `Dataset.Tabular.from_delimited_files` und `Dataset.Tabular.from_parquet.files` eingeführt. Die Partitionsinformationen für die einzelnen Datenpfade werden basierend auf dem angegebenen Format in Spalten extrahiert. „{column_name}“ erstellt eine Zeichenfolgenspalte, und „{column_name:yyyyyy/MM/dd/HH/mm/ss}' erstellt eine datetime-Spalte, wobei „yyyy“, „MM“, „dd“, „HH“, „mm“ und „ss“ verwendet werden, um Jahr, Monat, Tag, Stunde, Minute und Sekunde für den datetime-Typ zu extrahieren. Das partition_format sollte an der Position des ersten Partitionsschlüssels beginnen und bis zum Ende des Dateipfads reichen. Beispielpfad: „../USA/2019/01/01/data.csv“. Dabei wird die Partition durch das Land und das Datum festgelegt, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' erstellt die Zeichenfolgenspalte „Country“ mit dem Wert „USA“ und die datetime-Spalte „PartitionDate“ mit dem Wert „2019-01-01“.
    + Die Methoden `to_csv_files` und `to_parquet_files` wurden `TabularDataset` hinzugefügt. Diese Methoden ermöglichen die Konvertierung zwischen `TabularDataset` und `FileDataset`, indem die Daten in Dateien des angegebenen Formats konvertiert werden.
    + Automatische Anmeldung bei der Basisimageregistrierung beim Speichern der von Model.package() erstellten Dockerfile-Datei.
    + „gpu_support“ ist nicht mehr erforderlich. AML erkennt und verwendet nun automatisch die Nvidia-Docker-Erweiterung, wenn sie verfügbar ist. Dieses Element wird in einer späteren Version entfernt.
    + Unterstützung für das Erstellen, Aktualisieren und Verwenden von PipelineDrafts wurde hinzugefügt.
    + Dieses Release verbessert die Ausführungsleistung automatisierter lokaler Machine Learning-Ausführungen.
    + Benutzer können Metriken aus dem Ausführungsverlauf nach Namen abfragen.
    + Verbesserte Protokollierung durch Verwendung von benutzerdefinierten Ausnahmen anstelle von generischen Ausnahmen in den Vorhersagetasks.
  + **azureml-explain-model**
    + Dem neuen MimicWrapper wurde der feature_maps-Parameter hinzugefügt, sodass Benutzer unformatierte Merkmalserklärungen abrufen können.
    + Datasetuploads sind jetzt standardmäßig für den Erläuterungsupload deaktiviert und können mit upload_datasets=True erneut aktiviert werden.
    + Is_law-Filterparameter wurden der Erläuterungsliste und Downloadfunktionen hinzugefügt.
    + `get_raw_explanation(feature_maps)`-Methode zu globalen und lokalen Erläuterungsobjekten hinzugefügt.
    + Versionsüberprüfung zu lightgbm mit ausgegebener Warnung hinzugefügt, wenn die Version kleiner als die unterstützte Version ist.
    + Optimierte Speichernutzung bei der Batchverarbeitung von Erläuterungen
    + AutoML-Modelle geben jetzt AutoMLExceptions zurück.
  + **azureml-pipeline-core**
    + Unterstützung für das Erstellen, Aktualisieren und Verwenden von PipelineDrafts wurde hinzugefügt. Kann verwendet werden, um änderbare Pipelinedefinitionen zu verwalten und diese interaktiv für die Ausführung zu verwenden.
  + **azureml-train-automl**
    + Ein Feature zum Installieren spezifischer Versionen von GPU-fähigen PyTorch v1.1.0, :::no-loc text="cuda"::: Toolkit 9.0, pytorch-transformers wurde erstellt, das zum Aktivieren von BERT/XLNet in der Python-Remote-Laufzeitumgebung erforderlich ist.
  + **azureml-train-core**
    + Früher Ausfall einiger Hyperparameterraum-Definitionsfehler direkt im SDK anstelle auf der Serverseite.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK, Version 1.1.14
+ **Fehlerbehebungen und Verbesserungen**
  + Das Schreiben in ADLS/ADLSGen2 wurde mit dem RAW-Pfad und den Anmeldeinformationen aktiviert.
  + Es wurde ein Fehler behoben, der dazu führte, dass `include_path=True` nicht für `read_parquet` funktionierte.
  + Ein `to_pandas_dataframe()`-Fehler aufgrund der Ausnahme „Ungültiger Eigenschaftswert: hostSecret“ wurde korrigiert.
  + Es wurde ein Fehler behoben, bei dem Dateien nicht im Spark-Modus auf DBFS gelesen werden konnten.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK für Python, Version 1.0.57
+ **Neue Features**
  + `TabularDataset` wurde aktiviert und kann mit AutomatedML genutzt werden. Weitere Informationen zu `TabularDataset` finden Sie unter https://aka.ms/azureml/howto/createdatasets.

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Sie können jetzt das TLS/SSL-Zertifikat für den im AKS-Cluster bereitgestellten Bewertungsendpunkt aktualisieren. Dies gilt sowohl für von Microsoft generierte Zertifikate als auch für Kundenzertifikate.
  + **azureml-automl-core**
    + Ein Problem in AutoML wurde behoben, bei dem Zeilen mit fehlenden Bezeichnungen nicht ordnungsgemäß entfernt wurden.
    + Die Fehlerprotokollierung in AutoML wurde verbessert: In die Protokolldatei werden nun immer vollständige Fehlermeldungen geschrieben.
    + Die Paketanheftung in AutoML wurde aktualisiert und umfasst nun `azureml-defaults`, `azureml-explain-model` und `azureml-dataprep`. In AutoML werden bei Paketkonflikten keine Warnungen mehr angezeigt (außer für das Paket `azureml-train-automl`).
    + Ein Problem in `timeseries` wurde behoben, bei dem Kreuzvalidierungsaufteilungen mit unterschiedlicher Größe zu Fehlern bei der binären Berechnung führten.
    + Wenn beim Ausführen der Ensembleiteration für den Trainingstyp der Kreuzvalidierung Fehler beim Herunterladen der Modelle auftraten, die für das gesamte Dataset trainiert wurden, bestand eine Inkonsistenz zwischen den Modellgewichtungen und den Modellen, die in das Abstimmungsensemble aufgenommen wurden.
    + Der Fehler wurde behoben, der auftrat, wenn Trainings- oder Validierungsbezeichnungen („y“ bzw. „y_valid“) in Form eines Pandas-Datenrahmens, jedoch nicht als NumPy-Array angegeben werden.
    + Das Problem wurde behoben, das bei den Vorhersageaufgaben auftrat, wenn in den booleschen Spalten von Eingabetabellen der Wert „Kein“ gefunden wurde.
    + AutoML-Benutzer können nun Trainingsreihen löschen, die bei der Prognose nicht lang genug sind. – AutoML-Benutzer können nun Intervalle aus dem Testsatz löschen, die bei der Prognose nicht im Trainingssatz vorhanden sind.
  + **azureml-core**
    + Ein Problem bei der Reihenfolge des Parameters „blob_cache_timeout“ wurde behoben.
    + Den Systemfehlern wurden externe Ausnahmetypen für die Anpassung und Transformation hinzugefügt.
    + Unterstützung für Key Vault-Geheimnisse für Remoteausführungen wurde hinzugefügt. Nun kann eine „azureml.core.keyvault.Keyvault“-Klasse hinzugefügt werden, um Geheimnisse aus dem mit Ihrem Arbeitsbereich verknüpften Schlüsseltresor hinzuzufügen, abzurufen und aufzulisten. Folgende Vorgänge werden unterstützt:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(name, value)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(name)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Weitere Methoden zum Abrufen des Standardschlüsseltresors und von Geheimnissen während der Remoteausführung:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(name)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Dem CLI-Befehl „submit-hyperdrive“ wurden weitere Überschreibungsparameter hinzugefügt.
    + Die Zuverlässigkeit von API-Aufrufen wurde durch Erweitern der Wiederholungsversuche auf häufige Ausnahmen von Bibliotheksanforderungen erhöht.
    + Unterstützung für das Übermitteln von Ausführungen über eine übermittelte Ausführung wurde hinzugefügt.
    + Ein Problem beim Ablauf des SAS-Tokens in FileWatcher wurde behoben, bei dem Dateien nicht mehr hochgeladen wurden, nachdem das ursprüngliche Token abgelaufen war.
    + Unterstützung für den Import von HTTP-CSV/TSV-Dateien in Datasets mit dem Python SDK wurde hinzugefügt.
    + Die Workspace.setup()-Methode wurde als veraltet markiert. In einer Warnmeldung wird Benutzern stattdessen die Verwendung von create() oder get()/from_config() empfohlen.
    + Die Environment.add_private_pip_wheel()-Methode wurde hinzugefügt, mit der private benutzerdefinierte Python-Pakete (`whl`) in den Arbeitsbereich hochgeladen und zum Erstellen und Materialisieren der Umgebung verwendet werden können.
    + Sie können jetzt das TLS/SSL-Zertifikat für den im AKS-Cluster bereitgestellten Bewertungsendpunkt aktualisieren. Dies gilt sowohl für von Microsoft generierte Zertifikate als auch für Kundenzertifikate.
  + **azureml-explain-model**
    + Ein Parameter wurde hinzugefügt, über den den Erläuterungen zum Hochladen eine Modell-ID hinzugefügt werden kann.
    + Den Erläuterungen im Speicher und beim Hochladen wurde `is_raw`-Tagging hinzugefügt.
    + PyTorch-Unterstützung und Tests für das Paket „azureml-explain-model“ wurden hinzugefügt.
  + **azureml-opendatasets**
    + Erkennung und Protokollierung der automatischen Testumgebung werden nun unterstützt.
    + Klassen zum Abrufen der US-amerikanischen Bevölkerung nach County und Postleitzahl wurden hinzugefügt.
  + **azureml-pipeline-core**
    + Den Definitionen für den Eingabe- und den Ausgabeport wurde die label-Eigenschaft hinzugefügt.
  + **azureml-telemetry**
    + Eine fehlerhafte Telemetriekonfiguration wurde korrigiert.
  + **azureml-train-automl**
    + Das Problem wurde behoben, bei dem bei Setupfehlern der Fehler nicht im Feld „errors“ für die Setupausführung protokolliert und daher nicht im Feld „errors“ der übergeordneten Ausführung gespeichert wurde.
    + Ein Problem in AutoML wurde behoben, bei dem Zeilen mit fehlenden Bezeichnungen nicht ordnungsgemäß entfernt wurden.
    + AutoML-Benutzer können nun Trainingsreihen löschen, die bei der Prognose nicht lang genug sind.
    + AutoML-Benutzer können nun Intervalle aus dem Testsatz löschen, die bei der Prognose nicht im Trainingssatz vorhanden sind.
    + AutoMLStep durchläuft nun die `automl`-Konfiguration bis zum Back-End, um Probleme bei Änderungen oder Ergänzungen neuer Konfigurationsparameter zu vermeiden.
    + AutoML Data GuardRail befindet sich jetzt in der öffentlichen Vorschau. Dem Benutzer wird nach dem Training ein Data GuardRail-Bericht (für Klassifizierungs-/Regressionsaufgaben) angezeigt, und er kann darauf auch über die SDK-API zugreifen.
  + **azureml-train-core**
    + In PyTorch wurde die Unterstützung von Torch 1.2 hinzugefügt.
  + **azureml-widgets**
    + Konfusionsmatrixdiagramme für das Klassifizierungstraining wurden verbessert.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK, Version 1.1.12
+ **Neue Features**
  + Listen von Zeichenfolgen können nun als Eingabe an `read_*`-Methoden übergeben werden.

+ **Fehlerbehebungen und Verbesserungen**
  + Die Leistung von `read_parquet` bei der Ausführung in Spark wurde verbessert.
  + Ein Problem wurde behoben, bei dem `column_type_builder` bei einer einzelnen Spalte mit mehrdeutigen Datumsformaten nicht ausgeführt wurde.

### <a name="azure-portal"></a>Azure-Portal
+ **Previewfunktion**
  + Das Streaming von Protokoll- und Ausgabedateien ist nun für Seiten mit Ausführungsdetails verfügbar. Die Dateien streamen Updates in Echtzeit, wenn die Umschaltfläche für die Vorschau aktiviert ist.
  + Die Möglichkeit, ein Kontingent auf Arbeitsbereichsebene festzulegen, wurde als Vorschauversion veröffentlicht. AmlCompute-Kontingente werden auf Abonnementebene zugeordnet, aber wir ermöglichen Ihnen nun, dieses Kontingent für eine gerechte Freigabe und Governance zwischen Arbeitsbereichen zu verteilen und zuzuordnen. Klicken Sie in der linken Navigationsleiste Ihres Arbeitsbereichs auf das Blatt **Nutzung + Kontingente**, und wählen Sie die Registerkarte **Configure Quotas** (Kontingente konfigurieren) aus. Sie müssen Abonnementadministrator sein, um auf Arbeitsbereichsebene Kontingente festlegen zu können, da dies ein arbeitsbereichsübergreifender Vorgang ist.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK für Python v1.0.55

+ **Neue Features**
  + Die tokenbasierte Authentifizierung wird jetzt für die Aufrufe unterstützt, die an den in AKS bereitgestellten Bewertungsendpunkt ausgegeben werden. Wir setzen die Unterstützung der aktuellen, schlüsselbasierten Authentifizierung fort, und Benutzer können jeweils einen dieser Authentifizierungsmechanismen verwenden.
  + Möglichkeit zum Registrieren eines Blob-Speichers, der sich hinter dem virtuellen Netzwerk (VNet) als Datenspeicher befindet.

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-automl-core**
    + Korrigiert einen Fehler, bei dem die Validierungsgröße für CV-Teilungen klein ist und zu ungültig vorhergesagten gegenüber echten Diagrammen für Regression und Prognosen führt.
    + Die Protokollierung von Vorhersageaufgaben bei Remoteausführungen wurde verbessert, und dem Benutzer wird nun eine umfassende Fehlermeldung bereitgestellt, wenn die Ausführung fehlschlägt.
    + Fehler von `Timeseries` wurden korrigiert, wenn das Vorverarbeitungsflag TRUE war.
    + Die Umsetzbarkeit einiger Fehlermeldungen zur Vorhersagedatenüberprüfung wurde verbessert.
    + Verringerte Arbeitsspeichernutzung bei AutoML-Läufen durch Löschen und/oder verzögertes Laden von Datasets, insbesondere zwischen der Erzeugung von Prozessen.
  + **azureml-contrib-explain-model**
    + Explainern wurde das Flag „model_task“ hinzugefügt, um Benutzern das Außerkraftsetzen der automatischen Standardrückschlusslogik für den Modelltyp zu gestatten.
    + Änderungen bei Widgets: Wird automatisch mit `contrib` installiert, keine Installation/Aktivierung von `nbextension` mehr. – Unterstützung der Erklärung durch globale Wichtigkeit der Funktion (z. B. permutativ).
    + Dashboardänderungen: - Boxplots und Violinplots zusätzlich zum `beeswarm`-Plot auf der Zusammenfassungsseite. - Viel schnelleres Rendern von `beeswarm`-Plots bei „Top-k“-Schieberegleränderung. - Hilfreiche Meldung, die erläutert, wie „Top-k“ berechnet wird. - Nützliche, anpassbare Meldungen anstelle von Diagrammen, wenn keine Daten bereitgestellt werden.
  + **azureml-core**
    + Die Model.package()-Methode wurde hinzugefügt, um Docker-Images und Dockerfiles zu erstellen, die Modelle und deren Abhängigkeiten einkapseln.
    + Aktualisierung der lokalen Webdienste, sodass sie InferenceConfigs akzeptieren, die Umgebungsobjekte enthalten.
    + Behoben wurde: Model.register() erzeugt ungültige Modelle, wenn "." (für das aktuelle Verzeichnis) als model_path-Parameter übergeben wird.
    + Run.submit_child wurde hinzugefügt, die Funktionalität spiegelt Experiment.submit, während gleichzeitig die Ausführung als übergeordnetes Element der übermittelten untergeordneten Ausführung angegeben wird.
    + Unterstützung von Konfigurationsoptionen aus Model.register in Run.register_model.
    + Möglichkeit zum Ausführen von JAR-Aufträgen in einem vorhandenen Cluster.
    + Jetzt Unterstützung der Parameter instance_pool_id und cluster_log_dbfs_path.
    + Unterstützung für die Verwendung eines Umgebungsobjekts beim Bereitstellen eines Modells in einem Webdienst wurde hinzugefügt. Das Umgebungsobjekt kann jetzt als Teil des InferenceConfig-Objekts bereitgestellt werden.
    + appinsifht-Zuordnung für neue Regionen wurde hinzugefügt: -centralus -westus -northcentralus
    + Dokumentation für alle Attribute in allen Datastore-Klassen wurde hinzugefügt.
    + Der blob_cache_timeout-Parameter wurde zu `Datastore.register_azure_blob_container` hinzugefügt.
    + Die Methoden save_to_directory und load_from_directory wurden zu „azureml.core.environment.Environment“ hinzugefügt.
    + Die Befehle „az ml environment download“ und „az ml environment register“ wurden der CLI hinzugefügt.
    + Die Methode Environment.add _private_pip_wheel wurde hinzugefügt.
  + **azureml-explain-model**
    + Die Nachverfolgung von Datasets wurde mithilfe des Dataset-Anbieters (Vorschau) zu Erläuterungen hinzugefügt.
    + Reduzierte Standardbatchgröße beim Streamen globaler Erläuterungen von 10.000 auf 100.
    + Explainern wurde das Flag „model_task“ hinzugefügt, um Benutzern das Außerkraftsetzen der automatischen Standardrückschlusslogik für den Modelltyp zu gestatten.
  + **azureml-mlflow**
    + Fehler in mlflow.azureml.build_image behoben, bei dem geschachtelte Verzeichnisse ignoriert werden.
  + **azureml-pipeline-steps**
    + Möglichkeit zum Ausführen von JAR-Aufträgen in einem vorhandenen Azure Databricks-Cluster wurde hinzugefügt.
    + Unterstützung der Parameter instance_pool_id und cluster_log_dbfs_path. für den DatabricksStep-Schritt hinzugefügt.
    + Unterstützung für Pipelineparameter im DatabricksStep-Schritt hinzugefügt.
  + **azureml-train-automl**
    + `docstrings` für die ensemblebezogenen Dateien wurden hinzugefügt.
    + Die Ausdrucksweise der Dokumente für `max_cores_per_iteration` und `max_concurrent_iterations` wurde angepasst.
    + Die Protokollierung von Vorhersageaufgaben bei Remoteausführungen wurde verbessert, und dem Benutzer wird nun eine umfassende Fehlermeldung bereitgestellt, wenn die Ausführung fehlschlägt.
    + get_data wurde aus dem Pipeline-Notebook `automlstep` entfernt.
    + `dataprep` wird jetzt in `automlstep` unterstützt.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Neue Features**
  + Sie können jetzt die Ausführung bestimmter Inspektoren (z. B. Histogramm, Punktdiagramm usw.) für bestimmte Spalten anfordern.
  + `append_columns`wurde ein prarallelisiertes Argument hinzugefügt. Wenn „True“, werden Daten in den Arbeitsspeicher geladen, aber die Ausführung wird parallel ausgeführt. Wenn „False“, wird die Ausführung gestreamt, aber singlethread.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK für Python v1.0.53

+ **Neue Features**
  + Für das automatisierte maschinelle Lernen wird jetzt das Trainieren von ONNX-Modellen auf dem Remotecomputeziel unterstützt.
  + In Azure Machine Learning ist es jetzt möglich, das Training von einer vorherigen Ausführung, einem Prüfpunkt oder für bestimmte Modelldateien fortzusetzen.
    + Informieren Sie sich, wie Sie [Estimators verwenden, um das Training für eine vorherige Ausführung fortzusetzen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb).

+ **Fehlerbehebungen und Verbesserungen**
  + **azure-cli-ml**
    + Die CLI-Befehle „model deploy“ und „service update“ akzeptieren jetzt Parameter, Konfigurationsdateien oder eine Kombination daraus. Parameter haben Vorrang vor Attributen in Dateien.
    + Die Modellbeschreibung kann nach der Registrierung jetzt aktualisiert werden.
  + **azureml-automl-core**
    + Aktualisierung der NimbusML-Abhängigkeit auf Version 1.2.0 (derzeitige aktuelle Version).
    + Unterstützung für NimbusML-Estimators und -Pipelines für die Verwendung in AutoML-Estimators.
    + Es wurde ein Fehler im Vorgang bei der Ensembleauswahl behoben, bei dem das resultierende Ensemble unnötig vergrößert wurde, auch wenn die Ergebnisse konstant blieben.
    + Aktivierung der Wiederverwendung einiger Featurebereitstellungen für Vorhersageaufgaben über Kreuzvalidierungsaufteilungen hinweg. So wird die Laufzeit des Setups für aufwändige Featurebereitstellungen, z. B. Verzögerungen und gleitende Fenster, ungefähr um den Faktor „n_cross_validations“ beschleunigt.
    + Es wurde ein Problem behoben, das aufgetreten ist, wenn die Zeit außerhalb des von Pandas unterstützten Zeitbereichs gelegen hat. Nun wird ein DataException-Vorgang ausgelöst, falls die Zeit kleiner als „pd.Timestamp.min“ oder größer als „pd.Timestamp.max“ ist.
    + Bei der Vorhersage sind jetzt verschiedene Häufigkeiten in Trainings- und Testsätzen zulässig, falls sie aneinander ausgerichtet werden können. Dies ist beispielsweise für „Vierteljährlich ab Januar“ und „Vierteljährlich ab Oktober“ möglich.
    + Die „parameters“-Eigenschaft wurde dem „TimeSeriesTransformer“-Element hinzugefügt.
    + Entfernen Sie alte Ausnahmeklassen.
    + Bei Vorhersageaufgaben akzeptiert der Parameter `target_lags` jetzt einen einzelnen Integerwert oder eine Liste mit Integerwerten. Wenn der Integerwert angegeben wurde, wird nur eine Verzögerung erstellt. Bei Angabe einer Liste werden die eindeutigen Werte von Verzögerungen verwendet. Mit „target_lags=[1, 2, 2, 4]“ werden Verzögerungen mit einer, zwei und vier Perioden erstellt.
    + Der Fehler im Zusammenhang mit dem Verlust von Spaltentypen nach der Transformation wurde behoben (Link zum Fehler).
    + Bei `model.forecast(X, y_query)` wurde „y_query“ gestattet, einen Objekttyp anzunehmen, der „None(s)“ (Nichts) am Anfang enthält (#459519).
    + Der `automl`-Ausgabe wurden erwartete Werte hinzugefügt.
  + **azureml-contrib-datadrift**
    +  Verbesserungen am Beispielnotebook, z. B. Wechsel zu „azureml-opendatasets“ anstelle von „azureml-contrib-opendatasets“ und Leistungsverbesserungen bei der Anreicherung von Daten.
  + **azureml-contrib-explain-model**
    + Fehlerbehebung: „transformations“-Argument für LIME Explainer für unformatierte Featurepriorität im azureml-contrib-explain-model-Paket.
    + Bilderklärungen im Image Explainer für das AzureML-contrib-explain-model-Paket wurden Segmentierungen hinzugefügt.
    + SciPy Sparse-Unterstützung für Lime Explainer hinzugefügt.
    + Dem Mimic Explainer wurde `batch_size` hinzugefügt, wenn `include_local=False` für das Streaming globaler Erklärungen in Batches zutrifft, um die Ausführungszeit von DecisionTreeExplainableModel zu verbessern.
  + **azureml-contrib-featureengineering**
    + Fehlerbehebung für Aufruf von set_featurizer_timeseries_params(): Änderung des Wörterbuchwerttyps und Nullüberprüfung. - Notebook für `timeseries`-Featurizer wurde hinzugefügt.
    + Aktualisierung der NimbusML-Abhängigkeit auf Version 1.2.0 (derzeitige aktuelle Version).
  + **azureml-core**
    + Die Möglichkeit zum Anfügen von DBFS-Datenspeichern in der AzureML CLI wurde hinzugefügt.
    + Der Fehler für den Datenspeicherupload, bei dem ein leerer Ordner erstellt wurde, wenn `target_path` mit `/` gestartet wurde, wurde behoben.
    + Ein Problem mit `deepcopy` in ServicePrincipalAuthentication wurde behoben.
    + Die Befehle „az ml environment show“ und „az ml environment list“ wurden der CLI hinzugefügt.
    + Umgebungen unterstützen jetzt das Angeben einer „base_dockerfile“ als Alternative zu einem bereits erstellten „base_image“.
    + Die nicht genutzte RunConfiguration-Einstellung „auto_prepare_environment“ wurde als veraltet gekennzeichnet.
    + Die Modellbeschreibung kann nach der Registrierung jetzt aktualisiert werden.
    + Fehlerbehebung: Beim Löschen von Modellen und Bildern werden jetzt mehr Informationen zum Abrufen von Upstreamobjekten bereitgestellt, die von ihnen abhängen, wenn der Löschvorgang aufgrund einer Upstreamabhängigkeit fehlschlägt.
    + Es wurde ein Fehler behoben, bei dem eine leere Dauer für Bereitstellungen ausgegeben wurde, die beim Erstellen eines Arbeitsbereichs für einige Umgebungen durchgeführt werden.
    + Ausnahmen aufgrund eines Fehlers beim Erstellen von Arbeitsbereichen verbessert. Benutzern wird nicht eine Meldung der Art „Arbeitsbereich kann nicht erstellt werden. ... kann nicht gefunden werden.“ angezeigt, sondern stattdessen der tatsächliche Erstellungsfehler.
    + Unterstützung für die Tokenauthentifizierung in AKS-Webdiensten hinzugefügt.
    + `Webservice`-Objekten wurde die `get_token()`-Methode hinzugefügt.
    + CLI-Unterstützung für die Verwaltung von Machine Learning-Datasets wurde hinzugefügt.
    + Für `Datastore.register_azure_blob_container` kann jetzt optional ein `blob_cache_timeout`-Wert (in Sekunden) verwendet werden, mit dem die blobfuse-Bereitstellungsparameter konfiguriert werden, um den Cacheablauf für diesen Datenspeicher zu aktivieren. In der Standardeinstellung erfolgt kein Timeout. Wenn beispielsweise ein Blob gelesen wird, verbleibt er im lokalen Cache, bis der Auftrag abgeschlossen ist. Für die meisten Aufträge wird diese Einstellung bevorzugt, aber bei einigen Aufträgen müssen mehr Daten aus einem großen Dataset gelesen werden, als auf die Knoten passen. Bei diesen Aufträgen ist der Vorgang eher erfolgreich, wenn dieser Parameter optimiert wird. Gehen Sie beim Optimieren dieses Parameters mit Bedacht vor: Wenn Sie den Wert zu niedrig festlegen, kann dies zu einer schlechten Leistung führen, da die in einer Epoche verwendeten Daten ggf. ablaufen, bevor sie erneut verwendet werden. Alle Lesevorgänge erfolgen aus dem Blobspeicher/Netzwerk statt aus dem lokalen Cache. Hieraus ergibt sich eine negative Auswirkung auf die Trainingszeiten.
    + Die Modellbeschreibung kann nach der Registrierung jetzt richtig aktualisiert werden.
    + Beim Löschen von Modellen und Images werden jetzt mehr Informationen zu Upstream-Objekten angegeben, die davon abhängig sind und dazu führen, dass der Löschvorgang nicht erfolgreich ist.
    + Die Ressourcenverwendung bei Remoteausführungen, die „azureml.mlflow“ nutzen, wurde verbessert.
  + **azureml-explain-model**
    + Fehlerbehebung: „transformations“-Argument für LIME Explainer für unformatierte Featurepriorität im azureml-contrib-explain-model-Paket.
    + SciPy Sparse-Unterstützung für Lime Explainer hinzugefügt.
    + Shape Linear Explainer-Wrapper wurde hinzugefügt, und Tabular Explainer wurde eine weitere Ebene zur Erklärung von linearen Modellen hinzugefügt.
    + Für Mimic Explainer in der Erklärungsmodellbibliothek wurde der Fehler behoben, der bei geringer Dateneingabe für „include_local=False“ aufgetreten ist.
    + Der `automl`-Ausgabe wurden erwartete Werte hinzugefügt.
    + Die Featurepriorität für die Permutation wurde für den Fall korrigiert, in dem das „transformations“-Argument bereitgestellt wurde, um die unformatierte Featurepriorität zu erhalten.
    + Dem Mimic Explainer wurde `batch_size` hinzugefügt, wenn `include_local=False` für das Streaming globaler Erklärungen in Batches zutrifft, um die Ausführungszeit von DecisionTreeExplainableModel zu verbessern.
    + Für die Modellerklärungsbibliothek wurden Fehler für Blackbox Explainer behoben, bei denen für die Vorhersage eine Pandas-Datenrahmeneingabe erforderlich ist.
    + Der Fehler, bei dem `explanation.expected_values` gelegentlich einen float-Wert anstelle einer Liste mit einem darin enthaltenen float-Wert zurückgegeben hat, wurde behoben.
  + **azureml-mlflow**
    + Leistung von „mlflow.set_experiment(experiment_name)“ wurde verbessert.
    + Ein Fehler bei der Nutzung von „InteractiveLoginAuthentication“ für „mlflow tracking_uri“ wurde behoben.
    + Die Ressourcenverwendung bei Remoteausführungen, die „azureml.mlflow“ nutzen, wurde verbessert.
    + Die Dokumentation des „azureml-mlflow“-Pakets wurde verbessert.
    + Es wurde ein Fehler gepatcht, bei dem „mlflow.log_artifacts("my_dir")“ Artefakte unter „my_dir/<artifact-paths>“ gespeichert hat, anstatt unter „<artifact-paths>“.
  + **azureml-opendatasets**
    + `pyarrow` von `opendatasets` wurde an alte Versionen (< 0.14.0) angeheftet, da dort ein Arbeitsspeicherproblem eingeführt wurde.
    + „azureml-contrib-opendatasets“ wurde in „azureml-opendatasets“ verschoben.
    + Open Dataset-Klassen wurde gestattet, sich bei einem Azure Machine Learning-Arbeitsbereich zu registrieren und nahtlos AML-Dataset-Funktionen zu nutzen.
    + Die NoaaIsdWeather-Erweiterungsleistung in Nicht-Spark-Versionen wurde erheblich verbessert.
  + **azureml-pipeline-steps**
    + DBFS Datastore wird in DatabricksStep jetzt für Ein- und Ausgaben unterstützt.
    + Aktualisierung der Dokumentation für Azure Batch-Schritt in Bezug auf Ein-/Ausgaben.
    + In AzureBatchStep wurde der Standardwert von *delete_batch_job_after_finish* in *true* geändert.
  + **azureml-telemetry**
    +  „azureml-contrib-opendatasets“ wurde in „azureml-opendatasets“ verschoben.
    + Open Dataset-Klassen wurde gestattet, sich bei einem Azure Machine Learning-Arbeitsbereich zu registrieren und nahtlos AML-Dataset-Funktionen zu nutzen.
    + Die NoaaIsdWeather-Erweiterungsleistung in Nicht-Spark-Versionen wurde erheblich verbessert.
  + **azureml-train-automl**
    + Aktualisierung der Dokumentation zu „get_output“, damit der tatsächliche Rückgabetyp widergespiegelt wird, und zusätzliche Hinweise zum Abrufen wichtiger Eigenschaften.
    + Aktualisierung der NimbusML-Abhängigkeit auf Version 1.2.0 (derzeitige aktuelle Version).
    + Der `automl`-Ausgabe wurden erwartete Werte hinzugefügt.
  + **azureml-train-core**
    + Zeichenfolgen werden jetzt als Computeziel für die automatisierte Hyperparameteroptimierung akzeptiert.
    + Die nicht genutzte RunConfiguration-Einstellung „auto_prepare_environment“ wurde als veraltet gekennzeichnet.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK, Version 1.1.9

+ **Neue Features**
  + Unterstützung für das direkte Lesen einer Datei aus einer HTTP- oder HTTPS-URL wurde hinzugefügt.

+ **Fehlerbehebungen und Verbesserungen**
  + Verbesserte Fehlermeldung beim Versuch, ein Parquet-Dataset aus einer Remotequelle zu lesen (dies wird derzeit nicht unterstützt).
  + Es wurde ein Fehler behoben, der beim Schreiben in Dateien im Parquet-Format in ADLS Gen 2 und beim Aktualisieren des ADLS Gen 2-Containernamens im Pfad auftrat.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Grafische Benutzeroberfläche
+ **Vorschaufunktionen**
  + Das Modul „Execute R Script“ wurde in der grafischen Benutzeroberfläche hinzugefügt.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK für Python, v1.0.48

+ **Neue Features**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** ist jetzt verfügbar als **azureml-opendatasets**. Das alte Paket kann weiterhin funktionieren, aber wir empfehlen Ihnen die Verwendung von **azureml-opendatasets**, um in den Genuss umfangreicherer Funktionen und Verbesserungen zu kommen.
    + Mit diesem neuen Paket können Sie geöffnete Datasets als Dataset im Azure Machine Learning-Arbeitsbereich registrieren und jegliche Funktionen nutzen, die dieses Dataset bietet.
    + Es umfasst außerdem vorhandene Funktionen, wie das Nutzen geöffneter Datasets als Pandas/SPARK-Dataframes sowie Standortverknüpfungen für einige Datasets wie Wetter.

+ **Vorschaufunktionen**
    + HyperDriveConfig kann jetzt Pipelineobjekte als Parameter akzeptieren, um die Hyperparameteroptimierung mithilfe einer Pipeline zu unterstützen.

+ **Fehlerbehebungen und Verbesserungen**
  + **azureml-train-automl**
    + Der Fehler im Zusammenhang mit dem Verlust von Spaltentypen nach der Transformation wurde behoben.
    + Der Fehler, bei dem „y_query“ gestattet wurde, einen Objekttyp anzunehmen, der „None(s)“ (Nichts) am Anfang enthielt, wurde behoben.
    + Das Problem im Vorgang bei der Ensembleauswahl, wodurch das resultierende Ensemble unnötig vergrößert wurde, auch wenn die Ergebnisse konstant blieben, wurde behoben.
    + Das Problem mit den Einstellungen „allow list_models“ und „block list_models“ in AutoMLStep wurde behoben.
    + Das Problem, das die Verwendung der Vorverarbeitung verhindert hat, wenn AutoML im Kontext von Azure ML-Pipelines verwendet worden wäre, wurde behoben.
  + **azureml-opendatasets**
    + „azureml-contrib-opendatasets“ wurde in „azureml-opendatasets“ verschoben.
    + Open Dataset-Klassen wurde gestattet, sich bei einem Azure Machine Learning-Arbeitsbereich zu registrieren und nahtlos AML-Dataset-Funktionen zu nutzen.
    + Die NoaaIsdWeather-Erweiterungsleistung in Nicht-Spark-Versionen wurde erheblich verbessert.
  + **azureml-explain-model**
    + Die Onlinedokumentation für Interpretierbarkeitsobjekte wurde aktualisiert.
    + Dem Mimic Explainer wurde `batch_size` hinzugefügt, wenn `include_local=False` für das Streaming globaler Erläuterungen in Batches zutrifft, um die Ausführungszeit von DecisionTreeExplainableModel für die Modellerklärbarkeitsbibliothek zu verbessern.
    + Das Problem, bei dem `explanation.expected_values` gelegentlich einen float-Wert anstelle einer Liste mit einem enthaltenen float-Wert zurückgab, wurde behoben.
    + Der `automl`-Ausgabe für den Mimic Explainer in der Erläuterungsmodellbibliothek wurden erwartete Werte hinzugefügt.
    + Die Wichtigkeit der Permutationsfunktion wurde korrigiert, wenn das „transformations“-Argument bereitgestellt wurde, um die unformatierte Funktionswichtigkeit zu erhalten.
  + **azureml-core**
    + Die Möglichkeit zum Anfügen von DBFS-Datenspeichern in der AzureML CLI wurde hinzugefügt.
    + Das Problem mit dem Datenspeicherupload, bei dem ein leerer Ordner erstellt wird, wenn `target_path` mit `/` gestartet wird, wurde behoben.
    + Der Vergleich von zwei Datasets wurde ermöglicht.
    + Beim Löschen von Modellen und Bildern werden jetzt mehr Informationen zum Abrufen von Upstreamobjekten bereitgestellt, die von ihnen abhängen, wenn der Löschvorgang aufgrund einer Upstreamabhängigkeit fehlschlägt.
    + Die nicht verwendete „RunConfiguration“-Einstellung in auto_prepare_environment wurde als veraltet markiert.
  + **azureml-mlflow**
    + Die Ressourcenverwendung bei Remoteausführungen, die „azureml.mlflow“ verwenden, wurde verbessert.
    + Die Dokumentation des „azureml-mlflow“-Pakets wurde verbessert.
    + Das Problem, bei dem „mlflow.log_artifacts("my_dir")“ Artefakte unter „my_dir/artifact-paths“ anstelle unter „artifact-paths“ speichern würde, wurde behoben.
  + **azureml-pipeline-core**
    + Der Parameter „hash_paths“ ist für alle Pipelineschritte veraltet und wird in Zukunft entfernt. Inhalte von „source_directory“ (mit Ausnahme der in `.amlignore` oder `.gitignore` aufgeführten Dateien) werden standardmäßig hashcodiert.
    + Module und ModuleStep wurden kontinuierlich verbessert, um computetypspezifische Module zur Vorbereitung auf die RunConfiguration-Integration und andere Änderungen zu unterstützen, damit deren Verwendung in Pipelines möglich wird.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Verbesserte Dokumentation hinsichtlich Eingaben/Ausgaben.
    + AzureBatchStep: Der Standardwert von „delete_batch_job_after_finish“ wurde in „true“ geändert.
  + **azureml-train-core**
    + Zeichenfolgen werden jetzt als Computeziel für die automatisierte Hyperparameteroptimierung akzeptiert.
    + Die nicht verwendete „RunConfiguration“-Einstellung in auto_prepare_environment wurde als veraltet markiert.
    + Die Parameter `conda_dependencies_file_path` und `pip_requirements_file_path` wurden zugunsten von `conda_dependencies_file` bzw. `pip_requirements_file` als veraltet markiert.
  + **azureml-opendatasets**
    + Die NoaaIsdWeather-Erweiterungsleistung in Nicht-Spark-Versionen wurde erheblich verbessert.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK für Python, Version 1.0.33 veröffentlicht.

+ Hardwarebeschleunigte Azure ML-Modelle in [FPGAs](how-to-deploy-fpga-web-service.md) sind allgemein verfügbar.
  + Mit dem [Paket „azureml-accel-models“ ](how-to-deploy-fpga-web-service.md) haben Sie jetzt folgende Möglichkeiten:
    + Trainieren der Gewichtungen eines unterstützten Deep Neural Network (ResNet 50, ResNet 152, DenseNet-121, VGG-16 und SSD-VGG)
    + Verwenden von Transferlernen mit dem unterstützten DNN
    + Registrieren des Modells beim Modellverwaltungsdienst und Verpacken des Modells in Container
    + Bereitstellen des Modells auf einem virtuellen Azure-Computer mit einem FPGA in einem Azure Kubernetes Service (AKS)-Cluster
  + Bereitstellen des Containers auf einem [Azure Data Box-Edge](../databox-online/azure-stack-edge-overview.md)-Servergerät
  + Bewerten Ihrer Daten mit dem gRPC-Endpunkt mit diesem [Beispiel](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatisiertes maschinelles Lernen

+ Featuresweeping, um das dynamische Hinzufügen von :::no-loc text="featurizers"::: zur Leistungsoptimierung zu ermöglichen. Neue :::no-loc text="featurizers":::: Einbettungen von Arbeit, Gewichtung von Beweisen, Zielcodierungen, Textzielcodierung, Clusterabstand
+ Intelligente Kreuzvalidierung zur Verarbeitung trainierter/gültiger Teilungen im Rahmen des automatisierten maschinellen Lernens
+ Einige Änderungen zur Speicheroptimierung und Verbesserung der Laufzeitleistung
+ Leistungssteigerung in Modellerklärung
+ ONNX-Modellwechsel für lokale Ausführung
+ Subsampling-Unterstützung hinzugefügt
+ Intelligentes Beenden, wenn keine Beendigungskriterien definiert sind
+ Gestapelte Ensembles

+ Zeitreihe und Vorhersage
  + Neue Prognosefunktion
  + Sie können jetzt Kreuzvalidierung mit rollierendem Ursprung für Zeitreihendaten verwenden.
  + Neue Funktionalität zur Konfiguration von Zeitreihenverzögerungen hinzugefügt
  + Neue Funktionalität zur Unterstützung von Aggregatfeatures für rollierende Zeitfenster hinzugefügt
  + Neue Feiertagserkennung und Featurizer, wenn der Ländercode in den Experiment-Einstellungen definiert ist

+ Azure Databricks
  + Funktion zur Vorhersage von Zeitreihen und Modellerklärbarkeit/-interpretierbarkeit aktiviert
  + Automatisierte ML-Experimente können nun abgebrochen und wieder aufgenommen (fortgesetzt) werden.
  + Unterstützung für Multi-Core-Verarbeitung hinzugefügt

### <a name="mlops"></a>MLOps
+ **Lokale Bereitstellung und Debuggen für die Bewertung von Containern**<br/> Sie können jetzt ein ML-Modell lokal bereitstellen und mit Ihrer Bewertungsdatei und Abhängigkeiten schnell durchlaufen, um sicherzustellen, dass sie sich wie erwartet verhalten.

+ **„InferenceConfig“ und „Model.deploy()“ eingeführt**<br/> Die Modellimplementierung unterstützt nun die Angabe eines Quellordners mit einem Eingabeskript, wie bei einer RUNCONFIG-Datei.  Darüber hinaus wurde Modellimplementierung zu einem einzelnen Befehl vereinfacht.

+ **Nachverfolgen von Git-Verweisen**<br/> Kunden fragen seit einiger Zeit nach grundlegenden Git-Integrationsmöglichkeiten, da diese für einen umfassenden Überwachungspfad hilfreich sind. Wir haben Nachverfolgung für die wichtigsten Einheiten in Azure ML für Git-bezogene Metadaten (Repository, Commit, fehlerfreier Zustand) implementiert. Diese Informationen werden automatisch vom SDK und der CLI gesammelt.

+ **Modellprofilerstellungs- und Validierungsdienst**<br/> Kunden beklagen sich häufig über die Schwierigkeit der richtigen Dimensionierung der mit ihrem Rückschlussdienst verbundenen Computeleistung. Mit unserem Modellprofil-Erstellungsdienst kann der Kunde Beispieleingaben bereitstellen und wir erstellen Profile für 16 verschiedene CPU-/Arbeitsspeicherkonfigurationen, um die optimale Größe für die Bereitstellung zu ermitteln.

+ **Verwenden eines eigenen Basisimages für Rückschlüsse**<br/> Eine weitere häufige Beschwerde betraf die Schwierigkeit, bei der gemeinsamen Nutzung von Abhängigkeiten von der Experimentierung zu Rückschlüssen zu gelangen. Mit unserer neuen Funktion zur gemeinsamen Nutzung von Basisimages können Sie nun Ihre Experimentierbasisimages einschließlich Abhängigkeiten usw. für Rückschlüsse wiederverwenden. Dies sollte Bereitstellungen beschleunigen und den Abstand zwischen der inneren und äußeren Schleife verringern.

+ **Verbesserte Swagger-Schemagenerierung**<br/> Unsere bisherige Swagger-Generierungsmethode war fehleranfällig und konnte nicht automatisiert werden. Wir haben eine neue Inline-Methode zum Generieren von Swagger-Schemas mit einer beliebigen Python-Funktion über Decorator-Elemente. Wir haben diesen Code als Open Source freigegeben, und unser Schemagenerierungsprotokoll ist nicht an die Azure ML-Plattform gekoppelt.

+ **Azure ML CLI allgemein verfügbar (GA)**<br/> Modelle können jetzt mit einem einzigen CLI-Befehl bereitgestellt werden. Wir haben häufig das Kundenfeedback erhalten, dass niemand ein ML-Modell über ein Jupyter-Notebook bereitstellt. Die [**CLI-Referenzdokumentation**](./reference-azure-machine-learning-cli.md) wurde aktualisiert.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK für Python, Version 1.0.30 veröffentlicht.

Das [`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?preserve-view=true&view=azure-ml-py)-Element wurde eingeführt, um unter Beibehaltung desselben Endpunkts eine neue Version einer veröffentlichten Pipeline hinzuzufügen.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure-Portal
  + Sie können jetzt ein vorhandenes Skript, das auf einem vorhandenen Remotecomputecluster ausgeführt wird, erneut übermitteln.
  + Sie können jetzt eine veröffentlichte Pipeline mit neuen Parametern auf der Registerkarte „Pipelines“ ausführen.
  + Ausführungsdetails unterstützen jetzt eine neue Momentaufnahme-Dateianzeige. Sie können eine Momentaufnahme des Verzeichnisses zu dem Zeitpunkt anzeigen, zu dem Sie eine bestimmte Ausführung übermittelt haben. Sie können auch das Notebook herunterladen, das zum Starten der Ausführung übermittelt wurde.
  + Sie können jetzt übergeordnete Ausführungen über das Azure-Portal abbrechen.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK für Python, Version 1.0.23

+ **Neue Features**
  + Das Azure Machine Learning SDK unterstützt jetzt Python 3.7.
  + Azure Machine Learning-DNN-Kalkulatoren bieten jetzt integrierte Unterstützung mehrerer Versionen. Beispielsweise akzeptiert der Schätzer `TensorFlow` jetzt den Parameter `framework_version`, und Benutzer können als Version „1.10“ oder „1.12“ angeben. Eine Liste der von Ihrer aktuellen SDK-Version unterstützten Versionen erhalten Sie, indem Sie `get_supported_versions()` für die gewünschte Frameworkklasse aufrufen (z. B. `TensorFlow.get_supported_versions()`).
  Eine Liste der von der aktuellen SDK-Version unterstützten Versionen finden Sie in der [Dokumentation für DNN-Kalkulatoren](/python/api/azureml-train-core/azureml.train.dnn?preserve-view=true&view=azure-ml-py).

## <a name="2019-03-25"></a>25.3.2019

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK für Python v1.0.21

+ **Neue Features**
  + Die Methode *azureml.core.Run.create_children* ermöglicht die Erstellung mit geringer Wartezeit von mehreren untergeordneten Ausführungen mit einem einzigen Aufruf.

## <a name="2019-03-11"></a>11.03.2019

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK für Python v1.0.18

 + **Änderungen**
   + Das azureml-tensorboard-Paket ersetzt azureml-contrib-tensorboard.
   + Mit diesem Release können Sie direkt während der Erstellung Ihres verwalteten Computeclusters (amlcompute) ein Benutzerkonto darin einrichten. Dazu werden diese Eigenschaften in der Bereitstellungskonfiguration übergeben. Weitere Informationen finden Sie in der [SDK-Referenzdokumentation](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Neue Features**
  + Unterstützt jetzt das Hinzufügen von zwei numerischen Spalten, um eine resultierende-Spalte mithilfe der Ausdruckssprache zu generieren.

+ **Fehlerbehebungen und Verbesserungen**
  + Die Dokumentation und Parameterüberprüfung für „random_split“ wurden verbessert.

## <a name="2019-02-27"></a>27.02.2019

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Fehlerbehebung**
  + Ein Authentifizierungsproblem beim Dienstprinzipal wurde behoben, das durch eine API-Änderung hervorgerufen wurde.

## <a name="2019-02-25"></a>25.02.2019

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK für Python v1.0.17

+ **Neue Features**
  + Azure Machine Learning bietet jetzt erstklassige Unterstützung für das beliebte DNN-Framework Chainer. Mithilfe von [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py) können Klassenbenutzer Chainer-Modelle auf einfache Weise trainieren und bereitstellen.
    + Erfahren Sie mehr über das [Ausführen von verteiltem Training mit ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + Erfahren Sie mehr über die [Hyperparameteroptimierung mit Chainer mithilfe von HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning-Pipelines haben neu die Funktionalität zum Auslösen einer Pipelineausführung auf der Grundlage von Änderungen im Datenspeicher erhalten. Die Pipeline [Notizbuch planen](https://aka.ms/pl-schedule) wurde aktualisiert, um diese Funktion ins rechte Licht zu rücken.

+ **Fehlerbehebungen und Verbesserungen**
  + Wir haben Azure Machine Learning-Pipelines die Unterstützung für das Festlegen der source_directory_data_store-Eigenschaft auf einen gewünschten Datenspeicher (wie etwa einen Blob-Speicher) auf [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration?preserve-view=true&view=azure-ml-py) hinzugefügt, die im [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?preserve-view=true&view=azure-ml-py) verfügbar sind. Standardmäßig wird in den Schritten der Azure File-Speicher als unterstützender Datenspeicher verwendet, bei dem es zu Drosselungsproblemen kommen kann, wenn eine große Anzahl von Schritten parallel abgearbeitet wird.

### <a name="azure-portal"></a>Azure-Portal

+ **Neue Features**
  + Neue Benutzeroberfläche mit Drag & Drop im Tabellen-Editor für Berichte. Benutzer können eine Spalte aus der Quelle auf den Tabellenbereich ziehen, wo eine Vorschau der Tabelle angezeigt wird. Die Spalten können neu angeordnet werden.
  + Neue Protokolldateianzeige
  + Links zu Experimentausführungen, Compute, Modellen, Images und Bereitstellungen auf der Registerkarte „Aktivitäten“

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Übersicht für [Azure Machine Learning](overview-what-is-azure-ml.md).
