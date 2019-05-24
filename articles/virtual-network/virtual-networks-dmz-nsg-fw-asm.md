---
title: Umkreisnetzwerk-Beispiel – Erstellen eines Umkreisnetzwerks zum Schutz von Anwendungen mit einer Firewall und NSGs | Microsoft-Dokumentation
description: Erstellen eines Umkreisnetzwerks mit einer Firewall und Netzwerksicherheitsgruppen (NSGs)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: c1c64945aaa0bc4cd83cc769dab1c2a755896c01
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603423"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>Beispiel 2: Erstellen eines Umkreisnetzwerks zum Schutz von Anwendungen mit einer Firewall und NSGs
[Zurück zur Seite für Microsoft-Clouddienste und Netzwerksicherheit][HOME]

Dieses Beispiel zeigt die Erstellung eines Umkreisnetzwerks (auch als *DMZ*, *entmilitarisierte Zone* und *überwachtes Subnetz* bezeichnet) mit einer Firewall, vier Windows Server-Computern und Netzwerksicherheitsgruppen (NSGs). Es enthält Details zu jedem der relevanten Befehle, um ein besseres Verständnis der einzelnen Schritte zu vermitteln. Der Abschnitt „Datenverkehrsszenarien“ enthält eine Schritt-für-Schritt-Beschreiung der Bewegung von Datenverkehr durch die Verteidigungslinien im Umkreisnetzwerk. Der Abschnitt „Referenzen“ schließlich enthält den vollständigen Code sowie Anweisungen zum Aufbau dieser Umgebung, damit Sie verschiedene Szenarien testen und ausprobieren können.

![Eingehende Umkreisnetzwerk-Verbindungen mit virtuellem Netzwerkgerät und NSG][1]

## <a name="environment"></a>Environment 
Dieses Beispiel basiert auf einem Szenario mit einem Azure-Abonnement, das diese Elemente enthält:

* Zwei Clouddienste: „FrontEnd001“ und „BackEnd001“.
* Ein virtuelles Netzwerk mit dem Namen „CorpNetwork“, das zwei Subnetze aufweist: „FrontEnd“ und „BackEnd“.
* Eine einzelne NSG, die auf beide Subnetze angewendet wird.
* Ein virtuelles Netzwerkgerät: Eine Barracuda NextGen-Firewall, die mit dem FrontEnd-Subnetz verbunden ist.
* Einen Windows-Server, der einen Anwendungswebserver darstellt: „IIS01“.
* Zwei Windows Server-Computer, die Anwendungs-Back-End-Server darstellen: „AppVM01“ und „AppVM02“.
* Einen Windows Server-Computer, der einen DNS-Server darstellt: „DNS01“.

> [!NOTE]
> In diesem Beispiel wird zwar eine Barracuda NextGen-Firewall verwendet, es könnten aber viele virtuelle Netzwerkgeräte verwendet werden.
> 
> 

Mithilfe des PowerShell-Skripts im Abschnitt „Referenzen“ dieses Artikels lässt sich der größte Teil der hier beschriebenen Umgebung erstellen. Die VMs und virtuellen Netzwerke werden durch das Skript erstellt, diese Prozesse sind in diesem Dokument jedoch nicht im Detail beschrieben.

So erstellen Sie die Umgebung:

1. Speichern Sie die im Referenzabschnitt enthaltene und mit den Namen, den Speicherorten und den IP-Adressen für Ihr Szenario aktualisierte XML-Netzwerkkonfigurationsdatei.
2. Aktualisieren Sie die definierten Benutzervariablen im PowerShell-Skript gemäß der Umgebung, in der das Skript ausgeführt werden soll (Abonnements, Dienstnamen usw.).
3. Führen Sie das Skript in PowerShell aus.

> [!NOTE]
> Die im PowerShell-Skript angegebene Region muss mit der Region übereinstimmen, die in der Netzwerkkonfigurations-XML-Datei angegeben ist.
>
>

Nach der erfolgreichen Ausführung des Skripts können Sie diese Schritte ausführen:

1. Einrichten der Firewallregeln. Informationen dazu finden Sie im Abschnitt „Firewallregeln“ dieses Artikels.
2. Wenn Sie es wünschen, können Sie den Webserver und den App-Server mit einer einfachen Webanwendung einrichten, um Tests mit dieser Konfiguration des Umkreisnetzwerks zu ermöglichen. Sie können die zwei Anwendungsskripts verwenden, die im Abschnitt „Referenzen“ bereitstehen.

Im nächsten Abschnitt werden die meisten der Anweisungen im Skript erläutert, die sich auf NSGs beziehen.

## <a name="nsgs"></a>NSGs
In diesem Beispiel wird eine Netzwerksicherheitsgruppe (NSG) erstellt und dann mit sechs Regeln geladen.

> [!TIP]
> Im Allgemeinen sollten Sie zuerst die spezifischeren Regeln zum Zulassen von Aktionen und danach die allgemeineren Regeln zum Ablehnen von Aktionen erstellen. Die zugewiesene Priorität steuert, welche Regeln zuerst ausgewertet werden. Sobald Datenverkehr erkannt wird, auf den eine Regel zutrifft, werden keine weiteren Regeln ausgewertet. NSG-Regeln können sowohl in eingehender als auch in ausgehender Richtung (aus Sicht des Subnetzes) angewendet werden.
> 
> 

Deklarativ werden diese Regeln für eingehenden Datenverkehr erstellt:

1. Interner DNS-Datenverkehr (Port 53) wird zugelassen.
2. RDP-Datenverkehr (Port 3389) aus dem Internet zu jedem virtuellen Computer wird zugelassen.
3. HTTP-Datenverkehr (Port 80) über das Internet an das virtuelle Netzwerkgerät (Firewall) wird zugelassen.
4. Der gesamte Datenverkehr (alle Ports) von IIS01 an AppVM01 wird zugelassen.
5. Der gesamte Datenverkehr (alle Ports) aus dem Internet in das gesamte virtuelle Netzwerk (beide Subnetze) wird abgelehnt.
6. Der gesamte Datenverkehr (alle Ports) vom FrontEnd-Subnetz zum BackEnd-Subnetz wird abgelehnt.

Wenn diese Regeln an jedes Subnetz gebunden sind und eine HTTP-Anforderung aus dem Internet an den Webserver eingeht, scheint sowohl Regel 3 (Zulassen) als auch Regel 5 (Verweigern) zu gelten. Da Regel 3 aber eine höhere Priorität hat, wird nur diese Regel angewendet. Regel 5 kommt nicht zur Anwendung. Die HTTP-Anforderung wird also zur Firewall durchgelassen.

Falls derselbe Datenverkehr versuchen würde, den DNS01-Server zu erreichen, würde Regel 5 (Verweigern) zuerst gelten. Für den Datenverkehr wird die Übergabe an den Server also nicht zugelassen. Regel 6 (Verweigern) verhindert die Kommunikation des FrontEnd-Subnetzes mit dem BackEnd-Subnetz (mit Ausnahme von zulässigem Datenverkehr gemäß den Regeln 1 und 4). Dadurch wird das BackEnd-Netzwerk geschützt für den Fall, dass ein Angreifer die Webanwendung auf dem FrontEnd gefährdet. In diesem Fall hätte der Angreifer eingeschränkten Zugriff auf das „geschützte“ BackEnd-Netzwerk. (Der Angreifer könnte nur auf die auf dem AppVM01-Server verfügbar gemachten Ressourcen zugreifen.)

Es gibt eine Standardregel für ausgehenden Datenverkehr, die das Senden von Datenverkehr an das Internet zulässt. In diesem Beispiel wird ausgehender Datenverkehr zugelassen, und es werden keine Ausgangsregeln geändert. Um den Datenverkehr in beide Richtungen abzusichern, ist benutzerdefiniertes Routing erforderlich. Mehr über diese Technik erfahren Sie in einem anderen Beispiel im [Hauptdokument zu Sicherheitgrenzen][HOME].

Die hier beschriebenen NSG-Regeln weisen eine Ähnlichkeit mit den NSG-Regeln unter [Beispiel 1 – Erstellen einer einfachen DMZ mit NSGs][Example1] auf. Arbeiten Sie die NSG-Beschreibung in diesem Artikel durch, und betrachten Sie die einzelnen NSG-Regeln und ihre Attribute im Detail.

## <a name="firewall-rules"></a>Firewallregeln
Sie müssen einen Verwaltungsclient auf einem Computer installieren, um die Firewall zu verwalten und die erforderlichen Konfigurationen zu erstellen. Informationen zur Verwaltung des Geräts finden Sie in der Dokumentation zu Ihrer Firewall (bzw. einem anderen virtuellen Netzwerkgerät). Im Rest dieses Abschnitts wird die Konfiguration der eigentlichen Firewall über den Verwaltungsclient des Anbieters beschrieben (d.h. nicht über das Azure-Portal oder PowerShell).

Anweisungen zum Herunterladen des Clients und Herstellen einer Verbindung mit der in diesem Beispiel verwendeten Barracuda-Firewall finden Sie unter [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin).

Sie müssen die Weiterleitungsregeln der Firewall erstellen. Da im Szenario in diesem Beispiel nur eingehender Internetdatenverkehr an die Firewall und dann an den Webserver geleitet wird, ist nur eine NAT-Weiterleitungsregel erforderlich. Für die Barracuda-Firewall in diesem Beispiel würde es sich bei der Regel um eine Destination NAT-Regel („Dst NAT“) zum Übergeben des Datenverkehrs handeln.

Führen Sie diese Schritte aus, um die folgende Regel zu erstellen (oder vorhandene Standardregeln zu überprüfen):
1. Wählen Sie im Barracuda NG Admin-Clientdashboard auf der Registerkarte „Konfiguration“ im Abschnitt **Operational Configuration** (Betriebskonfiguration) **Ruleset** (Regelsatz) aus. 

   In einem Raster namens **Main Rules** (Hauptregeln) werden die vorhandenen aktiven und deaktivierten Regeln der Firewall angezeigt.

2. Um eine neue Regel zu erstellen, wählen Sie die kleine grüne **+**-Schaltfläche in der oberen rechten Ecke des Rasters aus. (Möglicherweise ist die Firewall gesperrt. Wenn Sie eine Schaltfläche mit der Bezeichnung **Lock** (Sperre) sehen und nicht imstande sind, Regeln zu erstellen oder zu bearbeiten, wählen Sie die Schaltfläche aus, um den Regelsatz zu entsperren und die Bearbeitung zuzulassen.)
  
3. Um eine vorhandene Regel zu bearbeiten, klicken mit der rechten Maustaste darauf, und wählen Sie **Edit Rule** (Regel bearbeiten) aus.

Erstellen Sie eine neue Regel mit einem Namen wie **WebDatenverkehr.** 

Das Symbol für die Destination NAT-Regel sieht wie folgt aus:  ![Destination NAT-Symbol][2]

Die Regel selbst sieht ungefähr so aus:

![Firewallregel][3]

Alle eingehenden Adressen, die auf die Firewall treffen und für HTTP (Port 80 bzw. 443 für HTTPS) bestimmt sind, werden an die Schnittstelle „DHCP1 Local IP“ der Firewall gesendet und an den Webserver mit der IP-Adresse 10.0.1.5 umgeleitet. Da der Datenverkehr über Port 80 eingeht und über Port 80 an den Webserver geleitet wird, ist keine Portänderung erforderlich. Die Zielliste hätte aber 10.0.1.5:8080 lauten können, wenn der Webserver an Port 8080 lauschen würde, wodurch der eingehende Port 80 der Firewall in den eingehenden Port 8080 auf dem Webserver übersetzt werden würde.

Sie sollten auch eine Verbindungsmethode angeben. Für die Zielregel aus dem Internet ist „Dynamic SNAT" die am besten geeignete Methode.

Obwohl Sie nur eine Regel erstellt haben, ist es wichtig, deren Priorität richtig festzulegen. Wenn sich diese neue Regel im Raster aller Regeln der Firewall an unterster Stelle befindet (unterhalb der Regel „BLOCKALL“), wird sie niemals berücksichtigt. Achten Sie darauf, dass die neue Regel für den Webdatenverkehr oberhalb der Regel BLOCKALL angeordnet ist.

Nach dem Erstellen müssen Sie die Regel per Push auf die Firewall übertragen und sie dann aktivieren. Wenn Sie diese Schritte nicht ausführen, tritt die Regeländerung nicht in Kraft. Im nächsten Abschnitt werden der Push- und der Aktivierungsprozess beschrieben.

## <a name="rule-activation"></a>Regelaktivierung
Jetzt, da die Regel dem Regelsatz hinzugefügt wurde, müssen Sie den Regelsatz auf die Firewall hochladen und sie aktivieren.

![Aktivierung der Firewallregeln][4]

In der oberen rechten Ecke des Verwaltungsclients sehen Sie eine Gruppe von Schaltflächen. Wählen Sie **Send Changes** (Änderungen senden) aus, um den geänderten Regelsatz an die Firewall zu senden, und wählen Sie dann **Activate** (Aktivieren) aus.

Jetzt, nach der Aktivierung des Firewall-Regelsatzes, ist die Umgebung vollständig. Wenn Sie möchten, können Sie die Anwendungsbeispielskripts im Abschnitt „Referenzen“ ausführen, um der Umgebung eine Anwendung hinzuzufügen. Wenn Sie eine Anwendung hinzufügen, können Sie die im nächsten Abschnitt beschriebenen Szenarien zum Datenverkehr testen.

> [!IMPORTANT]
> Es sollte Ihnen bewusst sein, dass Sie nicht direkt auf den Webserver zugreifen. Wenn ein Browser eine HTTP-Seite von FrontEnd001.CloudApp.Net anfordert, übergibt der HTTP-Endpunkt (Port 80) den Datenverkehr an die Firewall, nicht an den Webserver. Die Firewall verwendet dann aufgrund der Regel, die Sie zuvor erstellt haben, NAT, um die Anforderung an den Webserver zuzuordnen.
> 
> 

## <a name="traffic-scenarios"></a>Datenverkehrsszenarien
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(Zugelassen) Web an Webserver über die Firewall
1. Ein Internetbenutzer fordert eine HTTP-Seite von FrontEnd001.CloudApp.Net (einem Internet-Clouddienst) an.
2. Der Clouddienst übergibt Datenverkehr über einen offenen Endpunkt an Port 80 an die lokale Firewallschnittstelle unter 10.0.1.4:80.
3. Das FrontEnd-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
   1. NSG-Regel 1 (DNS) trifft nicht zu. Mit der nächsten Regel fortfahren.
   2. NSG-Regel 2 (RDP) trifft nicht zu. Mit der nächsten Regel fortfahren.
   3. NSG-Regel 3 (Internet an Firewall) trifft zu. Datenverkehr zulassen. Verarbeitung von Regeln beenden.
4. Datenverkehr trifft an der internen IP-Adresse der Firewall ein (10.0.1.4).
5. Eine Firewall-Weiterleitungsregel bestimmt, dass es sich um Datenverkehr für Port 80 handelt und leitet diesen an den Webserver IIS01 um.
6. IIS01 lauscht auf Webdatenverkehr, empfängt die Anforderung und beginnt mit der Verarbeitung der Anforderung.
7. IIS01 fordert Informationen bei der SQL Server-Instanz auf AppVM01 an.
8. Es gibt keine Ausgangsregeln im FrontEnd-Subnetz, daher wird der Datenverkehr zugelassen.
9. Das BackEnd-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
   1. NSG-Regel 1 (DNS) trifft nicht zu. Mit der nächsten Regel fortfahren.
   2. NSG-Regel 2 (RDP) trifft nicht zu. Mit der nächsten Regel fortfahren.
   3. NSG-Regel 3 (Internet an Firewall) trifft nicht zu. Mit der nächsten Regel fortfahren.
   4. NSG-Regel 4 (IIS01 an AppVM01) trifft zu. Datenverkehr zulassen. Verarbeitung von Regeln beenden.
10. Die SQL Server-Instanz auf AppVM01 empfängt die Anforderung und antwortet.
11. Da es keine Ausgangsregeln im BackEnd-Subnetz gibt, wird die Antwort zugelassen.
12. Das FrontEnd-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
    1. Es gibt keine NSG-Regel für eingehenden Datenverkehr vom Bac-End- zum FrontEnd-Subnetz, daher trifft keine der NSG-Regeln zu.
    2. Da die Systemstandardregel Datenverkehr zwischen Subnetzen zulässt, wird der Datenverkehr zugelassen.
13. IIS01 empfängt die Antwort von AppVM01, erstellt die HTTP-Antwort und sendet sie an den Anforderer.
14. Da es sich um eine NAT-Sitzung von der Firewall handelt, wird als Antwortziel (zunächst) die Firewall verwendet.
15. Die Firewall empfängt die Antwort vom Webserver und leitet sie wieder an den Internetbenutzer zurück.
16. Da im FrontEnd-Subnetz keine Ausgangsregeln vorhanden sind, wird die Antwort zugelassen, und der Internetbenutzer empfängt die Webseite.

#### <a name="allowed-rdp-to-backend"></a>(Zulässig) RDP an BackEnd
1. Server Admin im Internet fordert eine RDP-Sitzung für AppVM01 an BackEnd001.CloudApp.Net:*xxxxx* an, wobei *xxxxx* für die zufällig zugewiesene Portnummer für RDP zu AppVM01 steht. (Sie finden den zugewiesenen Port im Azure-Portal oder mithilfe von PowerShell.)
2. Da die Firewall nur unter der Adresse FrontEnd001.CloudApp.Net lauscht, ist sie an diesem Datenverkehrsfluss nicht beteiligt.
3. Das BackEnd-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
   1. NSG-Regel 1 (DNS) trifft nicht zu. Mit der nächsten Regel fortfahren.
   2. NSG-Regel 2 (RDP) trifft zu. Datenverkehr zulassen. Verarbeitung von Regeln beenden.
4. Da keine Ausgangsregeln vorhanden sind, werden die Standardregeln angewendet, und der zurückkommende Datenverkehr wird zugelassen.
5. Die RDP-Sitzung wird aktiviert.
6. AppVM01 fordert einen Benutzernamen und ein Kennwort an.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Zugelassen) Webserver-DNS-Lookup auf DNS-Server
1. Der Webserver IIS01 benötigt einen Datenfeed von www.data.gov, muss jedoch die Adresse auflösen.
2. Die Netzwerkkonfiguration für das virtuelle Netzwerk zeigt DNS01 (10.0.2.4 im BackEnd-Subnetz) als den primären DNS-Server. IIS01 sendet die DNS-Anforderung an DNS01.
3. Da es keine Ausgangsregeln im FrontEnd-Subnetz gibt, wird der Datenverkehr zugelassen.
4. Das BackEnd-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
   1. NSG-Regel 1 (DNS) trifft zu. Datenverkehr zulassen. Verarbeitung von Regeln beenden.
5. Der DNS-Server empfängt die Anforderung.
6. Der DNS-Server hat die Adresse nicht im Cache und fragt sie bei einem DNS-Stammserver im Internet ab.
7. Da es keine Ausgangsregeln im BackEnd-Subnetz gibt, wird der Datenverkehr zugelassen.
8. Der Internet-DNS-Server antwortet. Da die Sitzung intern eingeleitet wurde, wird die Antwort zugelassen.
9. Der DNS-Server speichert die Antwort zwischen und antwortet auf die Anforderung von IIS01.
10. Da es keine Ausgangsregeln im BackEnd-Subnetz gibt, wird der Datenverkehr zugelassen.
11. Das FrontEnd-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
    1. Es gibt keine NSG-Regel für eingehenden Datenverkehr vom BackEnd- zum FrontEnd-Subnetz, daher trifft keine der NSG-Regeln zu.
    2. Die Systemstandardregel erlaubt Datenverkehr zwischen Subnetzen, daher wird der Datenverkehr zugelassen.
12. IIS01 empfängt die Antwort von DNS01.

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(Zugelassen) Webserver-Dateizugriff auf AppVM01
1. IIS01 fordert eine Datei auf AppVM01 an.
2. Da es keine Ausgangsregeln im FrontEnd-Subnetz gibt, wird der Datenverkehr zugelassen.
3. Das BackEnd-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
   1. NSG-Regel 1 (DNS) trifft nicht zu. Mit der nächsten Regel fortfahren.
   2. NSG-Regel 2 (RDP) trifft nicht zu. Mit der nächsten Regel fortfahren.
   3. NSG-Regel 3 (Internet an Firewall) trifft nicht zu. Mit der nächsten Regel fortfahren.
   4. NSG-Regel 4 (IIS01 an AppVM01) trifft zu. Datenverkehr zulassen. Verarbeitung von Regeln beenden.
4. AppVM01 empfängt die Anforderung und antwortet mit der Datei (unter der Annahme, dass der Zugriff autorisiert wird).
5. Da es keine Ausgangsregeln im BackEnd-Subnetz gibt, wird die Antwort zugelassen.
6. Das FrontEnd-Subnetz beginnt mit der Verarbeitung der eingehenden Regel:
   1. Es gibt keine NSG-Regel für eingehenden Datenverkehr vom BackEnd- zum FrontEnd-Subnetz, daher trifft keine der NSG-Regeln zu.
   2. Die Systemstandardregel erlaubt Datenverkehr zwischen Subnetzen, daher wird der Datenverkehr zugelassen.
7. IIS01 empfängt die Datei.

#### <a name="denied-web-direct-to-web-server"></a>(Verweigert) Web direkt an Webserver
Da sich der Webserver IIS01 und die Firewall in demselben Clouddienst befinden, haben beide die gleiche öffentliche IP-Adresse. Daher wird der gesamte HTTP-Datenverkehr an die Firewall geleitet. Eine Anforderung würde zwar erfolgreich bedient, sie kann aber nicht direkt an den Webserver gerichtet werden. Sie muss, wie vorgesehen, zuerst die Firewall passieren. Informationen zum Datenverkehrsfluss finden Sie im ersten Szenario dieses Abschnitts.

#### <a name="denied-web-to-backend-server"></a>(Verweigert) Web an BackEnd-Server
1. Ein Internetbenutzer versucht, über den BackEnd001.CloudApp.Net-Dienst auf eine Datei auf AppVM01 zuzugreifen.
2. Da keine Endpunkte für Dateifreigaben geöffnet sind, würde die Anforderung nicht durch den Clouddienst geleitet und daher den Server nicht erreichen.
3. Wenn die Endpunkte aus irgendeinem Grund offen sind, blockiert die NSG-Regel 5 (Internet an VNet) diesen Datenverkehr.

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(Verweigert) Web-DNS-Lookup auf dem DNS-Server
1. Ein Internetbenutzer versucht, über den BackEnd001.CloudApp.Net-Dienst auf DNS01 einen internen DNS-Eintrag nachzuschlagen.
2. Da keine Endpunkte für DNS geöffnet sind, wird die Anforderung nicht durch den Clouddienst geleitet und erreicht daher den Server nicht.
3. Wenn die Endpunkte aus irgendeinem Grund offen sind, blockiert die NSG-Regel 5 (Internet an VNet) diesen Datenverkehr. (Regel 1 [DNS] trifft aus zwei Gründen nicht zu. Erstens befindet sich die Quelladresse im Internet, und diese Regel trifft nur zu, wenn die Quelle im lokalen virtuellen Netzwerk liegt. Zweitens ist diese Regel eine Zulassungsregel, also kann sie niemals Datenverkehr verweigern.)

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(Verweigert) Webzugriff auf SQL über die Firewall
1. Ein Internetbenutzer fordert SQL-Daten von FrontEnd001.CloudApp.Net (einem Internet-Clouddienst) an.
2. Da keine Endpunkte für SQL geöffnet sind, wird die Anforderung nicht durch den Clouddienst geleitet und erreicht daher die Firewall nicht.
3. Wenn Endpunkte aus irgendeinem Grund geöffnet sind, beginnt das Fron-End-Subnetz mit der Verarbeitung der Eingangsregel:
   1. NSG-Regel 1 (DNS) trifft nicht zu. Mit der nächsten Regel fortfahren.
   2. NSG-Regel 2 (RDP) trifft nicht zu. Mit der nächsten Regel fortfahren.
   3. NSG-Regel 3 (Internet an Firewall) trifft zu. Datenverkehr zulassen. Verarbeitung von Regeln beenden.
4. Datenverkehr trifft an der internen IP-Adresse der Firewall ein (10.0.1.4).
5. Die Firewall verfügt nicht über Weiterleitungsregeln für SQL und verwirft den Datenverkehr.

## <a name="conclusion"></a>Zusammenfassung
Dieses Beispiel zeigt eine relativ einfache Möglichkeit zum Schützen Ihrer Anwendung mit einer Firewall und zum Isolieren des Back-End-Subnetzes vor eingehendem Datenverkehr.

Sie können weitere Beispiele und eine Übersicht über Netzwerk-Sicherheitsgrenzen [hier][HOME] finden.

## <a name="references"></a>Referenzen
### <a name="full-script-and-network-config"></a>Vollständiges Skript und Netzwerkkonfiguration
Speichern Sie das vollständige Skript in einer PowerShell-Skriptdatei. Speichern Sie das Skript der Netzwerkkonfiguration in einer Datei namens „NetworkConf2.xml“.
Ändern Sie die benutzerdefinierten Variablen nach Bedarf. Führen Sie das Skript aus, und befolgen Sie dann die Anweisungen im Abschnitt „Firewallregeln“ dieses Artikels.

#### <a name="full-script"></a>Vollständiges Skript
Dieses Skript führt auf der Grundlage der benutzerdefinierten Variablen die folgenden Schritte aus:

1. Herstellen einer Verbindung mit einem Azure-Abonnement.
2. Erstellen Sie ein Speicherkonto.
3. Erstellen eines virtuellen Netzwerks und zweier Subnetze, wie in der Netzwerkkonfigurationsdatei definiert.
4. Erstellen von vier Windows Server-VMs.
5. Konfigurieren der NSG. Bei der Konfiguration werden diese Schritte ausgeführt:
   * Erstellt eine NSG.
   * Füllt die NSG mit Regeln auf.
   * Bindet die Netzwerksicherheitsgruppe an die geeigneten Subnetze.

Sie sollten dieses PowerShell-Skript lokal auf einem mit dem Internet verbundenen PC oder Server ausführen.

> [!IMPORTANT]
> Bei der Ausführung des Skripts werden möglicherweise Warnungen und Informationsmeldungen in PowerShell angezeigt. Sie brauchen sich nur um Fehlermeldungen zu kümmern, die rot angezeigt werden.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>Netzwerkkonfigurationsdatei
Speichern Sie diese XML-Datei mit den aktualisierten Speicherorten, und fügen Sie dann einen Link zu dieser Datei in die $NetworkConfigFile-Variable im vorausgehenden Skript ein.

```xml
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>Beispielanwendungsskripts
Wenn Sie eine Beispielanwendung für dieses und weitere Beispiele zu Umkreisnetzwerken installieren möchten, sehen Sie sich das [Beispielanwendungsskript][SampleApp] an.

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Eingehende DMZ mit NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Symbol der Ziel-NAT-Regel"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Firewallregel"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Aktivierung der Firewallregeln"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
