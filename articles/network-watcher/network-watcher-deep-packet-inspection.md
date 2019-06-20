---
title: Paketuntersuchung mit Azure Network Watcher | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Network Watcher verwenden, um die Paketuntersuchung für Daten durchzuführen, die von einer VM gesammelt wurden.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 7f3fc69bbfd881a26ceb25705852558b66c60153
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64716902"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Paketuntersuchung mit Azure Network Watcher

Mit dem Feature für die Paketerfassung von Network Watcher können Sie Erfassungssitzungen auf Ihren Azure-VMs über das Portal, PowerShell, CLI und programmgesteuert per SDK und die REST-API initiieren und verwalten. Sie können die Paketerfassung für Szenarien einsetzen, für die Daten auf Paketebene erforderlich sind, indem Sie die Informationen in einem leicht verwendbaren Format bereitstellen. Indem Sie frei erhältliche Tools zum Untersuchen der Daten verwenden, können Sie die Kommunikation an und von Ihren VMs untersuchen und Erkenntnisse zum Netzwerkdatenverkehr gewinnen. Einige Beispiel für die Nutzung von Daten der Paketerfassung sind: Untersuchen von Netzwerk- oder Anwendungsproblemen, Erkennen von missbräuchlicher Nutzung des Netzwerks und von Eindringversuchen oder Einhalten der vorgeschriebenen Konformität. In diesem Artikel wird veranschaulicht, wie Sie eine von Network Watcher bereitgestellte Paketerfassungsdatei mit einem gängigen Open Source-Tool öffnen. Außerdem enthält der Artikel Beispiele dazu, wie Sie eine Verbindungswartezeit berechnen, ungewöhnlichen Datenverkehr identifizieren und Netzwerkstatistiken untersuchen.

## <a name="before-you-begin"></a>Voraussetzungen

Im Artikel werden einige vorkonfigurierte Szenarien einer zuvor durchgeführten Paketerfassung durchgegangen. Mit diesen Szenarien werden Funktionen veranschaulicht, auf die zugegriffen werden kann, indem eine Paketerfassung überprüft wird. In diesem Szenario wird [WireShark](https://www.wireshark.org/) zum Untersuchen der Paketerfassung verwendet.

Es wird davon ausgegangen, dass Sie bereits eine Paketerfassung für einen virtuellen Computer durchgeführt haben. Informationen dazu, wie Sie eine Paketerfassung erstellen, finden Sie unter [Verwalten von Paketerfassungen mit Azure Network Watcher über das Portal](network-watcher-packet-capture-manage-portal.md). Informationen zur Vorgehensweise mit REST finden Sie unter [Manage packet captures with Azure Network Watcher using Azure REST API](network-watcher-packet-capture-manage-rest.md) (Verwalten von Paketerfassungen mit Azure Network Watcher per Azure-REST-API).

## <a name="scenario"></a>Szenario

In diesem Szenario führen Sie Folgendes durch:

* Überprüfen einer Paketerfassung

## <a name="calculate-network-latency"></a>Berechnen der Netzwerkwartezeit

In diesem Szenario wird veranschaulicht, wie Sie die anfängliche Roundtripzeit (Round Trip Time, RTT) einer TCP-Konversation (Transmission Control Protocol) zwischen zwei Endpunkten anzeigen.

Bei der Herstellung einer TCP-Verbindung basieren die ersten drei Pakete, die für die Verbindung gesendet werden, auf einem Muster, das üblicherweise als Drei-Wege-Handshake bezeichnet wird. Indem die ersten beiden gesendeten Pakete dieses Handshake-Vorgangs untersucht werden (eine erste Anforderung vom Client und eine Antwort vom Server), können wir die Wartezeit bei der Herstellung der Verbindung berechnen. Diese Wartezeit wird als Roundtripzeit (Round Trip Time, RTT) bezeichnet. Weitere Informationen zum TCP-Protokoll und zum Drei-Wege-Handshake finden Sie unter der folgenden Ressource: [https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip )

### <a name="step-1"></a>Schritt 1

Starten Sie WireShark.

### <a name="step-2"></a>Schritt 2

Laden Sie die **CAP**-Datei aus Ihrer Paketerfassung. Sie finden diese Datei – je nach Konfiguration – in dem Blob, in dem sie lokal auf dem virtuellen Computer gespeichert wurde.

### <a name="step-3"></a>Schritt 3

Zum Anzeigen der anfänglichen Roundtripzeit (RTT) von TCP-Konversationen betrachten wir nur die ersten beiden Pakete, die am TCP-Handshake beteiligt sind. Wir verwenden die ersten beiden Pakete im Drei-Wege-Handshake, also die Pakete [SYN] und [SYN, ACK]. Sie sind nach Flags benannt, die im TCP-Header festgelegt sind. Das letzte Handshake-Paket ([ACK]) wird in diesem Szenario nicht verwendet. Das Paket [SYN] wird vom Client gesendet. Nachdem es empfangen wurde, sendet der Server das Paket [ACK] als Bestätigung dafür, dass [SYN] vom Client empfangen wurde. Aufgrund der Tatsache, dass für die Antwort des Servers nur sehr wenig Aufwand erforderlich ist, berechnen wir den RTT-Wert wie folgt: Wir subtrahieren die Zeit bis zum Empfang des Pakets [SYN, ACK] auf dem Client von der Zeit, die für das Senden des Pakets [SYN] vom Client benötigt wurde.

Bei Verwendung von WireShark wird dieser Wert für uns berechnet.

Damit wir die ersten beiden Pakete im Drei-Wege-Handshake (TCP) leichter anzeigen können, nutzen wir die von WireShark bereitgestellte Filterfunktion.

Erweitern Sie zum Anwenden des Filters in WireShark das Segment „Transmission Control Protocol“ eines [SYN]-Pakets in Ihrer Erfassung, und untersuchen Sie die im TCP-Header festgelegten Flags.

Da wir nach allen [SYN]- und [SYN, ACK]-Paketen filtern möchten, vergewissern wir uns unter „Flags“, dass das Syn-Bit auf 1 festgelegt ist. Anschließend klicken wir mit der rechten Maustaste auf das Syn-Bit und wählen „Apply as Filter“ (Als Filter anwenden) > „Selected“ (Ausgewählt) aus.

![Abbildung 7][7]

### <a name="step-4"></a>Schritt 4

Nachdem Sie das Fenster gefiltert haben und nur Pakete mit festgelegtem [SYN]-Bit angezeigt werden, können Sie Konversationen, die für Sie interessant sind, einfach auswählen, um den RTT-Anfangswert anzuzeigen. Ein einfacher Weg zum Anzeigen des RTT-Werts in WireShark ist das Klicken auf die Dropdownliste für die „SEQ/ACK“-Analyse. Der RTT-Wert wird angezeigt. In diesem Fall beträgt der RTT-Wert 0,0022114 Sekunden bzw. 2,211 ms.

![Abbildung 8][8]

## <a name="unwanted-protocols"></a>Unerwünschte Protokolle

Sie können auf der Instanz eines virtuellen Computers, den Sie in Azure bereitgestellt haben, eine größere Zahl von Anwendungen ausführen. Viele dieser Anwendungen kommunizieren unter Umständen aber ohne Ihre ausdrückliche Zustimmung über das Netzwerk. Indem wir die Paketerfassung zum Speichern der Netzwerkkommunikation verwenden, können wir untersuchen, wie die Anwendungen im Netzwerk kommunizieren, und etwaige Probleme ermitteln.

In diesem Beispiel überprüfen wir eine zuvor durchgeführte Paketerfassung auf unerwünschte Protokolle, die darauf hinweisen können, dass von einer auf dem Computer ausgeführten Anwendung eine nicht autorisierte Kommunikation ausgeht.

### <a name="step-1"></a>Schritt 1

Verwenden Sie die gleiche Erfassung wie im vorherigen Szenario, und klicken Sie auf **Statistics (Statistiken)**  > **Protocol Hierarchy (Protokollhierarchie)** .

![Menü „Protocol Hierarchy“ (Protokollhierarchie)][2]

Das Fenster „Protocol Hierarchy“ (Protokollhierarchie) wird angezeigt. Diese Ansicht enthält eine Liste mit allen Protokollen, die während der Erfassungssitzung verwendet wurden, und mit der Anzahl von Paketen, die mit den Protokollen übertragen und empfangen wurden. Diese Ansicht kann hilfreich sein, um auf Ihren virtuellen Computern oder im Netzwerk nach unerwünschtem Netzwerkdatenverkehr zu suchen.

![Protokollhierarchie geöffnet][3]

Im folgenden Screenshot ist zu sehen, dass mit dem BitTorrent-Protokoll Datenverkehr erzeugt wurde. Dieses Protokoll wird für den Peer-to-Peer-Austausch von Dateien verwendet. Als Administrator erwarten Sie keinen BitTorrent-Datenverkehr auf diesen virtuellen Computern. Da Sie nun über diesen Datenverkehr informiert sind, können Sie die Peer-to-Peer-Software entfernen, die auf diesem virtuellen Computer installiert ist, oder den Datenverkehr mit Netzwerksicherheitsgruppen oder einer Firewall blockieren. Außerdem können Sie die Paketerfassungen auch anhand eines Zeitplans durchführen, um die Protokollnutzung auf Ihren virtuellen Computern regelmäßig zu überprüfen. Ein Beispiel dazu, wie Sie Netzwerkaufgaben in Azure automatisieren können, finden Sie unter [Monitor VPN gateways with Network Watcher troubleshooting](network-watcher-monitor-with-azure-automation.md) (Überwachen von VPN Gateways per Network Watcher-Problembehandlung).

## <a name="finding-top-destinations-and-ports"></a>Ermitteln der am häufigsten verwendeten Ziele und Ports

Das Verständnis der Arten von Datenverkehr, der Endpunkte und der Ports, über die kommuniziert wird, ist wichtig, wenn Sie für Anwendungen und Ressourcen in Ihrem Netzwerk die Überwachung oder Problembehandlung durchführen. Mit einer Paketerfassungsdatei wie oben können wir schnell ermitteln, mit welchen Zielen die VM am häufigsten kommuniziert und welche Ports genutzt werden.

### <a name="step-1"></a>Schritt 1

Verwenden Sie die gleiche Erfassung wie im vorherigen Szenario, und klicken Sie auf **Statistics (Statistiken)**  > **IPv4 Statistics (IPv4-Statistiken)**  > **Destinations and Ports (Ziele und Ports)** .

![Fenster für Paketerfassung][4]

### <a name="step-2"></a>Schritt 2

Wenn wir uns die Ergebnisse ansehen, sticht eine Zeile hervor. Über Port 111 wurden mehrere Verbindungen hergestellt. Der am häufigsten genutzte Port war 3389 (Remotedesktop), und bei den restlichen Ports handelt es sich um dynamische RPC-Ports.

Dieser Datenverkehr ist zwar unter Umständen harmlos, aber es ist ein Port, der für viele Verbindungen genutzt wurde und dem Administrator nicht bekannt ist.

![Abbildung 5][5]

### <a name="step-3"></a>Schritt 3

Nachdem wir einen ungewöhnlichen Port ermittelt haben, können wir unsere Erfassung nach diesem Port filtern.

Der Filter lautet in diesem Fall wie folgt:

```
tcp.port == 111
```

Wir geben den obigen Filtertext in das Filtertextfeld ein und drücken die EINGABETASTE.

![Abbildung 6][6]

In den Ergebnissen ist zu sehen, dass der gesamte Datenverkehr von einem lokalen virtuellen Computer in demselben Subnetz stammt. Falls wir noch nicht verstehen, warum dieser Datenverkehr auftritt, können wir die Pakete weiter untersuchen und ermitteln, warum diese Aufrufe über Port 111 erfolgen. Wenn wir diese Informationen besitzen, können wir geeignete Maßnahmen einleiten.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich unter [Übersicht über die Azure-Netzwerküberwachung](network-watcher-monitoring-overview.md) über die anderen Diagnosefunktionen von Network Watcher.

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













