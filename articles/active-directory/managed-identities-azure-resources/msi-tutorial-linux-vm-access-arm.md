---
title: Zugreifen auf Azure Resource Manager mithilfe einer benutzerseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers
description: In diesem Tutorial wird erläutert, wie Sie eine benutzerseitig zugewiesene verwaltete Identität auf einem virtuellen Linux-Computer verwenden, um auf Azure Resource Manager zuzugreifen.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: barclayn
ROBOTS: NOINDEX,NOFOLLOW
ms.collection: M365-identity-device-management
ms.openlocfilehash: 247b3c1b4341eff11069a6af324fff5cf1fba62c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546589"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Tutorial: Zugreifen auf Azure Resource Manager mithilfe einer benutzerseitig zugewiesenen verwalteten Identität auf einem virtuellen Linux-Computer

In diesem Tutorial erfahren Sie, wie Sie eine benutzerseitig zugewiesene verwaltete Identität erstellen, diese einem virtuellen Linux-Computer zuweisen und anschließend verwenden, um auf die Azure Resource Manager-API zuzugreifen. Verwaltete Identitäten für Azure-Ressourcen werden von Azure automatisch verwaltet. Sie ermöglichen die Authentifizierung von Diensten mit Unterstützung für die Azure AD-Authentifizierung, ohne dass Anmeldeinformationen in Ihren Code eingebettet werden müssen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer benutzerseitig zugewiesenen verwalteten Identität
> * Zuweisen der benutzerseitig zugewiesenen verwalteten Identität zu einem virtuellen Linux-Computer 
> * Gewähren des Zugriffs auf eine Ressourcengruppe in Azure Resource Manager für die benutzerseitig zugewiesene verwaltete Identität 
> * Abrufen eines Zugriffstokens mithilfe der benutzerseitig zugewiesenen verwalteten Identität und Verwenden dieses Zugriffstokens zum Aufrufen von Azure Resource Manager 

## <a name="prerequisites"></a>Voraussetzungen

- Kenntnisse im Bereich verwaltete Identitäten. Wenn Sie mit der Funktion für verwaltete Identitäten für Azure-Ressourcen nicht vertraut sind, finden Sie hier eine [Übersicht](overview.md). 
- Ein Azure-Konto. [Registrieren Sie sich für ein kostenloses Azure-Konto.](https://azure.microsoft.com/free/)
- Darüber hinaus benötigen Sie einen virtuellen Linux-Computer. Wenn Sie einen virtuellen Computer für dieses Tutorial erstellen müssen, können Sie den Artikel [Schnellstart: Erstellen eines virtuellen Linux-Computers im Azure-Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine) durcharbeiten.
- Die Beispielskripts können auf zwei Arten ausgeführt werden:
    - Verwenden Sie den Dienst [Azure Cloud Shell](../../cloud-shell/overview.md), den Sie mithilfe der Schaltfläche **Testen** in der rechten oberen Ecke der Codeblöcke öffnen können.
    - Führen Sie Skripts lokal aus, indem Sie die neueste Version der [Azure CLI](/cli/azure/install-azure-cli) installieren, und melden Sie sich dann mit dem Befehl [az login](/cli/azure/reference-index#az-login) bei Azure an.

## <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

Erstellen Sie mit [az identity create](/cli/azure/identity#az-identity-create) eine benutzerseitig zugewiesene verwaltete Identität. Der Parameter `-g` gibt die Ressourcengruppe an, in der die vom Benutzer zugewiesene verwaltete Identität erstellt wird, und der Parameter `-n` gibt den Namen an. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<UAMI NAME>` durch Ihre eigenen Werte:
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

Die Antwort enthält Details zu der erstellten benutzerseitig zugewiesenen verwalteten Identität, ähnlich dem folgenden Beispiel. Notieren Sie sich den Wert `id` Ihrer benutzerseitig zugewiesenen verwalteten Identität, da er im nächsten Schritt verwendet wird:

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-an-identity-to-your-linux-vm"></a>Zuweisen einer Identität zum virtuellen Linux-Computer

Eine benutzerseitig zugewiesene verwaltete Identität kann von Clients für mehrere Azure-Ressourcen verwendet werden. Verwenden Sie die folgenden Befehle, um einem einzelnen virtuellen Computer die benutzerseitig zugewiesene verwaltete Identität zuzuweisen. Verwenden Sie die Eigenschaft `Id`, die im vorherigen Schritt für den Parameter `-IdentityID` zurückgegeben wird.

Weisen Sie mit [az vm identity assign](/cli/azure/vm) Ihrem virtuellen Linux-Computer die benutzerseitig zugewiesene verwaltete Identität zu. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<VM NAME>` durch Ihre eigenen Werte. Verwenden Sie die Eigenschaft `id`, die im vorherigen Schritt für den Parameterwert `--identities` zurückgegeben wird.

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-access-to-a-resource-group-in-azure-resource-manager"></a>Gewähren des Zugriffs auf eine Ressourcengruppe in Azure Resource Manager

Verwaltete Identitäten für Azure-Ressourcen stellen Identitäten dar, mit denen Ihr Code Zugriffstoken zur Authentifizierung bei Ressourcen-APIs anfordern kann, die die Azure AD-Authentifizierung unterstützen. In diesem Tutorial greift Ihr Code auf die Azure Resource Manager-API zu.  

Bevor Ihr Code auf die API zugreifen kann, müssen Sie der Identität Zugriff auf eine Ressource im Azure Resource Manager gewähren. In diesem Fall ist diese die Ressourcengruppe, in der die VM enthalten ist. Aktualisieren Sie abhängig von Ihrer Umgebung die Werte für `<SUBSCRIPTION ID>` und `<RESOURCE GROUP>`. Ersetzen Sie `<UAMI PRINCIPALID>` durch die Eigenschaft `principalId`, die unter [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](#create-a-user-assigned-managed-identity) vom Befehl `az identity create` zurückgegeben wurde:

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Die Antwort enthält Details zu der erstellten Rollenzuweisung, ähnlich wie im folgenden Beispiel dargestellt wird:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwendung zum Aufrufen von Resource Manager 

Für den Rest des Tutorials arbeiten wir von dem virtuellen Computer aus, den wir zuvor erstellt haben.

Zum Ausführen dieser Schritte benötigen Sie einen SSH-Client. Wenn Sie Windows verwenden, können Sie den SSH-Client im [Windows-Subsystem für Linux](/windows/wsl/about) verwenden. 

1. Melden Sie sich beim Azure-[Portal](https://portal.azure.com) an.
2. Navigieren Sie im Portal zu **Virtuelle Computer**, wechseln Sie dann zum virtuellen Linux-Computer, und klicken Sie in der **Übersicht** auf **Verbinden**. Kopieren Sie die Zeichenfolge, um eine Verbindung mit Ihrem virtuellen Computer herzustellen.
3. Stellen Sie eine Verbindung zwischen der VM und dem gewünschten SSH-Client her. Wenn Sie Windows verwenden, können Sie den SSH-Client im [Windows-Subsystem für Linux](/windows/wsl/about) verwenden. Wenn Sie Hilfe beim Konfigurieren der SSH-Clientschlüssel benötigen, lesen Sie die Informationen unter [Vorgehensweise: Verwenden von SSH-Schlüsseln mit Windows in Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) oder [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. Erstellen Sie im Terminalfenster mit CURL eine Anforderung an den Azure IMDS-Identitätsendpunkt (Instance Metadata Service) zum Abrufen eines Zugriffstokens für den Azure Resource Manager.  

   Im folgenden Beispiel wird die cURL-Anforderung zum Abrufen eines Zugriffstokens angezeigt. Ersetzen Sie `<CLIENT ID>` durch die Eigenschaft `clientId`, die unter [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](#create-a-user-assigned-managed-identity) vom Befehl `az identity create` zurückgegeben wurde: 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Der Wert des `resource`-Parameters muss exakt mit dem von Azure AD erwarteten Wert übereinstimmen. Wenn Sie die Resource Manager-Ressourcen-ID verwenden, müssen Sie den nachgestellten Schrägstrich im URI verwenden. 
    
    Die Antwort enthält das Zugriffstoken, das Sie für den Zugriff auf Azure Resource Manager benötigen. 
    
    Beispielantwort:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. Verwenden Sie dieses Zugriffstoken, um auf Azure Resource Manager zuzugreifen und die Eigenschaften der Ressourcengruppe zu lesen, für die Sie der benutzerseitig zugewiesenen verwalteten Identität zuvor den Zugriff gewährt haben. Ersetzen Sie `<SUBSCRIPTION ID>` und `<RESOURCE GROUP>` durch die Werte, die Sie zuvor angegeben haben, und `<ACCESS TOKEN>` durch den im vorherigen Schritt zurückgegebenen Token.

    > [!NOTE]
    > Bei der URL wird die Groß-/Kleinschreibung beachtet. Achten Sie daher darauf, dieselbe Groß- und Kleinschreibung zu verwenden wie zuvor beim Benennen der Ressourcengruppe, einschließlich des Großbuchstaben „G“ in `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Die Antwort enthält die jeweiligen Informationen zur Ressourcengruppe, ähnlich wie im folgenden Beispiel dargestellt wird: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie eine benutzerseitig zugewiesene verwaltete Identität erstellen und an einen virtuellen Linux-Computer anfügen, um auf die Azure Resource Manager-API zuzugreifen.  Weitere Informationen zu Azure Resource Manager finden Sie hier:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
