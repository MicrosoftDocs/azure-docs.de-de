---
title: 'Tutorial: Azure Active Directory-Integration mit FreshDesk | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Freshdesk konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4bdeecc5682eb09ac0a65d3834ad1770258c4e0
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65898446"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Azure Active Directory-Integration mit FreshDesk

In diesem Tutorial erfahren Sie, wie Sie Freshdesk in Azure Active Directory (Azure AD) integrieren.
Die Integration von Freshdesk in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf FreshDesk hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei FreshDesk angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Freshdesk konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* FreshDesk-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* FreshDesk unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-freshdesk-from-the-gallery"></a>Hinzufügen von Freshdesk aus dem Katalog

Zum Konfigurieren der Integration von Freshdesk in Azure AD müssen Sie Freshdesk aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Freshdesk aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **FreshDesk** ein, wählen Sie im Ergebnisbereich **FreshDesk** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![FreshDesk in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei FreshDesk basierend auf einer Testbenutzerin mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in FreshDesk eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Freshdesk sind die folgenden Bausteine erforderlich:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für FreshDesk](#configure-freshdesk-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines FreshDesk-Testbenutzers](#create-freshdesk-test-user)**, um eine Entsprechung von Britta Simon in FreshDesk zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in FreshDesk die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **FreshDesk** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für FreshDesk](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im Format `https://<tenant-name>.freshdesk.com` oder einen anderen von FreshDesk vorgeschlagenen Wert an.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im Format `https://<tenant-name>.freshdesk.com` oder einen anderen von FreshDesk vorgeschlagenen Wert an.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam von FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die FreshDesk-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen: Der Standardwert von **Eindeutige Benutzer-ID** lautet **user.userprincipalname**, aber FreshDesk erwartet, dass dieser Wert der E-Mail-Adresse des Benutzers zugeordnet ist. Hierfür können Sie das **user.mail**-Attribut aus der Liste verwenden oder den entsprechenden Attributwert gemäß der Konfiguration in Ihrer Organisation angeben. 

    ![image](common/edit-attribute.png)

6. Bearbeiten Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die Ansprüche mithilfe des Symbols zum **Bearbeiten**, oder fügen Sie die Ansprüche über **Neuen Anspruch hinzufügen** hinzu, um das SAML-Tokenattribut wie in der obigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus:
    
    | NAME | Quellattribut |
    | ---------------| --------------- |
    | Eindeutige Benutzer-ID | user.mail |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

8. Öffnen Sie eine **Eingabeaufforderung**, und führen Sie die folgenden Befehle aus:

    a. Geben Sie in der Eingabeaufforderung den Wert `certutil.exe -dump FreshDesk.cer` ein.

    > [!NOTE]
    > In diesem Fall ist **FreshDesk.cer** das Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben.

    b. Kopieren Sie den Wert für **Cert Hash(sha256)**, und fügen Sie ihn in Notepad ein. 

9. Kopieren Sie im Abschnitt **FreshDesk einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-freshdesk-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für FreshDesk

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Freshdesk-Unternehmenswebsite als Administrator an.

2. Wählen Sie das Symbol **Einstellungen** aus, und führen Sie im Abschnitt **Sicherheit** die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/freshdesk-tutorial/IC776770.png "Einmaliges Anmelden")
  
    a. Wählen Sie für **Einmaliges Anmelden (SSO)** die Einstellung **Ein** aus.

    b. Wählen Sie **SAML-SSO**aus.

    c. Fügen Sie im Textfeld **SAML-Anmelde-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie im Textfeld **Abmelde-URL** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Fügen Sie im Textfeld **Fingerabdruck des Sicherheitszertifikats** den Wert für **Cert Hash(sha256)** ein, den Sie zuvor erhalten haben.
  
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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf FreshDesk gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **FreshDesk**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **FreshDesk** ein, und wählen Sie den Eintrag aus.

    ![FreshDesk-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-freshdesk-test-user"></a>Erstellen eines FreshDesk-Testbenutzers

Damit sich Azure AD-Benutzer bei Freshdesk anmelden können, müssen sie in Freshdesk bereitgestellt werden.  
Im Fall von Freshdesk ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen von Benutzerkonten die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem **Freshdesk** -Mandanten an.

2. Klicken Sie oben im Menü auf **Administrator**.

    ![Admin](./media/freshdesk-tutorial/IC776772.png "Admin")

3. Klicken Sie auf der Registerkarte **Allgemeine Einstellungen** auf **Agents**.
  
    ![Agents](./media/freshdesk-tutorial/IC776773.png "Agents")

4. Klicken Sie auf **Neuer Agent**.

    ![Neuer Agent](./media/freshdesk-tutorial/IC776774.png "Neuer Agent")

5. Führen Sie im Dialogfeld „Agent-Informationen“ die folgenden Schritte aus:

    ![Agent-Informationen](./media/freshdesk-tutorial/IC776775.png "Agent-Informationen")

    a. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse des Azure AD-Kontos ein, das Sie bereitstellen möchten.

    b. Geben Sie im Textfeld **Vollständiger Name** den Namen des Azure AD-Kontos ein, das Sie bereitstellen möchten.

    c. Geben Sie im Textfeld **Titel** den Titel des Azure AD-Kontos ein, das Sie bereitstellen möchten.

    d. Klicken Sie auf **Speichern**.

    >[!NOTE]
    >Der Besitzer des Azure AD-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.
    >
    >[!NOTE]
    >Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von FreshDesk-Benutzerkonten oder mithilfe der von FreshDesk bereitgestellten APIs in FreshDesk bereitstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „FreshDesk“ klicken, sollten Sie automatisch bei Ihrer FreshDesk-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

