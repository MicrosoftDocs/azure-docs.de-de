---
title: 'Azure Application Gateway: CRS-Regelgruppen und -Regeln von Web Application Firewall'
description: Diese Seite enthält Informationen zu CRS-Regelgruppen und -Regeln von Web Application Firewall.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 4/11/2019
ms.author: victorh
ms.openlocfilehash: 0ad5cc76c0f4631fd60eea7d0a57e4740b6a9db3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60832913"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>CRS-Regelgruppen und -Regeln der Web Application Firewall

Mit der Application Gateway Web Application Firewall (WAF) werden Webanwendungen vor allgemeinen Sicherheitsrisiken und Exploits geschützt. Hierfür werden Regeln verwendet, die basierend auf den OWASP-Kernregelsätzen 3.0 oder 2.2.9 definiert sind. Diese Regeln können einzeln deaktiviert werden. In diesem Artikel sind die aktuellen Regeln und Regelsätze enthalten.

Die folgenden Regelgruppen und Regeln sind bei Verwendung von Application Gateway mit Web Application Firewall verfügbar.

# <a name="owasp-30tabowasp3"></a>[OWASP 3.0](#tab/owasp3)

## <a name="owasp30"></a>-Regelsätze

### <a name="General"></a> <p x-ms-format-detection="none">Allgemein</p>

|RuleId|BESCHREIBUNG|
|---|---|
|200004|Mehrteiliger Parser hat eine mögliche fehlende Übereinstimmung für eine Grenze erkannt.|

### <a name="crs911"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|BESCHREIBUNG|
|---|---|
|911100|Methode ist gemäß Richtlinie nicht zulässig|


### <a name="crs913"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|BESCHREIBUNG|
|---|---|
|913100|Benutzer-Agent mit Zuordnung zu Sicherheitsscanner gefunden|
|913110|Anforderungsheader mit Zuordnung zu Sicherheitsscanner gefunden|
|913120|Anforderungsdateiname/-argument mit Zuordnung zu Sicherheitsscanner gefunden|
|913101|Benutzer-Agent mit Zuordnung zu Skripterstellungs- bzw. generischem HTTP-Client gefunden|
|913102|Benutzer-Agent mit Zuordnung zu Webcrawler/Bot gefunden|

### <a name="crs920"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|BESCHREIBUNG|
|---|---|
|920100|Ungültige HTTP-Anforderungszeile|
|920130|Fehler beim Analysieren des Anforderungstexts|
|920140|Fehler bei der strengen Überprüfung des mehrteiligen Anforderungstexts|
|920160|Content-Length-HTTP-Header ist nicht numerisch.|
|920170|GET- oder HEAD-Anforderung mit Textinhalt|
|920180|POST-Anforderung mit fehlendem Content-Length-Header|
|920190|Bereich = Ungültiger Wert für letztes Byte|
|920210|Mehrere bzw. in Konflikt stehende Verbindungsheaderdaten gefunden|
|920220|Versuchter Missbrauch der URL-Codierung|
|920240|Versuchter Missbrauch der URL-Codierung|
|920250|Versuchter Missbrauch der UTF8-Codierung|
|920260|Versuchter Missbrauch: Unicode (volle/halbe Breite)|
|920270|Ungültiges Zeichen in der Anforderung (Zeichen NULL)|
|920280|Fehlender Hostheader in Anforderung|
|920290|Leerer Hostheader|
|920310|Anforderung hat einen leeren Accept-Header|
|920311|Anforderung hat einen leeren Accept-Header|
|920330|Leerer Benutzer-Agent-Header|
|920340|Anforderung enthält Inhalt, aber keinen Content-Type-Header|
|920350|Hostheader ist eine numerische IP-Adresse|
|920380|Zu viele Argumente in der Anforderung|
|920360|Argumentname ist zu lang|
|920370|Argumentwert ist zu lang|
|920390|Gesamtzahl für Argumente überschritten|
|920400|Hochgeladene Datei zu groß|
|920410|Gesamtgröße der hochgeladenen Dateien zu hoch|
|920420|Anforderungsinhaltstyp ist gemäß Richtlinie nicht zulässig|
|920430|HTTP-Protokollversion ist gemäß Richtlinie nicht zulässig|
|920440|URL-Dateierweiterung wird durch Richtlinie eingeschränkt|
|920450|HTTP-Header ist durch Richtlinie eingeschränkt (%@{MATCHED_VAR})|
|920200|Bereich = Zu viele Felder (mehr als 6)|
|920201|Bereich = Zu viele Felder für PDF-Anforderung (mehr als 35)|
|920230|Mehrere URL-Codierungen erkannt|
|920300|Fehlender Accept-Header für Anforderung|
|920271|Ungültiges Zeichen in der Anforderung (nicht druckbare Zeichen)|
|920320|Benutzer-Agent-Header fehlt|
|920272|Ungültiges Zeichen in der Anforderung (außerhalb des Bereichs von druckbaren Zeichen unterhalb von ASCII 127)|
|920202|Bereich = Zu viele Felder für PDF-Anforderung (mehr als 6)|
|920273|Ungültiges Zeichen in der Anforderung (außerhalb des sehr strengen Satzes)|
|920274|Ungültiges Zeichen in Anforderungsheadern (außerhalb des sehr strengen Satzes)|
|920460|Ungewöhnliche Escapezeichen|

### <a name="crs921"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|BESCHREIBUNG|
|---|---|
|921100|HTTP Request Smuggling-Angriff|
|921110|HTTP Request Smuggling-Angriff|
|921120|HTTP Response Splitting-Angriff|
|921130|HTTP Response Splitting-Angriff|
|921140|HTTP Header Injection-Angriff über Header|
|921150|HTTP Header Injection-Angriff über Nutzlast (CR/LF erkannt)|
|921160|HTTP Header Injection-Angriff über Nutzlast (CR/LF und Headername erkannt)|
|921151|HTTP Header Injection-Angriff über Nutzlast (CR/LF erkannt)|
|921170|HTTP-Parameterverunreinigung|
|921180|HTTP-Parameterverunreinigung (%@{TX.1})|

### <a name="crs930"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|BESCHREIBUNG|
|---|---|
|930100|Path Traversal-Angriff (/../)|
|930110|Path Traversal-Angriff (/../)|
|930120|Zugriffsversuch auf Betriebssystemdatei|
|930130|Zugriffsversuch auf Datei mit eingeschränktem Zugriff|

### <a name="crs931"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|BESCHREIBUNG|
|---|---|
|931100|Möglicher RFI-Angriff (Remote File Inclusion) = Verwendung von IP-Adresse für URL-Parameter|
|931110|Möglicher RFI-Angriff (Remote File Inclusion) = Verwendung eines häufigen und für RFI anfälligen Parameternamens mit URL-Nutzlast|
|931120|Möglicher RFI-Angriff (Remote File Inclusion) = Verwendung von URL-Nutzlast mit nachgestelltem Fragezeichen (?)|
|931130|Möglicher RFI-Angriff (Remote File Inclusion) = Domänenexterner Verweis/Link|

### <a name="crs932"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|BESCHREIBUNG|
|---|---|
|932120|Ausführung eines Remotebefehls = Windows PowerShell-Befehl gefunden|
|932130|Ausführung eines Remotebefehls = Unix Shell-Ausdruck gefunden|
|932140|Ausführung eines Remotebefehls = Windows-FOR/IF-Befehl gefunden|
|932160|Ausführung eines Remotebefehls = Unix Shell-Code gefunden|
|932170|Ausführung eines Remotebefehls = Shellshock (CVE-2014-6271)|
|932171|Ausführung eines Remotebefehls = Shellshock (CVE-2014-6271)|

### <a name="crs933"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|BESCHREIBUNG|
|---|---|
|933100|PHP Injection-Angriff = Öffnendes/Schließendes Tag gefunden|
|933110|PHP Injection-Angriff = Upload von PHP-Skriptdatei gefunden|
|933120|PHP Injection-Angriff = Konfigurationsdirektive gefunden|
|933130|PHP Injection-Angriff = Variablen gefunden|
|933150|PHP Injection-Angriff = PHP-Funktionsname mit hohem Risikofaktor gefunden|
|933160|PHP Injection-Angriff = PHP-Funktionsaufruf mit hohem Risikofaktor gefunden|
|933180|PHP Injection-Angriff = Aufruf einer Variablenfunktion gefunden|
|933151|PHP Injection-Angriff = PHP-Funktionsname mit mittlerem Risikofaktor gefunden|
|933131|PHP Injection-Angriff = Variablen gefunden|
|933161|PHP Injection-Angriff = PHP-Funktionsaufruf mit niedrigem Wert gefunden|
|933111|PHP Injection-Angriff = Upload von PHP-Skriptdatei gefunden|

### <a name="crs941"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|BESCHREIBUNG|
|---|---|
|941100|XSS-Angriff per libinjection erkannt|
|941110|XSS-Filter – Kategorie 1 = Skripttagvektor|
|941130|XSS-Filter – Kategorie 3 = Attributvektor|
|941140|XSS-Filter – Kategorie 4 = JavaScript-URI-Vektor|
|941150|XSS-Filter – Kategorie 5 = Unzulässige HTML-Attribute|
|941180|Schlüsselwörter von Sperrliste für Knotenvalidierung|
|941190|XSS mit Stylesheets|
|941200|XSS mit VML-Frames|
|941210|XSS mit verschleiertem JavaScript|
|941220|XSS mit verschleiertem VBScript|
|941230|XSS mit Tag 'embed'|
|941240|XSS mit Attribut 'import' oder 'implementation'|
|941260|XSS mit Tag 'meta'|
|941270|XSS mit Href 'link'|
|941280|XSS mit Tag 'base'|
|941290|XSS mit Tag 'applet'|
|941300|XSS mit Tag 'object'|
|941310|Falsch formatierte US-ASCII-Codierung für XSS-Filter – Angriff erkannt|
|941330|IE-XSS-Filter – Angriff erkannt|
|941340|IE-XSS-Filter – Angriff erkannt|
|941350|UTF-7-Codierung – IE XSS – Angriff erkannt|
|941320|Möglicher XSS-Angriff erkannt – HTML-Taghandler|

### <a name="crs942"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|BESCHREIBUNG|
|---|---|
|942100|SQL Injection-Angriff per libinjection erkannt|
|942110|Angriff mit Einschleusung von SQL-Befehlen: Allgemeine Tests auf Einschleusung von SQL-Befehlen erkannt|
|942130|Angriff mit Einschleusung von SQL-Befehlen: SQL-Tautologie erkannt.|
|942140|SQL Injection-Angriff = Häufige Datenbanknamen erkannt|
|942160|Erkennt Blind SQLI-Tests per sleep() oder benchmark().|
|942170|Erkennt SQL Benchmark- und Sleep Injection-Angriffsversuche, einschließlich bedingter Abfragen.|
|942190|Erkennt die Ausführung von MSSQL-Code und Versuche, Informationen auszulesen|
|942200|Erkennt durch MySQL-Kommentare oder -Leerzeichen verschleierte Einschleusungen und die Terminierung per Backtick|
|942230|Erkennt Angriffsversuche mit bedingter SQL-Einschleusung.|
|942260|Erkennt Versuche der Umgehung der einfachen SQL-Authentifizierung, 2/3|
|942270|Suche nach grundlegender SQL-Einschleusung. Häufig verwendete Angriffszeichenfolge für MySQL Oracle und andere.|
|942290|Ermittelt Angriffsversuche mit grundlegender MongoDB SQL-Einschleusung.|
|942300|Erkennt MySQL-Kommentare, Bedingungen und Einschleusungen von „ch(ar)“|
|942320|Erkennt Einschleusungen von gespeicherten Prozeduren/Funktionen für MySQL und PostgreSQL.|
|942330|Erkennt Probings von klassischen Einschleusungen von SQL-Befehlen, 1/2|
|942340|Erkennt Versuche der Umgehung der einfachen SQL-Authentifizierung, 3/3|
|942350|Erkennt MySQL-UDF-Einschleusung und andere Versuche der Manipulation von Daten bzw. der Struktur.|
|942360|Erkennt Versuche des Verkettens einfacher eingeschleuster SQL-Befehle und von SQL/LFI|
|942370|Erkennt Probings von klassischen Einschleusungen von SQL-Befehlen, 2/2|
|942150|Angriff mit Einschleusung von SQL-Befehlen|
|942410|Angriff mit Einschleusung von SQL-Befehlen|
|942430|Eingeschränkte Anomalieerkennung für SQL-Zeichen (Argumente): Anzahl von Sonderzeichen überschritten (12)|
|942440|SQL-Kommentarsequenz erkannt|
|942450|Hexadezimale SQL-Codierung identifiziert|
|942251|Erkennt HAVING-Einschleusungen.|
|942460|Warnung: Erkennung einer Metazeichenanomalie – Wiederholte Non-Word-Zeichen|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|BESCHREIBUNG|
|---|---|
|943100|Möglicher Session Fixation-Angriff = Festlegung von Cookiewerten in HTML|
|943110|Möglicher Session Fixation-Angriff = SessionID-Parametername mit domänenexternem Verweiser|
|943120|Möglicher Session Fixation-Angriff = SessionID-Parametername ohne Verweiser|

# <a name="owasp-229tabowasp2"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="owasp229"></a>-Regelsätze

### <a name="crs20"></a> crs_20_protocol_violations

|RuleId|BESCHREIBUNG|
|---|---|
|960911|Ungültige HTTP-Anforderungszeile|
|981227|Apache-Fehler = Ungültiger URI in Anforderung|
|960912|Fehler beim Analysieren des Anforderungstexts|
|960914|Fehler bei der strengen Überprüfung des mehrteiligen Anforderungstexts|
|960915|Mehrteiliger Parser hat eine mögliche fehlende Übereinstimmung für eine Grenze erkannt.|
|960016|Content-Length-HTTP-Header ist nicht numerisch.|
|960011|GET- oder HEAD-Anforderung mit Textinhalt|
|960012|POST-Anforderung mit fehlendem Content-Length-Header|
|960902|Ungültige Verwendung der Identitätscodierung|
|960022|Erwartungsheader für HTTP 1.0 unzulässig|
|960020|Für Pragma-Header ist für HTTP/1.1-Anforderungen ein Cache-Control-Header erforderlich.|
|958291|Bereich = Feld ist vorhanden und beginnt mit 0.|
|958230|Bereich = Ungültiger Wert für letztes Byte|
|958295|Mehrere bzw. in Konflikt stehende Verbindungsheaderdaten gefunden|
|950107|Versuchter Missbrauch der URL-Codierung|
|950109|Mehrere URL-Codierungen erkannt|
|950108|Versuchter Missbrauch der URL-Codierung|
|950801|Versuchter Missbrauch der UTF8-Codierung|
|950116|Versuchter Missbrauch: Unicode (volle/halbe Breite)|
|960901|Ungültiges Zeichen in Anforderung|
|960018|Ungültiges Zeichen in Anforderung|

### <a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|BESCHREIBUNG|
|---|---|
|960008|Fehlender Hostheader in Anforderung|
|960007|Leerer Hostheader|
|960015|Fehlender Accept-Header für Anforderung|
|960021|Anforderung hat einen leeren Accept-Header|
|960009|Fehlender Benutzer-Agent-Header für Anforderung|
|960006|Leerer Benutzer-Agent-Header|
|960904|Anforderung enthält Inhalt, aber keinen Content-Type-Header|
|960017|Hostheader ist eine numerische IP-Adresse|

### <a name="crs23"></a> crs_23_request_limits

|RuleId|BESCHREIBUNG|
|---|---|
|960209|Argumentname ist zu lang|
|960208|Argumentwert ist zu lang|
|960335|Zu viele Argumente in der Anforderung|
|960341|Gesamtzahl für Argumente überschritten|
|960342|Hochgeladene Datei zu groß|
|960343|Gesamtgröße der hochgeladenen Dateien zu hoch|

### <a name="crs30"></a> crs_30_http_policy

|RuleId|BESCHREIBUNG|
|---|---|
|960032|Methode ist gemäß Richtlinie nicht zulässig|
|960010|Anforderungsinhaltstyp ist gemäß Richtlinie nicht zulässig|
|960034|HTTP-Protokollversion ist gemäß Richtlinie nicht zulässig|
|960035|URL-Dateierweiterung wird durch Richtlinie eingeschränkt|
|960038|HTTP-Header ist durch Richtlinie eingeschränkt|

### <a name="crs35"></a> crs_35_bad_robots

|RuleId|BESCHREIBUNG|
|---|---|
|990002|Anforderung gibt an, dass die Website mit einem Sicherheitsscanner gescannt wurde|
|990901|Anforderung gibt an, dass die Website mit einem Sicherheitsscanner gescannt wurde|
|990902|Anforderung gibt an, dass die Website mit einem Sicherheitsscanner gescannt wurde|
|990012|Nicht autorisierter Websitecrawler|

### <a name="crs40"></a> crs_40_generic_attacks

|RuleId|BESCHREIBUNG|
|---|---|
|960024|Warnung: Erkennung einer Metazeichenanomalie – Wiederholte Non-Word-Zeichen|
|950008|Einschleusung von nicht dokumentierten ColdFusion-Tags|
|950010|Angriff mit LDAP-Einschleusung|
|950011|Angriff mit SSI-Einschleusung|
|950018|Universelle PDF-XSS-URL erkannt|
|950019|Angriff mit E-Mail-Einschleusung|
|950012|HTTP Request Smuggling-Angriff|
|950910|HTTP Response Splitting-Angriff|
|950911|HTTP Response Splitting-Angriff|
|950117|Remote File Inclusion-Angriff|
|950118|Remote File Inclusion-Angriff|
|950119|Remote File Inclusion-Angriff|
|950120|Möglicher RFI-Angriff (Remote File Inclusion) = Domänenexterner Verweis/Link|
|981133|Regel 981133|
|981134|Regel 981134|
|950009|Session Fixation-Angriff|
|950003|Session Fixation|
|950000|Session Fixation|
|950005|Zugriffsversuch auf Remotedatei|
|950002|Zugriff auf Systembefehl|
|950006|Einschleusung von Systembefehl|
|959151|Angriff mit PHP-Einschleusung|
|958976|Angriff mit PHP-Einschleusung|
|958977|Angriff mit PHP-Einschleusung|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|BESCHREIBUNG|
|---|---|
|981231|SQL-Kommentarsequenz erkannt|
|981260|Hexadezimale SQL-Codierung identifiziert|
|981320|SQL Injection-Angriff = Häufige Datenbanknamen erkannt|
|981300|Regel 981300|
|981301|Regel 981301|
|981302|Regel 981302|
|981303|Regel 981303|
|981304|Regel 981304|
|981305|Regel 981305|
|981306|Regel 981306|
|981307|Regel 981307|
|981308|Regel 981308|
|981309|Regel 981309|
|981310|Regel 981310|
|981311|Regel 981311|
|981312|Regel 981312|
|981313|Regel 981313|
|981314|Regel 981314|
|981315|Regel 981315|
|981316|Regel 981316|
|981317|Warnung: Anomalieerkennung für SQL-SELECT-Anweisung|
|950007|Angriff mit blinder Einschleusung von SQL-Befehlen|
|950001|Angriff mit Einschleusung von SQL-Befehlen|
|950908|Angriff mit Einschleusung von SQL-Befehlen|
|959073|Angriff mit Einschleusung von SQL-Befehlen|
|981272|Erkennt Blind SQLI-Tests per sleep() oder benchmark().|
|981250|Erkennt SQL Benchmark- und Sleep Injection-Angriffsversuche, einschließlich bedingter Abfragen.|
|981241|Erkennt Angriffsversuche mit bedingter SQL-Einschleusung.|
|981276|Suche nach grundlegender SQL-Einschleusung. Häufig verwendete Angriffszeichenfolge für MySQL Oracle und andere.|
|981270|Ermittelt Angriffsversuche mit grundlegender MongoDB SQL-Einschleusung.|
|981253|Erkennt Einschleusungen von gespeicherten Prozeduren/Funktionen für MySQL und PostgreSQL.|
|981251|Erkennt MySQL-UDF-Einschleusung und andere Versuche der Manipulation von Daten bzw. der Struktur.|

### <a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|BESCHREIBUNG|
|---|---|
|973336|XSS-Filter – Kategorie 1 = Skripttagvektor|
|973338|XSS-Filter – Kategorie 3 = JavaScript-URI-Vektor|
|981136|Regel 981136|
|981018|Regel 981018|
|958016|XSS-Angriff (Cross-Site Scripting)|
|958414|XSS-Angriff (Cross-Site Scripting)|
|958032|XSS-Angriff (Cross-Site Scripting)|
|958026|XSS-Angriff (Cross-Site Scripting)|
|958027|XSS-Angriff (Cross-Site Scripting)|
|958054|XSS-Angriff (Cross-Site Scripting)|
|958418|XSS-Angriff (Cross-Site Scripting)|
|958034|XSS-Angriff (Cross-Site Scripting)|
|958019|XSS-Angriff (Cross-Site Scripting)|
|958013|XSS-Angriff (Cross-Site Scripting)|
|958408|XSS-Angriff (Cross-Site Scripting)|
|958012|XSS-Angriff (Cross-Site Scripting)|
|958423|XSS-Angriff (Cross-Site Scripting)|
|958002|XSS-Angriff (Cross-Site Scripting)|
|958017|XSS-Angriff (Cross-Site Scripting)|
|958007|XSS-Angriff (Cross-Site Scripting)|
|958047|XSS-Angriff (Cross-Site Scripting)|
|958410|XSS-Angriff (Cross-Site Scripting)|
|958415|XSS-Angriff (Cross-Site Scripting)|
|958022|XSS-Angriff (Cross-Site Scripting)|
|958405|XSS-Angriff (Cross-Site Scripting)|
|958419|XSS-Angriff (Cross-Site Scripting)|
|958028|XSS-Angriff (Cross-Site Scripting)|
|958057|XSS-Angriff (Cross-Site Scripting)|
|958031|XSS-Angriff (Cross-Site Scripting)|
|958006|XSS-Angriff (Cross-Site Scripting)|
|958033|XSS-Angriff (Cross-Site Scripting)|
|958038|XSS-Angriff (Cross-Site Scripting)|
|958409|XSS-Angriff (Cross-Site Scripting)|
|958001|XSS-Angriff (Cross-Site Scripting)|
|958005|XSS-Angriff (Cross-Site Scripting)|
|958404|XSS-Angriff (Cross-Site Scripting)|
|958023|XSS-Angriff (Cross-Site Scripting)|
|958010|XSS-Angriff (Cross-Site Scripting)|
|958411|XSS-Angriff (Cross-Site Scripting)|
|958422|XSS-Angriff (Cross-Site Scripting)|
|958036|XSS-Angriff (Cross-Site Scripting)|
|958000|XSS-Angriff (Cross-Site Scripting)|
|958018|XSS-Angriff (Cross-Site Scripting)|
|958406|XSS-Angriff (Cross-Site Scripting)|
|958040|XSS-Angriff (Cross-Site Scripting)|
|958052|XSS-Angriff (Cross-Site Scripting)|
|958037|XSS-Angriff (Cross-Site Scripting)|
|958049|XSS-Angriff (Cross-Site Scripting)|
|958030|XSS-Angriff (Cross-Site Scripting)|
|958041|XSS-Angriff (Cross-Site Scripting)|
|958416|XSS-Angriff (Cross-Site Scripting)|
|958024|XSS-Angriff (Cross-Site Scripting)|
|958059|XSS-Angriff (Cross-Site Scripting)|
|958417|XSS-Angriff (Cross-Site Scripting)|
|958020|XSS-Angriff (Cross-Site Scripting)|
|958045|XSS-Angriff (Cross-Site Scripting)|
|958004|XSS-Angriff (Cross-Site Scripting)|
|958421|XSS-Angriff (Cross-Site Scripting)|
|958009|XSS-Angriff (Cross-Site Scripting)|
|958025|XSS-Angriff (Cross-Site Scripting)|
|958413|XSS-Angriff (Cross-Site Scripting)|
|958051|XSS-Angriff (Cross-Site Scripting)|
|958420|XSS-Angriff (Cross-Site Scripting)|
|958407|XSS-Angriff (Cross-Site Scripting)|
|958056|XSS-Angriff (Cross-Site Scripting)|
|958011|XSS-Angriff (Cross-Site Scripting)|
|958412|XSS-Angriff (Cross-Site Scripting)|
|958008|XSS-Angriff (Cross-Site Scripting)|
|958046|XSS-Angriff (Cross-Site Scripting)|
|958039|XSS-Angriff (Cross-Site Scripting)|
|958003|XSS-Angriff (Cross-Site Scripting)|
|973300|Möglicher XSS-Angriff erkannt – HTML-Taghandler|
|973301|XSS-Angriff erkannt|
|973302|XSS-Angriff erkannt|
|973303|XSS-Angriff erkannt|
|973304|XSS-Angriff erkannt|
|973305|XSS-Angriff erkannt|
|973306|XSS-Angriff erkannt|
|973307|XSS-Angriff erkannt|
|973308|XSS-Angriff erkannt|
|973309|XSS-Angriff erkannt|
|973311|XSS-Angriff erkannt|
|973313|XSS-Angriff erkannt|
|973314|XSS-Angriff erkannt|
|973331|IE-XSS-Filter – Angriff erkannt|
|973315|IE-XSS-Filter – Angriff erkannt|
|973330|IE-XSS-Filter – Angriff erkannt|
|973327|IE-XSS-Filter – Angriff erkannt|
|973326|IE-XSS-Filter – Angriff erkannt|
|973346|IE-XSS-Filter – Angriff erkannt|
|973345|IE-XSS-Filter – Angriff erkannt|
|973324|IE-XSS-Filter – Angriff erkannt|
|973323|IE-XSS-Filter – Angriff erkannt|
|973348|IE-XSS-Filter – Angriff erkannt|
|973321|IE-XSS-Filter – Angriff erkannt|
|973320|IE-XSS-Filter – Angriff erkannt|
|973318|IE-XSS-Filter – Angriff erkannt|
|973317|IE-XSS-Filter – Angriff erkannt|
|973329|IE-XSS-Filter – Angriff erkannt|
|973328|IE-XSS-Filter – Angriff erkannt|

### <a name="crs42"></a> crs_42_tight_security

|RuleId|BESCHREIBUNG|
|---|---|
|950103|Path Traversal-Angriff|

### <a name="crs45"></a> crs_45_trojans

|RuleId|BESCHREIBUNG|
|---|---|
|950110|Backdoor-Zugriff|
|950921|Backdoor-Zugriff|
|950922|Backdoor-Zugriff|

---

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Deaktivieren von WAF-Regeln: [Anpassen von WAF-Regeln](application-gateway-customize-waf-rules-portal.md)