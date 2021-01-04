---
title: Beschleunigung dynamischer Websites durch das Azure CDN
description: Azure CDN unterstützt die Optimierung zur Beschleunigung dynamischer Websites (Dynamic Site Acceleration, DSA) für Dateien mit dynamischen Inhalten.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 5387fdc224cd77ee5273767df5033a51dc27608c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008360"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Beschleunigung dynamischer Websites durch das Azure CDN

Die explosionsartige Verbreitung von sozialen Medien, E-Commerce und hyperpersonalisierten Webinhalten führt dazu, dass eine rasant zunehmende Menge von Bereitstellungsinhalten für Endbenutzer in Echtzeit generiert wird. Benutzer erwarten ein schnelles, zuverlässiges und personalisiertes Weberlebnis – unabhängig von ihrem Browser, Standort, Gerät oder Netzwerk. Genau jene Innovationen, die für diese hervorragenden Erfahrungen sorgen sollen, bringen jedoch auch lange Seitendownloadzeiten mit sich und beeinträchtigen die Qualität der Benutzererfahrung für den Kunden. 

Zu den Standardfunktionen eines CDN (Content Delivery Network) zählt die Möglichkeit, Dateien näher beim Endbenutzer zwischenzuspeichern, um die Übermittlung statischer Dateien zu beschleunigen. Bei dynamischen Webanwendungen ist die Zwischenspeicherung dieser Inhalte an Edgestandorten jedoch nicht möglich, da der Server die Inhalte als Reaktion auf das Benutzerverhalten generiert. Die Beschleunigung der Übermittlung derartiger Inhalte ist komplexer als konventionelle Edgezwischenspeicherung und erfordert eine Komplettlösung, die jedes Element entlang des gesamten Datenpfads vom Beginn bis zur Übermittlung exakt optimiert. Durch die Optimierung der Beschleunigung dynamischer Websites (Dynamic Site Acceleration, DSA) des Azure CDN wird die Leistung von Webseiten mit dynamischen Inhalten merklich verbessert.

Das **Azure CDN von Akamai** und das **Azure CDN von Verizon** ermöglichen beide während der Erstellung des Endpunkts über das Menü **Optimiert für** eine DSA-Optimierung. Beschleunigung dynamischer Websites von Microsoft wird über [Azure Front Door Service](../frontdoor/front-door-overview.md) angeboten.

> [!Important]
> Bei **Azure CDN von Akamai**-Profilen dürfen Sie nach deren Erstellung die Optimierung eines CDN-Endpunkts ändern.
>   
> Bei **Azure CDN von Verizon**-Profilen können Sie nach deren Erstellung die Optimierung eines CDN-Endpunkts nicht ändern.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>CDN-Endpunktkonfiguration zur Beschleunigung der Übermittlung dynamischer Dateien

Um einen CDN-Endpunkt für die Optimierung der Übermittlung von dynamischen Dateien zu konfigurieren, können Sie entweder das Azure-Portal oder die REST-APIs verwenden oder dies programmgesteuert über eines der Client-SDKs tun. 

**So konfigurieren Sie einen CDN-Endpunkt für die DSA-Optimierung mithilfe des Azure-Portals**

1. Klicken Sie auf der Seite **CDN-Profil** auf **Endpunkt**.

   ![Hinzufügen eines neuen CDN-Endpunkts](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Der Bereich **Endpunkt hinzufügen** wird angezeigt.

2. Wählen Sie unter **Optimiert für** die Option **Beschleunigung dynamischer Websites** aus.

    ![Erstellen eines neuen CDN-Endpunkts durch die DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Geben Sie für **Testpfad** einen gültigen Pfad zu einer Datei ein.

    Der Testpfad ist ein spezielles Feature für die DSA. Für die Erstellung ist ein gültiger Testpfad erforderlich. Bei der DSA wird eine kleine *Testpfad*-Datei verwendet, die zum Ursprungsserver hinzugefügt wird, um die Netzwerkroutingkonfiguration für das CDN zu optimieren. Sie können für die Testpfaddatei die Beispieldatei herunterladen und in Ihre Website hochladen, oder stattdessen eine vorhandene Ressource mit einer Größe von ungefähr 10 KB verwenden, sofern eine vorhanden ist.

4. Geben Sie die Werte für die erforderlichen Endpunktoptionen ein (weitere Informationen finden Sie unter [Erstellen eines neuen CDN-Endpunkts](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), und wählen Sie dann **Hinzufügen** aus.

   Nachdem der CDN-Endpunkt erstellt wurde, werden die DSA-Optimierungen auf alle Dateien angewendet, die bestimmte Kriterien erfüllen. 


**So konfigurieren Sie einen vorhandenen Endpunkt für die DSA (nur für das Azure CDN von Akamai-Profile)**

1. Wählen Sie auf der Seite **CDN-Profil** den Endpunkt aus, den Sie ändern möchten.

2. Wählen Sie im linken Bereich **Optimierung** aus. 

   Die Seite **Optimierung** wird angezeigt.

3. Wählen Sie unter **Optimiert für** die Option **Beschleunigung dynamischer Websites** und dann **Speichern** aus.

> [!Note]
> Für die Beschleunigung dynamischer Websites fallen zusätzliche Gebühren an. Weitere Informationen finden Sie unter [Azure Content Delivery Network – Preise](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Optimierung für die Beschleunigung dynamischer Websites mit Azure CDN

Bei der Beschleunigung dynamischer Websites mit dem Azure CDN wird die Übermittlung dynamischer Ressourcen durch folgende Methoden beschleunigt:

-   [Routenoptimierung](#route-optimization)
-   [TCP-Optimierungen](#tcp-optimizations)
-   [Objektvorabruf (nur das Azure CDN von Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptive Bildkomprimierung (nur das Azure CDN von Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Routenoptimierung

Die Routenoptimierung spielt eine entscheidende Rolle, da das Internet von Dynamik geprägt ist und Datenverkehr sowie vorübergehende Ausfälle die Netzwerktopologie stetig verändern. Das Border Gateway Protocol (BGP) ist das Internet-Standardroutingprotokoll, aber es gibt unter Umständen schnellere Routen über zwischengeschaltete PoP-Server (Point of Presence). 

Die Routenoptimierung wählt den optimalen Pfad zum Ursprung, damit eine Website durchgängig zugänglich ist und dynamische Inhalte Endbenutzern über die schnellstmögliche und zuverlässigste Route übermittelt werden. 

Das Akamai-Netzwerk nutzt Techniken zum Sammeln von Echtzeitdaten und Vergleichen verschiedener Pfade über unterschiedliche Knoten auf dem Akamai-Server. Darüber hinaus kommt auch die BGP-Standardroute innerhalb des öffentlichen Internets zum Einsatz, um die schnellste Route zwischen dem Ursprung und dem CDN-Edge zu bestimmen. Mit diesen Verfahren werden Internet-Staufallen und lange Routen vermieden. 

Auf ähnliche Weise wird im Verizon-Netzwerk eine Kombination aus Anycast DNS, Unterstützungs-PoPs mit hoher Kapazität und Integritätsprüfungen verwendet, um die besten Gateways zu ermitteln, die für die Weiterleitung von Daten vom Client zum Ursprung am ehesten geeignet sind.
 
Das Ergebnis: Vollständig dynamische und transaktionale Inhalt werden schneller und zuverlässiger an Endbenutzer übermittelt – selbst dann, wenn sie nicht zwischengespeichert werden können. 

### <a name="tcp-optimizations"></a>TCP-Optimierungen

Das Transmission Control-Protokoll (TCP) ist das Standardprotokoll der Internetprotokollfamilie, um Informationen zwischen Anwendungen in einem IP-Netzwerk zu übermitteln.  Für die Einrichtung einer TCP-Verbindung müssen standardmäßig mehrere Anforderungen hin- und hergeleitet werden. Zudem sind auch Grenzwerte erforderlich, um Überlastungspunkte im Netzwerk zu vermeiden, die zu Ineffizienzen bei der Skalierung führen. Beim **Azure CDN von Akamai** wird dieses Problem bewältigt, indem Optimierungen in drei Bereichen vorgenommen werden: 

 - [Beseitigen von langsamen TCP-Starts](#eliminating-tcp-slow-start)
 - [Nutzen von permanenten Verbindungen](#leveraging-persistent-connections)
 - [Anpassen von TCP-Paketparametern](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Beseitigen von langsamen TCP-Starts

Der *langsame Start von TCP* ist ein Algorithmus des Transmission Control-Protokolls (TCP), der eine Netzwerküberlastung verhindert, indem die Menge der über das Netzwerk gesendeten Daten beschränkt wird. Er beginnt mit kleinen Überlastungsfenstergrößen zwischen Absender und Empfänger, bis der Höchstwert erreicht oder ein Paketverlust erkannt wird.

 Sowohl das **Azure CDN von Akamai**-Profil als auch das **Azure CDN von Verizon**-Profil beseitigen langsame TCP-Starts durch die folgenden drei Schritte:

1. Die Integritäts- und Bandbreitenüberwachung wird eingesetzt, um die Bandbreite von Verbindungen zwischen PoP-Edgeservern zu messen.
    
2. Metriken werden von PoP-Edgeservern gemeinsam genutzt, sodass jeder Server über die Netzwerkbedingungen und die Serverintegrität der anderen PoP-Komponenten in der Nähe informiert ist.  
    
3. Die CDN-Edgeserver treffen Annahmen über einige Übertragungsparameter, z.B. über die optimale Fenstergröße, den Zeitpunkt, an dem die Kommunikation mit anderen CDN-Edgeservern in dessen Nähe stattfinden soll. Dieser Schritt bedeutet, dass die anfängliche Überlastungsfenstergröße erhöht werden kann, wenn die Integrität der Verbindung zwischen den CDN-Edgeservern höheren Paketdatenübertragungen standhalten kann.  

#### <a name="leveraging-persistent-connections"></a>Nutzen von permanenten Verbindungen

Mit einer CDN-Instanz stellen vergleichsweise weniger eindeutige Computergruppen eine direkte Verbindung mit Ihrem Ursprungsserver her als Benutzer. Das Azure CDN fasst Benutzeranforderungen zudem in Pools zusammen, um weniger Verbindungen mit dem Ursprungsserver herzustellen.

Wie bereits erwähnt sind mehrere Handshakeanforderungen erforderlich, um eine TCP-Verbindung herzustellen. Permanente Verbindungen, die vom HTTP-Header `Keep-Alive` implementiert werden, nutzen die Wiederverwendung von vorhandenen TCP-Verbindungen für mehrere HTTP-Anforderungen, um Roundtripzeiten zu sparen und die Übermittlung zu beschleunigen. 

Beim **Azure CDN von Verizon** werden außerdem regelmäßige Keep-Alive-Pakete über die TCP-Verbindung gesendet, um zu verhindern, dass eine offene Verbindung geschlossen wird.

#### <a name="tuning-tcp-packet-parameters"></a>Anpassen von TCP-Paketparametern

Das **Azure CDN von Akamai** passt Parameter an, die Verbindungen zwischen Servern regeln, und verringert die Menge langer Roundtrips, die erforderlich sind, um die auf der Website eingebetteten Inhalte abzurufen. Dies geschieht mit den folgenden Verfahren:

- Durch Vergrößern des anfänglichen Überlastungsfensters, damit eine größere Anzahl von Paketen gesendet werden kann, ohne auf eine Bestätigung zu warten.
- Durch Verringern des anfänglichen Neuübertragungstimeouts, damit ein Verlust schneller erkannt und die Übertragung beschleunigt wird.
- Durch Verringern des minimalen und maximalen Neuübertragungstimeouts, um die Wartezeit zu verkürzen, bevor die Annahme getroffen werden muss, dass bei der Übertragung Pakete verloren gegangen sind.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Objektvorabruf (nur das Azure CDN von Akamai)

Die meisten Websites bestehen aus einer HTML-Seite, die auf verschiedene andere Ressourcen wie Bilder und Skripts verweist. Wenn ein Client eine Webseite anfordert, lädt der Browser in der Regel zunächst das HTML-Objekt herunter und analysiert es. Anschließend werden zusätzliche Anforderungen an verknüpfte Ressourcen gesendet, die zum vollständigen Laden der Seite erforderlich sind. 

Der *Vorabruf* ist eine Methode zum Abrufen von Bildern und Skripts, die auf der HTML-Seite eingebettet sind, während die HTML-Seite im Browser verarbeitet wird und sogar bevor der Browser diese Objektanforderungen stellt. 

Sofern die Option „Vorabrufen“ aktiviert ist, wenn das CDN die HTML-Basisseite im Browser des Clients verarbeitet, analysiert das CDN die HTML-Datei, sendet zusätzliche Anforderungen für verknüpfte Ressourcen und speichert diese im jeweiligen Cache. Wenn der Client die Anforderungen für die verknüpften Ressourcen sendet, verfügt der CDN-Edgeserver bereits über die angeforderten Objekte und kann sie sofort ohne Roundtrip zum Ursprung verarbeiten. Diese Optimierung ist sowohl für zwischenspeicherbare als auch nicht zwischenspeicherbare Inhalte von Vorteil.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptive Bildkomprimierung (nur das Azure CDN von Akamai)

Bei einigen Geräten, insbesondere mobilen Geräten, kommt es bisweilen zu langsameren Netzwerkgeschwindigkeiten. In diesen Szenarien ist es für den Benutzer besser, schneller kleinere Bilder auf der Webseite zu sehen, als lange auf die Anzeige von Bildern mit voller Auflösung zu warten.

Diese Funktion überwacht automatisch die Netzwerkqualität und wendet bei langsamen Netzwerkgeschwindigkeiten standardmäßige JPEG-Komprimierungsmethoden an, um die Übermittlungszeit zu verkürzen.

Adaptive Bildkomprimierung | Dateierweiterungen  
--- | ---  
JPEG-Komprimierung | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Caching

Für die DSA ist die Zwischenspeicherung im CDN standardmäßig deaktiviert. Dies gilt auch, wenn vom Ursprung `Cache-Control`- oder `Expires`-Header in die Antwort eingefügt werden. Die DSA wird in der Regel bei dynamischen Objekten eingesetzt, die nicht zwischengespeichert werden sollten, da sie für jeden Client eindeutig sind. Das Zwischenspeichern kann dieses Verhalten hinfällig machen.

Wenn Sie über eine Website mit einer Mischung aus statischen und dynamischen Ressourcen verfügen, erzielen Sie die beste Leistung, indem Sie sich für einen Hybridansatz entscheiden. 

Sie können das Zwischenspeichern für das Profil **Azure CDN Standard von Verizon** und das Profil **Azure CDN Standard von Akamai** für bestimmte DSA-Endpunkte aktivieren, indem Sie [Cacheregeln](cdn-caching-rules.md) verwenden.

So greifen Sie auf Cacheregeln zu

1. Wählen Sie auf der Seite **CDN-Profil** unter „Einstellungen“ die Option **Cacheregeln**.  
    
    ![Schaltfläche für CDN-Cacheregeln](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Die Seite **Cacheregeln** wird geöffnet.

2. Erstellen Sie eine globale oder benutzerdefinierte Cacheregel, um das Zwischenspeichern für den DSA-Endpunkt zu aktivieren. 

Bei Profilen vom Typ **Azure CDN Premium von Verizon** können Sie das Zwischenspeichern für bestimmte DSA-Endpunkte mithilfe der [Regel-Engine](./cdn-verizon-premium-rules-engine.md) aktivieren. Alle Regeln, die erstellt werden, betreffen nur die für die DSA optimierten Endpunkte Ihres Profils. 

So greifen Sie auf die Regel-Engine zu
    
1. Klicken Sie auf der Seite **CDN-Profil** auf **Verwalten**.  
    
    ![Verwaltungsschaltfläche für CDN-Profile](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Das CDN-Verwaltungsportal wird geöffnet.

2. Wählen Sie im CDN-Verwaltungsportal **ADN** und dann **Regel-Engine** aus. 

    ![Regel-Engine für die DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternativ können Sie zwei CDN-Endpunkte verwenden: Ein mit der DSA optimierter Endpunkt zur Bereitstellung von dynamischen Ressourcen und ein anderer mit einem statischen Optimierungstyp, z.B. allgemeine Webbereitstellung, für die Bereitstellung von zwischenspeicherbaren Ressourcen. Ändern Sie Ihre Webseiten-URLs so, dass eine direkte Verknüpfung mit der Ressource für den CDN-Endpunkt hergestellt wird, den Sie verwenden möchten. 

Beispiel: `mydynamic.azureedge.net/index.html` ist eine dynamische Seite, die vom Endpunkt mit Beschleunigung dynamischer Websites geladen wird.    Die HTML-Seite verweist auf mehrere statische Ressourcen, z.B. JavaScript-Bibliotheken oder Images, die vom statischen CDN-Endpunkt geladen werden, z.B. `mystatic.azureedge.net/banner.jpg` und `mystatic.azureedge.net/scripts.js`.