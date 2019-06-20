---
title: 'Tutorial: Azure Active Directory-Integration in Sectigo Certificate Manager | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Sectigo Certificate Manager konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 15-04-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 573e06f0dd57b92f7621ecf77039159a64249f18
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786492"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Tutorial: Azure Active Directory-Integration in Sectigo Certificate Manager

In diesem Tutorial erfahren Sie, wie Sie Sectigo Certificate Manager in Azure Active Directory (Azure AD) integrieren.

Die Integration von Sectigo Certificate Manager in Azure AD bietet Ihnen die folgenden Vorteile:

* Sie können Azure AD verwenden, um zu steuern, wer Zugriff auf Sectigo Certificate Manager hat.
* Benutzer können sich mit ihren Azure AD-Konten automatisch bei Sectigo Certificate Manager anmelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort, im Azure-Portal, verwalten.

Weitere Informationen zur Integration von SaaS-Apps (Software as a Service) in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Sectigo Certificate Manager konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure AD-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Sectigo Certificate Manager-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung und integrieren Sectigo Certificate Manager in Azure AD.

Sectigo Certificate Manager unterstützt die folgenden Features:

* **SP-initiiertes einmaliges Anmelden**
* **IDP-initiiertes einmaliges Anmelden**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Hinzufügen von Sectigo Certificate Manager im Azure-Portal

Um Sectigo Certificate Manager in Azure AD zu integrieren, müssen Sie Sectigo Certificate Manager Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Menü die Option **Azure Active Directory** aus.

    ![Die Option „Azure Active Directory“](common/select-azuread.png)

1. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie zum Hinzufügen einer Anwendung **Neue Anwendung** aus.

    ![Die Option „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie **Sectigo Certificate Manager** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Sectigo Certificate Manager** aus, und klicken Sie dann auf **Hinzufügen**.

    ![Sectigo Certificate Manager in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Sectigo Certificate Manager mithilfe eines Testbenutzers namens **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Sectigo Certificate Manager eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Sectigo Certificate Manager müssen Sie die folgenden Schritte ausführen:

| Aufgabe | BESCHREIBUNG |
| --- | --- |
| **[Konfigurieren des einmaligen Anmeldens in Azure AD](#configure-azure-ad-single-sign-on)** | Ermöglicht Ihren Benutzern, dieses Feature zu verwenden. |
| **[Konfigurieren des einmaligen Anmeldens für Sectigo Certificate Manager](#configure-sectigo-certificate-manager-single-sign-on)** | Konfiguriert die Einstellungen für einmaliges Anmelden in der Anwendung. |
| **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** | Testet das einmalige Anmelden in Azure AD für einen Benutzer namens Britta Simon. |
| **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** | Ermöglicht Britta Simon die Verwendung des einmaligen Anmeldens in Azure AD. |
| **[Erstellen eines Sectigo Certificate Manager-Testbenutzers](#create-a-sectigo-certificate-manager-test-user)** | Erstellt eine Entsprechung von Britta Simon in Sectigo Certificate Manager, die mit ihrer Darstellung in Azure AD verknüpft ist. |
| **[Testen des einmaligen Anmeldens](#test-single-sign-on)** | Überprüft, ob die Konfiguration funktioniert. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren Sie das einmalige Anmelden von Azure AD bei Sectigo Certificate Manager im Azure-Portal.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) im Anwendungsintegrationsbereich für **Sectigo Certificate Manager** die Option **Einmaliges Anmelden** aus.

    ![Option „Einmaliges Anmelden konfigurieren“](common/select-sso.png)

1. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML** oder **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** (Stiftsymbol), um den Bereich **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Zum Konfigurieren des *IDP-initiierten Modus* führen Sie im Bereich **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    1. Geben Sie im Feld **Bezeichner** eine der folgenden URLs ein:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. Geben Sie im Feld **Antwort-URL** eine der folgenden URLs ein:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Wählen Sie **Zusätzliche URLs festlegen** aus.

    1. Geben Sie im Feld **Relayzustand** eine der folgenden URLs ein:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![SSO-Informationen zur Domäne und zu den URLs für Sectigo Certificate Manager](common/idp-relay.png)

1.  Zum Konfigurieren der Anwendung im *SP-initiierten Modus* führen Sie die folgenden Schritte aus:

    * Geben Sie im Feld **Anmelde-URL** eine der folgenden URLs ein:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![SSO-Informationen zur Domäne und zu den URLs für Sectigo Certificate Manager](common/both-signonurl.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** neben **Zertifikat (Base64)** aus. Wählen Sie eine Option zum Herunterladen entsprechend Ihren Anforderungen aus. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Option zum Herunterladen des Zertifikats (Base64)](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Sectigo Certificate Manager einrichten** die folgenden URLs entsprechend Ihren Anforderungen:

    * Anmelde-URL
    * Azure AD-Bezeichner
    * Abmelde-URL

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Sectigo Certificate Manager

Zum Konfigurieren des einmaligen Anmeldens auf Sectigo Certificate Manager-Seite senden Sie das heruntergeladene Zertifikat (Base64) und die kopierten URLs aus dem Azure-Portal an das [Supportteam von Sectigo Certificate Manager](https://sectigo.com/support). Anhand der von Ihnen gesendeten Informationen stellt das Supportteam von Sectigo Certificate Manager sicher, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet ist.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

    ![Die Optionen „Benutzer“ und „Alle Benutzer“](common/users.png)

1. Wählen Sie **Neuer Benutzer** aus.

    ![Die Option „Neuer Benutzer“](common/new-user.png)

1. Führen Sie im Bereich **Benutzer** die folgenden Schritte aus:

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie im Feld **Benutzername** den Eintrag **brittasimon\@\<IhreUnternehmensdomäne>.\<Erweiterung\>** ein. Beispiel: **brittasimon\@contoso.com**

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**. Notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    1. Klicken Sie auf **Erstellen**.

    ![Der Bereich „Benutzer“](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf Sectigo Certificate Manager, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Sectigo Certificate Manager** aus.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **Sectigo Certificate Manager** aus.

    ![Sectigo Certificate Manager in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie im Menü die Option **Benutzer und Gruppen** aus.

    ![Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Bereich **Benutzer und Gruppen** den Eintrag **Britta Simon** in der Benutzerliste aus. Wählen Sie **Auswählen**.

1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Bereich **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie **Auswählen**.

1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Erstellen eines Sectigo Certificate Manager-Testbenutzers

In diesem Abschnitt erstellen Sie in Sectigo Certificate Manager einen Benutzer mit dem Namen Britta Simon. Wenden Sie sich an das [Supportteam von Sectigo Certificate Manager](https://sectigo.com/support), um den Benutzer der Sectigo Certificate Manager-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Portals „Meine Apps“.

Nachdem Sie einmaliges Anmelden eingerichtet haben, werden Sie bei Auswahl von **Sectigo Certificate Manager** im Portal „Meine Apps“ automatisch bei Sectigo Certificate Manager angemeldet. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in diesen Artikeln:

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


