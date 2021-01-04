---
title: 'Tutorial: Azure Active Directory-Integration mit E Sales Manager Remix | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und E Sales Manager Remix konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: c06595b683092abf52300481068daab26394c4cb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995640"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrieren von Azure Active Directory mit E Sales Manager Remix

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory (Azure AD) mit E Sales Manager Remix integrieren.

Die Integration von Azure AD mit E Sales Manager Remix bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf E Sales Manager Remix hat.
- Sie können Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei E Sales Manager Remix anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort, im Azure-Portal, verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit E Sales Manager Remix konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein SSO-fähiges E Sales Manager Remix-Abonnement

> [!NOTE]
> Es wird *nicht* empfohlen, zum Testen der Schritte in diesem Tutorial eine Produktionsumgebung zu verwenden.

Beachten Sie beim Testen der Schritte in diesem Tutorial die folgenden Empfehlungen:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. 

Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

* Hinzufügen von E Sales Manager Remix aus dem Katalog
* Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Hinzufügen von E Sales Manager Remix aus dem Katalog
Zum Konfigurieren der Integration von Azure AD mit E Sales Manager Remix müssen Sie E Sales Manager Remix wie folgt aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen:

1. Wählen Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) die Option **Azure Active Directory** aus. 

    ![Schaltfläche „Azure Active Directory“][1]

1. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Fenster „Unternehmensanwendungen“][2]
    
1. Um eine neue Anwendung hinzuzufügen, wählen Sie oben im Fenster **Neue Anwendung** aus.

    ![Schaltfläche „Neue Anwendung“][3]

1. Geben Sie in das Suchfeld **E Sales Manager Remix** ein, und wählen Sie **E Sales Manager Remix** in der Ergebnisliste und dann **Hinzufügen** aus.

    ![E Sales Manager Remix in der Ergebnisliste](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei E Sales Manager Remix mithilfe einer Testbenutzerin namens Britta Simon.

Damit das einmalige Anmelden funktioniert, muss Azure AD den E Sales Manager Remix-Benutzer und sein Gegenstück in Azure AD identifizieren. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in E Sales Manager Remix muss eine Linkbeziehung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit E Sales Manager Remix führen Sie die Bausteine in den nächsten fünf Abschnitten aus:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

Aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal, und konfigurieren Sie das einmalige Anmelden in Ihrer E Sales Manager Remix-Anwendung, indem Sie wie folgt vorgehen:

1. Wählen Sie im Azure-Portal auf der Anwendungsintegrationsseite für **E Sales Manager Remix** die Option **Einmaliges Anmelden** aus.

    ![Link „Einmaliges Anmelden“][4]

1. Wählen Sie im Fenster **Einmaliges Anmelden** im Feld **SSO-Mode** **SAML-basierte Anmeldung** aus.
 
    ![Fenster „Einmaliges Anmelden“](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Führen Sie unter **Domäne und URLs für E Sales Manager Remix** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein: *https://\<Server-Based-URL>/\<sub-domain>/esales-pc*.

    b. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: *https://\<Server-Based-URL>/\<sub-domain>/* .

    c. Notieren Sie den Wert von **Bezeichner** zur späteren Verwendung in diesem Tutorial.
    
    > [!NOTE] 
    > Die vorangehenden Werte sind keine echten Werte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für den E Sales Manager Remix-Client](mailto:esupport@softbrain.co.jp), um diese Werte zu erhalten.

1. Wählen Sie unter **SAML-Signaturzertifikat** die Option **Zertifikat (Base64)** , und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Link zum Herunterladen des Zertifikats (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Aktivieren Sie das Kontrollkästchen **Alle weiteren Benutzerattribute anzeigen und bearbeiten**, und wählen Sie dann das Attribut **emailaddress** aus.
    
    ![Fenster „Benutzerattribute“](./media/esalesmanagerremix-tutorial/configure1.png)

    Das Fenster **Attribut bearbeiten** wird geöffnet.

1. Kopieren Sie die Werte in **Namespace** und **Name**. Generieren Sie den Wert nach dem Muster *\<Namespace>/\<Name>* , und speichern Sie ihn zur späteren Verwendung in diesem Tutorial.

    ![Fenster „Attribut bearbeiten“](./media/esalesmanagerremix-tutorial/configure2.png)

1. Wählen Sie unter **E Sales Manager Remix-Konfiguration** die Option **E Sales Manager Remix konfigurieren** aus.

    ![Der Screenshot zeigt den Abschnitt „E Sales Manager Remix-Konfiguration“ mit ausgewählter Option „E Sales Manager Remix konfigurieren“.](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Das Fenster **Anmeldung konfigurieren** wird geöffnet.

1. Kopieren Sie im Abschnitt **Kurzübersicht** die Abmelde-URL und die URL für den SAML-SSO-Dienst.

1. Wählen Sie **Speichern** aus.

    ![Die Schaltfläche „Speichern“](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Melden Sie sich bei Ihrer E Sales Manager Remix-Anwendung als Administrator an.

1. Wählen Sie rechts oben **To Administrator Menu** (Zum Administratormenü) aus.

    ![Befehl „To Administrator Menu“ (Zum Administratormenü)](./media/esalesmanagerremix-tutorial/configure4.png)

1. Wählen Sie im linken Bereich **System settings** > **Cooperation with external system** (Systemeinstellungen > Zusammenarbeit mit externem System) aus.

    ![Links „System settings“ und „Cooperation with external system“ (Systemeinstellungen > Zusammenarbeit mit externem System)](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. Wählen Sie im Fenster **Cooperation with external system** (Zusammenarbeit mit externem System) die Option **SAML** aus.

    ![Fenster „Cooperation with external system“ (Zusammenarbeit mit externem System)](./media/esalesmanagerremix-tutorial/configure6.png)

1. Führen Sie unter **SAML authentication setting** (SAML-Authentifizierungseinstellungen) die folgenden Schritte aus:

    ![Abschnitt „SAML authentication setting“ (SAML-Authentifizierungseinstellungen)](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Aktivieren Sie das Kontrollkästchen **PC version**.
    
    b. Wählen Sie in der Dropdownliste im Abschnitt **Collaboration item** (Zusammenarbeitselement) die Option **email** aus.

    c. Fügen Sie in das Feld **Collaboration item** (Zusammenarbeitselement) den Anspruchswert ein, den Sie aus dem Azure-Portal kopiert haben (d.h. **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** ).

    d. Fügen Sie in das Feld **Issuer (entity ID)** (Aussteller (Entitäts-ID)) den Wert für „Bezeichner“ ein, den Sie aus dem Azure-Portal im Abschnitt **Domäne und URLs für E Sales Manager Remix** kopiert haben.

    e. Wählen Sie **File selection** (Dateiauswahl) aus, um das heruntergeladene Zertifikat aus dem Azure-Portal hochzuladen.

    f. Fügen Sie in das Feld **ID provider login URL** (Anmelde-URL des Identitätsanbieters) die URL des SAML-SSO-Diensts ein, die Sie zuvor aus dem Azure-Portal kopiert haben.

    g. Fügen Sie in das Feld **Identity Provider Logout URL** (Abmelde-URL des Identitätsanbieters) die Abmelde-URL ein, die Sie zuvor aus dem Azure-Portal kopiert haben.

    h. Wählen Sie **Setting complete** (Einstellung abgeschlossen) aus.

> [!TIP]
> Zum Einrichten der App können Sie eine Kurzversion der vorstehenden Anweisungen im [Azure-Portal](https://portal.azure.com) lesen. Nachdem Sie diese App über den Abschnitt **Active Directory** > **Unternehmensanwendungen** hinzugefügt haben, navigieren Sie zur Registerkarte **Einmaliges Anmelden**, und rufen Sie unten im Abschnitt **Konfiguration** die eingebettete Dokumentation auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie unter [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal wie folgt einen Testbenutzer mit dem Namen Britta Simon:

![Erstellen eines Azure AD-Testbenutzers][100]

1. Klicken Sie im linken Bereich des Azure-Portals auf **Azure Active Directory**.

    ![Link „Azure Active Directory“](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Um die Liste der aktuellen Benutzer anzuzeigen, wählen Sie **Benutzer und Gruppen** > **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Wählen Sie oben im Fenster **Alle Benutzer** die Option **Hinzufügen** aus.

    ![Schaltfläche „Hinzufügen“](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Das Fenster **Benutzer** wird geöffnet.

1. Gehen Sie im Fenster **Benutzer** wie folgt vor:

    ![Fenster „Benutzer“](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie im Feld **Benutzername** die E-Mail-Adresse des Benutzers Britta Simon ein.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie den Wert im Feld **Kennwort**.

    d. Klicken Sie auf **Erstellen**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Erstellen eines E Sales Manager Remix-Testbenutzers

1. Melden Sie sich bei Ihrer E Sales Manager Remix-Anwendung als Administrator an.

1. Wählen Sie **To Administrator Menu** (Zum Administratormenü) aus dem Menü oben rechts aus.

    ![E Sales Manager Remix-Konfiguration](./media/esalesmanagerremix-tutorial/configure4.png)

1. Wählen Sie **Your company settings** > **Maintenance of departments and employees** (Ihre Unternehmenseinstellungen > Verwaltung von Abteilungen und Mitarbeitern) und dann **Employees registered** (Registrierte Mitarbeiter) aus.

    ![Registerkarte „Employees registered“ (Registrierte Mitarbeiter)](./media/esalesmanagerremix-tutorial/user1.png)

1. Führen Sie im Abschnitt **New employee registration** (Registrierung neuer Mitarbeiter) die folgenden Schritte aus:
    
    ![Abschnitt „New employee registration“ (Registrierung neuer Mitarbeiter)](./media/esalesmanagerremix-tutorial/user2.png)

    a. Geben Sie im Feld **Employee Name** (Mitarbeitername) den Namen des Benutzers ein (z.B. **Britta**).

    b. Füllen Sie die weiteren erforderlichen Felder aus.
    
    c. Wenn Sie SAML aktivieren, kann sich der Administrator nicht über die Anmeldeseite anmelden. Gewähren Sie dem Benutzer Administratoranmelderechte, indem Sie das Kontrollkästchen **Admin Login** (Administratoranmeldung) aktivieren.

    d. Wählen Sie **Registration** (Registrierung) aus.

1. Wenn Sie sich in Zukunft als Administrator anmelden möchten, melden Sie sich als der Benutzer mit den Administratorberechtigungen an, und wählen dann rechts oben **To Administrator Menu** (Zum Administratormenü) aus.

    ![Befehl „To Administrator Menu“ (Zum Administratormenü)](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie der Benutzerin Britta Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf E Sales Manager Remix gewähren. Führen Sie hierzu folgende Schritte aus: 

![Zuweisen der Benutzerrolle][200] 

1. Öffnen Sie im Azure-Portal die **Anwendungsansicht**, navigieren Sie zur **Verzeichnisansicht**, und wählen Sie dann **Unternehmensanwendungen** > **Alle Anwendungen** aus.

    ![Links „Unternehmensanwendungen“ und „Alle Anwendungen“][201] 

1. Wählen Sie in der **Anwendungsliste** **E Sales Manager Remix** aus.

    ![Link „E Sales Manager Remix“](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. Wählen Sie auf der linken Seite **Benutzer und Gruppen**.

    ![Link „Benutzer und Gruppen“][202]

1. Wählen Sie **Hinzufügen** und dann im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen**.

    ![Bereich „Zuweisung hinzufügen“][203]

1. Wählen Sie im Fenster **Benutzer und Gruppen** in der Liste **Benutzer** die Option **Britta Simon** aus.

1. Wählen Sie die Schaltfläche **Auswählen** aus.

1. Wählen Sie auf der Seite **Zuweisung hinzufügen** die Option **Zuweisen** aus.
    
### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel „E Sales Manager Remix“ auswählen, sollten Sie automatisch bei Ihrer E Sales Manager Remix-Anwendung angemeldet werden.

Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png