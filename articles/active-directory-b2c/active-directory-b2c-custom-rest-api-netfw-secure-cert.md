---
title: Schützen Ihres RESTful-Diensts mit Clientzertifikaten in Azure Active Directory B2C | Microsoft-Dokumentation
description: Schützen Sie Ihre Austauschvorgänge für benutzerdefinierte REST-API-Ansprüche in Ihrem Azure AD B2C mit Clientzertifikaten.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b007aa4619effbd34e4e969e4ce7b58f3b0c4cf6
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510539"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Schützen Ihres RESTful-Diensts mit Clientzertifikaten

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In einem verwandten Artikel [erstellen Sie einen RESTful-Dienst](active-directory-b2c-custom-rest-api-netfw.md), der mit Azure Active Directory B2C (Azure AD B2C) interagiert.

In diesem Artikel erfahren Sie, wie Sie den Zugriff auf Ihre Azure-Web-App (RESTful-API) mithilfe eines Clientzertifikats einschränken. Dieser Mechanismus wird als gegenseitige TLS-Authentifizierung oder *Clientzertifikatauthentifizierung* bezeichnet. Nur Dienste mit den richtigen Zertifikaten, z.B. Azure AD B2C, erhalten Zugriff auf Ihren Dienst.

>[!NOTE]
>Sie können Ihren RESTful-Dienst auch unter Verwendung der [HTTP-Standardauthentifizierung](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) schützen. Die HTTP-Standardauthentifizierung gilt jedoch als unsicherer als die Authentifizierung über ein Clientzertifikat. Unsere Empfehlung: Schützen Sie den RESTful-Dienst wie in diesem Artikel beschrieben mit der Clientzertifikatauthentifizierung.

In diesem Artikel wird Folgendes ausführlich erläutert:
* Einrichten Ihrer Web-App für die Verwendung der Clientzertifikatauthentifizierung
* Hochladen des Zertifikats in Azure AD B2C-Richtlinienschlüssel
* Konfigurieren Ihrer benutzerdefinierten Richtlinie zur Verwendung des Clientzertifikats

## <a name="prerequisites"></a>Voraussetzungen
* Führen Sie die Schritte im Artikel zum [Integrieren von REST-API-Anspruchsaustauschvorgängen](active-directory-b2c-custom-rest-api-netfw.md) aus.
* Erstellen Sie ein gültiges Zertifikat (PFX-Datei mit privatem Schlüssel).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Schritt 1: Konfigurieren einer Web-App für die Clientzertifikatauthentifizierung
Damit **Azure App Service** so eingerichtet werden kann, dass Clientzertifikate erforderlich sind, muss die Websiteeinstellung `clientCertEnabled` der Web-App auf *true* festgelegt werden. Um diese Änderung wirksam zu machen, öffnen Sie im Azure-Portal die Seite Ihrer Web-App. Wählen Sie im linken Navigationsbereich unter **Einstellungen** die Option **SSL-Einstellungen**. Aktivieren Sie im Abschnitt **Clientzertifikate** die Option **Eingehendes Clientzertifikat**.

>[!NOTE]
>Stellen Sie sicher, dass Sie mindestens den Azure App Service-Plan „Standard“ verwenden. Weitere Informationen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](https://docs.microsoft.com/azure/app-service/overview-hosting-plans).

>[!NOTE]
>Weitere Informationen zum Festlegen der **clientCertEnabled**-Eigenschaft finden Sie unter [Konfigurieren der gegenseitigen TLS-Authentifizierung für eine Web-App](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Schritt 2: Hochladen Ihres Zertifikats in Azure AD B2C-Richtlinienschlüssel
Nachdem Sie `clientCertEnabled` auf *true* festgelegt haben, setzt die Kommunikation mit Ihrer RESTful-API nun ein Clientzertifikat voraus. Gehen Sie zum Abrufen, Hochladen und Speichern des Clientzertifikats in Ihrem Azure AD B2C-Mandanten wie folgt vor: 
1. Wählen Sie in Ihrem Azure AD B2C-Mandanten **B2C-Einstellungen** > **Framework für die Identitätsfunktion** aus.

2. Um die in Ihrem Mandanten verfügbaren Schlüssel anzuzeigen, wählen Sie **Richtlinienschlüssel** aus.

3. Wählen Sie **Hinzufügen**.  
    Das Fenster **Einen Schlüssel erstellen** wird geöffnet.

4. Wählen Sie im Feld **Optionen** die Option **Upload** aus.

5. Geben Sie im Feld **Name** die Zeichenfolge **B2cRestClientCertificate** ein.  
    Das Präfix *B2C_1A_* wird automatisch hinzugefügt.

6. Wählen Sie im Feld **Dateiupload** die PFX-Datei Ihres Zertifikats mit dem privaten Schlüssel aus.

7. Geben Sie im Feld **Kennwort** das Kennwort des Zertifikats ein.

    ![Hochladen des Richtlinienschlüssels](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Klicken Sie auf **Erstellen**.

8. Um die in Ihrem Mandanten verfügbaren Schlüssel anzuzeigen und zu bestätigen, dass Sie den Schlüssel `B2C_1A_B2cRestClientCertificate` erstellt haben, wählen Sie **Richtlinienschlüssel** aus.

## <a name="step-3-change-the-technical-profile"></a>Schritt 3: Ändern des technischen Profils
Um die Clientzertifikatauthentifizierung in Ihrer benutzerdefinierten Richtlinie zu unterstützen, ändern Sie das technische Profil wie folgt:

1. Öffnen Sie die Erweiterungsrichtliniendatei *TrustFrameworkExtensions.xml* in Ihrem Arbeitsverzeichnis.

2. Suchen Sie den `<TechnicalProfile>`-Knoten, der `Id="REST-API-SignUp"` enthält.

3. Suchen Sie das `<Metadata>`-Element.

4. Ändern Sie *AuthenticationType* wie folgt in *ClientCertificate*:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Fügen Sie den folgenden XML-Codeausschnitt unmittelbar hinter dem schließenden `<Metadata>`-Element hinzu: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Nachdem Sie den Ausschnitt hinzugefügt haben, sollte Ihr technisches Profil wie der folgende XML-Code aussehen:

    ![Festlegen der ClientCertificate-Authentifizierungs-XML-Elemente](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Schritt 4: Hochladen der Richtlinie in Ihren Mandanten

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum [Kontext Ihres Azure AD B2C-Mandanten](active-directory-b2c-navigate-to-b2c-context.md), und wählen Sie anschließend **Azure AD B2C** aus.

2. Wählen Sie **Framework für die Identitätsfunktion** aus.

3. Wählen Sie die Option **Alle Richtlinien** aus.

4. Wählen Sie **Richtlinie hochladen** aus.

5. Aktivieren Sie das Kontrollkästchen **Richtlinie überschreiben, sofern vorhanden**.

6. Laden Sie die Datei *TrustFrameworkExtensions.xml* hoch, und stellen Sie sicher, dass sie die Überprüfung besteht.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Schritt 5: Testen der benutzerdefinierten Richtlinie mit „Jetzt ausführen“
1. Öffnen Sie **Azure AD B2C-Einstellungen**, und wählen Sie **Framework für die Identitätsfunktion** aus.

    >[!NOTE]
    >Für „Jetzt ausführen“ muss mindestens eine Anwendung vorab im Mandanten registriert werden. Informationen zum Registrieren von Anwendungen finden Sie in den Artikeln [Erste Schritte](active-directory-b2c-get-started.md) bzw. [Anwendungsregistrierung](active-directory-b2c-app-registration.md) zu Azure AD B2C.

2. Öffnen Sie **B2C_1A_signup_signin**. Dies ist die benutzerdefinierte Richtlinie der vertrauenden Seite (Relying Party, RP), die Sie hochgeladen haben. Wählen Sie anschließend **Jetzt ausführen** aus.

3. Testen Sie den Prozess, indem Sie **Test** in das Feld **Vorname** eingeben.  
    Azure AD B2C zeigt am oberen Rand des Fensters eine Fehlermeldung an.    

    ![Testen Ihrer Identitäts-API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Geben Sie in das Feld **Vorname** einen Namen (nicht „Test“) ein.  
    Azure AD B2C meldet den Benutzer an und sendet dann eine Treuenummer an Ihre Anwendung. Die Treuenummer ist in diesem JWT-Beispiel enthalten:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
     "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
     "acr": "b2c_1a_signup_signin",
     "nonce": "defaultNonce",
     "iat": 1507122303,
     "auth_time": 1507122303,
     "loyaltyNumber": "290",
     "given_name": "Emily",
     "emails": ["B2cdemo@outlook.com"]
   }
   ```

   >[!NOTE]
   >Wenn Sie die Fehlermeldung *Der Name ist ungültig. Geben Sie einen gültigen Namen an* erhalten, bedeutet das, dass Azure AD B2C Ihren RESTful-Dienst erfolgreich mit dem Clientzertifikat aufgerufen hat. Der nächste Schritt besteht im Überprüfen des Zertifikats.

## <a name="step-6-add-certificate-validation"></a>Schritt 6: Hinzufügen der Zertifikatüberprüfung
Das Clientzertifikat, das Azure AD B2C an Ihren RESTful-Dienst sendet, wird von der Azure App Service-Plattform keiner Überprüfung unterzogen (mit Ausnahme der Überprüfung des Vorhandenseins). Für das Überprüfen des Zertifikats ist die Web-App zuständig. 

In diesem Abschnitt fügen Sie ASP.NET-Beispielcode hinzu, mit dem die Zertifikateigenschaften zum Zweck der Authentifizierung überprüft werden.

> [!NOTE]
>Weitere Informationen zum Konfigurieren von Azure App Service für die Clientzertifikatauthentifizierung finden Sie unter [Konfigurieren der gegenseitigen TLS-Authentifizierung für Web-Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Hinzufügen von Anwendungseinstellungen zur Datei „web.config“ des Projekts
Fügen Sie in dem zuvor erstellten Visual Studio-Projekt der Datei *web.config* hinter dem `appSettings`-Element die folgenden Anwendungseinstellungen hinzu:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Ersetzen Sie die Werte **Antragstellername**, **Ausstellernamen** und **Zertifikatfingerabdruck** für das Zertifikat durch Ihre Zertifikatwerte.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 Hinzufügen der IsValidClientCertificate-Funktion
Öffnen Sie die Datei *Controllers\IdentityController.cs*, und fügen Sie der Controllerklasse `Identity` die folgende Funktion hinzu: 

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

Im vorhergehenden Beispielcode akzeptieren wir das Zertifikat nur dann als gültig, wenn alle folgenden Bedingungen erfüllt sind:
* Das Zertifikat ist nicht abgelaufen und für die aktuelle Uhrzeit auf dem Server aktiv.
* Der `Subject`-Name des Zertifikats entspricht dem Anwendungseinstellungswert `ClientCertificate:Subject`.
* Der `Issuer`-Name des Zertifikats entspricht dem Anwendungseinstellungswert `ClientCertificate:Issuer`.
* Der `thumbprint` des Zertifikats entspricht dem Anwendungseinstellungswert `ClientCertificate:Thumbprint`.

>[!IMPORTANT]
>Abhängig von der Vertraulichkeit Ihres Diensts können Sie ggf. weitere Überprüfungen hinzufügen. Sie könnten z.B. testen, ob das Zertifikat mit einer vertrauenswürdigen Stammzertifizierungsstelle verkettet ist, den Namen der Organisation überprüfen usw.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 Aufrufen der IsValidClientCertificate-Funktion
Öffnen Sie die Datei *Controllers\IdentityController.cs*, und fügen Sie dann am Anfang der `SignUp()`-Funktion den folgenden Codeausschnitt hinzu: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Nachdem Sie den Ausschnitt hinzugefügt haben, sollte Ihr `Identity`-Controller folgendem Code entsprechen:

![Hinzufügen des Zertifikatüberprüfungscodes](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Schritt 7: Veröffentlichen und Testen des Projekts in Azure
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **Contoso.AADB2C.API**, und wählen Sie dann **Veröffentlichen** aus.

2. Wiederholen Sie Schritt 6, und testen Sie die benutzerdefinierte Richtlinie mit der Überprüfung des Zertifikats. Versuchen Sie, die Richtlinie auszuführen, und stellen Sie sicher, dass alles funktioniert, nachdem Sie die Validierung hinzugefügt haben.

3. Ändern Sie in der Datei *web.config* den Wert von `ClientCertificate:Subject` in **invalid**. Führen Sie die Richtlinie erneut aus. Es sollte eine Fehlermeldung angezeigt werden.

4. Ändern Sie den Wert wieder in **valid**, und überprüfen Sie, ob die Richtlinie Ihre REST-API aufrufen kann.

Informationen zum Beheben von Problemen bei diesem Schritt finden Sie unter [Erfassen von Protokollen mithilfe von Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Optional:) Herunterladen der vollständigen Richtliniendateien und des Codes
* Nachdem Sie die exemplarische Vorgehensweise unter [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md) abgeschlossen haben, empfiehlt es sich, ein Szenario mit Ihren eigenen benutzerdefinierten Richtliniendateien zu erstellen. Zu Referenzzwecken haben wir [Beispiele für Richtliniendateien](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert) bereitgestellt.
* Sie können den vollständigen Code unter [Sample Visual Studio solution for reference](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API) (Visual Studio-Beispiellösung zur Referenz) herunterladen. 
