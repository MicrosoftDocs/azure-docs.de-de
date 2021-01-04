---
title: Konfigurieren verwalteter Identitäten in Azure-VM mithilfe von REST – Azure AD
description: Schrittweise Anweisungen zum Konfigurieren von vom System und vom Benutzer zugewiesenen verwalteten Identitäten auf einem virtuellen Azure-Computer mithilfe von CURL zum Durchführen von REST-API-Aufrufen
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: f70229fabe4331adb7740cf09cf2bf8f3e3e4617
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587518"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von REST-API-Aufrufen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen Azure-Dienste mit einer automatisch verwalteten Systemidentität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erfahren Sie, wie Sie unter Verwendung von CURL für Aufrufe an den Azure Resource Manager-REST-Endpunkt die folgenden Vorgänge für verwaltete Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer ausführen können:

- Aktivieren und Deaktivieren der vom System zugewiesenen verwalteten Identität auf einem virtuellen Azure-Computer
- Hinzufügen und Entfernen einer vom Benutzer zugewiesenen verwalteten Identität auf einem virtuellen Azure-Computer

Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie mit verwalteten Identitäten für Azure-Ressourcen noch nicht vertraut sind, lesen Sie [Was sind verwaltete Identitäten für Azure-Ressourcen?](overview.md). Informationen zu systemseitig zugewiesenen und benutzerseitig zugewiesenen Typen verwalteter Identitäten finden Sie unter [Arten von verwalteten Identitäten](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie unter Verwendung von CURL für Aufrufe an den Azure Resource Manager-REST-Endpunkt eine systemzugewiesene verwaltete Identität auf einem virtuellen Azure-Computer aktivieren und deaktivieren können.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität beim Erstellen eines virtuellen Azure-Computers

Zum Erstellen eines virtuellen Azure-Computers, auf dem die systemseitig zugewiesene verwaltete Identität aktiviert ist, benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Erstellen Sie eine [Ressourcengruppe](../../azure-resource-manager/management/overview.md#terminology) für das Einschließen und Bereitstellen des virtuellen Computers und der zugehörigen Ressourcen. Verwenden Sie hierfür [az group create](/cli/azure/group/#az-group-create). Sie können diesen Schritt überspringen, wenn Sie bereits über eine Ressourcengruppe verfügen, die Sie stattdessen verwenden möchten:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Erstellen einer [Netzwerkschnittstelle](/cli/azure/network/nic#az-network-nic-create) für den virtuellen Computer:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um den virtuellen Computer mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Erstellen Sie über Azure Cloud Shell einen virtuellen Computer mithilfe von CURL zum Aufrufen des Azure Resource Manager-REST-Endpunkts. Im folgenden Beispiel wird ein virtueller Computer namens *myVM* mit einer systemzugewiesenen verwalteten Identität erstellt, wie es im Anforderungstext durch den Wert `"identity":{"type":"SystemAssigned"}` angegeben ist. Ersetzen Sie `<ACCESS TOKEN>` durch den Wert, den Sie im vorherigen Schritt zum Anfordern eines Bearer-Zugriffstokens erhalten haben, und ersetzen Sie den Wert `<SUBSCRIPTION ID>` entsprechend Ihrer Umgebung.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Anforderungsheader**
   
   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 
   
   **Anforderungstext**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Aktivieren einer vom System zugewiesenen Identität auf einem vorhandenen virtuellen Azure-Computer

Zum Aktivieren der systemseitig zugewiesenen verwalteten Identität auf einem virtuellen Computer, der ursprünglich ohne diese bereitgestellt wurde, benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um den virtuellen Computer mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Verwenden Sie den folgenden CURL-Befehl zum Aufrufen des Azure Resource Manager-REST-Endpunkts, um die systemzugewiesene verwaltete Identität auf dem virtuellen Computer zu aktivieren, wie es im Anforderungstext durch den Wert `{"identity":{"type":"SystemAssigned"}` für einen virtuellen Computer namens *myVM* angegeben ist.  Ersetzen Sie `<ACCESS TOKEN>` durch den Wert, den Sie im vorherigen Schritt zum Anfordern eines Bearer-Zugriffstokens erhalten haben, und ersetzen Sie den Wert `<SUBSCRIPTION ID>` entsprechend Ihrer Umgebung.
   
   > [!IMPORTANT]
   > Um sicherzustellen, dass Sie keine vorhandenen benutzerzugewiesenen verwalteten Identitäten löschen, die dem virtuellen Computer zugewiesen sind, müssen Sie die benutzerzugewiesenen verwalteten Identitäten mit dem folgenden CURL-Befehl auflisten: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` Wenn dem virtuellen Computer benutzerzugewiesene verwaltete Identitäten zugewiesen sind, wie es im `identity`-Wert in der Antwort angegeben ist, fahren Sie mit Schritt 3 fort. Dort wird gezeigt, wie Sie benutzerzugewiesene verwaltete Identitäten beibehalten und gleichzeitig die systemzugewiesene verwaltete Identität auf dem virtuellen Computer aktivieren.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 
   
   **Anforderungstext**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Zum Aktivieren der systemzugewiesenen verwalteten Identität auf einem virtuellen Computer mit vorhandenen benutzerzugewiesenen verwalteten Identitäten müssen Sie `SystemAssigned` zum `type`-Wert hinzufügen.  
   
   Wenn dem virtuellen Computer beispielsweise die benutzerzugewiesenen verwalteten Identitäten `ID1` und `ID2` zugewiesen sind, und Sie dem virtuellen Computer die systemzugewiesene verwaltete Identität hinzufügen möchten, verwenden Sie den folgenden CURL-Aufruf. Ersetzen Sie `<ACCESS TOKEN>` und `<SUBSCRIPTION ID>` durch Werte entsprechend Ihrer Umgebung.

   Bei der API-Version `2018-06-01` werden die vom Benutzer zugewiesenen verwalteten Identitäten im Wert `userAssignedIdentities` in einem Wörterbuchformat gespeichert (im Gegensatz zum Wert `identityIds` in einem Arrayformat, der in API-Version `2017-12-01` verwendet wird).
   
   **API-VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 

   **Anforderungstext**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **API-VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 

   **Anforderungstext**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Deaktivieren einer vom System zugewiesenen verwalteten Identität auf einem virtuellen Azure-Computer

Zum Deaktivieren der systemseitig zugewiesenen verwalteten Identität auf einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um den virtuellen Computer mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aktualisieren Sie den virtuellen Computer mithilfe von CURL zum Aufrufen des Azure Resource Manager-REST-Endpunkts, um die systemzugewiesene verwaltete Identität zu deaktivieren.  Im folgenden Beispiel wird die systemzugewiesene verwaltete Identität auf einem virtuellen Computer namens *myVM* deaktiviert, wie es im Anforderungstext durch den Wert `{"identity":{"type":"None"}}` angegeben ist.  Ersetzen Sie `<ACCESS TOKEN>` durch den Wert, den Sie im vorherigen Schritt zum Anfordern eines Bearer-Zugriffstokens erhalten haben, und ersetzen Sie den Wert `<SUBSCRIPTION ID>` entsprechend Ihrer Umgebung.

   > [!IMPORTANT]
   > Um sicherzustellen, dass Sie keine vorhandenen benutzerzugewiesenen verwalteten Identitäten löschen, die dem virtuellen Computer zugewiesen sind, müssen Sie die benutzerzugewiesenen verwalteten Identitäten mit dem folgenden CURL-Befehl auflisten: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` Wenn dem virtuellen Computer benutzerzugewiesene verwaltete Identitäten zugewiesen sind, wie es im `identity`-Wert in der Antwort angegeben ist, fahren Sie mit Schritt 3 fort. Dort wird gezeigt, wie Sie benutzerzugewiesene verwaltete Identitäten beibehalten und gleichzeitig die systemzugewiesene verwaltete Identität auf dem virtuellen Computer deaktivieren.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 

   **Anforderungstext**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Zum Entfernen der systemzugewiesenen verwalteten Identität von einem virtuellen Computer mit benutzerzugewiesenen verwalteten Identitäten entfernen Sie `SystemAssigned` aus dem Wert `{"identity":{"type:" "}}`, während Sie den Wert `UserAssigned` und die `userAssignedIdentities`-Wörterbuchwerte beibehalten, wenn Sie **API-Version 2018-06-01** verwenden. Bei Verwendung der **API-Version 2017-12-01** (oder einer älteren Version) behalten Sie das `identityIds`-Array bei.

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie unter Verwendung von CURL für Aufrufe an den Azure Resource Manager-REST-Endpunkt eine benutzerzugewiesene verwaltete Identität auf einem virtuellen Computer hinzufügen und entfernen können.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität beim Erstellen eines virtuellen Azure-Computers

Für die Zuweisung einer benutzerseitig zugewiesenen Identität zu einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisungen [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) und [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um den virtuellen Computer mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Erstellen einer [Netzwerkschnittstelle](/cli/azure/network/nic#az-network-nic-create) für den virtuellen Computer:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um den virtuellen Computer mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Erstellen Sie eine benutzerseitig zugewiesene Identität anhand der Anweisungen unter [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Erstellen Sie einen virtuellen Computer mithilfe von CURL zum Aufrufen des Azure Resource Manager-REST-Endpunkts. Im folgenden Beispiel wird ein virtueller Computer namens *myVM* in der Ressourcengruppe *myResourceGroup* mit einer benutzerzugewiesenen verwalteten Identität `ID1` erstellt, wie es im Anforderungstext durch den Wert `"identity":{"type":"UserAssigned"}` angegeben ist. Ersetzen Sie `<ACCESS TOKEN>` durch den Wert, den Sie im vorherigen Schritt zum Anfordern eines Bearer-Zugriffstokens erhalten haben, und ersetzen Sie den Wert `<SUBSCRIPTION ID>` entsprechend Ihrer Umgebung.
 
   **API-VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 

   **Anforderungstext**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **API-VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 

   **Anforderungstext**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einem vorhandenen virtuellen Azure-Computer

Für die Zuweisung einer benutzerseitig zugewiesenen Identität zu einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisungen [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) und [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um den virtuellen Computer mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Erstellen Sie eine benutzerzugewiesene verwaltete Identität anhand der Anweisungen unter [Erstellen einer vom Benutzer zugewiesenen verwalteten Identität](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Um sicherzustellen, dass Sie keine vorhandenen benutzer- oder systemzugewiesenen verwalteten Identitäten löschen, die dem virtuellen Computer zugewiesen sind, müssen Sie die Identitätstypen, die dem virtuellen Computer zugewiesen sind, mit dem folgenden CURL-Befehl auflisten. Sollten der VM-Skalierungsgruppe verwaltete Identitäten zugewiesen sein, sind diese unter dem `identity`-Wert aufgelistet.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.

    Wenn dem virtuellen Computer benutzer- oder systemseitig zugewiesene verwaltete Identitäten zugewiesen sind, wie es im `identity`-Wert in der Antwort angegeben ist, fahren Sie mit Schritt 5 fort. Dort wird gezeigt, wie Sie die systemseitig zugewiesene verwaltete Identität beibehalten und gleichzeitig dem virtuellen Computer eine benutzerseitig zugewiesene verwaltete Identität hinzufügen.

4. Wenn dem virtuellen Computer keine benutzerzugewiesenen verwalteten Identitäten zugewiesen sind, verwenden Sie den folgenden CURL-Befehl zum Aufrufen des Azure Resource Manager-REST-Endpunkts, um dem virtuellen Computer die erste benutzerzugewiesene verwaltete Identität zuzuweisen.

   Im folgenden Beispiel wird einem virtuellen Computer namens *myVM* in der Ressourcengruppe *myResourceGroup* die vom Benutzer zugewiesene verwaltete Identität `ID1` zugewiesen.  Ersetzen Sie `<ACCESS TOKEN>` durch den Wert, den Sie im vorherigen Schritt zum Anfordern eines Bearer-Zugriffstokens erhalten haben, und ersetzen Sie den Wert `<SUBSCRIPTION ID>` entsprechend Ihrer Umgebung.

   **API-VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        |
 
   **Anforderungstext**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **API-VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 

   **Anforderungstext**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. Gehen Sie wie folgt vor, wenn Ihrem virtuellen Computer eine benutzer- oder systemzugewiesene verwaltete Identität zugewiesen ist:
   
   **API-VERSION 2018-06-01**

   Fügen Sie die vom Benutzer zugewiesene verwaltete Identität dem `userAssignedIdentities`-Wörterbuchwert hinzu.
    
   Wenn Ihrem virtuellen Computer derzeit beispielweise die systemzugewiesene verwaltete Identität und die benutzerzugewiesene verwaltete Identität `ID1` zugewiesen sind und Sie die benutzerzugewiesene verwaltete Identität `ID2` hinzufügen möchten, verwenden Sie folgenden Befehl:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 

   **Anforderungstext**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **API-VERSION 2017-12-01**

   Behalten Sie beim Hinzufügen der neuen benutzerzugewiesenen verwalteten Identität die benutzerzugewiesenen verwalteten Identitäten bei, die im `identityIds`-Arraywert erhalten bleiben sollen.

   Wenn Ihrem virtuellen Computer derzeit beispielweise die systemzugewiesene verwaltete Identität und die benutzerzugewiesene verwaltete Identität `ID1` zugewiesen sind und Sie die benutzerzugewiesene verwaltete Identität `ID2` hinzufügen möchten, verwenden Sie folgenden Befehl: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 

   **Anforderungstext**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Entfernen einer vom Benutzer zugewiesenen verwalteten Identität von einer Azure-VM

Für das Entfernen einer benutzerseitig zugewiesenen Identität von einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

1. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um den virtuellen Computer mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Um sicherzustellen, dass Sie keine vorhandenen benutzerzugewiesenen verwalteten Identitäten löschen, die dem virtuellen Computer weiterhin zugewiesen sein sollen, und auch nicht die systemzugewiesene verwaltete Identität entfernen, müssen Sie die verwalteten Identitäten mit dem folgenden CURL-Befehl auflisten: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.
 
   Sollten dem virtuellen Computer verwaltete Identitäten zugewiesen sein, sind diese in der Antwort im `identity`-Wert aufgelistet.

   Wenn beispielsweise die benutzerzugewiesenen verwalteten Identitäten `ID1` und `ID2` Ihrem virtuellen Computer zugewiesen sind und nur `ID1` zugewiesen bleiben und die systemzugewiesene Identität beibehalten werden soll, verwenden Sie folgenden Befehl:
   
   **API-VERSION 2018-06-01**

   Fügen Sie `null` der benutzerzugewiesenen verwalteten Identität hinzu, die Sie entfernen möchten:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 

   **Anforderungstext**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **API-VERSION 2017-12-01**

   Behalten Sie nur die vom Benutzer zugewiesenen verwalteten Identitäten bei, die im `identityIds`-Array erhalten bleiben sollen:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        | 

   **Anforderungstext**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Wenn der virtuelle Azure-Computer sowohl system- als auch benutzerzugewiesene verwaltete Identitäten aufweist, können Sie alle benutzerzugewiesenen verwalteten Identitäten entfernen, indem Sie mit dem folgenden Befehl in den Modus wechseln, in dem nur die systemzugewiesene verwaltete Identität verwendet wird:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Anforderungsheader**

|Anforderungsheader  |BESCHREIBUNG  |
|---------|---------|
|*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
|*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest. | 

**Anforderungstext**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Wenn der virtuelle Computer nur benutzerzugewiesene verwaltete Identitäten enthält und sie alle entfernen möchten, verwenden Sie den folgenden Befehl:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Anforderungsheader**

|Anforderungsheader  |BESCHREIBUNG  |
|---------|---------|
|*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
|*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.| 

**Anforderungstext**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Erstellen, Auflisten oder Löschen von benutzerzugewiesenen verwalteten Identitäten mithilfe von REST finden Sie unter:

- [Erstellen, Auflisten oder Löschen einer vom Benutzer zugewiesenen verwalteten Identität mithilfe von REST-API-Aufrufen](how-to-manage-ua-identity-rest.md)
