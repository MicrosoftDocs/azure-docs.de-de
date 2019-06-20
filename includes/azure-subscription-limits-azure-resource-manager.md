---
title: include file
description: include file
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 04/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 712b70960e09a9c2b0e7a998bc0bddbc28c1e112
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178450"
---
| Resource | Standardlimit | Maximales Limit |
| --- | --- | --- |
| VMs pro [Abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> pro Region |25.000 pro Region |
| Gesamte Kerne pro [Abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> pro Region | Wenden Sie sich an den Support. |
| VM-Kerne pro Serie (z. B. Dv2 und F) pro [Abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> pro Region | Wenden Sie sich an den Support. |
| [Co-Administratoren](../articles/billing-add-change-azure-subscription-administrator.md) pro Abonnement |Unbegrenzt. |Unbegrenzt. |
| [Speicherkonten](../articles/storage/common/storage-quickstart-create-account.md) pro Region und Abonnement |250 |250 |
| [Ressourcengruppen](../articles/azure-resource-manager/resource-group-overview.md) pro Abonnement |980 |980 |
| [Verfügbarkeitsgruppen](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) pro Abonnement |2\.000 pro Region |2\.000 pro Region |
| Anforderungsgröße für Azure Resource Manager-API |4\.194.304 Byte |4\.194.304 Byte |
| Tags pro Abonnement<sup>2</sup> |Unbegrenzt. |Unbegrenzt. |
| Eindeutige Tag-Berechnungen pro Abonnement<sup>2</sup> | 10.000 | 10.000 |
| [Clouddienste](../articles/cloud-services/cloud-services-choose-me.md) pro Abonnement |Nicht zutreffend<sup>3</sup> |Nicht zutreffend<sup>3</sup> |
| [Affinitätsgruppen](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) pro Abonnement |Nicht zutreffend<sup>3</sup> |Nicht zutreffend<sup>3</sup> |
| [Bereitstellungen auf Abonnementebene](../articles/azure-resource-manager/deploy-to-subscription.md) pro Standort | 800<sup>4</sup> | 800 |

<sup>1</sup> Standardgrenzwerte variieren nach angebotenem Kategorietyp, z. B. kostenlose Testversion, nutzungsbasierte Bezahlung, und nach Serie, wie z. B. Dv2, F und G.

<sup>2</sup>Sie können eine unbegrenzte Anzahl von Tags pro Abonnement anwenden. Die Anzahl von Tags pro Ressource oder Ressourcengruppe ist auf 15 beschränkt. Resource Manager gibt nur eine [Liste mit eindeutigen Tagnamen und Werten](/rest/api/resources/tags) im Abonnement zurück, wenn die Anzahl der Tags 10.000 oder weniger beträgt. Sie können eine Ressource jedoch auch anhand des Tags finden, wenn die Anzahl 10.000 überschreitet.  

<sup>3</sup>Diese Features sind für Azure-Ressourcengruppen und Resource Manager nicht mehr erforderlich.

<sup>4</sup>Wenn der Grenzwert von 800 Bereitstellungen erreicht ist, löschen Sie nicht mehr benötigte Bereitstellungen aus dem Verlauf. Um Bereitstellungen auf Abonnementebene zu löschen, verwenden Sie [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) oder [az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Für VM-Kerne gilt ein regionaler Gesamtgrenzwert. Außerdem gilt ein Grenzwert für die regionaler Serien pro Größe wie Dv2 und F. Diese Grenzwerte werden separat erzwungen. Angenommen, Sie verwenden ein Abonnement mit einem Kerngesamtgrenzwert von 30 für VMs in der Region „USA, Osten“, einem Kerngrenzwert von 30 für die A-Serie und einem Kerngrenzwert von 30 für die D-Serie. Für dieses Abonnement dürfen dann 30 virtuelle A1-Computer bzw. 30 virtuelle D1-Computer bereitgestellt werden – oder eine Kombination daraus, bei der die Gesamtanzahl von 30 Kernen nicht überschritten wird. Ein Beispiel wäre eine Kombination aus 10 A1-VMs und 20 D1-VMs.  
> <!-- -->
> 
> 

