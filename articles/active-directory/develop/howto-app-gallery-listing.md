---
title: Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie eine Anwendung, die einmaliges Anmelden unterstützt, im Azure Active Directory-App-Katalog listen.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 005e5c92a16760d8eec5dc37526f4b1f2dbd751c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540548"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>Gewusst wie: Auflisten Ihrer Anwendung zum Azure Active Directory-Anwendungskatalog

In diesem Artikel wird erläutert, wie eine Anwendung im Azure AD-Anwendungskatalog aufgelistet wird, das einmalige Anmelden (Single Sign-On, SSO) implementiert und der Listeneintrag verwaltet wird.

## <a name="what-is-the-azure-ad-application-gallery"></a>Was ist der Azure AD-Anwendungskatalog?

- Kunden finden so die bestmögliche Umgebung für einmaliges Anmelden.
- Die Konfiguration der Anwendung ist einfach und in wenigen Schritten erledigt.
- Eine schnelle Suche findet Ihre Anwendung im Katalog.
- Azure AD-Kunden mit den Tarifen Free, Basic und Premium können diese Integration nutzen.
- Gemeinsame Kunden erhalten ausführliche Tutorials zur Konfiguration.
- Kunden, die SCIM verwenden, können für die gleiche App die Bereitstellung nutzen.

## <a name="prerequisites"></a>Voraussetzungen

- Bei Verbundanwendungen (OpenID und SAML/WS-Fed) muss die Anwendung das SaaS-Modell für die Aufnahme in den Azure AD-Katalog unterstützen. Die Unternehmenskataloganwendungen sollten mehrere Kundenkonfigurationen und nicht einen bestimmten Kunden unterstützen.

- Für OpenID Connect muss die Anwendung mehrinstanzenfähig sein, und das [Azure AD-Zustimmungsframework](consent-framework.md) muss für die Anwendung ordnungsgemäß implementiert werden. Der Benutzer kann die Anmeldeanforderung an einen gemeinsamen Endpunkt senden, damit jeder Kunde der Anwendung zustimmen kann. Sie können den Benutzerzugriff anhand der Mandanten-ID und des im Token erhaltenen UPN des Benutzers steuern.

- Für SAML 2.0/WS-Fed muss Ihre Anwendung über eine Funktion zur SAML-/WS-Fed-SSO-Integration im SP- oder IDP-Modus verfügen. Stellen Sie die ordnungsgemäße Funktionsweise sicher, bevor Sie die Anforderung übermitteln.

- Stellen Sie beim Kennwort-SSO sicher, dass Ihre Anwendung die Formularauthentifizierung unterstützt, sodass Kennworttresore verwendet werden können, damit einmaliges Anmelden wie erwartet funktioniert.

- Für Anforderungen zur automatischen Benutzerbereitstellung sollte die Anwendung im Katalog aufgeführt sein, wobei die Funktion für einmaliges Anmelden mit SAML 2.0/WS-Fed aktiviert ist. Sie können SSO und die Benutzerbereitstellung zusammen im Portal anfordern, sofern dies nicht bereits aufgeführt ist.

>[!NOTE]
>Die Ausführung erfolgt mit einer hohen Anzahl von SCIM-Connektoranforderungen, sodass wir die Annahme neuer Anforderungen in unserem Portal eingestellt haben. Bitte warten Sie bis auf weiteres mit Ihren Anforderungen. Wir entschuldigen uns für diese Verzögerung und etwaige Unannehmlichkeiten.

## <a name="submit-the-request-in-the-portal"></a>Übermitteln der Anforderung im Portal

Nachdem Sie getestet haben, dass Ihre Anwendungsintegration mit Azure AD funktioniert, übermitteln Sie Ihre Anforderung für den Zugriff auf unser [Anwendungsnetzwerkportal](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Wenn Sie über ein Office 365-Konto verfügen, verwenden Sie dieses für die Anmeldung bei diesem Portal. Verwenden Sie andernfalls Ihr Microsoft-Konto (z.B. Outlook oder Hotmail) für die Anmeldung.

Wenn die folgende Seite nach der Anmeldung angezeigt wird, wenden Sie sich an das [Azure AD SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>), und geben Sie das E-Mail-Konto an, das für die Übermittlung der Anforderung verwendet werden soll. Das Azure AD-Team wird das Konto dann im Microsoft-Anwendungsnetzwerkportal hinzufügen.

![Zugriffsanforderung im SharePoint-Portal](./media/howto-app-gallery-listing/errorimage.png)

Sobald das Konto hinzugefügt wurde, können Sie sich am Microsoft-Anwendungsnetzwerkportal anmelden.

Wenn nach dem Anmelden die folgende Seite angezeigt wird, geben Sie eine geschäftliche Begründung für den Zugriff in das Textfeld ein, und wählen Sie dann **Zugriff anfordern** aus.

  ![Zugriffsanforderung im SharePoint-Portal](./media/howto-app-gallery-listing/accessrequest.png)

Unser Team überprüft die Details und gewährt Ihnen entsprechend Zugriff. Nachdem Ihre Anforderung genehmigt wurde, können Sie sich am Portal anmelden und die Anforderung senden, indem Sie auf die Kachel **Anforderung senden (ISV)** auf der Startseite klicken.

![Startseite des SharePoint-Portals](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Wenn Sie Probleme mit dem Zugriff haben, wenden Sie sich an das [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-federation-protocol"></a>Implementieren von SSO mithilfe eines Verbundprotokolls

Damit eine Anwendung in den Azure AD-App-Katalog aufgenommen werden kann, müssen Sie zunächst eines der folgenden von Azure AD unterstützten Verbundprotokolle implementieren und den Geschäftsbedingungen für den Azure AD-Anwendungskatalog zustimmen. Die Geschäftsbedingungen für den Azure AD-Anwendungskatalog finden Sie [hier](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: Wenn Sie Ihre Anwendung mithilfe des OpenID Connect-Protokolls in Azure AD integrieren möchten, befolgen Sie die [Anweisungen für Entwickler](authentication-scenarios.md).

    ![Zeitplan für das Listing einer OpenID Connect-Anwendung im Katalog](./media/howto-app-gallery-listing/openid.png)

    * Wenn Sie Ihre Anwendung mit OpenID Connect hinzufügen möchten, um sie im Katalog listen zu lassen, wählen Sie wie oben gezeigt **OpenID Connect und OAuth 2.0** aus.
    * Wenn Sie Probleme mit dem Zugriff haben, wenden Sie sich an das [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

- **SAML 2.0** oder **WS-Fed**: Jede App, die SAML 2.0 unterstützt, kann direkt in einen Azure AD-Mandanten integriert werden. Befolgen Sie dazu die [Anweisungen zum Hinzufügen einer benutzerdefinierten Anwendung](../active-directory-saas-custom-apps.md).

  ![Zeitplan für das Listing einer SAML 2.0- oder WS-Fed-Anwendung im Katalog](./media/howto-app-gallery-listing/saml.png)

  * Wenn Sie Ihre Anwendung mit **SAML 2.0** oder **WS-Fed** hinzufügen möchten, um sie im Katalog listen zu lassen, wählen Sie wie oben gezeigt **SAML 2.0/WS-Fed** aus.
  * Wenn Sie Probleme mit dem Zugriff haben, wenden Sie sich an das [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Implementieren von SSO mithilfe von Kennwort-SSO

Erstellen Sie eine Webanwendung mit einer HTML-Anmeldeseite, um das [kennwortbasierte einmalige Anmelden](../manage-apps/what-is-single-sign-on.md) zu konfigurieren. Das kennwortbasierte einmalige Anmelden (auch als „Password Vaulting“ oder „Kennworttresor“ bezeichnet) ermöglicht es Ihnen, den Benutzerzugriff und die Kennwörter für Webanwendungen zu verwalten, die keinen Identitätsverbund unterstützen. Es ist auch für Szenarien nützlich, in denen mehrere Benutzer ein Konto gemeinsam verwenden müssen, wie z.B. bei den App-Konten für die sozialen Medien Ihrer Organisation.

![Zeitplan für das Listing einer Kennwort-SSO-Anwendung im Katalog](./media/howto-app-gallery-listing/passwordsso.png)

* Wenn Sie Ihre Anwendung mit Kennwort-SSO hinzufügen möchten, um sie im Katalog listen zu lassen, wählen Sie wie oben gezeigt **Kennwort-SSO** aus.
* Wenn Sie Probleme mit dem Zugriff haben, wenden Sie sich an das [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="updateremove-existing-listing"></a>Aktualisieren bzw. Entfernen vorhandener Auflistungen

Um eine bestehende Anwendung im Azure AD-App-Katalog zu aktualisieren oder zu entfernen, müssen Sie zuerst die Anfrage im [Anwendungsnetzwerkportal](https://microsoft.sharepoint.com/teams/apponboarding/Apps) stellen. Wenn Sie über ein Office 365-Konto verfügen, verwenden Sie dieses für die Anmeldung bei diesem Portal. Verwenden Sie andernfalls Ihr Microsoft-Konto (z.B. Outlook oder Hotmail) für die Anmeldung.

- Wählen Sie die entsprechende Option wie in der folgenden Abbildung gezeigt aus:

    ![Zeitachse für Auflistung einer SAML-Anwendung in der Galerie](./media/howto-app-gallery-listing/updateorremove.png)

    * Wenn Sie eine vorhandene Anwendung aktualisieren möchten, wählen Sie **Vorhandene Anwendungsauflistung aktualisieren**.
    * Wenn Sie eine vorhandene Anwendung aus dem Azure AD-Katalog entfernen möchten, wählen Sie **Vorhandene Anwendungsauflistung entfernen** aus.
    * Wenn Sie Probleme mit dem Zugriff haben, wenden Sie sich an das [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="listing-requests-by-customers"></a>Auflisten von Anforderungen nach Kunden

Kunden können die Anforderung zum Auflisten einer Anwendung übermitteln, indem sie auf **App-Anforderungen nach Kunden** -> **Neue Anforderung übermitteln** klicken.

![Kachel „Kunden haben Apps angefordert“](./media/howto-app-gallery-listing/customer-submit-request.png)

Unten sehen Sie den Fluss für von Kunden angeforderten Anwendungen:

![Fluss für von Kunden angeforderten Anwendungen](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Zeitpläne

Das Auflisten einer SAML 2.0- oder WS-Fed-Anwendung im Katalog dauert etwa 7 bis 10 Werktage.

   ![Zeitachse für Auflistung einer SAML-Anwendung in der Galerie](./media/howto-app-gallery-listing/timeline.png)

Das Auflisten einer OpenID Connect-Anwendung im Katalog dauert etwa 2 bis 5 Werktage.

   ![Zeitachse für Auflistung einer SAML-Anwendung in der Galerie](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Eskalationen

Für Eskalationen senden Sie eine E-Mail an das [Azure AD-SSO-Integrationsteam](mailto:SaaSApplicationIntegrations@service.microsoft.com) an SaaSApplicationIntegrations@service.microsoft.com. Wir melden uns so schnell wie möglich bei Ihnen.
