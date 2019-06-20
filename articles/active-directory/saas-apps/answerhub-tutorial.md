---
title: 'Tutorial: Azure Active Directory-Integration mit AnswerHub | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und AnswerHub konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52c49bdd51bda7876d19a681bde79c9dbeeb4ea7
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901281"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Azure Active Directory-Integration mit AnswerHub

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory (Azure AD) in AnswerHub integrieren.
Die Integration von AnswerHub in Azure AD hat die folgenden Vorteile:

* Sie können Azure AD verwenden, um zu steuern, wer Zugriff auf AnswerHub hat.
* Sie können für Ihre Benutzer die automatische Anmeldung an AnswerHub mit ihren Azure AD-Konten ermöglichen (einmaliges Anmelden, Single Sign-On, SSO).
* Sie haben die Möglichkeit, Ihre Konten an einem zentralen Ort zu verwalten: über das Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in AnswerHub konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie nicht über eine Azure AD-Umgebung verfügen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion erhalten.
* Ein AnswerHub-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* AnswerHub unterstützt SP-initiiertes einmaliges Anmelden.

## <a name="add-answerhub-from-the-gallery"></a>Hinzufügen von AnswerHub aus dem Katalog

Zum Einrichten der Integration von AnswerHub in Azure AD müssen Sie AnswerHub aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Fügen Sie AnswerHub wie folgt aus dem Katalog hinzu:**

1. Wählen Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) die Option **Azure Active Directory** aus.

    ![Azure Active Directory-Schaltfläche](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Um eine Anwendung hinzuzufügen, wählen Sie oben im Fenster **Neue Anwendung**.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **AnswerHub** ein. Wählen Sie in der Ergebnisliste den Eintrag **AnswerHub** und dann **Hinzufügen**.

     ![AnswerHub in der Ergebnisliste](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Einrichten des einmaligen Anmeldens in Azure AD und Testen

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit AnswerHub basierend auf einer Testbenutzerin namens „Britta Simon“.
Für einmaliges Anmelden müssen Sie einen Link zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in AnswerHub einrichten.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für AnswerHub müssen Sie die folgenden Schritte ausführen:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on), um Ihren Benutzern das Verwenden der Funktion zu ermöglichen.
2. [Konfigurieren des einmaligen Anmeldens für AnswerHub](#configure-answerhub-single-sign-on), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite einzurichten.
3. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user) mit dem Namen „Britta Simon“.
4. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. Erstellen eines AnswerHub-Testbenutzers, der dem Azure AD-Testbenutzer entspricht und damit verknüpft ist.
6. [Testen des einmaligen Anmeldens](#test-single-sign-on), um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt richten Sie einmaliges Anmelden in Azure AD im Azure-Portal ein.

**Konfigurieren Sie einmaliges Anmelden von Azure AD mit AnswerHub wie folgt:**

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **AnswerHub** auf **Einmaliges Anmelden**.

    ![Schaltfläche für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Dialogfeld zum Auswählen der Methode für einmaliges Anmelden](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol „Bearbeiten“ aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![Abschnitt „Grundlegende SAML-Konfiguration“](common/sp-identifier.png)

    a. Geben Sie im Feld **Anmelde-URL** eine URL mit diesem Muster ein: `https://<company>.answerhub.com`.

    b. Geben Sie im Feld **Bezeichner (Entitäts-ID)** eine URL mit diesem Muster ein: `https://<company>.answerhub.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam von AnswerHub](mailto:success@answerhub.com), um die Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Link **Herunterladen** neben **Zertifikat (Base64)** gemäß Ihren Anforderungen aus, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Downloadlink für Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **AnswerHub einrichten** die entsprechende URL (bzw. mehrere) gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

   Sie können diese URLs kopieren:
    - Anmelde-URL

    - Azure AD-Bezeichner

    - Abmelde-URL

### <a name="configure-answerhub-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für AnswerHub

In diesem Abschnitt richten Sie einmaliges Anmelden für AnswerHub ein.  

**Konfigurieren Sie einmaliges Anmelden für AnswerHub wie folgt:**

1. Melden Sie sich in einem anderen Webbrowserfenster bei der AnswerHub-Unternehmenswebsite als Administrator an.

    > [!NOTE]
    > Wenden Sie sich an das [Supportteam von AnswerHub](mailto:success@answerhub.com.), falls Sie Hilfe beim Konfigurieren von AnswerHub benötigen.

2. Wechseln Sie zu **Administration**.

3. Wählen Sie auf der Registerkarte **Benutzer und Gruppen** im linken Bereich im Abschnitt **Social Settings** (Einstellungen für soziale Netzwerke) die Option **SAML Setup** (SAML-Setup).

4. Führen Sie auf der Registerkarte **IDP Config** (IDP-Konfiguration) die folgenden Schritte aus:

    ![Registerkarte „Benutzer und Gruppen“](./media/answerhub-tutorial/ic785172.png "SAML-Setup")  
  
    a. Fügen Sie im Feld **IDP Login Url** (IDP-Anmelde-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.
  
    b. Fügen Sie im Feld **IDP Logout URL** (IDP-Abmelde-URL) die **Abmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    c. Geben Sie im Feld **IDP Name Identifier Format** (IDP-Namensbezeichnerformat) den Wert für den **Bezeichner** ein, den Sie im Azure-Portal im Abschnitt **Benutzerattribute** ausgewählt haben.
  
    d. Wählen Sie **Keys and Certificates** (Schlüssel und Zertifikate).

5. Führen Sie im Abschnitt **Keys and Certificates** (Schlüssel und Zertifikate) die folgenden Schritte aus:

    ![Abschnitt „Keys and Certificates“ (Schlüssel und Zertifikate)](./media/answerhub-tutorial/ic785173.png "Keys and Certificates (Schlüssel und Zertifikate)")  

    a. Öffnen Sie das Base64-codierte Zertifikat, das Sie über das Azure-Portal heruntergeladen haben, im Editor, und kopieren Sie seinen Inhalt. Fügen Sie den Inhalt dann in das Feld **IDP Public Key (x509 Format)** (Öffentlicher IDP-Schlüssel (X.509-Format)) ein.
  
    b. Wählen Sie **Speichern** aus.

6. Wählen Sie auf der Registerkarte **IDP Config** (IDP-Konfiguration) die Option **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

**Erstellen Sie wie folgt einen Azure AD-Testbenutzer:**

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Auswahl von „Azure Active Directory“ > „Benutzer“ > „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Benutzereigenschaften](common/user-properties.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** die Zeichenfolge **brittasimon\@<Ihreunternehmensdomäne.erweiterung>** ein.  
    Beispiel: BrittaSimon@contoso.com.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt richten Sie Britta Simon für die Verwendung des einmaligen Anmeldens von Azure AD ein, indem Sie ihr Zugriff auf AnswerHub gewähren.

**Weisen Sie den Azure AD-Testbenutzer wie folgt zu:**

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **AnswerHub**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste mit den Anwendungen **AnswerHub** aus.

    ![Liste „Anwendungen“](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Benutzer und Gruppen auswählen](common/users-groups-blade.png)

4. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **Britta Simon** und anschließend am unteren Bildschirmrand die Schaltfläche **Auswählen**.

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. 

7. Wählen Sie unten auf der Seite die Schaltfläche **Auswählen**.

8. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-an-answerhub-test-user"></a>Erstellen eines AnswerHub-Testbenutzers

Damit sich Azure AD-Benutzer an AnswerHub anmelden können, müssen Sie diese in AnswerHub hinzufügen. In AnswerHub wird diese Aufgabe manuell durchgeführt.

**Richten Sie wie folgt ein Benutzerkonto ein:**

1. Melden Sie sich bei Ihrer **AnswerHub** -Unternehmenswebsite als Administrator an.

2. Wechseln Sie zu **Administration**.

3. Wählen Sie die Registerkarte **Benutzer und Gruppen**.

4. Wählen Sie im linken Bereich im Abschnitt **Benutzer verwalten** die Option **Create or import users** (Benutzer erstellen oder importieren) und dann **Benutzer und Gruppen**.

   ![Registerkarte „Benutzer und Gruppen“](./media/answerhub-tutorial/ic785175.png "Benutzer und Gruppen")

5. Geben Sie in den entsprechenden Feldern die **E-Mail-Adresse**, den **Benutzernamen** und das **Kennwort** eines gültigen Azure AD-Kontos ein, das Sie hinzufügen möchten, und wählen Sie anschließend **Speichern**.

> [!NOTE]
> Sie können beliebige andere Tools für die Erstellung von Benutzerkonten oder die von AnswerHub bereitgestellte API verwenden, um Azure AD-Benutzerkonten einzurichten.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel „AnswerHub“ wählen, sollten Sie automatisch bei der AnswerHub-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

