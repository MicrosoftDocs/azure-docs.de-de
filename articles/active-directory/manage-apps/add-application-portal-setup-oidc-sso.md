---
title: 'Schnellstart: Einrichten des OIDC-basierten einmaligen Anmeldens (Single Sign-On, SSO) für eine Anwendung auf Ihrem Azure Active Directory-Mandanten (Azure AD-Mandanten)'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie OIDC-basiertes einmaliges Anmelden (Single Sign-On, SSO) für eine Anwendung auf Ihrem Azure Active Directory-Mandanten (Azure AD-Mandanten) einrichten.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: e5695d5ded92e9bb41d39a082790cfbb456dbc38
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258353"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Schnellstart: Einrichten des OIDC-basierten einmaligen Anmeldens (Single Sign-On, SSO) für eine Anwendung auf Ihrem Azure Active Directory-Mandanten (Azure AD-Mandanten)

Beginnen Sie mit vereinfachten Benutzeranmeldungen, indem Sie einmaliges Anmelden (Single Sign-On, SSO) für eine Anwendung einrichten, die Sie Ihrem Azure Active Directory-Mandanten (Azure AD-Mandanten) hinzugefügt haben. Nachdem Sie SSO eingerichtet haben, können sich Ihre Benutzer mit ihren Azure AD-Anmeldeinformationen bei einer Anwendung anmelden. SSO ist in der kostenlosen Edition von Azure AD enthalten.

Weitere Informationen zu OIDC-basiertem SSO finden Sie unter [Grundlegendes zum OIDC-basierten einmaligen Anmelden](configure-oidc-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um SSO für eine Anwendung einzurichten, die Sie Ihrem Azure AD-Mandanten hinzugefügt haben:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Eine Anwendung, die SSO unterstützt und bereits vorkonfiguriert und dem Azure AD-Katalog hinzugefügt wurde. Bei den meisten Apps kann Azure AD für SSO verwendet werden. Die Apps im Azure AD-Katalog sind vorkonfiguriert. Falls Ihre App nicht aufgelistet oder eine benutzerdefinierte App ist, können Sie sie trotzdem mit Azure AD verwenden. Sehen Sie sich die Tutorials und die restliche Dokumentation im Inhaltsverzeichnis an. In dieser Schnellstartanleitung liegt der Schwerpunkt auf Apps, die von den App-Entwicklern für SSO vorkonfiguriert und dem Azure AD-Katalog hinzugefügt wurden.
- Optional: Gehen Sie den Schnellstart [Anzeigen Ihrer Apps](view-applications-portal.md) durch.
- Optional: Gehen Sie den Schnellstart [Hinzufügen einer App](add-application-portal.md) durch.
- Optional: Gehen Sie den Schnellstart [Konfigurieren einer App](add-application-portal-configure.md) durch.
- Optional: Arbeiten Sie den Artikel [Zuweisen von Benutzern zu einer App](add-application-portal-assign-users.md) durch.

>[!IMPORTANT]
>Verwenden Sie zum Testen der in dieser Schnellstartanleitung aufgeführten Schritte keine Produktionsumgebung.

## <a name="enable-single-sign-on-for-an-app"></a>Aktivieren des einmaligen Anmeldens für eine App

Beim Hinzufügen einer App, bei der der OIDC-Standard für einmaliges Anmelden (SSO) genutzt wird, verfügen Sie über eine Setupschaltfläche. Wenn Sie die Schaltfläche auswählen, gelangen Sie zur Anwendungswebsite und können den Registrierungsvorgang für die App durchführen. Der Vorgang zum Hinzufügen einer App wurde in der Schnellstartanleitung „Hinzufügen einer App“ dieser Reihe bereits beschrieben. Wenn Sie eine Anwendung konfigurieren, die bereits hinzugefügt wurde, lesen Sie die erste Schnellstartanleitung. Darin wird Schritt für Schritt beschrieben, wie Sie die Anwendungen anzeigen können, die sich bereits auf Ihrem Mandanten befinden. 

Richten Sie einmaliges Anmelden wie folgt für eine Anwendung ein:

1. In einer der vorherigen Schnellstartanleitungen dieser Reihe wurde beschrieben, wie Sie eine App hinzufügen, bei der Ihr Azure AD-Mandant für die Identitätsverwaltung verwendet wird. Wenn der App-Entwickler den OIDC-Standard zum Implementieren des einmaligen Anmeldens verwendet hat, ist beim Hinzufügen der App eine Schaltfläche für die Registrierung vorhanden. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Screenshot: Option für einmaliges Anmelden und Schaltfläche für die Registrierung" lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Wenn Sie **Registrieren** auswählen, wird die Anmeldeseite für App-Entwickler angezeigt. Melden Sie sich mit Ihren Azure Active Directory-Anmeldeinformationen an. 

   > [!IMPORTANT]
    > Falls Sie bereits über ein Abonnement für die Anwendung verfügen, wird eine Überprüfung der Benutzerdetails und Mandanten- bzw. Verzeichnisinformationen durchgeführt. Falls der Benutzer von der Anwendung nicht verifiziert werden kann, werden Sie auf die Registrierungsseite für den Anwendungsdienst oder die Fehlerseite umgeleitet.

3. Nach erfolgreichem Abschluss der Authentifizierung wird in einem Dialogfeld nach der Administratoreinwilligung gefragt. Wählen Sie die Option **Zustimmung im Namen Ihrer Organisation** und dann **Annehmen** aus. Weitere Informationen zur Benutzer- und Administratoreinwilligung finden Sie unter [Grundlegendes zur Benutzer- und Administratoreinwilligung](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Screenshot: Einwilligungsbildschirm für eine App" lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. Die Anwendung wird Ihrem Mandanten hinzugefügt, und die Startseite der Anwendung wird angezeigt.


> [!TIP]
> Sie können die App-Verwaltung mithilfe der Graph-API automatisieren. Informationen hierzu finden Sie unter [Automatisieren einer SAML-basierten SSO-App-Konfiguration mit Microsoft Graph-API](/graph/application-saml-sso-configure-api).

Im folgenden Video werden Schritt für Schritt weitere Details im Zusammenhang mit dem Hinzufügen einer OIDC-basierten App zu Azure AD erläutert.

Hinzufügen einer OIDC-basierten App in Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Schnellstartserie abgeschlossen haben, sollten Sie die App löschen, um Ihren Testmandanten zu bereinigen. Das Löschen der App wird in der letzten Schnellstartanleitung dieser Reihe behandelt. Informationen finden Sie unter [Schnellstart: Löschen einer Anwendung von Ihrem Azure AD-Mandanten (Azure Active Directory)](delete-application-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie eine App löschen:
> [!div class="nextstepaction"]
> [Löschen einer App](delete-application-portal.md)
