---
title: 'Tutorial: Azure Active Directory-Integration mit TINFOIL SECURITY | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und TINFOIL SECURITY konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 135b5719422d1b28a82ac2eda06f76d6dd746800
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65813732"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutorial: Azure Active Directory-Integration in TINFOIL SECURITY

In diesem Tutorial erfahren Sie, wie Sie TINFOIL SECURITY in Azure Active Directory (Azure AD) integrieren.
Die Integration von TINFOIL SECURITY in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf TINFOIL SECURITY hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei TINFOIL SECURITY anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in TINFOIL SECURITY konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein TINFOIL SECURITY-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* TINFOIL SECURITY unterstützt **IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Hinzufügen von TINFOIL SECURITY aus dem Katalog

Zum Konfigurieren der Integration von TINFOIL SECURITY in Azure AD müssen Sie TINFOIL SECURITY aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um TINFOIL SECURITY aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **TINFOIL SECURITY** ein, wählen Sie im Ergebnisbereich **TINFOIL SECURITY** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![TINFOIL SECURITY in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit TINFOIL SECURITY mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in TINFOIL SECURITY eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei TINFOIL SECURITY müssen Sie folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für TINFOIL SECURITY](#configure-tinfoil-security-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines TINFOIL SECURITY-Testbenutzers](#create-tinfoil-security-test-user)**, um ein Pendant von Britta Simon in TINFOIL SECURITY zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit TINFOIL SECURITY die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **TINFOIL SECURITY** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

    ![SSO-Informationen zur Domäne und zu den URLs für TINFOIL SECURITY](common/preintegrated.png)

5. Die TINFOIL SECURITY-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

        ![image](common/edit-attribute.png)

6. Darüber hinaus wird von der TINFOIL SECURITY-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:

    | NAME | Quellattribut |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Fügen Sie in das Textfeld **Quellattribut** den Konto-ID-Wert ein, den Sie später im Tutorial abrufen.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

7. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![Bearbeiten des SAML-Signaturzertifikats](common/edit-certificate.png)

8. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** den **Fingerabdruck**, und speichern Sie ihn auf Ihrem Computer.

    ![Kopieren des Fingerabdruckwerts](common/copy-thumbprint.png)

9. Kopieren Sie im Abschnitt **TINFOIL SECURITY einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-tinfoil-security-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für TINFOIL SECURITY

1. Melden Sie sich in einem anderen Webbrowserfenster bei der TINFOIL SECURITY-Unternehmenswebsite als Administrator an.

2. Klicken Sie oben auf der Symbolleiste auf **Einstellungen**.
   
    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "Dashboard")

3. Klicken Sie auf **Sicherheit**.
   
    ![Sicherheit](./media/tinfoil-security-tutorial/ic798972.png "Sicherheit")

4. Führen Sie auf der Konfigurationsseite **Single Sign-On** die folgenden Schritte aus:
   
    ![Einmaliges Anmelden](./media/tinfoil-security-tutorial/ic798973.png "des einmaligen Anmeldens")
   
    a. Wählen Sie **SAML aktivieren**.
   
    b. Klicken Sie auf **Manuelle Konfiguration**.
   
    c. Fügen Sie in das Textfeld **SAML Post URL** (SAML POST-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
   
    d. Fügen Sie im Textfeld **SAML Certificate Fingerprint** (SAML-Zertifikatsfingerabdruck) den Wert von **Fingerabdruck** ein, den Sie aus dem Abschnitt **SAML-Signaturzertifikat** kopiert haben.
  
    e. Kopieren Sie den Wert Ihrer **Konto-ID**, und fügen Sie ihn im Azure-Portal im Abschnitt **Attribut hinzufügen** in das Textfeld **Attributwert** ein.
   
    f. Klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen brittasimon@yourcompanydomain.extension ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf TINFOIL SECURITY gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **TINFOIL SECURITY** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **TINFOIL SECURITY** aus.

    ![TINFOIL SECURITY-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-tinfoil-security-test-user"></a>Erstellen eines TINFOIL SECURITY-Testbenutzers

Damit sich Azure AD-Benutzer bei TINFOIL SECURITY anmelden können, müssen sie in TINFOIL SECURITY bereitgestellt werden. Im Fall von TINFOIL SECURITY ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzers die folgenden Schritte aus:**

1. Wenn der Benutzer einen Teil eines Enterprise-Kontos ist, müssen Sie sich [an das TINFOIL SECURITY-Supportteam wenden](https://www.tinfoilsecurity.com/contact), um das Benutzerkonto anlegen zu lassen.

1. Wenn der Benutzer ein normaler TINFOIL SECURITY-SaaS-Benutzer ist, kann der Benutzer zu allen Websites des Benutzers einen Mitarbeiter hinzufügen. Dies löst einen Prozess aus, bei dem eine Einladung an die angegebene E-Mail-Adresse gesendet wird, um ein neues TINFOIL SECURITY-Benutzerkonto zu erstellen.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von TINFOIL SECURITY-Benutzerkonten oder mithilfe der von TINFOIL SECURITY bereitgestellten APIs erstellen.
> 

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „TINFOIL SECURITY“ klicken, sollten Sie automatisch bei der TINFOIL SECURITY-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

