---
title: 'Tutorial: Azure Active Directory-Integration mit Ceridian Dayforce HCM | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Ceridian Dayforce HCM konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: bd7213f31eb737bedd64d627141c2576fdadb79a
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456376"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: Azure Active Directory-Integration mit Ceridian Dayforce HCM

In diesem Tutorial erfahren Sie, wie Sie Ceridian Dayforce HCM in Azure Active Directory (Azure AD) integrieren.
Die Integration von Ceridian Dayforce HCM in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Ceridian Dayforce HCM hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Ceridian Dayforce HCM anzumelden (einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Ceridian Dayforce HCM konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Ceridian Dayforce HCM-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Ceridian Dayforce HCM unterstützt **SP** -initiiertes einmaliges Anmelden.

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Hinzufügen von Ceridian Dayforce HCM aus dem Katalog

Zum Konfigurieren der Integration von Ceridian Dayforce HCM in Azure AD müssen Sie Ceridian Dayforce HCM aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie zum Hinzufügen von Ceridian Dayforce HCM aus dem Katalog die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Ceridian Dayforce HCM** ein, und klicken Sie auf **Ceridian Dayforce HCM** in der Ergebnisliste. Klicken Sie dann auf die Schaltfläche **Add** (Hinzufügen), um die Anwendung hinzuzufügen.

     ![Ceridian Dayforce HCM in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Dieser Abschnitt veranschaulicht anhand einer Testbenutzerin namens **Britta Simon** , wie das einmalige Anmelden von Azure AD in Ceridian Dayforce HCM konfiguriert und getestet wird.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Ceridian Dayforce HCM eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Ceridian Dayforce HCM müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Ceridian Dayforce HCM](#configure-ceridian-dayforce-hcm-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen Sie einen Ceridian Dayforce HCM-Testbenutzer](#create-ceridian-dayforce-hcm-test-user)** , um eine Entsprechung von Britta Simon in Ceridian Dayforce HCM zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Ceridian Dayforce HCM die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Ceridian Dayforce HCM** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Ceridian Dayforce HCM](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die Benutzer zum Anmelden bei Ihrer Ceridian Dayforce HCM-Anwendung verwenden.

    | Environment | URL |
    | :-- | :-- |
    | Für die Bereitstellung | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Zu Testzwecken | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Geben Sie im Textfeld **Identifier** (Bezeichner) eine URL nach folgendem Muster ein:

    | Environment | URL |
    | :-- | :-- |
    | Für die Bereitstellung | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Zu Testzwecken | `https://fs-test.dayforcehcm.com/sp` |

    c. Geben Sie im Textfeld **Antwort-URL** die URL ein, die von Azure AD zum Senden der Antwort verwendet wird.

    | Environment | URL |
    | :-- | :-- |
    | Für die Bereitstellung | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Zu Testzwecken | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Wenden Sie sich an das [Ceridian Dayforce HCM Client support team (Ceridian Dayforce HCM Client-Supportteam)](https://www.ceridian.com/support), um diese Werte abzurufen. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die Ceridian Dayforce HCM-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten** , um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Screenshot der Benutzerattribute mit ausgewähltem Symbol „Bearbeiten“](common/edit-attribute.png)

6. Konfigurieren Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut wie in der obigen Abbildung gezeigt, und führen Sie die folgenden Schritte aus:
    
    | Name | Quellattribut|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen** , um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Screenshot der Benutzeransprüche mit der Option „Neuen Anspruch hinzufügen“](common/new-save-attribute.png)

    ![Screenshot des Dialogfelds „Benutzeransprüche verwalten“, in dem Sie die hier beschriebenen Werte eingeben können](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Wählen Sie in der Liste **Quellattribut** das Benutzerattribut aus, das Sie für Ihre Implementierung verwenden möchten. Wenn Sie z.B. den Wert „EmployeeID“ als eindeutige Benutzer-ID verwenden möchten und den Attributwert in „ExtensionAttribute2“ gespeichert haben, wählen Sie „user.extensionattribute2“ aus.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um das Ihrer Anforderung entsprechende **Metadaten-XML** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

8. Kopieren Sie im Abschnitt **Ceridian Dayforce HCM einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-ceridian-dayforce-hcm-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Ceridian Dayforce HCM

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Ceridian Dayforce HCM** müssen Sie die heruntergeladene **Metadaten-XML** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von Ceridian Dayforce HCM](https://www.ceridian.com/support) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung**.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Ceridian Dayforce HCM gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** , **Alle Anwendungen** und dann **Ceridian Dayforce HCM** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Ceridian Dayforce HCM** aus.

    ![Der Link „Ceridian Dayforce HCM“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Erstellen einen Ceridian Dayforce HCM-Testbenutzers

In diesem Abschnitt erstellen Sie in Ceridian Dayforce HCM eine Benutzerin namens Britta Simon. Wenden Sie sich an das [Ceridian Dayforce HCM-Supportteam](https://www.ceridian.com/support), um die Benutzer auf der Ceridian Dayforce HCM-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Ceridian Dayforce HCM“ klicken, sollten Sie automatisch bei Ihrer Ceridian Dayforce HCM-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)