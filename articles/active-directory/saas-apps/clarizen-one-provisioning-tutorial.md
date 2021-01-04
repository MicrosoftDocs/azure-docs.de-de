---
title: 'Tutorial: Konfigurieren von Clarizen One für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Benutzerkonten aus Azure AD automatisch in Clarizen One bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 1658e6adf0c9de0cbd7412b963fb9a134f633430
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96179694"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Clarizen One für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in Clarizen One als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen in [Clarizen One](https://www.clarizen.com/) bereit bzw. hebt die Bereitstellung von Benutzern und Gruppen auf. Informationen zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen (Software as a Service) mit Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Unterstützte Funktionen

> [!div class="checklist"]
> * Erstellen von Benutzern in Clarizen One
> * Entfernen von Benutzern aus Clarizen One, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Clarizen One
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in Clarizen One
> * [Einmaliges Anmelden (SSO)](./clarizen-tutorial.md) bei Clarizen One (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) zum Konfigurieren der Bereitstellung, zum Beispiel Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder globaler Administrator.
* Ein Benutzerkonto in Clarizen One mit den [Berechtigungen](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support) **Integration User** und **Lite Admin**.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung

1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
1. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
1. Legen Sie fest, welche Daten [zwischen Azure AD und Clarizen One zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Clarizen One für die Unterstützung der Bereitstellung mit Azure AD

1. Wählen Sie entsprechend Ihrer Clarizen One-Umgebung und dem Rechenzentrum eine der vier folgenden Mandanten-URLs aus:
      * Rechenzentrum „US Production“: https://servicesapp2.clarizen.com/scim/v2
      * Rechenzentrum „EU Production“: https://serviceseu1.clarizen.com/scim/v2
      * Rechenzentrum „US Sandbox“: https://servicesapp.clarizentb.com/scim/v2
      * Rechenzentrum „EU Sandbox“: https://serviceseu.clarizentb.com/scim/v2

1. Generieren Sie einen [API-Schlüssel](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Dieser Wert wird im Azure-Portal auf der Registerkarte **Bereitstellung** für Ihre Clarizen One-Anwendung im Feld **Geheimes Token** eingegeben.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Clarizen One aus dem Azure AD-Anwendungskatalog

Fügen Sie Clarizen One aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Clarizen One zu beginnen. Wenn Sie Clarizen One zuvor für einmaliges Anmelden (SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Wenn Sie die Integration anfänglich testen, erstellen Sie eine separate App. Weitere Informationen zum Hinzufügen einer Anwendung aus dem Katalog finden Sie unter [Hinzufügen einer Anwendung zu Ihrem Azure AD-Mandanten](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder anhand von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, führen Sie die Schritte unter [Verwalten von Benutzerzuweisungen für eine App in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) aus, um der Anwendung Benutzer und Gruppen zuzuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen der unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden.

* Beim Zuweisen von Benutzern und Gruppen zu Clarizen One müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht wirklich berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen.
* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben.

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für Clarizen One

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in Clarizen One anhand von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Clarizen One in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Screenshot: Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **Clarizen One** aus.

    ![Screenshot: Clarizen One-Link in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot: Registerkarte „Bereitstellung“](common/provisioning.png)

1. Legen Sie **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot: Registerkarte „Bereitstellung“ mit der Option „Automatisch“](common/provisioning-automatic.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** unter **Mandanten-URL** und **Geheimes Token** die Werte für Clarizen One ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Clarizen One herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Clarizen One-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Screenshot: Feld „Geheimes Token“](common/provisioning-testconnection-tenanturltoken.png)

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Screenshot: Feld „Benachrichtigungs-E-Mail“](common/provisioning-notification-email.png)

1. Wählen Sie **Speichern** aus.

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Clarizen One** synchronisieren aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Clarizen One synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Clarizen One für Updatevorgänge verwendet. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern, müssen Sie sicherstellen, dass die Clarizen One-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|
   |---|---|
   |userName|String|
   |displayName|String|
   |aktiv|Boolean|
   |title|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "home"].value|String|
   |emails[type eq "other"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |name.honorificPrefix|String|
   |name.honorificSuffix|String|
   |addresses[type eq "other"].formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].country|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].streetAddress|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |phoneNumbers[type eq "home"].value|String|
   |phoneNumbers[type eq "other"].value|String|
   |phoneNumbers[type eq "pager"].value|String|
   |externalId|String|
   |nickName|String|
   |locale|String|
   |roles[primary eq"True".type]|String|
   |roles[primary eq"True".value]|String|
   |timezone|String|
   |userType|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Verweis|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Clarizen One synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Clarizen One synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Clarizen One für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

      |attribute|type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|Verweis|

1. Wenn Sie Bereichsfilter konfigurieren möchten, folgen Sie den Anleitungen im Tutorial [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Um den Azure AD-Bereitstellungsdienst für Clarizen One zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Screenshot: Bereitstellungsstatus „Ein“](common/provisioning-toggle-on.png)

1. Definieren Sie die Benutzer oder Gruppen, die in Clarizen One bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Screenshot: Bereitstellungsbereich](common/provisioning-scope.png)

1. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Screenshot: Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird.

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung

Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen.

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
1. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
1. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zum Quarantänestatus finden Sie unter [Anwendungsbereitstellung im Quarantänestatus](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

Wenn Sie der Katalog-App Clarizen One einen Benutzer zuweisen, wählen Sie nur die Rolle **Benutzer** aus. Die folgenden Rollen sind ungültig:

* Administrator (Admin)
* Benutzer – Berichterstattung per E-Mail
* Externer Benutzer
* Benutzer – Finanzdaten
* Benutzer – Social Media
* Superuser
* Benutzer – Zeit und Ausgaben

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)