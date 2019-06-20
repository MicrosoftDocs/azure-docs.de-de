---
title: Verwalten von Fehlerdomänen in Azure-VM-Skalierungsgruppen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie beim Erstellen einer VM-Skalierungsgruppe die richtige Anzahl von Fehlerdomänen (FDs) auswählen.
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: drewm
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: rajraj
ms.openlocfilehash: bab264769576b6e5478236c452d7de920d887c1a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617978"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Auswählen der richtigen Anzahl von Fehlerdomänen für VM-Skalierungsgruppen
VM-Skalierungsgruppen werden in Azure-Regionen ohne Zonen standardmäßig mit fünf Fehlerdomänen erstellt. Für die Regionen, die Zonenbereitstellung von VM-Skalierungsgruppen unterstützen, ist der Standardwert der Anzahl von Fehlerdomänen eine für jede Zone. FD = 1 bedeutet in diesem Fall, dass die zur Skalierungsgruppe gehörenden VM-Instanzen auf bestmögliche Weise über viele Racks verteilt werden.

Sie können auch die Anzahl der Fehlerdomänen für die Skalierungsgruppe an der Anzahl von Fehlerdomänen für verwaltete Datenträger ausrichten. Diese Ausrichtung kann dabei helfen, Quorumverluste zu verhindern, wenn eine gesamte Fehlerdomäne für verwaltete Datenträger ausfällt. Die FD-Anzahl kann auf die Anzahl der in allen Regionen verfügbaren Fehlerdomänen für verwaltete Datenträger oder eine kleinere Zahl festgelegt werden. In [diesem Dokument](../virtual-machines/windows/manage-availability.md) erfahren Sie mehr über die Anzahl von Fehlerdomänen für verwaltete Datenträger nach Region.

## <a name="rest-api"></a>REST-API
Sie können die Eigenschaft `properties.platformFaultDomainCount` auf 1, 2 oder 3 festlegen (der Standardwert bei fehlender Angabe ist 5). Weitere Informationen finden Sie in der REST-API-Dokumentation [hier](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Sie können den Parameter `--platform-fault-domain-count` auf 1, 2 oder 3 festlegen (der Standardwert bei fehlender Angabe ist 5). Weitere Informationen finden Sie in der [Dokumentation der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Verfügbarkeits-und Redundanzfeatures](../virtual-machines/windows/regions-and-availability.md) für Azure-Umgebungen.
