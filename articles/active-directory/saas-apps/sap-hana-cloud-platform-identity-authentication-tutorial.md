---
title: 'Tutorial: Azure Active Directory-Integration mit SAP Cloud Platform Identity Authentication | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Cloud Platform Identity Authentication konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: fc5e0270c4533eaae1310dc84b616d675a79c8ea
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895086"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory mit SAP Cloud Platform Identity Authentication

In diesem Tutorial erfahren Sie, wie Sie SAP Cloud Platform Identity Authentication in Azure Active Directory (Azure AD) integrieren. Die Integration von SAP Cloud Platform Identity Authentication in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAP Cloud Platform Identity Authentication hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei SAP Cloud Platform Identity Authentication anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SAP Cloud Platform Identity Authentication-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Cloud Platform Identity Authentication unterstützt **SP-** und **IDP-initiiertes**  einmaliges Anmelden.
* Nach dem Konfigurieren von SAP Cloud Platform Identity Authentication können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

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

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAP Cloud Platform Identity Authentication** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SAP Cloud Platform Identity Authentication** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Konfigurieren und Testen des einmalige Anmeldens von Azure AD für SAP Cloud Platform Identity Authentication

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAP Cloud Platform Identity Authentication mithilfe eines Testbenutzers namens **B.Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Cloud Platform Identity Authentication eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit SAP Cloud Platform Identity Authentication zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines SAP Cloud Platform Identity Authentication-Testbenutzers](#create-sap-cloud-platform-identity-authentication-test-user)** , um in SAP Cloud Platform Identity Authentication eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SAP Cloud Platform Identity Authentication** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten Modus** konfigurieren möchten:

    ![Screenshot: Abschnitt „Grundlegende SAML-Konfiguration“ mit den hervorgehobenen Textfeldern „Bezeichner“ und „Antwort-URL“ und der ausgewählten Schaltfläche „Speichern“](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Diese Werte erhalten Sie vom [Supportteam für den SAP Cloud Platform Identity Authentication-Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html). Falls Sie diesen Bezeichnerwert nicht verstehen, können Sie die SAP Cloud Platform Identity Authentication-Dokumentation zum Thema [Tenant SAML 2.0 Configuration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) lesen.

5. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für SAP Cloud Platform Identity Authentication](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Verwenden Sie Ihre jeweilige Anmelde-URL für die Geschäftsanwendung. Wenden Sie sich bei etwaigen Fragen an das [Supportteam für den SAP Cloud Platform Identity Authentication-Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html).

1. SAP Cloud Platform Identity Authentication erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus erwartet die SAP Cloud Platform Identity Authentication-Anwendung, dass in der SAML-Antwort einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um das Ihrer Anforderung entsprechende **Metadaten-XML** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

9. Kopieren Sie im Abschnitt **SAP Cloud Platform Identity Authentication einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon das einmalige Anmelden bei Azure, indem Sie ihr Zugriff auf SAP Cloud Platform Identity Authentication gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SAP Cloud Platform Identity Authentication** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Konfigurieren des einmaligen Anmeldens für SAP Cloud Platform Identity Authentication

1. Wechseln Sie zum Konfigurieren von SSO für Ihre Anwendung zur SAP Cloud Platform Identity Authentication-Verwaltungskonsole. Die URL hat das folgende Muster: `https://<tenant-id>.accounts.ondemand.com/admin`. Lesen Sie dann in der Dokumentation von SAP Cloud Platform Identity Authentication den Artikel [Integration with Microsoft Azure AD](https://developers.sap.com/tutorials/cp-ias-azure-ad.html).

2. Wählen Sie im Azure-Portal die Schaltfläche **Speichern** aus.

3. Führen Sie die folgenden Schritte nur aus, wenn Sie SSO für eine weitere SAP-Anwendung hinzufügen und aktivieren möchten. Wiederholen Sie die Schritte aus dem Abschnitt **Hinzufügen von SAP Cloud Platform Identity Authentication über den Katalog** .

4. Wählen Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAP Cloud Platform Identity Authentication** die Option **Anmeldung über Link** aus.

    ![Konfigurieren der Anmeldung über Link](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Speichern Sie die Konfiguration.

> [!NOTE]
> Die neue Anwendung nutzt die „Einmaliges Anmelden“-Konfiguration der vorherigen SAP-Anwendung. Achten Sie darauf, dass Sie in der SAP Cloud Platform Identity Authentication-Verwaltungskonsole dieselben Unternehmensidentitätsanbieter verwenden.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Erstellen eines SAP Cloud Platform Identity Authentication-Testbenutzers

Sie müssen keinen Benutzer in SAP Cloud Platform Identity Authentication erstellen. Benutzer im Azure AD-Benutzerspeicher können die Funktionalität für einmaliges Anmelden (SSO) verwenden.

SAP Cloud Platform Identity Authentication unterstützt die Identitätsverbundoption. Durch diese Option kann die Anwendung überprüfen, ob im Benutzerspeicher von SAP Cloud Platform Identity Authentication Benutzer vorhanden sind, die vom Unternehmensidentitätsanbieter authentifiziert werden.

Die Option für Identitätsverbund ist standardmäßig deaktiviert. Ist Identitätsverbund aktiviert, können auf die Anwendung nur Benutzer zugreifen, die in SAP Cloud Platform Identity Authentication importiert wurden.

Weitere Informationen zum Aktivieren oder Deaktivieren des Identitätsverbunds in SAP Cloud Platform Identity Authentication finden Sie in [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html) (Konfigurieren des Identitätsverbunds mit dem Benutzerspeicher von SAP Cloud Platform Identity Authentication) unter „Enable Identity Federation with SAP Cloud Platform Identity Authentication“ (Aktivieren des Identitätsverbunds in SAP Cloud Platform Identity Authentication).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel für SAP Cloud Platform Identity Authentication klicken, sollten Sie automatisch bei der SAP Cloud Platform Identity Authentication-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [SAP Cloud Platform Identity Authentication mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)