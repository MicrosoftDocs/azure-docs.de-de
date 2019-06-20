---
title: include file
description: include file
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178454"
---
| Resource | Standardlimit |
| --- | :--- |
| Maximale Anzahl Cluster pro Abonnement | 100 |
| Maximale Anzahl Knoten pro Cluster | 100 |
| Maximale Anzahl Pods pro Knoten: [Basisnetzwerke][basic-networking] mit Kubenet | 110 |
| Maximale Anzahl Pods pro Knoten: [Erweiterte Netzwerkunterstützung][advanced-networking] mit Azure Container Networking Interface | Bereitstellung mithilfe der Azure CLI: 30<sup>1</sup><br />Azure Resource Manager-Vorlage: 30<sup>1</sup><br />Bereitstellung über das Portal: 30 |

<sup>1</sup>Wenn Sie einen AKS-Cluster (Azure Kubernetes Service) über die Azure-Befehlszeilenschnittstelle oder über eine Resource Manager-Vorlage bereitstellen, kann dieser Wert auf bis zu 250 Pods pro Knoten festgelegt werden. Wenn Sie bereits einen AKS-Cluster bereitgestellt haben oder einen Cluster über das Azure-Portal bereitstellen, kann die maximale Anzahl von Pods pro Knoten nicht konfiguriert werden.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
