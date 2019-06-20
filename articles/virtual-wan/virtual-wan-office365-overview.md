---
title: Office 365-Steuerebene in Azure Virtual WAN
description: Erfahren Sie mehr über die Office 365-Steuerebene in Virtual WAN.
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: cb91c1364a91c101ecf8362acd7aab01440143fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685268"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>Office 365-Steuerebene in Virtual WAN

Virtual WAN-Kunden mit ausgewählten SDWAN-Geräten können im Azure-Portal Office 365-Richtlinien für den Internetübergang für vertrauenswürdigen Datenverkehr konfigurieren. Hierdurch wird Folgendes ermöglicht:
- Office 365-Datenverkehr kann in das Microsoft-Netzwerk in der Nähe des Benutzers gelangen, was eine optimale Benutzererfahrung ermöglicht.
- Vermeiden von Rücktransport von Datenverkehr und NAT-Loopback zum Einsparen von WAN-Kosten.
- Bereitstellung gemäß den Office 365-Prinzipien für Konnektivität.

## <a name="faqs"></a>Häufig gestellte Fragen
### <a name="what-is-the-customer-benefit"></a>Was ist der Vorteil für den Kunden?
Mit dieser Funktion in Virtual WAN können Kunden nun die Office 365-Datenverkehrskategorien angeben, denen sie für den direkten Internetübergang vertrauen. Dieser vertrauenswürdige Office 365-Verkehr umgeht Proxys und wird direkt vom Benutzerstandort zum nächsten Microsoft POP geleitet. Dadurch wird Rücktransport von Datenverkehr und NAT-Loopback vermieden, was für eine optimale Benutzererfahrung und niedrigere WAN-Kosten sorgt. 

### <a name="what-are-the-office-365-traffic-categories"></a>Was sind die Kategorien von Office 365-Datenverkehr?
Office 365-Endpunkte stellen Adressen und Subnetze dar. Endpunkte können URLs, IP-Adressen oder IP-Bereiche sein. URLs können entweder ein FQDN wie *account.office.net* oder eine Platzhalter-URL wie * *.office365.com* sein. Endpunkte werden in drei Kategorien unterteilt, und zwar basierend auf ihrer Wichtigkeit in **Optimieren**, **Zulassen** und **Standard**. Weitere Details zu den Endpunktkategorien finden Sie [hier](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories).

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>Welche Office 365-Datenverkehrskategorie wird von Microsoft für den direkten Internetübergang empfohlen?
Die Kategorie **Optimieren** ist der kritischste Netzwerkendpunkt und wird benötigt, um SSL-Breaks zu umgehen und andere Netzwerksicherheitsgeräte zu prüfen. Diese Endpunkte sollten einen direkten Internetzugang in der Nähe der Benutzer haben. Diese Endpunkte stellen Office 365-Szenarien dar, die am empfindlichsten auf Netzwerkleistung, Latenz und Verfügbarkeit reagieren. Diese Kategorie umfasst eine kleine Gruppe (ca. 10) von Schlüssel-URLs und eine definierte Gruppe von IP-Subnetzen, die für die wichtigsten Office 365-Workloads wie Exchange Online, SharePoint Online, Skype for Business Online und Microsoft Teams bestimmt sind. 

Die Kategorie **Zulassen** wird auch für den direkten Internetausgang empfohlen. Netzwerkverkehr dieser Kategorie kann jedoch eine gewisse Netzwerklatenz tolerieren. Endpunkte in den Kategorien „Optimieren“ und „Zulassen“ werden alle in Microsoft-Rechenzentren gehostet und als Teil von Office 365 verwaltet. Die Kategorie „Standard“ kann zu einem standardmäßigen Internetausgangsort geleitet werden und erfordert keinen direkten Internetausgang bzw. keine Umgehung von SSL-Breaks und Überprüfungen von Geräten.

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>Wie richte ich meine Office 365-Richtlinien über Virtual WAN ein?
Sie können Richtlinien über **Virtual WAN** -> **Einstellungen** -> **Registerkarte „Konfiguration“** aktivieren. Hier können Sie Ihre bevorzugte Kategorien von Office 365-Datenverkehr für den direkten Internetübergang angeben.

![Konfigurieren der Office 365-Steuerebene in Virtual WAN](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>Wie funktioniert das?

1.  Office 365-Datenverkehr gelangt in der Nähe des Benutzers in das Microsoft-Netzwerk, was eine optimale Benutzererfahrung ermöglicht.
2.  Routingrichtlinien werden von SDWAN genutzt. Anschließend werden Sicherheitsproxys für die vertrauenswürdigen Kategorien umgangen, und es erfolgen lokale direkte Übergänge für diese Kategorien.
3.  Rücktransport von Datenverkehr und NAT-Loopback werden zum Einsparen von WAN-Kosten vermieden.

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>Welche Partnergeräte unterstützen dies über Virtual WAN?
Citrix unterstützt derzeit diese Richtlinien über Virtual WAN.

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>Was geschieht mit den übrigen Kategorien von (nicht vertrauenswürdigem) Office 365-Datenverkehr?
Der restliche Office 365-Datenverkehr folgt dem Standardpfad des Kunden für Internetdatenverkehr.

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>Was geschieht, wenn ich meine Office 365-Richtlinien bereits über meinen SDWAN-Anbieter angegeben habe?
Wenn Sie Richtlinien über die SDWAN-Benutzeroberfläche und Azure Virtual WAN angeben, haben die in Virtual WAN festgelegten Richtlinien Vorrang.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Virtual WAN](virtual-wan-about.md).