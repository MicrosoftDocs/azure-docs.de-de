---
title: HTTP-Variablen für Azure CDN-Regel-Engine | Microsoft-Dokumentation
description: HTTP-Variablen ermöglichen Ihnen das Abrufen von HTTP-Anforderungs- und -Antwortmetadaten.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: d572da27cee33cf546933e55a59c27dac4c1efd9
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475207"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>HTTP-Variablen für Azure CDN-Regel-Engine
HTTP-Variablen stellen die Methoden für den Abruf von HTTP-Anforderungs- und -Antwortmetadaten bereit. Diese Metadaten können dann zur dynamischen Änderung einer Anforderung oder Antwort verwendet werden. Die Verwendung von HTTP-Variablen ist auf die folgenden Regel-Engine-Features beschränkt :

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definitionen
In der folgenden Tabelle sind die unterstützten HTTP-Variablen beschrieben. Ein leerer Wert wird zurückgegeben, wenn GEO-Metadaten (z.B. Postleitzahl) für eine bestimmte Anforderung nicht verfügbar sind.


| NAME | Variable | BESCHREIBUNG | Beispielwert |
| ---- | -------- | ----------- | ------------ |
| ASN (Anfordernde Person) | %{geo_asnum} | Gibt die AS-Nummer der anfordernden Person an. <br /><br />**Veraltet:** %{virt_dst_asnum}. <br />Diese Variable wurde zugunsten von „%{geo_asnum}“ als veraltet markiert. Eine Regel mit dieser veralteten Variable funktioniert zwar weiterhin, Sie sollten sie jedoch so aktualisieren, dass die neue Variable verwendet wird. | AS15133 |
| Ort (Anfordernde Person) | %{geo_city} | Gibt den Ort der anfordernden Person an. | Los Angeles |
| Kontinent (Anfordernde Person) | %{geo_continent} | Gibt den Kontinent der anfordernden Person über die jeweilige Abkürzung an. <br />Gültige Werte sind: <br />AF: Afrika<br />AS: Asien<br />EU: Europa<br />NA: Nordamerika<br />OC: Ozeanien<br />SA: Südamerika<br /><br />**Veraltet:** %{virt_dst_continent}. <br />Diese Variable wurde zugunsten von „%{geo_continent}“ als veraltet markiert. <br />Eine Regel mit dieser veralteten Variable funktioniert zwar weiterhin, Sie sollten sie jedoch so aktualisieren, dass die neue Variable verwendet wird.| – |
| Cookiewert | %{cookie_Cookie} | Gibt den Wert zurück, der dem durch den Cookieausdruck identifizierten Cookieschlüssel entspricht. | Verwendungsbeispiel: <br />%{cookie__utma}<br /><br />Beispielwert:<br />111662281.2.10.1222100123 |
| Land (Anfordernde Person) | %{geo_country} | Gibt das Herkunftsland der anfordernden Person über den jeweiligen Ländercode an. <br />**Veraltet:** %{virt_dst_country}. <br /><br />Diese Variable wurde zugunsten von „%{geo_country}“ als veraltet markiert. Eine Regel mit dieser veralteten Variable funktioniert zwar weiterhin, Sie sollten sie jedoch so aktualisieren, dass die neue Variable verwendet wird. | US |
| Designated Market Area (Anfordernde Person) | %{geo_dma_code} |Gibt den Medienmarkt der anfordernden Person anhand des Regionscodes an. <br /><br />Dieses Feld gilt nur für Anforderungen, die aus den Vereinigten Staaten stammen.| 745 |
| HTTP-Anforderungsmethode | %{request_method} | Gibt die HTTP-Anforderungsmethode an. | GET |
| HTTP-Statuscode | %{status} | Gibt den HTTP-Statuscode für die Antwort an. | 200 |
| IP-Adresse (Anfordernde Person) | %{virt_dst_addr} | Gibt die IP-Adresse des Anforderers an. | 192.168.1.1 |
| Breitengrad (Anfordernde Person) | %{geo_latitude} | Gibt den Breitengrad der anfordernden Person an. | 34.0995 |
| Längengrad (Anfordernde Person) | %{geo_longitude} | Gibt den Längengrad der anfordernden Person an. | -118.4143 |
| Metropolitan Statistical Area (Anfordernde Person) | %{geo_metro_code} | Gibt die Metropolregion der anfordernden Person an. <br /><br />Dieses Feld gilt nur für Anforderungen, die aus den Vereinigten Staaten stammen.<br />| 745 |
| Port (Anfordernde Person) | %{virt_dst_port} | Gibt den kurzlebigen Port der anfordernden Person an. | 55885 |
| Postleitzahl (Anfordernde Person) | %{geo_postal_code} | Gibt die Postleitzahl der anfordernden Person an. | 90210 |
| Abfragezeichenfolge gefunden | %{is_args} | Der Wert für diese Variable variiert je nachdem, ob die Anforderung eine Abfragezeichenfolge enthält.<br /><br />– Abfragezeichenfolge gefunden: ?<br />- Keine Abfragezeichenfolge: NULL | ? |
| Abfragezeichenfolgen-Parameter gefunden | %{is_amp} | Der Wert für diese Variable variiert je nachdem, ob die Anforderung mindestens einen Abfragezeichenfolge-Parameter enthält.<br /><br />– Parameter gefunden: &<br />- Keine Parameter: NULL | & |
| Parameterwert der Abfragezeichenfolge | %{arg_&lt;parameter&gt;} | Gibt den Wert zurück, der dem durch den &lt;Parameter&gt;-Ausdruck identifizierten Abfragezeichenfolgen-Parameter entspricht. | Verwendungsbeispiel: <br />%{arg_language}<br /><br />Beispiel für Abfragezeichenfolge-Parameter: <br />?language=en<br /><br />Beispielwert: en |
| Abfragezeichenfolgenwert | %{query_string} | Gibt den vollständigen in der Anforderungs-URL definierten Abfragezeichenfolgenwert an. |key1=val1&key2=val2&key3=val3 |
| Verweisdomäne | %{referring_domain} | Gibt die im Verweisanforderungsheader definierte Domäne an. | <www.google.com> |
| Region (Anfordernde Person) | %{geo_region} | Gibt die Region der anfordernden Person (z.B. Bundesland oder Kanton) über deren alphanumerische Abkürzung an. | CA |
| Anforderungsheaderwert | %{http_RequestHeader} | Gibt den Wert zurück, der dem durch den RequestHeader-Ausdruck identifizierten Anforderungsheader entspricht. <br /><br />Wenn der Name des Anforderungsheaders einen Bindestrich (z.B. Benutzer-Agent) enthält, ersetzen Sie diesen durch einen Unterstrich (z.B. Benutzer_Agent).| Beispielverwendung: %{http_Connection}<br /><br />Beispielwert: Keep-Alive | 
| Anforderungshost | %{host} | Gibt den in der Anforderungs-URL definierten Host an. | <www.mydomain.com> |
| Anforderungsprotokoll | %{request_protocol} | Gibt das Anforderungsprotokoll an. | HTTP/1.1 |
| Request Scheme | %{scheme} | Gibt das Anforderungsschema an. |http |
| Anforderungs-URI (relativ) | %{request_uri} | Gibt den relativen Pfad, einschließlich der Abfragezeichenfolge, der im Anforderungs-URI definiert ist. | /marketing/foo.js?loggedin=true |
| Anforderungs-URI (relativ, ohne Abfragezeichenfolge) | %{uri} | Gibt den relativen Pfad zum angeforderten Inhalt an. <br /><br/>Wichtige Informationen:<br />– Dieser relative Pfad schließt die Abfragezeichenfolge aus.<br />– Dieser relative Pfad spiegelt erneute URL-Generierungen wider. Eine URL wird unter den folgenden Bedingungen neu generiert:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Feature „URL Rewrite“: Dieses Feature generiert den im Anforderungs-URI definierten relativen Pfad neu.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge-CNAME-URL: Diese Art von Anforderung wird in die entsprechende CDN-URL umgeschrieben. |/800001/corigin/rewrittendir/foo.js |
| Anforderungs-URI | %{request} | Beschreibt die Anforderung. <br />Syntax: &lt;HTTP-Methode&gt; &lt;relativer Pfad&gt; &lt;HTTP-Protokoll&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Antwortheaderwert | %{resp_&lt;ResponseHeader&gt;} | Gibt den Wert zurück, der dem durch den &lt;ResponseHeader&gt;-Ausdruck identifizierten Antwortheader entspricht. <br /><br />Wenn der Name des Anwortheaders einen Bindestrich (z.B. Benutzer-Agent) enthält, ersetzen Sie diesen durch einen Unterstrich (z.B. Benutzer_Agent). | Beispielverwendung: %{resp_Content_Length}<br /><br />Beispielwert: 100 |

## <a name="usage"></a>Verwendung
In der folgenden Tabelle ist die richtige Syntax zum Angeben einer HTTP-Variable beschrieben.


| Syntax | Beispiel | BESCHREIBUNG |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Verwenden Sie diese Syntax zum Abrufen des gesamten Werts, welcher dem angegebenen &lt;HTTPVariable&gt;-Element entspricht. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Verwenden Sie diese Syntax zum Festlegen des Falls für den gesamten Wert, welcher dem angegebenen &lt;HTTPVariableDelimiter&gt;-Element entspricht. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Verwenden Sie einen regulären Ausdruck für &lt;HTTPVariableDelimiterExpression&gt; um den Wert einer HTTP-Variablen zu ersetzen, zu löschen oder zu bearbeiten. |

HTTP-Variablennamen unterstützen nur alphabetische Zeichen und Unterstriche. Konvertieren Sie nicht unterstützte Zeichen in Unterstriche.

## <a name="delimiter-reference"></a>Trennzeichenverweis
Nach einer HTTP-Variablen kann ein Trennzeichen angegeben werden, um eine der folgenden Auswirkungen zu erzielen:

- Transformieren des der Variable zugeordneten Werts

     Beispiel: Konvertieren des gesamten Werts in Kleinbuchstaben.

- Löschen des der Variable zugeordneten Werts

- Bearbeiten des der Variable zugeordneten Werts

     Beispiel: Verwenden Sie reguläre Ausdrücke, um den der HTTP-Variable zugeordneten Wert zu ändern.

Die Trennzeichen sind in der folgenden Tabelle beschrieben.

| Trennzeichen | BESCHREIBUNG |
| --------- | ----------- |
| := | Gibt an, dass der Variablen ein Standardwert zugewiesen wird, wenn eine der folgenden Bedingungen auf sie zutrifft: <br />– Fehlt <br />– Auf NULL festgelegt. |
| :+ | Gibt an, dass der Variablen ein Standardwert zugewiesen wird, wenn ihr ein Wert zugewiesen wurde. |
| : | Gibt an, dass eine Teilzeichenfolge des Werts erweitert wird, die der Variablen zugewiesen ist. |
| # | Gibt an, dass das nach diesem Trennzeichen angegebene Muster gelöscht werden soll, wenn es sich am Anfang des der Variablen zugeordneten Werts befindet. |
| % | Gibt an, dass das nach diesem Trennzeichen angegebene Muster gelöscht werden soll, wenn es sich am Ende des der Variablen zugeordneten Werts befindet. <br />Diese Definition ist nur anwendbar, wenn das Symbol „%“ als Trennzeichen verwendet wird. |
| / | Begrenzt eine HTTP-Variable oder ein Muster. |
| // | Sucht und ersetzt alle Instanzen des angegebenen Musters. |
| /= | Sucht und kopiert alle Vorkommen des angegebenen Musters und generiert sie erneut. |
| , | Konvertiert den der HTTP-Variablen zugeordneten Wert in Kleinbuchstaben. |
| ^ | Konvertiert den der HTTP-Variablen zugeordneten Wert in Großbuchstaben. |
| ,, | Konvertiert alle Instanzen des angegebenen Zeichens in dem der HTTP-Variablen zugeordneten Wert in Kleinbuchstaben. |
| ^^ | Konvertiert alle Instanzen des angegebenen Zeichens in dem der HTTP-Variablen zugeordneten Wert in Großbuchstaben. |

## <a name="exceptions"></a>Ausnahmen
In der folgenden Tabelle sind Umstände beschrieben, unter denen der angegebene Text nicht als HTTP-Variable behandelt wird.

| Bedingung | BESCHREIBUNG | Beispiel |
| --------- | ----------- | --------|
| „%“ mit Escapezeichen versehen | Das Prozentzeichen kann durch die Verwendung eines umgekehrten Schrägstrichs mit einem Escapezeichen versehen werden. <br />Der Beispielwert auf der rechten Seite wird als Literalwert und nicht als HTTP-Variable behandelt.| \%{host} |
| Unbekannte Variablen | Für unbekannte Variablen wird immer eine leere Zeichenfolge zurückgegeben. | %{unknown_variable} |
| Ungültige Zeichen oder Syntax | Variablen, die ungültige Zeichen oder eine ungültige Syntax enthalten, werden als Literalwerte behandelt. <br /><br />Beispiel 1: Der angegebene Wert enthält ein ungültiges Zeichen (z. B. „-“). <br /><br />Beispiel 2: Der angegebene Wert enthält doppelte geschweifte Klammern. <br /><br />Beispiel 3: Beim angegebenen Wert fehlt eine schließende geschweifte Klammer.<br /> | Beispiel Nr. 1: %{resp_user-agent} <br /><br />Beispiel Nr. 2: %{{host}} <br /><br />Beispiel Nr. 3: %{host |
| Fehlender Variablenname | Ein NULL-Wert wird immer zurückgegeben, wenn eine Variable nicht angegeben ist. | %{} |
| Nachgestellte Zeichen | Zeichen, die am Ende einer Variable stehen, werden als Literalwerte behandelt. <br />Der Beispielwert auf der rechten Seite enthält eine nachgestellte geschweifte Klammer, die als Literalwert behandelt wird. | %{host}} |

## <a name="setting-default-header-values"></a>Festlegen von Standardwerten für Header
Einem Header kann ein Standardwert zugewiesen werden, wenn er eine der folgenden Bedingungen erfüllt:
- Fehlt/nicht festgelegt
- Auf NULL festgelegt.

In der folgenden Tabelle ist beschrieben, wie ein Standardwert definiert wird.

| Bedingung | Syntax | Beispiel | BESCHREIBUNG |
| --------- | ------ | --------| ----------- |
| Ein Header wird auf einen Standardwert festgelegt, wenn er eine der folgenden Bedingungen erfüllt: <br /><br />– Fehlender Header <br /><br />– Headerwert ist auf NULL festgelegt.| %{Variable:=Value} | %{http_referrer:=unspecified} | Der Verweisheader wird nur auf *nicht angegeben* festgelegt, wenn er entweder fehlt oder auf NULL festgelegt ist. Wenn er festgelegt wurde, erfolgt keine Aktion. |
| Ein Header wird auf einen Standardwert festgelegt, wenn er fehlt. | %{Variable=Value} | %{http_referrer=unspecified} | Der Verweisheader wird nur auf *nicht angegeben* festgelegt, wenn er fehlt. Wenn er festgelegt wurde, erfolgt keine Aktion. |
| Der Header wird auf einen Standardwert festgelegt, wenn keine der folgenden Bedingungen auf ihn zutrifft: <br /><br />– Fehlt<br /><br /> – Auf NULL festgelegt. | %{Variable:+Value} | %{http_referrer:+unspecified} | Der Verweisheader wird nur auf *nicht angegeben* festgelegt, wenn ihm ein Wert zugewiesen wurde. Wenn er fehlt oder auf NULL festgelegt ist, erfolgt keine Aktion. |

## <a name="manipulating-variables"></a>Bearbeiten von Variablen
Variablen können folgendermaßen bearbeitet werden:

- Erweitern von Teilzeichenfolgen
- Entfernen von Mustern

### <a name="substring-expansion"></a>Teilzeichenfolgenerweiterung
Eine Variable wird standardmäßig auf ihren vollständigen Wert erweitert. Verwenden Sie die folgende Syntax, um nur eine Teilzeichenfolge des Wert der Variablen zu erweitern.

`%<Variable>:<Offset>:<Length>}`

Wichtige Informationen:

- Der dem Offset-Ausdruck zugewiesene Wert bestimmt das Anfangszeichen der Teilzeichenfolge:

     - Positiv: Das Anfangszeichen der Teilzeichenfolge wird anhand des ersten Zeichens in der Zeichenfolge berechnet.
     - Null: Das Anfangszeichen der Teilzeichenfolge ist das erste Zeichen in der Zeichenfolge.
     - Negativ: Das Anfangszeichen der Teilzeichenfolge wird anhand des letzten Zeichens in der Zeichenfolge berechnet.

- Die Länge der Teilzeichenfolge richtet sich nach dem Ausdruck *Länge*:

     - Nicht angegeben: Durch Auslassen des Längenausdrucks kann die Teilzeichenfolge alle Zeichen zwischen dem Anfangszeichen und dem Ende der Zeichenfolge enthalten.
     - Positiv: Legt die Länge der Teilzeichenfolge ab dem Anfangszeichen nach rechts fest.
     - Negativ: Legt die Länge der Teilzeichenfolge ab dem Anfangszeichen nach links fest.

#### <a name="example"></a>Beispiel:

Das folgende Beispiel basiert auf der folgenden Beispielanforderungs-URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Die folgende Zeichenfolge veranschaulicht verschiedene Methoden zum Bearbeiten von Variablen:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Basierend auf der Beispielanforderungs-URL erzeugt die Variablenbearbeitung oben den folgenden Wert:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Musterentfernung
Text, der einem bestimmten Muster entspricht, kann vom Anfang oder Ende des Werts einer Variablen entfernt werden.

| Syntax | Aktion |
| ------ | ------ |
| %{Variable#Pattern} | Text wird entfernt, wenn sich das angegebene Muster am Anfang des Werts einer Variablen befindet. |
| %{Variable%Pattern} | Text wird entfernt, wenn sich das angegebene Muster am Ende des Werts einer Variablen befindet. |

#### <a name="example"></a>Beispiel:

In diesem Beispielszenario ist die Variable *request_uri* festgelegt auf:

`/800001/myorigin/marketing/product.html?language=en-US`

In der folgenden Tabelle wird veranschaulicht, wie diese Syntax funktioniert.

| Beispielsyntax | Ergebnisse | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Da die Variable mit dem Muster beginnt, wurde sie ersetzt. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Da die Variable nicht mit dem Muster endet, wurde keine Änderung vorgenommen.|

### <a name="find-and-replace"></a>Suchen und Ersetzen
Die Syntax für Suchen und Ersetzen ist in der folgenden Tabelle beschrieben.

| Syntax | Aktion |
| ------ | ------ |
| %{Variable/Find/Replace} | Suchen und Ersetzen des ersten Vorkommens des angegebenen Musters. |
| %{Variable//Find/Replace} | Suchen und Ersetzen aller Vorkommen des angegebenen Musters. |
| %{Variable^} |Konvertieren des gesamten Werts in Großbuchstaben. |
| %{Variable^Find} | Konvertieren des ersten Vorkommens des angegebenen Musters in Großbuchstaben. |
| %{Variable,} | Konvertieren des gesamten Werts in Kleinbuchstaben. |
| %{Variable,Find} | Konvertieren des ersten Vorkommens des angegebenen Musters in Kleinbuchstaben. |

### <a name="find-and-rewrite"></a>Suchen und erneut generieren
Für eine Abwandlung des Vorgangs „Suchen und Ersetzen“ verwenden Sie den Text, der dem angegebenen Muster entspricht, wenn es neu generiert wird. Die Syntax für Suchen und erneutes Generieren ist in der folgenden Tabelle beschrieben.

| Syntax | Aktion |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Sucht und kopiert alle Vorkommen des angegebenen Musters und generiert sie erneut. |
| %{Variable/^Find/Rewrite} | Sucht und kopiert alle Vorkommen des angegebenen Musters, wenn es am Beginn der Variablen steht, und generiert sie erneut. |
| %{Variable/$Find/Rewrite} | Sucht und kopiert alle Vorkommen des angegebenen Musters, wenn es am Ende der Variablen steht, und generiert sie erneut. |
| %{Variable/Find} | Suchen und Löschen aller Vorkommen des angegebenen Musters. |

Wichtige Informationen:

- Erweitern Sie Text, der dem angegebenen Muster entspricht, indem Sie ein Dollarzeichen gefolgt von einer ganzen Zahl (z.B. $1) angeben.

- Mehrere Muster können angegeben werden. Die Reihenfolge, in der das Muster angegeben ist, bestimmt die ganze Zahl, die ihm zugewiesen wird. Im folgenden Beispiel entspricht das erste Muster „www.“, das zweite Muster entspricht der Domäne zweiter Ebene, und das dritte Muster entspricht der Domäne der obersten Ebene:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Der erneut generierte Wert kann aus einer beliebige Kombination aus Text und diesen Platzhalter bestehen.

    Im vorherigen Beispiel wird der Hostname in `cdn.$2.$3:80` umgeschrieben (z.B. cdn.mydomain.com:80).

- Die Groß-/Kleinschreibung eines Musterplatzhalters (z.B. $1) kann über die folgenden Flags geändert werden:
     - U: Der erweiterte Wert wird großgeschrieben.

         Beispielsyntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Der erweiterte Wert wird kleingeschrieben.

         Beispielsyntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Vor dem Muster muss ein Operator angegeben werden. Der angegebene Operator bestimmt das Verhalten für die Mustererfassung:

     - `=`: Gibt an, dass alle Vorkommen des angegebenen Musters erfasst und erneut generiert werden müssen.
     - `^`: Gibt an, dass nur Text, der mit dem angegebenen Muster beginnt, erfasst wird.
     - `$`: Gibt an, dass nur Text, der mit dem angegebenen Muster endet, erfasst wird.
 
- Wenn Sie den Wert */Rewrite* weglassen, wird der Text, der dem Muster entspricht, gelöscht.


