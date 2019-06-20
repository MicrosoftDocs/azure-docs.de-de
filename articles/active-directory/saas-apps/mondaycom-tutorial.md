---
title: 'Tutorial: Azure Active Directory-Integration mit monday.com | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und monday.com konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc142bf02a44ea85861f4cc648fd7ee8602c7520
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896880"
---
# <a name="tutorial-azure-active-directory-integration-with-mondaycom"></a>Tutorial: Azure Active Directory-Integration mit monday.com

In diesem Tutorial erfahren Sie, wie Sie monday.com in Azure Active Directory (Azure AD) integrieren.

Die Integration von monday.com in Azure AD bietet die folgenden Vorteile:

* Sie können mit Azure AD steuern, wer Zugriff auf monday.com hat.
* Benutzer können sich mit ihren Azure AD-Konten automatisch bei monday.com anmelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort, im Azure-Portal, verwalten.

Weitere Informationen zur Integration von SaaS-Apps (Software as a Service) in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit monday.com konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure AD-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* monday.com-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung und integrieren monday.com in Azure AD.

monday.com unterstützt die folgenden Features:

* **SP-initiiertes einmaliges Anmelden**
* **IDP-initiiertes einmaliges Anmelden**
* **Just-In-Time-Benutzerbereitstellung**

## <a name="add-mondaycom-in-the-azure-portal"></a>Hinzufügen von monday.com im Azure-Portal

Damit Sie monday.com in Azure AD integrieren können, müssen Sie monday.com zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Menü die Option **Azure Active Directory** aus.

    ![Die Option „Azure Active Directory“](common/select-azuread.png)

1. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie zum Hinzufügen einer Anwendung **Neue Anwendung** aus.

    ![Die Option „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie **monday.com** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **monday.com** und dann **Hinzufügen** aus.

    ![monday.com in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit monday.com mithilfe eines Testbenutzers namens **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in monday.com eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit monday.com müssen Sie die folgenden Schritte ausführen:

| Aufgabe | BESCHREIBUNG |
| --- | --- |
| **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** | Ermöglicht Ihren Benutzern die Verwendung dieses Features. |
| **[Konfigurieren des einmaligen Anmeldens für monday.com](#configure-mondaycom-single-sign-on)** | Konfiguriert die Einstellungen für einmaliges Anmelden in der Anwendung. |
| **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** | Testet das einmalige Anmelden von Azure AD für einen Benutzer namens Britta Simon. |
| **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** | Ermöglicht Britta Simon die Verwendung des einmaligen Anmeldens von Azure AD. |
| **[Erstellen eines monday.com-Testbenutzers](#create-a-mondaycom-test-user)** | Erstellt ein Pendant zu Britta Simon in monday.com, das mit ihrer Darstellung in Azure AD verknüpft ist. |
| **[Testen des einmaligen Anmelden](#test-single-sign-on)** | Überprüft, ob die Konfiguration funktioniert. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren Sie das einmalige Anmelden von Azure AD mit monday.com im Azure-Portal.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) im Anwendungsintegrationsbereich für **monday.com** die Option **Einmaliges Anmelden** aus.

    ![Option „Einmaliges Anmelden konfigurieren“](common/select-sso.png)

1. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML** oder **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** (Stiftsymbol) aus, um den Bereich **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Bereich **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine Metadatendatei des Dienstanbieters verfügen und den *IDP-initiierten Modus* konfigurieren möchten:

    1. Wählen Sie **Metadatendatei hochladen** aus.

       ![Option „Metadatendatei hochladen“](common/upload-metadata.png)

    1. Wählen Sie zum Auswählen der Metadatendatei das Ordnersymbol und dann **Hochladen** aus.

       ![Auswählen der Metadatendatei und der Schaltfläche „Hochladen“](common/browse-upload-metadata.png)

    1. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Bereich **Grundlegende SAML-Konfiguration** automatisch eingefügt:

       ![Die IDP-Werte im Bereich „Grundlegende SAML-Konfiguration“](common/idp-intiated.png)

       > [!Note]
       > Werden die Werte für **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt, geben Sie sie manuell ein.

1. So konfigurieren Sie die Anwendung im *SP-initiierten Modus*:

    1. Wählen Sie **Zusätzliche URLs festlegen** aus.
    
    1. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein: https:\//\<Ihre-Domäne>.monday.com. Die Anmelde-URL erhalten Sie vom [Supportteam für den monday.com-Client](mailto:support@monday.com).

        ![Option „Zusätzliche URLs festlegen“](common/metadata-upload-additional-signon.png)

1. Die monday.com-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Wählen Sie zum Verwalten dieser Attributwerte im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** aus, um den Bereich **Benutzerattribute** zu öffnen.

    ![Bereich „Benutzerattribute“](common/edit-attribute.png)

1. Wählen Sie unter **Benutzeransprüche** die Option **Bearbeiten** aus, um die Ansprüche zu bearbeiten. Wählen Sie zum Hinzufügen eines Anspruchs **Neuen Anspruch hinzufügen** aus, und konfigurieren Sie anschließend das SAML-Tokenattribut wie in der vorherigen Abbildung dargestellt. Führen Sie anschließend die folgenden Schritte aus: 

    1. Wählen Sie **Neuen Anspruch hinzufügen** aus.

        ![Option „Neuen Anspruch hinzufügen“ im Bereich „Benutzeransprüche“](common/new-save-attribute.png)

    1. Legen Sie im Bereich **Benutzeransprüche verwalten** die folgenden Werte fest:
        
       1. Geben Sie im Feld **Name** den für die Zeile mit dem Benutzeranspruch angezeigten Attributnamen ein.

       1. Lassen Sie **Namespace** leer.

       1. Wählen Sie für **Quelle** die Option **Attribut** aus.

       1. Wählen Sie in der Liste **Quellattribut** den Attributwert für die Zeile mit dem Benutzeranspruch aus.

       1. Wählen Sie **OK** und anschließend **Speichern** aus.

       ![Option „Benutzeransprüche verwalten“](common/new-attribute-details.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** unter **SAML-Signaturzertifikat** die Option **Herunterladen** neben **Zertifikat (Base64)** aus. Wählen Sie eine Option zum Herunterladen entsprechend Ihren Anforderungen aus. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Option zum Herunterladen des Zertifikats (Base64)](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **monday.com einrichten** die folgenden URLs entsprechend Ihren Anforderungen:

    * Anmelde-URL
    * Azure AD-Bezeichner
    * Abmelde-URL

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-mondaycom-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für monday.com

Zum Konfigurieren des einmaligen Anmeldens aufseiten von monday.com senden Sie das heruntergeladene Zertifikat (Base64) und die kopierten URLs aus dem Azure-Portal an das [Supportteam von monday.com](mailto:support@monday.com). Anhand der von Ihnen gesendeten Informationen stellt das Supportteam von monday.com sicher, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet ist.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

    ![Die Optionen „Benutzer“ und „Alle Benutzer“](common/users.png)

1. Wählen Sie **Neuer Benutzer** aus.

    ![Die Option „Neuer Benutzer“](common/new-user.png)

1. Führen Sie im Bereich **Benutzer** die folgenden Schritte aus:

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie im Feld **Benutzername** die Zeichenfolge **brittasimon\@\<IhreUnternehmensdomäne>.\<Erweiterung** ein. Beispiel: **brittasimon\@contoso.com**

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**. Notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    1. Klicken Sie auf **Erstellen**.

    ![Der Bereich „Benutzer“](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf monday.com, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **monday.com** aus.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **monday.com**aus.

    ![monday.com in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie im Menü die Option **Benutzer und Gruppen** aus.

    ![Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Bereich **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus. Wählen Sie **Auswählen**.

1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Bereich **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie **Auswählen**.

1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-mondaycom-test-user"></a>Erstellen eines monday.com-Testbenutzers

In diesem Abschnitt wird in monday.com ein Benutzer mit dem Namen Britta Simon erstellt. monday.com unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in monday.com vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Portals „Meine Apps“.

Nachdem Sie einmaliges Anmelden eingerichtet haben, werden Sie bei Auswahl von **monday.com** im Portal „Meine Apps“ automatisch bei monday.com angemeldet. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in diesen Artikeln:

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
