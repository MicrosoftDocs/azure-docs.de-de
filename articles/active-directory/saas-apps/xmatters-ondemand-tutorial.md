---
title: 'Tutorial: Azure Active Directory-Integration mit xMatters OnDemand | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und xMatters OnDemand konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 4217f85394d66c3da116cf92767ad1da8ad7e799
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920191"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Azure Active Directory-Integration mit xMatters OnDemand

In diesem Tutorial erfahren Sie, wie Sie xMatters OnDemand in Azure Active Directory (Azure AD) integrieren.
Die Integration von xMatters OnDemand in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf xMatters OnDemand hat.
* Sie können Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei xMatters OnDemand anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit xMatters OnDemand konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* xMatters OnDemand-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* xMatters OnDemand unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Hinzufügen von xMatters OnDemand aus dem Katalog

Zum Konfigurieren der Integration von xMatters OnDemand in Azure AD müssen Sie xMatters OnDemand aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um xMatters OnDemand aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **xMatters OnDemand**  in das Suchfeld ein, wählen Sie im Ergebnisbereich den Eintrag **xMatters OnDemand**  aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![xMatters OnDemand in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie anhand einer Testbenutzerin namens **Britta Simon** das einmalige Anmelden von Azure AD bei xMatters OnDemand.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in xMatters OnDemand eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei xMatters OnDemand müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für xMatters OnDemand ](#configure-xmatters-ondemand-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines xMatters OnDemand-Testbenutzers](#create-xmatters-ondemand-test-user)** , um ein Pendant von Britta Simon in xMatters OnDemand zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei xMatters OnDemand die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **xMatters OnDemand** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für xMatters OnDemand](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Wenden Sie sich an das [Kundensupportteam von xMatters OnDemand](https://www.xmatters.com/company/contact-us/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

    > [!IMPORTANT]
    > Sie müssen das Zertifikat an das [xMatters OnDemand-Supportteam](https://www.xmatters.com/company/contact-us/) weiterleiten. Das Zertifikat muss vom xMatters-Supportteam hochgeladen werden, bevor Sie die Konfiguration der einmaligen Anmeldung abschließen können.

6. Kopieren Sie im Abschnitt **xMatters OnDemand einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für xMatters OnDemand

1. Melden Sie sich in einem anderen Webbrowserfenster bei der xMatters OnDemand-Unternehmenswebsite als Administrator an.

2. Klicken Sie in der Symbolleiste oben auf **Administrator**, und klicken Sie dann in der Navigationsleiste auf der linken Seite auf **Unternehmensdetails**.

    ![Admin](./media/xmatters-ondemand-tutorial/IC776795.png "Admin")

3. Führen Sie auf der Seite **SAML-Konfiguration** die folgenden Schritte aus:

    ![SAML-Konfiguration](./media/xmatters-ondemand-tutorial/IC776796.png "SAML-Konfiguration")

    a. Wählen Sie **SAML aktivieren**.

    b. Fügen Sie in das Textfeld **Identity Provider ID** (Identitätsanbieter-ID) den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Single Sign On URL** (URL für einmaliges Anmelden) den Wert von **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie in das Textfeld **Single Logout URL** (URL für einmaliges Abmelden) den Wert von **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Klicken Sie auf der Seite „Unternehmensdetails“ oben auf **Speichern**.

    ![Unternehmensdetails](./media/xmatters-ondemand-tutorial/IC776797.png "Unternehmensdetails")

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen brittasimon@yourcompanydomain.extension ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf xMatters OnDemand gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, wählen Sie **Alle Anwendungen** aus, und wählen Sie dann **xMatters OnDemand** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **xMatters OnDemand** aus.

    ![xMatters OnDemand-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-xmatters-ondemand-test-user"></a>Erstellen eines xMatters OnDemand-Testbenutzers

In diesem Abschnitt wird in xMatters OnDemand eine Benutzerin namens Britta Simon erstellt.

**Wenn Sie einen Benutzer manuell erstellen möchten, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem **XMatters OnDemand**-Mandanten an.

2. Klicken Sie auf die Registerkarte **Benutzer** und dann auf **Benutzer hinzufügen**.

    ![Benutzer](./media/xmatters-ondemand-tutorial/IC781048.png "Benutzer")

3. Führen Sie im Abschnitt **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/xmatters-ondemand-tutorial/IC781049.png "Benutzer hinzufügen")

    a. Wählen Sie **Aktiv**.

    b. Geben Sie im Textfeld **Benutzer-ID** die Benutzer-ID des Benutzers ein, z.B. Brittasimon@contoso.com.

    c. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers ein, z.B. Britta.

    d. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein, z.B. Simon.

    e. Geben Sie im Textfeld **Website** die gültige Website eines gültigen Azure AD-Kontos ein, das Sie bereitstellen möchten.

    f. Klicken Sie auf **Speichern**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „xMatters OnDemand“ klicken, sollten Sie automatisch bei Ihrer xMatters OnDemand-Anwendung angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

