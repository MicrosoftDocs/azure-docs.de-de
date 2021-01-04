---
title: Unterstützte Betriebssysteme, Container-Engines (Azure IoT Edge)
description: Erfahren Sie, welche Betriebssysteme zur Ausführung von Azure IoT Edge-Daemon und -Runtime verwendet werden können, und welche Containerengines für Ihre Produktionsgeräte unterstützt werden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a08fc25083ecd6917f658f27cb954391228adf7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188699"
---
# <a name="azure-iot-edge-supported-systems"></a>Von Azure IoT Edge unterstützte Systeme

In diesem Artikel erhalten Sie Informationen darüber, welche Systeme und Komponenten (offiziell oder als Vorschauversion) von IoT Edge unterstützt werden.

Wenn bei der Verwendung von Azure IoT Edge Probleme auftreten, gibt es mehrere Möglichkeiten, um Unterstützung zu erhalten. Versuchen Sie es mit einer der folgenden Supportoptionen:

**Melden von Fehlern:** Der Großteil der Entwicklungsarbeiten für Azure IoT Edge findet im Rahmen des entsprechenden Open-Source-Projekts statt. Fehler können auf der [Problemseite](https://github.com/azure/iotedge/issues) des Projekts gemeldet werden. Fehlerbehebungen aus dem Projekt werden zeitnah in Produktupdates integriert.

**Microsoft-Kundendienstteam:** Benutzer mit einem [Supportplan](https://azure.microsoft.com/support/plans/) können direkt über das [Azure-Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) ein Supportticket erstellen, um sich an das Microsoft-Kundendienstteam zu wenden.

**Anfragen von Features:** Angefragte Features werden von Azure IoT Edge über die [User Voice-Seite](https://feedback.azure.com/forums/907045-azure-iot-edge) des Produkts verfolgt.

## <a name="container-engines"></a>Container-Engines

Azure IoT Edge-Module werden als Container implementiert, für deren Start IoT Edge eine Containerengine benötigt. Mit „moby-engine“ stellt Microsoft eine Container-Engine bereit, um diese Anforderung zu erfüllen. Diese Containerengine basiert auf dem Open-Source-Projekt „Moby“. Weitere gängige Container-Engines sind Docker CE und Docker EE. Diese basieren ebenfalls auf dem Open-Source-Projekt „Moby“ und sind mit Azure IoT Edge kompatibel. Microsoft bietet bestmöglichen Support für Systeme mit diesen Containerengines, kann aber keine Fehlerbehebungen für diese bereitstellen. Aus diesem Grund empfiehlt Microsoft, für Produktionssysteme die Moby-Engine zu verwenden.

<br>
<center>

![Die Moby-Engine als Containerruntime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Betriebssysteme

Azure IoT Edge ist für die meisten containerfähigen Betriebssysteme geeignet. Allerdings werden nicht alle diese Systeme gleich unterstützt. Betriebssysteme sind in Ebenen unterteilt, die jeweils den Grad der zu erwartenden Unterstützung angeben.

* Systeme der Ebene 1 werden unterstützt. Bei Systemen der Ebene 1, Microsoft:
  * Microsoft führt für dieses Betriebssystem automatisierte Tests durch.
  * Microsoft stellt Installationspakete für sie bereit.
* Systeme der Ebene 2 sind mit Azure IoT Edge kompatibel und relativ einfach zu verwenden. Bei Systemen der Ebene 2:
  * Microsoft hat informelle Tests auf den Plattformen durchgeführt oder weiß, dass ein Partner Azure IoT Edge auf der Plattform erfolgreich ausführt.
  * Installationspakete für andere Plattformen können ggf. auch für diese Plattformen verwendet werden.

Die Familie des Hostbetriebssystems muss mit der Familie des Gastbetriebssystems, das im Container eines Moduls verwendet wird, immer übereinstimmen. Mit anderen Worten: Sie können unter Linux nur Linux-Container und unter Windows nur Windows-Container verwenden. Bei Verwendung von Windows werden nur Container mit Prozessisolation unterstützt, keine Container mit Hyper-V-Isolation.  

<br>
<center>

![Hostbetriebssystem stimmt mit Gastbetriebssystem überein.](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Ebene 1

Die in der folgenden Tabelle aufgeführten Systeme (entweder allgemein verfügbare Versionen oder Vorschauversionen) werden von Microsoft unterstützt und mit jedem neuen Release getestet. 

| Betriebssystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Public Preview  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Public Preview |
| [Windows 10 IoT Enterprise](/windows/iot-core/windows-iot-enterprise), Build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Core](/windows/iot-core/windows-iot-core), Build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](/windows-server/get-started-19/rel-notes-19), Build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](/windows/iot-core/windows-server), Build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

Die oben aufgeführten Windows-Betriebssysteme stellen die Anforderungen für Geräte dar, die Windows-Container unter Windows ausführen, wobei es sich um die einzige unterstützte Konfiguration für die Produktion handelt. Die Azure IoT Edge-Installationspakete für Windows ermöglichen die Verwendung von Linux-Containern unter Windows, diese Konfiguration ist jedoch nur für Entwicklungs- und Testzwecke ausgelegt. 

### <a name="tier-2"></a>Ebene 2

Die in der folgenden Tabelle aufgeführten Systeme gelten als kompatibel mit Azure IoT Edge, werden aber nicht aktiv getestet oder von Microsoft verwaltet.

| Betriebssystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS Buster <sup>1</sup> |  | ![Raspberry Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>2</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10-Systeme, einschließlich Raspberry Pi OS Buster, verwenden eine OpenSSL-Version, die von IoT Edge nicht unterstützt wird. Verwenden Sie den folgenden Befehl, um vor der Installation von IoT Edge eine frühere Version zu installieren:

```bash
sudo apt-get install libssl1.0.2
```

<sup>2</sup> Die Debian 9-Pakete aus dem [Repository für Azure IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases) sollten mit Ubuntu 20.04 sofort verwendet werden können.

## <a name="releases"></a>Releases

IoT Edge-Releaseressourcen und -Hinweise sind auf der Seite [azure-iotedge releases](https://github.com/Azure/azure-iotedge/releases) verfügbar. Dieser Abschnitt spiegelt Informationen aus diesen Versionshinweisen wider, um Ihnen die Visualisierung der Komponenten der einzelnen Versionen zu erleichtern.

IoT Edge-Komponenten können einzeln installiert oder aktualisiert werden und sind abwärtskompatibel mit Komponenten aus älteren Versionen. In der folgenden Tabelle sind die in den einzelnen Releases enthaltenen Komponenten aufgeführt:

| Release | Sicherheitsdaemon | Edge-Hub<br>Edge-Agent | Libiothsm | Moby |
|--|--|--|--|--|
| **1.0.10** | 1.0.10 | 1.0.10 | 1.0.10 |  |
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

IoT Edge verwendet das Microsoft.Azure.Device.Client SDK. Weitere Informationen finden Sie im [GitHub-Repository „Azure IoT C# SDK“](https://github.com/Azure/azure-iot-sdk-csharp) oder im [Inhalt der Referenz zum Azure SDK für .NET](/dotnet/api/overview/azure/iot/client). Die folgende Liste zeigt die Version des Client SDK, mit dem die einzelnen Releases getestet wurden:

* **IoT Edge 1.0.10**: Client SDK 1.28.0
* **IoT Edge 1.0.9**: Client SDK 1.21.1
* **IoT Edge 1.0.8**: Client SDK 1.20.3
* **IoT Edge 1.0.7**: Client SDK 1.20.1
* **IoT Edge 1.0.6**: Client SDK 1.17.1
* **IoT Edge 1.0.5**: Client SDK 1.17.1

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