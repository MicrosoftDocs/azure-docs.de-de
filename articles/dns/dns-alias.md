---
title: 'Übersicht: Azure DNS-Aliaseinträge – Azure DNS'
description: In diesem Artikel erfahren Sie mehr über die Unterstützung für Aliaseinträge in Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 8b2576669357aae7e5fe423515933c2ce4a23a7d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954476"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS-Aliaseinträge – Übersicht

Azure DNS-Aliaseinträge sind Qualifizierungen für einen DNS-Ressourceneintragssatz. Sie können aus Ihrer DNS-Zone heraus auf andere Azure-Ressourcen verweisen. So können Sie beispielsweise einen Aliaseintragssatz erstellen, der anstatt auf einen A-Datensatz auf eine öffentliche Azure-IP-Adresse verweist. Ihr Aliaseintragssatz verweist dynamisch auf eine Dienstinstanz einer öffentlichen Azure-IP-Adresse. Dadurch aktualisiert sich der Aliaseintragssatz während der DNS-Auflösung nahtlos selbst.

Aliaseintragssätze werden für die folgenden Eintragstypen in einer Azure DNS-Zone unterstützt: 

- Ein
- AAAA
- CNAME

> [!NOTE]
> Wenn Sie beabsichtigen, für den Verweis auf ein [Azure Traffic Manager-Profil](../traffic-manager/quickstart-create-traffic-manager-profile.md) einen Aliaseintrag für die A- oder AAAA-Eintragstypen zu verwenden, stellen Sie sicher, dass das Traffic Manager-Profil nur [externe Endpunkte](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) aufweist. Sie müssen die IPv4- oder IPv6-Adresse für externe Endpunkte in Traffic Manager angeben. Sie können keine vollständig qualifizierten Domänennamen (FQDNs) in Endpunkten verwenden. Verwenden Sie idealerweise statische IP-Adressen.

## <a name="capabilities"></a>Funktionen

- **Verweisen auf eine öffentliche IP-Ressource aus einem DNS-A/AAAA-Eintragssatz:** Sie können einen A/AAAA-Eintragssatz erstellen und zu einem Aliaseintragssatz machen, um auf eine öffentliche IP-Ressource (Standard oder Basic) zu verweisen. Der DNS-Ressourceneintragssatz verändert sich automatisch, wenn die öffentliche IP-Adresse geändert oder gelöscht wird. Verbleibende DNS-Einträge, die auf falsche IP-Adressen verweisen, werden vermieden.

   Es gibt aktuell eine Beschränkung von 20 Aliaseintragssätzen pro Ressource.

- **Verweisen auf ein Traffic Manager-Profil aus einem DNS-A/AAAA/CNAME-Eintragssatz:** Sie können einen A/AAAA- oder einen CNAME-Eintragssatz erstellen und ihn über Aliaseinträge an ein Traffic Manager-Profil verweisen. Dies ist besonders nützlich, wenn Sie Datenverkehr an einem Zonen-Apex weiterleiten müssen, weil herkömmliche CNAME-Einträge für einen Zonen-Apex nicht unterstützt werden. Beispiel: Ihr Traffic Manager-Profil heißt „myprofile.trafficmanager.net“, und die DNS-Zone Ihres Unternehmens heißt „contoso.com“. Sie können einen Aliaseintragssatz vom Typ A/AAAA für „contoso.com“ (den Zonen-Apex) erstellen und an „myprofile.trafficmanager.net“ verweisen.
- **Verweisen auf einen Endpunkt eines Azure Content Delivery Network (CDN)** Dies ist nützlich, wenn Sie statische Websites mit Azure Storage und Azure CDN erstellen.
- **Verweisen auf einen anderen DNS-Ressourceneintragssatz innerhalb derselben Zone:** Aliaseinträge können auf andere Eintragssätze desselben Typs verweisen. So kann beispielsweise ein DNS-CNAME-Eintragssatz ein Alias für einen anderen CNAME-Eintragssatz sein. Dies ist nützlich, wenn Sie möchten, dass einige Eintragssätze Aliase und andere keine Aliase sind.

## <a name="scenarios"></a>Szenarien

Aliaseinträge sind in einigen allgemeinen Szenarien hilfreich.

### <a name="prevent-dangling-dns-records"></a>Vermeiden verwaister DNS-Einträge

Ein verbreitetes Problem bei herkömmlichen DNS-Einträgen sind verwaiste Einträge. Dabei handelt es sich zum Beispiel um DNS-Einträge, die bei Änderungen an IP-Adressen nicht aktualisiert wurden. Dieses Problem tritt besonders bei A/AAAA- und CNAME-Eintragstypen auf.

Wenn bei einem herkömmlichen DNS-Zoneneintrag die Ziel-IP-Adresse oder der CNAME nicht mehr vorhanden ist, muss der zugeordnete DNS-Eintrag manuell aktualisiert werden. In einigen Organisationen wird diese manuelle Aktualisierung aufgrund von Problemen beim Vorgang oder aufgrund der Trennung von Rollen und zugeordneten Berechtigungsebenen möglicherweise nicht rechtzeitig durchgeführt. So kann es beispielsweise sein, dass eine Rolle zum Löschen von CNAMEs oder IP-Adressen autorisiert ist, die zu einer Anwendung gehören. Sie verfügt aber möglicherweise nicht über die erforderlichen Berechtigungen, um den DNS-Eintrag zu aktualisieren, der auf diese Ziele verweist. Eine Verzögerung bei der Aktualisierung des DNS-Eintrags kann für die Benutzer potenziell zu einem Ausfall führen.

Aliaseinträge verhindern verbleibende Verweise durch eine enge Kopplung des Lebenszyklus eines DNS-Eintrags an eine Azure-Ressource. Stellen Sie sich beispielsweise einen DNS-Eintrag vor, der als Aliaseintrag qualifiziert wird, um auf eine öffentliche IP-Adresse oder auf ein Traffic Manager-Profil zu verweisen. Wenn Sie diese zugrunde liegenden Ressourcen löschen, wird der DNS-Aliaseintrag ein leerer Eintragssatz. Er verweist nicht mehr auf die gelöschte Ressource.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Automatisches Aktualisieren von DNS-Eintragssätzen bei Änderung von Anwendungs-IP-Adressen

Dieses Szenario ähnelt dem vorherigen Szenario. Möglicherweise wird eine Anwendung verschoben, oder der zugrunde liegende virtuelle Computer wird neu gestartet. Ein Aliaseintrag wird automatisch aktualisiert, wenn sich die IP-Adresse für die zugrunde liegende öffentliche IP-Ressource ändert. Dadurch wird das potenzielle Sicherheitsrisiko vermieden, dass Benutzer zu einer anderen Anwendung geleitet werden, der die alte öffentliche IP-Adresse zugewiesen wurde.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hosten von Anwendungen mit Lastenausgleich im Zonen-Apex

Das DNS-Protokoll verhindert die Zuweisung von CNAME-Einträgen am Zonen-Apex. Wenn die Domäne beispielsweise „contoso.com“ heißt, können Sie CNAME-Einträge für „bezeichnung.contoso.com“, aber keinen CNAME-Eintrag für „contoso.com“ selbst erstellen.
Diese Einschränkung stellt Anwendungsbesitzer, die über Anwendungen mit Lastenausgleich hinter [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) verfügen, vor ein Problem. Da für die Verwendung eines Traffic Manager-Profils die Erstellung eines CNAME-Eintrags erforderlich ist, kann vom Zonen-Apex aus nicht auf das Traffic Manager-Profil verwiesen werden.

Dieses Problem wird mithilfe von Aliaseinträgen gelöst. Im Gegensatz zu CNAME-Einträgen werden Aliaseinträge am Zonen-Apex erstellt, und Anwendungsbesitzer können damit Ihren Zonen-Apex-Eintrag an ein Traffic Manager-Profil mit externen Endpunkten verweisen. Anwendungsbesitzer verweisen auf das gleiche Traffic Manager-Profil, das auch für andere Domänen innerhalb ihrer DNS-Zone verwendet wird.

So können beispielsweise „contoso.com“ und „www\.contoso.com“ jeweils auf das gleiche Traffic Manager-Profil verweisen. Weitere Informationen zur Verwendung von Aliaseinträgen mit Azure Traffic Manager-Profilen finden Sie im Abschnitt „Nächste Schritte“.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Verweisen an der Zonenspitze auf Azure CDN-Endpunkte

Genau wie ein Traffic Manager-Profil können Sie auch Aliasdatensätze verwenden, um an der Zonenspitze Ihrer DNS-Zone auf Azure CDN-Endpunkte zu verweisen. Dies ist nützlich, wenn Sie statische Websites mit Azure Storage und Azure CDN erstellen. Anschließend können Sie auf die Website zugreifen, ohne dem DNS-Namen „www“ voranstellen zu müssen.

Wenn Ihre statische Website `www.contoso.com` heißt, können Ihre Benutzer mit `contoso.com` auf Ihre Website zugreifen, ohne dem DNS-Namen „www“ voranstellen zu müssen.

Wie zuvor beschrieben, werden CNAME-Einträge am Zonen-Apex nicht unterstützt. Daher können Sie keinen CNAME-Eintrag verwenden, um mit „contoso.com“ auf Ihren CDN-Endpunkt zu verweisen. Stattdessen können Sie einen Aliaseintrag verwenden, um direkt an der Zonenspitze auf einen CDN-Endpunkt zu verweisen.

> [!NOTE]
> Das Verweisen einer Zonenspitze auf CDN-Endpunkte für Azure CDN von Akamai wird derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Aliaseinträgen finden Sie in den folgenden Artikeln:

- [Tutorial: Konfigurieren eines Aliaseintrags, um auf eine öffentliche Azure-IP-Adresse zu verweisen](tutorial-alias-pip.md)
- [Tutorial: Konfigurieren eines Alias-Ressourceneintrags zur Unterstützung von Apex-Domänennamen mit Traffic Manager](tutorial-alias-tm.md)
- [Häufig gestellte Fragen zu DNS](./dns-faq.md#alias-records)