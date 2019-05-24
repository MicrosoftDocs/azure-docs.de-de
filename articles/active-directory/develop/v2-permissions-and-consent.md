---
title: Bereiche, Berechtigungen und Zustimmung für die Microsoft Identity Platform | Microsoft-Dokumentation
description: Beschreibung der Autorisierung im Microsoft Identity Platform-Endpunkt, einschließlich Bereichen, Berechtigungen und Zustimmung.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 717607de6d9d193a7373637d0d0fcd879b54fed0
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544880"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Anwendungen, die sich in die Microsoft Identity Platform integrieren lassen, folgen einem bestimmten Autorisierungsmodell, mit dem Benutzer und Administratoren den Zugriff auf Daten steuern können. Die Implementierung des Autorisierungsmodells wurde für den Microsoft Identity Platform-Endpunkt aktualisiert. Eine App muss daher jetzt auf andere Weise mit der Microsoft Identity Platform interagieren. Dieser Artikel behandelt die grundlegenden Konzepte dieses Autorisierungsmodells einschließlich der Bereiche, Berechtigungen und Zustimmung.

> [!NOTE]
> Der Microsoft Identity Platform-Endpunkt unterstützt nicht alle Szenarien und Features. Informieren Sie sich über die [Einschränkungen von Microsoft Identity Platform](active-directory-v2-limitations.md), um zu bestimmen, ob Sie den Microsoft Identity Platform-Endpunkt verwenden sollten.

## <a name="scopes-and-permissions"></a>Bereiche und Berechtigungen

Die Microsoft Identity Platform implementiert das [OAuth 2.0](active-directory-v2-protocols.md)-Autorisierungsprotokoll. OAuth 2.0 ist eine Methode, über die eine Drittanbieter-App im Auftrag eines Benutzers auf im Web gehostete Ressourcen zugreifen kann. Alle im Web gehosteten Ressourcen, die in die Microsoft Identity Platform integriert werden können, verfügen über einen Ressourcenbezeichner bzw. *Anwendungs-ID-URI*. Zu den von Microsoft im Web gehosteten Ressourcen zählen beispielsweise:

* Microsoft Graph: `https://graph.microsoft.com`
* API für Office 365-E-Mail: `https://outlook.office.com`
* Microsoft Azure AD Graph: `https://graph.windows.net`

> [!NOTE]
> Es wird dringend empfohlen, Microsoft Graph anstelle von Microsoft Azure AD Graph, der API für Office 365-E-Mail usw. zu verwenden.

Dasselbe gilt für alle Ressourcen von Drittanbietern, die in die Microsoft Identity Platform integriert wurden. Diese Ressourcen können auch einen Satz von Berechtigungen definieren, die zum Unterteilen der Funktionalität der Ressource in kleinere Einheiten verwendet werden können. Beispielsweise verfügt [Microsoft Graph](https://graph.microsoft.com) über definierte Berechtigungen, um unter anderem folgende Aufgaben auszuführen:

* Lesen des Kalenders eines Benutzers
* Schreiben in den Kalender eines Benutzers
* Senden von E-Mails als Benutzer

Durch das Definieren dieser Berechtigungstypen hat die Ressource eine präzisere Kontrolle über die Daten und die Art und Weise, wie API-Funktionen verfügbar gemacht werden. Eine Drittanbieter-App kann diese Berechtigungen von Benutzern und Administratoren anfordern. Diese müssen die Anforderung dann genehmigen, bevor die App auf Daten zugreifen oder im Namen eines Benutzers agieren kann. Durch Segmentieren der Ressourcenfunktionalität in kleinere Berechtigungssätze können Drittanbieter-Apps so erstellt werden, dass nur die spezifischen Berechtigungen angefordert werden, die diese Apps zum Durchführen ihrer Funktion benötigen. Benutzer und Administratoren können genau erkennen, auf welche Daten die App zugreifen kann, sodass sie sicher sein können, dass die App keine böswilligen Absichten verfolgt. Entwickler sollten sich immer an die Regel der geringsten Rechte halten und nur die Berechtigungen anfordern, die für die Funktion ihrer Anwendungen erforderlich sind.

In OAuth 2.0 werden diese Berechtigungstypen *Bereiche* genannt. Oft werden sie auch als *Berechtigungen* bezeichnet. Eine Berechtigung wird in der Microsoft Identity Platform als Zeichenfolgenwert dargestellt. Im Beispiel von Microsoft Graph lautet der Zeichenfolgenwert für jede Berechtigung wie folgt:

* Lesen des Kalenders eines Benutzers mit `Calendars.Read`
* Schreiben in den Kalender eines Benutzers mit `Calendars.ReadWrite`
* Senden von E-Mails als Benutzer mit `Mail.Send`

Eine App fordert diese Berechtigungen meist durch Angabe der Bereiche in Anforderungen an den Microsoft Identity Platform-Autorisierungsendpunkt an. Bestimmte erhöhte Berechtigungen können jedoch nur mit der Einwilligung des Administrators gewährt werden und werden mit dem [Endpunkt für die Administratoreinwilligung](v2-permissions-and-consent.md#admin-restricted-permissions) angefordert/gewährt. Weitere Informationen finden Sie im Folgenden.

## <a name="permission-types"></a>Berechtigungstypen

Die Microsoft Identity Platform unterstützt zwei Arten von Berechtigungen: **delegierte Berechtigungen** und **Anwendungsberechtigungen**.

* **Delegierte Berechtigungen** werden von Apps verwendet, bei denen ein angemeldeter Benutzer vorhanden ist. Für diese Apps willigt entweder der Benutzer oder ein Administrator in die Erteilung der von der App angeforderten Berechtigungen ein, und an die App wird die Berechtigung delegiert, bei Aufrufen der Zielressource als der angemeldete Benutzer zu agieren. Bei manchen delegierten Berechtigungen können Benutzer ohne Administratorrechte die Einwilligung erteilen, einige erhöhte Berechtigungen erfordern jedoch eine [Administratoreinwilligung](v2-permissions-and-consent.md#admin-restricted-permissions). Um zu erfahren, welche Administratorrollen delegierten Berechtigungen zustimmen können, lesen Sie [Berechtigungen der Administratorrolle in Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Anwendungsberechtigungen** werden von Apps verwendet, die ohne vorhandenen angemeldeten Benutzer ausgeführt werden. Dies können beispielsweise Apps sein, die als Hintergrunddienste oder Daemons ausgeführt werden.  Für Anwendungsberechtigungen ist immer die [Einwilligung eines Administrators](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) erforderlich.

_Effektive Berechtigungen_ sind die Berechtigungen, über die Ihre App verfügt, wenn sie Anforderungen an die Zielressource sendet. Es ist wichtig, den Unterschied zwischen den delegierten Berechtigungen und Anwendungsberechtigungen, die Ihrer App gewährt werden, und ihren effektiven Berechtigungen beim Durchführen von Aufrufen an die Zielressource zu verstehen.

- Bei delegierten Berechtigungen sind die _effektiven Berechtigungen_ Ihrer App die jeweils geringsten Rechte, die sich zusammen genommen aus den delegierten Berechtigungen, die der App (per Einwilligung) gewährt wurden, und den Berechtigungen des derzeit angemeldeten Benutzers ergeben. Ihre App kann niemals über mehr Berechtigungen als der angemeldete Benutzer verfügen. In Organisationen können die Berechtigungen des angemeldeten Benutzers anhand einer Richtlinie oder der Mitgliedschaft in einer oder mehreren Administratorrollen bestimmt werden. Um zu erfahren, welche Administratorrollen delegierten Berechtigungen zustimmen können, lesen Sie [Berechtigungen der Administratorrolle in Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Angenommen, Ihrer App wurde die delegierte Berechtigung _User.ReadWrite.All_ gewährt. Mit dieser Berechtigung wird Ihrer App nominell die Berechtigung zum Lesen und Aktualisieren des Profils jedes Benutzers einer Organisation gewährt. Wenn es sich beim angemeldeten Benutzer um einen globalen Administrator handelt, kann Ihre App das Profil jedes Benutzers in der Organisation aktualisieren. Falls der angemeldete Benutzer aber keine Administratorrolle innehat, kann Ihre App nur das Profil des angemeldeten Benutzers aktualisieren. Sie kann die Profile von anderen Benutzern der Organisation nicht aktualisieren, da der Benutzer, für den die App die Berechtigung zum Durchführen von Aktionen in dessen Namen hat, nicht über diese Berechtigungen verfügt.
  
- Bei Anwendungsberechtigungen umfassen die _effektiven Berechtigungen_ Ihrer App die vollständige Berechtigungsstufe, die mit der Berechtigung verbunden ist. Beispielsweise kann eine App mit der Anwendungsberechtigung _User.ReadWrite.All_ das Profil aller Benutzer in der Organisation aktualisieren. 

## <a name="openid-connect-scopes"></a>OpenID Connect-Bereiche

Die Microsoft Identity Platform-Implementierung von OpenID Connect bietet einige klar definierte Bereiche, die für keine bestimmte Ressource gelten: `openid`, `email`, `profile` und `offline_access`. Die OpenID Connect-Bereiche `address` und `phone` werden nicht unterstützt.

### <a name="openid"></a>openid

Bei einer App-Anmeldung mit [OpenID Connect](active-directory-v2-protocols.md) muss der `openid`-Bereich angefordert werden. Der `openid`-Bereich wird auf der Zustimmungsseite des Arbeitskontos als Berechtigung „Sie werden angemeldet“ angezeigt, und auf der Zustimmungsseite des persönlichen Microsoft-Kontos als Berechtigung „Ihr Profil anzeigen und mit Ihrem Microsoft-Konto eine Verbindung zu Apps und Diensten herstellen“. Mit dieser Berechtigung kann eine App einen eindeutigen Bezeichner für den Benutzer in Form des Anspruchs `sub` empfangen. Sie ermöglicht der App zudem Zugriff auf den Endpunkt mit den Benutzerinformationen. Der Bereich `openid` kann am Microsoft Identity Platform-Tokenendpunkt zum Abrufen von ID-Token verwendet werden, die wiederum von der App für die Authentifizierung verwendet werden können.

### <a name="email"></a>email

Der Bereich `email` kann zusammen mit dem Bereich `openid` und weiteren Bereichen verwendet werden. Er gibt der App Zugriff auf die primäre E-Mail-Adresse des Benutzers in Form des Anspruchs `email` . Der Anspruch `email` ist nur in einem Token enthalten, wenn dem Benutzerkonto eine E-Mail-Adresse zugewiesen ist (dies ist nicht immer der Fall). Bei Verwendung des Bereichs `email` sollte die Verarbeitung durch Ihre App auch dann möglich sein, wenn der Anspruch `email` nicht im Token vorhanden ist.

### <a name="profile"></a>Profil

Der Bereich `profile` kann zusammen mit dem Bereich `openid` und weiteren Bereichen verwendet werden. Er ermöglicht der App Zugriff auf eine Vielzahl von Benutzerinformationen. Dazu gehören u.a. Vorname, Nachname, bevorzugter Benutzername und Objekt-ID des Benutzers. Eine vollständige Liste der verfügbaren Profilansprüche im Parameter „id_tokens“ für einen bestimmten Benutzer finden Sie in der [`id_tokens`-Referenz](id-tokens.md).

### <a name="offlineaccess"></a>offline_access

Mit dem [`offline_access`-Bereich](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) kann Ihre App im Auftrag des Benutzers für einen längeren Zeitraum auf Ressourcen zugreifen. Auf der Einwilligungsseite wird dieser Bereich als Berechtigung „Zugriff auf Daten erhalten, auf die Sie Zugriff gewährt haben“ angezeigt. Wenn ein Benutzer den `offline_access`-Bereich genehmigt, kann Ihre App Aktualisierungstoken vom Microsoft Identity Platform-Tokenendpunkt empfangen. Aktualisierungstoken sind langlebig. Ihrer App kann neue Zugriffstoken abrufen, wenn ältere ablaufen.

Wenn die App den `offline_access`-Bereich nicht explizit anfordert, werden auch keine Aktualisierungstoken empfangen. Dies bedeutet, dass Sie beim Einlösen eines Autorisierungscodes im [OAuth 2.0-Autorisierungscodefluss](active-directory-v2-protocols.md) nur ein Zugriffstoken vom `/token`-Endpunkt erhalten. Das Zugriffstoken ist für eine kurze Zeit gültig. Das Zugriffstoken läuft in der Regel nach einer Stunde ab. Zu diesem Zeitpunkt muss Ihre App den Benutzer zurück auf den `/authorize`-Endpunkt leiten, um einen neuen Autorisierungscode abzurufen. Während dieser Umleitung muss der Benutzer möglicherweise seine Anmeldeinformationen erneut eingeben oder den Berechtigungen erneut zustimmen, je nach App-Typ. Während der `offline_access`-Bereich automatisch vom Server angefordert wird, muss Ihr Client ihn aber trotzdem anfordern, um die Aktualisierungstoken zu empfangen.

Weitere Informationen zum Abrufen und Verwenden von Aktualisierungstoken finden Sie in der [Microsoft Identity Platform-Protokollreferenz](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Anfordern der Zustimmung einzelner Benutzer

In einer Autorisierungsanforderung von [OpenID Connect oder OAuth 2.0](active-directory-v2-protocols.md) kann eine App die erforderlichen Berechtigungen mithilfe des `scope`-Abfrageparameters anfordern. Wenn sich ein Benutzer beispielsweise in einer App anmeldet, sendet die App eine Anforderung wie die folgende (mit Zeilenumbrüchen zur besseren Lesbarkeit):

```
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

Der `scope`-Parameter ist eine durch Leerzeichen getrennte Liste von delegierten Berechtigungen, die von der App angefordert werden. Jede Berechtigung wird durch Anfügen des Berechtigungswerts an den Ressourcenbezeichner (Anwendungs-ID-URI) angegeben. Die Anforderung im Beispiel gibt an, dass die Anwendung eine Berechtigung zum Lesen des Kalenders des Benutzers und Senden von E-Mails als Benutzer benötigt.

Nachdem der Benutzer seine Anmeldeinformationen eingegeben hat, überprüft der Microsoft Identity Platform-Endpunkt, ob es einen übereinstimmenden Datensatz der *Benutzerzustimmung* gibt. Wenn der Benutzer in der Vergangenheit für keine der angeforderten Berechtigungen seine Einwilligung erteilt hat und auch kein Administrator für die gesamte Organisation in die Erteilung dieser Berechtigungen eingewilligt hat, fordert der Microsoft Identity Platform-Endpunkt den Benutzer auf, die angeforderten Berechtigungen zu gewähren.

> [!NOTE]
> Zu diesem Zeitpunkt werden die Berechtigungen `offline_access` („Zugriff auf Daten erhalten, auf die Sie Zugriff gewährt haben“) und `user.read` („Anmelden und Ihr Profil lesen“) automatisch in die erste Einwilligung für eine Anwendung aufgenommen.  Diese Berechtigungen sind im Allgemeinen für die ordnungsgemäße Funktionalität der App erforderlich – `offline_access` gibt der App Zugriff auf Aktualisierungstoken, die für native und Web-Apps entscheidend sind, während `user.read` Zugriff auf den `sub`-Anspruch gewährt, sodass der Client oder die App den Benutzer im Laufe der Zeit korrekt identifizieren und auf rudimentäre Benutzerinformationen zugreifen kann.  

![Zustimmung im Arbeitskonto](./media/v2-permissions-and-consent/work_account_consent.png)

Wenn der Benutzer die Berechtigungsanforderung genehmigt, wird die Einwilligung aufgezeichnet, und der Benutzer muss bei nachfolgenden Anmeldungen bei der Anwendung nicht erneut seine Einwilligung erteilen.

## <a name="requesting-consent-for-an-entire-tenant"></a>Anfordern der Zustimmung für einen gesamten Mandanten

Organisationen, die Lizenzen oder Abonnements für eine Anwendung erwerben, möchten die Anwendung oft proaktiv zur Verwendung durch alle Mitglieder der Organisation einrichten. Im Rahmen dieses Prozesses kann ein Administrator der Anwendung die Einwilligung erteilen, im Auftrag beliebiger Benutzer im Mandanten zu agieren. Wenn der Administrator seine Einwilligung für den gesamten Mandanten erteilt, sehen die Benutzer der Organisation keine Einwilligungsseite für die Anwendung.

Um die Einwilligung für delegierte Berechtigungen für alle Benutzer in einem Mandanten anzufordern, kann Ihre App den Endpunkt für die Administratoreinwilligung verwenden.

Darüber hinaus müssen Anwendungen den Endpunkt für die Administratoreinwilligung zum Anfordern von Anwendungsberechtigungen verwenden.

## <a name="admin-restricted-permissions"></a>Auf den Administrator beschränkte Berechtigungen

Einige hochrangige Berechtigungen im Microsoft-Ökosystem können als *auf den Administrator beschränkt* gesetzt werden. Im Folgenden sehen Sie einige Beispiele für derartige Berechtigungen:

* Lesen der vollständigen Profile aller Benutzer mit `User.Read.All`
* Schreiben von Daten in das Verzeichnis einer Organisation mit `Directory.ReadWrite.All`
* Lesen aller Gruppen im Verzeichnis einer Organisation mit `Groups.Read.All`

Obwohl ein Endbenutzer einer Anwendung den Zugriff auf diese Daten gewähren kann, dürfen Organisationsbenutzer den Zugriff auf denselben Satz sensibler Unternehmensdaten nicht erteilen. Wenn Ihre Anwendung von einem Organisationsbenutzer Zugriff auf eine dieser Berechtigungen anfordert, wird dem Benutzer in einer Fehlermeldung mitgeteilt, dass er nicht befugt ist, den Berechtigungen Ihrer App zuzustimmen.

Wenn Ihre App den Zugriff auf diese dem Administrator vorbehaltenen Bereiche für Organisationen benötigt, sollten Sie diese ebenfalls über den Endpunkt für die Administratorzustimmung direkt von einem Unternehmensadministrator anfordern, wie im Folgenden beschrieben.

Wenn die Anwendung erhöhte delegierte Berechtigungen anfordert und ein Administrator diese über den Endpunkt für die Administratoreinwilligung gewährt, wird die Einwilligung für alle Benutzer im Mandanten erteilt.

Wenn die Anwendung Anwendungsberechtigungen anfordert und ein Administrator diese über den Endpunkt für die Administratoreinwilligung gewährt, erfolgt diese Gewährung nicht für einen bestimmten Benutzer. Stattdessen werden der Clientanwendung *direkt* Berechtigungen gewährt. Diese Berechtigungstypen werden nur von Daemondiensten und anderen nicht interaktiven Anwendungen verwendet, die im Hintergrund ausgeführt werden.

## <a name="using-the-admin-consent-endpoint"></a>Verwenden des Endpunkts für die Administratorzustimmung

Wenn ein Unternehmensadministrator Ihre Anwendung verwendet und zum Autorisierungsendpunkt geleitet wird, erkennt die Microsoft Identity Platform die Rolle des Benutzers, und er wird gefragt, ob er seine Einwilligung für die angeforderten Berechtigungen für den gesamten Mandanten erteilen möchte. Es ist jedoch auch ein dedizierter Endpunkt für die Administratoreinwilligung verfügbar, den Sie verwenden können, wenn Sie die Gewährung von Berechtigungen proaktiv für den gesamten Mandanten von einem Administrator anfordern möchten. Dieser Endpunkt ist auch zum Anfordern von Anwendungsberechtigungen erforderlich (diese können nicht mit dem Autorisierungsendpunkt angefordert werden).

Anhand dieser Schritte kann Ihre App Berechtigungen für alle Benutzer in einem Mandanten anfordern, einschließlich der auf Administratoren beschränkten Bereiche. Dieser Vorgang erfordert erhöhte Rechte und sollte nur ausgeführt werden, wenn er für Ihr Szenario erforderlich ist.

Ein Codebeispiel, in dem die beschriebenen Schritte implementiert sind, finden Sie unter [Beispiel für auf Administratoren beschränkte Bereiche](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Anfordern der Berechtigungen im App-Registrierungsportal

Die Administratoreinwilligung akzeptiert keinen Bereichsparameter. Alle angeforderten Berechtigungen müssen daher in der Registrierung der Anwendung statisch definiert sein. Stellen Sie nach Möglichkeit sicher, dass die statisch definierten Berechtigungen für eine bestimmte Anwendung eine Obermenge der Berechtigungen sind, die die Anwendung dynamisch/inkrementell anfordert.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>So konfigurieren Sie die Liste statisch angeforderter Berechtigungen für eine Anwendung

1. Wechseln Sie im [Azure-Portal – App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) zu Ihrer Anwendung oder [erstellen Sie eine App](quickstart-register-app.md), sofern noch nicht geschehen.
2. Suchen Sie den Abschnitt **Microsoft Graph-Berechtigungen**, und fügen Sie die Berechtigungen hinzu, die von Ihrer App benötigt werden.
3. **Speichern** Sie die App-Registrierung.

### <a name="recommended-sign-the-user-into-your-app"></a>Empfohlen: Anmelden des Benutzers bei Ihrer App

Beim Erstellen einer Anwendung, die den Endpunkt für die Administratorzustimmung verwendet, benötigt die App in der Regel eine Seite/Ansicht, die dem Administrator das Genehmigen der App-Berechtigungen gestattet. Diese Seite kann Teil des Anmelde-Flows der App, Teil der App-Einstellungen oder ein dedizierter Flow „Verbinden“ sein. In vielen Fällen ist es sinnvoll, wenn die App diese Ansicht „Verbinden“ erst anzeigt, wenn ein Benutzer sich mit einem Geschäfts-, Schul- oder Unikonto von Microsoft angemeldet hat.

Durch das Anmelden des Benutzers bei der App können Sie die Organisation identifizieren, der der Administrator angehört, bevor Sie zur Genehmigung der nötigen Berechtigungen auffordern. Auch wenn es nicht unbedingt erforderlich ist, können Sie für Ihre Organisationsbenutzer auf diese Weise eine intuitivere Benutzeroberfläche erstellen. Befolgen Sie unsere Tutorials zum [Microsoft Identity Platform-Protokoll](active-directory-v2-protocols.md), um den Benutzer anzumelden.

### <a name="request-the-permissions-from-a-directory-admin"></a>Anfordern der Berechtigungen von einem Verzeichnisadministrator

Wenn Sie dazu bereit sind, vom Administrator der Organisation Berechtigungen anzufordern, können Sie den Benutzer zum *Endpunkt für die Administratorzustimmung* von Microsoft Identity Platform umleiten.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich | Der Verzeichnismandant, von dem Sie die Berechtigung anfordern möchten. Kann als eindeutiger Bezeichner oder Anzeigename bereitgestellt oder mit `common` generisch referenziert werden, wie im Beispiel gezeigt. |
| `client_id` | Erforderlich | Die **Anwendungs-ID (Client-ID)**, die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `redirect_uri` | Erforderlich |Der Umleitungs-URI, an den die Antwort zur Verarbeitung durch die App gesendet werden soll. Er muss genau mit einem der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben. |
| `state` | Empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Der Status wird verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z.B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |

An diesem Punkt erzwingt Azure AD, dass sich nur ein Mandantenadministrator anmelden kann, um die Anforderung abzuschließen. Der Administrator wird aufgefordert, alle Berechtigungen zu genehmigen, die Sie für Ihre App im Registrierungsportal angefordert haben.

#### <a name="successful-response"></a>Erfolgreiche Antwort

Wenn der Administrator die Berechtigungen für Ihre Anwendung genehmigt, lautet die erfolgreiche Antwort wie folgt:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `tenant` | Der Verzeichnismandant, der Ihrer Anwendung die angeforderten Berechtigungen erteilt hat, im GUID-Format. |
| `state` | Ein in der Anforderung enthaltener Wert, der auch in der Tokenantwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Der Status wird verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z.B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| `admin_consent` | Wird auf `True` festgelegt. |

#### <a name="error-response"></a>Fehlerantwort

Wenn der Administrator die Berechtigungen für Ihre App nicht genehmigt, lautet die Fehlerantwort wie folgt:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der ein Entwickler die Grundursache eines Fehlers identifizieren kann. |

Nachdem Sie eine erfolgreiche Antwort vom Endpunkt für die Administratorzustimmung erhalten haben, erhält Ihre App die Berechtigungen, die sie angefordert hat. Als Nächstes können Sie ein Token für die Ressource anfordern, die Sie möchten.

## <a name="using-permissions"></a>Verwenden von Berechtigungen

Nachdem der Benutzer den Berechtigungen für Ihre App zugestimmt hat, kann Ihre App Zugriffstoken abrufen, die für die Berechtigung der App stehen, in irgendeiner Weise auf die Ressource zugreifen zu dürfen. Ein Zugriffstoken kann nur für eine einzelne Ressource verwendet werden, allerdings ist darin jede Berechtigung codiert, die Ihrer App für diese Ressource erteilt wurde. Um ein Zugriffstoken zu erhalten, kann Ihre App eine Anforderung an den Microsoft Identity Platform-Tokenendpunkt senden. Beispiel:

```
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

Sie können das resultierende Zugriffstoken in HTTP-Anforderungen an die Ressource verwenden. Es zeigt der Ressource zuverlässig, dass die App über die erforderliche Berechtigung verfügt, eine bestimmte Aufgabe auszuführen. 

Weitere Informationen zum OAuth 2.0-Protokoll und zum Abrufen von Zugriffstoken finden Sie in der [Protokollreferenz zum Microsoft Identity Platform-Endpunkt](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Der Bereich „/.default“

Sie können den `/.default`-Bereich verwenden, um die Migration Ihrer Anwendungen vom v1.0-Endpunkt zum Microsoft Identity Platform-Endpunkt zu unterstützen. Dies ist ein integrierter Bereich für jede Anwendung, der sich auf die statische Liste der Berechtigungen bezieht, die bei der Anwendungsregistrierung konfiguriert wurde. Ein `scope`-Wert von `https://graph.microsoft.com/.default` ist funktionell identisch mit den v1.0-Endpunkten `resource=https://graph.microsoft.com` – d.h. es wird ein Token mit den Bereichen auf Microsoft Graph angefordert, für den die Anwendung im Azure-Portal registriert wurde.

Der Bereich „/.default“ kann in jedem OAuth 2.0-Flow verwendet werden, ist aber im [On-Behalf-Of-Fluss](v2-oauth2-on-behalf-of-flow.md) und im [Flow für Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md) erforderlich.  

> [!NOTE]
> Clients können keine statische (`/.default`) und dynamische Einwilligung in einer einzelnen Anforderung kombinieren. Daher führt `scope=https://graph.microsoft.com/.default+mail.read` aufgrund der Kombination aus Bereichstypen zu einem Fehler.

### <a name="default-and-consent"></a>/.default und Einwilligung

Der `/.default`-Bereich löst auch das v1. 0-Endpunktverhalten für `prompt=consent` aus. Es wird die Einwilligung für alle von der Anwendung registrierten Berechtigungen angefordert, unabhängig von der Ressource. Wenn der Bereich `/.default` als Teil der Anforderung eingebunden ist, gibt er ein Token zurück, das die Bereiche für die angeforderte Ressource enthält.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, wenn der Benutzer bereits eingewilligt hat

Da `/.default` funktionell identisch mit dem Verhalten des `resource`-zentrierten v1.0-Endpunkts ist, bringt es auch das Einwilligungsverhalten des v1.0-Endpunkts mit sich. Das heißt, `/.default` löst nur dann eine Einwilligungsaufforderung aus, wenn der Benutzer keine Berechtigung zwischen dem Client und der Ressource erteilt hat. Wenn eine solche Einwilligung vorliegt, wird ein Token mit allen Bereichen zurückgegeben, die der Benutzer für diese Ressource gewährt hat. Wenn jedoch keine Berechtigung erteilt wurde oder der Parameter `prompt=consent` angegeben wurde, wird eine Einwilligungsaufforderung für alle von der Clientanwendung registrierten Bereiche angezeigt.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Beispiel 1: Der Benutzer oder Administrator des Mandanten hat die Berechtigungen erteilt.

Der Benutzer (oder ein Mandantenadministrator) hat dem Client die Microsoft Graph-Berechtigungen `mail.read` und `user.read` erteilt. Wenn der Client eine Anforderung für `scope=https://graph.microsoft.com/.default` stellt, wird unabhängig vom Inhalt der für Microsoft Graph registrierten Berechtigungen der Clientanwendungen keine Einwilligungsaufforderung angezeigt. Es wird ein Token mit den Bereichen `mail.read` und `user.read` zurückgegeben.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Beispiel 2: Der Benutzer hat keine Berechtigungen zwischen dem Client und der Ressource erteilt.

Es gibt keine Einwilligung für den Benutzer zwischen dem Client und Microsoft Graph. Der Client ist für die Berechtigungen `user.read` und `contacts.read` sowie den Bereich „Azure Key Vault“ `https://vault.azure.net/user_impersonation` registriert. Wenn der Client ein Token für `scope=https://graph.microsoft.com/.default` anfordert, sieht der Benutzer einen Einwilligungsbildschirm für die Bereiche `user.read`, `contacts.read` und Key Vault `user_impersonation`. Das zurückgegebene Token enthält nur die Bereiche `user.read` und `contacts.read`.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Beispiel 3: Der Benutzer hat eingewilligt und der Client fordert zusätzliche Bereiche an.

Der Benutzer hat bereits in `mail.read` für den Client eingewilligt. Der Client hat sich in seiner Registrierung für den Bereich `contacts.read` registriert. Wenn der Client eine Anforderung für ein Token mit `scope=https://graph.microsoft.com/.default` stellt und die Einwilligung über `prompt=consent` anfordert, sieht der Benutzer einen Einwilligungsbildschirm für alle von der Anwendung registrierten Berechtigungen. `contacts.read` wird im Einwilligungsbildschirm angezeigt, `mail.read` aber nicht. Das zurückgegebene Token gilt für Microsoft Graph und enthält `mail.read` und `contacts.read`.

### <a name="using-the-default-scope-with-the-client"></a>Verwenden des Bereichs „/.default“ mit dem Client

Ein Sonderfall des Bereichs `/.default` liegt vor, wenn ein Client seinen eigenen Bereich `/.default` anfordert. Das folgende Beispiel veranschaulicht dieses Szenario.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Dies erzeugt einen Einwilligungsbildschirm für alle registrierten Berechtigungen (falls zutreffend basierend auf den obigen Beschreibungen der Einwilligung und `/.default`), und gibt dann ein id_token statt ein Zugriffstoken zurück.  Dieses Verhalten gilt für bestimmte Legacy-Clients, die von ADAL nach MSAL wechseln, und sollte nicht von neuen Clients verwendet werden, die auf den Microsoft Identity Platform-Endpunkt ausgerichtet sind.  

## <a name="troubleshooting-permissions-and-consent"></a>Problembehandlung für Berechtigungen und Einwilligungen

Falls Sie oder die Benutzer Ihrer Anwendung während des Einwilligungsprozesses unerwartete Fehler feststellen, finden Sie diesem Artikel Anleitungen zur Problembehandlung: [Unerwarteter Fehler beim Vorgang des Genehmigens einer Anwendung](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
