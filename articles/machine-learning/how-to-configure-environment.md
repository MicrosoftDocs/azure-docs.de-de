---
title: Einrichten der Python-Entwicklungsumgebung
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Python-Entwicklungsumgebungen für Azure Machine Learning in Jupyter Notebooks, Visual Studio Code, Azure Databricks und Data Science Virtual Machines einrichten.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 6fe43c39b17e574e1913cd9e7b4292a71f1e418d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031758"
---
# <a name="set-up-a-python-development-environment-for-azure-machine-learning"></a>Einrichten einer Python-Entwicklungsumgebung für Azure Machine Learning

Hier erfahren Sie, wie Sie eine Python-Entwicklungsumgebung für Azure Machine Learning konfigurieren.

In der folgenden Tabelle sind die einzelnen in diesem Artikel behandelten Entwicklungsumgebungen zusammen mit den jeweiligen Vor- und Nachteilen aufgeführt.

| Environment | Vorteile | Nachteile |
| --- | --- | --- |
| [Lokale Umgebung](#local) | Vollständige Kontrolle über Ihre Entwicklungsumgebung und die Abhängigkeiten. Ausführung mit einem Buildtool, einer Umgebung oder einer IDE Ihrer Wahl. | Der Einstieg dauert länger. Die erforderlichen SDK-Pakete müssen installiert werden. Außerdem muss eine Umgebung installiert werden, wenn noch keine vorhanden ist. |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Vergleichbar mit der cloudbasierten Compute-Instanz (Python und das SDK sind vorinstalliert), aber mit zusätzlichen gängigen vorinstallierten Data Science- und Machine Learning-Tools. Einfache Skalierung und Kombination mit anderen benutzerdefinierten Tools und Workflows. | Langsamerer Einstieg im Vergleich zur cloudbasierten Compute-Instanz. |
| [Azure Machine Learning-Computeinstanz](#compute-instance) | Einfachste Methode für den Einstieg. Das gesamte SDK ist bereits auf der Arbeitsbereichs-VM installiert. Die Notebooktutorials sind vorab geklont und bereit zur Ausführung. | Keine Kontrolle über Ihre Entwicklungsumgebung und die Abhängigkeiten. Zusätzliche Kosten für den virtuellen Linux-Computer (virtueller Computer kann bei Nichtverwendung beendet werden, um Gebühren zu vermeiden). Preisdetails finden Sie [hier](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Azure Databricks](how-to-configure-databricks-automl-environment.md) | Ideal für die Ausführung umfangreicher Workflows mit maschinellem Lernen auf der skalierbaren Apache Spark-Plattform. | Übermaß für experimentelles maschinelles Lernen oder kleinere Experimente und Workflows. Zusätzliche Kosten für Azure Databricks. Preisdetails finden Sie [hier](https://azure.microsoft.com/pricing/details/databricks/). |

Dieser Artikel enthält außerdem zusätzliche Anwendungstipps für die folgenden Tools:

* Jupyter Notebooks: Wenn Sie Jupyter Notebooks bereits verwenden, enthält das SDK einige zusätzliche Elemente, die Sie installieren sollten.

* Visual Studio Code: Wenn Sie Visual Studio Code verwenden, enthält die [Azure Machine Learning-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) eine umfassende Sprachunterstützung für Python sowie Features, mit denen die Arbeit mit Azure Machine Learning wesentlich bequemer und produktiver wird.

## <a name="prerequisites"></a>Voraussetzungen

* Einen Azure Machine Learning-Arbeitsbereich. Wenn Sie nicht über einen Azure Machine Learning-Arbeitsbereich verfügen, können Sie einen über das [Azure-Portal](how-to-manage-workspace.md), die [Azure-Befehlszeilenschnittstelle](how-to-manage-workspace-cli.md#create-a-workspace) oder über [Azure Resource Manager-Vorlagen](how-to-create-workspace-template.md) erstellen.

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> Nur lokal und DSVM: Erstellen einer Konfigurationsdatei für den Arbeitsbereich

Die Arbeitsbereichs-Konfigurationsdatei ist eine JSON-Datei, die das SDK zur Kommunikation mit Ihrem Azure Machine Learning-Arbeitsbereich instruiert. Die Datei weist den Namen *config.json* und das folgende Format auf:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Diese JSON-Datei muss sich in der Verzeichnisstruktur befinden, die Ihre Python-Skripts oder Jupyter Notebooks enthält. Sie kann sich im selben Verzeichnis, in einem Unterverzeichnis namens *.azureml* oder in einem übergeordneten Verzeichnis befinden.

Verwenden Sie die [`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#from-config-path-none--auth-none---logger-none---file-name-none-&preserve-view=true)-Methode, um diese Datei in Ihrem Code zu verwenden. Dieser Code lädt die Informationen aus der Datei und stellt eine Verbindung mit Ihrem Arbeitsbereich her.

Für das Erstellen einer Arbeitsbereichskonfigurationsdatei können Sie eine der folgenden beiden Methoden verwenden:

* Azure-Portal

    **Datei herunterladen**: Wählen Sie im [Azure-Portal](https://ms.portal.azure.com) im Abschnitt **Übersicht** Ihres Arbeitsbereichs **config.json herunterladen** aus.

    ![Azure-Portal](./media/how-to-configure-environment/configure.png)

* Python-SDK für Azure Machine Learning

    Erstellen Sie ein Skript, um eine Verbindung zu Ihrem Azure Machine Learning-Arbeitsbereich herzustellen, und verwenden Sie die [`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#write-config-path-none--file-name-none-&preserve-view=true)-Methode, um Ihre Datei zu erstellen. Speichern Sie sie als *.azureml/config.json*. Achten Sie darauf, dass Sie `subscription_id`, `resource_group` und `workspace_name` durch Ihre eigenen Werte ersetzen.

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

## <a name="local-computer-or-remote-vm-environment"></a><a id="local"></a>Lokaler Computer oder Remote-VM-Umgebung

Sie können eine Entwicklungsumgebung auf einem lokalen Computer oder einer Remote-VM (z. B. einer Azure Machine Learning-Compute-Instanz oder DSVM) einrichten. 

So konfigurieren Sie eine Entwicklungsumgebung lokal oder auf einer Remote-VM

1. Erstellen Sie eine virtuelle Python-Umgebung (virtualenv, conda).

    > [!NOTE]
    > Obwohl es nicht erforderlich ist, wird empfohlen, dass Sie [Anaconda](https://www.anaconda.com/download/) oder [Miniconda](https://www.anaconda.com/download/) verwenden, um virtuelle Python-Umgebungen zu verwalten und Pakete zu installieren.

    > [!IMPORTANT]
    > Wenn Sie unter Linux oder macOS eine andere Shell als Bash verwenden (z.B. zsh), werden beim Ausführen einiger Befehle möglicherweise Fehler ausgegeben. Um dieses Problem zu umgehen, starten Sie mit dem Befehl `bash` eine neue Bash-Shell starten, und führen Sie die Befehle in dieser aus.

1. Aktivieren Sie Ihre neu erstellte virtuelle Python-Umgebung.
1. Installieren Sie das [Python SDK für Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
1. [Erstellen Sie eine Arbeitsbereichskonfigurationsdatei](#workspace), oder verwenden Sie eine vorhandene, um Ihre lokale Umgebung so zu konfigurieren, dass Ihr Azure Machine Learning-Arbeitsbereich verwendet wird.

Da Sie nun Ihre lokale Umgebung eingerichtet haben, können Sie mit der Verwendung von Azure Machine Learning beginnen. Informationen zu den ersten Schritten finden Sie unter [Tutorial: Erste Schritte mit Azure Machine Learning in Ihrer Entwicklungsumgebung (Teil 1 von 4)](tutorial-1st-experiment-sdk-setup-local.md).

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebooks

Wenn Sie einen lokalen Jupyter Notebook-Server betreiben, sollten Sie ein IPython-Kernel für Ihre virtuelle Python-Umgebung erstellen. Dies unterstützt Sie dabei, für das erwartete Kernel- und Paketimportverhalten zu sorgen.

1. Aktivieren umgebungsspezifischer IPython-Kernel

    ```bash
    conda install notebook ipykernel
    ```

1. Erstellen Sie ein Kernel für Ihre virtuelle Python-Umgebung. Ersetzen Sie `<myenv>` durch den Namen Ihrer virtuellen Python-Umgebung.

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. Starten des Jupyter Notebook-Servers

Unter [Beispielnotebooks des Azure Machine Learning-Diensts](https://github.com/Azure/MachineLearningNotebooks) finden Sie Informationen zu den ersten Schritten mit Azure Machine Learning und Jupyter Notebook-Instanzen.

> [!NOTE]
> Ein Communityrepository mit Beispielen finden Sie unter https://github.com/Azure/azureml-examples.

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

So verwenden Visual Studio Code für die Entwicklung

1. Installieren Sie [Visual Studio Code](https://code.visualstudio.com/Download).
1. Installieren Sie die [Visual Studio Code-Erweiterung für Azure Machine Learning (Vorschau)](tutorial-setup-vscode-extension.md).

Sobald Sie die Visual Studio Code-Erweiterung installiert haben, können Sie Ihre [Azure Machine Learning-Ressourcen](how-to-manage-resources-vscode.md) verwalten, [Experimente ausführen und debuggen](how-to-debug-visual-studio-code.md) und [trainierte Modelle bereitstellen](tutorial-train-deploy-image-classification-model-vscode.md).

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Azure Machine Learning-Computeinstanz

Die Azure Machine Learning-[Compute-Instanz](concept-compute-instance.md) ist eine sichere, cloudbasierte Azure-Arbeitsstation, die wissenschaftlichen Fachkräften für Daten einen Jupyter Notebook-Server, JupyterLab und eine vollständige Machine Learning-Umgebung bereitstellt.

Für eine Compute-Instanz muss nichts installiert oder konfiguriert werden.  

In Ihrem Azure Machine Learning-Arbeitsbereich können Sie jederzeit eine Notebook-VM erstellen. Geben Sie einfach einen Namen und einen Azure-VM-Typ an. Sie die Einrichtung unter [Tutorial: Einrichten der Umgebung und des Arbeitsbereichs](tutorial-1st-experiment-sdk-setup.md).

Weitere Informationen zu Compute-Instanzen, einschließlich der Installation von Paketen, finden Sie unter [Erstellen und Verwalten einer Azure Machine Learning-Compute-Instanz](how-to-create-manage-compute-instance.md).

> [!TIP]
> [Halten Sie die Compute-Instanz an](how-to-create-manage-compute-instance.md#manage), um wiederkehrende Kosten für eine nicht genutzte Compute-Instanz zu vermeiden.

Zusätzlich zu einem Jupyter Notebook-Server und JupyterLab können Sie Compute-Instanzen im [integrierten Notebook-Feature in Azure Machine Learning Studio](how-to-run-jupyter-notebooks.md) verwenden.

Sie können auch die Visual Studio Code-Erweiterung für Azure Machine Learning verwenden, um [eine Azure Machine Learning-Compute-Instanz als Remoteserver für Jupyter Notebook zu konfigurieren](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Data Science Virtual Machine

Die Data Science VM (DSVM) ist ein angepasstes VM-Image, das Sie als Entwicklungsumgebung verwenden können. Sie ist für Data-Science-Aufgaben konzipiert und mit folgenden Tools und folgender Software vorab konfiguriert:

  - Pakete wie TensorFlow, PyTorch, Scikit-learn, XGBoost und das Azure Machine Learning SDK.
  - Beliebte Data Science-Tools wie Spark (eigenständig) und Drill.
  - Azure-Tools wie die Azure CLI, AzCopy und Storage-Explorer.
  - Integrierte Entwicklungsumgebungen (IDEs), z.B. Visual Studio Code und PyCharm.
  - Jupyter Notebook-Server

Eine umfassendere Liste der Tools finden Sie unter [Welche Tools sind in Azure Data Science Virtual Machine enthalten?](data-science-virtual-machine/tools-included.md).

> [!IMPORTANT]
> Wenn Sie eine DSVM als [Computeziel](concept-compute-target.md) für Trainings- oder Rückschlussaufträge verwenden möchten, wird nur Ubuntu unterstützt.

So verwenden Sie eine DSVM als Entwicklungsumgebung

1. Erstellen Sie mithilfe einer der folgenden Methoden eine DSVM:

    * Verwenden Sie das Azure-Portal, um eine DSVM-Instanz für [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) oder [Windows](data-science-virtual-machine/provision-vm.md) zu erstellen.
    * [Erstellen Sie eine DSVM mithilfe von ARM-Vorlagen.](data-science-virtual-machine/dsvm-tutorial-resource-manager.md)
    * Verwenden der Azure-CLI

        Verwenden Sie zum Erstellen einer DSVM unter Ubuntu den folgenden Befehl:

        ```azurecli-interactive
        # create a Ubuntu Data Science VM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Verwenden Sie den folgenden Befehl, um eine DSVM-Instanz für Windows zu erstellen:

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Aktivieren Sie die Conda-Umgebung, die das Azure Machine Learning SDK enthält.

    * Für eine DSVM mit Ubuntu:

        ```bash
        conda activate py36
        ```

    * Für eine DSVM mit Windows:

        ```bash
        conda activate AzureML
        ```

1. [Erstellen Sie eine Konfigurationsdatei für den Arbeitsbereich](#workspace), oder verwenden Sie eine vorhandene, um die DSVM so zu konfigurieren, dass der Azure Machine Learning-Arbeitsbereich genutzt wird.

Ähnlich wie bei lokalen Umgebungen können Sie Visual Studio Code und die [Visual Studio Code-Erweiterung für Azure Machine Learning](#vscode) verwenden, um mit Azure Machine Learning zu interagieren.

Weitere Informationen finden Sie unter [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).


## <a name="next-steps"></a>Nächste Schritte

- [Trainieren Sie ein Modell](tutorial-train-models-with-aml.md) in Azure Machine Learning mit dem MNIST-Dataset.
- Weitere Informationen erhalten Sie in der [Referenz zum Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). 