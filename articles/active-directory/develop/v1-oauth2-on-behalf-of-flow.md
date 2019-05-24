---
title: Azure Active Directory-Dienst-zu-Dienst-Authentifizierung unter Verwendung des OAuth 2.0-Spezifikationsentwurfs für „Im Auftrag von“ | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie HTTP-Nachrichten zum Implementieren der Dienst-zu-Dienst-Authentifizierung über den Im-Auftrag-von-Fluss von OAuth 2.0 verwenden.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2983980786fc706d103c0147a0776f2ff8c2d4f
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545466"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Dienst-zu-Dienst-Aufrufe unter Verwendung einer delegierten Benutzeridentität im Im-Auftrag-von-Fluss

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Mithilfe des Im-Auftrag-von-Flusses (On-Behalf-Of, OBO) von OAuth 2.0 kann eine Anwendung, die einen Dienst oder eine Web-API aufruft, eine Benutzerauthentifizierung für einen anderen Dienst oder eine andere Web-API übergeben. Der OBO-Fluss gibt die delegierte Benutzeridentität und Berechtigungen über die Anforderungskette weiter. Damit der Dienst auf der mittleren Ebene Authentifizierungsanforderungen an den nachgeschalteten Dienst stellen kann, muss für den Benutzer ein Zugriffstoken aus Azure Active Directory (Azure AD) abgesichert werden.

> [!IMPORTANT]
> Ab Mai 2018 kann für den Im-Auftrag-von-Fluss kein `id_token` mehr verwendet werden.  Single-Page-Webanwendungen müssen ein Zugriffstoken an einen vertraulichen Client der mittleren Ebene übergeben, um OBO-Flüsse auszuführen. Unter [Einschränkungen](#client-limitations) finden Sie ausführliche Informationen dazu, welche Clients Im-Auftrag-von-Aufrufe ausführen können.

## <a name="on-behalf-of-flow-diagram"></a>„Im Auftrag von“-Ablauf

Der OBO-Fluss beginnt, nachdem der Benutzer in einer Anwendung authentifiziert wurde, die den [Autorisierungscode-Vergabefluss von OAuth 2.0](v1-protocols-oauth-code.md) verwendet. Zu diesem Zeitpunkt sendet die Anwendung ein Zugriffstoken (Token A) an die Web-API der mittleren Ebene (API A), das die Ansprüche des Benutzers und die Genehmigung für den Zugriff auf API A enthält. Als Nächstes führt API A eine Authentifizierungsanforderung an die nachgeschaltete Web-API (API B) durch.

Diese Schritte bilden den On-Behalf-Of-Fluss: ![OAuth2.0 – On-Behalf-Of-Fluss](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Die Clientanwendung stellt mit Token A eine Anforderung an die API A.
1. Die API A wird beim Azure AD-Tokenausstellungs-Endpunkt authentifiziert und fordert ein Token für den Zugriff auf die API B an.
1. Der Azure AD-Tokenausstellungs-Endpunkt überprüft mit Token A die Anmeldeinformationen von API A und stellt das Zugriffstoken für API B aus (Token B).
1. Die Anforderung an die API B enthält das Token B im Autorisierungsheader.
1. API B gibt die Daten aus der gesicherten Ressource zurück.

>[!NOTE]
>Der Zielgruppenanspruch in einem Zugriffstoken wird zum Anfordern eines Tokens für einen nachgeschalteten Dienst verwendet und muss die ID des Diensts sein, der die OBO-Anforderung durchführt. Das Token muss außerdem mit dem globalen Signaturschlüssel von Azure Active Directory signiert sein. (Dies gilt standardmäßig für Anwendungen, die über **App-Registrierungen** im Portal registriert werden.)

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrieren der Anwendung und des Diensts in Azure AD

Registrieren Sie den Diensts auf der mittleren Ebene und die Clientanwendung in Azure AD.

### <a name="register-the-middle-tier-service"></a>Registrieren des Diensts auf der mittleren Ebene

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der oberen Leiste Ihr Konto aus, und wählen Sie in der Liste **Verzeichnis** einen Active Directory-Mandanten für Ihre Anwendung aus.
1. Wählen Sie im linken Bereich **Weitere Dienste** und dann **Azure Active Directory** aus.
1. Wählen Sie **App-Registrierungen** und dann **Neue Anwendungsregistrierung** aus.
1. Geben Sie einen Anzeigenamen für die Anwendung ein, und wählen Sie den Anwendungstyp aus.
    1. Legen Sie abhängig vom Anwendungstyp entweder die Anmelde-URL oder die Umleitungs-URL auf die Basis-URL fest.
    1. Klicken Sie auf **Erstellen**, um die Anwendung zu erstellen.
1. Generieren Sie einen geheimen Clientschlüssel, bevor Sie das Azure-Portal beenden.
   1. Wählen Sie im Azure-Portal Ihre Anwendung und dann **Einstellungen** aus.
   1. Wählen Sie im Menü „Einstellungen“ die Option **Schlüssel** aus, und fügen Sie einen Schlüssel mit einer Schlüsseldauer von einem oder zwei Jahren hinzu.
   1. Wenn Sie diese Seite speichern, zeigt das Azure-Portal den Schlüsselwert an. Kopieren Sie den Schlüsselwert, und speichern Sie ihn an einem sicheren Ort.

      > [!IMPORTANT]
      > Sie benötigen den Schlüssel zum Konfigurieren der Anwendungseinstellungen in Ihrer Implementierung. Dieser Wert wird nicht erneut angezeigt, und er kann auf keine andere Weise abgerufen werden. Notieren Sie ihn, wenn er im Azure-Portal angezeigt wird.

### <a name="register-the-client-application"></a>Registrieren der Clientanwendung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der oberen Leiste Ihr Konto aus, und wählen Sie in der Liste **Verzeichnis** einen Active Directory-Mandanten für Ihre Anwendung aus.
1. Wählen Sie im linken Bereich **Weitere Dienste** und dann **Azure Active Directory** aus.
1. Wählen Sie **App-Registrierungen** und dann **Neue Anwendungsregistrierung** aus.
1. Geben Sie einen Anzeigenamen für die Anwendung ein, und wählen Sie den Anwendungstyp aus.
   1. Legen Sie abhängig vom Anwendungstyp entweder die Anmelde-URL oder die Umleitungs-URL auf die Basis-URL fest.
   1. Klicken Sie auf **Erstellen**, um die Anwendung zu erstellen.
1. Konfigurieren Sie die Berechtigungen für Ihre Anwendung.
   1. Wählen Sie im Menü „Einstellungen“ den Abschnitt **Erforderliche Berechtigungen** und dann **Hinzufügen** und **Hiermit wählen Sie eine API aus** aus.
   1. Geben Sie den Namen des Diensts der mittleren Ebene in das Textfeld ein.
   1. Wählen Sie **Berechtigungen auswählen** und dann **Auf den Dienstnamen zugreifen** aus.

### <a name="configure-known-client-applications"></a>Konfigurieren der bekannten Clientanwendungen

In diesem Szenario muss der Dienst auf der mittleren Ebene die Benutzereinwilligung für den Zugriff auf die Downstream-API ohne Benutzereingriff erlangen. Die Zugriffsgewährung auf die Downstream-API muss vorab als Teil des Genehmigungsschrittes während der Authentifizierung angezeigt werden.

Führen Sie die folgenden Schritte aus, um die Registrierung der Client-App in Azure AD explizit an die Registrierung des Diensts auf der mittleren Ebene zu binden. Dadurch werden die erforderlichen Zustimmungen durch den Client und die mittlere Ebene in einem einzigen Dialog zusammengeführt.

1. Navigieren Sie zu der Registrierung auf der mittleren Ebene, und wählen Sie **Manifest** aus, um den Manifest-Editor zu öffnen.
1. Suchen Sie die Array-Eigenschaft `knownClientApplications`, und fügen Sie die Client-ID der Clientanwendung als Element hinzu.
1. Speichern Sie das Manifest durch Auswahl von **Speichern**.

## <a name="service-to-service-access-token-request"></a>Dienst-zu-Dienst-Zugriffstokenanforderung

Verwenden Sie zum Anfordern eines Zugriffstokens einen HTTP POST-Aufruf an den mandantenspezifischen Azure AD-Endpunkt unter Verwendung der folgenden Parameter:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Die Clientanwendung wird durch ein gemeinsames Geheimnis oder durch ein Zertifikat geschützt.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Erster Fall: Zugriffstokenanforderung mit einem gemeinsamen Geheimnis

Bei Verwendung eines gemeinsamen Geheimnisses enthält eine Dienst-zu-Dienst-Zugriffstokenanforderung die folgenden Parameter:

| Parameter |  | BESCHREIBUNG |
| --- | --- | --- |
| grant_type |required | Typ der Tokenanforderung Da eine OBO-Anforderung ein JSON Web Token (JWT) verwendet, muss der Wert **urn:ietf:params:oauth:grant-type:jwt-bearer** lauten. |
| Assertion |required | Der Wert des bei der Anforderung verwendeten Zugriffstoken. |
| client_id |required | Die dem aufrufenden Dienst während der Registrierung bei Azure AD zugewiesene App-ID. Wählen Sie zum Ermitteln der App-ID im Azure-Portal zuerst **Active Directory**, dann das Verzeichnis und schließlich den Anwendungsnamen aus. |
| client_secret |required | Schlüssel, der für den aufrufenden Dienst in Azure AD registriert ist. Dieser Wert sollte zum Zeitpunkt der Registrierung notiert worden sein. |
| resource |required | Der App-ID-URI des empfangenden Diensts (geschützte Ressource). Wählen Sie zum Ermitteln des App-ID-URIs im Azure-Portal **Active Directory** und dann das Verzeichnis aus. Wählen Sie den Namen der Anwendung, **Alle Einstellungen** und dann **Eigenschaften** aus. |
| requested_token_use |required | Gibt an, wie die Anforderung verarbeitet werden soll. Im Im-Auftrag-Fluss muss der Wert **on_behalf_of** lauten. |
| scope |required | Eine durch Leerzeichen getrennte Liste von Bereichen für die Tokenanforderung. Für OpenID Connect muss der Bereich **openid** angegeben werden.|

#### <a name="example"></a>Beispiel

Mit dem folgenden HTTP POST-Element wird ein Zugriffstoken für die Web-API https://graph.windows.net angefordert. Mit der `client_id` wird der Dienst identifiziert, der das Zugriffstoken anfordert.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Zweiter Fall: Zugriffstokenanforderung mit einem Zertifikat

Eine Dienst-zu-Dienst-Zugriffstokenanforderung mit einem Zertifikat enthält die folgenden Parameter:

| Parameter |  | BESCHREIBUNG |
| --- | --- | --- |
| grant_type |required | Typ der Tokenanforderung Da eine OBO-Anforderung ein JWT-Zugriffstoken verwendet, muss der Wert **urn:ietf:params:oauth:grant-type:jwt-bearer** lauten. |
| Assertion |required | Der Wert des bei der Anforderung verwendeten Tokens. |
| client_id |required | Die dem aufrufenden Dienst während der Registrierung bei Azure AD zugewiesene App-ID. Wählen Sie zum Ermitteln der App-ID im Azure-Portal zuerst **Active Directory**, dann das Verzeichnis und schließlich den Anwendungsnamen aus. |
| client_assertion_type |required |Der Wert muss `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` sein. |
| client_assertion |required | Ein JSON Web Token, das Sie benötigen, um das Zertifikat, das Sie als Anmeldeinformationen für Ihre Anwendung registriert haben, zu erstellen und zu signieren. Informationen zum Assertionsformat und zum Registrieren Ihres Zertifikats finden Sie unter [Zertifikatanmeldeinformationen](active-directory-certificate-credentials.md).|
| resource |required | Der App-ID-URI des empfangenden Diensts (geschützte Ressource). Wählen Sie zum Ermitteln des App-ID-URIs im Azure-Portal **Active Directory** und dann das Verzeichnis aus. Wählen Sie den Namen der Anwendung, **Alle Einstellungen** und dann **Eigenschaften** aus. |
| requested_token_use |required | Gibt an, wie die Anforderung verarbeitet werden soll. Im Im-Auftrag-Fluss muss der Wert **on_behalf_of** lauten. |
| scope |required | Eine durch Leerzeichen getrennte Liste von Bereichen für die Tokenanforderung. Für OpenID Connect muss der Bereich **openid** angegeben werden.|

Diese Parameter sind nahezu identisch mit der Anforderung mit einem gemeinsamen Geheimnis, außer dass `client_secret parameter` durch die beiden Parameter `client_assertion_type` und `client_assertion` ersetzt wird.

#### <a name="example"></a>Beispiel

Mit dem folgenden HTTP POST-Element wird ein Zugriffstoken für die Web-API https://graph.windows.net mit einem Zertifikat angefordert. Mit der `client_id` wird der Dienst identifiziert, der das Zugriffstoken anfordert.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Dienst-zu-Dienst-Zugriffstokenantwort

Eine erfolgreiche Antwort enthält eine JSON OAuth 2.0-Antwort mit den folgenden Parametern:

| Parameter | BESCHREIBUNG |
| --- | --- |
| token_type |Gibt den Wert des Tokentyps an. **Bearertoken**ist der einzige Typ, den Azure AD unterstützt. Weitere Informationen zu Bearertoken finden Sie unter [OAuth 2.0-Autorisierungsframework: Verwendung von Bearertoken (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Der durch das Token gewährte Zugriffsbereich. |
| expires_in |Gibt an, wie lange das Zugriffstoken gültig ist (in Sekunden). |
| expires_on |Die Uhrzeit, zu der das Zugriffstoken abläuft. Das Datum wird als Anzahl der Sekunden ab 1970-01-01T0:0:0Z UTC bis zur Ablaufzeit dargestellt. Dieser Wert wird verwendet, um die Lebensdauer von zwischengespeicherten Token zu bestimmen. |
| resource |Der App-ID-URI des empfangenden Diensts (geschützte Ressource). |
| access_token |Das angeforderte Zugriffstoken. Der aufrufende Dienst kann dieses Token verwenden, um die Authentifizierung für den empfangenden Dienst durchzuführen. |
| id_token |Das angeforderte ID-Token. Dieses Token kann vom aufrufenden Dienst dazu verwendet werden, die Identität des Benutzers zu überprüfen und eine Sitzung mit dem Benutzer zu beginnen. |
| refresh_token |Das Aktualisierungstoken für das angeforderte Zugriffstoken. Der aufrufende Dienst kann dieses Token verwenden, um nach Ablauf des aktuellen Zugriffstokens ein neues Zugriffstoken anzufordern. |

### <a name="success-response-example"></a>Beispiel für eine erfolgreiche Antwort

Das folgende Beispiel zeigt eine erfolgreiche Antwort auf eine Anforderung eines Zugriffstokens für die Web-API https://graph.windows.net.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Beispiel für eine fehlerhafte Antwort

Der Azure AD-Tokenendpunkt gibt eine Fehlerantwort zurück, wenn versucht wird, ein Zugriffstoken für eine Downstream-API abzurufen, für die eine Richtlinie für bedingten Zugriff (z.B. mehrstufige Authentifizierung) festgelegt ist. Der Dienst der mittleren Ebene zeigt diesen Fehler der Clientanwendung an, so dass diese der Richtlinie entsprechend die geeignete Benutzerinteraktion bieten kann.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Verwenden des Zugriffstokens für den Zugriff auf die gesicherte Ressource

Der Dienst der mittleren Ebene kann mit dem erhaltenen Zugriffstoken bei der nachgelagerten Web-API authentifizierte Anforderungen stellen. Hierfür wird das Token in den `Authorization`-Header eingetragen.

### <a name="example"></a>Beispiel

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>SAML-Assertionen, die mit einem OAuth 2.0-OBO-Fluss abgerufen wurden

Einige OAuth-basierte Webdienste benötigen Zugriff auf andere Webdienst-APIs, die SAML-Assertionen in nicht interaktiven Flüssen akzeptieren. Azure Active Directory kann es eine SAML-Assertion als Antwort auf einen Im-Namen-von-Fluss mit einem SAML-basierten Webdienst als Zielressource bereitstellen.

>[!NOTE]
>Dies ist eine nicht standardmäßige Erweiterung für den OAuth 2.0-Im-Namen-von-Fluss, der einer OAuth2-basierten Anwendung Zugriff auf Webdienst-API-Endpunkte ermöglicht, die SAML-Token nutzen.

> [!TIP]
> Wenn Sie einen geschützten SAML-Webdienst über eine Front-End-Anwendung aufrufen, können Sie einfach die API aufrufen und einen normalen interaktiven Authentifizierungsfluss initiieren, der die vorhandene Sitzung eines Benutzers verwendet. Sie müssen lediglich einen OBO-Fluss verwenden, wenn ein Dienst-zu-Dienst-Aufruf ein SAML-Token für die Bereitstellung des Benutzerkontexts erfordert.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Abrufen eines SAML-Tokens mithilfe einer OBO-Anforderung mit einem gemeinsamen Geheimnis

Eine Dienst-zu-Dienst-Anforderung für eine SAML-Assertion weist die folgenden Parameter auf:

| Parameter |  | BESCHREIBUNG |
| --- | --- | --- |
| grant_type |required | Typ der Tokenanforderung Bei Anforderungen mit einem JWT muss der Wert **urn:ietf:params:oauth:grant-type:jwt-bearer** lauten. |
| Assertion |required | Der Wert des bei der Anforderung verwendeten Zugriffstoken.|
| client_id |required | Die dem aufrufenden Dienst während der Registrierung bei Azure AD zugewiesene App-ID. Wählen Sie zum Ermitteln der App-ID im Azure-Portal zuerst **Active Directory**, dann das Verzeichnis und schließlich den Anwendungsnamen aus. |
| client_secret |required | Schlüssel, der für den aufrufenden Dienst in Azure AD registriert ist. Dieser Wert sollte zum Zeitpunkt der Registrierung notiert worden sein. |
| resource |required | Der App-ID-URI des empfangenden Diensts (geschützte Ressource). Dies ist die Ressource, die die Zielgruppe des SAML-Token darstellt. Wählen Sie zum Ermitteln des App-ID-URIs im Azure-Portal **Active Directory** und dann das Verzeichnis aus. Wählen Sie den Namen der Anwendung, **Alle Einstellungen** und dann **Eigenschaften** aus. |
| requested_token_use |required | Gibt an, wie die Anforderung verarbeitet werden soll. Im Im-Auftrag-Fluss muss der Wert **on_behalf_of** lauten. |
| requested_token_type | required | Gibt den Typ des angeforderten Token an. Abhängig von den Anforderungen der Ressource, auf die zugegriffen wird, kann der Wert **urn:Ietf:params:oauth:token-type:saml2** oder **urn:Ietf:params:oauth:token-type:saml1** lauten. |

Die Antwort enthält ein UTF8- und Base64url-codiertes SAML-Token.

- **SubjectConfirmationData für eine SAML-Assertion von einem OBO-Aufruf:** Wenn die Zielanwendung einen Empfängerwert in **SubjectConfirmationData** erfordert, muss als Wert in der Konfiguration der Ressourcenanwendung die Antwort-URL ohne Platzhalter festgelegt werden.
- **Der SubjectConfirmationData-Knoten:** Der Knoten darf kein **InResponseTo**-Attribut enthalten, da er nicht Teil einer SAML-Antwort ist. Die Anwendung, die das SAML-Token empfängt, muss die SAML-Assertion ohne **InResponseTo**-Attribut akzeptieren können.

- **Zustimmung:** Um ein SAML-Token zu empfangen, das Benutzerdaten zu einem OAuth-Flow enthält, muss Zustimmung erteilt worden sein. Informationen zu Berechtigungen und zum Erhalt der Zustimmung des Administrators finden Sie unter [Berechtigungen und Zustimmung im Azure Active Directory v1.0-Endpunkt](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent).

### <a name="response-with-saml-assertion"></a>Antwort mit SAML-Assertion

| Parameter | BESCHREIBUNG |
| --- | --- |
| token_type |Gibt den Wert des Tokentyps an. **Bearertoken**ist der einzige Typ, den Azure AD unterstützt. Weitere Informationen zu Bearertoken finden Sie unter [OAuth 2.0-Autorisierungsframework: Verwendung von Bearertoken (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Der durch das Token gewährte Zugriffsbereich. |
| expires_in |Gibt an, wie lange das Zugriffstoken gültig ist (in Sekunden). |
| expires_on |Die Uhrzeit, zu der das Zugriffstoken abläuft. Das Datum wird als Anzahl der Sekunden ab 1970-01-01T0:0:0Z UTC bis zur Ablaufzeit dargestellt. Dieser Wert wird verwendet, um die Lebensdauer von zwischengespeicherten Token zu bestimmen. |
| resource |Der App-ID-URI des empfangenden Diensts (geschützte Ressource). |
| access_token |Der Parameter, der die SAML-Assertion zurückgibt. |
| refresh_token |Das Aktualisierungstoken. Der aufrufende Dienst kann dieses Token verwenden, um nach Ablauf der aktuellen SAML-Assertion ein neues Zugriffstoken anzufordern. |

- token_type: Bearer
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- resource: `https://api.contoso.com`
- access_token: \<SAML-Assertion\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Aktualisierungstoken\>

## <a name="client-limitations"></a>Clienteinschränkungen

Öffentliche Clients mit Platzhaltern in Antwort-URLs können `id_token` nicht für OBO-Flüsse verwenden. Allerdings kann ein vertraulicher Client weiterhin **Zugriffstoken** einlösen, die über den Fluss für die implizite Genehmigung erworben wurden, selbst wenn der öffentliche Client einen Umleitungs-URI mit einem Platzhalter registriert hat.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das OAuth 2.0-Protokoll und eine andere Möglichkeit, Dienst-zu-Dienst-Authentifizierungen mit Clientanmeldedaten auszuführen:

* [Dienst-zu-Dienst-Authentifizierung mit Vergabe der OAuth 2.0-Client-Anmeldedaten in Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 in Azure AD](v1-protocols-oauth-code.md)
