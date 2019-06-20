---
title: Ansätze für die Benutzermigration in Azure Active Directory B2C | Microsoft-Dokumentation
description: Hier werden grundlegende und erweiterte Konzepte der Benutzermigration mithilfe der Graph-API (und optional mithilfe benutzerdefinierter Azure AD B2C-Richtlinien) erläutert.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1e913b02f99095afb7ee1a3f2122e3c1fe1a60b5
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507664"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Benutzermigration
Beim Migrieren Ihres Identitätsanbieters zu Azure Active Directory B2C (Azure AD B2C) müssen Sie unter Umständen auch das Benutzerkonto migrieren. In diesem Artikel wird erläutert, wie vorhandene Benutzerkonten von einem beliebigen Identitätsanbieter zu Azure AD B2C migriert werden. Der Artikel enthält keine verbindliche Anleitung, sondern beschreibt lediglich einige mögliche Szenarien. Der Entwickler ist jeweils dafür verantwortlich, dass ein Ansatz geeignet ist.

## <a name="user-migration-flows"></a>Benutzermigrationsmethoden
Mit Azure AD B2C können Sie Benutzer über die [Azure AD Graph-API][B2C-GraphQuickStart] migrieren. Für den Benutzermigrationsprozess stehen zwei Methoden zur Verfügung:

- **Prämigration**: Diese Methode trifft zu, wenn Sie entweder eindeutigen Zugriff auf die Anmeldeinformationen eines Benutzers haben (Benutzername und Kennwort) oder wenn die Anmeldeinformationen verschlüsselt sind und von Ihnen entschlüsselt werden können. Der Prozess der Prämigration umfasst das Lesen der Benutzer im alten Identitätsanbieter und das Erstellen neuer Konten im Azure AD B2C-Verzeichnis.

- **Prämigration und Kennwortzurücksetzung**: Diese Methode ist geeignet, wenn auf das Kennwort eines Benutzers nicht zugegriffen werden kann. Beispiel:
   - Das Kennwort wird im HASH-Format gespeichert.
   - Das Kennwort wird in einem Identitätsanbieter gespeichert, auf den Sie nicht zugreifen können. Der alte Identitätsanbieter überprüft die Benutzeranmeldeinformationen durch das Aufrufen eines Webdiensts.

Bei beiden Methoden führen Sie zunächst den Prozess für die Prämigration aus: Sie lesen die Benutzer im alten Identitätsanbieter und erstellen neue Konten im Azure AD B2C-Verzeichnis. Wenn Sie nicht über das Kennwort verfügen, erstellen Sie das Konto mit einem zufällig generierten Kennwort. Anschließend fordern Sie den Benutzer auf, das Kennwort zu ändern. Wenn sich der Benutzer zum ersten Mal anmeldet, wird der Benutzer von Azure AD B2C zum Zurücksetzen aufgefordert.

## <a name="password-policy"></a>Kennwortrichtlinie
Die Kennwortrichtlinie von Azure AD B2C (für lokale Konten) basiert auf der Azure AD-Kennwortrichtlinie. Die Azure AD B2C-Richtlinien zur Registrierung, Anmeldung und Kennwortzurücksetzung verwenden sichere Kennwörter, und die Kennwörter laufen nicht ab. Weitere Informationen finden Sie unter [Kennwortrichtlinie in Azure AD][AD-PasswordPolicies].

Wenn die Konten, die Sie migrieren möchten, über eine geringere Kennwortsicherheit als die [hohe Kennwortsicherheit unter Azure AD B2C][AD-PasswordPolicies] verfügen, können Sie die Erzwingung sicherer Kennwörter deaktivieren. Legen Sie zum Ändern der standardmäßigen Kennwortrichtlinie die Eigenschaft `passwordPolicies` auf `DisableStrongPassword` fest. Beispielsweise können Sie die Anforderung zum Erstellen eines Benutzers wie folgt ändern:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Schritt 1: Migrieren von Benutzern mithilfe der Azure AD Graph-API
Sie erstellen das Azure AD B2C-Benutzerkonto mithilfe der Graph-API (mit dem bekannten oder einem zufällig generierten Kennwort). In diesem Abschnitt wird die Vorgehensweise zum Erstellen von Benutzerkonten im Azure AD B2C-Verzeichnis mit der Graph-API beschrieben.

### <a name="step-11-register-your-application-in-your-tenant"></a>Schritt 1.1: Registrieren Ihrer Anwendung in Ihrem Mandanten
Um mit der Graph-API zu kommunizieren, benötigen Sie zuerst ein Dienstkonto mit Administratorberechtigungen. In Azure AD registrieren Sie eine Anwendung und eine Authentifizierung für Azure AD. Die Anwendungsanmeldeinformationen bestehen aus **Anwendungs-ID** und **Anwendungsgeheimnis**. Die Anwendung verhält sich beim Aufrufen der Graph-API wie sie selbst und nicht wie ein Benutzer.

Registrieren Sie zuerst Ihre Migrationsanwendung bei Azure AD. Erstellen Sie anschließend einen Anwendungsschlüssel (Anwendungsgeheimnis), und statten Sie die Anwendung mit Schreibberechtigungen aus.

1. Melden Sie sich beim [Azure-Portal][Portal] an.
   
1. Wählen Sie Ihren Azure AD **B2C**-Mandanten aus, indem Sie oben rechts im Fenster Ihr Konto auswählen.
   
1. Klicken Sie im linken Bereich auf **Azure Active Directory** (nicht auf Azure AD B2C). Unter Umständen müssen Sie **Weitere Dienste** auswählen, um die Option zu finden.
   
1. Wählen Sie **App-Registrierungen** aus.
   
1. Wählen Sie **Registrierung einer neuen Anwendung** aus.
   
   ![Registrierung einer neuen Anwendung](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
   
1. Erstellen Sie eine neue Anwendung, indem Sie wie folgt vorgehen:
   - Verwenden Sie für **Name** entweder **B2CUserMigration** oder einen anderen beliebigen Namen.
   - Verwenden Sie für **Anwendungstyp** die Option **Web-App/API**.
   - Verwenden Sie als **Anmelde-URL** die URL `https://localhost` (für diese Anwendung nicht relevant).
   - Klicken Sie auf **Erstellen**.
   
1. Wählen Sie nach der Erstellung der Anwendung in der Liste **Anwendungen** die neu erstellte Anwendung **B2CUserMigration**.
   
1. Klicken Sie auf **Eigenschaften**, kopieren Sie den Wert aus **Anwendungs-ID**, und speichern Sie ihn zur späteren Verwendung.

### <a name="step-12-create-the-application-secret"></a>Schritt 1.2: Erstellen des Anwendungsgeheimnisses
1. Wählen Sie im Azure-Portal im Fenster **Registrierte App** die Option **Schlüssel**.
   
1. Fügen Sie einen neuen Schlüssel hinzu (auch als geheimer Clientschlüssel bezeichnet), und kopieren Sie den Schlüssel dann zur späteren Verwendung.
   
   ![Anwendungs-ID und Schlüssel](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)
   
### <a name="step-13-grant-administrative-permission-to-your-application"></a>Schritt 1.3: Gewähren von Administratorberechtigungen für die Anwendung
1. Wählen Sie im Azure-Portal im Fenster **Registrierte App** die Option **Erforderliche Berechtigungen**.

1. Wählen Sie die Option **Windows Azure Active Directory**.
   
1. Wählen Sie im Bereich **Zugriff aktivieren** unter **Anwendungsberechtigungen** die Option **Lese- und Schreibzugriff auf Verzeichnisdaten** und anschließend **Speichern**.
   
1. Wählen Sie im Bereich **Erforderliche Berechtigungen** die Option **Berechtigungen erteilen**.
   
   ![Anwendungsberechtigungen](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)
   
Sie verfügen jetzt über eine Anwendung mit Berechtigungen zum Erstellen, Lesen und Aktualisieren von Benutzern über Ihren Azure AD B2C-Mandanten.

### <a name="step-14-optional-environment-cleanup"></a>Schritt 1.4: Bereinigen der Umgebung (optional)
Die Berechtigung „Lese- und Schreibzugriff auf Verzeichnisdaten“ schließt *nicht* die Berechtigung zum Löschen von Benutzern ein. Damit Ihre Anwendung die Möglichkeit zum Löschen von Benutzern erhält (zum Bereinigen Ihrer Umgebung), müssen Sie einen zusätzlichen Schritt ausführen. Dies umfasst auch die Ausführung von PowerShell zum Festlegen von Benutzerkonto-Administratorberechtigungen. Andernfalls können Sie mit dem nächsten Abschnitt fortfahren.

> [!IMPORTANT]
> Sie müssen ein B2C-Mandantenadministratorkonto verwenden, das für den B2C-Mandanten *lokal* angeordnet ist. Die Syntax für den Kontonamen lautet: *admin\@contosob2c.onmicrosoft.com*.

>[!NOTE]
> Für das folgende PowerShell-Skript ist [Azure Active Directory PowerShell Version 2][AD-Powershell] erforderlich.

Gehen Sie in diesem PowerShell-Skript wie folgt vor:
1. Sie stellen eine Verbindung mit dem Onlinedienst her. Führen Sie hierzu an der Windows PowerShell-Eingabeaufforderung das `Connect-AzureAD`-Cmdlet aus, und geben Sie Ihre Anmeldeinformationen an.
   
1. Weisen Sie mithilfe der **Anwendungs-ID** der Anwendung die Rolle „Benutzerkontoadministrator“ zu. Da diese Rollen allgemein bekannte Bezeichner haben, müssen Sie lediglich Ihre **Anwendungs-ID** in das Skript eingeben.
   
```powershell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Ersetzen Sie den Wert `$AppId` durch Ihre **Anwendungs-ID** in Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Schritt 2: Anwendungsbeispiel für die Prämigration
[Laden Sie den Beispielcode herunter, und führen Sie ihn aus][UserMigrationSample]. Sie können den Download in Form einer ZIP-Datei durchführen.

### <a name="step-21-edit-the-migration-data-file"></a>Schritt 2.1: Bearbeiten der Migrationsdatendatei
Die Beispiel-App verwendet eine JSON-Datei mit Dummybenutzerdaten. Nach der erfolgreichen Ausführung des Beispiels können Sie den Code ändern, um die Daten aus Ihrer eigenen Datenbank zu nutzen. Oder Sie können das Benutzerprofil in eine JSON-Datei exportieren und die App dann so festlegen, dass diese Datei verwendet wird.

Öffnen Sie zum Bearbeiten der JSON-Datei die Visual Studio-Projektmappe `AADB2C.UserMigration.sln`. Öffnen Sie im Projekt `AADB2C.UserMigration` die Datei `UsersData.json`.

![Benutzerdatendatei](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Wie Sie sehen, enthält die Datei eine Liste mit den Benutzerentitäten. Jede Benutzerentität weist die folgenden Eigenschaften auf:
- email
- displayName
- firstName
- lastName
- password (kann leer sein)

> [!NOTE]
> Zur Kompilierzeit kopiert Visual Studio die Datei in das Verzeichnis `bin`.

### <a name="step-22-configure-the-application-settings"></a>Schritt 2.2: Konfigurieren der Anwendungseinstellungen
Öffnen Sie unter dem Projekt `AADB2C.UserMigration` die Datei *App.config*. Ersetzen Sie die folgenden App-Einstellungen durch Ihre eigenen Werte:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - Die Verwendung der Azure-Tabellenverbindungszeichenfolge wird in den nächsten Abschnitten beschrieben.
> - Der Name des B2C-Mandanten ist die Domäne, die Sie bei der Erstellung des Mandanten eingegeben haben, und wird im Azure-Portal angezeigt. Der Mandantenname endet normalerweise auf das Suffix *.onmicrosoft.com* (z.B. *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Schritt 2.3: Ausführen des Prämigrationsprozesses
Klicken Sie mit der rechten Maustaste auf die Projektmappe `AADB2C.UserMigration`, und erstellen Sie das Beispiel dann neu. War der Vorgang erfolgreich, sollte sich nun die ausführbare Datei `UserMigration.exe` unter `AADB2C.UserMigration\bin\Debug\net461` befinden. Verwenden Sie zum Ausführen des Migrationsprozesses einen der folgenden Befehlszeilenparameter:

- Um **Benutzer mit Kennwort zu migrieren**, verwenden Sie den Befehl `UserMigration.exe 1`.

- Um **Benutzer mit zufälligem Kennwort zu migrieren**, verwenden Sie den Befehl `UserMigration.exe 2`. Bei diesem Vorgang wird zudem eine Azure-Tabellenentität erstellt. Später konfigurieren Sie die Richtlinie zum Aufrufen des REST-API-Diensts. Der Dienst nutzt eine Azure-Tabelle zum Nachverfolgen und Verwalten des Migrationsprozesses.

![Demo für den Migrationsprozess](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Schritt 2.4: Überprüfen des Prämigrationsprozesses
Verwenden Sie zum Überprüfen der Migration eine der beiden folgenden Methoden:

- Verwenden Sie das Azure-Portal, um anhand des Anzeigenamens nach einem Benutzer zu suchen:
   
   1. Öffnen Sie **Azure AD B2C**, und wählen Sie die Option **Benutzer und Gruppen**.
   
   1. Geben Sie in das Suchfeld den Anzeigenamen des Benutzers ein, und zeigen Sie dann das Profil des Benutzers an.
   
- Verwenden Sie diese Beispielanwendung, um einen Benutzer anhand der E-Mail-Adresse für die Anmeldung abzurufen:
   
   1. Führen Sie den folgenden Befehl aus:
   
      ```Console
          UserMigration.exe 3 {email address}
      ```
      
      > [!TIP]
      > Sie können einen Benutzer anhand des Anzeigenamens abrufen, indem Sie den folgenden Befehl verwenden: `UserMigration.exe 4 "<Display name>"`.
      
   1. Öffnen Sie die Datei „UserProfile.json“ in einem JSON-Editor, um die Informationen des Benutzers anzuzeigen.
   
      ![Datei „UserProfile.json“](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)
      
### <a name="step-25-optional-environment-cleanup"></a>Schritt 2.5: Bereinigen der Umgebung (optional)
Wenn Sie den Azure AD-Mandanten bereinigen und die Benutzer aus dem Azure AD-Verzeichnis entfernen möchten, führen Sie den Befehl `UserMigration.exe 5` aus.

> [!NOTE]
> * Konfigurieren Sie zum Bereinigen des Mandanten Benutzerkontoadministrator-Berechtigungen für Ihre Anwendung.
> * Die Beispielmigrations-App bereinigt alle in der JSON-Datei aufgelisteten Benutzer.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Schritt 2.6: Anmelden mit migrierten Benutzern (mit Kennwort)
Nach dem Ausführen des Prämigrationsprozesses mit Benutzerkennwörtern können die Konten verwendet werden, und Benutzer können sich mit Azure AD B2C an Ihrer Anwendung anmelden. Wenn Sie keinen Zugriff auf die Benutzerkennwörter haben, können Sie mit dem nächsten Abschnitt fortfahren.

## <a name="step-3-help-users-reset-their-password"></a>Schritt 3: Unterstützen von Benutzern beim Zurücksetzen ihrer Kennwörter
Wenn Sie Benutzer mit einem zufällig generierten Kennwort migrieren, müssen diese ihr Kennwort zurücksetzen. Senden Sie ihnen als Hilfe zum Zurücksetzen des Kennworts eine Begrüßungs-E-Mail mit einem Link, über den das Kennwort zurückgesetzt werden kann.

Gehen Sie wie folgt vor, um den Link zu Ihrer Richtlinie für die Kennwortzurücksetzung zu erhalten:

1. Wählen Sie **Azure AD-B2C-Einstellungen** und dann die Richtlinieneigenschaften für **Kennwort zurücksetzen**.

1. Wählen Sie Ihre Anwendung aus.

    > [!NOTE]
    > Für „Jetzt ausführen“ muss mindestens eine Anwendung vorab im Mandanten registriert werden. Informationen zum Registrieren von Anwendungen finden Sie in den Artikeln [Erste Schritte][B2C-GetStarted] und [Anwendungsregistrierung][B2C-AppRegister] zu Azure AD B2C.

1. Wählen Sie **Jetzt ausführen**, und überprüfen Sie die Richtlinie.

1. Kopieren Sie im Feld **Endpunkt für sofortige Ausführung** die URL, und senden Sie sie anschließend an Ihre Benutzer.

    ![Festlegen von Diagnoseprotokollen](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Schritt 4: Ändern der Richtlinie zum Überprüfen und Festlegen des Benutzermigrationsstatus (optional)

> [!NOTE]
> Sie müssen eine benutzerdefinierte Richtlinie verwenden, um den Benutzermigrationsstatus zu überprüfen und zu ändern. Dazu müssen die unter [Erste Schritte mit benutzerdefinierten Richtlinien][B2C-GetStartedCustom] aufgeführten Setupanweisungen befolgt werden.
>

Wenn sich Benutzer anmelden, ohne zuerst das Kennwort zu ändern, sollte von der Richtlinie eine Fehlermeldung angezeigt werden. Beispiel:
>*Ihr Kennwort ist abgelaufen. Wählen Sie den Link „Kennwort zurücksetzen“, um es zurückzusetzen.*

Für diesen optionalen Schritt ist die Verwendung von benutzerdefinierten Azure AD B2C-Richtlinien erforderlich. Informationen hierzu finden Sie im Artikel [Azure Active Directory B2C: Erste Schritte mit benutzerdefinierten Richtlinien][B2C-GetStartedCustom].

In diesem Abschnitt ändern Sie die Richtlinie, um den Benutzermigrationsstatus bei der Anmeldung zu überprüfen. Wenn der Benutzer das Kennwort nicht geändert hat, sollte eine HTTP 409-Fehlermeldung mit der Aufforderung zurückgegeben werden, dass der Benutzer den Link **Kennwort vergessen?** wählen soll.

Verwenden Sie eine Azure-Tabelle, um die Kennwortänderung nachzuverfolgen. Wenn Sie den Prozess für die Prämigration mit dem Befehlszeilenparameter `2` ausführen, erstellen Sie eine Benutzerentität in einer Azure-Tabelle. Ihr Dienst führt Folgendes durch:

- Bei der Anmeldung ruft die Azure AD B2C-Richtlinie den RESTful-Migrationsdienst auf und sendet eine E-Mail als Eingabeanspruch. Der Dienst sucht in der Azure-Tabelle nach der E-Mail-Adresse. Wenn die Adresse vorhanden ist, löst der Dienst eine Fehlermeldung aus: *Ihr Kennwort muss geändert werden*.

- Nachdem der Benutzer das Kennwort geändert hat, wird die Entität aus der Azure-Tabelle entfernt.

>[!NOTE]
>Zur Vereinfachung des Beispiels wird eine Azure-Tabelle verwendet. Sie können den Migrationsstatus in einer beliebigen Datenbank oder als benutzerdefinierte Eigenschaft im Azure AD B2C-Konto speichern.

### <a name="41-update-your-application-setting"></a>4.1: Aktualisieren Ihrer Anwendungseinstellung
1. Öffnen Sie zum Testen des RESTful-API-Demos die Datei `AADB2C.UserMigration.sln` in Visual Studio.

1. Öffnen Sie im Projekt `AADB2C.UserMigration.API` die Datei *Web.config*. Ersetzen Sie die Einstellung durch die in [Schritt 2.2](#step-22-configure-the-application-settings) konfigurierte Einstellung:

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Schritt 4.2: Bereitstellen der Webanwendung in Azure App Service
Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `AADB2C.UserMigration.API`, und wählen Sie „Veröffentlichen“ aus. Befolgen Sie die Anweisungen für die Veröffentlichung in Azure App Service. Weitere Informationen finden Sie unter [Bereitstellen der App in Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Schritt 4.3: Hinzufügen eines technischen Profils und der Überprüfung technischer Profile zur Richtlinie
1. Erweitern Sie „Projektmappenelemente“ im Projektmappen-Explorer, und öffnen Sie die Richtliniendatei *TrustFrameworkExtensions.xml*.
1. Ändern Sie `yourtenant.onmicrosoft.com` in den Feldern `TenantId`, `PublicPolicyUri` und `<TenantId>` in den Namen Ihres Mandanten.
1. Ersetzen Sie unter dem Element `<TechnicalProfile Id="login-NonInteractive">` alle Instanzen von `ProxyIdentityExperienceFrameworkAppId` und `IdentityExperienceFrameworkAppId` durch die in [Erste Schritte mit benutzerdefinierten Richtlinien][B2C-GetStartedCustom] konfigurierten Anwendungs-IDs.
1. Suchen Sie den folgenden XML-Codeausschnitt unter dem Knoten `<ClaimsProviders>`. Ändern Sie den Wert von `ServiceUrl` so, dass er auf Ihre Azure App Service-URL verweist.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Mit dem obigen technischen Profil wird ein Eingabeanspruch definiert: `signInName` (als E-Mail senden). Bei der Anmeldung wird der Anspruch an Ihren RESTful-Endpunkt gesendet.

Definieren Sie das technische Profil für die RESTful-API, und konfigurieren Sie anschließend die Azure AD B2C-Richtlinie für das Aufrufen des technischen Profils. Der XML-Codeausschnitt setzt das in der Basisrichtlinie definierte Element `SelfAsserted-LocalAccountSignin-Email` außer Kraft. Darüber hinaus fügt der XML-Codeausschnitt `ValidationTechnicalProfile` mit einer Referenz-ID hinzu, die auf das technische Profil `LocalAccountUserMigration` verweist.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Schritt 4.4: Hochladen der Richtlinie in Ihren Mandanten
1. Wechseln Sie im [Azure-Portal][Portal] zum [Kontext Ihres Azure AD B2C-Mandanten][B2C-NavContext], und wählen Sie anschließend **Azure AD B2C** aus.

1. Wählen Sie **Framework für die Identitätsfunktion** aus.

1. Wählen Sie die Option **Alle Richtlinien** aus.

1. Wählen Sie **Richtlinie hochladen** aus.

1. Aktivieren Sie das Kontrollkästchen **Richtlinie überschreiben, sofern vorhanden**.

1. Laden Sie die Datei *TrustFrameworkExtensions.xml* hoch, und stellen Sie sicher, dass sie die Überprüfung besteht.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Schritt 4.5: Testen der benutzerdefinierten Richtlinie mit „Jetzt ausführen“
1. Wählen Sie **Azure AD B2C-Einstellungen** aus, und navigieren Sie dann zu **Framework für die Identitätsfunktion**.

1. Öffnen Sie **B2C_1A_signup_signin**. Dies ist die benutzerdefinierte Richtlinie der vertrauenden Seite (Relying Party, RP), die Sie hochgeladen haben. Wählen Sie anschließend **Jetzt ausführen**.

1. Versuchen Sie, sich mit den Anmeldeinformationen eines migrierten Benutzers anzumelden, und wählen Sie anschließend **Anmelden**. Ihre REST-API sollte die folgende Fehlermeldung auslösen:

    ![Festlegen von Diagnoseprotokollen](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Schritt 4.6: Behandeln von Problemen mit der REST-API (optional)
Sie können Protokollierungsinformationen nahezu in Echtzeit anzeigen und überwachen.

1. Wählen Sie im Menü mit den Einstellungen für Ihre RESTful-Anwendung unter **Überwachung** die Option **Diagnoseprotokolle**.

1. Legen Sie **Anwendungsprotokollierung (Dateisystem)** auf **Ein** fest.

1. Legen Sie die **Ebene** auf **Ausführlich** fest.

1. Wählen Sie **Speichern** aus.

    ![Festlegen von Diagnoseprotokollen](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. Wählen Sie im Menü **Einstellungen** die Option **Protokollstream**.

1. Überprüfen Sie die Ausgabe der RESTful-API.

> [!IMPORTANT]
> Verwenden Sie die Diagnoseprotokolle nur während der Entwicklung und bei Tests. Die RESTful-API-Ausgabe enthält unter Umständen vertrauliche Informationen, die während der Produktion nicht verfügbar gemacht werden dürfen.
>

## <a name="optional-download-the-complete-policy-files"></a>(Optional:) Herunterladen der vollständigen Richtliniendateien
Nachdem Sie die exemplarische Vorgehensweise unter [Erste Schritte mit benutzerdefinierten Richtlinien][B2C-GetStartedCustom] abgeschlossen haben, empfiehlt es sich, ein Szenario mit Ihren eigenen benutzerdefinierten Richtliniendateien zu erstellen. Zu Referenzzwecken haben wir [Beispiele für Richtliniendateien][UserMigrationSample] bereitgestellt.

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/yoelhor/Azure-AD-B2C-UserMigration
