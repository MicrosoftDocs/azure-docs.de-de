---
title: Häufig gestellte Fragen – Azure DNS
description: In diesem Artikel finden Sie häufig gestellte Fragen zu Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: rohink
ms.openlocfilehash: 2f7e4eadc25028db4668db8d245803c7ddba8688
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968246"
---
# <a name="azure-dns-faq"></a>Häufig gestellte Fragen zu Azure DNS

## <a name="about-azure-dns"></a>Informationen zu Azure DNS

### <a name="what-is-azure-dns"></a>Was ist Azure DNS?

Das Domain Name System (DNS) führt die Übersetzung bzw. Auflösung einer Website oder eines Dienstnamens in die zugehörige IP-Adresse durch. Azure DNS ist ein Hostingdienst für DNS-Domänen. Er ermöglicht eine Namensauflösung mithilfe der Microsoft Azure-Infrastruktur. Indem Sie Ihre Domänen in Azure hosten, können Sie Ihre DNS-Einträge unter Verwendung der gleichen Anmeldeinformationen, APIs, Tools und Abrechnungsabläufe wie bei Ihren anderen Azure-Diensten verwalten.

DNS-Domänen in Azure DNS werden im globalen Azure-Netzwerk von DNS-Servern gehostet. Bei diesem System werden Anycast-Netzwerke verwendet, sodass jede DNS-Abfrage vom nächsten verfügbaren DNS-Server beantwortet wird. Azure DNS sorgt für Ihre Domäne für eine schnelle Leistung und Hochverfügbarkeit.

Azure DNS basiert auf Azure Resource Manager. Azure DNS profitiert von Resource Manager-Funktionen, z. B. rollenbasierter Zugriffskontrolle von Azure, Überwachungsprotokollen und Ressourcensperren. Sie können Domänen und Einträge über das Azure-Portal, Azure PowerShell-Cmdlets und die plattformübergreifende Azure-Befehlszeilenschnittstelle verwalten. Anwendungen, für die eine automatische DNS-Verwaltung erforderlich ist, können über die REST-API und SDKs mit dem Dienst zusammenarbeiten.

### <a name="how-much-does-azure-dns-cost"></a>Wie viel kostet Azure DNS?

Das Abrechnungsmodell von Azure DNS basiert auf der Anzahl von DNS-Zonen, die in Azure DNS gehostet werden. Außerdem basiert es auf der Anzahl von empfangenen DNS-Abfragen. Rabatte werden basierend auf der Nutzung angeboten.

Weitere Informationen finden Sie auf der [Seite mit den Preisen von Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Wie lautet die Vereinbarung zum Servicelevel (SLA) für Azure DNS?

Azure garantiert für gültige DNS-Anforderungen, dass in 100 % der Fälle eine Antwort von mindestens einem Azure DNS-Namenserver empfangen wird.

Weitere Informationen finden Sie auf der [Seite mit der SLA für Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Was ist eine DNS-Zone? Ist das dasselbe wie eine DNS-Domäne? 

Eine Domäne ist ein eindeutiger Name im Domain Name System. Ein Beispiel wäre etwa „contoso.com“.

Eine DNS-Zone wird zum Hosten der DNS-Einträge für eine bestimmte Domäne verwendet. Beispielsweise kann die Domäne „contoso.com“ mehrere DNS-Einträge enthalten. Die Einträge können „mail.contoso.com“ für einen Mailserver und „www\.contoso.com“ für eine Website enthalten. Diese Einträge werden in der DNS-Zone „contoso.com“ gehostet.

Ein Domänenname ist *nur ein Name*. Eine DNS-Zone ist eine Datenressource, die die DNS-Einträge für einen Domänennamen enthält. Azure DNS ermöglicht es Ihnen, eine DNS-Zone zu hosten und die DNS-Einträge für eine Domäne in Azure zu verwalten. Darüber hinaus stellt der Dienst DNS-Namenserver bereit, um DNS-Abfragen aus dem Internet zu beantworten.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Muss ich einen DNS-Domänennamen erwerben, um Azure DNS nutzen zu können? 

Nicht unbedingt.

Sie müssen keine Domäne erwerben, um eine DNS-Zone in Azure DNS zu hosten. Sie können jederzeit eine DNS-Zone erstellen, ohne einen Domänennamen zu besitzen. DNS-Abfragen für diese Zone werden nur aufgelöst, wenn sie an die Azure DNS-Namenserver weitergeleitet werden, die der Zone zugewiesen sind.

Wenn Sie Ihre DNS-Zone mit der globalen DNS-Hierarchie verknüpfen möchten, müssen Sie den Domänennamen erwerben. DNS-Abfragen aus aller Welt können dann Ihre DNS-Zone finden und Antworten mit Ihren DNS-Einträgen erhalten.

## <a name="azure-dns-features"></a>Azure DNS-Features

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Gibt es Einschränkungen beim Verwenden von Aliaseinträgen für einen Domänennamen-Apex mit Traffic Manager?

Ja. Sie müssen für Azure Traffic Manager statische öffentliche IP-Adressen verwenden. Konfigurieren Sie den **externen Endpunkt** als Ziel, indem Sie eine statische IP-Adresse verwenden. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Unterstützt Azure DNS die Weiterleitung von Datenverkehr oder Endpunktfailover auf Basis von DNS?

Die Weiterleitung von Datenverkehr und Endpunktfailover auf Basis von DNS werden von Traffic Manager bereitgestellt. Traffic Manager ist ein separater Azure-Dienst, der mit Azure DNS verwendet werden kann. Weitere Informationen finden Sie in der [Traffic Manager-Übersicht](../traffic-manager/traffic-manager-overview.md).

Azure DNS unterstützt nur das Hosten von statischen DNS-Domänen, in denen jede DNS-Abfrage für einen bestimmten DNS-Eintrag immer die gleiche DNS-Antwort erhält.

### <a name="does-azure-dns-support-domain-name-registration"></a>Unterstützt Azure DNS die Registrierung von Domänennamen?

Nein. Für Azure DNS wird die Option zum Erwerben von Domänennamen derzeit nicht unterstützt. Zum Erwerben von Domänen müssen Sie eine Drittanbieter-Registrierungsstelle für Domänennamen verwenden. Die Registrierungsstelle erhebt in der Regel eine geringe jährliche Gebühr. Die Domänen können dann in Azure DNS für die Verwaltung von DNS-Einträgen gehostet werden. Weitere Informationen finden Sie unter [Delegieren einer Domäne an Azure DNS](dns-domain-delegation.md).

Das Feature zum Erwerben von Domänennamen wird im Azure-Backlog nachverfolgt. Verwenden Sie die Feedbackwebsite, [um Ihre Unterstützung für dieses Feature zu registrieren](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Unterstützt Azure DNS DNSSEC?

Nein. Azure DNS unterstützt derzeit keine Domain Name System-Sicherheitserweiterungen (Domain Name System Security Extensions, DNSSEC).

Das Feature DNSSEC wird im Azure DNS-Backlog nachverfolgt. Verwenden Sie die Feedbackwebsite, [um Ihre Unterstützung für dieses Feature zu registrieren](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Unterstützt Azure DNS Zonenübertragungen (AXFR/IXFR)?

Nein. Azure DNS unterstützt derzeit keine Zonenübertragungen. DNS-Zonen können [mithilfe der Azure CLI in Azure DNS importiert werden](dns-import-export.md). DNS-Einträge werden über das [Azure DNS-Verwaltungsportal](dns-operations-recordsets-portal.md), die [REST-API](/powershell/module/az.dns), das [SDK](dns-sdk.md), [PowerShell-Cmdlets](dns-operations-recordsets.md) oder das [CLI-Tool](dns-operations-recordsets-cli.md) verwaltet.

Das Feature für Zonenübertragungen wird im Azure DNS-Backlog nachverfolgt. Verwenden Sie die Feedbackwebsite, [um Ihre Unterstützung für dieses Feature zu registrieren](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Unterstützt Azure DNS URL-Umleitungen?

Nein. URL-Umleitungsdienste sind kein DNS-Dienst. Sie sind nicht auf der DNS-Ebene aktiv, sondern auf der HTTP-Ebene. Bei einigen DNS-Anbietern gehört ein Dienst für URL-Umleitungen zum Gesamtangebot. Dieser Dienst wird von Azure DNS derzeit nicht unterstützt.

Das Feature für die URL-Umleitung wird im Azure DNS-Backlog nachverfolgt. Verwenden Sie die Feedbackwebsite, [um Ihre Unterstützung für dieses Feature zu registrieren](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Unterstützt Azure DNS den erweiterten ASCII-Codierungssatz (8 Bit) für TXT-Eintragssätze?

Ja. Azure DNS unterstützt den erweiterten ASCII-Codierungssatz für TXT-Eintragssätze. Sie müssen aber die aktuelle Version der Azure-REST-APIs und SDKs und von PowerShell und der CLI verwenden. Versionen, die ein älteres Datum als 1. Oktober 2017 haben, oder das SDK 2.1 unterstützen den erweiterten ASCII-Satz nicht. 

Beispielsweise können Sie eine Zeichenfolge als Wert für einen TXT-Eintrag angeben, der das erweiterte ASCII-Zeichen \128 enthält. Ein Beispiel hierfür ist „abcd\128efgh“. Azure DNS nutzt den Bytewert dieses Zeichens (128) für die interne Darstellung. Zum Zeitpunkt der DNS-Auflösung wird dieser Bytewert in der Antwort zurückgegeben. Beachten Sie auch, dass „abc“ und „\097\098\099“, was die Auflösung betrifft, austauschbar sind. 

Wir befolgen für TXT-Einträge die Escaperegeln für das Zonendatei-Masterformat in [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt). Zum Beispiel fungiert `\` jetzt tatsächlich laut RFC als Escapezeichen für alles. Wenn Sie `A\B` als TXT-Eintragswert angeben, wird dieser nur als `AB` dargestellt und aufgelöst. Wenn Sie wirklich möchten, dass der TXT-Eintrag in `A\B` aufgelöst wird, müssen Sie `\` wieder mit dem Escapezeichen versehen. Geben Sie beispielsweise `A\\B` an.

Diese Unterstützung ist derzeit nicht für TXT-Einträge verfügbar, die im Azure-Portal erstellt wurden.

## <a name="alias-records"></a>Aliaseinträge

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>In welchen Szenarien sind Aliaseinträge nützlich?

Informationen hierzu finden Sie unter [Azure DNS-Aliaseinträge – Übersicht](dns-alias.md) im Abschnitt mit den Szenarien.

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Welche Eintragstypen werden für Aliaseintragssätze unterstützt?

Aliaseintragssätze werden für die folgenden Eintragstypen in einer Azure DNS-Zone unterstützt:
 
- Ein 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Welche Ressourcen werden als Ziele für Aliaseintragssätze unterstützt?

- **Verweisen auf eine öffentliche IP-Ressource aus einem DNS-A/AAAA-Eintragssatz:** Sie können einen A/AAAA-Eintragssatz erstellen und zu einem Aliaseintragssatz machen, um auf eine öffentliche IP-Ressource zu verweisen.
- **Verweisen auf ein Traffic Manager-Profil aus einem DNS-A/AAAA/CNAME-Eintragssatz:** Sie können über einen DNS-Eintragssatz vom Typ CNAME auf den CNAME eines Traffic Manager-Profils verweisen. Ein Beispiel hierfür ist „contoso.trafficmanager.net“. Außerdem können Sie nun auch auf ein Traffic Manager-Profil verweisen, das über externe Endpunkte eines A- oder AAAA-Eintragssatzes in Ihrer DNS-Zone verfügt.
- **Verweisen auf einen Endpunkt eines Azure Content Delivery Network (CDN)** Dies ist nützlich, wenn Sie statische Websites mit Azure Storage und Azure CDN erstellen.
- **Verweisen auf einen anderen DNS-Ressourceneintragssatz innerhalb derselben Zone:** Aliaseinträge können auf andere Eintragssätze desselben Typs verweisen. So kann beispielsweise ein DNS-CNAME-Eintragssatz ein Alias für einen anderen CNAME-Eintragsatz desselben Typs sein. Dies ist nützlich, wenn Sie möchten, dass einige Eintragssätze Aliase und andere keine Aliase sind.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Kann ich Aliaseinträge im Azure-Portal erstellen und aktualisieren?

Ja. Sie können Aliaseinträge im Azure-Portal mit den Azure-REST-APIs, PowerShell, der CLI und den SDKs erstellen oder verwalten.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Wird mit Aliaseinträgen sichergestellt, dass mein DNS-Eintragssatz gelöscht wird, wenn die zugrunde liegende öffentliche IP-Adresse gelöscht wird?

Ja. Dieses Feature ist eine der Kernfunktionen von Aliaseinträgen. Es unterstützt Sie dabei, potenzielle Ausfälle für die Benutzer Ihrer Anwendung zu vermeiden.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Kann mit Aliaseinträgen sichergestellt werden, dass mein DNS-Eintragssatz mit den richtigen IP-Adressen aktualisiert wird, wenn sich die zugrunde liegende öffentliche IP-Adresse ändert?

Ja. Dieses Feature ist eine der Kernfunktionen von Aliaseinträgen. Sie können mit dessen Hilfe potenzielle Ausfälle oder Sicherheitsrisiken für Ihre Anwendung vermeiden.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Gelten Einschränkungen für die Verwendung von Aliaseintragssätzen für A- oder AAAA-Einträge zum Verweisen auf Traffic Manager?

Ja. Um in Form eines Alias für einen A- oder AAAA-Eintragssatz auf ein Traffic Manager-Profil zu verweisen, müssen für das Traffic Manager-Profil ausschließlich externe Endpunkte verwendet werden. Geben Sie beim Erstellen der externen Endpunkte in Traffic Manager die tatsächlichen IP-Adressen der Endpunkte an.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Fallen zusätzliche Gebühren für die Verwendung von Aliaseinträgen an?

Aliaseinträge stellen eine Qualifikation für einen gültigen DNS-Ressourceneintragssatz dar. Für Aliaseinträge fällt keine weitere Abrechnung an.

## <a name="use-azure-dns"></a>Verwenden von Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Kann ich eine Domäne mit Azure DNS und einem anderen DNS-Anbieter gemeinsam hosten?

Ja. Azure DNS unterstützt das gemeinsame Hosten von Domänen mit anderen DNS-Diensten.

Ändern Sie zum Einrichten des gemeinsamen Hostens die NS-Einträge für die Domäne, damit sie auf die Namenserver beider Anbieter verweisen. Mit den NS-Einträgen (Namenserver) wird gesteuert, welche Anbieter DNS-Abfragen für die Domäne empfangen. Sie können diese NS-Einträge in Azure DNS, beim anderen Anbieter und in der übergeordneten Zone ändern. Die übergeordnete Zone wird normalerweise über die Domänennamen-Registrierungsstelle konfiguriert. Weitere Informationen zur DNS-Delegierung finden Sie unter [Delegieren von Domänen](dns-domain-delegation.md).

Stellen Sie auch sicher, dass die DNS-Einträge für die Domäne zwischen beiden DNS-Anbietern synchron sind. Azure DNS unterstützt derzeit keine DNS-Zonenübertragungen. DNS-Einträge müssen über das [Azure DNS-Verwaltungsportal](dns-operations-recordsets-portal.md), die [REST-API](/rest/api/dns/), das [SDK](dns-sdk.md), [PowerShell-Cmdlets](dns-operations-recordsets.md) oder das [CLI-Tool](dns-operations-recordsets-cli.md) synchronisiert werden.

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Muss ich meine Domäne an alle vier Azure DNS-Namenserver delegieren?

Ja. Azure DNS weist jeder DNS-Zone vier Namenserver zu. Diese Anordnung dient der Fehlereingrenzung und Verbesserung der Resilienz. Delegieren Sie Ihre Domäne an alle vier Namenserver, um die Qualifikation für die Azure DNS-SLA zu ermöglichen.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Welche Nutzungsgrenzwerte gelten für Azure DNS?

Bei Verwendung von Azure DNS gelten folgende Standardgrenzwerte.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Kann ich eine Azure DNS-Zone zwischen Ressourcengruppen oder Abonnements verschieben?

Ja. DNS-Zonen können zwischen Ressourcengruppen oder Abonnements verschoben werden.

Es ergeben sich keine Auswirkungen auf DNS-Abfragen, wenn Sie eine DNS-Zone verschieben. Die Namenserver, die der Zone zugewiesen sind, bleiben gleich. DNS-Abfragen werden als normaler Durchsatz verarbeitet.

Weitere Informationen und Anweisungen zum Verschieben von DNS-Zonen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Wie lange dauert es, bis DNS-Änderungen wirksam werden?

Neue DNS-Zonen und DNS-Einträge werden normalerweise schnell auf den Azure DNS-Namenservern angezeigt. Dies dauert nur einige Sekunden.

Änderungen an vorhandenen DNS-Einträgen können etwas länger dauern. Sie werden normalerweise innerhalb von 60 Sekunden auf den Azure DNS-Namenservern angezeigt. Die DNS-Zwischenspeicherung durch DNS-Clients und rekursive DNS-Resolver außerhalb von Azure DNS kann sich auch auf die Dauer auswirken. Verwenden Sie zum Steuern dieser Zwischenspeicherungsdauer die Eigenschaft für die Gültigkeitsdauer (Time To Live, TTL) der einzelnen Ressourceneintragssätze.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Wie kann ich meine DNS-Zonen vor versehentlichem Löschen schützen?

Azure DNS wird mit Azure Resource Manager verwaltet. Azure DNS profitiert von den Features für die Zugriffssteuerung, die von Azure Resource Manager bereitgestellt werden. Mit der rollenbasierten Zugriffssteuerung von Azure kann gesteuert werden, welche Benutzer über Lese- oder Schreibzugriff auf DNS-Zonen und Ressourceneintragssätze verfügen. Mit Ressourcensperren wird die versehentliche Änderung oder Löschung von DNS-Zonen und -Ressourceneintragssätzen verhindert.

Weitere Informationen finden Sie unter [Schützen von DNS-Zonen und -Einträgen](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Wie richte ich SPF-Datensätze in Azure DNS ein?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Wird bei Azure DNS-Namenservern über IPv6 aufgelöst? 

Ja. Azure DNS-Namenserver verfügen über einen dualen Stapel. Dies bedeutet, dass hierfür IPv4- und IPv6-Adressen verwendet werden. Zum Ermitteln der IPv6-Adresse für die Azure DNS-Namenserver, die Ihrer DNS-Zone zugewiesen sind, verwenden Sie ein Tool wie „nslookup“. z. B. `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Wie richte ich einen IDN in Azure DNS ein?

Bei internationalen Domänennamen (IDNs) wird jeder DNS-Name mit [Punycode](https://en.wikipedia.org/wiki/Punycode) codiert. DNS-Abfragen werden mit diesen per Punycode codierten Namen durchgeführt.

Konvertieren Sie den Zonennamen oder den Namen des Ressourceneintragssatzes in Punycode, um IDNs in Azure DNS zu konfigurieren. Azure DNS unterstützt die integrierte Konvertierung in bzw. aus Punycode gegenwärtig nicht.

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zu Azure DNS](dns-overview.md)

- [Weitere Informationen zur Verwendung von Azure DNS für private Domänen](private-dns-overview.md)

- [Weitere Informationen zu DNS-Zonen und -Einträgen](dns-zones-records.md)

- [Erste Schritte mit Azure DNS](dns-getstarted-portal.md)