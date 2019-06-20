---
title: include file
description: include file
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: feec6a695ad867d26d32904d020648b029f9da35
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178181"
---
## <a name="create-a-media-services-account"></a>Erstellen eines Media Services-Kontos

Sie müssen zunächst ein Media Services-Konto erstellen. In diesem Abschnitt wird gezeigt, was Sie für die Kontoerstellung mithilfe der Azure CLI benötigen.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des folgenden Befehls eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Ressourcen wie Azure Media Services-Konten und die zugehörigen Speicherkonten bereitgestellt und verwaltet werden.

Sie können `amsResourceGroup` durch Ihren Wert ersetzen.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Wenn Sie ein Media Services-Konto erstellen, müssen Sie den Namen einer Azure Storage-Kontoressource angeben. Das angegebene Speicherkonto wird an Ihr Media Services-Konto angefügt. Weitere Informationen zur Verwendung von Speicherkonten in Media Services finden Sie unter [Storage accounts](../articles/media-services/latest/storage-account-concept.md) (Storage-Konten).

Sie müssen über ein **primäres** Storage-Konto verfügen. Darüber hinaus können Sie beliebig viele **sekundäre** Storage-Konten an Ihr Media Services-Konto anfügen. Media Services unterstützt Konten vom Typ **Allgemein v2** (GPv2) oder **Allgemein v1** (GPv1). Reine Blobkonten sind als **primäre** Konten nicht zulässig. Weitere Informationen zu Storage-Konten finden Sie unter [Optionen für Azure Storage-Konten](../articles/storage/common/storage-account-options.md). 

In diesem Beispiel erstellen Sie ein universelles LRS-Standardkonto der Version 2. Falls Sie mit Speicherkonten experimentieren möchten, verwenden Sie `--sku Standard_LRS`. Wenn Sie eine SKU für die Produktion auswählen, sollten Sie jedoch die Verwendung von `--sku Standard_RAGRS` erwägen, da diese Option geografische Replikation zum Gewährleisten der Geschäftskontinuität bietet. Weitere Informationen finden Sie unter [Storage accounts](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest) (Speicherkonten).
 
Der folgende Befehl erstellt ein Storage-Konto, das dem Media Services-Konto zugeordnet werden soll. Im folgenden Skript können Sie `storageaccountforams` durch Ihren Wert ersetzen. `amsResourceGroup` muss dem Wert entsprechen, den Sie für die Ressourcengruppe im vorherigen Schritt angegeben haben. Der Speicherkontoname muss kürzer als 24 Zeichen sein.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Erstellen eines Media Services-Kontos

Der folgende Azure CLI-Befehl erstellt ein neues Media Services-Konto. Sie können die folgenden Werte ersetzen: `amsaccount`, `storageaccountforams` (muss dem Wert für Ihr Speicherkonto entsprechen) und `amsResourceGroup` (muss dem Wert für Ihre Ressourcengruppe entsprechen).

```azurecli
az ams account create --name amsaccount \
  -l westus2 \
  -g amsResourceGroup --storage-account storageaccountforams
```
