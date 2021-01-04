---
title: Konfigurieren einer OpenID/OAuth-Anwendung aus dem Azure AD-App-Katalog | Microsoft-Dokumentation
description: Schritte zum Konfigurieren einer OpenID/OAuth-Anwendung aus dem Azure AD-App-Katalog
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.custom: has-adal-ref
ms.openlocfilehash: 17d78b4d6db9e65868995d8ea7835a0d911e8589
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348692"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Konfigurieren einer OpenID/OAuth-Anwendung aus dem Azure AD-App-Katalog

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Hinzufügen einer OpenID-Anwendung aus dem Katalog

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie oben im Dialogfeld **Neue Anwendung** aus.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld den Namen der Anwendung ein. Wählen Sie im Ergebnisbereich die gewünschte Anwendung aus, und melden Sie sich bei der Anwendung an.

    ![OpenID in der Ergebnisliste](common/search-new-app.png)


1. Klicken Sie auf der Seite „Anwendungsname“ auf die Schaltfläche **Registrieren**.

    ![Schaltfläche „Hinzufügen“](./media/openidoauth-tutorial/addbutton.png)

    > [!NOTE]
    > Hier muss der Mandantenadministrator die Schaltfläche zum Registrieren auswählen und die Anwendung zulassen. Die Anwendung wird dann dem Kundenmandanten hinzugefügt. Dort können Sie die Konfigurationen vornehmen. Die Anwendung muss nicht explizit hinzugefügt werden.

5. Sie werden zum Eingeben der Anmeldeinformationen auf die Seite für die Anwendungsanmeldung oder die Seite von Azure Active Directory (Azure AD) weitergeleitet.

6. Geben Sie nach der erfolgreichen Authentifizierung auf der Zustimmungsseite Ihre Einwilligung. Daraufhin wird die Startseite der Anwendung angezeigt.

    > [!NOTE]
    > Sie können nur eine Instanz der Anwendung hinzufügen. Wenn Sie bereits eine Instanz hinzugefügt haben und danach noch einmal versuchen, Ihre Einwilligung zu erteilen, wird die Anwendung dem Mandanten nicht erneut hinzugefügt. Das heißt, Sie können im Mandanten nur eine App-Instanz verwenden.

1. Führen Sie die Schritte im folgenden Video aus, um eine OpenID-Anwendung aus dem Katalog hinzuzufügen.
    >[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="authentication-flow-using-openid-connect"></a>Authentifizierungsfluss bei OpenID Connect

Der grundlegende Anmeldevorgang umfasst die folgenden Schritte:

![Authentifizierungsfluss bei OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Mehrinstanzenfähige Anwendungen
Eine mehrinstanzenfähige Anwendung ist für die Verwendung in mehreren Organisationen vorgesehen. Hierbei handelt es sich in der Regel um SaaS-Anwendungen (Software as a Service), die von einem unabhängigen Softwarehersteller (Independent Software Vendor, ISV) geschrieben wurden.

Mehrinstanzenfähige Anwendungen müssen in jedem Verzeichnis bereitgestellt werden, in dem sie verwendet werden. Für die Registrierung benötigen sie die Einwilligung des Benutzers oder Administrators. Dieser Zustimmungsprozess beginnt, wenn eine Anwendung im Verzeichnis registriert wurde und Zugriff auf die Graph-API (oder ggf. eine andere Web-API) erhält. Wenn sich ein Benutzer oder Administrator aus einer anderen Organisation für die Verwendung der Anwendung registriert, erscheint ein Dialogfeld mit den für die Anwendung erforderlichen Berechtigungen.

Der Benutzer bzw. Administrator kann dann seine Einwilligung für die Anwendung erteilen. Das bedeutet konkret, dass er der Anwendung Zugriff auf die angegebenen Daten gewährt und die Anwendung schließlich im Verzeichnis registriert.

> [!NOTE]
> Wenn Sie Ihre Anwendung für Benutzer in mehreren Verzeichnissen bereitstellen, müssen Sie ermitteln, unter welchem Mandanten sich diese befinden. Eine Anwendung mit einem einzelnen Mandanten muss nur in ihrem eigenen Verzeichnis nach einem Benutzer suchen. Eine mehrinstanzenfähige Anwendung muss einen bestimmten Benutzer in allen Verzeichnissen in Azure AD identifizieren.
>
> Für diese Aufgabe bietet Azure AD anstelle eines mandantenspezifischen Endpunkts einen allgemeinen Authentifizierungsendpunkt, an den jede mehrinstanzenfähige Anwendung Anmeldeanforderungen richten kann. Dieser Endpunkt lautet `https://login.microsoftonline.com/common` für alle Verzeichnisse in Azure AD. Ein mandantenspezifischer Endpunkt wäre beispielsweise `https://login.microsoftonline.com/contoso.onmicrosoft.com`.
>
> Der allgemeine Endpunkt ist wichtig für die Entwicklung Ihrer Anwendung. Sie benötigen die notwendige Logik, um mehrere Mandanten bei der Anmelde-, Abmelde- und Tokenüberprüfung zu verwalten.

Standardmäßig unterstützt Azure AD mehrinstanzenfähige Anwendungen. Sie sind organisationsübergreifend leicht zugänglich und einfach zu bedienen, nachdem die Einwilligung erteilt wurde.

## <a name="consent-framework"></a>Zustimmungsframework

Sie können das Zustimmungsframework von Azure AD verwenden, um mehrinstanzenfähige Web- und native Clientanwendungen zu entwickeln. Diese Anwendungen ermöglichen die Anmeldung mit Benutzerkonten über einen Azure AD-Mandanten, der sich von dem Mandanten unterscheidet, unter dem die Anwendung registriert ist. Möglicherweise benötigen sie auch Zugriff auf Web-APIs wie:
- Die Microsoft Graph-API für den Zugriff auf Azure AD, Intune und Dienste in Microsoft 365
- APIs anderer Microsoft-Dienste
- Ihre eigenen Web-APIs

Das Framework basiert darauf, dass Benutzer oder Administratoren ihre Einwilligung zur Registrierung einer Anwendung in ihrem Verzeichnis erteilen. Die Registrierung kann den Zugriff auf Verzeichnisdaten umfassen. Nach dem Einwilligen kann die Clientanwendung die Microsoft Graph-API im Namen des Benutzers aufrufen und die Informationen nach Bedarf verwenden.

Die [Microsoft Graph-API](https://developer.microsoft.com/graph/) ermöglicht den Zugriff auf Daten in Microsoft 365 wie:

- Kalender und Nachrichten in Exchange
- Websites und Listen in SharePoint
- Dokumente in OneDrive
- Notizbücher in OneNote
- Aufgaben in Planner
- Arbeitsmappen in Excel

Die Graph-API bietet auch Zugriff auf Benutzer und Gruppen in Azure AD und andere Datenobjekte in weiteren Microsoft Cloud Services.

Die folgenden Schritte beschreiben den Einwilligungsprozess für Anwendungsentwickler und Benutzer:

1. Angenommen, Sie verfügen über eine Webclientanwendung, die für den Zugriff auf eine Ressource oder API bestimmte Berechtigungen anfordern muss. Das Azure-Portal wird verwendet, um zur Konfigurationszeit Berechtigungsanforderungen zu deklarieren. Wie andere Konfigurationseinstellungen auch werden diese Teil der Azure AD-Registrierung der Anwendung. Für den Berechtigungsanforderungspfad müssen die folgenden Schritte ausgeführt werden:

    a. Klicken Sie im linken Bereich des Menüs auf **App-Registrierungen**, und öffnen Sie Ihre Anwendung, indem Sie den Anwendungsnamen in das Suchfeld eingeben.

    ![Screenshot, auf dem „App-Registrierungen“ im linken Menü ausgewählt und das Suchfeld „Anwendungs-ID“ hervorgehoben ist](./media/openidoauth-tutorial/application.png)

    b. Klicken Sie auf **API-Berechtigungen anzeigen**.

    ![Screenshot: Seite „API aufrufen“ mit hervorgehobener Schaltfläche „API-Berechtigungen anzeigen“](./media/openidoauth-tutorial/api-permission.png)

    c. Klicken Sie auf **Berechtigung hinzufügen**.

    ![Screenshot: Abschnitt „API-Berechtigungen“ mit hervorgehobener Schaltfläche „Berechtigung hinzufügen“](./media/openidoauth-tutorial/add-permission.png)

    d. Klicken Sie auf **Microsoft Graph**.

    ![Screenshot: Seite „API-Berechtigungen anfordern“, auf der die Registerkarte „Microsoft-API“ und die Kachel „Microsoft Graph“ ausgewählt sind](./media/openidoauth-tutorial/microsoft-graph.png)

    e. Wählen Sie unter **Delegierte Berechtigungen** und **Anwendungsberechtigungen** die erforderlichen Optionen aus.

    ![Graph-API](./media/openidoauth-tutorial/graphapi.png)

2. Beachten Sie, dass die Berechtigungen Ihrer Anwendung aktualisiert wurden. Die Anwendung wird ausgeführt, und ein Benutzer ist im Begriff, sie zum ersten Mal zu verwenden. Zuerst muss die Anwendung einen Autorisierungscode vom Azure AD-/Authorisierungsendpunkt erhalten. Der Autorisierungscode kann dann genutzt werden, um ein neues Zugriffs- und Aktualisierungstoken zu beschaffen.

3. Wenn der Benutzer noch nicht authentifiziert ist, wird er vom Azure AD-/Authorisierungsendpunkt zum Anmelden aufgefordert.

    ![Screenshot der Anmeldeaufforderung für das Konto](./media/openidoauth-tutorial/authentication.png)

4. Nachdem der Benutzer sich angemeldet hat, ermittelt Azure AD, ob ihm eine Seite zum Einwilligen angezeigt wird. Das Ergebnis dieser Ermittlung ist davon abhängig, ob der Benutzer (oder der Administrator seiner Organisation) die Anwendungszustimmung bereits erteilt hat.

   Wenn die Einwilligung nicht erteilt wurde, fordert Azure AD den Benutzer dazu auf und zeigt an, welche Berechtigungen für die Funktionalität benötigt werden. Die Berechtigungen, die im Dialogfeld für die Einwilligung angezeigt werden, entsprechen den ausgewählten delegierten Berechtigungen im Azure-Portal.

    ![Zustimmungsseite](./media/openidoauth-tutorial/consentpage.png)

Ein normaler Benutzer kann einigen Berechtigungen zustimmen. Andere Berechtigungen bedürfen der Einwilligung eines Mandantenadministrators.

## <a name="difference-between-admin-consent-and-user-consent"></a>Unterschied: Administratoreinwilligung vs. Benutzereinwilligung

Als Administrator können Sie auch für alle Benutzer in Ihrem Mandanten den delegierten Berechtigungen einer Anwendung zustimmen. Die Administratoreinwilligung verhindert, dass das Dialogfeld für die Einwilligung jedem Benutzer im Mandanten angezeigt wird. Benutzer mit der Administratorrolle können im Azure-Portal ihre Einwilligung erteilen. Wählen Sie auf der Seite **Einstellungen** für Ihre Anwendung **Erforderliche Berechtigungen** > **Administratoreinwilligung gewähren** aus.

![Schaltfläche „Berechtigungen erteilen“](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Das explizite Einwilligen über die Schaltfläche **Administratoreinwilligung gewähren** ist nun für Single-Page-Webanwendungen (SPAs) erforderlich, die „ADAL.js“ nutzen. Andernfalls tritt für die Anwendung ein Fehler auf, wenn das Zugriffstoken angefordert wird.

Nur für die App geltende Berechtigungen erfordern immer die Zustimmung eines Mandantenadministrators. Wenn die Anwendung eine nur für die App geltende Berechtigung anfordert und ein Benutzer versucht, sich bei der Anwendung anzumelden, wird eine Fehlermeldung angezeigt. Die Meldung besagt, dass der Benutzer nicht einwilligen kann.

Wenn Ihre Anwendung Berechtigungen nutzt, die die Einwilligung des Administrators erfordern, müssen Sie z.B. eine Schaltfläche oder einen Link implementieren, damit der Administrator die Aktion starten kann. Die Anforderung, die Ihre Anwendung für diese Aktion sendet, ist die reguläre OAuth2/OpenID Connect-Autorisierungsanforderung. Diese Anforderung enthält den Abfragezeichenfolgen-Parameter *prompt=admin_consent*.

Nachdem der Administrator seine Einwilligung erteilt hat und der Dienstprinzipal im Kundenmandanten erstellt wurde, ist für nachfolgende Anmeldeanforderungen der Parameter *prompt=admin_consent* nicht mehr erforderlich. Da der Administrator festgelegt hat, dass die angeforderten Berechtigungen zulässig sind, werden ab diesem Zeitpunkt keine weiteren Benutzer im Mandanten zum Einwilligen aufgefordert.

Ein Mandantenadministrator kann die Funktion deaktivieren, dass reguläre Benutzer Anwendungen zustimmen. Wenn diese Funktion deaktiviert ist, ist die Zustimmung des Administrators immer erforderlich, damit die Anwendung im Mandanten verwendet werden kann. Wenn Sie Ihre Anwendung mit deaktivierter Endbenutzereinwilligung testen möchten, können Sie die Konfiguration im [Azure-Portal](https://portal.azure.com/) ändern. Navigieren Sie dazu zum Abschnitt [Benutzereinstellungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) und dann zu **Unternehmensanwendungen**.

Der Parameter *prompt=admin_consent* kann auch von Anwendungen verwendet werden, die Berechtigungen anfordern, die keine Administratoreinwilligung erfordern. Ein Beispiel ist eine Anwendung, die erfordert, dass sich der Administrator des Mandanten einmal „registriert“ und danach keine anderen Benutzer zur Einwilligung aufgefordert werden.

Angenommen, eine Anwendung erfordert die Einwilligung des Administrators und ein Administrator meldet sich an, ohne dass der Parameter *prompt=admin_conent* gesendet wird. Wenn der Administrator der Anwendung erfolgreich seine Einwilligung erteilt, gilt das nur für sein Benutzerkonto. Normale Benutzer können sich weiterhin nicht anmelden und nicht ihre Einwilligung für die Anwendung geben. Diese Funktion ist sinnvoll, wenn Sie dem Mandantenadministrator die Möglichkeit geben möchten, Ihre Anwendung zu untersuchen, bevor Sie anderen Benutzern Zugriff gewähren.

## <a name="next-steps"></a>Nächste Schritte

[Einrichten des OIDC-basierten einmaligen Anmeldens (Single Sign-On, SSO) für eine Anwendung in Ihrem Azure Active Directory-Mandanten (Azure AD-Mandanten)](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)
