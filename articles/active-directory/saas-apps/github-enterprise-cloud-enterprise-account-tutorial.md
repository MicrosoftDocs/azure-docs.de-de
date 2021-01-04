---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit GitHub Enterprise Cloud - Enterprise Account | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und GitHub Enterprise Cloud - Enterprise Account konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: jeedes
ms.openlocfilehash: d88cbb79b42637721412dd0a35c231782a896721
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029845"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit GitHub Enterprise Cloud - Enterprise Account

In diesem Tutorial erfahren Sie, wie Sie GitHub Enterprise Cloud - Enterprise Account in Azure Active Directory (Azure AD) integrieren. Die Integration von GitHub Enterprise Cloud - Enterprise Account mit Azure AD ermöglicht Folgendes:

* Steuern in Azure AD, wer Zugriff auf ein GitHub Enterprise Account und die Organisationen unter dem Enterprise Account hat

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein [GitHub Enterprise Account](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts)
* Ein GitHub-Benutzerkonto, bei dem es sich um einen Enterprise Account-Besitzer handelt 

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* GitHub Enterprise Cloud - Enterprise Account unterstützt **SP**- und **IDP**-initiiertes einmaliges Anmelden.
* GitHub Enterprise Cloud - Enterprise Account unterstützt die **Just-In-Time**-Benutzerbereitstellung.
* Nach dem Konfigurieren von GitHub Enterprise Cloud - Enterprise Account können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Hinzufügen von GitHub Enterprise Cloud - Enterprise Account aus dem Katalog

Zum Konfigurieren der Integration von GitHub Enterprise Cloud - Enterprise Account in Azure AD müssen Sie GitHub Enterprise Cloud - Enterprise Account aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **GitHub Enterprise Cloud - Enterprise Account** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **GitHub Enterprise Cloud - Enterprise Account** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Konfigurieren und Testen des einmaligen Anmeldens für GitHub Enterprise Cloud - Enterprise Account

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit GitHub Enterprise Cloud - Enterprise Account mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in GitHub Enterprise Cloud - Enterprise Account eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit GitHub Enterprise Cloud - Enterprise Account die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen Ihres Azure AD-Benutzers und des Testbenutzerkontos zur GitHub-App](#assign-the-azure-ad-test-user)** , um Ihr Benutzerkonto zu aktivieren und das einmalige Anmelden mit Azure AD für den Testbenutzer `B.Simon` zu testen.
1. **[Aktivieren und Testen von SAML für das Enterprise Account und die zugehörigen Organisationen](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Testen des einmaligen Anmeldens mit einem anderen Enterprise Account-Besitzer oder dem Konto eines Mitglieds der Organisation](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **GitHub Enterprise Cloud - Enterprise Account** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://github.com/enterprises/<ENTERPRISE-SLUG>`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

     Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`.

    > [!NOTE]
    > Ersetzen Sie `<ENTERPRISE-SLUG>` durch den tatsächlichen Namen Ihres GitHub Enterprise Account.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificateBase64.png)

1. Kopieren Sie im Abschnitt **GitHub Enterprise Cloud - Enterprise Account einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen `B.Simon`.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>Zuweisen Ihres Azure AD-Benutzers und des Testbenutzerkontos zur GitHub-App

In diesem Abschnitt ermöglichen Sie `B.Simon` und Ihrem Benutzerkonto die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf GitHub Enterprise Cloud – Enterprise Account gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **GitHub Enterprise Cloud - Enterprise Account** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** und Ihr Benutzerkonto aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>Aktivieren und Testen von SAML für das Enterprise Account und die zugehörigen Organisationen

Führen Sie die Schritte in [dieser GitHub-Dokumentation](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account) aus, um einmaliges Anmelden aufseiten von **GitHub Enterprise Cloud – Enterprise Account** zu konfigurieren. 
1. Melden Sie sich bei „GitHub.com“ mit einem Benutzerkonto an, bei dem es sich um einen [Enterprise Account-Besitzer](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner) handelt. 
1. Kopieren Sie den Wert im Azure-Portal in der App aus dem Feld `Login URL`, und fügen Sie ihn in den SAML-Einstellungen für das GitHub Enterprise Account in das Feld `Sign on URL` ein. 
1. Kopieren Sie den Wert im Azure-Portal in der App aus dem Feld `Azure AD Identifier`, und fügen Sie ihn in den SAML-Einstellungen für das GitHub Enterprise Account in das Feld `Issuer` ein. 
1. Kopieren Sie den Inhalt der Datei **Zertifikat (Base64)** , die Sie in den Schritten oben über das Azure-Portal heruntergeladen haben, und fügen Sie sie in den SAML-Einstellungen für das GitHub Enterprise Account in das entsprechende Feld ein. 
1. Klicken Sie auf `Test SAML configuration`, und vergewissern Sie sich, dass Sie die Authentifizierung über das GitHub Enterprise Account für Azure AD erfolgreich durchführen können.
1. Speichern Sie die Einstellungen, nachdem der Test erfolgreich abgeschlossen wurde. 
1. Nachdem Sie die Authentifizierung über SAML für das GitHub Enterprise Account zum ersten Mal durchgeführt haben, wird unter dem Konto eine _verknüpfte externe Identität_ erstellt, mit der das angemeldete GitHub-Benutzerkonto dem Azure AD-Benutzerkonto zugeordnet wird.  
 
Nachdem Sie das einmalige Anmelden per SAML (SAML SSO) für Ihr GitHub Enterprise Account aktiviert haben, ist SAML SSO standardmäßig für alle Organisationen unter Ihrem Enterprise Account aktiviert. Alle Mitglieder müssen sich per SAML SSO authentifizieren, um Zugriff auf die Organisationen zu erhalten, in denen sie Mitglied sind. Enterprise-Besitzer müssen sich per SAML SSO authentifizieren, wenn sie auf ein Enterprise Account zugreifen.

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>Testen des einmaligen Anmeldens mit einem anderen Enterprise Account-Besitzer oder Konto eines Mitglieds der Organisation

Nachdem die SAML-Integration für das GitHub Enterprise Account eingerichtet wurde (gilt auch für die GitHub-Organisationen unter dem Enterprise Account), sollten auch andere Enterprise Account-Besitzer, die der App in Azure AD zugewiesen sind, zur GitHub Enterprise Account-URL (`https://github.com/enterprises/<enterprise account>`) navigieren können. Sie können dann die Authentifizierung per SAML durchführen und auf die Richtlinien und Einstellungen unter dem GitHub Enterprise Account zugreifen. 

Ein Organisationsbesitzer einer Organisation unter einem Enterprise Account sollte [einen Benutzer zum Beitreten zu seiner GitHub-Organisation einladen](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization) können. Melden Sie sich bei „GitHub.com“ mit einem Konto eines Organisationsbesitzers an, und führen Sie die im Artikel angegebenen Schritte zum Einladen von `B.Simon` aus. Für `B.Simon` muss ein GitHub-Benutzerkonto erstellt werden, falls es noch nicht vorhanden ist. 

Gehen Sie wie folgt vor, um den Zugriff auf die GitHub-Organisation unter dem Enterprise Account mit dem Konto des Testbenutzers `B.Simon` zu testen:
1. Laden Sie `B.Simon` als Organisationsbesitzer für eine Organisation unter dem Enterprise Account ein. 
1. Melden Sie sich bei „GitHub.com“ mit dem Benutzerkonto an, das Sie mit dem Azure AD-Benutzerkonto von `B.Simon` verknüpfen möchten.
1. Melden Sie sich bei Azure AD an, indem Sie das Benutzerkonto `B.Simon` verwenden.
1. Navigieren Sie zur GitHub-Organisation. Der Benutzer sollte zur erneuten Authentifizierung per SAML aufgefordert werden. Nach dem erfolgreichen Abschluss der SAML-Authentifizierung sollte `B.Simon` auf die Organisationsressourcen zugreifen können. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [GitHub Enterprise Cloud - Enterprise Account mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)
