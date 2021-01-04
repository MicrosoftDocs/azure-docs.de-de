---
title: include file
description: include file
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 68ce927c05f7179cca0aa2833460b9550f0a82d2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028219"
---
> [!div class="op_single_selector"]
> * [Azure portal](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [Azure-Befehlszeilenschnittstelle](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
>

Ein virtueller Azure-Computer (VM) verfügt über mindestens eine angefügte Netzwerkschnittstelle (NIC). Jede NIC kann eine oder mehrere zugewiesene statische und dynamische öffentliche oder private IP-Adressen besitzen. Durch das Zuweisen von mehreren IP-Adressen zu einem virtuellen Computer bestehen folgende Möglichkeiten:

* Hosten mehrerer Websites oder Dienste mit unterschiedlichen IP-Adressen und SSL-Zertifikaten auf einem einzelnen Server
* Übernehmen der Rolle als virtuelles Netzwerkgerät, z.B. als Firewall oder Load Balancer
* Fähigkeit zum Hinzufügen einer privaten IP-Adresse für eine der NICs zu einem Azure Load Balancer-Back-End-Pool hinzufügen. Bisher konnte nur die primäre IP-Adresse für die primäre NIC einem Back-End-Pool hinzugefügt werden. Weitere Informationen dazu, wie in Konfigurationen mit mehreren IPs ein Lastenausgleich durchgeführt werden kann, finden Sie im Artikel [Load balancing multiple IP configurations](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Lastenausgleich bei Konfigurationen mit mehreren IPs).

Jeder NIC, die an einen virtuellen Computer angefügt ist, ist mindestens eine IP-Konfiguration zugeordnet. Jeder Konfiguration wird eine statische oder dynamische private IP-Adresse zugewiesen. Jeder Konfiguration kann auch eine öffentliche IP-Adressressource zugeordnet sein. Einer öffentlichen IP-Adressressource ist entweder eine dynamische oder eine statische IP-Adresse zugewiesen. Weitere Informationen zu IP-Adressen in Azure finden Sie im Artikel [IP-Adressen in Azure](../articles/virtual-network/public-ip-addresses.md). 

Es gilt ein Grenzwert dafür, wie viele private IP-Adressen einer NIC zugewiesen werden können. Außerdem ist begrenzt, wie viele öffentliche IP-Adressen in einem Azure-Abonnement verwendet werden können. Weitere Informationen finden Sie im Artikel [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../articles/azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).