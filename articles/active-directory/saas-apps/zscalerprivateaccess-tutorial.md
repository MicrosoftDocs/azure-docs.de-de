---
title: 'Tutorial: Azure Active Directory-Integration mit Zscaler Private Access (ZPA) | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zscaler Private Access (ZPA) konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.openlocfilehash: aaef5c01e554ac226828f601dc6144d13846198a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517411"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Tutorial: Integrieren von Zscaler Private Access (ZPA) mit Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Zscaler Private Access (ZPA) in Azure Active Directory (Azure AD) integrieren. Die Integration von Zscaler Private Access (ZPA) in Azure AD bietet die folgenden Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Zscaler Private Access (ZPA) hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Zscaler Private Access (ZPA) anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement für Zscaler Private Access (ZPA), für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. Zscaler Private Access (ZPA) unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Hinzufügen von Zscaler Private Access (ZPA) über den Katalog

Zum Konfigurieren der Integration von Zscaler Private Access (ZPA) in Azure AD müssen Sie Zscaler Private Access (ZPA) aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Zscaler Private Access (ZPA)** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Zscaler Private Access (ZPA)** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zscaler Private Access (ZPA) mithilfe eines Testbenutzers namens **Britta Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zscaler Private Access (ZPA) eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Zscaler Private Access (ZPA) müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
2. **[Konfigurieren des einmaligen Anmeldens für Zscaler Private Access (ZPA)](#configure-zscaler-private-access-zpa)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Testbenutzers für Zscaler Private Access (ZPA)](#create-zscaler-private-access-zpa-test-user)** , um eine Entsprechung von Britta Simon in Zscaler Private Access (ZPA) zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Zscaler Private Access (ZPA)** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    1. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`.

    1. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL ein: `https://samlsp.private.zscaler.com/auth/metadata`.

    > [!NOTE]
    > Der Wert **Anmelde-URL** entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der tatsächlichen Anmelde-URL. Wenden Sie sich an das [Supportteam für den Zscaler Private Access (ZPA)-Client](https://help.zscaler.com/zpa-submit-ticket), um den Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML** , und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Zscaler Private Access (ZPA) einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Konfigurieren von Zscaler Private Access (ZPA)

1. Wenn Sie die Konfiguration in Zscaler Private Access (ZPA) automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Zscaler Private Access (ZPA) einrichten** , um zur Zscaler Private Access (ZPA)-Anwendung weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Zscaler Private Access (ZPA) anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Zscaler Private Access (ZPA) manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Unternehmenswebsite für Zscaler Private Access (ZPA) als Administrator an, und führen Sie die folgenden Schritte aus:

4. Klicken Sie links im Menü auf **Administration** (Verwaltung) und dann im Abschnitt **AUTHENTICATION** (AUTHENTIFIZIERUNG) auf **IdP Configuration** (IdP-Konfiguration).

    ![Zscaler Private Access Administrator: Administration (Verwaltung)](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. Klicken Sie in der Ecke oben rechts auf **Add IdP Configuration** (IdP-Konfiguration hinzufügen). 

    ![Zscaler Private Access Administrator: IdP](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Führen Sie auf der Seite **Add IdP Configuration** (IdP-Konfiguration hinzufügen) die folgenden Schritte aus:
 
    ![Zscaler Private Access Administrator: Auswählen](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klicken Sie auf **Select File** (Datei auswählen), um im Feld **IdP Metadata File Upload** (Upload der IdP-Metadatendatei) die heruntergeladene Metadatendatei von Azure AD hochzuladen.

    b. Die **IdP-Metadaten** werden in Azure AD gelesen, und alle Felder werden aufgefüllt, wie hier gezeigt:

    ![Zscaler Private Access Administrator: Konfiguration](./media/zscalerprivateaccess-tutorial/config.png)

    c. Wählen Sie im Feld **Domains** (Domänen) Ihre Domäne aus.
    
    d. Klicken Sie auf **Speichern** .

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `Britta Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `BrittaSimon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zscaler Private Access (ZPA) gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste der Anwendungen **Zscaler Private Access (ZPA)** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie anschließend im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-zscaler-private-access-zpa-test-user"></a>Erstellen eines Testbenutzers in Zscaler Private Access (ZPA)

In diesem Abschnitt erstellen Sie in Zscaler Private Access (ZPA) einen Benutzer namens Britta Simon. Arbeiten Sie mit dem [Supportteam von Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) zusammen, um die Benutzer auf der Plattform Zscaler Private Access (ZPA) hinzuzufügen.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich die Kachel „Zscaler Private Access (ZPA)“ auswählen, sollten Sie automatisch bei der Zscaler Private Access (ZPA)-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)