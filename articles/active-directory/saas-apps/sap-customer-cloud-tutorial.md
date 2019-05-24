---
title: 'Tutorial: Azure Active Directory-Integration mit SAP Cloud for Customer | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Cloud for Customer konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba76692ec35ddfd0b6c8c49306d6056709d684e
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65902903"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Tutorial: Azure Active Directory-Integration mit SAP Cloud for Customer

In diesem Tutorial erfahren Sie, wie Sie SAP Cloud for Customer in Azure Active Directory (Azure AD) integrieren.
Die Integration von SAP Cloud for Customer in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD den Zugriff auf SAP Cloud for Customer steuern.
* Sie können Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei SAP Cloud for Customer anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SAP Cloud for Customer konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein SSO-fähiges SAP Cloud for Customer-Abonnement

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Cloud for Customer unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Hinzufügen von SAP Cloud for Customer aus dem Katalog

Zum Konfigurieren der Integration von SAP Cloud for Customer in Azure AD müssen Sie SAP Cloud for Customer aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um SAP Cloud for Customer aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SAP Cloud for Customer** ein, wählen Sie im Ergebnisbereich **SAP Cloud for Customer** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![SAP Cloud for Customer in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei SAP Cloud for Customer mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Cloud for Customer eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei SAP Cloud for Customer müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SAP Cloud for Customer](#configure-sap-cloud-for-customer-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines SAP Cloud for Customer-Testbenutzers](#create-sap-cloud-for-customer-test-user)**, um in SAP Cloud for Customer ein Pendant von Britta Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD bei SAP Cloud for Customer zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SAP Cloud for Customer** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für SAP Cloud for Customer](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server name>.crm.ondemand.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<server name>.crm.ondemand.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für den SAP Cloud for Customer-Client](https://www.sap.com/about/agreements.sap-cloud-services-customers.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die SAP Cloud for Customer-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

6. Führen Sie im Dialogfeld **Benutzerattribute und Ansprüche** im Abschnitt **Benutzerattribute** die folgenden Schritte aus:

    a. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Wählen Sie als **Quelle** die Option **Transformation** aus.

    c. Wählen Sie in der Liste **Transformation** die Option **ExtractMailPrefix()** aus.

    d. Wählen Sie in der Liste **Parameter 1** das Benutzerattribut aus, das Sie für Ihre Implementierung verwenden möchten.
    Wenn Sie z.B. den Wert „EmployeeID“ als eindeutige Benutzer-ID verwenden möchten und den Attributwert in „ExtensionAttribute2“ gespeichert haben, wählen Sie „user.extensionattribute2“ aus.

    e. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

8. Kopieren Sie im Abschnitt **SAP Cloud for Customer einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-sap-cloud-for-customer-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SAP Cloud for Customer
   
1. Melden Sie sich am Portal für SAP Cloud for Customer mit Administratorrechten an.
   
2. Navigieren Sie zum **allgemeinen Task für die Anwendungs- und Benutzerverwaltung**, und klicken Sie auf die Registerkarte **Identitätsanbieter**.
   
3. Klicken Sie auf **New Identity Provider** (Neuer Identitätsanbieter), und wählen Sie die XML-Metadatendatei aus, die Sie aus dem Azure-Portal heruntergeladen haben. Beim Importieren der Metadaten lädt das System automatisch das erforderliche Signatur- und Verschlüsselungszertifikat hoch.
   
    ![Configure single sign-on](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
4. Azure Active Directory erfordert die Assertionsverbraucherdienst-URL des Elements in der SAML-Anforderung. Aktivieren Sie daher das Kontrollkästchen **Include Assertion Consumer Service URL** (Assertionsverbraucherdienst-URL aufnehmen).
   
5. Klicken Sie auf **Activate Single Sign-On**(Einmaliges Anmelden aktivieren).
   
6. Speichern Sie die Änderungen.
   
7. Klicken Sie auf die Registerkarte **My System** (Mein System).
   
    ![Configure single sign-on](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
8. Fügen Sie in das Textfeld **Azure AD Sign On URL** (Azure AD-Anmelde-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.
   
    ![Configure single sign-on](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
9. Geben Sie an, ob der Mitarbeiter manuell zwischen der Anmeldung mit Benutzer-ID und Kennwort oder SSO wählen kann. Aktivieren Sie dazu die Option **Manual Identity Provider Selection** (Manuelle Auswahl des Identitätsanbieters).
   
10. Geben Sie im Abschnitt **SSO-URL** die URL ein, die von Ihren Mitarbeitern für die Anmeldung beim System verwendet werden soll. 
    In der Liste **URL Sent to Employee** (An Mitarbeiter gesendete URL) können Sie zwischen den folgenden Optionen wählen:
   
    **Non-SSO URL**
   
    Das System sendet nur die normale System-URL an den Mitarbeiter. Der Mitarbeiter kann sich nicht per SSO anmelden, sondern muss stattdessen ein Kennwort oder Zertifikat verwenden.
   
    **SSO-URL** 
   
    Das System sendet nur die SSO-URL an den Mitarbeiter. Der Mitarbeiter kann sich per SSO anmelden. Die Authentifizierungsanforderung wird über den IdP umgeleitet.
   
    **Automatische Auswahl**
   
    Ist SSO nicht aktiv, sendet das System die normale System-URL an den Mitarbeiter. Wenn SSO aktiv ist, überprüft das System, ob der Mitarbeiter über ein Kennwort verfügt. Ist ein Kennwort verfügbar, werden sowohl die SSO-URL als auch die URL ohne SSO an den Mitarbeiter gesendet. Besitzt der Mitarbeiter kein Kennwort, wird jedoch nur die SSO-URL an den Mitarbeiter gesendet.
   
11. Speichern Sie die Änderungen.

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

In diesem Abschnitt ermöglichen Sie Britta Simon das einmalige Anmelden bei Azure, indem Sie ihr Zugriff auf SAP Cloud for Customer gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **SAP Cloud for Customer** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **SAP Cloud for Customer** ein, und wählen Sie den Eintrag aus.

    ![Link „SAP Cloud for Customer“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Erstellen eines SAP Cloud for Customer-Testbenutzers

In diesem Abschnitt erstellen Sie in SAP Cloud for Customer einen Benutzer mit dem Namen Britta Simon. Lassen Sie sich beim Hinzufügen der Benutzer in der SAP Cloud for Customer-Umgebung vom  [Supportteam von SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) unterstützen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

> [!NOTE]
> Stellen Sie sicher, dass der Wert für „NameID“ mit dem Feld „username“ der SAP Cloud for Customer-Plattform übereinstimmt.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SAP Cloud for Customer“ klicken, sollten Sie automatisch bei der SAP Cloud for Customer-Anwendung angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

