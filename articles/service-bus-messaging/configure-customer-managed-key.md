---
title: Konfigurieren Ihres eigenen Schlüssels zum Verschlüsseln ruhender Azure Service Bus-Daten
description: Dieser Artikel enthält Informationen dazu, wie Sie einen eigenen Schlüssel für die Verschlüsselung ruhender Azure Service Bus-Daten konfigurieren.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 35680819350582062dd4227c65f9e72ae8b3ee5c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489699"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln für die Verschlüsselung ruhender Azure Service Bus-Daten mithilfe des Azure-Portals
Azure Service Bus Premium ermöglicht die Verschlüsselung ruhender Daten mit der Azure-Speicherdienstverschlüsselung (Azure Storage Service Encryption, SSE). Service Bus Premium verwendet Azure Storage zum Speichern der Daten. Standardmäßig werden alle mit Azure Storage gespeicherten Daten durch von Microsoft verwaltete Schlüssel verschlüsselt. 

## <a name="overview"></a>Übersicht
Azure Service Bus unterstützt jetzt die Option zum Verschlüsseln ruhender Daten mit von Microsoft oder vom Kunden verwalteten Schlüsseln (Bring Your Own Key, BYOK). Mit dieser Funktion können Sie kundenseitig verwaltete Schlüssel, die zum Verschlüsseln ruhender Azure Service Bus-Daten verwendet werden, erstellen, rotieren, deaktivieren und den Zugriff darauf widerrufen.

Die Aktivierung der BYOK-Funktion ist ein einmaliger Setupvorgang für Ihren Namespace.

> [!NOTE]
> Für die dienstseitige Verschlüsselung bestehen einige Einschränkungen für vom Kunden verwaltete Schlüssel. 
>   * Dieses Feature wird auf der [Azure Service Bus Premium](service-bus-premium-messaging.md)-Ebene unterstützt. Es kann nicht für Service Bus-Namespaces auf Standardebene aktiviert werden.
>   * Die Verschlüsselung kann nur für neue oder leere Namespaces aktiviert werden. Wenn der Namespace Warteschlangen oder Themen enthält, tritt beim Verschlüsselungsvorgang ein Fehler auf.

Verwenden Sie Azure Key Vault, um Ihre Schlüssel zu verwalten und die Schlüsselverwendung zu überwachen. Sie können entweder Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder mit den Azure Key Vault-APIs Schlüssel generieren. Weitere Informationen zum Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](../key-vault/general/overview.md)

In diesem Artikel erfahren Sie, wie Sie einen Schlüsseltresor mit kundenseitig verwalteten Schlüsseln mithilfe des Azure-Portals konfigurieren. Informationen zum Erstellen eines Schlüsseltresors über das Azure-Portal finden Sie unter [Schnellstart: Erstellen Sie eine Azure Key Vault-Instanz mithilfe des Azure-Portals](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Für die Verwendung kundenseitig verwalteter Schlüssel mit Azure Service Bus müssen für den Schlüsseltresor zwei Eigenschaften konfiguriert werden. Sie lauten wie folgt:  **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen). Diese Eigenschaften sind standardmäßig aktiviert, wenn Sie im Azure-Portal einen neuen Schlüsseltresor erstellen. Wenn Sie diese Eigenschaften jedoch für einen vorhandenen Schlüsseltresor aktivieren möchten, müssen Sie PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden.

## <a name="enable-customer-managed-keys"></a>Aktivieren von vom Kunden verwalteten Schlüsseln
Um vom Kunden verwaltete Schlüssel im Azure-Portal zu aktivieren, gehen Sie folgendermaßen vor:

1. Navigieren Sie zu Ihrem Service Bus Premium-Namespace.
2. Wählen Sie auf der Seite **Einstellungen** des Service Bus-Namespace die Option **Verschlüsselung** aus.
3. Wählen Sie die **Verschlüsselung im Ruhezustand mit kundenseitig verwalteten Schlüsseln**  wie in der folgenden Abbildung gezeigt aus.

    ![Kundenseitig verwaltete Schlüssel aktivieren](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Einrichten eines Schlüsseltresors mit Schlüsseln

Nachdem Sie kundenseitig verwaltete Schlüssel aktiviert haben, müssen Sie den kundenseitig verwalteten Schlüssel Ihrem Azure Service Bus-Namespace zuordnen. Service Bus unterstützt nur Azure Key Vault. Wenn Sie die Option **Verschlüsselung mit kundenseitig verwaltetem Schlüssel** im vorherigen Abschnitt aktivieren, muss der Schlüssel in Azure Key Vault importiert werden. Außerdem müssen die Eigenschaften **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen) für den Schlüssel konfiguriert sein. Diese Einstellungen können mithilfe von [PowerShell](../key-vault/general/key-vault-recovery.md) oder der [CLI](../key-vault/general/key-vault-recovery.md) konfiguriert werden.

1. Befolgen Sie zum Erstellen eines neuen Schlüsseltresors die [Schnellstartanleitung](../key-vault/general/overview.md) für Azure Key Vault. Weitere Informationen zum Importieren vorhandener Schlüssel finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](../key-vault/general/about-keys-secrets-certificates.md).
1. Verwenden Sie den Befehl [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create), um beim Erstellen eines Tresors sowohl das vorläufige Löschen als auch den Bereinigungsschutz zu aktivieren.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Verwenden Sie den Befehl [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update), um einem vorhandenen Tresor (für den bereits das vorläufige Löschen aktiviert ist) den Bereinigungsschutz hinzuzufügen.

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Führen Sie diese Schritte durch, um Schlüssel zu erstellen:
    1. Wählen Sie zum Erstellen eines neuen Schlüssels **Generieren/Importieren** im Menü **Schlüssel** unter **Einstellungen** aus.
        
        ![Auswählen der Schaltfläche „Generate/Import“ (Generieren/Importieren)](./media/configure-customer-managed-key/select-generate-import.png)

    1. Legen Sie **Optionen** auf **Generieren** fest, und benennen Sie den Schlüssel.

        ![Erstellen eines Schlüssels](./media/configure-customer-managed-key/create-key.png) 

    1. Sie können diesen Schlüssel jetzt in der Dropdownliste auswählen, um ihn dem Service Bus-Namespace zum Verschlüsseln zuzuordnen. 

        ![Auswählen des Schlüssels aus dem Schlüsseltresor](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Um Redundanz zu gewährleisten, können Sie bis zu drei Schlüssel hinzufügen. Wenn einer der Schlüssel abgelaufen ist oder nicht darauf zugegriffen werden kann, werden die anderen Schlüssel für die Verschlüsselung verwendet.
        
    1. Geben Sie die Details für den Schlüssel ein, und klicken Sie auf **Auswählen**. Dadurch wird die Verschlüsselung von ruhenden Daten im Namespace mit einem kundenseitig verwalteten Schlüssel ermöglicht. 


    > [!IMPORTANT]
    > Wenn Sie vom Kunden verwaltete Schlüssel zusammen mit der georedundanten Notfallwiederherstellung verwenden möchten, überprüfen Sie Folgendes: 
    >
    > Um die Verschlüsselung im Ruhezustand mit vom Kunden verwalteten Schlüsseln zu aktivieren, wird eine [Zugriffsrichtlinie](../key-vault/general/secure-your-key-vault.md) für die von Service Bus verwaltete Identität in der angegebenen Azure Key Vault-Instanz eingerichtet. Dies gewährleistet den kontrollierten Zugriff auf Azure Key Vault aus dem Azure Service Bus-Namespace.
    >
    > Gehen Sie daher folgendermaßen vor:
    > 
    >   * Wenn die [georedundante Notfallwiederherstellung](service-bus-geo-dr.md) bereits für den Service Bus-Namespace aktiviert ist und Sie den vom Kunden verwalteten Schlüssel aktivieren möchten: 
    >     * Heben Sie die Kopplung auf.
    >     * [Richten Sie die Zugriffsrichtlinien](../key-vault/general/assign-access-policy-portal.md) für die verwaltete Identität sowohl für den primären als auch für den sekundären Namespace auf den Schlüsseltresor ein.
    >     * Richten Sie die Verschlüsselung für den primären Namespace ein.
    >     * Stellen Sie die Kopplung des primären und des sekundären Namespace wieder her.
    > 
    >   * Wenn Sie in einem Service Bus-Namespace, in dem bereits vom Kunden verwaltete Schlüssel eingerichtet sind, georedundante Notfallwiederherstellung aktivieren möchten:
    >     * [Richten Sie die Zugriffsrichtlinien](../key-vault/general/assign-access-policy-portal.md) für die verwaltete Identität für den sekundären Namespace auf den Schlüsseltresor ein.
    >     * Koppeln Sie den primären und den sekundären Namespace.


## <a name="rotate-your-encryption-keys"></a>Rotieren der Verschlüsselungsschlüssel

Sie können Ihren Schlüssel im Schlüsseltresor mit dem Rotationsmechanismus von Azure Key Vault rotieren. Aktivierungs- und Ablaufdaten können auch festgelegt werden, um die Schlüsselrotation zu automatisieren. Der Service Bus-Dienst erkennt neue Schlüsselversionen und beginnt automatisch mit ihrer Verwendung.

## <a name="revoke-access-to-keys"></a>Widerrufen des Zugriffs auf Schlüssel

Wenn Sie den Zugriff auf die Verschlüsselungsschlüssel widerrufen, werden die Daten dadurch nicht aus Service Bus gelöscht. Es kann jedoch nicht über den Event Hubs-Namespace auf die Daten zugegriffen werden. Sie können den Verschlüsselungsschlüssel mithilfe von Zugriffsrichtlinien oder durch das Löschen des Schlüssels widerrufen. Unter [Sicherer Zugriff auf einen Schlüsseltresor](../key-vault/general/secure-your-key-vault.md) erfahren Sie mehr über Zugriffsrichtlinien und das Sichern Ihres Schlüsseltresors.

Nachdem der Verschlüsselungsschlüssel widerrufen wurde, funktioniert der Service Bus-Dienst im verschlüsselten Namespace nicht mehr. Wenn der Zugriff auf den Schlüssel aktiviert oder der gelöschte Schlüssel wiederhergestellt wird, wählt der Service Bus-Dienst den Schlüssel aus, sodass Sie aus dem verschlüsselten Service Bus-Namespace auf die Daten zugreifen können.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Verwenden von Resource Manager-Vorlagen zum Aktivieren von Verschlüsselung
In diesem Abschnitt wird gezeigt, wie die folgenden Aufgaben mithilfe von **Azure Resource Manager-Vorlagen** ausgeführt werden. 

1. Erstellen Sie einen Service Bus-Namespace vom Typ **Premium** mit einer **verwalteten Dienstidentität**.
2. Erstellen Sie einen **Schlüsseltresor**, und gewähren Sie der Dienstidentität Zugriff auf den Schlüsseltresor. 
3. Aktualisieren Sie den Service Bus-Namespace mit den Schlüsseltresorinformationen (Schlüssel/Wert). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Erstellen eines Service Bus-Namespace vom Typ „Premium“ mit einer verwalteten Dienstidentität
In diesem Abschnitt erfahren Sie, wie Sie einen Azure Service Bus-Namespace mit einer verwalteten Dienstidentität erstellen, indem Sie eine Azure Resource Manager-Vorlage und PowerShell verwenden. 

1. Erstellen Sie eine Azure Resource Manager-Vorlage, um einen Service Bus-Namespace des Premium-Tarifs mit einer verwalteten Dienstidentität zu erstellen. Benennen Sie die Datei: **CreateServiceBusPremiumNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Erstellen Sie eine Vorlagenparameterdatei mit dem folgenden Namen: **CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > Ersetzen Sie die folgenden Werte: 
    > - `<ServiceBusNamespaceName>`: Der Name Ihres Service Bus-Namespace.
    > - `<Location>`: Der Speicherort Ihres Service Bus-Namespace.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Führen Sie den folgenden PowerShell-Befehl aus, um die Vorlage bereitzustellen und einen Service Bus-Namespace des Typs „Premium“ zu erstellen. Rufen Sie dann die ID des Service Bus-Namespace ab, um sie später zu verwenden. Ersetzen Sie `{MyRG}` durch den Namen der Ressourcengruppe, bevor Sie den Befehl ausführen.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Gewähren des Zugriffs auf den Schlüsseltresor für die Service Bus-Namespaceidentität

1. Führen Sie den folgenden Befehl aus, um einen Schlüsseltresor zu erstellen, für den **Bereinigungsschutz** und **vorläufiges Löschen** aktiviert ist. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (ODER)
    
    Führen Sie den folgenden Befehl aus, um einen **vorhandenen Schlüsseltresor** zu aktualisieren. Geben Sie vor dem Ausführen des Befehls Werte für Ressourcengruppen und Schlüsseltresornamen an. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Legen Sie die Schlüsseltresor-Zugriffsrichtlinie so fest, dass die verwaltete Identität des Service Bus-Namespace auf den Schlüsselwert im Schlüsseltresor zugreifen kann. Verwenden Sie die ID des Service Bus-Namespace aus dem vorherigen Abschnitt. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Verschlüsseln von Daten im Service Bus-Namespace mit einem kundenseitig verwalteten Schlüssel aus dem Schlüsseltresor
Bisher haben Sie die folgenden Schritte ausgeführt: 

1. Sie haben einen Premium-Namespace mit einer verwalteten Identität erstellt.
2. Sie haben einen Schlüsseltresor erstellt und der verwalteten Identität Zugriff auf den Schlüsseltresor gewährt. 

In diesem Schritt aktualisieren Sie den Service Bus-Namespace mit Schlüsseltresorinformationen. 

1. Erstellen Sie eine JSON-Datei mit dem Namen **UpdateServiceBusNamespaceWithEncryption.json** mit folgendem Inhalt: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Erstellen Sie eine Vorlagenparameterdatei: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Ersetzen Sie die folgenden Werte: 
    > - `<ServiceBusNamespaceName>`: Der Name Ihres Service Bus-Namespace.
    > - `<Location>`: Der Speicherort Ihres Service Bus-Namespace.
    > - `<KeyVaultName>`: Der Name Ihres Schlüsseltresors.
    > - `<KeyName>`: Der Name des Schlüssels im Schlüsseltresor.  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Führen Sie den folgenden PowerShell-Befehl aus, um die Resource Manager-Vorlage bereitzustellen. Ersetzen Sie `{MyRG}` durch den Namen Ihrer Ressourcengruppe, bevor Sie den Befehl ausführen. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:
- [Übersicht über Service Bus](service-bus-messaging-overview.md)
- [Übersicht über Key Vault](../key-vault/general/overview.md)