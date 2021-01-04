---
title: Überlegungen zum Netzwerkbetrieb
description: Erfahren Sie etwas über den Netzwerkdatenverkehr in einer App Service-Umgebung (ASE) und das Festlegen von Netzwerksicherheitsgruppen und benutzerdefinierten Routen mit Ihrer ASE.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 07/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 91b6134e7c809a8af75aa1cf23523e352e0a1a0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997340"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Überlegungen zum Netzwerkbetrieb in einer App Service-Umgebung #

## <a name="overview"></a>Übersicht ##

 Die Azure [App Service-Umgebung][Intro] ist eine Bereitstellung von Azure App Service in einem Subnetz in Ihrem virtuellen Azure-Netzwerk (VNET). Es gibt zwei Bereitstellungstypen für eine App Service-Umgebung (ASE):

- **Externe ASE**: Macht die gehosteten Apps der ASE für eine über das Internet erreichbare IP-Adresse verfügbar. Weitere Informationen finden Sie unter [Erstellen einer externen ASE][MakeExternalASE].
- **ILB ASE**: Macht die gehosteten Apps der ASE für eine IP-Adresse in Ihrem VNet verfügbar. Der interne Endpunkt ist ein ILB (Internal Load Balancer, interner Lastenausgleich), daher der Name ILB-ASE. Weitere Informationen finden Sie unter [Erstellen und Verwenden einer ILB-ASE][MakeILBASE].

Alle App Service-Umgebungen – extern und ILB – besitzen eine virtuelle IP (VIP), die für eingehenden Verwaltungsdatenverkehr sowie bei Internetaufrufen aus der ASE als Absenderadresse verwendet wird. Die Aufrufe, die von einer ASE aus ins Internet erfolgen, verlassen das VNET über die der ASE zugewiesene VIP. Die öffentliche IP dieser VIP ist die Quell-IP für alle Aufrufe, die von der ASE ins Internet gehen. Wenn die Apps in Ihrer ASE Ressourcen in Ihrem VNet oder über ein VPN aufrufen, ist die Quell-IP eine der IPs in dem von Ihrer ASE verwendeten Subnetz. Da sich die ASE im VNet befindet, hat sie ohne zusätzliche Konfiguration auch Zugriff auf Ressourcen im VNet. Wenn das VNet mit Ihrem lokalen Netzwerk verbunden ist, verfügen die Apps in der ASE ohne zusätzliche Konfiguration über Zugriff auf die dort enthaltenen Ressourcen.

![Externe ASE][1] 

Wenn Sie über eine externe ASE verfügen, ist die öffentliche VIP auch der Endpunkt, auf den die ASE-Apps aufgelöst werden für:

* HTTP/S 
* FTP/S
* Webbereitstellung
* Remotedebuggen

![ILB ASE][2]

Wenn Sie über eine ILB-ASE verfügen, ist die Adresse der ILB der Endpunkt für HTTP/S, FTP/S, Webbereitstellung und Remotedebugging.

## <a name="ase-subnet-size"></a>ASE-Subnetzgröße ##

Die Größe des Subnetzes, das zum Hosten einer ASE verwendet wird, kann nach der ASE-Bereitstellung nicht mehr geändert werden.  Die ASE verwendet eine Adresse für jede Infrastrukturrolle sowie für jede Instanz eines isolierten App Service-Plans.  Zusätzlich werden im Azure-Netzwerk fünf Adressen für jedes erstellte Subnetz verwendet.  Eine ASE ohne App Service-Pläne verwendet 12 Adressen, bevor Sie eine App erstellen.  Wenn es sich um eine ILB-ASE handelt, werden vor dem Erstellen einer App in dieser ASE 13 Adressen verwendet. Wenn Sie Ihre ASE aufskalieren, werden Infrastrukturrollen jedes Vielfache von 15 und 20 Ihrer App Service-Planinstanzen hinzugefügt.

   > [!NOTE]
   > Im Subnetz kann sich ausschließlich die ASE befinden. Achten Sie darauf, einen Adressbereich auszuwählen, der auf künftiges Wachstum ausgelegt ist. Diese Einstellung kann später nicht geändert werden. Es wird eine Größe von `/24` mit 256 Adressen empfohlen.

Wenn Sie hoch- oder herunterskalieren, werden neue Rollen der entsprechenden Größe hinzugefügt und Ihre Workloads werden dann von der aktuellen Größe auf die Zielgröße migriert. Die ursprünglichen VMs werden erst nach der Migration der Workloads entfernt. Wenn Sie beispielsweise über eine ASE mit 100 ASP-Instanzen verfügen, gibt es einen bestimmten Zeitraum, in dem Sie die doppelte Anzahl von VMs benötigen.  Aus diesem Grund empfehlen wir die Verwendung von „/24“, um eventuelle Änderungen zu berücksichtigen.  

## <a name="ase-dependencies"></a>ASE-Abhängigkeiten ##

### <a name="ase-inbound-dependencies"></a>Eingehende ASE-Abhängigkeiten ###

Damit die App Service-Umgebung funktioniert, müssen die folgenden Ports geöffnet sein:

| Zweck | From | To |
|-----|------|----|
| Verwaltung | App Service-Verwaltungsadressen | ASE-Subnetz: 454, 455 |
|  Interne ASE-Kommunikation | ASE-Subnetz: Alle Ports | ASE-Subnetz: Alle Ports
|  Azure Load Balancer eingehend zulassen | Azure Load Balancer | ASE-Subnetz: 16001

Zwei weitere Ports können bei einem Portscan als offen angezeigt werden: 7654 und 1221. Diese antworten mit einer IP-Adresse und keinen weiteren Informationen. Sie können bei Bedarf blockiert werden. 

Zusätzlich zur Systemüberwachung ermöglicht der eingehende Verwaltungsdatenverkehr die Steuerung und Kontrolle der ASE. Die Quelladressen für diesen Datenverkehr sind im Dokument [ASE-Verwaltungsadressen][ASEManagement] aufgeführt. Die Netzwerksicherheitskonfiguration muss den Zugriff über die IP-Adressen der Verwaltung für die App Service-Umgebung an den Ports 454 und 455 zulassen. Wenn Sie den Zugriff von diesen Adressen blockieren, wird Ihr ASE fehlerhaft und dann entsprechend ausgesetzt. Der TCP-Datenverkehr, der über die Ports 454 und 455 eintrifft, muss wieder vom gleichen VIP ausgehen, sonst haben Sie ein asymmetrisches Routingproblem. 

Viele Ports im ASE-Subnetz werden für die Kommunikation zwischen internen Komponenten verwendet, und sie können sich ändern. Deshalb muss auf alle Ports im ASE-Subnetz aus dem ASE-Subnetz zugegriffen werden können. 

Für die Kommunikation zwischen dem Azure Load Balancer und dem ASE-Subnetz müssen mindestens die Ports 454, 455 und 16001 geöffnet sein. Port 16001 wird für Keep-Alive-Datenverkehr zwischen dem Load Balancer und der ASE verwendet. Wenn Sie eine ILB-ASE verwenden, können Sie den Datenverkehr auf die Ports 454, 455 und 16001 beschränken.  Wenn Sie eine externe ASE verwenden, müssen Sie die normalen App-Zugriffsports berücksichtigen.  

Darüber hinaus müssen Sie sich noch um die Anwendungsports kümmern:

| Zweck | Ports |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Remotedebuggen in Visual Studio  |  4020, 4022, 4024 |
|  Web Deploy-Dienst | 8172 |

Wenn Sie die Anwendungsports blockieren, könnte zwar Ihre ASE noch funktionieren, aber Ihre App möglicherweise nicht mehr.  Wenn Sie von der App zugewiesene IP-Adressen mit einer externen App Service-Umgebung verwenden, müssen Sie den gesamten Datenverkehr von den IP-Adressen zulassen, die Ihren Apps im Subnetz der App Service-Umgebung zugeordnet wurden. Dies gilt für die Ports, die im Portal der App Service-Umgebung auf der Seite mit den IP-Adressen angezeigt werden.

### <a name="ase-outbound-dependencies"></a>Ausgehende ASE-Abhängigkeiten ###

Eine ASE hängt beim ausgehenden Zugriff von mehreren externen Systemen ab. Viele dieser Systemabhängigkeiten werden über DNS-Namen festgelegt und lassen sich keiner festen IP-Adressengruppe zuordnen. Somit erfordert die ASE den ausgehenden Zugriff aus dem ASE-Subnetz auf alle externen IP-Adressen über eine Vielzahl von Ports. 

Die ASE kommuniziert über folgende Ports mit Adressen, auf die über das Internet zugegriffen werden kann:

| Verwendung | Ports |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL, Windows-Updates, Linux-Abhängigkeiten, Azure-Dienste | 80/443 |
| Azure SQL | 1433 | 
| Überwachung | 12000 |

Die ausgehenden Abhängigkeiten werden im Dokument zum [Sperren des ausgehenden Datenverkehrs der App Service-Umgebung](./firewall-integration.md) aufgeführt. Wenn die ASE nicht mehr auf ihre Abhängigkeiten zugreifen kann, ist sie nicht mehr funktionsfähig. Nach einer gewissen Zeit wird die ASE angehalten. 

### <a name="customer-dns"></a>Kunden-DNS ###

Wenn das VNet mit einem kundendefinierten DNS-Server konfiguriert ist, wird es von den Workloads des Mandanten verwendet. Die ASE verwendet Azure DNS zu Verwaltungszwecken. Wenn das VNET mit einem vom Kunden ausgewählten DNS-Server konfiguriert ist, muss dieser DNS-Server aus dem Subnetz erreichbar sein, in dem sich die ASE befindet.

Sie können die DNS-Auflösung Ihrer Web-App mit dem Konsolenbefehl *nameresolver* testen. Wechseln Sie zum Debugfenster in Ihrer SCM-Website für Ihre App oder zur App im Portal, und wählen Sie die Konsole aus. Über die Shell-Eingabeaufforderung können Sie den Befehl *nameresolver* zusammen mit dem DNS-Namen ausgeben, den Sie suchen möchten. Das Ergebnis, das Sie erhalten, ist identisch mit dem, das Ihre App bei gleicher Suche erhalten würde. Wenn Sie nslookup verwenden, wird stattdessen eine Suche mit Azure DNS durchgeführt.

Wenn Sie die DNS-Einstellung des VNet ändern, in dem sich Ihre ASE befindet, müssen Sie Ihre ASE neu starten. Um einen Neustart Ihrer ASE zu vermeiden, wird dringend empfohlen, dass Sie Ihre DNS-Einstellungen für Ihr VNet konfigurieren, bevor Sie die ASE erstellen.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portalabhängigkeiten ##

Neben den funktionalen Abhängigkeiten der ASE gibt es einige weitere zu beachtende Aspekte im Zusammenhang mit der Portalverwendung. Einige der Funktionen im Azure-Portal hängen vom direkten Zugriff auf den _SCM-Standort_ ab. Zu jeder App in Azure App Service gibt es zwei URLs. Die erste URL dient dem Zugriff auf Ihre App. Die zweite URL dient dem Zugriff auf den SCM-Standort, der auch als _Kudu-Konsole_ bezeichnet wird. Funktionen, die den SCM-Standort nutzen:

-   Webaufträge
-   Functions
-   Protokollstreaming
-   Kudu
-   Erweiterungen
-   Prozess-Explorer
-   Konsole

Bei Verwendung einer ILB-ASE ist der SCM-Standort von außerhalb des VNETs nicht zugänglich. Einige Features funktionieren nicht über das App-Portal, weil sie Zugriff auf den SCM-Standort einer App benötigen. Anstatt das Portal zu verwenden, können Sie eine direkte Verbindung mit dem SCM-Standort herstellen. 

Wenn Ihre ILB-ASE den Domänennamen *contoso.appserviceenvironment.net* aufweist und der App-Name *testapp* lautet, ist die App unter *testapp.contoso.appserviceenvironment.net* zu erreichen. Der entsprechende SCM-Standort ist unter *testapp.scm.contoso.appserviceenvironment.net* zu erreichen.

## <a name="ase-ip-addresses"></a>ASE-IP-Adressen ##

Eine ASE muss einige IP-Adressen berücksichtigen. Sie lauten wie folgt:

- **Öffentliche IP-Adresse für eingehenden Datenverkehr**: Für den App-Datenverkehr in einer externen ASE und für den Verwaltungsdatenverkehr sowohl in einer externen ASE als auch in einer ILB-ASE.
- **Öffentliche IP-Adresse für ausgehenden Datenverkehr**: Als „Absender“ für von der ASE ausgehende und das VNet verlassende Verbindungen, die nicht durch ein VPN weitergeleitet werden.
- **ILB IP-Adresse**: Die ILB-IP-Adresse ist nur in einer ILB-ASE vorhanden.
- **Von der App zugewiesene IP-basierte SSL-Adressen**: Kann nur in einer externen ASE verwendet werden, wenn gleichzeitig auch IP-basiertes SSL konfiguriert ist.

All diese IP-Adressen sind über die ASE-Benutzeroberfläche im Azure-Portal sichtbar. Wenn Sie eine ILB-ASE verwenden, wird die IP der ILB angezeigt.

   > [!NOTE]
   > Diese IP-Adressen werden nicht geändert, solange Ihr ASE aktiv bleibt.  Wenn Ihre ASE ausgesetzt und wiederhergestellt wird, ändern sich die von Ihrer ASE verwendeten Adressen. Die normale Ursache für die Aussetzung einer ASE ist, wenn Sie den eingehenden Verwaltungszugriff oder den Zugriff auf eine ASE-Abhängigkeit blockieren. 

![IP-Adressen][3]

### <a name="app-assigned-ip-addresses"></a>Von der App zugewiesene IP-Adressen ###

Mit einer externen ASE können Sie einzelnen Apps IP-Adressen zuweisen. Dies ist mit einer ILB-ASE nicht möglich. Weitere Informationen zum Konfigurieren einer eigenen IP-Adresse für Ihre App finden Sie unter [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS-Bindung in Azure App Service](../configure-ssl-bindings.md).

Wenn eine App über eine eigene IP-basierte SSL-Adresse verfügt, reserviert die ASE zwei Ports für die Zuordnung zu dieser IP-Adresse. Ein Port wird für den HTTP-Datenverkehr verwendet, während der andere Port für den HTTPS-Datenverkehr bestimmt ist. Diese Ports werden im Bereich „IP-Adressen“ der ASE-Benutzeroberfläche angezeigt. Der Datenverkehr muss diese Ports von der VIP aus erreichen können, da sonst nicht auf die Apps zugegriffen werden kann. Diese Anforderung ist beim Konfigurieren von Netzwerksicherheitsgruppen (NSGs) unbedingt zu berücksichtigen.

## <a name="network-security-groups"></a>Netzwerksicherheitsgruppen ##

[Netzwerksicherheitsgruppen][NSGs] bieten die Möglichkeit, den Netzwerkzugriff innerhalb eines virtuellen Netzwerks zu steuern. Wenn Sie das Portal verwenden, gibt es auf der niedrigsten Prioritätsstufe eine implizite Ablehnungsregel, durch die alles abgelehnt wird. Sie erstellen also Ihre eigenen Zulassungsregeln.

In einer ASE haben Sie keinen Zugriff auf die VMs, die zum Hosten der eigentlichen ASE verwendet werden. Diese sind in einem Microsoft-Abonnement enthalten. Wenn Sie den Zugriff auf die Apps in der ASE beschränken möchten, legen Sie im ASE-Subnetz Netzwerksicherheitsgruppen fest. Achten Sie dabei sorgfältig auf die ASE-Abhängigkeiten. Wenn Sie Abhängigkeiten blockieren, ist die ASE nicht mehr funktionsfähig.

NSGs können über das Azure-Portal und über PowerShell konfiguriert werden. Die folgenden Informationen beziehen sich auf das Azure-Portal. NSGs können Sie unter **Netzwerk** im Portal als Ressource der obersten Ebene erstellen.

Damit eine ASE funktioniert, müssen die erforderlichen Einträge in einer Netzwerksicherheitsgruppe zum Zulassen von Datenverkehr festgelegt werden:

**Eingehend**
* TCP aus dem IP-Diensttag „AppServiceManagement“ an den Ports 454 und 455
* TCP aus dem Lastenausgleich an Port 16001
* von ASE-Subnetz zu ASE-Subnetz an allen Ports

**Ausgehend**
* UDP an alle IPs über Port 53
* UDP an alle IPs an Port 123
* TCP an alle IPs an den Ports 80 und 443
* TCP an das IP-Diensttag `Sql` an Port 1433
* TCP an alle IPs an Port 12000
* an das ASE-Subnetz an allen Ports

Diese Ports umfassen nicht die Ports, die Ihre Apps zur erfolgreichen Verwendung benötigen. Beispielsweise kann es vorkommen, dass Ihre App einen MySQL-Server über Port 3306 aufrufen muss. Das Network Time Protocol (NTP) an Port 123 ist das vom Betriebssystem verwendete Zeitsynchronisierungsprotokoll. Die NTP-Endpunkte sind nicht spezifisch für App Services, können je nach Betriebssystem variieren und sind nicht in einer klar definierten Liste von Adressen enthalten. Um Zeitsynchronisierungsprobleme zu vermeiden, müssen Sie den UDP-Datenverkehr an alle Adressen an Port 123 zulassen. Der ausgehende TCP-Datenverkehr zu Port 12000 dient der Systemunterstützung und -analyse. Die Endpunkte sind dynamisch und nicht in einem klar definierten Satz von Adressen enthalten.

Die normalen App-Zugriffsports sind:

| Zweck | Ports |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Remotedebuggen in Visual Studio  |  4020, 4022, 4024 |
|  Web Deploy-Dienst | 8172 |

Wenn die Anforderungen in Bezug auf ein- und ausgehenden Datenverkehr berücksichtigt werden, sollten die Netzwerksicherheitsgruppen denen in diesem Beispiel ähneln. 

![Eingangssicherheitsregeln][4]

Eine Standardregel ermöglicht den IPs im VNet die Kommunikation mit dem ASE-Subnetz. Eine weitere Standardregel ermöglicht dem Lastenausgleich (auch als öffentliche VIP-Adresse bezeichnet) die Kommunikation mit der ASE. Sie können die Standardregeln anzeigen lassen, indem Sie neben dem Symbol **Hinzufügen** auf **Standardregeln** klicken. Wenn Sie vor den Standardregeln eine Ablehnungsregel für alles andere hinzufügen, unterbinden Sie den Datenverkehr zwischen der VIP und der ASE. Wenn Sie Datenverkehr aus dem VNet verhindern möchten, fügen Sie eine eigene Regel zum Zulassen von eingehendem Datenverkehr hinzu. Verwenden Sie eine auf AzureLoadBalancer festgelegte Quelle mit dem Ziel **Beliebig** und einem Portbereich von * *\** _. Da die NSG-Regel auf das ASE-Subnetz angewendet wird, müssen Sie kein spezifisches Ziel angeben.

Wenn Sie der App eine IP-Adresse zugewiesen haben, müssen Sie die Ports geöffnet halten. Sie können die Ports anzeigen lassen, indem Sie _ *App Service-Umgebung** > **IP-Adressen** auswählen.  

Alle Elemente in den folgenden Regeln für ausgehenden Datenverkehr sind mit Ausnahme des letzten Elements erforderlich. Durch sie ist der Netzwerkzugriff auf die weiter oben im vorliegenden Artikel beschriebenen ASE-Abhängigkeiten möglich. Wenn Sie diese sperren, funktioniert die ASE nicht mehr. Das letzte Listenelement ermöglicht der ASE die Kommunikation mit anderen Ressourcen in Ihrem VNet.

![Ausgangssicherheitsregeln][5]

Nach der Festlegung Ihrer NSGs müssen diese dem Subnetz zugewiesen werden, in dem sich Ihre ASE befindet. Wenn Sie das ASE-VNet oder -Subnetz nicht kennen, können Sie es sich auf der ASE-Portalseite anzeigen lassen. Um die NSG Ihrem Subnetz zuzuweisen, öffnen Sie die Benutzeroberfläche des Subnetzes, und wählen Sie die NSG aus.

## <a name="routes"></a>Routen ##

Bei der Tunnelerzwingung legen Sie Routen in Ihrem VNET so fest, dass der ausgehende Datenverkehr nicht direkt in das Internet, sondern beispielsweise an ein ExpressRoute-Gateway oder ein virtuelles Gerät geleitet wird.  Wenn Sie Ihre ASE auf diese Weise konfigurieren müssen, lesen Sie das Dokument unter [Konfigurieren Ihrer App Service-Umgebung mit erzwungenem Tunneling][forcedtunnel].  Dieses Dokument erläutert die verfügbaren Optionen zum Arbeiten mit ExpressRoute und Tunnelerzwingung.

Bei der Erstellung einer ASE im Portal erstellen wir auch einen Satz von Routentabellen im Subnetz, das mit der ASE erstellt wird.  Diese Routen weisen einfach an, ausgehenden Datenverkehr direkt an das Internet zu senden.  
Führen Sie diese Schritte aus, um die gleichen Routen manuell zu erstellen:

1. Öffnen Sie das Azure-Portal. Wählen Sie **Netzwerk** > **Routingtabellen** aus.

2. Erstellen Sie eine neue Routentabelle in derselben Region wie Ihr VNet.

3. Wählen Sie in der Benutzeroberfläche Ihrer Routentabelle **Routen** > **Hinzufügen**.

4. Legen Sie als **Typ des nächsten Hops** **Internet** und als **Adresspräfix** **0.0.0.0/0** fest. Wählen Sie **Speichern** aus.

    Folgendes sollte angezeigt werden:

    ![Funktionale Routen][6]

5. Wechseln Sie nach dem Erstellen der neuen Routentabelle in das Subnetz, in dem sich Ihre ASE befindet. Wählen Sie aus der Liste im Portal Ihre Routentabelle aus. Nachdem Sie die Änderung gespeichert haben, sollten die für Ihr Subnetz vermerkten NSGs und Routen angezeigt werden.

    ![NSGs und Routen][7]

## <a name="service-endpoints"></a>Dienstendpunkte ##

Dienstendpunkte ermöglichen Ihnen das Beschränken des Zugriffs auf mehrinstanzenfähige Dienste auf eine Gruppe von virtuellen Azure-Netzwerken und Subnetzen. Weitere Informationen zu Dienstendpunkten finden Sie in der Dokumentation zu [VNET-Dienstendpunkten][serviceendpoints]. 

Wenn Sie die Dienstendpunkte auf einer Ressource aktivieren, werden Routen erstellt, die eine höhere Priorität als alle anderen Routen haben. Wenn Sie in einer ASE mit erzwungenem Tunneling Dienstendpunkte für einen Azure-Dienst verwenden, wird für den Datenverkehr zu diesem Dienst kein Tunneling erzwungen. 

Wenn Dienstendpunkte in einem Subnetz mit einer Azure SQL-Instanz aktiviert werden, müssen für alle Azure SQL-Instanzen, mit denen aus diesem Subnetz Verbindungen hergestellt werden, Dienstendpunkte aktiviert sein. Falls Sie aus demselben Subnetz auf mehrere Azure SQL-Instanzen zugreifen möchten, ist es nicht möglich, dass Sie Dienstendpunkte nur auf einer Azure SQL-Instanz aktivieren, aber nicht auf einer anderen Instanz. In Bezug auf Dienstendpunkte verhält sich kein anderer Azure-Dienst so wie Azure SQL. Wenn Sie Dienstendpunkte mit Azure Storage aktivieren, sperren Sie den Zugriff auf diese Ressource aus Ihrem Subnetz. Der Zugriff auf andere Azure Storage-Konten ist aber auch dann möglich,wenn dafür keine Dienstendpunkte aktiviert wurden.  

![Dienstendpunkte][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md