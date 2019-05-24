---
title: 'Tutorial: Azure Active Directory-Integration mit SAP Cloud Platform Identity Authentication | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Cloud Platform Identity Authentication konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e242e85525b446fcbe8a2ec05da539fb45acf487
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65867976"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: Azure Active Directory-Integration mit SAP Cloud Platform Identity Authentication

In diesem Tutorial erfahren Sie, wie Sie SAP Cloud Platform Identity Authentication in Azure Active Directory (Azure AD) integrieren.
Die Integration von SAP Cloud Platform Identity Authentication in Azure AD hat folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf SAP Cloud Platform Identity Authentication haben soll.
* Sie können es Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei SAP Cloud Platform Identity Authentication anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration mit SAP Cloud Platform Identity Authentication benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* SAP Cloud Platform Identity Authentication-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Cloud Platform Identity Authentication unterstützt **SP-** und **IDP-initiiertes**  einmaliges Anmelden.

Bevor Sie sich mit den technischen Details beschäftigen, müssen Sie die Konzepte verstehen, die hier behandelt werden. Durch den Verbund von SAP Cloud Platform Identity Authentication und Active Directory-Verbunddienste (AD FS) können Sie einmaliges Anmelden (SSO) für Anwendungen oder Dienste, die durch Azure AD (als IdP) geschützt sind, mit SAP-Anwendungen und -Diensten implementieren, die durch SAP Cloud Platform Identity Authentication geschützt sind.

SAP Cloud Platform Identity Authentication fungiert derzeit als Proxyidentitätsanbieter für SAP-Anwendungen. Azure Active Directory dient in diesem Setup als führender Identitätsanbieter. 

Das folgende Diagramm veranschaulicht diese Beziehung:

![Erstellen eines Azure AD-Testbenutzers](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Mit diesem Setup wird Ihr SAP Cloud Platform Identity Authentication-Mandant als vertrauenswürdige Anwendung in Azure Active Directory konfiguriert.

Alle SAP-Anwendungen und -Dienste, die Sie auf diese Weise schützen möchten, werden anschließend in der SAP Cloud Platform Identity Authentication-Verwaltungskonsole konfiguriert.

Daher muss die Autorisierung zum Gewähren von Zugriff auf SAP-Anwendungen und -Dienste in SAP Cloud Platform Identity Authentication erfolgen (anstatt in Azure Active Directory).

Durch Konfigurieren von SAP Cloud Platform Identity Authentication als eine Anwendung über den Azure Active Directory Marketplace entfällt die Notwendigkeit, dass Sie einzelne Ansprüche oder SAML-Assertionen konfigurieren.

> [!NOTE]
> Bisher wurde nur Web-SSO von beiden Parteien getestet. Die Datenflüsse, die für die Kommunikation zwischen Apps und APIs oder zwischen APIs benötigt werden, sollten funktionieren, wurden aber noch nicht getestet. Sie werden im Rahmen späterer Aktivitäten getestet.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Hinzufügen von SAP Cloud Platform Identity Authentication über den Katalog

Um die Integration von SAP Cloud Platform Identity Authentication in Azure AD zu konfigurieren, müssen Sie SAP Cloud Platform Identity Authentication über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um SAP Cloud Platform Identity Authentication über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld die Zeichenfolge **SAP Cloud Platform Identity Authentication** ein, wählen Sie im Ergebnisbereich die Option **SAP Cloud Platform Identity Authentication** aus, und klicken Sie anschließend auf **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![SAP Cloud Platform Identity Authentication in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei [Anwendungsname] mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in [Anwendungsname] eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei [Anwendungsname] müssen Sie die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines SAP Cloud Platform Identity Authentication-Testbenutzers](#create-sap-cloud-platform-identity-authentication-test-user)**, um in SAP Cloud Platform Identity Authentication eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Azure AD-Darstellung verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit [Anwendungsname] zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SAP Cloud Platform Identity Authentication** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie im **IDP**-initiierten Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für SAP Cloud Platform Identity Authentication](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Diese Werte erhalten Sie vom [Supportteam für den SAP Cloud Platform Identity Authentication-Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html). Falls Sie diesen Bezeichnerwert nicht verstehen, können Sie die SAP Cloud Platform Identity Authentication-Dokumentation zum Thema [Tenant SAML 2.0 Configuration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) lesen.

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für SAP Cloud Platform Identity Authentication](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Verwenden Sie Ihre jeweilige Anmelde-URL für die Geschäftsanwendung. Wenden Sie sich bei etwaigen Fragen an das [Supportteam für den SAP Cloud Platform Identity Authentication-Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html).

6. Die SAP Cloud Platform Identity Authentication-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

7. Wenn Ihre SAP-Anwendung ein Attribut wie **firstName** erwartet, fügen Sie das Attribut **firstName** im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut wie in der obigen Abbildung gezeigt hinzu, und führen Sie die folgenden Schritte aus:

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den Attributnamen „firstName“ ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Wählen Sie in der Liste **Quellattribut** den Attributwert „user.givenname“ aus.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Metadaten-XML** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

9. Kopieren Sie im Abschnitt **SAP Cloud Platform Identity Authentication einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SAP Cloud Platform Identity Authentication

1. Wechseln Sie zum Konfigurieren von SSO für Ihre Anwendung zur SAP Cloud Platform Identity Authentication-Verwaltungskonsole. Die URL hat das folgende Muster: `https://<tenant-id>.accounts.ondemand.com/admin`. Lesen Sie dann in der Dokumentation von SAP Cloud Platform Identity Authentication den Artikel [Integration with Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. Wählen Sie im Azure-Portal die Schaltfläche **Speichern** aus.

3. Führen Sie die folgenden Schritte nur aus, wenn Sie SSO für eine weitere SAP-Anwendung hinzufügen und aktivieren möchten. Wiederholen Sie die Schritte aus dem Abschnitt **Hinzufügen von SAP Cloud Platform Identity Authentication über den Katalog**.

4. Wählen Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAP Cloud Platform Identity Authentication** die Option **Anmeldung über Link** aus.

    ![Konfigurieren der Anmeldung über Link](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Speichern Sie die Konfiguration.

> [!NOTE]
> Die neue Anwendung nutzt die „Einmaliges Anmelden“-Konfiguration der vorherigen SAP-Anwendung. Achten Sie darauf, dass Sie in der SAP Cloud Platform Identity Authentication-Verwaltungskonsole dieselben Unternehmensidentitätsanbieter verwenden.

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

In diesem Abschnitt ermöglichen Sie Britta Simon das einmalige Anmelden bei Azure, indem Sie ihr Zugriff auf SAP Cloud Platform Identity Authentication gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **SAP Cloud Platform Identity Authentication** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **SAP Cloud Platform Identity Authentication** aus.

    ![Der Link „SAP Cloud Platform Identity Authentication“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Erstellen eines SAP Cloud Platform Identity Authentication-Testbenutzers

Sie müssen keinen Benutzer in SAP Cloud Platform Identity Authentication erstellen. Benutzer im Azure AD-Benutzerspeicher können die Funktionalität für einmaliges Anmelden (SSO) verwenden.

SAP Cloud Platform Identity Authentication unterstützt die Identitätsverbundoption. Durch diese Option kann die Anwendung überprüfen, ob im Benutzerspeicher von SAP Cloud Platform Identity Authentication Benutzer vorhanden sind, die vom Unternehmensidentitätsanbieter authentifiziert werden.

Die Option für Identitätsverbund ist standardmäßig deaktiviert. Ist Identitätsverbund aktiviert, können auf die Anwendung nur Benutzer zugreifen, die in SAP Cloud Platform Identity Authentication importiert wurden.

Weitere Informationen zum Aktivieren oder Deaktivieren des Identitätsverbunds in SAP Cloud Platform Identity Authentication finden Sie in [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html) (Konfigurieren des Identitätsverbunds mit dem Benutzerspeicher von SAP Cloud Platform Identity Authentication) unter „Enable Identity Federation with SAP Cloud Platform Identity Authentication“ (Aktivieren des Identitätsverbunds in SAP Cloud Platform Identity Authentication).

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel für SAP Cloud Platform Identity Authentication klicken, sollten Sie automatisch bei der SAP Cloud Platform Identity Authentication-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
