---
title: Streamingendpunkte (Ursprung) in Azure Media Services | Microsoft-Dokumentation
description: In Azure Media Services stellt ein Streamingendpunkt (Ursprung) einen dynamischen Paketerstellungs- und Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt in einer Clientplayeranwendung oder einem Content Delivery Network (CDN) bereitstellen kann.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/27/2019
ms.author: juliako
ms.openlocfilehash: 3e1a5d8ba8b6d0ec8e3ec1ba9506a88ee87d8ed1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66515738"
---
# <a name="streaming-endpoints"></a>Streamingendpunkte 

In Microsoft Azure Media Services stellt ein [Streamingendpunkt](https://docs.microsoft.com/rest/api/media/streamingendpoints) einen dynamischen (Just-In-Time-)Paketerstellungs- und Ursprungsdienst dar, der Ihre Live- und On-Demand-Inhalte direkt in einer Clientplayeranwendung bereitstellen kann und dabei eines der allgemeinen Streamingmedienprotokolle (HLS oder DASH) verwendet. Zudem sorgt der **Streamingendpunkt** für eine dynamische (Just-In-Time-)Verschlüsselung zu branchenführenden DRMs.

Beim Erstellen eines Media Services-Kontos wird ein **Standard**-Streamingendpunkt mit dem Zustand „Beendet“ erstellt. Der **Standard**-Streamingendpunkt kann nicht gelöscht werden. Im Konto können zusätzliche Streamingendpunkt erstellt werden (siehe [Kontingente und Einschränkungen](limits-quotas-constraints.md)). 

> [!NOTE]
> Um das Streaming von Videos zu starten, muss der **Streamingendpunkt**, von dem aus Sie das Video streamen möchten, gestartet werden. 
>  
> Abgerechnet werden nur ausgeführte Streamingendpunkte.

## <a name="naming-convention"></a>Benennungskonvention

Für den Standardendpunkt: `{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

Für alle zusätzlichen Endpunkte: `{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>Typen  

Es gibt zwei **Streamingendpunkt**-Typen: **Standard** (Vorschau) und **Premium**. Der Typ wird durch die Anzahl der Skalierungseinheiten (`scaleUnits`) definiert, die Sie für den Streamingendpunkt zuordnen. 

Die Typen werden in der folgenden Tabelle beschrieben:  

|Type|Skalierungseinheiten|BESCHREIBUNG|
|--------|--------|--------|  
|**Standard**|0|Der standardmäßige Streamingendpunkt ist ein **Standard**-Typ, kann aber durch Anpassen der `scaleUnits` in den Premium-Typ geändert werden.|
|**Premium**|>0|**Premium**-Streamingendpunkte eignen sich für komplexere Workloads und bieten eine dedizierte und skalierbare Bandbreitenkapazität. Zum **Premium**-Typ wechseln Sie, indem Sie `scaleUnits` (Streamingeinheiten) anpassen. `scaleUnits` stellen eine dedizierte Ausgangskapazität bereit, die in Schritten von jeweils 200 MBit/s erworben werden kann. Bei Verwendung des **Premium**-Typs stellt jede aktivierte Einheit zusätzliche Bandbreitenkapazität für die Anwendung bereit. |

> [!NOTE]
> Für Kunden, die Inhalte für große Zielgruppen im Internet bereitstellen möchten, empfehlen wir, ein CDN auf dem Streamingendpunkt zu aktivieren.

SLA-Informationen finden Sie unter [Preise und SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Vergleichen von Streamingtypen

Feature|Standard|Premium
---|---|---
Erste 15 Tage kostenlos <sup>1</sup>| Ja |Nein
Throughput |Bis zu 600 MBit/s; kann einen deutlich höheren effektiven Durchsatz bereitstellen, wenn ein CDN verwendet wird.|200 Mbit/s pro Streamingeinheit. Kann einen deutlich höheren effektiven Durchsatz bereitstellen, wenn ein CDN verwendet wird.
CDN|Azure CDN, CDN eines Drittanbieters oder kein CDN.|Azure CDN, CDN eines Drittanbieters oder kein CDN.
Die Abrechnung erfolgt anteilsmäßig| Täglich|Täglich
Dynamische Verschlüsselung|Ja|Ja
Dynamische Paketerstellung|Ja|Ja
Skalieren|Automatische Skalierung bis zum Zieldurchsatz.|Zusätzliche SUs
IP-Filterung/G20/benutzerdefinierter Host <sup>2</sup>|Ja|Ja
Progressiver Download|Ja|Ja
Empfohlene Verwendung |Für den Großteil der Streamingszenarien empfohlen.|Professionelle Nutzung.

<sup>1</sup> Die kostenlose Testversion gilt nur für neu erstellte Media Services-Konten und den Standardstreamingendpunkt.<br/>
<sup>2</sup> Wird nur direkt am Streamingendpunkt verwendet, wenn das CDN nicht auf dem Endpunkt aktiviert ist.<br/>

## <a name="properties"></a>Eigenschaften 

Dieser Abschnitt enthält detaillierte Informationen zu einigen Streamingendpunkt-Eigenschaften. Beispiele zum Erstellen eines neuen Streamingendpunkts und Beschreibungen aller Eigenschaften finden Sie unter [Streamingendpunkte](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

- `accessControl` – Wird zum Konfigurieren der folgenden Sicherheitseinstellungen für den Streamingendpunkt verwendet: Authentifizierungsschlüssel und IP-Adressen im Signaturheader von Akamai, über die eine Verbindung mit dem Endpunkt zulässig ist.<br />Diese Eigenschaft kann nur festgelegt werden, wenn `cdnEnabled` auf FALSE festgelegt ist.
- `cdnEnabled` – Gibt an, ob die Azure CDN-Integration für den Streamingendpunkt aktiviert oder deaktiviert ist (standardmäßig deaktiviert). Wenn Sie `cdnEnabled` auf TRUE festlegen, werden die folgenden Konfigurationen deaktiviert: `customHostNames` und `accessControl`.
  
    Nicht alle Rechenzentren unterstützen die Azure CDN-Integration. Gehen Sie wie folgt vor, um zu überprüfen, ob die Azure CDN-Integration in Ihrem Rechenzentrum zur Verfügung steht:
 
  - Versuchen Sie, `cdnEnabled` auf TRUE festzulegen.
  - Überprüfen Sie das zurückgegebene Ergebnis auf einen `HTTP Error Code 412`-Fehler (PreconditionFailed) mit der Meldung „Streaming endpoint CdnEnabled property cannot be set to true as CDN capability is not available in the current region“ (Eigenschaft CdnEnabled des Streamingendpunkts kann nicht auf TRUE festgelegt werden, da die CDN-Funktion in der aktuellen Region nicht verfügbar ist). 

    Wenn dieser Fehler angezeigt wird, wird das CDN im Rechenzentrum nicht unterstützt. Versuchen Sie es mit einem anderen Rechenzentrum.
- `cdnProfile` – Wenn `cdnEnabled` auf TRUE festgelegt ist, können Sie auch `cdnProfile`-Werte übergeben. `cdnProfile` ist der Name des CDN-Profils, in dem der CDN-Endpunkt erstellt wird. Sie können eine vorhandene cdnProfile-Eigenschaft angeben oder eine neue verwenden. Wenn der Wert NULL ist und `cdnEnabled` auf TRUE festgelegt ist, wird der Standardwert „AzureMediaStreamingPlatformCdnProfile“ verwendet. Wenn die angegebene `cdnProfile`-Eigenschaft bereits vorhanden ist, wird ein Endpunkt unter dieser Eigenschaft erstellt. Wenn das Profil nicht vorhanden ist, wird automatisch ein neues Profil erstellt.
- `cdnProvider` – Wenn das CDN aktiviert ist, können Sie auch `cdnProvider`-Werte übergeben. `cdnProvider` steuert, welcher Anbieter verwendet wird. Derzeit werden drei Werte unterstützt: „StandardVerizon“, „PremiumVerizon“ und „StandardAkamai“. Wenn kein Wert angegeben wird und `cdnEnabled` auf TRUE festgelegt ist, wird „StandardVerizon“ verwendet (d. h. der Standardwert).
- `crossSiteAccessPolicies` – Wird zum Angeben von websiteübergreifenden Zugriffsrichtlinien für verschiedene Clients verwendet. Weitere Informationen finden Sie unter [Cross-domain policy file specification](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) (Dateispezifikation für domänenübergreifende Richtlinien) und [Making a Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx) (Verfügbarmachen eines Diensts über Netzwerkgrenzen hinweg).<br/>Die Einstellungen gelten nur für Smooth Streaming.
- `customHostNames` – Wird verwendet, um einen Streamingendpunkt so zu konfigurieren, dass an einen benutzerdefinierten Hostnamen gerichteter Datenverkehr akzeptiert wird.  Diese Eigenschaft gilt für Standard- und Premium-Streamingendpunkte und kann festgelegt werden, wenn `cdnEnabled` FALSE ist.
    
    Der Besitz des Domänennamens muss in Media Services bestätigt werden. In Media Services wird der Besitz des Domänennamens durch Anfordern eines `CName`-Eintrags überprüft, der die Media Services-Konto-ID als Komponente enthält, die der verwendeten Domäne hinzuzufügen ist. Beispiel: Damit „sports.contoso.com“ als benutzerdefinierter Hostname für den Streamingendpunkt verwendet wird, muss ein Eintrag für `<accountId>.contoso.com` so konfiguriert werden, dass er auf einen der Media Services-Überprüfungshostnamen verweist. Der Überprüfungshostname setzt sich aus „verifydns.\<mediaservices-dns-zone>“ zusammen. 

    Im Folgenden sind die erwarteten DNS-Zonen aufgeführt, die im Überprüfungseintrag für verschiedene Azure-Regionen zu verwenden sind.
  
  - Nordamerika, Europa, Singapur, Hongkong (SAR), Japan:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - China:
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Ein `CName`-Eintrag, der eine Zuordnung von „945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com“ zu „verifydns.media.azure.net“ vornimmt, beweist z. B., dass die Media Services-ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad den Besitz der Domäne contoso.com hat, sodass alle Namen unter contoso.com als benutzerdefinierter Hostname für einen Streamingendpunkt in diesem Konto verwendet werden können. Um den Wert der Mediendienst-ID zu suchen, wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Mediendienstkonto aus. Die **Konto-ID** wird oben rechts auf der Seite angezeigt.
        
    Wenn versucht wird, einen benutzerdefinierten Hostnamen festzulegen, ohne dass der `CName`-Eintrag ordnungsgemäß überprüft wird, treten bei der DNS-Antwort Fehler auf, und die Antwort wird dann einige Zeit zwischengespeichert. Nachdem ein entsprechender Eintrag eingerichtet wurde, kann es eine Weile dauern, bis die zwischengespeicherte Antwort erneut überprüft wird. Abhängig vom DNS-Anbieter der benutzerdefinierten Domäne kann die erneute Überprüfung des Eintrags zwischen einigen Minuten bis zu einer Stunde dauern.
        
    Zusätzlich zu dem `CName`-Eintrag, der die Zuordnung von `<accountId>.<parent domain>` zu `verifydns.<mediaservices-dns-zone>` definiert, müssen Sie einen anderen `CName`-Eintrag erstellen, der den benutzerdefinierten Hostnamen (z. B. `sports.contoso.com`) dem Hostnamen des Media Services-Streamingendpunkts zuordnet (z. B. `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Für Streamingendpunkte, die sich im selben Rechenzentrum befinden, kann nicht der gleiche benutzerdefinierte Hostname verwendet werden.

    Media Services unterstützt derzeit SSL mit benutzerdefinierten Domänen nicht. 
    
- `maxCacheAge` – Überschreibt den standardmäßigen Max-Age-HTTP-Cache Control Header, der vom Streamingendpunkt für Medienfragmente und On-Demand-Manifeste festgelegt wird. Der Wert wird in Sekunden festgelegt.
- `resourceState` -

    - Beendet – Der ursprüngliche Status eines Streamingendpunkts nach der Erstellung.
    - Wird gestartet – Der Streamingendpunkt geht über in den Status „Wird ausgeführt“.
    - Wird ausgeführt – Der Streamingendpunkt kann Inhalte an Clients streamen.
    - Wird skaliert – Die Skalierungseinheiten werden erhöht oder verringert.
    - Wird beendet – Der Streamingendpunkt geht über in den Status „Beendet“.
    - Wird gelöscht – Der Streamingendpunkt wird gelöscht.
    
- `scaleUnits` – Stellen eine dedizierte Ausgangskapazität bereit, die in Schritten von jeweils 200 MBit/s erworben werden kann. Wenn Sie zum **Premium**-Type wechseln möchten, passen Sie `scaleUnits` an.

## <a name="working-with-cdn"></a>Arbeiten mit dem CDN

In den meisten Fällen sollte das CDN aktiviert sein. Wenn Sie jedoch von einer maximalen Parallelität von weniger als 500 Benutzern ausgehen, wird empfohlen, das CDN zu deaktivieren, da sich das CDN am besten mit Parallelität skalieren lässt.

### <a name="considerations"></a>Überlegungen

* Der Streamingendpunkt `hostname` und die Streaming-URL ändern sich nicht, und zwar unabhängig davon, ob Sie CDN aktivieren oder nicht.
* Wenn Sie Ihre Inhalte mit oder ohne CDN testen können möchten, können Sie einen anderen Streamingendpunkt erstellen, für den kein CDN aktiviert ist.

### <a name="detailed-explanation-of-how-caching-works"></a>Ausführliche Erläuterung der Funktionsweise der Zwischenspeicherung

Beim Hinzufügen des CDN gilt kein spezifischer Bandbreitenwert, da die erforderliche Bandbreite für einen CDN-aktivierten Streamingendpunkt variieren kann. Vieles hängt ab von der Art des Inhalts, davon, wie beliebt dieser ist, sowie von Bitraten und den Protokollen. Im CDN wird nur der Inhalt zwischengespeichert, der angefordert wird. Das heißt, dass beliebter Inhalt direkt über das CDN bereitgestellt wird – wenn das Videofragment zwischengespeichert ist. Liveinhalte werden wahrscheinlich zwischengespeichert, da normalerweise viele Benutzer sich genau dasselbe ansehen. On-Demand-Inhalte können sich etwas schwieriger gestalten, da beliebte Inhalte und weniger beliebte Inhalte enthalten sein können. Bei Millionen von Videoobjekten, von denen keine beliebt sind (nur 1 oder 2 Benutzer pro Woche), jedoch Tausenden von Benutzern, die sich alle unterschiedlichen Videos ansehen, wird das CDN viel weniger effektiv. Mit diesen Cachefehlern erhöht sich die Last für den Streamingendpunkt.
 
Sie müssen zudem die Funktionsweise des adaptiven Streamings berücksichtigen. Jedes einzelne Videofragment wird als eigene Entität zwischengespeichert. Wenn ein Benutzer beispielsweise bei der ersten Wiedergabe eines bestimmten Videos Sequenzen überspringt und sich nur einige wenige Sekunden hier und dort ansieht, werden nur die vom Benutzer angesehenen Videofragmente im CDN zwischengespeichert. Mit dem adaptiven Streaming gibt es normalerweise 5 bis 7 unterschiedliche Videobitraten. Wenn ein Benutzer eine Bitrate ansieht und ein anderer Benutzer eine andere Bitrate, werden die Bitraten jeweils separat im CDN zwischengespeichert. Auch wenn zwei Benutzer die gleiche Bitrate ansehen, kann es sein, dass das Streaming über unterschiedliche Protokolle erfolgt. Jedes Protokoll (HLS, MPEG-DASH, Smooth Streaming) wird separat zwischengespeichert. Somit werden jede Bitrate und jedes Protokoll separat zwischengespeichert, und nur die Videofragmente, die angefordert wurden, werden zwischengespeichert.

### <a name="enable-azure-cdn-integration"></a>Aktivieren der Azure CDN-Integration

Nachdem ein Streamingendpunkt mit aktiviertem CDN bereitgestellt wurde, gibt es eine definierte Wartezeit auf Media Services, bevor das DNS-Update durchgeführt wird, um den Streamingendpunkt dem CDN-Endpunkt zuzuordnen.

Wenn Sie das CDN später deaktivieren bzw. aktivieren möchten, muss Ihr Streamingendpunkt den Zustand **Beendet** haben. Es kann bis zu zwei Stunden dauern, bis die Azure CDN-Integration aktiviert ist und die Änderungen auf allen CDN-POPs aktiv sind. Sie können jedoch Ihren Streamingendpunkt starten und von ihm aus unterbrechungsfrei streamen. Sobald die Integration abgeschlossen ist, wird der Stream vom CDN übermittelt. Während der Bereitstellungsphase hat Ihr Streamingendpunkt den Zustand **Wird gestartet**, und seine Leistung ist ggf. eingeschränkt.

Wenn der standardmäßige Streamingendpunkt erstellt wird, ist er standardmäßig mit Verizon Standard konfiguriert. Sie können Verizon Premium- oder Akamai Standard-Anbieter über REST-APIs konfigurieren. 

Die CDN-Integration ist in allen Azure-Rechenzentren mit Ausnahme der Regionen China und US-Regierung aktiviert.

> [!IMPORTANT]
> Die Azure Media Services-Integration in Azure CDN ist in **Azure CDN von Verizon**für Standard-Streamingendpunkte implementiert. Premium-Streamingendpunkte können mithilfe aller **Azure CDN-Tarife und -Anbieter** konfiguriert werden. Weitere Informationen zu Azure CDN-Features finden Sie in der [Übersicht über das Azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-has-been-made"></a>Bestimmen, ob eine DNS-Änderung vorgenommen wurde

Sie können mit https://www.digwebinterface.com feststellen, ob eine DNS-Änderung an einem Streamingendpunkt vorgenommen wurde (der Datenverkehr wird an das Azure CDN weitergeleitet). Wenn die Ergebnisse azureedge.net-Domänennamen in den Ergebnissen enthalten, wird der Datenverkehr nun auf das CDN geleitet.

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

Mit dem Beispiel [in diesem Repository](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) wird gezeigt, wie der Standard-Streamingendpunkt mit .NET gestartet wird.

