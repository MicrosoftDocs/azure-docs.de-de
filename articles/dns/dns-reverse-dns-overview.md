---
title: 'Azure DNS: Übersicht über Reverse-DNS in Azure'
description: In diesem Lernpfad erhalten Sie eine Einführung in die Funktionsweise und Verwendung von Reverse-DNS in Azure.
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 8af9549efc3e8dab54f55dd404346d87201dee2c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965611"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Übersicht über Reverse-DNS und die Unterstützung in Azure

Dieser Artikel bietet eine Übersicht über die Funktionsweise von Reverse-DNS und die in Azure unterstützten Reverse-DNS-Szenarien.

## <a name="what-is-reverse-dns"></a>Was ist Reverse-DNS?

Herkömmliche DNS-Einträge ermöglichen die Zuordnung eines DNS-Namens (Beispiel: www.contoso.com) zu einer IP-Adresse (Beispiel: 64.4.6.100).  Mit Reverse-DNS kann eine IP-Adresse (64.4.6.100) wieder in einen Namen (www.contoso.com) umgewandelt werden.

Reverse-DNS-Einträge werden in vielen Situationen verwendet. Ein gängiger Anwendungsbereich für Reverse-DNS-Einträge ist beispielsweise die Bekämpfung von Spam-E-Mails durch Überprüfung des E-Mail-Absenders.  Der E-Mail-Server, bei dem die Nachricht eingeht, ruft den Reverse-DNS-Eintrag der IP-Adresse des Servers ab, der die Nachricht gesendet hat, und überprüft, ob der Host zum Senden von E-Mails aus der Herkunftsdomäne autorisiert ist. 

## <a name="how-reverse-dns-works"></a>Funktionsweise von Reverse-DNS

Reverse-DNS-Einträge werden in speziellen DNS-Zonen (so genannten ARPA-Zonen) gehostet.  Diese Zonen bilden parallel zur normalen Hierarchie, in der Domänen wie „contoso.com“ gehostet werden, eine separate DNS-Hierarchie.

Der DNS-Eintrag „www.contoso.com“ wird also beispielsweise unter Verwendung eines DNS-A-Eintrags namens „www“ in der Zone „contoso.com“ implementiert.  Dieser A-Eintrag verweist auf die entsprechende IP-Adresse (in diesem Fall: 64.4.6.100).  Das Reverse-Lookup wird separat implementiert. Hierbei wird ein PTR-Eintrag namens „100“ in der Zone „6.4.64.in-addr.arpa“ verwendet. (Beachten Sie, dass IP-Adressen in ARPA-Zonen umgekehrt werden.)  Bei korrekter Konfiguration verweist dieser PTR-Eintrag auf den Namen „www.contoso.com“.

Wenn einer Organisation ein IP-Adressblock zugewiesen wird, erhält sie auch die Berechtigung zum Verwalten der entsprechenden ARPA-Zone. Die ARPA-Zonen, die den von Azure verwendeten IP-Adressblöcken entsprechen, werden von Microsoft gehostet und verwaltet. Ihr Internetdienstanbieter hostet unter Umständen die ARPA-Zone für Ihre eigenen IP-Adressen für Sie oder ermöglicht es Ihnen, die ARPA-Zone in einem DNS-Dienst Ihrer Wahl zu hosten (beispielsweise in Azure DNS).

> [!NOTE]
> DNS-Forward-Lookups und DNS-Reverse-Lookups werden in separaten, parallelen DNS-Hierarchien implementiert. Das Reverse-Lookup für „www.contoso.com“ wird **nicht** in der Zone „contoso.com“ gehostet, sondern in der ARPA-Zone für den entsprechenden IP-Adressblock. Für IPv4- und IPv6-Adressblöcke werden separate Zonen verwendet.

### <a name="ipv4"></a>IPv4

Der Name einer IPv4-Reverse-Lookupzone sollte folgendes Format aufweisen: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Beispiel: Wenn Sie eine Reverse-Lookupzone erstellen, um Einträge für Hosts mit IP-Adressen mit dem Präfix 192.0.2.0/24 zu hosten, wird der Zonenname gebildet, indem das Netzwerkpräfix der Adresse (192.0.2) isoliert, die Reihenfolge umgekehrt (2.0.192) und dann das Suffix `.in-addr.arpa` hinzugefügt wird.

|Subnetzklasse|Netzwerkpräfix  |Umgekehrtes Netzwerkpräfix  |Standardsuffix  |Name der Reverse-Zone |
|-------|----------------|------------|-----------------|---------------------------|
|Klasse A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Klasse B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Klasse C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Klassenlose IPv4-Delegierung

In einigen Fällen ist der einer Organisation zugeordnete IP-Adressbereich kleiner als ein Klasse-C-Bereich (/24). In diesem Fall fällt der IP-Adressbereich nicht auf eine Zonengrenze innerhalb der `.in-addr.arpa`-Zonenhierarchie und kann daher nicht als untergeordnete Zone delegiert werden.

Stattdessen wird ein anderer Mechanismus verwendet, um die Steuerung einzelner Reverse-Lookup-Einträge (PTR) an eine dedizierte DNS-Zone zu übertragen. Dieser Mechanismus delegiert eine untergeordnete Zone für jeden IP-Adressbereich und ordnet jede IP-Adresse im Bereich mit CNAME-Einträgen einzeln dieser untergeordneten Zone zu.

Nehmen Sie beispielsweise an, dass eine Organisation den IP-Adressbereich 192.0.2.128/26 von ihrem ISP erhält. Dies entspricht 64 IP-Adressen von 192.0.2.128 bis 192.0.2.191. Reverse-DNS wird für diesen Bereich wie folgt implementiert:
- Die Organisation erstellt eine Reverse-Lookupzone mit dem Namen 128-26.2.0.192.in-addr.arpa. Das Präfix „128-26“ entspricht dem Netzwerksegment, das der Organisation innerhalb des Klasse-C-Bereichs (/24) zugewiesen ist.
- Der Internetdienstanbieter erstellt NS-Datensätze, um die DNS-Delegierung für die genannten Zone aus der übergeordneten Klasse C einzurichten. Außerdem erstellt er CNAME-Einträge in der übergeordneten Reverse-Lookupzone (Klasse C), die jede IP-Adresse im IP-Adressbereich der neuen von der Organisation erstellten Zone zuordnet:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- Die Organisation verwaltet dann die einzelnen PTR-Einträge innerhalb ihrer untergeordneten Zone.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Ein Reverse-Lookup für die IP-Adresse 192.0.2.129 fragt einen PTR-Eintrag mit dem Namen 129.2.0.192.in-addr.arpa ab. Diese Abfrage wird über den CNAME-Eintrag in der übergeordneten Zone in den PTR-Eintrag in der untergeordneten Zone aufgelöst.

### <a name="ipv6"></a>IPv6

Der Name einer IPv6-Reverse-Lookupzone sollte folgendes Format aufweisen: `<IPv6 network prefix in reverse order>.ip6.arpa`.

Beispiel: Wenn Sie eine Reverse-Lookupzone erstellen, um Einträge für Hosts mit IP-Adressen mit dem Präfix 2001:db8:1000:abdc::/64 zu hosten, wird der Zonenname gebildet, indem das Netzwerkpräfix der Adresse (2001:db8:abdc::) isoliert wird. Erweitern Sie als Nächstes das IPv6-Netzwerkpräfix, sodass die [Nullkomprimierung](/previous-versions/windows/it-pro/windows-server-2003/cc781672(v=ws.10)) entfernt wird, falls diese zum Kürzen des IPv6-Adresspräfix (2001:0db8:abdc:0000::) verwendet wurde. Kehren Sie die Reihenfolge um, und verwenden Sie dabei einen Punkt als Trennzeichen zwischen den einzelnen Hexadezimalzahlen im Präfix, um das umgekehrte Netzwerkpräfix (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) zu erstellen, und fügen Sie das Suffix `.ip6.arpa` hinzu.


|Netzwerkpräfix  |Erweitertes und umgekehrtes Netzwerkpräfix |Standardsuffix |Name der Reverse-Zone  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Azure-Unterstützung für Reverse-DNS

Azure unterstützt im Zusammenhang mit Reverse-DNS zwei separate Szenarien:

**Hosten der entsprechenden Reverse-Lookupzone für Ihren IP-Adressblock**
Sie können mit Azure DNS [Ihre Reverse-Lookupzonen hosten und die PTR-Einträge für die einzelnen Reverse-DNS-Lookups verwalten](dns-reverse-dns-hosting.md). Dies ist für IPv4 und IPv6 möglich.  Die Vorgehensweise zum Erstellen der Reverse-Lookupzone (ARPA), zum Einrichten der Delegierung und zum Konfigurieren der PTR-Einträge ist die gleiche wie bei regulären DNS-Zonen.  Einziger Unterschied: Die Delegierung muss über Ihren Internetdienstanbieter und nicht über Ihre DNS-Registrierungsstelle konfiguriert werden, und es darf nur der PTR-Eintragstyp verwendet werden.

**Konfigurieren des Reverse-DNS-Eintrags für die IP-Adresse, die Ihrem Azure-Dienst zugewiesen ist** Azure ermöglicht die [Konfiguration des Reverse-Lookups für die IP-Adressen, die Ihrem Azure-Dienst zugeordnet sind](dns-reverse-dns-for-azure-services.md).  Dieses Reverse-Lookup wird von Azure als PTR-Eintrag in der entsprechenden ARPA-Zone konfiguriert.  Diese ARPA-Zonen entsprechen allen von Azure verwendeten IP-Adressbereichen und werden von Microsoft gehostet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Reverse-DNS finden Sie unter [Reverse DNS](https://en.wikipedia.org/wiki/Reverse_DNS_lookup) in der Wikipedia.
<br>
Erfahren Sie, wie [die Reverse-Lookupzone für Ihren vom ISP zugewiesenen IP-Adressbereich in Azure DNS gehostet wird](dns-reverse-dns-for-azure-services.md).
<br>
Erfahren Sie, wie Sie [Reverse-DNS-Einträge für Ihre Azure-Dienste verwalten](dns-reverse-dns-for-azure-services.md).