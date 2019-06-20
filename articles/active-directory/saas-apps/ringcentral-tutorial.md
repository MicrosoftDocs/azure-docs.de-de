---
title: 'Tutorial: Azure Active Directory-Integration mit RingCentral | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und RingCentral konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: jeedes
ms.openlocfilehash: 2e4f45deb8c63640a328e38cebc2ecbe67233c3b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66143137"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Tutorial: Integrieren von RingCentral in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie RingCentral in Azure Active Directory (Azure AD) integrieren. Bei der Integration von RingCentral in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf RingCentral hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei RingCentral anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie kein Abonnement besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige kostenlose Testversion erhalten.
* RingCentral-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. RingCentral unterstützt **IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-ringcentral-from-the-gallery"></a>Hinzufügen von RingCentral aus dem Katalog

Zum Konfigurieren der Integration von RingCentral in Azure AD müssen Sie RingCentral aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **RingCentral** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **RingCentral** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit RingCentral mithilfe eines Testbenutzers mit dem Namen **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in RingCentral eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit RingCentral müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
2. **[Konfigurieren des einmaligen Anmeldens für RingCentral](#configure-ringcentral-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines RingCentral-Testbenutzers](#create-ringcentral-test-user)** , um eine Entsprechung von Britta Simon in RingCentral zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **RingCentral** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    1. Klicken Sie auf **Metadatendatei hochladen**.
    1. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.
    1. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Abschnitt **Grundlegende SAML-Konfiguration** automatisch eingefügt.

    > [!Note]
    > Sie können die **Dienstanbieter-Metadatendatei** auf der SSO-Konfigurationsseite für RingCentral abrufen, die im weiteren Verlauf des Tutorials erläutert wird.

1. Geben Sie Werte in die folgenden Felder ein, wenn Sie keine **Dienstanbieter-Metadatendatei** besitzen:

    a. Geben Sie im Textfeld **Bezeichner** eine URL ein:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Geben Sie im Textfeld **Antwort-URL** eine URL folgendermaßen ein:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="configure-ringcentral-sso"></a>Konfigurieren des einmaligen Anmeldens für RingCentral

1. Melden Sie sich in einem anderen Webbrowserfenster als Sicherheitsadministrator bei RingCentral an.

1. Klicken Sie oben auf **Tools**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Navigieren Sie zu **Einmaliges Anmelden**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Klicken Sie auf der Seite **Einmaliges Anmelden** im Abschnitt **SSO-Konfiguration** unter **Schritt 1** auf **Bearbeiten** und führen Sie die folgenden Schritte aus:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Führen Sie auf der Seite **Einmaliges Anmelden einrichten** die folgenden Schritte aus:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Klicken Sie zum Hochladen der Metadatendatei, die Sie aus dem Azure-Portal heruntergeladen haben, auf **Durchsuchen**.

    b. Nach dem Upload der Metadatendatei werden die Werte im Abschnitt **Allgemeine SSO-Informationen** automatisch ausgefüllt.

    c. Wählen Sie unter dem Abschnitt **Attributzuordnung** für **E-Mail-Attribut zuordnen zu** die Adresse `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` aus.

    d. Klicken Sie auf **Speichern**.

    e. Klicken Sie in **Schritt 2** auf **Herunterladen**, um die **Dienstanbieter-Metadatendatei** herunterzuladen. Laden Sie die Datei im Abschnitt **Grundlegende SAML-Konfiguration** hoch, damit die Werte **Bezeichner** und **Antwort-URL** im Azure-Portal automatisch ausgefüllt werden.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Navigieren Sie auf der gleichen Seite zum Abschnitt **SSO aktivieren**, und führen Sie die folgenden Schritte aus:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Wählen Sie **SSO-Dienst aktivieren** aus.

    * Wählen Sie **Benutzeranmeldung mit SSO- oder RingCentral-Anmeldeinformationen zulassen** aus.

    * Klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen Britta Simon.

1. Wählen Sie im linken Bereich des Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `Britta Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `BrittaSimon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf RingCentral gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **RingCentral** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie anschließend im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-ringcentral-test-user"></a>Erstellen eines RingCentral-Testbenutzers

In diesem Abschnitt erstellen Sie in RingCentral einen Benutzer namens Britta Simon. Wenden Sie sich an das  [Supportteam für den RingCentral-Client](https://success.ringcentral.com/RCContactSupp), um die Benutzer auf der RingCentral-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich die Kachel „RingCentral“ auswählen, sollten Sie automatisch bei der RingCentral-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
