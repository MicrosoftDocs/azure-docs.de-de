---
title: 'Tutorial: Konfigurieren von Dropbox für Unternehmen für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Dropbox für Unternehmen konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 04d17e17ef11696efd52f04ea83639f2a9b81fea
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938748"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Dropbox für Unternehmen für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Dropbox für Unternehmen und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Dropbox für Unternehmen zu konfigurieren.

> [!IMPORTANT]
> Microsoft und Dropbox nehmen die alte Dropbox-Integration ab 1.4.2021 außer Betrieb. Um Dienstunterbrechungen zu vermeiden, empfehlen wir, zur neuen Dropbox-Integration zu migrieren, die Gruppen unterstützt. Zur Migration zur neuen Dropbox-Integration führen Sie die folgenden Schritte aus, um eine neue Dropbox-Instanz zur Bereitstellung in Ihrem Azure AD-Mandanten hinzuzufügen und zu konfigurieren. Sobald Sie die neue Dropbox-Integration konfiguriert haben, deaktivieren Sie die Bereitstellung der alten Integration, um Bereitstellungskonflikte zu vermeiden. Ausführlichere Schritte zum Migrieren zur neuen Dropbox-Integration finden Sie unter [Aktualisieren auf die neueste Dropbox-Version für Geschäftsanwendungen mithilfe von Azure AD](https://help.dropbox.com/installs-integrations/third-party/update-dropbox-azure-ad-connector).

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Ein Dropbox für Unternehmen-Mandant](https://www.dropbox.com/business/pricing)
* Ein Benutzerkonto in Dropbox für Unternehmen mit Administratorberechtigungen.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Hinzufügen von Dropbox für Unternehmen aus dem Katalog

Bevor Sie Dropbox für Unternehmen für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Dropbox für Unternehmen aus dem Azure AD-Anwendungskatalog der Liste der verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie zum Hinzufügen von Dropbox für Unternehmen aus dem Azure AD-Anwendungskatalog die folgenden Schritte aus:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Dropbox für Unternehmen** ein, wählen Sie im Ergebnisbereich **Dropbox für Unternehmen** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Dropbox für Unternehmen in der Ergebnisliste](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Zuweisen von Benutzern zu Dropbox für Unternehmen

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Dropbox für Unternehmen benötigen. Anschließend können Sie diese Benutzer bzw. Gruppen Dropbox für Unternehmen wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Dropbox für Unternehmen

* Es wird empfohlen, Dropbox für Unternehmen einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Dropbox für Unternehmen müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Konfigurieren der automatischen Benutzerbereitstellung für Dropbox für Unternehmen 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Dropbox für Unternehmen auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Dropbox für Unternehmen aktivieren. Befolgen Sie hierzu die Anweisungen im [Tutorial: Azure Active Directory-Integration in Dropbox für Unternehmen](dropboxforbusiness-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Dropbox für Unternehmen in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Dropbox für Unternehmen** aus.

    ![Der Link „Dropbox für Unternehmen“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Klicken Sie im Abschnitt **Administratoranmeldeinformationen** auf **Autorisieren**. Es wird ein Dropbox für Unternehmen-Anmeldedialogfeld in einem neuen Browserfenster geöffnet.

    ![Bereitstellung ](common/provisioning-oauth.png)

6. Melden Sie sich im Dialogfeld **Bei Dropbox für Unternehmen anmelden zum Verbinden mit Azure AD** bei Ihrem Dropbox für Unternehmen-Mandanten an, und bestätigen Sie Ihre Identität.

    ![Dropbox für Unternehmen-Anmeldung](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Klicken Sie nach Abschluss der Schritte 5 und 6 auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Dropbox für Unternehmen herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Dropbox für Unternehmen-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Token](common/provisioning-testconnection-oauth.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Dropbox synchronisieren**.

    ![Benutzerzuordnungen in Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Dropbox synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Dropbox für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Dropbox-Benutzerattribute](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Dropbox synchronisieren**.

    ![Gruppenzuordnungen in Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Dropbox synchronisiert werden. Die als **Übereinstimmend** ausgewählten Attribute werden verwendet, um die Gruppen in Dropbox für Updatevorgänge abzugleichen. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Dropbox-Gruppenattribute](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Um den Azure AD-Bereitstellungsdienst für Dropbox zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

16. Legen Sie die Benutzer bzw. Gruppen fest, die in Dropbox bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

17. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Dropbox ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen
 
* Dropbox unterstützt das Sperren eingeladener Benutzer nicht. Wenn ein eingeladener Benutzer gesperrt wird, wird dieser Benutzer gelöscht.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

