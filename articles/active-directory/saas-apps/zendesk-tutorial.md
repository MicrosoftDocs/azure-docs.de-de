---
title: 'Tutorial: Azure Active Directory-Integration mit Zendesk | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zendesk konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11370e651c338de3d139a4bb70c9658090052469
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896215"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Azure Active Directory-Integration mit Zendesk

In diesem Tutorial erfahren Sie, wie Sie Zendesk in Azure Active Directory (Azure AD) integrieren.
Die Integration von Zendesk in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Zendesk hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Zendesk anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Zendesk konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Zendesk-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zendesk unterstützt **SP**-initiiertes einmaliges Anmelden.

* Zendesk unterstützt die [**automatisierte** Benutzerbereitstellung](zendesk-provisioning-tutorial.md).

## <a name="adding-zendesk-from-the-gallery"></a>Hinzufügen von Zendesk aus dem Katalog

Zum Konfigurieren der Integration von Zendesk in Azure AD müssen Sie Zendesk aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Zendesk aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld den Namen **Zendesk** ein, wählen Sie im Ergebnisbereich den Namen **Zendesk** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Zendesk in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Zendesk mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zendesk eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Zendesk müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Zendesk](#configure-zendesk-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Zendesk-Testbenutzers](#create-zendesk-test-user)**, um eine Entsprechung von Britta Simon in Zendesk zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Zendesk die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Zendesk** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Zendesk](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.zendesk.com`.

   b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<subdomain>.zendesk.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für Zendesk-Kunden](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die Zendesk-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Es gibt keine vorgeschriebenen SAML-Attribute, aber Sie können dies optional über den Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

6. Konfigurieren Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut wie in der obigen Abbildung gezeigt, und führen Sie die folgenden Schritte aus:

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Wählen Sie in der Liste **Quellattribut** den passenden Attributwert aus.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Sie verwenden Erweiterungsattribute, um Attribute hinzuzufügen, die standardmäßig nicht in Azure AD verfügbar sind. Klicken Sie auf [User attributes that can be set in SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) (Benutzerattribute für SAML), um die vollständige Liste der SAML-Attribute abzurufen, die **Zendesk** akzeptiert.

7. Kopieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den **Fingerabdruck**, und speichern Sie ihn auf Ihrem Computer.

    ![Kopieren des Fingerabdruckwerts](common/copy-certificatethumbprint.png)

8. Kopieren Sie im Abschnitt **Zendesk einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

9. Es gibt zwei Möglichkeiten, wie Sie Zendesk konfigurieren können – automatisch oder manuell.
  
10. Wenn Sie die Konfiguration in Zendesk automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Zendesk einrichten**, um zur Anwendung Zendesk weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Zendesk anzumelden. Die Browsererweiterung führt die Konfiguration der Anwendung automatisch für Sie durch und automatisiert den Abschnitt **Konfigurieren des einmaligen Anmeldens für Zendesk**.

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Zendesk

1. Wenn Sie Zendesk manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Zendesk-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

2. Klicken Sie auf **Administrator**.

3. Klicken Sie im linken Navigationsbereich auf **Einstellungen** und anschließend auf **Sicherheit**.

4. Führen Sie auf der Seite **Sicherheit** die folgenden Schritte aus:

    ![Sicherheit](././media/zendesk-tutorial/ic773089.png "Sicherheit")

    ![Einmaliges Anmelden](././media/zendesk-tutorial/ic773090.png "Einmaliges Anmelden")

    a. Klicken Sie auf die Registerkarte **Admin & Agents**.

    b. Wählen Sie **Einmaliges Anmelden (SSO) und SAML** und anschließend **SAML** aus.

    c. Fügen Sie in das Textfeld **SAML-SSO-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie in das Textfeld **Remoteabmelde-URL** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Fügen Sie in das Textfeld **Certificate Fingerprint** (Zertifikatsfingerabdruck) den Wert **Fingerabdruck** des Zertifikats ein, das Sie aus dem Azure-Portal kopiert haben.

    f. Klicken Sie auf **Speichern**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zendesk gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Zendesk**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **Zendesk** ein, und wählen Sie den Eintrag aus.

    ![Zendesk-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-zendesk-test-user"></a>Erstellen eines Zendesk-Testbenutzers

In diesem Abschnitt wird in Zendesk eine Benutzerin namens Britta Simon erstellt. Zendesk unterstützt die automatische Benutzerbereitstellung, die standardmäßig aktiviert ist. Weitere Details zur Konfiguration der automatischen Benutzerbereitstellung finden Sie [hier](Zendesk-provisioning-tutorial.md).

**Wenn Sie einen Benutzer manuell erstellen möchten, führen Sie die folgenden Schritte aus:**

> [!NOTE]
> **Endbenutzer**-Konten werden bei der Anmeldung automatisch bereitgestellt. **Agent**- und **Admin**-Konten müssen vor dem Anmelden manuell in **Zendesk** bereitgestellt werden.

1. Melden Sie sich bei Ihrem **Zendesk** -Mandanten an.

2. Wählen Sie die Registerkarte **Kundenliste** .

3. Wählen Sie die Registerkarte **Benutzer** aus, und klicken Sie auf **Hinzufügen**.

    ![Benutzer hinzufügen](././media/zendesk-tutorial/ic773632.png "Benutzer hinzufügen")
4. Geben Sie **Name** und **E-Mail** eines vorhandenen Azure AD-Kontos ein, das Sie bereitstellen möchten, und klicken Sie dann auf **Speichern**.

    ![Neuer Benutzer](././media/zendesk-tutorial/ic773633.png "Neuer Benutzer")

> [!NOTE]
> Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Zendesk-Benutzerkonten oder mithilfe der von Zendesk bereitgestellten APIs erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Zendesk“ klicken, sollten Sie automatisch bei Ihrer Zendesk-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurieren der Benutzerbereitstellung](zendesk-provisioning-tutorial.md)