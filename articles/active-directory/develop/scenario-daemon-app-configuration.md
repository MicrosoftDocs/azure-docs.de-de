---
title: 'Konfigurieren von Daemon-Apps, die Web-APIs aufrufen: Microsoft Identity Platform | Azure'
description: Hier erfahren Sie, wie Sie den Code Ihrer Daemonanwendung konfigurieren, die Web-APIs aufruft (App-Konfiguration).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 8a01ee4e2b0d4e72c1b17cf56953675e735ead79
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582889"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Daemon-App, die Web-APIs aufruft – Codekonfiguration

Erfahren Sie, wie Sie den Code Ihrer Daemonanwendung konfigurieren können, die Web-APIs aufruft.

## <a name="msal-libraries-that-support-daemon-apps"></a>MSAL-Bibliotheken, die Daemon-Apps unterstützen

Folgende Microsoft-Bibliotheken unterstützen Daemon-Apps:

  MSAL-Bibliothek | BESCHREIBUNG
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Die Plattformen .NET Framework und .NET Core werden zum Entwickeln von Daemonanwendungen unterstützt. (UWP, Xamarin.iOS und Xamarin.Android werden nicht unterstützt, da mit diesen Plattformen öffentliche Clientanwendungen erstellt werden.)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Unterstützung für Daemonanwendungen in Python
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Unterstützung für Daemonanwendungen in Java

## <a name="configure-the-authority"></a>Konfigurieren der Autorität

Daemonanwendungen verwenden Anwendungsberechtigungen anstelle von delegierten Berechtigungen. Daher kann ihr unterstützter Kontotyp kein Konto in einem Organisationsverzeichnis und kein persönliches Microsoft-Konto (z. B. Skype, Xbox, Outlook.com) sein. Es gibt keinen Mandantenadministrator, der der Daemonanwendung für persönliche Microsoft-Konten Einwilligung erteilen könnte. Sie müssen *accounts in my organization* (Konten in meiner Organisation) oder *accounts in any organization* (Konten in allen Organisationen) auswählen.

Die in der Anwendungskonfiguration angegebene Autorität sollte über einen Mandanten verfügen (eine Mandanten-ID oder ein Domänennamen, die zu Ihrer Organisation gehören).

Selbst wenn Sie ein mehrinstanzenfähiges Tool bereitstellen möchten, sollten Sie bei diesem Flow eine Mandanten-ID oder einen Domänennamen verwenden und **nicht** `common` oder `organizations`, da der Dienst nicht zuverlässig ableiten kann, welcher Mandant verwendet werden soll.

## <a name="configure-and-instantiate-the-application"></a>Konfigurieren und Instanziieren der Anwendung

Die Clientanmeldeinformationen in MSAL-Bibliotheken (Geheimnis oder Zertifikat) werden als Parameter der vertraulichen Clientanwendungskonstruktion übergeben.

> [!IMPORTANT]
> Auch wenn Ihre Anwendung eine Konsolenanwendung ist, die als Dienst ausgeführt wird, muss sie eine vertrauliche Clientanwendung sein, wenn sie eine Daemonanwendung ist.

### <a name="configuration-file"></a>Konfigurationsdatei

Die Konfigurationsdatei definiert Folgendes:

- Die Cloudinstanz und die Mandanten-ID, die zusammen die *Autorität* bilden.
- Die Client-ID, die Sie bei der Anwendungsregistrierung erhalten haben
- Ein geheimer Clientschlüssel oder ein Zertifikat

# <a name="net"></a>[.NET](#tab/dotnet)

Hier finden Sie ein Beispiel für die Definition der Konfiguration in der Datei [*appsettings.json*](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json). Dieses Beispiel stammt aus dem Codebeispiel [.NET Core-Konsolen-Daemon](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) auf GitHub.

```json
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Sie stellen entweder ein `ClientSecret` oder einen `CertificateName` bereit. Diese Einstellungen sind exklusiv.

# <a name="python"></a>[Python](#tab/python)

Beim Erstellen eines vertraulichen Clients mit geheimen Clientschlüsseln sieht die Konfigurationsdatei [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) im Beispiel für den [Python-Daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) wie folgt aus:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Beim Erstellen eines vertraulichen Clients mit Zertifikaten sieht die Konfigurationsdatei [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) im Beispiel für den [Python-Daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) wie folgt aus:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

---

### <a name="instantiate-the-msal-application"></a>Instanziierung der MSAL-Anwendung

Sie müssen zum Instanziieren der MSAL-Anwendung das MSAL-Paket hinzufügen, darauf verweisen oder es importieren (abhängig von der Sprache).

Die Konstruktion unterscheidet sich je nach Verwendung von geheimen Clientschlüsseln oder Zertifikaten (oder als erweitertes Szenario mit signierten Assertionen).

#### <a name="reference-the-package"></a>Verweisen auf das Paket

Verweisen Sie im Anwendungscode auf das MSAL-Paket.

# <a name="net"></a>[.NET](#tab/dotnet)

Fügen Sie Ihrer Anwendung das NuGet-Paket [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) hinzu, und fügen Sie dann im Code eine `using`-Direktive hinzu, um darauf zu verweisen.

In MSAL.NET wird die vertrauliche Clientanwendung durch die `IConfidentialClientApplication`-Schnittstelle dargestellt.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
import json
import sys
import logging
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Instanziieren der vertraulichen Clientanwendung mit einem geheimen Clientschlüssel

Im Folgenden finden Sie den Code zum Instanziieren der vertraulichen Clientanwendung mit einem geheimen Clientschlüssel:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Die `Authority` ist eine Verkettung der Cloudinstanz und der Mandanten-ID, z. B. `https://login.microsoftonline.com/contoso.onmicrosoft.com` oder `https://login.microsoftonline.com/eb1ed152-0000-0000-0000-32401f3f9abd`. In der im Abschnitt [Konfigurationsdatei](#configuration-file) abgebildeten Datei *appsettings.json* werden diese durch die Werte `Instance` und `Tenant` dargestellt.

In dem Codebeispiel, aus dem der vorherige Ausschnitt stammt, ist `Authority` eine Eigenschaft für die [AuthenticationConfig](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/ffc4a9f5d9bdba5303e98a1af34232b434075ac7/1-Call-MSGraph/daemon-console/AuthenticationConfig.cs#L61-L70)-Klasse und als solche definiert:

```csharp
/// <summary>
/// URL of the authority
/// </summary>
public string Authority
{
    get
    {
        return String.Format(CultureInfo.InvariantCulture, Instance, Tenant);
    }
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Instanziieren der vertraulichen Clientanwendung mit einem Clientzertifikat

Im Folgenden finden Sie den Code zum Erstellen einer Anwendung mit einem Zertifikat:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

In MSAL.Java gibt es zwei Generatoren zum Instanziieren der vertraulichen Clientanwendung mit Zertifikaten:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

oder

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Erweitertes Szenario: Instanziieren der vertraulichen Clientanwendung mit Clientassertionen

# <a name="net"></a>[.NET](#tab/dotnet)

Die vertrauliche Clientanwendung kann ihre Identität statt mit einem geheimen Clientschlüssel oder einem Zertifikat auch mithilfe von Clientassertionen nachweisen.

MSAL.NET verfügt über zwei Methoden, um für die vertrauliche Client-App signierte Assertionen bereitzustellen:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Stellen Sie ein JSON Web Token bereit, wenn Sie `WithClientAssertion` verwenden. Dieses erweiterte Szenario wird unter [Clientassertionen](msal-net-client-assertions.md) ausführlich erläutert.

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Bei Verwendung von `WithClientClaims` erstellt MSAL.NET eine signierte Assertion, die die von Azure AD erwarteten Ansprüche sowie zusätzliche Clientansprüche enthält, die Sie senden möchten.
Dies wird im folgenden Code veranschaulicht:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();
```

Auch hierzu finden Sie ausführliche Informationen unter [Clientassertionen](msal-net-client-assertions.md).

# <a name="python"></a>[Python](#tab/python)

In MSAL Python können Sie Clientansprüche mithilfe der Ansprüche bereitstellen, die mit dem privaten Schlüssel von `ConfidentialClientApplication` signiert werden.

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Einzelheiten finden Sie in der Referenzdokumentation zu MSAL Python für die [Vertrauliche Clientanwendung (ConfidentialClientApplication)](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

## <a name="next-steps"></a>Nächste Schritte

# <a name="net"></a>[.NET](#tab/dotnet)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Abrufen eines Tokens für die App](./scenario-daemon-acquire-token.md?tabs=dotnet).

# <a name="python"></a>[Python](#tab/python)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Abrufen eines Tokens für die App](./scenario-daemon-acquire-token.md?tabs=python).

# <a name="java"></a>[Java](#tab/java)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Abrufen eines Tokens für die App](./scenario-daemon-acquire-token.md?tabs=java).

---
