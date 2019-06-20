---
title: 'Behandeln von Problemen mit der Leistung des virtuellen Netzwerks: Azure | Microsoft-Dokumentation'
description: Diese Seite enthält eine standardisierte Methode zum Testen der Leistung der Azure-Netzwerkverbindung.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: 9ec310ffaa9d2bb297abde9341bf7b6c2dc763b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60883315"
---
# <a name="troubleshooting-network-performance"></a>Beheben von Problemen bei der Netzwerkleistung
## <a name="overview"></a>Übersicht
Azure bietet stabile und schnelle Möglichkeiten für Verbindungen über das lokale Netzwerk mit Azure. Methoden wie Site-to-Site-VPN und ExpressRoute werden von Groß- und Kleinkunden für ihre Unternehmen in Azure erfolgreich eingesetzt. Was passiert jedoch, wenn die Leistung nicht Ihren Erwartungen oder früheren Erfahrungen entspricht? Anhand der Informationen in diesem Dokument können Sie die Art und Weise standardisieren, wie Sie Ihre spezifische Umgebung testen und als Baseline definieren.

In diesem Dokument wird erläutert, wie Sie die Netzwerklatenz und Bandbreite zwischen zwei Hosts einfach und konsistent testen können. Es enthält zudem einige Hinweise zu unterschiedlichen Möglichkeiten der Untersuchung des Azure-Netzwerks und zum Isolieren von Problempunkten. Für das erörterte PowerShell-Skript und die entsprechenden Tools sind zwei Hosts im Netzwerk erforderlich (an beiden Enden der getesteten Verbindung). Ein Host muss ein Windows Server- oder Windows Desktop-Host sein, auf dem anderen kann Windows oder Linux ausgeführt werden. 

>[!NOTE]
>Das Vorgehen bei der Problembehandlung sowie die verwendeten Tools und Methoden können nach persönlichen Vorlieben gewählt werden. In diesem Dokument werden der Ansatz und die Tools erläutert, die ich häufig verwende. Ihr Ansatz unterscheidet sich wahrscheinlich. Nichts spricht gegen unterschiedliche Ansätze zur Behebung von Problemen. Wenn Sie jedoch noch keinen Ansatz etabliert haben, kann Ihnen dieses Dokument als Einstieg für eigene Methoden, Tools und Präferenzen bei der Behebung von Netzwerkproblemen dienen.
>
>

## <a name="network-components"></a>Netzwerkkomponenten
Bevor wir uns der Problembehandlung zuwenden, erörtern wir einige allgemeine Begriffe und Komponenten. Dadurch wird sichergestellt, dass jede Komponente in der durchgehenden Kette berücksichtigt wird, die Verbindungen in Azure ermöglicht.
[![1]][1]

Auf der obersten Ebene beschreibe ich drei wesentliche Netzwerkroutingdomänen:

- das Azure-Netzwerk (blaue Cloud rechts)
- das Internet oder WAN (grüne Cloud in der Mitte)
- das Unternehmensnetzwerk (orangefarbene Cloud links)

Die einzelnen Komponenten werden von rechts nach links kurz erläutert:
 - **Virtueller Computer:** Der Server verfügt möglicherweise über mehrere Netzwerkkarten (NICs). Stellen Sie sicher, dass alle statischen Routen, Standardrouten und Einstellungen des Betriebssystems Datenverkehr wie erwartet senden und empfangen. Zudem verfügt jede VM-SKU über eine Bandbreiteneinschränkung. Bei Verwendung einer kleineren VM-SKU wird der Datenverkehr durch die für die Netzwerkkarte verfügbare Bandbreite eingeschränkt. Ich verwende normalerweise einen virtuellen DS5v2-Computer zum Testen (und lösche ihn nach dem Testen, um Geld zu sparen), um ausreichend Bandbreite auf dem virtuellen Computer sicherzustellen.
 - **Netzwerkkarte:** Stellen Sie sicher, dass Sie die private IP-Adresse kennen, die der betreffenden Netzwerkkarte zugewiesen ist.
 - **NIC-NSG:** Auf NIC-Ebene werden möglicherweise spezifische Netzwerksicherheitsgruppen (NSGs) angewendet. Stellen Sie sicher, dass sich der NSG-Regelsatz für den weiterzuleitenden Datenverkehr eignet. Stellen Sie beispielsweise sicher, dass die Ports 5201 für iPerf, 3389 für RDP oder 22 für SSH geöffnet sind, sodass Testdatenverkehr weitergeleitet werden kann.
 - **VNET-Subnetz:** Die Netzwerkkarte ist einem bestimmten Subnetz zugewiesen. Stellen Sie sicher, dass Sie das Subnetz und die diesem Subnetz zugeordneten Regeln kennen.
 - **Subnetz-NSG:** Genau wie bei der Netzwerkkarte können Netzwerksicherheitsgruppen auch auf das Subnetz angewendet werden. Stellen Sie sicher, dass sich der NSG-Regelsatz für den weiterzuleitenden Datenverkehr eignet. (Bei eingehendem Datenverkehr an der Netzwerkkarte wird zuerst die Subnetz-NSG und dann die NIC-NSG angewendet. Im Gegensatz dazu wird bei ausgehendem Datenverkehr vom virtuellen Computer zuerst die NIC-NSG und dann die Subnetz-NSG angewendet.)
 - **Subnetz-UDR:** Benutzerdefinierte Routen (UDRs) können den Datenverkehr an einen zwischenzeitlichen Hop (z.B. eine Firewall oder einen Load Balancer) weiterleiten. Sie sollten wissen, ob für den Datenverkehr eine UDR festgelegt ist, und wenn ja, wohin die Weiterleitung erfolgt und wie sich der nächste Hop auf den Datenverkehr auswirkt. (Eine Firewall kann z.B. einen Teil des Datenverkehrs zulassen und anderen Datenverkehr zwischen den gleichen beiden Hosts verweigern.)
 - **Gatewaysubnetz/NSG/UDR:** Genau wie das VM-Subnetz kann das Gatewaysubnetz NSGs und UDRs aufweisen. Sie sollten wissen, ob sie vorhanden sind und wie sie sich auf den Datenverkehr auswirken.
 - **VNET-Gateway (ExpressRoute):** Nachdem das Peering (ExpressRoute) oder VPN aktiviert ist, gibt es nur wenige Einstellungen, die sich darauf auswirken, ob oder wie der Datenverkehr weitergeleitet wird. Wenn mehrere ExpressRoute-Verbindungen oder VPN-Tunnel mit dem gleichen VNET-Gateway verbunden sind, sollten Sie die Einstellungen für die Verbindungsgewichtung berücksichtigen, da dies sich auf die Verbindungseinstellung und den Pfad des Datenverkehrs auswirkt.
 - **Routenfilter** (nicht dargestellt): Ein Routenfilter gilt nur für das Microsoft-Peering in ExpressRoute, es muss jedoch überprüft werden, ob die im Microsoft-Peering erwarteten Routen angezeigt werden. 

Im Folgenden wird das WAN der Verbindung behandelt. Diese Routingdomäne kann Ihr Dienstanbieter, das Unternehmens-WAN oder das Internet sein. Viele bei diesen Verbindungen involvierte Hops, Technologien und Unternehmen können die Problembehandlung erschweren. Oftmals versuchen Sie, zunächst Azure sowie die Unternehmensnetzwerke auszuschließen, bevor Sie die verschiedenen Unternehmen und Hops prüfen.

In der Abbildung oben ist das Unternehmensnetzwerk ganz links dargestellt. Je nach Größe Ihres Unternehmens kann diese Routingdomäne einige Netzwerkgeräte zwischen Ihnen und dem WAN oder mehrere Ebenen von Geräten in einem Campus- oder Unternehmensnetzwerk umfassen.

Aufgrund der Komplexität dieser drei verschiedenen allgemeinen Netzwerkumgebungen empfiehlt es sich häufig, an den Rändern zu beginnen und herauszufinden, wo die Leistung gut ist und wo sie sich verschlechtert. Mit dieser Herangehensweise können Sie die problematische der drei Routingdomänen identifizieren und sich dann bei der Problembehandlung auf die entsprechende Umgebung konzentrieren.

## <a name="tools"></a>Tools
Die meisten Netzwerkprobleme können mit allgemeinen Tools wie Ping und Traceroute analysiert und isoliert werden. Nur in seltenen Fällen müssen Sie tiefer gehen und eine Paketanalyse wie z.B. Wireshark durchführen. Zur Unterstützung bei der Problembehandlung wurde das Azure Connectivity Toolkit (AzureCT) entwickelt. Es umfasst einige dieser Tools in einem einfachen Paket. Für die Leistungstests verwende ich gerne iPerf und PSPing. iPerf ist ein häufig verwendetes Tool, das unter den meisten Betriebssystemen ausgeführt wird. iPerf eignet sich für allgemeine Leistungstests und kann relativ einfach verwendet werden. PSPing ist ein von SysInternals entwickeltes Ping-Tool. PSPing stellt eine einfache Möglichkeit dar, ICMP- und TCP-Pings zusammen auszuführen. Auch die Verwendung der Befehle ist einfach. Bei beiden handelt es sich um einfache Tools, die lediglich durch Kopieren der Dateien in ein Verzeichnis auf dem Host „installiert“ werden.

Ich habe alle diese Tools und Methoden in einem PowerShell-Modul (AzureCT) zusammengefasst, das Sie installieren und verwenden können.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – das Azure Connectivity Toolkit
Das PowerShell-Modul AzureCT enthält zwei Komponenten: [Verfügbarkeitstests][Availability Doc] und [Leistungstests][Performance Doc]. Dieses Dokument befasst sich nur mit Leistungstests. Daher konzentrieren wir uns auf die zwei Befehle zur Verbindungsleistung in diesem PowerShell-Modul.

Die Verwendung des Toolkits für Leistungstests erfolgt in drei grundlegenden Schritten. 1) Installieren des PowerShell-Moduls; 2) Installieren der Hilfsanwendungen iPerf und PSPing; 3) Ausführen des Leistungstests.

1. Installieren des PowerShell-Moduls

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Mit diesem Befehl wird das PowerShell-Modul heruntergeladen und lokal installiert.

2. Installieren der Hilfsanwendungen
    ```powershell
    Install-LinkPerformance
    ```
    Mit diesem AzureCT-Befehl werden iPerf und PSPing in dem neuen Verzeichnis „C:\ACTTools“ installiert. Außerdem werden die Windows-Firewall-Ports geöffnet, um Datenverkehr über ICMP und Port 5201 (iPerf) zuzulassen.

3. Ausführen des Leistungstests

    Zunächst müssen Sie auf dem Remotehost iPerf im Servermodus installieren und ausführen. Stellen Sie zudem sicher, dass der Remotehost auf Port 3389 (RDP für Windows) oder Port 22 (SSH für Linux) lauscht und für iPerf Datenverkehr auf Port 5201 zulässt. Wenn der Remotehost ein Windows-Remotehost ist, installieren Sie das AzureCT, und führen Sie den Befehl „Install-LinkPerformance“ aus, um iPerf und die Firewallregeln einzurichten, die zum erfolgreichen Starten von iPerf im Servermodus erforderlich sind. 
    
    Öffnen Sie nach dem Einrichten des Remotecomputers PowerShell auf dem lokalen Computer, und starten Sie den Test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Mit diesem Befehl wird eine Reihe gleichzeitiger Auslastungs- und Latenztests ausgeführt, um die Bandbreitenkapazität und Latenz der Netzwerkverbindung zu schätzen.

4. Überprüfen der Ausgabe der Tests

    Das Format der PowerShell-Ausgabe sieht in etwa wie folgt aus:

    [![4]][4]

    Die detaillierten Ergebnisse aller iPerf- und PSPing-Tests befinden sich in einzelnen Textdateien im Verzeichnis der AzureCT-Tools unter „C:\ACTTools“.

## <a name="troubleshooting"></a>Problembehandlung
Wenn Sie mit dem Leistungstest nicht die erwarteten Ergebnisse erhalten, sollten die Gründe in einem schrittweisen Prozess untersucht werden. Aufgrund der Anzahl der Komponenten im Pfad führt eine systematische Herangehensweise im Allgemeinen schneller zu einer Lösung als ein unkoordiniertes Vorgehen und möglicherweise überflüssige Wiederholungen der gleichen Tests.

>[!NOTE]
>In diesem Szenario handelt es sich um ein Leistungsproblem und um kein Verbindungsproblem. Wenn überhaupt kein Datenverkehr weitergeleitet wird, werden andere Schritte ausgeführt.
>
>

Wägen Sie zunächst Ihre Annahmen ab. Ist Ihre Erwartung angemessen? Bei einer ExpressRoute-Verbindung mit 1 GBit/s und einer Latenz von 100 ms ist es nicht angemessen, angesichts der Leistungsmerkmale von TCP über Verbindungen mit hoher Latenz von einem Datenverkehr in vollem Umfang von 1 GBit/s auszugehen. Weitere Annahmen zur Leistung finden Sie im Abschnitt [Referenzen](#references).

Als Nächstes empfiehlt es sich, an den Rändern zwischen den Routingdomänen zu versuchen, das Problem auf eine einzelne wesentliche Routingdomäne einzugrenzen, d.h. auf das Unternehmensnetzwerk, das WAN oder das Azure-Netzwerk. Häufig wird die Ursache der „Blackbox“ im Pfad zugerechnet. Dies ist zwar einfach, kann jedoch die Problembehandlung erheblich verzögern, vor allem wenn das Problem eigentlich in einem Bereich auftritt, in dem Sie Änderungen vornehmen können. Leiten Sie das Problem erst an Ihren Dienstanbieter oder Internetdienstanbieter weiter, nachdem Sie es eingehend geprüft haben.

Nachdem Sie die wesentliche Routingdomäne identifiziert haben, in der das Problem aufzutreten scheint, sollten Sie ein Diagramm des betreffenden Bereichs erstellen. Verwenden Sie dazu ein Whiteboard, einen Editor oder Visio. Ein Diagramm bietet eine konkrete „Aktionskarte“ für ein methodisches Vorgehen, um das Problem weiter einzugrenzen. Sie können Testpunkte planen und die Karte aktualisieren, wenn Sie im Verlauf der Tests bestimmte Bereiche ausschließen oder in tiefere Bereiche vordringen.

Unterteilen Sie im erstellten Diagramm das Netzwerk in Segmente, und grenzen Sie das Problem ein. Finden Sie heraus, wo es funktioniert und wo nicht. Verändern Sie die Testpunkte fortlaufend, um die betreffende Komponente zu identifizieren.

Vergessen Sie auch nicht, andere Schichten des OSI-Modells zu überprüfen. Es genügt möglicherweise nicht, sich auf das Netzwerk und die Schichten 1–3 (Bitübertragungsschicht, Datenschicht und Vermittlungsschicht) zu konzentrieren, die Probleme können auch in der Anwendungsschicht (Schicht 7) auftreten. Bleiben Sie offen, und überprüfen Sie Ihre Annahmen.

## <a name="advanced-expressroute-troubleshooting"></a>Erweiterte ExpressRoute-Problembehandlung
Wenn Sie nicht sicher sind, wo sich der Rand der Cloud tatsächlich befindet, kann die Isolierung der Azure-Komponenten eine Herausforderung darstellen. Bei Verwendung von ExpressRoute ist der Rand die Netzwerkkomponente Microsoft Enterprise Edge (MSEE). **Bei Verwendung von ExpressRoute** ist MSEE der erste Kontaktpunkt in das Microsoft-Netzwerk und der letzte Hop beim Verlassen des Microsoft-Netzwerks. Wenn Sie ein Verbindungsobjekt zwischen dem VNET-Gateway und der ExpressRoute-Verbindung erstellen, stellen Sie eigentlich eine Verbindung mit MSEE her. Die Erkennung von MSEE als erster oder letzter Hop (abhängig von der jeweiligen Richtung) ist entscheidend zum Isolieren von Problemen im Azure-Netzwerk, entweder als Nachweis dafür, dass das Problem in Azure auftritt, oder um weiter im WAN oder im Unternehmensnetzwerk zu suchen. 

[![2]][2]

>[!NOTE]
> Beachten Sie, dass sich MSEE nicht in der Azure-Cloud befindet. ExpressRoute befindet sich am Rand des Microsoft-Netzwerks und nicht in Azure. Nachdem Sie mit ExpressRoute eine Verbindung mit einem MSEE hergestellt haben, wird eine Verbindung mit dem Microsoft-Netzwerk hergestellt. Von hier aus gelangen Sie zu allen Clouddiensten, z.B. zu Office 365 (mit Microsoft-Peering) oder Azure (mit privatem Peering und/oder Microsoft-Peering).
>
>

Wenn zwei VNETs (VNET A und B in der Abbildung) mit **derselben** ExpressRoute-Verbindung verbunden sind, können Sie eine Reihe von Tests ausführen, um das Problem in Azure zu isolieren (oder nachzuweisen, dass es nicht in Azure auftritt).
 
### <a name="test-plan"></a>Testplan
1. Führen Sie den Test „Get-LinkPerformance“ zwischen VM1 und VM2 aus. Dieser Test bietet einen Einblick, ob es sich um ein lokales Problem handelt. Wenn bei diesem Test akzeptable Latenz- und Bandbreitenergebnisse zurückgegeben werden, können Sie das lokale VNET als gut markieren.
2. Vorausgesetzt, dass der lokale VNET-Datenverkehr gut ist, führen Sie den Test „Get-LinkPerformance“ zwischen VM1 und VM3 aus. Bei diesem Test wird die Verbindung über das Microsoft-Netzwerk nach unten bis zum MSEE und zurück in Azure geprüft. Wenn bei diesem Test akzeptable Latenz- und Bandbreitenergebnisse zurückgegeben werden, können Sie das Azure-Netzwerk als gut markieren.
3. Wenn Azure ausgeschlossen werden konnte, können Sie eine ähnliche Testreihe für das Unternehmensnetzwerk durchführen. Wenn auch diese Tests gute Ergebnisse liefern, ist es an der Zeit, dass Sie sich an Ihren Dienstanbieter oder Internetdienstanbieter wenden, um eine Diagnose der WAN-Verbindung durchzuführen. Beispiel: Führen Sie diesen Test zwischen zwei Filialen oder zwischen Ihrem Computer und einem Server im Rechenzentrum durch. Abhängig davon, was Sie testen, suchen Sie Endpunkte (Server, PCs usw.), über die dieser Pfad geprüft werden kann.

>[!IMPORTANT]
> Es ist wichtig, dass Sie für jeden Test die Tageszeit notieren, zu der der Test durchgeführt wird, und dass Sie die Ergebnisse in einem allgemeinen Speicherort speichern (z.B. OneNote oder Excel). Jeder Testlauf sollte identische Ausgaben zurückgeben, sodass Sie die Ergebnisdaten der einzelnen Testläufe vergleichen können und in den Daten keine Lücken entstehen. Der Hauptgrund, warum ich AzureCT zur Problembehandlung verwende, ist die Konsistenz über mehrere Tests hinweg. Das Geheimnis liegt nicht in den exakten ausgeführten Auslastungsszenarien, sondern stattdessen *wichtig* ist die Tatsache, dass ich eine *konsistente Test- und Datenausgabe* in jedem einzelnen Test erhalte. Die Aufzeichnung der Zeit und konsistente Daten bei jeder Ausführung sind besonders nützlich, wenn sich später herausstellt, dass das Problem sporadisch auftritt. Wenn Sie im Vorfeld bei der Datenerfassung gewissenhaft sind, ersparen Sie sich viele Stunden, in denen Sie dieselben Szenarien wieder und wieder testen (das habe ich vor vielen Jahren am eigenen Leib erfahren müssen).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Das Problem ist isoliert, was nun?
Je mehr Sie das Problem eingrenzen können, desto einfacher lässt es sich beheben. Sie kommen jedoch häufig an einen Punkt, an dem Sie mit der Problembehandlung nicht tiefer vordringen können oder weiter kommen. Beispiel: Sie können sehen, dass die Verbindung über Ihren Dienstanbieter Hops in Europa umfasst, der erwartete Pfad liegt jedoch vollständig in Asien. An diesem Punkt sollten Sie sich Unterstützung holen. An wen Sie sich wenden, hängt davon ab, in welcher Routingdomäne Sie das Problem isoliert haben. Noch besser wäre es, wenn Sie das Problem auf eine bestimmte Komponente eingrenzen können.

Bei Problemen im Unternehmensnetzwerk kann die interne IT-Abteilung oder der Dienstanbieter des Netzwerks (möglicherweise der Hardwarehersteller) Ihnen möglicherweise bei der Gerätekonfiguration oder der Reparatur der Hardware helfen.

Bei Problemen im WAN kann das Weiterleiten Ihrer Testergebnisse an Ihren Dienstanbieter oder Internetdienstanbieter dazu beitragen, dass diese das Problem überprüfen, ohne die von Ihnen bereits getesteten Bereiche erneut prüfen zu müssen. Nehmen Sie es aber nicht persönlich, wenn sie Ihre Ergebnisse selbst überprüfen möchten. „Vertrauen ist gut – Kontrolle ist besser“ ist ein gutes Motto bei der Problembehandlung basierend auf den Testergebnissen von anderen Personen.

Bei Problemen in Azure sollte nach der möglichst detaillierten Isolierung des Problems die [Dokumentation zum Azure-Netzwerk][Network Docs] zurate gezogen und dann bei Bedarf [ein Supportticket geöffnet werden][Ticket Link].

## <a name="references"></a>Referenzen
### <a name="latencybandwidth-expectations"></a>Erwartungen im Hinblick auf Latenz und Bandbreite
>[!TIP]
> Die geografische Latenz (Meilen oder Kilometer) zwischen den getesteten Endpunkten macht bei Weitem den größten Teil der Latenz aus. Auch wenn Latenz zwischen Geräten (physische und virtuelle Komponenten, Anzahl der Hops usw.) zu verzeichnen ist, hat sich in Bezug auf WAN-Verbindungen die geografische Latenz als wichtigster Aspekt der gesamten Latenz erwiesen. Dabei ist auch zu beachten, dass es sich bei der Entfernung um die Entfernung der Faserstrecke und nicht um die Luftlinie oder die Entfernung nach Karte handelt. Diese Entfernung ist unglaublich schwierig genau zu bestimmen. Daher verwende ich im Allgemeinen einen Entfernungsrechner im Internet. Dabei bin ich mir bewusst, dass dies eine sehr ungenaue Methode ist, die für eine allgemeine Erwartung aber ausreicht.
>
>

Ich habe einen ExpressRoute-Dienst in Seattle, Washington in den USA eingerichtet. In der nachfolgenden Tabelle sind die Werte für die Latenz und Bandbreite aufgeführt, die sich bei Tests von verschiedenen Azure-Standorten ergaben. Die geografische Entfernung zwischen jedem Ende des Tests habe ich geschätzt.

Testeinrichtung:
 - Ein physischer Server unter Windows Server 2016 mit einer Netzwerkkarte von 10 GBit/s ist mit einer ExpressRoute-Verbindung verbunden.
 - Eine ExpressRoute-Premium-Verbindung mit 10 GBit/s im identifizierten Standort ist mit privaten Peering aktiviert.
 - Ein Azure-VNET mit einem UltraPerformance-Gateway befindet sich in der angegebenen Region.
 - Ein virtueller DS5v2-Computer unter Windows Server 2016 befindet sich im VNET. Der virtuelle Computer war nicht in die Domäne eingebunden und wurde über das Azure-Standardimage erstellt (keine Optimierung oder Anpassung); AzureCT war installiert.
 - Alle Tests wurden mithilfe des AzureCT-Befehls „Get-LinkPerformance“ mit jeweils einem 5-Minuten-Auslastungstest bei jedem der sechs Testläufe ausgeführt. Beispiel:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Beim Datenfluss für jeden Test wurde die Last vom lokalen physischen Server (iPerf-Client in Seattle) bis zum virtuellen Azure-Computer (iPerf-Server in der aufgeführten Azure-Region) geleitet.
 - Die Daten in der Spalte „Latenz“ stammen vom Test „No Load“ (ein Test der TCP-Latenz ohne Ausführung von iPerf).
 - Die Daten der Spalte „Maximum Bandbreite“ stammen vom 16-TCP-Fluss-Auslastungstest mit einer Fenstergröße von 1 MB.

[![3]][3]

### <a name="latencybandwidth-results"></a>Ergebnisse für Latenz/Bandbreite
>[!IMPORTANT]
> Diese Zahlen dienen nur zur allgemeinen Referenz. Viele Faktoren beeinflussen die Latenz. Und obwohl diese Werte im Zeitverlauf im Allgemeinen konsistent sind, kann der Datenverkehr aufgrund bestimmter Bedingungen im Azure-Netzwerk oder dem Netzwerk der Dienstanbieter jederzeit über andere Pfade weitergeleitet werden, sodass dadurch die Latenz und Bandbreite beeinflusst werden. Grundsätzlich führen diese Änderungen zu keinen wesentlichen Unterschieden.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Location|Azure<br/>Region|Geschätzte<br/>Entfernung (km)|Latency|1 Sitzung:<br/>Bandbreite|Maximum<br/>Bandbreite|
| Seattle | USA, Westen 2        |    191 km |   5 ms | 262 MBit/s |  3,74 GBit/s |
| Seattle | USA (Westen)          |  1\.094 km |  18 ms |  82,3 MBit/s |  3,7 GBit/s |
| Seattle | USA (Mitte)       |  2\.357 km |  40 ms |  38,8 MBit/s |  2,55 GBit/s |
| Seattle | USA Süd Mitte |  2\.877 km |  51 ms |  30,6 MBit/s |  2,49 GBit/s |
| Seattle | USA Nord Mitte |  2\.792 km |  55 ms |  27,7 MBit/s |  2,19 GBit/s |
| Seattle | USA (Ost) 2        |  3\.769 km |  73 ms |  21,3 MBit/s |  1,79 GBit/s |
| Seattle | USA (Ost)          |  3\.699 km |  74 ms |  21,1 MBit/s |  1,78 GBit/s |
| Seattle | Japan, Osten       |  7\.705 km | 106 ms |  14,6 MBit/s |  1,22 GBit/s |
| Seattle | UK, Süden         |  7\.708 km | 146 ms |  10,6 MBit/s |   896 MBit/s |
| Seattle | Europa, Westen      |  7\.834 km | 153 ms |  10,2 MBit/s |   761 MBit/s |
| Seattle | Australien (Osten)   | 12\.484 km | 165 ms |   9,4 MBit/s |   794 MBit/s |
| Seattle | Asien, Südosten   | 12\.989 km | 170 ms |   9,2 MBit/s |   756 MBit/s |
| Seattle | Brasilien, Süden *   | 10\.930 km | 189 ms |   8,2 MBit/s |   699 MBit/s |
| Seattle | Indien (Süden)      | 12\.918 km | 202 ms |   7,7 MBit/s |   634 MBit/s |

\* Die Latenz für Brasilien ist ein gutes Beispiel dafür, dass sich die Luftlinie erheblich von der Entfernung der Faserstrecke unterscheiden kann. Ich würde erwarten, dass die Latenz ungefähr bei 160 ms liegt, tatsächlich sind es aber 189 ms. Dieser Unterschied gegenüber meiner Erwartung kann auf ein Netzwerkproblem in einem Bereich hindeuten, aber sehr wahrscheinlich verläuft die Faserstrecke nicht in einer direkten Linie durch Brasilien, sodass sich zusätzlich ca. 1.000 km zwischen Brasilien und Seattle ergeben.

## <a name="next-steps"></a>Nächste Schritte
1. Laden Sie das Azure Connectivity Toolkit von GitHub unter [https://aka.ms/AzCT][ACT].
2. Befolgen Sie die Anweisungen zum [Testen der Verbindungsleistung][Performance Doc].

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure-Netzwerkkomponenten"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute-Problembehandlung"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Leistungstestumgebung"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell-Ausgabe"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT
