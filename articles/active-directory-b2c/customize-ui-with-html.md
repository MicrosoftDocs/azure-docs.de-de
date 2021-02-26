---
title: Anpassen der Benutzeroberfläche mit HTML-Vorlagen
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie mit HTML-Vorlagen die Benutzeroberfläche für Ihre Anwendungen anpassen, die Azure Active Directory B2C verwenden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 78ad2540029d78084485ae2004194f9f7c2d6052
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050542"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>Anpassen der Benutzeroberfläche mit HTML-Vorlagen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Das Branding und Anpassen der Benutzeroberfläche, die Azure Active Directory B2C (Azure AD B2C) für Ihre Kunden anzeigt, trägt zu einer nahtlosen Benutzererfahrung in Ihrer Anwendung bei. Diese Benutzererfahrung umfasst Registrieren, Anmelden, Bearbeiten von Profilen und Zurücksetzen von Kennwörtern. In diesem Artikel werden die Methoden zum Anpassen der Benutzeroberfläche (User Interface, UI) erläutert. 

> [!TIP]
> Wenn Sie nur das Bannerlogo, das Hintergrundbild und die Hintergrundfarbe Ihrer Benutzerflowseiten ändern möchten, können Sie das Feature [Unternehmensbranding](customize-ui.md) ausprobieren.

## <a name="custom-html-and-css-overview"></a>Übersicht zu benutzerdefiniertem HTML und CSS

Azure AD B2C führt mithilfe von [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/) (ursprungsübergreifende Ressourcenfreigabe) den Code im Browser Ihres Kunden aus. Zur Laufzeit wird Inhalt über eine URL geladen, die Sie in Ihrem Benutzerflow oder Ihrer benutzerdefinierten Richtlinie angeben. Der Inhalt jeder Seite in der Benutzererfahrung wird von der URL geladen, die Sie für die jeweilige Seite angeben. Nachdem Inhalt über die URL geladen wurde, wird er mit einem von Azure AD B2C eingefügten HTML-Fragment zusammengeführt, und die Seite wird für Ihren Kunden angezeigt.

![Benutzerdefinierte Seiteninhalte (Rand)](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Benutzerdefinierte HTML-Seiteninhalte

Erstellen Sie eine HTML-Seite mit Ihrem eigenen Branding, um Ihren benutzerdefinierten Seiteninhalt bereitzustellen. Bei dieser Seite kann es sich um eine statische `*.html`-Seite oder eine dynamische Seite wie .NET, Node.js oder PHP handeln.

Ihr benutzerdefinierter Seiteninhalt darf beliebige HTML-Elemente (einschließlich CSS und JavaScript), jedoch keine unsicheren Elemente wie iframes enthalten. Das einzige erforderliche Element ist ein „div“-Element, bei dem `id` auf `api` festgelegt ist, wie z. B. `<div id="api"></div>` auf der HTML-Seite.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Anpassen der Azure AD B2C-Standardseiten

Anstatt benutzerdefinierte Seiteninhalte von Grund auf neu zu erstellen, können Sie die Inhalte von Azure AD B2C-Standardseiten anpassen.

In der folgenden Tabelle sind die von Azure AD B2C bereitgestellten Standardseiteninhalte aufgelistet. Laden Sie die Dateien herunter, und verwenden Sie sie als Ausgangspunkt für das Erstellen eigener benutzerdefinierter Seiten.

| Standardseite | BESCHREIBUNG | ID für Inhaltsdefinition<br/>(nur benutzerdefinierte Richtlinie) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Fehlerseite**: Diese Seite wird angezeigt, wenn eine Ausnahme oder ein Fehler auftreten. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Selbstbestätigte Seite**. Verwenden Sie diese Datei als benutzerdefinierten Seiteninhalt für Seiten zum Anmelden über ein soziales Netzwerk, zum Registrieren eines lokalen Kontos, zum Anmelden mit einem lokalen Konto, zum Zurücksetzen des Kennworts usw. Das Formular kann verschiedene Eingabesteuerelemente enthalten, z.B. ein Texteingabefeld, ein Kennworteingabefeld, ein Optionsfeld, Dropdownfelder mit einer Auswahlmöglichkeit und Kontrollkästchen mit mehreren Optionen. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Seite „Multi-Factor Authentication“** : Auf dieser Seite können Benutzer während der Registrierung oder Anmeldung ihre Telefonnummern verifizieren (per SMS oder Sprachnachricht). | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Seite für Profilaktualisierung**: Diese Seite enthält ein Formular, auf das Benutzer zum Aktualisieren des Profils zugreifen können. Diese Seite ähnelt der Seite für die Registrierung bei einem Konto für ein soziales Netzwerk, mit Ausnahme der Felder für die Kennworteingabe. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Einheitliche Seite für Registrierung oder Anmeldung**: Auf dieser Seite werden der Registrierungs- und der Anmeldevorgang für Benutzer durchgeführt. Benutzer können Unternehmensidentitätsanbieter, Identitätsanbieter in Form von sozialen Netzwerken wie Facebook und Google+ oder lokale Konten verwenden. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hosten des Seiteninhalts

Wenn Sie eigene HTML- und CSS-Dateien zum Anpassen der Benutzeroberfläche verwenden, hosten Sie Ihre Inhalte für die Benutzeroberfläche auf einem beliebigen öffentlich verfügbaren HTTPS-Endpunkt, der CORS unterstützt. Beispiele sind [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), [Azure App Services](../app-service/index.yml), Webserver, CDNs, AWS S3 oder Dateifreigabesysteme.

## <a name="guidelines-for-using-custom-page-content"></a>Richtlinien zum Verwenden benutzerdefinierter Seiteninhalte

- Verwenden Sie eine absolute URL, wenn Sie externe Ressourcen wie Medien-, CSS- und JavaScript-Dateien in Ihre HTML-Datei einbeziehen.
- Mit der [Seitenlayoutversion](page-layout.md) 1.2.0 und höher können Sie in den HTML-Tags das Attribut `data-preload="true"` hinzufügen, um die Ladereihenfolge für CSS und JavaScript zu steuern. Mit `data-preload="true"` wird die Seite erstellt, bevor sie dem Benutzer angezeigt wird. Mit diesem Attribut wird ein „Flackern“ der Seite verhindert, da die CSS-Datei vorab geladen und die unformatierte HTML-Datei dem Benutzer nicht angezeigt wird. Der folgende HTML-Codeausschnitt zeigt die Verwendung des `data-preload`-Tags.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Wir empfehlen, mit dem Standardseiteninhalt zu beginnen und darauf aufzubauen.
- In Ihren benutzerdefinierten Inhalt können Sie [JavaScript einschließen](javascript-and-page-layout.md).
- Unterstützte Browserversionen sind:
  - Internet Explorer 11, 10 und Microsoft Edge
  - Eingeschränkte Unterstützung für Internet Explorer 9 und 8
  - Google Chrome 42.0 und höher
  - Mozilla Firefox 38.0 und höher
  - Safari für iOS und macOS, Version 12 und höher
- Aufgrund von Sicherheitseinschränkungen unterstützt Azure AD B2C keine HTML-Elemente vom Typ `frame`, `iframe` oder `form`.

## <a name="localize-content"></a>Lokalisieren von Inhalt

Sie lokalisieren Ihren HTML-Inhalt, indem Sie die [Sprachanpassung](language-customization.md) in Ihrem Azure AD B2C-Mandanten aktivieren. Wenn Sie dieses Feature aktivieren, kann Azure AD B2C den Open ID Connect-Parameter `ui_locales` an Ihren Endpunkt weiterleiten. Ihr Inhaltsserver kann diesen Parameter verwenden, um benutzerdefinierte, sprachspezifische HTML-Seiten bereitzustellen.

Inhalt kann auf Grundlage des verwendeten Gebietsschemas aus unterschiedlichen Quellen abgerufen werden. Richten Sie in Ihrem CORS-fähigen Endpunkt eine Ordnerstruktur zum Hosten von Inhalten für bestimmte Sprachen ein. Wenn Sie den Platzhalterwert `{Culture:RFC5646}` verwenden, wird der passende Inhalt abgerufen.

Beispielsweise könnte Ihr benutzerdefinierter Seiten-URI wie folgt aussehen:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Sie können die Seite in Französisch laden, indem Sie Inhalt abrufen aus:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Exemplarische Vorgehensweise für benutzerdefinierte Seiteninhalte

Übersicht über den Prozess:

1. Vorbereiten eines Speicherorts zum Hosten Ihrer benutzerdefinierten Seiteninhalte (öffentlich zugänglicher, CORS-fähiger HTTPS-Endpunkt)
1. Herunterladen und Anpassen einer Datei mit Standardseiteninhalt, z. B. `unified.html`
1. Veröffentlichen des benutzerdefinierten Seiteninhalts über den öffentlich verfügbaren HTTPS-Endpunkt.
1. Festlegen der Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS) für Ihre Web-App
1. Verweisen in Ihrer Richtlinie auf den URI für den benutzerdefinierten Richtlinieninhalt

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. Erstellen des HTML-Inhalts

Erstellen Sie benutzerdefinierte Seiteninhalte mit dem Markennamen Ihres Produkts im Titel.

1. Kopieren Sie den folgenden HTML-Codeausschnitt. Hierbei handelt es sich um einen wohlgeformten HTML5-Ausschnitt mit einem leeren Element namens *\<div id="api"\>\</div\>* , das sich in den *\<body\>* -Tags befindet. Dieses Element gibt an, wo Azure AD B2C-Inhalt eingefügt werden soll.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Fügen Sie den kopierten Ausschnitt in einen Text-Editor ein.
1. Formatieren Sie die Elemente der Benutzeroberfläche, die von Azure AD B2C in Ihre Seite einfügt werden, mithilfe von CSS. Das folgende Beispiel zeigt eine einfachen CSS-Datei, die außerdem Einstellungen für die Registrierung eingefügter HTML-Elemente enthält:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  Speichern Sie die Datei als *customize-ui.html*.

> [!NOTE]
> HTML-Formularelemente werden bei der Verwendung von login.microsoftonline.com aufgrund von Sicherheitseinschränkungen entfernt. Nutzen Sie [b2clogin.com](b2clogin.md), wenn Sie HTML-Formularelemente in Ihren benutzerdefinierten HTML-Inhalten verwenden möchten.

### <a name="2-create-an-azure-blob-storage-account"></a>2. Erstellen eines Azure Blob Storage-Kontos

In diesem Artikel wird Azure Blob Storage zum Hosten unserer Inhalte verwendet. Sie können angeben, dass Ihre Inhalte auf einem Webserver gehostet werden sollen, aber Sie müssen [auf Ihrem Webserver CORS aktivieren](https://enable-cors.org/server.html).

Führen Sie die folgenden Schritte aus, um Ihren HTML-Inhalt im Blob-Speicher zu hosten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im Menü **Hub** die Option **Neu** > **Speicher** > **Speicherkonto**.
1. Wählen Sie ein **Abonnement** für Ihr Speicherkonto aus.
1. Erstellen Sie eine **Ressourcengruppe**, oder wählen Sie eine vorhandene Ressourcengruppe aus.
1. Geben Sie einen eindeutigen **Namen** für Ihr Speicherkonto ein.
1. Wählen Sie den **geografischen Standort** für das Speicherkonto aus.
1. Als **Bereitstellungsmodell** können Sie **Resource Manager** unverändert lassen.
1. Für **Leistung** können Sie **Standard** übernehmen.
1. Ändern Sie **Kontoart** in **Blob Storage**.
1. Für **Replikation** können Sie **RA-GRS** übernehmen.
1. Für **Zugriffstarif** können Sie **Heiß** übernehmen.
1. Wählen Sie **Überprüfen + erstellen** aus, um das Speicherkonto zu erstellen.
    Nach Abschluss der Bereitstellung wird die Seite **Speicherkonto** automatisch geöffnet.

#### <a name="21-create-a-container"></a>2.1 Erstellen eines Containers

Führen Sie die folgenden Schritte aus, um im Blob-Speicher einen öffentlichen Container zu erstellen:

1. Wählen Sie unter **Blob-Dienst** im linken Menü die Option **Blobs** aus.
1. Wählen Sie **+ Container** aus.
1. Geben Sie im Feld **Name** die Zeichenfolge *root* ein. Dies kann ein Name Ihrer Wahl sein, z. B. *contoso*, aber in diesem Beispiel verwenden wir der Einfachheit halber *root*.
1. Wählen Sie für **Öffentliche Zugriffsebene** die Option **Blob** aus, und klicken Sie dann auf **OK**.
1. Wählen Sie **root** aus, um den neuen Container zu öffnen.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Hochladen von Dateien mit benutzerdefinierten Seiteninhalten

1. Wählen Sie die Option **Hochladen**.
1. Wählen Sie das Ordnersymbol neben **Datei auswählen** aus.
1. Navigieren Sie zur Datei **customize-ui.html**, die Sie zuvor im Abschnitt „Seite für die Benutzeroberflächenanpassung“ erstellt haben, und wählen Sie die Datei aus.
1. Wenn Sie die Datei in einen Unterordner hochladen möchten, erweitern Sie die Option **Erweitert**, und geben Sie im Feld **In Ordner hochladen** einen Ordnernamen ein.
1. Wählen Sie die Option **Hochladen**.
1. Wählen Sie das Blob **customize-ui.html** aus, das Sie hochgeladen haben.
1. Wählen Sie rechts neben dem Textfeld **URL** das Symbol **In Zwischenablage kopieren** aus, um die URL in die Zwischenablage zu kopieren.
1. Navigieren Sie im Webbrowser zu der kopierten URL, um zu überprüfen, ob auf das hochgeladene Blob zugegriffen werden kann. Wenn ein Zugriff darauf nicht möglich ist und beispielsweise ein Fehler vom Typ `ResourceNotFound` auftritt, stellen Sie sicher, dass der Zugriffstyp für den Container auf **Blob** festgelegt ist.

### <a name="3-configure-cors"></a>3. Konfigurieren von CORS

Führen Sie die folgenden Schritte aus, um den Blob-Speicher für die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) zu konfigurieren:

1. Wählen Sie im Menü **CORS** aus.
1. Geben Sie für **Zulässige Ursprünge** den Wert `https://your-tenant-name.b2clogin.com` ein. Ersetzen Sie `your-tenant-name` durch den Namen des Azure AD B2C-Mandanten. Beispiel: `https://fabrikam.b2clogin.com`. Verwenden Sie bei der Eingabe Ihres Mandantennamens ausschließlich Kleinbuchstaben.
1. Wählen Sie für **Zulässige Methoden** sowohl `GET` als auch `OPTIONS` aus.
1. Geben Sie für **Zulässige Header** ein Sternchen (*) ein.
1. Geben Sie für **Verfügbar gemachte Header** ein Sternchen (*) ein.
1. Für **Max. Alter** geben Sie 200 ein.
1. Wählen Sie **Speichern** aus.

#### <a name="31-test-cors"></a>3.1 Testen von CORS

Führen Sie die folgenden Schritte aus, um zu überprüfen, ob Sie bereit sind:

1. Wiederholen Sie den CORS-Konfigurationsschritt. Geben Sie für **Zulässige Ursprünge** den Wert `https://www.test-cors.org` ein.
1. Navigieren Sie zu [www.test-cors.org](https://www.test-cors.org/). 
1. Fügen Sie im Feld **Remote-URL** die URL Ihrer HTML-Datei ein. Zum Beispiel, `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Wählen Sie **Anforderung senden** aus.
    Das Ergebnis sollte `XHR status: 200` lauten. 
    Wenn ein Fehler ausgegeben wird, sollten Sie sich vergewissern, ob Ihre CORS-Einstellungen richtig sind. Außerdem müssen Sie unter Umständen Ihren Browsercache löschen oder eine InPrivate-Browsersitzung öffnen, indem Sie STRG+UMSCHALT+P drücken.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. Aktualisieren des Benutzerflows

1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Benutzerflows** und dann den Benutzerflow *B2C_1_signupsignin1* aus.
1. Wählen Sie **Seitenlayouts** aus, und klicken Sie dann unter **Einheitliche Seite für Registrierung oder Anmeldung** bei **Benutzerdefinierten Seiteninhalt verwenden** auf **Ja**.
1. Geben Sie unter **Benutzerdefinierter Seiten-URI** den URI für die Datei *custom-ui.html* ein, die Sie zuvor gespeichert haben.
1. Klicken Sie am oberen Rand der Seite auf **Speichern**.

### <a name="5-test-the-user-flow"></a>5. Testen des Benutzerflows

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten **Benutzerflows** und dann den Benutzerflow *B2C_1_signupsignin1* aus.
1. Klicken Sie im oberen Bereich der Seite auf **Benutzerflow ausführen**.
1. Klicken Sie auf die Schaltfläche **Benutzerflow ausführen**.

Es sollte eine Seite ähnlich dem folgenden Beispiel mit den zentrierten Elementen angezeigt werden, basierend auf der CSS-Datei, die Sie erstellt haben:

![Webbrowser mit Seite für die Registrierung/Anmeldung mit benutzerdefinierten Benutzeroberflächenelementen](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. Ändern der Erweiterungsdatei

Um die Anpassung der Benutzeroberfläche zu konfigurieren, kopieren Sie die **ContentDefinition** und ihre untergeordneten Elemente in der Basisdatei, und fügen Sie sie in die Erweiterungsdatei ein.

1. Öffnen Sie die Basisdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>. Die Basisdatei ist eine der Richtliniendateien im Starter Pack für benutzerdefinierte Richtlinien, das Sie im unter „Voraussetzungen“ genannten Artikel [Erste Schritte mit benutzerdefinierten Richtlinien](./custom-policy-get-started.md) abgerufen haben sollten.
1. Suchen Sie nach dem Element **ContentDefinitions**, und kopieren Sie den gesamten Inhalt.
1. Öffnen Sie die Erweiterungsdatei. Beispiel: *TrustFrameworkExtensions.xml*. Suchen Sie nach dem Element **BuildingBlocks**. Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie den gesamten Inhalt des **ContentDefinitions**-Elements ein, das Sie als untergeordnetes Element des **BuildingBlocks**-Elements kopiert haben.
1. Suchen Sie dem **ContentDefinition**-Element, das `Id="api.signuporsignin"` in der kopierten XML-Datei enthält.
1. Ändern Sie den Wert von **LoadUri** in die URL der HTML-Datei, die Sie in den Speicher hochgeladen haben. Beispiel: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Ihre benutzerdefinierte Richtlinie sollte wie der folgende Codeausschnitt aussehen:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Speichern Sie die Erweiterungsdatei.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Hochladen und Testen Ihrer aktualisierten benutzerdefinierten Richtlinie

#### <a name="51-upload-the-custom-policy"></a>5.1 Hochladen der benutzerdefinierten Richtlinie

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
1. Suchen Sie nach **Azure AD B2C**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus.
1. Laden Sie die zuvor geänderte Erweiterungsdatei hoch.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Testen der benutzerdefinierten Richtlinie mit **Jetzt ausführen**

1. Wählen Sie die hochgeladene Richtlinie aus, und wählen Sie dann **Jetzt ausführen** aus.
1. Sie sollten sich mit einer E-Mail-Adresse registrieren können.

## <a name="configure-dynamic-custom-page-content-uri"></a>Konfigurieren eines URI für den dynamischen benutzerdefinierten Seiteninhalt

Mithilfe von benutzerdefinierten Azure AD B2C-Richtlinien können Sie einen Parameter im URL-Pfad oder eine Abfragezeichenfolge senden. Durch Übergeben des Parameters an Ihren HTML-Endpunkt können Sie den Seiteninhalt dynamisch ändern. Sie können z.B. das Hintergrundbild auf der Azure AD B2C-Registrierungs- oder Anmeldeseite auf der Basis eines Parameters ändern, den Sie von der Web- oder Mobilanwendung übergeben. Der Parameter kann ein beliebiger [Anspruchskonfliktlöser](claim-resolver-overview.md) sein, z. B. Anwendungs-ID, Sprach-ID oder ein benutzerdefinierter Abfragezeichenfolgeparameter wie `campaignId`.

### <a name="sending-query-string-parameters"></a>Senden von Abfragezeichenfolgeparametern

Um Abfragezeichenfolgeparameter zu senden, fügen Sie in der [Richtlinie für die vertrauende Seite](relyingparty.md) ein `ContentDefinitionParameters`-Element hinzu, wie unten gezeigt.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

Ändern Sie in der Inhaltsdefinition den Wert `LoadUri` in `https://<app_name>.azurewebsites.net/home/unified`. Ihre benutzerdefinierte Richtlinie `ContentDefinition` sollte wie der folgende Codeausschnitt aussehen:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Wenn Azure AD B2C die Seite lädt, wird der Endpunkt Ihres Webservers aufgerufen:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI für dynamischen Seiteninhalt

Inhalt kann anhand der verwendeten Parameter von unterschiedlichen Quellen abgerufen werden. Richten Sie in Ihrem CORS-fähigen Endpunkt eine Ordnerstruktur zum Hosten von Inhalten ein. Sie können den Inhalt beispielsweise in der folgenden Struktur organisieren. Stammverzeichnis *Ordner/Ordner pro Sprache/Ihre HTML-Dateien*. Beispielsweise könnte Ihr benutzerdefinierter Seiten-URI wie folgt aussehen:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C sendet den aus zwei Buchstaben bestehenden ISO-Code, z. B. `fr` für Französisch:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Beispielvorlagen

Beispielvorlagen für die Benutzeroberflächenanpassung finden Sie hier:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Dieses Projekt enthält die folgenden Vorlagen:
- [Ozeanblau](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Schiefergrau](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

So verwenden Sie das Beispiel

1. Klonen Sie das Repository auf Ihrem lokalen Computer. Wählen Sie den Vorlagenordner `/ocean_blue` oder `/slate_gray` aus.
1. Laden Sie alle Dateien aus dem Vorlagenordner und dem Ordner `/assets` in den Blob-Speicher hoch, wie in den vorherigen Abschnitten beschrieben.
1. Öffnen Sie als Nächstes jede `\*.html`-Datei im Stammverzeichnis von `/ocean_blue` bzw. `/slate_gray`, und ersetzen Sie alle Vorkommen relativer URLs durch die URLs in den CSS-, Bild- und Schriftartdateien, die Sie in Schritt 2 hochgeladen haben. Beispiel:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Beschreibung
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Speichern Sie die `\*.html`-Dateien, und laden Sie sie in den Blob-Speicher hoch.
1. Ändern Sie nun die Richtlinie, die auf Ihre HTML-Datei verweist, wie zuvor erwähnt.
1. Falls Schriftarten, Bilder oder CSS-Dateien fehlen, überprüfen Sie die Verweise in der Erweiterungsrichtlinie und die HTML-Dateien (\*.html).

## <a name="use-company-branding-assets-in-custom-html"></a>Verwenden von Unternehmensbrandingobjekten in benutzerdefiniertem HTML

Wenn Sie [Unternehmensbrandingobjekte](customize-ui.md#configure-company-branding) in benutzerdefiniertem HTML-Code verwenden möchten, fügen Sie die folgenden Tags außerhalb des Tags `<div id="api">` hinzu. Die Bildquelle wird durch die des Hintergrundbilds und Bannerlogos ersetzt.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [clientseitigen JavaScript-Code](javascript-and-page-layout.md) aktivieren.