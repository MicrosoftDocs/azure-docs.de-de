---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit New Relic | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und New Relic konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 4cf3f9d0ae23bab4d2412b47e5841d6b8a56b65a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327065"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit New Relic

In diesem Tutorial erfahren Sie, wie Sie New Relic in Azure Active Directory (Azure AD) integrieren. Die Integration von New Relic in Azure AD ermöglicht Folgendes:

* Steuern in Azure AD, wer Zugriff auf New Relic hat
* Ermöglichen, dass sich Benutzer mit ihren Azure AD-Konten automatisch bei New Relic anmelden können
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* New Relic-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* New Relic unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.
* Nach dem Konfigurieren von New Relic können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="add-new-relic-application-from-the-gallery"></a>Hinzufügen einer New Relic-Anwendung aus dem Katalog

Zum Konfigurieren der Integration von New Relic in Azure AD müssen Sie **New Relic (By Organization)** über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wähle den Dienst **Azure Active Directory** aus.
1. Wählen Sie **Unternehmensanwendungen**.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie auf der Seite **Azure AD-Katalog durchsuchen** im Suchfeld den Suchbegriff **New Relic (By Organization)** ein.
1. Wählen Sie im Ergebnisbereich **New Relic (By Organization)** und dann **Erstellen** aus. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für New Relic

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit New Relic mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in New Relic eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit New Relic die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für New Relic](#configure-new-relic-sso)** , um die Einstellungen für einmaliges Anmelden aufseiten von New Relic zu konfigurieren.
   1. **[Erstellen eines New Relic-Testbenutzers](#create-a-new-relic-test-user)** , um eine Entsprechung von B. Simon in New Relic zu erhalten, die mit dem Azure AD-Benutzer verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **New Relic (By Organization)** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.

1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für **Bezeichner** und **Antwort-URL** ein.

   * Diese Werte können mit der Anwendung **My Organization** von New Relic abgerufen werden. Führen Sie die folgenden Schritte aus, um diese Anwendung zu verwenden:
      1. Führen Sie die [Anmeldung](https://login.newrelic.com/) bei New Relic durch.
      1. Wählen Sie im obersten Menü die Option **Apps** aus.
      1. Wählen Sie im Abschnitt **Your apps** (Ihre Apps) die Option **My Organization** aus.
      1. Klicken Sie auf **Authentication domains** (Authentifizierungsdomänen).
      1. Wählen Sie die Authentifizierungsdomäne aus, mit der Sie für das einmalige Anmelden von Azure AD eine Verbindung herstellen möchten (falls Sie über mehr als eine Authentifizierungsdomäne verfügen). Die meisten Unternehmen verfügen nur über eine Authentifizierungsdomäne mit dem Namen **Default**. Wenn nur eine Authentifizierungsdomäne vorhanden ist, müssen Sie also keine Auswahl treffen.
      1. Im Abschnitt **Authentication** (Authentifizierung) enthält das Feld **Assertion consumer URL** (Assertionsverbraucher-URL) den Wert, der für die **Antwort-URL** verwendet wird.
      1. Im Abschnitt **Authentication** (Authentifizierung) enthält das Feld **Our entity ID** (Unsere Entitäts-ID) den Wert, der als **Bezeichner** verwendet wird.

1. Stellen Sie im Abschnitt **Benutzerattribute und Ansprüche** sicher, dass **Eindeutige Benutzer-ID** einem Feld zugeordnet ist, das die E-Mail-Adresse von New Relic enthält.

   * Sie können das Standardfeld **user.userprincipalname** verwenden, falls die darin enthaltenen Werte den E-Mail-Adressen für New Relic entsprechen.
   * Unter Umständen ist das Feld **user.mail** aber besser für Sie geeignet, wenn **user.userprincipalname** nicht die New Relic-E-Mail-Adresse ist.

1. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** die **App-Verbundmetadaten-URL**, und speichern Sie den Wert zur späteren Verwendung.

1. Kopieren Sie im Abschnitt **New Relic (By Organization) einrichten** die **Anmelde-URL**, und speichern Sie den Wert zur späteren Verwendung.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im Azure-Portal den Dienst **Azure Active Directory** aus.
1. Wählen Sie **Benutzer** aus.
1. Wählen Sie zum Hinzufügen eines neuen Benutzers oben auf dem Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie auf der Seite **Neuer Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `b.simon@contoso.com`. Dies sollte die E-Mail-Adresse sein, die Sie aufseiten von New Relic verwenden möchten.
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Aktivieren Sie das Kontrollkästchen **Show password** (Kennwort anzeigen), und speichern Sie dann den Wert, der im Feld **Initial password** (Anfangskennwort) angezeigt wird.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure AD, indem Sie ihr Zugriff auf die Anwendung **New Relic (By Organization)** gewähren.

1. Wählen Sie im Azure-Portal den Dienst **Azure Active Directory** aus.
1. Wählen Sie **Unternehmensanwendungen**.
1. Wählen Sie in der Anwendungsliste **New Relic by Organization** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** (bzw. je nach Planebene auch **Benutzer**) aus.

   ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** (bzw. **Benutzer**) in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-new-relic-sso"></a>Konfigurieren des einmaligen Anmeldens für New Relic

Führen Sie die folgenden Schritte aus, um einmaliges Anmelden für New Relic zu konfigurieren.

1. Führen Sie die [Anmeldung](https://login.newrelic.com/) bei New Relic durch.

1. Wählen Sie im obersten Menü die Option **Apps** aus.

1. Wählen Sie im Abschnitt **Your apps** (Ihre Apps) die Option **My Organization** aus.

1. Klicken Sie auf **Authentication domains** (Authentifizierungsdomänen).

1. Wählen Sie die Authentifizierungsdomäne aus, mit der Sie für das einmalige Anmelden von Azure AD eine Verbindung herstellen möchten (falls Sie über mehr als eine Authentifizierungsdomäne verfügen). Die meisten Unternehmen verfügen nur über eine Authentifizierungsdomäne mit dem Namen **Default**. Wenn nur eine Authentifizierungsdomäne vorhanden ist, müssen Sie also keine Auswahl treffen.

1. Klicken Sie im Abschnitt **Authentifizierung** auf **Konfigurieren**.

   1. Geben Sie im Feld **Source of SAML metadata** (Quelle der SAML-Metadaten) den Wert ein, den Sie weiter oben im Feld **App-Verbundmetadaten-URL** aufseiten von Azure AD gespeichert haben.

   1. Geben Sie im Feld **SSO Target URL** (SSO-Ziel-URL) den Wert aus dem Feld **Anmelde-URL** ein, den Sie in Azure AD zuvor gespeichert haben.

   1. Klicken Sie auf **Speichern**, nachdem Sie sich vergewissert haben, dass die Einstellungen sowohl für Azure AD als auch für New Relic korrekt sind. Wenn die Konfiguration auf beiden Seiten nicht korrekt ist, können sich Ihre Benutzer nicht bei New Relic anmelden.

### <a name="create-a-new-relic-test-user"></a>Erstellen eines New Relic-Testbenutzers

In diesem Abschnitt erstellen Sie in New Relic einen Benutzer namens „B. Simon“. Führen Sie zum Erstellen des Benutzers die folgenden Schritte aus.

1. Führen Sie die [Anmeldung](https://login.newrelic.com/) bei New Relic durch.

1. Wählen Sie im obersten Menü die Option **Apps** aus.

1. Wählen Sie im Abschnitt **Your apps** (Ihre Apps) die Option **Benutzerverwaltung** aus.

1. Klicken auf die Schaltfläche **Benutzer hinzufügen**.

   1. Geben Sie **B.Simon** in das Feld **Name** ein.
   
   1. Geben Sie im Feld **E-Mail** den Wert ein, der beim einmaligen Anmelden von Azure AD gesendet wird.
   
   1. Wählen Sie den **Typ** und die **Gruppe** für den Benutzer aus. Für einen Testbenutzer sind **Basic User** (Basisbenutzer) als Typ und **User** (Benutzer) als Gruppe eine gute Wahl.
   
   1. Klicken Sie auf **Benutzer hinzufügen**, um den Benutzer zu speichern.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel **New Relic (By Organization)** klicken, sollten Sie automatisch bei New Relic angemeldet werden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Ausprobieren von New Relic mit Azure AD](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
