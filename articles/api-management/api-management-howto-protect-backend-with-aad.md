---
title: Schützen des API-Back-Ends in API Management mithilfe von OAuth 2.0 und Azure AD
titleSuffix: Azure API Management
description: Hier erfahren Sie, wie Sie den Zugriff auf ein Web-API-Back-End in Azure API Management mit der OAuth 2.0-Benutzerautorisierung und Azure Active Directory sichern.
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: article
ms.date: 09/23/2020
ms.author: apimpm
ms.custom: contperf-fy21q1
ms.openlocfilehash: face4beab450e92be76b2bb90e45625e025de6ee
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027916"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-by-using-oauth-20-authorization-with-azure-ad"></a>Schützen eines Web-API-Back-Ends in Azure API Management mithilfe der OAuth 2.0-Autorisierung mit Azure AD 

In diesem Handbuch wird gezeigt, wie Sie Ihre [Azure API Management-Instanz](api-management-key-concepts.md) so konfigurieren, dass eine API über das [OAuth 2.0-Protokoll mit Azure Active Directory (Azure AD)](../active-directory/develop/active-directory-v2-protocols.md) geschützt ist. 

> [!NOTE]
> Diese Funktion ist in den Tarifen **Developer**, **Basic** und **Standard** und **Premium** von API Management verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie den Schritten in diesem Artikel folgen können, benötigen Sie folgende Komponenten:

- Eine API Management-Instanz
- Eine API, die veröffentlicht wird und die API Management-Instanz verwendet
- Einen Azure AD-Mandanten

## <a name="overview"></a>Übersicht

Im Folgenden finden Sie eine kurze Übersicht dieser Schritte:

1. Registrieren einer Anwendung (Back-End-App) in Azure AD, die die API darstellt
1. Registrieren einer anderen Anwendung (Client-App) in Azure AD, die eine Clientanwendung darstellt, aus der die API aufgerufen werden muss
1. Gewähren von Berechtigungen in Azure AD, damit die Client-App die Back-End-App aufrufen kann
1. Konfigurieren der Entwicklerkonsole zum Aufrufen der API unter Verwendung der OAuth 2.0-Benutzerautorisierung
1. Hinzufügen der **validate-jwt**-Richtlinie, um das OAuth-Token für jede eingehende Anforderung zu überprüfen

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrieren einer Anwendung in Azure AD, die die API darstellt

Um eine API mit Azure AD zu schützen, registrieren Sie zuerst eine Anwendung in Azure AD, die die API darstellt. In den folgenden Schritten wird das Azure-Portal zum Registrieren der Anwendung verwendet. Ausführliche Informationen zur App-Registrierung finden Sie unter [Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](../active-directory/develop/quickstart-configure-app-expose-web-apis.md)

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre Anwendung zu registrieren. Suchen Sie nach **App-Registrierungen**, und klicken Sie darauf.

1. Wählen Sie **Neue Registrierung** aus. 

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein:

   - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird, beispielsweise *Back-End-App*. 
   - Wählen Sie im Abschnitt **Unterstützte Kontotypen** eine Option aus,die Ihrem Szenario entspricht. 

1. Lassen Sie den Abschnitt **Umleitungs-URI** leer.

1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen. 

1. Suchen Sie auf der Seite **Übersicht** den Wert von **Anwendungsclient-ID** und notieren Sie ihn zur späteren Verwendung.

1. Wählen Sie **Eine API verfügbar machen** aus, und legen Sie den **Anwendungs-ID-URI** auf den Standardwert fest. Notieren Sie sich diesen Wert zur späteren Verwendung.

1. Wählen Sie die Schaltfläche **Bereich hinzufügen** aus, um die Seite **Hinzufügen eines Bereichs** anzuzeigen. Erstellen Sie dann einen neuen Bereich, der von der API unterstützt wird (z. B. `Files.Read`).

1. Wählen Sie die Schaltfläche **Bereich hinzufügen** aus, um den Bereich zu erstellen. Wiederholen Sie diesen Schritt, um alle von ihrer API unterstützten Bereiche hinzuzufügen.

1. Nachdem Sie die Bereiche erstellt haben, notieren Sie diese, um sie in einem nachfolgenden Schritt zu verwenden. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrieren einer anderen Anwendung in Azure AD, die eine Clientanwendung darstellt

Jede Clientanwendung, aus der die API aufgerufen wird, muss in Azure AD als Anwendung registriert werden. In diesem Beispiel wird die **Entwicklerkonsole** im API Management-Entwicklerportal als Clientanwendung verwendet. 

So registrieren Sie eine weitere Anwendung in Azure AD, die die Entwicklerkonsole darstellt

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre Anwendung zu registrieren.

1. Suchen Sie nach **App-Registrierungen**, und klicken Sie darauf.

1. Wählen Sie **Neue Registrierung** aus.

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein:

   - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird, beispielsweise *Client-App*. 
   - Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis (Beliebiges Azure AD-Verzeichnis – mehrinstanzenfähig)** aus. 

1. Wählen Sie im Abschnitt **Umleitungs-URI** die Option `Web` aus, und lassen Sie das URL-Feld vorerst leer.

1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen. 

1. Suchen Sie auf der Seite **Übersicht** den Wert von **Anwendungsclient-ID** und notieren Sie ihn zur späteren Verwendung.

1. Erstellen Sie einen geheimen Clientschlüssel für diese Anwendung, der in einem späteren Schritt von ihr verwendet wird.

   1. Wählen Sie in der Liste der Seiten für Ihre Client-App die Option **Zertifikate und Geheimnisse** aus, und klicken Sie auf **Neuer geheimer Clientschlüssel**.

   1. Geben Sie unter **Geheimen Clientschlüssel hinzufügen** eine **Beschreibung** ein. Legen Sie ein Ablaufdatum für den Schlüssel fest, und klicken Sie auf **Hinzufügen**.

Nachdem Sie das Geheimnis erstellt haben, notieren Sie sich den Schlüsselwert, um ihn in einem nachfolgenden Schritt zu verwenden. 

## <a name="grant-permissions-in-azure-ad"></a>Erteilen von Berechtigungen in Azure AD

Nachdem nun zwei Anwendungen registriert sind, die die API und die Entwicklerkonsole darstellen, gewähren Sie Berechtigungen, damit die Client-App die Back-End-App aufrufen kann.  

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihrer Clientanwendung Berechtigungen zu erteilen. Suchen Sie nach **App-Registrierungen**, und klicken Sie darauf.

1. Wählen Sie Ihre Client-App aus. Wählen Sie dann in der Liste der Seiten für die App **API-Berechtigungen** aus.

1. Wählen Sie **Berechtigung hinzufügen** aus.

1. Wählen Sie unter **API auswählen** die Option **Meine APIs** aus, suchen Sie dann die Back-End-App, und wählen Sie diese aus.

1. Wählen Sie unter **Delegierte Berechtigungen** die geeigneten Berechtigungen für Ihre Back-End-App aus, und wählen Sie dann **Berechtigungen hinzufügen** aus.

1. Wählen Sie optional auf der Seite **API-Berechtigungen** die Option **Administratorzustimmung für \<your-tenant-name> erteilen** aus, um die Zustimmung im Auftrag aller Benutzer in diesem Verzeichnis zu erteilen. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Aktivieren der OAuth 2.0-Benutzerautorisierung in der Entwicklerkonsole

An diesem Punkt haben Sie die Anwendungen in Azure AD erstellt und die erforderlichen Berechtigungen erteilt, damit die Client-App die Back-End-App aufrufen kann. 

In diesem Beispiel ist die Entwicklerkonsole die Client-App. In den folgenden Schritten wird beschrieben, wie OAuth 2.0-Benutzerautorisierung in der Entwicklerkonsole aktiviert wird. 

1. Navigieren Sie im Azure-Portal zu Ihrer API Management-Instanz.

1. Klicken Sie auf **OAuth 2.0** > **Hinzufügen**.

1. Geben Sie den **Anzeigenamen** und eine **Beschreibung** an.

1. Geben Sie als **URL für Clientregistrierungsseite** einen Platzhalterwert ein, beispielsweise `http://localhost`. **URL für Clientregistrierungsseite** verweist auf eine Seite, die Benutzer zum Erstellen und Konfigurieren eigener Konten für OAuth 2.0-Anbieter nutzen können, die dies unterstützen. In diesem Beispiel werden von Benutzern keine eigenen Konten erstellt und konfiguriert, daher verwenden Sie stattdessen einen Platzhalter.

1. Wählen Sie unter **Autorisierungszuweisungstypen** die Option **Autorisierungscode**.

1. Geben Sie die **URL für Autorisierungsendpunkt** und die **URL für Tokenendpunkt** ein. Rufen Sie diese Werte von der Seite **Endpunkte** in Ihrem Azure AD-Mandanten ab. Navigieren Sie erneut zur Seite **App-Registrierung**, und klicken Sie auf **Endpunkte**.


1. Kopieren Sie den **OAuth 2.0-Autorisierungsendpunkt**, und fügen Sie ihn in das Textfeld **URL für Autorisierungsendpunkt** ein. Wählen Sie unter „Autorisierungsanforderungsmethode“ die Option **POST** aus.

1. Kopieren Sie den **OAuth 2.0-Token-Endpunkt**, und fügen Sie ihn in das Textfeld **URL für Tokenendpunkt** ein. 

   >[!IMPORTANT]
   > Verwenden Sie entweder **v1**- oder **v2**-Endpunkte. Abhängig von der gewählten Version sieht der nachfolgende Schritt jedoch etwas anders aus. Es wird die Verwendung von v2-Endpunkten empfohlen. 

1. Wenn Sie **v1**-Endpunkte verwenden, fügen Sie einen body-Parameter mit dem Namen **resource** hinzu. Verwenden Sie als Wert für diesen Parameter die **Anwendungs-ID** der Back-End-App. 

1. Wenn Sie **v2**-Endpunkte verwenden, verwenden Sie im Feld **Standardbereich** den Bereich, den Sie für die Back-End-App erstellt haben. Legen Sie auch den Wert für die [`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute)-Eigenschaft in Ihrem [Anwendungsmanifest](../active-directory/develop/reference-app-manifest.md) auf `2` fest.

1. Geben Sie anschließend Clientanmeldeinformationen an. Diese sind die Anmeldeinformationen für die Client-App.

1. Verwenden Sie für **Client-ID** die **Anwendungs-ID** der Client-App.

1. Verwenden Sie für **Clientgeheimnis** den Schlüssel, den Sie zuvor für die Client-App erstellt haben. 

1. Unmittelbar hinter dem Clientgeheimnis folgt die Umleitungs-URL (**redirect_url**) für den Typ der Gewährung für Autorisierungscode. Notieren Sie sich diese URL.

1. Klicken Sie auf **Erstellen**.

1. Wechseln Sie zurück zu Ihrer Client-App-Registrierung in Azure Active Directory, und wählen Sie **Authentifizierung** aus.

1. Klicken Sie unter **Plattformkonfigurationen** auf **Plattform hinzufügen**, wählen Sie den Typ **Web** aus, fügen Sie die **redirect_url** unter **Umleitungs-URI** ein, und klicken Sie dann auf die Schaltfläche **Konfigurieren**, um zu speichern.

Nachdem Sie nun einen OAuth 2.0-Autorisierungsserver konfiguriert haben, kann die Entwicklerkonsole Zugriffstoken von Azure AD abrufen. 

Im nächsten Schritt aktivieren Sie die OAuth 2.0-Benutzerautorisierung für Ihre API. Dadurch weiß die Entwicklerkonsole, dass sie im Namen des Benutzers ein Zugriffstoken abrufen muss, bevor Aufrufe an die API erfolgen.

1. Navigieren Sie zu Ihrer API Management-Instanz und dann zu **APIs**.

1. Wählen Sie die API aus, die Sie schützen möchten. Beispiel: `Echo API`.

1. Wechseln Sie zu **Einstellungen**.

1. Wählen Sie unter **Sicherheit** die Option **OAuth 2.0** aus, und wählen Sie den OAuth 2.0-Server aus, den Sie zuvor konfiguriert haben. 

1. Wählen Sie **Speichern** aus.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Erfolgreiches Aufrufen der API aus dem Entwicklerportal

> [!NOTE]
> Dieser Abschnitt gilt nicht für den **Verbrauchstarif**, der keine Unterstützung für das Entwicklerportal bietet.

Nachdem nun die OAuth 2.0-Benutzerautorisierung für Ihre API aktiviert ist, ruft die Entwicklerkonsole im Namen des Benutzers ein Zugriffstoken ab, bevor die API aufgerufen wird.

1. Navigieren Sie im Entwicklerportal zu einem beliebigen Vorgang unter der API, und wählen Sie **Ausprobieren** aus. Dadurch wird die Entwicklerkonsole angezeigt.

1. Beachten Sie ein neues Element im Abschnitt **Autorisierung**, das dem soeben hinzugefügten Autorisierungsserver entspricht.

1. Wählen Sie in der Dropdownliste „Autorisierung“ die Option **Autorisierungscode** aus. Sie werden dann aufgefordert, sich beim Azure AD-Mandanten anzumelden. Wenn Sie bereits mit dem Konto angemeldet sind, werden Sie möglicherweise nicht dazu aufgefordert.

1. Nach der erfolgreichen Anmeldung wird der Anforderung ein `Authorization`-Header mit einem Zugriffstoken von Azure AD hinzugefügt. Hier sehen Sie ein Beispieltoken (Base64-codiert):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. Wählen Sie **Senden** aus, um die API erfolgreich aufzurufen.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurieren einer JWT-Überprüfungsrichtlinie zur Vorautorisierung von Anforderungen

Wenn an diesem Punkt ein Benutzer versucht, einen Aufruf über die Entwicklerkonsole auszuführen, wird er zum Anmelden aufgefordert. Die Entwicklerkonsole ruft ein Zugriffstoken im Auftrag des Benutzers ab und schließt das Token in die an die API gerichtete Anforderung ein.

Was aber passiert, wenn jemand Ihre API ohne Token oder mit einem ungültigen Token aufruft? Wenn z. B. versucht wird, die API ohne den `Authorization`-Header aufzurufen, wird der Aufruf dennoch durchgeleitet. Dies liegt daran, dass API Management das Zugriffstoken an diesem Punkt nicht überprüft. API Management übergibt den `Authorization`-Header einfach an die Back-End-API.

Verwenden Sie die Richtlinie [JWT überprüfen](./api-management-access-restriction-policies.md#ValidateJWT), um Anforderungen in API Management vorab zu autorisieren, indem die Zugriffstoken für jede eingehende Anforderung überprüft werden. Wenn für eine Anforderung kein gültiges Token vorliegt, wird sie von API Management blockiert. Fügen Sie beispielsweise die folgende Richtlinie zum Richtlinienabschnitt `<inbound>` der `Echo API` hinzu. Sie überprüft den Zielgruppenanspruch in einem Zugriffstoken und gibt eine Fehlermeldung zurück, wenn das Token nicht gültig ist. Informationen zum Konfigurieren von Richtlinien finden Sie unter [How to set or edit Azure API Management policies](./set-edit-policies.md) (Festlegen oder Bearbeiten von Azure API Management-Richtlinien).


```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

> [!NOTE]
> Diese `openid-config`-URL entspricht dem v1-Endpunkt. Verwenden Sie für den `openid-config`-v2-Endpunkt die folgende URL:
>
> `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`.

## <a name="build-an-application-to-call-the-api"></a>Erstellen einer Anwendung zum Aufrufen der API

In diesem Handbuch wird die Entwicklerkonsole in API Management als Beispielclientanwendung verwendet, um die durch OAuth 2.0 geschützte `Echo API` aufzurufen. Weitere Informationen zum Erstellen einer Anwendung und zum Implementieren von OAuth 2.0 finden Sie unter [Azure Active Directory-Codebeispiele](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Azure Active Directory und OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md)
- Hier finden Sie weitere [Videos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) zu API Management.
- Weitere Methoden zum Sichern Ihres Back-End-Diensts finden Sie unter [Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung in Azure API Management](./api-management-howto-mutual-certificates.md).
- [Erstellen einer API Management-Dienstinstanz](./get-started-create-service-instance.md)
- [Verwalten Ihrer ersten API](./import-and-publish.md)
