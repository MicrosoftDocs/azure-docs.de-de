---
title: Einschränken von Azure AD-Apps auf eine Gruppe von Benutzern | Azure
titleSuffix: Microsoft identity platform
description: Es wird beschrieben, wie Sie den Zugriff auf Ihre Apps, die für Azure AD registriert sind, auf eine Gruppe von ausgewählten Benutzern beschränken.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f3eff6f5660089472ef431bad9a4f05be1fffe24
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104144"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Gewusst wie: Einschränken Ihrer Azure AD-App auf eine Gruppe von Benutzern in einem Azure AD-Mandanten

Anwendungen, die für einen Azure AD-Mandanten (Azure Active Directory) registriert sind, sind standardmäßig für alle Benutzer des Mandanten verfügbar, deren Authentifizierung erfolgreich war.

Ähnlich ist es für eine [mehrinstanzenfähige](howto-convert-app-to-be-multi-tenant.md) App: Alle Benutzer des Azure AD-Mandanten, für den die App bereitgestellt wurde, können auf diese Anwendung zugreifen, nachdem sie sich erfolgreich am jeweiligen Mandanten angemeldet haben.

Für Mandantenadministratoren und Entwickler ist es häufig erforderlich, dass eine App auf eine bestimmte Gruppe von Benutzern beschränkt werden muss. Entwickler können dies erreichen, indem sie beliebte Autorisierungsmuster nutzen, z. B. die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC). Dieser Ansatz ist für Entwickler aber mit einem erheblichen Arbeitsaufwand verbunden.

Mandantenadministratoren und Entwickler können auch durch Verwendung dieser integrierten Funktion von Azure AD eine App auf eine bestimmte Gruppe von Benutzern oder Sicherheitsgruppen im Mandanten beschränken.

## <a name="supported-app-configurations"></a>Unterstützte App-Konfigurationen

Die Option zum Beschränken einer App auf eine bestimmte Gruppe von Benutzern oder Sicherheitsgruppen in einem Mandanten funktioniert für die folgenden Arten von Anwendungen:

- Anwendungen, die für die einmalige Verbundanmeldung mit SAML-basierter Authentifizierung konfiguriert sind.
- Anwendungsproxyanwendungen, die Azure AD-Vorauthentifizierung verwenden.
- Anwendungen, die direkt auf der Azure AD-Anwendungsplattform mit Verwendung der OAuth 2.0/OpenID Connect-Authentifizierung erstellt werden (nachdem ein Benutzer oder Administrator seine Zustimmung für die Anwendung erteilt hat).

     > [!NOTE]
     > Dieses Feature ist nur für Web-App/Web-API und Unternehmensanwendungen verfügbar. Apps, die als [nativ](./quickstart-register-app.md) registriert wurden, können im Mandanten nicht auf eine Gruppe von Benutzern oder Sicherheitsgruppen beschränkt werden.

## <a name="update-the-app-to-enable-user-assignment"></a>Aktualisieren der App zum Aktivieren der Benutzerzuweisung

Es gibt zwei Möglichkeiten zum Erstellen einer Anwendung mit aktivierter Benutzerzuweisung. Die erste Möglichkeit erfordert die Rolle **Globaler Administrator**, die zweite nicht.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Unternehmensanwendungen (erfordert die Rolle „Globaler Administrator“)

1. Melden Sie sich als **globaler Administrator** beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** die Optionen **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste die Anwendung aus, der Sie einen Benutzer oder eine Sicherheitsgruppe zuweisen möchten. 
    Verwenden Sie oben im Fenster die Filter, um nach einer bestimmten Anwendung zu suchen.
1. Wählen Sie auf der Seite **Übersicht** der Anwendung unter **Verwalten** die Option **Eigenschaften** aus.
1. Suchen Sie nach der Einstellung **Benutzerzuweisung erforderlich?** , und legen Sie sie auf **Ja** fest. Wenn diese Option auf **Ja** festgelegt ist, müssen Benutzer im Mandanten zuerst dieser Anwendung zugewiesen werden. Andernfalls können sie sich nicht bei dieser Anwendung anmelden.
1. Wählen Sie **Speichern** aus.

### <a name="app-registration"></a>App-Registrierung

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** aus.
1. Erstellen Sie die App, die Sie verwalten möchten, oder wählen Sie die App aus. Sie müssen der **Besitzer** dieser Anwendung sein.
1. Wählen Sie auf der Seite **Übersicht** der Anwendung im Abschnitt **Essentials** den Link **Verwaltete Anwendung in lokalem Verzeichnis** aus.
1. Wählen Sie unter **Verwalten** die Option **Eigenschaften** aus.
1. Suchen Sie nach der Einstellung **Benutzerzuweisung erforderlich?** , und legen Sie sie auf **Ja** fest. Wenn diese Option auf **Ja** festgelegt ist, müssen Benutzer im Mandanten zuerst dieser Anwendung zugewiesen werden. Andernfalls können sie sich nicht bei dieser Anwendung anmelden.
1. Wählen Sie **Speichern** aus.

## <a name="assign-users-and-groups-to-the-app"></a>Zuweisen von Benutzern und Gruppen zur App

Nachdem Sie Ihre App für die Aktivierung der Benutzerzuweisung konfiguriert haben, können Sie damit beginnen, der App Benutzer und Gruppen zuzuweisen.

1. Wählen Sie unter **Verwalten** die Optionen **Benutzer und Gruppen** > **Benutzer/Gruppe hinzufügen** aus.
1. Wählen Sie die Auswahl **Benutzer** aus. 

     Eine Liste mit Benutzern und Sicherheitsgruppen wird mit einem Textfeld angezeigt, mit dem bestimmte Benutzer oder Gruppen gesucht werden können. Auf diesem Bildschirm können Sie mehrere Benutzer und Gruppen auf einmal auswählen.

1. Wählen Sie nach dem Auswählen der Benutzer und Gruppen die Option **Auswählen** aus.
1. (Optional) Wenn Sie App-Rollen in Ihrer Anwendung definiert haben, können mit der Option **Rolle auswählen** die ausgewählten Benutzer und Gruppen einer der Anwendungsrollen zuweisen. 
1. Wählen Sie **Zuweisen** aus, um die Zuweisungen von Benutzern und Gruppen für die App abzuschließen. 
1. Vergewissern Sie sich, dass die hinzugefügten Benutzer und Gruppen in der aktualisierten Liste **Benutzer und Gruppen** angezeigt werden.

## <a name="more-information"></a>Weitere Informationen

- [Vorgehensweise: Hinzufügen von App-Rollen in Ihrer Anwendung](./howto-add-app-roles-in-azure-ad-apps.md)
- [Hinzufügen von Autorisierung zu einer ASP.NET Core-Web-App mit App-Rollen und Rollenansprüchen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Using Security Groups and Application Roles in your apps (Video)](https://www.youtube.com/watch?v=V8VUPixLSiM) (Verwenden von Sicherheitsgruppen und Anwendungsrollen in Ihren Apps (Video))
- [Azure Active Directory, now with Group Claims and Application Roles](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862) (Azure Active Directory jetzt mit Gruppenansprüchen und Anwendungsrollen)
- [Azure Active Directory-App-Manifest](./reference-app-manifest.md)
