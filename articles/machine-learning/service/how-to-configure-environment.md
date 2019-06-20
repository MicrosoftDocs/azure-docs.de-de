---
title: Einrichten einer Python-Entwicklungsumgebung
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie eine Entwicklungsumgebung für die Arbeit mit dem Azure Machine Learning Service konfigurieren. In diesem Artikel erfahren Sie, wie Sie Conda-Umgebungen verwenden, Konfigurationsdateien erstellen und Ihren eigenen cloudbasierten Notebook-Server, Jupyter Notebooks, Azure Notebooks, Azure Databricks, Azure Notebooks, IDEs, Code-Editoren und die Data Science Virtual Machine konfigurieren.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 7be6c9eda6d0a70d929efe4c00f661eb67105820
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606414"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurieren einer Entwicklungsumgebung für Azure Machine Learning

In diesem Artikel erfahren Sie, wie Sie eine Entwicklungsumgebung für die Arbeit mit dem Azure Machine Learning Service konfigurieren. Der Machine Learning Service ist plattformunabhängig.

Die einzigen Voraussetzungen für die Entwicklungsumgebung sind Python 3, Anaconda (für isolierte Umgebungen) und eine Konfigurationsdatei, die Informationen zu Ihrem Azure Machine Learning-Arbeitsbereich enthält.

In diesem Artikel werden in erster Linie die folgenden Umgebungen und Tools behandelt:

* Ihre eigene [cloudbasierte Notebook-VM](#notebookvm): Verwenden Sie zum Ausführen von Jupyter-Notebooks eine Computeressource auf Ihrer Arbeitsstation. Dies ist die einfachste Methode für den Einstieg, da das Azure Machine Learning SDK bereits installiert ist.

* [Data Science Virtual Machine (DSVM)](#dsvm): Eine vorkonfigurierte Entwicklungs- bzw. Experimentierumgebung in der Azure-Cloud, die für Data Science-Zwecke konzipiert ist und entweder auf ausschließlich CPU-basierten VM-Instanzen oder GPU-basierten Instanzen bereitgestellt werden kann. Python 3, Conda, Jupyter Notebooks und das Azure Machine Learning-SDK sind bereits installiert. Der virtuelle Computer ist mit gängigen Machine Learning- bzw. Deep Learning-Frameworks, -Tools und -Editoren zum Entwickeln von Lösungen für maschinelles Lernen ausgestattet. Dies ist wahrscheinlich die umfassendste Entwicklungsumgebung für maschinelles Lernen auf der Azure-Plattform.

* [Jupyter Notebook](#jupyter): Wenn Sie bereits ein Jupyter Notebook verwenden, enthält das SDK einige zusätzliche Elemente, die Sie installieren sollten.

* [Visual Studio Code](#vscode): Wenn Sie Visual Studio Code verwenden, bietet es einige nützliche Erweiterungen, die Sie installieren können.

* [Azure Databricks](#aml-databricks): Eine beliebte Datenanalyseplattform, die auf Apache Spark basiert. Erfahren Sie, wie Sie das Azure Machine Learning SDK in Ihren Cluster einbinden, um Modelle bereitzustellen.

* [Azure Notebooks](#aznotebooks): Ein in der Azure-Cloud gehosteter Jupyter Notebooks-Dienst. Außerdem eine einfache Methode für den Einstieg, da das Azure Machine Learning SDK bereits installiert ist.  

Wenn Sie bereits über eine Python 3-Umgebung verfügen oder nur die grundlegenden Schritte zum Installieren des SDK erhalten möchten, finden Sie weitere Informationen im Abschnitt [Lokaler Computer](#local).

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure Machine Learning-Dienstbereich. Informationen zum Erstellen des Arbeitsbereichs finden Sie unter [Erstellen eines Azure Machine Learning Service-Arbeitsbereichs](setup-create-workspace.md). Ein Arbeitsbereich ist alles, was Sie zum Einstieg in Ihren eigenen [cloudbasierten Notebook-Server](#notebookvm), eine [DSVM](#dsvm), [Azure Databricks](#aml-databricks) oder [Azure Notebooks](#aznotebooks) benötigen.

Um die SDK-Umgebung für Ihren [lokalen Computer](#local), Ihren [Jupyter Notebook-Server](#jupyter) oder Ihr [Visual Studio Code](#vscode) zu installieren, benötigen Sie außerdem:

- Den Paket-Manger [Anaconda](https://www.anaconda.com/download/) oder [Miniconda](https://conda.io/miniconda.html).

- Unter Linux oder macOS benötigen Sie die Bash-Shell.

    > [!TIP]
    > Wenn Sie unter Linux oder macOS eine andere Shell als Bash verwenden (z.B. zsh), werden beim Ausführen einiger Befehle möglicherweise Fehler ausgegeben. Um dieses Problem zu umgehen, starten Sie mit dem Befehl `bash` eine neue Bash-Shell starten, und führen Sie die Befehle in dieser aus.

- Unter Windows benötigen Sie die Eingabeaufforderung oder die Anaconda-Eingabeaufforderung (installiert mit Anaconda und Miniconda).

## <a id="notebookvm"></a>Ihre eigene cloudbasierte Notebook-VM

Die Notebook-VM (Vorschauversion) ist eine sichere, cloudbasierte Azure-Arbeitsstation, die Data Scientists einen Jupyter Notebook-Server, JupyterLab und eine vollständig vorbereitete Machine Learning-Umgebung bereitstellt. 

Die Notebook-VM ist: 

+ **Sicher.** Da der Zugriff auf VMs und Notebooks standardmäßig mit HTTPS und Azure Active Directory gesichert wird, können IT-Experten das einmalige Anmelden und andere Sicherheitsfunktionen wie die mehrstufige Authentifizierung einfach erzwingen.

+ **Vorkonfiguriert:** Diese vollständig einsatzbereite Machine Learning-Umgebung für Python zeichnet sich durch die beliebte IaaS-Umgebung Data Science VM aus und enthält Folgendes:
  + das aktuellste Azure Machine Learning-Python-SDK
  + eine automatische Konfiguration für Ihren Arbeitsbereich
  + einen Jupyter Notebook-Server
  + die JupyterLab-Notebook-IDE
  + vorkonfigurierte GPU-Treiber 
  + einige Deep Learning-Frameworks
 

  Wenn Sie das Programmieren bevorzugen, können Sie die Tutorials und Beispiele der VM nutzen, um sich in die Verwendung von Machine Learning Service einzuarbeiten. Die Beispielnotebooks sind im Azure Blob Storage-Konto Ihres Arbeitsbereichs gespeichert und können für VMs freigegeben werden. Bei der Ausführung können sie auf die Datenspeicher und die Computeressourcen Ihres Arbeitsbereichs zugreifen. 

+ **Schnell eingerichtet:** In Ihrem Azure Machine Learning-Arbeitsbereich können Sie jederzeit eine Notebook-VM erstellen. Geben Sie einfach einen Namen und einen Azure-VM-Typ an. Testen Sie die Einrichtung unter [Schnellstart: Verwenden eines cloudbasierten Notebook-Servers für die ersten Schritte mit Azure Machine Learning](quickstart-run-cloud-notebook.md).

+ **Anpassbar:** Das VM-Angebot ist schnell und sicher, bietet jedoch gleichzeitig einen uneingeschränkten Zugriff auf die Hardwarefunktionen, die Sie nach Bedarf konfigurieren können. Sie können beispielsweise in kurzer Zeit die neueste NVidia V100-VM erstellen, um neue neuronale Netzarchitekturen Schritt für Schritt zu debuggen.

Wenn Sie vermeiden möchten, dass Gebühren für die Notebook-VM anfallen, müssen Sie diese [beenden](quickstart-run-cloud-notebook.md#stop-the-notebook-vm). 

## <a id="dsvm"></a>Data Science Virtual Machine

Die DSVM ist ein benutzerdefiniertes VM-Image. Sie ist für Data Science-Aufgaben konzipiert, die mit Folgendem vorab konfiguriert wurden:

  - Pakete wie TensorFlow, PyTorch, Scikit-learn, XGBoost und das Azure Machine Learning SDK.
  - Beliebte Data Science-Tools wie Spark (eigenständig) und Drill.
  - Azure-Tools wie die Azure CLI, AzCopy und Storage-Explorer.
  - Integrierte Entwicklungsumgebungen (IDEs), z.B. Visual Studio Code und PyCharm.
  - Jupyter Notebook-Server

Das Azure Machine Learning SDK funktioniert mit der Ubuntu- und der Windows-Version der DSVM. Wenn Sie die DSVM jedoch als Computeziel verwenden möchten, wird nur Ubuntu unterstützt.

Um die DSVM als Entwicklungsumgebung zu verwenden, gehen Sie folgendermaßen vor:

1. Erstellen Sie in einer der folgenden Umgebungen eine DSVM:

    * Azure-Portal:

        * [Erstellen einer Ubuntu-Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Erstellen einer Windows-Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI:

        > [!IMPORTANT]
        > * Wenn Sie die Azure CLI verwenden, müssen Sie sich zuerst mit dem Befehl `az login` bei Ihrem Azure-Abonnement anmelden.
        >
        > * Wenn Sie die Befehle in diesem Schritt ausführen, müssen Sie einen Ressourcengruppennamen, einen Namen für die VM, einen Benutzernamen und ein Kennwort angeben.

        * Verwenden Sie zum Erstellen einer Data Science Virtual Machine-Instanz unter Ubuntu den folgenden Befehl:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Verwenden Sie zum Erstellen einer Data Science Virtual Machine-Instanz unter Windows den folgenden Befehl:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Das Azure Machine Learning SDK ist auf der DSVM bereits installiert. Um die Conda-Umgebung mit dem SDK zu verwenden, führen Sie einen der folgenden Befehle aus:

    * Für die DSVM unter Ubuntu:

        ```shell
        conda activate py36
        ```

    * Für die DSVM unter Windows:

        ```shell
        conda activate AzureML
        ```

1. Um sicherzustellen, dass Sie auf das SDK zugreifen und die Version überprüfen können, verwenden Sie den folgenden Python-Code:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Informationen zum Konfigurieren der DSVM für Ihren Azure Machine Learning Service-Arbeitsbereich finden Sie im Abschnitt [Erstellen einer Konfigurationsdatei für den Arbeitsbereich](#workspace).

Weitere Informationen finden Sie unter [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Lokaler Computer

Wenn Sie einen lokalen Computer verwenden (dabei kann es sich auch um einen virtuellen Remotecomputer handeln), gehen Sie folgendermaßen vor, um eine Anaconda-Umgebung zu erstellen und das SDK zu installieren:

1. Laden Sie [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python-Version 3.7) herunter, und installieren Sie die Distribution, falls Sie diese noch nicht besitzen.

1. Öffnen Sie eine Anaconda-Eingabeaufforderung, und erstellen Sie mit den folgenden Befehlen eine Umgebung:

    Führen Sie den folgenden Befehl aus, um die Umgebung zu erstellen.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Aktivieren Sie anschließend die Umgebung.

    ```shell
    conda activate myenv
    ```

    In diesem Beispiel wird eine Umgebung mit Python 3.6.5 erstellt. Es kann jedoch auch eine andere Nebenversion verwendet werden. Bei bestimmten Hauptversionen kann die SDK-Kompatibilität nicht garantiert werden. Empfohlen wird daher die Version 3.5 oder höher. Wenn Fehler auftreten, sollten Sie außerdem eine andere Version oder Nebenversion in Ihrer Anaconda-Umgebung testen. Es dauert einige Minuten, bis die Komponenten und Pakete heruntergeladen wurden und die Umgebung erstellt wurde.

1. Führen Sie die folgenden Befehle in der neuen Umgebung aus, um umgebungsspezifische IPython-Kernel zu aktivieren. Dadurch wird das erwartete Kernel- und Paketimportverhalten beim Arbeiten mit Jupyter-Notebooks in Anaconda-Umgebungen sichergestellt:

    ```shell
    conda install notebook ipykernel
    ```

    Führen Sie nun den folgenden Befehl zum Erstellen des Kernels aus:

    ```shell
    ipython kernel install --user
    ```

1. Verwenden Sie die folgenden Befehle, um die Pakete zu installieren:

    Durch diesen Befehl wird das grundlegende Azure Machine Learning SDK mit Notebooktools und zusätzlichen automl-Komponenten installiert. Da das `automl`-Zusatzpaket sehr umfangreich ist, müssen Sie dieses nicht innerhalb der Klammern angeben und installieren, wenn Sie keine automatisierten Machine Learning-Experimente durchführen möchten. Das `automl`-Zusatzpaket enthält standardmäßig auch das Azure Machine Learning Data Prep SDK als Abhängigkeit.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    Verwenden Sie den folgenden Befehl, um nur das Azure Machine Learning Data Prep SDK zu installieren:

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > Wenn Sie eine Meldung mit dem Hinweis erhalten, dass PyYAML nicht deinstalliert werden kann, verwenden Sie stattdessen den folgenden Befehl:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Die Installation des SDK nimmt mehrere Minuten in Anspruch. Weitere Informationen zu den Installationsoptionen finden Sie in der [Installationsanleitung](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Installieren Sie weitere Pakete für Ihre Machine Learning-Experimente.

    Verwenden Sie einen der folgenden Befehle, und ersetzen Sie *\<new package>* durch das zu installierende Paket: Wenn Sie mit `conda install` Pakete installieren, müssen diese Teil der aktuellen Kanäle sein. Neue Kanäle können in Anaconda Cloud hinzugefügt werden.

    ```shell
    conda install <new package>
    ```

    Alternativ können Sie Pakete mit `pip` installieren.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebooks

Jupyter Notebooks sind Teil des [Jupyter-Projekts](https://jupyter.org/). Sie stellen eine interaktive Programmierumgebung bereit, mit der Sie Dokumente erstellen können, in denen Livecode mit beschreibendem Text und Grafiken gemischt ist. Jupyter Notebooks sind auch eine hervorragende Möglichkeit, Ihre Ergebnisse mit anderen Personen zu teilen, da Sie die Ausgabe Ihrer Codeabschnitte im Dokument speichern können. Sie können Jupyter Notebooks auf vielen unterschiedlichen Plattformen installieren.

Durch die Vorgehensweise im Abschnitt [Lokaler Computer](#local) werden die erforderlichen Komponenten zum Ausführen von Jupyter-Notebooks in einer Anaconda-Umgebung installiert. Um diese Komponenten in Ihrer Jupyter Notebook-Umgebung zu aktivieren, gehen Sie folgendermaßen vor:

1. Öffnen Sie eine Anaconda-Eingabeaufforderung, und aktivieren Sie Ihre Umgebung.

    ```shell
    conda activate myenv
    ```

1. Starten Sie den Jupyter Notebook-Server mit dem folgenden Befehl:

    ```shell
    jupyter notebook
    ```

1. Erstellen Sie mit **New** (Neu) ein neues Notebook, wählen Sie **Python 3** als Kernel aus, und führen Sie dann folgenden Befehl in einer Notebookzelle aus, um zu überprüfen, ob Jupyter Notebook das SDK verwenden kann:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Wenn beim Importieren von Modulen Probleme auftreten und `ModuleNotFoundError` angezeigt wird, müssen Sie sicherstellen, dass für den Jupyter-Kernel der richtige Umgebungspfad angegeben ist. Führen Sie dazu folgenden Code in einer Notebookzelle aus:

    ```python
    import sys
    sys.path
    ```

1. Informationen zum Konfigurieren des Jupyter Notebooks für Ihren Azure Machine Learning Service-Arbeitsbereich finden Sie im Abschnitt [Erstellen einer Konfigurationsdatei für den Arbeitsbereich](#workspace).

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code ist ein Code-Editor für alle Plattformen. Für Python-Unterstützung benötigt er eine lokale Python 3- und Conda-Installation, aber er bietet zusätzliche Tools für die Arbeit mit künstlicher Intelligenz (KI). Darüber hinaus bietet er Unterstützung für die Auswahl der Conda-Umgebung aus dem Code-Editor heraus.

Um Visual Studio Code für die Entwicklung zu verwenden, gehen Sie folgendermaßen vor:

1. Informationen zur Verwendung von Visual Studio Code für die Python-Entwicklung finden Sie unter [Erste Schritte mit Python in VS Code](https://code.visualstudio.com/docs/python/python-tutorial).

1. Um die Conda-Umgebung auszuwählen, öffnen Sie VS Code, und drücken Sie die Tastenkombination STRG+UMSCHALT+P (Linux und Windows) bzw. BEFEHL+UMSCHALT+P (Mac).
    Die __Befehlspalette__ wird geöffnet.

1. Geben Sie __Python: Select Interpreter__ ein, und wählen Sie dann die Conda-Umgebung aus.

1. Um sicherzustellen, dass Sie das SDK verwenden können, erstellen Sie eine neue Python-Datei (.py), die folgenden Code enthält, und führen Sie sie aus:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Installationsanweisungen für die Azure Machine Learning-Erweiterung für Visual Studio Code finden Sie unter [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) (Tools für KI).

    Weitere Informationen finden Sie unter [Verwenden von Azure Machine Learning für Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks ist eine Apache Spark-basierte Umgebung in der Azure-Cloud. Die Plattform stellt für die Zusammenarbeit eine Notebook-basierte Umgebung mit CPU- oder GPU-basierten Computeclustern bereit.

Azure Databricks kann auf folgende Weisen mit Azure Machine Learning Service verwendet werden:
+ Sie können mit Spark MLlib ein Modell trainieren und es über Azure Databricks in ACI/AKS bereitstellen. 
+ Sie können mithilfe eines speziellen Azure ML SDK außerdem Features für [automatisiertes Machine Learning](concept-automated-ml.md) mit Azure Databricks verwenden.
+ Sie können Azure Databricks über eine [Azure Machine Learning-Pipeline](concept-ml-pipelines.md) als Computeziel verwenden. 

### <a name="set-up-your-databricks-cluster"></a>Einrichten des Databricks-Clusters

Erstellen Sie einen [Databricks-Cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Einige Einstellungen sind nur erforderlich, wenn Sie das SDK für automatisiertes Machine Learning in Databricks verwenden.
**Die Erstellung des Clusters dauert einige Minuten.**

Verwenden Sie die folgenden Einstellungen:

| Einstellung |Anwendungsbereich| Wert |
|----|---|---|
| Clustername |immer| IhrClustername |
| Databricks-Laufzeit |immer| Beliebige Nicht-ML-Runtime (nicht ML 4.x, 5.x) |
| Python-Version |immer| 3 |
| Worker |immer| 2 oder mehr |
| VM-Typen des Workerknotens <br>(bestimmt die maximale Anzahl gleichzeitiger Iterationen) |Automatisiertes maschinelles Lernen<br>Machine Learning| Arbeitsspeicheroptimierte VM bevorzugt |
| Automatische Skalierung aktivieren |Automatisiertes maschinelles Lernen<br>Machine Learning| Deaktivieren |

Warten Sie, bis der Cluster ausgeführt wird, bevor Sie fortfahren.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installieren des richtigen SDK in einer Databricks-Bibliothek
Erstellen Sie nach der Ausführung des Clusters [eine Bibliothek](https://docs.databricks.com/user-guide/libraries.html#create-a-library), um das entsprechende Azure Machine Learning SDK-Paket Ihrem Cluster anzufügen. 

1. Wählen Sie **nur eine** Option aus (weitere SDK-Installationen werden nicht unterstützt).

   |Zusatzkomponenten für &nbsp;SDK-Paket&nbsp;|`Source`|&nbsp;PyPi-Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Für Databricks| Python Egg oder PyPI hochladen | azureml-sdk[databricks]|
   |Für Databricks mit<br> Features für automatisiertes Machine Learning| Python Egg oder PyPI hochladen | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Sie können keine weiteren SDK-Zusatzkomponenten installieren. Wählen Sie nur eine der zuvor beschriebenen Optionen [databricks] oder [automl_databricks] aus.

   * Wählen Sie nicht **Attach automatically to all clusters** (Automatisch an alle Cluster anfügen) aus.
   * Wählen Sie **Anfügen** neben dem Namen Ihres Clusters aus.

1. Der Status wird in **Angefügt** geändert. Dieser Vorgang kann einige Minuten in Anspruch nehmen. Überprüfen Sie währenddessen, ob Fehler auftreten.  Führen Sie folgende Aktionen aus, wenn dieser Schritt fehlschlägt: 

   Versuchen Sie, Ihren Cluster wie folgt neu zu starten:
   1. Wählen Sie im linken Bereich die Option **Cluster** aus.
   1. Wählen Sie in der Tabelle den Namen Ihres Clusters aus.
   1. Klicken Sie auf der Registerkarte **Bibliotheken** auf **Neu starten**.
      
   Berücksichtigen Sie außerdem Folgendes:
   + In der Automl-Konfiguration fügen Sie bei der Verwendung von Azure Databricks die folgenden Parameter hinzu:
       1. ```max_concurrent_iterations``` basiert auf der Anzahl der Workerknoten in Ihrem Cluster. 
        2. ```spark_context=sc``` basiert auf dem standardmäßigen Spark-Kontext. 
   + Wenn Sie alternativ dazu eine alte SDK-Version nutzen, deaktivieren Sie diese in den installierten Bibliotheken des Clusters, und verschieben Sie sie in den Papierkorb. Installieren Sie die neue SDK-Version, und starten Sie den Cluster neu. Wenn danach ein Problem vorliegt, trennen Sie Ihren Cluster, und fügen Sie ihn wieder an.

Wenn die Installation erfolgreich war, sollte die importierte Bibliothek wie auf einem der folgenden Screenshots gezeigt aussehen:
   
SDK für Databricks **_ohne_** ![Azure Machine Learning SDK für Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) und automatisiertes Machine Learning

SDK für Databricks **mit** ![installiertem SDK für automatisiertes Machine Learning in Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Ausprobieren

So können Sie Azure Databricks testen:
+ Laden Sie die [Notebookarchivdatei](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) für das Azure Databricks/Azure Machine Learning SDK herunter, und [importieren Sie diese](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) in Ihren Databricks-Cluster.  
  Von den vielen verfügbaren Beispielnotebooks können **nur [ganz bestimmte](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) mit Azure Databricks verwendet werden.**
  
+ Erfahren Sie, wie Sie [mit Databricks als Computeziel für das Trainieren von Modellen eine Pipeline erstellen](how-to-create-your-first-pipeline.md).

## <a id="aznotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (Vorschauversion) ist eine interaktive Umgebung in der Azure-Cloud. Dies ist eine einfache Methode für den Einstieg in die Azure Machine Learning-Entwicklung.

* Das Azure Machine Learning SDK ist bereits installiert.
* Nachdem Sie im Azure-Portal einen Arbeitsbereich für den Azure Machine Learning Service erstellt haben, können Sie über eine Schaltfläche Ihre Azure Notebook-Umgebung automatisch für die Arbeit mit dem Arbeitsbereich konfigurieren.

Verwenden Sie das [Azure-Portal](https://portal.azure.com), um das Arbeiten mit Azure Notebooks zu beginnen.  Öffnen Sie Ihren Arbeitsbereich, und wählen Sie im Abschnitt **Übersicht** die Option **Erste Schritte in Azure Notebooks** aus.

Standardmäßig verwendet Azure Notebooks eine kostenlose Dienstebene, die auf 4GB Arbeitsspeicher und 1GB Daten beschränkt ist. Sie können diese Grenzwerte allerdings entfernen, indem Sie dem Azure Notebooks-Projekt eine Data Science Virtual Machine-Instanz anfügen. Weitere Informationen finden Sie unter [Verwalten und Konfigurieren von Projekten – Compute-Tarif](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a id="workspace"></a>Erstellen einer Konfigurationsdatei für den Arbeitsbereich

Die Arbeitsbereichs-Konfigurationsdatei ist eine JSON-Datei, die das SDK zur Kommunikation mit Ihrem Azure Machine Learning-Dienstarbeitsbereich instruiert. Die Datei weist den Namen *config.json* und das folgende Format auf:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Diese JSON-Datei muss sich in der Verzeichnisstruktur befinden, die Ihre Python-Skripts oder Jupyter Notebooks enthält. Sie kann sich im selben Verzeichnis, in einem Unterverzeichnis namens *.azureml* oder in einem übergeordneten Verzeichnis befinden.

Um in Ihrem Code auf diese Datei zuzugreifen, verwenden Sie `ws=Workspace.from_config()`. Dieser Code lädt die Informationen aus der Datei und stellt eine Verbindung mit Ihrem Arbeitsbereich her.

Sie können die Konfigurationsdatei auf drei Arten erstellen:

* **Befolgen Sie die Schritte unter [Erstellen eines Azure Machine Learning Service-Arbeitsbereichs](setup-create-workspace.md#sdk)** : In Ihrer Azure Notebooks-Bibliothek wird eine *config.json*-Datei erstellt. Diese Datei enthält die Konfigurationsinformationen für Ihren Arbeitsbereich. Sie können die Datei *config.json* in andere Entwicklungsumgebungen herunterladen oder kopieren.

* **Datei herunterladen**: Wählen Sie im [Azure-Portal](https://ms.portal.azure.com) im Abschnitt **Übersicht** Ihres Arbeitsbereichs **config.json herunterladen** aus.

     ![Azure-Portal](./media/how-to-configure-environment/configure.png)

* **Erstellen Sie die Datei programmgesteuert**: Im folgenden Codeausschnitt stellen Sie eine Verbindung mit einem Arbeitsbereich her, indem Sie die Abonnement-ID, die Ressourcengruppe und den Arbeitsbereichsnamen angeben. Anschließend speichert der Code die Arbeitsbereichskonfiguration in dieser Datei:

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

    Dieser Code schreibt die Konfigurationsdatei in die Datei *.azureml/config.json*.


## <a name="next-steps"></a>Nächste Schritte

- [Trainieren eines Modells](tutorial-train-models-with-aml.md) in Azure Machine Learning mit der MNIST-Datenbank
- Anzeigen der Referenz [Azure Machine Learning SDK für Python](https://aka.ms/aml-sdk)
- Informationen zum [Datenvorbereitungspaket für Azure Machine Learning](https://aka.ms/data-prep-sdk)
