---
title: 'Tutorial: Konfigurieren von ServiceNow für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier wird beschrieben, wie Sie Benutzerkonten aus Azure AD automatisch in ServiceNow bereitstellen und die Bereitstellung wieder aufheben.
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
ms.openlocfilehash: b3b62e7c16106fd9d94d4a3438331dab4ce8b6e8
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539042"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von ServiceNow für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in ServiceNow als auch in Azure Active Directory (Azure AD) ausführen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration von Azure AD werden mithilfe des Azure AD-Bereitstellungsdiensts Benutzer und Gruppen in [ServiceNow](https://www.servicenow.com/) bereitgestellt bzw. deren Bereitstellung aufgehoben. 

Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in ServiceNow
> * Entfernen von Benutzern aus ServiceNow, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD and ServiceNow
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in ServiceNow
> * Zulassen des [einmaligen Anmeldens](servicenow-tutorial.md) für ServiceNow (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator)
* [ServiceNow-Instanz](https://www.servicenow.com/) (Version Calgary oder höher)
* [ServiceNow Express-Instanz](https://www.servicenow.com/) (Version Helsinki oder höher)
* Benutzerkonto in ServiceNow mit Administratorrolle

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Bestimmen Sie, welche Daten [zwischen Azure AD und ServiceNow zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von ServiceNow für die Unterstützung der Bereitstellung mit Azure AD

1. Identifizieren Sie den Namen Ihrer ServiceNow-Instanz. Sie finden den Instanznamen in der URL, die Sie für den Zugriff auf ServiceNow verwenden. Im folgenden Beispiel lautet der Instanzname **dev35214**.

   ![Screenshot: ServiceNow-Instanz](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Rufen Sie Anmeldeinformationen für einen Administrator in ServiceNow ab. Navigieren Sie zu dem Benutzerprofil in ServiceNow, und stellen Sie sicher, dass der Benutzer Administratorrechte hat. 

   ![Screenshot: ServiceNow-Administratorrolle](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von ServiceNow aus dem Azure AD-Anwendungskatalog

Fügen Sie ServiceNow aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung für ServiceNow zu beginnen. Wenn Sie ServiceNow zuvor für einmaliges Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Wir empfehlen jedoch, beim Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4: Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer oder die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe [dieser Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [Bereichsfilter verwenden](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Beachten Sie dabei Folgendes:

* Beim Zuweisen von Benutzern und Gruppen zu ServiceNow müssen Sie eine andere Rolle als „Standardzugriff“ auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für ServiceNow 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und Gruppen in TestApp erläutert. Als Grundlage für die Konfiguration können Sie Benutzer- und Gruppenzuweisungen in Azure AD verwenden.

So konfigurieren Sie die automatische Benutzerbereitstellung für ServiceNow in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Screenshot: Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen den Eintrag **ServiceNow** aus.

    ![Screenshot: Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot: Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Anmeldeinformationen und den Benutzernahmen für den ServiceNow-Administrator ein. Um sicherzustellen, dass Azure AD eine Verbindung mit ServiceNow herstellen kann, wählen Sie **Verbindung testen** aus. Wenn die Verbindung nicht möglich ist, müssen Sie sicherstellen, dass Ihr ServiceNow-Konto über Administratorberechtigungen verfügt. Versuchen Sie es anschließend noch einmal.

    ![Screenshot: Dienstbereitstellungsseite, auf der Sie Administratoranmeldeinformationen eingeben können](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie dann das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Screenshot: Felder für Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit ServiceNow synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit ServiceNow synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute zum Abgleichen der Benutzerkonten in ServiceNow bei Updatevorgängen verwendet werden. 

   Wenn Sie sich dafür entscheiden, das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) zu ändern, müssen Sie sicherstellen, dass die ServiceNow-API das Filtern von Benutzern anhand dieses Attributs unterstützt. 
   
   Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit ServiceNow synchronisieren** aus.

11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit ServiceNow synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden bei Updatevorgängen in ServiceNow für den Abgleich der Gruppen verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

12. Wenn Sie Bereichsfilter konfigurieren möchten, folgen Sie den Anleitungen im Tutorial [Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für ServiceNow zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Screenshot: Aktivierte Option „Bereitstellungsstatus“](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und Gruppen fest, die in ServiceNow bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Screenshot: Auswahlmöglichkeiten für Bereitstellungsbereich](common/provisioning-scope.png)

15. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Screenshot: Schaltfläche zum Speichern einer Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus nimmt mehr Zeit in Anspruch als die nachfolgenden Zyklen. Nachfolgende Zyklen erfolgen etwa alle 40 Minuten, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

- Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
- Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
- Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung
* Bei der Bereitstellung bestimmter Attribute (etwa **Abteilung** und **Standort**) in ServiceNow müssen die Werte bereits in einer Referenztabelle in ServiceNow vorhanden sein. Ist dies nicht der Fall, wird der Fehler **InvalidLookupReference** angezeigt. 

   Beispiel: Eine bestimmte Tabelle in ServiceNow enthält möglicherweise zwei Standorte (Seattle, Los Angeles) und drei Abteilungen (Vertrieb, Finanzen, Marketing). Wenn Sie versuchen, einen Benutzer bereitzustellen, der zur Abteilung „Vertrieb“ am Standort „Seattle“ gehört, wird der Benutzer erfolgreich bereitgestellt. Wenn Sie versuchen, einen Benutzer bereitzustellen, der zur Abteilung „Vertrieb“ am Standort „LA“ gehört, wird er nicht bereitgestellt. Der Standort „LA“ muss der Referenztabelle in ServiceNow hinzugefügt werden, oder das Benutzerattribut in Azure AD muss so aktualisiert werden, dass es dem Format in ServiceNow entspricht. 
* Wird der Fehler **EntryJoiningPropertyValueIsMissing** angezeigt, überprüfen Sie die [Attributzuordnungen](../app-provisioning/customize-application-attributes.md), um das passende Attribut zu ermitteln. Dieser Wert muss bei dem bereitzustellenden Benutzer bzw. der Gruppe vorhanden sein. 
* Überprüfen Sie die [ServiceNow SOAP-API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html), um alle Anforderungen oder Beschränkungen (z. B. Format für die Angabe des Ländercodes für einen Benutzer) zu verstehen.
* Bereitstellungsanforderungen werden standardmäßig an https://{Ihr-Instanzname}.service-now.com/{Tabellenname} gesendet. Wenn Sie eine benutzerdefinierte Mandanten-URL benötigen, können Sie die gesamte URL als Instanznamen angeben.
* Der Fehler **ServiceNowInstanceInvalid** weist auf ein Problem bei der Kommunikation mit der ServiceNow-Instanz hin. Fehlertext:
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  Sollten beim Testen der Verbindung Probleme auftreten, wählen Sie für die folgenden Einstellungen in ServiceNow **Nein** aus:
   
  - **System Security** > **High security settings** > **Require basic authentication for incoming SCHEMA requests** („Systemsicherheit“ > „Einstellungen für hohe Sicherheit“ > „Einfache Authentifizierung für eingehende SCHEMA-Anforderungen erforderlich“)
  - **System Properties** > **Web Services** > **Require basic authorization for incoming SOAP requests** („Systemeigenschaften“ > „Webdienste“ > „Einfache Authentifizierung für eingehende SOAP-Anforderungen erforderlich“)

     ![Screenshot: Option zum Autorisieren von SOAP-Anforderungen](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  Wird das Problem dadurch immer noch nicht behoben, wenden Sie sich an den ServiceNow-Support, und bitten Sie ihn, zur Problembehandlung das SOAP-Debuggen zu aktivieren. 

* Der Azure AD-Bereitstellungsdienst wird zurzeit unter bestimmten [IP-Adressbereichen](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges) betrieben. Falls erforderlich, können Sie andere IP-Adressbereiche einschränken und diese bestimmten IP-Adressbereiche der Zulassungsliste Ihrer Anwendung hinzufügen. Dadurch wird der Datenverkehrsfluss vom Azure AD-Bereitstellungsdienst zu Ihrer Anwendung ermöglicht.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
