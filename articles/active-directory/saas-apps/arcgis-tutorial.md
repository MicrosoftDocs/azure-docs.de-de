---
title: 'Tutorial: Azure Active Directory-Integration von ArcGIS Online | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ArcGIS Online konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037e01120a695b1bacf2b1d4bc47e9ca9ed3fbd2
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65900368"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Tutorial: Azure Active Directory-Integration von ArcGIS Online

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory (Azure AD) in ArcGIS Online integrieren.
Die Integration von Azure AD in ArcGIS Online bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer auf ArcGIS Online Zugriff hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei ArcGIS Online anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Integration von Azure AD in ArcGIS Online konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* ArcGIS Online-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ArcGIS Online unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-arcgis-online-from-the-gallery"></a>Hinzufügen von ArcGIS Online aus dem Katalog

Zum Konfigurieren der Integration von Azure AD in ArcGIS Online müssen Sie Ihrer Liste der verwalteten SaaS-Apps ArcGIS Online aus dem Katalog hinzufügen.

**Um ArcGIS Online aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte durch:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **ArcGIS Online** ein, wählen Sie im Ergebnisbereich **ArcGIS Online** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![ArcGIS Online in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei ArcGIS Online mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ArcGIS Online eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD in ArcGIS Online müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für ArcGIS Online](#configure-arcgis-online-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines ArcGIS Online-Testbenutzers](#create-arcgis-online-test-user)**, um eine Entsprechung von Britta Simon in ArcGIS Online zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in ArcGIS Online die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **ArcGIS Online** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und den URLs für ArcGIS Online](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.maps.arcgis.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `<companyname>.maps.arcgis.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für den ArcGIS Online-Client](https://support.esri.com/en/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Wenn Sie die Konfiguration in **ArcGIS Online** automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **ArcGIS Online einrichten**, um zur Anwendung ArcGIS Online weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei ArcGIS Online anzumelden. Die Browsererweiterung führt die Konfiguration der Anwendung automatisch für Sie durch und automatisiert die Schritte im Abschnitt **Konfigurieren des einmaligen Anmeldens für ArcGIS Online**.

### <a name="configure-arcgis-online-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für ArcGIS Online

1. Wenn Sie ArcGIS Online manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der ArcGIS-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

2. Klicken Sie auf **EINSTELLUNGEN BEARBEITEN**.

    ![Einstellungen bearbeiten](./media/arcgis-tutorial/ic784742.png "Einstellungen bearbeiten")

3. Klicken Sie auf **Sicherheit**.

    ![Sicherheit](./media/arcgis-tutorial/ic784743.png "Sicherheit")

4. Klicken Sie unter **Unternehmensanmeldungen** auf **IDENTITÄTSANBIETER FESTLEGEN**.

    ![Unternehmensanmeldungen](./media/arcgis-tutorial/ic784744.png "Unternehmensanmeldungen")

5. Führen Sie auf der Konfigurationsseite **Set Identity Provider** die folgenden Schritte aus.

    ![Identitätsanbieter festlegen](./media/arcgis-tutorial/ic784745.png "Identitätsanbieter festlegen")

    a. Geben Sie in das Textfeld **Name** den Namen Ihrer Organisation ein.

    b. Wählen Sie für **Metadaten für Unternehmensidentitätsanbieter werden bereitgestellt durch** die Option **eine Datei** aus.

    c. Klicken Sie auf **Datei auswählen**, um die heruntergeladene Metadatendatei hochzuladen.

    d. Klicken Sie auf **IDENTITÄTSANBIETER FESTLEGEN**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ArcGIS Online gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **ArcGIS Online**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **ArcGIS Online** ein, und wählen Sie den Eintrag aus.

    ![ArcGIS Online-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-arcgis-online-test-user"></a>Erstellen eines ArcGIS Online-Testbenutzers

Damit sich Azure AD-Benutzer bei ArcGIS Online anmelden können, müssen sie in ArcGIS Online bereitgestellt werden.  
Im Fall von ArcGIS Online ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem **ArcGIS** -Mandanten an.

2. Klicken Sie auf **MITGLIEDER EINLADEN**.
   
    ![Mitglieder einladen](./media/arcgis-tutorial/ic784747.png "Mitglieder einladen")

3. Wählen Sie **Mitglieder automatisch hinzufügen, ohne eine E-Mail zu senden** aus, und klicken Sie dann auf **WEITER**.
   
    ![Mitglieder automatisch hinzufügen](./media/arcgis-tutorial/ic784748.png "Mitglieder automatisch hinzufügen")

4. Führen Sie auf der Dialogfeldseite **Members** die folgenden Schritte aus:
   
     ![Hinzufügen und überprüfen Sie](./media/arcgis-tutorial/ic784749.png "hinzufügen und überprüfen")
    
     a. Geben Sie **E-Mail-Adresse**, **Vorname** und **Nachname** eines gültigen AAD-Kontos ein, das Sie bereitstellen möchten.
  
     b. Klicken Sie auf **HINZUFÜGEN UND ÜBERPRÜFEN**.
5. Überprüfen Sie die eingegebenen Daten, und klicken Sie dann auf **MITGLIEDER HINZUFÜGEN**.
   
    ![Mitglied hinzufügen](./media/arcgis-tutorial/ic784750.png "Mitglied hinzufügen")
        
    > [!NOTE]
    > Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail und folgt einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „ArcGIS Online“ klicken, sollten Sie automatisch bei Ihrer ArcGIS Online-Anwendung angemeldet werden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

