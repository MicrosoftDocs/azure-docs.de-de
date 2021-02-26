---
title: Verwenden einer Firewall
titleSuffix: Azure Machine Learning
description: Steuern Sie den Zugriff auf Azure Machine Learning-Arbeitsbereiche mit Azure Firewall. Erfahren Sie mehr über die Hosts, die Sie durch die Firewall zulassen müssen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 0fcea6a44f5379ff3da5b348ae45486be6c2516a
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831313"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Verwenden des Arbeitsbereichs hinter einer Firewall für Azure Machine Learning

In diesem Artikel erfahren Sie, wie Sie Azure Firewall konfigurieren, um den Zugriff auf Ihren Azure Machine Learning-Arbeitsbereich und das öffentliche Internet zu steuern. Weitere Informationen zum Schützen von Azure Machine Learning finden Sie unter [Unternehmenssicherheit für Azure Machine Learning](concept-enterprise-security.md).

> [!WARNING]
> Der Zugriff auf den Datenspeicher hinter einer Firewall wird nur in Code First-Umgebungen unterstützt. Die Verwendung von [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) für den Zugriff auf Daten hinter einer Firewall wird nicht unterstützt. Wenn Sie Datenspeicher in einem privaten Netzwerk mit Studio nutzen möchten, müssen Sie zunächst [ein virtuelles Netzwerk einrichten](../virtual-network/quick-create-portal.md) und [Studio Zugriff auf die in einem virtuellen Netzwerk gespeicherten Daten gewähren](how-to-enable-studio-virtual-network.md).

## <a name="azure-firewall"></a>Azure Firewall

Wenn Sie Azure Firewall verwenden, erstellen Sie mithilfe der __Ziel-Netzwerkadressübersetzung__ (Destination Network Address Translation, DNAT) NAT-Regeln für eingehenden Datenverkehr. Für ausgehenden Datenverkehr erstellen Sie __Netzwerkregeln__ und/oder __Anwendungsregeln__. Diese Regelsammlungen werden unter [Wie lauten einige der Azure Firewall-Konzepte?](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts) ausführlicher beschrieben.

### <a name="inbound-configuration"></a>Konfiguration für eingehenden Datenverkehr

Wenn Sie eine Azure Machine Learning-__Compute-Instanz__ oder einen Azure Machine Learning-__Computecluster__ verwenden, fügen Sie eine [benutzerdefinierte Route](../virtual-network/virtual-networks-udr-overview.md) (User-Defined Route, UDR) für das Subnetz hinzu, das die Azure Machine Learning-Ressourcen enthält. Diese Route erzwingt die Übertragung des Datenverkehrs __von__ den IP-Adressen der `BatchNodeManagement`- und `AzureMachineLearning`-Ressourcen an die öffentliche IP-Adresse Ihrer Compute-Instanz und Ihres Computeclusters.

Diese benutzerdefinierten Routen ermöglichen dem Batch-Dienst, für die zeitliche Planung von Tasks mit den Serverknoten zu kommunizieren. Fügen Sie auch die IP-Adresse für Azure Machine Learning Service hinzu, da diese für den Zugriff auf Compute-Instanzen erforderlich ist. Wenn Sie die IP-Adresse für Azure Machine Learning Service hinzufügen, müssen Sie die IP-Adresse für die __primären und sekundären__ Azure-Regionen hinzufügen. Die primäre Region ist diejenige, in der sich Ihr Arbeitsbereich befindet.

Informationen zum Auffinden der sekundären Region finden Sie im Abschnitt [Sicherstellen von Geschäftskontinuität und Notfallwiederherstellung mit Azure-Regionspaaren](../best-practices-availability-paired-regions.md#azure-regional-pairs). Wenn sich Ihr Azure Machine Learning Service z. B. in „USA, Osten 2“ befindet, ist die sekundäre Region „USA, Mitte“. 

Über die folgenden Methoden können Sie eine Liste der IP-Adressen des Batch-Diensts und von Azure Machine Learning Service abrufen:

* Laden Sie die [Azure-IP-Adressbereiche und Diensttags](https://www.microsoft.com/download/details.aspx?id=56519) herunter, und suchen Sie in der Datei nach `BatchNodeManagement.<region>` und `AzureMachineLearning.<region>`, wobei `<region>` Ihre Azure-Region ist.

* Laden Sie die Informationen mithilfe der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) herunter. Im folgenden Beispiel werden die IP-Adressinformationen heruntergeladen und die Informationen für die Region „USA, Osten 2“ (primär) und „USA, Mitte“ (sekundär) herausgefiltert:

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
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

Weitere Informationen finden Sie unter [Erstellen eines Azure Batch-Pools in einem virtuellen Netzwerk](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="outbound-configuration"></a>Konfiguration für ausgehenden Datenverkehr

1. Fügen Sie __Netzwerkregeln__ hinzu, um Datenverkehr __zu__ und __von__ den folgenden Diensttags zuzulassen:

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Storage.region
    * KeyVault.region
    * ContainerRegistry.region

    Wenn Sie planen, die von Microsoft bereitgestellten standardmäßigen Docker-Images zu verwenden und vom Benutzer verwaltete Abhängigkeiten zu aktivieren, müssen Sie auch die folgenden Diensttags hinzufügen:

    * MicrosoftContainerRegistry.region
    * AzureFrontDoor.FirstParty

    Ersetzen Sie `region` in Einträgen, die diesen Wert enthalten, durch die von Ihnen verwendete Azure-Region. Beispiel: `keyvault.westus`.

    Wählen Sie `TCP` als __Protokoll__ aus. Wählen Sie `*` für die __Quell- und Zielports__ aus.

1. Fügen Sie __Anwendungsregeln__ für die folgenden Hosts hinzu:

    > [!NOTE]
    > Diese Liste ist nicht vollständig. Sie enthält nur die Hosts, die für die am häufigsten verwendeten Python-Ressourcen im Internet erforderlich sind. Wenn Sie z. B. Zugriff auf ein GitHub-Repository oder einen anderen Host benötigen, müssen Sie die erforderlichen Hosts für dieses Szenario ermitteln und hinzufügen.

    | **Hostname** | **Zweck** |
    | ---- | ---- |
    | **graph.windows.net** | Verwendet von einer Compute-Instanz oder einem Computecluster von Azure Machine Learning |
    | **anaconda.com**</br>**\*.anaconda.com** | Wird verwendet, um Standardpakete zu installieren |
    | **\*.anaconda.org** | Wird verwendet, um Repositorydaten abzurufen |
    | **pypi.org** | Wird zum Auflisten von Abhängigkeiten vom Standardindex verwendet, sofern vorhanden, und der Index wird nicht durch Benutzereinstellungen überschrieben. Wenn der Index überschrieben wird, müssen Sie auch **\*.pythonhosted.org** zulassen. |
    | **cloud.r-project.org** | Wird beim Installieren von CRAN-Paketen für die R-Entwicklung verwendet. |
    | **\*pytorch.org** | Wird von einigen Beispielen verwendet, die auf PyTorch basieren. |
    | **\*.tensorflow.org** | Wird von einigen Beispielen verwendet, die auf Tensorflow basieren. |

    Wählen Sie __http, https__ für __Protokoll:Port__ aus.

    Weitere Informationen zum Konfigurieren von Anwendungsregeln finden Sie unter [Bereitstellen und Konfigurieren von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Informationen zum Einschränken des Zugriffs auf Modelle, die in Azure Kubernetes Service (AKS) bereitgestellt werden, finden Sie unter [Steuern des ausgehenden Datenverkehrs in Azure Kubernetes Service](../aks/limit-egress-traffic.md).

## <a name="other-firewalls"></a>Andere Firewalls

Die Anleitungen in diesem Abschnitt sind generisch, da jede Firewall über eine eigene Terminologie und bestimmte Konfigurationen verfügt. Wenn Sie Fragen dazu haben, wie Sie die Kommunikation über die Firewall zulassen, lesen Sie die Dokumentation für die von Ihnen verwendete Firewall.

Wenn sie nicht ordnungsgemäß konfiguriert ist, kann die Firewall Probleme bei der Verwendung Ihres Arbeitsbereichs verursachen. Es gibt eine Vielzahl von Hostnamen, die vom Azure Machine Learning-Arbeitsbereich verwendet werden. In den folgenden Abschnitten sind Hosts aufgeführt, die für Azure Machine Learning erforderlich sind.

### <a name="microsoft-hosts"></a>Microsoft-Hosts

Die Hosts in diesem Abschnitt befinden sich im Besitz von Microsoft und stellen Dienste bereit, die für die ordnungsgemäße Funktionsweise Ihres Arbeitsbereichs erforderlich sind. In den folgenden Tabellen finden Sie die Hostnamen für die Regionen „Azure (öffentlich)“, „Azure Government“ und „Azure China 21ViaNet“.

**Allgemeine Azure-Hosts**

| **Erforderlich für** | **Azure (öffentlich)** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure-Portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure Machine Learning-Hosts**

| **Erforderlich für** | **Azure (öffentlich)** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| Experimentieren, Verlauf, Hyperdrive, Beschriftung | \*.experiments.azureml.net | \*.ml.azure.us | \*.ml.azure.cn |
| Modellverwaltung | \*.modelmanagement.azureml.net | \*.ml.azure.us | \*.ml.azure.cn |
| Pipeline | \*.aether.ms | \*.ml.azure.us | \*.ml.azure.cn |
| Designer (Studio-Dienst) | \*.studioservice.azureml.com | \*.ml.azure.us | \*.ml.azure.cn |
| Integriertes Notebook | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| Integriertes Notebook | \*.file.core.windows.net | \*.file.core.usgovcloudapi.net | \*.file.core.chinacloudapi.cn |
| Integriertes Notebook | \*.dfs.core.windows.net | \*.dfs.core.usgovcloudapi.net | \*.dfs.core.chinacloudapi.cn |
| Integriertes Notebook | \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | \*.blob.core.chinacloudapi.cn |
| Integriertes Notebook | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Integriertes Notebook | \*.aznbcontent.net |  | |

**Azure Machine Learning-Compute-Instanz- und -Computeclusterhosts**

| **Erforderlich für** | **Azure (öffentlich)** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Computecluster/-Instanz | \*.batchai.core.windows.net | \*.batchai.core.usgovcloudapi.net |\*.batchai.ml.azure.cn |
| Computecluster/-Instanz | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| Compute-Instanz | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| Compute-Instanz | \*.instances.azureml.ms |  |  |

**Zugeordnete Ressourcen, die von Azure Machine Learning verwendet werden**

| **Erforderlich für** | **Azure (öffentlich)** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Storage-Konto | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure-Schlüsseltresor | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> Wenn Sie die Verbundidentität verwenden möchten, befolgen Sie die Anleitungen im Artikel [Bewährte Methoden zum Sichern von Active Directory-Verbunddiensten](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs).

Informationen zum Hinzufügen von IP-Adressen für `BatchNodeManagement` und `AzureMachineLearning` finden Sie auch unter [Tunnelerzwingung](how-to-secure-training-vnet.md#forced-tunneling).

Informationen zum Einschränken des Zugriffs auf Modelle, die in Azure Kubernetes Service (AKS) bereitgestellt werden, finden Sie unter [Steuern des ausgehenden Datenverkehrs in Azure Kubernetes Service](../aks/limit-egress-traffic.md).

### <a name="python-hosts"></a>Python-Hosts

Die Hosts in diesem Abschnitt werden zum Installieren von Python-Paketen verwendet. Sie sind für Entwicklung, Training und Bereitstellung erforderlich. 

> [!NOTE]
> Diese Liste ist nicht vollständig. Sie enthält nur die Hosts, die für die am häufigsten verwendeten Python-Ressourcen im Internet erforderlich sind. Wenn Sie z. B. Zugriff auf ein GitHub-Repository oder einen anderen Host benötigen, müssen Sie die erforderlichen Hosts für dieses Szenario ermitteln und hinzufügen.

| **Hostname** | **Zweck** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | Wird verwendet, um Standardpakete zu installieren |
| **\*.anaconda.org** | Wird verwendet, um Repositorydaten abzurufen |
| **pypi.org** | Wird zum Auflisten von Abhängigkeiten vom Standardindex verwendet, sofern vorhanden, und der Index wird nicht durch Benutzereinstellungen überschrieben. Wenn der Index überschrieben wird, müssen Sie auch **\*.pythonhosted.org** zulassen. |
| **\*pytorch.org** | Wird von einigen Beispielen verwendet, die auf PyTorch basieren. |
| **\*.tensorflow.org** | Wird von einigen Beispielen verwendet, die auf Tensorflow basieren. |

### <a name="r-hosts"></a>R-Hosts

Die Hosts in diesem Abschnitt werden zum Installieren von R-Paketen verwendet. Sie sind für Entwicklung, Training und Bereitstellung erforderlich.

> [!NOTE]
> Diese Liste ist nicht vollständig. Sie enthält nur die Hosts, die für die am häufigsten verwendeten R-Ressourcen im Internet erforderlich sind. Wenn Sie z. B. Zugriff auf ein GitHub-Repository oder einen anderen Host benötigen, müssen Sie die erforderlichen Hosts für dieses Szenario ermitteln und hinzufügen.

| **Hostname** | **Zweck** |
| ---- | ---- |
| **cloud.r-project.org** | Beim Installieren von CRAN-Paketen verwendet |

> [!IMPORTANT]
> Intern verwendet das R SDK für Azure Machine Learning Python-Pakete. Daher müssen Sie auch Python-Hosts durch die Firewall zulassen.
## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](../firewall/tutorial-firewall-deploy-portal.md)
* [Verwenden einer Machine Learning Compute-Instanz](how-to-network-security-overview.md)
