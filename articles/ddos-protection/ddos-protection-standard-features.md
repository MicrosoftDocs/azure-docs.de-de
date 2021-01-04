---
title: Azure DDoS Protection-Features
description: Informationen zu Azure DDoS Protection-Features
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 104c9dcd3b7fd931e4f54841c9de9d17cfd72353
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937320"
---
# <a name="azure-ddos-protection-standard-features"></a>Funktionen von Azure DDoS Protection Standard

Die folgenden Abschnitte zeigen die Hauptfeatures des Diensts Azure DDoS Protection Standard.

## <a name="always-on-traffic-monitoring"></a>Stets verfügbare Überwachung des Datenverkehrs

DDoS Protection Standard überwacht die tatsächliche Auslastung des Datenverkehrs und vergleicht sie ständig mit den Schwellenwerten der DDoS-Richtlinie. Bei Überschreitung des Schwellenwerts für den Datenverkehr wird die DDoS-Abwehr automatisch eingeleitet. Sinkt der Datenverkehr wieder unter den Schwellenwert, wird die Abwehr beendet.

![Abwehrfunktion von Azure DDoS Protection Standard](./media/ddos-protection-overview/mitigation.png)

Während des Abwehrvorgangs wird der an die geschützte Ressource gesendete Datenverkehr von DDoS Protection umgeleitet und mehrfach überprüft, wie etwa folgendermaßen:

- Sicherstellen, dass Pakete den Internetspezifikationen entsprechen und nicht falsch formatiert sind.
- Interagieren Sie mit dem Client, um zu bestimmen, ob es sich bei dem Datenverkehr möglicherweise um ein gefälschtes Paket handelt (Beispiel: SYN-Authentifizierung oder SYN-Cookie oder durch Verwerfen eines Pakets, damit die Quelle es erneut übermittelt).
- Übertragungsratenlimits für Pakete, wenn keine andere Erzwingungsmethode ausgeführt werden kann.

DDoS Protection sperrt Angriffsdatenverkehr und leitet den verbleibenden Datenverkehr an das vorgesehene Ziel weiter. Innerhalb weniger Minuten nach Angriffserkennung werden Sie mithilfe der Metriken von Azure Monitor benachrichtigt. Durch Konfigurieren der Protokollierung der DDoS Protection-Telemetrie können Sie für eine zukünftige Analyse die Protokolle in die verfügbaren Optionen schreiben. Metrische Daten werden in Azure Monitor für DDoS Protection Standard 30 Tage lang beibehalten.

## <a name="adaptive-real-time-tuning"></a>Adaptive Echtzeitoptimierung

Der Azure DDoS Protection Basic-Dienst schützt Kunden und verhindert Auswirkungen auf andere Kunden. Wenn z.B. ein Dienst für ein typisches Volumen legitimen eingehenden Datenverkehrs bereitgestellt wird, das kleiner ist als die *Triggerrate* der infrastrukturweiten DDoS Protection-Richtlinie, könnte ein DDoS-Angriff auf die Ressourcen dieses Kunden unbemerkt bleiben. Allgemeiner gesagt: Die komplexe Natur aktueller Angriffe (z.B. Multi-Vektor-DDoS), sowie das anwendungsspezifische Verhalten von Mandanten erfordern kundenspezifische, angepasste Schutzrichtlinien. Der Dienst führt diese Anpassung mithilfe von zwei Erkenntnissen durch:

- Automatische Ermittlung der Datenverkehrsmuster pro Kunde (pro IP) für Schichten 3 und 4

- Minimieren falsch positiver Ergebnisse – davon ausgehend, dass die Skalierung in Azure das Absorbieren signifikanter Datenverkehrsmengen erlaubt

![Diagramm der Arbeitsweise von DDoS Protection Standard mit Markierung der „Policy Generation“ (Generierung der Richtlinie)](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection: Telemetriedaten, Überwachung und Warnungen

DDoS Protection Standard stellt während der Dauer eines DDoS-Angriffs umfangreiche Telemetriedaten über [Azure Monitor](../azure-monitor/overview.md) zur Verfügung. Sie können Warnungen für alle Azure Monitor-Metriken konfigurieren, die DDoS Protection verwendet. Sie können die Protokollierung mit Splunk (Azure Event Hubs), Azure Monitor-Protokolle und Azure Storage für die erweiterte Analyse über die Schnittstelle für die Azure Monitor-Diagnose integrieren.

### <a name="ddos-mitigation-policies"></a>DDoS-Entschärfungsrichtlinien

Wählen Sie im Azure-Portal **Monitor** > **Metrik** aus. Wählen Sie im Bereich **Metrik** die Ressourcengruppe, den Ressourcentyp **Öffentliche IP-Adresse** und Ihre öffentliche Azure-IP-Adresse aus. DDoS-Metriken werden im Bereich **Verfügbare Metriken** angezeigt.

DDoS Protection Standard wendet drei automatisch optimierte Entschärfungsrichtlinien (TCP-SYN, TCP und UDP) für jede öffentliche IP-Adresse der geschützten Ressource in dem virtuellen Netzwerk an, für das DDoS aktiviert ist. Sie können die Schwellenwerte der Richtlinien anzeigen, indem Sie die Metrik **Inbound packets to trigger DDoS mitigation** (Eingehende Pakete zum Auslösen einer DDoS-Entschärfung) auswählen.

![Verfügbare Metriken und Metrikdiagramm](./media/ddos-best-practices/image-7.png)

Die Schwellenwerte der Richtlinien werden automatisch über unsere Profilerstellung für Netzwerkdatenverkehr konfiguriert, die auf Machine Learning basiert. Die DDoS-Entschärfung wird nur dann für eine IP-Adresse durchgeführt, die einem Angriff ausgesetzt ist, wenn der Richtlinienschwellenwert überschritten wird.

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metrik für eine IP-Adresse während eines DDoS-Angriffs

Wenn die öffentliche IP-Adresse einem Angriff ausgesetzt wird, ändert sich der Wert der Metrik **Unter DDoS-Angriff oder nicht** automatisch in 1, da DDoS Protection eine Entschärfung des Angriffsdatenverkehrs durchführt.

![Metrik und Diagramm „Unter DDoS-Angriff oder nicht“](./media/ddos-best-practices/image-8.png)

Es ist empfehlenswert, eine Warnung für diese Metrik zu konfigurieren. Dadurch werden Sie benachrichtigt, wenn eine aktive DDoS-Entschärfung für Ihre öffentliche IP-Adresse durchgeführt wird.

Weitere Informationen finden Sie unter [Verwalten des Azure DDoS-Standardschutzs mithilfe des Azure-Portals](manage-ddos-protection.md).

## <a name="web-application-firewall-for-resource-attacks"></a>Web Application Firewall für Ressourcenangriffe

Speziell für Ressourcenangriffe auf die Anwendungsschicht sollten Sie Web Application Firewall (WAF) konfigurieren, um Webanwendungen zu schützen. WAF untersucht eingehenden Webdatenverkehr, um SQL-Einfügungen, Cross-Site Scripting, DDoS und andere Schicht 7-Angriffe zu blockieren. Azure stellt [WAF als Feature von Application Gateway](../web-application-firewall/ag/ag-overview.md) bereit, das zentralisierten Schutz Ihrer Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken bietet. Über den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1) sind andere WAF-Angebote bei Azure-Partnern erhältlich, die möglicherweise für Ihre Anforderungen besser geeignet sind.

Auch Web Application Firewalls sind anfällig für volumetrische und Zustandserschöpfungsangriffe. Sie sollten zum Schutz vor volumetrischen Angriffen und Protokollangriffen unbedingt DDoS Protection Standard auf dem virtuellen WAF-Netzwerk aktivieren. Weitere Informationen finden Sie im Abschnitt [DDoS Protection – Referenzarchitekturen](ddos-protection-reference-architectures.md).

## <a name="protection-planning"></a>Planen des Schutzes

Planung und Vorbereitung sind entscheidend, um zu verstehen, wie ein System sich während eines DDoS-Angriffs verhalten wird. Zu diesen Schritten gehört auch das Erstellen eines Plans für Incident Management.

Sie sollten sicherstellen, dass DDoS Protection Standard für das virtuelle Netzwerk der Endpunkte aktiviert ist, die dem Internet zugewandt sind. Das Konfigurieren von DDoS-Warnungen hilft Ihnen, ständig ein wachsames Auge auf mögliche Angriffe auf Ihre Infrastruktur zu haben. 

Überwachen Sie Ihre Anwendungen separat. Es ist wichtig, das normale Verhalten einer Anwendung zu verstehen. Wenn die Anwendung sich während eines DDoS-Angriffs nicht erwartungsgemäß verhält, müssen Maßnahmen ergriffen werden. 

Erfahren Sie, wie Ihre Dienste auf einen Angriff reagieren werden, indem Sie [Simulationen testen](test-through-simulations.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [einen DDoS-Schutzplan erstellen](manage-ddos-protection.md).