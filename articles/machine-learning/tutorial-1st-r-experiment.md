---
title: 'Tutorial: Verwenden von R zum Erstellen eines Machine Learning-Modells (Vorschau)'
titleSuffix: Azure Machine Learning
description: In diesem Tutorial verwenden Sie das Azure Machine Learning R SDK, um ein logistisches Regressionsmodell zu erstellen, mit dem die Wahrscheinlichkeit einer tödlichen Verletzung bei einem Autounfall vorhergesagt wird.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 0e94288b49cd57b59c126c95ca507477f1c56946
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321528"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model-preview"></a>Tutorial: Verwenden von R zum Erstellen eines Machine Learning-Modells (Vorschau)


> [!IMPORTANT]
> Das Azure Machine Learning R SDK ist derzeit als Public Preview verfügbar.
> Die Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Tutorial verwenden Sie das Azure Machine Learning R SDK (Vorschau), um ein logistisches Regressionsmodell zu erstellen, mit dem die Wahrscheinlichkeit einer tödlichen Verletzung bei einem Autounfall vorhergesagt wird. Es wird gezeigt, wie die Azure Machine Learning-Cloudressourcen mit R verwendet werden, um eine skalierbare Umgebung für das Trainieren und Bereitstellen eines Modells zu erhalten.  

In diesem Tutorial führen Sie die folgenden Aufgaben aus:
> [!div class="checklist"]
> * Erstellen eines Azure Machine Learning-Arbeitsbereichs
> * Öffnen von RStudio über Ihren Arbeitsbereich
> * Klonen der Dateien von https://github.com/Azure/azureml-sdk-for-r , die zum Ausführen dieses Tutorials in Ihrem Arbeitsbereich erforderlich sind
> * Laden von Daten und Vorbereiten des Trainings
> * Hochladen von Daten in einen Datenspeicher, damit diese für das Remotetraining zur Verfügung stehen
> * Erstellen einer für das Remotetraining des Modells verwendeten Computeressource
> * Trainieren eines `caret`-Modells zum Vorhersagen der Wahrscheinlichkeit eines Todesfalls
> * Bereitstellen eines Vorhersageendpunkts
> * Testen des Modells über R

Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.


## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Ein Azure Machine Learning-Arbeitsbereich ist eine grundlegende Cloudressource zum Experimentieren, Trainieren und Bereitstellen von Machine Learning-Modellen. Er verknüpft Ihr Azure-Abonnement und Ihre Ressourcengruppe mit einem einfach nutzbaren Objekt im Dienst. 

Es gibt viele [Möglichkeiten, einen Arbeitsbereich zu erstellen](how-to-manage-workspace.md). In diesem Tutorial erstellen Sie einen Arbeitsbereich über das Azure-Portal, einer webbasierten Konsole zum Verwalten Ihrer Azure-Ressourcen. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Notieren Sie sich Ihren **Arbeitsbereich** und Ihr **Abonnement**. Sie benötigen diese Informationen, um sicherzustellen, dass Sie Ihr Experiment an der richtigen Stelle erstellen. 


## <a name="open-rstudio"></a><a name="open"></a>Öffnen von RStudio

In diesem Beispiel wird eine Compute-Instanz in Ihrem Arbeitsbereich für eine vorkonfigurierte Umgebung ohne Installationsaufwand verwendet. Verwenden Sie [Ihre eigene Umgebung](https://azure.github.io/azureml-sdk-for-r/articles/installation.html), wenn Sie Ihre Umgebung, Pakete und Abhängigkeiten auf Ihrem eigenen Computer lieber selbst gestalten möchten.

Verwenden Sie RStudio in einer Azure ML-Compute-Instanz für dieses Tutorial.  

1. Wählen Sie links **Compute** aus.

1. Fügen Sie eine Computeressource hinzu, sofern noch keine vorhanden ist.

1. Wenn die Computeressource ausgeführt wird, verwenden Sie zum Öffnen von RStudio den Link **RStudio**.


## <a name="clone-the-sample-vignettes"></a><a name="azure"></a>Klonen der Beispielvignetten 

Klonen Sie das https://github.com/Azure/azureml-sdk-for-r -GitHub-Repository, um eine Kopie der Vignettendateien zu erhalten, die Sie in diesem Tutorial ausführen werden.

1. Navigieren Sie in RStudio zur Registerkarte „Terminal“ und dann zu dem Verzeichnis, in dem Sie das Repository klonen möchten.

1. Führen Sie `git clone https://github.com/Azure/azureml-sdk-for-r` im Terminal aus, um das Repository zu klonen.

1. Navigieren Sie in RStudio zum Ordner *Vignettes* (Vignetten) des geklonten *azureml-sdk-for-r* -Ordners.  Wählen Sie unter *vignettes* (Vignetten) die Datei *train-and-deploy-first-model.Rmd* aus, um die in diesem Tutorial verwendete Vignette zu suchen. Die zusätzlichen Dateien, die für die Vignette verwendet werden, befinden sich im Unterordner *train-and-deploy-first-model*. Nachdem Sie die Vignette geöffnet haben, legen Sie den Speicherort des Arbeitsverzeichnisses auf **Session > Set Working Directory > To Source File Location** (Sitzung > Arbeitsverzeichnis festlegen > Auf Quelldatei-Speicherort). 

> [!Important]
> Der restliche Inhalt dieses Artikels entspricht dem Inhalt der Datei *train-and-deploy-first-model.Rmd*. Falls Sie mit RMarkdown vertraut sind, können Sie den Code aus dieser Datei verwenden.  Alternativ können Sie die Codeausschnitte aus der Datei oder diesem Artikel kopieren und in ein R-Skript oder die Befehlszeile einfügen. 


## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung
Das Setup für Ihre Entwicklungsarbeit in diesem Tutorial umfasst die folgenden Aktionen:

* Installieren erforderlicher Pakete
* Herstellen einer Verbindung mit einem Arbeitsbereich, damit Ihre Compute-Instanz mit Remoteressourcen kommunizieren kann
* Erstellen eines Experiments zum Nachverfolgen Ihrer Ausführungen
* Erstellen eines Remotecomputeziels für das Training

### <a name="install-required-packages"></a>Installieren erforderlicher Pakete
In der Compute-Instanz ist bereits die aktuelle Version des R SDK von CRAN installiert. Wenn Sie stattdessen die Entwicklungsversion von GitHub installieren möchten, um die neuesten Fehlerbehebungen zu erhalten, führen Sie Folgendes aus:
    
```R
remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
azuremlsdk::install_azureml()
```

> [!WARNING]
> Wenn während des Installationsvorgangs die Eingabeaufforderung `Would you like to install Miniconda? [Y/n]:` angezeigt wird, antworten Sie mit `n`, da Anaconda in der Compute-Instanz bereits installiert ist und eine Miniconda-Installation nicht erforderlich ist.

Importieren Sie jetzt das Paket **azuremlsdk**.

```R
library(azuremlsdk)
```

Die Trainings- und Bewertungsskripts (`accidents.R` und `accident_predict.R`) verfügen über einige zusätzliche Abhängigkeiten. Wenn Sie die lokale Ausführung dieser Skripts planen, sollten Sie sicherstellen, dass Sie auch über diese erforderlichen Pakete verfügen.

### <a name="load-your-workspace"></a>Laden Ihres Arbeitsbereichs
Instanziieren Sie ein Arbeitsbereichsobjekt aus Ihrem vorhandenen Arbeitsbereich. Mit dem folgenden Code werden die Arbeitsbereichsdetails aus der Datei **config.json** geladen. Sie können einen Arbeitsbereich auch mit [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html) abrufen.

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Erstellen eines Experiments
Mit einem Azure ML-Experiment wird eine Gruppierung von Ausführungen nachverfolgt. Hierfür wird normalerweise dasselbe Trainingsskript verwendet. Erstellen Sie ein Experiment, um die Ausführungen zum Trainieren des caret-Modells mit den Unfalldaten nachzuverfolgen.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Erstellen eines Computeziels
Unter Verwendung von Azure Machine Learning Compute (AmlCompute), einem verwalteten Dienst, können Datenanalysten Machine Learning-Modelle in Clustern von virtuellen Azure-Computern trainieren. Beispiele hierfür sind unter anderem VMs mit GPU-Unterstützung. In diesem Tutorial erstellen Sie den Cluster „AmlCompute“ mit einem einzelnen Knoten als Trainingsumgebung. Mit dem folgenden Code wird der Computecluster für Sie erstellt, sofern er in Ihrem Arbeitsbereich noch nicht vorhanden ist.

Unter Umständen müssen Sie einige Minuten warten, bis der Computecluster bereitgestellt wurde, sofern dies noch nicht erfolgt ist.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>Aufbereiten von Daten für das Training
In diesem Tutorial werden Daten der US-amerikanischen [National Highway Traffic Safety Administration](https://cdan.nhtsa.gov/tsftables/tsfar.htm) verwendet (danke an [Mary C. Meyer und Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm)).
Dieses Dataset enthält Daten von mehr als 25.000 Autounfällen in den USA. Darin sind Variablen enthalten, die Sie zum Vorhersagen der Wahrscheinlichkeit eines Todesfalls verwenden können. Importieren Sie die Daten zuerst in R, und transformieren Sie sie für die Analyse in den neuen Datenrahmen `accidents`. Exportieren Sie ihn anschließend in eine `Rdata`-Datei.

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Hochladen von Daten in den Datenspeicher
Laden Sie Daten in die Cloud hoch, damit über Ihre Umgebung für das Remotetraining darauf zugegriffen werden kann. Jeder Azure Machine Learning-Arbeitsbereich verfügt über einen Standarddatenspeicher, in dem die Verbindungsinformationen in dem Azure-Blobcontainer gespeichert werden, der unter dem an den Arbeitsbereich angefügten Speicherkonto bereitgestellt wird. Mit dem folgenden Code werden die oben erstellten Unfalldaten in diesen Datenspeicher hochgeladen.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Trainieren eines Modells

Passen Sie für dieses Tutorial ein logistisches Regressionsmodell an Ihre hochgeladenen Daten an, indem Sie Ihren Remotecomputecluster verwenden. Zum Übermitteln eines Auftrags ist Folgendes erforderlich:

* Vorbereiten des Trainingsskripts
* Erstellen eines Estimators
* Übermitteln des Auftrags

### <a name="prepare-the-training-script"></a>Vorbereiten des Trainingsskripts
Ein Trainingsskript mit dem Namen `accidents.R` wurde für Sie im *train-and-deploy-first-model* -Verzeichnis bereitgestellt. Beachten Sie die folgenden Details **im Trainingsskript** , die angegeben wurden, um den Azure Machine Learning-Dienst für das Training zu nutzen:

* Für das Trainingsskript wird das Argument `-d` genutzt, um das Verzeichnis mit den Trainingsdaten zu ermitteln. Wenn Sie Ihren Auftrag definieren und später dann senden, verweisen Sie auf den Datenspeicher für dieses Argument. Azure ML stellt den Speicherordner im Remotecluster für den Trainingsauftrag bereit.
* Mit dem Trainingsskript wird die endgültige Genauigkeit als Metrik für die Ausführungsaufzeichnung in Azure ML mit `log_metric_to_run()` protokolliert. Das Azure ML SDK verfügt über Protokollierungs-APIs zum Protokollieren verschiedener Metriken bei Trainingsausführungen. Diese Metriken werden aufgezeichnet und in der Ausführungsaufzeichnung des Experiments dauerhaft gespeichert. Auf die Metriken kann dann jederzeit zugegriffen werden, oder sie können in [Studio](https://ml.azure.com) auf der Seite mit den Ausführungsdetails angezeigt werden. In der [Referenz](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) finden Sie alle Protokollierungsmethoden (`log_*()`).
* Das Trainingsskript speichert Ihr Modell in einem Verzeichnis namens **outputs**. Der Ordner `./outputs` erhält von Azure ML eine Sonderbehandlung. Während des Trainings werden in `./outputs` geschriebene Dateien von Azure ML automatisch in Ihre Ausführungsaufzeichnung hochgeladen und dauerhaft als Artefakte gespeichert. Indem Sie das trainierte Modell in `./outputs` speichern, können Sie auch nach Abschluss der Ausführung auf Ihr Modell zugreifen und es abrufen. Der Zugriff auf Ihre Umgebung für das Remotetraining ist dann nicht mehr möglich.

### <a name="create-an-estimator"></a>Erstellen eines Estimators

Mit einem Azure ML-Estimator werden die Informationen für die Ausführungskonfiguration gekapselt, die zum Ausführen eines Trainingsskripts auf dem Computeziel benötigt werden. Azure ML-Ausführungen werden als Containeraufträge auf dem angegebenen Computeziel ausgeführt. Standardmäßig enthält das für Ihren Trainingsauftrag erstellte Docker-Image R, das Azure ML SDK und eine Reihe von häufig verwendeten R-Paketen. Hier ist die vollständige Liste mit den Standardpaketen angegeben.

Definieren Sie Folgendes, um den Estimator zu erstellen:

* Das Verzeichnis mit Ihren Skripts, die für das Training benötigt werden (`source_directory`). Alle Dateien in diesem Verzeichnis werden zur Ausführung auf den bzw. die Clusterknoten hochgeladen. Das Verzeichnis muss Ihr Trainingsskript und alle zusätzlich erforderlichen Skripts enthalten.
* Das Trainingsskript, das ausgeführt wird (`entry_script`).
* Das Computeziel (`compute_target`), in diesem Fall der weiter oben erstellte Cluster „AmlCompute“.
* Die für das Trainingsskript erforderlichen Parameter (`script_params`). Azure ML führt Ihr Trainingsskript als Befehlszeilenskript mit `Rscript` aus. In diesem Tutorial geben Sie ein Argument für das Skript an. Dies ist der Bereitstellungspunkt für das Datenverzeichnis, auf den Sie mit `ds$path(target_path)` zugreifen können.
* Alle Umgebungsabhängigkeiten, die für das Training erforderlich sind. Das für das Training erstellte Docker-Standardimage enthält bereits die drei Pakete (`caret`, `e1071` und `optparse`), die im Trainingsskript benötigt werden.  Daher müssen Sie keine zusätzlichen Informationen angeben. Falls Sie nicht standardmäßig enthaltene R-Pakete verwenden, können Sie den Parameter `cran_packages` des Estimators nutzen, um weitere CRAN-Pakete hinzuzufügen. In der Referenz zu [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) sind alle konfigurierbaren Optionen angegeben.

```R
est <- estimator(source_directory = "train-and-deploy-first-model",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Übermitteln des Auftrags für den Remotecluster

Übermitteln Sie Ihren Auftrag abschließend für die Ausführung in Ihrem Cluster. Mit `submit_experiment()` wird ein Run-Objekt zurückgegeben, das Sie dann für die Kommunikation mit der Ausführung verwenden. Insgesamt dauert die erste Ausführung **ungefähr 10 Minuten**. Bei späteren Ausführungen wird aber dasselbe Docker-Image wiederverwendet, solange sich die Skriptabhängigkeiten nicht ändern.  In diesem Fall wird das Image zwischengespeichert, und der Container weist eine deutlich kürzere Startdauer auf.

```R
run <- submit_experiment(exp, est)
```

Sie können die Details der Ausführung im RStudio-Viewer anzeigen. Wenn Sie auf den angegebenen Link „Webansicht“ klicken, gelangen Sie zu Azure Machine Learning Studio. In dieser Anwendung können Sie die Ausführung auf der Benutzeroberfläche überwachen.

```R
view_run_details(run)
```

Das Modelltraining wird im Hintergrund durchgeführt. Warten Sie, bis das Training des Modells abgeschlossen ist, bevor Sie weiteren Code ausführen.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Sie – und Ihre Kolleginnen und Kollegen mit Zugriff auf den Arbeitsbereich – können mehrere Experimente parallel übermitteln, und Azure ML übernimmt das Planen der Aufgaben im Computecluster. Sie können den Cluster sogar so konfigurieren, dass automatisch auf mehrere Knoten hochskaliert und diese Skalierung dann rückgängig gemacht wird, wenn in der Warteschlange keine Computeaufgaben mehr vorhanden sind. Diese Konfiguration ist ein kostengünstiger Ansatz, um Teams die gemeinsame Nutzung von Computeressourcen zu ermöglichen.

## <a name="retrieve-training-results"></a>Abrufen der Trainingsergebnisse
Nachdem das Training für Ihr Modell abgeschlossen wurde, können Sie auf die Artefakte Ihres Auftrags zugreifen, die in der Ausführungsaufzeichnung gespeichert wurden, z. B. die protokollierten Metriken und das fertige trainierte Modell.

### <a name="get-the-logged-metrics"></a>Abrufen der protokollierten Metriken
Im Trainingsskript `accidents.R` haben Sie eine Metrik Ihres Modells protokolliert: die Genauigkeit der Vorhersagen in den Trainingsdaten. Sie können Metriken in [Studio](https://ml.azure.com) anzeigen oder wie folgt als R-Liste für die lokale Sitzung extrahieren:

```R
metrics <- get_run_metrics(run)
metrics
```

Wenn Sie mehrere Experimente ausgeführt haben (z. B. mit verschiedenen Variablen, Algorithmen oder Hyperparametern), können Sie die Metriken jeder Ausführung verwenden, um die Modelle zu vergleichen und das für die Produktion bestimmte Modell auszuwählen.

### <a name="get-the-trained-model"></a>Abrufen des trainierten Modells
Sie können das trainierte Modell abrufen und die Ergebnisse in Ihrer lokalen R-Sitzung anzeigen. Mit dem folgenden Code wird der Inhalt des Verzeichnisses `./outputs` heruntergeladen, in dem die Modelldatei enthalten ist.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Es sind einige Faktoren zu erkennen, die zu einer Erhöhung der geschätzten Wahrscheinlichkeit eines tödlichen Unfalls beitragen:

* Höhere Aufprallgeschwindigkeit 
* Männlicher Fahrer
* Älterer Fahrzeuginsasse
* Beifahrer

Für folgende Faktoren ist die Wahrscheinlichkeit eines tödlichen Unfalls geringer:

* Vorhandensein von Airbags
* Vorhandensein von Sicherheitsgurten
* Frontaler Aufprall 

Das Herstellungsjahr des Fahrzeugs hat keine größeren Auswirkungen.

Sie können dieses Modell verwenden, um neue Vorhersagen zu treffen:

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Bereitstellen als Webdienst

Mit Ihrem Modell können Sie die Gefahr einer tödlichen Verletzung aufgrund einer Kollision vorhersagen. Verwenden Sie Azure ML, um Ihr Modell als Vorhersagedienst bereitzustellen. In diesem Tutorial stellen Sie den Webdienst in [Azure Container Instances](../container-instances/index.yml) (ACI) bereit.

### <a name="register-the-model"></a>Registrieren des Modells

Registrieren Sie zuerst das Modell, das Sie in Ihren Arbeitsbereich heruntergeladen haben, mit [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html). Bei einem registrierten Modell kann es sich um eine beliebige Auflistung von Dateien handeln, aber in diesem Fall ist das R-Modellobjekt ausreichend. Azure ML verwendet das registrierte Modell für die Bereitstellung.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definieren der Rückschlussabhängigkeiten
Zum Erstellen eines Webdiensts für Ihr Modell müssen Sie zuerst ein Bewertungsskript (`entry_script`) erstellen. Hierbei handelt es sich um ein R-Skript, für das Variablenwerte (im JSON-Format) als Eingabe verwendet werden und eine Vorhersage für Ihr Modell ausgegeben wird. Verwenden Sie für dieses Tutorial die angegebene Bewertungsdatei `accident_predict.R`. Das Bewertungsskript muss eine `init()`-Methode enthalten, mit der Ihr Modell geladen und eine Funktion zurückgegeben wird, für die das Modell zum Treffen einer Vorhersage basierend auf den Eingabedaten verwendet wird. Weitere Informationen finden Sie in der [Dokumentation](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details).

Definieren Sie als Nächstes eine Azure ML- **Umgebung** für die Paketabhängigkeiten Ihres Skripts. Mit einer Umgebung geben Sie R-Pakete an (per CRAN oder auf andere Weise), die für die Ausführung Ihres Skripts benötigt werden. Sie können auch die Werte von Umgebungsvariablen angeben, auf die Ihr Skript verweisen kann, um sein Verhalten zu ändern. Standardmäßig wird von Azure ML das gleiche Docker-Standardimage erstellt, das mit dem Estimator für das Training verwendet wird. Erstellen Sie eine Umgebung ohne spezielle Attribute, da für das Tutorial keine besonderen Anforderungen gelten.

```R
r_env <- r_environment(name = "basic_env")
```

Geben Sie den Parameter `custom_docker_image` an, wenn Sie stattdessen Ihr eigenes Docker-Image für die Bereitstellung verwenden möchten. Alle konfigurierbaren Optionen zum Definieren einer Umgebung finden Sie in der Referenz zu [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html).

Nun ist alles bereit, um eine **Rückschlusskonfiguration** zum Einkapseln Ihres Bewertungsskripts und der Umgebungsabhängigkeiten zu erstellen.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  source_directory = "train-and-deploy-first-model",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Bereitstellen für ACI
In diesem Tutorial stellen Sie Ihren Dienst für ACI bereit. Mit diesem Code wird ein einzelner Container bereitgestellt, um auf eingehende Anforderungen zu reagieren. Dies ist für Tests und geringere Auslastungen geeignet. Weitere konfigurierbare Optionen finden Sie unter [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html). (Für Produktionsbereitstellungen können Sie auch eine [Bereitstellung für Azure Kubernetes Service](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks.html) durchführen.)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Sie stellen Ihr Modell jetzt als Webdienst bereit. Die Bereitstellung **kann mehrere Minuten dauern**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Testen des bereitgestellten Diensts

Nachdem Ihr Modell als Dienst bereitgestellt wurde, können Sie den Dienst mit R testen, indem Sie [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html) verwenden.  Stellen Sie einen neuen Satz mit Daten für die Vorhersage bereit, konvertieren Sie sie in das JSON-Format, und senden Sie sie an den Dienst.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

Sie können auch den HTTP-Endpunkt des Webdiensts abrufen, der REST-Clientaufrufe akzeptiert. Sie können diesen Endpunkt für jeden freigeben, der den Webdienst testen oder in eine Anwendung integrieren möchte.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcen, wenn Sie sie nicht mehr benötigen. Löschen Sie keine Ressourcen, die Sie noch verwenden möchten. 

Löschen des Webdiensts:
```R
delete_webservice(aci_service)
```

Löschen des registrierten Modells:
```R
delete_model(model)
```

Löschen des Computeclusters:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Alles löschen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Sie können die Ressourcengruppe auch behalten und einen einzelnen Arbeitsbereich löschen. Zeigen Sie die Eigenschaften des Arbeitsbereichs an, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

* Nachdem Sie nun Ihr erstes Azure Machine Learning-Experiment in R abgeschlossen haben, können Sie sich über das [Azure Machine Learning SDK für R](https://azure.github.io/azureml-sdk-for-r/index.html) informieren.

* Informieren Sie sich anhand der Beispiele in den anderen Ordnern vom Typ *Vignetten* ausführlicher über Azure Machine Learning mit R.