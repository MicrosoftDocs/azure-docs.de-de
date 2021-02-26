---
title: Übersicht über das selbstgehostete Gateway | Microsoft-Dokumentation
description: Erfahren Sie, wie die Funktion „selbstgehostetes Gateway“ von Azure API Management Organisationen beim Verwalten von APIs in Hybridumgebungen und Umgebungen mit mehreren Clouds unterstützt.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/25/2021
ms.author: apimpm
ms.openlocfilehash: 48abce693ca22163c0a1742ba71faf36fc6156a1
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989100"
---
# <a name="self-hosted-gateway-overview"></a>Übersicht über das selbstgehostete Gateway

In diesem Artikel wird erläutert, wie die Funktion „selbstgehostetes Gateway“ von API Management die API-Verwaltung in Hybridumgebungen und Umgebungen mit mehreren Clouds vereinfacht. Außerdem werden die allgemeine Architektur und die Funktionen vorgestellt.

## <a name="hybrid-and-multi-cloud-api-management"></a>API-Verwaltung in Hybridumgebungen und Umgebungen mit mehreren Clouds

Das Feature „selbstgehostetes Gateway“ weitet die API Management-Unterstützung auf Hybridumgebungen und Umgebungen mit mehreren Clouds aus und ermöglicht es Organisationen, lokal und cloudübergreifend gehostete APIs über einen einzigen API Management-Dienst in Azure effizient und sicher zu verwalten.

Mit dem selbstgehosteten Gateway erhalten Kunden die Flexibilität, eine containerbasierte Version der API Management-Gatewaykomponente in den gleichen Umgebungen bereitzustellen, in denen auch die APIs gehostet werden. Alle selbstgehosteten Gateways werden über den API Management-Dienst verwaltet, mit dem sie sich im Verbund befinden. So profitieren Kunden von Transparenz und einheitlichen Verwaltungsfunktionen für alle internen und externen APIs. Indem die Gateways in der Nähe der APIs platziert werden, können Kunden die Datenverkehrsflows der APIs optimieren und Sicherheits- und Complianceanforderungen erfüllen.

Jeder API Management-Dienst besteht aus den folgenden Hauptkomponenten:

-   Eine Verwaltungsebene – als API verfügbar gemacht –, die zum Konfigurieren des Dienst über das Azure-Portal, PowerShell oder andere unterstützte Mechanismen verwendet wird
-   Eine Gatewayebene (bzw. Datenebene), auf der die Proxyweiterleitung von API-Anforderungen, die Anwendung von Richtlinien und die Sammlung von Telemetriedaten erfolgt
-   Ein Entwicklerportal, in dem Entwickler APIs ermitteln, kennen lernen und integrieren

Standardmäßig werden all diese Komponenten in Azure bereitgestellt, sodass sämtlicher API-Datenverkehr (auf dem Bild unten als durchgehende schwarze Pfeile dargestellt) durch Azure geleitet wird, unabhängig davon, wo die Back-End-Systeme gehostet werden, die die APIs implementieren. Mit dem einfachen Betrieb dieses Modells gehen allerdings höhere Latenzen, Complianceprobleme und in einigen Fällen zusätzliche Gebühren für die Datenübertragung einher.

![API-Datenverkehrsflow ohne selbstgehostete Gateways](media/self-hosted-gateway-overview/without-gateways.png)

Indem selbstgehostete Gateways in denselben Umgebungen, in denen die Implementierungen der Back-End-APIs gehostet werden, bereitgestellt werden, kann der API-Datenverkehr direkt an die Back-End-APIs geleitet werden. Dies verbessert die Latenz, optimiert die Datenübertragungskosten und sorgt für Compliance. Gleichzeitig bleiben die Vorteile erhalten: Die Verwaltung, der Einblick und die Ermittlung aller APIs in der Organisation erfolgt an einer zentralen Stelle – unabhängig davon, wo die Implementierungen gehostet werden.

![API-Datenverkehrsflow mit selbstgehosteten Gateways](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Paketerstellung und Features

Das selbstgehostete Gateway ist eine containerisierte, funktional gleichwertige Version des verwalteten Gateways, das im Rahmen jedes API Management-Diensts in Azure bereitgestellt wird. Das selbstgehostete Gateway ist in der Microsoft Container Registry als Linux-basierter Docker-[Container](https://aka.ms/apim/sputnik/dhub) verfügbar. Es kann in Docker, Kubernetes oder jeder anderen Lösung für die Containerorchestrierung bereitgestellt werden, die auf einem lokalen Servercluster, in einer Cloudinfrastruktur oder zu Evaluierungs- und Entwicklungszwecken auf einem PC ausgeführt wird.

Die folgende, in verwalteten Gateways zu findende Funktionalität ist im selbstgehosteten Gateway **nicht verfügbar**:

- Azure Monitor-Protokolle
- Upstreamverwaltung (Back-End-seitig) von TLS-Version und Verschlüsselung
- Überprüfung von Server- und Clientzertifikaten mithilfe von [Stammzertifikaten der Zertifizierungsstelle](api-management-howto-ca-certificates.md), die in den API Management-Dienst hochgeladen wurden. Weitere Informationen finden Sie unter [Zertifikatsüberprüfung in selbstgehostetem Gateway](api-management-howto-mutual-certificates-for-clients.md#certificate-validation-in-self-hosted-gateway).
- Integration in [Service Fabric](../service-fabric/service-fabric-api-management-overview.md)
- TLS-Sitzungswiederaufnahme
- Erneute Aushandlung des Clientzertifikats. Dies bedeutet, dass API-Consumer, damit die [Clientzertifikatauthentifizierung](api-management-howto-mutual-certificates-for-clients.md) funktioniert, ihre Zertifikate als Teil des ersten TLS-Handshakes präsentieren müssen. Um dies sicherzustellen, aktivieren Sie die Einstellung „Clientzertifikat aushandeln“, wenn Sie den benutzerdefinierten Hostnamen eines selbstgehosteten Gateways konfigurieren.
- Integrierter Cache. Informationen zum Verwenden eines externen Caches in selbstgehosteten Gateways finden Sie in diesem [Dokument](api-management-howto-cache-external.md).

## <a name="connectivity-to-azure"></a>Konnektivität mit Azure

Selbstgehostete Gateways erfordern ausgehende TCP/IP-Konnektivität mit Azure an Port 443. Jedes selbstgehostete Gateway muss einer einzelnen API Management-Dienstinstanz zugeordnet werden und wird über deren Verwaltungsebene konfiguriert. Das selbstgehostete Gateway nutzt die Konnektivität mit Azure für Folgendes:

-   Melden des Status durch minütliches Senden von Heartbeatmeldungen
-   Regelmäßiges Überprüfen auf Konfigurationsupdates (alle 10 Sekunden) und Anwenden der Updates, sobald sie verfügbar sind
-   Senden von Anforderungsprotokollen und -metriken an Azure Monitor, sofern konfiguriert
-   Senden von Ereignissen an Application Insights, sofern eingerichtet

Wenn die Konnektivität mit Azure unterbrochen wird, kann das selbstgehostete Gateway keine Konfigurationsupdates empfangen, Statusmeldungen senden oder Telemetriedaten hochladen.

Das selbstgehostete Gateway ist für „fail-static“ konzipiert und übersteht temporäre Unterbrechungen der Konnektivität mit Azure. Es kann mit oder ohne lokale Konfigurationssicherung bereitgestellt werden. Im ersten Fall speichern selbstgehostete Gateways in regelmäßigen Abständen eine Sicherungskopie der jüngsten heruntergeladenen Konfiguration auf einem persistenten Volume, das an den Container oder Pod angeschlossen ist.

Wenn die Konfigurationssicherung deaktiviert ist und die Konnektivität mit Azure unterbrochen wird, geschieht Folgendes:

-   Die Ausführung selbstgehosteter Gateways funktioniert auch weiterhin unter Verwendung einer arbeitsspeicherinternen Kopie der Konfiguration.
-   Angehaltene selbstgehostete Gateways können nicht gestartet werden.

Wenn die Konfigurationssicherung aktiviert ist und die Konnektivität mit Azure unterbrochen wird, geschieht Folgendes:

-   Die Ausführung selbstgehosteter Gateways funktioniert auch weiterhin unter Verwendung einer arbeitsspeicherinternen Kopie der Konfiguration.
-   Angehaltene selbstgehostete Gateways können unter Verwendung einer Sicherungskopie der Konfiguration starten.

Wenn die Konnektivität wiederhergestellt ist, stellt jedes vom Ausfall betroffene selbstgehostete Gateway automatisch wieder eine Verbindung mit dem zugehörigen API Management-Dienst her und lädt alle Konfigurationsupdates herunter, die zur Verfügung gestellt wurden, während das Gateway offline war.

## <a name="next-steps"></a>Nächste Schritte

-   [Whitepaper mit weiteren Hintergrundinformationen zu diesem Thema](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Bereitstellen eines selbstgehosteten Gateways in Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [Bereitstellen eines selbstgehosteten Gateways in Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
