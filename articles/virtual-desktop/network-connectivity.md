---
title: Grundlegendes zur Netzwerkkonnektivität für Windows Virtual Desktop
titleSuffix: Azure
description: Erfahren Sie mehr über die Netzwerkkonnektivität für Windows Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: e4149864e16196b695d38a8c46ab5af835453412
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221209"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>Grundlegendes zur Netzwerkkonnektivität für Windows Virtual Desktop

Windows Virtual Desktop bietet die Möglichkeit, Clientsitzungen auf den Sitzungshosts zu hosten, die in Azure ausgeführt werden. Microsoft verwaltet Teile der Dienste im Auftrag des Kunden und stellt sichere Endpunkte zum Verbinden von Clients und Sitzungshosts bereit. Das folgende Diagramm bietet eine allgemeine Übersicht über die von Windows Virtual Desktop verwendeten Netzwerkverbindungen.

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Diagramm zu Netzwerkverbindungen von Windows Virtual Desktop" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Sitzungskonnektivität

Windows Virtual Desktop verwendet Remotedesktopprotokoll (RDP), um Remotefunktionen für die Anzeige und Eingabe über Netzwerkverbindungen bereitzustellen. RDP wurde anfänglich mit Windows NT 4.0 Terminal Server Edition veröffentlicht und entwickelte sich mit jedem Release von Microsoft Windows und Windows Server kontinuierlich weiter. Von Anfang an wurde RDP so entwickelt, dass es vom zugrunde liegenden Transportstapel unabhängig ist, und heute unterstützt es mehrere Arten von Transporten.

## <a name="reverse-connect-transport"></a>Reverse Connection-Transport

Windows Virtual Desktop verwendet den Reverse Connection-Transport zum Einrichten der Remotesitzung und für den Transport des RDP-Datenverkehrs. Im Gegensatz zu Bereitstellungen der lokalen Remotedesktopdienste verwendet der Reverse Connection-Transport keinen TCP-Listener, um eingehende RDP-Verbindungen zu empfangen. Stattdessen wird die ausgehende Konnektivität mit der Windows Virtual Desktop-Infrastruktur über die HTTPS-Verbindung verwendet.

## <a name="session-host-communication-channel"></a>Kommunikationskanal des Sitzungshosts

Beim Start des Windows Virtual Desktop-Sitzungshosts richtet der Remote Desktop Agent Loader-Dienst den beständigen Kommunikationskanal des Brokers von Windows Virtual Desktop ein. Dieser Kommunikationskanal überlagert eine sichere TLS-Verbindung (Transport Layer Security) und fungiert als Bus für den Dienstnachrichtenaustausch zwischen Sitzungshost und Windows Virtual Desktop-Infrastruktur.

## <a name="client-connection-sequence"></a>Clientverbindungssequenz

Die Clientverbindungssequenz wird im Folgenden beschrieben:

1. Der unterstützte Windows Virtual Desktop-Clientbenutzer abonniert den Windows Virtual Desktop-Arbeitsbereich.
2. Azure Active Directory authentifiziert den Benutzer und gibt das Token zurück, das zum Aufzählen der für einen Benutzer verfügbaren Ressourcen verwendet wird.
3. Der Client übergibt das Token an den Feedabonnementdienst von Windows Virtual Desktop.
4. Der Feedabonnementdienst von Windows Virtual Desktop überprüft das Token.
5. Der Feedabonnementdienst von Windows Virtual Desktop übergibt die Liste der verfügbaren Desktops und RemoteApps in Form einer digital signierten Verbindungskonfiguration an den Client zurück.
6. Der Client speichert die Verbindungskonfiguration für jede verfügbare Ressource in einem Satz von RDP-Dateien.
7. Wenn ein Benutzer die Ressource zum Verbinden auswählt, verwendet der Client die zugehörige RDP-Datei, stellt die sichere TLS 1.2-Verbindung mit der nächstgelegenen Windows Virtual Desktop-Gatewayinstanz her und übergibt die Verbindungsinformationen.
8. Das Windows Virtual Desktop-Gateway überprüft die Anforderung und fordert den Windows Virtual Desktop-Broker auf, die Verbindung zu orchestrieren.
9. Der Windows Virtual Desktop-Broker identifiziert den Sitzungshost und verwendet den zuvor eingerichteten permanenten Kommunikationskanal, um die Verbindung zu initialisieren.
10. Der Remotedesktopstack initiiert die TLS 1.2-Verbindung mit derselben Windows Virtual Desktop Gateway-Instanz, die vom Client verwendet wird.
11. Nachdem sowohl der Client als auch der Sitzungshost mit dem Gateway verbunden sind, beginnt das Gateway mit der Übermittlung der Rohdaten zwischen beiden Endpunkten. Dadurch wird der grundlegende Reverse Connection-Transport für das RDP festgelegt.
12. Nachdem der Basistransport festgelegt wurde, startet der Client den RDP-Handshake.

## <a name="connection-security"></a>Verbindungssicherheit

TLS 1.2 wird für alle Verbindungen verwendet, die von den Clients und Sitzungshosts mit den Komponenten der Windows Virtual Desktop-Infrastruktur initiiert werden. Windows Virtual Desktop verwendet dasselbe TLS 1.2-Verschlüsselungsverfahren wie [Azure Front Door](../frontdoor/front-door-faq.md#what-are-the-current-cipher-suites-supported-by-azure-front-door). Es ist wichtig, sicherzustellen, dass sowohl Clientcomputer als auch Sitzungshosts diese Verschlüsselungsverfahren verwenden können.
Für den Reverse Connection-Transport stellt sowohl der Client als auch der Sitzungshost eine Verbindung mit dem Windows Virtual Desktop-Gateway her. Nach dem Einrichten der TCP-Verbindung überprüft der Client oder Sitzungshost das Zertifikat des Windows Virtual Desktop-Gateways.
Nach dem Einrichten des Basistransports stellt RDP mithilfe der Zertifikate des Sitzungshosts eine geschachtelte TLS-Verbindung zwischen dem Client und dem Sitzungshost her. Standardmäßig wird das Zertifikat, das für die RDP-Verschlüsselung verwendet wird, vom Betriebssystem bei der Bereitstellung selbst generiert. Bei Bedarf können Kunden zentral verwaltete Zertifikate bereitstellen, die von der Unternehmenszertifizierungsstelle ausgestellt werden. Weitere Informationen zur Konfiguration von Zertifikaten finden Sie in der [Windows Server-Dokumentation](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den Bandbreitenanforderungen für Windows Virtual Desktop finden Sie unter [RDP-Bandbreitenanforderungen (Remotedesktopprotokoll)](rdp-bandwidth.md).
* Informationen zu den ersten Schritten mit Quality of Service (QoS) für Windows Virtual Desktop finden Sie unter [Implementieren von Quality of Service (QoS) für Windows Virtual Desktop](rdp-quality-of-service-qos.md).
