---
title: 'Tutorial: Azure Active Directory-Integration mit Zoom | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zoom konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 641fe5439e320208d41969b9563293257648d488
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65895865"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Tutorial: Azure Active Directory-Integration mit Zoom

In diesem Tutorial erfahren Sie, wie Sie Zoom in Azure Active Directory (Azure AD) integrieren.
Die Integration von Zoom in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Zoom hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Zoom anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Zoom konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Zoom-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zoom unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-zoom-from-the-gallery"></a>Hinzufügen von Zoom aus dem Katalog

Zum Konfigurieren der Integration von Zoom in Azure AD müssen Sie Zoom aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Zoom aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Zoom** ein, wählen Sie im Ergebnisbereich **Zoom** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Zoom in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Zoom basierend auf einem Testbenutzer mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zoom eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Zoom müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Zoom](#configure-zoom-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Zoom-Testbenutzers](#create-zoom-test-user)**, um eine Entsprechung von Britta Simon in Zoom zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Zoom die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Zoom** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Zoom](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.zoom.us`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `<companyname>.zoom.us`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von Zoom](https://support.zoom.us/hc/en-us), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die Zoom-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

6. Darüber hinaus wird von der Zoom-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:
    
    | NAME | Namespace  |  Quellattribut|
    | ---------------| --------------- | --------- |
    | E-Mail-Adresse  | user.mail  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail` |
    | Vorname  | user.givenname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | Nachname  | user.surname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | Telefonnummer  | user.telephonenumber  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone` |
    | Department  | user.department  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department` |
    | role |    user.assignedrole |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role` |

    > [!NOTE]
    > Klicken Sie [hier](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), um herauszufinden, wie Sie die Rolle in Azure AD konfigurieren.

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Wählen Sie „Source“ als **Attribut** aus.

    d. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    e. Klicken Sie auf **OK**.

    f. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Zoom erwartet in der SAML-Nutzlast möglicherweise einen Gruppenanspruch. Sollten Sie also eine Gruppe erstellt haben, geben Sie die Gruppeninformationen an das [Clientsupportteam von Zoom](https://support.zoom.us/hc/en-us) weiter, damit das Team diese Gruppeninformationen bei sich konfigurieren kann. Geben Sie außerdem die Objekt-ID an das [Clientsupportteam von Zoom](https://support.zoom.us/hc/en-us) weiter, damit es diese bei sich konfigurieren kann. Gehen Sie zum Abrufen der Objekt-ID gemäß der [Dokumentation](https://support.zoom.us/hc/en-us/articles/115005887566) vor.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

8. Kopieren Sie im Abschnitt **Zoom einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-zoom-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Zoom

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Zoom-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf die Registerkarte **Einmaliges Anmelden** .

    ![Registerkarte für einmaliges Anmelden](./media/zoom-tutorial/ic784700.png "Einmaliges Anmelden")

3. Klicken Sie auf die Registerkarte **Sicherheitskontrollen**, und navigieren Sie dann zu den Einstellungen für **Einmaliges Anmelden**.

4. Führen Sie im Abschnitt „Einmaliges Anmelden“ die folgenden Schritte aus:

    ![Abschnitt für einmaliges Anmelden](./media/zoom-tutorial/ic784701.png "Einmaliges Anmelden")

    a. Fügen Sie in das Textfeld **Anmelde-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Für die **URL der Abmeldeseite** müssen Sie im Azure-Portal auf der linken Seite auf **Azure Active Directory** klicken und anschließend zu **App-Registrierungen** navigieren.

    ![Schaltfläche „Azure Active Directory“](./media/zoom-tutorial/appreg.png)

    c. Klicken Sie auf **Endpunkte**.

    ![Endpunkt-Schaltfläche](./media/zoom-tutorial/endpoint.png)

    d. Kopieren Sie den **SAML-P-ABMELDUNGSENDPUNKT**, und fügen Sie ihn in das Textfeld **URL der Abmeldeseite** ein.

    ![Schaltfläche zum Kopieren des Endpunkts](./media/zoom-tutorial/endpoint1.png)

    e. Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat des Identitätsanbieters** ein.

    f. Fügen Sie im Textfeld **Aussteller** den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben. 

    g. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Weitere Informationen finden Sie in der Dokumentation zum Zoomen [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566).

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zoom gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Zoom**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **Zoom** ein, und wählen Sie den Eintrag aus.

    ![Zoom-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-zoom-test-user"></a>Erstellen eines Zoom-Testbenutzers

Damit sich Azure AD-Benutzer bei Zoom anmelden können, müssen sie in Zoom bereitgestellt werden. Im Fall von Zoom ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:

1. Melden Sie sich bei der **Zoom** -Unternehmenswebsite als Administrator an.

2. Klicken Sie auf die Registerkarte **Kontenverwaltung** und anschließend auf **Benutzerverwaltung**.

3. Klicken Sie im Abschnitt „Benutzerverwaltung“ auf **Benutzer hinzufügen**.

    ![Benutzerverwaltung](./media/zoom-tutorial/ic784703.png "Benutzerverwaltung")

4. Führen Sie auf der Seite **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/zoom-tutorial/ic784704.png "Benutzer hinzufügen")

    a. Wählen Sie als **Benutzertyp** die Option **Basic** aus.

    b. Geben Sie im Textfeld **Emails** die E-Mail-Adresse eines gültigen Azure AD-Benutzerkontos ein, das Sie bereitstellen möchten.

    c. Klicken Sie auf **Hinzufügen**.

> [!NOTE]
> Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Zoom-Benutzerkonten oder mithilfe der von Zoom bereitgestellten APIs erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Zoom“ klicken, sollten Sie automatisch bei Ihrer Zoom-Anwendung angemeldet werden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
