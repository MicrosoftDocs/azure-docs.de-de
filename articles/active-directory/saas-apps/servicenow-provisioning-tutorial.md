---
title: 'Tutorial: Konfigurieren von ServiceNow für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie Benutzerkonten aus Azure AD für ServiceNow automatisch bereitstellen bzw. die Bereitstellung aufheben.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 5894a57e6495ca02002c8f32b893e696c7f5679b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350703"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von ServiceNow für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in ServiceNow als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD automatisch mithilfe des Azure AD-Bereitstellungsdiensts Benutzer und Gruppen für [ServiceNow](https://www.servicenow.com/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in ServiceNow
> * Entfernen von Benutzern aus ServiceNow, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD and ServiceNow
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in ServiceNow
> * [Einmaliges Anmelden (SSO)](servicenow-tutorial.md) für ServiceNow (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z.B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* [ServiceNow-Instanz](https://www.servicenow.com/) (Version Calgary oder höher)
* [ServiceNow Express-Instanz](https://www.servicenow.com/) (Version Helsinki oder höher)
* Benutzerkonto in ServiceNow mit Administratorrolle

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Bestimmen Sie, welche Daten [zwischen Azure AD und ServiceNow zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von ServiceNow für die Unterstützung der Bereitstellung mit Azure AD

1. Identifizieren Sie den Namen Ihrer ServiceNow-Instanz. Sie finden den Instanznamen in der URL, die Sie für den Zugriff auf ServiceNow verwenden. Im folgenden Beispiel lautet der Instanzname „dev35214“.

   ![ServiceNow-Instanz](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. Rufen Sie Anmeldeinformationen für einen Administrator in ServiceNow ab. Navigieren Sie zu dem Benutzerprofil in ServiceNow, und stellen Sie sicher, dass der Benutzer Administratorrechte hat. 

   ![ServiceNow-Administratorrolle](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von ServiceNow aus dem Azure AD-Anwendungskatalog

Fügen Sie ServiceNow aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung für ServiceNow zu beginnen. Wenn Sie ServiceNow zuvor für das einmalige Anmelden (SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu ServiceNow müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für ServiceNow 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in ServiceNow auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für ServiceNow in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **ServiceNow** aus.

    ![ServiceNow-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Anmeldeinformationen und den Benutzernahmen für den ServiceNow-Administrator ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit ServiceNow herstellen kann. Wenn die Verbindung nicht möglich ist, müssen Sie sicherstellen, dass Ihr ServiceNow-Konto über Administratorberechtigungen verfügt. Versuchen Sie es anschließend noch einmal.

    ![Screenshot: Dienstbereitstellungsseite, auf der Sie Administratoranmeldeinformationen eingeben können](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit ServiceNow synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit ServiceNow synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute zum Abgleichen der Benutzerkonten in ServiceNow bei Updatevorgängen verwendet werden. Wenn Sie sich dafür entscheiden, das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) zu ändern, müssen Sie sicherstellen, dass die ServiceNow-API das Filtern von Benutzern anhand dieses Attributs unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit ServiceNow synchronisieren** aus.

11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit ServiceNow synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden bei Updatevorgängen in ServiceNow für den Abgleich der Gruppen verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für ServiceNow zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in ServiceNow bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung
* **InvalidLookupReference:** Bei der Bereitstellung bestimmter Attribute wie „Abteilung“ und „Standort“ in ServiceNow müssen die Werte bereits in einer Referenztabelle in ServiceNow vorhanden sein. Beispiel: Die Tabelle **Tabellenname einfügen** in ServiceNow muss zwei Standorte (Seattle, Los Angeles) und drei Abteilungen (Vertrieb, Finanzen, Marketing) enthalten. Wenn Sie versuchen, einen Benutzer bereitzustellen, der zur Abteilung „Vertrieb“ am Standort „Seattle“ gehört, wird dieser erfolgreich bereitstellt. Wenn Sie versuchen, einen Benutzer bereitzustellen, der zur Abteilung „Vertrieb“ am Standort „LA“ gehört, wird dieser nicht bereitstellt. Entweder muss der Standort „LA“ der Referenztabelle in ServiceNow hinzugefügt werden, oder das Benutzerattribut in Azure AD muss so aktualisiert werden, dass es dem Format in ServiceNow entspricht. 
* **EntryJoiningPropertyValueIsMissing:** Überprüfen Sie Ihre [Attributzuordnungen](../app-provisioning/customize-application-attributes.md), um das übereinstimmende Attribut zu identifizieren. Dieser Wert muss bei dem bereitzustellenden Benutzer bzw. der Gruppe vorhanden sein. 
* Überprüfen Sie die [ServiceNow SOAP-API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html), um alle Anforderungen oder Beschränkungen (z.B. Format für die Angabe des Ländercodes für einen Benutzer) zu verstehen.
* Bereitstellungsanforderungen werden standardmäßig an https://{Ihr-Instanzname}.service-now.com/{Tabellenname} gesendet. Wenn Sie eine benutzerdefinierte Mandanten-URL benötigen, können Sie die gesamte URL im Feld „Instanzname“ angeben.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Dieser Fehler weist auf ein Problem bei der Kommunikation mit der ServiceNow-Instanz hin. Vergewissern Sie sich, dass die folgenden Einstellungen in ServiceNow *deaktiviert* sind:
   
   1. Wählen Sie **System Security** > **High security settings** > **Require basic authentication for incoming SCHEMA requests** („Systemsicherheit“ -> „Einstellungen für hohe Sicherheit“ -> „Einfache Authentifizierung für eingehende SCHEMA-Anforderungen erforderlich“).
   2. Wählen Sie **System Properties** > **Web Services** > **Require basic authorization for incoming SOAP requests** („Systemeigenschaften“ -> „Webdienste“ -> „Einfache Authentifizierung für eingehende SOAP-Anforderungen erforderlich“).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)