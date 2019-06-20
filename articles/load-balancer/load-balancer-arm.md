---
title: Azure Resource Manager-Unterstützung für Load Balancer | Microsoft Docs
description: Verwenden von PowerShell für Load Balancer mit Azure Resource Manager. Verwenden von Vorlagen für Lastenausgleich
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 596ac871067886ee3124c0f21beb35cb3b8fe1ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60888979"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Verwenden der Azure Resource Manager-Unterstützung mit Azure Load Balancer



Azure Resource Manager ist das bevorzugte Verwaltungsframework für Dienste in Azure. Azure Load Balancer kann mithilfe von APIs und Tools auf Basis von Azure Resource Manager verwaltet werden.

## <a name="concepts"></a>Konzepte

Mit Resource Manager enthält Azure Load Balancer die folgenden untergeordneten Ressourcen:

* Front-End-IP-Konfiguration: ein Lastenausgleich kann ein oder mehrere Front-End-IP-Adressen umfassen, die auch als virtuelle IPs (VIPs) bezeichnet werden. Diese IP-Adressen dienen als Eingang für den Datenverkehr.
* Back-End-Adresspool: IP-Adressen, die der Netzwerkschnittstellenkarte des virtuellen Computers zugeordnet ist, an die die Last verteilt wird.
* Lastenausgleichsregeln: Eine Regeleigenschaft ordnet eine bestimmte IP-/Port-Kombination des Front-Ends einer IP-Adressen-/Port-Kombination des Back-Ends zu. Ein einzelnes Lastenausgleichsmodul kann mehrere Lastenausgleichsregeln umfassen. Jede Regel ist eine Kombination aus der Front-End-IP und dem Front-End-Port sowie der Back-End-IP und dem Back-End-Port von virtuellen Computern.
* Überprüfungen – Mit Überprüfungen können Sie die Integrität der VM-Instanzen nachverfolgen. Schlägt eine Integritätsüberprüfung fehl, wird die VM-Instanz automatisch aus der Rotation entfernt.
* NAT-Regeln für eingehenden Datenverkehr: NAT-Regeln definieren den Eingangsdatenverkehr, der durch die Front-End-IP fließt und an die Back-End-IP verteilt wird.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Schnellstartvorlagen

Mit dem Azure-Ressourcen-Manager können Sie Ihre Anwendungen mit einer deklarativen Vorlage bereitstellen. In einer Vorlage können Sie mehrere Dienste zusammen mit ihren Abhängigkeiten bereitstellen. Sie verwenden die gleiche Vorlage, um Ihre Anwendung während jeder Phase des Anwendungslebenszyklus wiederholt bereitzustellen.

Vorlagen können Definitionen für virtuelle Computer, virtuelle Netzwerke, Verfügbarkeitsgruppen, Netzwerkkarten, Speicherkonten, Lastenausgleichsmodule, Netzwerksicherheitsgruppen und öffentliche IP-Adressen umfassen. Mit Vorlagen können Sie alle Elemente erstellen, die Sie für eine komplexe Anwendung benötigen. Die Vorlagendatei kann zur Versionskontrolle und Zusammenarbeit in ein Content Management-System eingecheckt werden.

[Weitere Informationen zu Vorlagen](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Weitere Informationen zu Netzwerkressourcen](../networking/networking-overview.md)

Schnellstartvorlagen, die Azure Load Balancer verwenden, finden Sie in diesem [GitHub-Repository](https://github.com/Azure/azure-quickstart-templates), das eine Reihe von in der Community erstellten Vorlagen enthält.

Beispiele für Vorlagen:

* [2 VMs in a Load Balancer and load balancing rules (in englischer Sprache)](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs in a VNET with an Internal Load Balancer and Load Balancer rules (in englischer Sprache)](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs in a Load Balancer and configure NAT rules on the LB (in englischer Sprache)](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Einrichten von Azure-Lastenausgleich per PowerShell oder CLI

Erste Schritte mit Cmdlets, Befehlszeilentools und REST-APIs von Azure Resource Manager

* [Azure Networking Cmdlets](https://docs.microsoft.com/powershell/module/az.network#networking) können verwendet werden, um einen Lastenausgleich zu erstellen.
* [Erstellen eines Load Balancers mit dem Azure-Ressourcen-Manager](load-balancer-get-started-ilb-arm-ps.md)
* [Verwenden der Azure-CLI mit der Azure-Ressourcenverwaltung](../xplat-cli-azure-resource-manager.md)
* [Load Balancer REST APIs (in englischer Sprache)](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Nächste Schritte

Sie können auch einen [Lastenausgleich für Internetzugriff erstellen](load-balancer-get-started-internet-arm-ps.md) und die Art des [Verteilungsmodus](load-balancer-distribution-mode.md) des Lastenausgleichs für ein bestimmtes Datenverkehrsverhalten im Netzwerk konfigurieren.

Erfahren Sie, wie Sie [TCP-Leerlauftimeout-Einstellungen für ein Lastenausgleichsmodul](load-balancer-tcp-idle-timeout.md)verwalten. Dies ist wichtig, wenn Ihre Anwendung Verbindungen für Server hinter einem Lastenausgleichsmodul beibehalten muss.
