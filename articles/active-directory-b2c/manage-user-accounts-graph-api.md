---
title: Verwalten von Benutzern mit der Microsoft Graph-API
titleSuffix: Azure AD B2C
description: Erfahren Sie mehr zum Verwalten von Benutzern in einem Azure AD B2C-Mandanten durch Aufrufen der Microsoft Graph-API und Automatisieren des Vorgangs mithilfe einer Anwendungsidentität.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a5fcbf80850fd9de77e6f9a431afea6d48cb14d1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949410"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Verwalten von Azure AD B2C-Benutzerkonten mit Microsoft Graph

Microsoft Graph ermöglicht Ihnen das Verwalten von Benutzerkonten in Ihrem Azure AD B2C-Verzeichnis durch das Bereitstellen von Methoden zum Erstellen, Lesen, Aktualisieren und Löschen in der Microsoft Graph-API. Sie können einen vorhandenen Benutzerspeicher zu einem Azure AD B2C-Mandanten migrieren und andere Verwaltungsvorgänge für Benutzerkonten durch Aufrufen der Microsoft Graph-API ausführen.

In den folgenden Abschnitten werden die wichtigsten Aspekte der Azure AD B2C-Benutzerverwaltung mit der Microsoft Graph-API vorgestellt. Die hier dargestellten Vorgänge, Typen und Eigenschaften der Microsoft Graph-API stellen nur einen kleinen Teil dessen dar, was in der Referenzdokumentation zur Microsoft Graph-API enthalten ist.

## <a name="register-a-management-application"></a>Registrieren einer Managementanwendung

Bevor von Ihnen geschriebene Benutzerverwaltungsanwendungen oder -skripts mit den Ressourcen in Ihrem Azure AD B2C-Mandanten interagieren können, benötigen Sie eine Anwendungsregistrierung, über die die dafür erforderlichen Berechtigungen erteilt werden.

Befolgen Sie die Anleitungsschritte in diesem Artikel, um eine Anwendungsregistrierung zu erstellen, die von Ihrer Managementanwendung verwendet werden kann:

[Verwalten von Azure AD B2C mit Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Microsoft Graph-Vorgänge für die Benutzerverwaltung

Die folgenden Benutzerverwaltungsvorgänge sind in der [Microsoft Graph-API](/graph/api/resources/user) verfügbar:

- [Abrufen einer Liste der Benutzer](/graph/api/user-list)
- [Erstellen eines Benutzers](/graph/api/user-post-users)
- [Abrufen eines Benutzers](/graph/api/user-get)
- [Aktualisieren eines Benutzers](/graph/api/user-update)
- [Löschen eines Benutzers](/graph/api/user-delete)

## <a name="user-properties"></a>Benutzereigenschaften

### <a name="display-name-property"></a>Eigenschaft „displayName“ (Anzeigename)

Der `displayName` ist der Name, der im Azure-Portal in der Benutzerverwaltung für den Benutzer und im Zugriffstoken, das Azure AD B2C an die Anwendung zurückgibt, angezeigt werden soll. Diese Eigenschaft ist obligatorisch.

### <a name="identities-property"></a>Eigenschaft „Identities“ (Identitäten)

Einem Kundenkonto (von einem Endbenutzer, einem Partner oder einem anderen Benutzer) kann einer der folgenden Identitätstypen zugeordnet werden:

- **Lokale** Identität: Der Benutzername und das Kennwort werden lokal im Azure AD B2C-Verzeichnis gespeichert. Diese Identitäten werden häufig als „lokale Konten“ bezeichnet.
- **Verbundidentität:** Die Identitäten dieser auch als *Konten für soziale Netzwerke* oder *Unternehmenskonten* bezeichneten Benutzer werden von einem Verbundidentitätsanbieter wie Facebook, Microsoft, AD FS oder Salesforce verwaltet.

Ein Benutzer mit einem Kundenkonto kann sich mit mehreren Identitäten anmelden. Er kann z. B. den Benutzernamen, die E-Mail-Adresse, die Mitarbeiter-ID, eine Behördenkennung und andere Daten verwenden. Ein einzelnes Konto kann über mehrere Identitäten verfügen. Dabei kann es sich um lokale Identitäten und Identitäten für soziale Netzwerke handeln, die auch dasselbe Kennwort nutzen können.

In der Microsoft Graph-API werden sowohl lokale als auch Verbundidentitäten im Benutzerattribut `identities` gespeichert, das den Typ [objectIdentity][graph-objectIdentity] aufweist. Die Sammlung `identities` stellt einen Satz von Identitäten dar, die für die Anmeldung bei einem Benutzerkonto verwendet werden. Diese Sammlung ermöglicht dem Benutzer, sich mit den zugehörigen Identitäten beim Benutzerkonto anzumelden.

| Eigenschaft   | type |BESCHREIBUNG|
|:---------------|:--------|:----------|
|signInType|Zeichenfolge| Gibt die Benutzeranmeldetypen in Ihrem Verzeichnis an. Für ein lokales Konto: `emailAddress`, `emailAddress1`, `emailAddress2`, `emailAddress3`, `userName` oder ein beliebiger anderer Typ. Bei einem Konto für soziale Netzwerke muss dies auf `federated` festgelegt werden.|
|Issuer (Aussteller)|Zeichenfolge|Gibt den Aussteller der Identität an. Bei lokalen Konten (bei denen **signInType** nicht `federated` ist) ist diese Eigenschaft der Standarddomänenname des lokalen B2C-Mandanten, z. B. `contoso.onmicrosoft.com`. Bei einer Identität für ein soziales Netzwerk (bei der der **signInType** `federated` ist) entspricht der Wert dem Namen des Ausstellers, z. B. `facebook.com`.|
|issuerAssignedId|Zeichenfolge|Gibt den dem Benutzer vom Aussteller zugewiesenen eindeutigen Bezeichner an. Die Kombination aus **issuer** und **issuerAssignedId** muss innerhalb Ihres Mandanten eindeutig sein. Wenn für das lokale Konto **signInType** auf `emailAddress` oder `userName` festgelegt ist, stellt dies den Anmeldenamen für den Benutzer dar.<br>Wenn **signInType** auf <ul><li>`emailAddress` festgelegt ist (oder mit `emailAddress` beginnt wie bei `emailAddress1`), muss die **issuerAssignedId** eine gültige E-Mail-Adresse sein.</li><li>`userName` festgelegt (oder einen beliebigen anderen Wert) ist, muss die **issuerAssignedId** ein gültiger [lokaler Teil einer E-Mail-Adresse](https://tools.ietf.org/html/rfc3696#section-3) sein.</li><li>`federated` festgelegt ist, stellt die **issuerAssignedId** den eindeutigen Bezeichner des Verbundkontos dar.</li></ul>|

Die folgende **Identities**-Eigenschaft mit einer lokalen Kontoidentität mit einem Anmeldenamen, einer E-Mail-Adresse für die Anmeldung und einer Social Media-Identität. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Bei Verbundidentitäten handelt es sich abhängig vom Identitätsanbieter bei der **issuerAssignedId** um einen eindeutigen Wert für einen bestimmten Benutzer pro Anwendung oder ein Entwicklungskonto. Konfigurieren Sie die Azure AD B2C-Richtlinie mit der gleichen Anwendungs-ID, die zuvor vom Anbieter des sozialen Netzwerks zugewiesen wurde, oder mit der einer anderen Anwendung im selben Entwicklungskonto.

### <a name="password-profile-property"></a>Eigenschaft „passwordProfile“ (Kennwortprofil)

Für eine lokale Identität ist die **passwordProfile**-Eigenschaft erforderlich und enthält das Kennwort des Benutzers. Die `forceChangePasswordNextSignIn`-Eigenschaft muss auf `false` festgelegt werden.

Für eine Verbundidentität (Identität eines sozialen Netzwerks) ist die **passwordProfile**-Eigenschaft nicht erforderlich.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Eigenschaft „passwordPolicies“ (Kennwortrichtlinien)

Die Kennwortrichtlinie von Azure AD B2C (für lokale Konten) basiert auf der Richtlinie für [hohe Kennwortsicherheit](../active-directory/authentication/concept-sspr-policy.md) von Azure Active Directory. Die Azure AD B2C-Richtlinien zur Registrierung, Anmeldung und Kennwortzurücksetzung erfordern sichere Kennwörter, die nicht ablaufen.

Wenn bei Benutzermigrationsvorgängen die Konten, die Sie migrieren möchten, über eine geringere Kennwortsicherheit als die [hohe Kennwortsicherheit](../active-directory/authentication/concept-sspr-policy.md) unter Azure AD B2C verfügen, können Sie die Erzwingung sicherer Kennwörter deaktivieren. Legen Sie zum Ändern der standardmäßigen Kennwortrichtlinie die Eigenschaft `passwordPolicies` auf `DisableStrongPassword` fest. Beispielsweise können Sie die Anforderung zum Erstellen eines Benutzers wie folgt ändern:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Erweiterungseigenschaften

Für jede kundenorientierte Anwendung gelten spezifische Anforderungen im Hinblick auf die Informationen, die erfasst werden sollen. Der Azure AD B2C-Mandant umfasst einen integrierten Satz von in Eigenschaften gespeicherten Informationen, z. B. Vorname, Nachname, Ort und Postleitzahl. Mit Azure AD B2C haben Sie die Möglichkeit, den für die einzelnen Kundenkonten gespeicherten Satz von Eigenschaften zu erweitern. Weitere Informationen zum Definieren benutzerdefinierter Attribute finden Sie unter [Benutzerdefinierte Attribute (Benutzerflows)](user-flow-custom-attributes.md) und [Benutzerdefinierte Attribute (benutzerdefinierte Richtlinien)](custom-policy-custom-attributes.md).

Die Microsoft Graph-API unterstützt das Erstellen und Aktualisieren eines Benutzers mit Erweiterungsattributen. Erweiterungsattribute in den Graph-API werden gemäß der Konvention `extension_ApplicationClientID_attributename` benannt, wobei `ApplicationClientID` die **Anwendungs-ID (Client)** der Anwendung `b2c-extensions-app` ist (im Azure-Portal unter **App-Registrierungen** > **Alle Anwendungen**). Beachten Sie, dass die **Anwendungs-ID (Client)** in der Darstellung im Namen des Erweiterungsattributs keine Bindestriche enthält. Beispiel:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Codebeispiel: Programmgesteuertes Verwalten von Benutzerkonten

Dieses Codebeispiel ist eine .NET Core-Konsolenanwendung, die das [Microsoft Graph SDK](/graph/sdks/sdks-overview) für die Interaktion mit der Microsoft Graph-API verwendet. Der Code veranschaulicht, wie die API aufgerufen wird, um Benutzer in einem Azure AD B2C-Mandanten programmgesteuert zu verwalten.
Sie können das [Beispielarchiv herunterladen](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), das Repository auf GitHub [durchsuchen](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) oder das Repository klonen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Nachdem Sie das Codebeispiel abgerufen haben, konfigurieren Sie es für Ihre Umgebung, und erstellen Sie dann das Projekt:

1. Öffnen Sie das Projekt in [Visual Studio](https://visualstudio.microsoft.com) oder [Visual Studio Code](https://code.visualstudio.com).
1. Öffnen Sie `src/appsettings.json`.
1. Ersetzen Sie im Abschnitt `appSettings` den Wert `your-b2c-tenant` durch den Namen Ihres Mandanten und `Application (client) ID` und `Client secret` durch die Werte für die Registrierung Ihrer Managementanwendung. (Weitere Informationen finden Sie im Abschnitt [Registrieren einer Managementanwendung](#register-a-management-application) dieses Artikels.)
1. Öffnen Sie in Ihrem lokalen Klon des Repositorys ein Konsolenfenster, wechseln Sie in das Verzeichnis `src`, und erstellen Sie dann das Projekt:
    ```console
    cd src
    dotnet build
    ```
1. Führen Sie die Anwendung mit Befehl `dotnet` aus:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

Die Anwendung zeigt eine Liste der Befehle an, die Sie ausführen können. Beispielsweise können Sie alle Benutzer oder einen einzelnen Benutzer abrufen, einen Benutzer löschen, das Kennwort eines Benutzers aktualisieren und einen Massenimport ausführen.

### <a name="code-discussion"></a>Überlegungen zum Code

Der Beispielcode verwendet das [Microsoft Graph SDK](/graph/sdks/sdks-overview), das entwickelt wurde, um das Entwickeln hochwertiger, effizienter und robuster Anwendungen mit Zugriff auf Microsoft Graph zu vereinfachen.

Alle Anforderungen an die Microsoft Graph-API erfordern ein Zugriffstoken für die Authentifizierung. Die Lösung nutzt das NuGet-Paket [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/), das einen auf einem Authentifizierungsszenario basierenden Wrapper der Microsoft Authentication Library (MSAL) für die Verwendung mit dem Microsoft Graph SDK bereitstellt.

Die `RunAsync`-Methode in der Datei _Program.cs_ führt Folgendes aus:

1. Sie liest Anwendungseinstellungen aus der Datei _appsettings.json_
1. Sie initialisiert den Authentifizierungsanbieter unter Verwendung des [Gewährungsflows für OAuth 2.0-Clientanmeldeinformationen](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Mit dem Gewährungsflow für Clientanmeldeinformationen kann die App ein Zugriffstoken abrufen, um die Microsoft Graph-API aufzurufen.
1. Sie richtet den Microsoft Graph-Dienstclient mit dem Authentifizierungsanbieter ein:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Der initialisierte *GraphServiceClient* wird dann in _UserService.cs_ verwendet, um die Vorgänge zur Benutzerverwaltung auszuführen. Beispielsweise können Sie eine Liste der Benutzerkonten im Mandanten abrufen:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

Das [Ausführen von API-Aufrufen mithilfe der Microsoft Graph-SDKs](/graph/sdks/create-requests) umfasst Informationen zum Lesen und Schreiben von Informationen aus Microsoft Graph. Verwenden Sie `$select`, um die zurückgegebenen Eigenschaften zu steuern, benutzerdefinierte Abfrageparameter bereitzustellen und die Abfrageparameter `$filter` und `$orderBy` zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

Einen vollständigen Index der Microsoft Graph-API-Vorgänge, die für Azure AD B2C-Ressourcen unterstützt werden, finden Sie unter [Microsoft Graph Vorgänge, die für Azure AD B2C verfügbar sind](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)