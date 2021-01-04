---
title: 'Erstellen einer DNS-Zone und eines zugehörigen Eintrags für einen Domänennamen: Azure CLI – Azure DNS'
description: Dieses Azure CLI-Skriptbeispiel zeigt das Erstellen einer DNS-Zone und eines Datensatzes für einen Domänennamen.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 348b7911930711a25c88595b6360341ef6e00468
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954425"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Azure CLI-Skriptbeispiel: Erstellen einer DNS-Zone und eines Datensatzes

Dieses Azure CLI-Skriptbeispiel erstellt eine DNS-Zone und einen Datensatz für einen Domänennamen. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, die DNS-Zone und alle zugehörigen Ressourcen zu entfernen.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, einen virtuellen Computer, eine Verfügbarkeitsgruppe, einen Load Balancer und alle zugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Erstellt eine Azure DNS-Zone. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Fügt einer DNS-Zone einen *A*-Datensatz hinzu. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Auflisten aller *A*-Datensätze in einer DNS-Zone. |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).