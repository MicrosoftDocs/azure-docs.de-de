---
title: Rollover von Signaturschlüsseln in Microsoft Identity Platform
description: Dieser Artikel beschreibt bewährte Verfahren für das Rollover von Signaturschlüsseln für Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 8/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: 981ac775e7153cfd03dc1760bbbc4e50fd9ecc57
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169544"
---
# <a name="signing-key-rollover-in-microsoft-identity-platform"></a>Rollover von Signaturschlüsseln in Microsoft Identity Platform
In diesem Artikel wird erläutert, was Sie über die öffentlichen Schlüssel wissen müssen, die in Microsoft Identity Platform zum Signieren von Sicherheitstoken verwendet werden. Es sollte beachtet werden, dass für diese Schlüssel regelmäßig ein Rollover durchgeführt wird und dass in einem Notfall sofort ein Rollover erfolgen kann. Alle Anwendungen, die Microsoft Identity Platform verwenden, müssen den Schlüsselrollovervorgang programmgesteuert verarbeiten können. In diesem Artikel erfahren Sie, wie die Schlüssel funktionieren, wie Sie die Auswirkung des Rollovers auf Ihre Anwendung bewerten und wie Sie Ihre Anwendung bei Bedarf aktualisieren oder einen regelmäßigen manuellen Rolloverprozess für Schlüssel einrichten.

## <a name="overview-of-signing-keys-in-microsoft-identity-platform"></a>Übersicht über Signaturschlüssel in Microsoft Identity Platform
Microsoft Identity Platform verwendet die auf Branchenstandards basierende Verschlüsselung mit öffentlichem Schlüssel zum Einrichten einer Vertrauensstellung zwischen sich selbst und den Anwendungen, die Microsoft Identity Platform verwenden. In der Praxis funktioniert dies wie folgt: Microsoft Identity Platform verwendet einen Signaturschlüssel, der aus einem Paar mit einem öffentlichen und einem privaten Schlüssel besteht. Wenn sich ein Benutzer bei einer Anwendung anmeldet, die Microsoft Identity Platform für die Authentifizierung verwendet, erstellt Microsoft Identity Platform ein Sicherheitstoken, das Informationen zum Benutzer enthält. Das Token wird von Microsoft Identity Platform mit dessen privaten Schlüssel signiert, bevor es zur Anwendung zurückgesendet wird. Um zu überprüfen, ob das Token gültig ist und von Microsoft Identity Platform stammt, muss die Anwendung die Signatur des Tokens überprüfen. Hierzu wird der öffentliche, von Microsoft Identity Platform verfügbar gemachte Schlüssel verwendet, der im [OpenID Connect-Ermittlungsdokument](https://openid.net/specs/openid-connect-discovery-1_0.html) oder im SAML/WS-Fed-[Verbundmetadaten-Dokument](../azuread-dev/azure-ad-federation-metadata.md) des Mandanten enthalten ist.

Aus Sicherheitsgründen wird für den Signaturschlüssel von Microsoft Identity Platform regelmäßig ein Rollover durchgeführt, das im Notfall auch sofort erfolgen kann. Es gibt keine festgelegte oder garantierte Zeit zwischen diesen Schlüsselrollovern – jede in Microsoft Identity Platform integrierte Anwendung muss darauf vorbereitet sein, unabhängig von der Häufigkeit ein Schlüsselrolloverereignis zu verarbeiten. Wenn dies nicht der Fall ist und die Anwendung versucht, einen abgelaufenen Schlüssel zum Überprüfen der Signatur auf einem Token zu verwenden, schlägt die Anmeldeanforderung fehl.  Es hat sich bewährt, alle 24 Stunden eine Überprüfung auf Updates durchzuführen und dabei gedrosselte (maximal alle fünf Minuten) sofortige Aktualisierungen des Schlüsseldokuments auszuführen, wenn ein Token mit einem unbekannten Schlüsselbezeichner gefunden wird. 

Im OpenID Connect Discovery-Dokument und Verbundmetadaten-Dokument ist immer mehr als ein gültiger Schlüssel verfügbar. Ihre Anwendung sollte einen oder alle der im Dokument angegebenen Schlüssel verwenden können, da ein Schlüssel z. B. geändert oder durch einen anderen ersetzt werden kann usw.  Die Anzahl der vorhandenen Schlüssel kann sich im Lauf der Zeit je nach der interne Architektur von Microsoft Identity Platform ändern, wenn neue Plattformen, neue Clouds oder neue Authentifizierungsprotokolle unterstützt werden. Weder die Reihenfolge der Schlüssel in der JSON-Antwort noch die Reihenfolge, in der sie verfügbar gemacht wurden, sollten in Ihrer App berücksichtigt werden. 

Anwendungen, die nur einen einzigen Unterzeichnerschlüssel unterstützen, oder solche, die manuelle Aktualisierungen der Unterzeichnerschlüssel erfordern, sind grundsätzlich weniger sicher und zuverlässig.  Sie sollten auf die Verwendung der [Standardbibliotheken](reference-v2-libraries.md) umgestellt werden, um sicherzustellen, dass sie immer die aktuellen Unterzeichnerschlüssel verwenden (neben anderen bewährten Methoden). 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Bewerten, ob Ihre Anwendung betroffen ist und welche Schritte erforderlich sind
Die Art und Weise, wie Ihre Anwendung den Schlüsselrollover behandelt, hängt von Variablen ab, z.B. dem Typ der Anwendung oder dem Identitätsprotokoll und der Bibliothek. In den folgenden Abschnitten wird bewertet, ob die häufigsten Arten von Anwendungen vom Schlüsselrollover betroffen sind. Außerdem enthalten sie eine Anleitung, wie Sie die Anwendung aktualisieren, damit der automatische Rollover oder die manuelle Aktualisierung des Schlüssels unterstützt werden.

* [Native Clientanwendungen mit Ressourcenzugriff](#nativeclient)
* [Webanwendungen/-APIs mit Ressourcenzugriff](#webclient)
* [Mit Azure App Services erstellte Webanwendungen/-APIs zum Schutz von Ressourcen](#appservices)
* [Webanwendungen/-APIs zum Schutz von Ressourcen unter Verwendung von .NET OWIN OpenID Connect-, WS-Fed- oder WindowsAzureActiveDirectoryBearerAuthentication-Middleware](#owin)
* [Webanwendungen/-APIs zum Schutz von Ressourcen unter Verwendung von .NET Core OpenID Connect- oder JwtBearerAuthentication-Middleware](#owincore)
* [Webanwendungen/-APIs zum Schutz von Ressourcen unter Verwendung des Node.js-passport-azure-ad-Moduls](#passport)
* [Mit Visual Studio 2015 oder höher erstellte Webanwendungen/-APIs zum Schutz von Ressourcen](#vs2015)
* [Mit Visual Studio 2013 erstellte Webanwendungen zum Schutz von Ressourcen](#vs2013)
* Mit Visual Studio 2013 erstellte Web-APIs zum Schutz von Ressourcen
* [Mit Visual Studio 2012 erstellte Webanwendungen zum Schutz von Ressourcen](#vs2012)
* [Mit Visual Studio 2010/2008 oder Windows Identity Foundation erstellte Webanwendungen zum Schutz von Ressourcen](#vs2010)
* [Webanwendungen/-APIs zum Schutz von Ressourcen unter Verwendung anderer Bibliotheken oder durch manuelle Implementierung unterstützter Protokolle](#other)

**Ausnahmen:**

* Bei Anwendungen, die über den Azure AD-Anwendungskatalog hinzugefügt werden, steht jeweils eine separate Anleitung für Signaturschlüssel zur Verfügung. (Dies gilt auch für benutzerdefinierte Anwendungen.) Weitere Informationen finden Sie [hier](../manage-apps/manage-certificates-for-federated-single-sign-on.md).
* Für lokale, mittels Anwendungsproxy veröffentlichte Anwendungen sind Signaturschlüssel nicht relevant.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Native Clientanwendungen mit Ressourcenzugriff
Anwendungen, die nur auf Ressourcen zugreifen (d.h. Microsoft Graph, KeyVault, Outlook-API und andere Microsoft-APIs), rufen in der Regel lediglich ein Token ab und übergeben es an den Ressourcenbesitzer. Da sie keine Ressourcen schützen, untersuchen sie das Token nicht und müssen somit auch nicht sicherstellen, dass es ordnungsgemäß signiert ist.

Native Clientanwendungen (sowohl für Desktop- als auch für Mobilgeräte) fallen in diese Kategorie und werden durch den Rollover nicht beeinträchtigt.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Webanwendungen/-APIs mit Ressourcenzugriff
Anwendungen, die nur auf Ressourcen zugreifen (d.h. Microsoft Graph, KeyVault, Outlook-API und andere Microsoft-APIs), rufen in der Regel lediglich ein Token ab und übergeben es an den Ressourcenbesitzer. Da sie keine Ressourcen schützen, untersuchen sie das Token nicht und müssen somit auch nicht sicherstellen, dass es ordnungsgemäß signiert ist.

Webanwendungen und Web-APIs, die den App-exklusiven Flow (Clientanmeldeinformationen/Clientzertifikat) nutzen, um Token anzufordern, fallen in diese Kategorie und werden durch den Rollover nicht beeinträchtigt.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Mit Azure App Services erstellte Webanwendungen/-APIs zum Schutz von Ressourcen
Die Authentifizierungs-/Autorisierungsfunktion von Azure App Services (EasyAuth) verfügt bereits über die erforderliche Logik zur automatischen Behandlung des Schlüsselrollovers.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Webanwendungen/-APIs zum Schutz von Ressourcen unter Verwendung von .NET OWIN OpenID Connect-, WS-Fed- oder WindowsAzureActiveDirectoryBearerAuthentication-Middleware
Wenn Ihre Anwendung .NET OWIN OpenID Connect-, WS-Fed- oder WindowsAzureActiveDirectoryBearerAuthentication-Middleware verwendet, verfügt sie bereits über die erforderliche Logik zur automatischen Behandlung des Schlüsselrollovers.

Sie können überprüfen, ob Ihre Anwendung diese Komponenten nutzt, indem Sie in der Datei „Startup.cs“ oder „Startup.Auth.cs“ der Anwendung nach den folgenden Codeausschnitten suchen:

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Webanwendungen/-APIs zum Schutz von Ressourcen unter Verwendung von .NET Core OpenID Connect- oder JwtBearerAuthentication-Middleware
Wenn Ihre Anwendung .NET Core OWIN OpenID Connect- oder JwtBearerAuthentication-Middleware verwendet, verfügt sie bereits über die erforderliche Logik zur automatischen Behandlung des Schlüsselrollovers.

Sie können überprüfen, ob Ihre Anwendung diese Komponenten nutzt, indem Sie in der Datei „Startup.cs“ oder „Startup.Auth.cs“ der Anwendung nach den folgenden Codeausschnitten suchen:

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Webanwendungen/-APIs zum Schutz von Ressourcen unter Verwendung des Node.js-passport-azure-ad-Moduls
Wenn Ihre Anwendung das Node.js-passport-ad-Modul verwendet, verfügt sie bereits über die erforderliche Logik zur automatischen Behandlung des Schlüsselrollovers.

Sie können überprüfen, ob Ihre Anwendung passport-ad verwendet, indem Sie in der Datei „app.js“ der Anwendung nach dem folgenden Codeausschnitt suchen:

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Mit Visual Studio 2015 oder höher erstellte Webanwendungen/-APIs zum Schutz von Ressourcen
Wenn Ihre Anwendung mithilfe einer Webanwendungsvorlage in Visual Studio 2015 oder höher erstellt wurde und Sie im Menü **Authentifizierung ändern** die Option **Geschäfts-, Schul- oder Unikonten** ausgewählt haben, verfügt sie bereits über die erforderliche Logik zur automatischen Ausführung des Schlüsselrollovers. Mit dieser Logik, die in die OWIN OpenID Connect-Middleware eingebettet ist, werden die Schlüssel aus dem OpenID Connect Discovery-Dokument abgerufen und zwischengespeichert und regelmäßig aktualisiert.

Wenn Sie die Authentifizierung Ihrer Lösung manuell hinzugefügt haben, verfügt die Anwendung unter Umständen nicht über die erforderliche Logik für einen Schlüsselrollover. Sie müssen diese Logik selbst erstellen oder die Schritte unter [Webanwendungen/-APIs zum Schutz von Ressourcen unter Verwendung anderer Bibliotheken oder durch manuelle Implementierung unterstützter Protokolle](#other)ausführen.

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Mit Visual Studio 2013 erstellte Webanwendungen zum Schutz von Ressourcen
Wenn Ihre Anwendung mithilfe einer Webanwendungsvorlage in Visual Studio 2013 erstellt wurde und Sie im Menü **Authentifizierung ändern** die Option **Organisationskonten** ausgewählt haben, verfügt sie bereits über die erforderliche Logik zur automatischen Behandlung des Schlüsselrollovers. Diese Logik speichert den eindeutigen Bezeichner Ihrer Organisation und die Signaturschlüsselinformationen in zwei Datenbanktabellen, die dem Projekt zugeordnet sind. Sie finden die Verbindungszeichenfolge für die Datenbank in der „Web.config“-Datei des Projekts.

Wenn Sie die Authentifizierung Ihrer Lösung manuell hinzugefügt haben, verfügt die Anwendung unter Umständen nicht über die erforderliche Logik für einen Schlüsselrollover. Sie müssen diese Logik selbst erstellen oder die Schritte unter [Webanwendungen/-APIs zum Schutz von Ressourcen unter Verwendung anderer Bibliotheken oder durch manuelle Implementierung unterstützter Protokolle](#other)ausführen.

Die folgenden Schritte helfen Ihnen dabei, sicherzustellen, dass die Logik in Ihrer Anwendung korrekt funktioniert.

1. Öffnen Sie die Projektmappe in Visual Studio 2013, und klicken Sie im rechten Fensterbereich auf die Registerkarte **Server-Explorer** .
2. Erweitern Sie **Datenverbindungen**, **DefaultConnection** und dann **Tabellen**. Suchen Sie die Tabelle **IssuingAuthorityKeys**, klicken Sie mit der rechten Maustaste darauf, und klicken Sie dann auf **Tabellendaten anzeigen**.
3. In der Tabelle **IssuingAuthorityKeys** befindet sich mindestens eine Zeile, die dem Fingerabdruckwert des Schlüssels entspricht. Löschen Sie alle Zeilen in der Tabelle.
4. Klicken Sie mit der rechten Maustaste auf die Tabelle **Mandanten**, und klicken Sie dann auf **Tabellendaten anzeigen**.
5. Die Tabelle **Mandanten** enthält mindestens eine Zeile, die einer eindeutigen Verzeichnismandanten-ID entspricht. Löschen Sie alle Zeilen in der Tabelle. Wenn Sie die Zeilen in den Tabellen **Mandanten** und **IssuingAuthorityKeys** nicht löschen, erhalten Sie einen Laufzeitfehler.
6. Erstellen Sie die Anwendung, und führen Sie sie aus. Wenn Sie sich bei Ihrem Konto angemeldet haben, können Sie die Anwendung anhalten.
7. Kehren Sie zum **Server-Explorer** zurück, und sehen Sie sich die Werte in den Tabellen **IssuingAuthorityKeys** und **Mandanten** an. Sie werden feststellen, dass sie automatisch mit den entsprechenden Informationen aus dem Verbundmetadaten-Dokument aufgefüllt worden sind.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Mit Visual Studio 2013 erstellte Web-APIs zum Schutz von Ressourcen
Wenn Sie eine Web-API-Anwendung mithilfe der Web-API-Vorlage in Visual Studio 2013 erstellt und im Menü **Authentifizierung ändern** die Option **Organisationskonten** ausgewählt haben, verfügt sie bereits über die erforderliche Logik für einen Schlüsselrollover.

Wenn Sie die Authentifizierung manuell konfiguriert haben, gehen Sie folgendermaßen vor, um zu erfahren, wie Sie Ihre Web-API konfigurieren, damit die Schlüsselinformationen automatisch aktualisiert werden.

Der folgende Codeausschnitt veranschaulicht, wie die neuesten Schlüssel aus dem Verbundmetadaten-Dokument abgerufen werden. Verwenden Sie anschließend den [JWT-Tokenhandler](/previous-versions/dotnet/framework/security/json-web-token-handler), um das Token zu überprüfen. Bei diesem Codeausschnitt wird davon ausgegangen, dass Sie Ihre eigenen Verfahren zum Zwischenspeichern verwenden, um den Schlüssel zum Überprüfen zukünftiger Token von Microsoft Identity Platform in einer Datenbank, Konfigurationsdatei usw. dauerhaft zu aufzubewahren.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Mit Visual Studio 2012 erstellte Webanwendungen zum Schutz von Ressourcen
Wenn Ihre Anwendung in Visual Studio 2012 erstellt wurde, haben Sie wahrscheinlich das Identitäts- und Zugriffstool zum Konfigurieren Ihrer Anwendung verwendet. Möglicherweise verwenden Sie auch die [Validierung der Ausstellernamenregistration (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry). Die VINR ist für die Verwaltung von Informationen zu vertrauenswürdigen Identitätsanbietern (Microsoft Identity Platform) und den Schlüsseln, die zum Überprüfen der von ihnen ausgestellten Token verwendet werden, zuständig. Die VINR erleichtert es zudem, die in einer „Web.config“-Datei gespeicherten Schlüsselinformationen automatisch zu aktualisieren, indem das aktuelle Ihrem Verzeichnis zugeordnete Verbundmetadaten-Dokument heruntergeladen wird. Mit dem aktuellen Dokument wird geprüft, ob die Konfiguration veraltet ist, und bei Bedarf wird die Anwendung aktualisiert, sodass der neue Schlüssel verwendet wird.

Wenn Sie die Anwendung mithilfe eines der Codebeispiele oder der von Microsoft bereitgestellten Dokumentation zur exemplarischen Vorgehensweise erstellt haben, ist die Logik für das Schlüsselrollover in Ihrem Projekt bereits enthalten. Beachten Sie, dass der folgende Code in Ihrem Projekt bereits vorhanden ist. Wenn Ihre Anwendung diese Logik noch nicht enthält, führen Sie die folgenden Schritte aus, um sie hinzuzufügen und um sicherzustellen, dass sie korrekt funktioniert.

1. Fügen Sie im **Projektmappen-Explorer** einen Verweis auf die **System.IdentityModel**-Assembly für das entsprechende Projekt hinzu.
2. Öffnen Sie die Datei **Global.asax.cs** , und fügen Sie die folgenden using-Direktiven hinzu:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Fügen Sie der Datei **Global.asax.cs** die folgende Methode hinzu:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Rufen Sie wie folgt die **RefreshValidationSettings()** -Methode in der **Application_Start()** -Methode in **Global.asax.cs** auf:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Nachdem Sie diese Schritte ausgeführt haben, wird die „Web.config“-Datei Ihrer Anwendung mit den neuesten Informationen aus den Verbundmetadaten-Dokument, einschließlich der neuesten Schlüssel, aktualisiert. Diese Aktualisierung erfolgt jedes Mal, wenn Ihr Anwendungspool in IIS wiederverwendet wird. Standardmäßig ist IIS so eingestellt, dass Anwendungen alle 29 Stunden wiederverwendet werden.

Gehen Sie folgendermaßen vor, um sicherzustellen, dass die Logik für das Schlüsselrollover funktioniert.

1. Wenn Sie sich vergewissert haben, dass Ihre Anwendung den oben dargestellten Code verwendet, öffnen Sie die Datei **Web.config**, navigieren Sie zum Block **\<issuerNameRegistry>** , und suchen Sie dort die folgenden Zeilen:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. In der Tabelle **\<add thumbprint="">** den Fingerabdruckwert, indem Sie ein beliebiges Zeichen durch ein anderes ersetzen. Speichern Sie die Datei **Web.config** .
3. Erstellen Sie die Anwendung, und führen Sie sie anschließend aus. Wenn Sie den Anmeldevorgang abschließen, aktualisiert die Anwendung den Schlüssel, indem die erforderlichen Informationen vom Verbundmetadaten-Dokument Ihres Verzeichnisses heruntergeladen werden. Falls bei der Anmeldung Probleme auftreten, vergewissern Sie sich, dass die Änderungen in Ihrer Anwendung korrekt sind. Lesen Sie hierzu den Artikel [Hinzufügen einer Anmeldung zu einer Webanwendung mithilfe von Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect), oder laden Sie das folgende Codebeispiel herunter: [Mehrinstanzenfähige Cloudanwendung für Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Mit Visual Studio 2008 oder 2010 und Windows Identity Foundation (WIF) v1.0 für .NET 3.5 erstellte Webanwendungen zum Schutz von Ressourcen
Wenn Sie eine Anwendung auf WIF v1. 0 erstellt haben, gibt es keine automatische Aktualisierung der Konfiguration Ihrer Anwendung, um einen neuen Schlüssel zu verwenden.

* *Einfachste Möglichkeit:* Verwenden Sie das im WIF SDK enthaltene Tool „FedUtil“. Dieses Tool kann das neueste Metadatendokument abrufen und Ihre Konfiguration aktualisieren.
* Aktualisieren Sie Ihre Anwendung auf .NET 4.5, das die aktuelle Version von WIF im System-Namespace enthält. Verwenden Sie anschließend die [Validierung der Ausstellernamenregistration (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry) , um automatische Aktualisierungen der Anwendungskonfiguration durchzuführen.
* Führen Sie gemäß den Anweisungen am Ende dieses Anleitungsdokuments einen manuellen Rollover durch.

Anweisungen zum Aktualisieren Ihrer Konfiguration mithilfe des FedUtil-Tools:

1. Stellen Sie sicher, dass das WIF v1.0-SDK auf Ihrem Entwicklungscomputer für Visual Studio 2008 oder 2010 installiert ist. Falls es noch nicht installiert ist, können Sie es [hier herunterladen](https://www.microsoft.com/en-us/download/details.aspx?id=4451).
2. Öffnen Sie die Projektmappe in Visual Studio, klicken Sie anschließend mit der rechten Maustaste auf das betreffende Projekt, und wählen Sie **Update federation metadata** (Verbundmetadaten aktualisieren) aus. Wenn diese Option nicht verfügbar ist, wurde FedUtil und/oder das WIF v1. 0 SDK nicht installiert.
3. Wenn Sie dazu aufgefordert werden, wählen Sie **Aktualisieren** aus, um mit der Aktualisierung Ihrer Verbundmetadaten zu beginnen. Sofern Sie über Zugriff auf die Serverumgebung verfügen, in der die Anwendung gehostet wird, können Sie optional die [automatische Metadaten-Aktualisierungsplanung](/previous-versions/windows-identity-foundation/ee517272(v=msdn.10))von FedUtil verwenden.
4. Klicken Sie auf **Fertig stellen** , um die Aktualisierung abzuschließen.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Webanwendungen/-APIs zum Schutz von Ressourcen unter Verwendung anderer Bibliotheken oder durch manuelle Implementierung unterstützter Protokolle
Wenn Sie eine andere Bibliothek verwenden oder eines der unterstützten Protokolle manuell implementiert haben, müssen Sie die Bibliothek bzw. die Implementierung überprüfen. Stellen Sie sicher, dass der Schlüssel entweder aus dem OpenID Connect Discovery-Dokument oder aus dem Verbundmetadaten-Dokument abgerufen wird. Eine Möglichkeit der Überprüfung ist das Durchsuchen Ihres Codes oder des Codes der Bibliothek nach Aufrufen des OpenID Discovery-Dokuments oder Verbundmetadaten-Dokuments.

Wenn der Schlüssel an einem Speicherort gespeichert wird oder in der Anwendung hartcodiert ist, können Sie ihn manuell abrufen und entsprechend aktualisieren, indem Sie gemäß den Anweisungen am Ende dieses Anleitungsdokuments einen manuellen Rollover durchführen. **Es wird dringend empfohlen, Ihre Anwendung um Unterstützung des automatischen Rollovers zu erweitern,** , wie in den Vorgehensweisen dieses Artikels beschrieben. So verhindern Sie zukünftige Störungen und Mehraufwand, wenn die Rolloverkadenz von Microsoft Identity Platform erhöht oder im Notfall ein außerplanmäßiges Rollover durchgeführt wird.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>So ermitteln Sie, ob Ihre Anwendung betroffen ist
Sie können überprüfen, ob Ihre Anwendung automatische Schlüsselrollover unterstützt, indem Sie die Skripts aus [diesem GitHub-Repository](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Gewusst wie: Durchführen eines manuellen Rollovers, falls die Anwendung keinen automatischen Rollover unterstützt
Falls Ihre Anwendung **keinen** automatischen Rollover unterstützt, müssen Sie einen Prozess einrichten, der in regelmäßigen Abständen die Signaturschlüssel von Microsoft Identity Platform überprüft und entsprechend einen manuellen Rollover durchführt. Entsprechende Skripts und Anweisungen finden Sie in [diesem GitHub-Repository](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey).