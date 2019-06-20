---
title: Verwenden der Graph-API in Azure Active Directory B2C | Microsoft-Dokumentation
description: Aufrufen der Graph-API für einen B2C-Mandanten durch Verwenden einer Anwendungsidentität zum Automatisieren des Prozesses.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 88b1d05a47f4a8267ab936a922ac190a925bd5ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510181"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Verwenden der Azure AD-Graph-API

>[!NOTE]
> Sie müssen die [Azure AD-Graph-API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-operations-overview) verwenden, um Benutzer in einem Azure AD B2C-Verzeichnis zu verwalten. Dies unterscheidet sich von der Microsoft Graph-API. [Hier](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/)erhalten Sie weitere Informationen.

Azure Active Directory (Azure AD) B2C-Mandanten sind normalerweise sehr groß. Das bedeutet, dass viele allgemeine Aufgaben zur Mandantenverwaltung programmgesteuert durchgeführt werden müssen. Ein gutes Beispiel ist die Benutzerverwaltung. Unter Umständen müssen Sie einen vorhandenen Benutzerspeicher zu einem B2C-Mandanten migrieren. Sie möchten die Benutzerregistrierung vielleicht auf Ihrer eigenen Seite hosten und Benutzerkonten im Azure AD B2C-Verzeichnis im Hintergrund erstellen. Diese Arten von Aufgaben erfordern die Fähigkeit zum Erstellen, Lesen, Aktualisieren und Löschen von Benutzerkonten. Hierfür können Sie die Azure AD Graph-API verwenden.

Für B2C-Mandanten gibt es zwei primäre Modi für die Kommunikation mit der Graph-API.

* Für interaktive Aufgaben mit einmaliger Ausführung sollten Sie beim Ausführen der Aufgaben ein Administratorkonto im B2C-Mandanten verwenden. In diesem Modus muss sich ein Administrator mit Anmeldeinformationen anmelden, bevor er alle Aufrufe der Graph-API ausführen kann.
* Für automatisierte, kontinuierliche Aufgaben sollte eine Art von Dienstkonto verwendet werden, dem Sie die benötigten Rechte zum Ausführen von Verwaltungsaufgaben gewähren. In Azure AD registrieren Sie dazu eine Anwendung und authentifizieren sie bei Azure AD. Dies geschieht mithilfe einer **Anwendungs-ID** , die die [OAuth 2.0-Clientanmeldeinformationen](../active-directory/develop/service-to-service.md)verwendet. In diesem Fall verhält sich die Anwendung beim Aufrufen der Graph-API wie sie selbst und nicht wie ein Benutzer.

In diesem Artikel wird erläutert, wie Sie den automatisierten Anwendungsfall durchführen. Sie erstellen das .NET 4.5-Element `B2CGraphClient`, das Benutzervorgänge zum Erstellen, Lesen, Aktualisieren und Löschen (CRUD) ausführt. Der Client verfügt über eine Windows-Befehlszeilenschnittstelle, über die Sie verschiedene Methoden aufrufen können. Der Code ist aber so geschrieben, dass er sich auf nicht interaktive, automatisierte Weise verhält.

## <a name="get-an-azure-ad-b2c-tenant"></a>Erhalten eines Azure AD-B2C-Mandanten
Damit Sie Anwendungen oder Benutzer erstellen können, benötigen Sie einen Azure AD B2C-Mandanten. Falls Sie noch keinen Mandanten besitzen, führen Sie die [ersten Schritte mit Azure AD B2C](active-directory-b2c-get-started.md)aus.

## <a name="register-your-application-in-your-tenant"></a>Registrieren Ihrer Anwendung in Ihrem Mandanten
Nachdem Sie über einen B2C-Mandanten verfügen, müssen Sie Ihre Anwendung über das [Azure-Portal](https://portal.azure.com) registrieren.

> [!IMPORTANT]
> Um die Graph-API mit dem B2C-Mandanten zu verwenden, müssen Sie eine Anwendung mit dem Dienst *App-Registrierungen* im Azure-Portal registrieren, **NICHT** über das Menü *Anwendungen* in Azure AD B2C. Anhand der folgenden Anweisungen gelangen Sie zum entsprechenden Menü. Sie können vorhandene B2C-Anwendungen, die Sie im Menü *Anwendungen* von Azure AD B2C registriert haben, nicht wiederverwenden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie Ihren Azure AD B2C-Mandanten aus, indem Sie in der rechten oberen Ecke der Seite Ihr Konto auswählen.
3. Wählen Sie im linken Navigationsbereich **Alle Dienste** aus, und klicken Sie auf **App-Registrierungen** und dann auf **Hinzufügen**.
4. Folgen Sie der Anleitung, und erstellen Sie eine neue Anwendung. 
    1. Wählen Sie **Web-App/API** als Anwendungstyp aus.    
    2. Geben Sie eine **beliebige Anmelde-URL** an (z. B. `https://B2CGraphAPI`), da dies für dieses Beispiel nicht relevant ist.  
5. Die Anwendung wird jetzt in der Liste der Anwendungen angezeigt. Klicken Sie darauf, um die **Anwendungs-ID** (auch als Client-ID bezeichnet) abzurufen. Kopieren Sie sie, da Sie sie in einem späteren Abschnitt benötigen.
6. Klicken Sie im Menü „Einstellungen“ auf **Schlüssel**.
7. Geben Sie im Abschnitt **Kennwörter** die Schlüsselbeschreibung ein, und wählen Sie eine Dauer aus. Klicken Sie dann auf **Speichern**. Kopieren Sie den Schlüsselwert (auch als geheimer Clientschlüssel bezeichnet) für die Verwendung in einem späteren Abschnitt.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Konfigurieren der Berechtigungen zum Erstellen, Lesen und Aktualisieren für Ihre Anwendung
Nun müssen Sie Ihre Anwendung so konfigurieren, dass sie alle erforderlichen Berechtigungen zum Erstellen, Lesen, Aktualisieren und Löschen von Benutzern erhält.

1. Fahren Sie im Menü „App-Registrierungen“ im Azure-Portal fort, und wählen Sie Ihre Anwendung aus.
2. Klicken Sie im Menü „Einstellungen“ auf **Erforderliche Berechtigungen**.
3. Klicken Sie im Menü „Erforderliche Berechtigungen“ auf **Microsoft Azure Active Directory**.
4. Wählen Sie auf im Menü „Zugriff aktivieren“ die Berechtigung **Lese- und Schreibzugriff auf Verzeichnisdaten** unter **Anwendungsberechtigungen** aus, und klicken Sie auf **Speichern**.
5. Klicken Sie schließlich wieder im Menü „Erforderliche Berechtigungen“ auf die Schaltfläche **Berechtigungen erteilen**.

Sie verfügen jetzt über eine Anwendung mit der Berechtigung zum Erstellen, Lesen und Aktualisieren von Benutzern in Ihrem B2C-Mandanten.

> [!NOTE]
> Die Berechtigungserteilung dauert unter Umständen einige Minuten.
> 
> 

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>Konfigurieren der Berechtigungen für das Löschen oder Aktualisieren des Kennworts für Ihre Anwendung
Derzeit schließt die Berechtigung *Lese- und Schreibzugriff auf Verzeichnisdaten* **NICHT** die Möglichkeit ein, Benutzer zu löschen oder Benutzerkennwörter zu ändern. Wenn Ihre Anwendung die Möglichkeit erhalten soll, Benutzer zu löschen oder Kennwörter zu ändern, müssen Sie diese zusätzlichen Schritte mit PowerShell ausführen, andernfalls können Sie mit dem nächsten Abschnitt fortfahren.

Installieren Sie zunächst das [Azure AD PowerShell v1-Modul (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0), wenn es noch nicht installiert ist:

```powershell
Install-Module MSOnline
```

Nachdem Sie das PowerShell-Modul installiert haben, stellen Sie eine Verbindung mit Ihrem Azure AD B2C-Mandanten her.

> [!IMPORTANT]
> Sie müssen ein B2C-Mandantenadministratorkonto verwenden, das für den B2C-Mandanten **lokal** ist. Diese Konten sehen wie folgt aus: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Nun verwenden Sie im Skript unten die **Anwendungs-ID**, um der Anwendung die Benutzerrolle des Kontoadministrators zuzuweisen. Diese Rollen haben allgemein bekannte Bezeichner, daher müssen Sie lediglich in das Skript unten Ihre **Anwendungs-ID** einfügen.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Die Anwendung verfügt jetzt außerdem über die Berechtigungen zum Löschen von Benutzern und zum Aktualisieren von Kennwörtern in Ihrem B2C-Mandanten.

## <a name="download-configure-and-build-the-sample-code"></a>Herunterladen, Konfigurieren und Erstellen des Beispielcodes
Laden Sie zunächst den Beispielcode herunter, und bereiten Sie ihn für die Ausführung vor. Dann sehen wir ihn uns genauer an.  Sie können den [Beispielcode als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Sie können ihn auch in einem Verzeichnis Ihrer Wahl klonen:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Öffnen Sie die Visual Studio-Projektmappe `B2CGraphClient\B2CGraphClient.sln` in Visual Studio. Öffnen Sie im Projekt `B2CGraphClient` die Datei "`App.config`". Ersetzen Sie die drei App-Einstellungen durch Ihre eigenen Werte:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Klicken Sie anschließend mit der rechten Maustaste auf die Projektmappe `B2CGraphClient` , und erstellen Sie das Beispiel neu. War der Vorgang erfolgreich, sollte sich nun die ausführbare Datei `B2C.exe` unter `B2CGraphClient\bin\Debug` befinden.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Erstellen von CRUD-Benutzervorgängen mithilfe der Graph-API
Öffnen Sie zum Verwenden von B2CGraphClient eine Windows-Befehlseingabeaufforderung (`cmd`), und wechseln Sie in das Verzeichnis `Debug`. Führen Sie anschließend den Befehl `B2C Help` aus.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Für jeden Befehl wird eine kurze Beschreibung angezeigt. Bei jedem Aufruf von einem dieser Befehle sendet `B2CGraphClient` eine Anforderung an die Azure AD Graph-API.

### <a name="get-an-access-token"></a>Abrufen eines Zugriffstokens
Alle Anforderungen an die Graph-API erfordern ein Zugriffstoken für die Authentifizierung. `B2CGraphClient` verwendet die Open Source Active Directory Authentication Library (ADAL) für die Beschaffung von Zugriffstoken. ADAL erleichtert die Tokenbeschaffung, indem eine einfache API bereitgestellt wird und einige wichtige Details abgedeckt werden, z. B. das Zwischenspeichern der Zugriffstoken. Sie müssen ADAL jedoch nicht zum Abrufen von Token verwenden. Sie können Token auch abrufen, indem Sie HTTP-Anforderungen erstellen.

> [!NOTE]
> In diesem Codebeispiel wird ADAL v2 verwendet, um mit der Graph-API zu kommunizieren.  Sie müssen ADAL v2 oder v3 nutzen, um Zugriffstoken zu erhalten, die mit der Azure AD Graph-API verwendet werden können.
> 
> 

Bei der Ausführung von `B2CGraphClient` wird eine Instanz der `B2CGraphClient`-Klasse erstellt. Mit dem Konstruktor für diese Klasse wird das Authentifizierungsgerüst für ADAL eingerichtet:

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Als Beispiel verwenden wir den Befehl `B2C Get-User` . Wenn `B2C Get-User` ohne zusätzliche Eingaben aufgerufen wird, ruft die Befehlszeilenschnittstelle die `B2CGraphClient.GetAllUsers(...)`-Methode auf. Diese Methode ruft `B2CGraphClient.SendGraphGetRequest(...)`auf, wodurch eine HTTP GET-Anforderung an die Graph-API gesendet wird. Bevor `B2CGraphClient.SendGraphGetRequest(...)` die GET-Anforderung sendet, wird per ADAL zuerst ein Zugriffstoken abgerufen:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Sie können ein Zugriffstoken für die Graph-API abrufen, indem Sie die `AuthenticationContext.AcquireToken(...)` -Methode von ADAL aufrufen. ADAL gibt anschließend ein `access_token` zurück, das die Identität der Anwendung darstellt.

### <a name="read-users"></a>Lesen von Benutzern
Wenn Sie eine Liste mit Benutzern oder einen bestimmten Benutzer aus der Graph-API abrufen möchten, können Sie eine HTTP `GET`-Anforderung an den Endpunkt `/users` senden. Eine Anforderung für alle Benutzer in einem Mandanten sieht folgendermaßen aus:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Um diese Anforderung anzuzeigen, führen Sie Folgendes aus:

 ```cmd
 B2C Get-User
 ```

Hierbei sind zwei wichtige Punkte zu beachten:

* Das über ADAL abgerufene Zugriffstoken wird dem Header `Authorization` über das Schema `Bearer` hinzugefügt.
* Für B2C-Mandanten müssen Sie den Abfrageparameter `api-version=1.6` verwenden.

Diese Details werden beide in der `B2CGraphClient.SendGraphGetRequest(...)` -Methode behandelt:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Erstellen von Benutzerkonten für Consumer
Beim Erstellen von Benutzerkonten in Ihrem B2C-Mandanten können Sie eine HTTP `POST`-Anforderung an den Endpunkt `/users` senden:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Die meisten Eigenschaften dieser Anforderung müssen Consumerbenutzer erstellen. Klicken Sie [hier](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser), um weitere Informationen zu erhalten. Beachten Sie, dass `//` -Kommentare zur Veranschaulichung eingefügt wurden. Fügen Sie sie nicht in einer tatsächlichen Anforderung ein.

Führen Sie zum Anzeigen der Anforderung einen der folgenden Befehle aus:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

Der Befehl `Create-User` verwendet eine JSON-Datei als Eingabeparameter. Diese Datei enthält eine JSON-Darstellung eines Benutzerobjekts. Es gibt zwei JSON-Beispieldateien im Beispielcode: `usertemplate-email.json` und `usertemplate-username.json`. Sie können diese Dateien Ihren Anforderungen entsprechend ändern. Zusätzlich zu den obigen erforderlichen Feldern enthalten diese Dateien auch verschiedene optionale Felder, die Sie verwenden können. Details zu den optionalen Feldern finden Sie in der [Referenz zu Azure AD Graph-API-Entitäten](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity).

Sie sehen, wie die POST-Anforderung in `B2CGraphClient.SendGraphPostRequest(...)`aufgebaut ist:

* An den Header `Authorization` der Anforderung wird ein Zugriffstoken angefügt.
* `api-version=1.6`wird festgelegt.
* Das JSON-Benutzerobjekt wird in den Hauptteil der Anforderung eingefügt.

> [!NOTE]
> Wenn die Konten, die Sie aus einem vorhandenen Benutzerspeicher migrieren möchten, über eine geringere Kennwortsicherheit als die [hohe Kennwortsicherheit unter Azure AD B2C](/previous-versions/azure/jj943764(v=azure.100)) verfügen, können Sie die Erzwingung sicherer Kennwörter deaktivieren. Verwenden Sie hierfür den Wert `DisableStrongPassword` in der `passwordPolicies`-Eigenschaft. Beispielsweise können Sie die oben angegebene Anforderung zum Erstellen eines Benutzers wie folgt ändern: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Aktualisieren von Benutzerkonten für Consumer
Die Aktualisierung von Benutzerobjekten ähnelt dem Prozess zum Erstellen von Benutzerobjekten. Dieser Prozess verwendet jedoch die HTTP `PATCH` -Methode:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

Versuchen Sie, einen Benutzer zu aktualisieren, indem Sie die JSON-Dateien mit neuen Daten aktualisieren. Anschließend können Sie mithilfe von `B2CGraphClient` einen der folgenden Befehle ausführen:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Überprüfen Sie die `B2CGraphClient.SendGraphPatchRequest(...)` -Methode auf Details zum Senden dieser Anforderung.

### <a name="search-users"></a>Suchen nach Benutzern
Sie haben zwei Möglichkeiten, um in Ihrem B2C-Mandanten nach Benutzern zu suchen. Verwenden Sie entweder die Objekt-ID des Benutzers, oder verwenden Sie den Anmeldebezeichner des Benutzers (die `signInNames` -Eigenschaft).

Führen Sie einen der folgenden Befehle aus, um nach einem bestimmten Benutzer zu suchen:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Hier sind einige Beispiele angegeben:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Löschen von Benutzern
Der Vorgang zum Löschen eines Benutzers ist ganz einfach. Erstellen Sie mithilfe der HTTP `DELETE` -Methode die URL mit der richtigen Objekt-ID:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Verwenden Sie zum Anzeigen eines Beispiels den folgenden Befehl, und sehen Sie sich die Löschanforderung an, die in der Konsole ausgegeben wird:

```cmd
B2C Delete-User <object-id-of-user>
```

Überprüfen Sie die `B2CGraphClient.SendGraphDeleteRequest(...)` -Methode auf Details zum Senden dieser Anforderung.

Sie können neben der Benutzerverwaltung zahlreiche weitere Aktionen mit der Azure AD Graph-API ausführen. Die [Referenz zur Azure AD Graph-API](/previous-versions/azure/ad/graph/api/api-catalog) enthält Details zu jeder Aktion und Beispielanforderungen.

## <a name="use-custom-attributes"></a>Verwenden von benutzerdefinierten Attributen
In den meisten Consumeranwendungen müssen bestimmte benutzerdefinierte Informationen zu einem Benutzerprofil gespeichert werden. Eine Möglichkeit dazu besteht im Definieren eines benutzerdefinierten Attributs in Ihrem B2C-Mandanten. Sie können dann dieses Attribut wie jede andere Eigenschaft eines Benutzerobjekts behandeln. Sie können das Attribut aktualisieren und löschen, nach dem Attribut abfragen, es in Anmeldetoken als Anspruch senden usw.

Informationen zum Definieren eines benutzerdefinierten Attributs in Ihrem B2C-Mandanten finden Sie unter [Azure Active Directory B2C-Vorschau: Verwenden benutzerdefinierter Attribute zum Erfassen von Informationen über Ihre Kunden](active-directory-b2c-reference-custom-attr.md).

Sie können die benutzerdefinierten Attribute, die in Ihrem B2C-Mandanten definiert sind, mit `B2CGraphClient`anzeigen:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Die Ausgaben dieser Funktionen umfassen die Details der einzelnen benutzerdefinierten Attribute, z. B.:

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Sie können den vollständigen Namen, z.B. `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, als Eigenschaft in Ihren Benutzerobjekten verwenden.  Aktualisieren Sie die JSON-Datei mit der neuen Eigenschaft und einem Wert für die Eigenschaft, und führen Sie Folgendes aus:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Mit `B2CGraphClient`verfügen Sie über eine Dienstanwendung, mit der Ihre B2C-Mandantenbenutzer programmgesteuert verwaltet werden können. `B2CGraphClient` nutzt die eigene Anwendungsidentität für die Authentifizierung gegenüber der Azure AD Graph-API. Darüber hinaus werden Token mithilfe eines geheimen Clientschlüssels abgerufen. Beachten Sie beim Einbinden dieser Funktionalität in Ihre Anwendung einige wichtige Punkte für B2C-Apps:

* Sie müssen der Anwendung im Mandanten die richtigen Berechtigungen gewähren.
* Derzeit müssen Sie zum Abrufen von Zugriffstoken ADAL (nicht MSAL) verwenden. (Sie können Protokollnachrichten auch direkt ohne Verwendung einer Bibliothek senden.)
* Verwenden Sie beim Aufrufen der Graph-API `api-version=1.6`.
* Beim Erstellen und Aktualisieren von Consumerbenutzern sind wie oben beschrieben einige Eigenschaften erforderlich.

Wenn Sie Fragen haben oder Aktionen anfordern möchten, die Sie mit der Graph-API in Ihrem B2C-Mandanten durchführen möchten, hinterlassen Sie einen Kommentar zu diesem Artikel, oder legen Sie im GitHub-Repository des Codebeispiels einen entsprechenden Eintrag an.

