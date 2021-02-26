---
title: 'Tutorial: Konfigurieren von Snowflake für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Snowflake konfigurieren.
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
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539534"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Snowflake für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die Sie in Snowflake und Azure Active Directory (Azure AD) ausführen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und Gruppen in [Snowflake](https://www.Snowflake.com/pricing/) zu konfigurieren. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Was ist die automatisierte Bereitstellung von SaaS-App-Benutzern in Azure AD?](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Informationen zu Nutzungsbedingungen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Snowflake
> * Entfernen von Benutzern aus Snowflake, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Snowflake
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in Snowflake
> * Zulassen des [einmaligen Anmeldens](./snowflake-tutorial.md) für Snowflake (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator)
* [Einen Snowflake-Mandanten](https://www.Snowflake.com/pricing/)
* Ein Benutzerkonto in Snowflake mit Administratorberechtigungen

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und Snowflake zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Snowflake für die Unterstützung der Bereitstellung mit Azure AD

Bevor Sie Snowflake für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie die SCIM-Bereitstellung (System for Cross-Domain Identity Management, System für domänenübergreifendes Identitätsmanagement) in Snowflake aktivieren.

1. Melden Sie sich bei Ihrer Snowflake-Administratorkonsole an. Geben Sie die folgende Abfrage in das hervorgehobene Arbeitsblatt ein, und wählen Sie dann **Run** (Ausführen) aus.

    ![Screenshot: Snowflake-Verwaltungskonsole mit der Abfrage und der Schaltfläche „Run“ (Ausführen)](media/Snowflake-provisioning-tutorial/image00.png)

2.  Für Ihren Snowflake-Mandanten wird ein SCIM-Zugriffstoken generiert. Wählen Sie zum Abrufen des Tokens den Link aus, der im folgenden Screenshot hervorgehoben ist:

    ![Screenshot: Arbeitsblatt auf der Benutzeroberfläche von Snowflake mit hervorgehobenem SCIM-Zugriffstoken](media/Snowflake-provisioning-tutorial/image01.png)

3. Kopieren Sie den generierten Tokenwert, und wählen Sie **Done** (Fertig) aus. Dieser Wert wird im Azure-Portal auf der Registerkarte **Bereitstellung** für Ihre Snowflake-Anwendung in das Feld **Geheimes Token** eingegeben.

    ![Screenshot: Abschnitt „Details“, in dem das Token in das Textfeld kopiert wird und die Option „Done“ (Fertig) hervorgehoben ist](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Snowflake aus dem Azure AD-Anwendungskatalog

Fügen Sie Snowflake aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Snowflake zu beginnen. Wenn Sie Snowflake zuvor für einmaliges Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Wir empfehlen jedoch, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4: Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer oder die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe [dieser Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [Bereichsfilter verwenden](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Beachten Sie dabei Folgendes:

* Beim Zuweisen von Benutzern und Gruppen zu Snowflake müssen Sie eine andere Rolle als „Standardzugriff“ auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für Snowflake 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und Gruppen in Snowflake erläutert. Als Grundlage für die Konfiguration können Sie Benutzer- und Gruppenzuweisungen in Azure AD verwenden.

Gehen Sie wie folgt vor, um die automatische Benutzerbereitstellung für Snowflake in Azure AD zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Screenshot: Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen den Eintrag **Snowflake** aus.

    ![Screenshot: Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot: Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die zuvor für die SCIM 2.0-Basis-URL und das Authentifizierungstoken abgerufenen Werte in die Felder **Mandanten-URL** und **Geheimes Token** ein. 

   Um sicherzustellen, dass Azure AD eine Verbindung mit Snowflake herstellen kann, wählen Sie **Verbindung testen** aus. Wenn die Verbindung nicht möglich ist, müssen Sie sicherstellen, dass Ihr Snowflake-Konto über Administratorberechtigungen verfügt. Versuchen Sie es anschließend noch mal.

    ![Screenshot: Felder für Mandanten-URL und geheimes Token und Schaltfläche „Verbindung testen“](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie dann das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Screenshot: Felder für Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Snowflake synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Snowflake synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Snowflake für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |Attribut|type|
   |---|---|
   |aktiv|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|String|

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Snowflake synchronisieren** aus.

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Snowflake synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Snowflake für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    |Attribut|type|
    |---|---|
    |displayName|String|
    |members|Verweis|

12. Wenn Sie Bereichsfilter konfigurieren möchten, folgen Sie den Anleitungen im Tutorial [Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Snowflake zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

     ![Screenshot: Aktivierte Option „Bereitstellungsstatus“](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und Gruppen fest, die in Snowflake bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen. 

    Sollte diese Option nicht verfügbar sein, konfigurieren Sie die erforderlichen Felder unter **Administratoranmeldeinformationen**, wählen Sie **Speichern** aus, und aktualisieren Sie die Seite. 

     ![Screenshot: Auswahlmöglichkeiten für Bereitstellungsbereich](common/provisioning-scope.png)

15. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

     ![Screenshot: Schaltfläche zum Speichern einer Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die nachfolgenden Synchronisierungen. Nachfolgende Synchronisierungen erfolgen etwa alle 40 Minuten, solange der Azure AD-Bereitstellungsdienst ausgeführt wird.

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

- Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
- Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
- Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Connector-Einschränkungen

Die von Snowflake generierten SCIM-Token laufen nach sechs Monaten ab. Beachten Sie, dass diese Token vor ihrem Ablauf erneuert werden müssen, damit die Bereitstellungssynchronisierung weiterhin funktioniert. 

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

Der Azure AD-Bereitstellungsdienst wird zurzeit unter bestimmten [IP-Adressbereichen](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges) betrieben. Falls erforderlich, können Sie andere IP-Adressbereiche einschränken und diese bestimmten IP-Adressbereiche der Zulassungsliste Ihrer Anwendung hinzufügen. Dadurch wird der Datenverkehrsfluss vom Azure AD-Bereitstellungsdienst zu Ihrer Anwendung ermöglicht.

## <a name="change-log"></a>Änderungsprotokoll

* 21.07.2020: Vorläufiges Löschen für alle Benutzer (über das active-Attribut) hinzugefügt

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte
* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
