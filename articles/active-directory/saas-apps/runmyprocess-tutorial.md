---
title: 'Tutorial: Azure Active Directory-Integration mit RunMyProcess | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie das einmalige Anmelden zwischen Azure Active Directory und RunMyProcess konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: 011bbbda07806f1493ae27fbeef8509f0d12c44b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518448"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Tutorial: Integrieren von RunMyProcess in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie RunMyProcess in Azure Active Directory (Azure AD) integrieren. Die Integration von RunMyProcess in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf RunMyProcess hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei RunMyProcess anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein RunMyProcess-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* RunMyProcess unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-runmyprocess-from-the-gallery"></a>Hinzufügen von RunMyProcess aus dem Katalog

Zum Konfigurieren der Integration von RunMyProcess in Azure AD müssen Sie RunMyProcess aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **RunMyProcess** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **RunMyProcess** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit RunMyProcess mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in RunMyProcess eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit RunMyProcess die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für RunMyProcess](#configure-runmyprocess-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
5. **[Erstellen eines RunMyProcess-Testbenutzers](#create-runmyprocess-test-user)** , um ein Pendant von B. Simon in RunMyProcess zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **RunMyProcess** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam für den RunMyProcess-Client](mailto:support@runmyprocess.com), um den Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **RunMyProcess einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Konfigurieren des einmaligen Anmeldens für RunMyProcess

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrem RunMyProcess-Mandanten als Administrator an.

1. Klicken Sie links im Navigationsbereich auf **Konto** , und wählen Sie **Konfiguration** .

    ![Screenshot, in dem unter „Konto“ die Option „Konfiguration“ ausgewählt ist.](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Navigieren Sie zum Abschnitt **Authentifizierungsmethode** , und führen Sie die folgenden Schritte aus:

    ![Screenshot der Registerkarte „Authentifizierungsmethode“, auf der Sie die beschriebenen Werte eingeben können.](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Wählen unter **Methode** die Option **SSO mit Samlv2** aus.

    b. Fügen Sie in das Textfeld **SSO redirect** (SSO-Umleitung) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Logout redirect** (Abmeldeumleitung) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Geben Sie im Textfeld **Name Id Format** (Format der Namens-ID) den Wert von **Namensbezeichnerformat** als **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** ein.

    e. Öffnen Sie die Zertifikatdatei, die Sie aus dem Azure-Portal heruntergeladen haben, im Editor, kopieren Sie den Inhalt der Zertifikatdatei, und fügen Sie ihn in das Textfeld **Certificate** (Zertifikat) ein.

    f. Klicken Sie auf das Symbol **Speichern** .

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf RunMyProcess gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **RunMyProcess** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-runmyprocess-test-user"></a>Erstellen eines RunMyProcess-Testbenutzers

Damit sich Azure AD-Benutzer bei RunMyProcess anmelden können, müssen sie in RunMyProcess bereitgestellt werden. Im Fall von RunMyProcess ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der RunMyProcess-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf **Konto** , und wählen Sie links im Navigationsbereich die Option **Benutzer** . Klicken Sie anschließend auf **Neuer Benutzer** .

    ![Neuer Benutzer](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Neuer Benutzer")

1. Führen Sie im Abschnitt **Benutzereinstellungen** die folgenden Schritte aus:

    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil")
  
    a. Geben Sie **Name** und **E-Mail-Adresse** eines gültigen Azure AD-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.

    b. Wählen Sie eine **IDE-Sprache** , eine **Sprache** und ein **Profil** aus.

    c. Wählen Sie **Kontoerstellungs-E-Mail an mich senden** aus.

    d. Klicken Sie auf **Speichern** .

    > [!NOTE]
    > Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von RunMyProcess-Benutzerkonten oder mithilfe der von RunMyProcess bereitgestellten APIs erstellen.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „RunMyProcess“ klicken, sollten Sie automatisch bei der RunMyProcess-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)