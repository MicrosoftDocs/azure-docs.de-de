---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: bf26af7fa4b1b31514fb82c5e28a85154b2e274a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158464"
---
* Da für die Konvertierung ein Neustart des virtuellen Computers erforderlich ist, sollten Sie die Migration Ihrer virtuellen Computer während eines bereits vorhandenen Wartungsfensters durchführen. 

* Die Konvertierung kann nicht rückgängig gemacht werden. 

* Beachten Sie, dass Benutzer mit der Rolle [Mitwirkender von virtuellen Computern](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) die Größe des virtuellen Computers nicht mehr ändern können (was vor dem Wechsel möglich war). Der Grund dafür ist, dass virtuelle Computer mit verwalteten Datenträgern erfordern, dass der Benutzer über die Berechtigung „Microsoft.Compute/disks/write“ für die Betriebssystemdatenträger verfügt.

* Testen Sie die Konvertierung unbedingt. Migrieren Sie einen virtuellen Testcomputer, bevor Sie die Migration in der Produktion durchführen.

* Bei der Konvertierung heben Sie die Zuordnung der VM auf. Die VM erhält eine neue IP-Adresse, wenn sie nach der Konvertierung gestartet wird. Bei Bedarf können Sie dem virtuellen Computer [eine statische IP-Adresse zuweisen](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Die ursprünglichen VHDs und das Speicherkonto, die vor der Konvertierung vom virtuellen Computer verwendet wurden, werden nicht gelöscht. Sie verursachen weiterhin Kosten. Um zu vermeiden, dass diese Artefakte in Rechnung gestellt werden, löschen Sie die ursprünglichen VHD-Blobs, nachdem Sie sichergestellt haben, dass die Konvertierung abgeschlossen ist.

* Überprüfen Sie die Mindestversion des Azure-VM-Agents, der zur Unterstützung des Konvertierungsprozesses erforderlich ist. Informationen zum Überprüfen und Aktualisieren der Agent-Version finden Sie unter [Minimum version support for VM agents in Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) (Unterstützte Mindestversion für VM-Agents in Azure).