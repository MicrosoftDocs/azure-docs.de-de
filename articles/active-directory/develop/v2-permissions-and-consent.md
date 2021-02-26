---
title: Bereiche, Berechtigungen und Einwilligung für Microsoft Identity Platform
description: Lernen Sie die Autorisierung im Microsoft Identity Platform-Endpunkt, einschließlich Bereichen, Berechtigungen und Zustimmung, kennen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 2658c088304eba457b25bb3dc421b356ba70b57f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102477"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt

In Microsoft Identity Platform integrierte Anwendungen folgen einem Autorisierungsmodell, mit dem Benutzer und Administratoren den Zugriff auf Daten steuern können. Die Implementierung des Autorisierungsmodells wurde in Microsoft Identity Platform aktualisiert. Dadurch muss eine App auf andere Weise mit Microsoft Identity Platform interagieren. Dieser Artikel behandelt die grundlegenden Konzepte dieses Autorisierungsmodells einschließlich der Bereiche, Berechtigungen und Zustimmung.

## <a name="scopes-and-permissions"></a>Bereiche und Berechtigungen

Die Microsoft Identity Platform implementiert das [OAuth 2.0](active-directory-v2-protocols.md)-Autorisierungsprotokoll. OAuth 2.0 ist eine Methode, über die eine Drittanbieter-App im Auftrag eines Benutzers auf im Web gehostete Ressourcen zugreifen kann. Alle im Web gehosteten Ressourcen, die in Microsoft Identity Platform integriert werden, verfügen über einen Ressourcenbezeichner bzw. *Anwendungs-ID-URI*. 

Im Anschluss folgen einige Beispiele für im Web gehostete Microsoft-Ressourcen:

* Microsoft Graph: `https://graph.microsoft.com`
* API für Microsoft 365-E-Mail: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Dasselbe gilt für alle Ressourcen von Drittanbietern, die in die Microsoft Identity Platform integriert wurden. Diese Ressourcen können auch einen Satz von Berechtigungen definieren, die zum Unterteilen der Funktionalität der Ressource in kleinere Einheiten verwendet werden können. Beispielsweise verfügt [Microsoft Graph](https://graph.microsoft.com) über definierte Berechtigungen, um unter anderem folgende Aufgaben auszuführen:

* Lesen des Kalenders eines Benutzers
* Schreiben in den Kalender eines Benutzers
* Senden von E-Mails als Benutzer

Aufgrund dieser Arten von Berechtigungsdefinitionen kann die Ressource die Daten und die Art und Weise, wie API-Funktionen verfügbar gemacht werden, präzise steuern. Eine Drittanbieter-App kann diese Berechtigungen von Benutzern und Administratoren anfordern. Diese müssen die Anforderung dann genehmigen, bevor die App auf Daten zugreifen oder im Namen eines Benutzers agieren kann. 

Die Unterteilung der Funktionen einer Ressource in kleinere Berechtigungssätze ermöglicht die Erstellung von Drittanbieter-Apps, von denen nur die spezifischen Berechtigungen angefordert werden, die diese Apps für ihr jeweilige Funktion benötigen. Benutzer und Administratoren können erkennen, auf welche Daten die App zugreifen kann. Und sie können mit höherer Wahrscheinlichkeit davon ausgehen, dass die App keine böswilligen Absichten verfolgt. Entwickler sollten sich immer an das Prinzip der geringsten Rechte halten und nur die Berechtigungen anfordern, die für die Funktion ihrer Anwendungen erforderlich sind.

In OAuth 2.0 werden diese Arten von Berechtigungssätzen *Bereiche* genannt. Oftmals werden sie auch als *Berechtigungen* bezeichnet. In Microsoft Identity Platform wird eine Berechtigung als Zeichenfolgenwert dargestellt. Im Beispiel von Microsoft Graph lautet der Zeichenfolgenwert für die einzelnen Berechtigungen wie folgt:

* Lesen des Kalenders eines Benutzers mit `Calendars.Read`
* Schreiben in den Kalender eines Benutzers mit `Calendars.ReadWrite`
* Senden von E-Mails als Benutzer mit `Mail.Send`

Eine App fordert diese Berechtigungen meist durch Angabe der Bereiche in Anforderungen an den Microsoft Identity Platform-Autorisierungsendpunkt an. Bestimmte erhöhte Berechtigungen können jedoch nur mit der Einwilligung des Administrators gewährt werden. Sie können unter Verwendung des [Endpunkts für die Administratoreinwilligung](#admin-restricted-permissions) angefordert/gewährt werden. Lesen Sie weiter, um mehr zu erfahren.

## <a name="permission-types"></a>Berechtigungstypen

Microsoft Identity Platform unterstützt zwei Arten von Berechtigungen: *delegierte Berechtigungen* und *Anwendungsberechtigungen*.

* **Delegierte Berechtigungen** werden von Apps verwendet, bei denen ein angemeldeter Benutzer vorhanden ist. Bei diesen Apps willigt entweder der Benutzer oder ein Administrator in die von der App angeforderten Berechtigungen ein. An die App wird die Berechtigung delegiert, bei an die Zielressource gerichteten Aufrufen als angemeldeter Benutzer zu fungieren. 

    Bei einigen delegierten Berechtigungen ist eine Einwilligung durch Benutzer ohne Administratorberechtigungen möglich. Bei bestimmten erhöhten Berechtigungen ist jedoch eine [Administratoreinwilligung](#admin-restricted-permissions) erforderlich. Informationen dazu, welche Administratorrollen in delegierte Berechtigungen einwilligen können, finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../roles/permissions-reference.md) (Azure AD).

* **Anwendungsberechtigungen** werden von Apps verwendet, die ohne vorhandenen angemeldeten Benutzer ausgeführt werden. Dies können beispielsweise Apps sein, die als Hintergrunddienste oder Daemons ausgeführt werden. Anwendungsberechtigungen [erfordern die Einwilligung eines Administrators](#requesting-consent-for-an-entire-tenant).

_Effektive Berechtigungen_ sind Berechtigungen, über die Ihre App verfügt, wenn sie Anforderungen an die Zielressource sendet. Es ist wichtig, den Unterschied zwischen delegierten Berechtigungen und Anwendungsberechtigungen, die Ihrer App gewährt werden, und den effektiven Berechtigungen zu verstehen, die Ihrer App beim Senden von Aufrufen an die Zielressource gewährt werden.

- Bei delegierten Berechtigungen sind die _effektiven Berechtigungen_ Ihrer App die jeweils geringsten Rechte, die sich zusammen genommen aus den delegierten Berechtigungen, die der App (per Einwilligung) gewährt wurden, und den Berechtigungen des derzeit angemeldeten Benutzers ergeben. Ihre App kann niemals über mehr Berechtigungen als der angemeldete Benutzer verfügen. 

   In Organisationen können die Berechtigungen des angemeldeten Benutzers anhand einer Richtlinie oder der Mitgliedschaft in einer oder mehreren Administratorrollen bestimmt werden. Um zu erfahren, welche Administratorrollen delegierten Berechtigungen zustimmen können, lesen Sie [Berechtigungen der Administratorrolle in Azure AD](../roles/permissions-reference.md).

   Angenommen, Ihrer App wurde die delegierte Berechtigung _User.ReadWrite.All_ gewährt. Mit dieser Berechtigung wird Ihrer App nominell die Berechtigung zum Lesen und Aktualisieren des Profils jedes Benutzers einer Organisation gewährt. Wenn es sich bei dem angemeldeten Benutzer um einen globalen Administrator handelt, kann Ihre App das Profil jedes Benutzers in der Organisation aktualisieren. Verfügt der angemeldete Benutzer dagegen nicht über eine Administratorrolle, kann Ihre App nur das Profil des angemeldeten Benutzers aktualisieren. Sie kann die Profile von anderen Benutzern der Organisation nicht aktualisieren, da der Benutzer, für den die App die Berechtigung zum Durchführen von Aktionen in dessen Namen hat, nicht über diese Berechtigungen verfügt.

- Für Anwendungsberechtigungen umfassen die _effektiven Berechtigungen_ Ihrer App die vollständige Berechtigungsebene, die mit der Berechtigung verbunden ist. Beispielsweise kann eine App mit der Anwendungsberechtigung _User.ReadWrite.All_ das Profil aller Benutzer in der Organisation aktualisieren.

## <a name="openid-connect-scopes"></a>OpenID Connect-Bereiche

Die Microsoft Identity Platform-Implementierung von OpenID Connect bietet einige klar definierte Bereiche, die ebenfalls in Microsoft Graph gehostet werden: `openid`, `email`, `profile` und `offline_access`. Die OpenID Connect-Bereiche `address` und `phone` werden nicht unterstützt.

Wenn Sie die OpenID Connect-Bereiche und ein Token anfordern, erhalten Sie ein Token zum Abrufen des [UserInfo-Endpunkts](userinfo.md).

### <a name="openid"></a>openid

Bei einer App-Anmeldung mit [OpenID Connect](active-directory-v2-protocols.md) muss der Bereich `openid` angefordert werden. Der Bereich `openid` wird auf der Einwilligungsseite des Arbeitskontos als Berechtigung **Anmeldung in Ihrem Namen** angezeigt. Auf der Einwilligungsseite des persönlichen Microsoft-Kontos wird sie als Berechtigung **Ihr Profil anzeigen und mit Ihrem Microsoft-Konto eine Verbindung zu Apps und Diensten herstellen** angezeigt. 

Mit dieser Berechtigung kann eine App einen eindeutigen Bezeichner für den Benutzer in Form des Anspruchs `sub` empfangen. Die Berechtigung ermöglicht es der App außerdem, auf den Endpunkt mit den Benutzerinformationen (UserInfo) zuzugreifen. Der Bereich `openid` kann am Microsoft Identity Platform-Tokenendpunkt zum Abrufen von ID-Token verwendet werden. Diese Token können von der App wiederum für die Authentifizierung verwendet werden.

### <a name="email"></a>email

Der Bereich `email` kann zusammen mit dem Bereich `openid` und weiteren Bereichen verwendet werden. Er gibt der App Zugriff auf die primäre E-Mail-Adresse des Benutzers in Form des Anspruchs `email` . 

Der Anspruch `email` ist nur in einem Token enthalten, wenn dem Benutzerkonto eine E-Mail-Adresse zugewiesen ist (dies ist nicht immer der Fall). Wenn Ihre App den Bereich `email` verwendet, muss sie mit Szenarien umgehen können, in denen das Token keinen Anspruch vom Typ `email` enthält.

### <a name="profile"></a>Profil

Der Bereich `profile` kann mit dem Bereich `openid` und mit beliebigen anderen Bereichen kombiniert werden. Er ermöglicht der App Zugriff auf eine Vielzahl von Benutzerinformationen. Dazu gehören u.a. Vorname, Nachname, bevorzugter Benutzername und Objekt-ID des Benutzers. 

Eine vollständige Liste der Ansprüche vom Typ `profile`, die im Parameter `id_tokens` für einen bestimmten Benutzer verfügbar sind, finden Sie in der [Referenz zu `id_tokens`](id-tokens.md).

### <a name="offline_access"></a>offline_access

Mit dem [`offline_access`-Bereich](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) kann Ihre App im Auftrag des Benutzers für einen längeren Zeitraum auf Ressourcen zugreifen. Auf der Einwilligungsseite wird dieser Bereich als Berechtigung **Zugriff auf Daten erhalten, auf die Sie Zugriff gewährt haben** angezeigt. 

Wenn ein Benutzer den `offline_access`-Bereich genehmigt, kann Ihre App Aktualisierungstoken vom Microsoft Identity Platform-Tokenendpunkt empfangen. Aktualisierungstoken sind langlebig. Ihrer App kann neue Zugriffstoken abrufen, wenn ältere ablaufen.

> [!NOTE]
> Diese Berechtigung wird aktuell auf allen Einwilligungsseiten angezeigt – auch für Flüsse ohne Aktualisierungstoken ([impliziter Fluss](v2-oauth2-implicit-grant-flow.md)). Durch dieses Setup werden Szenarien abgedeckt, in denen ein Client im impliziten Fluss beginnt und anschließend mit dem Codefluss fortfährt, in dem ein Aktualisierungstoken erwartet wird.

Im Rahmen von Microsoft Identity Platform (Anforderungen an den v2.0-Endpunkt) muss Ihre App explizit den Bereich `offline_access` anfordern, um Aktualisierungstoken zu erhalten. Beim Einlösen eines Autorisierungscodes im [OAuth 2.0-Autorisierungscodefluss](active-directory-v2-protocols.md) erhalten Sie vom Endpunkt `/token` also nur ein Zugriffstoken. 

Das Zugriffstoken ist für eine kurze Zeit gültig. Es läuft in der Regel nach einer Stunde ab. Zu diesem Zeitpunkt muss Ihre App den Benutzer zurück auf den `/authorize`-Endpunkt leiten, um einen neuen Autorisierungscode abzurufen. Während dieser Umleitung muss der Benutzer möglicherweise seine Anmeldeinformationen erneut eingeben oder den Berechtigungen erneut zustimmen, je nach App-Typ.

Weitere Informationen zum Abrufen und Verwenden von Aktualisierungstoken finden Sie in der [Microsoft Identity Platform-Protokollreferenz](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Anfordern der Zustimmung einzelner Benutzer

In einer Autorisierungsanforderung von [OpenID Connect oder OAuth 2.0](active-directory-v2-protocols.md) kann eine App die erforderlichen Berechtigungen mithilfe des `scope`-Abfrageparameters anfordern. Wenn sich ein Benutzer beispielsweise bei einer App anmeldet, sendet die App eine Anforderung wie die folgende. Zur besseren Lesbarkeit wurden Zeilenumbrüche eingefügt:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Der `scope`-Parameter ist eine durch Leerzeichen getrennte Liste von delegierten Berechtigungen, die von der App angefordert werden. Die einzelnen Berechtigungen werden jeweils durch Anfügen des Berechtigungswerts an den Ressourcenbezeichner (Anwendungs-ID-URI) angegeben. Die Anforderung im Beispiel gibt an, dass die Anwendung eine Berechtigung zum Lesen des Kalenders des Benutzers und Senden von E-Mails als Benutzer benötigt.

Nachdem der Benutzer seine Anmeldeinformationen eingegeben hat, überprüft Microsoft Identity Platform, ob es einen übereinstimmenden Eintrag für die *Benutzerzustimmung* gibt. Wenn der Benutzer in der Vergangenheit für keine der angeforderten Berechtigungen seine Zustimmung erteilt und der Administrator im Namen der gesamten Organisation diesen Berechtigungen nicht zugestimmt hat, wird der Benutzer durch Microsoft Identity Platform aufgefordert, die angeforderten Berechtigungen zu gewähren.

Zu diesem Zeitpunkt werden die Berechtigungen `offline_access` („Zugriff auf Daten erhalten, auf die Sie Zugriff gewährt haben“) und `user.read` („Anmelden und Ihr Profil lesen“) automatisch in die erste Einwilligung für eine Anwendung aufgenommen.  Diese Berechtigungen sind im Allgemeinen für die ordnungsgemäße Funktionalität der App erforderlich. Die Berechtigung `offline_access` ermöglicht der App den Zugriff auf Aktualisierungstoken, die für native und Web-Apps entscheidend sind. Die Berechtigung `user.read` ermöglicht den Zugriff auf den Anspruch `sub`. Dadurch kann der Client oder die App den Benutzer im Laufe der Zeit korrekt identifizieren und auf rudimentäre Benutzerinformationen zugreifen.

![Screenshot eines Beispiels für die Einwilligung im Arbeitskonto](./media/v2-permissions-and-consent/work_account_consent.png)

Wenn der Benutzer die Berechtigungsanforderung genehmigt, wird die Einwilligung erfasst. Dadurch ist bei nachfolgenden Anmeldevorgängen keine erneute Einwilligung des Benutzers erforderlich.

## <a name="requesting-consent-for-an-entire-tenant"></a>Anfordern der Zustimmung für einen gesamten Mandanten

Organisationen, die Lizenzen oder Abonnements für eine Anwendung erwerben, möchten die Anwendung oft proaktiv zur Verwendung durch alle Mitglieder der Organisation einrichten. Im Rahmen dieses Prozesses kann ein Administrator der Anwendung die Einwilligung erteilen, im Auftrag beliebiger Benutzer im Mandanten zu agieren. Wenn der Administrator eine Einwilligung für den gesamten Mandanten erteilt, wird den Benutzern der Organisation keine Einwilligungsseite für die Anwendung angezeigt.

Um die Einwilligung für delegierte Berechtigungen für alle Benutzer in einem Mandanten anzufordern, kann Ihre App den Endpunkt für die Administratoreinwilligung verwenden.

Darüber hinaus müssen Anwendungen den Endpunkt für die Administratoreinwilligung zum Anfordern von Anwendungsberechtigungen verwenden.

## <a name="admin-restricted-permissions"></a>Auf den Administrator beschränkte Berechtigungen

Einige erhöhte Berechtigungen im Microsoft-Ökosystem können als *auf den Administrator beschränkt* festgelegt werden. Im Anschluss folgen einige Beispiele für derartige Berechtigungen:

* Lesen der vollständigen Profile aller Benutzer mit `User.Read.All`
* Schreiben von Daten in das Verzeichnis einer Organisation mit `Directory.ReadWrite.All`
* Lesen aller Gruppen im Verzeichnis einer Organisation mit `Groups.Read.All`

Ein Endbenutzer kann einer Anwendung zwar ggf. Zugriff auf diese Daten gewähren, Organisationsbenutzer können allerdings keinen Zugriff auf die gleichen sensible Unternehmensdaten erteilen. Wenn Ihre Anwendung von einem Organisationsbenutzer Zugriff auf eine dieser Berechtigungen anfordert, wird dem Benutzer in einer Fehlermeldung mitgeteilt, dass er nicht befugt ist, den Berechtigungen Ihrer App zuzustimmen.

Wenn Ihre App Bereiche für auf Administratoren beschränkte Berechtigungen erfordert, muss der Administrator einer Organisation seine Einwilligung für diese Bereiche im Namen der Organisationsbenutzer erteilen. Ihre App kann den Endpunkt für die Administratoreinwilligung verwenden, um zu vermeiden, dass von Benutzern eine Einwilligung für Berechtigungen angefordert wird, die sie nicht erteilen können. Informationen zum Endpunkt für die Administratoreinwilligung finden Sie im nächsten Abschnitt.

Wenn die Anwendung erhöhte delegierte Berechtigungen anfordert und ein Administrator diese über den Endpunkt für die Administratoreinwilligung gewährt, wird die Einwilligung für alle Benutzer im Mandanten erteilt.

Wenn die Anwendung Anwendungsberechtigungen anfordert und ein Administrator diese über den Endpunkt für die Administratoreinwilligung gewährt, erfolgt diese Gewährung nicht für einen bestimmten Benutzer. Stattdessen werden der Clientanwendung *direkt* Berechtigungen gewährt. Diese Berechtigungstypen werden nur von Daemondiensten und anderen nicht interaktiven Anwendungen verwendet, die im Hintergrund ausgeführt werden.

## <a name="using-the-admin-consent-endpoint"></a>Verwenden des Endpunkts für die Administratorzustimmung

Nach der Erteilung der Administratoreinwilligung über den Endpunkt für die Administratoreinwilligung sind keine weiteren Schritte erforderlich. Benutzer müssen nichts weiter tun. Nach Erteilung der Administratoreinwilligung können Benutzer ein Zugriffstoken über einen typischen Authentifizierungsfluss erhalten. Das resultierende Zugriffstoken verfügt über die Berechtigungen, für die die Einwilligung erteilt wurde.

Wenn ein globaler Administrator Ihre Anwendung verwendet und an den Autorisierungsendpunkt weitergeleitet wird, wird die Rolle des Benutzers von Microsoft Identity Platform erkannt. Der globale Administrator wird gefragt, ob er seine Einwilligung für die angeforderten Berechtigungen im Namen des gesamten Mandanten erteilen möchte. Stattdessen können Sie einen dedizierten Endpunkt für die Administratoreinwilligung verwenden, um einen Administrator proaktiv aufzufordern, eine Berechtigung für den gesamten Mandanten zu erteilen. Dieser Endpunkt wird auch zum Anfordern von Anwendungsberechtigungen benötigt. Anwendungsberechtigungen können nicht mithilfe des Autorisierungsendpunkts angefordert werden.

Anhand dieser Schritte kann Ihre App Berechtigungen für alle Benutzer in einem Mandanten anfordern, einschließlich der auf Administratoren beschränkten Bereiche. Dieser Vorgang ist mit erhöhten Berechtigungen verbunden. Verwenden Sie ihn nur, wenn dies für Ihr Szenario erforderlich ist.

Ein Codebeispiel, in dem die beschriebenen Schritte implementiert werden, finden Sie auf GitHub im [Beispiel für auf Administratoren beschränkte Bereiche](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Anfordern der Berechtigungen im App-Registrierungsportal

Im App-Registrierungsportal können Anwendungen die von ihnen benötigten Berechtigungen auflisten – einschließlich delegierter Berechtigungen und Anwendungsberechtigungen. Dies ermöglicht die Verwendung des Bereichs `/.default` und der Option **Administratoreinwilligung erteilen** im Azure-Portal.  

Im Allgemeinen sollten die Berechtigungen für eine bestimmte Anwendung statisch definiert werden. Dabei sollte es sich um eine übergeordnete Gruppe der Berechtigungen handeln, die von der App dynamisch oder inkrementell angefordert werden.

> [!NOTE]
>Anwendungsberechtigungen können nur über [`/.default`](#the-default-scope)angefordert werden. Wenn Ihre App Anwendungsberechtigungen benötigt, müssen Sie daher sicherstellen, dass diese im App-Registrierungsportal aufgeführt sind.

So konfigurieren Sie die Liste statisch angeforderter Berechtigungen für eine Anwendung:

1. Navigieren Sie in der Schnellstartumgebung <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure-Portal: App-Registrierungen</a> zu Ihrer Anwendung.
1. Wählen Sie eine Anwendung aus, oder [erstellen Sie ggf. eine App](quickstart-register-app.md).
1. Wählen Sie auf der Seite **Übersicht** der Anwendung unter **Verwalten** Folgendes aus: **API-Berechtigungen** > **Berechtigung hinzufügen**.
1. Wählen Sie in der Liste der verfügbaren APIs die Option **Microsoft Graph** aus. Fügen Sie dann die von Ihrer App benötigten Berechtigungen hinzu.
1. Klicken Sie auf **Berechtigungen hinzufügen**.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Empfohlen: Anmelden des Benutzers bei Ihrer App

Beim Erstellen einer Anwendung, die den Endpunkt für die Administratorzustimmung verwendet, benötigt die App in der Regel eine Seite/Ansicht, die dem Administrator das Genehmigen der App-Berechtigungen gestattet. Bei dieser Seite kann es sich um Folgendes handeln:

* Komponente des Registrierungsflusses der App
* Teil der App-Einstellungen
* Dedizierter Verbindungsfluss 

In vielen Fällen ist es sinnvoll, wenn die App diese Verbindungsansicht erst anzeigt, wenn ein Benutzer sich mit einem Geschäfts-, Schul- oder Unikonto von Microsoft angemeldet hat.

Durch die Anmeldung des Benutzers bei der App können Sie die Organisation identifizieren, der der Administrator angehört, bevor Sie zur Genehmigung der nötigen Berechtigungen auffordern. Dieser Schritt ist zwar nicht unbedingt erforderlich, kann aber dazu beitragen, eine intuitivere Benutzeroberfläche für Ihre Organisationsbenutzer zu erstellen. 

Gehen Sie gemäß den [Tutorials zum Microsoft Identity Platform-Protokoll](active-directory-v2-protocols.md) vor, um den Benutzer anzumelden.

### <a name="request-the-permissions-from-a-directory-admin"></a>Anfordern der Berechtigungen von einem Verzeichnisadministrator

Wenn Sie dazu bereit sind, vom Administrator der Organisation Berechtigungen anzufordern, können Sie den Benutzer zum Endpunkt für die Administratorzustimmung von Microsoft Identity Platform umleiten.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parameter        | Bedingung        | Beschreibung                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Erforderlich | Der Verzeichnismandant, von dem Sie die Berechtigung anfordern möchten. Er kann als GUID oder als Anzeigename bereitgestellt werden. Alternativ kann er generisch mit Organisationen referenziert werden, wie im Beispiel zu sehen. Verwenden Sie nicht „Allgemein“, da persönliche Konten die Administratoreinwilligung nur im Kontext eines Mandanten bereitstellen können. Verwenden Sie nach Möglichkeit die Mandanten-ID, um die bestmögliche Kompatibilität mit persönlichen Konten zu gewährleisten, die Mandanten verwalten. |
| `client_id` | Erforderlich | Die Anwendungs-ID (Client-ID), die Ihrer App [im Azure-Portal unter „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `redirect_uri` | Erforderlich |Der Umleitungs-URI, an den die Antwort zur Verarbeitung durch die App gesendet werden soll. Er muss genau mit einem der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben. |
| `state` | Empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Der Status wird verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z.B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
|`scope`        | Erforderlich        | Definiert den von der Anwendung angeforderten Satz an Berechtigungen. Bereiche können statisch (mit [`/.default`](#the-default-scope)) oder dynamisch sein.  Diese Gruppe kann die OpenID Connect-Bereiche (`openid`, `profile`, `email`) enthalten. Wenn Sie Anwendungsberechtigungen benötigen, müssen Sie `/.default` verwenden, um die statisch konfigurierte Liste der Berechtigungen anzufordern.  |


An diesem Punkt erzwingt Azure AD, dass sich nur ein Mandantenadministrator anmelden kann, um die Anforderung abzuschließen. Der Administrator wird gebeten, alle Berechtigungen zu genehmigen, die Sie im Parameter `scope` angefordert haben.  Bei Verwendung eines statischen Werts (`/.default`) funktioniert er wie der v1.0-Endpunkt für die Administratoreinwilligung und fordert die Zustimmung für alle Bereiche an, die in den erforderlichen Berechtigungen für die App gefunden werden.

#### <a name="successful-response"></a>Erfolgreiche Antwort

Wenn der Administrator die Berechtigungen für Ihre Anwendung genehmigt, lautet die erfolgreiche Antwort wie folgt:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `tenant` | Der Verzeichnismandant, der Ihrer Anwendung die angeforderten Berechtigungen erteilt hat, im GUID-Format. |
| `state` | Ein in der Anforderung enthaltener Wert, der auch in der Tokenantwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Der Status wird verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z.B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| `admin_consent` | Wird auf `True` festgelegt. |

#### <a name="error-response"></a>Fehlerantwort

Wenn der Administrator die Berechtigungen für Ihre App nicht genehmigt, lautet die Fehlerantwort wie folgt:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beschreibung |
| --- | --- |
| `error` | Eine Zeichenfolge mit einem Fehlercode, die zum Klassifizieren der auftretenden Fehlertypen verwendet werden kann. Sie kann auch verwendet werden, um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der ein Entwickler die Grundursache eines Fehlers identifizieren kann. |

Nachdem Sie eine erfolgreiche Antwort vom Endpunkt für die Administratorzustimmung erhalten haben, erhält Ihre App die Berechtigungen, die sie angefordert hat. Als Nächstes können Sie ein Token für die Ressource anfordern, die Sie möchten.

## <a name="using-permissions"></a>Verwenden von Berechtigungen

Nachdem der Benutzer seine Einwilligung für Berechtigungen Ihrer App erteilt hat, kann Ihre App Zugriffstoken abrufen, die die Berechtigung der App darstellen, in irgendeiner Weise auf die Ressource zuzugreifen. Ein Zugriffstoken kann nur für eine einzelne Ressource verwendet werden. Allerdings ist darin jede Berechtigung codiert, die Ihrer App für diese Ressource erteilt wurde. Um ein Zugriffstoken zu erhalten, kann Ihre App eine Anforderung an den Microsoft Identity Platform-Tokenendpunkt senden. Beispiel:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Sie können das resultierende Zugriffstoken in HTTP-Anforderungen an die Ressource verwenden. Es zeigt der Ressource zuverlässig, dass die App über die erforderliche Berechtigung für eine bestimmte Aufgabe verfügt.

Weitere Informationen zum OAuth 2.0-Protokoll und zum Abrufen von Zugriffstoken finden Sie in der [Protokollreferenz zum Microsoft Identity Platform-Endpunkt](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Der Bereich „/.default“

Sie können den `/.default`-Bereich verwenden, um die Migration Ihrer Anwendungen vom v1.0-Endpunkt zum Microsoft Identity Platform-Endpunkt zu unterstützen. Der Bereich `/.default` ist für jede Anwendung integriert, die sich auf die statische Liste der Berechtigungen bezieht, die bei der Anwendungsregistrierung konfiguriert wurde. 

Der Wert `https://graph.microsoft.com/.default` vom Typ `scope` ist funktionell identisch mit `resource=https://graph.microsoft.com` des v1.0-Endpunkts. Durch Angabe des Bereichs `https://graph.microsoft.com/.default` in der Anforderung fordert Ihre Anwendung ein Zugriffstoken an, das Bereiche für jede Microsoft Graph-Berechtigungen enthält, die Sie im App-Registrierungsportal für die App ausgewählt haben. Zur Erstellung des Bereichs wird der Ressourcen-URI mit `/.default` kombiniert. Wenn der Ressourcen-URI also `https://contosoApp.com` lautet, ist der angeforderte Bereich `https://contosoApp.com/.default`.  Informationen zu Fällen, in denen ein zweiter Schrägstrich erforderlich ist, damit das Token ordnungsgemäß angefordert wird, finden Sie im [Abschnitt zu nachgestellten Schrägstrichen](#trailing-slash-and-default).

Der Bereich `/.default` kann in einem beliebigen OAuth 2.0-Fluss verwendet werden. Im [On-Behalf-Of-Fluss](v2-oauth2-on-behalf-of-flow.md) und im [Fluss von Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md) ist er jedoch erforderlich. Er wird außerdem benötigt, wenn Sie den v2-Endpunkt für die Administratoreinwilligung verwenden, um Anwendungsberechtigungen anzufordern.

Clients können keine statische (`/.default`) und dynamische Einwilligung in einer einzelnen Anforderung kombinieren. `scope=https://graph.microsoft.com/.default+mail.read` führt daher zu einem Fehler, da hier Bereichstypen kombiniert werden.

### <a name="default-and-consent"></a>/.default und Einwilligung

Der `/.default`-Bereich löst auch das v1. 0-Endpunktverhalten für `prompt=consent` aus. Er fordert unabhängig von der Ressource eine Einwilligung für alle von der Anwendung registrierten Berechtigungen an. Wenn der Bereich `/.default` Teil der Anforderung ist, wird ein Token zurückgegeben, das die Bereiche für die angeforderte Ressource enthält.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, wenn der Benutzer bereits eingewilligt hat

Der Bereich `/.default` ist funktionell identisch mit dem Verhalten des `resource`-orientierten v1.0-Endpunkts. Er verfügt auch über das Einwilligungsverhalten v1.0-Endpunkts. Das bedeutet: Von `/.default` wird nur dann eine Einwilligungsaufforderung ausgelöst, wenn der Benutzer keine Berechtigung zwischen dem Client und der Ressource erteilt hat. 

Liegt eine solche Einwilligung vor, enthält das zurückgegebene Token alle Bereiche, die der Benutzer für diese Ressource gewährt hat. Wenn jedoch keine Berechtigung erteilt oder der Parameter `prompt=consent` angegeben wurde, wird eine Einwilligungsaufforderung für alle von der Clientanwendung registrierten Bereiche angezeigt.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Beispiel 1: Der Benutzer oder Administrator des Mandanten hat die Berechtigungen erteilt.

In diesem Beispiel hat der Benutzer (oder ein Mandantenadministrator) dem Client die Microsoft Graph-Berechtigungen `mail.read` und `user.read` erteilt. 

Wenn der Client `scope=https://graph.microsoft.com/.default` anfordert, wird unabhängig vom Inhalt der für Microsoft Graph registrierten Berechtigungen der Clientanwendungen keine Einwilligungsaufforderung angezeigt. Das zurückgegebene Token enthält die Bereiche `mail.read` und `user.read`.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Beispiel 2: Der Benutzer hat keine Berechtigungen zwischen dem Client und der Ressource erteilt.

In diesem Beispiel hat der Benutzer keine Einwilligung zwischen dem Client und Microsoft Graph erteilt. Der Client hat sich für die Berechtigungen `user.read` und `contacts.read` registriert. Außerdem hat er sich für den Azure Key Vault-Bereich `https://vault.azure.net/user_impersonation` registriert. 

Wenn der Client ein Token für `scope=https://graph.microsoft.com/.default` anfordert, wird dem Benutzer eine Einwilligungsseite für die Bereiche `user.read` und `contacts.read` sowie für die Key Vault-Bereiche vom Typ `user_impersonation` angezeigt. Das zurückgegebene Token enthält nur die Bereiche `user.read` und `contacts.read`. Es kann nur für Microsoft Graph verwendet werden.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Beispiel 3: Der Benutzer hat eingewilligt, und der Client fordert zusätzliche Bereiche an.

In diesem Beispiel hat der Benutzer bereits in `mail.read` für den Client eingewilligt. Der Client hat sich für den Bereich `contacts.read` registriert. 

Wenn der Client mithilfe von `scope=https://graph.microsoft.com/.default` ein Token und über `prompt=consent` eine Einwilligung anfordert, wird dem Benutzer eine Einwilligungsseite für alle von der Anwendung registrierten Berechtigungen angezeigt (und zwar nur für diese). Der Bereich `contacts.read` befindet sich auf der Einwilligungsseite, `mail.read` aber nicht. Das zurückgegebene Token gilt für Microsoft Graph. Es enthält `mail.read` und `contacts.read`.

### <a name="using-the-default-scope-with-the-client"></a>Verwenden des Bereichs „/.default“ mit dem Client

In bestimmten Fällen kann ein Client seinen eigenen Bereich vom Typ `/.default` anfordern. Das folgende Beispiel veranschaulicht dieses Szenario.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

In diesem Codebeispiel wird eine Einwilligungsseite für alle registrierten Berechtigungen generiert, wenn die obigen Beschreibungen von Einwilligung und `/.default` für das Szenario gelten. Anschließend gibt der Code kein Zugriffstoken, sondern ein ID-Token (`id_token`) zurück.  

Dieses Verhalten ist für einige Legacyclients geeignet, die von der Azure AD-Authentifizierungsbibliothek (ADAL) zur Microsoft-Authentifizierungsbibliothek (MSAL) migriert werden. Dieses Setup sollte *nicht* für neue Clients verwendet werden, die für Microsoft Identity Platform konzipiert sind.

### <a name="client-credentials-grant-flow-and-default"></a>Flow zum Gewähren von Clientanmeldeinformationen und Verwenden von „/.default“  

`/.default` kann auch verwendet werden, um Anwendungsberechtigungen (oder *Rollen*) in einer nicht interaktiven Anwendung wie etwa einer Daemon-App anzufordern, die den Fluss zum Gewähren von [Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md) verwendet, um eine Web-API aufzurufen.

Informationen zum Erstellen von Anwendungsberechtigungen (Rollen) für eine Web-API finden Sie unter [Vorgehensweise: Hinzufügen von App-Rollen zu Ihrer Anwendung und Empfangen der Rollen im Token](howto-add-app-roles-in-azure-ad-apps.md).

Anforderungen von Clientanmeldeinformationen in Ihrer Client-App *müssen* `scope={resource}/.default` enthalten. `{resource}` ist in diesem Fall die Web-API, die Ihre App aufrufen möchte. Das Erstellen einer Anforderung von Clientanmeldeinformationen mit individuellen Anwendungsberechtigungen (Rollen) wird *nicht* unterstützt. Alle Anwendungsberechtigungen (Rollen), die für diese Web-API gewährt wurden, sind in dem zurückgegebenen Zugriffstoken enthalten.

Informationen zum Gewähren von Zugriff auf die von Ihnen definierten Anwendungsberechtigungen, einschließlich der Administratoreinwilligung für die Anwendung, finden Sie unter [Schnellstart: Konfigurieren einer Clientanwendung für den Zugriff auf eine Web-API](quickstart-configure-app-access-web-apis.md).

### <a name="trailing-slash-and-default"></a>Nachgestellter Schrägstrich und „/.default“

Einige Ressourcen-URIs enthalten einen nachgestellten Schrägstrich (beispielsweise `https://contoso.com/` anstelle von `https://contoso.com`). Der nachgestellte Schrägstrich kann zu Problemen bei der Tokenüberprüfung führen. Probleme treten in erster Linie auf, wenn ein Token für Azure Resource Manager (`https://management.azure.com/`) angefordert wird. In diesem Fall bedeutet ein nachgestellter Schrägstrich im Ressourcen-URI, dass der Schrägstrich vorhanden sein muss, wenn das Token angefordert wird.  Wenn Sie also ein Token für `https://management.azure.com/` anfordern möchten und dabei `/.default` verwenden, müssen Sie `https://management.azure.com//.default` (mit doppeltem Schrägstrich) anfordern. Im Allgemeinen gilt: Wenn Sie sich vergewissert haben, dass das Token ausgestellt wird, und das Token von der API abgelehnt wird, obwohl sie es eigentlich akzeptieren sollte, können Sie versuchen, einen zweiten Schrägstrich hinzufügen und den Vorgang zu wiederholen. 

## <a name="troubleshooting-permissions-and-consent"></a>Problembehandlung für Berechtigungen und Einwilligungen

Problembehandlungsschritte finden Sie unter [Unerwarteter Fehler beim Vorgang des Genehmigens einer Anwendung](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Nächste Schritte

* [Microsoft Identity Platform – ID-Token](id-tokens.md)
* [Microsoft Identity Platform – Zugriffstoken](access-tokens.md)
