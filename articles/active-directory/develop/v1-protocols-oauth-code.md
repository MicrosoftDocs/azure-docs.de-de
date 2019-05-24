---
title: Grundlegendes zum Codefluss der OAuth 2.0-Autorisierung in Azure AD
description: Dieser Artikel beschreibt, wie Sie HTTP-Nachrichten zum Autorisieren des Zugriffs auf Webanwendungen und Web-APIs in Ihrem Mandanten mithilfe von Azure Active Directory und OAuth 2.0 verwenden.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e05d79773cfd2ebae8047e75d41684de9101787a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962181"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autorisieren des Zugriffs auf Azure Active Directory-Webanwendungen mit dem Flow zum Erteilen des OAuth 2.0-Codes

Azure Active Directory (Azure AD) verwendet OAuth 2.0, um den Zugriff auf Webanwendungen und Web-APIs in Ihrem Azure AD-Mandanten zu autorisieren. Diese sprachunabhängige Anleitung beschreibt das Senden und Empfangen von HTTP-Nachrichten ohne Verwendung unserer [Open Source-Bibliotheken](active-directory-authentication-libraries.md).

Der OAuth 2.0-Autorisierungscodefluss wird in [Abschnitt 4.1 der OAuth 2.0-Spezifikation](https://tools.ietf.org/html/rfc6749#section-4.1)beschrieben. Er wird zur Authentifizierung und Autorisierung bei den meisten Anwendungstypen verwendet, einschließlich Web-Apps und nativ installierten Apps.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>OAuth 2.0-Autorisierungsfluss

Allgemein sieht der gesamte Autorisierungsfluss für eine Anwendung etwa wie folgt aus:

![OAuth-Autorisierungscodefluss](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Anfordern eines Autorisierungscodes

Der Autorisierungscodefluss beginnt damit, dass der Client den Benutzer auf den `/authorize` -Endpunkt leitet. In dieser Anforderung gibt der Client die Berechtigungen an, die er vom Benutzer abrufen muss. Sie können den OAuth 2.0-Autorisierungsendpunkt für Ihren Mandanten abrufen, indem Sie im Azure-Portal **App-Registrierungen > Endpunkte** auswählen.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parameter |  | BESCHREIBUNG |
| --- | --- | --- |
| tenant |required |Mit dem `{tenant}` -Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Die zulässigen Werte sind Mandantenbezeichner (also etwa `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` oder für mandantenunabhängige Token `common`). |
| client_id |required |Die Anwendungs-ID, die Ihrer App zugewiesen wird, wenn Sie sie bei Azure AD registrieren. Diese finden Sie im Azure-Portal. Klicken Sie auf der Randleiste mit den Diensten auf **Azure Active Directory** und auf **App-Registrierungen**. Wählen Sie dann die Anwendung. |
| response_type |required |Muss `code` für den Autorisierungscodefluss enthalten. |
| redirect_uri |empfohlen |Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. Für native und mobile Apps sollten Sie den Standardwert `urn:ietf:wg:oauth:2.0:oob` verwenden. |
| response_mode |optional |Gibt die Methode an, die zum Senden des resultierenden Tokens zurück an Ihre App verwendet werden soll. Kann `query`, `fragment` oder `form_post` sein. `query` gibt den Code als ein Abfragezeichenfolgen-Parameter in der Umleitungs-URI an. Wenn Sie ein ID-Token mit dem impliziten Flow anfordern, können Sie `query` nicht gemäß [OpenID-Spezifikation](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations) verwenden. Wenn Sie lediglich den Code anfordern, können Sie `query`, `fragment` oder `form_post` verwenden. `form_post` führt ein POST-Element mit dem Code zu Ihrer Umleitungs-URI aus. Der Standardwert ist `query` für einen Codefluss.  |
| state |empfohlen |Ein in der Anforderung enthaltener Wert, der ebenfalls in der Tokenantwort zurückgegeben wird. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um [websiteübergreifende Anforderungsfälschungsangriffe zu verhindern](https://tools.ietf.org/html/rfc6749#section-10.12). Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| resource | empfohlen |Der App-ID-URI der Ziel-Web-API (geschützte Ressource). Klicken Sie zum Ermitteln des App-ID-URI im Azure-Portal auf **Azure Active Directory** und auf **App-Registrierungen**. Öffnen Sie anschließend die Seite **Einstellungen**, und klicken Sie auf **Eigenschaften**. Es kann sich auch um eine externe Ressource wie `https://graph.microsoft.com` handeln. Dieser Parameter muss in einer der Autorisierungs- oder Tokenanforderungen vorhanden sein, um sicherzustellen, dass weniger Authentifizierungsanforderungen diesen in die Autorisierungsanforderung platzieren und so die Zustimmung des Benutzers empfangen wird. |
| scope | **ignoriert** | Bei Azure AD v1-Apps müssen Bereiche im Azure-Portal unter **Einstellungen** der Anwendungen sowie **Erforderliche Berechtigungen** statisch konfiguriert werden. |
| prompt |optional |Geben Sie den Typ der erforderlichen Benutzerinteraktion an.<p> Gültige Werte sind: <p> *login:* Der Benutzer sollte zum erneuten Authentifizieren aufgefordert werden. <p> *select_account:* Der Benutzer wird aufgefordert, ein Konto auszuwählen. Dabei wird das einmalige Anmelden unterbrochen. Der Benutzer kann ein bereits vorhandenes und angemeldetes Konto auswählen, seine Anmeldeinformationen für ein gespeichertes Konto eingeben oder ein ganz anderes Konto verwenden. <p> *consent:* Die Benutzerzustimmung wurde erteilt, muss aber aktualisiert werden. Der Benutzer sollte zur Erteilung der Zustimmung aufgefordert werden. <p> *admin_consent:* Ein Administrator sollte aufgefordert werden, die Zustimmung im Namen aller Benutzer der Organisation zu erteilen. |
| login_hint |optional |Dieser Wert kann verwendet werden, um das Feld für den Benutzernamen oder die E-Mail-Adresse auf der Anmeldeseite vorab für den Benutzer auszufüllen, wenn dessen Benutzername im Vorfeld bekannt ist. Apps verwenden diesen Parameter häufig für die erneute Authentifizierung, nachdem sie den Benutzernamen aus einer vorherigen Anmeldung mithilfe des Anspruchs `preferred_username` extrahiert haben. |
| domain_hint |optional |Stellt einen Hinweis zum Mandanten oder zur Domäne bereit, die der Benutzer zum Anmelden verwenden sollte. Der Wert von „domain_hint“ ist eine registrierte Domäne für den Mandanten. Wenn der Mandant mit einem lokalen Verzeichnis verbunden ist, führt AAD eine Umleitung an den angegebenen Mandantenverbundserver durch. |
| code_challenge_method | empfohlen    | Die Methode wird zum Codieren von `code_verifier` für den `code_challenge`-Parameter verwendet. Kann `plain` oder `S256` sein. Wenn ausgeschlossen, wird angenommen, dass `code_challenge` Klartext ist, wenn `code_challenge` enthalten ist. Azure AAD 1.0 unterstützt sowohl `plain` als auch `S256`. Weitere Informationen finden Sie unter [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | empfohlen    | Wird verwendet, um die Gewährung von Autorisierungscodes über Proof Key for Code Exchange (PKCE) von einem nativen oder öffentlichen Client aus zu sichern. Erforderlich, wenn `code_challenge_method` enthalten ist. Weitere Informationen finden Sie unter [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Wenn der Benutzer Teil einer Organisation ist, kann ein Administrator der Organisation im Auftrag des Benutzers zustimmen oder ablehnen oder dem Benutzer die Zustimmung erlauben. Der Benutzer erhält die Option zur Zustimmung nur dann, wenn der Administrator dies zulässt.
>
>

Nun wird der Benutzer zur Eingabe der Anmeldeinformationen und zur Zustimmung zu den Berechtigungen im Azure-Portal aufgefordert, die von der App angefordert werden. Nach der Authentifizierung und der Zustimmung durch den Benutzer versendet Azure AD eine Antwort an Ihre App unter der `redirect_uri`-Adresse in Ihrer Anforderung mit dem Code.

### <a name="successful-response"></a>Erfolgreiche Antwort
Eine erfolgreiche Antwort sieht wie folgt aus:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| admin_consent |Der Wert ist „true“, wenn ein Administrator einer Aufforderung zu einer Zustimmungsanforderung zugestimmt hat. |
| code |Der Autorisierungscode, den die App angefordert hat. Die Anwendung kann den Autorisierungscode zum Anfordern eines Zugriffstokens für die Zielressource verwenden. |
| session_state |Ein eindeutiger Wert, der die aktuelle Benutzersitzung identifiziert. Dieser Wert ist eine GUID, sollte jedoch als opaker Wert behandelt werden, der ohne Prüfung übergeben wird. |
| state |Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Es hat sich bewährt, wenn die Anwendung überprüft, ob die Statuswerte in der Anforderung und in der Antwort identisch sind, bevor die Antwort verwendet wird. Dies trägt zur Erkennung von [Angriffen vom Typ „websiteübergreifende Anforderungsfälschung“](https://tools.ietf.org/html/rfc6749#section-10.12) auf den Client bei. |

### <a name="error-response"></a>Fehlerantwort
Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese entsprechend behandeln kann.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| error |Ein in Abschnitt 5.2 definierter Fehlercodewert des [OAuth 2.0-Autorisierungsframeworks](https://tools.ietf.org/html/rfc6749). In der folgenden Tabelle finden Sie die Fehlercodes, die von Azure AD ausgegeben werden. |
| error_description |Detailliertere Beschreibung des Fehlers. Diese Meldung ist nicht für den Endbenutzer ausgelegt. |
| state |Der Statuswert ist ein zufällig generierter, nicht wiederverwendeter Wert, der in der Anforderung versendet und in der Antwort zurückgegeben wird, um webseitenübergreifende Anforderungsfälschungen (CSFR) zu vermeiden. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Fehlercodes beim Autorisierungsendpunktfehler
Die folgende Tabelle beschreibt die verschiedenen Fehlercodes, die im `error` -Parameter der Fehlerantwort zurückgegeben werden können:

| Fehlercode | BESCHREIBUNG | Clientaktion |
| --- | --- | --- |
| invalid_request |Protokollfehler, z.B. ein fehlender erforderlicher Parameter. |Korrigieren Sie die Anforderung, und senden Sie sie erneut. Dies ist ein Entwicklungsfehler, der in der Regel bei den Eingangstests festgestellt wird. |
| unauthorized_client |Die Clientanwendung darf keinen Autorisierungscode anfordern. |Dies tritt in der Regel auf, wenn die Clientanwendung nicht in Azure AD registriert ist oder dem Azure AD-Mandanten des Benutzers nicht hinzugefügt wird. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |
| access_denied |Der Ressourcenbesitzer hat die Zustimmung verweigert. |Die Clientanwendung kann den Benutzer benachrichtigen, dass sie ohne Zustimmung des Benutzers nicht fortfahren kann. |
| unsupported_response_type |Der Autorisierungsserver unterstützt den Antworttyp in der Anforderung nicht. |Korrigieren Sie die Anforderung, und senden Sie sie erneut. Dies ist ein Entwicklungsfehler, der in der Regel bei den Eingangstests festgestellt wird. |
| server_error |Der Server hat einen unerwarteten Fehler erkannt. |Wiederholen Sie die Anforderung. Diese Fehler werden durch temporäre Bedingungen verursacht. Die Clientanwendung kann dem Benutzer erklären, dass ihre Antwort aufgrund eines temporären Fehlers verzögert ist. |
| temporarily_unavailable |Der Server ist vorübergehend überlastet und kann die Anforderung nicht verarbeiten. |Wiederholen Sie die Anforderung. Die Clientanwendung kann dem Benutzer erklären, dass ihre Antwort aufgrund einer temporären Bedingung verzögert ist. |
| invalid_resource |Die Zielressource ist ungültig, da sie nicht vorhanden ist, Azure AD sie nicht findet oder sie nicht ordnungsgemäß konfiguriert ist. |Dies gibt an, dass die Ressource, falls vorhanden, im Mandanten nicht konfiguriert wurde. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Fordern Sie ein Zugriffstoken mithilfe des Autorisierungscodes an.
Wenn Sie einen Autorisierungscode erworben und die Berechtigung vom Benutzer erhalten haben, können Sie den Code für ein Zugriffstoken auf die gewünschte Ressource einlösen, indem Sie eine POST-Anforderung an den `/token` -Endpunkt senden:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parameter |  | BESCHREIBUNG |
| --- | --- | --- |
| tenant |required |Mit dem `{tenant}` -Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Die zulässigen Werte sind Mandantenbezeichner (also etwa `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` oder für mandantenunabhängige Token `common`). |
| client_id |required |Die Anwendungs-Id, die Ihrer App zugewiesen wird, wenn Sie sie mit Azure AD registrieren. Diese finden Sie im Azure-Portal. Die Anwendungs-ID wird in den Einstellungen der App-Registrierung angezeigt. |
| grant_type |required |Muss der `authorization_code` für den Autorisierungscodefluss sein. |
| code |required |Der `authorization_code` , den Sie im vorherigen Abschnitt abgerufen haben. |
| redirect_uri |required | Ein `redirect_uri` für die Clientanwendung registriert. |
| client_secret |Erforderlich für Web-Apps. Für öffentliche Clients nicht zulässig. |Das Anwendungsgeheimnis, das Sie für Ihre App im Azure-Portal unter **Schlüssel** erstellt haben. Er darf nicht in einer nativen App (öffentlicher Client) verwendet werden, da Clientgeheimnisse nicht zuverlässig auf Geräten gespeichert werden können. Er ist für Web-Apps und Web-APIs (alle vertraulichen Clients) erforderlich, die `client_secret` sicher auf dem Server speichern können. Der geheime Clientschlüssel (client_secret) sollte vor dem Senden URL-codiert werden. |
| resource | empfohlen |Der App-ID-URI der Ziel-Web-API (geschützte Ressource). Klicken Sie zum Ermitteln des App-ID-URI im Azure-Portal auf **Azure Active Directory** und auf **App-Registrierungen**. Öffnen Sie anschließend die Seite **Einstellungen**, und klicken Sie auf **Eigenschaften**. Es kann sich auch um eine externe Ressource wie `https://graph.microsoft.com` handeln. Dieser Parameter muss in einer der Autorisierungs- oder Tokenanforderungen vorhanden sein, um sicherzustellen, dass weniger Authentifizierungsanforderungen diesen in die Autorisierungsanforderung platzieren und so die Zustimmung des Benutzers empfangen wird. Wenn dieser in der Autorisierungs- und Tokenanforderung enthalten ist, müssen die Ressourcenparameter übereinstimmen. | 
| code_verifier | optional | Derselbe code_verifier-Parameter, der auch zum Abrufen von „authorization_code“ verwendet wurde. Erforderlich, wenn PKCE bei der Anforderung für die Gewährung des Autorisierungscodes verwendet wurde. Weitere Informationen finden Sie unter [PKCE RFC](https://tools.ietf.org/html/rfc7636).   |

Klicken Sie zum Ermitteln des App-ID-URI im Azure-Portal auf **Azure Active Directory** und auf **App-Registrierungen**. Öffnen Sie anschließend die Seite **Einstellungen**, und klicken Sie auf **Eigenschaften**.

### <a name="successful-response"></a>Erfolgreiche Antwort
Azure AD gibt bei einer erfolgreichen Antwort ein [Zugriffstoken](access-tokens.md) zurück. Um die Anzahl von Netzwerkaufrufen der Clientanwendung und die damit verbundene Latenz zu verringern, sollte die Clientanwendung Zugriffstoken für die Tokenlebensdauer zwischenspeichern, die in der OAuth 2.0-Antwort angegeben ist. Verwenden Sie zum Bestimmen der Tokenlebensdauer entweder den Parameterwert `expires_in` oder `expires_on`.

Wenn eine Web-API-Ressource den Fehlercode `invalid_token` zurückgibt, kann dies darauf hinweisen, dass von der Ressource ein abgelaufenes Token ermittelt wurde. Falls sich die Zeiten der Client- und Ressourcenuhr unterscheiden (als „zeitlicher Versatz“ bezeichnet), wird das Token von der Ressource ggf. als abgelaufen angesehen, bevor das Token aus dem Clientcache entfernt wird. Löschen Sie das Token in diesem Fall auch dann aus dem Cache, wenn der berechnete Lebensdauerzeitraum noch nicht abgelaufen ist.

Eine erfolgreiche Antwort sieht wie folgt aus:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parameter | BESCHREIBUNG |
| --- | --- |
| access_token |Das angeforderte [Zugriffstoken](access-tokens.md) als signiertes JSON-Webtoken (JWT). Die App kann dieses Token zur Authentifizierung auf geschützten Ressourcen verwenden, wie z. B. eine Web-API. |
| token_type |Gibt den Wert des Tokentyps an. Bearertoken ist der einzige Typ, den Azure AD unterstützt. Weitere Informationen zu Bearertoken finden Sie unter [OAuth 2.0-Autorisierungsframework: Verwendung von Bearertoken (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Gibt an, wie lange das Zugriffstoken (in Sekunden) gültig ist. |
| expires_on |Die Uhrzeit, zu der das Zugriffstoken abläuft. Das Datum wird als Anzahl der Sekunden ab 1970-01-01T0:0:0Z UTC bis zur Ablaufzeit dargestellt. Dieser Wert wird verwendet, um die Lebensdauer von zwischengespeicherten Token zu bestimmen. |
| resource |Der App-ID-URI der Web-API (geschützte Ressource). |
| scope |Die Identitätswechselberechtigungen, die der Clientanwendung gewährt wurden. Die Standardberechtigung ist `user_impersonation`. Der Besitzer der gesicherten Ressource kann zusätzliche Werte in Azure AD registrieren. |
| refresh_token |Ein Aktualisierungstoken von OAuth 2.0. Die App kann dieses Token verwenden, um nach Ablauf der aktuellen Zugriffstoken zusätzliche Zugriffstoken zu erhalten. Aktualisierungstoken sind langlebig und können verwendet werden, um den Zugriff auf Ressourcen für längere Zeit beizubehalten. |
| id_token |Ein nicht signiertes JSON-Webtoken (JWT), das ein [ID-Token](id-tokens.md) darstellt. Die App kann die Segmente dieses Tokens mit einer base64-URL decodieren, um Informationen über den angemeldeten Benutzer abzurufen. Die App kann die Werte zwischenspeichern und sie anzeigen, sollte sich jedoch nicht für Autorisierungs- und Sicherheitsgrenzen auf sie verlassen. |

Weitere Informationen zu JSON-Webtoken finden Sie im [JWT IETF-Spezifikationsentwurf](https://go.microsoft.com/fwlink/?LinkId=392344).   Weitere Informationen zu `id_tokens` finden Sie unter [v1.0 OpenID Connect-Datenfluss](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Fehlerantwort
Die Fehler am Tokenausstellungs-Endpunkt sind HTTP-Fehlercodes, da der Client den Tokenausstellungs-Endpunkt direkt aufruft. Zusätzlich zum HTTP-Statuscode gibt der Azure AD-Tokenausstellungs-Endpunkt auch ein JSON-Dokument mit Objekten zurück, die den Fehler beschreiben.

Eine Beispiel für eine Fehlerantwort sieht wie folgt aus:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parameter | BESCHREIBUNG |
| --- | --- |
| error |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error_description |Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |
| error_codes |Eine Liste der STS-spezifischen Fehlercodes, die bei der Diagnose helfen können. |
| timestamp |Der Zeitpunkt, zu dem der Fehler aufgetreten ist |
| trace_id |Ein eindeutiger Bezeichner für die Anforderung, die bei der Diagnose helfen kann |
| correlation_id |Ein eindeutiger Bezeichner für die Anforderung, die bei der komponentenübergreifenden Diagnose helfen kann |

#### <a name="http-status-codes"></a>HTTP-Statuscodes
Die folgende Tabelle enthält die HTTP-Statuscodes, die vom Tokenausstellungs-Endpunkt zurückgegeben werden. In einigen Fällen ist der Fehlercode ausreichend, um die Antwort zu beschreiben. Wenn jedoch Fehler auftreten, müssen Sie die zugehörigen JSON-Dokumente analysieren und den Fehlercode überprüfen.

| HTTP-Code | BESCHREIBUNG |
| --- | --- |
| 400 |Standard-HTTP-Code. Wird in den meisten Fällen verwendet und wird in der Regel aufgrund einer fehlerhaften Anforderung ausgegeben. Korrigieren Sie die Anforderung, und senden Sie sie erneut. |
| 401 |Fehler bei der Authentifizierung. Tritt beispielsweise auf, wenn die Anforderung den Parameter „client_secret“ nicht enthält. |
| 403 |Fehler bei der Autorisierung. Der Benutzer verfügt beispielsweise nicht über die Berechtigung zum Zugriff auf die Ressource. |
| 500 |Ein interner Fehler ist beim Dienst aufgetreten. Wiederholen Sie die Anforderung. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Fehlercodes für Token-Endpunktfehler
| Fehlercode | BESCHREIBUNG | Clientaktion |
| --- | --- | --- |
| invalid_request |Protokollfehler, z.B. ein fehlender erforderlicher Parameter. |Korrigieren Sie die Anforderung, und senden Sie sie erneut. |
| invalid_grant |Der Autorisierungscode ist ungültig oder abgelaufen. |Versuchen Sie, eine neue Anforderung an den `/authorize`-Endpunkt zu senden. |
| unauthorized_client |Der authentifizierte Client ist zur Verwendung dieses Autorisierungsgewährungstyps nicht autorisiert. |Dies tritt in der Regel auf, wenn die Clientanwendung nicht in Azure AD registriert ist oder dem Azure AD-Mandanten des Benutzers nicht hinzugefügt wird. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |
| invalid_client |Clientauthentifizierung fehlgeschlagen. |Die Client-Anmeldeinformationen sind nicht gültig. Um das Problem zu beheben, aktualisiert der Anwendungsadministrator die Anmeldeinformationen. |
| unsupported_grant_type |Der Autorisierungsserver unterstützt den Autorisierungsgewährungstyp nicht. |Ändern Sie den Gewährungstyp in der Anforderung. Diese Art von Fehler sollte nur während der Entwicklung auftreten und bei den ersten Tests erkannt werden. |
| invalid_resource |Die Zielressource ist ungültig, da sie nicht vorhanden ist, Azure AD sie nicht findet oder sie nicht ordnungsgemäß konfiguriert ist. |Dies gibt an, dass die Ressource, falls vorhanden, im Mandanten nicht konfiguriert wurde. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |
| interaction_required |Die Anforderung erfordert eine Benutzerinteraktion. Beispielsweise ist ein zusätzlicher Schritt zur Authentifizierung erforderlich. | Wiederholen Sie die Anforderung derselben Ressource mit einer interaktiven Autorisierungsanforderung anstelle einer nicht interaktiven Anforderung. |
| temporarily_unavailable |Der Server ist vorübergehend überlastet und kann die Anforderung nicht verarbeiten. |Wiederholen Sie die Anforderung. Die Clientanwendung kann dem Benutzer erklären, dass ihre Antwort aufgrund einer temporären Bedingung verzögert ist. |

## <a name="use-the-access-token-to-access-the-resource"></a>Verwenden des Zugriffstokens für den Zugriff auf die Ressource
Nachdem Sie erfolgreich ein `access_token` abgerufen haben, können Sie das Token für Anforderungen an Web-APIs verwenden, indem Sie es in den `Authorization`-Header einschließen: Die Spezifikation [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) erklärt, wie Bearertoken in HTTP-Anforderungen für den Zugriff auf geschützte Ressourcen verwendet werden.

### <a name="sample-request"></a>Beispiel für eine Anforderung
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Fehlerantwort
Gesicherte Ressourcen, die RFC 6750 implementieren, geben HTTP-Statuscodes zurück. Wenn die Anforderung keine Authentifizierungsinformationen enthält oder wenn das Token fehlt, enthält die Antwort einen `WWW-Authenticate` -Header. Wenn eine Anforderung fehlschlägt, antwortet der Ressourcenserver mit einem HTTP-Statuscode und einem Fehlercode.

Im Folgenden finden Sie ein Beispiel für eine nicht erfolgreiche Antwort, wenn die Clientanforderung das Bearertoken nicht enthält:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Fehlerparameter
| Parameter | BESCHREIBUNG |
| --- | --- |
| authorization_uri |Der URI (physische Endpunkt) des Autorisierungsservers. Dieser Wert wird auch als Suchschlüssel verwendet, um weitere Informationen über den Server aus einem Discovery-Endpunkt zu erhalten. <p><p>  Der Client muss überprüfen, ob der Autorisierungsserver vertrauenswürdig ist. Wenn die Ressource von Azure AD geschützt wird, ist die Prüfung ausreichend, ob die URL mit https://login.microsoftonline.com oder einem anderen Hostnamen beginnt, den Azure AD unterstützt. Eine mandantenspezifische Ressource sollte immer einen mandantenspezifischen Autorisierungs-URI zurückgeben. |
| error |Ein in Abschnitt 5.2 definierter Fehlercodewert des [OAuth 2.0-Autorisierungsframeworks](https://tools.ietf.org/html/rfc6749). |
| error_description |Detailliertere Beschreibung des Fehlers. Diese Meldung ist nicht für den Endbenutzer ausgelegt. |
| resource_id |Gibt den eindeutigen Bezeichner der Ressource zurück. Die Clientanwendung kann diesen Bezeichner als Wert für den `resource` -Parameter verwenden, wenn sie ein Token für die Ressource anfordert. <p><p> Es ist wichtig, dass die Clientanwendung diesen Wert überprüft, da andernfalls ein schädlicher Dienst möglicherweise einen Angriff mit einer **Erhöhung von Rechten** durchführt. <p><p> Die empfohlene Strategie zur Verhinderung eines Angriffs besteht darin sicherzustellen, dass die `resource_id` mit dem Basiselement der Web-API-URL übereinstimmt, auf die zugegriffen wird. Wenn beispielsweise auf https://service.contoso.com/data zugegriffen wird, kann die `resource_id` „htttps://service.contoso.com/“ lauten. Die Clientanwendung muss eine `resource_id` ablehnen, die nicht mit der Basis-URL beginnt, sofern es kein zuverlässiges alternatives Verfahren zum Überprüfen der ID gibt. |

#### <a name="bearer-scheme-error-codes"></a>Bearerschema-Fehlercodes
Die Spezifikation RFC 6750 definiert die folgenden Fehler für Ressourcen, die in der Antwort den WWW-Authenticate-Header und das Bearer-Schema verwenden.

| HTTP-Statuscode | Fehlercode | BESCHREIBUNG | Clientaktion |
| --- | --- | --- | --- |
| 400 |invalid_request |Die Anforderung ist nicht richtig formatiert. Möglicherweise fehlt ein Parameter, oder der gleiche Parameter wird zweimal verwendet. |Beheben Sie den Fehler, und wiederholen Sie die Anforderung. Diese Art von Fehler sollte nur während der Entwicklung auftreten und bei den ersten Tests erkannt werden. |
| 401 |invalid_token |Das Zugriffstoken ist nicht vorhanden, ungültig oder wurde widerrufen. Der Wert des Parameters „error_description“ enthält weitere Details. |Fordern Sie ein neues Token vom Autorisierungsserver an. Wenn das neue Token fehlschlägt, ist ein unerwarteter Fehler aufgetreten. Sendet eine Fehlermeldung an den Benutzer. Eine Wiederholung erfolgt nach einer beliebigen Zeitspanne. |
| 403 |insufficient_scope |Das Zugriffstoken enthält nicht die erforderlichen Berechtigungen zum Identitätswechsel, um auf die Ressource zuzugreifen. |Senden Sie eine neue Autorisierungsanforderung an den Autorisierungsendpunkt. Wenn die Antwort den Bereichsparameter enthält, verwenden Sie den Bereichswert in der Anforderung für die Ressource. |
| 403 |insufficient_access |Der Antragsteller des Tokens besitzt nicht die Berechtigungen, die für den Zugriff auf die Ressource erforderlich sind. |Der Benutzer wird aufgefordert, ein anderes Konto zu verwenden oder Berechtigungen für die angegebene Ressource anzufordern. |

## <a name="refreshing-the-access-tokens"></a>Aktualisieren der Zugriffstoken

Zugriffstoken sind kurzlebig und müssen nach Ablauf aktualisiert werden, damit Sie weiterhin auf Ressourcen zugreifen können. Übermitteln Sie zum Aktualisieren von `access_token` eine weitere `POST`-Anforderung an den `/token`-Endpunkt, und zwar dieses Mal unter Angabe des `refresh_token` anstelle von `code`.  Aktualisierungstoken sind für alle Ressourcen gültig, für die Ihrem Client bereits die Zustimmung zum Zugriff erteilt wurde. Daher kann ein Aktualisierungstoken, das für eine Anforderung für `resource=https://graph.microsoft.com` ausgestellt wurde, zum Anfordern eines neuen Zugriffstokens für `resource=https://contoso.com/api` verwendet werden. 

Für Aktualisierungstoken werden keine Lebensdauern angegeben. Normalerweise verfügen Aktualisierungstoken über relativ lange Lebensdauern. In einigen Fällen laufen Aktualisierungstoken aber ab, werden widerrufen oder verfügen nicht über ausreichende Berechtigungen für die gewünschte Aktion. Von Ihrer Anwendung müssen Fehler, die vom Tokenausstellungs-Endpunkt zurückgegeben werden, erwartet und richtig behandelt werden.

Wenn Sie eine Antwort mit einem Aktualisierungstokenfehler erhalten, sollten Sie das aktuelle Aktualisierungstoken verwerfen und einen neuen Autorisierungscode oder ein neues Zugriffstoken anfordern. Wenn Sie ein Aktualisierungstoken im Ablauf der Autorisierungscodeerteilung verwenden und eine Antwort mit dem Fehlercode `interaction_required` oder `invalid_grant` erhalten, sollten Sie besonders darauf achten, dass Sie das Aktualisierungstoken verwerfen und einen neuen Autorisierungscode anfordern.

Ein Beispiel für eine Anforderung an den **mandantenspezifischen** Endpunkt (Sie können auch den **allgemeinen** Endpunkt verwenden), um ein neues Zugriffstoken mithilfe eines Aktualisierungstokens abzurufen, sieht wie folgt aus:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Erfolgreiche Antwort
Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Parameter | BESCHREIBUNG |
| --- | --- |
| token_type |Der Tokentyp. Der einzige derzeit unterstützte Wert ist **bearer**. |
| expires_in |Die verbleibende Gültigkeitsdauer des Tokens in Sekunden. Ein typischer Wert wäre etwa 3600 (eine Stunde). |
| expires_on |Datum und Uhrzeit, wenn das Token abläuft. Das Datum wird als Anzahl der Sekunden ab 1970-01-01T0:0:0Z UTC bis zur Ablaufzeit dargestellt. |
| resource |Gibt die gesicherte Ressource an, die das Zugriffstoken für den Zugriff verwenden kann. |
| scope |Die Identitätswechselberechtigungen, die der nativen Clientanwendung gewährt wurden. Die Standardberechtigung lautet **user_impersonation**. Der Besitzer der Zielressource kann alternative Werte in Azure AD registrieren. |
| access_token |Das neue Zugriffstoken, das angefordert wurde. |
| refresh_token |Ein neues OAuth 2.0-Aktualisierungstoken, das zum Anfordern neuer Zugriffstoken verwendet werden kann, wenn das Token in dieser Antwort abläuft. |

### <a name="error-response"></a>Fehlerantwort
Eine Beispiel für eine Fehlerantwort sieht wie folgt aus:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| error |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error_description |Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |
| error_codes |Eine Liste der STS-spezifischen Fehlercodes, die bei der Diagnose helfen können. |
| timestamp |Der Zeitpunkt, zu dem der Fehler aufgetreten ist |
| trace_id |Ein eindeutiger Bezeichner für die Anforderung, die bei der Diagnose helfen kann |
| correlation_id |Ein eindeutiger Bezeichner für die Anforderung, die bei der komponentenübergreifenden Diagnose helfen kann |

Eine Beschreibung der Fehlercodes und der jeweils empfohlenen Clientaktion finden Sie unter [Fehlercodes für Token-Endpunktfehler](#error-codes-for-token-endpoint-errors).
