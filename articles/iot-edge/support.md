---
title: Unterstützte Betriebssystem, Containerengines – Azure IoT Edge | Microsoft-Dokumentation
description: Erfahren Sie, welche Betriebssysteme zur Ausführung von Azure IoT Edge-Daemon und -Runtime verwendet werden können, und welche Containerengines für Ihre Produktionsgeräte unterstützt werden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 178cbf930c946170834eb1f7de17e6d5bc0dda48
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058290"
---
# <a name="azure-iot-edge-supported-systems"></a>Von Azure IoT Edge unterstützte Systeme

Für Azure IoT Edge stehen verschiedene Supportoptionen zur Verfügung:

**Melden von Fehlern:** Der Großteil der Entwicklungsarbeiten für Azure IoT Edge findet im Rahmen des entsprechenden Open-Source-Projekts statt. Fehler können auf der [Problemseite](https://github.com/azure/iotedge/issues) des Projekts gemeldet werden. Fehlerbehebungen aus dem Projekt werden zeitnah in Produktupdates integriert.

**Microsoft-Kundendienstteam:** Benutzer mit einem [Supportplan](https://azure.microsoft.com/support/plans/) können direkt über das [Azure-Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) ein Supportticket erstellen, um sich an das Microsoft-Kundendienstteam zu wenden.

**Funktionsanfragen:** Angefragte Funktionen werden von Azure IoT Edge über die [User Voice-Seite](https://feedback.azure.com/forums/907045-azure-iot-edge) des Produkts verfolgt.

## <a name="container-engines"></a>Container-Engines
Azure IoT Edge benötigt eine Container-Engine zum Starten von Modulen, da sie als Container implementiert werden. Mit „moby-engine“ stellt Microsoft eine Container-Engine bereit, um diese Anforderung zu erfüllen. Sie basiert auf dem Open-Source-Projekt „Moby“. Weitere gängige Container-Engines sind Docker CE und Docker EE. Sie basieren ebenfalls auf dem Open-Source-Projekt „Moby“ und sind mit Azure IoT Edge kompatibel. Microsoft bietet bestmöglichen Support für Systeme mit diesen Container-Engines, kann für sie aber keine Fehlerkorrekturen für interne Probleme bereitstellen. Aus diesem Grund empfiehlt Microsoft, für Produktionssysteme die Moby-Engine zu verwenden.

<br>
<center>

![Moby als Containerruntime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Betriebssysteme
Azure IoT Edge ist für die meisten containerfähigen Betriebssysteme geeignet. Allerdings werden nicht alle diese Systeme gleich unterstützt. Betriebssysteme sind in Ebenen unterteilt, die jeweils den Grad der zu erwartenden Unterstützung angeben.
* Systeme der Ebene 1 können als offiziell unterstützt betrachtet werden. Bei Systemen der Ebene 1, Microsoft:
    * Microsoft führt für dieses Betriebssystem automatisierte Tests durch.
    * Microsoft stellt Installationspakete für sie bereit.
* Systeme der Ebene 2 können als mit Azure IoT Edge kompatibel betrachtet und relativ einfach verwendet werden. Bei Systemen der Ebene 2:
    * Microsoft hat Ad-hoc-Tests auf den Plattformen durchgeführt oder weiß, dass ein Partner Azure IoT Edge erfolgreich auf der Plattform ausführt.
    * Installationspakete für andere Plattformen können ggf. auch für diese Plattformen verwendet werden.
    
Die Familie des Hostbetriebssystems muss mit der Familie des Gastbetriebssystems, das im Container eines Moduls verwendet wird, immer übereinstimmen. Mit anderen Worten: Sie können unter Linux nur Linux-Container und unter Windows nur Windows-Container verwenden. Bei Verwendung von Windows werden nur Container mit Prozessisolation unterstützt, keine Container mit Hyper-V-Isolation.  

<br>
<center>

![Hostbetriebssystem stimmt mit Gastbetriebssystem überein.](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Ebene 1
Allgemein verfügbar

| Betriebssystem | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | Nein | Ja|
| Ubuntu Server 16.04 | Ja | Nein |
| Ubuntu Server 18.04 | Ja | Nein |
| Windows 10 IoT Enterprise, Build 17763 | Ja | Nein |
| Windows Server 2019, Build 17763 | Ja | Nein |
| Windows Server IoT 2019, Build 17763 | Ja | Nein |

Öffentliche Vorschau

| Betriebssystem | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core, Build 17763 | Ja | Nein |


Die oben aufgeführten Windows-Betriebssysteme stellen die Anforderungen für Geräte dar, die Windows-Container unter Windows ausführen. Dies ist die einzige unterstützte Konfiguration für die Produktion. Die Azure IoT Edge-Installationspakete für Windows ermöglichen die Verwendung von Linux-Containern unter Windows, diese Konfiguration ist jedoch nur für Entwicklungs- und Testzwecke ausgelegt. Die Verwendung von Linux-Containern unter Windows ist keine unterstützte Konfiguration für die Produktion. Jede Version von Windows 10, Build 14393 oder höher, und Windows Server 2016 oder höher kann für dieses Entwicklungsszenario verwendet werden.

### <a name="tier-2"></a>Ebene 2

| Betriebssystem | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Ja | Ja |
| Debian 8 | Ja | Ja |
| Debian 9 | Ja | Ja |
| RHEL 7.5 | Ja | Ja |
| Ubuntu 18.04 | Ja | Ja |
| Ubuntu 16.04 | Ja | Ja |
| Wind River 8 | Ja | Nein |
| Yocto | Ja | Nein |


## <a name="virtual-machines"></a>Virtual Machines
Azure IoT Edge kann auf virtuellen Computern ausgeführt werden. Das Verwenden eines virtuellen Computers als IoT Edge-Gerät ist eine verbreitete Vorgehensweise, wenn Kunden eine vorhandene Infrastruktur um Edgeintelligenz erweitern möchten. Die Familie des Host-VM-Betriebssystems muss mit der Familie des Gastbetriebssystems übereinstimmen, das im Container eines Moduls verwendet wird. Diese Anforderung gilt auch, wenn Azure IoT Edge direkt auf einem Gerät ausgeführt wird. Azure IoT Edge ist unabhängig von der zugrunde liegenden Virtualisierungstechnologie und funktioniert auf virtuellen Computern, die von Plattformen wie Hyper-V und vSphere unterstützt werden.

<br>
<center>

![Azure IoT Edge auf einem virtuellen Computer](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Mindestsystemanforderungen
Azure IoT Edge funktioniert einwandfrei auf Geräten, die klein wie ein Raspberry Pi3 sind, bis hin zur Größe von Serverhardware. Die Auswahl der richtigen Hardware für Ihr Szenario hängt von den Workloads ab, die Sie ausführen möchten. Die endgültige Entscheidung für ein Gerät zu treffen, kann kompliziert sein; allerdings können Sie mit der Prototyperstellung einer Lösung auf herkömmlichen Laptops oder Desktops problemlos beginnen.

Das Sammeln von Erfahrungen während der Prototyperstellung wird Sie bei Ihrer endgültigen Geräteauswahl leiten. Zu den Fragen, die Sie berücksichtigen sollten, gehören: 

* Wie viele Module umfasst Ihre Workload?
* Wie viele Schichten werden von den Containern in Ihren Modulen gemeinsam genutzt?
* In welcher Sprache werden Ihre Module geschrieben? 
* Wie viele Daten sollen Ihre Module verarbeiten?
* Benötigen Ihre Module spezielle Hardware zum Beschleunigen ihrer Workloads?
* Was sind die gewünschten Leistungsmerkmale Ihrer Lösung?
* Wie hoch ist Ihr Hardwarebudget?
