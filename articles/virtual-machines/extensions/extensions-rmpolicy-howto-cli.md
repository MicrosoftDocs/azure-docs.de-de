---
title: Verwenden der Azure Policy, um die Installation der VM-Erweiterung einzuschränken (Linux)
description: Verwenden Sie Azure Policy, um VM-Erweiterungsbereitstellungen einzuschränken.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 26874c33c496b57bf6317a7b837b3afddc2f5e37
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955649"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Verwenden von Azure Policy, um die Installation von Erweiterungen auf virtuellen Linux-Computern einzuschränken

Wenn Sie die Verwendung oder Installation bestimmter Erweiterungen auf Ihren Linux-VMs verhindern möchten, können Sie mithilfe der Befehlszeilenschnittstelle eine Azure Policy-Definition zum Einschränken von Erweiterungen für VMs in einer Ressourcengruppe erstellen. 

Dieses Tutorial verwendet die CLI innerhalb der Azure Cloud Shell, die ständig auf die neueste Version aktualisiert wird. Wenn Sie die Azure CLI lokal ausführen möchten, müssen Sie Version 2.0.26 oder höher installieren. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Erstellen einer Regeldatei

Um einzuschränken, welche Erweiterungen installiert werden können, benötigen Sie eine [Regel](../../governance/policy/concepts/definition-structure.md#policy-rule), um die Logik zum Identifizieren der Erweiterung bereitzustellen.

Dieses Beispiel zeigt Ihnen, wie Sie die Installation von Erweiterungen ablehnen, die von „Microsoft.OSTCExtensions“ veröffentlicht wurden, indem Sie eine Regeldatei in Azure Cloud Shell erstellen, aber wenn Sie lokal in der CLI arbeiten, können Sie auch eine lokale Datei erstellen und den Pfad (~/clouddrive) durch den Pfad zu der lokalen Datei auf Ihrem Computer ersetzen.

Geben Sie in einer [Bash-Cloud Shell](https://shell.azure.com/bash) ein:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn in die Datei ein.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Wenn Sie fertig sind, drücken Sie die **Esc** Taste, und geben Sie dann **:wq** ein, um die Datei zu speichern und zu schließen.


## <a name="create-a-parameters-file"></a>Erstellen einer Parameterdatei

Sie benötigen auch eine [Parameterdatei](../../governance/policy/concepts/definition-structure.md#parameters), die für Sie eine Struktur für die Übergabe einer Liste der zu blockierenden Erweiterungen erstellt. 

Dieses Beispiel zeigt Ihnen, wie Sie eine Parameterdatei für Linux-VMs in Cloud Shell erstellen, aber wenn Sie lokal in der CLI arbeiten, können Sie auch eine lokale Datei erstellen und den Pfad (~/clouddrive) durch den Pfad zu der lokalen Datei auf Ihrem Computer ersetzen.

Geben Sie in der [Bash-Cloud Shell](https://shell.azure.com/bash) ein:

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn in die Datei ein.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "displayName": "Denied extension"
        }
    }
}
```

Wenn Sie fertig sind, drücken Sie die **Esc** Taste, und geben Sie dann **:wq** ein, um die Datei zu speichern und zu schließen.

## <a name="create-the-policy"></a>Erstellen der Richtlinie

Eine Richtliniendefinition ist ein Objekt, mit dem die Konfiguration gespeichert wird, die Sie verwenden möchten. Die Richtliniendefinition verwendet die Regeln und Parameterdateien zum Definieren der Richtlinie. Erstellen Sie die Richtliniendefinition mit [az policy definition create](/cli/azure/role/assignment?view=azure-cli-latest).

In diesem Beispiel sind die Regeln und Parameter die Dateien, die Sie erstellt und als JSON-Dateien in Ihrer Cloud Shell gespeichert haben.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Zuweisen der Richtlinie

In diesem Beispiel wird die Richtlinie mit [az policy assignment create](/cli/azure/policy/assignment) einer Ressourcengruppe zugewiesen. Alle in der Ressourcengruppe **myResourceGroup** erstellten virtuellen Computer werden nicht in der Lage sein, die Linux VM Access- oder Custom Script-Erweiterungen für Linux zu installieren. Die Ressourcengruppe muss vorhanden sein, damit Sie die Richtlinie zuweisen können.

Rufen Sie mit [az account list](/cli/azure/account?view=azure-cli-latest) Ihre Abonnement-ID ab, die Sie anstelle der im Beispiel verwendeten nutzen.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Testen der Richtlinie

Testen Sie die Richtlinie, indem Sie einen neuen virtuellen Computer erstellen und versuchen, einen neuen Benutzer hinzuzufügen.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Versuchen Sie, einen neuen Benutzer mit dem Namen **myNewUser** mit der VM Access-Erweiterung zu erstellen.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Entfernen der Zuweisung

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Entfernen der Richtlinie

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Was ist Azure Policy?](../../governance/policy/overview.md).
