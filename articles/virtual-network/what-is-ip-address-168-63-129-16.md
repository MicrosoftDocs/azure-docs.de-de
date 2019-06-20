---
title: Was ist die IP-Adresse 168.63.129.16? | Microsoft-Dokumentation
description: Erfahren Sie mehr zur IP-Adresse 168.63.129.16 und deren Funktionsweise mit Ihren Ressourcen.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: acfd5230d1bd572ea5179651558e3f736a8570af
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833123"
---
# <a name="what-is-ip-address-1686312916"></a>Was ist die IP-Adresse 168.63.129.16?

Die IP-Adresse 168.63.129.16 ist eine virtuelle öffentliche IP-Adresse, die verwendet wird, um einen Kommunikationskanal zu den Ressourcen der Azure-Plattform zu ermöglichen. Kunden können in Azure einen beliebigen Adressraum für ihr privates virtuelles Netzwerk definieren. Daher müssen die Ressourcen der Azure-Plattform als eindeutige öffentliche IP-Adresse dargestellt werden. Diese virtuelle öffentliche IP-Adresse erleichtert die folgenden Vorgänge:

- Ermöglicht die Kommunikation zwischen VM-Agent und Azure-Plattform, um zu signalisieren, dass er sich im Zustand „Bereit“ befindet.
- Ermöglicht die Kommunikation mit dem virtuellen DNS-Server, um für die Ressource (wie einen VM) eine gefilterte Namensauflösung bereitzustellen, die keinen benutzerdefinierten DNS-Server hat. Dieser Filter stellt sicher, dass Kunden nur die Hostnamen ihrer Ressourcen auflösen können.
- Ermöglicht [Integritätstests des Azure Load Balancers](../load-balancer/load-balancer-custom-probe-overview.md), um den Integritätszustand der VMs zu ermitteln.
- Ermöglichen des Abrufs einer dynamischen IP-Adresse vom DHCP-Dienst in Azure durch die VM.
- Ermöglicht Taktmeldungen für einen Gast-Agent für die PaaS-Rolle.

## <a name="scope-of-ip-address-1686312916"></a>Bereich der IP-Adresse 168.63.129.16

Die öffentliche IP-Adresse 168.63.129.16 wird in allen Regionen und allen nationalen Clouds verwendet. Diese spezielle öffentliche IP-Adresse ist Eigentum von Microsoft und wird nicht geändert. Sie ist gemäß der Standard-Netzwerksicherheitsgruppe zulässig. Es wird empfohlen, dass Sie diese IP-Adresse in lokalen Firewallrichtlinien zulassen. Die Kommunikation zwischen dieser speziellen IP-Adresse und den Ressourcen ist sicher, da nur die interne Azure-Plattform eine Nachricht von dieser IP-Adresse beziehen kann. Wenn diese Adresse gesperrt ist, kann es in verschiedenen Szenarien zu unerwartetem Verhalten kommen.

[Azure Load Balancer-Integritätstests](../load-balancer/load-balancer-custom-probe-overview.md) gehen von dieser IP-Adresse aus. Wenn Sie diese IP-Adresse blockieren, treten bei Ihren Tests Fehler auf.

In einem nicht virtuellen Netzwerkszenario (klassisch) wird der Integritätstest von einer privaten IP bezogen und 168.63.129.16 nicht verwendet.

## <a name="next-steps"></a>Nächste Schritte

- [Sicherheitsgruppen](security-overview.md)
- [Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe](manage-network-security-group.md)
