---
title: Was ist eine Azure Machine Learning-Compute-Instanz?
titleSuffix: Azure Machine Learning
description: Erfahren Sie mehr über die Azure Machine Learning-Compute-Instanz, eine vollständig verwaltete cloudbasierte Arbeitsstation.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/02/2020
ms.openlocfilehash: ef753043218f259c69082dbb8682517be79cf95c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099794"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Was ist eine Azure Machine Learning-Compute-Instanz?

Eine Azure Machine Learning-Compute-Instanz ist eine verwaltete cloudbasierte Arbeitsstation für Datenanalysten.

Compute-Instanzen vereinfachen den Einstieg in die Azure Machine Learning-Entwicklung und verhelfen IT-Administratoren zu Verwaltungs- und Unternehmensbereitschaftsfunktionen.  

Nutzen Sie eine Compute-Instanz als Ihre vollständig konfigurierte und verwaltete Entwicklungsumgebung in der Cloud für maschinelles Lernen. Sie können auch als Computeziel für Entwicklungs- und Testzwecke für Training und Rückschluss verwendet werden.  

Verwenden Sie für das Modelltraining in Produktionsqualität einen [Azure Machine Learning-Computecluster](how-to-create-attach-compute-cluster.md) mit Skalierungsmöglichkeiten dank mehrerer Knoten. Verwenden Sie für die Modellimplementierung in Produktionsqualität den [Azure Kubernetes Service-Cluster](how-to-deploy-azure-kubernetes-service.md).

Damit die Jupyter-Funktionen für Computeinstanzen eingesetzt werden können, stellen Sie sicher, dass die Websocketkommunikation nicht deaktiviert ist. Stellen Sie sicher, dass Ihr Netzwerk WebSocket-Verbindungen mit *.instances.azureml.net und *.instances.azureml.ms zulässt.

## <a name="why-use-a-compute-instance"></a>Gründe für eine Compute-Instanz

Eine Compute-Instanz ist eine vollständig verwaltete cloudbasierte Arbeitsstation, die für Ihre Machine Learning-Entwicklungsumgebung optimiert ist. Ihnen bieten sich folgende Vorteile:

|Hauptvorteile|BESCHREIBUNG|
|----|----|
|Produktivität|Sie können Modelle mit integrierten Notebooks und den folgenden Tools im Azure Machine Learning Studio erstellen und bereitstellen:<br/>-  Jupyter<br/>-  JupyterLab<br/>-  RStudio (Vorschauversion)<br/>Die Compute-Instanz ist vollständig in den Arbeitsbereich und das Studio von Azure Machine Learning integriert. Sie können Notebooks und Daten mit anderen Datenanalysten im Arbeitsbereich gemeinsam nutzen.<br/> Sie können auch [VS Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) mit Compute-Instanzen verwenden.
|Verwaltet und sicher|Verringern Sie Ihren Sicherheitsaufwand, und gewinnen Sie Compliance mit Anforderungen der Unternehmenssicherheit. Compute-Instanzen bieten verlässliche Verwaltungsrichtlinien und sichere Netzwerkkonfigurationen wie die folgenden:<br/><br/>– Automatisierte Bereitstellung über Resource Manager-Vorlagen oder das Azure Machine Learning SDK<br/>- [Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/overview.md)<br/>- [Unterstützung virtueller Netzwerke](./how-to-secure-training-vnet.md#compute-instance)<br/>- SSH-Richtlinie zum Aktivieren/Deaktivieren des SSH-Zugriffs<br/>TLS 1.2 aktiviert |
|Vorkonfiguriert&nbsp;für&nbsp;ML|Sparen Sie Zeit bei der Einrichtung von Aufgaben mit vorkonfigurierten und aktuellen ML-Paketen, Deep Learning- Frameworks und GPU-Treibern.|
|Vollständig anpassbar|Umfassende Unterstützung für Azure-VM-Typen einschließlich GPUs und durchweg einfache Anpassungen wie die Installation von Paketen und Treibern machen erweiterte Szenarien zu einem Kinderspiel. |

Sie können selbst [eine Compute-Instanz erstellen](how-to-create-manage-compute-instance.md?tabs=python#create), oder ein Administrator kann [eine Compute-Instanz für Sie erstellen](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview).

## <a name="tools-and-environments"></a><a name="contents"></a>Tools und Umgebungen

> [!IMPORTANT]
> Die in diesem Artikel markierten Elemente (Vorschau) sind aktuell als öffentliche Vorschau verfügbar.
> Die Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Eine Azure Machine Learning-Compute-Instanz ermöglicht Ihnen das Erstellen, Trainieren und Bereitstellen von Modellen in einer vollständig integrierten Notebookumgebung in Ihrem Arbeitsbereich.

Sie können Jupyter-Notebooks in [VS Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) unter Verwendung der Compute-Instanz als Remoteserver ausführen, ohne dass SSH erforderlich ist. Sie können die VS Code-Integration auch über die [Remote-SSH-Erweiterung](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/) aktivieren.

Sie können [Pakete installieren](how-to-access-terminal.md#install-packages) und [Kernel](how-to-access-terminal.md#add-new-kernels) zu Ihrer Compute-Instanz hinzufügen.  

Die folgenden Tools und Umgebungen sind in der Compute-Instanz bereits installiert: 

|Allgemeine Tools und Umgebungen|Details|
|----|:----:|
|Treiber|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI Library||
|Azure CLI ||
|Azure Machine Learning-Beispiele ||
|Docker||
|nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**R**-Tools und -Umgebungen|Details|
|----|:----:|
|RStudio Server Open Source Edition (Vorschauversion)||
|R-Kernel||
|Azure Machine Learning SDK für R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK-Beispiele|

|**PYTHON**-Tools und Umgebungen|Details|
|----|----|
|Anaconda Python||
|Jupyter und Erweiterungen||
|Jupyterlab und Erweiterungen||
[Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)</br>aus PyPI|Umfasst die meisten der AzureML-Zusatzpakete.  Um die vollständige Liste anzuzeigen, [öffnen Sie auf Ihrer Compute-Instanz ein Terminalfenster](how-to-access-terminal.md), und führen Sie Folgendes aus: <br/> `conda list -n azureml_py36 azureml*` |
|Andere PyPi-Pakete|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-Pakete|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Deep Learning-Pakete|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-Pakete|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Python- und R-SDK-Beispiele für Azure Machine Learning||

Python-Pakete sind alle in der **Python 3.6 – AzureML**-Umgebung installiert.  

## <a name="accessing-files"></a>Zugreifen auf Dateien

Notebooks und R-Skripts werden im Standardspeicherkonto Ihres Arbeitsbereichs in der Azure-Dateifreigabe gespeichert.  Diese Dateien befinden sich im Verzeichnis „Benutzerdateien“. Dieser Speicher erleichtert die gemeinsame Nutzung von Notebooks zwischen Compute-Instanzen. Das Speicherkonto bewahrt auch Ihre Notebooks sicher auf, wenn Sie eine Compute-Instanz beenden oder löschen.

Das Azure-Dateifreigabekonto Ihres Arbeitsbereichs wird als Laufwerk in die Compute-Instanz eingebunden. Dieses Laufwerk ist das Standardarbeitsverzeichnis für Jupyter, Jupyter Labs und RStudio. Das bedeutet, dass die Notebooks und andere Dateien, die Sie in Jupyter, JupyterLab oder RStudio erstellen, automatisch auf der Dateifreigabe gespeichert werden und auch für die Verwendung in anderen Compute-Instanzen zur Verfügung stehen.

Auf die Dateien in der Dateifreigabe kann von allen Compute-Instanzen im gleichen Arbeitsbereich zugegriffen werden. Änderungen an diesen Dateien auf der Compute-Instanz werden zuverlässig dauerhaft in der Dateifreigabe zurückgespeichert.

Sie können auch die neuesten Azure Machine Learning-Beispiele in Ihren Ordner im Verzeichnis mit den Benutzerdateien in der Dateifreigabe des Arbeitsbereichs klonen.

Das Schreiben kleiner Dateien kann auf Netzlaufwerken langsamer sein als das Schreiben auf den lokalen Datenträger der Compute-Instanz selbst.  Wenn Sie viele kleine Dateien schreiben, sollten Sie versuchen, ein Verzeichnis direkt auf der Compute-Instanz zu verwenden, z. B. ein `/tmp`-Verzeichnis. Beachten Sie, dass auf diese Dateien von anderen Compute-Instanzen nicht zugegriffen werden kann. 

Sie können das Verzeichnis `/tmp` auf der Compute-Instanz für Ihre temporären Daten verwenden.  Schreiben Sie jedoch keine großen Dateien mit Daten auf den Betriebssystemdatenträger der Compute-Instanz.  Verwenden Sie stattdessen [Datenspeicher](concept-azure-machine-learning-architecture.md#datasets-and-datastores). Wenn Sie die JupyterLab Git-Erweiterung installiert haben, kann dies auch zu einer Verlangsamung der Leistung von Compute-Instanzen führen.

## <a name="managing-a-compute-instance"></a>Verwalten einer Compute-Instanz

Wählen Sie Ihrem Arbeitsbereich in Azure Machine Learning Studio **Compute** aus, und wählen sie dann oben **Compute-Instanz** aus.

![Verwalten einer Compute-Instanz](./media/concept-compute-instance/manage-compute-instance.png)

Sie können folgende Aktionen ausführen:

* [Erstellen einer Compute-Instanz](#create). 
* Aktualisieren der Registerkarte „Compute-Instanzen“.
* Starten, Beenden und erneutes Starten einer Compute-Instanz.  Sie zahlen für die Instanz, wann immer sie ausgeführt wird. Beenden Sie die Compute-Instanz, wenn Sie sie nicht verwenden, um Kosten zu sparen. Das Beenden einer Compute-Instanz gibt diese frei. Starten Sie sie dann erneut, wenn Sie sie benötigen. Beachten Sie, dass das Beenden der Compute-Instanz die Abrechnung der Computestunden stoppt, aber Ihnen werden weiterhin Datenträger, öffentliche IP-Adresse und Standardlastenausgleich in Rechnung gestellt.
* Löschen einer Compute-Instanz.
* Filtern Sie die Liste der Compute-Instanzen, um nur diejenigen anzuzeigen, die Sie erstellt haben.

Für jede Compute-Instanz in Ihrem Arbeitsbereich, die Sie verwenden können, haben Sie folgende Möglichkeiten:

* Zugreifen auf Jupyter, JupyterLab, RStudio auf der Computeinstanz
* SSH-Verbindung mit Compute-Instanz. Der SSH-Zugriff ist standardmäßig deaktiviert, kann aber zum Zeitpunkt der Erstellung der Compute-Instanz aktiviert werden. Der SSH-Zugriff erfolgt über einen Mechanismus mit öffentlichem/privatem Schlüssel. Auf der Registerkarte finden Sie Details zur SSH-Verbindung wie IP-Adresse, Benutzername und Portnummer.
* Informieren Sie sich über Details einer bestimmten Compute-Instanz wie IP-Adresse und Region.

Mithilfe von [Azure RBAC](../role-based-access-control/overview.md) können Sie steuern, welche Benutzer im Arbeitsbereich eine Compute-Instanz erstellen, löschen, starten, beenden und neu starten können. Alle Benutzer mit der Rolle „Mitwirkender“ und „Besitzer“ des Arbeitsbereichs können Compute-Instanzen im gesamten Arbeitsbereich erstellen, löschen, starten, beenden und neu starten. Allerdings darf nur der Ersteller einer bestimmten Compute-Instanz oder der zugewiesene Benutzer (falls sie in seinem Namen erstellt wurde) auf dieser Compute-Instanz auf Jupyter, JupyterLab und RStudio zugreifen. Eine Compute-Instanz ist für einen einzelnen Benutzer vorgesehen, der über Root-Zugriff und Terminalzugriff über Jupyter/JupyterLab/RStudio verfügt. Die Compute-Instanz weist die Einzelbenutzeranmeldung auf, und alle Aktionen verwenden die Identität dieses Benutzers für Azure RBAC und die Zuordnung von Experimentausführungen. Der SSH-Zugriff wird über einen Mechanismus mit öffentlichem/privatem Schlüssel gesteuert.

Diese Aktionen können von Azure RBAC gesteuert werden:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

Zum Erstellen einer Compute-Instanz müssen Sie über Berechtigungen für die folgenden Aktionen verfügen:
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


### <a name="create-a-compute-instance"></a><a name="create"></a>Erstellen einer Compute-Instanz

Erstellen Sie in Ihrem Arbeitsbereich im Azure Machine Learning Studio [eine neue Compute-Instanz](how-to-create-attach-compute-studio.md#compute-instance) entweder im Abschnitt **Compute** oder im Abschnitt **Notebooks**, wenn Sie bereit sind, eines Ihrer Notebooks auszuführen. 

Sie können auch eine Instanz
* direkt in der [integrierten Notebookumgebung](tutorial-1st-experiment-sdk-setup.md#azure) erstellen.
* Im Azure-Portal
* Über eine Azure Resource Manager-Vorlage. Eine Beispielvorlage finden Sie unter [Erstellen einer Azure Machine Learning Compute-Instanzvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* Mit dem [Azure Machine Learning SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md)
* Über die [CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

Das Kontingent dedizierter Kerne pro Region pro VM-Familie und gesamte regionale Kontingent, das für die Erstellung von Compute-Instanzen gilt, ist einheitlich und wird mit dem Kontingent für Azure Machine Learning-Trainingcomputecluster gemeinsam genutzt. Das Beenden der Compute-Instanz gibt keine Kontingente frei, um sicherzustellen, dass Sie die Compute-Instanz erneut starten können.


### <a name="create-on-behalf-of-preview"></a>Erstellen im Namen von (Vorschau)

Als Administrator können Sie im Namen einer wissenschaftlichen Fachkraft für Daten eine Compute-Instanz erstellen und ihr die Instanz mit der folgenden Methode zuweisen:
* [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Ausführliche Informationen zum Suchen der in dieser Vorlage benötigten „TenantID“ und „ObjectID“ finden Sie unter [Ermitteln von Identitätsobjekt-IDs für die Authentifizierungskonfiguration](../healthcare-apis/find-identity-object-ids.md).  Sie können diese Werte auch im Azure Active Directory-Portal abrufen.
* REST-API

Die wissenschaftliche Fachkraft für Daten, für die Sie die Compute-Instanz erstellen, benötigt die folgenden Azure RBAC-Berechtigungen: 
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

Die wissenschaftliche Fachkraft für Daten kann die Compute-Instanz starten, beenden und neu starten. Sie kann die Compute-Instanz für folgende Zwecke verwenden:
* Jupyter
* JupyterLab
* RStudio
* Integrierte Notebooks

## <a name="compute-target"></a>Computeziel

Compute-Instanzen können als [Trainingscomputeziele](concept-compute-target.md#train) verwendet werden, ähnlich wie Azure Machine Learning-Computetrainingscluster. 

Folgendes gilt für eine Compute-Instanz:
* Sie verfügt über eine Auftragswarteschlange.
* Sie führt Aufträge sicher in einer virtuellen Netzwerkumgebung aus, ohne dass Unternehmen hierfür SSH-Ports öffnen müssen. Der Auftrag wird in einer Containerumgebung ausgeführt und packt die Abhängigkeiten Ihres Modells in einen Docker-Container.
* Sie kann mehrere kleine Aufträge parallel ausführen (Vorschauversion).  Zwei Aufträge pro Kern können parallel ausgeführt werden, während die restlichen Aufträge in die Warteschlange eingereiht werden.
* Unterstützt verteilte Trainingsaufträge mit einem einzelnen Knoten mit mehreren GPUs

Sie können die Compute-Instanz als gefolgertes lokales Bereitstellungsziel für Test-/Debugszenarien verwenden.


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Was ist mit der Notebook-VM passiert?

Compute-Instanzen ersetzen die Notebook-VM.  

Alle Notebookdateien, die in der Dateifreigabe des Arbeitsbereichs gespeichert sind, und Daten in den Datenspeichern des Arbeitsbereichs sind auf einer Compute-Instanz zugänglich. Allerdings müssen alle benutzerdefinierten Pakete, die zuvor auf einer Notebook-VM installiert waren, auf der Compute-Instanz neu installiert werden. Kontingentbeschränkungen, die für die Erstellung von Computeclustern gelten, gelten ebenso für die Erstellung von Compute-Instanzen.

Neue Notebook-VMs können nicht erstellt werden. Sie können jedoch weiterhin auf die von Ihnen erstellten Notebook-VMs bei voller Funktionalität zugreifen und diese nutzen. Compute-Instanzen können im gleichen Arbeitsbereich wie die vorhandenen Notebook-VMs erstellt werden.


## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Verwalten einer Compute-Instanz](how-to-create-manage-compute-instance.md)
* [Tutorial: Trainieren Ihres ersten ML-Modells](tutorial-1st-experiment-sdk-train.md) zeigt, wie eine Compute-Instanz mit einem integrierten Notebook verwendet wird.
