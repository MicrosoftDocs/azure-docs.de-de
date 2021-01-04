---
title: 'Tutorial: Azure Active Directory-Integration mit SAP Business ByDesign | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Business ByDesign konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 6d04601cb11e57f50c82680077793aef1b816bfa
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895125"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Azure Active Directory-Integration mit SAP Business ByDesign

In diesem Tutorial erfahren Sie, wie Sie SAP Business ByDesign in Azure Active Directory (Azure AD) integrieren.
Die Integration von SAP Business ByDesign bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf SAP Business ByDesign hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei SAP Business ByDesign anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SAP Business ByDesign konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SAP Business ByDesign-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Business ByDesign unterstützt **SP** -initiiertes einmaliges Anmelden.

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Hinzufügen von SAP Business ByDesign aus dem Katalog

Zum Konfigurieren der Integration von SAP Business ByDesign in Azure AD müssen Sie SAP Business ByDesign aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um SAP Business ByDesign aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SAP Business ByDesign** ein, wählen Sie im Ergebnisbereich **SAP Business ByDesign** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![SAP Business ByDesign in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei SAP Business ByDesign mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Business ByDesign eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei SAP Business ByDesign müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SAP Business ByDesign](#configure-sap-business-bydesign-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines SAP Business ByDesign-Testbenutzers](#create-sap-business-bydesign-test-user)** , um eine Entsprechung von Britta Simon in SAP Business ByDesign zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD bei SAP Business ByDesign zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SAP Business ByDesign** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für SAP Business ByDesign](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<servername>.sapbydesign.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<servername>.sapbydesign.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die SAP Business ByDesign-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten** , um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Screenshot des Dialogfelds „Benutzerattribute“, in dem die Schaltfläche „Bearbeiten“ ausgewählt ist](common/edit-attribute.png)

6. Klicken Sie auf das Symbol **Bearbeiten** , um den **Wert für Namensbezeichner** zu bearbeiten.

    ![Screenshot der Seite „Benutzerattribute und Ansprüche“ mit ausgewähltem Symbol „Bearbeiten“](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Führen Sie im Abschnitt **Benutzeransprüche verwalten** die folgenden Schritte aus: ![image](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Wählen Sie als **Quelle** die Option **Transformation** aus.

    b. Wählen Sie in der Dropdownliste **Transformation** den Eintrag **ExtractMailPrefix()** aus.

    c. Wählen Sie in der Dropdownliste **Parameter 1** das Benutzerattribut aus, das Sie für Ihre Implementierung verwenden möchten. Wenn Sie z.B. den Wert „EmployeeID“ als eindeutige Benutzer-ID verwenden möchten und den Attributwert in „ExtensionAttribute2“ gespeichert haben, wählen Sie „user.extensionattribute2“ aus.

    d. Klicken Sie auf **Speichern**.

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

9. Kopieren Sie im Abschnitt **SAP Business ByDesign einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SAP Business ByDesign

1. Melden Sie sich beim SAP Business ByDesign-Portal mit Administratorrechten an.

2. Navigieren Sie zum **allgemeinen Task für die Anwendungs- und Benutzerverwaltung** , und klicken Sie auf die Registerkarte **Identitätsanbieter**.

3. Klicken Sie auf **New Identity Provider** (Neuer Identitätsanbieter), und wählen Sie die XML-Metadatendatei aus, die Sie aus dem Azure-Portal heruntergeladen haben. Beim Importieren der Metadaten lädt das System automatisch das erforderliche Signatur- und Verschlüsselungszertifikat hoch.

    ![Screenshot der Seite zum Konfigurieren des einmaligen Anmeldens mit ausgewählter Registerkarte für den Identitätsanbieter und ausgewählter Aktion für neuen Identitätsanbieter](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Um die **Assertion Consumer Service-URL** in der SAML-Anforderung hinzuzufügen, aktivieren Sie **Include Assertion Consumer Service URL** (Assertion Consumer Service-URL aufnehmen).

5. Klicken Sie auf **Activate Single Sign-On** (Einmaliges Anmelden aktivieren).

6. Speichern Sie die Änderungen.

7. Klicken Sie auf die Registerkarte **My System** (Mein System).

    ![Screenshot der Seite zum Konfigurieren des einmaligen Anmeldens mit ausgewählter Registerkarte für das eigene System](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Fügen Sie im Textfeld **Azure AD Sign On URL** (Azure AD-Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    ![Einmaliges Anmelden konfigurieren](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Geben Sie an, ob der Mitarbeiter manuell zwischen der Anmeldung mit Benutzer-ID und Kennwort oder SSO wählen kann. Aktivieren Sie dazu die Option **Manual Identity Provider Selection** (Manuelle Auswahl des Identitätsanbieters).

10. Geben Sie im Abschnitt **SSO URL** (SSO-URL) die URL an, die von Mitarbeitern für die Anmeldung beim System verwendet werden soll.
    In der Dropdownliste „URL Sent to Employee“ (An Mitarbeiter gesendete URL) können Sie zwischen den folgenden Optionen wählen:

    **Non-SSO URL**

    Das System sendet nur die normale System-URL an den Mitarbeiter. Der Mitarbeiter kann sich nicht per SSO anmelden, sondern muss stattdessen ein Kennwort oder Zertifikat verwenden.

    **SSO-URL**

    Das System sendet nur die SSO-URL an den Mitarbeiter. Der Mitarbeiter kann sich per SSO anmelden. Die Authentifizierungsanforderung wird über den IdP umgeleitet.

    **Automatische Auswahl**

    Ist SSO nicht aktiv, sendet das System die normale System-URL an den Mitarbeiter. Wenn SSO aktiv ist, überprüft das System, ob der Mitarbeiter über ein Kennwort verfügt. Ist ein Kennwort verfügbar, werden sowohl die SSO-URL als auch die URL ohne SSO an den Mitarbeiter gesendet. Besitzt der Mitarbeiter kein Kennwort, wird jedoch nur die SSO-URL an den Mitarbeiter gesendet.

11. Speichern Sie die Änderungen.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAP Business ByDesign gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **SAP Business ByDesign** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **SAP Business ByDesign** aus.

    ![SAP Business ByDesign-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-sap-business-bydesign-test-user"></a>Erstellen eines SAP Business ByDesign-Testbenutzers

In diesem Abschnitt erstellen Sie in SAP Business ByDesign einen Benutzer mit dem Namen Britta Simon. Wenden Sie sich an das [Clientsupportteam von SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html), um die Benutzer in der SAP Business ByDesign-Plattform hinzuzufügen. 

> [!NOTE]
> Stellen Sie sicher, dass der Wert für „NameID“ mit dem Feld „username“ der SAP Business ByDesign-Plattform übereinstimmt.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SAP Business ByDesign“ klicken, sollten Sie automatisch bei Ihrer SAP Business ByDesign-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)