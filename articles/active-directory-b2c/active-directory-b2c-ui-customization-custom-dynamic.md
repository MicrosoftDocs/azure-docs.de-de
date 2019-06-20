---
title: Dynamisches Anpassen der Azure Active Directory B2C-Benutzeroberfläche (UI) mithilfe von benutzerdefinierten Richtlinien | Microsoft-Dokumentation
description: Unterstützung mehrerer Brandingdarstellungen mit HTML5/CSS-Inhalt, der sich zur Laufzeit dynamisch ändert.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e1abdfa8bc47f42f7373760370588c0bc41fc1dc
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507777"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Konfigurieren der Benutzeroberfläche mit dynamischen Inhalten mithilfe von benutzerdefinierten Richtlinien

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Mithilfe von benutzerdefinierten Azure Active Directory B2C-Richtlinien (Azure AD B2C) können Sie einen Parameter in einer Abfragezeichenfolge senden. Durch Übergeben des Parameters an Ihren HTML-Endpunkt können Sie den Seiteninhalt dynamisch ändern. Sie können z.B. das Hintergrundbild auf der Azure AD B2C-Registrierungs- oder Anmeldeseite auf der Basis eines Parameters ändern, den Sie von der Web- oder Mobilanwendung übergeben. 

## <a name="prerequisites"></a>Voraussetzungen
Dieser Artikel konzentriert sich auf das Anpassen der Azure AD B2C-Benutzeroberfläche mit *dynamischen Inhalten* unter Verwendung benutzerdefinierter Richtlinien. Informationen zum Einstieg finden Sie unter [Benutzeroberflächenanpassung in einer benutzerdefinierten Richtlinie](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>Im Artikel [Azure Active Directory B2C: Konfigurieren der Benutzeroberflächenanpassung in einer benutzerdefinierten Richtlinie](active-directory-b2c-ui-customization-custom.md) werden die folgenden Grundlagen erläutert:
> * Das Anpassungsfeature für die Seitenbenutzeroberfläche (UI).
> * Wichtige Tools zum Testen des Anpassungsfeatures für die Seitenbenutzeroberfläche mit unserem Beispielinhalt.
> * Die wichtigsten Benutzeroberflächenelemente jedes Seitentyps.
> * Bewährte Methoden für die Anwendung dieses Features.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Hinzufügen eines Links zu HTML5/CSS-Vorlagen für die User Journey

In einer benutzerdefinierten Richtlinie definiert eine Inhaltsdefinition den HTML5-Seiten-URI, der für einen bestimmten Schritt der Benutzeroberfläche (z.B. die Anmelde- oder Registrierungsseite) verwendet wird. Die Basisrichtlinie definiert das standardmäßige Aussehen und Verhalten durch Verweis auf einen URI der HTML5-Dateien (im CSS). In der Erweiterungsrichtlinie können Sie das Aussehen und Verhalten durch Überschreiben des LoadUri für die HTML5-Datei ändern. Inhaltsdefinitionen enthalten URLs zu externem Inhalt, der durch Erstellen von HTML5/CSS-Dateien nach Bedarf definiert wird. 

Der Abschnitt `ContentDefinitions` enthält eine Reihe von `ContentDefinition`-XML-Elementen. Das ID-Attribut des `ContentDefinition`-Elements gibt den Typ der Seite an, der mit der Inhaltsdefinition verknüpft ist. Das heißt, dass das Element den Kontext definiert, den eine benutzerdefinierte HTML5/CSS-Vorlage anwendet. In der folgenden Tabelle werden die Gruppe mit den IDs der Inhaltsdefinitionen, die von der IEF-Engine erkannt werden, und die entsprechenden Seitentypen beschrieben.

| ID für Inhaltsdefinition | Standardmäßige HTML5-Vorlage| BESCHREIBUNG | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Fehlerseite**: Diese Seite wird angezeigt, wenn eine Ausnahme oder ein Fehler auftreten. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seite zur Auswahl des Identitätsanbieters**: Auf dieser Seite sind Identitätsanbieter aufgelistet, unter denen Benutzer bei der Anmeldung wählen können. Bei den Optionen handelt es sich normalerweise um Unternehmensidentitätsanbieter oder Identitätsanbieter in Form von sozialen Netzwerken wie Facebook und Google+ oder lokale Konten. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seite zur Auswahl des Identitätsanbieters für die Registrierung**: Auf dieser Seite sind Identitätsanbieter aufgelistet, unter denen Benutzer bei der Registrierung wählen können. Bei den Optionen handelt es sich um Unternehmensidentitätsanbieter, Identitätsanbieter in Form von sozialen Netzwerken wie Facebook und Google+ oder lokale Konten. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Seite „Kennwort vergessen“** : Diese Seite enthält ein Formular, das Benutzer ausfüllen müssen, um eine Kennwortzurücksetzung zu initiieren.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Seite für Anmeldung mit lokalem Konto**: Diese Seite enthält ein Formular für die Anmeldung mit einem lokalen Konto, das auf einer E-Mail-Adresse oder einem Benutzernamen basiert. Das Formular kann ein Texteingabefeld und ein Kennworteingabefeld enthalten. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Seite für Registrierung mit lokalem Konto**. Diese Seite enthält ein Formular für die Registrierung für ein lokales Konto, das auf einer E-Mail-Adresse oder einem Benutzernamen basiert. Das Formular kann verschiedene Eingabesteuerelemente enthalten, z.B. ein Texteingabefeld, ein Kennworteingabefeld, ein Optionsfeld, Dropdownfelder mit einer Auswahlmöglichkeit und Kontrollkästchen mit mehreren Optionen. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Seite „Multi-Factor Authentication“** : Auf dieser Seite können Benutzer während der Registrierung oder Anmeldung ihre Telefonnummern verifizieren (per SMS oder Sprachnachricht). |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Seite zur Registrierung über Konto für soziales Netzwerk**: Diese Seite enthält ein Formular, das Benutzer ausfüllen müssen, wenn die Registrierung mit einem vorhandenen Konto bei einem sozialen Netzwerk als Identitätsanbieter erfolgt. Diese Seite ähnelt der vorherigen Seite für die Registrierung bei einem Konto für ein soziales Netzwerk, mit Ausnahme der Felder für die Kennworteingabe. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Seite für Profilaktualisierung**: Diese Seite enthält ein Formular, auf das Benutzer zum Aktualisieren des Profils zugreifen können. Diese Seite ähnelt der Seite für die Registrierung bei einem Konto für ein soziales Netzwerk, mit Ausnahme der Felder für die Kennworteingabe. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Einheitliche Seite für Registrierung oder Anmeldung**: Auf dieser Seite werden der Registrierungs- und der Anmeldevorgang für Benutzer durchgeführt. Benutzer können Unternehmensidentitätsanbieter, Identitätsanbieter in Form von sozialen Netzwerken wie Facebook und Google+ oder lokale Konten verwenden.  |

## <a name="serving-dynamic-content"></a>Bedienen von dynamischem Inhalt
Im Artikel [Azure Active Directory B2C: Konfigurieren der Benutzeroberflächenanpassung in einer benutzerdefinierten Richtlinie](active-directory-b2c-ui-customization-custom.md) laden Sie HTML5-Dateien in Azure Blob Storage hoch. Diese HTML5-Dateien sind statisch und stellen für jede Anforderung den gleichen HTML-Inhalt dar. 

In diesem Artikel verwenden Sie eine ASP.NET-Web-App, die Abfragezeichenfolgen-Parameter akzeptieren und darauf entsprechend reagieren kann. 

In dieser exemplarischen Vorgehensweise führen Sie folgende Aktionen aus:
* Erstellen einer ASP.NET Core-Webanwendung, die Ihre HTML5-Vorlagen hostet 
* Hinzufügen der benutzerdefinierten HTML5-Vorlage _unified.cshtml_ 
* Veröffentlichen Ihrer Web-App in Azure App Service 
* Festlegen der Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS) für Ihre Web-App
* Überschreiben der `LoadUri`-Elemente, sodass sie auf Ihre HTML5-Datei verweisen

## <a name="step-1-create-an-aspnet-web-app"></a>Schritt 1: Erstellen einer ASP.NET-Web-App

1. Erstellen Sie in Visual Studio ein Projekt durch Auswählen von **Datei** > **Neu** > **Projekt**.

2. Wählen Sie im Fenster **Neues Projekt** die Optionen **Visual C#**  > **Web** > **ASP.NET Core-Webanwendung (.NET Core)** aus.

3. Benennen Sie die Anwendung (z.B. *Contoso.AADB2C.UI*), und wählen Sie dann **OK** aus.

    ![Erstellen eines neuen Visual Studio-Projekts](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Wählen Sie die Vorlage **Webanwendung** aus.

5. Legen Sie die Authentifizierung auf **Keine Authentifizierung** fest.

    ![Auswählen der Vorlage „Webanwendung“](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Wählen Sie **OK** aus, um das Projekt zu erstellen.

## <a name="step-2-create-mvc-view"></a>Schritt 2: Erstellen der MVC-Ansicht
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Schritt 2.1: Herunterladen des in der HTML5-Vorlage integrierten B2C
Ihre benutzerdefinierte HTML5-Vorlage basiert auf dem in der HTML5-Vorlage integrierten Azure AD B2C. Sie können die Datei [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) herunterladen, oder laden Sie die Vorlage vom [Startpaket](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip) herunter. Sie verwenden diese HTML5-Datei zum Erstellen einer einheitlichen Registrierungs- oder Anmeldeseite.

### <a name="step-22-add-the-mvc-view"></a>Schritt 2.2: Hinzufügen der MVC-Ansicht
1. Klicken Sie mit der rechten Maustaste auf den Ordner „Home“ unter „Views“, und klicken Sie dann auf **Hinzufügen** > **Neues Element**.

    ![Hinzufügen des neuen MVC-Elements](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. Wählen Sie im Fenster **Neues Element hinzufügen – Contoso.AADB2C.UI** die Optionen **Web > ASP.NET** aus.

3. Wählen Sie **MVC-Ansichtsseite** aus.

4. Ändern Sie im Feld **Name** den Namen in **unified.cshtml**.

5. Wählen Sie **Hinzufügen**.

    ![Hinzufügen der MVC-Ansicht](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Wenn die Datei *unified.cshtml* noch nicht geöffnet ist, doppelklicken Sie auf die Datei, um sie zu öffnen, und löschen Sie dann den Inhalt der Datei.

7. In dieser exemplarischen Vorgehensweise wird der Verweis auf die Layoutseite entfernt. Fügen Sie der Datei _unified.cshtml_ den folgenden Codeausschnitt hinzu:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Öffnen Sie die Datei _unified.cshtml_, die Sie aus dem in der HTML5-Vorlage integrierten Azure AD B2C heruntergeladen haben.

9. Ersetzen Sie die einzelnen @-Zeichen durch doppelte @@-Zeichen.

10. Kopieren Sie den Inhalt der Datei, und fügen Sie ihn unterhalb der Layout-Definition ein. Der Code sollte wie folgt aussehen:

    ![Datei „unified.cshtml“ nach dem Hinzufügen der HTML5-Vorlage](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Schritt 2.3: Ändern des Hintergrundbilds

Suchen Sie das `<img>`-Element, das den `ID`-Wert *background_background_image* enthält, und ersetzen Sie den `src`-Wert durch **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** oder ein anderes Hintergrundbild, das Sie verwenden möchten.

![Ändern des Seitenhintergrunds](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Schritt 2.4: Hinzufügen Ihrer Ansicht zum MVC-Controller

1. Öffnen Sie **Controllers\HomeController.cs**, und fügen Sie die folgende Methode hinzu: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Dieser Code gibt an, dass die Methode eine Vorlagendatei des Typs *View* verwenden soll, um eine Antwort an den Browser auszugeben. Da wir den Namen der Vorlagendatei *View* nicht explizit angeben, verwendet MVC standardmäßig die View-Datei von _unified.cshtml_ im Ordner */Views/Home*.
    
    Nach dem Hinzufügen der _unified_-Methode sollte der Code wie folgt aussehen:
    
    ![Ändern des Controllers zum Ausgeben der Ansicht](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Debuggen Sie Ihre Web-App, und stellen Sie sicher, dass der Zugriff auf die _einheitliche_ Seite möglich ist (z.B. `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Schritt 2.5: Veröffentlichen in Azure
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **Contoso.AADB2C.UI**, und wählen Sie dann **Veröffentlichen** aus.

    ![Veröffentlichen in Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Wählen Sie die Kachel **Microsoft Azure App Service** und anschließend **Veröffentlichen** aus.

    ![Erstellen eines neuen Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    Das Fenster **App Service erstellen** wird geöffnet. Darin können Sie alle Azure-Ressourcen erstellen, die zum Ausführen der ASP.NET-Web-App in Azure benötigt werden.

    > [!NOTE]
    > Weitere Informationen zum Veröffentlichen finden Sie unter [Erstellen einer ASP.NET-Web-App in Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. Geben Sie im Feld **Web-App-Name** einen eindeutigen Web-App-Namen ein (gültige Zeichen sind a-z, A-Z, 0-9, und der Bindestrich [-]). Die URL der Web-App lautet `http://<app_name>.azurewebsites.NET`, wobei `<app_name>` der Name der Web-App ist. Sie können den automatisch generierten Namen übernehmen. Dieser ist eindeutig.

4. Wählen Sie **Erstellen** aus, um mit der Erstellung der Azure-Ressourcen zu beginnen.

    ![Bereitstellen der App Service-Eigenschaften](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Nach Abschluss des Erstellungsvorgangs wird die ASP.NET Web-App in Azure veröffentlicht und anschließend im Standardbrowser gestartet.

5. Kopieren Sie die URL der _einheitlichen_ Seite (z.B. _https://<App-Name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Schritt 3: Konfigurieren von CORS in Azure App Service
1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option **App Services** und anschließend den Namen Ihrer API-App aus.

    ![Auswählen der API-App im Azure-Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. Wählen Sie im Abschnitt **Einstellungen** unter dem Abschnitt **API** die Option **CORS** aus.

    ![Auswählen der CORS-Einstellungen](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. Nehmen Sie im Fenster **CORS** im Feld **Zulässige Ursprünge** eine der folgenden Eingaben vor:

    * Geben Sie die URLs ein, von denen JavaScript-Aufrufe ausgehen können sollen. Sie dürfen bei der Eingabe Ihrer URLs ausschließlich Kleinbuchstaben verwenden.
    * Geben Sie ein Sternchen (*) ein, um anzugeben, dass alle Ursprungsdomänen akzeptiert werden.

4. Wählen Sie **Speichern** aus.

    ![Fenster „CORS“](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Nachdem Sie **Speichern** ausgewählt haben, akzeptiert die API-App JavaScript-Aufrufe von den angegebenen URLs. 

## <a name="step-4-html5-template-validation"></a>Schritt 4: Überprüfen der HTML5-Vorlage
Die HTML5-Vorlage ist einsatzbereit. Sie ist jedoch im `ContentDefinition`-Code nicht verfügbar. Vor dem Hinzufügen von `ContentDefinition` zu Ihrer benutzerdefinierten Richtlinie müssen Sie Folgendes sicherstellen:
* Ihr Inhalt ist HTML5-konform und zugänglich.
* Ihr Inhaltsserver ist für CORS aktiviert.

    >[!NOTE]
    >Über die Website [test-cors.org](https://test-cors.org/) können Sie überprüfen, ob auf der Site, auf der Sie den Inhalt hosten, CORS aktiviert ist, sowie CORS-Anforderungen testen. 

* Bereitgestellte Inhalte sind über **HTTPS** sicher.
* Sie verwenden für alle Links, CSS-Inhalte und Bilder *absolute URLs* (z. B. `https://yourdomain/content`).

## <a name="step-5-configure-your-content-definition"></a>Schritt 5: Konfigurieren Ihrer Inhaltsdefinition
Führen Sie folgende Schritte aus, um `ContentDefinition` zu konfigurieren:
1. Öffnen Sie die Basisdatei Ihrer Richtlinie (z.B. *TrustFrameworkBase.xml*).

2. Suchen Sie das `<ContentDefinitions>`-Element, und kopieren Sie den gesamten Inhalt des Knotens `<ContentDefinitions>`.

3. Öffnen Sie die Erweiterungsdatei (z.B. *TrustFrameworkExtensions.xml*), und suchen Sie dann das `<BuildingBlocks>`-Element. Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.

4. Fügen Sie den gesamten Inhalt des Knotens `<ContentDefinitions>` ein, den Sie als untergeordnetes Element des `<BuildingBlocks>`-Elements kopiert haben. 

5. Suchen Sie den Knoten `<ContentDefinition>`, der in der kopierten XML-Datei `Id="api.signuporsignin"` enthält.

6. Ändern Sie den Wert von `LoadUri` von _~/tenant/default/unified_ in _https://<App-Name>.azurewebsites.net/home/unified_.  
    Ihre benutzerdefinierte Richtlinie sollte wie folgt aussehen:
    
    ![Ihre Inhaltsdefinition](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Schritt 6: Hochladen der Richtlinie in Ihren Mandanten
1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum [Kontext Ihres Azure AD B2C-Mandanten](active-directory-b2c-navigate-to-b2c-context.md), und wählen Sie anschließend **Azure AD B2C** aus.

2. Wählen Sie **Framework für die Identitätsfunktion** aus.

3. Wählen Sie die Option **Alle Richtlinien** aus.

4. Wählen Sie **Richtlinie hochladen** aus.

5. Aktivieren Sie das Kontrollkästchen **Richtlinie überschreiben, sofern vorhanden**.

6. Laden Sie die Datei *TrustFrameworkExtensions.xml* hoch, und stellen Sie sicher, dass sie die Überprüfung besteht.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Schritt 7: Testen der benutzerdefinierten Richtlinie mit „Jetzt ausführen“
1. Wählen Sie **Azure AD B2C-Einstellungen** und dann **Framework für die Identitätsfunktion** aus.

    >[!NOTE]
    >Für „Jetzt ausführen“ muss mindestens eine Anwendung vorab im Mandanten registriert werden. Informationen zum Registrieren von Anwendungen finden Sie in den Artikeln [Erste Schritte](active-directory-b2c-get-started.md) bzw. [Anwendungsregistrierung](active-directory-b2c-app-registration.md) zu Azure AD B2C.

2. Öffnen Sie **B2C_1A_signup_signin**. Dies ist die benutzerdefinierte Richtlinie der vertrauenden Seite (Relying Party, RP), die Sie hochgeladen haben. Wählen Sie anschließend **Jetzt ausführen** aus.  
    Sie sollten Ihre benutzerdefinierten HTML5-Inhalte mit dem Hintergrund sehen, den Sie zuvor erstellt haben.

    ![Ihre Registrierungs- oder Anmelderichtlinie](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Schritt 8: Dynamischen Inhalt hinzufügen
Ändern Sie den Hintergrund basierend auf dem Abfragezeichenfolgen-Parameter mit dem Namen _campaignId_. Die Anwendung Ihrer vertrauenden Seite (Web- und mobile Apps) sendet den Parameter an Azure AD B2C. Die Richtlinie liest den Parameter und sendet seinen Wert an die HTML5-Vorlage. 

### <a name="step-81-add-a-content-definition-parameter"></a>Schritt 8.1: Hinzufügen eines Inhaltsdefinitionsparameters

Fügen Sie das `ContentDefinitionParameters`-Element mit den folgenden Schritten hinzu:
1. Öffnen Sie die Datei *SignUpOrSignin* Ihrer Richtlinie (z.B. *SignUpOrSignin.xml*).

2. Fügen Sie unter dem Knoten `<DefaultUserJourney>` den Knoten `UserJourneyBehaviors` hinzu:  

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ContentDefinitionParameters>
          <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Schritt 8.2: Ändern Ihres Codes, sodass er Abfragezeichenfolgen-Parameter akzeptiert und das Hintergrundbild ersetzt
Ändern Sie die HomeController-Methode `unified`, sodass der campaignId-Parameter akzeptiert wird. Die Methode überprüft dann den Wert des Parameters und legt die `ViewData["background"]`-Variable entsprechend fest.

1. Öffnen Sie die Datei *Controllers\HomeController.cs*, und ändern Sie dann die `unified`-Methode durch Hinzufügen des folgenden Codeausschnitts:

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Suchen Sie das `<img>`-Element mit der ID `background_background_image`, und ersetzen Sie den `src`-Wert durch `@ViewData["background"]`.

    ![Ändern des Seitenhintergrunds](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>Schritt 8.3: Hochladen der Änderungen und Veröffentlichen Ihrer Richtlinie
1. Veröffentlichen Sie Ihr Visual Studio-Projekt in Azure App Service.

2. Laden Sie die Richtliniendatei *SignUpOrSignin.xml* in Azure AD B2C hoch.

3. Öffnen Sie **B2C_1A_signup_signin**. Dies ist die benutzerdefinierte Richtlinie der vertrauenden Seite, die Sie hochgeladen haben. Wählen Sie anschließend **Jetzt ausführen** aus.  
    Daraufhin sollte das gleiche Hintergrundbild wie zuvor angezeigt werden.

4. Kopieren Sie die URL aus der Adressleiste des Browsers.

5. Fügen Sie dem URI den Abfragezeichenfolgen-Parameter _campaignId_ hinzu. Fügen Sie z.B. `&campaignId=hawaii` hinzu, wie in der folgenden Abbildung dargestellt:

    ![Ändern des Seitenhintergrunds](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Drücken Sie die **EINGABETASTE**, um das Hawaii-Hintergrundbild anzuzeigen.

    ![Ändern des Seitenhintergrunds](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Ändern Sie den Wert in *Tokyo*, und drücken Sie dann die **EINGABETASTE**.  
    Im Browser wird der Tokio-Hintergrund angezeigt.

    ![Ändern des Seitenhintergrunds](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Schritt 9: Ändern des verbleibenden Teils der User Journey
Wenn Sie auf der Anmeldeseite den Link **Jetzt registrieren** auswählen, wird im Browser das Standardhintergrundbild und nicht das von Ihnen definierte Bild angezeigt. Dieses Verhalten ist darauf zurückzuführen, dass Sie nur die Registrierungs- oder Anmeldeseite geändert haben. So ändern Sie die restlichen selbstbestätigten Inhaltsdefinitionen
1. Wechseln Sie zurück zu „Schritt 2“, und führen Sie folgende Schritte aus:

    a. Laden Sie die *selfasserted*-Datei herunter.

    b. Kopieren Sie den Dateiinhalt.

    c. Erstellen Sie die neue Ansicht *selfasserted*.

    d. Fügen Sie *selfasserted* dem **Home**-Controller hinzu.

2. Wechseln Sie zurück zu „Schritt 4“, und führen Sie folgende Schritte aus: 

    a. Suchen Sie in Ihrer Erweiterungsrichtlinie den `<ContentDefinition>`-Knoten, der `Id="api.selfasserted"`, `Id="api.localaccountsignup"` und `Id="api.localaccountpasswordreset"` enthält.

    b. Legen Sie das `LoadUri`-Attribut auf Ihren *selfasserted*-URI fest.

3. Wechseln Sie zurück zu „Schritt 8.2“, und ändern Sie den Code zum Akzeptieren von Abfragezeichenfolgen-Parametern, dieses Mal jedoch in die *selfasserted*-Funktion. 

4. Laden Sie die Richtliniendatei *TrustFrameworkExtensions.xml* hoch, und stellen Sie sicher, dass sie die Überprüfung besteht.

5. Führen Sie den Test der Richtlinie aus, und wählen Sie dann **Jetzt registrieren** aus, um das Ergebnis anzuzeigen.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Optional:) Herunterladen der vollständigen Richtliniendateien und des Codes
* Nachdem Sie die exemplarische Vorgehensweise unter [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md) abgeschlossen haben, empfiehlt es sich, ein Szenario mit Ihren eigenen benutzerdefinierten Richtliniendateien zu erstellen. Zu Referenzzwecken haben wir [Beispiele für Richtliniendateien](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization) bereitgestellt.
* Sie können den vollständigen Code unter [Sample Visual Studio solution for reference](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization) (Visual Studio-Beispiellösung zur Referenz) herunterladen.




