---
title: 'Tutorial: Azure Active Directory-Integration mit SD Elements | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SD Elements konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: a18971125724e6e2fdc6b37de15894efb8b9071a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65867570"
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Tutorial: Azure Active Directory-Integration in SD Elements

In diesem Tutorial erfahren Sie, wie Sie SD Elements in Azure Active Directory (Azure AD) integrieren.
Die Integration von SD Elements in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD den Zugriff auf SD Elements steuern.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei SD Elements angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in SD Elements konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein SD Elements-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SD Elements unterstützt **IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-sd-elements-from-the-gallery"></a>Hinzufügen von SD Elements aus dem Katalog

Zum Konfigurieren der Integration von SD Elements in Azure AD müssen Sie SD Elements aus dem Katalog zu Ihrer Liste verwalteter SaaS-Apps hinzufügen.

**Führen Sie folgende Schritte aus, um SD Elements aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SD Elements** ein, wählen Sie im Ergebnisbereich **SD Elements** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![SD Elements in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei SD Elements mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SD Elements eingerichtet werden.

Um das einmalige Anmelden von Azure AD mit SD Elements konfigurieren und testen zu können, ist Folgendes erforderlich:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SD Elements](#configure-sd-elements-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines SD Elements-Testbenutzers](#create-sd-elements-test-user)**, um ein Pendant von Britta Simon in SD Elements zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in SD Elements folgende Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SD Elements** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für SD Elements](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Wenden Sie sich an das [Clientsupportteam von SD Elements](mailto:support@sdelements.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die SD Elements-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

6. Bearbeiten Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die Ansprüche mithilfe des Symbols zum **Bearbeiten**, oder fügen Sie die Ansprüche über **Neuen Anspruch hinzufügen** hinzu, um das SAML-Tokenattribut wie in der obigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus:

    | NAME |  Quellattribut|
    | --- | --- |
    | email |user.mail |
    | firstname |user.givenname |
    | lastname |user.surname |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

8. Kopieren Sie im Abschnitt **SD Elements einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-sd-elements-single-sign-on"></a>Einmaliges Anmelden für SD Elements konfigurieren

1. Wenden Sie sich zur Aktivierung des einmaligen Anmeldens mit der heruntergeladenen Zertifikatdatei an das [SD Elements-Supportteam](mailto:support@sdelements.com) .

1. Melden Sie sich in einem anderen Browserfenster bei Ihrem SD Elements-Mandanten als Administrator an.

1. Klicken Sie im oberen Menü auf **System** und anschließend auf **Single Sign-on**.

    ![Configure single sign-on](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. Führen Sie im Dialogfeld **Single Sign-On Settings** folgende Schritte aus:

    ![Configure single sign-on](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Wählen Sie für **SSO-Typ** die Option **SAML** aus.

    b. Fügen Sie in das Textfeld **Identity Provider Entity ID** (Entitäts-ID des Identitätsanbieters) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Identity Provider Single Sign-On Service** (Dienst für einmaliges Anmelden des Identitätsanbieters) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** **brittasimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SD Elements gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **SD Elements** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **SD Elements**aus.

    ![Der SD Elements-Link Sie in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-sd-elements-test-user"></a>Erstellen eines SD Elements-Testbenutzers

In diesem Abschnitt wird in SD Elements eine Benutzerin namens Britta Simon erstellt. Im Falle von in SD Elements müssen Benutzer manuell erstellt werden.

**Führen Sie die folgenden Schritte aus, um in SD Elements eine Benutzerin namens Britta Simon zu erstellen:**

1. Melden Sie sich in einem Webbrowserfenster bei der SD Elements-Unternehmenswebsite als Administrator an.

1. Klicken Sie im oberen Menü auf **Benutzerverwaltung** und dann auf **Benutzer**.

    ![Erstellen eines SD Elements-Testbenutzers](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Klicken Sie auf **Add New User**.

    ![Erstellen eines SD Elements-Testbenutzers](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. Führen Sie im Dialogfeld **Add New User** (Neuen Benutzer hinzufügen) folgende Schritte aus:

    ![Erstellen eines SD Elements-Testbenutzers](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers ein, z.B. **brittasimon@contoso.com**.

    b. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers ein (beispielsweise **Britta**).

    c. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein (beispielsweise **Simon**).

    d. Wählen Sie für **Rolle** die Option **Benutzer** aus.

    e. Klicken Sie auf **Benutzer erstellen**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SD Elements“ klicken, sollten Sie automatisch bei Ihrer SD Elements-Anwendung angemeldet werden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)