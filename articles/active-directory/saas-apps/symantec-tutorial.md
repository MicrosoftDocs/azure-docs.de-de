---
title: 'Tutorial: Azure Active Directory-Integration mit Symantec Web Security Service (WSS) | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Symantec Web Security Service (WSS) konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 230f7fd9c62f657ce8ab893db2256808dce9a7ba
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518363"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Tutorial: Azure Active Directory-Integration mit Symantec Web Security Service (WSS)

In diesem Tutorial erfahren Sie, wie Sie Ihr Symantec Web Security Service-Konto (WSS) mit Ihrem Azure AD-Konto (Azure Active Directory) integrieren, sodass WSS einen Endbenutzer, der in Azure AD mithilfe der SAML-Authentifizierung bereitgestellt wurde, authentifizieren und Richtlinienregeln auf Benutzer- oder Gruppenebene erzwingen kann.

Die Integration von Symantec Web Security Service (WSS) in Azure AD bietet die folgenden Vorteile:

- Sie verwalten alle Benutzer und Gruppen, die vom WSS-Konto verwendet werden, über das Azure AD-Portal.

- Sie erlauben den Endbenutzern die Authentifizierung bei WSS mit ihren Azure AD-Anmeldeinformationen.

- Sie aktivieren die Erzwingung von Richtlinienregeln auf Benutzer- und Gruppenebene, die in Ihrem WSS-Konto definiert sind.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration mit Symantec Web Security Service (WSS) benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Symantec Web Security Service (WSS)-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Symantec Web Security Service (WSS) unterstützt **IDP** -initiiertes einmaliges Anmelden.

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Hinzufügen von Symantec Web Security Service (WSS) aus dem Katalog

Zum Konfigurieren der Integration von Symantec Web Security Service (WSS) in Azure AD müssen Sie Symantec Web Security Service (WSS) aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um Symantec Web Security Service (WSS) über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Symantec Web Security Service (WSS)** ein, wählen Sie im Ergebnisbereich **Symantec Web Security Service (WSS)** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![Symantec Web Security Service (WSS) in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Symantec Web Security Service (WSS) basierend auf einer Testbenutzerin mit dem Namen **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Symantec Web Security Service (WSS) eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Symantec Web Security Service (WSS) müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **Konfigurieren des einmaligen Anmeldens für Symantec Web Security Service (WSS)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Symantec Web Security Service (WSS)-Testbenutzers](#create-symantec-web-security-service-wss-test-user)** , um eine Entsprechung von Britta Simon in Symantec Web Security Service (WSS) zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Symantec Web Security Service (WSS) die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Symantec Web Security Service (WSS)** die Option **Einmaliges Anmelden** .

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Symantec Web Security Service (WSS)](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL ein: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL ein: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`.

    > [!NOTE]
    > Wenden Sie sich an das [Clientsupportteam von Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us), wenn die Werte für **Bezeichner** und **Antwort-URL** nicht funktionieren. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Symantec Web Security Service (WSS)

Weitere Informationen zum Konfigurieren des einmaligen Anmeldens auf Seiten von Symantec Web Security Service (WSS) finden Sie in der WSS-Onlinedokumentation. Die heruntergeladene **Verbundmetadaten-XML** muss in das WSS-Portal importiert werden. Wenden Sie sich an das [Supportteam von Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us), wenn Sie Unterstützung bei der Konfiguration im WSS-Portal benötigen.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung** .  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Symantec Web Security Service (WSS) gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **Symantec Web Security Service (WSS)** .

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in Anwendungsliste **Symantec Web Security Service (WSS)** ein, und wählen Sie den Eintrag aus.

    ![Symantec Web Security Service-Link (WSS) in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-symantec-web-security-service-wss-test-user"></a>Erstellen eines Symantec Web Security Service (WSS)-Testbenutzers

In diesem Abschnitt erstellen Sie in Symantec Web Security Service (WSS) einen Benutzer namens Britta Simon. Der entsprechende Endbenutzername kann manuell im WSS-Portal erstellt werden. Sie können aber auch warten, bis die Benutzer/Gruppen in Azure AD bereitgestellt und nach wenigen Mitnuten (~15 Minuten) mit dem WSS-Portal synchronisiert werden. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. Die öffentliche IP-Adresse des Endbenutzercomputers, der zum Browsen zu Websites verwendet wird, muss ebenfalls im Portal von Symantec Web Security Service (WSS) bereitgestellt werden.

> [!NOTE]
> [Klicken Sie hier](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1), um die öffentliche IP-Adresse Ihres Computers zu erhalten.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt Testen Sie die Funktion des einmaligen Anmeldens, nachdem Sie Ihr WSS-Konto für die Verwendung von Azure AD für die SAML-Authentifizierung konfiguriert haben.

Nachdem Sie Ihren Webbrowser für die Weiterleitung von Datenverkehr an WSS konfiguriert haben, werden Sie auf die Azure-Anmeldeseite umgeleitet, wenn Sie in Ihrem Webbrowser öffnen und zu einer Website browsen. Geben Sie die Anmeldeinformationen des Testendbenutzers, der in Azure AD bereitgestellt wurde (d.h. BrittaSimon), und das zugehörige Kennwort ein. Nach der Authentifizierung können Sie zu der gewünschten Website browsen. Wenn Sie auf WSS-Seite eine Richtlinienregel erstellt haben, die verhindert, dass BrittaSimon zu einer bestimmten Website browst, sollte Ihnen die WSS-Sperrseite angezeigt werden, wenn Sie versuchen, als Benutzerin BrittaSimon zu dieser Website zu browsen.

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)