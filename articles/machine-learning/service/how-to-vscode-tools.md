---
title: Verwenden von Visual Studio Code für maschinelles Lernen
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie Azure Machine Learning für Visual Studio Code installieren und ein einfaches Experiment mit Azure Machine Learning erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 70f9c34957b977aff9fc6211bf79415ed9abe255
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016525"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Erste Schritte mit Azure Machine Learning für Visual Studio Code

In diesem Artikel erfahren Sie, wie Sie die Erweiterung für Azure Machine Learning für Visual Studio Code verwenden, um Machine Learning- und Deep Learning-Modelle zu trainieren und bereitzustellen.

[Azure Machine Learning Service](overview-what-is-azure-ml.md) bietet Unterstützung für Experimente, die Sie lokal und auf Remotecomputezielen ausführen. Für jedes Experiment können Sie mehrere Ausführungen nachverfolgen, so oft wie Sie verschiedene Techniken, Hyperparameter und mehr iterativ ausprobieren müssen. Sie können Azure Machine Learning verwenden, um benutzerdefinierte Metriken und experimentelle Läufe nachzuverfolgen, wodurch Sie Reproduzierbarkeit und Überwachung im Sinne von Data Science gewinnen.

Sie können diese Modelle auch für Ihre Test- und Produktionsanforderungen bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

+ Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

+ Visual Studio Code muss installiert sein. Visual Studio Code ist ein einfacher, aber leistungsstarker Quellcode-Editor, der auf Ihrem Desktop ausgeführt wird. Er bietet integrierte Unterstützung für Python und andere Programmiersprachen. Wenn Sie Visual Studio Code noch nicht installiert haben, finden Sie [hier die entsprechenden Anweisungen](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installieren Sie Python 3.5 oder höher](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Installieren der Erweiterung für Azure Machine Learning für Visual Studio Code

Beim Installieren der Azure Machine Learning-Erweiterung werden automatisch zwei weitere Erweiterungen installiert (sofern Sie Zugriff auf das Internet haben). Dabei handelt es sich um die [Azure-Kontoerweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) und die [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)-Erweiterung.

Zum Arbeiten mit Azure Machine Learning müssen Sie Visual Studio Code in eine Python-integrierte Entwicklungsumgebung (IDE) umwandeln. Sie benötigen die Microsoft Python-Erweiterung, um [Python in Visual Studio Code](https://code.visualstudio.com/docs/languages/python) zu verwenden. Diese Erweiterung wird automatisch mit der Azure Machine Learning-Erweiterung installiert. Die Erweiterung macht Visual Studio Code zu einer ausgezeichneten IDE, und sie funktioniert auf jedem Betriebssystem und mit einer Vielzahl von Python-Interpretern. Die Microsoft Python-Erweiterung nutzt die gesamte Leistungsfähigkeit von Visual Studio Code, um Autovervollständigung, IntelliSense, Linting, Debugging und Unittests bereitzustellen. Die Erweiterung ermöglicht auch den einfachen Wechsel zwischen Python-Umgebungen, einschließlich virtueller Umgebungen und Conda-Umgebungen. Weitere Informationen zum Bearbeiten, Ausführen und Debuggen von Python-Code finden Sie im [Python-Hallo Welt-Tutorial](https://code.visualstudio.com/docs/python/python-tutorial).

Installieren der Azure Machine Learning-Erweiterung:

1. Öffnen Sie Visual Studio Code.

1. Öffnen Sie in einem Webbrowser die [Azure Machine Learning-Erweiterung für Visual Studio Code (Vorschauversion)](https://aka.ms/vscodetoolsforai).

1. Wählen Sie auf dieser Webseite die Option **Installieren** aus. 

1. Wählen Sie auf der Registerkarte der Erweiterung die Option **Installieren**.

1. Eine Willkommensregisterkarte für die Erweiterung wird in Visual Studio Code geöffnet, und das Azure-Symbol (im folgenden Screenshot rot markiert) wird der Aktivitätsleiste hinzugefügt.

   ![Azure-Symbol auf der Aktivitätsleiste von Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Wählen Sie im Dialogfeld die Option **Anmelden** aus, und befolgen Sie die Aufforderung auf dem Bildschirm für die Authentifizierung bei Azure. 
   
   Die Azure-Kontoerweiterung, die zusammen mit der Azure Machine Learning-Erweiterung für Visual Studio Code installiert wurde, hilft Ihnen bei der Authentifizierung bei Ihrem Azure-Konto. Eine Liste mit Befehlen finden Sie auf der Seite für die [Azure-Kontoerweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Sehen Sie sich die [IntelliCode-Erweiterung für Visual Studio Code (Vorschauversion)](https://go.microsoft.com/fwlink/?linkid=2006060) an. IntelliCode bietet eine Reihe von KI-gestützten Funktionen für IntelliSense in Python, z. B. das Ableiten der relevantesten automatischen Vervollständigung basierend auf dem aktuellen Codekontext.

## <a name="install-the-azure-machine-learning-sdk"></a>Installieren des Azure Machine Learning SDK

1. Stellen Sie sicher, dass Python 3.5 oder höher installiert ist und von Visual Studio Code erkannt wird. Wenn Sie es jetzt installieren, starten Sie Visual Studio Code neu, und [wählen Sie einen Python-Interpreter aus](https://code.visualstudio.com/docs/python/python-tutorial).

1. Geben Sie im integrierten Terminalfenster den zu verwendenden Python-Interpreter an. Oder wählen Sie die EINGABETASTE, um Ihren standardmäßigen Python-Interpreter zu verwenden.

   ![Auswählen des Interpreters](./media/vscode-tools-for-ai/python.png)

1. Rechts unten im Fenster wird die Benachrichtigung eingeblendet, dass das [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) automatisch installiert wird. Die neu erstellte Python-Umgebung ist lokal und privat, und sie erfüllt die Visual Studio Code-Voraussetzungen für die Arbeit mit Azure Machine Learning Service.

   ![Installieren des Azure Machine Learning SDK für Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Erste Schritte mit Azure Machine Learning

Bevor Sie mit dem Trainieren und Bereitstellen von Machine Learning-Modellen in Visual Studio Code beginnen, müssen Sie in der Cloud einen [Azure Machine Learning Service-Arbeitsbereich](concept-workspace.md) erstellen. Dieser Arbeitsbereich enthält Ihre Modelle und Ressourcen. 

Erstellen eines Arbeitsbereichs und Hinzufügen Ihres ersten Experiments:

1. Wählen Sie auf der Aktivitätsleiste von Visual Studio Code das Azure-Symbol aus. Die Seitenleiste für Azure Machine Learning wird angezeigt.

   [![Erstellen eines Arbeitsbereichs](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Klicken Sie mit der rechten Maustaste auf Ihr Azure-Abonnement, und wählen Sie **Arbeitsbereich erstellen** aus. Eine Liste wird angezeigt. Im animierten Beispielbild ist der Name des Abonnements **Free Trial**, und der Arbeitsbereich heißt **TeamWorkspace**. 

1. Wählen Sie eine Ressourcengruppe von der Liste aus, oder erstellen Sie mithilfe des Assistenten in der Befehlspalette eine neue.

1. Geben Sie im Feld einen eindeutigen und klaren Namen für Ihren neuen Arbeitsbereich ein. Im Beispielbild heißt der Arbeitsbereich **TeamWorkspace**.

1. Wählen Sie die EINGABETASTE, um den neuen Arbeitsbereich zu erstellen. Er wird dann in der Struktur unter dem Namen des Abonnements angezeigt.

1. Klicken Sie mit der rechten Maustaste auf den Knoten **Experiment**, und wählen Sie dann im Kontextmenü **Experiment erstellen** aus.  Für Experimente werden Ihre Ausführungen mit Azure Machine Learning gespeichert.

1. Geben Sie in dem Feld einen Namen für das Experiment ein. In den Beispielscreenshots hat das Experiment den Namen **MNIST**.
 
1. Wählen Sie die EINGABETASTE, um das neue Experiment zu erstellen. Das Experiment wird dann in der Struktur unter dem Namen des Arbeitsbereichs angezeigt.

1. In einem Arbeitsbereich können Sie mit der rechten Maustaste auf ein Experiment klicken, um es als das **aktive** Experiment festzulegen. Das **aktive** Experiment ist Ihr aktuelles Experiment. Ihr geöffneter Ordner in Visual Studio Code wird in der Cloud mit diesem Experiment verknüpft. Dieser Ordner sollte Ihre lokalen Python-Skripts enthalten.

Jetzt werden Ihre wichtigsten Metriken in der Experimentübersicht gespeichert. Ebenso werden die von Ihnen trainierten Modelle automatisch in Azure Machine Learning hochgeladen und gemeinsam mit Ihren Experimentmetriken und -protokollen gespeichert. 

[![Anfügen eines Ordners in Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Erstellen und Verwalten von Computezielen

Mit Azure Machine Learning für Visual Studio Code können Sie Ihre Daten vorbereiten, Modelle trainieren und sie sowohl lokal als auch an Remotecomputezielen bereitstellen.

Die Erweiterung unterstützt mehrere Remotecomputeziele für Azure Machine Learning. Weitere Informationen finden Sie in der vollständigen Liste der unterstützten [Computeziele für Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Erstellen von Computezielen für Azure Machine Learning in Visual Studio Code

Erstellen eines Computeziels:

1. Wählen Sie auf der Aktivitätsleiste von Visual Studio Code das Azure-Symbol aus. Die Seitenleiste für Azure Machine Learning wird angezeigt.

2. Erweitern Sie in der Strukturansicht Ihr Azure-Abonnement und den Arbeitsbereich des Azure Machine Learning-Diensts. Im folgenden Beispielbild ist der Name des Abonnements **Free Trial**, und der Arbeitsbereich heißt **TeamWorkspace**. 

3. Klicken Sie unter dem Arbeitsbereichsknoten mit der rechten Maustaste auf den Knoten **Compute**, und wählen Sie **Create Compute** (Compute erstellen) aus.

4. Wählen Sie den Computezieltyp in der Liste aus. 

5. Wählen Sie in der Befehlspalette eine VM-Größe aus.

6. Geben Sie in der Befehlspalette einen Namen für das Computeziel in das Feld ein. 

7. Geben Sie alle erweiterten Eigenschaften in der JSON-Konfigurationsdatei an, die auf einer neuen Registerkarte geöffnet wird. Sie können Eigenschaften wie etwa die maximale Knotenanzahl angeben.

8. Wenn Sie mit der Konfiguration Ihres Computeziels fertig sind, wählen Sie im Fenster unten rechts die Option **Senden**.

Hier sehen Sie ein Beispiel für die Erstellung eines Azure Machine Learning Computeziels (AMLCompute):

[![Erstellen eines AML-Computeziels in Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Die Laufzeitkonfigurationsdatei

Die Visual Studio Code-Erweiterung erstellt automatisch ein lokales Computeziel und Laufzeitkonfigurationen für die lokalen und Docker-basierten Umgebungen auf Ihrem lokalen Computer. Die Laufzeitkonfigurationsdateien befinden sich unter dem zugeordneten Computezielknoten. 

## <a name="train-and-tune-models"></a>Trainieren und Optimieren von Modellen

Verwenden Sie Azure Machine Learning für Visual Studio Code (Vorschauversion), um Ihren Code schnell zu durchlaufen, ihn schrittweise auszuführen und zu debuggen, und Ihre Lösung für Quellcodeverwaltung zu verwenden. 

Lokales Ausführen Ihres Experiments mit Azure Machine Learning:

1. Wählen Sie auf der Aktivitätsleiste von Visual Studio Code das Azure-Symbol aus. Die Seitenleiste für Azure Machine Learning wird angezeigt.

1. Erweitern Sie in der Strukturansicht Ihr Azure-Abonnement und den Arbeitsbereich des Azure Machine Learning-Diensts. 

1. Erweitern Sie unter dem Knoten „Arbeitsbereich“ den Knoten **Compute**, und klicken Sie mit der rechten Maustaste auf die **Ausführungskonfiguration** der Computeressource, die Sie verwenden möchten. 

1. Wählen Sie **Run Experiment** (Experiment ausführen) aus.

1. Wählen Sie im Datei-Explorer das auszuführende Skript aus. 

1. Wählen Sie **View Experiment Run** (Experimentausführung anzeigen) aus, um das integrierte Azure Machine Learning-Portal anzuzeigen, in dem Sie Ihre Ausführungen überwachen und Ihre trainierten Modelle anzeigen können.

Hier ist ein Beispiel für die lokale Ausführung eines Experiments:

[![Lokales Ausführen eines Experiments](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Verwenden von Remotecomputeressourcen für Experimente in Visual Studio Code

Sie müssen eine Laufzeitkonfigurationsdatei erstellen, um beim Trainieren ein Remotecomputeziel zu verwenden. Diese Datei teilt Azure Machine Learning nicht nur mit, wo Ihr Experiment ausgeführt sondern auch, wie die Umgebung vorbereitet werden soll.

#### <a name="the-conda-dependencies-file"></a>Die Conda-Abhängigkeitsdatei

Standardmäßig wird eine neue Conda-Umgebung für Sie erstellt, und Ihre Installationsabhängigkeiten werden verwaltet. Allerdings müssen Sie Ihre Abhängigkeiten und deren Versionen in der Datei *aml_config/conda_dependencies.yml* angeben. 

Der folgende Ausschnitt aus der Standarddatei *aml_config/conda_dependencies.yml* gibt `tensorflow=1.12.0` an. Wenn Sie die Version der Abhängigkeit nicht angeben, wird die aktuelle Version verwendet. Sie können der Konfigurationsdatei zusätzliche Abhängigkeiten hinzufügen.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

Ausführen Ihres Experiment mit Azure Machine Learning auf einem Remotecomputeziel:

1. Wählen Sie auf der Aktivitätsleiste von Visual Studio Code das Azure-Symbol aus. Die Seitenleiste für Azure Machine Learning wird angezeigt.

1. Erweitern Sie in der Strukturansicht Ihr Azure-Abonnement und den Arbeitsbereich des Azure Machine Learning-Diensts. 

1. Klicken Sie im Editor-Fenster mit der rechten Maustaste auf das Python-Skript, und wählen Sie **AML: Run as Experiment in Azure** (AML: als Experiment in Azure ausführen) aus. 

1. Wählen Sie in der Befehlspalette das Computeziel aus. 

1. Geben Sie in der Befehlspalette den Namen der Laufzeitkonfiguration in das Feld ein. 

1. Bearbeiten Sie die Datei *conda_dependencies.yml*, um die Laufzeitabhängigkeiten des Experiments festzulegen. Wählen Sie dann im Fenster unten rechts die Option **Senden**. 

1. Wählen Sie **View Experiment Run** (Experimentausführung anzeigen) aus, um das integrierte Azure Machine Learning-Portal anzuzeigen, in dem Sie Ihre Ausführungen überwachen und Ihre trainierten Modelle anzeigen können.

Hier sehen Sie ein Beispiel für das Ausführen eines Experiments auf einem Remotecomputeziel:

[![Ausführen eines Experiments auf einem Remoteziel](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Erstellen und Verwalten von Modellen
In Azure Machine Learning können Sie Ihre Machine Learning-Modelle in der Cloud und am Edge bereitstellen und verwalten. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Registrieren Ihres Modells bei Azure Machine Learning in Visual Studio Code

Nachdem Sie Ihr Modell nun trainiert haben, können Sie es in Ihrem Arbeitsbereich registrieren. Registrierte Modelle lassen sich nachverfolgen und bereitstellen.

Registrieren des Modells:

1. Wählen Sie auf der Aktivitätsleiste von Visual Studio Code das Azure-Symbol aus. Die Seitenleiste für Azure Machine Learning wird angezeigt.

1. Erweitern Sie in der Strukturansicht Ihr Azure-Abonnement und den Arbeitsbereich des Azure Machine Learning-Diensts.

1. Klicken Sie unter dem Knoten „Arbeitsbereich“ mit der rechten Maustaste auf **Modelle**, und wählen Sie **Register Model** (Modell registrieren) aus.

1. Geben Sie in der Befehlspalette einen Modellnamen in das Feld ein. 

1. Wählen Sie in der Liste aus, ob Sie eine **Modelldatei** (für einzelne Modelle) oder einen **Modellordner** (für Modelle mit mehreren Dateien, wie etwa TensorFlow) hochladen möchten. 

1. Wählen Sie Ihren Ordner oder die Datei aus.

1. Wenn Sie mit der Konfiguration Ihrer Modelleigenschaften fertig sind, wählen Sie im Fenster unten rechts die Option **Senden**. 

Hier sehen Sie ein Beispiel für die Registrierung Ihres Modells bei Azure Machine Learning:

[![Registrieren eines Modells bei AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Bereitstellen des Diensts über Visual Studio Code

In Visual Studio Code können Sie Ihren Webdienst an diesen Orten bereitstellen:
+ Azure Container Instances (ACI) für Tests
+ Azure Kubernetes Service (AKS) für die Produktion

Sie brauchen für das Testen nicht vorab einen ACI-Container zu erstellen, da ACI-Container dynamisch erstellt werden. AKS-Cluster müssen allerdings im Voraus konfiguriert werden. Weitere Informationen finden Sie unter [Bereitstellen von Modellen mit dem Azure Machine Learning-Dienst](how-to-deploy-and-where.md).

Bereitstellen eines Webdiensts:

1. Wählen Sie auf der Aktivitätsleiste von Visual Studio Code das Azure-Symbol aus. Die Seitenleiste für Azure Machine Learning wird angezeigt.

1. Erweitern Sie in der Strukturansicht Ihr Azure-Abonnement und Ihren Arbeitsbereich des Azure Machine Learning-Diensts.

1. Erweitern Sie unter dem Arbeitsbereich-Knoten den Knoten **Modelle**.

1. Klicken Sie mit der rechten Maustaste auf das Modell, das Sie bereitstellen möchten, und wählen Sie im Kontextmenü die Option **Deploy Service from Registered Model** (Dienst aus registriertem Modell bereitstellen) aus.

1. Wählen Sie in der Befehlspalette das Computeziel aus, an dem die Bereitstellung erfolgen soll. 

1. Geben Sie in der Befehlspalette einen Namen für diesen Dienst in das Feld ein.  

1. Drücken Sie in der Befehlspalette die EINGABETASTE auf Ihrer Tastatur, um nach der Skriptdatei zu suchen und sie auszuwählen.

1. Drücken Sie in der Befehlspalette die EINGABETASTE auf Ihrer Tastatur, um nach der Conda-Abhängigkeitsdatei zu suchen und sie auszuwählen.

1. Wenn Sie mit der Konfiguration Ihrer Diensteigenschaften fertig sind, wählen Sie im Fenster unten rechts die Option **Senden**, um die Bereitstellung durchzuführen. In der Diensteigenschaftendatei können Sie eine lokale Docker-Datei oder eine Datei vom Typ „schema.json“ angeben.

Der Webdienst ist nun bereitgestellt.

Hier sehen Sie ein Beispiel für die Bereitstellung eines Webdiensts:

[![Bereitstellen eines Webdiensts](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Verwenden von Tastenkombinationen

Sie können über die Tastatur auf die Azure Machine Learning-Funktionen in Visual Studio Code zugreifen. Die wichtigste Tastenkombination, die Sie kennen sollten, ist STRG+UMSCHALT+P, mit der die Befehlspalette geöffnet wird. Über die Befehlspalette haben Sie Zugriff auf alle Funktionen von Visual Studio Code, einschließlich der Tastenkombinationen für die am häufigsten verwendeten Vorgänge.

[![Tastenkombinationen für Azure Machine Learning für Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* Eine exemplarische Vorgehensweise zum Trainieren mit Azure Machine Learning außerhalb von Visual Studio Code finden Sie in [Tutorial: Trainieren von Modellen mit Azure Machine Learning](tutorial-train-models-with-aml.md).
* Eine exemplarische Vorgehensweise zum lokalen Bearbeiten, Ausführen und Debuggen von Code finden Sie im [Python-Hello World-Tutorial](https://code.visualstudio.com/docs/python/python-tutorial).
