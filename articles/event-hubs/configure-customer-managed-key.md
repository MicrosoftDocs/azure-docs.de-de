---
title: Konfigurieren Ihres eigenen Schlüssels zum Verschlüsseln ruhender Azure Event Hubs-Daten
description: Dieser Artikel enthält Informationen dazu, wie Sie einen eigenen Schlüssel für die Verschlüsselung ruhender Azure Event Hubs-Daten konfigurieren.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 53622344e36e514543d547dec95caaf1b0b76a13
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430678"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln für die Verschlüsselung ruhender Azure Event Hubs-Daten mithilfe des Azure-Portals
Azure Event Hubs ermöglicht die Verschlüsselung ruhender Daten mit Azure Storage Service Encryption (Azure SSE). Der Event Hubs-Dienst verwendet zum Speichern der Daten Azure Storage. Alle in Azure Storage gespeicherten Daten werden mit von Microsoft verwalteten Schlüsseln verschlüsselt. Wenn Sie einen eigenen Schlüssel verwenden – Bring Your Own Key (BYOK) oder kundenseitig verwalteter Schlüssel –, werden die Daten trotzdem mit dem von Microsoft verwalteten Schlüssel verschlüsselt. Zusätzlich wird der von Microsoft verwaltete Schlüssel jedoch mit dem kundenseitig verwalteten Schlüssel verschlüsselt. Mit dieser Funktion können Sie kundenseitig verwaltete Schlüssel, die zum Verschlüsseln der von Microsoft verwalteten Schlüssel verwendet werden, erstellen, rotieren, deaktivieren und den Zugriff darauf widerrufen. Die Aktivierung der BYOK-Funktion ist ein einmaliger Setupvorgang für Ihren Namespace.

> [!NOTE]
> - Die BYOK-Funktion wird von [Event Hubs Dedicated](event-hubs-dedicated-overview.md)-Clustern mit einem Mandanten unterstützt. Sie kann nicht für Event Hubs-Standardnamespaces aktiviert werden.
> - Die Verschlüsselung kann nur für neue oder leere Namespaces aktiviert werden. Wenn der Namespace Event Hubs enthält, tritt beim Verschlüsselungsvorgang ein Fehler auf.

Verwenden Sie Azure Key Vault, um Ihre Schlüssel zu verwalten und die Schlüsselverwendung zu überwachen. Sie können entweder Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder mit den Azure Key Vault-APIs Schlüssel generieren. Weitere Informationen zum Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](../key-vault/general/overview.md)

In diesem Artikel erfahren Sie, wie Sie einen Schlüsseltresor mit kundenseitig verwalteten Schlüsseln mithilfe des Azure-Portals konfigurieren. Informationen zum Erstellen eines Schlüsseltresors über das Azure-Portal finden Sie unter [Schnellstart: Erstellen Sie eine Azure Key Vault-Instanz mithilfe des Azure-Portals](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Für die Verwendung kundenseitig verwalteter Schlüssel mit Azure Event Hubs müssen für den Schlüsseltresor zwei erforderliche Eigenschaften konfiguriert werden. Sie lauten wie folgt:  **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen). Diese Eigenschaften sind standardmäßig aktiviert, wenn Sie im Azure-Portal einen neuen Schlüsseltresor erstellen. Wenn Sie diese Eigenschaften jedoch für einen vorhandenen Schlüsseltresor aktivieren möchten, müssen Sie PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden.

## <a name="enable-customer-managed-keys"></a>Aktivieren von vom Kunden verwalteten Schlüsseln
Um vom Kunden verwaltete Schlüssel im Azure-Portal zu aktivieren, gehen Sie folgendermaßen vor:

1. Navigieren Sie zu Ihrem Event Hubs Dedicated-Cluster.
1. Wählen Sie den Namespace aus, für den Sie BYOK aktivieren möchten.
1. Wählen Sie auf der Seite **Einstellungen** des Event Hubs-Namespace die Option **Verschlüsselung** aus. 
1. Wählen Sie die **Verschlüsselung im Ruhezustand mit kundenseitig verwalteten Schlüsseln**  wie in der folgenden Abbildung gezeigt aus. 

    ![Kundenseitig verwaltete Schlüssel aktivieren](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Einrichten eines Schlüsseltresors mit Schlüsseln
Nachdem Sie kundenseitig verwaltete Schlüssel aktiviert haben, müssen Sie den kundenseitig verwalteten Schlüssel Ihrem Azure Event Hubs-Namespace zuordnen. Event Hubs unterstützt nur Azure Key Vault. Wenn Sie die Option **Verschlüsselung mit kundenseitig verwaltetem Schlüssel** im vorherigen Abschnitt aktivieren, muss der Schlüssel in Azure Key Vault importiert werden. Außerdem müssen die Eigenschaften **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen) für den Schlüssel konfiguriert sein. Diese Einstellungen können mithilfe von [PowerShell](../key-vault/general/key-vault-recovery.md) oder der [CLI](../key-vault/general/key-vault-recovery.md) konfiguriert werden.

1. Befolgen Sie zum Erstellen eines neuen Schlüsseltresors die [Schnellstartanleitung](../key-vault/general/overview.md) für Azure Key Vault. Weitere Informationen zum Importieren vorhandener Schlüssel finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](../key-vault/general/about-keys-secrets-certificates.md).
1. Verwenden Sie den Befehl [az keyvault create](/cli/azure/keyvault#az-keyvault-create), um beim Erstellen eines Tresors sowohl das vorläufige Löschen als auch den Bereinigungsschutz zu aktivieren.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Verwenden Sie den Befehl [az keyvault update](/cli/azure/keyvault#az-keyvault-update), um einem vorhandenen Tresor (für den bereits das vorläufige Löschen aktiviert ist) den Bereinigungsschutz hinzuzufügen.

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Führen Sie diese Schritte durch, um Schlüssel zu erstellen:
    1. Wählen Sie zum Erstellen eines neuen Schlüssels **Generieren/Importieren** im Menü **Schlüssel** unter **Einstellungen** aus.
        
        ![Auswählen der Schaltfläche „Generate/Import“ (Generieren/Importieren)](./media/configure-customer-managed-key/select-generate-import.png)
    1. Legen Sie **Optionen** auf **Generieren** fest, und benennen Sie den Schlüssel.

        ![Erstellen eines Schlüssels](./media/configure-customer-managed-key/create-key.png) 
    1. Sie können diesen Schlüssel jetzt aus der Dropdownliste auswählen, um ihn dem Event Hubs-Namespace zum Verschlüsseln zuzuordnen. 

        ![Auswählen des Schlüssels aus dem Schlüsseltresor](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Geben Sie die Details für den Schlüssel ein, und klicken Sie auf **Auswählen**. Dadurch wird die Verschlüsselung des von Microsoft verwalteten Schlüssels mit Ihrem Schlüssel (kundenseitig verwalteter Schlüssel) ermöglicht. 


## <a name="rotate-your-encryption-keys"></a>Rotieren der Verschlüsselungsschlüssel
Sie können Ihren Schlüssel im Schlüsseltresor mit dem Rotationsmechanismus von Azure Key Vault rotieren. Aktivierungs- und Ablaufdaten können auch festgelegt werden, um die Schlüsselrotation zu automatisieren. Der Event Hubs-Dienst erkennt neue Schlüsselversionen und beginnt automatisch mit ihrer Verwendung.

## <a name="revoke-access-to-keys"></a>Widerrufen des Zugriffs auf Schlüssel
Wenn Sie den Zugriff auf die Verschlüsselungsschlüssel widerrufen, werden die Daten dadurch nicht aus Event Hubs gelöscht. Der Zugriff auf die Daten ist jedoch nicht über den Event Hubs-Namespace möglich. Sie können den Verschlüsselungsschlüssel mithilfe von Zugriffsrichtlinien oder durch das Löschen des Schlüssels widerrufen. Unter [Sicherer Zugriff auf einen Schlüsseltresor](../key-vault/general/secure-your-key-vault.md) erfahren Sie mehr über Zugriffsrichtlinien und das Sichern Ihres Schlüsseltresors.

Nachdem der Verschlüsselungsschlüssel widerrufen wurde, funktioniert der Event Hubs-Dienst im verschlüsselten Namespace nicht mehr. Wenn der Zugriff auf den Schlüssel aktiviert ist oder der gelöschte Schlüssel wiederhergestellt wurde, wählt der Event Hubs-Dienst den Schlüssel aus, sodass Sie aus dem verschlüsselten Event Hubs-Namespace auf die Daten zugreifen können.

## <a name="set-up-diagnostic-logs"></a>Einrichten von Diagnoseprotokollen 
Durch das Einrichten von Diagnoseprotokollen für durch BYOK aktivierte Namespaces erhalten Sie die erforderlichen Informationen zu den Vorgängen. Diese Protokolle können aktiviert und später in einen Event Hub gestreamt, mithilfe von Protokollanalysen analysiert oder in den Speicher gestreamt werden, um benutzerdefinierte Analysen auszuführen. Weitere Informationen zu Diagnoseprotokollen finden Sie unter [Übersicht über Azure-Diagnoseprotokolle](../azure-monitor/platform/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Aktivieren von Benutzerprotokollen
Führen Sie die folgenden Schritte aus, um Protokolle für kundenseitig verwaltete Schlüssel zu aktivieren.

1. Navigieren Sie im Azure-Portal zum Namespace, für den BYOK aktiviert ist.
1. Wählen Sie unter **Überwachung** die Option **Diagnoseeinstellungen** aus.

    ![Auswählen der Diagnoseeinstellungen](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Klicken Sie auf **Diagnoseeinstellung hinzufügen**. 

    ![Auswählen von „Diagnoseeinstellung hinzufügen“](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Geben Sie einen **Namen** an, und wählen Sie aus, wohin die Protokolle gestreamt werden sollen.
1. Wählen Sie **CustomerManagedKeyUserLogs** und anschließend die Option **Speichern** aus. Diese Aktion aktiviert die Protokolle für BYOK im Namespace.

    ![Auswählen der Option für kundenseitig verwaltete Schlüsselbenutzerprotokolle](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Protokollschema 
Alle Protokolle werden im JavaScript Object Notation (JSON)-Format gespeichert. Jeder Eintrag enthält Zeichenfolgenfelder im Format, das in der nachfolgenden Tabelle beschrieben ist. 

| Name | BESCHREIBUNG |
| ---- | ----------- | 
| TaskName | Beschreibung der Aufgabe, bei der ein Fehler aufgetreten ist |
| ActivityId | Interne ID zur Nachverfolgung. |
| category | Definiert die Klassifizierung der Aufgabe. Wenn beispielsweise der Schlüssel aus Ihrem Schlüsseltresor deaktiviert wird, dann handelt es sich hierbei um eine Informationskategorie. Wenn ein Schlüssel nicht entpackt werden kann, dann handelt es sich um einen Fehler. |
| resourceId | Azure Resource Manager-Ressourcen-ID |
| keyVault | Der vollständige Name des Schlüsseltresors |
| Schlüssel | Der Schlüsselname, der zum Verschlüsseln des Event Hubs-Namespace verwendet wird |
| version | Die Version des verwendeten Schlüssels |
| operation | Der Vorgang, der für den Schlüssel in Ihrem Schlüsseltresor ausgeführt wird. Dazu zählen etwa das Deaktivieren/Aktvieren, das Packen und das Entpacken des Schlüssels. |
| code | Der Code, der dem Vorgang zugeordnet ist. Beispiel: Der Fehlercode 404 bedeutet, dass der Schlüssel nicht gefunden wurde. |
| message | Eine Fehlermeldung, die dem Vorgang zugeordnet ist |

Im Folgenden finden Sie ein Beispiel für das Protokoll für einen kundenseitig verwalteten Schlüssel:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Verwenden von Resource Manager-Vorlagen zum Aktivieren der Verschlüsselung
In diesem Abschnitt wird gezeigt, wie die folgenden Aufgaben mithilfe von **Azure Resource Manager-Vorlagen** ausgeführt werden: 

1. Erstellen eines **Event Hubs-Namespace** mit einer verwalteten Dienstidentität
2. Erstellen eines **Schlüsseltresors** und Gewähren von Zugriff für die Dienstidentität 
3. Aktualisieren des Event Hubs-Namespace mit den Schlüsseltresorinformationen (Schlüssel/Wert) 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Erstellen eines Event Hubs-Clusters und -Namespace mit verwalteter Dienstidentität
In diesem Abschnitt erfahren Sie, wie Sie mit einer Azure Resource Manager-Vorlage und PowerShell einen Azure Event Hubs-Namespace mit verwalteter Dienstidentität erstellen. 

1. Erstellen Sie eine Azure Resource Manager-Vorlage für die Erstellung eines Event Hubs-Namespace mit einer verwalteten Dienstidentität. Nennen Sie die Datei **CreateEventHubClusterAndNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Erstellen Sie eine Vorlagenparameterdatei mit dem folgenden Namen: **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Ersetzen Sie die folgenden Werte: 
    > - `<EventHubsClusterName>`: Der Name Ihres Event Hubs-Clusters.    
    > - `<EventHubsNamespaceName>`: Der Name Ihres Event Hubs-Namespace.
    > - `<Location>`: Der Standort Ihres Event Hubs-Namespace.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Führen Sie den folgenden PowerShell-Befehl aus, um die Vorlage bereitzustellen und einen Event Hubs-Namespace zu erstellen. Rufen Sie dann die ID des Event Hubs-Namespace ab. Sie wird später noch benötigt. Ersetzen Sie `{MyRG}` durch den Namen der Ressourcengruppe, bevor Sie den Befehl ausführen.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Gewähren von Zugriff auf den Schlüsseltresor für die Event Hubs-Namespaceidentität

1. Führen Sie den folgenden Befehl aus, um einen Schlüsseltresor zu erstellen, für den **Bereinigungsschutz** und **vorläufiges Löschen** aktiviert sind: 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (ODER)    
    
    Führen Sie den folgenden Befehl aus, um einen **vorhandenen Schlüsseltresor** zu aktualisieren. Geben Sie vor dem Ausführen des Befehls den Namen der Ressourcengruppe und des Schlüsseltresors an. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Legen Sie die Schlüsseltresor-Zugriffsrichtlinie so fest, dass die verwaltete Identität des Event Hubs-Namespace auf den Schlüsselwert im Schlüsseltresor zugreifen kann. Verwenden Sie die ID des Event Hubs-Namespace aus dem vorherigen Abschnitt. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Verschlüsseln von Daten im Event Hubs-Namespace mit einem kundenseitig verwalteten Schlüssel aus dem Schlüsseltresor
Bisher haben Sie die folgenden Schritte ausgeführt: 

1. Sie haben einen Premium-Namespace mit einer verwalteten Identität erstellt.
2. Sie haben einen Schlüsseltresor erstellt und der verwalteten Identität Zugriff auf den Schlüsseltresor gewährt. 

In diesem Schritt aktualisieren Sie den Event Hubs-Namespace mit Schlüsseltresorinformationen. 

1. Erstellen Sie eine JSON-Datei mit dem Namen **CreateEventHubClusterAndNamespace.json** und folgendem Inhalt: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. Erstellen Sie eine Vorlagenparameterdatei: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Ersetzen Sie die folgenden Werte: 
    > - `<EventHubsClusterName>`: Der Name Ihres Event Hubs-Clusters.        
    > - `<EventHubsNamespaceName>`: Der Name Ihres Event Hubs-Namespace.
    > - `<Location>`: Der Standort Ihres Event Hubs-Namespace.
    > - `<KeyVaultName>`: Der Name Ihres Schlüsseltresors.
    > - `<KeyName>`: Der Name des Schlüssels im Schlüsseltresor.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Problembehandlung
Es wird empfohlen, Protokolle immer wie im vorherigen Abschnitt gezeigt zu aktivieren. Dies hilft dabei, die Aktivitäten nachzuverfolgen, wenn die BYOK-Verschlüsselung aktiviert ist. Außerdem können dadurch Probleme eingegrenzt werden.

Im Folgenden finden Sie die allgemeinen Fehlercodes, nach denen Sie suchen müssen, wenn die BYOK-Verschlüsselung aktiviert ist.

| Aktion | Fehlercode | Resultierender Zustand der Daten |
| ------ | ---------- | ----------------------- | 
| Widerrufen der Berechtigung zum Packen/Entpacken aus einem Schlüsseltresor | 403 |    Inaccessible |
| Entfernen der AAD-Rollenmitgliedschaft aus einem AAD-Prinzipal, der die Berechtigung zum Packen/Entpacken gewährt hat | 403 |  Inaccessible |
| Löschen eines Verschlüsselungsschlüssels aus dem Schlüsseltresor | 404 | Inaccessible |
| Löschen des Schlüsseltresors | 404 | Zugriff nicht möglich (unter der Annahme, dass vorläufiges Löschen aktiviert ist, da dies eine erforderliche Einstellung ist) |
| Ändern des Ablaufzeitraums für den Verschlüsselungsschlüssel, sodass er bereits abgelaufen ist | 403 |   Inaccessible  |
| Ändern des Werts für NBF (not before, nicht vor), sodass der Schlüsselverschlüsselungsschlüssel nicht aktiv ist | 403 | Inaccessible  |
| Auswählen der Option **Allow MSFT Services** (MSFT-Dienste zulassen) für die Firewall des Schlüsseltresors oder anderweitiges Blockieren des Netzwerkzugriffs auf den Schlüsseltresor, der den Verschlüsselungsschlüssel enthält | 403 | Inaccessible |
| Verschieben des Schlüsseltresors in einen anderen Mandanten | 404 | Inaccessible |  
| Zeitweilig auftretendes Netzwerkproblem oder DNS-/AAD-/MSI-Ausfall |  | Zugriff mithilfe des zwischengespeicherten Datenverschlüsselungsschlüssels |

> [!IMPORTANT]
> Zum Aktivieren der georedundanten Notfallwiederherstellung in einem Namespace, der die BYOK-Verschlüsselung verwendet, muss sich der sekundäre Namespace für die Kopplung in einem dedizierten Cluster befinden, und eine vom System zugewiesene verwaltete Identität muss für ihn aktiviert sein. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:
- [Übersicht über Event Hubs](event-hubs-about.md)
- [Übersicht über Key Vault](../key-vault/general/overview.md)