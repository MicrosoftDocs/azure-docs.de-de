---
title: 'Tutorial: Konfigurieren der automatischen Benutzerbereitstellung mit Azure Active Directory für Atlassian Cloud | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory so konfigurieren, dass Benutzerkonten in Atlassian Cloud automatisch bereitgestellt oder entfernt werden.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 691d35267c255c933a8098b99301fbb795a3cd0c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181066"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren der automatischen Benutzerbereitstellung für Atlassian Cloud

In diesem Tutorial werden die Schritte erläutert, die in Atlassian Cloud und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in [Atlassian Cloud](https://www.atlassian.com/licensing/cloud) zu konfigurieren. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Atlassian Cloud
> * Entfernen von Benutzern aus Atlassian Cloud, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Atlassian Cloud
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in Atlassian Cloud
> * [Einmaliges Anmelden](./atlassian-cloud-tutorial.md) bei Atlassian Cloud (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z.B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator).
* [Einen Atlassian Cloud-Mandanten](https://www.atlassian.com/licensing/cloud)
* Ein Benutzerkonto in Atlassian Cloud mit Administratorrechten.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und Atlassian Cloud zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Atlassian Cloud für die Unterstützung der Bereitstellung mit Azure AD

1. Navigieren Sie zu [Atlassian Organization Manager](https://admin.atlassian.com), wählen Sie die Organisation aus, und klicken Sie auf **Verzeichnis**.

    ![Screenshot: Seite „Verwaltung“ mit hervorgehobener Option „Directory“ (Verzeichnis)](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. Klicken Sie auf **Benutzerbereitstellung** und dann auf **Verzeichnis erstellen**. Kopieren Sie die **Verzeichnisbasis-URL** und das **Bearertoken**. Diese Werte werden im Azure AD-Portal auf der Registerkarte „Bereitstellung“ Ihrer Atlassian Cloud-Anwendung in das Feld **Mandanten-URL** bzw. **Geheimes Token** eingegeben.

    ![Screenshot: Seite „Verwaltung“ mit hervorgehobener Option „User provisioning“ (Benutzerbereitstellung)](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Screenshot: Seite „Create a token“ (Token erstellen)](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    ![Screenshot der Seite „demo-time-directory-token“](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Atlassian Cloud aus dem Azure AD-Anwendungskatalog

Fügen Sie Atlassian Cloud aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Atlassian Cloud zu beginnen. Wenn Sie Atlassian Cloud zuvor für das einmalige Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu Atlassian Cloud müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in Atlassian Cloud 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Atlassian Cloud auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Gehen Sie wie folgt vor, um die automatische Benutzerbereitstellung für Atlassian Cloud in Azure AD zu konfigurieren:

1. Melde Sie sich im [Azure-Portal](https://portal.azure.com) an und wählen Sie dann nacheinander **Unternehmensanwendungen**, **Alle Anwendungen** und **Atlassian Cloud** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Atlassian Cloud** aus.

    ![Wählen Sie in der Anwendungsliste Atlassian Cloud aus.](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Werte für **Mandanten-URL** und **Geheimes Token** ein, die Sie zuvor aus Ihrem Atlassian Cloud-Konto abgerufen haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Atlassian Cloud herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Atlassian Cloud-Konto über Administratorberechtigungen verfügt, und den Vorgang wiederholen.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Mappings** (Zuordnungen) die Option **Synchronize Azure Active Directory Users to Atlassian Cloud** (Azure Active Directory-Benutzer mit Atlassian Cloud synchronisieren).

9. Überprüfen Sie im Abschnitt **Attribute Mapping** (Attributzuordnungen) die Benutzerattribute, die von Azure AD mit Atlassian Cloud synchronisiert werden. Beachten Sie, dass die als **Matching** (übereinstimmend) ausgewählten Attribute für den Abgleich der Benutzerkonten in Atlassian Cloud für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|
   |---|---|
   |userName|String|
   |aktiv|Boolean|
   |name.familyName|String|
   |name.givenName|String|
   |emails[type eq "work"].value|String|   

10. Wählen Sie im Abschnitt **Mappings** (Zuordnungen) die Option **Synchronize Azure Active Directory Groups to Atlassian Cloud** (Azure Active Directory-Gruppen mit Atlassian Cloud synchronisieren).

11. Überprüfen Sie im Abschnitt **Attribute Mapping** (Attributzuordnungen) die Gruppenattribute, die von Azure AD mit Atlassian Cloud synchronisiert werden. Die als **Matching** (übereinstimmend) ausgewählten Attribute werden für den Abgleich der Gruppen in Atlassian Cloud für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

      |attribute|type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|Verweis|

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**, um den Azure AD-Bereitstellungsdienst für Atlassian Cloud zu aktivieren.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer bzw. Gruppen fest, die in Atlassian Cloud bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Settings** (Einstellungen) unter **Scope** (Bereich) die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

16. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird.

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Atlassian Cloud lässt die Benutzerbereitstellung nur aus [verifizierten Domänen](https://confluence.atlassian.com/cloud/organization-administration-938859734.html) zu.
* Atlassian Cloud unterstützt momentan nicht das Umbenennen von Gruppen. Das bedeutet, dass Änderungen des Anzeigenamens einer Gruppe in Azure AD nicht in Atlassian Cloud übernommen werden.
* Der Wert des Benutzerattributs **mail** in Azure AD wird nur aufgefüllt, wenn der Benutzer über ein Microsoft Exchange-Postfach verfügt. Wenn der Benutzer kein Microsoft Exchange-Postfach hat, wird empfohlen, dem **emails**-Attribut in Atlassian Cloud ein anderes gewünschtes Attribut zuzuordnen.

## <a name="change-log"></a>Änderungsprotokoll

* 15.06.2020 – Unterstützung für Batch-PATCH für Gruppen hinzugefügt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png