---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Netvision Compas | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Netvision Compas konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.openlocfilehash: 423ac882c81582f2843eeba37d11c660662ad6e2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519400"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Netvision Compas

In diesem Tutorial erfahren Sie, wie Sie Netvision Compas in Azure Active Directory (Azure AD) integrieren. Die Integration von Netvision Compas in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Netvision Compas hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei Netvision Compas anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Netvision Compas-Abonnement für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Netvision Compas unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Nach dem Konfigurieren des einmaligen Anmeldens von Netvision Compas können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.


## <a name="adding-netvision-compas-from-the-gallery"></a>Hinzufügen von Netvision Compas aus dem Katalog

Zum Konfigurieren der Integration von Netvision Compas in Azure AD müssen Sie Netvision Compas aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Netvision Compas** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Netvision Compas** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Netvision Compas

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Netvision Compas mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Netvision Compas eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Netvision Compas die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Netvision Compas](#configure-netvision-compas-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Konfigurieren eines Netvision Compas-Testbenutzers](#configure-netvision-compas-test-user)** , um eine Entsprechung von B. Simon in Netvision Compas zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Netvision Compas** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP** -initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<TENANT>.compas.cloud/Identity/Saml20`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den Netvision Compas-Client](mailto:contact@net.vision). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML** , und wählen Sie **Herunterladen** aus, um die Metadatendatei herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)



### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Netvision Compas gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Netvision Compas** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-netvision-compas-sso"></a>Konfigurieren des einmaligen Anmeldens für Netvision Compas

In diesem Abschnitt aktivieren Sie SAML-SSO in **Netvision Compas** .
1. Melden Sie sich mit einem Administratorkonto bei **Netvision Compas** an, und öffnen Sie den Verwaltungsbereich.

    ![Verwaltungsbereich](media/netvision-compas-tutorial/admin.png)

1. Navigieren Sie zum Bereich **System** , und wählen Sie **Identity Providers** (Identitätsanbieter) aus.

    ![Admin-IDPs](media/netvision-compas-tutorial/admin-idps.png)

1. Wählen Sie die Aktion **Add** (Hinzufügen) aus, um Azure AD als neuen IDP zu registrieren.

    ![Hinzufügen des IDP](media/netvision-compas-tutorial/idps-add.png)

1. Wählen Sie für **Provider type** (Anbietertyp) die Option **SAML** aus.
1. Geben Sie aussagekräftige Werte für die Felder **Display name** (Anzeigename) und **Description** (Beschreibung) ein.
1. Weisen Sie dem IDP **Netvision Compas** -Benutzer zu, indem Sie Benutzer in der Liste **Available users** (Verfügbare Benutzer) und anschließend die Schaltfläche **Add selected** (Ausgewählte hinzufügen) auswählen. Benutzer können dem IDP auch mithilfe des Bereitstellungsverfahrens zugewiesen werden.
1. Klicken Sie für die SAML-Option für **Metadaten** auf die Schaltfläche **Choose File** (Datei auswählen), und wählen Sie die Metadatendatei aus, die Sie zuvor auf dem Computer gespeichert haben.
1. Klicken Sie auf **Speichern** .

    ![Bearbeiten des IDP](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Konfigurieren eines Netvision Compas-Testbenutzers

In diesem Abschnitt konfigurieren Sie einen vorhandenen Benutzer in **Netvision Compas** , um Azure AD für einmaliges Anmelden zu verwenden.
1. Führen Sie das Verfahren für die Benutzerbereitstellung von **Netvision Compas** wie von Ihrem Unternehmen vorgegeben aus, oder bearbeiten Sie ein vorhandenes Benutzerkonto.
1. Stellen Sie beim Definieren des Benutzerprofils sicher, dass die Benutzeradresse unter **Email (Personal)** (E-Mail (privat)) mit dem Azure AD-Benutzernamen übereinstimmt: username@companydomain.extension. Beispiel: `B.Simon@contoso.com`.

    ![Benutzer bearbeiten](media/netvision-compas-tutorial/user-config.png)

Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für das einmalige Anmelden.

### <a name="using-the-access-panel-idp-initiated"></a>Verwenden des Zugriffsbereichs (IDP-initiiert)

Wenn Sie im Zugriffsbereich auf die Kachel „Netvision Compas“ klicken, sollten Sie automatisch bei der Netvision Compas-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Direktzugriff auf Netvision Compas (SP-initiiert)

1. Rufen Sie die **Netvision Compas** -URL auf. Beispiel: `https://tenant.compas.cloud`.
1. Geben Sie den **Netvision Compas** -Benutzernamen ein, und wählen Sie **Next** (Weiter) aus.

    ![Anmelden des Benutzers](media/netvision-compas-tutorial/login-user.png)

1. **(optional)** Sind dem Benutzer mehrere IDPs in **Netvision Compas** zugewiesen, wird eine Liste der verfügbaren IDPs angezeigt. Wählen Sie den Azure AD-IDP aus, den Sie zuvor in **Netvision Compas** konfiguriert haben.

    ![Auswählen der Anmeldung](media/netvision-compas-tutorial/login-choose.png)

1. Sie werden zu Azure AD umgeleitet, um die Authentifizierung durchzuführen. Sobald Sie erfolgreich authentifiziert wurden, sollten Sie automatisch bei der **Netvision Compas** -Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Netvision Compas mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)