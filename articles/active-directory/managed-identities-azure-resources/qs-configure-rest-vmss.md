---
title: Konfigurieren verwalteter Identitäten für eine Azure-VM-Skalierungsgruppe mithilfe von REST– Azure AD
description: Schrittweise Anweisungen zum Konfigurieren von vom System und dem Benutzer zugewiesenen verwalteten Identitäten für eine Azure-VM-Skalierungsgruppe mithilfe von CURL zum Durchführen von REST-API-Aufrufen.
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
ms.date: 06/25/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: c79942aad2ce450bc22aa0a0cfc32e67a667bd48
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006233"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe mithilfe von REST-API-Aufrufen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Systemidentität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erfahren Sie, wie Sie unter Verwendung von CURL für Aufrufe an den Azure Resource Manager-REST-Endpunkt die folgenden Vorgänge für verwaltete Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe ausführen können:

- Aktivieren und Deaktivieren der systemzugewiesenen verwalteten Identität in einer Azure VM-Skalierungsgruppe
- Hinzufügen und Entfernen einer benutzerzugewiesenen verwalteten Identität in einer Azure VM-Skalierungsgruppe

Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie mit verwalteten Identitäten für Azure-Ressourcen noch nicht vertraut sind, lesen Sie [Was sind verwaltete Identitäten für Azure-Ressourcen?](overview.md). Informationen zu systemseitig zugewiesenen und benutzerseitig zugewiesenen Typen verwalteter Identitäten finden Sie unter [Arten von verwalteten Identitäten](overview.md#managed-identity-types).

- Um die Verwaltungsvorgänge in diesem Artikel auszuführen, benötigt Ihr Konto die folgenden Azure-Rollenzuweisungen:

  - [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor), um eine VM-Skalierungsgruppe zu erstellen und die vom System und/oder Benutzer zugewiesene verwaltete Identität für eine VM-Skalierungsgruppe zu aktivieren bzw. zu entfernen

  - [Mitwirkender für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-contributor), um eine vom Benutzer zugewiesene verwaltete Identität zu erstellen

  - [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator), um eine vom Benutzer zugewiesene Identität einer VM-Skalierungsgruppe zuzuweisen bzw. daraus zu entfernen

  > [!NOTE]
  > Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie unter Verwendung von CURL für Aufrufe an den Azure Resource Manager-REST-Endpunkt eine systemzugewiesene verwaltete Identität in einer VM-Skalierungsgruppe aktivieren und deaktivieren können.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Aktivieren der vom System zugewiesenen verwalteten Identität bei der Erstellung einer VM-Skalierungsgruppe

Zum Erstellen einer VM-Skalierungsgruppe mit aktivierter systemzugewiesener verwalteter Identität müssen Sie eine VM-Skalierungsgruppe erstellen und ein Zugriffstoken abrufen, um CURL zum Aufrufen des Resource Manager-Endpunkts mit dem Wert für den systemzugewiesenen verwalteten Identitätstyp zu verwenden.

1. Erstellen Sie eine [Ressourcengruppe](../../azure-resource-manager/management/overview.md#terminology) für das Einschließen und Bereitstellen der VM-Skalierungsgruppe und der zugehörigen Ressourcen. Verwenden Sie hierfür [az group create](/cli/azure/group/#az-group-create). Sie können diesen Schritt überspringen, wenn Sie bereits über eine Ressourcengruppe verfügen, die Sie stattdessen verwenden möchten:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Erstellen Sie eine [Netzwerkschnittstelle](/cli/azure/network/nic#az-network-nic-create) für die VM-Skalierungsgruppe:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um die VM-Skalierungsgruppe mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Erstellen Sie über Azure Cloud Shell eine VM-Skalierungsgruppe mithilfe von CURL zum Aufrufen des Azure Resource Manager-REST-Endpunkts. Im folgenden Beispiel wird eine VM-Skalierungsgruppe namens *myVMSS* in *myResourceGroup* mit einer systemzugewiesenen verwalteten Identität erstellt, wie es im Anforderungstext durch den Wert `"identity":{"type":"SystemAssigned"}` angegeben ist. Ersetzen Sie `<ACCESS TOKEN>` durch den Wert, den Sie im vorherigen Schritt zum Anfordern eines Bearer-Zugriffstokens erhalten haben, und ersetzen Sie den Wert `<SUBSCRIPTION ID>` entsprechend Ihrer Umgebung.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest. | 

   **Anforderungstext**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität in einer vorhandenen VM-Skalierungsgruppe

Um die systemzugewiesene verwaltete Identität in einer vorhandenen VM-Skalierungsgruppe zu aktivieren, müssen Sie ein Zugriffstoken abrufen und dann CURL verwenden, um den Resource Manager-REST-Endpunkt zum Aktualisieren des Identitätstyps aufzurufen.

1. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um die VM-Skalierungsgruppe mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Verwenden Sie den folgenden CURL-Befehl zum Aufrufen des Azure Resource Manager-REST-Endpunkts, um die systemzugewiesene verwaltete Identität in der VM-Skalierungsgruppe zu aktivieren, wie es im Anforderungstext durch den Wert `{"identity":{"type":"SystemAssigned"}` für eine VM-Skalierungsgruppe namens *myVMSS* angegeben ist.  Ersetzen Sie `<ACCESS TOKEN>` durch den Wert, den Sie im vorherigen Schritt zum Anfordern eines Bearer-Zugriffstokens erhalten haben, und ersetzen Sie den Wert `<SUBSCRIPTION ID>` entsprechend Ihrer Umgebung.
   
   > [!IMPORTANT]
   > Um sicherzustellen, dass Sie keine vorhandenen benutzerzugewiesenen verwalteten Identitäten löschen, die der VM-Skalierungsgruppe zugewiesen sind, müssen Sie die benutzerzugewiesenen verwalteten Identitäten mit dem folgenden CURL-Befehl auflisten: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` Wenn der VM-Skalierungsgruppe benutzerzugewiesene verwaltete Identitäten zugewiesen sind, wie es im `identity`-Wert in der Antwort angegeben ist, fahren Sie mit Schritt 3 fort. Dort wird gezeigt, wie Sie benutzerzugewiesene verwaltete Identitäten beibehalten und gleichzeitig die systemzugewiesene verwaltete Identität in der VM-Skalierungsgruppe aktivieren.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

3. Zum Aktivieren der systemzugewiesenen verwalteten Identität in einer VM-Skalierungsgruppe mit vorhandenen benutzerzugewiesenen verwalteten Identitäten müssen Sie `SystemAssigned` zum `type`-Wert hinzufügen.  
   
   Wenn der VM-Skalierungsgruppe beispielsweise die benutzerzugewiesenen verwalteten Identitäten `ID1` und `ID2` zugewiesen sind und Sie der VM-Skalierungsgruppe eine systemzugewiesene verwalteten Identität hinzufügen möchten, verwenden Sie den folgenden CURL-Aufruf. Ersetzen Sie `<ACCESS TOKEN>` und `<SUBSCRIPTION ID>` durch Werte entsprechend Ihrer Umgebung.

   Bei der API-Version `2018-06-01` werden die vom Benutzer zugewiesenen verwalteten Identitäten im Wert `userAssignedIdentities` in einem Wörterbuchformat gespeichert (im Gegensatz zum Wert `identityIds` in einem Arrayformat, der in API-Version `2017-12-01` verwendet wird).
   
   **API-VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
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

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Deaktivieren einer vom System zugewiesenen verwalteten Identität in einer VM-Skalierungsgruppe

Um eine systemzugewiesene Identität in einer vorhandenen VM-Skalierungsgruppe zu deaktivieren, müssen Sie ein Zugriffstoken abrufen und dann CURL verwenden, um den Resource Manager-REST-Endpunkt zum Aktualisieren des Identitätstyps in `None` aufzurufen.

1. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um die VM-Skalierungsgruppe mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aktualisieren Sie die VM-Skalierungsgruppe mithilfe von CURL zum Aufrufen des Azure Resource Manager-REST-Endpunkts, um die systemzugewiesene verwalteten Identität zu deaktivieren.  Im folgenden Beispiel wird die systemzugewiesene verwaltete Identität in einer VM-Skalierungsgruppe namens *myVMSS* deaktiviert, wie es im Anforderungstext durch den Wert `{"identity":{"type":"None"}}` angegeben ist.  Ersetzen Sie `<ACCESS TOKEN>` durch den Wert, den Sie im vorherigen Schritt zum Anfordern eines Bearer-Zugriffstokens erhalten haben, und ersetzen Sie den Wert `<SUBSCRIPTION ID>` entsprechend Ihrer Umgebung.

   > [!IMPORTANT]
   > Um sicherzustellen, dass Sie keine vorhandenen benutzerzugewiesenen verwalteten Identitäten löschen, die der VM-Skalierungsgruppe zugewiesen sind, müssen Sie die benutzerzugewiesenen verwalteten Identitäten mit dem folgenden CURL-Befehl auflisten: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` Wenn der VM-Skalierungsgruppe benutzerzugewiesene verwaltete Identitäten zugewiesen sind, fahren Sie mit Schritt 3 fort. Dort wird gezeigt, wie Sie die benutzerzugewiesenen verwalteten Identitäten beibehalten und gleichzeitig die systemzugewiesene verwaltete Identität aus der VM-Skalierungsgruppe entfernen.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"None"
       }
    }
   ```

   Zum Entfernen der systemzugewiesenen verwalteten Identität aus einer VM-Skalierungsgruppe mit benutzerzugewiesenen verwalteten Identitäten entfernen Sie `SystemAssigned` aus dem Wert `{"identity":{"type:" "}}`, während Sie den Wert `UserAssigned` und die `userAssignedIdentities`-Wörterbuchwerte beibehalten, wenn Sie **API-Version 2018-06-01** verwenden. Bei Verwendung der **API-Version 2017-12-01** (oder einer älteren Version) behalten Sie das `identityIds`-Array bei.

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie unter Verwendung von CURL für Aufrufe an den Azure Resource Manager-REST-Endpunkt eine benutzerzugewiesene verwaltete Identität in einer VM-Skalierungsgruppe hinzufügen und entfernen können.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität bei der Erstellung einer VM-Skalierungsgruppe

1. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um die VM-Skalierungsgruppe mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Erstellen Sie eine [Netzwerkschnittstelle](/cli/azure/network/nic#az-network-nic-create) für die VM-Skalierungsgruppe:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um die VM-Skalierungsgruppe mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Erstellen Sie eine benutzerseitig zugewiesene Identität anhand der Anweisungen unter [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Erstellen Sie eine VM-Skalierungsgruppe mithilfe von CURL zum Aufrufen des Azure Resource Manager-REST-Endpunkts. Im folgenden Beispiel wird eine VM-Skalierungsgruppe namens *myVMSS* in der Ressourcengruppe *myResourceGroup* mit einer benutzerzugewiesenen verwaltete Identität `ID1` erstellt, wie es im Anforderungstext durch den Wert `"identity":{"type":"UserAssigned"}` angegeben ist. Ersetzen Sie `<ACCESS TOKEN>` durch den Wert, den Sie im vorherigen Schritt zum Anfordern eines Bearer-Zugriffstokens erhalten haben, und ersetzen Sie den Wert `<SUBSCRIPTION ID>` entsprechend Ihrer Umgebung.
 
   **API-VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest. | 

   **Anforderungstext**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **API-VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest. |
 
   **Anforderungstext**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einer vorhandenen Azure-VM-Skalierungsgruppe

1. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um die VM-Skalierungsgruppe mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Erstellen Sie eine benutzerzugewiesene verwaltete Identität anhand der Anweisungen unter [Erstellen einer vom Benutzer zugewiesenen verwalteten Identität](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Um sicherzustellen, dass Sie keine vorhandenen benutzer- oder systemzugewiesenen verwalteten Identitäten löschen, die der VM-Skalierungsgruppe zugewiesen sind, müssen Sie die Identitätstypen, die der VM-Skalierungsgruppe zugewiesen sind, mit dem folgenden CURL-Befehl auflisten. Sollten der VM-Skalierungsgruppe verwaltete Identitäten zugewiesen sein, sind diese unter dem Wert `identity` aufgelistet.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest. |   
 

4. Wenn der VM-Skalierungsgruppe keine benutzer- oder systemzugewiesenen verwalteten Identitäten zugewiesen sind, verwenden Sie den folgenden CURL-Befehl zum Aufrufen des Azure Resource Manager-REST-Endpunkts, um der VM-Skalierungsgruppe die erste benutzerzugewiesene verwaltete Identität zuzuweisen.  Wenn der VM-Skalierungsgruppe benutzer- oder systemzugewiesene verwaltete Identitäten zugewiesen sind, fahren Sie mit Schritt 5 fort. Dort wird gezeigt, wie Sie einer VM-Skalierungsgruppe mehrere benutzerzugewiesene verwaltete Identitäten hinzufügen und gleichzeitig die systemzugewiesene verwaltete Identität beibehalten.

   Im folgenden Beispiel wird einer VM-Skalierungsgruppe namens *myVMSS* in der Ressourcengruppe *myResourceGroup* eine benutzerzugewiesene verwaltete Identität `ID1` zugewiesen.  Ersetzen Sie `<ACCESS TOKEN>` durch den Wert, den Sie im vorherigen Schritt zum Anfordern eines Bearer-Zugriffstokens erhalten haben, und ersetzen Sie den Wert `<SUBSCRIPTION ID>` entsprechend Ihrer Umgebung.

   **API-VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **API-VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Gehen Sie wie folgt vor, wenn Ihrer VM-Skalierungsgruppe eine benutzer- oder systemzugewiesene verwaltete Identität zugewiesen ist:
   
   **API-VERSION 2018-06-01**

   Fügen Sie die vom Benutzer zugewiesene verwaltete Identität dem `userAssignedIdentities`-Wörterbuchwert hinzu.

   Wenn Ihrer VM-Skalierungsgruppe derzeit beispielweise die systemzugewiesene verwaltete Identität und die benutzerzugewiesene verwaltete Identität `ID1` zugewiesen sind und Sie die benutzerzugewiesene verwaltete Identität `ID2` hinzufügen möchten, verwenden Sie folgenden Befehl:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

   Wenn Ihrer VM-Skalierungsgruppe derzeit beispielweise die systemzugewiesene Identität und die benutzerzugewiesene verwaltete Identität `ID1` zugewiesen sind und Sie die benutzerzugewiesene verwaltete Identität `ID2` hinzufügen möchten, verwenden Sie folgenden Befehl:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Entfernen einer vom Benutzer zugewiesenen verwalteten Identität aus einer VM-Skalierungsgruppe

1. Rufen Sie ein Bearer-Zugriffstoken ab, das Sie dann im nächsten Schritt im Autorisierungsheader verwenden, um die VM-Skalierungsgruppe mit einer systemzugewiesenen verwalteten Identität zu erstellen.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Um sicherzustellen, dass Sie keine vorhandenen benutzerzugewiesenen verwalteten Identitäten löschen, die der VM-Skalierungsgruppe weiterhin zugewiesen sein sollen, und auch nicht die systemzugewiesene verwaltete Identität entfernen, müssen Sie die verwalteten Identitäten mit dem folgenden CURL-Befehl auflisten:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Anforderungsheader**

   |Anforderungsheader  |BESCHREIBUNG  |
   |---------|---------|
   |*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest. |
   
   Sollten dem virtuellen Computer verwaltete Identitäten zugewiesen sein, sind diese in der Antwort im `identity`-Wert aufgelistet. 
    
   Wenn beispielsweise die benutzerzugewiesenen verwalteten Identitäten `ID1` und `ID2` Ihrer VM-Skalierungsgruppe zugewiesen sind und nur `ID1` zugewiesen bleiben und die systemzugewiesene verwaltete Identität beibehalten werden soll, verwenden Sie folgenden Befehl:

   **API-VERSION 2018-06-01**

   Fügen Sie `null` der benutzerzugewiesenen verwalteten Identität hinzu, die Sie entfernen möchten:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Wenn die VM-Skalierungsgruppe sowohl system- als auch benutzerzugewiesene verwaltete Identitäten aufweist, können Sie alle benutzerzugewiesenen verwalteten Identitäten entfernen, indem Sie mit dem folgenden Befehl in den Modus wechseln, in dem nur die systemzugewiesene Identität verwendet wird:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
    
Wenn die VM-Skalierungsgruppe nur benutzerzugewiesene verwaltete Identitäten enthält und Sie alle entfernen möchten, verwenden Sie den folgenden Befehl:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Erstellen, Auflisten oder Löschen von benutzerzugewiesenen verwalteten Identitäten mithilfe von REST finden Sie unter:

- [Erstellen, Auflisten oder Löschen einer vom Benutzer zugewiesenen verwalteten Identität mithilfe von REST-API-Aufrufen](how-to-manage-ua-identity-rest.md)
