---
title: Konfigurieren verwalteter Identitäten für eine VM-Skalierungsgruppe – Azure-Befehlszeilenschnittstelle – Azure AD
description: Schrittweise Anweisungen zum Konfigurieren von vom System und vom Benutzer zugewiesenen verwalteten Identitäten in einer Azure-VM-Skalierungsgruppe mithilfe der Azure-Befehlszeilenschnittstelle.
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
ms.openlocfilehash: 0f88eccc524ffac424f8f5048990f693aa67613d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590935"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe mit der Azure-Befehlszeilenschnittstelle

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle die folgenden Vorgänge für verwaltete Identitäten für eine Azure-VM-Skalierungsgruppe ausführen:
- Aktivieren und Deaktivieren der systemzugewiesenen verwalteten Identität in einer Azure VM-Skalierungsgruppe
- Hinzufügen und Entfernen einer benutzerzugewiesenen verwalteten Identität in einer Azure VM-Skalierungsgruppe

Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie mit verwalteten Identitäten für Azure-Ressourcen noch nicht vertraut sind, lesen Sie [Was sind verwaltete Identitäten für Azure-Ressourcen?](overview.md). Informationen zu systemseitig zugewiesenen und benutzerseitig zugewiesenen Typen verwalteter Identitäten finden Sie unter [Arten von verwalteten Identitäten](overview.md#managed-identity-types).

- Um die Verwaltungsvorgänge in diesem Artikel auszuführen, benötigt Ihr Konto die folgenden Zuweisungen der rollenbasierten Azure-Zugriffssteuerung:

  - [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor), um eine VM-Skalierungsgruppe zu erstellen und die vom System und/oder Benutzer zugewiesene verwaltete Identität in einer VM-Skalierungsgruppe zu aktivieren bzw. daraus zu entfernen

  - [Mitwirkender für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-contributor), um eine vom Benutzer zugewiesene verwaltete Identität zu erstellen

  - [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator), um eine vom Benutzer zugewiesene verwaltete Identität einer VM-Skalierungsgruppe zuzuweisen bzw. daraus zu entfernen

  > [!NOTE]
  > Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie die systemseitig zugewiesene verwaltete Identität für eine Azure-VM-Skalierungsgruppe mit der Azure-Befehlszeilenschnittstelle aktivieren und deaktivieren.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivieren der vom System zugewiesenen verwalteten Identität bei der Erstellung einer Azure-VM-Skalierungsgruppe

So erstellen Sie eine VM-Skalierungsgruppe samt aktivierter vom System zugewiesener verwalteter Identität

1. Erstellen Sie eine [Ressourcengruppe](../../azure-resource-manager/management/overview.md#terminology) für das Einschließen und Bereitstellen der VM-Skalierungsgruppe und der zugehörigen Ressourcen. Verwenden Sie hierfür [az group create](/cli/azure/group/#az-group-create). Sie können diesen Schritt überspringen, wenn Sie bereits über eine Ressourcengruppe verfügen, die Sie stattdessen verwenden möchten:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. [Erstellen](/cli/azure/vmss/#az-vmss-create) einer VM-Skalierungsgruppe Im folgenden Beispiel wird die VM-Skalierungsgruppe *myVMSS* mit einer vom System zugewiesenen verwalteten Identität erstellt. Dies wird durch den Parameter `--assign-identity` gefordert. Der `--admin-username`-Parameter und der `--admin-password`-Parameter geben den Namen und das Kennwort des Administratorbenutzers für die Anmeldung am virtuellen Computer an. Aktualisieren Sie diese Werte ggf. mit den entsprechenden Werten für Ihre Umgebung: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität in einer vorhandenen Azure-VM-Skalierungsgruppe

Wenn Sie die systemseitig zugewiesene verwaltete Identität in einer vorhandenen Azure-VM-Skalierungsgruppe [aktivieren](/cli/azure/vmss/identity/#az-vmss-identity-assign) müssen, gehen Sie wie folgt vor:

```azurecli-interactive
az vmss identity assign -g myResourceGroup -n myVMSS
```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Deaktivieren der vom System zugewiesenen verwalteten Identität in einer Azure-VM-Skalierungsgruppe

Wenn Sie über eine VM-Skalierungsgruppe verfügen, die nicht mehr die vom System zugewiesene verwaltete Identität, jedoch weiterhin vom Benutzer zugewiesene verwaltete Identitäten benötigt, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Bei einem virtuellen Computer, der nicht mehr die vom System zugewiesene verwaltete Identität benötigt und über keine vom Benutzer zugewiesenen verwalteten Identitäten verfügt, verwenden Sie den folgenden Befehl:

> [!NOTE]
> Für den Wert `none` müssen Sie die Groß-/Kleinschreibung beachten. Er darf nur aus Kleinbuchstaben bestehen. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle eine vom Benutzer zugewiesene verwaltete Identität aktivieren und entfernen.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität bei der Erstellung einer VM-Skalierungsgruppe

Dieser Abschnitt führt Sie durch das Erstellen einer VM-Skalierungsgruppe und das Zuweisen einer benutzerseitig zugewiesenen verwalteten Identität zu der VM-Skalierungsgruppe. Wenn Sie eine bereits vorhandene VM-Skalierungsgruppe verwenden möchten, überspringen Sie diesen Abschnitt, und fahren Sie mit dem nächsten Abschnitt fort.

1. Sie können diesen Schritt überspringen, wenn Sie bereits über eine Ressourcengruppe verfügen, die Sie verwenden möchten. Erstellen Sie mit [az group create](/cli/azure/group/#az-group-create) eine [Ressourcengruppe](~/articles/azure-resource-manager/management/overview.md#terminology) zum Einschließen und Bereitstellen Ihrer vom Benutzer zugewiesenen verwalteten Identität. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<LOCATION>` durch Ihre eigenen Werte. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Erstellen Sie mit [az identity create](/cli/azure/identity#az-identity-create) eine benutzerseitig zugewiesene verwaltete Identität.  Der Parameter `-g` gibt die Ressourcengruppe an, in der die vom Benutzer zugewiesene verwaltete Identität erstellt wird, und der Parameter `-n` gibt den Namen an. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<USER ASSIGNED IDENTITY NAME>` durch Ihre eigenen Werte:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Die Antwort enthält Details zu der erstellten vom Benutzer zugewiesenen verwalteten Identität, ähnlich dem folgenden Beispiel. Der Wert `id` der Ressource, der der vom Benutzer zugewiesenen verwalteten Identität zugewiesen ist, wird im nächsten Schritt verwendet.

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

3. [Erstellen](/cli/azure/vmss/#az-vmss-create) einer VM-Skalierungsgruppe Im folgenden Beispiel wird eine VM-Skalierungsgruppe erstellt, der die neue benutzerseitig zugewiesene verwaltete Identität zugewiesen ist. Dies wird durch den Parameter `--assign-identity` angegeben. Ersetzen Sie die Parameter`<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` und `<USER ASSIGNED IDENTITY>` durch Ihre eigenen Werte. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einer vorhandenen VM-Skalierungsgruppe

1. Erstellen Sie mit [az identity create](/cli/azure/identity#az-identity-create) eine benutzerseitig zugewiesene verwaltete Identität.  Der Parameter `-g` gibt die Ressourcengruppe an, in der die vom Benutzer zugewiesene verwaltete Identität erstellt wird, und der Parameter `-n` gibt den Namen an. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<USER ASSIGNED IDENTITY NAME>` durch Ihre eigenen Werte:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Die Antwort enthält Details zu der erstellten vom Benutzer zugewiesenen verwalteten Identität, ähnlich dem folgenden Beispiel.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [Zuweisen](/cli/azure/vmss/identity) einer vom Benutzer zugewiesenen verwalteten Identität zu einer VM-Skalierungsgruppe Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<VIRTUAL MACHINE SCALE SET NAME>` durch Ihre eigenen Werte. `<USER ASSIGNED IDENTITY>` ist die im vorherigen Schritt erstellte `name`-Eigenschaft der Ressource der vom Benutzer zugewiesenen Identität:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Entfernen einer vom Benutzer zugewiesenen verwalteten Identität aus einer Azure-VM-Skalierungsgruppe

Zum [Entfernen](/cli/azure/vmss/identity#az-vmss-identity-remove) einer vom Benutzer zugewiesenen verwalteten Identität aus einer VM-Skalierungsgruppe wird `az vmss identity remove` verwendet. Wenn dies die einzige vom Benutzer zugewiesene verwaltete Identität der VM-Skalierungsgruppe ist, wird `UserAssigned` aus dem Wert des Identitätstyps entfernt.  Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<VIRTUAL MACHINE SCALE SET NAME>` durch Ihre eigenen Werte. `<USER ASSIGNED IDENTITY>` ist die `name`-Eigenschaft der vom Benutzer zugewiesenen verwalteten Identität. Diese Eigenschaft kann mit `az vmss identity show` aus dem Identitätsabschnitt der VM-Skalierungsgruppe abgerufen werden:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Wenn Ihre VM-Skalierungsgruppe keine vom System zugewiesene verwaltete Identität hat und Sie alle vom Benutzer zugewiesenen verwalteten Identitäten entfernen möchten, verwenden Sie den folgenden Befehl:

> [!NOTE]
> Für den Wert `none` müssen Sie die Groß-/Kleinschreibung beachten. Er darf nur aus Kleinbuchstaben bestehen.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Wenn die VM-Skalierungsgruppe sowohl vom System als auch vom Benutzer zugewiesene verwaltete Identitäten aufweist, können Sie alle vom Benutzer zugewiesenen Identitäten entfernen, indem Sie in den Modus wechseln, in dem nur die vom System zugewiesene verwaltete Identität verwendet wird. Verwenden Sie den folgenden Befehl:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](overview.md)
- Die vollständige Schnellstartanleitung für die Erstellung einer Azure-VM-Skalierungsgruppe finden Sie unter [Erstellen einer Skalierungsgruppe](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set).
