---
title: 'Tutorial: Azure Active Directory-Integration mit HubSpot | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und HubSpot konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d74cc1665867568032bb1343e4f2c26c50fe15a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65770190"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutorial: Azure Active Directory-Integration in HubSpot

In diesem Tutorial erfahren Sie, wie Sie HubSpot in Azure Active Directory (Azure AD) integrieren.

Die Integration von HubSpot in Azure AD bietet die folgenden Vorteile:

* Sie können mit Azure AD steuern, wer Zugriff auf HubSpot hat.
* Benutzer können sich mit ihren Azure AD-Konten automatisch bei HubSpot anmelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort, im Azure-Portal, verwalten.

Weitere Informationen zur Integration von SaaS-Apps (Software-as-a-Service) in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit HubSpot konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure AD-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein HubSpot-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung und integrieren HubSpot in Azure AD.

HubSpot unterstützt die folgenden Features:

* **SP-initiiertes einmaliges Anmelden**
* **IDP-initiiertes einmaliges Anmelden**

## <a name="add-hubspot-in-the-azure-portal"></a>Hinzufügen von HubSpot im Azure-Portal

Damit Sie HubSpot in Azure AD integrieren können, müssen Sie HubSpot zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Menü **Azure Active Directory** aus.

    ![Die Option „Azure Active Directory“](common/select-azuread.png)

1. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie zum Hinzufügen einer Anwendung **Neue Anwendung** aus.

    ![Die Option „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie im Suchfeld **HubSpot** ein. Wählen Sie in den Suchergebnissen **HubSpot** und dann **Hinzufügen** aus.

    ![HubSpot in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit HubSpot mithilfe eines Testbenutzers namens **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in HubSpot eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit HubSpot müssen Sie die folgenden Schritte ausführen:

| Aufgabe | BESCHREIBUNG |
| --- | --- |
| **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** | Ermöglicht Ihren Benutzern die Verwendung dieses Features. |
| **[Konfigurieren des einmaligen Anmeldens für HubSpot](#configure-hubspot-single-sign-on)** | Konfiguriert die Einstellungen für einmaliges Anmelden in der Anwendung. |
| **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** | Testet das einmalige Anmelden von Azure AD für einen Benutzer namens Britta Simon. |
| **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** | Ermöglicht Britta Simon die Verwendung des einmaligen Anmeldens von Azure AD. |
| **[Erstellen eines HubSpot-Testbenutzers](#create-a-hubspot-test-user)** | Erstellt ein Pendant zu Britta Simon in HubSpot, das mit ihrer Darstellung in Azure AD verknüpft ist. |
| **[Testen des einmaligen Anmeldens](#test-single-sign-on)** | Überprüft, ob die Konfiguration funktioniert. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren Sie das einmalige Anmelden von Azure AD mit HubSpot im Azure-Portal.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) im Anwendungsintegrationsbereich für **HubSpot** die Option **Einmaliges Anmelden** aus.

    ![Option „Einmaliges Anmelden konfigurieren“](common/select-sso.png)

1. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML** oder **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** (Stiftsymbol) aus, um den Bereich **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Zum Konfigurieren des *IDP-initiierten Modus* führen Sie im Bereich **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    1. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<KUNDEN-ID\>.

    1. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<KUNDEN-ID\>.

    ![SSO-Informationen zur Domäne und zu den URLs für HubSpot](common/idp-intiated.png)

    > [!NOTE]
    > Sie können sich zum Formatieren der URLs auch die Muster im Bereich **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. So konfigurieren Sie die Anwendung im *SP-initiierten Modus*:

    1. Wählen Sie **Zusätzliche URLs festlegen** aus.

    1. Geben Sie im Feld **Anmelde-URL** die URL **https:\//app.hubspot.com/login** ein.

    ![Option „Zusätzliche URLs festlegen“](common/metadata-upload-additional-signon.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** neben **Zertifikat (Base64)** aus. Wählen Sie eine Option zum Herunterladen entsprechend Ihren Anforderungen aus. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Option zum Herunterladen des Zertifikats (Base64)](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **HubSpot einrichten** die folgenden URLs entsprechend Ihren Anforderungen:

    * Anmelde-URL
    * Azure AD-Bezeichner
    * Abmelde-URL

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für HubSpot

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei Ihrem HubSpot-Administratorkonto an.

1. Wählen Sie oben rechts auf der Seite das Symbol für die **Einstellungen** aus.

    ![Symbol „Settings“ (Einstellungen) in HubSpot](./media/hubspot-tutorial/config1.png)

1. Wählen Sie **Account Defaults** (Kontostandardwerte) aus.

    ![Option „Account Defaults“ (Kontostandardwerte) in HubSpot](./media/hubspot-tutorial/config2.png)

1. Scrollen Sie nach unten zum Abschnitt **Security** (Sicherheit), und wählen Sie **Set up** (Einrichten) aus.

    ![Option „Set up“ (Einrichten) in HubSpot](./media/hubspot-tutorial/config3.png)

1. Führen Sie im Abschnitt **Set up single sign-on** (Einmaliges Anmelden einrichten) die folgenden Schritte aus:

    1. Wählen Sie im Feld **Audience URL (Service Provider Entity ID)** (Zielgruppen-URL (ID der Dienstanbieterentität)) die Option **Copy** (Kopieren) aus, um den Wert zu kopieren. Fügen Sie im Azure-Portal im Bereich **Grundlegende SAML-Konfiguration** den Wert in das Feld **Bezeichner** ein.

    1. Wählen Sie im Feld **Sign on URL, ACS, Recipient, or Redirect** (Anmelde-URL, ACS, Empfänger oder Umleitung) die Option **Copy** (Kopieren) aus, um den Wert zu kopieren. Fügen Sie im Azure-Portal im Bereich **Grundlegende SAML-Konfiguration** den Wert in das Feld **Antwort-URL** ein.

    1. Fügen Sie in HubSpot im Feld **Identity Provider Identifier or Issuer URL** (Identitätsanbieterbezeichner oder Aussteller-URL) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie in HubSpot im Feld **Identity Provider Single Sign-On URL** (URL für einmaliges Anmelden des Identitätsanbieters) den Wert für **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Öffnen Sie im Windows-Editor das heruntergeladene Zertifikat (Base64). Kopieren Sie den Inhalt der Datei. Fügen Sie ihn anschließend in HubSpot in das Feld  **X.509 Certificate** (X.509-Zertifikat) ein.

    1. Wählen Sie **Überprüfen**aus.

        ![Abschnitt „Set up single sign-on“ (Einmaliges Anmelden einrichten) in HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

    ![Optionen „Benutzer“ und „Alle Benutzer“](common/users.png)

1. Wählen Sie **Neuer Benutzer** aus.

    ![Option „Neuer Benutzer“](common/new-user.png)

1. Führen Sie im Bereich **Benutzer** die folgenden Schritte aus:

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie im Feld **Benutzername** den Namen **brittasimon\@\<IhreUnternehmensdomäne>.\<Erweiterung\>** ein. Beispiel: **brittasimon\@contoso.com**

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**. Notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    1. Klicken Sie auf **Erstellen**.

    ![Der Bereich „Benutzer“](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf HubSpot, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **HubSpot** aus.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **HubSpot** aus.

    ![HubSpot in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie im Menü die Option **Benutzer und Gruppen** aus.

    ![Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Bereich **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus. Wählen Sie **Auswählen**.

1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Bereich **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie **Auswählen**.

1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-hubspot-test-user"></a>Erstellen eines HubSpot-Testbenutzers

Damit sich Azure AD-Benutzer bei HubSpot anmelden können, müssen sie in HubSpot bereitgestellt werden. Im Fall von HubSpot ist die Bereitstellung eine manuelle Aufgabe.

So stellen Sie in HubSpot ein Benutzerkonto bereit:

1. Melden Sie sich bei der HubSpot-Unternehmenswebsite als Administrator an.

1. Wählen Sie oben rechts auf der Seite das Symbol für die **Einstellungen** aus.

    ![Symbol „Settings“ (Einstellungen) in HubSpot](./media/hubspot-tutorial/config1.png)

1. Wählen Sie **Users & Teams** (Benutzer und Teams) aus.

    ![Option „Users & Teams“ (Benutzer und Teams) in HubSpot](./media/hubspot-tutorial/user1.png)

1. Wählen Sie **Create User** (Benutzer erstellen) aus.

    ![Option „Create user“ (Benutzer erstellen) in HubSpot](./media/hubspot-tutorial/user2.png)

1. Geben Sie im Feld **Add email addess(es)** (E-Mail-Adresse(n) hinzufügen) die E-Mail-Adresse des Benutzers im Format „brittasimon\@contoso.com“ ein, und wählen Sie **Next** (Weiter) aus.

    ![Das Feld „Add email address(es)“ (E-Mail-Adresse(n) hinzufügen) im Abschnitt „Create users“ (Benutzer erstellen) in HubSpot](./media/hubspot-tutorial/user3.png)

1. Wählen Sie im Abschnitt **Create users** (Benutzer erstellen) jede Registerkarte aus. Legen Sie auf jeder Registerkarte die relevanten Optionen und Berechtigungen für den Benutzer fest. Klicken Sie anschließend auf **Weiter**.

    ![Registerkarten im Abschnitt „Create users“ (Benutzer erstellen) in HubSpot](./media/hubspot-tutorial/user4.png)

1. Wählen Sie **Send** (Senden) aus, um die Einladung an den Benutzer zu senden.

    ![Option „Send“ (Senden) in HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Der Benutzer wird aktiviert, nachdem der Benutzer die Einladung angenommen hat.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Portals „Meine Apps“.

Nachdem Sie einmaliges Anmelden eingerichtet haben, werden Sie bei Auswahl von **HubSpot** im Portal „Meine Apps“ automatisch bei HubSpot angemeldet. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
