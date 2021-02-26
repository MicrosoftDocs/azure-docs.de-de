---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Pflichtfelder. Weitere Informationen finden Sie unter aka.ms/skyeye/meta.
Titel: Streamingendpunkte (Ursprung): Azure Media Services description: Erfahren Sie mehr über Streamingendpunkte (Ursprung), einen dynamischen Paket- und Streamingdienst, der Inhalte direkt an eine Clientplayer-App oder ein Content Delivery Network (CDN) übermittelt. services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: conceptual ms.date: 02/13/2020 ms.author: inhenkel
---

# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Streamingendpunkte (Ursprung) in Azure Media Services

In Microsoft Azure Media Services stellt ein [Streamingendpunkt](/rest/api/media/streamingendpoints) einen dynamischen (Just-In-Time-)Paketerstellungs- und Ursprungsdienst dar, der Ihre Live- und On-Demand-Inhalte direkt in einer Clientplayer-App bereitstellen kann und dabei eines der allgemeinen Streamingmedienprotokolle (HLS oder DASH) verwendet. Zudem sorgt der **Streamingendpunkt** für eine dynamische (Just-In-Time-)Verschlüsselung zu branchenführenden DRMs. 

Beim Erstellen eines Media Services-Kontos wird ein **Standard**-Streamingendpunkt mit dem Zustand „Beendet“ erstellt. Im Konto können weitere Streamingendpunkte erstellt werden (siehe [Kontingente und Grenzwerte](limits-quotas-constraints.md)).

> [!NOTE]
> Um das Streaming von Videos zu starten, muss der **Streamingendpunkt**, von dem aus Sie das Video streamen möchten, gestartet werden.
>
> Es werden nur ausgeführte Streamingendpunkte abgerechnet.

Lesen Sie auch das Thema zur [dynamischen Paketerstellung](dynamic-packaging-overview.md). 

## <a name="naming-convention"></a>Benennungskonvention

Das Format des Hostnamens der Streaming-URL ist: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, wobei `servicename` = der Streamingendpunktname oder der Liveereignisname.

Bei Verwendung des standardmäßigen Streamingendpunkts wird `servicename` ausgelassen, sodass die URL `{accountname}-{regionname}.streaming.azure.net` lautet.

### <a name="limitations"></a>Einschränkungen

* Der Streamingendpunktname darf maximal 24 Zeichen lang sein.
* Der Name sollte diesem [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference)-Muster folgen: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Typen

Es gibt zwei **Streamingendpunkt**-Typen: **Standard** (Vorschau) und **Premium**. Der Typ wird durch die Anzahl der Skalierungseinheiten (`scaleUnits`) definiert, die Sie für den Streamingendpunkt zuordnen.

Die maximale Grenze für die Streamingeinheit beträgt normalerweise 10. Kontaktieren Sie uns [hier](https://azure.microsoft.com/support/create-ticket/), um den Grenzwert für Ihr Konto zu erhöhen.

Die Typen werden in der folgenden Tabelle beschrieben:

|type|Skalierungseinheiten|BESCHREIBUNG|
|--------|--------|--------|  
|**Standard**|0|Der standardmäßige Streamingendpunkt ist ein **Standard**-Typ. Dieser kann jedoch durch Anpassen der `scaleUnits` in den Premium-Typ geändert werden.|
|**Premium**|>0|**Premium**-Streamingendpunkte eignen sich für komplexere Workloads und bieten eine dedizierte und skalierbare Bandbreitenkapazität. Zum **Premium**-Typ wechseln Sie, indem Sie `scaleUnits` (Streamingeinheiten) anpassen. `scaleUnits` stellen eine dedizierte Ausgangskapazität bereit, die in Schritten von jeweils 200 MBit/s erworben werden kann. Bei Verwendung des **Premium**-Typs stellt jede aktivierte Einheit zusätzliche Bandbreitenkapazität für die App bereit. |

> [!NOTE]
> Für Kunden, die Inhalte für große Zielgruppen im Internet bereitstellen möchten, empfehlen wir, ein CDN für den Streamingendpunkt zu aktivieren.

SLA-Informationen finden Sie unter [Preise und SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Vergleichen von Streamingtypen

Funktion|Standard|Premium
---|---|---
Throughput |Bis zu 600 MBit/s; kann einen deutlich höheren effektiven Durchsatz bereitstellen, wenn ein CDN verwendet wird.|200 Mbit/s pro Streamingeinheit. Kann einen deutlich höheren effektiven Durchsatz bereitstellen, wenn ein CDN verwendet wird.
CDN|Azure CDN, CDN eines Drittanbieters oder kein CDN.|Azure CDN, CDN eines Drittanbieters oder kein CDN.
Die Abrechnung erfolgt anteilsmäßig| Täglich|Täglich
Dynamische Verschlüsselung|Ja|Ja
Dynamische Paketerstellung|Ja|Ja
Skalieren|Automatische Skalierung bis zum Zieldurchsatz.|Zusätzliche SUs
IP-Filterung/G20/benutzerdefinierter Host <sup>1</sup>|Ja|Ja
Progressiver Download|Ja|Ja
Empfohlene Verwendung |Für den Großteil der Streamingszenarien empfohlen.|Professionelle Nutzung.

<sup>1</sup> Wird nur direkt für den Streamingendpunkt verwendet, wenn das CDN nicht für den Endpunkt aktiviert ist.<br/>

## <a name="streaming-endpoint-properties"></a>Eigenschaften von Streamingendpunkten

Dieser Abschnitt enthält detaillierte Informationen zu einigen Streamingendpunkt-Eigenschaften. Beispiele zum Erstellen eines neuen Streamingendpunkts und Beschreibungen aller Eigenschaften finden Sie unter [Streamingendpunkte](/rest/api/media/streamingendpoints/create).

- `accessControl`: Wird zum Konfigurieren der folgenden Sicherheitseinstellungen für den Streamingendpunkt verwendet: Authentifizierungsschlüssel und IP-Adressen im Signaturheader von Akamai, über die eine Verbindung mit dem Endpunkt zulässig ist. Diese Eigenschaft kann nur festgelegt werden, wenn `cdnEnabled` auf FALSE festgelegt ist.

- `cdnEnabled`: Gibt an, ob die Azure CDN-Integration für den Streamingendpunkt aktiviert ist (standardmäßig deaktiviert). Wenn Sie `cdnEnabled` auf TRUE festlegen, werden die folgenden Konfigurationen deaktiviert: `customHostNames` und `accessControl`.

    Nicht alle Rechenzentren unterstützen die Azure CDN-Integration. Führen Sie die folgenden Schritte aus, um zu überprüfen, ob die Azure CDN-Integration in Ihrem Rechenzentrum zur Verfügung steht:

  - Versuchen Sie, `cdnEnabled` auf TRUE festzulegen.
  - Überprüfen Sie das zurückgegebene Ergebnis auf einen `HTTP Error Code 412`-Fehler (PreconditionFailed) mit der Meldung „Streaming endpoint CdnEnabled property cannot be set to true as CDN capability is not available in the current region“ (Eigenschaft CdnEnabled des Streamingendpunkts kann nicht auf TRUE festgelegt werden, da die CDN-Funktion in der aktuellen Region nicht verfügbar ist).

    Wenn dieser Fehler angezeigt wird, wird das CDN im Rechenzentrum nicht unterstützt. Versuchen Sie es mit einem anderen Rechenzentrum.

- `cdnProfile`: Wenn `cdnEnabled` auf TRUE festgelegt ist, können Sie auch `cdnProfile`-Werte übergeben. `cdnProfile` ist der Name des CDN-Profils, in dem der CDN-Endpunkt erstellt wird. Sie können eine vorhandene cdnProfile-Eigenschaft angeben oder eine neue verwenden. Wenn der Wert NULL ist und `cdnEnabled` auf TRUE festgelegt ist, wird der Standardwert „AzureMediaStreamingPlatformCdnProfile“ verwendet. Wenn die angegebene `cdnProfile`-Eigenschaft bereits vorhanden ist, wird ein Endpunkt unter dieser Eigenschaft erstellt. Wenn das Profil nicht vorhanden ist, wird automatisch ein neues Profil erstellt.
- `cdnProvider`: Wenn das CDN aktiviert ist, können Sie auch `cdnProvider`-Werte übergeben. `cdnProvider` steuert, welcher Anbieter verwendet wird. Derzeit werden drei Werte unterstützt: „StandardVerizon“, „PremiumVerizon“ und „StandardAkamai“. Wenn kein Wert angegeben wird und `cdnEnabled` auf „true“ festgelegt ist, wird „StandardVerizon“ verwendet (d. h. der Standardwert).
- `crossSiteAccessPolicies`: Wird zum Angeben von websiteübergreifenden Zugriffsrichtlinien für verschiedene Clients verwendet. Weitere Informationen finden Sie unter [Cross-domain policy file specification](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) (Dateispezifikation für domänenübergreifende Richtlinien) und [Making a Service Available Across Domain Boundaries](/previous-versions/azure/azure-services/gg185950(v=azure.100)) (Verfügbarmachen eines Diensts über Netzwerkgrenzen hinweg). Die Einstellungen gelten nur für Smooth Streaming.
- `customHostNames`: Wird verwendet, um einen Streamingendpunkt so zu konfigurieren, dass an einen benutzerdefinierten Hostnamen gerichteter Datenverkehr akzeptiert wird. Diese Eigenschaft gilt für Standard- und Premium-Streamingendpunkte und kann festgelegt werden, wenn `cdnEnabled` FALSE ist.

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

    Media Services unterstützt derzeit TLS mit benutzerdefinierten Domänen nicht.

- `maxCacheAge` – Überschreibt den standardmäßigen Max-Age-HTTP-Cache Control Header, der vom Streamingendpunkt für Medienfragmente und On-Demand-Manifeste festgelegt wird. Der Wert wird in Sekunden festgelegt.
- `resourceState` -

    - Beendet: Der ursprüngliche Status eines Streamingendpunkts nach der Erstellung.
    - Wird gestartet: Der Streamingendpunkt geht über in den Status „Wird ausgeführt“.
    - Wird ausgeführt: Der Streamingendpunkt kann Inhalte an Clients streamen.
    - Wird skaliert: Die Skalierungseinheiten werden erhöht oder verringert.
    - Wird beendet: Der Streamingendpunkt geht über in den Status „Beendet“.
    - Wird gelöscht: Der Streamingendpunkt wird gelöscht.

- `scaleUnits`: Stellen eine dedizierte Ausgangskapazität bereit, die in Schritten von jeweils 200 MBit/s erworben werden kann. Wenn Sie zum **Premium**-Type wechseln möchten, passen Sie `scaleUnits` an.

## <a name="why-use-multiple-streaming-endpoints"></a>Gründe für die Verwendung mehrerer Streamingendpunkte

Ein einzelner Streamingendpunkt kann sowohl Live- als auch On-Demand-Videos streamen, und die meisten Kunden verwenden nur einen Streamingendpunkt. Dieser Abschnitt enthält einige Beispiele dafür, warum Sie möglicherweise mehrere Streamingendpunkte verwenden müssen.

* Jede reservierte Einheit ermöglicht eine Bandbreite von 200 MBit/s. Wenn Sie mehr als 2.000 MBit/s (2 GBit/s) Bandbreite benötigen, können Sie den zweiten Streamingendpunkt und den Lastenausgleich verwenden, um zusätzliche Bandbreite zu erhalten.

    Das CDN ist jedoch die beste Möglichkeit, eine Aufskalierung für Streaminginhalte zu erreichen. Wenn Sie jedoch so viele Inhalte bereitstellen, dass das CDN mehr als 2 GBit/s pullt, können Sie zusätzliche Streamingendpunkte (Ursprünge) hinzufügen. In diesem Fall müssten Sie Inhalts-URLs ausgeben, die auf die beiden Streamingendpunkte verteilt sind. Dieser Ansatz ermöglicht ein besseres Zwischenspeichern als der Versuch, Anforderungen nach dem Zufallsprinzip (z. B. über einen Traffic Manager) an jeden Ursprung zu senden. 
    
    > [!TIP]
    > Wenn das CDN mehr als 2 GBit/s pullt, könnte eine fehlerhafte Konfiguration vorliegen (z. B. kein Ursprungsschutz).
    
* Lastenausgleich für verschiedene CDN-Anbieter. Sie könnten z. B. den standardmäßigen Streamingendpunkt für die Verwendung des Verizon CDN einrichten und einen zweiten Endpunkt für die Verwendung von Akamai erstellen. Fügen Sie dann etwas Lastenausgleich zwischen den beiden hinzu, um einen Multi-CDN-Lastenausgleich zu erreichen. 

    Kunden führen jedoch häufig einen Lastenausgleich über mehrere CDN-Anbieter mit einem einzelnen Ursprung durch.
* Streamen von gemischten Inhalten: Liveinhalte und Video on Demand. 

    Die Zugriffsmuster für Live- und bedarfsgesteuerte Inhalte sind sehr unterschiedlich. Bei den Liveinhalten besteht tendenziell eine große gleichzeitige Nachfrage nach denselben Inhalten. Der Video-on-Demand-Inhalt (z. B. ausführliche Archivinhalte) wird nur in geringem Maße für denselben Inhalt verwendet. Das Zwischenspeichern funktioniert also sehr gut bei den Liveinhalten, aber nicht so gut bei den ausführlichen Inhalten.

    Stellen Sie sich ein Szenario vor, in dem Ihre Kunden hauptsächlich Liveinhalte sehen, aber nur gelegentlich bedarfsgesteuerte Inhalte verfolgen und diese vom selben Streamingendpunkt aus bedient werden. Die geringe Nutzung von bedarfsgesteuerten Inhalten würde Cachespeicherplatz belegen, der besser für die Liveinhalte eingespart werden könnte. In diesem Szenario würden wir empfehlen, den Liveinhalt von einem Streamingendpunkt und den ausführlichen Inhalt von einem anderen Streamingendpunkt aus bereitzustellen. Dies wird die Leistung der Inhalte der Liveereignisse verbessern.
    
## <a name="scaling-streaming-with-cdn"></a>Skalieren des Streamings mit CDN

Weitere Informationen finden Sie in folgenden Artikeln:

- [CDN-Übersicht](../../cdn/cdn-overview.md)
- [Skalieren des Streamings mit CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Stellen von Fragen und Abrufen von Updates

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="see-also"></a>Weitere Informationen

[Dynamische Paketerstellung](dynamic-packaging-overview.md)

## <a name="next-steps"></a>Nächste Schritte

[Verwalten von Streamingendpunkten](manage-streaming-endpoints-howto.md)
