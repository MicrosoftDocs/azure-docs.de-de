---
title: 'Tutorial: Azure Active Directory-Integration mit TOPdesk - Secure | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und TOPdesk - Secure konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 1dd57dd31c0dcf42b7847dc8e3f3945961c3d37f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520390"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: Azure Active Directory-Integration mit TOPdesk - Secure

In diesem Tutorial erfahren Sie, wie Sie TOPdesk - Secure in Azure Active Directory (Azure AD) integrieren.
Die Integration von TOPdesk - Secure in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf TOPdesk - Secure hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei TOPdesk – Secure angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in TOPdesk - Secure konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* TOPdesk – Secure-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* TOPdesk – Secure unterstützt **SP** -initiiertes einmaliges Anmelden.

## <a name="adding-topdesk---secure-from-the-gallery"></a>Hinzufügen von TOPdesk - Secure aus dem Katalog

Zum Konfigurieren der Integration von TOPdesk - Secure in Azure AD müssen Sie TOPdesk - Secure aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um TOPdesk - Secure aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **TOPdesk - Secure** ein, wählen Sie im Ergebnisbereich **TOPdesk - Secure** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![TOPdesk - Secure in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei TOPdesk – Secure mithilfe einer Testbenutzerin namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in TOPdesk – Secure eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei TOPdesk - Secure müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für TOPdesk – Secure](#configure-topdesk---secure-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines TOPdesk – Secure-Testbenutzers](#create-topdesk---secure-test-user)** , um eine Entsprechung von Britta Simon in TOPdesk – Secure zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei TOPdesk – Secure die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **TOPdesk – Secure** die Option **Einmaliges Anmelden** .

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und den URLs für TOPdesk - Secure](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.topdesk.net`

    b. Geben Sie im Feld **Identifier URL** (Bezeichner-URL) die TOPdesk-Metadaten-URL ein, die Sie aus der TOPdesk-Konfiguration abrufen können. Verwenden Sie das folgende Format: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Wenden Sie sich an das [Supportteam für den TOPdesk - Secure-Client](https://www.topdesk.com/us/support/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **TOPdesk – Secure einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-topdesk---secure-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für TOPdesk – Secure

1. Melden Sie sich bei der **TOPdesk - Secure** -Unternehmenswebsite als Administrator an.

2. Klicken Sie im Menü **TOPdesk** auf **Einstellungen** .

    ![Einstellungen](./media/topdesk-secure-tutorial/ic790598.png "Einstellungen")

3. Klicken Sie auf **Anmeldeeinstellungen** .

    ![Login Settings (Anmeldeeinstellungen)](./media/topdesk-secure-tutorial/ic790599.png "Anmeldeeinstellungen")

4. Erweitern Sie das Menü **Anmeldeeinstellungen** , und klicken Sie dann auf **Allgemein** .

    ![Allgemein](./media/topdesk-secure-tutorial/ic790600.png "Allgemein")

5. Führen Sie im Abschnitt **Secure** des Konfigurationsabschnitts **SAML-Anmeldung** die folgenden Schritte aus:

    ![Technische Einstellungen](./media/topdesk-secure-tutorial/ic790855.png "Technische Einstellungen")

    a. Klicken Sie auf **Herunterladen** , um die  öffentliche Metadatendatei herunterzuladen und sie dann lokal auf Ihrem Computer zu speichern.

    b. Öffnen Sie die Metadatendatei, und suchen Sie den Knoten **AssertionConsumerService** .

    ![Assertion Consumer Service](./media/topdesk-secure-tutorial/ic790856.png "Assertion Consumer Service")

    c. Kopieren Sie den Wert **AssertionConsumerService** , und fügen Sie ihn im Abschnitt **Domäne und URLs für TOPdesk - Secure** in das Textfeld Antwort-URL ein.

6. Zum Erstellen einer Zertifikatsdatei führen Sie die folgenden Schritte durch:

    ![Certificate](./media/topdesk-secure-tutorial/ic790606.png "Zertifikat")

    a. Öffnen Sie die aus dem Azure-Portal heruntergeladene Metadatendatei.

    b. Erweitern Sie den Knoten **RoleDescriptor** , der über einen **xsi:type** von **fed:ApplicationServiceType** verfügt.

    c. Kopieren Sie den Wert des Knotens **X509Certificate** .

    d. Speichern Sie den kopierten **X509Certificate** -Wert lokal in einer Datei auf Ihrem Computer.

7. Klicken Sie im Abschnitt **Öffentlich** auf **Hinzufügen** .

    ![Add (Hinzufügen)](./media/topdesk-secure-tutorial/ic790607.png "Hinzufügen")

8. Führen Sie auf der Seite **SAML Configuration Assistant** die folgenden Schritte aus:

    ![SAML Configuration Assistant (SAML-Konfigurationsassistent)](./media/topdesk-secure-tutorial/ic790608.png "SAML Configuration Assistant")

    a. Klicken Sie zum Hochladen der aus dem Azure-Portal heruntergeladenen Metadatendatei unter **Verbundmetadaten** auf **Durchsuchen** .

    b. Klicken Sie zum Hochladen Ihrer Zertifikatsdatei unter **Zertifikat (RSA)** auf **Durchsuchen** .

    c. Für **Privater Schlüssel (RSA, PKCS8, DER)** können Sie Ihren eigenen privaten Schlüssel hochladen oder sich an das [Supportteam für den TOPdesk - Secure-Client](https://www.topdesk.com/us/support) wenden, um den privaten Schlüssel zu erhalten.

    d. Klicken Sie zum Hochladen der Logodatei, die Sie vom TOPdesk-Supportteam erhalten haben, unter **Logosymbol** auf **Durchsuchen** .

    e. Geben Sie **in das Textfeld** Benutzernamenattribut`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein.

    f. Geben Sie in das Textfeld **Anzeigenname** einen Namen für die Konfiguration ein.

    g. Klicken Sie auf **Speichern** .

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung** .  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf TOPdesk - Secure erteilen.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **TOPdesk – Secure** .

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste den Eintrag **TOPdesk – Secure** ein, und wählen Sie den Eintrag aus.

    ![TOPdesk - Secure-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-topdesk---secure-test-user"></a>Erstellen eines TOPdesk – Secure-Testbenutzers

Damit sich Azure AD-Benutzer bei TOPdesk - Secure anmelden können, müssen sie in TOPdesk - Secure bereitgestellt werden.  
Im Fall von TOPdesk - Secure ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>So konfigurieren Sie die Benutzerbereitstellung

1. Melden Sie sich bei Ihrer **TOPdesk - Secure** -Unternehmenswebsite als Administrator an.

2. Klicken Sie im Menü oben auf **TOPdesk \> Neu \> Support-Dateien \> Operator** .

    ![Operator](./media/topdesk-secure-tutorial/ic790610.png "Operator")

3. Führen Sie im Dialogfeld **Neuer Benutzer** die folgenden Schritte aus:

    ![New Operator (Neuer Benutzer)](./media/topdesk-secure-tutorial/ic790611.png "Neuer Benutzer")

    a. Klicken Sie auf die Registerkarte **Allgemein** .

    b. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein, z.B. **Simon** .

    c. Wählen Sie im Abschnitt **Speicherort** eine **Website** für das Konto aus.

    d. Geben Sie in das Textfeld **Anmeldename** des Abschnitts **TOPdesk-Anmeldung** einen Anmeldenamen für den Benutzer ein.

    e. Klicken Sie auf **Speichern** .

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von TOPdesk – Secure-Benutzerkonten oder mit den APIs von TOPdesk – Secure bereitstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „TOPdesk – Secure“ klicken, sollten Sie automatisch bei Ihrer TOPdesk – Secure-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)