---
title: include file
description: include file
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 711b662c35b5f8fec96f1edee765696bc1028bf8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178443"
---
### <a name="general-requirements"></a>Allgemeine Anforderungen

* Das VNET muss sich im gleichen Abonnement und in der gleichen Region befinden wie das für die Poolerstellung verwendete Batch-Konto.

* Der Pool, der das VNET verwendet, kann bis zu 4.096 Knoten umfassen.

* Das für den Pool angegebene Subnetz muss über ausreichend nicht zugewiesene IP-Adressen verfügen, um die Anzahl virtueller Computer aufnehmen zu können, die für den Pool geplant sind, d. h. die Summe der `targetDedicatedNodes`- und `targetLowPriorityNodes`-Eigenschaften des Pools. Wenn das Subnetz nicht über ausreichend nicht zugewiesene IP-Adressen verfügt, belegt der Pool teilweise die Computeknoten und es tritt ein Anpassungsfehler auf. 

* Ihr Azure Storage-Endpunkt muss von jedem benutzerdefinierten DNS-Server aufgelöst werden, der Ihr VNET bedient. Insbesondere URLs im Format `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` und `<account>.blob.core.windows.net` müssen auflösbar sein. 

Darüber hinaus gelten ggf. noch weitere VNET-Anforderungen. Diese hängen jedoch davon ab, ob sich der Batch-Pool in der Konfiguration des virtuellen Computers oder in der Cloud Services-Konfiguration befindet. Für neue Poolbereitstellungen in einem VNET wird die Konfiguration des virtuellen Computers empfohlen.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pools in der Konfiguration des virtuellen Computers

**Unterstützte VNETs:** Nur Azure Resource Manager-basierte VNETs

**Subnetz-ID:** Verwenden Sie den *Ressourcenbezeichner* des Subnetzes, wenn Sie das Subnetz über die Batch-APIs angeben. Die Subnetz-ID hat folgendes Format:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Berechtigungen:** Überprüfen Sie, ob Ihre Sicherheitsrichtlinien oder -sperren für das Abonnement oder die Ressourcengruppe Ihres VNETs die VNET-Verwaltungsberechtigungen eines Benutzers einschränken.

**Zusätzliche Netzwerkressourcen:** Batch ordnet in der Ressourcengruppe, die das VNET enthält, automatisch zusätzliche Netzwerkressourcen zu. Für je 50 dedizierte Knoten (oder je 20 Knoten mit niedriger Priorität) ordnet Batch zu: 1 Netzwerksicherheitsgruppe (NSG), 1 öffentliche IP-Adresse und 1 Lastenausgleich. Diese Ressourcen werden durch die [Ressourcenkontingente](../articles/azure-subscription-service-limits.md) des Abonnements beschränkt. Bei umfangreichen Pools muss ggf. eine Kontingenterhöhung für eine oder mehrere der Ressourcen angefordert werden.

#### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Das Subnetz muss eingehende Kommunikation des Batch-Diensts zulassen, um Aufgaben auf den Computeknoten planen zu können, und ausgehende Kommunikation, um mit Azure Storage und anderen Ressourcen kommunizieren zu können. Für Pools in der Konfiguration des virtuellen Computers fügt Batch NSGs auf der Ebene der Netzwerkschnittstellen (NICs) hinzu, die an virtuelle Computer angefügt sind. Diese NSGs konfigurieren automatisch Eingangs- und Ausgangsregeln, um folgenden Datenverkehr zuzulassen:

* Eingehender TCP-Datenverkehr an den Ports 29876 und 29877 von IP-Adressen der Batch-Dienstrolle 
* Eingehender TCP-Datenverkehr am Port 22 (Linux-Knoten) oder Port 3389 (Windows-Knoten), um Remotezugriff zu ermöglichen Für bestimmte Arten von Multi-Instanz-Aufgaben unter Linux (z.B. MPI) müssen Sie auch den Datenverkehr auf dem SSH-Port 22 für IPs in dem Subnetz zulassen, das die Batch-Computeknoten enthält.
* Ausgehender Datenverkehr an allen Ports zum virtuellen Netzwerk
* Ausgehender Datenverkehr an allen Ports zum Internet

> [!IMPORTANT]
> Seien Sie vorsichtig, wenn Sie Eingangs- und Ausgangsregeln in von Batch konfigurierten NSGs ändern. Falls die Kommunikation mit den Computeknoten im angegebenen Subnetz durch eine Netzwerksicherheitsgruppe (NSG) verhindert wird, legt der Batch-Dienst den Zustand der Computeknoten auf **Nicht verwendbar** fest.

Sie müssen keine NSGs auf der Subnetzebene angeben, da Batch eigene NSGs konfiguriert. Wenn das angegebene Subnetz allerdings über zugewiesene Netzwerksicherheitsgruppen (NSGs) und/oder eine Firewall verfügt, konfigurieren Sie die Eingangs- und Ausgangssicherheitsregeln gemäß den folgenden Tabellen. Konfigurieren Sie eingehenden Datenverkehr am Port 3389 (Windows) bzw. am Port 22 (Linux) nur, wenn Sie Remotezugriff auf die virtuellen Computer des Pools von externen Quellen aus zulassen müssen. Dieser Schritt ist nicht erforderlich, um die virtuellen Computer des Pools verwenden zu können. Beachten Sie, dass Sie den virtuellen Subnetzdatenverkehr auf Port 22 für Linux aktivieren müssen, wenn Sie bestimmte Arten von Multi-Instanz-Aufgaben wie MPI verwenden.

**Eingangssicherheitsregeln**

| Quell-IP-Adressen | Quelldiensttag | Quellports | Ziel | Zielports | Protocol | Aktion |
| --- | --- | --- | --- | --- | --- | --- |
| – | `BatchNodeManagement` [Diensttag](../articles/virtual-network/security-overview.md#service-tags) | * | Beliebig | 29876–29877 | TCP | ZULASSEN |
| Benutzerquellen-IPs für den Remotezugriff auf Computeknoten und/oder Computeknoten-Subnetz für Multi-Instanz-Aufgaben unter Linux, falls erforderlich. | – | * | Beliebig | 3389 (Windows), 22 (Linux) | TCP | ZULASSEN |

**Ausgangssicherheitsregeln**

| `Source` | Quellports | Ziel | Zieldiensttag | Protocol | Aktion |
| --- | --- | --- | --- | --- | --- |
| Beliebig | 443 | [Diensttag](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (in der gleichen Region wie Ihr Batchkonto und VNET)  | Beliebig | ZULASSEN |

### <a name="pools-in-the-cloud-services-configuration"></a>Pools in der Cloud Services-Konfiguration

**Unterstützte VNETs:** Nur klassische VNETs

**Subnetz-ID:** Verwenden Sie den *Ressourcenbezeichner* des Subnetzes, wenn Sie das Subnetz über die Batch-APIs angeben. Die Subnetz-ID hat folgendes Format:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicVirtualNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Berechtigungen:** Der Dienstprinzipal `MicrosoftAzureBatch` muss für das angegebene VNET über die RBAC-Rolle `Classic Virtual Machine Contributor` verfügen.

#### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Das Subnetz muss eingehende Kommunikation des Batch-Diensts zulassen, um Aufgaben auf den Computeknoten planen zu können, und ausgehende Kommunikation, um mit Azure Storage und anderen Ressourcen kommunizieren zu können.

Sie müssen keine NSG angeben, da Batch nur eingehende Kommunikation von Batch-IP-Adressen an die Poolknoten konfiguriert. Wenn das angegebene Subnetz allerdings über zugewiesene NSGs und/oder eine Firewall verfügt, konfigurieren Sie die Eingangs- und Ausgangssicherheitsregeln gemäß den folgenden Tabellen. Falls die Kommunikation mit den Computeknoten im angegebenen Subnetz durch eine Netzwerksicherheitsgruppe (NSG) verhindert wird, legt der Batch-Dienst den Zustand der Computeknoten auf **Nicht verwendbar** fest.

Konfigurieren Sie eingehenden Datenverkehr am Port 3389 für Windows, wenn Sie RDP-Zugriff auf die Poolknoten zulassen müssen. Dieser Schritt ist nicht erforderlich, um die Poolknoten verwenden zu können.

**Eingangssicherheitsregeln**

| Quell-IP-Adressen | Quellports | Ziel | Zielports | Protocol | Aktion |
| --- | --- | --- | --- | --- | --- |
Beliebig <br /><br />Dafür ist zwar im Grunde die Zulassung aller IP-Adressen erforderlich, der Batch-Dienst wendet jedoch auf der Ebene jedes Knotens eine ACL-Regel an, die alle nicht vom Batch-Dienst stammenden IP-Adressen herausfiltert. | * | Beliebig | 10100, 20100, 30100 | TCP | ZULASSEN |
| Optional, um RDP-Zugriff auf Computeknoten zu ermöglichen. | * | Beliebig | 3389 | TCP | ZULASSEN |

**Ausgangssicherheitsregeln**

| `Source` | Quellports | Ziel | Zielports | Protocol | Aktion |
| --- | --- | --- | --- | --- | --- |
| Beliebig | * | Beliebig | 443  | Beliebig | ZULASSEN |
