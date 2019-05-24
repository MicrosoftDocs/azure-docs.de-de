---
title: Includedatei
description: Includedatei
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: fe6c6d461fd6b99ce2ce57ebdd61a3f3e961489c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155379"
---
## <a name="storage"></a>Storage

|  |  |
|---------|---------|
| [Zulässige SKUs für Speicherkonten und virtuelle Computer](../articles/governance/policy/samples/allowed-skus-storage.md) | Erfordert, dass Speicherkonten und virtuelle Computer genehmigte SKUs verwenden. Verwendet integrierte Richtlinien, um die Verwendung von genehmigten SKUs zu gewährleisten. Sie geben ein Array von genehmigten SKUs für virtuelle Computer und ein Array von genehmigten SKUs für Speicherkonten an. |
| [Zulässige Speicherkonten-SKUs](../articles/governance/policy/samples/allowed-storage-account-skus.md) | Erfordert, dass Speicherkonten eine genehmigte SKU verwenden. Sie geben ein Array von zulässigen SKUs an. |
| [Verweigern des kalten Zugriffstierings für Speicherkonten](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Untersagt die Verwendung des kalten Zugriffstierings für Blob-Speicherkonten.  |
| [Gewährleisten eines HTTPS-Datenverkehrs ausschließlich für Speicherkonten](../articles/governance/policy/samples/ensure-https-storage-account.md) | Erfordert, dass Speicherkonten HTTPS-Datenverkehr verwenden.  |
| [Gewährleisten der Verschlüsselung von Speicherdateien](../articles/governance/policy/samples/ensure-storage-file-encryption.md) | Erfordert, dass die Dateiverschlüsselung für Speicherkonten aktiviert ist.  |
| [Verlangen der Speicherkontenverschlüsselung](../articles/governance/policy/samples/require-storage-account-encryption.md) | Erfordert das Verwenden der Blobverschlüsselung für das Speicherkonto.  |