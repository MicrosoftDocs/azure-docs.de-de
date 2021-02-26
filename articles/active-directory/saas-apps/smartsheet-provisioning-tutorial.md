---
title: 'Tutorial: Konfigurieren von Smartsheet für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und für das Aufheben der Bereitstellung von Benutzerkonten in Smartsheet konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: e9ee994564e175d3c41cfd5ce415ead8c67df353
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103548"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Smartsheet für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Smartsheet und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen bzw. Aufheben der Bereitstellung von Benutzern und/oder Gruppen in [Smartsheet](https://www.smartsheet.com/pricing) zu konfigurieren. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Smartsheet
> * Entfernen von Benutzern aus Smartsheet, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Smartsheet
> * Einmaliges Anmelden bei Smartsheet (empfohlen)

> [!NOTE]
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z.B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator)
* [Einen Smartsheet-Mandanten](https://www.smartsheet.com/pricing)
* Ein Benutzerkonto in einem Smartsheet Enterprise- oder Enterprise Premier-Plan mit Systemadministratorberechtigungen

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und Smartsheet zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Smartsheet für die Unterstützung der Bereitstellung mit Azure AD

Bevor Sie Smartsheet für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie in Smartsheet die SCIM-Bereitstellung aktivieren.

1. Melden Sie sich als **Systemadministrator** beim **[Smartsheet-Portal](https://app.smartsheet.com/b/home)** an, und navigieren Sie zu **Kontoadministrator**.

    ![Smartsheet-Kontoadministrator](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Wechseln Sie zu **Sicherheitssteuerung > Automatisiertes Benutzer-Provisioning > Bearbeiten**.

    ![Smartsheet-Sicherheitssteuerung](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Fügen Sie die E-Mail-Domänen für die Benutzer hinzu, die Sie aus Azure AD in Smartsheet bereitstellen möchten, und überprüfen Sie die Domänen. Wählen Sie **DEAKTIVIERT** aus, um sicherzustellen, dass alle Bereitstellungsaktionen nur von Azure AD stammen, und um außerdem sicherzustellen, dass Ihre Smartsheet-Benutzerliste mit Azure AD-Zuweisungen synchronisiert wird.

    ![Smartsheet-Benutzerbereitstellung](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Sobald die Überprüfung abgeschlossen ist, müssen Sie die Domäne aktivieren. 

    ![Domäne in Smartsheet aktivieren](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generieren Sie das **geheime Token**, das zum Konfigurieren der automatischen Benutzerbereitstellung mit Azure AD erforderlich ist, indem Sie zu **Apps & Integrationen** navigieren.

    ![Screenshot der Smartsheet-Administratorseite mit dem Benutzer „avatar“ und der aufgerufenen Option „Apps & Integrations“](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Wählen Sie **API-Zugriff** aus. Klicken Sie auf **Neues Zugriffstoken generieren**.

    ![Screenshot des Dialogfelds „Persönliche Einstellungen“ mit den aufgerufenen Optionen „API-Zugriff“ und „Neues Zugriffstoken generieren“](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Legen Sie den Namen des API-Zugriffstokens fest. Klicken Sie auf **OK**.

    ![Screenshot von Schritt 1 von 2: „Generieren eines API-Zugriffstokens“ mit der aufgerufenen Option „OK“](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Kopieren Sie das API-Zugriffstoken, und speichern Sie es, weil Sie es nur zu diesem Zeitpunkt anzeigen können. Diese Angabe ist in Azure AD im Feld **Geheimes Token** erforderlich.

    ![Smartsheet-Token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Smartsheets aus dem Azure AD-Anwendungskatalog

Fügen Sie Smartsheet aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Smartsheet zu beginnen. Wenn Sie Smartsheet zuvor für das einmalige Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu Smartsheet müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Um Parität der Benutzerrollenzuweisungen zwischen Smartsheet und Azure AD sicherzustellen, wird empfohlen, die gleichen Rollenzuweisungen zu verwenden, die in der vollständigen Smartsheet-Benutzerliste aufgefüllt sind. Wenn Sie diese Benutzerliste von Smartsheet abrufen möchten, navigieren Sie zu **Kontoadministrator > Benutzerverwaltung > Weitere Aktionen > Benutzerliste (CSV) herunterladen**.

* Damit ein Benutzer auf bestimmte Features in der App zugreifen kann, muss er in Smartsheet über mehrere Rollen verfügen. Weitere Informationen zu Benutzertypen und Berechtigungen in Smartsheet finden Sie unter [User Types and Permissions](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions) (Benutzertypen und Berechtigungen).

*  Wenn einem Benutzer mehreren Rollen in Smartsheet zugewiesen sind, **MÜSSEN** Sie sicherstellen, dass diese Rollenzuweisungen in Azure AD repliziert werden, um ein Szenario zu vermeiden, in dem Benutzer den Zugriff auf Smartsheet-Objekte dauerhaft verlieren könnten. Jede eindeutige Rolle in Smartsheet **MUSS** einer anderen Gruppe in Azure AD zugewiesen werden. Der Benutzer **MUSS** dann allen Gruppen hinzugefügt werden, die den gewünschten Rollen entsprechen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für Smartsheet 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Smartsheet auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Gehen Sie wie folgt vor, um die automatische Benutzerbereitstellung für Smartsheet in Azure AD zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Anwendung **Smartsheet** aus.

    ![Smartsheet-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die zuvor von Smartsheet abgerufenen Werte für **SCIM 2.0 base URL and Access Token** („SCIM 2.0-Basis-URL“ und „Zugriffstoken“) in die Felder **Mandanten-URL** und **Geheimes Token** ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Smartsheet herstellen kann. Wenn die Verbindung nicht hergestellt werden kann, stellen Sie sicher, dass Ihr Smartsheet-Konto über Systemadministratorberechtigungen verfügt, und versuchen Sie es erneut.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Smartsheet synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Smartsheet synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Smartsheet für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;|
   |aktiv|Boolean|
   |title|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |emails[type eq "work"].value|String|
   |externalId|String|
   |roles|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|


10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für Smartsheet zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer und/oder Gruppen fest, die in Smartsheet bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Smartsheet unterstützt keine vorläufigen Löschungen. Wenn das Attribut **active** eines Benutzers auf „False“ festgelegt ist, wird der Benutzer in Smartsheet dauerhaft gelöscht.

## <a name="change-log"></a>Änderungsprotokoll

* 16.06.2020: Unterstützung für Erweiterungsattribute für Kostenstelle, Sparte, Vorgesetzter und Abteilung zur Verwendung in Unternehmen hinzugefügt.
* 10.02.2021: Unterstützung für Kernattribute vom Typ „emails[type eq "work"]“ für Benutzer hinzugefügt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)