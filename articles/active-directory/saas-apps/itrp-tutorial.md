---
title: 'Tutorial: Azure Active Directory-Integration mit ITRP | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ITRP konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 2dff68f98e2922d5fc7a4fca1e6de8740bc2ae68
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "92459678"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Azure Active Directory-Integration mit ITRP

In diesem Tutorial erfahren Sie, wie Sie ITRP in Azure Active Directory (Azure AD) integrieren.
Diese Integration bietet die folgenden Vorteile:

* Sie können mit Azure AD steuern, wer Zugriff auf ITRP hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei ITRP anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit ITRP konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein ITRP-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ITRP unterstützt SP-initiiertes einmaliges Anmelden.

## <a name="add-itrp-from-the-gallery"></a>Hinzufügen von ITRP aus dem Katalog

Zum Einrichten der Integration von ITRP in Azure AD müssen Sie ITRP aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory** :

    ![Wählen Sie „Azure Active Directory“.](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** > **Alle Anwendungen** :

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Um eine Anwendung hinzuzufügen, wählen Sie oben im Fenster die Option **Neue Anwendung** :

    ![Auswählen von „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **ITRP** ein. Wählen Sie in den Suchergebnissen den Eintrag **ITRP** aus, und wählen Sie dann **Hinzufügen** aus.

     ![Suchergebnisse](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ITRP mithilfe eines Testbenutzers namens Britta Simon.
Damit einmaliges Anmelden funktioniert, müssen Sie eine Beziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ITRP einrichten.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit ITRP müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden der Funktion zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für ITRP](#configure-itrp-single-sign-on)** auf der Anwendungsseite
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD für den Benutzer zu aktivieren.
5. **[Erstellen eines ITRP-Testbenutzers](#create-an-itrp-test-user)** , der mit der Darstellung des Benutzers in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-single-sign-on)** , um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit ITRP die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für ITRP die Option **Einmaliges Anmelden** aus:

    ![„Einmaliges Anmelden“ auswählen](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren:

    ![SSO-Methode auswählen](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen:

    ![Screenshot: Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit Auswahl des Symbols „Bearbeiten“](common/edit-urls.png)

4. Führen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![Dialogfeld „Grundlegende SAML-Konfiguration“](common/sp-identifier.png)

    1. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein:
    
       `https://<tenant-name>.itrp.com`

    1. Geben Sie im Feld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Diese Werte sind Platzhalter. Sie müssen die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner verwenden. Die Werte erhalten Sie vom [Supportteam von ITRP](https://www.4me.com/support/). Sie können sich auch die Muster im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** ansehen.

5. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** das Symbol **Bearbeiten** aus, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen:

    ![Screenshot: Seite „SAML Signing Certificate“ (SAML-Signaturzertifikat) mit Auswahl des Symbols „Bearbeiten“](common/edit-certificate.png)

6. Kopieren Sie im Dialogfeld **SAML-Signaturzertifikat** den Wert **Fingerabdruck** , und speichern Sie ihn:.

    ![Kopieren des Fingerabdruckwerts](common/copy-thumbprint.png)

7. Kopieren Sie im Abschnitt **ITRP einrichten** die entsprechenden URLs gemäß Ihren Anforderungen:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    1. **Anmelde-URL**

    1. **Azure AD-Bezeichner**

    1. **Abmelde-URL** :

### <a name="configure-itrp-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für ITRP

1. Melden Sie sich in einem neuen Webbrowserfenster auf der ITRP-Unternehmenswebsite als Administrator an.

1. Wählen Sie im oberen Fensterbereich das Symbol **Settings** (Einstellungen) aus.

    ![Symbol „Einstellungen“](./media/itrp-tutorial/ic775570.png "Symbol „Einstellungen“")

1. Wählen Sie im linken Bereich **Single Sign-On** (Einmaliges Anmelden) aus:

    ![Auswählen von „Einmaliges Anmelden“](./media/itrp-tutorial/ic775571.png "„Einmaliges Anmelden“ auswählen")

1. Führen Sie im Konfigurationsabschnitt **Single Sign-On** (Einmaliges Anmelden) die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Single Sign-On“ (Einmaliges Anmelden) mit Auswahl von „Enabled“ (Aktiviert)](./media/itrp-tutorial/ic775572.png "Abschnitt „Einmaliges Anmelden“")

    ![Screenshot: Abschnitt „Single Sign-On“ (Einmaliges Anmelden), in dem Sie die in diesem Schritt beschriebenen Informationen hinzufügen können](./media/itrp-tutorial/ic775573.png "Abschnitt „Einmaliges Anmelden“")

    1. Wählen Sie **Aktiviert**.

    1. Fügen Sie im Feld **Remote logout URL** (Remoteabmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie im Feld **SAML SSO URL** (SAML-SSO-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie in das Textfeld **Certificate Fingerprint** (Zertifikatsfingerabdruck) den Wert **Fingerabdruck** des Zertifikats ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Wählen Sie **Speichern** aus.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin namens „Britta Simon“.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** aus, und wählen Sie dann **Benutzer** und anschließend **Alle Benutzer** aus:

    ![„Alle Benutzer“ auswählen](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus:

    ![„Neuer Benutzer“ auswählen](common/new-user.png)

3. Führen Sie im Dialogfeld **Benutzer** die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie in das Feld **Benutzername** den Namen **BrittaSimon@\<yourcompanydomain>.\<extension>** ein. (Beispiel: BrittaSimon@contoso.com.)

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den im Feld **Kennwort** angezeigten Wert.

    1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ITRP gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **ITRP** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen den Eintrag **ITRP** aus.

    ![Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie im linken Bereich die Option **Benutzer und Gruppen** aus:

    ![Benutzer und Gruppen auswählen](common/users-groups-blade.png)

4. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Auswählen von „Benutzer hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Fenster auf die Schaltfläche **Auswählen**.

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie unten im Fenster auf die Schaltfläche **Auswählen**.

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-an-itrp-test-user"></a>Erstellen eines ITRP-Testbenutzers

Damit sich Azure AD-Benutzer bei ITRP anmelden können, müssen Sie sie in ITRP hinzufügen. Sie müssen diese manuell hinzufügen.

Zum Erstellen eines Benutzerkontos führen Sie die folgenden Schritte aus:

1. Melden Sie sich bei Ihrem ITRP-Mandanten an.

1. Wählen Sie im oberen Fensterbereich das Symbol **Records** (Datensätze) aus.

    ![Symbol „Datensätze“](./media/itrp-tutorial/ic775575.png "Symbol „Datensätze“")

1. Wählen Sie im Menü **People** (Personen) aus:

    ![Auswählen von „Personen“](./media/itrp-tutorial/ic775587.png "„Personen“ auswählen")

1. Wählen Sie das Pluszeichen ( **+** ) aus, um eine neue Person hinzuzufügen:

    ![Auswählen des Pluszeichens](./media/itrp-tutorial/ic775576.png "Pluszeichen auswählen")

1. Führen Sie im Dialogfeld **Add New Person** (Neue Person hinzufügen) die folgenden Schritte aus:

    ![Dialogfeld „Neue Person hinzufügen“](./media/itrp-tutorial/ic775577.png "Dialogfeld „Neue Person hinzufügen“")

    1. Geben Sie den Namen und die E-Mail-Adresse eines gültigen Azure AD-Kontos ein, das Sie hinzufügen möchten.

    1. Wählen Sie **Speichern** aus.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten mithilfe beliebiger Tools zum Erstellen von Benutzerkonten oder mithilfe der von ITRP bereitgestellten API erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Jetzt müssen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich testen.

Wenn Sie im Zugriffsbereich die Kachel „ITRP“ auswählen, sollten Sie automatisch bei der ITRP-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)