---
title: Erstellen von Arbeitsbereichen im Portal
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Azure Machine Learning-Arbeitsbereiche im Azure-Portal oder mit dem SDK für Python erstellen, anzeigen und löschen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 4425fdf488665ad555c73c59682041cb23a9ca66
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447332"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Erstellen und Verwalten von Azure Machine Learning-Arbeitsbereichen 

In diesem Artikel erstellen, betrachten und löschen Sie [**Azure Machine Learning-Arbeitsbereiche**](concept-workspace.md) für [Azure Machine Learning](overview-what-is-azure-ml.md) über das Azure-Portal oder mit dem [SDK für Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py).

Wenn sich Ihre Anforderungen ändern oder die Anforderungen an die Automatisierung zunehmen, können Sie Arbeitsbereiche auch [mithilfe der CLI](reference-azure-machine-learning-cli.md) oder [über die VS-Code-Erweiterung](tutorial-setup-vscode-extension.md) erstellen und löschen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.
* Wenn Sie das Python SDK verwenden, [installieren Sie das SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

# <a name="python"></a>[Python](#tab/python)

* **Standardspezifikation** Standardmäßig werden sowohl abhängige Ressourcen als auch die Ressourcengruppe automatisch erstellt. Dieser Code erstellt einen Arbeitsbereich namens `myworkspace` und eine Ressourcengruppe namens `myresourcegroup` in `eastus2`.
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    Legen Sie `create_resource_group` auf „False“ fest, wenn Sie über eine vorhandene Azure-Ressourcengruppe verfügen, die Sie für den Arbeitsbereich verwenden möchten.

* <a name="create-multi-tenant"></a>**Mehrere Mandanten**  Wenn Sie über mehrere Konten verfügen, fügen Sie die Mandanten-ID der Azure Active Directory-Instanz hinzu, die Sie verwenden möchten.  Ihre Mandanten-ID finden Sie im [Azure-Portal](https://portal.azure.com) unter **Azure Active Directory, Externe Identitäten**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[Sovereign Cloud](reference-machine-learning-cloud-parity.md)** Sie benötigen zusätzlichen Code, um sich bei Azure zu authentifizieren, wenn Sie in einer Sovereign Cloud arbeiten.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **Verwenden vorhandener Azure-Ressourcen**  Sie können auch einen Arbeitsbereich erstellen, der vorhandene Azure-Ressourcen mit dem Azure-Ressourcen-ID-Format verwendet. Die bestimmten Azure-Ressourcen-IDs finden Sie im Azure-Portal oder mit dem SDK. In diesem Beispiel wird davon ausgegangen, dass die Ressourcengruppe, das Speicherkonto, der Schlüsseltresor, App Insights und die Containerregistrierung bereits vorhanden sind.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

Weitere Informationen finden Sie unter [SDK-Referenz für den Arbeitsbereich](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py).

Wenn Sie Probleme beim Zugriff auf Ihr Abonnement haben, finden Sie weitere Informationen unter [Einrichten der Authentifizierung für Azure Machine Learning-Ressourcen und -Workflows](how-to-setup-authentication.md) sowie im Notebook [Authentifizierung in Azure Machine Learning](https://aka.ms/aml-notebook-auth).

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich mit den Anmeldeinformationen für Ihr Azure-Abonnement beim [Azure-Portal](https://portal.azure.com/) an. 

1. Wählen Sie im Azure-Portal oben links die Option **+ Ressource erstellen** aus.

      ![Neue Ressource erstellen](./media/how-to-manage-workspace/create-workspace.gif)

1. Suchen Sie mithilfe der Suchleiste **Machine Learning**.

1. Wählen Sie **Machine Learning** aus.

1. Wählen Sie im Bereich **Machine Learning** die Option **Erstellen** aus, um zu beginnen.

1. Geben Sie die folgenden Informationen an, um den neuen Arbeitsbereich zu konfigurieren:

   Feld|BESCHREIBUNG 
   ---|---
   Arbeitsbereichname |Geben Sie einen eindeutigen Namen ein, der Ihren Arbeitsbereich identifiziert. In diesem Beispiel verwenden wir **docs-ws**. Namen müssen in der Ressourcengruppe eindeutig sein. Verwenden Sie einen Namen, der leicht zu merken ist und sich von den von anderen Benutzern erstellten Arbeitsbereichen unterscheidet. Für den Namen des Arbeitsbereichs wird die Groß-/Kleinschreibung nicht beachtet.
   Subscription |Wählen Sie das gewünschte Azure-Abonnement aus.
   Resource group | Verwenden Sie eine vorhandene Ressourcengruppe in Ihrem Abonnement, oder geben Sie einen Namen ein, um eine neue Ressourcengruppe zu erstellen. Eine Ressourcengruppe enthält verwandte Ressourcen für eine Azure-Lösung. In diesem Beispiel verwenden wir **docs-aml**. Die Rolle *Mitwirkender* oder *Besitzer* ist für die Verwendung einer vorhandenen Ressourcengruppe erforderlich.  Weitere Informationen zum Zugriff finden Sie unter [Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich](how-to-assign-roles.md).
   Region | Wählen Sie die Azure-Region aus, die Ihren Benutzern und den Datenressourcen am nächsten ist, um Ihren Arbeitsbereich zu erstellen.
   | Speicherkonto | Das Standardspeicherkonto für den Arbeitsbereich. Standardmäßig wird ein neues erstellt. |
   | Key Vault | Die Azure Key Vault-Instanz, die vom Arbeitsbereich verwendet wird. Standardmäßig wird eine neue erstellt. |
   | Application Insights | Die Application Insights-Instanz für den Arbeitsbereich. Standardmäßig wird eine neue erstellt. |
   | Container Registry | Die Azure Container Registry-Instanz für den Arbeitsbereich. Standardmäßig wird _keine_ neue anfänglich für den Arbeitsbereich erstellt. Stattdessen wird sie einmal erstellt, sobald Sie sie benötigen, wenn Sie während Training oder Bereitstellung ein Docker-Image erstellen. |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="Konfigurieren Sie Ihren Arbeitsbereich.":::

1. Wählen Sie **Überprüfen + erstellen** aus, nachdem die Konfiguration des Arbeitsbereichs abgeschlossen ist. Verwenden Sie optional die Abschnitte [Netzwerk](#networking) und [Erweitert](#advanced), um weitere Einstellungen für den Arbeitsbereich zu konfigurieren.

1. Überprüfen Sie die Einstellungen, und nehmen Sie zusätzliche Änderungen oder Korrekturen vor. Wenn Sie mit den Einstellungen zufrieden sind, wählen Sie **Erstellen** aus.

   > [!Warning] 
   > Die Erstellung des Arbeitsbereichs in der Cloud kann einige Minuten dauern.

   Wenn der Vorgang abgeschlossen ist, wird eine Erfolgsmeldung zur Bereitstellung angezeigt. 
 
 1. Um den neuen Arbeitsbereich anzuzeigen, wählen Sie **Zu Ressource wechseln** aus.
 
---

### <a name="networking"></a>Netzwerk  

> [!IMPORTANT]  
> Weitere Informationen zur Verwendung eines privaten Endpunkts und eines virtuellen Netzwerks mit Ihrem Arbeitsbereich finden Sie unter [Netzwerkisolation und Datenschutz](how-to-network-security-overview.md).


# <a name="python"></a>[Python](#tab/python)

Das Azure Machine Learning Python SDK bietet die Klasse [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?preserve-view=true&view=azure-ml-py), die mit [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-&preserve-view=true) verwendet werden kann, um einen Arbeitsbereich mit einem privaten Endpunkt zu erstellen. Diese Klasse erfordert ein vorhandenes virtuelles Netzwerk.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Bei der standardmäßigen Netzwerkkonfiguration wird ein __öffentlicher Endpunkt__ verwendet, der über das öffentliche Internet zugänglich ist. Um den Zugriff auf Ihren Arbeitsbereich auf ein von Ihnen erstelltes virtuelles Azure-Netzwerk zu beschränken, können Sie __Privater Endpunkt__ (Vorschauversion) als __Konnektivitätsmethode__ auswählen und dann über __+ Hinzufügen__ den Endpunkt konfigurieren.   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Auswahl des privaten Endpunkts":::  

1. Legen Sie im Formular __Privaten Endpunkt erstellen__ den Speicherort, den Namen und das zu verwendende virtuelle Netzwerk fest. Wenn Sie den Endpunkt mit einer privaten DNS-Zone verwenden möchten, klicken Sie auf __In private DNS-Zone integrieren__, und wählen Sie im Feld __Private DNS-Zone__ die gewünschte Zone aus. Klicken Sie auf __OK__, um den Endpunkt zu erstellen.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Erstellen des privaten Endpunkts":::   

1. Wenn Sie die Netzwerkkonfiguration abgeschlossen haben, können Sie auf __Überprüfen und erstellen__ klicken oder mit der optionalen Konfiguration unter __Erweitert__ fortfahren.

---

> [!IMPORTANT]  
> Das Verwenden eines privaten Endpunkts mit Azure Machine Learning-Arbeitsbereichen befindet sich derzeit in der Public Preview. Diese Vorschau wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.     
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="multiple-workspaces-with-private-endpoint"></a>Mehrere Arbeitsbereiche mit privatem Endpunkt

Wenn Sie einen privaten Endpunkt erstellen, wird eine neue private DNS-Zone namens __privatelink.api.azureml.ms__ erstellt. Diese enthält einen Link zum virtuellen Netzwerk. Wenn Sie mehrere Arbeitsbereiche mit privaten Endpunkten in derselben Ressourcengruppe erstellen, kann nur das virtuelle Netzwerk für den ersten privaten Endpunkt zur DNS-Zone hinzugefügt werden. Um den virtuellen Netzwerken, die von den zusätzlichen Arbeitsbereichen bzw. privaten Endpunkten verwendet werden, Einträge hinzuzufügen, führen Sie folgende Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Ressourcengruppe aus, die den Arbeitsbereich enthält. Wählen Sie dann die Ressource namens __privatelink.api.azureml.ms__ für die private DNS-Zone aus.
2. Klicken Sie unter __Einstellungen__ auf __VNET-Verknüpfungen__.
3. Wählen Sie __Hinzufügen__. Geben Sie auf der Seite __VNET-Verknüpfung hinzufügen__ einen eindeutigen __Linknamen__ an, und wählen Sie dann das __virtuelle Netzwerk__ aus, das hinzugefügt werden soll. Klicken Sie auf __OK__, um die Netzwerkverknüpfung hinzuzufügen.

Weitere Informationen finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md).

### <a name="vulnerability-scanning"></a>Überprüfung auf Sicherheitsrisiken

Azure Security Center bietet einheitliche Funktionen für die Sicherheitsverwaltung und den erweiterten Schutz vor Bedrohungen für Hybrid Cloud-Workloads. Sie sollten zulassen, dass Azure Security Center Ihre Ressourcen überprüft und die Empfehlungen befolgt. Weitere Informationen finden Sie unter [Azure Container Registry-Imagescans durch Security Center](../security-center/defender-for-container-registries-introduction.md) und [Azure Kubernetes Service-Integration in Security Center](../security-center/defender-for-kubernetes-introduction.md).

### <a name="advanced"></a>Erweitert

Standardmäßig werden Metadaten für den Arbeitsbereich in einer Azure Cosmos DB-Instanz gespeichert, die von Microsoft verwaltet wird. Diese Daten werden mit von Microsoft verwalteten Schlüsseln verschlüsselt.

Wählen Sie __Arbeitsbereich mit hohen geschäftlichen Auswirkungen__ im Portal aus oder legen Sie `hbi_workspace=true ` in Python fest, um die von Microsoft in Ihrem Arbeitsbereich gesammelten Daten zu beschränken. Weitere Informationen zu dieser Einstellung finden Sie unter [Verschlüsselung ruhender Daten](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]  
> Die Auswahl von „starken geschäftlichen Auswirkungen“ kann nur beim Erstellen eines Arbeitsbereichs erfolgen. Diese Einstellung kann nach dem Erstellen des Arbeitsbereichs nicht mehr geändert werden.   

#### <a name="use-your-own-key"></a>Eigenen Schlüssel verwenden

Sie können einen eigenen Schlüssel für die Datenverschlüsselung bereitstellen. Damit wird die Azure Cosmos DB-Instanz erstellt, die Metadaten in Ihrem Azure-Abonnement speichert.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Führen Sie die folgenden Schritte aus, um Ihren eigenen Schlüssel bereitzustellen:

> [!IMPORTANT]  
> Vor diesen Schritten müssen Sie zunächst die folgenden Aktionen ausführen:   
>
> 1. Autorisieren Sie die __Machine Learning-App__ (in der Identitäts- und Zugriffsverwaltung) mit den Berechtigungen für Mitwirkende in Ihrem Abonnement.  
> 1. Befolgen Sie die Schritte in [Konfigurieren von kundenseitig verwalteten Schlüsseln](../cosmos-db/how-to-setup-cmk.md), um:
>     * Registrieren des Azure Cosmos DB-Anbieters
>     * Erstellen und Konfigurieren einer Azure Key Vault-Instanz
>     * Generieren eines Schlüssels
>   
>     Sie müssen die Azure Cosmos DB-Instanz nicht manuell erstellen. Es wird eine während der Erstellung des Arbeitsbereichs für Sie erstellt. Diese Azure Cosmos DB-Instanz wird in einer separaten Ressourcengruppe mit einem Namen erstellt, der auf diesem Muster basiert: `<your-workspace-resource-name>_<GUID>`.   
>   
> Diese Einstellung kann nach dem Erstellen des Arbeitsbereichs nicht mehr geändert werden. Wenn Sie die von Ihrem Arbeitsbereich verwendete Azure Cosmos DB löschen, müssen Sie auch den Arbeitsbereich löschen, der sie verwendet.

# <a name="python"></a>[Python](#tab/python)

Verwenden Sie `cmk_keyvault` und `resource_cmk_uri`, um den kundenseitig verwalteten Schlüssel anzugeben.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Klicken Sie auf __Kundenseitig verwalteter Schlüssel__ und dann auf __Klicken Sie hier, um einen Schlüssel auszuwählen__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Kundenseitig verwaltete Schlüssel":::

1. Wählen Sie im Formular __Schlüssel aus Azure Key Vault auswählen__ eine vorhandene Azure Key Vault-instanz, einen darin enthaltenen Schlüssel sowie die Schlüsselversion aus. Dieser Schlüssel wird zum Verschlüsseln der in Azure Cosmos DB gespeicherten Daten verwendet. Klicken Sie abschließend auf die Schaltfläche __Auswählen__, um diesen Schlüssel zu verwenden.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Auswählen des Schlüssels":::

---

### <a name="download-a-configuration-file"></a>Herunterladen einer Konfigurationsdatei

Wenn Sie eine [Compute-Instanz](tutorial-1st-experiment-sdk-setup.md#azure) erstellen, überspringen Sie diesen Schritt.  Die Compute-Instanz hat bereits eine Kopie dieser Datei für Sie erstellt.

# <a name="python"></a>[Python](#tab/python)

Wenn Sie planen, in Ihrer lokalen Umgebung Code zu verwenden, der auf diesen Arbeitsbereich (`ws`) verweist, schreiben Sie die Konfigurationsdatei:

```python
ws.write_config()
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wenn Sie Code in Ihrer lokalen Umgebung verwenden möchten, der auf diesen Arbeitsbereich verweist, wählen Sie im Abschnitt **Übersicht** des Arbeitsbereichs die Option **„config.json“ herunterladen** aus.  

   ![Herunterladen von „config.json“](./media/how-to-manage-workspace/configure.png)

---

Legen Sie die Datei in der Verzeichnisstruktur mit Ihren Python-Skripts oder Jupyter Notebooks ab. Sie kann sich im selben Verzeichnis, in einem Unterverzeichnis namens *.azureml* oder in einem übergeordneten Verzeichnis befinden. Bei der Erstellung einer Compute-Instanz wird diese Datei automatisch dem richtigen Verzeichnis auf dem virtuellen Computer hinzugefügt.

## <a name="connect-to-a-workspace"></a>Stellen Sie eine Verbindung mit einem Arbeitsbereich her.

In Ihrem Python-Code erstellen Sie ein Arbeitsbereichsobjekt, um eine Verbindung zu Ihrem Arbeitsbereich herzustellen.  Dieser Code liest den Inhalt der Konfigurationsdatei, um Ihren Arbeitsbereich zu finden.  Sie werden aufgefordert, sich anzumelden, falls Sie nicht bereits authentifiziert sind.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**Mehrere Mandanten**  Wenn Sie über mehrere Konten verfügen, fügen Sie die Mandanten-ID der Azure Active Directory-Instanz hinzu, die Sie verwenden möchten.  Ihre Mandanten-ID finden Sie im [Azure-Portal](https://portal.azure.com) unter **Azure Active Directory, Externe Identitäten**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[Sovereign Cloud](reference-machine-learning-cloud-parity.md)** Sie benötigen zusätzlichen Code, um sich bei Azure zu authentifizieren, wenn Sie in einer Sovereign Cloud arbeiten.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
Wenn Sie Probleme beim Zugriff auf Ihr Abonnement haben, finden Sie weitere Informationen unter [Einrichten der Authentifizierung für Azure Machine Learning-Ressourcen und -Workflows](how-to-setup-authentication.md) sowie im Notebook [Authentifizierung in Azure Machine Learning](https://aka.ms/aml-notebook-auth).

## <a name="find-a-workspace"></a><a name="view"></a>Suchen nach einem Arbeitsbereich

Zeigen Sie eine Liste mit allen Arbeitsbereichen an, die Sie verwenden können.

# <a name="python"></a>[Python](#tab/python)

Ihre Abonnements finden Sie auf der Seite [Abonnements im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).  Kopieren Sie die ID, und verwenden Sie sie im nachfolgenden Code, um alle für dieses Abonnement verfügbaren Arbeitsbereiche anzuzeigen.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Geben Sie im oberen Suchfeld **Machine Learning** ein.  

1. Wählen Sie **Machine Learning** aus.

   ![Suchen nach Azure Machine Learning-Arbeitsbereich](./media/how-to-manage-workspace/find-workspaces.png)

1. Durchsuchen Sie die Liste der gefundenen Arbeitsbereiche. Sie können basierend auf Abonnement, Ressourcengruppen und Standorten filtern.  

1. Wählen Sie einen Arbeitsbereich aus, um seine Eigenschaften anzuzeigen.

---


## <a name="delete-a-workspace"></a>Löschen eines Arbeitsbereichs

Wenn Sie einen Arbeitsbereich nicht mehr benötigen, löschen Sie ihn.  

# <a name="python"></a>[Python](#tab/python)

Löschen des Arbeitsbereichs `ws`:

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

Die Standardaktion ist nicht das Löschen von Ressourcen, die mit dem Arbeitsbereich verbunden sind, d. h. Containerregistrierung, Speicherkonto, Schlüsseltresor und Application Insights.  Legen Sie `delete_dependent_resources` auf „True“ fest, um auch diese Ressourcen zu löschen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie im [Azure-Portal](https://portal.azure.com/) am oberen Rand des zu löschenden Arbeitsbereichs die Option **Löschen** aus.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Löschen des Arbeitsbereichs":::

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Problembehandlung

### <a name="resource-provider-errors"></a>Fehler der Ressourcenanbieter

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Verschieben des Arbeitsbereichs

> [!WARNING]
> Das Verschieben des Azure Machine Learning-Arbeitsbereichs in ein anderes Abonnement oder das Verschieben des besitzenden Abonnements in einen neuen Mandanten wird nicht unterstützt. Andernfalls können Fehler auftreten.

### <a name="deleting-the-azure-container-registry"></a>Löschen der Azure Container Registry

Der Azure Machine Learning-Arbeitsbereich verwendet für einige Operationen die Azure Container Registry (ACR). Es wird automatisch eine ACR-Instanz erstellt, wenn erstmals eine erforderlich ist.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>Beispiele

Beispiele für das Erstellen eines Arbeitsbereichs:
* Verwenden des Azure-Portals zum [Erstellen eines Arbeitsbereichs und einer Compute-Instanz](tutorial-1st-experiment-sdk-setup.md)
* Verwenden des Python SDK zum [Erstellen eines Arbeitsbereichs in Ihrer eigenen Umgebung](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="next-steps"></a>Nächste Schritte

Sobald Sie über einen Arbeitsbereich verfügen, erfahren Sie, wie ein Modell [trainiert und bereitgestellt wird](tutorial-train-models-with-aml.md).