---
title: Datenverschlüsselung mit Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Azure Machine Learning-Computes und -Datenspeicher die Datenverschlüsselung ruhender und übertragener Daten bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: 211ef9571b5a126686b4583330dc0f80863fd47e
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992045"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Datenverschlüsselung mit Azure Machine Learning

Azure Machine Learning verwendet eine Vielzahl von Azure-Datenspeicherdiensten und -Computeressourcen beim Trainieren von Modellen und bei der Durchführung von Rückschlüssen. Jeder dieser Ansätze hat seine eigene Vorgehensweise beim Bereitstellen der Verschlüsselung von ruhenden und übertragenen Daten. In diesem Artikel erfahren Sie mehr über die einzelnen Methoden und welche für Ihr Szenario am besten geeignet ist.

> [!IMPORTANT]
> Für die Verschlüsselung in der Produktionsqualität während des __Trainings__ empfiehlt Microsoft, den Azure Machine Learning-Computecluster zu verwenden. Für die Verschlüsselung in der Produktionsqualität während der __Inferenz__ empfiehlt Microsoft die Verwendung von Azure Kubernetes Service.
>
> Die Azure Machine Learning-Computeinstanz ist eine Dev/Test-Umgebung. Wenn Sie sie verwenden, wird empfohlen, dass Sie Ihre Dateien in einer Dateifreigabe speichern (z. B. Notebooks und Skripte). Ihre Daten sollten in einem Datenspeicher gespeichert werden.

## <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

> [!IMPORTANT]
> Wenn Ihr Arbeitsbereich vertrauliche Daten enthält, wird empfohlen, das [hbi_workspace-Flag](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) während der Erstellung Ihres Arbeitsbereichs festzulegen. Das `hbi_workspace`-Flag kann nur beim Erstellen eines Arbeitsbereichs festgelegt werden. Es kann für einen vorhandenen Arbeitsbereich nicht geändert werden.

Das Flag `hbi_workspace` steuert die Menge der [von Microsoft zu Diagnosezwecken gesammelten Daten](#microsoft-collected-data) und ermöglicht eine [zusätzliche Verschlüsselung in von Microsoft verwalteten Umgebungen](../security/fundamentals/encryption-atrest.md). Darüber hinaus ermöglicht es folgende Aktionen:

* Startet die Verschlüsselung des lokalen Scratch-Datenträgers in Ihrem Azure Machine Learning-Computecluster, sofern Sie in diesem Abonnement keine vorherigen Cluster erstellt haben. Andernfalls müssen Sie ein Supportticket erstellen, um die Verschlüsselung des Scratch-Datenträgers Ihrer Computecluster zu aktivieren. 
* Bereinigt Ihren lokalen Scratch-Datenträger zwischen den Ausführungen.
* Führt unter Verwendung Ihres Schlüsseltresors die sichere Übergabe der Anmeldeinformationen für Speicherkonto, Containerregistrierung und SSH-Konto von der Ausführungsebene zu Ihren Computeclustern durch.
* Aktiviert IP-Filterung, um sicherzustellen, dass die zugrunde liegenden Batch-Pools nicht von anderen externen Diensten als AzureMachineLearningService aufgerufen werden können.
* Beachten Sie, dass Compute-Instanzen im HBI-Arbeitsbereich nicht unterstützt werden.

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning speichert Momentaufnahmen, Ausgaben und Protokolle in dem Azure Blob Storage-Konto, das mit dem Azure Machine Learning-Arbeitsbereich und Ihrem Abonnement verknüpft ist. Alle in Azure Blob Storage gespeicherten Daten werden im Ruhezustand mit von Microsoft verwalteten Schlüsseln verschlüsselt.

Informationen dazu, wie Sie Ihre eigenen Schlüssel für die in Azure Blob Storage gespeicherten Daten verwenden können, finden Sie unter [Konfigurieren von Kunden verwalteter Schlüssel für die Azure Storage-Verschlüsselung mithilfe des Azure-Portals](../storage/common/customer-managed-keys-configure-key-vault.md).

Trainingsdaten werden in der Regel auch in Azure Blob Storage gespeichert, damit sie zum Trainieren von Computezielen zur Verfügung stehen. Dieser Speicher wird nicht von Azure Machine Learning verwaltet, sondern er wird Computezielen als Remotedateisystem bereitgestellt.

Wenn Sie Ihren Schlüssel __rotieren oder widerrufen__ müssen, können Sie dies jederzeit veranlassen. Wenn ein Schlüssel rotiert wird, beginnt das Speicherkonto, den neuen Schlüssel (neueste Version) zur Verschlüsselung der ruhenden Daten zu verwenden. Wenn ein Schlüssel widerrufen (deaktiviert) wird, kümmert sich das Speicherkonto um fehlgeschlagene Anforderungen. Normalerweise dauert es eine Stunde, bis die Rotation oder der Widerruf wirksam wird.

Informationen zum erneuten Generieren der Zugriffsschlüssel finden Sie unter [Erneutes Generieren von Speicherzugriffsschlüsseln](how-to-change-storage-access-key.md).

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning speichert Metadaten in einer Azure Cosmos DB-Instanz. Diese Instanz ist mit einem Microsoft-Abonnement verbunden, das von Azure Machine Learning verwaltet wird. Alle in Azure Cosmos DB gespeicherten Daten werden im Ruhezustand mit von Microsoft verwalteten Schlüsseln verschlüsselt.

Um Ihre eigenen (vom Kunden verwalteten) Schlüssel zur Verschlüsselung der Azure-Cosmos-DB-Instanz zu verwenden, können Sie eine dedizierte Cosmos-DB-Instanz zur Verwendung mit Ihrem Arbeitsbereich erstellen. Wir empfehlen diesen Ansatz, wenn Sie Ihre Daten, z. B. Informationen zum Ausführungsverlauf, außerhalb der mehrinstanzenfähigen Cosmos DB-Instanz speichern möchten, die in unserem Microsoft-Abonnement gehostet wird. 

Um die Bereitstellung einer Cosmos DB-Instanz in Ihrem Abonnement mit vom Kunden verwalteten Schlüsseln zu ermöglichen, führen Sie die folgenden Aktionen durch:

* Registrieren Sie die Anbieter von Microsoft.MachineLearning- und Microsoft.DocumentDB-Ressourcen in Ihrem Abonnement, falls dies noch nicht geschehen ist.

* Verwenden Sie die folgenden Parameter, wenn Sie den Azure Machine Learning-Arbeitsbereich erstellen. Beide Parameter sind obligatorisch und werden in SDK-, CLI-, REST-APIs und Resource Manager-Vorlagen unterstützt.

    * `resource_cmk_uri`: Dieser Parameter ist der vollständige Ressourcen-URI des vom Kunden verwalteten Schlüssels in Ihrem Schlüsseltresor, einschließlich der [Versionsinformationen für den Schlüssel](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Dieser Parameter ist die Ressourcen-ID des Schlüsseltresors in Ihrem Abonnement. Dieser Schlüsseltresor muss sich in derselben Region und demselben Abonnement befinden, das Sie für den Azure Machine Learning-Arbeitsbereich verwenden werden. 
    
        > [!NOTE]
        > Diese Schlüsseltresorinstanz kann sich von dem Schlüsseltresor unterscheiden, der von Azure Machine Learning beim Bereitstellen des Arbeitsbereichs erstellt wird. Wenn Sie dieselbe Schlüsseltresorinstanz für den Arbeitsbereich verwenden möchten, übergeben Sie denselben Schlüsselspeicher beim Bereitstellen des Arbeitsbereichs mithilfe des [key_vault-Parameters](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Wenn Sie Ihren Schlüssel __rotieren oder widerrufen__ müssen, können Sie dies jederzeit veranlassen. Wenn ein Schlüssel rotiert wird, beginnt Cosmos DB, den neuen Schlüssel (neueste Version) zur Verschlüsselung der ruhenden Daten zu verwenden. Wenn ein Schlüssel widerrufen (deaktiviert) wird, kümmert sich Cosmos DB um fehlgeschlagene Anforderungen. Normalerweise dauert es eine Stunde, bis die Rotation oder der Widerruf wirksam wird.

Weitere Informationen zu von Kunden verwalteten Schlüsseln mit Cosmos DB finden Sie unter [Konfigurieren von kundenseitig verwalteten Schlüsseln für Ihr Azure Cosmos DB-Konto](../cosmos-db/how-to-setup-cmk.md).

### <a name="azure-container-registry"></a>Azure Container Registry

Alle Containerimages in Ihrer Registrierung (Azure Container Registry) werden im Ruhezustand verschlüsselt. Azure verschlüsselt ein Image automatisch vor dessen Speicherung, und entschlüsselt es, wenn Azure Machine Learning das Image herunterlädt (pullt).

Um Ihre eigenen (vom Kunden verwalteten) Schlüssel zur Verschlüsselung Ihrer Azure Container Registry zu verwenden, müssen Sie Ihre eigene ACR erstellen und diese während der Bereitstellung des Arbeitsbereichs anfügen oder die Standardinstanz verschlüsseln, die zum Zeitpunkt der Arbeitsbereichsbereitstellung erstellt wird.

> [!IMPORTANT]
> Azure Machine Learning erfordert, dass das Administratorkonto in Ihrer Azure Container Registry aktiviert ist. Diese Einstellung ist standardmäßig deaktiviert, wenn Sie eine Containerregistrierung erstellen. Informationen zum Aktivieren des Administratorkontos finden Sie unter [Administratorkonto](../container-registry/container-registry-authentication.md#admin-account).
>
> Nachdem eine Azure Container Registry für einen Arbeitsbereich erstellt wurde, dürfen Sie diese nicht mehr löschen. Dadurch wird Ihr Azure Machine Learning-Arbeitsbereich beschädigt.

Ein Beispiel für die Erstellung eines Arbeitsbereichs unter Verwendung einer bestehenden Azure Container Registry finden Sie in den folgenden Artikeln:

* [Erstellen eines Arbeitsbereichs für Azure Machine Learning mit der Azure CLI](how-to-manage-workspace-cli.md).
* [Erstellen eines Arbeitsbereichs mit dem Python SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Verwenden einer Azure Resource Manager-Vorlage zum Erstellen eines Arbeitsbereichs für Azure Machine Learning](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Azure Container Instances

Sie können eine bereitgestellte ACI-Ressource (Azure Container Instances) mit vom Kunden verwalteten Schlüsseln verschlüsseln. Der für ACI verwendete vom Kunden verwaltete Schlüssel kann in Azure Key Vault für Ihren Arbeitsbereich gespeichert werden. Informationen zum Generieren eines Schlüssels finden Sie unter [Verschlüsseln von Daten mit einem kundenseitig verwalteten Schlüssel](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Erstellen Sie zur Verwendung des Schlüssels bei der Bereitstellung eines Modells in Azure Container Instance eine neue Bereitstellungskonfiguration mit `AciWebservice.deploy_configuration()`. Geben Sie die Schlüsselinformationen unter Verwendung der folgenden Parameter an:

* `cmk_vault_base_url`: Die URL des Schlüsseltresors, der den Schlüssel enthält.
* `cmk_key_name`: Der Name des Schlüssels.
* `cmk_key_version`: Die Version des Schlüssels.

Weitere Informationen zum Erstellen und Verwenden einer Bereitstellungskonfiguration finden Sie in den folgenden Artikeln:

* [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)-Referenz
* [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md)
* [Bereitstellen eines Modells in Azure Container Instances](how-to-deploy-azure-container-instance.md)

Weitere Informationen zur Verwendung eines vom Kunden verwalteten Schlüssels mit ACI finden Sie unter [Verschlüsseln von Daten mit einem kundenseitig verwalteten Schlüssel](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Sie können eine bereitgestellte Ressource des Azure Kubernetes Service jederzeit mit vom Kunden verwalteten Schlüsseln verschlüsseln. Weitere Informationen finden Sie unter [Bring Your Own Keys (BYOK) mit Azure Kubernetes Service (AKS)](../aks/azure-disk-customer-managed-keys.md). 

Dieser Prozess ermöglicht es Ihnen, sowohl die Daten als auch den Betriebssystemdatenträger der bereitgestellten virtuellen Computer im Kubernetes-Cluster zu verschlüsseln.

> [!IMPORTANT]
> Dieser Prozess funktioniert nur mit AKS K8s Version 1.17 oder höher. Azure Machine Learning hat am 13. Januar 2020 die Unterstützung für AKS 1.17 hinzugefügt.

### <a name="machine-learning-compute"></a>Machine Learning Compute

Der Betriebssystem-Datenträger für jeden in Azure Storage gespeicherten Computeknoten wird mit von Microsoft verwalteten Schlüsseln in Speicherkonten von Azure Machine Learning verschlüsselt. Dieses Computeziel ist kurzlebig, und Cluster werden in der Regel zentral herunterskaliert, wenn keine Ausführungen in der Warteschlange stehen. Die Bereitstellung des zugrunde liegenden virtuellen Computers wird aufgehoben, und der Betriebssystem-Datenträger wird gelöscht. Azure Disk Encryption wird für den Betriebssystem-Datenträger nicht unterstützt.

Jeder virtuelle Computer verfügt auch über einen lokalen temporären Datenträger für Betriebssystem-Vorgänge. Wenn Sie möchten, können Sie den Datenträger zum Bereitstellen von Trainingsdaten verwenden. Der Datenträger wird standardmäßig für Arbeitsbereiche mit dem Parameter `hbi_workspace` verschlüsselt, der auf `TRUE` festgelegt ist. Diese Umgebung ist auf die Dauer Ihrer Ausführung befristet, und die Unterstützung für die Verschlüsselung beschränkt sich auf vom System verwaltete Schlüssel.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks kann in Azure Machine Learning-Pipelines verwendet werden. Das von Azure Databricks verwendete Databricks-Dateisystem (Databricks File System, DBFS) wird standardmäßig mithilfe eines von Microsoft verwalteten Schlüssels verschlüsselt. Informationen zum Konfigurieren von Azure Databricks für die Verwendung von kundenseitig verwalteten Schlüsseln finden Sie unter [Konfigurieren der kundenseitig verwalteten Schlüsseln im Standard-DBFS (Stamm)](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="microsoft-generated-data"></a>Von Microsoft generierte Daten

Bei der Verwendung von Diensten wie automatisiertes ML generiert Microsoft möglicherweise vorübergehende, vorverarbeitete Daten für das Training mehrerer Modelle. Diese Daten werden in einem Datenspeicher in Ihrem Arbeitsbereich gespeichert, der es Ihnen ermöglicht, die Zugriffssteuerung und Verschlüsselung entsprechend durchzusetzen.

Möglicherweise möchten Sie auch [Diagnoseinformationen verschlüsseln, die von Ihrem bereitgestellten Endpunkt](how-to-enable-app-insights.md) in Ihrer Azure Application Insights-Instanz protokolliert werden.

## <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung

Azure Machine Learning verwendet TLS zum Sichern der internen Kommunikation zwischen Azure Machine Learning-Microservices. Der gesamte Azure Storage-Zugriff erfolgt ebenfalls über einen sicheren Kanal.

Zum Sichern externer Aufrufe an den Bewertungsendpunkt verwendet Azure Machine Learning TLS. Weitere Informationen finden Sie unter [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](./how-to-secure-web-service.md).

## <a name="data-collection-and-handling"></a>Datensammlung und -verarbeitung

### <a name="microsoft-collected-data"></a>Von Microsoft gesammelte Daten

Microsoft sammelt möglicherweise Informationen, z. B. Ressourcennamen (wie den Datasetnamen oder den Namen des Machine Learning-Experiments) oder Variablen von Auftragsumgebungen zu Diagnosezwecken, die nicht den Benutzer identifizieren. Alle diese Daten werden mit von Microsoft verwalteten Schlüsseln in einem Speicher gespeichert, der in Microsoft-eigenen Abonnements gehostet wird, und folgen den [Standarddatenschutzrichtlinien und Datenverarbeitungsstandards von Microsoft](https://privacy.microsoft.com/privacystatement).

Microsoft empfiehlt außerdem, keine vertraulichen Informationen (z. B. Geheimnisse von Kontoschlüsseln) in Umgebungsvariablen zu speichern. Umgebungsvariablen werden von uns protokolliert, verschlüsselt und gespeichert. Vermeiden Sie beim Benennen von [run_id](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) daher vertrauliche Informationen wie Benutzernamen oder geheime Projektnamen. Diese Informationen können in Telemetrieprotokollen enthalten sein, auf die Microsoft-Supporttechniker zugreifen können.

Sie können sich von der Erfassung von Diagnosedaten abmelden, indem Sie den Parameter `hbi_workspace` während der Bereitstellung des Arbeitsbereichs auf `TRUE` festlegen. Diese Funktionalität wird unterstützt, wenn AzureML Python-SDK-, CLI-, REST-APIs oder Azure Resource Manager-Vorlagen verwendet werden.

## <a name="using-azure-key-vault"></a>Verwenden von Azure Key Vault

Azure Machine Learning verwendet die mit dem Arbeitsbereich verknüpfte Azure Key Vault-Instanz zum Speichern von Anmeldeinformationen verschiedener Art:

* Der verknüpften Speicherkonto-Verbindungszeichenfolge
* Kennwörter in Azure Container Repository-Instanzen
* Verbindungszeichenfolgen zur Verbindung mit Datenspeichern.

SSH-Kennwörter und Schlüssel für Computeziele wie Azure HDInsight und virtuelle Computer werden in einem separaten Schlüsseltresor gespeichert, der dem Microsoft-Abonnement zugeordnet ist. Azure Machine Learning speichert weder Kennwörter noch Schlüssel, die von Benutzern bereitgestellt wurden. Stattdessen werden eigene SSH-Schlüssel generiert, autorisiert und gespeichert, um eine Verbindung mit virtuellen Computern und HDInsight herzustellen und die Experimente auszuführen.

Jedem Arbeitsbereich ist eine vom System zugewiesene verwaltete Identität zugeordnet, die den gleichen Namen hat wie der Arbeitsbereich. Diese verwaltete Identität verfügt über Zugriff auf alle Schlüssel, Geheimnisse und Zertifikate im Schlüsseltresor.

## <a name="next-steps"></a>Nächste Schritte

* [Verbindung mit Azure-Speicher herstellen](how-to-access-data.md)
* [Abrufen von Daten aus einem Datenspeicher](how-to-create-register-datasets.md)
* [Verbinden mit Daten](how-to-connect-data-ui.md)
* [Trainieren mit Datasets](how-to-train-with-datasets.md)