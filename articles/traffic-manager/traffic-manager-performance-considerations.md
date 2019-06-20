---
title: Leistungsüberlegungen zu Azure Traffic Manager | Microsoft Docs
description: Grundlegendes zur Leistung von Traffic Manager und Testen der Leistung auf Websites bei Verwendung von Traffic Manager
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: allensu
ms.openlocfilehash: 315165677bd3186bb3bdc87ed688c426776569fc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071053"
---
# <a name="performance-considerations-for-traffic-manager"></a>Leistungsüberlegungen zu Traffic Manager

Auf dieser Seite werden Überlegungen zur Leistung bei der Verwendung von Traffic Manager erläutert. Stellen Sie sich folgendes Szenario vor:

Sie verfügen über Instanzen Ihrer Website in den Regionen USA, Westen und Ostasien. Bei einer der Instanzen ist die Integritätsprüfung für den Traffic Manager-Test fehlgeschlagen. Anwendungsdatenverkehr wird an die fehlerfreie Region geleitet. Dieser Failover wird erwartet, aber die Leistung kann aufgrund der Latenz ein Problem darstellen, nachdem der Datenverkehr jetzt zu einer weiter entfernten Region geleitet wird.

## <a name="performance-considerations-for-traffic-manager"></a>Leistungsüberlegungen zu Traffic Manager

Die einzige Leistungsbeeinträchtigung, die Traffic Manager für Websites haben kann, geschieht bei der ersten DNS-Suche. Eine DNS-Anforderung für den Namen Ihres Traffic Manager-Profils wird durch den DNS-Stammserver verarbeitet, der die Zone „trafficmanager.net“ hostet. Traffic Manager füllt und aktualisiert regelmäßig die regulären Microsoft DNS-Stammserver auf Grundlage Ihrer Traffic Manager-Richtlinie und der Testergebnisse. So erfolgt auch die erste DNS-Suche, ohne dass DNS-Abfragen an Traffic Manager gesendet werden.

Traffic Manager besteht aus mehreren Komponenten: DNS-Namenservern, einem API-Dienst, der Speicherebene, und einem Endpunkt-Überwachungsdienst. Wenn ein Fehler bei einer Traffic Manager-Dienstkomponente auftritt, hat dies keine Auswirkungen auf den DNS-Namen, der Ihrem Traffic Manager-Profil zugeordnet ist. Die Datensätze auf Microsoft DNS-Servern bleiben unverändert. Es findet jedoch keine Endpunktüberwachung und DNS-Aktualisierung statt. Aus diesem Grund kann Traffic Manager DNS nicht so aktualisieren, dass auf den Failoverstandort verwiesen wird, wenn der primäre Standort ausfällt.

Die DNS-Namensauflösung ist schnell, und Ergebnisse werden zwischengespeichert. Die Geschwindigkeit der ersten DNS-Suche hängt von den DNS-Servern ab, die der Client für die Namensauflösung verwendet. In der Regel kann ein Client eine DNS-Suche innerhalb von ~50 ms durchführen. Die Ergebnisse der Suche werden während der Zeit der DNS-Gültigkeitsdauer (Time-to-live, TTL) zwischengespeichert. Die standardmäßige Gültigkeitsdauer für Traffic Manager beträgt 300 Sekunden.

Der Datenverkehr fließt NICHT durch Traffic Manager. Sobald die DNS-Suche abgeschlossen ist verfügt der Client über eine IP-Adresse für eine Instanz Ihrer Website. Der Client stellt direkt mit dieser Adresse eine Verbindung her, und wird nicht durch Traffic Manager geleitet. Die Traffic Manager-Richtlinie, die Sie auswählen, hat keinen Einfluss auf die DNS-Leistung. Allerdings kann die Routingmethode „performance“ negative Auswirkungen auf die Anwendungserfahrung haben. Wenn Ihre Richtlinie zum Beispiel Datenverkehr aus Nordamerika zu einer in Asien gehosteten Instanz weiterleitet, kann die Netzwerklatenz für diese Sitzungen ein Leistungsproblem darstellen.

## <a name="measuring-traffic-manager-performance"></a>Messen der Leistung von Traffic Manager

Es gibt mehrere Websites, die Sie nutzen können, um die Leistung und das Verhalten eines Traffic Manager-Profils zu verstehen. Viele dieser Websites sind kostenlos, können aber Einschränkungen haben. Einige Websites bieten eine verbesserte Überwachung und Berichterstellung gegen eine Gebühr.

Die Tools dieser Websites messen DNS-Latenzen und zeigen aufgelöste IP-Adressen für Clientstandorte auf der ganzen Welt an. Die meisten dieser Tools führen keine Zwischenspeicherung der DNS-Ergebnisse durch. Deswegen zeigen diese Tools jedes Mal die komplette DNS-Suche, wenn ein Test ausgeführt wird. Wenn Sie von Ihrem eigenem Client aus Tests durchführen, sehen Sie die Leistung der kompletten DNS-Suche nur einmal während der Gültigkeitsdauer.

## <a name="sample-tools-to-measure-dns-performance"></a>Beispieltools zum Messen der DNS-Leistung

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS bietet viele Leistungstools. Mithilfe des DNS-Vergleichstools können Sie die Dauer der Auflösung Ihres DNS-Namen anzeigen lassen, und sehen, wie dieser Wert im Vergleich zu anderen DNS-Serviceanbietern abschneidet.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Eines der einfachsten Tools ist WebSitePulse. Geben Sie die URL ein, um DNS-Auflösungszeit, erstes Byte, letztes Byte und andere Leistungsstatistiken anzeigen zu lassen. Sie haben die Wahl zwischen drei verschiedenen Teststandorten. In diesem Beispiel können Sie sehen, dass die erste DNS-Suche bei der ersten Ausführung des Tests 0,204 Sekunden dauert.

    ![Impuls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Da die Ergebnisse zwischengespeichert werden, dauert die DNS-Suche beim zweiten Test auf dem gleichen Traffic Manager-Endpunkt 0,002 Sekunden.

    ![Impuls2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [CA App Synthetic Monitor](https://asm.ca.com/en/checkit.php)

    Diese Website wurde früher als „Watchmouse Check Website Tool“ bezeichnet, und zeigt Ihnen die DNS-Auflösungszeit aus mehreren geografischen Regionen gleichzeitig an. Wenn Sie die URL eingeben, werden die DNS-Auflösungszeit, die Verbindungszeit und die Verbindungsgeschwindigkeit aus mehreren geografischen Standorten angezeigt. Verwenden Sie diesen Test, um anzuzeigen, welcher gehostete Dienst von verschiedenen Standorten auf der ganzen Welt zurückgegeben wird.

    ![Impuls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Dieses Tool bietet Leistungsstatistiken für jedes Element einer Website. Die Registerkarte „Page Analysis“ zeigt die für die DNS-Suche aufgewendete Zeit in Prozent an.

* [What's My DNS?](https://www.whatsmydns.net/)

    Diese Website führt eine DNS-Suche von 20 verschiedenen geografischen Standorten aus durch, und zeigt die Ergebnisse auf einer Karte an.

* [Dig Web Interface](https://www.digwebinterface.com)

    Diese Website gibt detailliertere DNS-Informationen an, darunter CNAMEs- und A-Datensätze. Achten Sie darauf, dass Sie die Optionen „Colorize output“ und „Stats“ aktivieren, und unter „Nameservers“ die Option „All“ auswählen.

## <a name="next-steps"></a>Nächste Schritte

[Informationen zu Traffic Manager-Routingmethoden für Datenverkehr](traffic-manager-routing-methods.md)

[Testen der Traffic Manager-Einstellungen](traffic-manager-testing-settings.md)

[Vorgänge für Traffic Manager (REST-API-Referenz)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager-Cmdlets](https://docs.microsoft.com/powershell/module/az.trafficmanager)

