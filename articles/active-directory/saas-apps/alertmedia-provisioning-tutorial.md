---
title: 'Tutorial: Konfigurieren von AlertMedia für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Benutzerkonten aus Azure AD automatisch in AlertMedia bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: a5df0dd7-05a3-4744-9d51-ec33e89a934f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: Zhchia
ms.openlocfilehash: b15b20ee8e643dc260578d17913810aaf68cc929
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181093"
---
# <a name="tutorial-configure-alertmedia-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von AlertMedia für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in AlertMedia als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen in [AlertMedia](https://www.alertmedia.com/) bereit bzw. hebt die Bereitstellung von Benutzern und Gruppen auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in AlertMedia
> * Entfernen von Benutzern aus AlertMedia, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und AlertMedia
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in AlertMedia
> * [Einmaliges Anmelden](./alertmedia-tutorial.md) bei AlertMedia (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Ein [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md).
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Einen [AlertMedia-Mandanten](https://dashboard.alertmedia.com/#/login)
* Ein Benutzerkonto in AlertMedia mit Administratorberechtigungen zum Konfigurieren einer API-Integration

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und AlertMedia zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-alertmedia-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von AlertMedia für die Unterstützung der Bereitstellung mit Azure AD

1. Melden Sie sich bei Ihrem AlertMedia-Konto an. Navigieren Sie zu **Company > API** (Unternehmen > API).
2. Klicken Sie auf **Add New** (Neue hinzufügen).
3. Legen Sie für Ihre **API-Integration** einen Namen fest, über den Sie einfach erkennen können, wo die Schlüssel verwendet werden.
4. Wählen Sie den Administrator aus, dem Sie die Integration zuordnen möchten.
5. Klicken Sie auf die Schaltfläche **Generate Keys** (Schlüssel generieren) und dann auf **Save** (Speichern).
6. Kopieren und speichern Sie das **Clienttoken** aus Ihrer Integration. Es wird im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer AlertMedia-Anwendung im Feld **Geheimes Token** verwendet.


## <a name="step-3-add-alertmedia-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von AlertMedia aus dem Azure AD-Anwendungskatalog

Fügen Sie AlertMedia aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in AlertMedia zu beginnen. Wenn Sie AlertMedia zuvor für das einmalige Anmelden (SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu AlertMedia müssen Sie eine andere Rolle als den **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-alertmedia"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in AlertMedia 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in ServiceNow auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-alertmedia-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für AlertMedia in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **AlertMedia** aus.

    ![AlertMedia-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** Ihre **Mandanten-URL** für AlertMedia als einen der folgenden Werte ein.
      * (keine benutzerdefinierte Domäne) `https://dashboard.alertmedia.com/api/scim/v3`

      * (benutzerdefinierte Domäne) `https://subdomain.alertmedia.com/api/scim/v3`

      Geben Sie das **Geheime Token** ein, das Sie in Schritt 2 abgerufen haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit AlertMedia herstellen kann. Vergewissern Sie bei einem Verbindungsfehler, dass Ihr AlertMedia-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit AlertMedia synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die aus Azure AD mit AlertMedia synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in AlertMedia für Updatevorgänge verwendet werden. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die AlertMedia-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|
   |---|---|
   |userName|String|
   |aktiv|Boolean|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:first_name|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:last_name|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:email|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:email2|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:email3|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:title|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:mobile_phone|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:mobile_phone_post_dial|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:mobile_phone2|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:mobile_phone2_post_dial|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:mobile_phone3|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:mobile_phone3_post_dial|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:home_phone|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:home_phone_post_dial|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:office_phone|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:office_phone_post_dial|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:address|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:address2|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:city|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:state|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:country|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:zipcode|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:notes|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:customer_user_id|String|
   |urn:ietf:params:scim:schemas:extension:alertmedia:2.0:CustomAttribute:User:user_type|String|

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit AlertMedia synchronisieren** aus.

11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die aus Azure AD mit AlertMedia synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden verwendet, um die Gruppen in AlertMedia für Updatevorgänge abzugleichen. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

      |attribute|type|
      |---|---|
      |displayName|String|
      |members|Verweis|

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für AlertMedia zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die für AlertMedia bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)