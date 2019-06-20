---
title: 'Tutorial: Azure Active Directory-Integration mit G Suite | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und G Suite konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7462536443b2be23eef0f8b4c6f6b6412fb7032
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65898748"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Tutorial: Azure Active Directory-Integration mit G Suite

In diesem Tutorial erfahren Sie, wie Sie G Suite in Azure Active Directory (Azure AD) integrieren.
Die Integration von G Suite in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf G Suite hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei G Suite anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit G Suite konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein G Suite-Abonnement, für das einmaliges Anmelden aktiviert ist
- Eine Google Apps-Abonnement oder ein Google Cloud Platform-Abonnement.

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden. Dieses Dokument wurde unter Verwendung der neuen Benutzeroberfläche für das einmalige Anmelden (Single-Sign-On, SSO) erstellt. Wenn Sie noch die alte Benutzeroberfläche verwenden, sieht das Setup anders aus. Sie können die neue Benutzeroberfläche in den SSO-Einstellungen der G Suite-Anwendung aktivieren. Wechseln Sie zu **Azure AD, Unternehmensanwendungen**, wählen Sie **G Suite** und **Einmaliges Anmelden** aus, und klicken Sie dann auf **Neue Benutzeroberfläche ausprobieren**.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

1. **F: Unterstützt diese Integration die Google Cloud Platform-SSO-Integration in Azure AD?**

    A: Ja. Für Google Cloud Platform und Google Apps wird dieselbe Authentifizierungsplattform verwendet. Um die Integration von GCP vorzunehmen, müssen Sie daher SSO mit Google Apps konfigurieren.

2. **F: Sind Chromebooks und andere Chrome-Geräte mit dem einmaligen Anmelden in Azure AD kompatibel?**
  
    A: Ja, Benutzer können sich mit ihren Azure AD-Anmeldeinformationen bei ihren Chromebook-Geräten anmelden. In diesem [G Suite-Supportartikel](https://support.google.com/chrome/a/answer/6060880) finden Sie Informationen darüber, warum Benutzer möglicherweise zweimal zum Eingeben der Anmeldeinformationen aufgefordert werden.

3. **F: Wenn ich einmaliges Anmelden aktiviere, können Benutzer dann ihre Azure AD-Anmeldeinformationen zum Anmelden bei Google-Produkten wie Google Classroom, GMail, Google Drive, YouTube usw. verwenden?**

    A: Ja, je nachdem, [welche G Suite-Komponente](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) Sie für Ihre Organisation aktivieren oder deaktivieren.

4. **F: Kann ich einmaliges Anmelden nur für einen Teil meiner G Suite-Benutzer aktivieren?**

    A: Nein. Wenn Sie einmaliges Anmelden aktivieren, müssen sich sofort sämtliche Ihrer G Suite-Benutzer mit ihren Azure AD-Anmeldeinformationen authentifizieren. Da G Suite die Verwendung mehrerer Identitätsanbieter nicht unterstützt, kann entweder Azure AD oder Google als Identitätsanbieter für Ihre G Suite-Umgebung fungieren – aber nicht beide gleichzeitig.

5. **F: Wenn ein Benutzer über Windows angemeldet ist, wird er dann automatisch bei G Suite authentifiziert, ohne dass er zur Eingabe eines Kennworts aufgefordert wird?**

    A: Es gibt zwei Optionen zur Unterstützung dieses Szenarios. Benutzer können sich über die [Azure Active Directory-Einbindung](../device-management-introduction.md)bei Windows 10-Geräten anmelden. Alternativ dazu können Benutzer sich bei Windows-Geräten anmelden, die über eine Domäne mit einem lokalen Active Directory verbunden sind, das für einmaliges Anmelden bei Azure AD über eine [AD FS](../hybrid/plan-connect-user-signin.md) -Bereitstellung (Active Directory-Verbunddienste) aktiviert wurde. Für beide Optionen müssen Sie die Schritte im folgenden Tutorial ausführen, um einmaliges Anmelden zwischen Azure AD und G Suite zu aktivieren.

6. **F: Was muss ich tun, wenn ich die Fehlermeldung „Ungültige E-Mail-Adresse“ erhalte?**

    A: Für dieses Setup ist das E-Mail-Attribut erforderlich, damit sich die Benutzer anmelden können. Dieses Attribut kann nicht manuell festgelegt werden.

    Das E-Mail-Attribut wird für alle Benutzer mit einer gültigen Exchange-Lizenz automatisch aufgefüllt. Wenn der Benutzer nicht für E-Mail aktiviert wurde, erhalten Sie diesen Fehler, weil die Anwendung dieses Attribut zur Zugriffserteilung benötigt.

    Wechseln Sie unter Verwendung eines Administratorkontos zu portal.office.com, klicken Sie dann im Admin-Center auf „Abrechnung“ > „Abonnements“, und wählen Sie Ihr Office 365-Abonnement aus. Klicken Sie anschließend auf „Benutzern zuweisen“, wählen Sie die Benutzer aus, deren Abonnement Sie überprüfen möchten, und klicken Sie dann im Bereich rechts auf „Lizenzen bearbeiten“.

    Nachdem die O365-Lizenz zugewiesen wurde, kann es einige Minuten dauern, bis sie wirksam wird. Anschließend wird das Attribut „user.mail“ automatisch aufgefüllt, und das Problem sollte behoben sein.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* G Suite unterstützt das **SP-initiierte** einmalige Anmelden.
* G Suite unterstützt die **[automatische Benutzerbereitstellung](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)** .

## <a name="adding-g-suite-from-the-gallery"></a>Hinzufügen von G Suite aus dem Katalog

Zum Konfigurieren der Integration von G Suite in Azure AD müssen Sie G Suite aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um G Suite aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **G Suite** in das Suchfeld ein, wählen Sie im Ergebnisbereich den Eintrag **G Suite** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![G Suite in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit G Suite mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in G Suite eingerichtet werden.

Um einmaliges Anmelden in Azure AD mit G Suite zu konfigurieren und zu testen, müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für G Suite](#configure-g-suite-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines G Suite-Testbenutzers](#create-g-suite-test-user)** , um ein Pendant von Britta Simon in G Suite zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Um einmaliges Anmelden von Azure AD mit G Suite zu konfigurieren, führen Sie die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **G Suite** **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Konfiguration für **Gmail** vornehmen möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für G Suite](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`.

    b. Geben Sie im Textfeld **Identifier** (Bezeichner) eine URL nach folgendem Muster ein:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam für G Suite](https://www.google.com/contact/), um diese Werte zu erhalten.

5. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Konfiguration für **Google Cloud Platform** vornehmen möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für G Suite](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`.

    b. Geben Sie im Textfeld **Identifier** (Bezeichner) eine URL nach folgendem Muster ein:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam für G Suite](https://www.google.com/contact/), um diese Werte zu erhalten.

6. Die G Suite-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen: Der Standardwert von **Eindeutige Benutzer-ID** lautet **user.userprincipalname**, G Suite erwartet jedoch, dass dieser Wert der E-Mail-Adresse des Benutzers zugeordnet ist. Hierfür können Sie das **user.mail**-Attribut aus der Liste verwenden oder den entsprechenden Attributwert gemäß der Konfiguration in Ihrer Organisation angeben.

    ![image](common/edit-attribute.png)

7. Bearbeiten Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die Ansprüche mithilfe des Symbols zum **Bearbeiten**, oder fügen Sie die Ansprüche über **Neuen Anspruch hinzufügen** hinzu, um das SAML-Tokenattribut wie in der obigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus:

    | NAME | Quellattribut |
    | ---------------| --------------- |
    | Eindeutige Benutzer-ID | User.mail |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

9. Kopieren Sie im Abschnitt **G Suite einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-g-suite-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für G Suite

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei der [G Suite-Verwaltungskonsole](https://admin.google.com/) mit Ihrem Administratorkonto an.

2. Klicken Sie auf **Sicherheit**. Wenn der Link nicht angezeigt wird, kann er unter dem Menü **Weitere Steuerelemente** im unteren Bereich des Bildschirms versteckt sein.

    ![Klicken Sie auf "Sicherheit".][10]

3. Klicken Sie auf der Seite **Sicherheit** auf **Einmaliges Anmelden (SSO) einrichten**.

    ![Klicken Sie auf "SSO".][11]

4. Führen Sie die folgenden Konfigurationsänderungen durch:

    ![Konfigurieren von SSO][12]

    a. Wählen Sie **Einmaliges Anmelden mit externem Identitätsanbieter einrichten**.

    b. Fügen Sie in G Suite im Feld **URL der Anmeldeseite** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in G Suite im Feld **URL der Abmeldeseite** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie in G Suite im Feld **Kennwort-URL ändern** den Wert von **Kennwort-URL ändern** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Laden Sie in G Suite für das **Verifizierungszertifikat** das Zertifikat hoch, das Sie aus dem Azure-Portal heruntergeladen haben.

    f. Aktivieren Sie **Domainspezifischen Aussteller verwenden**.

    g. Klicken Sie auf **Änderungen speichern**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf G Suite gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **G Suite** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **G Suite** ein, und wählen Sie den Eintrag aus.

    ![G Suite-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-g-suite-test-user"></a>Erstellen eines G-Suite-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in G Suite Software. G Suite unterstützt die automatische Bereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt keine Aktion an. Wenn ein Benutzer nicht bereits in G Suite-Software vorhanden ist, wird ein neuer Benutzer erstellt, sobald Sie versuchen, auf G Suite-Software zuzugreifen.

> [!NOTE]
> Stellen Sie sicher, dass Ihr Benutzer bereits in G Suite vorhanden ist, wenn die Bereitstellung in Azure AD vor dem Testen des einmaligen Anmeldens nicht aktiviert wurde.

> [!NOTE]
> Setzen Sie sich mit dem [Supportteam von Google](https://www.google.com/contact/) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „G Suite“ klicken, sollten Sie automatisch bei der G Suite-Anwendung angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Konfigurieren der Benutzerbereitstellung](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
