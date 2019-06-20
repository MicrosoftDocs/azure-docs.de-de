---
title: Übereinstimmungsbedingungen der Regel-Engine für Azure CDN Premium von Verizon | Microsoft-Dokumentation
description: Referenzdokumentation zu den Übereinstimmungsbedingungen der Regel-Engine für Azure Content Delivery Network Premium von Verizon.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5197fdfe78b1b091de713754967f58157cc4a1b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482028"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Übereinstimmungsbedingungen der Regel-Engine für Azure CDN Premium von Verizon

Dieser Artikel enthält ausführliche Beschreibungen der verfügbaren Übereinstimmungsbedingungen für die [Regel-Engine](cdn-verizon-premium-rules-engine.md) des Azure Content Delivery Network (CDN) Premium von Verizon.

Der zweite Teil einer Regel ist die Übereinstimmungsbedingung. Eine Übereinstimmungsbedingung gibt bestimmte Typen von Anforderungen an, für die verschiedene sog. Features ausgeführt werden.

Sie können eine Übereinstimmungsbedingung z.B. für Folgendes verwenden:

- Filtern von Anforderungen für Inhalte an einem bestimmten Standort
- Filtern von Anforderungen, die von einer bestimmten IP-Adresse oder einem bestimmten Land bzw. einer bestimmten Region generiert wurden
- Filtern von Anforderungen nach Headerinformationen

## <a name="always-match-condition"></a>Übereinstimmungsbedingung „Always“

Die Übereinstimmungsbedingung „Always“ wendet einen Standardsatz von Features auf alle Anforderungen an.

NAME | Zweck
-----|--------
[Always](#always) | Wendet einen Standardsatz von Features auf alle Anforderungen an.

## <a name="device-match-condition"></a>Übereinstimmungsbedingung „Device“

Die Übereinstimmungsbedingung „Device“ bestimmt Anforderungen von einem Mobilgerät anhand seiner Eigenschaften.  

NAME | Zweck
-----|--------
[Device](#device) | Bestimmt Anforderungen von einem Mobilgerät anhand seiner Eigenschaften.

## <a name="location-match-conditions"></a>Location-Übereinstimmungsbedingungen

Die Location-Übereinstimmungsbedingungen bestimmen Anforderungen basierend auf dem Standort des Anfordernden.

NAME | Zweck
-----|--------
[AS Number](#as-number) | Bestimmt Anforderungen, die aus einem bestimmten Netzwerk stammen.
[Country](#country) | Bestimmt Anforderungen, die aus den angegebenen Ländern/Regionen stammen.

## <a name="origin-match-conditions"></a>Origin-Übereinstimmungsbedingungen

Die Origin-Übereinstimmungsbedingungen bestimmen Anforderungen, die auf CDN-Speicher oder einen Kundenursprungsserver zeigen.

NAME | Zweck
-----|--------
[CDN Origin](#cdn-origin) | Bestimmt Anforderungen für Inhalte, die in Content Delivery Network-Speicher gespeichert sind.
[Customer Origin](#customer-origin) | Bestimmt Anforderungen von Inhalten, die auf einem spezifischen Kundenursprungsserver gespeichert sind.

## <a name="request-match-conditions"></a>Request-Übereinstimmungsbedingungen

Die Request-Übereinstimmungsbedingungen bestimmen Anforderungen basierend auf ihren Eigenschaften.

NAME | Zweck
-----|--------
[Client IP Address](#client-ip-address) | Bestimmt Anforderungen, die von einer bestimmten IP-Adresse stammen.
[Cookie Parameter](#cookie-parameter) | Überprüft die Cookies, die jeder Anforderung für den angegebenen Wert zugeordnet sind.
[Cookie Parameter Regex](#cookie-parameter-regex) | Überprüft die Cookies, die jeder Anforderung zugeordnet sind, auf den angegebenen regulären Ausdruck.
[Edge Cname](#edge-cname) | Bestimmt Anforderungen, die auf einen bestimmten Edge-CNAME zeigen.
[Referring Domain](#referring-domain) | Bestimmt Anforderungen, auf die von den angegebenen Hostnamen verwiesen wurde.
[Request Header Literal](#request-header-literal) | Bestimmt Anforderungen, die den angegebenen auf einen bestimmten Wert festgelegten Header enthalten.
[Request Header Regex](#request-header-regex) | Bestimmt Anforderungen, die den angegebenen Header enthalten, der auf einen Wert festgelegt ist, der dem angegebenen regulären Ausdruck entspricht.
[Request Header Wildcard](#request-header-wildcard) | Bestimmt Anforderungen, die den angegebenen Header enthalten, der auf einen Wert festgelegt ist, der dem angegebenen Muster entspricht.
[Request Method](#request-method) | Bestimmt Anforderungen anhand ihrer HTTP-Methode.
[Request Scheme](#request-scheme) | Bestimmt Anforderungen anhand ihres HTTP-Protokolls.

## <a name="url-match-conditions"></a>URL-Übereinstimmungsbedingungen

Die URL-Übereinstimmungsbedingungen bestimmen Anforderungen basierend auf ihren URLs.

NAME | Zweck
-----|--------
[URL Path Directory](#url-path-directory) | Bestimmt Anforderungen anhand ihres relativen Pfads.
[URL Path Extension](#url-path-extension) | Bestimmt Anforderungen anhand ihrer Dateinamenerweiterung.
[URL Path Filename](#url-path-filename) | Bestimmt Anforderungen anhand ihres Dateinamens.
[URL Path Literal](#url-path-literal) | Vergleicht den relativen Pfad einer Anforderung mit dem angegebenen Wert.
[URL Path Regex](#url-path-regex) | Vergleicht den relativen Pfad einer Anforderung mit dem angegebenen regulären Ausdruck.
[URL Path Wildcard](#url-path-wildcard) | Vergleicht den relativen Pfad einer Anforderung mit dem angegebenen Muster.
[URL Query Literal](#url-query-literal) | Vergleicht die Abfragezeichenfolge einer Anforderung mit dem angegebenen Wert.
[URL Query Parameter](#url-query-parameter) | Bestimmt Anforderungen, die den angegebenen Abfragezeichenfolgen-Parameter enthalten, der auf einen Wert festgelegt ist, der einem angegebenen Muster entspricht.
[URL Query Regex](#url-query-regex) | Bestimmt Anforderungen, die den angegebenen Abfragezeichenfolgen-Parameter enthalten, der auf einen Wert festgelegt ist, der einem angegebenen regulären Ausdruck entspricht.
[URL Query Wildcard](#url-query-wildcard) | Vergleicht den angegebenen Wert mit der Abfragezeichenfolge der Anforderung.

## <a name="reference-for-rules-engine-match-conditions"></a>Referenz für Übereinstimmungsbedingungen der Regel-Engine

---

### <a name="always"></a>Always

Die Übereinstimmungsbedingung „Always“ wendet einen Standardsatz von Features auf alle Anforderungen an.

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>AS Number

Das AS Number-Netzwerk wird durch seine autonome Systemnummer (ASN) definiert. 

Mit der Option **Entspricht**/**Entspricht nicht** werden die Bedingungen bestimmt, unter denen die Übereinstimmungsbedingung „AS Number“ erfüllt ist:

- **Entspricht:** Für diese Option ist es erforderlich, dass die ASN des Clientnetzwerks mit einer der angegebenen ASNs übereinstimmt. 
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die ASN des Clientnetzwerks nicht mit einer der angegebenen ASNs übereinstimmt.

Wichtige Informationen:

- Wenn Sie mehrere ASNs angeben möchten, trennen Sie die Nummern durch ein einzelnes Leerzeichen voneinander. Die Angabe „64514 64515“ beispielsweise führt zur Übereinstimmung mit Anforderungen von 64514 oder 64515.
- Bestimmte Anforderungen geben möglicherweise keine gültige ASN zurück. Ein Fragezeichen (?) kennzeichnet Anforderungen, für die keine gültige ASN ermittelt werden konnte.
- Geben Sie die gesamte ASN für das gewünschte Netzwerk an. Für Teilwerte gibt es keine Übereinstimmung.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>CDN Origin

Die Übereinstimmungsbedingung „CDN Origin“ ist erfüllt, wenn beide folgenden Bedingungen erfüllt sind:

- Der Inhalt wurde aus dem CDN-Speicher angefordert.
- Der Anforderungs-URI verwendet den Typ des Inhaltszugriffspunkts (z.B. /000001), der in dieser Übereinstimmungsbedingung definiert ist:
  - CDN-URL: Der Anforderungs-URI muss den ausgewählten Inhaltszugriffspunkt enthalten.
  - Edge-CNAME-URL: Die entsprechende Edge-CNAME-Konfiguration muss auf den ausgewählten Inhaltszugriffspunkt zeigen.
  
Wichtige Informationen:

- Der Inhaltszugriffspunkt bestimmt den Dienst, der den angeforderten Inhalt bereitstellen soll.
- Verwenden Sie keine AND IF-Anweisung, um bestimmte Übereinstimmungsbedingungen zu kombinieren. Durch Kombination einer CDN Origin-Übereinstimmungsbedingung mit einer Customer Origin-Übereinstimmungsbedingung würde beispielsweise ein Übereinstimmungsmuster entstehen, für das nie eine Übereinstimmung gefunden werden kann. Aus diesem Grund können zwei CDN Origin-Übereinstimmungsbedingungen nicht mit einer AND IF-Anweisung kombiniert werden.

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Client IP Address

Mit der Option **Entspricht**/**Entspricht nicht** werden die Bedingungen bestimmt, unter denen die Übereinstimmungsbedingung „Client IP Address“ erfüllt ist:

- **Entspricht:** Für diese Option ist es erforderlich, dass die IP-Adresse des Clients mit einer der angegebenen IP-Adressen übereinstimmt. 
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die IP-Adresse des Clients nicht mit einer der angegebenen IP-Adressen übereinstimmt. 

Wichtige Informationen:

- Verwenden Sie die CIDR-Notation.
- Wenn Sie mehrere IP-Adressen und/oder IP-Adressblöcke angeben, trennen Sie diese mit einem einzelnen Leerzeichen. Beispiel:
  - **IPv4-Beispiel:** Die Angabe „1.2.3.4 10.20.30.40“ führt zur Übereinstimmung mit eingehenden Anforderungen von der Adresse 1.2.3.4 oder 10.20.30.40.
  - **IPv6-Beispiel:** Die Angabe „1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80“ führt zur Übereinstimmung mit eingehenden Anforderungen von der Adresse 1:2:3:4:5:6:7:8 oder 10:20:30:40:50:60:70:80.
- Die Syntax für einen IP-Adressblock besteht aus der IP-Basisadresse, gefolgt von einem Schrägstrich und der Präfixgröße. Beispiel:
  - **IPv4-Beispiel:** Die Angabe „5.5.5.64/26“ führt zur Übereinstimmung mit eingehenden Anforderungen von den Adressen 5.5.5.64 bis 5.5.5.127.
  - **IPv6-Beispiel:** Die Angabe „1:2:3:/48“ führt zur Übereinstimmung mit eingehenden Anforderungen von den Adressen 1:2:3:0:0:0:0:0 bis 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Cookie Parameter

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „Cookie Parameter“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass eine Anforderung das angegebene Cookie mit einem Wert enthält, der mindestens einem der in dieser Übereinstimmungsbedingung definierten Werte entspricht.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung eines der folgenden Kriterien erfüllt:
  - Die Anforderung enthält das angegebene Cookie nicht.
  - Die Anforderung enthält das angegebene Cookie, aber der Wert entspricht keinem der Werte, die in dieser Übereinstimmungsbedingung definiert sind.
  
Wichtige Informationen:

- Cookiename:
  - Da Platzhalterwerte, z.B. Sternchen (*), beim Angeben eines Cookienamens nicht unterstützt werden, können nur exakte Übereinstimmungen von Cookienamen für Vergleiche herangezogen werden.
  - Für jede Instanz dieser Übereinstimmungsbedingung kann nur ein einziger Cookiename angegeben werden.
  - Beim Vergleichen von Cookienamen wird die Groß-/Kleinschreibung nicht beachtet.
- Cookiewert:
  - Wenn Sie mehrere Cookiewerte angeben möchten, trennen Sie diese durch ein einzelnes Leerzeichen voneinander.
  - Für einen Cookiewert können [Platzhalterwerte](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) verwendet werden.
  - Wenn kein Platzhalterwert angegeben wurde, ist diese Übereinstimmungsbedingung nur bei einer exakten Übereinstimmung erfüllt. Durch die Angabe von „Wert“ wird eine Übereinstimmung mit „Wert“ erzielt, aber nicht mit „Wert1“ oder „Wert2“.
  - Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob beim Vergleich des Cookiewerts einer Anforderung die Groß-/Kleinschreibung berücksichtigt wird.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Cookie Parameter Regex

Die Übereinstimmungsbedingung „Cookie Parameter Regex“ definiert einen Cookienamen und einen Cookiewert. Sie können [reguläre Ausdrücke](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) verwenden, um den gewünschten Cookiewert zu definieren.

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „Cookie Parameter Regex“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass Anforderungen das angegebene Cookie mit einem Wert enthalten, der dem angegebenen regulären Ausdruck entspricht.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung eines der folgenden Kriterien erfüllt:
  - Die Anforderung enthält das angegebene Cookie nicht.
  - Die Anforderung enthält das angegebene Cookie, aber der Wert entspricht nicht dem angegebenen regulären Ausdruck.
  
Wichtige Informationen:

- Cookiename:
  - Da reguläre Ausdrücke und Platzhalterwerte, z.B. Sternchen (*), beim Angeben eines Cookienamens nicht unterstützt werden, können nur exakte Übereinstimmungen von Cookienamen für Vergleiche herangezogen werden.
  - Für jede Instanz dieser Übereinstimmungsbedingung kann nur ein einziger Cookiename angegeben werden.
  - Beim Vergleichen von Cookienamen wird die Groß-/Kleinschreibung nicht beachtet.
- Cookiewert:
  - In Cookiewerten können reguläre Ausdrücke verwendet werden.
  - Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob beim Vergleich des Cookiewerts einer Anforderung die Groß-/Kleinschreibung berücksichtigt wird.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

Sie können ein Land anhand seines Ländercodes angeben. 

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „Country“ erfüllt ist:

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung die angegebenen Ländercodewerte enthält. 
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung die angegebenen Ländercodewerte nicht enthält.

Wichtige Informationen:

- Wenn Sie mehrere Ländercodes angeben möchten, trennen Sie diese durch ein einzelnes Leerzeichen voneinander.
- Bei der Angabe von Ländercodes werden keine Platzhalterzeichen unterstützt.
- Die Ländercodes „EU“ und „AP“ umfassen nicht alle IP-Adressen in diesen Regionen.
- Bestimmte Anforderungen geben möglicherweise keinen gültigen Ländercode zurück. Ein Fragezeichen (?) kennzeichnet Anforderungen, für die kein gültiger Ländercode ermittelt werden konnte.
- Bei Ländercodes wird die Groß-/Kleinschreibung berücksichtigt.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementieren des Länderfilters mit der Regel-Engine

Mit dieser Übereinstimmungsbedingung können Sie basierend auf dem Ursprungsstandort einer Anforderung eine Vielzahl von Anpassungen vornehmen. Das Verhalten des Features für die Länderfilterung kann beispielsweise mit der folgenden Konfiguration repliziert werden:

- URL Path Wildcard-Übereinstimmung: Legen Sie die [Übereinstimmungsbedingung „URL Path Wildcard“](#url-path-wildcard) auf das zu schützende Verzeichnis fest. 
    Fügen Sie am Ende des relativen Pfads ein Sternchen an, um sicherzustellen, dass der Zugriff auf alle untergeordneten Elemente durch diese Regel eingeschränkt wird.

- Country-Übereinstimmung: Legen Sie die Übereinstimmungsbedingung „Country“ auf die gewünschte Gruppe von Ländern fest.
  - Zulassen: Legen Sie die Übereinstimmungsbedingung „Country“ auf **Entspricht nicht** fest, um nur für die angegebenen Länder den Zugriff auf die gespeicherten Inhalte an dem Standort zuzulassen, der mit der Übereinstimmungsbedingung „URL Path Wildcard“ definiert wurde.
  - Blockieren: Legen Sie die Übereinstimmungsbedingung „Country“ auf **Entspricht** fest, um für die angegebenen Länder den Zugriff auf die gespeicherten Inhalte an dem Standort zu blockieren, der mit der Übereinstimmungsbedingung „URL Path Wildcard“ definiert wurde.

- Feature „Deny Access (403)“: Aktivieren Sie das [Feature „Deny Access (403)“](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403), um den Zulassen- bzw. Blockieren-Teil des Features für die Länderfilterung zu replizieren.

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Customer Origin

Wichtige Informationen:

- Die Übereinstimmungsbedingung „Customer Origin“ wird unabhängig davon erfüllt, ob Inhalt über eine CDN-URL oder eine Edge-CNAME-URL angefordert wird, die auf den ausgewählten Kundenursprung verweist.
- Eine Kundenursprungskonfiguration, auf die von einer Regel verwiesen wird, kann von der Seite „Customer Origin“ nicht gelöscht werden. Bevor Sie versuchen, eine Kundenursprungskonfiguration zu löschen, stellen Sie sicher, dass die folgenden Konfigurationen nicht darauf verweisen:
  - Eine Customer Origin-Übereinstimmungsbedingung
  - Eine Edge-CNAME-Konfiguration
- Verwenden Sie keine AND IF-Anweisung, um bestimmte Übereinstimmungsbedingungen zu kombinieren. Durch Kombination einer Customer Origin-Übereinstimmungsbedingung mit einer CDN Origin-Übereinstimmungsbedingung würde beispielsweise ein Übereinstimmungsmuster entstehen, für das nie eine Übereinstimmung gefunden werden kann. Aus diesem Grund können zwei Customer Origin-Übereinstimmungsbedingungen nicht mit einer AND IF-Anweisung kombiniert werden.

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Gerät

Die Übereinstimmungsbedingung „Device“ bestimmt Anforderungen von einem Mobilgerät anhand seiner Eigenschaften. Die Mobilgeräterkennung wird mithilfe von [WURFL](http://wurfl.sourceforge.net/) durchgeführt. 

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „Device“ erfüllt ist:

- **Entspricht:** Für diese Option ist es erforderlich, dass das Gerät des Anforderers mit dem angegebenen Wert übereinstimmt. 
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass das Gerät des Anforderers nicht mit dem angegebenen Wert übereinstimmt.

Wichtige Informationen:

- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um festzulegen, ob für den angegebenen Wert die Groß-/Kleinschreibung berücksichtigt wird.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### <a name="string-type"></a>Zeichenfolgentyp

Eine WURFL-Funktion akzeptiert normalerweise eine beliebige Kombination von Zahlen, Buchstaben und Zeichen. Aufgrund der hohen Flexibilität dieser Funktion müssen Sie auswählen, wie der Wert, der dieser Übereinstimmungsbedingung zugeordnet ist, interpretiert wird. In der folgenden Tabelle sind die verfügbaren Optionen beschrieben:

Type     | BESCHREIBUNG
---------|------------
Literal  | Wählen Sie diese Option, um zu verhindern, dass die meisten Zeichen eine spezielle Bedeutung erhalten, indem der [Literalwert](cdn-verizon-premium-rules-engine-reference.md#literal-values) verwendet wird.
Platzhalter | Wählen Sie diese Option, um alle [Platzhalterzeichen]([Platzhalterwerte](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) zu nutzen.
RegEx    | Wählen Sie diese Option, um [reguläre Ausdrücke](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) zu verwenden. Reguläre Ausdrücke sind hilfreich beim Definieren eines Musters für Zeichen.

#### <a name="wurfl-capabilities"></a>WURFL-Funktionen

Eine WURFL-Funktion bezieht sich auf eine Kategorie zur Beschreibung von mobilen Geräten. Mit der ausgewählten Funktion wird der Typ der Mobilgerätbeschreibung ermittelt, der zum Identifizieren von Anforderungen verwendet wird.

In der folgenden Tabelle sind WURFL-Funktionen und die zugehörigen Variablen für die Regel-Engine aufgeführt.

> [!NOTE]
> Die folgenden Variablen werden für die Features **Modify Client Request Header** und **Modify Client Response Header** unterstützt.

Funktion | Variable | BESCHREIBUNG | Beispielwerte
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | Eine Zeichenfolge, die den Markennamen des Geräts angibt. | Samsung
Device OS | %{wurfl_cap_device_os} | Eine Zeichenfolge, die das auf dem Gerät installierte Betriebssystem angibt. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | Eine Zeichenfolge, die die Versionsnummer des auf dem Gerät installierten Betriebssystems angibt. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | Ein boolescher Wert, der angibt, ob das Gerät die duale Ausrichtung unterstützt. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | Eine Zeichenfolge, die die bevorzugte Dokumenttypdefinition (DTD) des mobilen Geräts für den HTML-Inhalt angibt. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | Ein boolescher Wert, der angibt, ob das Gerät Base64-codierte Images unterstützt. | false
Is Android | %{wurfl_vcap_is_android} | Ein boolescher Wert, der angibt, ob für das Gerät das Android-Betriebssystem verwendet wird. | true
Is IOS | %{wurfl_vcap_is_ios} | Ein boolescher Wert, der angibt, ob für das Gerät das iOS-Betriebssystem verwendet wird. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | Ein boolescher Wert, der angibt, ob es sich um ein Smart TV-Gerät handelt. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | Ein boolescher Wert, der angibt, ob es sich beim Gerät um ein Smartphone handelt. | true
Is Tablet | %{wurfl_cap_is_tablet} | Ein boolescher Wert, der angibt, ob es sich beim Gerät um ein Tablet handelt. Diese Beschreibung ist unabhängig von Betriebssystem. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | Ein boolescher Wert, der angibt, ob das Gerät als drahtloses Gerät betrachtet wird. | true
Marketing Name | %{wurfl_cap_marketing_name} | Eine Zeichenfolge, die den Marketingnamen des Geräts angibt. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | Eine Zeichenfolge, die den Browser angibt, der zum Anfordern des Inhalts vom Gerät verwendet wird. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | Eine Zeichenfolge, die die Version des Browsers angibt, der zum Anfordern des Inhalts vom Gerät verwendet wird. | 31
Model Name | %{wurfl_cap_model_name} | Eine Zeichenfolge, die den Modellnamen des Geräts angibt. | s3
Progressive Download | %{wurfl_cap_progressive_download} | Ein boolescher Wert, der angibt, ob das Gerät die Wiedergabe von Audio- und Videodaten während des Herunterladens unterstützt. | true
Release Date | %{wurfl_cap_release_date} | Eine Zeichenfolge, die das Jahr und den Monat angibt, in dem das Gerät der WURFL-Datenbank hinzugefügt wurde.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | Eine ganze Zahl, die für das Gerät die Höhe in Pixel angibt. | 768
Resolution Width | %{wurfl_cap_resolution_width} | Eine ganze Zahl, die für das Gerät die Breite in Pixel angibt. | 1024

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Edge Cname

Wichtige Informationen:

- Die Liste mit den verfügbaren Edge-CNAMEs ist auf die Edge-CNAMEs beschränkt, die auf der Edge-CNAME-Seite der Plattform konfiguriert wurden, auf der die Regel-Engine konfiguriert wird.
- Bevor Sie versuchen, eine Edge-CNAME-Konfiguration zu löschen, stellen Sie sicher, dass keine Edge Cname-Übereinstimmungsbedingung darauf verweist. Edge CNAME-Konfigurationen, die in einer Regel definiert wurden, können nicht von der Edge-CNAME-Seite gelöscht werden.
- Verwenden Sie keine AND IF-Anweisung, um bestimmte Übereinstimmungsbedingungen zu kombinieren. Durch Kombination einer Edge Cname-Übereinstimmungsbedingung mit einer Customer Origin-Übereinstimmungsbedingung würde beispielsweise ein Übereinstimmungsmuster entstehen, für das nie eine Übereinstimmung gefunden werden kann. Aus diesem Grund können zwei Edge Cname-Übereinstimmungsbedingungen nicht mit einer AND IF-Anweisung kombiniert werden.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Referring Domain

Der Hostname des verweisenden Elements, über das der Inhalt angefordert wurde, bestimmt, ob die Referring Domain-Bedingung erfüllt wird.

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „Referring Domain“ erfüllt ist:

- **Entspricht:** Für diese Option ist es erforderlich, dass der Name des verweisenden Hosts mit den angegebenen Werten übereinstimmt. 
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass der Name des verweisenden Hosts nicht mit dem angegebenen Wert übereinstimmt.

Wichtige Informationen:

- Wenn Sie mehrere Hostnamen angeben möchten, trennen Sie die Namen durch ein einzelnes Leerzeichen voneinander.
- Für diese Übereinstimmungsbedingung werden [Platzhalterwerte](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) unterstützt.
- Wenn der angegebene Wert kein Sternchen enthält, muss er exakt mit dem Namen des verweisenden Hosts übereinstimmen. Bei der Angabe von „mydomain.com“ würde beispielsweise keine Übereinstimmung mit „www.mydomain.com“ erzielt.
- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob ein Vergleich unter Berücksichtigung der Groß-/Kleinschreibung durchgeführt wird.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Request Header Literal

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „Request Header Literal“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung den angegebenen Header enthält. Der Wert der Anforderung muss dem Wert entsprechen, der in dieser Übereinstimmungsbedingung definiert ist.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung eines der folgenden Kriterien erfüllt:
  - Die Anforderung enthält den angegebenen Header nicht.
  - Die Anforderung enthält den angegebenen Header, aber der Wert entspricht nicht dem Wert, der in dieser Übereinstimmungsbedingung definiert ist.
  
Wichtige Informationen:

- Beim Vergleichen von Headernamen wird die Groß-/Kleinschreibung nie beachtet. Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob bei Vergleichen von Headerwerten die Groß-/Kleinschreibung berücksichtigt wird.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Request Header Regex

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „Request Header Regex“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung den angegebenen Header enthält. Der Wert der Anforderung muss dem Muster entsprechen, das im angegebenen [regulären Ausdruck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) definiert ist.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung eines der folgenden Kriterien erfüllt:
  - Die Anforderung enthält den angegebenen Header nicht.
  - Die Anforderung enthält den angegebenen Header, aber der Wert entspricht nicht dem angegebenen regulären Ausdruck.

Wichtige Informationen:

- Headername:
  - Beim Vergleichen von Headernamen wird die Groß-/Kleinschreibung nicht beachtet.
  - Ersetzen Sie Leerzeichen im Headernamen durch „%20“.
- Headerwert:
  - In Headerwerten können reguläre Ausdrücke verwendet werden.
  - Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob bei Vergleichen von Headerwerten die Groß-/Kleinschreibung berücksichtigt wird.
  - Die Übereinstimmungsbedingung ist nur erfüllt, wenn ein Headerwert mindestens einem der angegebenen Muster genau entspricht.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Request Header Wildcard

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „Request Header Wildcard“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung den angegebenen Header enthält. Der Wert der Anforderung muss mindestens einem der Werte entsprechen, die in dieser Übereinstimmungsbedingung definiert sind.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung eines der folgenden Kriterien erfüllt:
  - Die Anforderung enthält den angegebenen Header nicht.
  - Die Anforderung enthält den angegebenen Header, aber der Wert entspricht keinem der angegebenen Werte.
  
Wichtige Informationen:

- Headername:
  - Beim Vergleichen von Headernamen wird die Groß-/Kleinschreibung nicht beachtet.
  - Leerzeichen im Headernamen sollten durch „%20“ ersetzt werden. Sie können diesen Wert auch verwenden, um Leerzeichen in einem Headerwert anzugeben.
- Headerwert:
  - Für einen Headerwert können [Platzhalterzeichen](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) verwendet werden.
  - Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob bei Vergleichen von Headerwerten die Groß-/Kleinschreibung berücksichtigt wird.
  - Diese Übereinstimmungsbedingung ist erfüllt, wenn ein Headerwert mindestens einem der angegebenen Muster genau entspricht.
  - Wenn Sie mehrere Werte angeben möchten, trennen Sie diese durch ein einzelnes Leerzeichen voneinander.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Request Method

Die Übereinstimmungsbedingung „Request Method“ wird nur erfüllt, wenn Objekte über die ausgewählte Anforderungsmethode angefordert werden. Folgende Anforderungsmethoden sind verfügbar:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Wichtige Informationen:

- Standardmäßig kann nur die GET-Anforderungsmethode zwischengespeicherten Inhalt im Netzwerk generieren. Alle anderen Anforderungsmethoden werden per Proxy durch das Netzwerk gesendet.
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Request Scheme

Die Übereinstimmungsbedingung „Request Scheme“ ist nur erfüllt, wenn Objekte über das ausgewählte Protokoll angefordert werden. Die verfügbaren Protokolle lauten:

- HTTP
- HTTPS

Wichtige Informationen:

- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>URL Path Directory

Identifiziert eine Anforderung anhand ihres relativen Pfads, also ohne den Dateinamen des angeforderten Objekts.

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „URL Path Directory“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung einen relativen URL-Pfad (ohne Dateiname) enthält, der mit dem angegebenen URL-Muster übereinstimmt.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung einen relativen URL-Pfad (ohne Dateiname) enthält, der nicht mit dem angegebenen URL-Muster übereinstimmt.

Wichtige Informationen:

- Verwenden Sie die Option **Relativ zu**, um anzugeben, ob der URL-Vergleich vor oder nach dem Inhaltszugriffspunkt beginnt. Der Inhaltszugriffspunkt ist der Teil des Pfads zwischen dem Verizon CDN-Hostnamen und dem relativen Pfad zum angeforderten Objekt (z.B. /800001/CustomerOrigin). Hiermit wird ein Standort anhand des Servertyps (z.B. CDN oder Kundenursprung) und Ihrer Kundenkontonummer identifiziert.

   Die folgenden Werte sind für die Option **Relativ zu** verfügbar:
  - **Stamm:** Gibt an, dass der URL-Vergleichspunkt direkt nach dem CDN-Hostnamen beginnt. 

  Beispiel: http:\//wpc.0001.&lt;Domäne&gt;/**800001/myorigin/myfolder**/index.htm

  - **Ursprung:** Gibt an, dass der URL-Vergleichspunkt nach dem Inhaltszugriffspunkt (z.B. /000001 oder /800001/myorigin) beginnt. Da der CNAME „\*.azureedge.net“ standardmäßig relativ zum Ursprungsverzeichnis des Verizon CDN-Hostnamens erstellt wird, sollten Azure CDN-Benutzer den Wert **Ursprung** verwenden. 

  Beispiel: https:\//&lt;Endpunkt&gt;.azureedge.net/**myfolder**/index.htm 

  Diese URL verweist auf den folgenden Verizon CDN-Hostnamen: http:\//wpc.0001.&lt;Domäne&gt;/800001/myorigin/**myfolder**/index.htm

- Vor dem URL-Vergleich wird eine Edge-CNAME-URL in eine CDN-URL umgeschrieben.

    Beispielsweise verweisen die beiden folgenden URLs auf dasselbe Objekt und verfügen daher über den gleichen URL-Pfad.
  - CDN-URL: http:\//wpc.0001.&lt;Domäne&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge-CNAME-URL: http:\//&lt;Endpunkt&gt;.azureedge.net/path/asset.htm
    
    Weitere Informationen:
  - Benutzerdefinierte Domäne: https:\//my.domain.com/path/asset.htm
    
    - URL-Pfad (relativ zum Stamm): /800001/CustomerOrigin/path/
    
    - URL-Pfad (relativ zum Ursprung): /path/

- Der Teil der URL, der für den URL-Vergleich verwendet wird, endet direkt vor dem Dateinamen des angeforderten Objekts. Bei dieser Art von Pfad ist ein nachgestellter Schrägstrich das letzte Zeichen.

- Ersetzen Sie alle Leerstellen im URL-Pfadmuster durch „%20“.

- Jedes URL-Pfadmuster kann ein oder mehrere Sternchen (*) enthalten, wobei jedes Sternchen für ein Zeichen oder eine Folge von Zeichen steht.

- Geben Sie mehrere URL-Pfade im Muster an, indem Sie als Trennzeichen jeweils ein einzelnes Leerzeichen verwenden.

    Beispiel: */sales/ */marketing/

- Beim Angeben eines URL-Pfads können [Platzhalterwerte](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) verwendet werden.

- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob ein Vergleich unter Berücksichtigung der Groß-/Kleinschreibung durchgeführt wird.

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>URL Path Extension

Identifiziert Anforderungen anhand der Dateierweiterung des angeforderten Objekts.

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „URL Path Extension“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die URL der Anforderung eine Dateierweiterung enthält, die genau mit dem angegebenen Muster überstimmt.

   Beispiel: Wenn Sie „htm“ angeben, ergibt sich eine Übereinstimmung für „htm“-Objekte, aber nicht für „html“-Objekte.  

- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die URL der Anforderung eine Dateierweiterung enthält, die nicht mit dem angegebenen Muster übereinstimmt.

Wichtige Informationen:

- Geben Sie die abzugleichenden Dateierweiterungen im Feld **Wert** ein. Lassen Sie den vorangestellten Punkt weg. Verwenden Sie also „htm“ anstelle von „.htm“.

- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob ein Vergleich unter Berücksichtigung der Groß-/Kleinschreibung durchgeführt wird.

- Geben Sie mehrere Dateierweiterungen an, indem Sie als Trennzeichen jeweils eine einzelne Leerstelle verwenden. 

    Beispiel: htm html

- Wenn Sie „htm“ angeben, ergeben sich beispielsweise Übereinstimmungen für „htm“-Objekte, aber nicht für „html“-Objekte.

#### <a name="sample-scenario"></a>Beispielszenario

Bei der folgenden Beispielkonfiguration wird vorausgesetzt, dass diese Übereinstimmungsbedingung erfüllt ist, wenn sich für eine Anforderung eine Übereinstimmung mit einer der angegebenen Erweiterungen ergibt.

Wertangabe: asp aspx php html

Diese Übereinstimmungsbedingung ist erfüllt, wenn URLs gefunden werden, die auf die folgenden Erweiterungen enden:

- .asp
- .aspx
- .php
- .html

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>URL Path Filename

Identifiziert Anforderungen anhand des Dateinamens des angeforderten Objekts. Bei dieser Übereinstimmungsbedingung besteht ein Dateiname aus dem Namen des angeforderten Objekts, einem Punkt und der Dateierweiterung (z.B. „index.html“).

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „URL Path Filename“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung im URL-Pfad einen Dateinamen enthält, der mit dem angegebenen Muster übereinstimmt.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung im URL-Pfad einen Dateinamen enthält, der nicht mit dem angegebenen Muster übereinstimmt.

Wichtige Informationen:

- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob ein Vergleich unter Berücksichtigung der Groß-/Kleinschreibung durchgeführt wird.

- Geben Sie mehrere Dateinamen an, indem Sie als Trennzeichen jeweils eine einzelne Leerstelle verwenden.

    Beispiel: index.htm index.html

- Ersetzen Sie Leerzeichen in einem Dateinamen durch „%20“.

- Für einen Dateinamen können [Platzhalterwerte](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) verwendet werden. Beispielsweise kann jedes Dateinamensmuster ein oder mehrere Sternchen (*) enthalten, wobei jedes Sternchen für ein Zeichen oder eine Folge von Zeichen steht.

- Wenn keine Platzhalterzeichen angegeben wurden, ist diese Übereinstimmungsbedingung nur bei einer exakten Entsprechung erfüllt.

    Wenn Sie beispielsweise „presentation.ppt“ angeben, ergibt sich eine Übereinstimmung für ein Objekt mit dem Namen „presentation.ppt“, aber nicht für „presentation.pptx“.

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL Path Literal

Vergleicht den URL-Pfad einer Anforderung, einschließlich Dateiname, mit dem angegebenen Wert.

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „URL Path Literal“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung einen URL-Pfad enthält, der mit dem angegebenen Muster übereinstimmt.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung einen URL-Pfad enthält, der nicht mit dem angegebenen Muster übereinstimmt.

Wichtige Informationen:

- Verwenden Sie die Option **Relativ zu**, um anzugeben, ob der URL-Vergleichspunkt vor oder nach dem Inhaltszugriffspunkt beginnt. 

    Die folgenden Werte sind für die Option **Relativ zu** verfügbar:
  - **Stamm:** Gibt an, dass der URL-Vergleichspunkt direkt nach dem CDN-Hostnamen beginnt.

    Beispiel: http:\//wpc.0001.&lt;Domäne&gt;/**800001/myorigin/myfolder/index.htm**

  - **Ursprung:** Gibt an, dass der URL-Vergleichspunkt nach dem Inhaltszugriffspunkt (z.B. /000001 oder /800001/myorigin) beginnt. Da der CNAME „\*.azureedge.net“ standardmäßig relativ zum Ursprungsverzeichnis des Verizon CDN-Hostnamens erstellt wird, sollten Azure CDN-Benutzer den Wert **Ursprung** verwenden. 

    Beispiel: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  Diese URL verweist auf den folgenden Verizon CDN-Hostnamen: http:\//wpc.0001.&lt;Domäne&gt;/800001/myorigin/**myfolder/index.htm**

- Vor einem URL-Vergleich wird eine Edge-CNAME-URL in eine CDN-URL umgeschrieben.

Beispielsweise verweisen die beiden folgenden URLs auf dasselbe Objekt und verfügen daher über den gleichen URL-Pfad:

- CDN-URL: http:\//wpc.0001.&lt;Domäne&gt;/800001/CustomerOrigin/path/asset.htm
- Edge-CNAME-URL: http:\//&lt;Endpunkt&gt;.azureedge.net/path/asset.htm

    Weitere Informationen:
    
    - URL-Pfad (relativ zum Stamm): /800001/CustomerOrigin/path/asset.htm
   
    - URL-Pfad (relativ zum Ursprung): /path/asset.htm

- Abfragezeichenfolgen in der URL werden ignoriert.
- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob ein Vergleich unter Berücksichtigung der Groß-/Kleinschreibung durchgeführt wird.
- Der für diese Übereinstimmungsbedingung angegebene Wert wird mit dem relativen Pfad der exakten Anforderung verglichen, die vom Client gesendet wird.

- Verwenden Sie die Übereinstimmungsbedingung [URL Path Directory](#url-path-directory) oder [URL Path Wildcard](#url-path-wildcard), um alle Anforderungen abzugleichen, die an ein bestimmtes Verzeichnis gesendet werden.

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL Path Regex

Vergleicht den URL-Pfad einer Anforderung mit dem angegebenen [regulären Ausdruck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „URL Path Regex“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung einen URL-Pfad enthält, der mit dem angegebenen regulären Ausdruck übereinstimmt.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung einen URL-Pfad enthält, der nicht mit dem angegebenen regulären Ausdruck übereinstimmt.

Wichtige Informationen:

- Vor einem URL-Vergleich wird eine Edge-CNAME-URL in eine CDN-URL umgeschrieben.

    Beispielsweise verweisen hier beide URLs auf dasselbe Objekt und verfügen daher über den gleichen URL-Pfad.

     - CDN-URL: http:\//wpc.0001.&lt;Domäne&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge-CNAME-URL: http:\//my.domain.com/path/asset.htm

    Weitere Informationen:
    
     - URL-Pfad: /800001/CustomerOrigin/path/asset.htm

- Abfragezeichenfolgen in der URL werden ignoriert.
    
- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob ein Vergleich unter Berücksichtigung der Groß-/Kleinschreibung durchgeführt wird.
    
- Leerzeichen im URL-Pfad sollten durch „%20“ ersetzt werden.

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>URL Path Wildcard

Vergleicht den URL-Pfad einer Anforderung mit dem angegebenen Platzhaltermuster.

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „URL Path Wildcard“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung einen URL-Pfad enthält, der mit dem angegebenen Platzhaltermuster übereinstimmt.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung einen URL-Pfad enthält, der nicht mit dem angegebenen Platzhaltermuster übereinstimmt.

Wichtige Informationen:

- Option **Relativ zu**: Mit dieser Option wird bestimmt, ob der URL-Vergleichspunkt vor oder nach dem Inhaltszugriffspunkt beginnt.

   Für diese Option können die folgenden Werte verwendet werden:
     - **Stamm:** Gibt an, dass der URL-Vergleichspunkt direkt nach dem CDN-Hostnamen beginnt.

       Beispiel: http:\//wpc.0001.&lt;Domäne&gt;/**800001/myorigin/myfolder/index.htm**

     - **Ursprung:** Gibt an, dass der URL-Vergleichspunkt nach dem Inhaltszugriffspunkt (z.B. /000001 oder /800001/myorigin) beginnt. Da der CNAME „\*.azureedge.net“ standardmäßig relativ zum Ursprungsverzeichnis des Verizon CDN-Hostnamens erstellt wird, sollten Azure CDN-Benutzer den Wert **Ursprung** verwenden. 

       Beispiel: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     Diese URL verweist auf den folgenden Verizon CDN-Hostnamen: http:\//wpc.0001.&lt;Domäne&gt;/800001/myorigin/**myfolder/index.htm**

- Vor einem URL-Vergleich wird eine Edge-CNAME-URL in eine CDN-URL umgeschrieben.

    Beispielsweise verweisen die beiden folgenden URLs auf dasselbe Objekt und verfügen daher über den gleichen URL-Pfad:
     - CDN-URL: http://wpc.0001.&lt;Domäne&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge-CNAME-URL: http:\//&lt;Endpunkt&gt;.azureedge.net/path/asset.htm
    
    Weitere Informationen:
    
     - URL-Pfad (relativ zum Stamm): /800001/CustomerOrigin/path/asset.htm
    
     - URL-Pfad (relativ zum Ursprung): /path/asset.htm
    
- Sie können mehrere URL-Pfade angeben, indem Sie sie durch ein einzelnes Leerzeichen voneinander trennen.

   Beispiel: /marketing/asset.* /sales/\*.htm

- Abfragezeichenfolgen in der URL werden ignoriert.
    
- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob ein Vergleich unter Berücksichtigung der Groß-/Kleinschreibung durchgeführt wird.
    
- Ersetzen Sie Leerzeichen im URL-Pfad durch „%20“.
    
- Für den Wert, der für einen URL-Pfad angegeben wird, können [Platzhalterwerte](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) verwendet werden. Jedes URL-Pfadmuster kann ein oder mehrere Sternchen (*) enthalten, wobei jedes Sternchen für ein Zeichen oder eine Folge von Zeichen stehen kann.

#### <a name="sample-scenarios"></a>Beispielszenarien

Bei den Beispielkonfigurationen in der folgenden Tabelle wird vorausgesetzt, dass diese Übereinstimmungsbedingung erfüllt ist, wenn sich für eine Anforderung eine Übereinstimmung mit dem angegebenen URL-Muster ergibt:

Wert                   | Relativ zu    | Ergebnis 
------------------------|----------------|-------
\*/test.html \*/test.php  | Stamm oder Ursprung | Für dieses Muster ergeben sich in beliebigen Ordnern Übereinstimmungen für Anforderungen von Objekten mit dem Namen „test.html“ oder „test.php“.
/80ABCD/origin/text/*   | Stamm           | Für dieses Muster ergibt sich eine Übereinstimmung, wenn das angeforderte Objekt die folgenden Kriterien erfüllt: <br />- Es muss sich an einem Kundenursprungsort mit dem Namen „origin“ befinden. <br />- Der relative Pfad muss mit einem Ordner mit dem Namen „text“ beginnen. Dies bedeutet, dass sich das angeforderte Objekt entweder im Ordner „text“ oder in einem der rekursiven Unterordner befinden kann.
*/css/* */js/*          | Stamm oder Ursprung | Für dieses Muster ergeben sich Übereinstimmungen für alle CDN- oder Edge-CNAME-URLs, die den Ordner „css“ oder „js“ enthalten.
*.jpg *.gif *.png       | Stamm oder Ursprung | Für dieses Muster ergeben sich Übereinstimmungen für alle CDN- oder Edge-CNAME-URLs, die auf „.jpg“, „.gif“ oder „.png“ enden. Eine alternative Möglichkeit zum Angeben dieses Musters ist die Verwendung der [Übereinstimmungsbedingung „URL Path Extension“](#url-path-extension).
/images/\* /media/\*      | Origin         | Für dieses Muster ergibt sich eine Übereinstimmung für CDN- oder Edge-CNAME-URLs, deren relativer Pfad mit dem Ordner „images“ oder „media“ beginnt. <br />- CDN-URL: http:\//wpc.0001.&lt;Domäne&gt;/800001/myorigin/images/sales/event1.png<br />- Beispiel für Edge-CNAME-URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL Query Literal

Vergleicht die Abfragezeichenfolge einer Anforderung mit dem angegebenen Wert.

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „URL Query Literal“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung eine URL-Abfragezeichenfolge enthält, die mit der angegebenen Abfragezeichenfolge übereinstimmt.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung eine URL-Abfragezeichenfolge enthält, die nicht mit der angegebenen Abfragezeichenfolge übereinstimmt.

Wichtige Informationen:

- Diese Übereinstimmungsbedingung ist nur bei genauen Übereinstimmungen der Abfragezeichenfolge erfüllt.
    
- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob bei Vergleichen von Abfragezeichenfolgen die Groß-/Kleinschreibung berücksichtigt wird.
    
- Fügen Sie in den Text des Abfragezeichenfolgen-Werts kein vorangestelltes Fragezeichen (?) ein.
    
- Für bestimmte Zeichen ist eine URL-Codierung erforderlich. Verwenden Sie das Prozentzeichen, um für die folgenden Zeichen die URL-Codierung durchzuführen:

   Zeichen | URL-Codierung
   ----------|---------
   Leerzeichen     | %20
   &         | %25

- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>URL Query Parameter

Identifiziert Anforderungen, die den angegebenen Abfragezeichenfolgenparameter enthalten. Dieser Parameter wird auf einen Wert festgelegt, der mit einem angegebenen Muster übereinstimmt. Abfragezeichenfolgenparameter (z.B. parameter=value) in der Anforderungs-URL bestimmen, ob die Bedingung erfüllt ist. Mit dieser Übereinstimmungsbedingung wird ein Abfragezeichenfolgenparameter anhand des Namens identifiziert, und für den Parameterwert werden ein oder mehrere Werte akzeptiert. 

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „URL Query Parameter“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass eine Anforderung den angegebenen Parameter mit einem Wert enthält, der mindestens einem der in dieser Übereinstimmungsbedingung definierten Werte entspricht.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung eines der folgenden Kriterien erfüllt:
  - Die Anforderung enthält den angegebenen Parameter nicht.
  - Die Anforderung enthält den angegebenen Parameter, aber der Wert entspricht keinem der Werte, die in dieser Übereinstimmungsbedingung definiert sind.

Diese Übereinstimmungsbedingung ermöglicht das einfache Angeben von Kombinationen aus Parametername und -wert. Erwägen Sie die Nutzung der Übereinstimmungsbedingung [URL Query Wildcard](#url-query-wildcard), um für eine höhere Flexibilität beim Abgleichen eines Abfragezeichenfolgenparameters zu sorgen.

Wichtige Informationen:

- Für jede Instanz dieser Übereinstimmungsbedingung kann nur ein einzelner URL-Abfrageparameter angegeben werden.
    
- Da beim Angeben eines Parameternamens Platzhalterwerte nicht unterstützt werden, können nur genaue Übereinstimmungen von Parameternamen für Vergleiche herangezogen werden.
- Parameterwerte können [Platzhalterwerte](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) enthalten.
   - Jedes Parameterwertmuster kann ein oder mehrere Sternchen (*) enthalten, wobei jedes Sternchen für ein Zeichen oder eine Folge von Zeichen stehen kann.
   - Für bestimmte Zeichen ist eine URL-Codierung erforderlich. Verwenden Sie das Prozentzeichen, um für die folgenden Zeichen die URL-Codierung durchzuführen:

       Zeichen | URL-Codierung
       ----------|---------
       Leerzeichen     | %20
       &         | %25

- Geben Sie mehrere Abfragezeichenfolgen-Parameterwerte an, indem Sie als Trennzeichen jeweils ein einzelnes Leerzeichen verwenden. Diese Übereinstimmungsbedingung ist erfüllt, wenn eine Anforderung eine der angegebenen Name-Wert-Kombinationen enthält.

   - Beispiel 1:

     - Konfiguration:

       ValueA ValueB

     - Diese Konfiguration entspricht den folgenden Abfragezeichenfolgenparametern:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Beispiel 2:

     - Konfiguration: 

        Value%20A Value%20B

     - Diese Konfiguration entspricht den folgenden Abfragezeichenfolgenparametern:

       Parameter1=Value%20A

       Parameter1=Value%20B

- Diese Übereinstimmungsbedingung ist nur erfüllt, wenn sich für mindestens eine der angegebenen Kombinationen aus Abfragezeichenfolgenname und -wert eine genaue Übereinstimmung ergibt.

   Für die Konfiguration aus dem vorherigen Beispiel wird die Kombination „Parameter1=ValueAdd“ aus Abfragezeichenfolgenname und -wert beispielsweise nicht als Übereinstimmung angesehen. Wenn Sie aber einen der folgenden Werte angeben, ergibt sich für diese Name-Wert-Kombination eine Übereinstimmung:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob bei Vergleichen von Abfragezeichenfolgen die Groß-/Kleinschreibung berücksichtigt wird.
    
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### <a name="sample-scenarios"></a>Beispielszenarien

Im folgenden Beispiel wird veranschaulicht, wie diese Option in bestimmten Situationen funktioniert:

NAME  | Wert |  Ergebnis
------|-------|--------
Benutzer  | Joe   | Für dieses Muster ergibt sich eine Übereinstimmung, wenn die Abfragezeichenfolge für eine angeforderte URL „?user=joe“ lautet.
Benutzer  | *     | Für dieses Muster ergibt sich eine Übereinstimmung, wenn die Abfragezeichenfolge für eine angeforderte URL den Parameter „User“ enthält.
E-Mail | Joe\* | Für dieses Muster ergibt sich eine Übereinstimmung, wenn die Abfragezeichenfolge für eine angeforderte URL einen Email-Parameter enthält, der mit „Joe“ beginnt.

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL Query Regex

Identifiziert Anforderungen, die den angegebenen Abfragezeichenfolgenparameter enthalten. Dieser Parameter wird auf einen Wert festgelegt, der mit einem angegebenen [regulären Ausdruck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) übereinstimmt.

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „URL Query Regex“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung eine URL-Abfragezeichenfolge enthält, die mit dem angegebenen regulären Ausdruck übereinstimmt.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung eine URL-Abfragezeichenfolge enthält, die nicht mit dem angegebenen regulären Ausdruck übereinstimmt.

Wichtige Informationen:

- Diese Übereinstimmungsbedingung ist nur erfüllt, wenn sich genaue Übereinstimmungen für den angegebenen regulären Ausdruck ergeben.
    
- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob bei Vergleichen von Abfragezeichenfolgen die Groß-/Kleinschreibung berücksichtigt wird.
    
- Bei dieser Option beginnt eine Abfragezeichenfolge mit dem ersten Zeichen nach dem Fragezeichen-Trennzeichen (?) für die Abfragezeichenfolge.
    
- Für bestimmte Zeichen ist eine URL-Codierung erforderlich. Verwenden Sie das Prozentzeichen, um für die folgenden Zeichen die URL-Codierung durchzuführen:

   Zeichen | URL-Codierung | Wert
   ----------|--------------|------
   Leerzeichen     | %20          | \%20
   &         | %25          | \%25

   Beachten Sie, dass Prozentzeichen mit Escapezeichen versehen werden müssen.

- Versehen Sie spezielle Zeichen regulärer Ausdrücke (z.B. \^$.+) mit doppelten Escapezeichen, um einen umgekehrten Schrägstrich in den regulären Ausdruck einzubinden.

   Beispiel:

   Wert | Interpretiert als 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL Query Wildcard

Vergleicht die angegebenen Werte mit der Abfragezeichenfolge der Anforderung.

Die Option **Entspricht**/**Entspricht nicht** bestimmt die Bedingungen, unter denen die Übereinstimmungsbedingung „URL Query Wildcard“ erfüllt ist.

- **Entspricht:** Für diese Option ist es erforderlich, dass die Anforderung eine URL-Abfragezeichenfolge enthält, die mit dem angegebenen Platzhalterwert übereinstimmt.
- **Entspricht nicht:** Für diese Option ist es erforderlich, dass die Anforderung eine URL-Abfragezeichenfolge enthält, die nicht mit dem angegebenen Platzhalterwert übereinstimmt.

Wichtige Informationen:

- Bei dieser Option beginnt eine Abfragezeichenfolge mit dem ersten Zeichen nach dem Fragezeichen-Trennzeichen (?) für die Abfragezeichenfolge.
- Parameterwerte können [Platzhalterwerte](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) enthalten:
   - Jedes Parameterwertmuster kann ein oder mehrere Sternchen (*) enthalten, wobei jedes Sternchen für ein Zeichen oder eine Folge von Zeichen stehen kann.
   - Für bestimmte Zeichen ist eine URL-Codierung erforderlich. Verwenden Sie das Prozentzeichen, um für die folgenden Zeichen die URL-Codierung durchzuführen:

     Zeichen | URL-Codierung
     ----------|---------
     Leerzeichen     | %20
     &         | %25

- Wenn Sie mehrere Werte angeben möchten, trennen Sie diese durch ein einzelnes Leerzeichen voneinander.

   Beispiel:  *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Diese Übereinstimmungsbedingung ist nur erfüllt, wenn sich genaue Übereinstimmungen mit mindestens einem der angegebenen Abfragezeichenfolgenmuster ergeben.
    
- Verwenden Sie die Option **Groß-/Kleinschreibung ignorieren**, um zu steuern, ob bei Vergleichen von Abfragezeichenfolgen die Groß-/Kleinschreibung berücksichtigt wird.
    
- Aufgrund der Art und Weise, in der Cacheeinstellungen nachverfolgt werden, ist diese Übereinstimmungsbedingung mit den folgenden Features nicht kompatibel:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### <a name="sample-scenarios"></a>Beispielszenarien

Im folgenden Beispiel wird veranschaulicht, wie diese Option in bestimmten Situationen funktioniert:

 NAME                 | BESCHREIBUNG
 ---------------------|------------
user=joe              | Für dieses Muster ergibt sich eine Übereinstimmung, wenn die Abfragezeichenfolge für eine angeforderte URL „?user=joe“ lautet.
\*user=\* \*optout=\* | Für dieses Muster ergibt sich eine Übereinstimmung, wenn die CDN-URL-Abfrage entweder den Parameter „user“ oder „optout“ enthält.

[Nach oben](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure Content Delivery Network](cdn-overview.md)
- [Regel-Engine – Referenz](cdn-verizon-premium-rules-engine-reference.md)
- [Regel-Engine – bedingte Ausdrücke](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regel-Engine – Features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Überschreiben des HTTP-Standardverhaltens mithilfe der Regel-Engine](cdn-verizon-premium-rules-engine.md)
