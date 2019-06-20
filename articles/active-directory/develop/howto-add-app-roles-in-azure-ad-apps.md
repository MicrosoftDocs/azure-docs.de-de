---
title: Hinzufügen von App-Rollen in Ihrer bei Azure Active Directory registrierten Anwendung und Empfangen der Rollen im Token
description: Hier erfahren Sie, wie Sie App-Rollen in einer bei Azure Active Directory registrierten Anwendung hinzufügen, diesen Rollen Benutzer und Gruppen zuweisen und die Rollen im `roles`-Anspruch im Token empfangen.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24e933399454942f4ee50440cffd791599679074
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299146"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Gewusst wie: Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) ist eine beliebte Methode zum Erzwingen der Autorisierung in Anwendungen. Bei der Verwendung der RBAC gewährt ein Administrator Berechtigungen für Rollen und nicht für einzelne Benutzer oder Gruppen. Der Administrator kann anschließend verschiedenen Benutzern und Gruppen Rollen zuweisen, um zu steuern, wer auf welche Inhalte und Funktionen zugreifen kann.

Entwickler können die RBAC mit Anwendungsrollen und Rollenansprüche verwenden und dadurch mit wenig Aufwand sicher die Autorisierung in ihren Apps erzwingen.

Ein weiterer Ansatz ist die Verwendung von Azure AD-Gruppen und Gruppenansprüchen wie unter [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) gezeigt. Azure AD-Gruppen und Anwendungsrollen schließen sich keinesfalls gegenseitig aus. Sie können zusammen verwendet werden, um eine noch differenziertere Zugriffssteuerung zu ermöglichen.

## <a name="declare-roles-for-an-application"></a>Deklarieren von Rollen für eine Anwendung

Diese Anwendungsrollen werden im [Azure-Portal](https://portal.azure.com) im Registrierungsmanifest der Anwendung definiert.  Wenn sich ein Benutzer bei der Anwendung anmeldet, gibt Azure AD für den Benutzer einen `roles`-Anspruch für jede einzeln zugewiesene Rolle und für die Gruppenmitgliedschaft aus.  Die Zuweisung von Benutzern und Gruppen zu Rollen kann über die Benutzeroberfläche des Portals oder programmgesteuert mit [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview) erfolgen.

### <a name="declare-app-roles-using-azure-portal"></a>Deklarieren von App-Rollen mithilfe des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der oberen Leiste Ihr Konto und dann **Verzeichnis wechseln** aus.
1. Wählen Sie im geöffneten Bereich **Verzeichnis + Abonnement** in der Liste **Favoriten** oder **Alle Verzeichnisse** den Active Directory-Mandanten aus, bei dem Sie Ihre Anwendung registrieren möchten.
1. Wählen Sie im linken Navigationsbereich **Alle Dienste** und dann **Azure Active Directory** aus.
1. Wählen Sie im Bereich **Azure Active Directory** die Option **App-Registrierungen** aus, um eine Liste aller Anwendungen anzuzeigen.

     Falls die gewünschte Anwendung hier nicht angezeigt wird, können Sie oben in der Liste **App-Registrierungen** die verschiedenen Filter verwenden, um die Liste einzuschränken. Sie können auch in der Liste nach unten scrollen, um nach Ihrer Anwendung zu suchen.

1. Wählen Sie die Anwendung aus, in der Sie App-Rollen definieren möchten.
1. Wählen Sie auf dem Blatt für Ihre Anwendung die Option **Manifest** aus.
1. Bearbeiten Sie das Manifest, indem Sie die `appRoles`-Einstellung suchen und alle Anwendungsrollen hinzufügen.

     > [!NOTE]
     > Für jede Anwendungsrollendefinition in diesem Manifest muss eine andere gültige GUID für die `id`-Eigenschaft angegeben sein. 
     > 
     > Die `value`-Eigenschaft der einzelnen Anwendungsrollendefinition muss exakt mit den Zeichenfolgen übereinstimmen, die im Code in der Anwendung verwendet werden. Die `value`-Eigenschaft darf keine Leerzeichen enthalten. Ist dies der Fall, erhalten Sie einen Fehler, wenn Sie das Manifest speichern.
     
1. Speichern Sie das Manifest.

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt den `appRoles`-Wert, den Sie `users` zuweisen können.

> [!NOTE]
>`id` muss eine eindeutige GUID sein.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>`displayName` darf keine Leerzeichen enthalten.

Sie können App-Rollen mit Ausrichtung auf `users`, `applications` oder auf beides festlegen. Wenn App-Rollen für `applications` verfügbar sind, werden sie auf dem Blatt **Erforderliche Berechtigungen** als Anwendungsberechtigungen angezeigt. Das folgende Beispiel zeigt eine App-Rolle für `Application`.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

Die Anzahl der definierten Rollen wirkt sich auf die Grenzwerte des Anwendungsmanifests aus. Diese wurden ausführlich auf der Seite [Grenzwerte für das Manifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) erläutert.

### <a name="assign-users-and-groups-to-roles"></a>Zuweisen von Benutzern und Gruppen zu Rollen

Nachdem Sie App-Rollen in Ihrer Anwendung hinzugefügt haben, können Sie diesen Rollen Benutzer und Gruppen zuweisen.

1. Wählen Sie im Bereich **Azure Active Directory** im Navigationsmenü **Azure Active Directory** auf der linken Seite die Option **Unternehmensanwendungen**.
1. Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.

     Falls die gewünschte Anwendung hier nicht angezeigt wird, können Sie oben in der Liste **Alle Anwendungen** die verschiedenen Filter verwenden, um die Liste einzuschränken. Sie können auch in der Liste nach unten scrollen, um nach Ihrer Anwendung zu suchen.

1. Wählen Sie die Anwendung aus, in der Sie Benutzer oder eine Sicherheitsgruppe zu Rollen zuweisen möchten.
1. Wählen Sie in der Anwendung im Navigationsmenü auf der linken Seite den Bereich **Benutzer und Gruppen**.
1. Wählen Sie oben in der Liste **Benutzer und Gruppen** die Schaltfläche **Benutzer hinzufügen** aus, um den Bereich **Zuweisung hinzufügen** zu öffnen.
1. Wählen Sie im Bereich **Zuweisung hinzufügen** den Selektor **Benutzer und Gruppen** aus.

     Eine Liste mit Benutzern und Sicherheitsgruppen wird mit einem Textfeld angezeigt, mit dem bestimmte Benutzer oder Gruppen gesucht werden können. Auf diesem Bildschirm können Sie mehrere Benutzer und Gruppen auf einmal auswählen.

1. Nachdem Sie mit dem Auswählen der Benutzer und Gruppen fertig sind, können Sie unten die Schaltfläche **Auswählen** verwenden, um mit dem nächsten Teil fortzufahren.
1. Wählen Sie im Bereich **Zuweisung hinzufügen** den Selektor **Benutzer** aus. Alle weiter oben im App-Manifest deklarierten Rollen werden angezeigt.
1. Wählen Sie eine Rolle und dann die Schaltfläche **Auswählen** aus.
1. Wählen Sie unten die Schaltfläche **Zuweisen** aus, um die Zuweisungen von Benutzern und Gruppen zur App abzuschließen.
1. Vergewissern Sie sich, dass die hinzugefügten Benutzer und Gruppen in der aktualisierten Liste **Benutzer und Gruppen** angezeigt werden.

## <a name="more-information"></a>Weitere Informationen

- [Authorization in a web app using Azure AD application roles &amp; role claims (Sample)](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapp-roleclaims/) (Autorisierung in einer Web-App mithilfe von Azure AD-Anwendungsrollen und -Rollenansprüchen (Beispiel))
- [Using Security Groups and Application Roles in your apps (Video)](https://www.youtube.com/watch?v=V8VUPixLSiM) (Verwenden von Sicherheitsgruppen und Anwendungsrollen in Ihren Apps (Video))
- [Azure Active Directory, now with Group Claims and Application Roles](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles) (Azure Active Directory jetzt mit Gruppenansprüchen und Anwendungsrollen)
- [Azure Active Directory-App-Manifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Azure Active Directory access tokens](access-tokens.md) (Azure Active Directory-Zugriffstoken)
- [`id_tokens`](id-tokens.md) (ID-Token)
