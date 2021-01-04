---
title: Schützen eines Azure Machine Learning-Arbeitsbereichs mit virtuellen Netzwerken
titleSuffix: Azure Machine Learning
description: Verwenden Sie eine isolierte Azure Virtual Network-Instanz, um Ihren Azure Machine Learning-Arbeitsbereich und zugehörige Ressourcen zu schützen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 07b8c130a2a22554e4cd5b33996d5a5ee967d47f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029531"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Schützen eines Azure Machine Learning-Arbeitsbereichs mit virtuellen Netzwerken

In diesem Artikel erfahren Sie, wie Sie einen Azure Machine Learning-Arbeitsbereich und die zugehörigen Ressourcen in einem virtuellen Netzwerk schützen.


Dieser Artikel ist der zweite Teil einer fünfteiligen Reihe, in der der Workflow zum Schützen eines Azure Machine Learning-Workflows erläutert wird. Es wird dringend empfohlen, zunächst [Teil 1: Übersicht über die Isolation virtueller Netzwerke und Datenschutz](how-to-network-security-overview.md) zu lesen, um die Gesamtarchitektur besser zu verstehen. 

Sehen Sie sich auch die anderen Artikel in dieser Reihe an:

[1. Übersicht über die Isolation virtueller Netzwerke und Datenschutz](how-to-network-security-overview.md) > **2. Schützen eines Azure Machine Learning-Arbeitsbereichs mit virtuellen Netzwerken** > [3. Schützen einer Azure Machine Learning-Trainingsumgebung mit virtuellen Netzwerken](how-to-secure-training-vnet.md) > [4. Schützen einer Azure Machine Learning-Rückschlussumgebung mit virtuellen Netzwerken](how-to-secure-inferencing-vnet.md) > [5. Verwenden von Azure Machine Learning Studio in einem virtuellen Azure-Netzwerk](how-to-enable-studio-virtual-network.md)

In diesem Artikel erfahren Sie, wie Sie die folgenden Arbeitsbereichsressourcen in einem virtuellen Netzwerk aktivieren:
> [!div class="checklist"]
> - Azure Machine Learning-Arbeitsbereich
> - Azure-Speicherkonten
> - Azure Machine Learning-Datenspeicher und -Datasets
> - Azure-Schlüsseltresor
> - Azure Container Registry

## <a name="prerequisites"></a>Voraussetzungen

+ Im Artikel [Übersicht über die Isolation virtueller Netzwerke und Datenschutz](how-to-network-security-overview.md) finden Sie Informationen zu gängigen Szenarien im Zusammenhang mit virtuellen Netzwerken sowie zur Gesamtarchitektur virtueller Netzwerke.

+ Ein vorhandenes virtuelles Netzwerk und Subnetz, die mit Ihren Computeressourcen verwendet werden können

+ Ihr Benutzerkonto muss über die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) zu den folgenden Aktionen berechtigt werden, um Ressourcen in einem virtuellen Netzwerk oder Subnetz bereitstellen zu können:

    - „Microsoft.Network/virtualNetworks/join/action“ auf der virtuellen Netzwerkressource
    - „Microsoft.Network/virtualNetworks/subnet/join/action“ auf der Subnetzressource

    Weitere Informationen zur rollenbasierten Zugriffssteuerung von Azure in Netzwerken finden Sie unter [Integrierte Netzwerkrollen](../role-based-access-control/built-in-roles.md#networking).


## <a name="secure-the-workspace-with-private-endpoint"></a>Schützen des Arbeitsbereichs mit privatem Endpunkt

Azure Private Link ermöglicht Ihnen das Herstellen einer Verbindung mit Ihrem Arbeitsbereich über einen privaten Endpunkt. Bei einem privaten Endpunkt handelt es sich um eine Gruppe privater IP-Adressen in Ihrem virtuellen Netzwerk. Sie können dann den Zugriff auf Ihren Arbeitsbereich so einschränken, dass er nur über die privaten IP-Adressen erfolgt. Private Link hilft dabei, das Risiko einer Datenexfiltration zu verringern.

Weitere Informationen zum Einrichten eines Private Link-Arbeitsbereichs finden Sie unter [Konfigurieren von Azure Private Link für einen Azure Machine Learning-Arbeitsbereich (Vorschauversion)](how-to-configure-private-link.md).

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Schützen von Azure-Speicherkonten mit Dienstendpunkten

Azure Machine Learning unterstützt Speicherkonten, die so konfiguriert sind, dass sie Dienstendpunkte oder private Endpunkte verwenden. In diesem Abschnitt erfahren Sie, wie Sie ein Azure-Speicherkonto mithilfe von Dienstendpunkten schützen. Informationen zu privaten Endpunkten finden Sie im nächsten Abschnitt.

> [!IMPORTANT]
> Sie können sowohl das _Standardspeicherkonto_ für Azure Machine Learning als auch _Nicht-Standardspeicherkonten_ in einem virtuellen Netzwerk anordnen.
>
> Das Standardspeicherkonto wird automatisch bereitgestellt, wenn Sie einen Arbeitsbereich erstellen.
>
> Für Nicht-Standardspeicherkonten können Sie mit dem `storage_account`-Parameter in der [`Workspace.create()`-Funktion](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-&preserve-view=true) ein benutzerdefiniertes Speicherkonto über die Azure-Ressourcen-ID angeben.

Führen Sie die folgenden Schritte aus, um ein Azure-Speicherkonto für den Arbeitsbereich in einem virtuellen Netzwerk zu verwenden:

1. Wechseln Sie im Azure-Portal zu dem Speicherdienst, den Sie in Ihrem Arbeitsbereich verwenden möchten.

   [![Der Speicher, der an den Azure Machine Learning-Arbeitsbereich angefügt ist](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Wählen Sie auf der Speicherdienstkonto-Seite __Firewalls und virtuelle Netzwerke__ aus.

   ![Der Bereich „Firewalls und virtuelle Netzwerke“ auf der Seite „Azure Storage“ im Azure-Portal.](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Führen Sie auf der Seite __Firewalls und virtuelle Netzwerke__ folgende Aktionen aus:
    1. Klicken Sie auf __Ausgewählte Netzwerke__.
    1. Wählen Sie unter __Virtuelle Netzwerke__ den Link __Vorhandenes virtuelles Netzwerk hinzufügen__ aus. Durch diese Aktion wird das virtuelle Netzwerk an dem Ort hinzugefügt, wo sich Ihre Computeressource befindet (siehe Schritt 1).

        > [!IMPORTANT]
        > Das Speicherkonto muss sich in demselben virtuellen Netzwerk und Subnetz befinden wie die Compute-Instanzen oder Cluster, die für Training oder Rückschluss verwendet werden.

    1. Aktivieren Sie das Kontrollkästchen __Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben__. Dadurch erhalten nicht alle Azure-Dienste Zugriff auf Ihr Speicherkonto.
    
        * Ressourcen einiger Dienste, **die in Ihrem Abonnement registriert sind**, können für bestimmte Vorgänge auf das Speicherkonto **im selben Abonnement** zugreifen. Hierzu zählen beispielsweise das Schreiben von Protokollen und Sicherungsvorgänge.
        * Ressourcen einiger Dienste kann durch __Zuweisen einer Azure-Rolle__ zur vom System zugewiesenen verwalteten Identität der explizite Zugriff auf Ihr Speicherkonto gewährt werden.

        Weitere Informationen finden Sie unter [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](../storage/common/storage-network-security.md#trusted-microsoft-services).

    > [!IMPORTANT]
    > Wenn Sie mit dem Azure Machine Learning SDK arbeiten, muss Ihre Entwicklungsumgebung eine Verbindung mit dem Azure Storage-Konto herstellen können. Wenn sich das Speicherkonto innerhalb eines virtuellen Netzwerks befindet, muss die Firewall den Zugriff über die IP-Adresse der Entwicklungsumgebung zulassen.
    >
    > Um den Zugriff auf das Speicherkonto zu ermöglichen, öffnen Sie die __Firewalls und virtuellen Netzwerke__ für das Speicherkonto *über einen Webbrowser auf dem Entwicklungsclient*. Fügen Sie dann mithilfe des Kontrollkästchens __Client-IP-Adresse hinzufügen__ die IP-Adresse Ihres Clients dem __ADRESSBEREICH__ hinzu. Sie können die IP-Adresse der Entwicklungsumgebung im Feld __ADRESSBEREICH__ auch manuell eingeben. Nachdem die IP-Adresse für den Client hinzugefügt wurde, kann er mit dem SDK auf das Speicherkonto zugreifen.

   [![Der Bereich „Firewalls und virtuelle Netzwerke“ im Azure-Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Schützen von Azure-Speicherkonten mit privaten Endpunkten

Azure Machine Learning unterstützt Speicherkonten, die so konfiguriert sind, dass sie Dienstendpunkte oder private Endpunkte verwenden. Wenn das Speicherkonto private Endpunkte verwendet, müssen Sie zwei private Endpunkte für Ihr Standardspeicherkonto konfigurieren:
1. Einen privaten Endpunkt mit einer untergeordneten Zielressource für **Blobs**
1. Einen privaten Endpunkt mit einer untergeordneten Zielressource für **Dateien** (Dateifreigabe)

![Screenshot: Seite für die Konfiguration von privaten Endpunkten mit den Optionen „Blob“ und „Datei“](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Wenn Sie einen privaten Endpunkt für ein Speicherkonto konfigurieren möchten, das **nicht** der Standardspeicher ist, wählen Sie als **untergeordnete Zielressource** den entsprechenden Typ für das Speicherkonto aus, das Sie hinzufügen möchten.

Weitere Informationen finden Sie unter [Verwenden privater Endpunkte für Azure Storage](../storage/common/storage-private-endpoints.md).

## <a name="secure-datastores-and-datasets"></a>Schützen von Datenspeichern und Datasets

In diesem Abschnitt erfahren Sie, wie Sie Datenspeicher und Datasets im SDK mit einem virtuellen Netzwerk verwenden. Weitere Informationen zu Studio finden Sie im Abschnitt [Verwenden von Azure Machine Learning Studio in einem virtuellen Azure-Netzwerk](how-to-enable-studio-virtual-network.md).

Für den Zugriff auf die Daten mithilfe des SDK müssen Sie die Authentifizierungsmethode verwenden, die für den jeweiligen Dienst erforderlich ist, in dem die Daten gespeichert sind. Wenn Sie beispielsweise einen Datenspeicher für den Zugriff auf Azure Data Lake Store Gen2 registrieren, müssen Sie weiterhin einen Dienstprinzipal verwenden, wie unter [Herstellen einer Verbindung mit Azure-Speicherdiensten](how-to-access-data.md#azure-data-lake-storage-generation-2) beschrieben.

### <a name="disable-data-validation"></a>Deaktivieren der Datenüberprüfung

Standardmäßig führt Azure Machine Learning Überprüfungen von Datengültigkeit und Anmeldeinformationen aus, wenn Sie versuchen, mit dem SDK auf Daten zuzugreifen. Wenn sich Ihre Daten hinter einem virtuellen Netzwerk befinden, kann Azure Machine Learning diese Überprüfungen nicht ausführen. Um dies zu vermeiden, müssen Sie Datenspeicher und Datasets erstellen, die die Überprüfung überspringen.

### <a name="use-datastores"></a>Verwenden von Datenspeichern

 Azure Data Lake Store Gen1 und Azure Data Lake Store Gen2 überspringen standardmäßig die Überprüfung, sodass keine weitere Aktion erforderlich ist. Für die folgenden Dienste können Sie jedoch eine ähnliche Syntax verwenden, um die Datenspeichervalidierung zu überspringen:

- Azure Blob Storage
- Azure-Dateifreigabe
- PostgreSQL
- Azure SQL-Datenbank

Im folgenden Codebeispiel wird ein neuer Azure-Blobdatenspeicher erstellt und `skip_validation=True` festgelegt.

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Verwenden von Datasets

Die Syntax zum Überspringen der Überprüfung von Datasets ähnelt den folgenden Datasettypen:
- Durch Trennzeichen getrennte Datei
- JSON 
- Parquet
- SQL
- Datei

Der folgende Code erstellt ein neues JSON-Dataset und legt `validate=False`fest.

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Schützen von Azure Key Vault

Azure Machine Learning verwendet eine zugeordnete Key Vault-Instanz zum Speichern der folgenden Anmeldeinformationen:
* Der verknüpften Speicherkonto-Verbindungszeichenfolge
* Kennwörter in Azure Container Repository-Instanzen
* Verbindungszeichenfolgen zur Verbindung mit Datenspeichern.

Um die Azure Machine Learning-Experimentierfunktionen mit Azure Key Vault hinter einem virtuellen Netzwerk zu verwenden, gehen Sie wie folgt vor:

1. Wechseln Sie zu der Key Vault-Instanz, die mit dem Arbeitsbereich verknüpft ist.

1. Wählen Sie auf der Seite __Key Vault__ im linken Bereich die Option __Netzwerk__ aus.

1. Führen Sie auf der Registerkarte __Firewalls und virtuelle Netzwerke__ folgende Aktionen aus:
    1. Wählen Sie unter __Zugriff erlauben von__ den Eintrag __Privater Endpunkt und ausgewählte Netzwerke__ aus.
    1. Wählen Sie unter __Virtuelle Netzwerke__ die Option __Vorhandenes virtuelles Netzwerk hinzufügen__ aus, um das virtuelle Netzwerk hinzuzufügen, in dem sich die Computeressourcen für Ihre Experimente befinden.
    1. Wählen Sie unter __Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?__ die Option __Ja__ aus.

   [![Der Abschnitt „Firewalls und virtuelle Netzwerke“ im Bereich „Key Vault“](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Aktivieren von Azure Container Registry (ACR)

Damit Sie Container Registry in einem virtuellen Netzwerk verwenden können, müssen die folgenden Voraussetzungen erfüllt sein:

* Für Ihre Azure Container Registry-Instanz (ACR) muss ein Premium-Tarif erworben worden sein. Informationen zum Durchführen eines Upgrades finden Sie unter [Wechseln von SKUs](../container-registry/container-registry-skus.md#changing-tiers).

* Azure Container Registry muss sich in demselben virtuellen Netzwerk und Subnetz befinden wie das Speicherkonto und die Computeziele, die für Training oder Rückschluss verwendet werden.

* Der Azure Machine Learning-Arbeitsbereich muss einen [Azure Machine Learning-Computecluster](how-to-create-attach-compute-cluster.md) enthalten.

    Wenn sich ACR hinter einem virtuellen Netzwerk befindet, kann Azure Machine Learning es nicht zum direkten Erstellen von Docker-Images verwenden. Stattdessen wird zum Erstellen der Images der Computecluster verwendet.

* Vor der Verwendung von ACR mit Azure Machine Learning in einem virtuellen Netzwerk müssen Sie einen Supportincident erstellen, um diese Funktion zu aktivieren. Weitere Informationen finden Sie unter [Verwalten und Erhöhen von Kontingenten](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

Sind diese Anforderungen erfüllt, führen Sie die folgenden Schritte zum Aktivieren von Azure Container Registry aus:

1. Suchen Sie den Namen der Azure Container Registry-Instanz für Ihren Arbeitsbereich mit einer der folgenden Methoden:

    __Azure portal__

    Im Abschnitt „Übersicht“ des Arbeitsbereichs ist der Wert __Registrierung__ mit der Azure Container Registry-Instanz verknüpft.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry für den Arbeitsbereich" border="true":::

    __Azure-Befehlszeilenschnittstelle__

    Wenn Sie die [Machine Learning-Erweiterung für die Azure CLI installiert](reference-azure-machine-learning-cli.md) haben, können Sie mit dem Befehl `az ml workspace show` die Arbeitsbereichsinformationen anzeigen.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Der Befehl gibt einen Wert zurück, der `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` ähnelt. Der letzte Teil der Zeichenfolge ist der Name der Azure Container Registry-Instanz für den Arbeitsbereich.

1. Um den Zugriff auf das virtuelle Netzwerk einzuschränken, führen Sie die Schritte unter [Konfigurieren des Netzwerkzugriffs für die Registrierung](../container-registry/container-registry-vnet.md#configure-network-access-for-registry) aus. Wenn Sie das virtuelle Netzwerk hinzufügen, wählen Sie das virtuelle Netzwerk und das Subnetz für die Azure Machine Learning-Ressourcen aus.

1. Verwenden Sie das Python-SDK für Azure Machine Learning, um einen Computecluster zum Erstellen von Docker-Images zu konfigurieren. Im folgenden Codeausschnitt wird diese Vorgehensweise veranschaulicht:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Das Speicherkonto, der Computecluster und die Azure Container Registry-Instanz müssen sich in demselben Subnetz des virtuellen Netzwerks befinden.
    
    Weitere Informationen finden Sie in der Referenz zur [update()](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-&preserve-view=true)-Methode.

1. Wenden Sie die folgende Azure Resource Manager-Vorlage an. Durch diese Vorlage kann Ihr Arbeitsbereich mit ACR kommunizieren.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "basic",
            "name": "basic"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

    Diese Vorlage erstellt einen _privaten Endpunkt_ für den Netzwerkzugriff aus dem Arbeitsbereich auf Ihre ACR. Der folgende Screenshot zeigt ein Beispiel für diesen privaten Endpunkt.

    :::image type="content" source="media/how-to-secure-workspace-vnet/acr-private-endpoint.png" alt-text="Einstellungen des privaten ACR-Endpunkts":::

    > [!IMPORTANT]
    > Löschen Sie diesen Endpunkt nicht! Wenn Sie ihn versehentlich löschen, können Sie die Vorlage aus diesem Schritt erneut anwenden, um einen neuen zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel ist der erste Teil einer vierteiligen Serie zu virtuellen Netzwerken. Weitere Informationen zum Schützen eines virtuellen Netzwerks finden Sie in den verbleibenden Artikeln:

* [Teil 1: Übersicht über die Isolation virtueller Netzwerke und Datenschutz](how-to-network-security-overview.md)
* [Teil 3: Schützen einer Azure Machine Learning-Trainingsumgebung mit virtuellen Netzwerken](how-to-secure-training-vnet.md)
* [Teil 4: Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
* [Teil 5: Verwenden von Studio in einem virtuellen Netzwerk](how-to-enable-studio-virtual-network.md)