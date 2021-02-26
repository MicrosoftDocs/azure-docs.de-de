---
title: Hinzufügen von App-Rollen und Abrufen dieser Rollen aus einem Token | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie App-Rollen zu einer bei Azure Active Directory registrierten Anwendung hinzufügen, diesen Rollen Benutzer und Gruppen zuweisen und sie im Rollenanspruch im Token empfangen.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: fce963bd9ffdc6f768d7b3de4a9e4870add06136
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104245"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>Vorgehensweise: Hinzufügen von App-Rollen zu Ihrer Anwendung und Empfangen der Rollen im Token

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) ist eine beliebte Methode zum Erzwingen der Autorisierung in Anwendungen. Bei der Verwendung der RBAC gewährt ein Administrator Berechtigungen für Rollen und nicht für einzelne Benutzer oder Gruppen. Der Administrator kann anschließend verschiedenen Benutzern und Gruppen Rollen zuweisen, um zu steuern, wer auf welche Inhalte und Funktionen zugreifen kann.

Entwickler können die RBAC mit Anwendungsrollen und Rollenansprüchen verwenden und dadurch mit weniger Aufwand sicher die Autorisierung in ihren Apps erzwingen.

Ein weiterer Ansatz ist die Verwendung von Azure AD-Gruppen und Gruppenansprüchen wie im [active-directory-aspnetcore-webapp-openidconnect-v2](https://aka.ms/groupssample)-Codebeispiel auf GitHub gezeigt. Azure AD-Gruppen und Anwendungsrollen schließen sich nicht gegenseitig aus. Sie können zusammen verwendet werden, um eine noch differenziertere Zugriffssteuerung zu ermöglichen.

## <a name="declare-roles-for-an-application"></a>Deklarieren von Rollen für eine Anwendung

App-Rollen definieren Sie im [Azure-Portal](https://portal.azure.com). App-Rollen werden in der Regel in einer Anwendungsregistrierung definiert, die einen Dienst, eine App oder eine API darstellt. Wenn sich ein Benutzer bei der Anwendung anmeldet, gibt Azure AD für den Benutzer einen `roles`-Anspruch für jede einzeln zugewiesene Rolle oder jeden einzeln zugewiesenen Dienstprinzipal und für die Gruppenmitgliedschaft aus. Dies kann zum Implementieren der anspruchsbasierten Autorisierung verwendet werden. App-Rollen können [einem Benutzer oder einer Gruppe von Benutzern](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app) zugewiesen werden. App-Rollen können auch dem Dienstprinzipal für eine andere Anwendung oder [dem Dienstprinzipal für eine verwaltete Identität](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md) zugewiesen werden.

> [!IMPORTANT]
> Wenn Sie einer Gruppe einen Dienstprinzipal hinzufügen und dieser Gruppe dann eine App-Rolle zuweisen, fügt Azure AD den ausgegebenen Token derzeit nicht den `roles`-Anspruch hinzu.

Es gibt zwei Möglichkeiten, App-Rollen mithilfe des Azure-Portals zu deklarieren:

* [Benutzeroberfläche der App-Rollen](#app-roles-ui--preview) | Vorschau
* [App-Manifest-Editor](#app-manifest-editor)

Die Anzahl der von Ihnen hinzugefügten Rollen wird durch Beschränkungen begrenzt, die Azure Active Directory erzwingt. Informationen zu diesen Grenzwerten finden Sie im Abschnitt [Grenzwerte für das Manifest](./reference-app-manifest.md#manifest-limits) der [Azure Active Directory-App-Manifestreferenz](reference-app-manifest.md).

### <a name="app-roles-ui--preview"></a>Benutzeroberfläche der App-Rollen | Vorschau

> [!IMPORTANT]
> Das Benutzeroberflächenfeature des Portals für App-Rollen[!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

So erstellen Sie eine App-Rolle mithilfe der Benutzeroberfläche des Azure-Portals:

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wählen Sie den Filter **Verzeichnis + Abonnement** im oberen Menü und dann den Azure Active Directory-Mandanten aus, der die App-Registrierung enthält, zu der Sie eine App-Rolle hinzufügen möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** und dann die Anwendung aus, in der Sie App-Rollen definieren möchten.
1. Wählen Sie **App-Rollen | Vorschau** und dann **App-Rolle erstellen** aus.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Bereich „App-Rollen“ einer App-Registrierung im Azure-Portal":::
1. Geben Sie im Bereich **App-Rolle erstellen** die Einstellungen für die Rolle ein. Die Tabelle im Anschluss an das Bild beschreibt die einzelnen Einstellungen und ihre Parameter.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Bereich „App-Rollen, Kontext erstellen“ einer App-Registrierung im Azure-Portal":::

    | Feld | BESCHREIBUNG | Beispiel |
    |-------|-------------|---------|
    | **Anzeigename** | Anzeigename für die App-Rolle, die auf den Administratorbildschirmen für Zustimmung und App-Zuweisung angezeigt wird. Dieser Wert kann Leerzeichen enthalten. | `Survey Writer` |
    | **Zulässige Mitgliedstypen** | Gibt an, ob diese App-Rolle Benutzern, Anwendungen oder beiden zugeordnet werden kann.<br/><br/>Wenn App-Rollen für `applications` verfügbar sind, werden sie als Anwendungsberechtigungen im Abschnitt **Verwalten** einer App-Registrierung unter **API-Berechtigungen > Berechtigung hinzufügen > Meine APIs > API auswählen > Anwendungsberechtigungen** angezeigt. | `Users/Groups` |
    | **Wert** | Gibt den Wert des Rollenanspruchs an, den die Anwendung in den Token erwarten sollte. Der Wert sollte genau mit der Zeichenfolge übereinstimmen, auf die im Code der Anwendung verwiesen wird. Der Wert darf keine Leerzeichen enthalten. | `Survey.Create` |
    | **Beschreibung** | Eine ausführlichere Beschreibung der App-Rolle, die dem Administrator auf den App-Zuweisungs- und -Zustimmungsoberflächen angezeigt wird. | `Writers can create surveys.` |
    | **Möchten Sie diese App-Rolle aktivieren?** | Gibt an, ob die App-Rolle aktiviert ist. Um eine App-Rolle zu löschen, deaktivieren Sie dieses Kontrollkästchen, und wenden Sie die Änderung an, bevor Sie den Löschvorgang starten. | *Überprüft* |

1. Wählen Sie **Übernehmen** aus, um die Änderungen zu speichern.

### <a name="app-manifest-editor"></a>App-Manifest-Editor

So fügen Sie Rollen hinzu, indem Sie das Manifest direkt bearbeiten:

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wählen Sie den Filter **Verzeichnis + Abonnement** im oberen Menü und dann den Azure Active Directory-Mandanten aus, der die App-Registrierung enthält, zu der Sie eine App-Rolle hinzufügen möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** und dann die Anwendung aus, in der Sie App-Rollen definieren möchten.
1. Wählen Sie wieder unter **Verwalten** die Option **Manifest** aus.
1. Bearbeiten Sie das Manifest, indem Sie die `appRoles`-Einstellung suchen und Ihre Anwendungsrollen hinzufügen. Sie können App-Rollen mit Ausrichtung auf `users`, `applications` oder beides festlegen. Die folgenden JSON-Codeausschnitte umfassen Beispiele für beide Möglichkeiten.
1. Speichern Sie das Manifest.

Jede App-Rollendefinition im Manifest muss über eine eindeutige GUID für den `id`-Wert verfügen.

Die `value`-Eigenschaft der einzelnen Anwendungsrollendefinition muss exakt mit den Zeichenfolgen übereinstimmen, die im Code in der Anwendung verwendet werden. Die `value`-Eigenschaft darf keine Leerzeichen enthalten. Ist dies der Fall, erhalten Sie einen Fehler, wenn Sie das Manifest speichern.

#### <a name="example-user-app-role"></a>Beispiel: Benutzer-App-Rolle

In diesem Beispiel wird eine App-Rolle mit dem Namen `Writer` definiert, die Sie einem `User` zuweisen können:

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>Beispiel: Anwendungs-App-Rolle

Wenn App-Rollen für `applications` verfügbar sind, werden sie als Anwendungsberechtigungen im Abschnitt **Verwalten** einer App-Registrierung unter **API-Berechtigungen > Berechtigung hinzufügen > Meine APIs > API auswählen > Anwendungsberechtigungen** angezeigt.

Dieses Beispiel zeigt eine App-Rolle für eine `Application`:

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>Zuweisen von Benutzern und Gruppen zu Rollen

Nachdem Sie App-Rollen in Ihrer Anwendung hinzugefügt haben, können Sie den Rollen Benutzer und Gruppen zuweisen. Die Zuweisung von Benutzern und Gruppen zu Rollen kann über die Benutzeroberfläche des Portals oder programmgesteuert mit [Microsoft Graph](/graph/api/user-post-approleassignments) erfolgen. Wenn sich Benutzer, denen verschiedene App-Rollen zugewiesen sind, bei der Anwendung anmelden, enthalten deren Token die zugewiesenen Rollen im `roles`-Anspruch.

So weisen Sie Benutzern und Gruppen im Azure-Portal Rollen zu:

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wählen Sie in **Azure Active Directory** im linken Navigationsmenü die Option **Unternehmensanwendungen** aus.
1. Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen. Falls die Anwendung nicht in der Liste angezeigt wird, können Sie oben in der Liste **Alle Anwendungen** die Filter verwenden, um den Inhalt der Liste einzugrenzen. Sie können auch in der Liste nach unten scrollen, um nach Ihrer Anwendung zu suchen.
1. Wählen Sie die Anwendung aus, in der Sie Benutzer oder eine Sicherheitsgruppe zu Rollen zuweisen möchten.
1. Klicken Sie unter **Verwalten** auf **Benutzer und Gruppen**.
1. Wählen Sie **Benutzer hinzufügen** aus, um den Bereich **Zuweisung hinzufügen** zu öffnen.
1. Wählen Sie im Bereich **Zuweisung hinzufügen** den Selektor **Benutzer und Gruppen** aus. Eine Liste von Benutzern und Sicherheitsgruppen wird angezeigt. Sie können sowohl nach einem bestimmten Benutzer oder einer bestimmten Gruppe suchen als auch mehrere in der Liste angezeigte Benutzer und Gruppen auswählen.
1. Nachdem Sie Benutzer und Gruppen ausgewählt haben, wählen Sie die Schaltfläche **Auswählen** aus, um fortzufahren.
1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Rolle auswählen** aus. Alle Rollen, die Sie für die Anwendung definiert haben, werden angezeigt.
1. Wählen Sie eine Rolle und dann die Schaltfläche **Auswählen** aus.
1. Wählen Sie die Schaltfläche **Zuweisen** aus, um die Zuweisungen von Benutzern und Gruppen zur App abzuschließen.

Vergewissern Sie sich, dass die hinzugefügten Benutzer und Gruppen in der Liste **Benutzer und Gruppen** angezeigt werden.

## <a name="assign-app-roles-to-applications"></a>Zuweisen von App-Rollen zu Anwendungen

Nachdem Sie App-Rollen in Ihrer Anwendung hinzugefügt haben, können Sie eine App-Rolle einer Client-App im Azure-Portal oder programmgesteuert mit [Microsoft Graph](/graph/api/user-post-approleassignments) zuweisen.

Wenn Sie einer Anwendung App-Rollen zuweisen, erstellen Sie *Anwendungsberechtigungen*. Anwendungsberechtigungen werden in der Regel von Daemon-Apps oder Back-End-Diensten verwendet, die ohne Benutzerinteraktion sich selbst authentifizieren und autorisierte API-Aufrufe ausführen müssen.

So weisen Sie einer Anwendung mithilfe des Azure-Portals App-Rollen zu:

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wählen Sie in **Azure Active Directory** im linken Navigationsmenü die Option **App-Registrierungen** aus.
1. Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen. Falls die Anwendung nicht in der Liste angezeigt wird, können Sie oben in der Liste **Alle Anwendungen** die Filter verwenden, um den Inhalt der Liste einzugrenzen. Sie können auch in der Liste nach unten scrollen, um nach Ihrer Anwendung zu suchen.
1. Wählen Sie die Anwendung aus, der Sie eine App-Rolle hinzufügen möchten.
1. Klicken Sie auf **API-Berechtigungen** > **Berechtigung hinzufügen**.
1. Wählen Sie die Registerkarte **Meine APIs** aus und dann die App, für die Sie App-Rollen definiert haben.
1. Wählen Sie **Anwendungsberechtigungen**.
1. Wählen Sie die Rolle(n) aus, die Sie zuweisen möchten.
1. Wählen Sie die Schaltfläche **Berechtigungen hinzufügen** aus, um das Hinzufügen der Rolle(n) abzuschließen.

Die neu hinzugefügten Rollen sollten im Bereich **API-Berechtigungen** der Registrierung Ihrer App angezeigt werden.

#### <a name="grant-admin-consent"></a>Gewähren der Administratoreinwilligung

Da es sich hierbei um *Anwendungsberechtigungen* handelt, nicht um delegierte Berechtigungen, muss ein Administrator die Zustimmung zur Verwendung der App-Rollen erteilen, die der Anwendung zugewiesen sind.

1. Wählen Sie im Bereich **API-Berechtigungen** der App-Registrierung **Administratorzustimmung für \<tenant name\> erteilen** aus.
1. Wählen Sie **Ja**, wenn Sie aufgefordert werden, die Zustimmung für die angeforderten Berechtigungen zu erteilen.

Die Spalte **Status** sollte **Gewährt für \<tenant name\>** widerspiegeln.

## <a name="use-app-roles-in-your-web-api"></a>Verwenden von App-Rollen in Ihrer Web-API

Nachdem Sie App-Rollen definiert und einem Benutzer, einer Gruppe oder Anwendung zugewiesen haben, fügen Sie Ihrer Web-API im nächsten Schritt Code hinzu, der prüft, ob diese Rollen vorhanden sind, wenn die API aufgerufen wird. Das heißt, wenn eine Client-App einen API-Vorgang anfordert, der nach Ihrer Entscheidung Autorisierung erfordert, muss der Code Ihrer API überprüfen, ob sich die Bereiche im Zugriffstoken befinden, das im Aufruf der Client-App angezeigt wird.

Informationen zum Hinzufügen der Autorisierung zu Ihrer Web-API finden Sie unter [Geschützte Web-API: Überprüfen von Bereichen und App-Rollen](scenario-protected-web-api-verification-scope-app-roles.md).

## <a name="app-roles-vs-groups"></a>App-Rollen im Vergleich zu Gruppen

Obwohl Sie App-Rollen oder Gruppen für die Autorisierung verwenden können, können wichtige Unterschiede zwischen ihnen Ihre Entscheidung beeinflussen, welche für das Szenario verwendet werden sollen.

| App-Rollen                                                                          | Gruppen                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| Sie sind für eine Anwendung spezifisch und werden in der App-Registrierung definiert. Sie werden mit der Anwendung verschoben. | Sie sind nicht spezifisch für eine App, sondern für einen Azure AD-Mandanten. |
| App-Rollen werden entfernt, wenn Ihre App-Registrierung entfernt wird.                      | Gruppen bleiben intakt, auch wenn die App entfernt wird.            |
| Werden im `roles`-Anspruch bereitgestellt.                                                     | Werden im `groups`-Anspruch bereitgestellt.                                 |

Entwickler können mit App-Rollen steuern, ob ein Benutzer sich bei einer App anmelden kann oder eine App ein Zugriffstoken für eine Web-API abrufen kann. Um diese Sicherheitssteuerung auf Gruppen auszuweiten, können Entwickler und Administratoren auch Sicherheitsgruppen zu App-Rollen zuweisen.

App-Rollen werden von Entwicklern bevorzugt, wenn sie die Parameter der Autorisierung in ihrer App selbst beschreiben und steuern möchten. Beispielsweise wird eine App, die Gruppen für die Autorisierung verwendet, im nächsten Mandanten nicht mehr funktionieren, da sowohl Gruppen-ID als auch Name unterschiedlich sein könnte. Eine App, die App-Rollen verwendet, bleibt sicher. Tatsächlich ist das Zuweisen von Gruppen zu App-Rollen mit SaaS-Apps aus denselben Gründen beliebt.

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Ressourcen erfahren Sie mehr über App-Rollen.

* Codebeispiele auf GitHub
  * [Add authorization using groups and group claims to an ASP.NET Core web app that signs-in users with the Microsoft identity platform](https://aka.ms/groupssample) (Hinzufügen der Autorisierung mit Gruppen und Gruppenansprüchen zu einer ASP.NET Core-Web-App, die Benutzer mit der Microsoft Identity Platform anmeldet)
  * [Angular Single-Page Application (SPA) calling a .NET Core Web API and using App Roles and Security Groups to implement Role-Based Access Control (RBAC)](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md) (Aufrufen einer .NET Core-Web-API durch eine Angular-SPA (Single-Page Application) und Verwenden von App-Rollen und Sicherheitsgruppen zum Implementieren rollenbasierter Zugriffssteuerung (Role-Based Access Control, RBAC))
* Referenzdokumentation
  * [Azure AD-App-Manifest](./reference-app-manifest.md)
  * [Azure AD-Zugriffstoken](access-tokens.md)
  * [Azure AD-ID-Token](id-tokens.md)
  * [Bereitstellen optionaler Ansprüche für Ihre App](active-directory-optional-claims.md)
* Video: [Implement authorization in your applications with Microsoft identity platform ](https://www.youtube.com/watch?v=LRoc-na27l0) (Implementieren der Autorisierung in Ihren Anwendungen mit Microsoft Identity Platform) (1:01:15)
