---
title: Schützen von Trainingsumgebungen mit virtuellen Netzwerken
titleSuffix: Azure Machine Learning
description: Verwenden Sie eine isolierte Azure Virtual Network-Instanz, um Ihre Azure Machine Learning-Trainingsumgebung zu schützen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 131feaf6ff01659b7d126604a5d081275e64508f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029565"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>Schützen einer Azure Machine Learning-Trainingsumgebung mit virtuellen Netzwerken

In diesem Artikel erfahren Sie, wie Sie Trainingsumgebungen mit einem virtuellen Netzwerk in Azure Machine Learning schützen.

Der Artikel ist Teil 3 einer fünfteiligen Artikelreihe, in der Sie schrittweise durch die Absicherung eines Azure Machine Learning-Workflows geführt werden. Es wird dringend empfohlen, zunächst [Teil 1: Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md) zu lesen, um die Gesamtarchitektur besser zu verstehen. 

Sehen Sie sich auch die anderen Artikel in dieser Reihe an:

[1. Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md) > [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md) > **3. Schützen der Trainingsumgebung** > [4. Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)  > [5. Verwenden von Studio in einem virtuellen Netzwerk](how-to-enable-studio-virtual-network.md)

In diesem Artikel erfahren Sie, wie Sie die folgenden Trainingscomputeressourcen in einem virtuellen Netzwerk schützen:
> [!div class="checklist"]
> - Azure Machine Learning-Computecluster
> - Azure Machine Learning-Computeinstanz
> - Azure Databricks
> - Virtual Machine
> - HDInsight-Cluster

## <a name="prerequisites"></a>Voraussetzungen

+ Im Artikel [Übersicht über die Netzwerksicherheit](how-to-network-security-overview.md) finden Sie Informationen zu gängigen Szenarien im Zusammenhang mit virtuellen Netzwerken sowie zur Gesamtarchitektur virtueller Netzwerke.

+ Ein vorhandenes virtuelles Netzwerk und Subnetz, die mit Ihren Computeressourcen verwendet werden können

+ Ihr Benutzerkonto muss über die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) zu den folgenden Aktionen berechtigt werden, um Ressourcen in einem virtuellen Netzwerk oder Subnetz bereitstellen zu können:

    - „Microsoft.Network/virtualNetworks/join/action“ auf der virtuellen Netzwerkressource
    - „Microsoft.Network/virtualNetworks/subnet/join/action“ auf der Subnetzressource

    Weitere Informationen zur rollenbasierten Zugriffssteuerung von Azure in Netzwerken finden Sie unter [Integrierte Netzwerkrollen](../role-based-access-control/built-in-roles.md#networking).


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Computecluster und -instanzen 

Wenn Sie ein [verwaltetes Azure Machine Learning-__Computeziel__](concept-compute-target.md#azure-machine-learning-compute-managed) oder eine [Azure Machine Learning-__Computeinstanz__](concept-compute-instance.md) in einem virtuellen Netzwerk verwenden möchten, müssen die folgenden Netzwerkanforderungen erfüllt sein:

> [!div class="checklist"]
> * Das virtuelle Netzwerk muss sich im selben Abonnement und in derselben Region befinden wie der Azure Machine Learning-Arbeitsbereich.
> * Das für die Computeinstanz oder den Computecluster angegebene Subnetz muss für die vorgesehene Anzahl virtueller Computer über genügend nicht zugewiesene IP-Adressen verfügen. Falls das Subnetz nicht genügend zugewiesene IP-Adressen hat, wird ein Computecluster teilweise zugeordnet.
> * Überprüfen Sie, ob Berechtigungen für die Verwaltung des virtuellen Netzwerks durch Ihre Sicherheitsrichtlinien oder -sperren für das Abonnement oder die Ressourcengruppe Ihres virtuellen Netzwerks eingeschränkt werden. Wenn Sie zum Schutz des virtuellen Netzwerks den Datenverkehr einschränken möchten, lassen Sie einige Ports für den Compute-Dienst geöffnet. Weitere Informationen finden Sie im Abschnitt [Erforderliche Ports](#mlcports).
> * Wenn Sie mehrere Compute-Instanzen oder -cluster in einem einzelnen virtuellen Netzwerk platzieren möchten, müssen Sie möglicherweise eine Kontingenterhöhung für eine oder mehrere Ihrer Ressourcen anfordern.
> * Wenn die Azure Storage-Konten für den Arbeitsbereich ebenfalls in einem virtuellen Netzwerk geschützt sind, müssen sie sich im selben virtuellen Netzwerk befinden wie die Azure Machine Learning Compute-Instanz oder der Cluster. 
> * Damit die Jupyter-Funktionen für Computeinstanzen eingesetzt werden können, stellen Sie sicher, dass die Websocketkommunikation nicht deaktiviert ist. Stellen Sie sicher, dass Ihr Netzwerk WebSocket-Verbindungen mit *.instances.azureml.net und *.instances.azureml.ms zulässt. 
> * Wenn eine Compute-Instanz in einem Private Link-Arbeitsbereich bereitgestellt wird, kann nur im virtuellen Netzwerk darauf zugegriffen werden. Wenn Sie benutzerdefiniertes DNS oder HOSTS-Dateien verwenden, fügen Sie einen Eintrag für `<instance-name>.<region>.instances.azureml.ms` mit der privaten IP-Adresse des privaten Endpunkts des Arbeitsbereichs hinzu. Weitere Informationen finden Sie im Artikel [Benutzerdefiniertes DNS](./how-to-custom-dns.md).
    
> [!TIP]
> Die Machine Learning-Compute-Instanz bzw. der Cluster ordnet __in der Ressourcengruppe mit dem virtuellen Netzwerk__ automatisch zusätzliche Netzwerkressourcen zu. Für alle Compute-Instanzen und -cluster ordnet der Dienst folgende Ressourcen zu:
> 
> * Eine Netzwerksicherheitsgruppe
> * Eine öffentliche IP-Adresse
> * Ein Lastenausgleichsmodul
> 
> Bei Clustern werden diese Ressourcen jedes Mal, wenn der Cluster auf 0 Knoten herunterskaliert wird, gelöscht (und neu erstellt). Die Ressourcen werden jedoch noch einen Moment lang beibehalten, bis die Instanz vollständig gelöscht ist (durch Beenden werden die Ressourcen nicht entfernt). 
> Diese Ressourcen werden durch die [Ressourcenkontingente](../azure-resource-manager/management/azure-subscription-service-limits.md) des Abonnements beschränkt.


### <a name="required-ports"></a><a id="mlcports"></a>Erforderliche Ports

Wenn Sie planen, das virtuelle Netzwerk zu schützen, indem Sie den Netzwerkverkehr zum bzw. aus dem öffentlichen Internet einschränken, müssen Sie eingehende Kommunikation vom Azure Batch-Dienst zulassen.

Der Batch-Dienst fügt Netzwerksicherheitsgruppen (NSG) auf der Ebene der Netzwerkschnittstellen (NIC) hinzu, die an virtuelle Computer angefügt sind. Diese NSGs konfigurieren automatisch Eingangs- und Ausgangsregeln, um folgenden Datenverkehr zuzulassen:

- Eingehender TCP-Datenverkehr an den Ports 29876 und 29877 aus einem __Diensttag__ von __BatchNodeManagement__.

    ![Eine Regel für eingehenden Datenverkehr, die das BatchNodeManagement-Diensttag verwendet.](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Optional) Eingehender TCP-Datenverkehr an Port 22, um Remotezugriff zu ermöglichen. Verwenden Sie diesen Port nur, wenn Sie mithilfe von SSH über die öffentliche IP-Adresse eine Verbindung herstellen möchten.

- Ausgehender Datenverkehr an allen Ports zum virtuellen Netzwerk

- Ausgehender Datenverkehr an allen Ports zum Internet

- Für eingehenden TCP-Datenverkehr für Computeinstanzen an Port 44224 vom __Diensttag__ von __AzureMachineLearning__.

> [!IMPORTANT]
> Seien Sie vorsichtig, wenn Sie Eingangs- und Ausgangsregeln in von Batch konfigurierten NSGs ändern. Wenn eine NSG die Kommunikation mit den Computeknoten blockiert, legt der Compute-Dienst den Zustand der Computeknoten auf „Nicht verwendbar“ fest.
>
> Sie müssen keine NSGs auf der Subnetzebene angeben, da der Azure Batch-Dienst eigene NSGs konfiguriert. Wenn dem Subnetz, in dem sich das Azure Machine Learning-Compute befindet, jedoch NSGs oder eine Firewall zugeordnet sind, müssen Sie auch den zuvor aufgelisteten Datenverkehr zulassen.

Die NSG-Regelkonfiguration im Azure-Portal wird in den folgenden Abbildungen dargestellt:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Die NSG-Eingangsregeln für Machine Learning Compute" border="true":::



![NSG-Eingangsregeln für Machine Learning Compute](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> Beschränken ausgehender Verbindungen aus dem virtuellen Netzwerk

Wenn Sie die Standardausgangsregeln nicht verwenden möchten und den ausgehenden Zugriff auf Ihr virtuelles Netzwerk beschränken möchten, gehen Sie wie folgt vor:

- Verweigern Sie ausgehende Internetverbindungen mit NSG-Regeln.

- Beschränken Sie für eine __Compute-Instanz__ oder einen __Computecluster__ den ausgehenden Verkehr auf die folgenden Elemente:
   - Azure Storage mithilfe der __Dienstkennung__ von __Storage.RegionName__. Dabei ist `{RegionName}` der Name einer Azure-Region.
   - Azure Container Registry mithilfe der __Dienstkennung__ von __AzureContainerRegistry.RegionName__. Dabei ist `{RegionName}` der Name einer Azure-Region.
   - Azure Machine Learning, mit dem __Diensttag__ von __AzureMachineLearning__
   - Azure Resource Manager mithilfe der __Dienstkennung__ von __AzureResourceManager__
   - Azure Active Directory mithilfe der __Dienstkennung__ von __AzureActiveDirectory__

Die NSG-Regelkonfiguration im Azure-Portal wird in der folgenden Abbildung dargestellt:

[![Die NSG-Ausgangsregeln für Machine Learning Compute](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Wenn Sie planen, die von Microsoft bereitgestellten standardmäßigen Docker-Images zu verwenden und vom Benutzer verwaltete Abhängigkeiten zu aktivieren, müssen Sie auch die folgenden __Diensttags__ verwenden:
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> Diese Konfiguration ist erforderlich, wenn Sie in Ihrem Trainingsskript über Code ähnlich den folgenden Codeausschnitten verfügen:
>
> __RunConfig-Training__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Schätzungstraining__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>Tunnelerzwingung

Wenn Sie das [erzwungene Tunneling](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) mit Azure Machine Learning Compute verwenden, müssen Sie dem Subnetz mit der Computeressource die Kommunikation mit dem öffentlichen Internet erlauben. Diese Kommunikation wird für die Aufgabenplanung und den Zugriff auf Azure Storage verwendet.

Es gibt zwei Möglichkeiten, dies zu erreichen:

* Verwenden Sie [Virtual Network NAT](../virtual-network/nat-overview.md). Ein NAT-Gateway stellt für ein oder mehrere Subnetze in Ihrem virtuellen Netzwerk die ausgehende Internetverbindung bereit. Weitere Informationen hierzu finden Sie unter [Entwerfen von virtuellen Netzwerken mit NAT-Gatewayressourcen](../virtual-network/nat-gateway-resource.md).

* Fügen Sie dem Subnetz mit der Computeressource [Benutzerdefinierte Routen (UDRs)](../virtual-network/virtual-networks-udr-overview.md) hinzu. Richten Sie für jede IP-Adresse eine benutzerdefinierte Route ein, die vom Azure Batch-Dienst in der Region Ihrer Ressourcen verwendet wird. Diese benutzerdefinierten Routen ermöglichen dem Batch-Dienst, für die zeitliche Planung von Tasks mit den Serverknoten zu kommunizieren. Fügen Sie außerdem die IP-Adresse für Azure Machine Learning Service hinzu, in dem die Ressourcen enthalten sind, da dies für den Zugriff auf Computeinstanzen erforderlich ist. Über die folgenden Methoden können Sie eine Liste der IP-Adressen des Batch-Diensts und von Azure Machine Learning Service abrufen:

    * Laden Sie die [Azure-IP-Adressbereiche und Diensttags](https://www.microsoft.com/download/details.aspx?id=56519) herunter, und suchen Sie in der Datei nach `BatchNodeManagement.<region>` und `AzureMachineLearning.<region>`, wobei `<region>` Ihre Azure-Region ist.

    * Laden Sie die Informationen mithilfe der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) herunter. Im folgenden Beispiel werden die IP-Adressinformationen heruntergeladen und die Informationen für die Region „USA, Osten 2“ herausgefiltert:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

        > [!TIP]
        > Wenn Sie die Regionen „USA – Virginia“, „USA – Arizona“ oder „China, Osten 2“ verwenden, geben diese Befehle keine IP-Adressen zurück. Verwenden Sie stattdessen einen der folgenden Links zum Herunterladen einer Liste von IP-Adressen:
        >
        > * [Azure IP Ranges and Service Tags for Azure Government](https://www.microsoft.com/download/details.aspx?id=57063) (Azure-IP-Bereiche und -Diensttags für Azure Government)
        > * [Azure IP Ranges and Service Tags for Azure China](https://www.microsoft.com//download/details.aspx?id=57062) (Azure-IP-Bereiche und -Diensttags für Azure China)
    
    Wenn Sie die benutzerdefinierten Routen hinzufügen, definieren Sie die Route für jedes zugehörige Batch-IP-Adresspräfix, und legen Sie den __Typ des nächsten Hops__ auf __Internet__ fest. Die folgende Abbildung zeigt ein Beispiel dieser benutzerdefinierten Route im Azure-Portal:

    ![Beispiel für eine benutzerdefinierte Route für ein Adresspräfix](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > Die IP-Adressen können sich im Laufe der Zeit ändern.

    Zusätzlich zu allen von Ihnen angegebenen benutzerdefinierten Routen muss der ausgehende Datenverkehr zu Azure Storage über Ihre lokale Netzwerkappliance zugelassen werden. Die speziellen URLs für diesen Datenverkehr sind in folgenden Formularen zu finden: `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` und `<account>.blob.core.windows.net`. 

    Weitere Informationen finden Sie unter [Erstellen eines Azure Batch-Pools in einem virtuellen Netzwerk](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).


### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Erstellen eines Computeclusters in einem virtuellen Netzwerk

Gehen Sie zum Erstellen eines Machine Learning Compute-Clusters wie folgt vor:

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com/) an, und wählen Sie dann Ihr Abonnement und den Arbeitsbereich aus.

1. Wählen Sie links __Compute__ aus.

1. Wählen Sie in der Mitte __Trainingscluster__ aus, und wählen Sie dann __+__ aus.

1. Erweitern Sie im Dialogfeld __Neuer Trainingscluster__ den Abschnitt __Erweiterte Einstellungen__.

1. Um diese Computeressource für die Verwendung eines virtuellen Netzwerks zu konfigurieren, führen Sie im Abschnitt __Virtuelles Netzwerk konfigurieren__ die folgenden Schritte aus:

    1. Wählen Sie in der Dropdownliste __Ressourcengruppe__ die Ressourcengruppe aus, die das virtuelle Netzwerk enthält.
    1. Wählen Sie in der Dropdownliste __Virtuelles Netzwerk__ das virtuelle Netzwerk aus, das das Subnetzwerk enthält.
    1. Wählen Sie in der Dropdownliste __Subnetz__ das zu verwendende Subnetz aus.

   ![Die virtuellen Netzerkeinstellungen für Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Ein Machine Learning Compute-Cluster kann auch mithilfe des Azure Machine Learning SDK erstellt werden. Der folgende Code erstellt einen neuen Machine Learning Compute-Cluster im Subnetz `default` eines virtuellen Netzwerks namens `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Nach Abschluss des Erstellungsprozesses trainieren Sie Ihr Modell, indem Sie den Cluster in einem Experiment verwenden. Weitere Informationen finden Sie unter [Auswählen und Verwenden eines Computeziels für das Training](how-to-set-up-training-targets.md).

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>Zugreifen auf Daten auf einem Compute-Instanz-Notebook

Wenn Sie Notebooks auf einer Azure-Compute-Instanz verwenden, müssen Sie sicherstellen, dass Ihr Notebook auf einer Computeressource hinter dem gleichen virtuellen Netzwerk und Subnetz wie Ihre Daten ausgeführt wird. 

Sie müssen die Compute-Instanz so konfigurieren, das sie sich während der Erstellung unter **Erweiterte Einstellungen** > **Konfigurieren des virtuellen Netzwerks** in demselben virtuellen Netzwerk befindet. Eine vorhandene Compute-Instanz kann in einem virtuellen Netzwerk nicht hinzugefügt werden.

## <a name="azure-databricks"></a>Azure Databricks

Damit Sie Azure Databricks in einem virtuellen Netzwerk mit Ihrem Arbeitsbereich verwenden können, müssen die folgenden Voraussetzungen erfüllt sein:

> [!div class="checklist"]
> * Das virtuelle Netzwerk muss sich im selben Abonnement und in derselben Region befinden wie der Azure Machine Learning-Arbeitsbereich.
> * Wenn die Azure Storage-Konten für den Arbeitsbereich ebenfalls in einem virtuellen Netzwerk geschützt sind, müssen sie sich im selben virtuellen Netzwerk befinden wie der Azure Databricks-Cluster.
> * Zusätzlich zu den von Azure Databricks verwendeten Subnetzen __databricks-private__ und __databricks-public__ ist auch das für das virtuelle Netzwerk erstellte __standardmäßige__ Subnetz erforderlich.

Weitere Informationen zur Verwendung von Azure Databricks mit einem virtuellen Netzwerk finden Sie unter [Bereitstellen von Azure Databricks in Ihrem Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtueller Computer oder HDInsight-Cluster

> [!IMPORTANT]
> Azure Machine Learning unterstützt nur virtuelle Computer, auf denen Ubuntu ausgeführt wird.

In diesem Abschnitt erfahren Sie, wie Sie einen virtuellen Computer oder einen Azure HDInsight-Cluster in einem virtuellen Netzwerk mit Ihrem Arbeitsbereich verwenden.

### <a name="create-the-vm-or-hdinsight-cluster"></a>Erstellen der VM oder des HDInsight-Clusters

Erstellen Sie einen virtuellen Computer oder einen HDInsight-Cluster mithilfe des Azure-Portals oder der Azure-Befehlszeilenschnittstelle, und platzieren Sie den Cluster in einem virtuellen Azure-Netzwerk. Weitere Informationen finden Sie in den folgenden Artikeln:
* [Erstellen und Verwalten virtueller Azure-Netzwerke für virtuelle Linux-Computer](../virtual-machines/linux/tutorial-virtual-network.md)

* [Erweitern von Azure HDInsight per Azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>Konfigurieren der Netzwerkports 

Damit Azure Machine Learning mit dem SSH-Port des virtuellen Computers oder des Clusters kommunizieren kann, konfigurieren Sie einen Quelleintrag für die Netzwerksicherheitsgruppe. Der SSH-Port ist in der Regel der Port 22. Gehen Sie wie folgt vor, um Datenverkehr aus dieser Quelle zuzulassen:

1. Wählen Sie in der Dropdownliste __Quelle__ den Eintrag __Diensttag__ aus.

1. Wählen Sie in der Dropdownliste __Quelldiensttag__ den Eintrag __AzureMachineLearning__ aus.

    ![Eingangsregeln für Experimente auf einem virtuellen Computer oder in einem HDInsight-Cluster innerhalb eines virtuellen Netzwerks](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. Wählen Sie in der Dropdownliste __Quellportbereiche__ den Eintrag __*__ aus.

1. Wählen in der Dropdownliste __Ziel__ die Option __Alle__ aus.

1. Wählen Sie in der Dropdownliste __Quellportbereiche__ den Eintrag __22__ aus.

1. Wählen Sie unter __Protokoll__ die Option __Alle__ aus.

1. Wählen Sie für __Aktion__ die Option __Zulassen__ aus.

Behalten Sie die Standardausgangsregeln für die Netzwerksicherheitsgruppe bei. Weitere Informationen finden Sie unter [Sicherheitsgruppen](../virtual-network/network-security-groups-overview.md#default-security-rules) bei den Standardsicherheitsregeln.

Wenn Sie die Standardausgangsregeln nicht verwenden möchten und den ausgehenden Zugriff auf Ihr virtuelles Netzwerk beschränken möchten, finden Sie weitere Informationen im Abschnitt [Beschränken der ausgehenden Konnektivität aus dem virtuellen Netzwerk](#limiting-outbound-from-vnet).

### <a name="attach-the-vm-or-hdinsight-cluster"></a>Anfügen der VM oder des HDInsight-Clusters

Fügen Sie den virtuellen Computer oder den HDInsight-Cluster an Ihren Azure Machine Learning-Arbeitsbereich an. Weitere Informationen finden Sie unter [Einrichten von Computezielen für das Modelltraining](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel ist der dritte Teil einer vierteiligen Serie zu virtuellen Netzwerken. Weitere Informationen zum Schützen eines virtuellen Netzwerks finden Sie in den verbleibenden Artikeln:

* [Teil 1: Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
* [Teil 2: Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
* [Teil 4: Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
* [Teil 5: Verwenden von Studio in einem virtuellen Netzwerk](how-to-enable-studio-virtual-network.md)