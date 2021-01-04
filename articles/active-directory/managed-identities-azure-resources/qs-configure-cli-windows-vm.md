---
title: Konfigurieren verwalteter Identitäten auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle – Azure AD
description: Ausführliche Anweisungen zum Konfigurieren von system- und benutzerseitig zugewiesenen verwalteten Identitäten auf einem virtuellen Azure-Computer mit der Azure-Befehlszeilenschnittstelle
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
ms.openlocfilehash: 90b18c2556796f23be0c1135b0bad0d53368a46d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590952"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle die folgenden Vorgänge für verwaltete Identitäten für Azure-Ressourcen auf einer Azure-VM ausführen können:

- Aktivieren und Deaktivieren der vom System zugewiesenen verwalteten Identität auf einem virtuellen Azure-Computer
- Hinzufügen und Entfernen einer vom Benutzer zugewiesenen verwalteten Identität auf einem virtuellen Azure-Computer

Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie mit verwalteten Identitäten für Azure-Ressourcen noch nicht vertraut sind, lesen Sie [Was sind verwaltete Identitäten für Azure-Ressourcen?](overview.md). Informationen zu systemseitig zugewiesenen und benutzerseitig zugewiesenen Typen verwalteter Identitäten finden Sie unter [Arten von verwalteten Identitäten](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie die vom System zugewiesene verwaltete Identität auf einem virtuellen Azure-Computer mit der Azure-Befehlszeilenschnittstelle aktivieren und deaktivieren können.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität beim Erstellen eines virtuellen Azure-Computers

Zum Erstellen eines virtuellen Azure-Computers, auf dem die systemseitig zugewiesene verwaltete Identität aktiviert ist, benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Erstellen Sie eine [Ressourcengruppe](../../azure-resource-manager/management/overview.md#terminology) für das Einschließen und Bereitstellen des virtuellen Computers und der zugehörigen Ressourcen. Verwenden Sie hierfür [az group create](/cli/azure/group/#az-group-create). Sie können diesen Schritt überspringen, wenn Sie bereits über eine Ressourcengruppe verfügen, die Sie stattdessen verwenden möchten:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. Erstellen Sie einen virtuellen Computer mit [az vm create](/cli/azure/vm/#az-vm-create). Im folgenden Beispiel wird der virtuelle Computer *myVM* mit einer vom System zugewiesenen verwalteten Identität erstellt. Dies wird vom Parameter `--assign-identity` gefordert. Der `--admin-username`-Parameter und der `--admin-password`-Parameter geben den Namen und das Kennwort des Administratorbenutzers für die Anmeldung am virtuellen Computer an. Aktualisieren Sie diese Werte ggf. mit den entsprechenden Werten für Ihre Umgebung: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität auf einem vorhandenen virtuellen Azure-Computer

Zum Aktivieren der systemseitig zugewiesenen verwalteten Identität auf einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Melden Sie sich bei Verwendung der Azure CLI in einer lokalen Konsole zunächst mit [az login](/cli/azure/reference-index#az-login) bei Azure an. Verwenden Sie ein Konto, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.

   ```azurecli-interactive
   az login
   ```

2. Verwenden Sie [az vmss identity assign](/cli/azure/vm/identity/) mit dem Befehl `identity assign`, um die vom System zugewiesene Identität auf einem vorhandenen virtuellen Computer zu aktivieren:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Deaktivieren einer vom System zugewiesenen Identität auf einem virtuellen Azure-Computer

Zum Deaktivieren der systemseitig zugewiesenen verwalteten Identität auf einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

Wenn Sie über einen virtuellen Computer verfügen, der nicht mehr die vom System zugewiesene Identität, jedoch weiterhin vom Benutzer zugewiesene Identitäten benötigt, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Bei einem virtuellen Computer, der nicht mehr die vom System zugewiesene Identität benötigt und über keine vom Benutzer zugewiesenen Identitäten verfügt, verwenden Sie den folgenden Befehl:

> [!NOTE]
> Für den Wert `none` müssen Sie die Groß-/Kleinschreibung beachten. Er darf nur aus Kleinbuchstaben bestehen. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle eine vom Benutzer zugewiesene verwaltete Identität einem virtuellen Azure-Computer hinzufügen oder von diesem entfernen. Wenn Sie Ihre benutzerseitig verwaltete Identität in einer anderen Ressourcengruppe als den virtuellen Computer erstellen, müssen Sie die URL der verwalteten Identität für die Zuweisung zum virtuellen Computer verwenden,
d. h.: --identities "/subscriptions/<SUBID>/resourcegroups/<RESROURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER_ASSIGNED_ID_NAME>".

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität beim Erstellen eines virtuellen Azure-Computers

Für die Zuweisung einer benutzerseitig zugewiesenen Identität an einen virtuellen Computer während dessen Erstellung benötigt Ihr Konto die Rollenzuweisungen [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) und [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Sie können diesen Schritt überspringen, wenn Sie bereits über eine Ressourcengruppe verfügen, die Sie verwenden möchten. Erstellen Sie mit [az group create](/cli/azure/group/#az-group-create) eine [Ressourcengruppe](~/articles/azure-resource-manager/management/overview.md#terminology) zum Einschließen und Bereitstellen Ihrer vom Benutzer zugewiesenen verwalteten Identität. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<LOCATION>` durch Ihre eigenen Werte. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Erstellen Sie mit [az identity create](/cli/azure/identity#az-identity-create) eine benutzerseitig zugewiesene verwaltete Identität.  Der Parameter `-g` gibt die Ressourcengruppe an, in der die vom Benutzer zugewiesene verwaltete Identität erstellt wird, und der Parameter `-n` gibt den Namen an.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Die Antwort enthält Details zu der erstellten vom Benutzer zugewiesenen verwalteten Identität, ähnlich dem folgenden Beispiel. Der Wert der Ressourcen-ID, der der vom Benutzer zugewiesenen verwalteten Identität zugewiesen ist, wird im nächsten Schritt verwendet.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Erstellen Sie einen virtuellen Computer mit [az vm create](/cli/azure/vm/#az-vm-create). Im folgenden Beispiel wird ein virtueller Computer erstellt, dem die neue vom Benutzer zugewiesene verwaltete Identität zugewiesen ist. Dies wird durch den Parameter `--assign-identity` angegeben. Ersetzen Sie die Parameter`<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` und `<USER ASSIGNED IDENTITY NAME>` durch Ihre eigenen Werte. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einem vorhandenen virtuellen Azure-Computer

Für die Zuweisung einer benutzerseitig zugewiesenen Identität zu einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisungen [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) und [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Erstellen Sie mit [az identity create](/cli/azure/identity#az-identity-create) eine vom Benutzer zugewiesene Identität.  Der Parameter `-g` gibt die Ressourcengruppe an, in der die vom Benutzer zugewiesene Identität erstellt wird, und der Parameter `-n` gibt den Namen an. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<USER ASSIGNED IDENTITY NAME>` durch Ihre eigenen Werte:

    > [!IMPORTANT]
    > Das Erstellen einer vom Benutzer zugewiesenen verwalteten Identität mit Sonderzeichen (z.B. Unterstrich) im Namen wird derzeit nicht unterstützt. Verwenden Sie alphanumerische Zeichen. Überprüfen Sie zu einem späteren Zeitpunkt auf dieser Seite, ob neue Informationen vorliegen.  Weitere Informationen finden Sie unter [FAQs und bekannte Probleme mit der verwalteten Dienstidentität (Managed Service Identity, MSI) für Azure Active Directory](known-issues.md).

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Die Antwort enthält Details zu der erstellten vom Benutzer zugewiesenen verwalteten Identität, ähnlich dem folgenden Beispiel. 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Verwenden Sie [az vm identity assign](/cli/azure/vm), um Ihrem virtuellen Computer die vom Benutzer zugewiesene Identität zuzuweisen. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<VM NAME>` durch Ihre eigenen Werte. `<USER ASSIGNED IDENTITY NAME>` ist die im vorherigen Schritt erstellte `name`-Eigenschaft der Ressource der benutzerseitig zugewiesenen verwalteten Identität: Wenn Sie Ihre benutzerseitig verwaltete Identität in einer anderen Ressourcengruppe als den virtuellen Computer erstellen, müssen Sie die URL der verwalteten Identität verwenden.

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Entfernen einer vom Benutzer zugewiesenen verwalteten Identität von einer Azure-VM

Für das Entfernen einer benutzerseitig zugewiesenen Identität von einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor). 

Wenn dies die einzige vom Benutzer zugewiesene verwaltete Identität des virtuellen Computers ist, wird `UserAssigned` aus dem Wert des Identitätstyps entfernt.  Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<VM NAME>` durch Ihre eigenen Werte. `<USER ASSIGNED IDENTITY>` ist die `name`-Eigenschaft der vom Benutzer zugewiesen Identität. Diese Eigenschaft kann mit `az vm identity show` aus dem Identitätsabschnitt des virtuellen Computers abgerufen werden:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Wenn Ihre VM keine vom System zugewiesene verwaltete Identität hat, und Sie alle vom Benutzer zugewiesenen Identitäten entfernen möchten, verwenden Sie den folgenden Befehl:

> [!NOTE]
> Für den Wert `none` müssen Sie die Groß-/Kleinschreibung beachten. Er darf nur aus Kleinbuchstaben bestehen.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Wenn Ihr virtueller Computer sowohl vom System als auch vom Benutzer zugewiesene Identitäten hat, können Sie alle vom Benutzer zugewiesenen Identitäten entfernen, indem Sie in den Modus wechseln, in dem nur die vom System zugewiesene Identität verwendet wird. Verwenden Sie den folgenden Befehl:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Nächste Schritte
- [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](overview.md)
- Die vollständigen Schnellstarts zum Erstellen virtueller Azure-Computer finden Sie unter: 
  - [Erstellen eines virtuellen Windows-Computers mit der CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Erstellen eines virtuellen Linux-Computers mit der CLI](../../virtual-machines/linux/quick-create-cli.md)
