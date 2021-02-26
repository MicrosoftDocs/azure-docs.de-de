---
title: Was ist die Subnetzdelegierung in Azure Virtual Network?
description: Informationen zur Subnetzdelegierung in Azure Virtual Network
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2020
ms.author: kumud
ms.openlocfilehash: 2801aa6b2e2b72df815b170200587c49ec0bae14
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538991"
---
# <a name="what-is-subnet-delegation"></a>Was ist die Subnetzdelegierung?

Mithilfe der Subnetzdelegierung können Sie ein bestimmtes Subnetz für einen Azure-PaaS-Dienst festlegen, der in Ihr virtuelles Netzwerk eingefügt werden muss. Die Subnetzdelegierung bietet dem Kunden volle Kontrolle über die Verwaltung der Integration von Azure-Diensten in ihre virtuellen Netzwerke.

Wenn Sie ein Subnetz an einen Azure-Dienst delegieren, können Sie mit diesem Dienst einige grundlegende Netzwerkkonfigurationsregeln für dieses Subnetz einrichten. So kann der Azure-Dienst die Instanzen stabil betreiben. Dadurch kann der Azure-Dienst einige Bedingungen festlegen, für die der Zeitraum vor oder nach der Bereitstellung gelten:
- Bereitstellen des Diensts in einem freigegebenen oder dedizierten Subnetz
- Hinzufügen einiger Netzwerkzielrichtlinien zum Dienst (nach der Bereitstellung), die für die ordnungsgemäße Funktionsweise des Diensts erforderlich sind

##  <a name="advantages-of-subnet-delegation"></a>Vorteile der Subnetzdelegierung

Das Delegieren eines Subnetzes an bestimmte Dienste bietet die folgenden Vorteile:

- Sie können ein Subnetz für einen oder mehrere Azure-Dienste designieren und die Instanzen im Subnetz gemäß den Anforderungen verwalten. Der Besitzer des virtuellen Netzwerks kann beispielsweise Folgendes für ein delegiertes Subnetz definieren, um die Ressourcen und den Zugriff besser verwalten zu können:
    - Richtlinien für Netzwerkfilterdatenverkehr mit Netzwerksicherheitsgruppen
    - Routing von Richtlinien mit benutzerdefinierten Routen
    - Dienstintegration mit Dienstendpunktkonfigurationen
- Sie können Dienste besser einfügen, um die Integration mit dem virtuellen Netzwerk zu verbessern, indem Sie die Voraussetzungen für Bereitstellung in Form von Netzwerkzielrichtlinien festlegen. So stellen Sie sicher, dass sämtliche Aktionen, die sich auf die Funktionsweise des eingefügten Diensts auswirken, beim Starttest blockiert werden können.


## <a name="who-can-delegate"></a>Wer kann delegieren?
Die Subnetzdelegierung ist eine Übung, die die Besitzer des virtuellen Netzwerks ausführen müssen, um eines der Subnetze für einen bestimmten Azure-Dienst zu designieren. Der Azure-Dienst stellt wiederum die Instanzen in diesem Subnetz für die Nutzung durch die Kundenworkloads bereit.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Auswirkung der Subnetzdelegierung auf Ihr Subnetz
Jeder Azure-Dienst definiert ein eigenes Bereitstellungsmodell, in dem diese definieren können, welche Eigenschaften in einem delegierten Subnetz für das Einfügen unterstützt werden. Im Folgenden finden Sie Beispiele:
- freigegebenes Subnetz mit anderen Azure-Diensten oder VMs bzw. VM-Skalierungsgruppen im selben Subnetz (oder ausschließliche Unterstützung eines dedizierten Subnetzes, das nur Instanzen dieses Diensts umfasst)
- NSG-Zuordnung zum delegierten Subnetz
- Zuordnung von NSGs, die dem delegierten Subnetz zugeordnet sind, zu anderen Subnetzen
- Zuordnung von Routingtabellen zum delegierten Subnetz
- Zuordnung von NSGs, die dem delegierten Subnetz zugeordnet sind, zu anderen Subnetzen durch die Routingtabelle
- Vorgabe der Mindestanzahl von IP-Adressen im delegierten Subnetz
- Vorgabe des IP-Adressbereichs im delegierten Subnetz aus dem privaten IP-Adressbereich (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12)
- Vorgabe, dass die benutzerdefinierte DNS-Konfiguration einen Azure DNS-Eintrag besitzt
- Vorgabe, dass die Delegierung entfernt wird, bevor das Subnetz oder virtuelle Netzwerk gelöscht werden kann.
- Verwendung mit einem privaten Endpunkt ist nicht möglich, wenn das Subnetz delegiert ist.

Eingefügte Dienste können folgendermaßen auch eigene Richtlinien hinzufügen:
- **Sicherheitsrichtlinien:** Sammlung von Sicherheitsregeln, die für einen bestimmten Dienst erforderlich sind
- **Routenrichtlinien:** Sammlung von Routen, die für einen bestimmten Dienst erforderlich sind

## <a name="what-subnet-delegation-does-not-do"></a>Wofür die Subnetzdelegierung nicht vorgesehen ist

Azure-Dienste, die in ein delegiertes Subnetz eingefügt werden, verfügen weiterhin über die grundlegenden Eigenschaften, die für nicht delegierte Subnetze verfügbar sind, wie z. B.:
-  Azure-Dienste können Instanzen in Kundensubnetze einfügen, können sich jedoch nicht auf vorhandene Workloads auswirken.
-  Die von diesen Diensten angewendeten Richtlinien oder Routen sind flexibel und können vom Kunden überschrieben werden.

## <a name="next-steps"></a>Nächste Schritte

- [Delegieren eines Subnetzes](manage-subnet-delegation.md)
