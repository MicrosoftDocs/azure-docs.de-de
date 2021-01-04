---
title: Sitzungsverwaltung – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über mögliche Maßnahmen zur Risikominderung im Bereich der Sitzungsverwaltung für durch das Threat Modeling Tool offengelegte Bedrohungen. Lesen Sie die Informationen zur Risikominderung, und sehen Sie sich die Codebeispiele an.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: a1f4d4a3bb78da82753d651e1a73cf244096d5df
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518100"
---
# <a name="security-frame-session-management"></a>Sicherheitsrahmen: Sitzungsverwaltung
| Produkt/Dienst | Artikel |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementieren der richtigen Abmeldung mit ADAL-Methoden bei Verwendung von Azure AD](#logout-adal)</li></ul> |
| **IoT-Gerät** | <ul><li>[Verwenden von begrenzten Gültigkeitsdauern für generierte SAS-Token](#finite-tokens)</li></ul> |
| **Azure DocumentDB** | <ul><li>[Verwenden von minimalen Tokengültigkeitsdauern für generierte Ressourcentoken](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementieren der richtigen Abmeldung mit WsFederation-Methoden bei Verwendung von ADFS](#wsfederation-logout)</li></ul> |
| **Identity Server** | <ul><li>[Implementieren der richtigen Abmeldung bei Verwendung von Identity Server](#proper-logout)</li></ul> |
| **Web Application** | <ul><li>[Für Anwendungen, die per HTTPS verfügbar sind, müssen sichere Cookies verwendet werden](#https-secure-cookies)</li><li>[Für alle HTTP-basierten Anwendungen sollte HTTP nur für die Cookiedefinition angegeben werden](#cookie-definition)</li><li>[Einleiten von Gegenmaßnahmen für Angriffe vom Typ „Websiteübergreifende Anforderungsfälschung“ auf ASP.NET-Webseiten](#csrf-asp)</li><li>[Einrichten der Sitzung für die Inaktivitätsgültigkeitsdauer](#inactivity-lifetime)</li><li>[Implementieren der richtigen Abmeldung von der Anwendung](#proper-app-logout)</li></ul> |
| **Web-API** | <ul><li>[Einleiten von Gegenmaßnahmen für Angriffe vom Typ „Websiteübergreifende Anforderungsfälschung“ auf ASP.NET-Web-APIs](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Implementieren der richtigen Abmeldung mit ADAL-Methoden bei Verwendung von Azure AD

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure AD | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Wenn für die Anwendung von Azure AD ausgestellte Zugriffstoken benötigt werden, sollte der Abmeldeereignishandler Folgendes aufrufen: |

### <a name="example"></a>Beispiel
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Beispiel
Außerdem sollte die Sitzung des Benutzers durch den Aufruf der Session.Abandon()-Methode zerstört werden. Die folgende Methode veranschaulicht die sichere Implementierung der Benutzerabmeldung:
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Verwenden von begrenzten Gültigkeitsdauern für generierte SAS-Token

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Gerät | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | SAS-Token, die für die Authentifizierung für Azure IoT Hub generiert werden, sollten über einen begrenzten Ablaufzeitraum verfügen. Halten Sie die Gültigkeitsdauern von SAS-Token möglichst kurz, um den verfügbaren Zeitraum für die erneute Verwendung zu begrenzen, falls die Token kompromittiert werden.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Verwenden von minimalen Tokengültigkeitsdauern für generierte Ressourcentoken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure DocumentDB | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Reduzieren Sie die Zeitspanne von Ressourcentoken auf das erforderliche Minimum. Ressourcentoken verfügen über einen gültigen Zeitspannenwert von einer Stunde.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>Implementieren der richtigen Abmeldung mit WsFederation-Methoden bei Verwendung von ADFS

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | ADFS | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Wenn für die Anwendung von ADFS ausgestellte STS-Token benötigt werden, sollte der Abmeldeereignishandler die WSFederationAuthenticationModule.FederatedSignOut()-Methode aufrufen, um den Benutzer abzumelden. Außerdem sollte die aktuelle Sitzung zerstört werden, und der Sitzungstokenwert sollte zurückgesetzt und aufgehoben werden.|

### <a name="example"></a>Beispiel
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Implementieren der richtigen Abmeldung bei Verwendung von Identity Server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Identity Server | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [IdentityServer3 – Federated sign out](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Schritte** | Für IdentityServer wird die Erstellung eines Verbunds mit externen Identitätsanbietern unterstützt. Wenn sich ein Benutzer eines vorgeschalteten Identitätsanbieters abmeldet, ist es je nach verwendetem Protokoll unter Umständen möglich, nach dem Abmelden des Benutzers eine Benachrichtigung zu erhalten. IdentityServer kann dann seine Clients benachrichtigen, damit diese den Benutzer ebenfalls abmelden können. Die Details der Implementierung finden Sie in der Dokumentation, die im Bereich „Referenzen“ angegeben ist.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>Für Anwendungen, die per HTTPS verfügbar sind, müssen sichere Cookies verwendet werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | EnvironmentType – OnPrem |
| **Referenzen**              | [httpCookies-Element (ASP.NET-Einstellungsschema)](/previous-versions/dotnet/netframework-4.0/ms228262(v=vs.100)), [HttpCookie.Secure-Eigenschaft](/dotnet/api/system.web.httpcookie.secure) |
| **Schritte** | Auf Cookies kann normalerweise nur über die Domäne zugegriffen werden, für die sie zugeordnet wurden. Die Definition von „Domäne“ enthält leider nicht das Protokoll, sodass auf Cookies, die per HTTPS erstellt werden, über HTTP zugegriffen werden kann. Mit dem Attribut „secure“ wird für den Browser angegeben, dass das Cookie nur per HTTPS verfügbar gemacht werden sollte. Stellen Sie sicher, dass für alle Cookies, die per HTTPS festgelegt werden, das Attribut **secure** verwendet wird. Die Anforderung kann in der Datei „web.config“ erzwungen werden, indem das Attribut „requireSSL“ auf „TRUE“ festgelegt wird. Dies ist der bevorzugte Ansatz, da hiermit das Attribut **secure** für alle aktuellen und zukünftigen Cookies erzwungen wird, ohne dass zusätzliche Codeänderungen vorgenommen werden müssen.|

### <a name="example"></a>Beispiel
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Diese Einstellung wird auch erzwungen, wenn für den Zugriff auf die Anwendung HTTP verwendet wird. Bei Verwendung von HTTP zum Zugreifen auf die Anwendung führt die Einstellung für die Anwendung zu einem Fehler, da die Cookies mit dem Attribut „secure“ festgelegt werden und der Browser sie nicht zurück an die Anwendung sendet.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Web Forms, MVC5 |
| **Attribute**              | EnvironmentType – OnPrem |
| **Referenzen**              | –  |
| **Schritte** | Wenn die Webanwendung die vertrauende Seite ist und der IdP der ADFS-Server ist, kann das Attribut „secure“ des FedAuth-Tokens konfiguriert werden, indem „requireSSL“ im Abschnitt `system.identityModel.services` der Datei „web.config“ auf „True“ festgelegt wird:|

### <a name="example"></a>Beispiel
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Für alle HTTP-basierten Anwendungen sollte HTTP nur für die Cookiedefinition angegeben werden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Secure Cookie Attribute](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) (Cookieattribut „Secure“) |
| **Schritte** | Für Cookies wurde das neue Attribut „httpOnly“ eingeführt, um das Risiko einer Offenlegung von Informationen bei einem XSS-Angriff (Cross-Site Scripting) zu verringern. Es wird von allen bekannteren Browsern unterstützt. Mit dem Attribut wird angegeben, dass ein Cookie nicht per Skript zugänglich ist. Durch die Verwendung von HttpOnly-Cookies wird für eine Webanwendung die Gefahr verringert, dass im Cookie enthaltene vertrauliche Informationen per Skript gestohlen und an die Website eines Angreifers gesendet werden. |

### <a name="example"></a>Beispiel
Für alle HTTP-basierten Anwendungen, die Cookies verwenden, sollte in der Cookiedefinition „HttpOnly“ angegeben werden, indem die folgende Konfiguration in „web.config“ implementiert wird:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Web Forms |
| **Attribute**              | –  |
| **Referenzen**              | [FormsAuthentication.RequireSSL-Eigenschaft](/dotnet/api/system.web.security.formsauthentication.requiressl) |
| **Schritte** | Der RequireSSL-Eigenschaftswert wird in der Konfigurationsdatei für eine ASP.NET-Anwendung festgelegt, indem das requireSSL-Attribut des Konfigurationselements verwendet wird. Sie können in der Datei „Web.config“ für Ihre ASP.NET-Anwendung angeben, ob TLS (Transport Layer Security), früher als SSL (Secure Sockets Layer) bezeichnet, zum Zurückgeben des Forms-Authentifizierungscookies an den Server erforderlich ist, indem Sie das Attribut requireSSL festlegen.|

### <a name="example"></a>Beispiel 
Im folgenden Codebeispiel wird das Attribut „requireSSL“ in der Datei „Web.config“ festgelegt.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC5 |
| **Attribute**              | EnvironmentType – OnPrem |
| **Referenzen**              | [Windows Identity Foundation (WIF) Configuration – Part II](/archive/blogs/alikl/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler) (WIF-Konfiguration (Windows Identity Foundation) – Teil II) |
| **Schritte** | Zum Angeben des Attributs „httpOnly“ für FedAuth-Cookies sollte der Wert für das Attribut „hideFromCsript“ auf „True“ festgelegt werden. |

### <a name="example"></a>Beispiel
Hier ist die richtige Konfiguration dargestellt:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>Einleiten von Gegenmaßnahmen für Angriffe vom Typ „Websiteübergreifende Anforderungsfälschung“ auf ASP.NET-Webseiten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Websiteübergreifende Anforderungsfälschung (CSRF oder XSRF) ist ein Angriffstyp, bei dem ein Angreifer Aktionen im Sicherheitskontext einer etablierten Sitzung eines anderen Benutzers auf einer Website ausführen kann. Das Ziel ist das Ändern oder Löschen von Inhalten, wenn die Zielwebsite ausschließlich Sitzungscookies zum Authentifizieren der empfangenen Anforderungen verwendet. Ein Angreifer kann dieses Sicherheitsrisiko ausnutzen, indem er den Browser eines anderen Benutzers zum Laden einer URL mit einem Befehl einer anfälligen Website verwendet, auf der der Benutzer bereits angemeldet ist. Für einen Angreifer gibt es hierfür viele Möglichkeiten, z.B. das Hosten einer anderen Website, die eine Ressource vom anfälligen Server lädt, oder das Verleiten des Benutzers zum Klicken auf einen Link. Dieser Angriff kann verhindert werden, wenn der Server ein weiteres Token an den Client sendet, die Verwendung dieses Tokens in allen zukünftigen Anforderungen zur Bedingung macht und sicherstellt, dass alle zukünftigen Anforderungen ein Token enthalten, das zur aktuellen Sitzung gehört. Zu diesem Zweck kann AntiForgeryToken oder ViewState von ASP.NET verwendet werden. |

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC5, MVC6 |
| **Attribute**              | –  |
| **Referenzen**              | [XSRF/CSRF Prevention in ASP.NET MVC and Web Pages](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) (XSRF/CSRF-Verhinderung in ASP.NET MVC und -Webseiten) |
| **Schritte** | Anti-CSRF und ASP.NET-MVC-Formulare: Verwenden Sie die `AntiForgeryToken`-Hilfsmethode für Ansichten. Fügen Sie `Html.AntiForgeryToken()` in das Formular ein, z.B.:|

### <a name="example"></a>Beispiel
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Beispiel
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Beispiel
Gleichzeitig erhält der Besucher mit Html.AntiForgeryToken() ein Cookie mit dem Namen „__RequestVerificationToken“. Der Wert entspricht dem oben dargestellten zufälligen ausgeblendeten Wert. Fügen Sie zum Überprüfen einer eingehenden Formularbereitstellung der Zielaktionsmethode als Nächstes den Filter [ValidateAntiForgeryToken] hinzu. Beispiel:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Autorisierungsfilter, mit dem Folgendes überprüft wird:
* Die eingehende Anforderung enthält das Cookie „__RequestVerificationToken“.
* Die eingehende Anforderung enthält den `Request.Form`-Eintrag „__RequestVerificationToken“.
* Diese Cookie- und `Request.Form`-Werte stimmen überein. Sofern keine weiteren Bedenken vorliegen, wird die Anforderung normal verarbeitet. Wenn nicht, tritt ein Autorisierungsfehler mit einer Meldung der Art „Ein erforderliches Antifälschungstoken wurde nicht bereitgestellt oder war ungültig“ auf. 

### <a name="example"></a>Beispiel
Anti-CSRF und AJAX: Das Formulartoken kann für AJAX-Anforderungen ein Problem darstellen, da eine AJAX-Anforderung unter Umständen JSON-Daten und keine HTML-Formulardaten sendet. Eine Lösung besteht darin, die Token in einem benutzerdefinierten HTTP-Header zu senden. Im folgenden Code wird Razor-Syntax zum Generieren der Token verwendet, und anschließend werden die Token einer AJAX-Anforderung hinzugefügt. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Beispiel
Extrahieren Sie die Token beim Verarbeiten der Anforderung aus dem Anforderungsheader. Rufen Sie anschließend die AntiForgery.Validate-Methode auf, um die Token zu überprüfen. Die Validate-Methode löst eine Ausnahme aus, wenn die Token nicht gültig sind.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Web Forms |
| **Attribute**              | –  |
| **Referenzen**              | [Abwehr von Webangriffen mit ASP.NET](/previous-versions/dotnet/articles/ms972969(v=msdn.10)#securitybarriers_topic2) |
| **Schritte** | CSRF-Angriffen in WebForm-basierten Anwendungen kann begegnet werden, indem Sie „ViewStateUserKey“ auf eine zufällige Zeichenfolge festlegen, die für jeden Benutzer variiert (Benutzer-ID oder, noch besser, Sitzungs-ID). Aus verschiedenen technischen und sozialen Gründen ist die Sitzungs-ID hier deutlich besser geeignet, weil sie unvorhersagbar ist, abläuft und sich von Benutzer zu Benutzer unterscheidet.|

### <a name="example"></a>Beispiel
Hier ist der Code angegeben, der auf allen Seiten vorhanden sein muss:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>Einrichten der Sitzung für die Inaktivitätsgültigkeitsdauer

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [HttpSessionState.Timeout-Eigenschaft](/dotnet/api/system.web.sessionstate.httpsessionstate.timeout) |
| **Schritte** | Das Sitzungstimeout entspricht dem Ereignis, das eintritt, wenn Benutzer während eines (vom Webserver vorgegebenen) Intervallzeitraums keine Aktion auf einer Website durchführen. Mit dem Ereignis wird auf Serverseite der Status der Benutzersitzung in „ungültig“ (z.B. nicht mehr verwendet) geändert, und der Webserver wird angewiesen, die Sitzung zu zerstören (Löschen aller enthaltenen Daten). Im folgenden Codebeispiel wird das Attribut für das Sitzungstimeout in der Datei „Web.config“ auf 15 Minuten festgelegt.|

### <a name="example"></a>Beispiel
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Aktivieren der Bedrohungserkennung in Azure SQL

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Web Forms |
| **Attribute**              | –  |
| **Referenzen**              | [Forms-Element für Authentifizierung (ASP.NET-Einstellungsschema)](/previous-versions/dotnet/netframework-4.0/1d3t3c61(v=vs.100)) |
| **Schritte** | Festlegen des Cookietimeouts für das Forms-Authentifizierungsticket auf 15 Minuten|

### <a name="example"></a>Beispiel
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Web Forms, MVC5 |
| **Attribute**              | EnvironmentType – OnPrem |
| **Referenzen**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Schritte** | Wenn die Webanwendung die vertrauende Seite und AD FS der Sicherheitstokendienst ist, kann die Lebensdauer der Authentifizierungscookies (FedAuth-Token) mit der folgenden Konfiguration in „web.config“ festgelegt werden:|

### <a name="example"></a>Beispiel
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Beispiel
Die Gültigkeitsdauer für das von ADFS ausgestellte SAML-Anspruchstoken sollte ebenfalls auf 15 Minuten festgelegt werden, indem der folgende PowerShell-Befehl auf dem ADFS-Server ausgeführt wird:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>Implementieren der richtigen Abmeldung von der Anwendung

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Führen Sie eine korrekte Abmeldung von der Anwendung durch, wenn Benutzer auf die Schaltfläche „Abmelden“ klicken. Beim Abmelden sollte die Anwendung die Sitzung des Benutzers zerstören und außerdem den Sitzungscookiewert zurücksetzen und aufheben. Dies gilt auch für den Authentifizierungscookiewert. Wenn mehrere Sitzungen an eine einzelne Benutzeridentität gebunden sind, müssen sie bei einem Timeout oder bei der Abmeldung alle zusammen beendet werden. Stellen Sie abschließend sicher, dass die Abmeldefunktionalität auf jeder Seite verfügbar ist. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>Einleiten von Gegenmaßnahmen für Angriffe vom Typ „Websiteübergreifende Anforderungsfälschung“ auf ASP.NET-Web-APIs

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Websiteübergreifende Anforderungsfälschung (CSRF oder XSRF) ist ein Angriffstyp, bei dem ein Angreifer Aktionen im Sicherheitskontext einer etablierten Sitzung eines anderen Benutzers auf einer Website ausführen kann. Das Ziel ist das Ändern oder Löschen von Inhalten, wenn die Zielwebsite ausschließlich Sitzungscookies zum Authentifizieren der empfangenen Anforderungen verwendet. Ein Angreifer kann dieses Sicherheitsrisiko ausnutzen, indem er den Browser eines anderen Benutzers zum Laden einer URL mit einem Befehl einer anfälligen Website verwendet, auf der der Benutzer bereits angemeldet ist. Für einen Angreifer gibt es hierfür viele Möglichkeiten, z.B. das Hosten einer anderen Website, die eine Ressource vom anfälligen Server lädt, oder das Verleiten des Benutzers zum Klicken auf einen Link. Dieser Angriff kann verhindert werden, wenn der Server ein weiteres Token an den Client sendet, die Verwendung dieses Tokens in allen zukünftigen Anforderungen zur Bedingung macht und sicherstellt, dass alle zukünftigen Anforderungen ein Token enthalten, das zur aktuellen Sitzung gehört. Zu diesem Zweck kann AntiForgeryToken oder ViewState von ASP.NET verwendet werden. |

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC5, MVC6 |
| **Attribute**              | –  |
| **Referenzen**              | [Preventing Cross-Site Request Forgery (CSRF) Attacks in ASP.NET Web API](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) (Verhindern von Angriffen mit websiteübergreifender Anforderungsfälschung in der ASP.NET-Web-API) |
| **Schritte** | Anti-CSRF und AJAX: Das Formulartoken kann für AJAX-Anforderungen ein Problem darstellen, da eine AJAX-Anforderung unter Umständen JSON-Daten und keine HTML-Formulardaten sendet. Eine Lösung besteht darin, die Token in einem benutzerdefinierten HTTP-Header zu senden. Im folgenden Code wird Razor-Syntax zum Generieren der Token verwendet, und anschließend werden die Token einer AJAX-Anforderung hinzugefügt. |

### <a name="example"></a>Beispiel
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Beispiel
Extrahieren Sie die Token beim Verarbeiten der Anforderung aus dem Anforderungsheader. Rufen Sie anschließend die AntiForgery.Validate-Methode auf, um die Token zu überprüfen. Die Validate-Methode löst eine Ausnahme aus, wenn die Token nicht gültig sind.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Beispiel
Anti-CSRF- und ASP.NET MVC-Formulare: Verwenden Sie die AntiForgeryToken-Hilfsmethode für Ansichten. Fügen Sie „Html.AntiForgeryToken()“ in das Formular ein, z.B.:
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Beispiel
Im obigen Beispiel wird etwa Folgendes ausgegeben:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Beispiel
Gleichzeitig erhält der Besucher mit Html.AntiForgeryToken() ein Cookie mit dem Namen „__RequestVerificationToken“. Der Wert entspricht dem oben dargestellten zufälligen ausgeblendeten Wert. Fügen Sie zum Überprüfen einer eingehenden Formularbereitstellung der Zielaktionsmethode als Nächstes den Filter [ValidateAntiForgeryToken] hinzu. Beispiel:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Autorisierungsfilter, mit dem Folgendes überprüft wird:
* Die eingehende Anforderung enthält das Cookie „__RequestVerificationToken“.
* Die eingehende Anforderung enthält den `Request.Form`-Eintrag „__RequestVerificationToken“.
* Diese Cookie- und `Request.Form`-Werte stimmen überein. Sofern keine weiteren Bedenken vorliegen, wird die Anforderung normal verarbeitet. Wenn nicht, tritt ein Autorisierungsfehler mit einer Meldung der Art „Ein erforderliches Antifälschungstoken wurde nicht bereitgestellt oder war ungültig“ auf.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | MVC5, MVC6 |
| **Attribute**              | Identitätsanbieter: ADFS, Identitätsanbieter: Azure AD |
| **Referenzen**              | [Secure a Web API with Individual Accounts and Local Login in ASP.NET Web API 2.2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) (Schützen einer Web-API mit einzelnen Konten und lokaler Anmeldung in ASP.NET-Web-API 2.2) |
| **Schritte** | Wenn die Web-API mit OAuth 2.0 geschützt wird, wird im Autorisierungsheader der Anforderung ein Bearertoken erwartet und nur dann Zugriff auf die Anforderung gewährt, wenn das Token gültig ist. Im Gegensatz zur cookiebasierten Authentifizierung fügen Browser die Bearertoken nicht an Anforderungen an. Der anfordernde Client muss das Bearertoken explizit im Anforderungsheader anfügen. Daher werden Bearertoken für ASP.NET-Web-APIs, die per OAuth 2.0 geschützt sind, als Verteidigung gegen CSRF-Angriffe angesehen. Beachten Sie Folgendes: Wenn im MVC-Teil der Anwendung die Formularauthentifizierung verwendet wird (also Cookies), müssen für die MVC-Web-App Antifälschungstoken genutzt werden. |

### <a name="example"></a>Beispiel
Die Web-API muss angewiesen werden, NUR Bearertoken und keine Cookies zu verwenden. Sie können dazu die folgende Konfiguration in der `WebApiConfig.Register`-Methode verwenden:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Die SuppressDefaultHostAuthentication-Methode weist die Web-API an, Authentifizierungen zu ignorieren, die vor dem Empfang der Anforderung in der Web-API-Pipeline durchgeführt wurden. Dazu wird IIS oder OWIN-Middleware verwendet. Auf diese Weise können Sie die Authentifizierung der Web-API ausschließlich auf Bearertoken einschränken.