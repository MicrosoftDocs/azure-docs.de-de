---
title: Integrieren von Azure Key Vault mit Kubernetes
description: In diesem Tutorial greifen Sie auf Geheimnisse in Azure Key Vault zu und rufen sie daraus ab, indem Sie den Secrets Store CSI-Treiber (Container Storage Interface) für die Einbindung in Kubernetes-Pods verwenden.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: c628ba780ae64fceb32322fdb2004d69e2ebf24b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452757"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Tutorial: Konfigurieren und Ausführen des Azure Key Vault-Anbieters für den Secrets Store CSI-Treiber auf Kubernetes

> [!IMPORTANT]
> Der CSI-Treiber ist ein Open-Source-Projekt, das vom technischen Support von Azure nicht unterstützt wird. Senden Sie Feedback und Probleme im Zusammenhang mit der Key Vault-Integration des CSI-Treibers über den GitHub-Link am unteren Rand der Seite. Dieses Tool wird den Benutzern zur Verfügung gestellt, damit sie es selbst in Clustern installieren und Feedback von unserer Community erhalten können.

In diesem Tutorial greifen Sie auf Geheimnisse in Azure Key Vault zu und rufen sie daraus ab, indem Sie den Secrets Store CSI-Treiber (Container Storage Interface) für die Einbindung der Geheimnisse in Kubernetes-Pods verwenden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Dienstprinzipals oder Verwenden von verwalteten Identitäten.
> * Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) über die Azure CLI.
> * Installieren von Helm und des Secrets Store CSI-Treibers.
> * Erstellen einer Azure Key Vault-Instanz und Festlegen Ihrer Geheimnisse.
> * Erstellen Ihres eigenen „SecretProviderClass“-Objekts.
> * Zuweisen Ihres Dienstprinzipals oder Verwenden von verwalteten Identitäten.
> * Bereitstellen Ihres Pods mit eingebundenen Geheimnissen aus Ihrem Schlüsseltresor.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli-windows?view=azure-cli-latest), bevor Sie mit diesem Tutorial beginnen.

## <a name="create-a-service-principal-or-use-managed-identities"></a>Erstellen eines Dienstprinzipals oder Verwenden von verwalteten Identitäten

Wenn Sie verwaltete Identitäten verwenden möchten, können Sie mit dem nächsten Abschnitt fortfahren.

Erstellen Sie einen Dienstprinzipal, um zu steuern, auf welche Ressourcen von Ihrer Azure Key Vault-Instanz aus zugegriffen werden kann. Die Zugriffsberechtigungen dieses Dienstprinzipals sind durch die Rollen eingeschränkt, die ihm zugewiesen sind. Mit diesem Feature haben Sie die Kontrolle darüber, wie der Dienstprinzipal Ihre Geheimnisse verwalten kann. Im folgenden Beispiel lautet der Name des Dienstprinzipals *contosoServicePrincipal*.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Daraufhin wird eine Reihe von Schlüssel-Wert-Paaren zurückgegeben:

![Screenshot, der die appId und das Kennwort (password) für contosoServicePrincipal anzeigt.](../media/kubernetes-key-vault-1.png)

Kopieren Sie die **appId**- und **password**-Anmeldeinformationen zur späteren Verwendung.

## <a name="flow-for-using-managed-identity"></a>Flow für die Verwendung einer verwalteten Identität

Im folgenden Diagramm wird der Flow für eine verwaltete Identität bei der AKS-Key Vault-Integration veranschaulicht:

![Diagramm, das den Flow für eine verwaltete Identität bei der AKS-Key Vault-Integration veranschaulicht](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) über die Azure CLI

Sie müssen Azure Cloud Shell nicht verwenden. Ihre Eingabeaufforderung (Terminal) mit einer installierten Azure CLI reicht aus. 

Führen Sie die Abschnitte „Erstellen einer Ressourcengruppe“, „Erstellen eines AKS-Clusters“ und „Herstellen einer Verbindung mit dem Cluster“ in [Bereitstellen eines Azure Kubernetes Service-Clusters mithilfe der Azure CLI](../../aks/kubernetes-walkthrough.md) aus. 

> [!NOTE] 
> Wenn Sie beabsichtigen, eine Pod-Identität anstelle eines Dienstprinzipals zu verwenden, stellen Sie sicher, dass Sie sie beim Erstellen des Kubernetes-Clusters aktivieren, wie im folgenden Befehl gezeigt:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Legen Sie Ihre PATH-Umgebungsvariable](https://www.java.com/en/download/help/path.xml) auf die heruntergeladene Datei *kubectl.exe* fest.
1. Überprüfen Sie Ihre Kubernetes-Version, indem Sie den folgenden Befehl verwenden, der die Client- und Serverversion ausgibt. Die Clientversion ist die Datei *kubectl.exe*, die Sie installiert haben, und die Serverversion ist die Azure Kubernetes Services-Instanz (AKS), auf der Ihr Cluster ausgeführt wird.
    ```azurecli
    kubectl version
    ```
1. Stellen Sie sicher, dass Ihre Kubernetes-Version 1.1.16.0 oder höher ist. Stellen Sie bei Windows-Clustern sicher, dass Sie mindestens Kubernetes-Version 1.1.18.0 verwenden. Mit dem folgenden Befehl wird ein Upgrade sowohl für den Kubernetes-Cluster als auch für den Knotenpool durchgeführt. Die Ausführung des Befehls kann einige Minuten dauern. In diesem Beispiel ist die Ressourcengruppe *contosoResourceGroup*, und der Kubernetes-Cluster ist *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Um die Metadaten des von Ihnen erstellten AKS-Clusters anzuzeigen, verwenden Sie den folgenden Befehl. Kopieren Sie **principalId**, **clientId**, **subscriptionId** und **nodeResourceGroup** zur späteren Verwendung. Wurden bei der Erstellung des AKS-Clusters keine verwalteten Identitäten aktiviert, sind **principalId** und **clientId** NULL. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    In der Ausgabe sind beide Parameter hervorgehoben:
    
    ![Screenshot der Azure CLI mit hervorgehobenen Werten von „principalId“ und „clientId“](../media/kubernetes-key-vault-2.png) ![Screenshot der Azure CLI mit hervorgehobenen Werten von „subscriptionId“ und „nodeResourceGroup“](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Installieren von Helm und des Secrets Store CSI-Treibers

Um den Secrets Store CSI-Treibers zu installieren, müssen Sie zuerst [Helm](https://helm.sh/docs/intro/install/) installieren.

Mit der [Secrets Store CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md)-Treiberschnittstelle können Sie die Geheimnisse abrufen, die in Ihrer Azure Key Vault-Instanz gespeichert sind. Anschließend können Sie die Treiberschnittstelle nutzen, um die Geheimnisinhalte in Kubernetes-Pods einzubinden.

1. Überprüfen Sie die Helm-Version, und stellen Sie sicher, dass es v3 oder höher ist:
    ```azurecli
    helm version
    ```
1. Installieren Sie den Secrets Store CSI-Treiber und den Azure Key Vault-Anbieter für den Treiber:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Erstellen einer Azure Key Vault-Instanz und Festlegen Ihrer Geheimnisse

Befolgen Sie zum Erstellen Ihres eigenen Schlüsseltresors und zum Festlegen Ihrer Geheimnisse die Anleitungen unter [Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe der Azure CLI](../secrets/quick-create-cli.md).

> [!NOTE] 
> Sie müssen weder Azure Cloud Shell verwenden noch eine neue Ressourcengruppe erstellen. Sie können die Ressourcengruppe verwenden, die Sie zuvor für den Kubernetes-Cluster erstellt haben.

## <a name="create-your-own-secretproviderclass-object"></a>Erstellen Ihres eigenen „SecretProviderClass“-Objekts

Um Ihr eigenes „SecretProviderClass“-Objekt mit anbieterspezifischen Parametern für den Secrets Store CSI-Treiber zu erstellen, [verwenden Sie diese Vorlage](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass_service_principal.yaml). Dieses Objekt ermöglicht den Identitätszugriff auf Ihren Schlüsseltresor.

Tragen Sie in der YAML-Beispieldatei „SecretProviderClass“ die fehlenden Parameter ein. Die folgenden Parameter sind erforderlich:

* **userAssignedIdentityID**: # [REQUIRED] If you're using a service principal, use the client ID to specify which user-assigned managed identity to use. If you're using a user-assigned identity as the VM's managed identity, specify the identity's client ID. If the value is empty, it defaults to use the system-assigned identity on the VM 
* **keyvaultName**: Der Name Ihres Schlüsseltresors.
* **objects**: Der Container für den gesamten Geheimnisinhalt, den Sie einbinden möchten.
    * **objectName**: Der Name des Geheimnisinhalts.
    * **objectType**: Der Objekttyp (Geheimnis, Schlüssel, Zertifikat).
* **resourceGroup**: The name of the resource group  # [REQUIRED for version < 0.0.4] the resource group of the KeyVault
* **subscriptionId**: The subscription ID of your key vault # [REQUIRED for version < 0.0.4] the subscription ID of the KeyVault
* **tenantID**: Die Mandanten-ID oder Verzeichnis-ID Ihres Schlüsseltresors.

Eine Dokumentation aller erforderlichen Felder finden Sie hier: [Link](https://github.com/Azure/secrets-store-csi-driver-provider-azure#create-a-new-azure-key-vault-resource-or-use-an-existing-one)

Die aktualisierte Vorlage ist im folgenden Code dargestellt. Laden Sie sie als YAML-Datei herunter, und füllen Sie die erforderlichen Felder aus. In diesem Beispiel ist der Schlüsseltresor **contosoKeyVault5**. Er verfügt über zwei Geheimnisse, **secret1** und **secret2**.

> [!NOTE] 
> Wenn Sie verwaltete Identitäten verwenden, legen Sie den Wert von **usePodIdentity** auf *true* fest, und legen Sie den Wert von **userAssignedIdentityID** auf ein Paar Anführungszeichen ( **""** ) fest. 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If you're using a service principal, use the client id to specify which user-assigned managed identity to use. If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the preceding command will return the client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
Die folgende Abbildung zeigt die Konsolenausgabe für **az keyvault show --name contosoKeyVault5** mit den relevanten hervorgehobenen Metadaten:

![Screenshot der Konsolenausgabe für „az keyvault show --name contosoKeyVault5“](../media/kubernetes-key-vault-4.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Zuweisen Ihres Dienstprinzipals oder Verwenden von verwalteten Identitäten

### <a name="assign-a-service-principal"></a>Zuweisen eines Dienstprinzipals

Wenn Sie einen Dienstprinzipal verwenden, gewähren Sie diesem Berechtigungen zum Zugreifen auf Ihren Schlüsseltresor und zum Abrufen von Geheimnissen. Weisen Sie die Rolle *Leser* zu, und gewähren Sie dem Dienstprinzipal Berechtigungen zum Abrufen (*get*) von Geheimnissen aus Ihrem Schlüsseltresor, indem Sie den folgenden Befehl ausführen:

1. Weisen Sie Ihren Dienstprinzipal Ihrem vorhandenen Schlüsseltresor zu. Der Parameter **$AZURE_CLIENT_ID** ist die **appId**, die Sie nach dem Erstellen Ihres Dienstprinzipals kopiert haben.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Die Ausgabe des Befehls ist in der folgenden Abbildung dargestellt: 

    ![Screenshot des principalId-Werts](../media/kubernetes-key-vault-5.png)

1. Gewähren Sie dem Dienstprinzipal Berechtigungen zum Abrufen von Geheimnissen:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    az keyvault set-policy -n $KEYVAULT_NAME --key-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Sie haben Ihren Dienstprinzipal nun so konfiguriert, dass er über Berechtigungen zum Lesen von Geheimnissen auf Ihrem Schlüsseltresor verfügt. **$AZURE_CLIENT_SECRET** ist das Kennwort für Ihren Dienstprinzipal. Fügen Sie die Anmeldeinformationen des Dienstprinzipals als Kubernetes-Geheimnis hinzu, auf das der Secrets Store CSI-Treiber zugreifen kann:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

> [!NOTE] 
> Wenn Sie den Kubernetes Pod bereitstellen und eine Fehlermeldung wegen einer ungültigen geheimen Clientschlüssel-ID erhalten, besitzen Sie möglicherweise eine ältere geheime Clientschlüssel-ID, die abgelaufen ist oder zurückgesetzt wurde. Um dieses Problem zu beheben, löschen Sie Ihr Geheimnis *secrets-store-creds*, und erstellen Sie ein neues mit der aktuellen ID für den geheimen Clientschlüssel. Führen Sie den folgenden Befehl aus, um Ihr *secrets-store-creds* zu löschen:
>
> ```azurecli
> kubectl delete secrets secrets-store-creds
> ```

Falls Sie die ID des geheimen Clientschlüssels für Ihren Dienstprinzipal vergessen haben, können Sie sie mit dem folgenden Befehl zurücksetzen:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="use-managed-identities"></a>Verwenden verwalteter Identitäten

Wenn Sie verwaltete Identitäten verwenden, weisen Sie dem AKS-Cluster, den Sie erstellt haben, spezifische Rollen zu. 

1. Ihrem AKS-Cluster muss die Rolle [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator) zugewiesen sein, um eine benutzerseitig zugewiesene verwaltete Identität erstellen, auflisten oder lesen zu können. Stellen Sie sicher, dass die **$clientId** die clientId des Kubernetes-Clusters ist. Der Bereich ist Teil des Azure-Abonnementdiensts, genauer gesagt der Knotenressourcengruppe, die bei der Erstellung des AKS-Clusters erstellt wurde. Durch diesen Bereich wird sichergestellt, dass sich die unten zugewiesenen Rollen nur auf Ressourcen in dieser Gruppe auswirken. 

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Installieren Sie die Azure AD-Identität (Azure Active Directory) in AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Erstellen Sie eine Azure AD-Identität. Kopieren Sie in der Ausgabe die **clientId** und **principalId** zur späteren Verwendung.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Weisen Sie der Azure AD-Identität, die Sie im vorherigen Schritt für Ihren Schlüsseltresor erstellt haben, die Rolle *Leser* zu, und erteilen Sie dann der Identität Berechtigungen, um Geheimnisse aus Ihrem Schlüsseltresor abzurufen. Verwenden Sie die **clientId** und **principalId** aus der Azure AD-Identität.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Bereitstellen Ihres Pods mit eingebundenen Geheimnissen aus Ihrem Schlüsseltresor

Um Ihr „SecretProviderClass“-Objekt zu konfigurieren, führen Sie den folgenden Befehl aus:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-a-service-principal"></a>Verwenden eines Dienstprinzipals

Wenn Sie einen Dienstprinzipal verwenden, verwenden Sie den folgenden Befehl, um Ihre Kubernetes-Pods mit der „SecretProviderClass“ und den von Ihnen zuvor konfigurierten „secrets-store-creds“ bereitzustellen. Hier finden Sie die Bereitstellungsvorlagen:
* Für [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-inline-volume-service-principal.yaml)
* Für [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml)

```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="use-managed-identities"></a>Verwenden verwalteter Identitäten

Wenn Sie verwaltete Identitäten verwenden, erstellen Sie eine *AzureIdentity* in Ihrem Cluster, die auf die zuvor von Ihnen erstellte Identität verweist. Erstellen Sie dann eine *AzureIdentityBinding*, die auf die von Ihnen erstellte AzureIdentity verweist. Tragen Sie die Parameter in der folgenden Vorlage ein, und speichern Sie diese dann als *podIdentityAndBinding.yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Führen Sie den folgenden Befehl aus, um die Bindung auszuführen:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Als Nächstes stellen Sie den Pod bereit. Der folgende Code ist die YAML-Datei für die Bereitstellung, die die Bindung der Pod-Identität aus dem vorangegangenen Schritt nutzt. Speichern Sie diese Datei als *podBindingDeployment.yaml*.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
        nodePublishSecretRef:           # Only required when using service principal mode
          name: secrets-store-creds     # Only required when using service principal mode
```

Führen Sie den folgenden Befehl aus, um Ihren Pod bereitzustellen:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>Überprüfen des Pod-Status und des Geheimnisinhalts 

Um die von Ihnen bereitgestellten Pods anzuzeigen, führen Sie folgenden Befehl aus:
```azurecli
kubectl get pods
```

Um den Status ihres Pods zu überprüfen, führen Sie den folgenden Befehl aus:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Screenshot der Azure CLI-Ausgabe, in dem der Zustand des Pods als „Wird ausgeführt“ und alle Ereignisse als „Normal“ angezeigt werden. ](../media/kubernetes-key-vault-6.png)

Im Ausgabefenster sollte sich der bereitgestellte Pod im Zustand *Wird ausgeführt* befinden. Unten im Abschnitt **Ereignisse** sind alle Ereignistypen als *Normal* angezeigt.

Nachdem Sie sich vergewissert haben, dass der Pod ausgeführt wird, können Sie überprüfen, ob der Pod die Geheimnisse aus Ihrem Schlüsseltresor enthält.

Um alle im Pod enthaltenen Geheimnisse anzuzeigen, führen Sie den folgenden Befehl aus:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Um den Inhalt eines bestimmten Geheimnisses anzuzeigen, führen Sie den folgenden Befehl aus:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Überprüfen Sie, ob der Inhalt des Geheimnisses angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

Um sicherzustellen, dass Ihr Schlüsseltresor wiederherstellbar ist:
> [!div class="nextstepaction"]
> [Aktivieren des vorläufigen Löschens](./key-vault-recovery.md)