---
title: Schützen einer API über OAuth 2.0 mit Azure Active Directory und API Management | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein Web-API-Back-End mit Azure Active Directory und API Management schützen.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 73dd46d1ca0a20748d7a3a7838c499f0c659253d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241674"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Schützen einer API über OAuth 2.0 mit Azure Active Directory und API Management

In diesem Handbuch wird gezeigt, wie Sie Ihre Azure API Management-Instanz so konfigurieren, dass eine API über das OAuth 2.0-Protokoll mit Azure Active Directory (Azure AD) geschützt ist. 

## <a name="prerequisites"></a>Voraussetzungen
Damit Sie den Schritten in diesem Artikel folgen können, benötigen Sie folgende Komponenten:
* Eine API Management-Instanz
* Eine API, die veröffentlicht wird und die API Management-Instanz verwendet
* Einen Azure AD-Mandanten

## <a name="overview"></a>Übersicht

Es folgt eine kurze Übersicht über die Schritte:

1. Registrieren einer Anwendung (Back-End-App) in Azure AD, die die API darstellt
2. Registrieren einer anderen Anwendung (Client-App) in Azure AD, die eine Clientanwendung darstellt, aus der die API aufgerufen werden muss
3. Gewähren von Berechtigungen in Azure AD, damit die Client-App die Back-End-App aufrufen kann
4. Konfigurieren der Entwicklerkonsole für die Verwendung der OAuth 2.0-Benutzerautorisierung
5. Hinzufügen der **validate-jwt**-Richtlinie, um das OAuth-Token für jede eingehende Anforderung zu überprüfen

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrieren einer Anwendung in Azure AD, die die API darstellt

Um eine API mit Azure AD zu schützen, besteht der erste Schritt darin, eine Anwendung in Azure AD zu registrieren, die die API darstellt. 

1. Navigieren Sie zur Seite [Azure-Portal – App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908). 

2. Wählen Sie **Neue Registrierung** aus. 

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein: 
    - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (beispielsweise `backend-app`). 
    - Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis** aus. 

1. Lassen Sie den Abschnitt **Umleitungs-URI** erst einmal leer.

1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen. 

1. Suchen Sie auf der Seite **Übersicht** den Wert von **Anwendungsclient-ID** und notieren Sie ihn zur späteren Verwendung.

Wenn die Anwendung erstellt wird, notieren Sie sich die **Anwendungs-ID**, um sie in einem späteren Schritt verwenden zu können. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrieren einer anderen Anwendung in Azure AD, die eine Clientanwendung darstellt

Jede Clientanwendung, aus der die API aufgerufen wird, muss auch in Azure AD als eine Anwendung registriert werden. In diesem Beispiel wird die Entwicklerkonsole im API Management-Entwicklerportal als Beispielclientanwendung verwendet. So registrieren Sie eine weitere Anwendung in Azure AD, die die Entwicklerkonsole darstellt:

1. Navigieren Sie zur Seite [Azure-Portal – App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908). 

1. Wählen Sie **Neue Registrierung** aus.

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein: 
    - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (beispielsweise `client-app`). 
    - Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis** aus. 

1. Wählen Sie im Abschnitt **Umleitungs-URI** die Option `Web` aus, und geben Sie die URL `https://contoso5.portal.azure-api.net/signin` ein.

1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen. 

1. Suchen Sie auf der Seite **Übersicht** den Wert von **Anwendungsclient-ID** und notieren Sie ihn zur späteren Verwendung.

Erstellen Sie nun einen geheimen Clientschlüssel für diese Anwendung, der in einem späteren Schritt verwendet wird.

1. Wählen Sie in der Liste der Seiten für Ihre Client-App die Option **Zertifikate und Geheimnisse** aus, und klicken Sie auf **Neuer geheimer Clientschlüssel**.

2. Geben Sie unter **Geheimen Clientschlüssel hinzufügen** eine **Beschreibung** ein. Legen Sie ein Ablaufdatum für den Schlüssel fest, und klicken Sie auf **Hinzufügen**.

Notieren Sie sich den Schlüsselwert. 

## <a name="grant-permissions-in-azure-ad"></a>Erteilen von Berechtigungen in Azure AD

Nachdem nun zwei Anwendungen registriert sind, die die API und die Entwicklerkonsole darstellen, müssen Sie Berechtigungen gewähren, damit die Client-App die Back-End-App aufrufen kann.  

1. Navigieren Sie zu **App-Registrierungen**. 

2. Wählen Sie `client-app` aus, und wechseln Sie in der Liste mit den Seiten für die App zu **API-Berechtigungen**.

3. Wählen Sie **Berechtigung hinzufügen** aus.

4. Suchen Sie unter **API auswählen** nach `backend-app`, und wählen Sie die Option aus.

5. Wählen Sie unter **Delegierte Berechtigungen** die geeigneten Berechtigungen für `backend-app` aus.

6. Wählen Sie **Berechtigungen hinzufügen** aus. 

> [!NOTE]
> Wenn **Azure Active Directory** unter den Berechtigungen für andere Programme nicht aufgeführt ist, klicken Sie auf **Hinzufügen**, und fügen Sie die Option aus der Liste hinzu.

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Aktivieren der OAuth 2.0-Benutzerautorisierung in der Entwicklerkonsole

An diesem Punkt haben Sie die Anwendungen in Azure AD erstellt und die erforderlichen Berechtigungen erteilt, damit die Client-App die Back-End-App aufrufen kann. 

In diesem Beispiel ist die Entwicklerkonsole die Client-App. In den folgenden Schritten wird beschrieben, wie OAuth 2.0-Benutzerautorisierung in der Entwicklerkonsole aktiviert wird. 

1. Navigieren Sie im Azure-Portal zu Ihrer API Management-Instanz.

2. Klicken Sie auf **OAuth 2.0** > **Hinzufügen**.

3. Geben Sie den **Anzeigenamen** und eine **Beschreibung** an.

4. Geben Sie als **URL für Clientregistrierungsseite** einen Platzhalterwert ein, beispielsweise `http://localhost`. **URL für Clientregistrierungsseite** verweist auf die Seite, die Benutzer zum Erstellen und Konfigurieren eigener Konten für OAuth 2.0-Anbieter nutzen können, die dies unterstützen. In diesem Beispiel werden von Benutzern keine eigenen Konten erstellt und konfiguriert, daher verwenden Sie stattdessen einen Platzhalter.

5. Wählen Sie unter **Autorisierungszuweisungstypen** die Option **Autorisierungscode**.

6. Geben Sie die **URL für Autorisierungsendpunkt** und die **URL für Tokenendpunkt** ein. Rufen Sie diese Werte von der Seite **Endpunkte** in Ihrem Azure AD-Mandanten ab. Navigieren Sie erneut zur Seite **App-Registrierung**, und klicken Sie auf **Endpunkte**.

    >[!NOTE]
    > Verwenden Sie hier die **v1**-Endpunkte.

7. Kopieren Sie den **OAuth 2.0-Autorisierungsendpunkt**, und fügen Sie ihn in das Textfeld **URL für Autorisierungsendpunkt** ein.

8. Kopieren Sie den **OAuth 2.0-Token-Endpunkt**, und fügen Sie ihn in das Textfeld **URL für Tokenendpunkt** ein. Fügen Sie zusätzlich zum Tokenendpunkt den Textkörperparameter **resource** hinzu. Verwenden Sie als Wert für diesen Parameter die **Anwendungs-ID** für die Back-End-App.

9. Geben Sie anschließend Clientanmeldeinformationen an. Diese sind die Anmeldeinformationen für die Client-App.

10. Verwenden Sie für **Client-ID** die **Anwendungs-ID** für die Client-App.

11. Verwenden Sie für **Clientgeheimnis** den Schlüssel, den Sie zuvor für die Client-App erstellt haben. 

12. Unmittelbar hinter dem Clientgeheimnis folgt die Umleitungs-URL (**redirect_url**) für den Typ der Gewährung für Autorisierungscode. Notieren Sie sich diese URL.

13. Klicken Sie auf **Erstellen**.

14. Navigieren Sie zurück zur Seite **Einstellungen** Ihrer Client-App.

15. Klicken Sie auf **Antwort-URLs**, und fügen Sie den Wert für **redirect_url** in der ersten Zeile ein. In diesem Beispiel haben Sie `https://localhost` durch die URL in der ersten Zeile ersetzt.  

Nachdem Sie nun einen OAuth 2.0-Autorisierungsserver konfiguriert haben, kann die Entwicklerkonsole Zugriffstoken von Azure AD abrufen. 

Im nächsten Schritt aktivieren Sie die OAuth 2.0-Benutzerautorisierung für Ihre API. Dadurch weiß die Entwicklerkonsole, dass sie im Namen des Benutzers ein Zugriffstoken abrufen muss, bevor Aufrufe an die API erfolgen.

1. Navigieren Sie zu Ihrer API Management-Instanz und dann zu **APIs**.

2. Wählen Sie die API aus, die Sie schützen möchten. In diesem Beispiel wird die `Echo API` verwendet.

3. Wechseln Sie zu **Einstellungen**.

4. Wählen Sie unter **Sicherheit** die Option **OAuth 2.0** aus, und wählen Sie den OAuth 2.0-Server aus, den Sie zuvor konfiguriert haben. 

5. Wählen Sie **Speichern** aus.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Erfolgreiches Aufrufen der API aus dem Entwicklerportal

> [!NOTE]
> Dieser Abschnitt gilt nicht für den **Verbrauchstarif**, der keine Unterstützung für das Entwicklerportal bietet.

Nachdem nun die OAuth 2.0-Benutzerautorisierung für die `Echo API` aktiviert ist, ruft die Entwicklerkonsole im Namen des Benutzers ein Zugriffstoken ab, bevor die API aufgerufen wird.

1. Navigieren Sie im Entwicklerportal zu einem beliebigen Vorgang unter der `Echo API`, und klicken Sie auf **Ausprobieren**. Dadurch wird die Entwicklerkonsole angezeigt.

2. Beachten Sie ein neues Element im Abschnitt **Autorisierung**, das dem soeben hinzugefügten Autorisierungsserver entspricht.

3. Wählen Sie in der Dropdownliste „Autorisierung“ die Option **Autorisierungscode** aus. Sie werden dann aufgefordert, sich beim Azure AD-Mandanten anzumelden. Wenn Sie bereits mit dem Konto angemeldet sind, werden Sie möglicherweise nicht dazu aufgefordert.

4. Nach der erfolgreichen Anmeldung wird der Anforderung ein `Authorization`-Header mit einem Zugriffstoken von Azure AD hinzugefügt. Hier sehen Sie ein Beispieltoken (Base64-codiert):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Klicken Sie auf **Senden**, und Sie können die API aufrufen.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurieren einer JWT-Überprüfungsrichtlinie zur Vorautorisierung von Anforderungen

Wenn an diesem Punkt ein Benutzer versucht, einen Aufruf über die Entwicklerkonsole auszuführen, wird er zum Anmelden aufgefordert. Die Entwicklerkonsole ruft im Namen des Benutzers ein Zugriffstoken ab.

Aber was passiert, wenn jemand die API ohne Token oder mit einem ungültigen Token aufruft? Sie können beispielsweise die API trotzdem aufrufen, auch wenn Sie den `Authorization`-Header löschen. Dies liegt daran, dass API Management das Zugriffstoken an diesem Punkt nicht überprüft. API Management übergibt den `Authorization`-Header einfach an die Back-End-API.

Sie können die Richtlinie [JWT überprüfen](api-management-access-restriction-policies.md#ValidateJWT) verwenden, um Anforderungen in API Management vorab zu autorisieren, indem die Zugriffstoken für jede eingehende Anforderung überprüft werden. Wenn für eine Anforderung kein gültiges Token vorliegt, wird sie von API Management blockiert. Sie können beispielsweise die folgende Richtlinie zum Richtlinienabschnitt `<inbound>` der `Echo API` hinzufügen. Sie überprüft den Zielgruppenanspruch in einem Zugriffstoken und gibt eine Fehlermeldung zurück, wenn das Token nicht gültig ist. Informationen zum Konfigurieren von Richtlinien finden Sie unter [How to set or edit Azure API Management policies](set-edit-policies.md) (Festlegen oder Bearbeiten von Azure API Management-Richtlinien).

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

## <a name="build-an-application-to-call-the-api"></a>Erstellen einer Anwendung zum Aufrufen der API

In diesem Handbuch wird die Entwicklerkonsole in API Management als Beispielclientanwendung verwendet, um die durch OAuth 2.0 geschützte `Echo API` aufzurufen. Weitere Informationen zum Erstellen einer Anwendung und zum Implementieren von OAuth 2.0 finden Sie unter [Azure Active Directory-Codebeispiele](../active-directory/develop/sample-v1-code.md).

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu [Azure Active Directory und OAuth 2.0](../active-directory/develop/authentication-scenarios.md)
* Hier finden Sie weitere [Videos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) zu API Management.
* Weitere Methoden zum Sichern Ihres Back-End-Diensts finden Sie unter [Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung in Azure API Management](api-management-howto-mutual-certificates.md).

* [Erstellen einer API Management-Dienstinstanz](get-started-create-service-instance.md)

* [Verwalten Ihrer ersten API](import-and-publish.md)
