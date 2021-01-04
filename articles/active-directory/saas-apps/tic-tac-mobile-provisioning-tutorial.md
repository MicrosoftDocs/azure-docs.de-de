---
title: 'Tutorial: Konfigurieren von Tic-Tac Mobile für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Benutzerkonten aus Azure AD für Tic-Tac Mobile automatisch bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: a09594d1bc6037f252ba71855ae302208b4980d4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182217"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Tic-Tac Mobile für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie in Tic-Tac Mobile und in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Wenn die automatische Benutzerbereitstellung konfiguriert ist, stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen in [Tic-Tac Mobile](https://www.tictacmobile.com/) bereit bzw. hebt die Bereitstellung von Benutzern und Gruppen auf. Informationen zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen (Software as a Service) mit Azure AD](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Unterstützte Funktionen

> [!div class="checklist"]
> * Erstellen von Benutzern in Tic-Tac Mobile
> * Entfernen von Benutzern aus Tic-Tac Mobile, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Tic-Tac Mobile

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) zum Konfigurieren der Bereitstellung, zum Beispiel Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder globaler Administrator.
* Ein [Tic-Tac Mobile](https://www.tictacmobile.com/)-Konto mit Superadministratorrolle.


## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung

1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
1. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
1. Legen Sie fest, welche Daten [zwischen Azure AD und Tic-Tac Mobile zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Tic-Tac Mobile für die Unterstützung der Bereitstellung mit Azure AD

Wenden Sie sich an support@tictacmobile.com, um die **Mandanten-URL** und das **geheime Token** zu erhalten. Um ein Token zu erhalten, müssen Sie über die Superadministratorrolle in Tic-Tac Mobile verfügen. Das Token wird im Azure-Portal auf der Registerkarte **Bereitstellung** im Feld **Geheimes Token** für Ihre Tic-Tac Mobile-Anwendung eingegeben.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Tic-Tac Mobile aus dem Azure AD-Anwendungskatalog

Fügen Sie Tic-Tac Mobile aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Tic-Tac Mobile zu beginnen. Wenn Sie Tic-Tac Mobile zuvor für einmaliges Anmelden (SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Wenn Sie die Integration anfänglich testen, erstellen Sie eine separate App. Weitere Informationen zum Hinzufügen einer Anwendung aus dem Katalog finden Sie unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder anhand von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, führen Sie die Schritte unter [Verwalten von Benutzerzuweisungen für eine App in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) aus, um der Anwendung Benutzer und Gruppen zuzuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen der unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden.

* Beim Zuweisen von Benutzern und Gruppen zu Tic-Tac Mobile müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht wirklich berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen.
* Fangen Sie klein an. Testen Sie die Bereitstellung mit wenigen Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben.

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in Tic-Tac Mobile

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in Tic-Tac Mobile anhand von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Konfigurieren der automatischen Benutzerbereitstellung für Tic-Tac Mobile in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Screenshot: Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **Tic-Tac Mobile** aus.

    ![Screenshot: Tic-Tac Mobile-Link in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot: Registerkarte „Bereitstellung“](common/provisioning.png)

1. Legen Sie **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot: Registerkarte „Bereitstellung“ mit der Option „Automatisch“](common/provisioning-automatic.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die **Mandanten-URL** und das **geheime Token** für Tic-Tac Mobile ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Tic-Tac Mobile herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Tic-Tac Mobile-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Screenshot: Feld „Geheimes Token“](common/provisioning-testconnection-tenanturltoken.png)

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Screenshot: Feld „Benachrichtigungs-E-Mail“](common/provisioning-notification-email.png)

1. Wählen Sie **Speichern** aus.

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Tic-Tac Mobile synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Tic-Tac Mobile synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Tic-Tac Mobile für Updatevorgänge verwendet werden. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern, müssen Sie sicherstellen, dass die Tic-Tac Mobile-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|
   |---|---|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |title|String|
   |emails[type eq"work"].value|String|
   |preferredLanguage|String|
   |externalId|String|
   |userType|String|
   |locale|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

1. Wenn Sie Bereichsfilter konfigurieren möchten, folgen Sie den Anleitungen im Tutorial [Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Um den Azure AD-Bereitstellungsdienst für Tic-Tac Mobile zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Screenshot: Bereitstellungsstatus „Ein“](common/provisioning-toggle-on.png)

1. Definieren Sie die Benutzer oder Gruppen, die in Tic-Tac Mobile bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Screenshot: Bereitstellungsbereich](common/provisioning-scope.png)

1. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Screenshot: Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird.

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung

Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen.

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
1. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
1. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zum Quarantänestatus finden Sie unter [Anwendungsbereitstellung im Quarantänestatus](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)