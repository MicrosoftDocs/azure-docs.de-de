---
title: include file
description: include file
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b6587a3928c2ddf0d00c90e07643525314690e42
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178228"
---
### <a name="virtual-machines"></a>Virtual Machines

|  |  |
|---------|---------|
| [Zulassen eines benutzerdefinierten VM-Images aus einer Ressourcengruppe](../articles/governance/policy/samples/allow-custom-vm-image.md) |  Verlangt, dass benutzerdefinierte Images aus einer genehmigten Ressourcengruppe stammen. Sie geben den Namen der genehmigten Ressourcengruppe an. |
| [Zulässige SKUs für Speicherkonten und virtuelle Computer](../articles/governance/policy/samples/allowed-skus-storage.md) | Erfordert, dass Speicherkonten und virtuelle Computer genehmigte SKUs verwenden. Verwendet integrierte Richtlinien, um die Verwendung von genehmigten SKUs zu gewährleisten. Sie geben ein Array von genehmigten SKUs für virtuelle Computer und ein Array von genehmigten SKUs für Speicherkonten an. |
| [Genehmigte VM-Images](../articles/governance/policy/samples/allowed-custom-images.md) | Verlangt, dass nur genehmigte benutzerdefinierte Images in Ihrer Umgebung bereitgestellt werden. Sie geben ein Array mit genehmigten Image-IDs an. |
| [Überwachen, ob keine Erweiterung vorhanden ist](../articles/governance/policy/samples/audit-extension-not-exist.md) | Überwacht, ob eine Erweiterung nicht mit einem virtuellen Computer bereitgestellt wird. Sie geben den Herausgeber und den Typ der Erweiterung an, um zu überprüfen, ob diese bereitgestellt wurde. |
| [Nicht zulässige VM-Erweiterungen](../articles/governance/policy/samples/not-allowed-vm-extension.md) | Untersagt die Verwendung bestimmter Erweiterungen. Sie geben ein Array an, das die unzulässigen Erweiterungstypen enthält. |