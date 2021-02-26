---
title: Hinzufügen von Regeln für Azure Load Balancer Standard und VM-Skalierungsgruppen
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: In diesem Lernpfad beginnen Sie mit Azure Load Balancer Standard und VM-Skalierungsgruppen.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592278"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Hinzufügen von Regeln für Azure Load Balancer mit VM-Skalierungsgruppen

Berücksichtigen Sie bei der Arbeit mit VM-Skalierungsgruppen und Azure Load Balancer die folgenden Richtlinien.

## <a name="port-forwarding-and-inbound-nat-rules"></a>Portweiterleitung und NAT-Regeln für eingehenden Datenverkehr

Nachdem die Skalierungsgruppe erstellt wurde, kann der Back-End-Port für eine Lastenausgleichsregel, die von einem Integritätstest der Load Balancer-Instanz verwendet wird, nicht mehr geändert werden. Entfernen Sie zum Ändern des Ports den Integritätstest, indem Sie die VM-Skalierungsgruppe und den Port aktualisieren. Konfigurieren Sie dann den Integritätstest noch mal.

Wenn Sie die VM-Skalierungsgruppe im Back-End-Pool der Load Balancer-Instanz verwenden, werden die NAT-Standardregel für eingehenden Datenverkehr automatisch erstellt.
  
## <a name="inbound-nat-pool"></a>NAT-Pool für eingehenden Datenverkehr

Jede VM-Skalierungsgruppe muss mindestens über einen NAT-Pool für eingehenden Datenverkehr verfügen. Bei einem NAT-Pool für eingehenden Datenverkehr handelt es sich um eine Sammlung von NAT-Regeln für eingehenden Datenverkehr. Ein NAT-Pool für eingehenden Datenverkehr kann nicht mehrere VM-Skalierungsgruppen unterstützen.

## <a name="load-balancing-rules"></a>Lastenausgleichsregeln

Wenn Sie die VM-Skalierungsgruppe im Back-End-Pool der Load Balancer-Instanz verwenden, wird die Standardregel für den Lastenausgleich für eingehenden Datenverkehr automatisch erstellt.
  
## <a name="outbound-rules"></a>Ausgangsregeln

Klicken Sie beim Erstellen der Lastenausgleichsregel für eingehenden Datenverkehr im Azure-Portal unter **Implizite Ausgangsregeln erstellen** auf **Nein**, um eine Ausgangsregel für einen Back-End-Pool zu erstellen, auf den bereits durch eine Lastenausgleichsregel verwiesen wird.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Screenshot: Erstellen der Lastenausgleichsregel" border="true":::

Verwenden Sie die folgenden Methoden, um eine VM-Skalierungsgruppe mit einer vorhandenen Load Balancer-Instanz bereitzustellen:

* [Konfigurieren einer VM-Skalierungsgruppe mit einer vorhandenen Azure Load Balancer-Instanz mithilfe des Azure-Portals](./configure-vm-scale-set-portal.md)
* [Konfigurieren einer VM-Skalierungsgruppe mit einer vorhandenen Azure Load Balancer-Instanz mithilfe von Azure PowerShell](./configure-vm-scale-set-powershell.md)
* [Konfigurieren einer VM-Skalierungsgruppe mit einer vorhandenen Azure Load Balancer-Instanz mithilfe der Azure CLI](./configure-vm-scale-set-cli.md)
* [Aktualisieren oder Löschen von Azure Load Balancer, der von einer VM-Skalierungsgruppe verwendet wird](./update-load-balancer-with-vm-scale-set.md)
