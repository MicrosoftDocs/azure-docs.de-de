---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 091e4dc0759ce1b227349d5d1b6b08b9eda66ee0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017343"
---
* Da für die Konvertierung ein Neustart des virtuellen Computers erforderlich ist, sollten Sie die Migration Ihrer virtuellen Computer während eines bereits vorhandenen Wartungsfensters durchführen. 

* Die Konvertierung kann nicht rückgängig gemacht werden. 

* Beachten Sie, dass Benutzer mit der Rolle [Mitwirkender von virtuellen Computern](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) die Größe des virtuellen Computers nicht mehr ändern können (was vor dem Wechsel möglich war). Der Grund dafür ist, dass virtuelle Computer mit verwalteten Datenträgern erfordern, dass der Benutzer über die Berechtigung „Microsoft.Compute/disks/write“ für die Betriebssystemdatenträger verfügt.

* Testen Sie die Konvertierung unbedingt. Migrieren Sie einen virtuellen Testcomputer, bevor Sie die Migration in der Produktion durchführen.

* Bei der Konvertierung heben Sie die Zuordnung der VM auf. Die VM erhält eine neue IP-Adresse, wenn sie nach der Konvertierung gestartet wird. Bei Bedarf können Sie dem virtuellen Computer [eine statische IP-Adresse zuweisen](../articles/virtual-network/public-ip-addresses.md).

* Überprüfen Sie die Mindestversion des Azure-VM-Agents, der zur Unterstützung des Konvertierungsprozesses erforderlich ist. Informationen zum Überprüfen und Aktualisieren der Agent-Version finden Sie unter [Minimum version support for VM agents in Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) (Unterstützte Mindestversion für VM-Agents in Azure).