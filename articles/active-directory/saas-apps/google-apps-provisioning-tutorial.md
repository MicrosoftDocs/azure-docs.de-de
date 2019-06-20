---
title: 'Tutorial: Konfigurieren von G Suite für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Benutzerkonten aus Azure AD für G Suite automatisch bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea1f4d4a6b60961515826a1ba7409bf149b318e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60276938"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von G Suite für die automatische Benutzerbereitstellung

In diesem Tutorial erfahren Sie, wie Sie Benutzerkonten aus Azure Active Directory (Azure AD) automatisch für G Suite bereitstellen und die Bereitstellung wieder aufheben.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit G Suite konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein G Suite-Abonnement, für das einmaliges Anmelden aktiviert ist
- Eine Google Apps-Abonnement oder ein Google Cloud Platform-Abonnement.

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-g-suite"></a>Zuweisen von Benutzern zu G Suite

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Sie müssen festlegen, welche Benutzer oder Gruppen in Azure AD Zugriff auf Ihre App benötigen, bevor Sie den Bereitstellungsdienst konfigurieren und aktivieren. Danach können Sie die entsprechenden Benutzer und Gruppen gemäß der Anleitung unter [Zuweisen von Benutzern oder Gruppen zu einer Unternehmens-App in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) (in englischer Sprache) zuweisen.

> [!IMPORTANT]
> Es empfiehlt sich, G Suite einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können Sie dann weitere Benutzer und Gruppen zuweisen.
> 
> Wählen Sie im Zuweisungsdialogfeld die Rolle **Benutzer** oder **Gruppe** aus, wenn Sie G Suite einen Benutzer zuweisen. Die Rolle **Standardzugriff** ist für Bereitstellungen nicht geeignet.

## <a name="enable-automated-user-provisioning"></a>Aktivieren der automatisierten Benutzerbereitstellung

Dieser Abschnitt beschreibt, wie Sie Azure AD mit der API für die Benutzerkontenbereitstellung von G Suite verbinden. Außerdem erfahren Sie, wie Sie den Bereitstellungsdienst zum Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in G Suite anhand der Benutzer- und Gruppenzuweisung in Azure AD konfigurieren.

>[!TIP]
>Sie können auch das SAML-basierte einmalige Anmelden für G Suite aktivieren. Befolgen Sie hierzu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurieren der automatischen Bereitstellung von Benutzerkonten

> [!NOTE]
> Eine weitere Option für die Automatisierung der Benutzerbereitstellung für G Suite ist die Verwendung von [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS stellt Ihre lokalen Active Directory-Identitäten für G Suite bereit. Im Gegensatz dazu stellt die Lösung in diesem Tutorial Ihre Azure Active Directory-Benutzer (Cloud) und E-Mail-fähigen Gruppen für G Suite bereit. 

1. Melden Sie sich bei der [Google Apps-Verwaltungskonsole](https://admin.google.com/) mit Ihrem Administratorkonto an, und wählen Sie **Sicherheit** aus. Sollte der Link nicht zu sehen sein, befindet er sich möglicherweise unter dem Menü **Weitere Steuerelemente** am unteren Bildschirmrand.

    ![Wählen Sie „Sicherheit“ aus.][10]

1. Wählen Sie auf der Seite **Sicherheit** die Option **API-Referenz** aus.

    ![Wählen Sie „API-Referenz“ aus.][15]

1. Wählen Sie **API-Zugriff aktivieren**aus.

    ![Wählen Sie „API-Referenz“ aus.][16]

   > [!IMPORTANT]
   > Für jeden Benutzer, den Sie für G Suite bereitstellen möchten, *muss* der Benutzername in Azure Active Directory an eine benutzerdefinierte Domäne gebunden werden. Beispielsweise werden Benutzernamen wie bob@contoso.onmicrosoft.com von G Suite nicht akzeptiert. bob@contoso.com ist hingegen zulässig. Sie können die Domäne eines vorhandenen Benutzers ändern, indem Sie seine Eigenschaften in Azure AD bearbeiten. Die folgende Anleitung beschreibt, wie Sie eine benutzerdefinierte Domäne für Azure Active Directory und G Suite einrichten.

1. Führen Sie die folgenden Schritte aus, wenn Sie Ihrem Azure Active Directory noch keinen benutzerdefinierten Domänennamen hinzugefügt haben:
  
    a. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Active Directory** aus. Wählen Sie in der Verzeichnisliste Ihr Verzeichnis aus.

    b. Wählen Sie im linken Navigationsbereich die Option **Domänenname** und anschließend **Hinzufügen** aus.

    ![Domäne](./media/google-apps-provisioning-tutorial/domain_1.png)

    ![Hinzufügen der Domäne](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. Geben Sie Ihren Domänennamen in das Feld **Domänenname** ein. Dieser Domänenname sollte dem Domänennamen entsprechen, den Sie für G Suite verwenden. Wählen Sie als Nächstes die Schaltfläche **Domäne hinzufügen** aus.

    ![Domänenname](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. Wählen Sie **Weiter** aus, um zur Überprüfungsseite zu gelangen. Bearbeiten Sie die DNS-Einträge der Domäne gemäß den Werten auf dieser Seite, um zu bestätigen, dass Ihnen die Domäne gehört. Sie können wählen, ob Sie zur Bestätigung **MX-Einträge** oder **TXT-Einträge** verwenden möchten. Dies hängt von der Auswahl für die Option **Eintragstyp** ab.

    Eine ausführlichere Anleitung zur Bestätigung von Domänennamen mit Azure AD finden Sie unter [Hinzufügen eigener Domänennamen zu Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

    ![Domäne](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. Wiederholen Sie die obigen Schritte für alle Domänen, die Sie Ihrem Verzeichnis hinzufügen möchten.

    > [!NOTE]
    > Für die Benutzerbereitstellung muss die benutzerdefinierte Domäne dem Domänennamen der Azure AD-Quelle entsprechen. Andernfalls lässt sich das Problem unter Umständen durch eine Anpassung der Attributzuordnung beheben.

1. Nachdem Sie alle Ihre Domänen mit Azure AD überprüft haben, müssen Sie sie mit Google Apps erneut überprüfen. Führen Sie für jede Domäne, die noch nicht für Google registriert ist, die folgenden Schritte aus:

    a. Wählen Sie in der [Google Apps-Verwaltungskonsole](https://admin.google.com/) die Option **Domänen** aus.

    ![Auswählen von „Domänen“][20]

    b. Wählen Sie **Domäne oder Domänenalias hinzufügen** aus.

    ![Hinzufügen einer neuen Domäne][21]

    c. Wählen Sie **Andere Domäne hinzufügen** aus, und geben Sie dann den Namen der Domäne ein, die Sie hinzufügen möchten.

    ![Geben Sie Ihren Domänennamen ein][22]

    d. Wählen Sie **Weiter und Domänenbesitzrecht überprüfen** aus. Führen Sie dann die Schritte aus, mit denen Sie prüfen, ob Sie den Domänennamen besitzen. Umfassende Anweisungen zum Überprüfen Ihrer Domäne mit Google finden Sie unter [Überprüfen des Websitebesitzrechts mit Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Wiederholen Sie die obigen Schritte für alle weiteren Domänen, die Sie Google Apps hinzufügen möchten.

    > [!WARNING]
    > Wenn Sie die primäre Domäne für Ihren G Suite-Mandanten ändern und das einmalige Anmelden mit Azure AD bereits konfiguriert haben, müssen Sie Schritt 3 unter Schritt 2: „Einmaliges Anmelden aktivieren“ wiederholen.

1. Wählen Sie in der [Google Apps-Verwaltungskonsole](https://admin.google.com/) die Option **Administratorrollen** aus.

    ![Auswählen von „Google Apps“][26]

1. Bestimmen Sie, welches Administratorkonto Sie zum Verwalten der Benutzerbereitstellung verwenden möchten. Bearbeiten Sie für die **Administratorrolle** des Kontos die **Berechtigungen** für diese Rolle. Aktivieren Sie alle **Admin-API-Berechtigungen**, damit dieses Konto für die Bereitstellung verwendet werden kann.

    ![Auswählen von „Google Apps“][27]

    > [!NOTE]
    > Wenn Sie eine Produktionsumgebung konfigurieren, sollten Sie speziell für diesen Schritt ein Administratorkonto in G Suite erstellen. Diesen Konten muss eine Administratorrolle zugeordnet sein, die über die erforderlichen API-Berechtigungen verfügt.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory** > **Unternehmens-Apps** > **Alle Anwendungen**.

1. Suchen Sie über das Suchfeld nach Ihrer G Suite-Instanz, wenn Sie G Suite bereits für einmaliges Anmelden konfiguriert haben. Wählen Sie andernfalls **Hinzufügen** aus, und suchen Sie im Anwendungskatalog nach **G Suite** oder **Google Apps**. Wählen Sie in den Suchergebnissen Ihre App aus, und fügen Sie die Anwendung Ihrer Anwendungsliste hinzu.

1. Wählen Sie Ihre G Suite-Instanz und dann die Registerkarte **Bereitstellung** aus.

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest. 

    ![Bereitstellung](./media/google-apps-provisioning-tutorial/provisioning.png)

1. Wählen Sie im Abschnitt **Administratoranmeldeinformationen** die Option **Autorisieren** aus. In einem neuen Browserfenster wird ein Dialogfeld für die Google-Autorisierung geöffnet.

1. Bestätigen Sie, dass Sie Azure Active Directory die Berechtigung erteilen möchten, Änderungen an Ihrem G Suite-Mandanten vorzunehmen. Wählen Sie **Akzeptieren** aus.

    ![Überprüfen Sie die Berechtigungen.][28]

1. Wählen Sie im Azure-Portal die Option **Verbindung testen** aus, um zu überprüfen, ob Azure AD eine Verbindung mit Ihrer App herstellen kann. Falls die Verbindung nicht hergestellt werden kann, überprüfen Sie, ob Ihr G Suite-Konto über Teamadministratorberechtigungen verfügt. Führen Sie dann erneut den Schritt **Autorisieren** aus.

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie dann das Kontrollkästchen.

1. Wählen Sie **Speichern** aus.

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Google Apps synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit G Suite synchronisiert werden. Die Attribute, bei denen es sich um **übereinstimmende** Eigenschaften handelt, werden zum Abgleich der G Suite-Benutzerkonten für Aktualisierungsvorgänge verwendet. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

1. Ändern Sie in den **Einstellungen** den **Bereitstellungsstatus** in **Ein**, um den Azure AD-Bereitstellungsdienst für G Suite zu aktivieren.

1. Wählen Sie **Speichern** aus.

Daraufhin wird die Erstsynchronisierung aller Benutzer oder Gruppen gestartet, die G Suite im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die während der Dienstausführung etwa alle 40 Minuten erfolgen. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und über Links zu Protokollen zur Bereitstellungsaktivität navigieren. In diesen Protokollen werden sämtliche Aktionen beschrieben, die vom Bereitstellungsdienst für Ihre App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren von einmaligem Anmelden](google-apps-tutorial.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
