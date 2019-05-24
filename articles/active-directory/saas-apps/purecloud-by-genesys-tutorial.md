---
title: 'Tutorial: Azure Active Directory-Integration mit PureCloud by Genesys | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und PureCloud by Genesys konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 764ad8f1ca19238e1986d1d187d19c405963a832
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65868827"
---
# <a name="tutorial-azure-active-directory-integration-with-purecloud-by-genesys"></a>Tutorial: Azure Active Directory-Integration mit PureCloud by Genesys

In diesem Tutorial erfahren Sie, wie Sie PureCloud by Genesys in Azure Active Directory (Azure AD) integrieren.
Die Integration von PureCloud by Genesys in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer auf PureCloud by Genesys Zugriff hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei PureCloud by Genesys anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für das Konfigurieren der Azure AD-Integration mit PureCloud by Genesys benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* PureCloud by Genesys-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* PureCloud by Genesys unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Hinzufügen von PureCloud by Genesys aus dem Katalog

Zum Konfigurieren der Integration von PureCloud by Genesys in Azure AD müssen Sie PureCloud by Genesys aus dem Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um PureCloud by Genesys aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **PureCloud by Genesys** ein, wählen Sie im Ergebnisbereich **PureCloud by Genesys** aus, und klicken Sie dann auf **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![PureCloud by Genesys in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit PureCloud by Genesys basierend auf einer Testbenutzerin mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in PureCloud by Genesys eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit PureCloud by Genesys zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für PureCloud by Genesys](#configure-purecloud-by-genesys-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines PureCloud by Genesys-Testbenutzers](#create-purecloud-by-genesys-test-user)**, um eine Entsprechung für Britta Simon in PureCloud by Genesys zu erhalten, die mit ihrer Azure AD-Darstellung verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei PureCloud by Genesys die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **PureCloud by Genesys** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für PureCloud by Genesys](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL gemäß Ihrer Region ein:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Geben Sie im Textfeld **Antwort-URL** eine URL gemäß Ihrer Region ein:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml` |

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für PureCloud by Genesys](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL gemäß Ihrer Region ein:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

6. Die PureCloud by Genesys-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

7. Darüber hinaus wird von der PureCloud by Genesys-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:

    | NAME | Quellattribut|
    | ---------------| --------------- |
    | E-Mail | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

9. Kopieren Sie im Abschnitt **PureCloud by Genesys einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-purecloud-by-genesys-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für PureCloud by Genesys

1. Melden Sie sich in einem anderen Webbrowserfenster bei PureCloud by Genesys als Administrator an.

2. Klicken Sie oben auf **Admin**, und navigieren Sie unter **Integrationen** zu **Einmaliges Anmelden**.

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure01.png)

3. Wechseln Sie zur Registerkarte **ADFS/Azure AD (Premium)**, und führen Sie die folgenden Schritte aus:

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Klicken Sie auf **Durchsuchen**, um das Base64-codierte Zertifikat, das Sie über das Azure-Portal heruntergeladen haben, unter **ADFS Certificate** (ADFS-Zertifikat) hochzuladen.

    b. Fügen Sie in das Textfeld **ADFS Issuer URI** (ADFS-Aussteller-URI) den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Target URI** (Ziel-URI) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Für den Wert **Bezeichner der vertrauenden Seite** müssen Sie zum Azure-Portal navigieren und auf der Anwendungsintegrationsseite **PureCloud by Genesys** auf die Registerkarte **Eigenschaften** klicken und den Wert **Application ID** (Anwendungs-ID) kopieren. Fügen Sie ihn in das Textfeld **Bezeichner der vertrauenden Seite** ein. 

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Klicken Sie unten auf der Seite auf **Speichern**.   

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

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon das einmalige Anmelden bei Azure, indem Sie ihr Zugriff auf PureCloud by Genesys gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **PureCloud by Genesys**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **PureCloud by Genesys** aus.

    ![PureCloud by Genesys-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-purecloud-by-genesys-test-user"></a>Erstellen eines PureCloud by Genesys-Testbenutzers

Damit sich Azure AD-Benutzer an PureCloud by Genesys anmelden können, müssen sie in PureCloud by Genesys bereitgestellt werden. In PureCloud by Genesys ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich als Administrator bei PureCloud by Genesys an.

2. Klicken Sie oben auf **Admin**, und navigieren Sie unter **People & Permissions** (Personen und Berechtigungen) zu **People** (Personen).

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure03.png)

3. Klicken Sie auf der Seite „People“ (Personen) auf **Add Person** (Person hinzufügen).

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure04.png)

4. Führen Sie im Popupfenster **Add People to the Organization** (Personen der Organisation hinzufügen) die folgenden Schritte aus:

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Geben Sie im Textfeld **Vollständiger Name** den Namen des Benutzers ein, z. B. **Brittasimon**.

    b. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers ein, z.B. **brittasimon\@contoso.com**.
    
    c. Klicken Sie auf **Create**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „PureCloud by Genesys“ klicken, sollten Sie automatisch an der PureCloud by Genesys-Anmeldung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

