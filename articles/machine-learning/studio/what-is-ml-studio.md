---
title: Info
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio ist ein Drag & Drop-Tool zum schnellen Erstellen von Modellen aus einer verwendungsbereiten Bibliothek mit Algorithmen und Modulen.
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/20/2019
ms.openlocfilehash: dd1eaa95a23deed0bf2098995be43402c605defc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024223"
---
# <a name="what-is-azure-machine-learning-studio"></a>Was ist Azure Machine Learning Studio?
Microsoft Azure Machine Learning Studio ist ein Tool für die Zusammenarbeit per Drag & Drop, mit der Sie Lösungen für Vorhersageanalysen erstellen, testen und bereitstellen können, die mit Ihren Daten arbeiten. Machine Learning Studio veröffentlicht Modelle als Webdienste, die von benutzerdefinierten Apps oder BI-Tools wie Excel problemlos genutzt werden können.

In Machine Learning Studio finden Datenwissenschaften, prädiktive Analysen, Cloudressourcen und Ihre Daten zusammen!


## <a name="the-machine-learning-studio-interactive-workspace"></a>Der interaktive Machine Learning Studio-Arbeitsbereich
Für die Entwicklung eines prädiktiven Analysemodells verwendet man typischerweise Daten aus einer oder mehreren Quellen, transformiert und analysiert diese Daten mithilfe verschiedener Datenbearbeitungen und Statistikfunktionen und generiert einen Ergebnissatz. Das Entwickeln eines solchen Modells ist ein iterativer Prozess. Sie ändern die verschiedenen Funktionen und deren Parameter, und die Ergebnisse nähern sich an, bis Sie der Ansicht sind, dass Sie ein trainiertes, effektives Modell erreicht haben.

**Azure Machine Learning Studio** stellt Ihnen einen interaktiven, visuellen Arbeitsbereich zur Verfügung, in dem Sie ein Vorhersageanalysemodell ganz einfach entwickeln, testen und durchlaufen können. Sie fügen per Drag & Drop ***DataSets*** und ***Analysemodule*** in eine interaktive Canvas ein und verbinden sie zu einem ***Experiment***, das Sie in Machine Learning Studio ausführen. Für die Iteration des Modelldesigns bearbeiten Sie das Experiment, speichern ggf. eine Kopie und führen es erneut aus. Wenn Sie bereit sind, können Sie Ihr ***Trainingsexperiment*** in ein ***Vorhersageexperiment*** konvertieren und anschließend als ***Webdienst*** veröffentlichen, damit andere Benutzer auf das Modell zugreifen können.

Es ist keine Programmierung erforderlich, sondern einfach nur das visuelle Verbinden von Datasets und Modulen zum Erstellen eines prädiktiven Analysemodells.

![Azure Machine Learning Studio-Diagramm: Erstellen von Experimenten, Lesen von Daten aus vielen Quellen, Schreiben der ausgewerteten Daten, Erstellen von Modellen.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Herunterladen des Machine Learning Studio-Übersichtsdiagramms
Laden Sie das Diagramm **Übersicht der Microsoft Azure Machine Learning Studio-Funktionen** herunter, und verschaffen Sie sich einen allgemeinen Überblick über die Funktionen von Machine Learning Studio. Drucken Sie das Diagramm in Tabloid-Größe (28 x 43 cm, 11 x 17 Zoll) aus, um schnell darauf zugreifen zu können.

**Laden Sie das Diagramm hier herunter: [Übersicht der Microsoft Azure Machine Learning Studio-Funktionen](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Übersicht der Microsoft Azure Machine Learning Studio-Funktionen](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>Erste Schritte mit Machine Learning Studio
Wenn Sie Machine Learning Studio zum ersten Mal öffnen,](https://studio.azureml.net) sehen Sie die **Startseite**. Hier finden Sie die Dokumentation, Videos, Webinare und weitere wertvolle Ressourcen.

Klicken Sie auf das Menü links oben. ![Menü](./media/what-is-ml-studio/menu.png) Daraufhin werden mehrere Optionen angezeigt.
### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
Hier stehen zwei Optionen zur Verfügung: **Startseite** (die Seite, auf der Sie begonnen haben), und **Studio**.

Klicken Sie auf **Studio**, um zu **Azure Machine Learning Studio** zu gelangen. Sie werden zunächst aufgefordert, sich mit Ihrem Microsoft-Konto oder Ihrem Geschäfts-, Schul- oder Unikonto anzumelden. Nach der Anmeldung sehen Sie auf der linken Seite die folgenden Registerkarten:

* **PROJECTS** (PROJEKTE): Sammlungen mit Experimenten, Datasets, Notebooks und anderen Ressourcen, die ein einzelnes Projekt darstellen
* **EXPERIMENTS**: Experimente, die Sie erstellt und ausgeführt oder als Entwürfe gespeichert haben
* **WEB SERVICES** : Webdienste, die Sie über Ihre Experimente bereitgestellt haben
* **NOTEBOOKS** : Jupyter-Notebooks, die Sie erstellt haben
* **DATASETS** : DataSets, die Sie in Studio hochgeladen haben
* **TRAINED MODELS** : Modelle, die Sie in Experimenten trainiert und in Studio gespeichert haben
* **EINSTELLUNGEN** – eine Sammlung von Einstellungen, mit denen Sie Ihr Konto und Ihre Ressourcen konfigurieren

### <a name="gallery"></a>Gallery
Wenn Sie auf **Katalog** klicken, gelangen Sie zum **[Azure KI-Katalog](https://gallery.azure.ai/)** . In diesem Katalog teilt eine Community von Datenwissenschaftlern und Entwicklern Lösungen, die mithilfe von Komponenten der Cortana Intelligence Suite erstellt wurden.

Weitere Informationen zum Katalog finden Sie unter [Teilen und Entdecken von Lösungen im Azure KI-Katalog](gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Komponenten eines Experiments
Ein Experiment besteht auf Datasets, die Daten für Analysemodule bereitstellen, die Sie miteinander verbinden und so ein Vorhersagemodell konstruieren. Ein gültiges Experiment hat spezifisch folgende Eigenschaften:

* Das Experiment besteht aus mindestens einem Dataset und einem Modul.
* Datasets können nur mit Modulen verbunden sein.
* Module können entweder mit Datasets oder mit anderen Modulen verbunden sein.
* Alle Eingangsports für Module müssen eine Verbindung zum Datenfluss aufweisen.
* Alle erforderlichen Parameter der einzelnen Module müssen festgelegt sein.

Sie können ein Experiment von Grund auf neu erstellen, oder Sie können ein vorhandenes Beispielexperiment als Vorlage verwenden. Weitere Informationen finden Sie unter [Kopieren von Beispielexperimenten zum Erstellen neuer Machine Learning-Experimente](sample-experiments.md).

Ein Beispiel für das Erstellen eines einfachen Experiments finden Sie unter [Erstellen eines einfachen Experiments in Azure Machine Learning Studio](create-experiment.md).

Eine ausführlichere exemplarische Vorgehensweise zum Erstellen einer Lösung für Vorhersageanalysen finden Sie unter [Entwickeln einer Vorhersagelösung mit Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Datasets
Ein DataSet besteht aus Daten, die in Machine Learning Studio hochgeladen wurden, sodass sie im Modellierungsprozess verwendet werden können. Machine Learning Studio enthält eine Reihe von Beispiel-DataSets, mit denen Sie experimentieren können, und bei Bedarf lassen sich weitere DataSets hochladen. Hier einige Beispiele der enthaltenen Datasets:

* **MPG-Daten für verschiedene Autos**: Meilen-pro-Gallone-Werte für Autos, die anhand der Anzahl von Zylindern, Motorleistung usw. aufgeführt sind.
* **Brustkrebsdaten:** diagnostische Brustkrebsdaten.
* **Waldbranddaten:** Größe von Waldbränden im nordöstlichen Portugal.

Beim Erstellen eines Experiments können Sie links neben dem Arbeitsbereich aus der Liste der Datasets auswählen.

Eine Liste der in Machine Learning Studio enthaltenen Beispiel-DataSets finden Sie unter [Verwenden von Beispiel-DataSets in Azure Machine Learning Studio](use-sample-datasets.md).

### <a name="modules"></a>Module
Ein Modul ist ein Algorithmus, den Sie auf Ihre Daten anwenden können. Machine Learning Studio hat eine Reihe von Modulen, die von Funktionen für die Dateneinspeisung bis zu Trainings-, Bewertungs- und Überprüfungsvorgängen reichen. Hier einige Beispiele der enthaltenen Module:

* [In ARFF konvertieren:][convert-to-arff] Konvertiert ein serialisiertes .NET-Dataset in ARFF (Attribute-Relation File Format).
* [Elementare Statistiken berechnen:][elementary-statistics] Berechnet elementare Statistiken wie Mittelwert, Standardabweichung usw.
* [Lineare Regression:][linear-regression] Erstellt ein lineares Onlineregressionsmodell, das auf einem Gradientenverfahrenmodell beruht.
* [Bewertungsmodell:][score-model] Bewertet ein trainiertes Klassifizierungs- oder Regressionsmodell.

Beim Erstellen eines Experiments können Sie links neben dem Arbeitsbereich aus der Liste der Module auswählen.

Ein Modul kann eine Reihe von Parametern haben, die Sie zum Konfigurieren der internen Algorithmen des Moduls einsetzen können. Wenn Sie ein Modul im Arbeitsbereich auswählen, werden dessen Parameter rechts neben dem Arbeitsbereich im Bereich **Eigenschaften** angezeigt. Sie können die Parameter in diesem Bereich zur Abstimmung Ihres Modells verändern.

Hilfe zur Navigation durch die umfassende Bibliothek verfügbarer Machine Learning-Algorithmen finden Sie unter [Auswählen von Algorithmen für Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Bereitstellen eines Predictive Analytics-Webdiensts
Wenn Ihr Predictive Analytics-Modell bereit ist, können Sie es als Webdienst direkt von Machine Learning Studio aus bereitstellen. Weitere Informationen zu diesem Verfahren finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts](publish-a-machine-learning-web-service.md).

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Inwiefern unterscheidet sich Azure Machine Studio von Azure Machine Learning Service?

[Azure Machine Learning Service](../service/overview-what-is-azure-ml.md) bietet sowohl SDKs **als auch** eine grafische Benutzeroberfläche (Vorschauversion) zum schnellen Vorbereiten von Daten sowie zum Trainieren und Bereitstellen von Machine Learning-Modellen. Diese grafische Benutzeroberfläche (Vorschauversion) bietet eine ähnliche Drag & Drop-Umgebung wie Studio. Im Gegensatz zur proprietären Computeplattform von Studio verwendet die grafische Benutzeroberfläche jedoch Ihre eigenen Computeressourcen und ist vollständig in Azure Machine Learning Service integriert.

Hier ist ein kurzer Vergleich:

|| Machine Learning Studio | Azure Machine Learning Service:<br/>Grafische Benutzeroberfläche|
|---| --- | --- |
|| Allgemein verfügbar (Generally Available, GA) | In der Vorschau|
|Module für Schnittstellen| Mehrere | Anfänglicher Satz von beliebten Modulen|
|Trainieren von Computezielen| Proprietäres Computeziel, nur CPU-Unterstützung| Unterstützt Azure Machine Learning-Compute, -GPU oder -CPU.<br/>(Weitere Computes in SDK unterstützt)|
|Computeziele für die Bereitstellung| Proprietäres Webdienstformat, nicht anpassbar | Unternehmenssicherheitsoptionen und Azure Kubernetes Service <br/>([Weitere Computes](../service/how-to-deploy-and-where.md) in SDK unterstützt) |
|Automatisiertes Modelltraining und Optimieren von Hyperparametern | Nein | Noch nicht auf der grafischen Benutzeroberfläche. <br/> (Im SDK und Azure-Portal unterstützt.) | 

Testen Sie die grafische Benutzeroberfläche (Vorschauversion) mit [Schnellstart: Vorbereiten und Visualisieren von Daten, ohne Code zu schreiben](../service/ui-quickstart-run-experiment.md)

> [!NOTE]
> Modelle, die in Studio erstellt werden, können nicht von Azure Machine Learning Service bereitgestellt und verwaltet werden. Modelle, die auf der grafischen Benutzeroberfläche erstellt und bereitgestellt werden, können dagegen über den Azure Machine Learning Service-Arbeitsbereich verwaltet werden.

## <a name="free-trial"></a>Kostenlose Testversion

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>Nächste Schritte
Die Grundlagen von Predictive Analytics und maschinellem Lernen werden anhand einer [Schritt-für-Schritt-Schnellstartanleitung](create-experiment.md) und mithilfe von [Beispielen](sample-experiments.md) vermittelt.

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
