---
title: 'Tutorial: Azure Active Directory-Integration mit SAP Business Object Cloud | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Business Object Cloud konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c64fb2dc4c494252f0078b8d23e05b15f07ebe00
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65903734"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Tutorial: Azure Active Directory-Integration mit SAP Business Object Cloud

In diesem Tutorial erfahren Sie, wie Sie SAP Business Object Cloud in Azure Active Directory (Azure AD) integrieren.
Die Integration von SAP Business Object Cloud in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD den Zugriff auf SAP Business Object Cloud steuern.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei SAP Business Object Cloud angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SAP Business Object Cloud konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* SAP Business Object Cloud-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Business Object Cloud unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Hinzufügen von SAP Business Object Cloud aus dem Katalog

Zum Konfigurieren der Integration von SAP Business Object Cloud in Azure AD müssen Sie SAP Business Object Cloud aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um SAP Business Object Cloud aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SAP Business Object Cloud** ein, wählen Sie im Ergebnisbereich **SAP Business Object Cloud** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![SAP Business Object Cloud in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei SAP Business Object Cloud basierend auf einer Testbenutzerin mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Business Object Cloud eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei SAP Business Object Cloud müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SAP Business Object Cloud](#configure-sap-business-object-cloud-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines SAP Business Object Cloud-Testbenutzers](#create-sap-business-object-cloud-test-user)**, um eine Entsprechung von Britta Simon in SAP Business Object Cloud zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in SAP Business Object Cloud die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SAP Business Object Cloud** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für SAP Business Object Cloud](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein:
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Die Werte in diesen URLs dienen nur Demonstrationszwecken. Aktualisieren Sie die Werte mit der tatsächlichen Anmelde-URL und der Bezeichner-URL. Um die Anmelde-URL zu erhalten, wenden Sie sich an das [SAP Business Object Cloud-Clientsupportteam](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Sie können die Bezeichner-URL abrufen, indem Sie die SAP Business Object Cloud-Metadaten aus der Verwaltungskonsole herunterladen. Dies wird weiter unten im Tutorial erläutert.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SAP Business Object Cloud

1. Melden Sie sich in einem anderen Webbrowserfenster bei der SAP Business Object Cloud-Unternehmenswebsite als Administrator an.

2. Wählen Sie **Menü** > **System** > **Administration**.
    
    ![„Menü“, „System“ und dann „Administration“ wählen](./media/sapboc-tutorial/config1.png)

3. Wählen Sie auf der Registerkarte **Security** das Symbol **Bearbeiten** (Stift) aus.
    
    ![Auf der Registerkarte „Security“ das Symbol „Bearbeiten“ auswählen](./media/sapboc-tutorial/config2.png)  

4. Wählen Sie als **Authentication Method** die Option **SAML Single Sign-On (SSO)** aus.

    ![„SAML Single Sign-On (SSO)“ als Authentifizierungsmethode auswählen](./media/sapboc-tutorial/config3.png)  

5. Um die Metadaten des Dienstanbieters herunterzuladen, (Schritt 1), wählen Sie **Download**. Suchen Sie in der Metadatendatei den Wert **entityID**, und kopieren Sie ihn. Fügen Sie im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** den Wert in das Feld **Bezeichner** ein.

    ![Wert „entityID“ kopieren und einfügen](./media/sapboc-tutorial/config4.png)  

6. Um die Metadaten des Dienstanbieters (Schritt 2) in der Datei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben, wählen Sie unter **Upload Identity Provider metadata** die Option **Upload**.  

    ![Unter „Upload Identity Provider metadata“ die Option „Upload“ wählen](./media/sapboc-tutorial/config5.png)

7. Wählen Sie in der Liste **User Attribute** das Benutzerattribut aus (Schritt 3), das Sie für Ihre Implementierung verwenden möchten. Dieses Benutzerattribut wird dem Identitätsanbieter zugeordnet. Um ein benutzerdefiniertes Attribut auf der Seite des Benutzers einzugeben, verwenden Sie die Option **Custom SAML Mapping**. Alternativ können Sie entweder **Email** oder **USER ID** als Benutzerattribut wählen. In unserem Beispiel haben wir **Email** ausgewählt, weil wir den Benutzer-ID-Anspruch mit dem Attribut **userprincipalname** im Abschnitt **Benutzerattribute und Ansprüche** im Azure-Portal zugeordnet haben. Hiermit wird eine individuelle Benutzer-E-Mail bereitgestellt, die in jeder erfolgreichen SAML-Antwort an die SAP Business Object Cloud-Anwendung gesendet wird.

    ![„Benutzerattribut“ auswählen](./media/sapboc-tutorial/config6.png)

8. Um das Konto mit dem Identitätsanbieter zu überprüfen (Schritt 4), geben Sie im Feld **Login Credential (Email)** die E-Mail-Adresse des Benutzers ein. Wählen Sie dann **Verify Account**. Das System fügt dem Benutzerkonto Anmeldeinformationen hinzu.

    ![E-Mail-Adresse eingeben und „Konto überprüfen“ wählen](./media/sapboc-tutorial/config7.png)

9. Wählen Sie das Symbol **Speichern** aus.

    ![Symbol „Speichern“](./media/sapboc-tutorial/save.png)

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

In diesem Abschnitt ermöglichen Sie Britta Simon das einmalige Anmelden bei Azure, indem Sie Zugriff auf SAP Business Object Cloud gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **SAP Business Object Cloud**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **SAP Business Object Cloud** aus.

    ![SAP Business Object Cloud-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-sap-business-object-cloud-test-user"></a>Erstellen eines SAP Business Object Cloud-Testbenutzers

Damit sich Azure AD-Benutzer bei SAP Business Object Cloud anmelden können, müssen sie in SAP Business Object Cloud bereitgestellt werden. In SAP Business Object Cloud wird die Bereitstellung manuell ausgeführt.

So stellen Sie ein Benutzerkonto bereit:

1. Melden Sie sich bei der SAP Business Object Cloud-Unternehmenswebsite als Administrator an.

2. Wählen Sie **Menü** > **Security** > **Users**.

    ![Mitarbeiter hinzufügen](./media/sapboc-tutorial/user1.png)

3. Um neue Benutzerdetails hinzuzufügen, wählen Sie auf der Seite **Users** die Option **+**. 

    ![Seite „Add Users“](./media/sapboc-tutorial/user4.png)

    Führen Sie anschließend die folgenden Schritte aus:

    a. Geben Sie im Textfeld **USER ID** die Benutzer-ID des Benutzers ein, z.B. **Britta**.

    b. Geben Sie im Textfeld **FIRST NAME** den Vornamen des Benutzers ein (z.B. **Britta**).

    c. Geben Sie im Textfeld **LAST NAME** den Nachnamen des Benutzers ein (z.B. **Simon**).

    d. Geben Sie im Textfeld **DISPLAY NAME** den vollständigen Namen des Benutzers ein (z.B. **Britta Simon**).

    e. Geben Sie im Feld **E-MAIL** die E-Mail-Adresse des Benutzers ein, z. B. **brittasimon\@contoso.com**.

    f. Wählen Sie auf der Seite **Select Roles** die passende Rolle für den Benutzer, und wählen Sie dann **OK**.

      ![Rolle wählen](./media/sapboc-tutorial/user3.png)

    g. Wählen Sie das Symbol **Speichern** aus.    

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SAP Business Object Cloud“ klicken, sollten Sie automatisch bei Ihrer SAP Business Object Cloud-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

