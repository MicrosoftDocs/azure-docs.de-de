---
title: Twilio Verify App mit Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Es wird beschrieben, wie Sie eine Beispiel-App für Onlinezahlungen mit der Twilio Verify API in Azure AD B2C integrieren. Erfüllen Sie die Anforderungen für PSD2-Transaktionen (Payment Services Directive 2) durch dynamisches Verknüpfen und eine sichere Kundenauthentifizierung.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 953653a758577ed3d48ca2d81403b4cb363ea294
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994042"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Integrieren der Twilio Verify App mit Azure Active Directory B2C

In dieser exemplarischen Vorgehensweise erfahren Sie, wie Sie eine Beispiel-App für Onlinezahlungen in Azure Active Directory B2C (Azure AD B2C) mit der Twilio Verify API integrieren. Mithilfe der Twilio Verify App können Azure AD B2C-Kunden die Transaktionsanforderungen der Zahlungsdiensterichtlinie PSD2 (Payment Services Directive 2) durch dynamisches Verknüpfen und eine sichere Kundenauthentifizierung erfüllen.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* [Einen Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.
* Ein [Testkonto](https://www.twilio.com/try-twilio) bei Twilio.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Twilio-Lösung besteht aus den folgenden Komponenten:

- .NET [PSD2-Demo-Web-App](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App), die das Anmelden oder Registrieren ermöglicht und mit der eine Dummytransaktion mit hohem Risiko durchgeführt wird
- Kombinierte Azure AD B2C-[Richtlinie für die Anmeldung und Registrierung](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)
- Azure AD B2C-Richtlinie, die per `id_token_hint` mit der Twilio Verify API integriert wird.
- .NET-Web-App zum Hosten eines OpenIdConnect-Endpunkts vom Typ `.well-known`, um die Validierung von `id_token_hint` zu ermöglichen.


    ![Twilio-Datenfluss](media/partner-twilio/twilio-flow.png)

| Schritt | BESCHREIBUNG |
|------|------|
| 1     | Der Benutzer initiiert die Anmeldung oder Registrierung für die PSD2-Demo-App. Der Benutzer wird über die kombinierte Azure AD B2C-Richtlinie für die Anmeldung und Registrierung authentifiziert. Ein Token wird an die Anwendung zurückgegeben. Bei der Registrierung wird die Telefonnummer des Benutzers per SMS/Telefon überprüft und in seinem Azure AD B2C-Konto aufgezeichnet.     |
| 2     | Der Benutzer initiiert eine Transaktion mit hohem Risiko, z. B. eine Überweisung von 50,00 US-Dollar. Das aktuelle Zugriffstoken des Benutzers wird in Bezug auf die PolicyId ausgewertet, um zu ermitteln, ob sich der Benutzer bereits mit einer benutzerdefinierten Step-up-Richtlinie authentifiziert hat.     |
| 3     | Die Anwendung zeichnet den Transaktionswert und den Empfänger (50,00 USD und John Doe) auf und generiert ein signiertes Token. Das Token wird als `id_token_hint` bezeichnet und enthält den folgenden Anspruch: `amount:$500, payee:john doe`. `id_token_hint` wird zusammen mit der Anforderung an die benutzerdefinierte Azure AD B2C-Richtlinie gesendet, die in Twilio integriert ist.     |
| 4     | Azure AD B2C verifiziert die Signatur von „id_token_hint“, indem der OpenID Connect-Endpunkt vom Typ `/.well-known` für die Anwendung überprüft wird. Nach der Überprüfung werden die Ansprüche aus diesem Token extrahiert (also `amount` und `payee`). Dem Benutzer wird eine Seite angezeigt, auf der die Mobiltelefonnummer per SMS-Nachricht überprüft wird.     |
| 5     | Der Benutzer fordert die Überprüfung seiner Telefonnummer per SMS-Nachricht an, und Azure AD B2C sendet eine REST-API-Anforderung an den Verify API-Endpunkt von Twilio. Darüber hinaus werden die Elemente `amount` und `payee` der Transaktion im Rahmen des PSD2-Prozesses gesendet, um die Einmalkennung (One-Time-Passcode, OTP) zu generieren. Twilio sendet eine SMS-Nachricht an die registrierte Telefonnummer des Benutzers.     |
| 6     |  Der Benutzer gibt die Einmalkennung (OTP) ein, die er per SMS-Nachricht erhalten hat, und sendet sie an Azure AD B2C. Azure AD B2C sendet eine API-Anforderung mit dieser Einmalkennung an die Verify API von Twilio, um ihre Richtigkeit zu überprüfen. Abschließend wird ein Token für die Anwendung ausgestellt, das über eine neue PolicyId verfügt. Hiermit wird für den Benutzer angegeben, dass er den Step-up-Vorgang für die Authentifizierung durchgeführt hat.    |
|      |      |

## <a name="onboard-with-twilio"></a>Durchführen des Onboardings mit Twilio

1. Eröffnen Sie ein [Testkonto](https://www.twilio.com/try-twilio) bei Twilio.

2. Erwerben Sie bei Twilio eine Telefonnummer. Dies ist in [diesem Artikel](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console) beschrieben.

3. Navigieren Sie in der Twilio-Konsole zur [Verify API](https://www.twilio.com/console/verify/services), und befolgen Sie die [Anleitung](https://www.twilio.com/docs/verify/verifying-transactions-psd2), um einen Dienst zu erstellen und die PSD2-Option zu aktivieren.  

## <a name="configure-the-psd2-demo-app"></a>Konfigurieren der PSD2-Demo-App

1. Öffnen Sie die Lösung B2C-WebAPI-DotNet, und ersetzen Sie die folgenden Werte durch Ihre eigenen mandantenspezifischen Werte in „web.config“:

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. Die [Web-App](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App) hostet auch den Generator für ID-Tokenhinweise und den Metadatenendpunkt.
   - Erstellen Sie Ihr Signaturzertifikat anhand [dieser Beispielbeschreibung](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate).
   - Aktualisieren Sie die folgenden Zeilen basierend auf Ihrem Zertifikat in „web.config“:
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. Laden Sie die Demoanwendung für den Hostinganbieter Ihrer Wahl hoch. Eine Anleitung für Azure App Service finden Sie in  [dieser Beispielbeschreibung](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service). Sie enthält auch Hinweise zum Hochladen Ihres Zertifikats.

4. Aktualisieren Sie die Registrierung Ihrer Azure AD B2C-Anwendung, indem Sie der URL, unter der die Anwendung gehostet wird, eine Entsprechung einer Antwort-URL hinzufügen.

5. Öffnen Sie die [Richtliniendateien](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy), und ersetzen Sie alle Instanzen von `contoso` durch Ihren Mandantennamen.

6. Suchen Sie nach dem technischen Profil  **Custom-SMS-Enroll** der Twilio-REST-API. Aktualisieren Sie die `ServiceURL` mit Ihrer Twilio AccountSID und der Absendernummer Ihrer erworbenen Telefonnummer.

7. Suchen Sie nach den technischen Profilen **TwilioRestAPI-Verify-Step1** und **TwilioRestAPI-Verify-Step2** der Twilio-REST-API, und aktualisieren Sie die `ServiceURL` mit Ihrer Twilio AccountSID.

## <a name="integrate-with-azure-ad-b2c"></a>Integration in Azure AD B2C

Fügen Sie Azure AD B2C die Richtliniendateien hinzu:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.

2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.

3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.

4. Navigieren Sie zu **Azure AD B2C** > **Identity Experience Framework** > **Richtlinienschlüssel**.

5. Fügen Sie einen neuen Schlüssel mit dem Namen **B2cRestTwilioClientId** hinzu. Wählen Sie **manual** (manuell) aus, und geben Sie den Wert der Twilio AccountSID an.

6. Fügen Sie einen neuen Schlüssel mit dem Namen **B2cRestTwilioClientSecret** hinzu. Wählen Sie **manual** (manuell) aus, und geben Sie den Wert des Twilio-Authentifizierungstokens an.

7. Laden Sie alle Richtliniendateien in Ihren Mandanten hoch.

8. Passen Sie die Zeichenfolge in der GenerateOTPMessageEnrol-Anspruchstransformation für Ihren SMS-Text für die Registrierung an.

## <a name="test-the-solution"></a>Testen der Lösung

* Navigieren Sie zu Ihrer Anwendung, und testen Sie die Aktionen für die Anmeldung, Registrierung und das Senden von Geld.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Codebeispiele zur Twilio-Integration](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2) finden Sie auf GitHub.  

- [Benutzerdefinierte Richtlinien in Azure Active Directory B2C](custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](custom-policy-get-started.md?tabs=applications)
