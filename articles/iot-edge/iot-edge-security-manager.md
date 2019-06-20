---
title: Erfahren Sie, wie der Sicherheits-Manager Geräte und Software schützt – Azure IoT Edge | Microsoft-Dokumentation
description: Verwaltet das Sicherheitsniveau für IoT Edge-Gerät und die Integrität von Sicherheitsdiensten.
services: iot-edge
keywords: Sicherheit, sicheres Element, Enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 07/30/2018
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bc441e2bbd36c8d078eb67aff48e58684a026289
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60444971"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge-Sicherheits-Manager

Der Azure IoT Edge-Sicherheits-Manager wird ein streng abgegrenzter Sicherheitskern für den Schutz der IoT Edge-Geräte und aller zugehörigen Komponenten durch das Abstrahieren der sicheren Hardware. Er fungiert als zentraler Punkt zur Erhöhung der Sicherheit und stellt den Integrationspunkt für Technologie durch Gerätehersteller (OEM) dar.

![Azure IoT Edge-Sicherheits-Manager](media/edge-security-manager/iot-edge-security-manager.png)

Der IoT Edge-Sicherheits-Manager soll die Integrität des IoT Edge-Geräts und aller inhärenten Softwarevorgänge schützen.  Dazu wird die Vertrauensstellung vom Hardware-Vertrauensanker (sofern vorhanden) übertragen, um ein sicheres Bootstrapping der IoT Edge-Runtime durchzuführen und die Integrität ihrer Vorgänge weiter zu überwachen.  Der IoT Edge-Sicherheits-Manager ist eine Software, die mit sicherer Hardware (sofern verfügbar und aktiviert) zusammenarbeitet, um die höchstmögliche Sicherheit zu bieten.  

Zu den Aufgaben des IoT Edge-Sicherheits-Managers gehören unter anderem:

* Gesichertes und gemessenes Bootstrapping für das Azure IoT Edge-Gerät
* Bereitstellen der Geräteidentität und Übergang der Vertrauensstellung bei Bedarf
* Hosten und Schützen von Gerätekomponenten von Clouddiensten wie dem Device Provisioning-Dienst
* Sicheres Bereitstellen von IoT Edge-Modulen mit eindeutiger Identität
* Gatekeeper für Hardware-Vertrauensanker über Notardienste
* Überwachen der Integrität der IoT Edge-Vorgänge zur Laufzeit

Der IoT Edge-Sicherheits-Manager besteht aus drei Hauptkomponenten:

* IoT Edge-Sicherheits-Daemon
* HSM-PAL (Hardware Security Module Platform Abstraction Layer)
* Optional, jedoch dringend empfohlen: Hardware-Vertrauensanker oder HSM (Hardwaresicherheitsmodul)

## <a name="the-iot-edge-security-daemon"></a>Der IoT Edge-Sicherheits-Daemon

Der IoT Edge-Security-Daemon ist die Software, die für die logischen Vorgänge des IoT Edge-Sicherheits-Managers verantwortlich ist. Er stellt einen großen Teil der vertrauenswürdigen Rechenbasis des IoT Edge-Geräts dar. 

### <a name="design-principles"></a>Entwurfsprinzipien

Der IoT Edge-Sicherheits-Daemon folgt zwei Kernprinzipien: Maximieren der betrieblichen Integrität und Minimieren von Überfrachtung und Änderungen.

#### <a name="maximize-operational-integrity"></a>Maximieren der operativen Integrität

Der IoT Edge-Sicherheits-Daemon wird mit der höchsten Integrität ausgeführt, die im Rahmen der Abwehrfunktionalität eines Hardware-Vertrauensankers möglich ist. Mit einer angemessenen Integration misst und überwacht der Hardware-Vertrauensanker den Sicherheits-Daemon statisch und zur Laufzeit, um Manipulationen zu verhindern.

Der physische Zugriff ist bei IoT-Geräten immer eine Bedrohung. Der Hardware-Vertrauensanker spielt eine wichtige Rolle beim Schutz der Integrität des IoT Edge-Sicherheits-Daemons.  Es gibt zwei Arten von Hardware-Vertrauensankern:

* Secure Elements für den Schutz vertraulicher Informationen wie Geheimnissen und kryptografischen Schlüsseln
* Secure Enclaves für den Schutz von Geheimnissen wie Schlüsseln und sensibler Workloads wie Messung und Abrechnung

Es gibt zwei Arten von Ausführungsumgebungen, die Hardware-Vertrauensanker nutzen:

* Die Standardumgebung – oder Rich Execution Environment (REE) – basiert auf der Verwendung von Secure Elements zum Schutz vertraulicher Informationen.
* Die vertrauenswürdige Ausführungsumgebung – oder Trusted Execution Environment (TEE) – basiert auf der Verwendung der Secure-Enclave-Technologie zum Schutz vertraulicher Informationen und für die Ausführung von Software.

Bei Geräten, die Secure Enclaves als Hardware-Vertrauensanker verwenden, wird davon ausgegangen, dass sich vertrauliche Logik im IoT Edge-Sicherheits-Daemon innerhalb der Enclave befindet.  Nicht sensible Bereiche des Sicherheits-Daemons können sich auch außerhalb der TEE befinden.  In jedem Fall wird erwartet, dass ODMs (Original Design Manufacturer) und OEMs (Original Equipment Manufacturer) die Vertrauensstellung von ihrem HSM erweitern und die Integrität des IoT Edge-Sicherheits-Daemons beim Start und zur Laufzeit überwachen und schützen.

#### <a name="minimize-bloat-and-churn"></a>Minimale Überfrachtung und Änderung

Eine weiteres Kernprinzip der IoT Edge-Sicherheits-Daemons ist die Minimierung von Änderungen.  Für die höchste Vertrauensstufe kann der IoT Edge-Sicherheits-Daemon eng mit dem Hardware-Vertrauensanker des Geräts verknüpft und als nativer Code ausgeführt werden.  Es ist üblich für diese Arten von Umsetzungen, die Daemon-Software über die sicheren Updatepfade des Hardware-Vertrauensankers (und nicht über die vom Betriebssystem bereitgestellten Aktualisierungsmechanismen) zu aktualisieren. Dieses Vorgehen kann abhängig von bestimmten Hardware- und Bereitstellungsszenarien eine Herausforderung darstellen.  Auch wenn die Sicherheitsaktualisierung für IoT-Geräte dringend empfohlen wird, sollten Sie berücksichtigen, dass übermäßige Updateanforderungen oder große Updatepayloads die Angriffsfläche auf vielfältige Weise vergrößern können.  Beispiele hierfür sind das Überspringen von Updates zur Maximierung der operativen Verfügbarkeit oder ein zu strikter Hardware-Vertrauensanker für die Verarbeitung großer Updatepayloads.  Daher ist das Design des IoT Edge-Sicherheits-Daemons darauf ausgelegt, den Aufwand und damit die vertrauenswürdige Rechenbasis klein zu halten und die Updateanforderungen auf diese Weise zu minimieren.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architektur des IoT Edge-Sicherheits-Daemons

![Azure IoT Edge-Sicherheits-Daemon](media/edge-security-manager/iot-edge-security-daemon.png)

Die Architektur des IoT Edge-Sicherheits-Daemons wurde so entworfen, dass die gesamte verfügbare Technologie der Hardware-Vertrauensanker für die Erhöhung der Sicherheit genutzt werden kann.  Sie ermöglicht auch den Betrieb in unterschiedlichen Umgebungen mit einer Standardausführungsumgebung (bzw. Rich Execution Environment, REE) und einer vertrauenswürdigen Ausführungsumgebung (Trusted Execution Environment, TEE), wenn Hardwaretechnologien TEEs bieten. Rollenspezifische Schnittstellen ermöglichen das Zusammenspiel der Hauptkomponenten von IoT Edge, um die Integrität des IoT Edge-Geräts und seiner Vorgänge zu gewährleisten.

#### <a name="cloud-interface"></a>Cloudschnittstelle

Die Cloudschnittstelle ermöglicht dem IoT Edge-Sicherheits-Daemon den Zugriff auf Clouddienste wie Cloudergänzungen zur Gerätesicherheit, etwa Sicherheitserneuerungen.  Der IoT Edge-Sicherheits-Daemon verwendet diese Schnittstelle zurzeit z.B. für den Zugriff auf den [Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/) von Azure IoT Hub für das Identity Lifecycle Management des Geräts.  

#### <a name="management-api"></a>Verwaltungs-API

Der IoT Edge-Sicherheits-Daemon bietet eine Verwaltungs-API, die vom IoT Edge-Agent beim Erstellen, Starten, Beenden und Entfernen von Edgemodulen aufgerufen wird. Der IoT Edge-Sicherheits-Daemon speichert „Registrierungen“ für alle aktiven Module. Diese Registrierungen ordnen die Modulidentität einigen Eigenschaften des Moduls zu. Einige Beispiele für diese Eigenschaften sind die Prozess-ID (pid) des Prozesses, der im Container ausgeführt wird, oder der Hash des Inhalts im Docker-Container.

Diese Eigenschaften werden von der Workload-API (siehe unten) verwendet, um zu bestätigen, dass der Aufrufer zum Ausführen einer Aktion autorisiert ist.

Die Verwaltungs-API ist eine privilegierte API, die nur vom IoT Edge-Agent aufgerufen werden kann.  Da der IoT Edge-Sicherheits-Daemon das Bootstrapping für den IoT Edge-Agent ausführt und diesen startet, kann er eine implizite Registrierung für den IoT Edge-Agent erstellen, nachdem er bestätigt hat, dass der IoT Edge-Agent nicht manipuliert wurde. Derselbe Nachweisprozess, den die Workload-API verwendet, wird auch verwendet, um den Zugriff auf die Verwaltungs-API auf ausschließlich den IoT Edge-Agent zu beschränken.

#### <a name="container-api"></a>Container-API

Der IoT Edge-Sicherheits-Daemon bietet eine Containerschnittstelle für die Interaktion mit dem verwendeten Containersystem (z.B. Moby oder Docker) bei der Modulinstanziierung.

#### <a name="workload-api"></a>Workload-API

Die Workload-API ist eine IoT Edge-Sicherheits-Daemon-API, auf die alle Module Zugriff haben – auch der IoT Edge-Agent. Sie ermöglicht einen Identitätsnachweis – ein vom HSM signiertes Token oder ein X.509-Zertifikat – und die zugehörige Vertrauenssammlung für ein Modul. Die Vertrauenssammlung enthält ZS-Zertifikate für alle anderen Server, denen die Module vertrauen sollen.

Der IoT Edge-Sicherheits-Daemon verwendet einen Nachweisprozess, um diese API zu schützen. Wenn ein Modul diese API aufruft, versucht der IoT Edge-Sicherheits-Daemon, eine Registrierung für die Identität zu finden. Ist er erfolgreich, verwendet er die Eigenschaften der Registrierung, um das Modul zu bewerten. Wenn das Ergebnis der Überprüfung mit der Registrierung übereinstimmt, wird ein neues vom HSM signiertes Token oder X.509-Zertifikat generiert. Die zugehörigen ZS-Zertifikate (Vertrauenssammlung) werden an das Modul zurückgegeben.  Das Modul verwendet dieses Zertifikat zum Verbinden mit IoT Hub oder anderen Modulen oder zum Starten eines Servers. Wenn das signierte Token oder das Zertifikat demnächst abläuft, muss das Modul ein neues Zertifikat anfordern. 

### <a name="integration-and-maintenance"></a>Integration und Wartung

Microsoft verwaltet die Hauptcodebasis für den [IoT Edge-Sicherheits-Daemon auf GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Installation und Updates

Installation und Updates des IoT Edge-Sicherheits-Daemons werden über das Paketverwaltungssystem des Betriebssystems verwaltet. IoT Edge-Geräte mit einem Hardware-Vertrauensanker sollten zusätzliche Sicherheitsmaßnahmen für die Integrität des Daemons bereitstellen, indem sie den Lebenszyklus über sichere Start- und Updateverwaltungssysteme verwalten.  Es liegt in der Verantwortung der Geräteentwickler, diese Vorgänge in Übereinstimmung mit den jeweiligen Gerätefunktionen zu untersuchen.

#### <a name="versioning"></a>Versionsverwaltung

Die IoT Edge-Runtime verfolgt und meldet die Version des IoT Edge-Sicherheits-Daemons. Die Version wird als das *runtime.platform.version*-Attribut der gemeldeten Eigenschaft des IoT Edge-Agent-Moduls gemeldet.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>HSM-PAL (Hardware Security Module Platform Abstraction Layer)

Die HSM-PAL abstrahiert alle Hardware-Vertrauensanker, damit Entwickler oder Benutzer von IoT Edge sich nicht mit deren Komplexität beschäftigen müssen.  Sie umfasst eine Kombination aus Anwendungsprogrammierschnittstelle (API) und domänenübergreifenden Kommunikationsverfahren (beispielsweise die Kommunikation zwischen einer Standardausführungsumgebung und einer Secure Enclave-Instanz).  Die eigentliche Implementierung der HSM-PAL hängt von der verwendeten Sicherheitshardware ab. Ihr Vorhandensein ermöglicht die Verwendung praktisch jeder Sicherheitshardware.

## <a name="secure-silicon-root-of-trust-hardware"></a>Sicherer Hardware-Vertrauensanker

Sichere Hardware ist erforderlich, um innerhalb der IoT Edge-Geräthardware einen Vertrauensanker zu schaffen.  Es gibt verschiedene Arten von Sicherheitshardware, z.B. Trusted Platform Module (TPM), eingebettete Secure Elements (eSE), ARM TrustZone, Intel SGX oder benutzerdefinierte sichere Hardwaretechnologien.  Die Verwendung von sicherer Vertrauensanker in Geräten wird aufgrund der Bedrohungen durch den physischen Zugriff auf IoT-Geräte dringend empfohlen.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge-Sicherheits-Manager – Integration und Wartung

Der IoT Edge-Sicherheits-Manager versucht, die Komponenten, die bei benutzerdefinierten Sicherheitsmaßnahmen zum Schutz der Sicherheit und Integrität der Azure IoT Edge-Plattform beitragen, zu identifizieren und zu isolieren. Drittanbieter wie Gerätehersteller sollten die ihnen zur Verfügung stehenden benutzerdefinierten Sicherheitsfeatures für ihre Gerätehardware nutzen.  Im nächsten Abschnitt finden Sie Links zu Beispielen für das Absichern des Azure IoT-Sicherheits-Managers mit dem Trusted Platform Module (TPM) für Windows und Linux. In diesen Beispielen werden Software oder virtuelle TPMs verwendet. Sie gelten jedoch direkt für die Verwendung diskreter TPM-Geräte.  

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den Blog unter [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) (Sichern von Intelligent Edge).

Erstellen und Bereitstellen eines [IoT Edge-Geräts mit einem virtuellen TPM auf einem virtuellen Linux-Computer](how-to-auto-provision-simulated-device-linux.md)

Erstellen und Bereitstellen eines [IoT Edge-Geräts mit simuliertem TPM unter Windows](how-to-auto-provision-simulated-device-windows.md)
