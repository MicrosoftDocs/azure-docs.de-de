---
title: 'Tutorial: Azure Active Directory-Integration mit Printix | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Printix konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: dfde9bbbeb7f6b349ecbdc4c2da605d39a0708da
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357877"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Tutorial: Azure Active Directory-Integration mit Printix

In diesem Tutorial erfahren Sie, wie Sie Printix in Azure Active Directory (Azure AD) integrieren.

Die Integration von Printix in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Printix hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Printix anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Printix konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Printix-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Printix aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-printix-from-the-gallery"></a>Hinzufügen von Printix aus dem Katalog
Zum Konfigurieren der Integration von Printix in Azure AD müssen Sie Printix aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Printix aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Der Screenshot zeigt das Azure-Portal mit „Unternehmensanwendungen“, ausgewählt unter „Verwalten“, mit ausgewähltem „Alle Anwendungen“.][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Der Screenshot zeigt die ausgewählte Schaltfläche „Neue Anwendung“.][3]

1. Geben Sie im Suchfeld **Printix** ein.

    ![Screenshot, der die Suche nach Printix im Dialogfeld „Aus Katalog hinzufügen“ zeigt.](./media/printix-tutorial/tutorial_printix_search.png)

1. Wählen Sie im Ergebnisbereich die Option **Printix** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Screenshot mit ausgewählter Option für Printix.](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Printix basierend auf einem Testbenutzer mit dem Namen Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Printix als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Printix muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in Printix den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit Printix müssen Sie die folgenden Bausteine ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines Printix-Testbenutzers](#creating-a-printix-test-user)** , um eine Entsprechung von Britta Simon in Printix zu erhalten, die mit der Darstellung dieses Benutzers in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt ermöglichen Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Printix-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens in Azure AD mit Printix die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Printix** auf **Einmaliges Anmelden**.

    ![Der Screenshot zeigt „Einmaliges Anmelden“, das im Azure-Portal unter „Verwalten“ ausgewählt ist.][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Der Screenshot zeigt den ausgewählten Modus „SAML-basierte Anmeldung“.](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. Führen Sie auf der Seite **Domäne und URLs für Printix** die folgenden Schritte aus:

    ![Screenshot mit dem Bereich für die Domäne und die URL für Printix, in dem Sie eine URL zum Anmelden eingeben können.](./media/printix-tutorial/tutorial_printix_url.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.printix.net`.

    > [!NOTE] 
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Clientsupportteam von Printix](mailto:support@printix.net), um den Wert zu erhalten. 
 
1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Metadaten-XML** , und speichern Sie die Metadatendatei dann auf Ihrem Computer.

    ![Der Screenshot zeigt den Bereich „SAML-Signaturzertifikat“, in dem Sie ein Zertifikat herunterladen können.](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Screenshot der Schaltfläche „Save“](./media/printix-tutorial/tutorial_general_400.png)

1. Melden Sie sich bei Ihrem Printix-Mandanten als Administrator an.

1. Klicken Sie im Menü am oberen Rand auf das Symbol in der oberen rechten Ecke, und wählen Sie **Authentifizierung**.
   
    ![Screenshot, der die im Menü ausgewählte Option „Authentifizierung“ zeigt.](./media/printix-tutorial/tutorial_printix_06.png)

1. Wählen Sie auf der Registerkarte **Setup** die Option **Azure-/Office 365-Authentifizierung aktivieren** aus.
   
    ![Screenshot der Seite „Printix.net“, auf der Sie die Aktivierung der Azure/Office 365-Authentifizierung auswählen können.](./media/printix-tutorial/tutorial_printix_07.png)

1. Geben Sie auf der Registerkarte **Azure** im Textfeld **Verbundmetadaten-Dokument** die Verbundmetadaten-URL ein. 

    Fügen Sie die Metadaten-XML-Datei, die Sie von Azure AD heruntergeladen haben, an eine E-Mail an, und senden Sie sie an das [Printix-Supportteam](mailto:support@printix.net). Das Team lädt die XML-Datei hoch und stellt eine Verbundmetadaten-URL bereit.
   
    ![Screenshot der Seite „Printix.net“, auf der Sie ein Verbundmetadaten-Dokument angeben können.](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Klicken Sie auf die Schaltfläche **Testen** und dann auf die Schaltfläche **OK** , wenn der Test erfolgreich war.
   
     Nachdem Sie auf die Schaltfläche **Testen** geklickt haben, wird die Azure Active Directory-Seite angezeigt. Eine Meldung der Art „Der Test war erfolgreich“ bedeutet in diesem Fall, dass nach dem Eingeben der Anmeldeinformationen für Ihr Azure-Testkonto die Meldung „Einstellungen getestet – OK“ angezeigt wird. Klicken Sie anschließend auf die Schaltfläche **OK**.
   
    ![Screenshot mit den Ergebnissen des Tests.](./media/printix-tutorial/tutorial_printix_09.png)

1. Klicken Sie auf der Seite **Authentifizierung** auf die Schaltfläche **Speichern**.


> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden** , und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Screenshot mit einem Namen und einem Benutzernamen, die erstellt werden sollen.](./media/printix-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen** , und klicken Sie auf **Alle Benutzer** , um die Liste der Benutzer anzuzeigen.
    
    ![Der Screenshot zeigt das Symbol „Azure AD“ im Azure-Portal.](./media/printix-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen** , um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Der Screenshot zeigt „Benutzer und Gruppen“, das im Menü „Verwalten“ ausgewählt wurde. „Alle Benutzer“ ist ausgewählt.](./media/printix-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Screenshot des Dialogfelds „Benutzer“, in dem Sie die beschriebenen Werte eingeben können.](./media/printix-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Erstellen**.
 
### <a name="creating-a-printix-test-user"></a>Erstellen eines Printix-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Printix. Printix unterstützt die Just-in-Time-Bereitstellung, die standardmäßig aktiviert ist.

Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Wenn noch kein Benutzer vorhanden ist, wird beim Zugreifen auf Printix ein neuer Benutzer erstellt. 

> [!NOTE]
> Setzen Sie sich mit dem [Supportteam von Printix](mailto:support@printix.net) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon das einmalige Anmelden bei Azure, indem Sie ihr Zugriff auf Printix gewähren.

![Screenshot eines Benutzers mit Standardzugriff.][200] 

**Um Britta Simon Printix zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen** , und klicken Sie auf **Alle Anwendungen**.

    ![Screenshot, der die unter „Verwalten“ ausgewählte Option „Unternehmensanwendungen“ anzeigt. „Alle Anwendungen“ ist ausgewählt.][201] 

1. Wählen Sie in der Anwendungsliste **Printix** aus.

    ![Screenshot mit der Anwendungsliste, aus der Sie Printix auswählen können.](./media/printix-tutorial/tutorial_printix_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Screenshot, der die im Menü „Verwalten“ ausgewählte Option „Benutzer und Gruppen“ anzeigt.][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Screenshot, der die Schaltfläche „Hinzufügen“ und die Seite „Zuweisung hinzufügen“ zeigt, auf der Sie „Benutzer und Gruppen“ auswählen können.][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Printix“ klicken, sollten Sie automatisch bei Ihrer Printix-Anwendung angemeldet werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

