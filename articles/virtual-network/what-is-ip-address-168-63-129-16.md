---
title: Was ist die IP-Adresse 168.63.129.16? | Microsoft-Dokumentation
description: Erfahren Sie mehr über die IP-Adresse 168.63.129.16, insbesondere dass sie verwendet wird, um einen Kommunikationskanal zu den Ressourcen der Azure-Plattform zu ermöglichen.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 03c1badf984fb150631c157f3fdc07856b60e965
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088896"
---
# <a name="what-is-ip-address-1686312916"></a>Was ist die IP-Adresse 168.63.129.16?

Die IP-Adresse 168.63.129.16 ist eine virtuelle öffentliche IP-Adresse, die verwendet wird, um einen Kommunikationskanal zu den Ressourcen der Azure-Plattform zu ermöglichen. Kunden können in Azure einen beliebigen Adressraum für ihr privates virtuelles Netzwerk definieren. Daher müssen die Ressourcen der Azure-Plattform als eindeutige öffentliche IP-Adresse dargestellt werden. Diese virtuelle öffentliche IP-Adresse erleichtert die folgenden Vorgänge:

- Ermöglicht die Kommunikation zwischen VM-Agent und Azure-Plattform, um zu signalisieren, dass er sich im Zustand „Bereit“ befindet.
- Ermöglicht die Kommunikation mit dem virtuellen DNS-Server, um für die Ressource (wie einen VM) eine gefilterte Namensauflösung bereitzustellen, die keinen benutzerdefinierten DNS-Server hat. Dieser Filter stellt sicher, dass Kunden nur die Hostnamen ihrer Ressourcen auflösen können.
- Ermöglicht [Integritätstests des Azure Load Balancers](../load-balancer/load-balancer-custom-probe-overview.md), um den Integritätszustand der VMs zu ermitteln.
- Ermöglichen des Abrufs einer dynamischen IP-Adresse vom DHCP-Dienst in Azure durch die VM.
- Ermöglicht Taktmeldungen für einen Gast-Agent für die PaaS-Rolle.

> [!NOTE]
> In einem Szenario ohne virtuelles Netzwerk (klassisch) wird anstelle von 168.63.129.16 eine private IP-Adresse verwendet. Diese private IP-Adresse wird dynamisch per DHCP ermittelt. Speziell für 168.63.129.16 festgelegte Firewallregeln müssen entsprechend angepasst werden.

## <a name="scope-of-ip-address-1686312916"></a>Bereich der IP-Adresse 168.63.129.16

Die öffentliche IP-Adresse 168.63.129.16 wird in allen Regionen und allen nationalen Clouds verwendet. Diese spezielle öffentliche IP-Adresse ist Eigentum von Microsoft und wird nicht geändert. Wir empfehlen Ihnen, diese IP-Adresse in lokalen Firewallrichtlinien (auf der VM, in ausgehender Richtung) zuzulassen. Die Kommunikation zwischen dieser speziellen IP-Adresse und den Ressourcen ist sicher, da nur die interne Azure-Plattform eine Nachricht von dieser IP-Adresse beziehen kann. Wenn diese Adresse gesperrt ist, kann es in verschiedenen Szenarien zu unerwartetem Verhalten kommen. 168.63.129.16 ist eine [virtuelle IP des Hostknotens](../virtual-network/security-overview.md#azure-platform-considerations) und unterliegt daher keinen benutzerdefinierten Routen.

- Für den VM-Agent ist ausgehende Kommunikation über die Ports 80/tcp und 32526/tcp mit dem WireServer (168.63.129.16) erforderlich. Diese Ports müssen in der lokalen Firewall auf dem virtuellen Computer geöffnet sein. Die Kommunikation mit 168.63.129.16 über diese Ports unterliegt nicht den konfigurierten Netzwerksicherheitsgruppen.

- 168.63.129.16 kann DNS-Dienste für die VM bereitstellen. Falls dies nicht erwünscht ist, können für den ausgehenden Datenverkehr an 168.63.129.16 die Ports 53/udp und 53/tcp in der lokalen Firewall auf der VM blockiert werden.

  Standardmäßig unterliegt die DNS-Kommunikation nicht den konfigurierten Netzwerksicherheitsgruppen, sofern dies nicht speziell eingerichtet und das Diensttag [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) genutzt wird. Um DNS-Datenverkehr an Azure DNS über NSG zu blockieren, erstellen Sie eine Ausgangsregel, um Datenverkehr an [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) zu verweigern, und geben Sie „*“ als „Zielportbereiche“ und „Beliebig“ als Protokoll an.

- Wenn die VM Teil eines Back-End-Pools für den Lastenausgleich ist, sollte für die [Integritätstest](../load-balancer/load-balancer-custom-probe-overview.md)-Kommunikation zugelassen werden, dass sie von „168.63.129.16“ ausgeht. Die Standardkonfiguration der Netzwerksicherheitsgruppen enthält eine Regel, die diese Kommunikation zulässt. Für diese Regel wird das Diensttag [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) genutzt. Dieser Datenverkehr kann bei Bedarf blockiert werden, indem die Netzwerksicherheitsgruppe konfiguriert wird, aber dies führt zum Auftreten von Fehlern für Tests.

## <a name="next-steps"></a>Nächste Schritte

- [Sicherheitsgruppen](security-overview.md)
- [Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe](manage-network-security-group.md)
