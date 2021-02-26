---
title: 'Tutorial: Azure Active Directory-Integration mit Adaptive Insights | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Adaptive Insights konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: d9710fd37da949f9b3f51db5cda57f582802520c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254693"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Tutorial: Integrieren von Adaptive Insights in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Adaptive Insights in Azure Active Directory (Azure AD) integrieren. Die Integration von Adaptive Insights in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Adaptive Insights hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Adaptive Insights anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Adaptive Insights-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Adaptive Insights unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="add-adaptive-insights-from-the-gallery"></a>Hinzufügen von Adaptive Insights aus dem Katalog

Zum Konfigurieren der Integration von Adaptive Insights in Azure AD müssen Sie Adaptive Insights aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Adaptive Insights** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Adaptive Insights** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-adaptive-insights"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Adaptive Insights

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Adaptive Insights mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Adaptive Insights eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Adaptive Insights die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Adaptive Insights](#configure-adaptive-insights-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Adaptive Insights-Testbenutzers](#create-adaptive-insights-test-user)** , um eine Entsprechung von B. Simon in Adaptive Insights zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Adaptive Insights** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Sie finden die Werte für den Bezeichner (Entitäts-ID) und die Antwort-URL auf der Adaptive Insights-Seite **SAML SSO Settings** (SAML-SSO-Einstellungen).

4. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Adaptive Insights einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Adaptive Insights gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Adaptive Insights** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-adaptive-insights-sso"></a>Konfigurieren des einmaligen Anmeldens für Adaptive Insights

1. Melden Sie sich in einem anderen Webbrowserfenster auf der Adaptive Insights-Unternehmenswebsite als Administrator an.

2. Wechseln Sie zu **Administration**.

    ![Screenshot: Administration im Navigationsbereich hervorgehoben](./media/adaptivesuite-tutorial/administration.png "Admin")

3. Klicken Sie im Abschnitt **Benutzer und Rollen** auf **SAML SSO Settings** (SAML-SSO-Einstellungen).

    ![Verwalten der SAML-SSO-Einstellungen](./media/adaptivesuite-tutorial/settings.png "Manage SAML SSO Settings")

4. Führen Sie auf der Seite **SAML SSO Settings** die folgenden Schritte aus:

    ![SAML-SSO-Einstellungen](./media/adaptivesuite-tutorial/saml.png "SAML SSO Settings")

    a. Geben Sie im Textfeld **Identity provider name** einen Namen für die Konfiguration ein.

    b. Fügen Sie den Wert der **Azure AD-ID**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Identity provider Entity ID** (Entitäts-ID des Identitätsanbieters) ein.

    c. Fügen Sie den Wert der **Anmelde-URL**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Identity Provider SSO URL** (SSO-URL des Identitätsanbieters) ein.

    d. Fügen Sie den Wert der **Abmelde-URL**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Custom logout URL** (Benutzerdefinierte Abmelde-URL) ein.

    e. Klicken Sie auf **Choose file**, um das heruntergeladene Zertifikat hochzuladen.

    f. Wählen Sie Folgendes aus:

     * Wählen Sie als **SAML user id** (SAML-Benutzer-ID) die Option **User’s Adaptive Insights user name** (Adaptive Insights-Benutzername des Benutzers) aus.

     * Wählen Sie als **SAML user id location** (SAML-Benutzer-ID-Speicherort) die **User id in NameID of Subject** (Benutzer-ID in NameID von Subject) aus.

     * Wählen Sie als **SAML NameID format** (SAML-NameID-Format) die Option **E-Mail-Adresse** aus.

     * Wählen Sie für **SAML aktivieren** die Option **Allow SAML SSO and direct Adaptive Insights login** (SAML-SSO und direkte Adaptive Insights-Anmeldung) aus.

    g. Kopieren Sie den Wert für **Adaptive Insights SSO URL** (Adaptive Insights-SSO-URL), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in die Textfelder **Bezeichner (Entitäts-ID)** und **Antwort-URL** ein.

    h. Klicken Sie auf **Speichern**.

### <a name="create-adaptive-insights-test-user"></a>Erstellen eines Adaptive Insights-Testbenutzers

Damit sich Azure AD-Benutzer bei Adaptive Insights anmelden können, müssen sie in Adaptive Insights bereitgestellt werden. Im Fall von Adaptive Insights muss die Bereitstellung manuell ausgeführt werden.

**Um die Benutzerbereitstellung zu konfigurieren, führen Sie die folgenden Schritte durch:**

1. Melden Sie sich auf der **Adaptive Insights**-Unternehmenswebsite als Administrator an.

2. Wechseln Sie zu **Administration**.

   ![Administrator](./media/adaptivesuite-tutorial/administration.png "Admin")

3. Klicken Sie im Abschnitt **Users and Roles** (Benutzer und Rollen) auf **Users** (Benutzer).

   ![Benutzer hinzufügen](./media/adaptivesuite-tutorial/users.png "Benutzer hinzufügen")

4. Führen Sie im Abschnitt **Neuer Benutzer** die folgenden Schritte aus:

   ![Absenden](./media/adaptivesuite-tutorial/new.png "Submit (Senden)")

   a. Geben Sie in die Textfelder **Name**, **Username** (Benutzername), **Email** (E-Mail) und **Password** (Kennwort) die entsprechenden Informationen eines gültigen Azure Active Directory-Benutzerkontos ein, das Sie bereitstellen möchten.

   b. Wählen Sie eine **Role** aus.

   c. Klicken Sie auf **Submit**(Senden).

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von Adaptive Insights-Benutzerkonten oder mit den APIs von Adaptive Insights bereitstellen.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Adaptive Insights-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Adaptive Insights“ klicken, sollten Sie automatisch bei der Adaptive Insights-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Adaptive Insights können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
