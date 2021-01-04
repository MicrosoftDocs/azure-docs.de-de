---
title: 'Tutorial: Azure Active Directory-Integration mit Zscaler Internet Access Administrator | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zscaler Internet Access Administrator konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: d74057e32b6f16bdb6dae3d96ac46c5cc93571aa
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609104"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Tutorial: Azure Active Directory-Integration mit Zscaler Internet Access Administrator

In diesem Tutorial erfahren Sie, wie Sie Zscaler Internet Access Administrator in Azure Active Directory (Azure AD) integrieren.
Die Integration von Zscaler Internet Access Administrator in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Zscaler Internet Access Administrator hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Zscaler Internet Access Administrator anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Zscaler Internet Access Administrator konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Zscaler Internet Access Administrator-Abonnement

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zscaler Internet Access Administrator unterstützt **IDP**-initiiertes SSO.

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Hinzufügen von Zscaler Internet Access Administrator aus dem Katalog

Zum Konfigurieren der Integration von Zscaler Internet Access Administrator in Azure AD müssen Sie Zscaler Internet Access Administrator aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Zscaler Internet Access Administrator aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Zscaler Internet Access Administrator** ein, wählen Sie im Ergebnisbereich **Zscaler Internet Access Administrator** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Zscaler Internet Access Administrator in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Zscaler Internet Access Administrator mithilfe einer Testbenutzerin namens **Britta Simon**.
Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zscaler Internet Access Administrator muss eine Linkbeziehung eingerichtet werden, damit das einmalige Anmelden funktioniert.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Zscaler Internet Access Administrator müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Zscaler Internet Access Administrator](#configure-zscaler-internet-access-administrator-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Testbenutzers für Zscaler Internet Access Administrator](#create-zscaler-internet-access-administrator-test-user)** , um eine Entsprechung von Britta Simon in Zscaler Internet Access Administrator zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Zscaler Internet Access Administrator die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Zscaler Internet Access Administrator** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![SSO-Informationen zur Domäne und zu den URLs für Zscaler Internet Access Administrator](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL gemäß Ihren Anforderungen ein:

    | Bezeichner |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Geben Sie im Textfeld **Antwort-URL** eine URL gemäß Ihren Anforderungen ein:

    | Antwort-URL |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Die Zscaler Internet Access Administrator-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute und Ansprüche** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute und Ansprüche** zu öffnen.

    ![Attributlink](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. Konfigurieren Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut wie in der obigen Abbildung gezeigt, und führen Sie die folgenden Schritte aus:

    | Name  | Quellattribut  |
    | ---------| ------------ |
    | Role | user.assignedroles |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Screenshot: Benutzeransprüche mit Option zum Hinzufügen eines neuen Anspruchs](./common/new-save-attribute.png)
    
    ![Screenshot des Dialogfelds „Benutzeransprüche verwalten“, in dem Sie die hier beschriebenen Werte eingeben können](./common/new-attribute-details.png)

    b. Wählen Sie in der Liste **Quellattribut** den Attributwert aus.

    c. Klicken Sie auf **OK**.

    d. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Klicken Sie [hier](../develop/active-directory-enterprise-app-role-management.md), um herauszufinden, wie Sie die Rolle in Azure AD konfigurieren.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

8. Kopieren Sie im Abschnitt für die **Einrichtung von Zscaler Internet Access Administrator** die URLs, die Ihre Anforderungen erfüllen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Zscaler Internet Access Administrator

1. Melden Sie sich in einem anderen Webbrowserfenster an Ihrer Zscaler Internet Access Administrator-Benutzeroberfläche an.

2. Navigieren Sie zu **Administration > Administrator Management** (Administration > Administratorverwaltung), und führen Sie die folgenden Schritte aus. Klicken Sie anschließend auf „Speichern“:

    ![Screenshot der Administratorverwaltung mit Optionen zum Aktivieren der SAML-Authentifizierung, Hochladen des SSL-Zertifikats und Angeben eines Ausstellers](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Verwaltung")

    a. Aktivieren Sie die Option **SAML-Authentifizierung aktivieren**.

    b. Klicken Sie auf **Upload** (Hochladen), um das Azure-SAML-Signaturzertifikat hochzuladen, das Sie aus dem Azure-Portal unter **Public SSL Certificate** (Öffentliches SSL-Zertifikat) heruntergeladen haben.

    c. Fügen Sie zur Erhöhung der Sicherheit optional die Details zum **Aussteller** hinzu, um den Aussteller der SAML-Antwort zu überprüfen.

3. Führen Sie auf der Administratorbenutzeroberfläche die folgenden Schritte aus:

    ![Screenshot der Administratorbenutzeroberfläche zum Ausführen der Schritte](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Bewegen Sie den Mauszeiger unten links auf das Menü **Aktivierung**.

    b. Klicken Sie auf **Aktivieren**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung**.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zscaler Internet Access Administrator gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** und **Zscaler Internet Access Administrator**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **Zscaler Internet Access Administrator** ein, und wählen Sie den entsprechenden Eintrag aus.

    ![Link für Zscaler Internet Access Administrator in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Erstellen des Zscaler Internet Access Administrator-Testbenutzers

Das Ziel dieses Abschnitts besteht darin, eine Benutzerin mit dem Namen Britta Simon in Zscaler Internet Access Administrator zu erstellen. Für Zscaler Internet Access wird die Just-In-Time-Bereitstellung für einmaliges Anmelden für Administratoren nicht unterstützt. Sie müssen manuell ein Administratorkonto erstellen.
Die Schritte zum Erstellen eines Administratorkontos finden Sie in der Zscaler-Dokumentation:

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel für Zscaler Internet Access Administrator klicken, sollten Sie automatisch an der Zscaler Internet Access Administrator-Benutzeroberfläche angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)
