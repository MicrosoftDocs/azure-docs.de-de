---
title: 'Tutorial: Benutzerbereitstellung für Slack – Azure AD'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Slack konfigurieren.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 92c2ae13b840d7a73d86365ce88584bcafc878e8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181457"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Slack für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in Slack und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in Slack automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Slack
> * Entfernen von Benutzern aus Slack, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Slack
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in Slack
> * [Einmaliges Anmelden](./slack-tutorial.md) bei Slack (empfohlen)


## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z.B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator).
* Einen Slack-Mandanten, für den mindestens der [Plus-Plan](https://aadsyncfabric.slack.com/pricing) aktiviert ist.
* Ein Benutzerkonto mit Teamadministratorberechtigungen in Slack.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und Slack zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Schritt 2: Hinzufügen von Slack aus dem Azure AD-Anwendungskatalog

Fügen Sie Slack aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Slack zu beginnen. Wenn Sie Slack zuvor für das einmalige Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Schritt 3: Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu Slack müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben.

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Schritt 4. Konfigurieren der automatischen Benutzerbereitstellung für Slack 

Dieser Abschnitt führt Sie durch das Herstellen einer Verbindung von Azure AD mit der Slack-API zur Bereitstellung von Benutzerkonten sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in Slack basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>So konfigurieren Sie die automatische Bereitstellung von Benutzerkonten für Slack in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **Slack** aus.

    ![Slack-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Klicken Sie im Abschnitt **Administratoranmeldeinformationen** auf **Autorisieren**. Dadurch wird ein Slack-Autorisierungsdialogfeld in einem neuen Browserfenster geöffnet.

    ![Screenshot: Schaltfläche zum Autorisieren der Administratoranmeldeinformationen](media/slack-provisioning-tutorial/authorization.png)


6. Melden Sie sich im neuen Fenster mit Ihrem Teamadministratorkonto bei Slack an. Wählen Sie im daraufhin angezeigten Autorisierungsdialogfeld das Slack-Team aus, für das Sie die Bereitstellung aktivieren möchten, und wählen Sie dann **Autorisieren**. Kehren Sie anschließend zum Azure-Portal zurück, um die Konfiguration der Bereitstellung abzuschließen.

    ![Autorisierungsdialogfeld](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Klicken Sie im Azure-Portal auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer Slack-App herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Slack-Konto über Teamadministratorberechtigungen verfügt, und wiederholen Sie den Schritt zum Autorisieren.

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

9. Wählen Sie **Speichern** aus.

10. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit Slack synchronisieren**.

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Slack synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Slack für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

   |attribute|type|
   |---|---|
   |aktiv|Boolean|
   |externalId|String|
   |displayName|String|
   |name.familyName|String|
   |name.givenName|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |nickName|String|
   |addresses[type eq "untyped"].streetAddress|String|
   |addresses[type eq "untyped"].locality|String|
   |addresses[type eq "untyped"].region|String|
   |addresses[type eq "untyped"].postalCode|String|
   |addresses[type eq "untyped"].country|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |roles[primary eq "True"].value|String|
   |locale|String|
   |name.honorificPrefix|String|
   |photos[type eq "photo"].value|String|
   |profileUrl|String|
   |timezone|String|
   |userType|String|
   |urn:scim:schemas:extension:enterprise:1.0.department|String|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Verweis|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|String|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|String|
   |urn:scim:schemas:extension:enterprise:1.0.organization|String|
   |urn:scim:schemas:extension:enterprise:1.0.division|String|

12. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Slack synchronisieren** aus.

13. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Slack synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Gruppen in Slack für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

      |attribute|type|
      |---|---|
      |displayName|String|
      |members|Verweis|

14. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Um den Azure AD-Bereitstellungsdienst für Slack zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

16. Legen Sie die Benutzer und/oder Gruppen fest, die in Slack bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

17. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-5-monitor-your-deployment"></a>Schritt 5: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

* Beachten Sie beim Konfigurieren des **displayName**-Attributs in Slack das folgende Verhalten:

  * Werte sind nicht ganz eindeutig (z.B. können 2 Benutzer denselben Anzeigenamen haben).

  * Es werden auch nicht englische Zeichen, Leerzeichen, Groß-/Kleinschreibung unterstützt. 
  
  * Zulässige Interpunktion sind Punkte, Unterstriche, Bindestriche, Apostrophe, Klammern (z.B. **( [ { } ] )** ) und Trennzeichen (z.B. **, / ;** ).
  
  * Die Eigenschaft „displayName“ darf kein @-Zeichen enthalten. Wenn die Eigenschaft ein @-Zeichen enthält, wird in den Bereitstellungsprotokollen möglicherweise ein übersprungenes Ereignis mit der Beschreibung „AttributeValidationFailed“ angezeigt.

  * Nehmen Sie nur Änderungen vor, wenn diese beiden Einstellungen im Arbeitsbereich bzw. der Organisation von Slack konfiguriert sind: **Profile syncing is enabled** (Profil Synchronisierung ist aktiviert) und **Users cannot change their display name** (Benutzer können den Anzeigenamen nicht ändern).

* Das **userName**-Attribut von Slack muss weniger als 21 Zeichen lang und eindeutig sein.

* Slack ermöglicht nur das Abgleichen mit den Attributen **userName** und **email**.  
  
* Häufige Fehlercodes sind in der offiziellen Slack-Dokumentation dokumentiert: https://api.slack.com/scim#errors

## <a name="change-log"></a>Änderungsprotokoll

* 16.06.2020 – Das „DisplayName“-Attribut wurde so geändert, dass es nur während der Erstellung eines neuen Benutzers aktualisiert wird.

## <a name="additional-resources"></a>Weitere Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)