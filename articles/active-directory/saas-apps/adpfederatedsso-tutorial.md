---
title: 'Tutorial: Azure Active Directory-Integration mit ADP | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und ADP konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eba63f8295fb5bebffdc8480f763c852521e331b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65859221"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Tutorial: Azure Active Directory-Integration mit ADP

In diesem Tutorial erfahren Sie, wie Sie ADP in Azure Active Directory (Azure AD) integrieren.
Die Integration von ADP in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf ADP haben soll.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei ADP angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Integration von ADP in Azure AD benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* ADP-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ADP unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-adp-from-the-gallery"></a>Hinzufügen von ADP über den Katalog

Zum Konfigurieren der Integration von ADP in Azure AD müssen Sie ADP aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um ADP aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **ADP** ein, wählen Sie im Ergebnisbereich **ADP** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![ADP in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei ADP mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ADP eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei ADP müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für ADP](#configure-adp-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines ADP-Testbenutzers](#create-adp-test-user)**, um in ADP eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens in Azure AD bei ADP die folgenden Schritte aus:

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ADP** auf die Registerkarte **Eigenschaften**, und führen Sie die folgenden Schritte aus: 

    ![Eigenschaften für das einmalige Anmelden](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Legen Sie den Wert im Feld **Aktiviert für die Benutzeranmeldung** auf **Ja** fest.

    b. Kopieren Sie die **URL für den Benutzerzugriff**. Diese muss im Abschnitt **Anmelde-URL konfigurieren** eingefügt werden, dies wird später in diesem Tutorial erläutert.

    c. Legen Sie das Feld **Benutzerzuweisung erforderlich** auf **Ja** fest.

    d. Legen Sie das Feld **Für Benutzer sichtbar** auf **Nein** fest.

2. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **ADP** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

3. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

5. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![„Domäne und URLs für ADP“ – SSO-Informationen](common/idp-identifier.png)

    Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL ein: `https://fed.adp.com`.

6. Die ADP-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen. Der Anspruchsname ist immer eine Kombination aus **„PersonImmutableID“** und dem Wert, der **employeeid** zugeordnet werden soll. 

    Die Benutzerzuordnung von Azure AD zu ADP erfolgt auf Grundlage der **employeeid**, aber je nach Anwendungseinstellungen ist auch eine Zuordnung zu einem anderen Wert möglich. Wenden Sie sich also zunächst an das [ADP-Supportteam](https://www.adp.com/contact-us/overview.aspx), um den korrekten Bezeichner eines Benutzers zu verwenden, und ordnen Sie diesen Wert dann dem Anspruch **PersonImmutableID** zu.

    ![image](common/edit-attribute.png)

7. Bearbeiten Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die Ansprüche mithilfe des Symbols zum **Bearbeiten**, oder fügen Sie die Ansprüche über **Neuen Anspruch hinzufügen** hinzu, um das SAML-Tokenattribut wie in der obigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus:
    
    | NAME | Quellattribut | 
    | ---------------| --------------- |
    | PersonImmutableID  | user.employeeid |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

    > [!NOTE] 
    > Bevor Sie die SAML-Assertion konfigurieren können, müssen Sie sich an Ihr [ADP-Supportteam](https://www.adp.com/contact-us/overview.aspx) wenden und für Ihren Mandanten den Wert des Attributs für den eindeutigen Benutzerbezeichner anfordern. Sie benötigen diesen Wert, um den benutzerdefinierten Anspruch für Ihre Anwendung zu konfigurieren. 

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="configure-adp-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für ADP

Um das einmalige Anmelden für **ADP** zu konfigurieren, müssen Sie die heruntergeladene **Metadaten-XML** auf die [ADP-Website](https://adpfedsso.adp.com/public/login/index.fcc) hochladen.

> [!NOTE]  
> Dieser Vorgang kann einige Tage in Anspruch nehmen.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurieren Ihrer ADP-Dienste für den Verbundzugriff

>[!Important]
> Mitarbeiter, die Verbundzugriff auf Ihre ADP-Dienste benötigen, müssen der ADP-Dienst-App zugewiesen und anschließend dem jeweiligen ADP-Dienst erneut zugewiesen werden.
Nach der Bestätigung durch Ihren ADP-Vertriebsbeauftragten konfigurieren Sie Ihre ADP-Dienste und weisen Benutzer zum Steuern des Benutzerzugriffs für die jeweiligen ADP-Dienste zu bzw. verwalten diese.

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **ADP** ein, wählen Sie im Ergebnisbereich **ADP** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![ADP in der Ergebnisliste](common/search-new-app.png)

5. Klicken Sie im Azure-Portal auf Ihrer Anwendungsintegrationsseite für **ADP** auf die Registerkarte **Eigenschaften**, und führen Sie die folgenden Schritte aus:  

    ![Eigenschaften für SSO über Link](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Legen Sie den Wert im Feld **Aktiviert für die Benutzeranmeldung** auf **Ja** fest.

    b.  Legen Sie das Feld **Benutzerzuweisung erforderlich** auf **Ja** fest.

    c.  Legen Sie das Feld **Für Benutzer sichtbar** auf **Ja** fest.

6. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **ADP** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

7. Wählen Sie im Dialogfeld **SSO-Methode auswählen** unter **Modus** die Option **Verknüpft**, um Ihre Anwendung mit **ADP** zu verknüpfen.

    ![SSO über Link](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Navigieren Sie zum Abschnitt **Anmelde-URL konfigurieren**, und führen Sie die folgenden Schritte aus:

    ![Eigenschaften für einmaliges Anmelden](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Fügen Sie die **URL für den Benutzerzugriff** ein, die Sie oben aus der Registerkarte **Eigenschaften** (aus der Haupt-ADP-App) kopiert haben.
                                                             
    b. Nachfolgend werden die 5 Apps aufgeführt, die unterschiedliche **Relayzustand-URLs** unterstützen. Sie müssen die geeignete **Relayzustand-URL** für die jeweilige Anwendung manuell an die **URL für den Benutzerzugriff** anfügen.
    
    * **ADP Workforce Now**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Speichern** Sie die Änderungen.

10. Beginnen Sie nach Bestätigung durch Ihren ADP-Vertriebsbeauftragten mit dem Test für einen oder zwei Benutzer.

    a. Weisen Sie der ADP-Dienst-App einige Benutzer zu, um den Verbundzugriff zu testen.

    b. Der Test ist erfolgreich, wenn die Benutzer auf die ADP-Dienst-App im Katalog und auf ihren ADP-Dienst zugreifen können.
 
11. Wenn der Test erfolgreich war, weisen Sie den ADP-Verbunddienst einzelnen Benutzern oder Benutzergruppen zu, und führen Sie ein Rollout für Ihre Mitarbeiter durch. Dies wird später in diesem Tutorial erläutert.
 
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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ADP gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **ADP**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **ADP** ein, und wählen Sie den Eintrag aus.

    ![ADP-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-adp-test-user"></a>Erstellen eines ADP-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in ADP. Wenden Sie sich an das [ADP-Supportteam](https://www.adp.com/contact-us/overview.aspx), um die Benutzer unter dem ADP-Konto hinzufügen zu lassen. 

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „ADP“ klicken, sollten Sie automatisch bei Ihrer ADP-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

