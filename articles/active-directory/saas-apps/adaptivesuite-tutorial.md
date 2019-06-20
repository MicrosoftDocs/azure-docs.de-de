---
title: 'Tutorial: Azure Active Directory-Integration mit Adaptive Insights | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Adaptive Insights konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2c7ca8699274b96f7f382dfe1958bf5babbbe99
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720173"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Tutorial: Azure Active Directory-Integration mit Adaptive Insights

In diesem Tutorial erfahren Sie, wie Sie Adaptive Insights in Azure Active Directory (Azure AD) integrieren.
Die Integration von Adaptive Insights in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Adaptive Insights hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Adaptive Insights anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Adaptive Insights konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Adaptive Insights-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Adaptive Insights unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-adaptive-insights-from-the-gallery"></a>Hinzufügen von Adaptive Insights aus dem Katalog

Zum Konfigurieren der Integration von Adaptive Insights in Azure AD müssen Sie Adaptive Insights aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**So fügen Sie Adaptive Insights aus dem Katalog hinzu**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Adaptive Insights** ein, wählen Sie im Ergebnisbereich **Adaptive Insights** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Adaptive Insights in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Adaptive Insights anhand einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Adaptive Insights eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Adaptive Insights zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Adaptive Insights](#configure-adaptive-insights-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Adaptive Insights-Testbenutzers](#create-adaptive-insights-test-user)** , um eine Entsprechung von Britta Simon in Adaptive Insights zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Adaptive Insights die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Adaptive Insights** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Adaptive Insights](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Sie finden die Werte für den Bezeichner (Entitäts-ID) und die Antwort-URL auf der Adaptive Insights-Seite **SAML SSO Settings** (SAML-SSO-Einstellungen).

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Adaptive Insights einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-adaptive-insights-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Adaptive Insights

1. Melden Sie sich in einem anderen Webbrowserfenster auf der Adaptive Insights-Unternehmenswebsite als Administrator an.

2. Wechseln Sie zu **Administrator**.

    ![Admin](./media/adaptivesuite-tutorial/ic805644.png "Admin")

3. Klicken Sie im Abschnitt **Benutzer und Rollen** auf **SAML-SSO-Einstellungen verwalten**.

    ![SAML-SSO-Einstellungen verwalten](./media/adaptivesuite-tutorial/ic805645.png "SAML-SSO-Einstellungen verwalten")

4. Führen Sie auf der Seite **SAML SSO Settings** die folgenden Schritte aus:

    ![SAML-SSO-Einstellungen](./media/adaptivesuite-tutorial/ic805646.png "SAML-SSO-Einstellungen")

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

    g. Kopieren Sie den Wert der **Adaptive Insights SSO URL** (Adaptive Insights- SSO-URL) und fügen Sie diesen im Azure-Portal im Abschnitt **Domäne und URLs für Adaptive Insights** in die Textfelder **Bezeichner (Entitäts-ID)** und **Antwort-URL** ein.

    h. Klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** die Zeichenfolge brittasimon@yourcompanydomain.extension ein. Beispiel: BrittaSimon@contoso.com.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf Adaptive Insights, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **Adaptive Insights** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Adaptive Insights** aus.

    ![Adaptive Insights-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-adaptive-insights-test-user"></a>Erstellen eines Adaptive Insights-Testbenutzers

Damit sich Azure AD-Benutzer bei Adaptive Insights anmelden können, müssen sie in Adaptive Insights bereitgestellt werden. Im Fall von Adaptive Insights muss die Bereitstellung manuell ausgeführt werden.

**Um die Benutzerbereitstellung zu konfigurieren, führen Sie die folgenden Schritte durch:**

1. Melden Sie sich auf der **Adaptive Insights**-Unternehmenswebsite als Administrator an.

2. Wechseln Sie zu **Administrator**.

   ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. Klicken Sie im Abschnitt **Benutzer und Rollen** auf **Benutzer hinzufügen**.

   ![Benutzer hinzufügen](./media/adaptivesuite-tutorial/IC805648.png "Benutzer hinzufügen")

4. Führen Sie im Abschnitt **Neuer Benutzer** die folgenden Schritte aus:

   ![Senden](./media/adaptivesuite-tutorial/IC805649.png "Senden")

   a. Geben Sie in die Textfelder **Name**, **Anmeldename**, **E-Mail** und **Kennwort** die entsprechenden Informationen eines gültigen Azure Active Directory-Benutzerkontos ein, das Sie bereitstellen möchten.

   b. Wählen Sie eine **Role**aus.

   c. Klicken Sie auf **Submit**.

> [!NOTE]
> Sie können AAD-Benutzerkonten auch mit anderen Tools zum Erstellen von Adaptive Insights-Benutzerkonten oder mit den von Adaptive Insights bereitgestellten APIs erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Adaptive Insights“ klicken, sollten Sie automatisch bei der Adaptive Insights-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)