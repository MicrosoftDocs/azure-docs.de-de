---
title: Verwalten von Ressourcen mit Microsoft Graph
titleSuffix: Azure AD B2C
description: Erfahren Sie mehr zum Verwalten von Ressourcen in einem Azure AD B2C-Mandanten durch das Aufrufen der Microsoft Graph-API und Automatisieren des Vorgangs mithilfe einer Anwendungsidentität.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a7e9e523d3aae7cf1444c048c023ca1d85fde41f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98952228"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Verwalten von Azure AD B2C mit Microsoft Graph

Mit Microsoft Graph können Sie Ressourcen in Ihrem Azure AD B2C-Verzeichnis verwalten. In der Microsoft Graph-API werden die folgenden Vorgänge für die Verwaltung von Azure AD B2C-Ressourcen unterstützt (einschließlich Benutzern, Identitätsanbietern, Benutzerflows, benutzerdefinierten Richtlinien und Richtlinienschlüsseln). Jeder Link in den folgenden Abschnitten bezieht sich auf die entsprechende Seite in der Referenz zur Microsoft Graph-API für diesen Vorgang. 

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden der MS Graph-API und Interagieren mit Ressourcen auf Ihrem Azure AD B2C-Mandanten benötigen Sie eine Anwendungsregistrierung, bei der die erforderlichen Berechtigungen gewährt werden. Führen Sie die Schritte im Artikel [Verwalten von Azure AD B2C mit Microsoft Graph](microsoft-graph-get-started.md) aus, um eine Anwendungsregistrierung zu erstellen, die von Ihrer Verwaltungsanwendung genutzt werden kann. 

## <a name="user-management"></a>Benutzerverwaltung

- [Auflisten von Benutzern](/graph/api/user-list)
- [Erstellen eines Consumerbenutzers](/graph/api/user-post-users)
- [Abrufen eines Benutzers](/graph/api/user-get)
- [Aktualisieren eines Benutzers](/graph/api/user-update)
- [Löschen eines Benutzers](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Verwaltung der Telefonnummer des Benutzers (Beta)

Eine Telefonnummer, mit der sich ein Benutzer per [SMS oder Sprachanruf](identity-provider-local.md#phone-sign-in-preview) oder per [mehrstufiger Authentifizierung](multi-factor-authentication.md) anmelden kann. Weitere Informationen finden Sie unter [Ressourcentyp „phoneAuthenticationMethod“](/graph/api/resources/phoneauthenticationmethod).

- [Add (Hinzufügen)](/graph/api/authentication-post-phonemethods)
- [Liste](/graph/api/authentication-list-phonemethods)
- [Get](/graph/api/phoneauthenticationmethod-get)
- [Aktualisieren](/graph/api/phoneauthenticationmethod-update)
- [Löschen](/graph/api/phoneauthenticationmethod-delete)

Hinweis: Durch den [Auflistungsvorgang](/graph/api/authentication-list-phonemethods) werden nur aktivierte Telefonnummern zurückgegeben. Die folgende Telefonnummer muss aktiviert werden, um mit den Auflistungsvorgängen verwendet werden zu können. 

![Aktivieren der Anmeldung per Telefon](./media/microsoft-graph-operations/enable-phone-sign-in.png)

## <a name="self-service-password-reset-email-address-beta"></a>E-Mail-Adresse für Self-Service-Kennwortzurücksetzung (Beta)

Eine E-Mail-Adresse, die von einem [Konto mit benutzernamenbasierter Anmeldung](identity-provider-local.md#username-sign-in) verwendet werden kann, um das Kennwort zurückzusetzen. Weitere Informationen finden Sie unter [Ressourcentyp „emailAuthenticationMethod“](/graph/api/resources/emailauthenticationmethod).

- [Add (Hinzufügen)](/graph/api/emailauthenticationmethod-post)
- [Liste](/graph/api/emailauthenticationmethod-list)
- [Get](/graph/api/emailauthenticationmethod-get)
- [Aktualisieren](/graph/api/emailauthenticationmethod-update)
- [Löschen](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Identitätsanbieter

Verwalten Sie die [Identitätsanbieter](add-identity-provider.md), die für die Benutzerflows in Ihrem Azure AD B2C-Mandanten verfügbar sind.

- [Auflisten von Identitätsanbietern, die im Azure AD B2C-Mandanten registriert sind](/graph/api/identityprovider-list)
- [Erstellen eines Identitätsanbieters](/graph/api/identityprovider-post-identityproviders)
- [Abrufen eines Identitätsanbieters](/graph/api/identityprovider-get)
- [Aktualisieren eines Identitätsanbieters](/graph/api/identityprovider-update)
- [Löschen eines Identitätsanbieters](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Benutzerflow

Konfigurieren Sie vordefinierte Richtlinien für Registrierung, Anmeldung, kombinierte Registrierung und Anmeldung, Kennwortzurücksetzung und Profilaktualisierung.

- [Auflisten von Benutzerflows](/graph/api/identitycontainer-list-b2cuserflows)
- [Erstellen eines Benutzerflows](/graph/api/identitycontainer-post-b2cuserflows)
- [Abrufen eines Benutzerflows](/graph/api/b2cidentityuserflow-get)
- [Löschen eines Benutzerflows](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Authentifizierungsmethoden mit Benutzerflow (Beta)

Wählen Sie einen Mechanismus, mit dem sich Benutzer über lokale Konten registrieren können. Lokale Konten sind die Konten, bei denen die Identitätsassertion von Azure AD durchgeführt wird. Weitere Informationen finden Sie unter [Ressourcentyp „b2cAuthenticationMethodsPolicy“](/graph/api/resources/b2cauthenticationmethodspolicy).

- [Get](/graph/api/b2cauthenticationmethodspolicy-get)
- [Aktualisieren](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Benutzerdefinierte Richtlinien

Mit den folgenden Vorgängen können Sie Ihre Azure AD B2C-Vertrauensframeworkrichtlinien verwalten, die als [benutzerdefinierte Richtlinien](custom-policy-overview.md) bezeichnet werden.

- [Auflisten aller Vertrauensframeworkrichtlinien, die in einem Mandanten konfiguriert sind](/graph/api/trustframework-list-trustframeworkpolicies)
- [Erstellen einer Vertrauensframeworkrichtlinie](/graph/api/trustframework-post-trustframeworkpolicy)
- [Lesen der Eigenschaften einer vorhandenen Vertrauensframeworkrichtlinie](/graph/api/trustframeworkpolicy-get)
- [Aktualisieren oder Erstellen einer Vertrauensframeworkrichtlinie](/graph/api/trustframework-put-trustframeworkpolicy)
- [Löschen einer vorhandenen Vertrauensframeworkrichtlinie](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Richtlinienschlüssel

Das Identity Experience Framework speichert die Geheimnisse, auf die in einer benutzerdefinierten Richtlinie verwiesen wird, um Vertrauen zwischen den Komponenten herzustellen. Diese Geheimnisse können symmetrische oder asymmetrische Schlüssel/Werte sein. Im Azure-Portal werden diese Entitäten als **Richtlinienschlüssel** angezeigt.

Die vorrangige Ressource für Richtlinienschlüssel in der Microsoft Graph-API ist das [Vertrauensframework-Keyset](/graph/api/resources/trustframeworkkeyset). Jedes **Keyset** enthält mindestens einen **Schlüssel**. Zum Erstellen eines Schlüssels müssen Sie zunächst ein leeres Keyset erstellen und anschließend darin einen Schlüssel generieren. Sie können ein manuelles Geheimnis erstellen, ein Zertifikat hochladen oder einen PKCS12-Schlüssel erstellen. Der Schlüssel kann ein generiertes Geheimnis, eine Zeichenfolge (z. B. der geheime Facebook-Anwendungsschlüssel) oder ein von Ihnen hochgeladenes Zertifikat sein. Wenn ein Keyset mehrere Schlüssel enthält, ist immer nur einer der Schlüssel aktiv.

### <a name="trust-framework-policy-keyset"></a>Vertrauensframeworkrichtlinie – Keyset

- [Auflisten der Vertrauensframework-Keysets](/graph/api/trustframework-list-keysets)
- [Erstellen eines Vertrauensframework-Keysets](/graph/api/trustframework-post-keysets)
- [Abrufen eines Keysets](/graph/api/trustframeworkkeyset-get)
- [Aktualisieren eines Vertrauensframework-Keysets](/graph/api/trustframeworkkeyset-update)
- [Löschen eines Vertrauensframework-Keysets](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Vertrauensframeworkrichtlinie – Schlüssel

- [Abrufen des derzeit aktiven Schlüssels im Keyset](/graph/api/trustframeworkkeyset-getactivekey)
- [Erstellen eines Schlüssels im Keyset](/graph/api/trustframeworkkeyset-generatekey)
- [Hochladen eines zeichenfolgebasierten Geheimnisses](/graph/api/trustframeworkkeyset-uploadsecret)
- [Hochladen eines X.509-Zertifikats](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Hochladen eines Zertifikats im PKCS12-Format](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Anwendungen

- [Auflisten von Anwendungen](/graph/api/application-list)
- [Erstellen einer Anwendung](/graph/api/resources/application)
- [Aktualisieren einer Anwendung](/graph/api/application-update)
- [Erstellen eines Dienstprinzipals](/graph/api/resources/serviceprincipal)
- [Erstellen einer oauth2Permission-Genehmigung](/graph/api/resources/oauth2permissiongrant)
- [Löschen der Anwendung](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Anwendungserweiterungseigenschaften

- [Auflisten von Erweiterungseigenschaften](/graph/api/application-list-extensionproperty)

Azure AD B2C stellt ein Verzeichnis bereit, das 100 benutzerdefinierte Attribute pro Benutzer enthalten kann. Bei Benutzerflows werden diese Erweiterungseigenschaften [mit dem Azure-Portal verwaltet](user-flow-custom-attributes.md). Bei benutzerdefinierten Richtlinien erstellt Azure AD B2C die Eigenschaft für Sie, wenn die Richtlinie zum ersten Mal einen Wert in die Erweiterungseigenschaft schreibt.

## <a name="audit-logs"></a>Überwachungsprotokolle

- [Auflisten von Überwachungsprotokollen](/graph/api/directoryaudit-list)

Weitere Informationen zum Zugriff auf Azure AD B2C-Überwachungsprotokolle finden Sie unter [Zugriff auf Azure AD B2C-Überwachungsprotokolle](view-audit-logs.md).

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Codebeispiel: Programmgesteuertes Verwalten von Benutzerkonten

Dieses Codebeispiel ist eine .NET Core-Konsolenanwendung, die das [Microsoft Graph SDK](/graph/sdks/sdks-overview) für die Interaktion mit der Microsoft Graph-API verwendet. Der Code veranschaulicht, wie die API aufgerufen wird, um Benutzer in einem Azure AD B2C-Mandanten programmgesteuert zu verwalten.
Sie können das [Beispielarchiv herunterladen](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), das Repository auf GitHub [durchsuchen](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) oder das Repository klonen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Nachdem Sie das Codebeispiel abgerufen haben, konfigurieren Sie es für Ihre Umgebung, und erstellen Sie dann das Projekt:

1. Öffnen Sie das Projekt in [Visual Studio](https://visualstudio.microsoft.com) oder [Visual Studio Code](https://code.visualstudio.com).
1. Öffnen Sie `src/appsettings.json`.
1. Ersetzen Sie im Abschnitt `appSettings` die Zeichenfolge `your-b2c-tenant` durch den Namen Ihres Mandanten und `Application (client) ID` und `Client secret` durch die Werte für Ihre Registrierung der Verwaltungsanwendung. Weitere Informationen finden Sie unter [Registrieren einer Microsoft Graph-Anwendung](microsoft-graph-get-started.md).
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

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
