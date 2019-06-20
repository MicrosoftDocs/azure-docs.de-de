---
title: Neuerungen im Release
titleSuffix: Azure Machine Learning service
description: Erfahren Sie mehr über die neuesten Updates des Azure Machine Learning Service und Python SDKs für maschinelles Lernen und Datenaufbereitung.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 2dd397e879dd76cabd119a3cbedff34041be2d13
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298483"
---
# <a name="azure-machine-learning-service-release-notes"></a>Azure Machine Learning-Dienst – Anmerkungen zu dieser Version

Erfahren Sie in diesem Artikel mehr über die Versionen des Azure Machine Learning-Diensts.  Eine vollständige Beschreibung jedes SDKs finden Sie in der jeweiligen Referenzdokumentation für:
+ Das [**Haupt-SDK für Python**](https://aka.ms/aml-sdk) von Azure Machine Learning
+ Das [**Datenaufbereitungs-SDK**](https://aka.ms/data-prep-sdk) von Azure Machine Learning

Sehen Sie die [Liste der bekannten Probleme](resource-known-issues.md) an, um mehr über bekannte Fehler und Problemumgehungen zu erfahren.

## <a name="2019-05-28"></a>28\.05.2019

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning Data Prep SDK, Version 1.1.4

+ **Neue Features**
  + Sie können jetzt die folgenden Ausdruckssprachfunktionen verwenden, um datetime-Werte zu extrahieren und sie nach der Analyse neuen Spalten zuzuweisen.
    + `RegEx.extract_record()` extrahiert DateTime-Elemente in eine neue Spalte.
    + `create_datetime()` erstellt DateTime-Objekte aus separaten datetime-Elementen.
  + Beim Aufrufen von `get_profile()` können Sie jetzt sehen, dass Quantilspalten als „(est.)“ bezeichnet werden, um deutlich anzugeben, dass es sich um Näherungswerte handelt.
  + Sie können jetzt beim Lesen aus Azure Blob Storage ** als Platzhalter verwenden.
    + Beispiel: `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Fehlerbehebungen**
  + Korrektur eines Fehlers im Zusammenhang mit dem Lesen einer Parquet-Datei aus einer Remotequelle (Azure-Blob).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK für Python, Version 1.0.39
+ **Änderungen**
  + Die für ein Ausführen verwendete Konfigurationsoption „auto_prepare_environment“ ist veraltet, wobei automatisches Vorbereiten zum Standard wird.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning Data Prep SDK, Version 1.1.3

+ **Neue Features**
  + Hinzugefügte Unterstützung für Lesen aus einer PostgreSQL-Datenbank, entweder durch Aufrufen von „read_postgresql“ oder durch Verwenden eines Datenspeichers.
    + Beispiele finden Sie in den Schrittanleitung:
      + [Data Ingestion notebook](https://aka.ms/aml-data-prep-ingestion-nb) (Datenerfassungsnotebook)
      + [Datastore notebook](https://aka.ms/aml-data-prep-datastore-nb) (Datenspeichernotebook)

+ **Fehlerbehebungen und Verbesserungen**
  + Behobene Probleme Spaltentypkonvertierung:
  + Eine boolesche oder numerische Spalte wird nun richtig in eine boolesche Spalte konvertiert.
  + Es gibt nun keinen Fehler mehr bei dem Versuch, eine Datumsspalte auf den Typ für Datum festzulegen.
  + Verbesserte JoinType-Typen und zugehörige Referenzdokumentation. Wenn Sie zwei Datenflüsse verknüpfen, können Sie jetzt einen der folgenden Verknüpfungstypen (Join-Typen) angeben:
    + NONE, MATCH, INNER, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Verbesserte Datentyprückschlüsse, um mehr Datumsformate zu erkennen.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure-Portal

Im Azure-Portal haben Sie jetzt folgende Möglichkeiten:
+ Erstellen und Ausführen automatisierter ML-Experimente 
+ Erstellen eines virtuellen Notebook-Computers, um selbst Jupyter-Notebooks zu testen
+ Brandneuer Erstellungsbereich (Vorschauversion) im Machine Learning Service-Arbeitsbereich, der automatisiertes maschinelles Lernen, eine grafische Benutzeroberfläche und gehostete virtuelle Notebook-Computer umfasst
    + Automatisches Erstellen eines Modells mithilfe von automatisiertem maschinellem Lernen 
    + Verwenden einer grafischen Benutzeroberfläche mit Drag & Drop-Funktion zum Ausführen von Experimenten
    + Erstellen eines virtuellen Notebook-Computers zum Untersuchen von Daten, Erstellen von Modellen und Bereitstellen von Diensten
+ Liveupdates von Diagrammen und Metriken auf Seiten mit Ausführungsberichten und -details
+ Aktualisierte Dateianzeige für Protokolle, Ausgaben und Snapshots auf den Detailseiten der Ausführung
+ Neue und verbesserte Berichterstellung auf der Registerkarte „Experimente“ 
+ Neue Möglichkeit zum Herunterladen der Datei „config.json“ von der Übersichtsseite des Azure Machine Learning Service-Arbeitsbereichs
+ Unterstützung der Erstellung von Machine Learning Service-Arbeitsbereichen über den Azure Databricks-Arbeitsbereich 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK für Python, Version 1.0.33
+ **Neue Features**
  + Die Methode _Workspace.create_ akzeptiert nun Standardclusterkonfigurationen für CPU- und GPU-Cluster.
  + Wenn die Erstellung des Arbeitsbereichs fehlschlägt, werden abhängige Ressourcen bereinigt.
  + Die standardmäßige Azure Container Registry SKU wurde auf „Basic“ umgestellt.
  + Die Azure Container Registry-Instanz wird verzögert erstellt, wenn sie für die Ausführung oder Bilderstellung benötigt wird.
  + Unterstützung von Umgebungen für Trainingsausführungen

### <a name="notebook-virtual-machine"></a>Virtueller Notebook-Computer 

Verwenden Sie einen virtuellen Notebook-Computer als sichere, unternehmensgerechte Hostingumgebung für Jupyter-Notebooks, in der Sie Machine Learning-Experimente programmieren, Modelle als Webendpunkte bereitstellen und alle anderen vom Azure Machine Learning SDK mit Python unterstützten Vorgänge durchführen können. Die Umgebung bietet mehrere Funktionen:
+ [Schnelles Starten eines vorkonfigurierten virtuellen Notebook-Computers](quickstart-run-cloud-notebook.md)  mit der aktuellen Version des Azure Machine Learning-SDK und zugehörigen Paketen.
+ Der Zugriff wird durch bewährte Technologien wie HTTPS, Azure Active Directory-Authentifizierung und Autorisierung gesichert.
+ Zuverlässige Cloudspeicherung von Notebooks und Code im Blob-Speicherkonto Ihres Azure Machine Learning-Arbeitsbereichs. Sie können Ihre virtuellen Notebook-Computer sicher löschen, ohne Ihre Arbeit zu verlieren.
+ Vorinstallierte Beispielnotebooks zum Erkunden und Experimentieren mit Azure Machine Learning Service-Funktionen.
+ Umfassende Anpassungsfunktionen für virtuelle Azure-Computer, virtuelle Computer jedes Typs, alle Pakete, alle Treiber. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK für Python, Version 1.0.33 veröffentlicht.

+ Hardwarebeschleunigte Azure ML-Modelle in [FPGAs](concept-accelerate-with-fpgas.md) sind allgemein verfügbar.
  + Mit dem [Paket „azureml-accel-models“ ](how-to-deploy-fpga-web-service.md) haben Sie jetzt folgende Möglichkeiten:
    + Trainieren der Gewichtungen eines unterstützten Deep Neural Network (ResNet 50, ResNet 152, DenseNet-121, VGG-16 und SSD-VGG)
    + Verwenden von Transferlernen mit dem unterstützten DNN
    + Registrieren des Modells beim Modellverwaltungsdienst und Verpacken des Modells in Container
    + Bereitstellen des Modells auf einem virtuellen Azure-Computer mit einem FPGA in einem Azure Kubernetes Service (AKS)-Cluster
  + Bereitstellen des Containers auf einem [Azure Data Box-Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)-Servergerät
  + Bewerten Ihrer Daten mit dem gRPC-Endpunkt mit diesem [Beispiel](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatisiertes maschinelles Lernen

+ Feature-Sweeping, um das dynamische Hinzufügen von Featurizern zur Leistungsoptimierung zu ermöglichen. Neue Featurizer: Einbettungen von Arbeit, Gewichtung der Beweise, Zielcodierungen, Textzielcodierung, Clusterabstand
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

+ **Nachverfolgen von Git-Verweisen**<br/> Kunden fragen seit einiger Zeit nach grundlegenden Git-Integrationsmöglichkeiten, da diese für einen End-to-End-Überwachungspfad hilfreich sind. Wir haben Nachverfolgung für die wichtigsten Einheiten in Azure ML für Git-bezogene Metadaten (Repository, Commit, fehlerfreier Zustand) implementiert. Diese Informationen werden automatisch vom SDK und der CLI gesammelt.

+ **Modellprofilerstellungs- und Validierungsdienst**<br/> Kunden beklagen sich häufig über die Schwierigkeit der richtigen Dimensionierung der mit ihrem Rückschlussdienst verbundenen Computeleistung. Mit unserem Modellprofil-Erstellungsdienst kann der Kunde Beispieleingaben bereitstellen und wir erstellen Profile für 16 verschiedene CPU-/Arbeitsspeicherkonfigurationen, um die optimale Größe für die Bereitstellung zu ermitteln.

+ **Verwenden eines eigenen Basisimages für Rückschlüsse**<br/> Eine weitere häufige Beschwerde betraf die Schwierigkeit, bei der gemeinsamen Nutzung von Abhängigkeiten von der Experimentierung zu Rückschlüssen zu gelangen. Mit unserer neuen Funktion zur gemeinsamen Nutzung von Basisimages können Sie nun Ihre Experimentierbasisimages einschließlich Abhängigkeiten usw. für Rückschlüsse wiederverwenden. Dies sollte Bereitstellungen beschleunigen und den Abstand zwischen der inneren und äußeren Schleife verringern.

+ **Verbesserte Swagger-Schemagenerierung**<br/> Unsere bisherige Swagger-Generierungsmethode war fehleranfällig und konnte nicht automatisiert werden. Wir haben eine neue Inline-Methode zum Generieren von Swagger-Schemas mit einer beliebigen Python-Funktion über Decorator-Elemente. Wir haben diesen Code als Open Source freigegeben, und unser Schemagenerierungsprotokoll ist nicht an die Azure ML-Plattform gekoppelt.

+ **Azure ML CLI allgemein verfügbar (GA)**<br/> Modelle können jetzt mit einem einzigen CLI-Befehl bereitgestellt werden. Wir haben häufig das Kundenfeedback erhalten, dass niemand ein ML-Modell über ein Jupyter-Notebook bereitstellt. Die [**CLI-Referenzdokumentation**](https://aka.ms/azmlcli) wurde aktualisiert.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK für Python, Version 1.0.30 veröffentlicht.

Das [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py)-Element wurde eingeführt, um unter Beibehaltung desselben Endpunkts eine neue Version einer veröffentlichten Pipeline hinzuzufügen.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning Data Prep SDK, Version 1.1.2

Hinweis: Die Pakete `numpy` und `pandas` des Data Prep Python SDK werden nicht mehr installiert. Weitere Informationen finden Sie in den [aktualisierten Installationsanweisungen](https://aka.ms/aml-data-prep-installation).

+ **Neue Features**
  + Sie können jetzt die Pivottransformation verwenden.
    + Schrittanleitung: [Pivotnotebook](https://aka.ms/aml-data-prep-pivot-nb)
  + Sie können jetzt reguläre Ausdrücke in nativen Funktionen verwenden.
    + Beispiele:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Sie können jetzt die Funktionen `to_upper` und `to_lower` in der Ausdruckssprache verwenden.
  + Sie können jetzt die Anzahl der eindeutigen Werte in den einzelnen Spalten in einem Datenprofil sehen.
  + Für einige der häufig verwendeten Leserschritte können Sie nun das Argument `infer_column_types` übergeben. Bei Festlegung auf `True` versucht die Datenvorbereitung, die Spaltentypen automatisch zu erkennen und zu konvertieren.
    + `inference_arguments` gilt jetzt als veraltet.
  + Sie können jetzt `Dataflow.shape` aufrufen.

+ **Fehlerbehebungen und Verbesserungen**
  + `keep_columns` akzeptiert jetzt das zusätzliche optionale Argument `validate_column_exists`, das überprüft, ob das Ergebnis von `keep_columns` Spalten enthält.
  + Alle Leserschritte (bei denen aus einer Datei gelesen wird) akzeptieren nun das zusätzliche optionale Argument `verify_exists`.
  + Verbesserte Leistung beim Lesen von Pandas-Datenrahmen und beim Abrufen von Datenprofilen.
  + Korrektur eines Fehlers, bei dem das Aufteilen eines einzelnen Schritts in Slices aus einem Dataflow zu einem Fehler mit einem Index geführt hat.

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
  + Azure Machine Learning-DNN-Kalkulatoren bieten jetzt integrierte Unterstützung mehrerer Versionen. Beispielsweise akzeptiert der Kalkulator `TensorFlow`  jetzt den Parameter `framework_version`, und Benutzer können als Version „1.10“ oder „1.12“ angeben. Eine Liste der von Ihrer aktuellen SDK-Version unterstützten Versionen erhalten Sie, indem Sie `get_supported_versions()` für die gewünschte Frameworkklasse aufrufen (z. B. `TensorFlow.get_supported_versions()`).
  Eine Liste der von der aktuellen SDK-Version unterstützten Versionen finden Sie in der [Dokumentation für DNN-Kalkulatoren](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning Data Prep SDK, Version 1.1.1

+ **Neue Features**
  + Sie können mehrere DataStore/DataPath/DataReference-Quellen mithilfe von Transformationen vom Typ „read_*“ lesen.
  + Sie können die folgenden Vorgänge für Spalten ausführen, um eine neue Spalte zu erstellen: division, floor, modulo, power, length.
  + Die Datenvorbereitung ist jetzt Teil der Azure Machine Learning-Diagnosesuite. Dabei werden standardmäßig die Diagnoseinformationen protokolliert.
    + Um dies zu deaktivieren, legen Sie diese Umgebungsvariable auf TRUE fest: DISABLE_DPREP_LOGGER

+ **Fehlerbehebungen und Verbesserungen**
  + Die Codedokumentation für häufig verwendete Klassen und Funktionen wurde verbessert.
  + Ein Fehler in auto_read_file, der Fehler beim Lesen von Excel-Dateien verursacht hat, wurde behoben.
  + Option zum Überschreiben des Ordners in read_pandas_dataframe wurde hinzugefügt.
  + Die Leistung der Installation der Abhängigkeit dotnetcore2 wurde verbessert, und Unterstützung für Fedora 27/28 und Ubuntu 18.04 wurde hinzugefügt.
  + Die Leistung beim Lesen aus Azure Blobs wurde verbessert.
  + Die Spaltentyperkennung unterstützt nun Spalten vom Typ Long.
  + Ein Fehler, durch den einige Datumswerte anstelle von Python-datetime-Objekten als Zeitstempel angezeigt wurden, wurde behoben.
  + Ein Fehler, durch den wurden einige Typenanzahlen als double-Werte anstelle von Integer-Werten angezeigt wurden, wurde behoben.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK für Python v1.0.21

+ **Neue Features**
  + Die Methode *azureml.core.Run.create_children* ermöglicht die Erstellung mit geringer Wartezeit von mehreren untergeordneten Ausführungen mit einem einzigen Aufruf.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning Data Prep SDK v1.1.0

+ **Wichtige Änderungen**
  + Das Konzept des Data Prep Package ist veraltet und wird nicht mehr unterstützt. Anstelle von mehreren Dataflows in einem Paket können Sie Dataflows auch einzeln beibehalten.
    + Schrittanleitung: [Öffnen und Speichern des Dataflows-Notebooks](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Neue Features**
  + Data Prep kann jetzt Spalten erkennen, die einem bestimmten semantischen Typ entsprechen, und diese entsprechend aufteilen. Zu den derzeit unterstützten Typen gehören: E-Mail-Adresse, geografische Koordinaten (Breitengrad und Längengrad), IPv4- und IPv6-Adressen, US-Telefonnummer und US-Postleitzahl.
    + Schrittanleitung: [Notebook für semantische Typen](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Data Prep unterstützt jetzt die folgenden Operationen, um eine Ergebnisspalte aus zwei numerischen Spalten zu generieren: Subtrahieren, Multiplizieren, Dividieren und Modulo.
  + Sie können `verify_has_data()` für einen Dataflow aufrufen, um zu prüfen, ob der Dataflow bei Ausführung Datensätze erzeugen würde.

+ **Fehlerbehebungen und Verbesserungen**
  + Sie können jetzt die Datengruppenanzahl angeben, die in einem Histogramm für numerische Spaltenprofile verwendet werden sollen.
  + Die `read_pandas_dataframe`-Transformation erfordert jetzt, dass der Datenrahmen Spaltennamen vom Typ Zeichenfolge oder Byte aufweist.
  + Es wurde ein Fehler in der `fill_nulls`-Transformation behoben, bei dem Werte nicht ordnungsgemäß ausgefüllt wurden, wenn die Spalte fehlte.

## <a name="2019-03-11"></a>11\.03.2019

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK für Python v1.0.18

 + **Änderungen**
   + Das azureml-tensorboard-Paket ersetzt azureml-contrib-tensorboard.
   + Mit diesem Release können Sie direkt während der Erstellung Ihres verwalteten Computeclusters (amlcompute) ein Benutzerkonto darin einrichten. Dazu werden diese Eigenschaften in der Bereitstellungskonfiguration übergeben. Weitere Informationen finden Sie in der [SDK-Referenzdokumentation](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Neue Features**
  + Unterstützt jetzt das Hinzufügen von zwei numerischen Spalten, um eine resultierende-Spalte mithilfe der Ausdruckssprache zu generieren.

+ **Fehlerbehebungen und Verbesserungen**
  + Die Dokumentation und Parameterüberprüfung für „random_split“ wurden verbessert.
  
## <a name="2019-02-27"></a>27\.02.2019

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Fehlerbehebung**
  + Ein Authentifizierungsproblem beim Dienstprinzipal wurde behoben, das durch eine API-Änderung hervorgerufen wurde.

## <a name="2019-02-25"></a>25\.02.2019

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK für Python v1.0.17

+ **Neue Features**

  + Azure Machine Learning bietet jetzt erstklassige Unterstützung für das beliebte DNN-Framework Chainer. Mithilfe von [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) können Klassenbenutzer Chainer-Modelle auf einfache Weise trainieren und bereitstellen.
    + Erfahren Sie mehr über das [Ausführen von verteiltem Training mit ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Erfahren Sie mehr über die [Hyperparameteroptimierung mit Chainer mithilfe von HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning Pipelines haben neu die Funktionalität zum Auslösen einer Pipelineausführung auf der Grundlage von Änderungen im Datenspeicher erhalten. Die Pipeline [Notizbuch planen](https://aka.ms/pl-schedule) wurde aktualisiert, um diese Funktion ins rechte Licht zu rücken.

+ **Fehlerbehebungen und Verbesserungen**
  + Wir haben unterstützende Azure Machine Learning Pipelines zum Festlegen der source_directory_data_store-Eigenschaft auf einen gewünschten Datenspeicher (wie etwa einen Blob-Speicher) auf [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) hinzugefügt, die im [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) verfügbar sind. Standardmäßig wird in den Schritten der Azure File-Speicher als unterstützender Datenspeicher verwendet, bei dem es zu Drosselungsproblemen kommen kann, wenn eine große Anzahl von Schritten parallel abgearbeitet wird.

### <a name="azure-portal"></a>Azure-Portal

+ **Neue Features**
  + Neue Benutzeroberfläche mit Drag & Drop im Tabellen-Editor für Berichte. Benutzer können eine Spalte aus der Quelle auf den Tabellenbereich ziehen, wo eine Vorschau der Tabelle angezeigt wird. Die Spalten können neu angeordnet werden.
  + Neue Protokolldateianzeige
  + Links zu Experimentausführungen, Compute, Modellen, Images und Bereitstellungen auf der Registerkarte „Aktivitäten“

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning Data Prep SDK v1.0.15

+ **Neue Features**
  + Data Prep unterstützt jetzt das Schreiben von Datenströmen aus einem Datenfluss. Ferner besteht jetzt die Möglichkeit, die Namen von Dateiströmen zu ändern, um neue Dateinamen zu erstellen.
    + Schrittanleitung: [Arbeiten mit dem Dateistrom-Notizbuch](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Fehlerbehebungen und Verbesserungen**
  + Verbesserte Leistung von t-Digest bei großen Datasets.
  + Data Prep unterstützt jetzt das Lesen von Daten aus einem Datenpfad.
  + 1-aus-n-Codierung funktioniert jetzt in Booleschen und numerischen Spalten.
  + Verschiedene weitere Fehlerbehebungen

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK für Python v1.0.15

+ **Neue Features**
  + Für Azure Machine Learning-Pipelines wurden „AzureBatchStep“ ([Notebook](https://aka.ms/pl-azbatch)), „HyperDriveStep“ (Notebook) und Zeitplanungsfunktionalität ([Notebook](https://aka.ms/pl-schedule)) hinzugefügt.
  +  DataTranferStep wurde aktualisiert und funktioniert nun mit Azure SQL Server und Azure Database for PostgreSQL ([Notebook](https://aka.ms/pl-data-trans)).

+ **Änderungen**
  + `PublishedPipeline.get_published_pipeline` wurde durch `PublishedPipeline.get` ersetzt.
  + `Schedule.get_schedule` wurde durch `Schedule.get` ersetzt.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **Neue Features**
  + Data Prep unterstützt jetzt das Lesen aus einer Azure SQL-Datenbank mithilfe von Datenspeicher.
 
+ **Änderungen**
  + Die Leistung des Arbeitsspeichers für bestimmte Vorgänge mit großen Datenmengen wurde verbessert.
  + `temp_folder` erfordert nun, dass `read_pandas_dataframe()` angegeben ist.
  + Die `name`-Eigenschaft von `ColumnProfile` ist veraltet: Verwenden Sie stattdessen `column_name`.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK für Python v1.0.10

+ **Änderungen**: 
  + Das Azure ML SDK besitzt keine „azure-cli“-Pakete mehr als Abhängigkeit. Insbesondere wurden die Abhängigkeiten „azure-cli-core“ und „azure-cli-profile“ aus „azureml-core“ entfernt. Dies sind die Änderungen mit Auswirkungen auf Benutzer:
    + Wenn Sie „az login“ ausführen und dann „azureml-sdk“ verwenden, führt das SDK die Browser- oder Geräteanmeldung ein weiteres Mal durch. Es verwendet keinen von „az login“ erstellten Anmeldeinformationsstatus.
    + Verwenden Sie für die Azure CLI-Authentifizierung, z. B. wie bei der Verwendung von "az login", die _azureml.core.authentication.AzureCliAuthentication_-Klasse. Führen Sie für die Azure CLI-Authentifizierung _pip install azure-cli_ in der Python-Umgebung durch, in der Sie „azureml-sdk“ installiert haben.
    + Wenn Sie „az login“ unter Verwendung eines Dienstprinzipals für die Automatisierung durchführen, empfehlen wir, dass Sie die _azureml.core.authentication.ServicePrincipalAuthentication_-Klasse verwenden, weil „azureml-sdk“ keinen von der Azure CLI erstellen Anmeldeinformationszustand verwendet. 

+ **Programmfehlerbehebungen**: Dieses Release enthält hauptsächlich kleinere Programmfehlerbehebungen.

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **Fehlerbehebungen**
  + Die Leistung beim Abrufen von Datenprofilen wurde verbessert.
  + Kleinere Fehler im Zusammenhang mit der Fehlerberichterstattung wurden behoben.
  
### <a name="azure-portal-new-features"></a>Azure-Portal: Neue Features
+ Neue Drag & Drop-Diagrammerstellungserfahrung für Berichte. Benutzer können eine Spalte oder ein Attribut aus dem Bereich in den Diagrammbereich ziehen, wo das System automatisch einen geeigneten Diagrammtyp für den Benutzer auswählt, basierend auf dem Typ der Daten. Benutzer können den Diagrammtyp in andere anwendbare Typen zu ändern oder zusätzliche Attribute hinzufügen.

    Unterstützte Diagrammtypen:
    - Liniendiagramm
    - Histogramm
    - Gestapeltes Balkendiagramm
    - Boxplot
    - Punktdiagramm
    - Blasendiagramm
+ Das Portal generiert Berichte für Experimente nun dynamisch. Wenn ein Benutzer eine Ausführung an ein Experiment übermittelt, wird automatisch ein Bericht mit protokollierten Metriken und Diagrammen erstellt, um einen Vergleich zwischen verschiedenen Ausführungen zu ermöglichen. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK für Python v1.0.8

+ **Programmfehlerbehebungen**: Dieses Release enthält hauptsächlich kleinere Programmfehlerbehebungen.

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **Neue Features**
  + Datenspeicherverbesserungen (dokumentiert in der [Datenspeicher-Anleitung](https://aka.ms/aml-data-prep-datastore-nb))
    + Möglichkeit zum Auslesen von und Schreiben in eine Azure-Dateifreigabe sowie in ADLS-Datenspeicher beim Hochskalieren hinzugefügt.
    + Bei der Verwendung von Datenspeichern unterstützt die Datenvorbereitung jetzt die Verwendung von Dienstprinzipalauthentifizierung anstelle von interaktiver Authentifizierung.
    + Unterstützung für wasb- und wasbs-URLs hinzugefügt.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK v1.0.6

+ **Fehlerbehebungen**
  + Korrektur eines Fehlers beim Lesen von öffentlichen lesbaren Azure-Blobcontainern in Spark

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK für Python v1.0.6
+ **Programmfehlerbehebungen**: Dieses Release enthält hauptsächlich kleinere Programmfehlerbehebungen.

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **Neue Features**
  + Die `to_bool`-Funktion ermöglicht nun die Konvertierung nicht übereinstimmender Werte in Fehlerwerte. Dies ist das neue Standardverhalten bei Nichtübereinstimmung für `to_bool` und `set_column_types`, während das bisherige Standardverhalten darin bestand, nicht übereinstimmende Werte in FALSE zu konvertieren.
  + Beim Aufruf von `to_pandas_dataframe` gibt es eine neue Option, um NULL- bzw. fehlende Werte in numerischen Spalten als NaN zu interpretieren.
  + Die Möglichkeit, den Rückgabetyp einiger Ausdrücke zu überprüfen, um die Typkonsistenz zu gewährleisten und frühzeitig fehlzuschlagen, wurde hinzugefügt.
  + Sie können nun `parse_json` aufrufen, um Werte in einer Spalte als JSON-Objekte zu analysieren und in mehreren Spalten zu erweitern.

+ **Fehlerbehebungen**
  + Programmfehler korrigiert, der in Python 3.5.2 für den Absturz von `set_column_types` sorgte.
  + Programmfehler korrigiert, der beim Herstellen einer Verbindung mit dem Datenspeicher über ein AML-Bild einen Absturz verursachte.

+ **Updates**
  * [Beispiel-Notebooks](https://aka.ms/aml-data-prep-notebooks) mit Tutorials mit den ersten Schritten, Fallstudien und Leitfäden.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Allgemeine Verfügbarkeit

Der Azure Machine Learning Service ist jetzt allgemein verfügbar.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
Mit diesem Release kündigen wir eine neue verwaltete Compute-Umgebung über [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) an. Dieses Computeziel ersetzt das Azure Batch KI-Computeziel für Azure Machine Learning. 

Dieses Computeziel:
+ Wird für das Modelltraining und Batchrückschlüsse/-bewertungen verwendet
+ Ist ein Computeziel mit einem oder mehreren Knoten
+ Übernimmt die Clusterverwaltung und Auftragsplanung für den Benutzer
+ Wird standardmäßig automatische skaliert
+ Unterstützt CPU- und GPU-Ressourcen 
+ Ermöglicht zur Kostensenkung die Nutzung von VMs mit niedriger Priorität

Azure Machine Learning Compute kann unter Python, mit dem Azure-Portal oder mit der CLI erstellt werden. Die Erstellung muss in der Region Ihres Arbeitsbereichs erfolgen, und ein Anfügen an einen anderen Arbeitsbereich ist nicht möglich. Bei diesem Computeziel wird für Ihre Ausführung ein Docker-Container genutzt, und Ihre Abhängigkeiten werden gepackt, um die gleiche Umgebung auf allen Ihren Knoten zu replizieren.

> [!Warning]
> Es wird empfohlen, einen neuen Arbeitsbereich für die Nutzung von Azure Machine Learning Compute zu erstellen. Es besteht eine geringe Wahrscheinlichkeit, dass Benutzern, die Azure Machine Learning Compute aus einem vorhandenen Arbeitsbereich erstellen möchten, ein Fehler angezeigt wird. Ein vorhandener Computevorgang in Ihrem Arbeitsbereich sollte ohne Beeinträchtigungen weiterhin funktionieren.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK für Python v1.0.2
+ **Wichtige Änderungen**
  + Mit diesem Release entfällt die Unterstützung für die Erstellung einer VM über Azure Machine Learning. Sie können aber weiterhin eine vorhandene Cloud-VM oder einen entfernten lokalen Server anfügen. 
  + Darüber hinaus entfällt die Unterstützung für Batch AI, da alle Funktionen jetzt per Azure Machine Learning Compute unterstützt werden sollten.

+ **Neu**
  + Für Machine Learning-Pipelines:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Aktualisiert**
  + Für Machine Learning-Pipelines:
    + Für [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) wird jetzt „runconfig“ akzeptiert.
    + Für [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) kann jetzt in eine und aus einer SQL-Datenquelle kopiert werden.
    + Planen von Funktionalität im SDK zum Erstellen und Aktualisieren von Zeitplänen für die Ausführung von veröffentlichten Pipelines

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **Wichtige Änderungen** 
  * `SummaryFunction.N` wurde in `SummaryFunction.Count` umbenannt.
  
+ **Fehlerbehebungen**
  * Verwenden Sie das aktuelle AML-Ausführungstoken, wenn Sie bei Remoteausführungen aus Datenspeichern lesen oder in Datenspeicher schreiben. Wenn das AML-Ausführungstoken zuvor in Python aktualisiert wird, wird die Runtime für die Datenaufbereitung nicht mit dem aktualisierten AML-Ausführungstoken aktualisiert.
  * Zusätzliche verständlichere Fehlermeldungen
  * „to_spark_dataframe()“ stürzt nicht mehr ab, wenn für Spark die `Kryo`-Serialisierung verwendet wird.
  * Für den Inspektor für die Anzahl von Werten können jetzt mehr als 1.000 eindeutige Werte angezeigt werden.
  * Für die zufällige Aufteilung tritt kein Fehler mehr auf, wenn der ursprüngliche Dataflow keinen Namen hat.  

+ **Weitere Informationen**
  * [Azure Machine Learning – Datenaufbereitungs-SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumentation und Notebooks
+ ML-Pipelines
  + Neue und aktualisierte Notebooks als Einstieg in Pipelines, Batchbereichsdefinition und Beispiele für die Stilübertragung: https://aka.ms/aml-pipeline-notebooks.
  + Erfahren Sie, wie Sie [Ihre erste Pipeline erstellen](how-to-create-your-first-pipeline.md).
  + Erfahren Sie, wie Sie [Batchvorhersagen mit Pipelines ausführen](how-to-run-batch-predictions.md).
+ Azure Machine Learning Compute-Ziel
  + [Beispielnotebooks](https://aka.ms/aml-notebooks) sind jetzt für die Verwendung des neuen verwalteten Computeziels aktualisiert.
  + [Informationen zu dieser Computeumgebung](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure-Portal: Neue Features
+ Erstellen und Verwalten von [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute)-Typen im Portal
+ Überwachen der Kontingentnutzung und des [Anforderungskontingents](how-to-manage-quotas.md) für Azure Machine Learning Compute
+ Anzeigen des Status für den Azure Machine Learning Compute-Cluster in Echtzeit
+ Nutzen der hinzugefügten Unterstützung für virtuelle Netzwerke für Azure Machine Learning Compute und die Azure Kubernetes Service-Erstellung
+ Erneutes Ausführen Ihrer veröffentlichten Pipelines mit vorhandenen Parametern
+ Neue [automatisierte Machine Learning-Diagramme](how-to-track-experiments.md#auto) für Klassifizierungsmodelle (Prognosegüte-, Gewinn-, Kalibrierungs-, Featurewichtigkeitsdiagramm mit Modellerklärung) und Regressionsmodelle (Restdaten- und Featurewichtigkeitsdiagramm mit Modellerklärung) 
+ Pipelines können im Azure-Portal angezeigt werden




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK für Python v0.1.80

+ **Wichtige Änderungen** 
  * Der Namespace *azureml.train.widgets* wurde in *azureml.widgets* verschoben.
  * *azureml.core.compute.AmlCompute* führt zur Veraltung der folgenden Klassen: *azureml.core.compute.BatchAICompute* und *azureml.core.compute.DSVMCompute*. Die letztgenannte Klasse wird in künftigen Versionen nicht mehr enthalten sein. Die AmlCompute-Klasse verfügt jetzt über eine einfachere Definition und benötigt lediglich Angaben für „vm_size“ und „max_nodes“. Sie skaliert Ihren Cluster automatisch von 0 auf den Wert von „max_nodes“, wenn ein Auftrag übermittelt wird. Unsere [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) wurden mit diesen Informationen aktualisiert und enthalten Beispiele zur Verwendung. Wir hoffen, dass Ihnen diese Vereinfachung gefällt – genauso wie viele weitere interessante Features in den nachfolgenden Releases!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1 

Erfahren Sie mehr zum Data Prep SDK, indem Sie die [Referenzdokumente](https://aka.ms/data-prep-sdk) lesen.
+ **Neue Features**
   * Es wurde eine neue DataPrep CLI erstellt, mit der DataPrep-Pakete ausgeführt werden können und das Datenprofil für ein Dataset oder einen Dataflow angezeigt werden kann.
   * Die SetColumnType-API wurde neu gestaltet, um die Nutzbarkeit zu verbessern.
   * „smart_read_file“ wurde in „auto_read_file“ umbenannt.
   * Datenschiefe und Kurtosis sind jetzt im Datenprofil enthalten.
   * Stichprobenentnahme mit geschichteten Stichproben ist möglich.
   * Das Lesen aus ZIP-Dateien, die CSV-Dateien enthalten, ist möglich.
   * Datasets können zeilenweise per zufälliger Aufteilung geteilt werden (z. B. in Test-/Trainingssätze).
   * Alle Spaltendatentypen können aus einem Dataflow oder Datenprofil abgerufen werden, indem `.dtypes` aufgerufen wird.
   * Die Zeilenanzahl kann aus einem Dataflow oder Datenprofil abgerufen werden, indem `.row_count` aufgerufen wird.

+ **Fehlerbehebungen**
   * Fehler bei Konvertierung von „long“ in „double“ behoben 
   * Fehler bei Assert-Vorgang nach jedem Hinzufügen einer Spalte behoben 
   * FuzzyGrouping-Fehler behoben, bei dem in einigen Fällen Gruppen nicht erkannt wurden
   * Fehler bei Sortierfunktion behoben, damit die Sortierreihenfolge für mehrere Spalten beibehalten wird
   * Fehler mit Ausdrücken behoben bzw. an Verarbeitung durch `pandas` angepasst
   * Fehler beim Lesen aus dem dbfs-Pfad behoben
   * Verständlichkeit von Fehlermeldungen verbessert 
   * Fehler behoben, der beim Lesen auf dem Remotecomputeziel mit dem AML-Token aufgetreten ist
   * Fehler für Linux DSVM behoben
   * Kein Absturz mehr, wenn in Zeichenfolgenprädikaten andere Werte als Zeichenfolgen enthalten sind
   * Assertionsfehler werden bei Dataflow-Fehler jetzt richtig behandelt
   * Per dbutils bereitgestellte Speicherorte werden unter Azure Databricks jetzt unterstützt

## <a name="2018-11-05"></a>05\.11.2018

### <a name="azure-portal"></a>Azure-Portal 
Im Azure-Portal für den Azure Machine Learning-Dienst wurden folgende Updates vorgenommen:
  * Neue Registerkarte namens **Pipelines** für veröffentlichte Pipelines
  * Unterstützung für das Anfügen eines vorhandenen HDInsight-Clusters als Computeziel hinzugefügt

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK für Python v0.1.74

+ **Wichtige Änderungen** 
  * *Workspace.compute_targets, datastores, experiments, images, models und *webservices* sind Eigenschaften, keine Methoden. Ersetzen Sie beispielsweise *Workspace.compute_targets()* durch *Workspace.compute_targets*.
  * *Run.get_context* ersetzt *Run.get_submitted_run*. Die letztgenannte Methode wird in künftigen Versionen entfernt.
  * Die Klasse *PipelineData* erwartet nun anstelle von „datastore_name“ ein datastore-Objekt als Parameter. Analog dazu akzeptiert *Pipeline* nun „default_datastore“ anstelle von „default_datastore_name“.

+ **Neue Features**
  * Das [Beispielnotebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) für Azure Machine Learning-Pipelines verwendet jetzt MPI-Schritte.
  * Das RunDetails-Widget für Jupyter-Notebooks wird aktualisiert, um eine Visualisierung der Pipeline anzuzeigen.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0 
 
+ **Neue Features**
  * Typanzahl zum Datenprofil hinzugefügt 
  * Wertanzahl und Histogramm nun verfügbar
  * Mehr Perzentile im Datenprofil
  * Median in der Zusammenfassung verfügbar
  * Python 3.7 nun unterstützt
  * Wenn Sie einen Dataflow mit Datenspeichern in einem Datenvorbereitungspaket speichern, werden die Datenspeicherinformationen als Teil des Datenvorbereitungspakets gespeichert.
  * Schreibvorgänge für den Datenspeicher nun unterstützt 
        
+ **Behobene Fehler**
  * 64-Bit-Ganzzahlüberläufe ohne Vorzeichen werden nun unter Linux ordnungsgemäß behandelt.
  * Falscher Text für Nur-Text-Dateien in „smart_read“ behoben
  * Zeichenfolgenspaltentyp wird nun in der Metrikansicht angezeigt
  * Typanzahl korrigiert, sodass nun Wertarten (ValueKinds) angezeigt werden, die einem einzelnen Feldtyp (FieldType) zugeordnet sind (anstatt einzelne)
  * Bei „Write_to_csv“ tritt kein Fehler mehr auf, wenn der Pfad als Zeichenfolge angegeben wird.
  * Wenn bei Verwendung von „Ersetzen“ das Suchfeld leer ist, tritt kein Fehler mehr auf. 

## <a name="2018-10-12"></a>12\.10.2018

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK für Python v0.1.68

+ **Neue Features**
  * Unterstützung für mehrere Mandanten beim Erstellen eines neuen Arbeitsbereichs.

+ **Behobene Fehler**
  * Sie müssen die Version der pynacl-Bibliothek beim Bereitstellen des Webdiensts nicht mehr anheften.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning – Datenaufbereitungs-SDK v0.3.0

+ **Neue Features**
  * Methode transform_partition_with_file(script_path) hinzugefügt, die es Benutzern ermöglicht, den Pfad einer auszuführenden Python-Datei zu übergeben

## <a name="2018-10-01"></a>01\.10.2018

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK für Python v0.1.65
[Version 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) enthält neue Features, mehr Dokumentation, Fehlerbehebungen und weitere [Beispielnotebooks](https://aka.ms/aml-notebooks).

Sehen Sie die [Liste der bekannten Probleme](resource-known-issues.md) an, um mehr über bekannte Fehler und Problemumgehungen zu erfahren.

+ **Wichtige Änderungen**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services geben ein Wörterbuch zurück, bisher wurde eine Liste zurückgegeben. Weitere Informationen finden Sie in der [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py)-API-Dokumentation.

  * Der Normalized-Mean-Square-Fehler in den primären Metriken wurde mit automatisiertem maschinellem Lernen entfernt.

+ **HyperDrive**
  * Verschiedene HyperDrive-Fehlerbehebungen für Bayessche Wahrscheinlichkeit, verbesserte Leistung für Abrufaufrufe der Metriken. 
  * Tensorflow 1.10-Upgrade von 1.9 
  * Docker-Image-Optimierung für den Kaltstart. 
  * Aufträge melden jetzt den richtigen Status, selbst wenn sie mit einem anderen Fehlercode als 0 beendet werden. 
  * Überprüfung des RunConfig-Attributs in SDK. 
  * Das HyperDrive-Ausführungsobjekt unterstützt jetzt Abbrechen, ähnlich einer normalen Ausführung: Das Übergeben von Parametern ist nicht erforderlich. 
  * Verbesserte Widgets, um den Status von Dropdownwerten für verteilte Ausführungen und HyperDrive-Ausführungen beizubehalten. 
  * TensorBoard und andere Protokolldateien unterstützen „Fixed“ für den Parameter „Server“. 
  * Dienstseitige Intel(R) MPI-Unterstützung. 
  * Fehlerbehebung bei der Parameteroptimierung für verteilte Ausführungskorrekturen während der Überprüfung in BatchAI. 
  * Der Kontext-Manager identifiziert jetzt die primäre Instanz. 

+ **Azure-Portal-Benutzeroberfläche**
  * log_table() und log_row() werden in den Ausführungsdetails unterstützt. 
  * Erstellen Sie automatisch Diagramme für Tabellen und Zeilen mit 1, 2 oder 3 numerischen Spalten und einer optionalen Kategoriespalte.

+ **Automatisiertes maschinelles Lernen**
  * Verbesserte Fehlerbehandlung und Dokumentation 
  * Behobene Leistungsprobleme beim Abrufen der Ausführungseigenschaft. 
  * Behobenes Problem beim Fortsetzen der Ausführung. 
  * Behobene Iterationsprobleme.
  * Behobener Fehler wegen fehlender Reaktion beim Training unter MAC OS.
  * Downsampling der durchschnittlichen PR/ROC-Kurve im benutzerdefinierten Überprüfungsszenario.
  * Zusätzliche Indexlogik wurde entfernt.
  * Filter aus der get_output-API entfernt.

+ **Pipelines**
  * Hinzugefügte Methode Pipeline.publish() zum direkten Veröffentlichen von Pipelines ohne vorhergehende Ausführung.   
  * Hinzugefügte Methode PipelineRun.get_pipeline_runs() zum Abfangen der Pipelineausführungen, die von einer veröffentlichten Pipeline generiert wurden.

+ **Project Brainwave**
  * Aktualisierte Unterstützung für neue KI-Modelle auf FPGAs verfügbar.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning – Datenaufbereitungs-SDK v0.2.0
[Version 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) umfasst folgende Features und Programmfehlerbehebungen:

+ **Neue Features**
  * Unterstützung für 1-aus-n-Codierung
  * Unterstützung für Quantiltransformation
   
+ **Behobener Fehler:**
  * Funktioniert mit jeder Tornado-Version, ein Downgrade Ihrer Tornado-Version ist nicht erforderlich
  * Der Wert gilt für alle Werte, nicht nur für die höchsten drei

## <a name="2018-09-public-preview-refresh"></a>09\.2018 (Aktualisierung der öffentlichen Vorschau)

Neues, aktualisiertes Release von Azure Machine Learning: Weitere Informationen zu diesem Release: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Übersicht zum [Azure Machine Learning Service](../service/overview-what-is-azure-ml.md).
