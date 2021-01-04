---
title: 'Tutorial: Konfigurieren von SmartFile für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in SmartFile konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 03c7efd390d4714680ed2ccd54d297b8137aaafc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348669"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von SmartFile für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in SmartFile und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und für das Aufheben der Bereitstellung von Benutzern und/oder Gruppen in SmartFile zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* Einen [SmartFile-Mandanten](https://www.SmartFile.com/pricing/).
* Ein Benutzerkonto in SmartFile mit Administratorberechtigungen.

## <a name="assigning-users-to-smartfile"></a>Zuweisen von Benutzern zu SmartFile

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf SmartFile benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen SmartFile wie folgt zuweisen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Wichtige Tipps zum Zuweisen von Benutzern zu SmartFile

* Es wird empfohlen, SmartFile einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu SmartFile müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="setup-smartfile-for-provisioning"></a>Einrichten von SmartFile für die Bereitstellung

Bevor Sie SmartFile für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie in SmartFile die SCIM-Bereitstellung aktivieren und zusätzliche erforderliche Details sammeln.

1. Melden Sie sich bei Ihrer SmartFile-Verwaltungskonsole an. Navigieren Sie zur oberen rechten Ecke der SmartFile-Verwaltungskonsole. Wählen Sie **Product Key** aus.

    ![SmartFile-Verwaltungskonsole](media/smartfile-provisioning-tutorial/login.png)

2. Kopieren Sie den **Product Key** und das **Product Password** (Produktkennwort), um ein Bearertoken zu generieren. Fügen Sie diese in einem Editor mit einem Doppelpunkt dazwischen ein.
    
     ![Screenshot: Abschnitt „Product Key“ mit den hervorgehobenen Textfeldern „Product Key“ und „Product Password“ (Produktkennwort).](media/smartfile-provisioning-tutorial/auth.png)

    ![Screenshot: Klartext mit dem durch einen Doppelpunkt getrennten Product Key und Produktkennwort](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Hinzufügen von SmartFile aus dem Katalog

Bevor Sie SmartFile für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie SmartFile aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie zum Hinzufügen von SmartFile aus dem Azure AD-Anwendungskatalog die folgenden Schritte aus:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **SmartFile** ein, klicken Sie im Ergebnisbereich erst auf **SmartFile** und dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![SmartFile in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Konfigurieren der automatischen Benutzerbereitstellung in SmartFile 

In diesem Abschnitt werden die Schritte erläutert, mit denen Sie den Azure AD-Bereitstellungsdienst zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in SmartFile auf der Grundlage von Benutzer- bzw. Gruppenzuweisungen in Azure AD konfigurieren.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für SmartFile aktivieren. Befolgen Sie hierzu die Anweisungen im [Tutorial: Azure Active Directory-Integration mit SmartFile](SmartFile-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Gehen Sie wie folgt vor, um die automatische Benutzerbereitstellung für SmartFile in Azure AD zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **SmartFile** aus.

    ![SmartFile-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5.  Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://<SmartFile sitename>.smartfile.com/ftp/scim` ein. Ein Beispiel ähnelt `https://demo1test.smartfile.com/ftp/scim`. Geben Sie den Wert des **Bearertokens** (ProductKey:ProductPassword) ein, den Sie zuvor unter **Geheimes Token** abgerufen haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit SmartFile herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr SmartFile-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit SmartFile synchronisieren** aus.

    ![Benutzerzuordnungen in SmartFile](media/smartfile-provisioning-tutorial/usermapping.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit SmartFile synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in SmartFile für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Benutzerattribute in SmartFile](media/smartfile-provisioning-tutorial/userattribute.png)

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit SmartFile synchronisieren** aus.

    ![Gruppenzuordnungen in SmartFile](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit SmartFile synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in SmartFile für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Gruppenattribute in SmartFile](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für SmartFile zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in SmartFile bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

    Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Bereitstellungsaktivitätsberichten aufrufen. In diesen Berichten sind alle vom Azure AD-Bereitstellungsdienst in SmartFile ausgeführten Aktionen aufgeführt.

    Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Reporting on automatic user account provisioning (Tutorial: Meldung zur automatischen Benutzerkontobereitstellung)](../app-provisioning/check-status-user-account-provisioning.md).
    
## <a name="connector-limitations"></a>Connector-Einschränkungen

* SmartFile unterstützt nur das endgültige Löschen. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

 [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
