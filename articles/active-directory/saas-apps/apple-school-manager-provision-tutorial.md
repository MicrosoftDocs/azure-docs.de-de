---
title: 'Tutorial: Konfigurieren von Apple School Manager für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie Benutzerkonten aus Azure AD automatisch für Apple School Manager bereitstellen und die Bereitstellung automatisch wieder aufheben.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: be21151be719098dd6ec65f369ea6fc81ff45a98
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591836"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Apple School Manager für die automatische Benutzerbereitstellung



In diesem Tutorial werden die Schritte beschrieben, die Sie in Apple School Manager und in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Nach der Konfiguration stellt Azure AD automatisch mithilfe des Azure AD-Bereitstellungsdiensts Benutzer für [Apple School Manager](https://school.apple.com/) bereit bzw. hebt deren Bereitstellung automatisch auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Apple School Manager
> * Entfernen von Benutzern aus Apple School Manager, wenn diese keinen Zugriff mehr benötigen
> * Ständiges Synchronisieren bestimmter Benutzerattribute zwischen Azure AD und Apple School Manager

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) zur Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder globaler Administrator) 
* Ein Apple School Manager-Konto mit der Rolle „Administrator“, „Standortmanager“ oder „Personenmanager“

> [!NOTE]
> Die Tokenübertragung an Azure AD und das erfolgreiche Herstellen einer Verbindung müssen innerhalb von 4 Kalendertagen abgeschlossen werden. Ansonsten muss der Prozess neu gestartet werden.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und Apple School Manager zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Apple School Manager für die Unterstützung der Bereitstellung mit Azure AD

1. Melden Sie sich in Apple School Manager mit einem Konto mit der Rolle „Administrator“, „Standortmanager“ oder „Personenmanager“ an.
2. Klicken Sie unten in der Seitenleiste auf „Einstellungen“, dann unter „Einstellungen der Organisation“ auf „Datenquelle“ und schließlich auf „Mit Datenquelle verbinden“.
3. Klicken Sie neben „SCIM“ auf „Verbinden“, lesen Sie die Warnung sorgfältig durch, klicken Sie auf „Kopieren“ und dann auf „Schließen“.
[Das Fenster „Connect to SCIM“ (Mit SCIM verbinden) mit einem Token und der Schaltfläche „Kopieren“ darunter] Lassen Sie dieses Fenster geöffnet, um die Mandanten-URL von Apple Business Manager in Azure AD zu kopieren. Diese ist: „https://federation.apple.com/feeds/school/scim“

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Das geheime Token sollte nur an den Azure AD-Administrator weitergegeben werden.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Apple School Manager über den Azure AD-Anwendungskatalog

Fügen Sie Apple School Manager über den Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung für Apple School Manager zu beginnen. Wenn Sie Apple School Manager bereits für das einmalige Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern zu Apple School Manager müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für Apple School Manager

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Apple School Manager** aus.

    ![Apple School Manager in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die zuvor in Apple School Manager abgerufenen Werte für **die SCIM 2.0-Basis-URL und das Zugriffstoken** in die Felder **Mandanten-URL** und **Geheimes Token** ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Apple School Manager herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Apple School Manager-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Wenn die Verbindung erfolgreich hergestellt wurde, zeigt Apple School Manager die SCIM-Verbindung als aktiv an. Es kann bis zu 60 Sekunden dauern, bis Apple School Manager den aktuellen Verbindungsstatus anzeigt.

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Klicken Sie im Abschnitt **Zuordnungen** auf **Synchronize Azure Active Directory Users to Apple School Manager** (Azure Active Directory-Benutzer mit Apple School Manager synchronisieren).

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Apple School Manager synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Apple School Manager für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|
   |---|---|
   |aktiv|Boolean|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.givenName|String|
   |externalId|String|
   |locale|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Ändern Sie den **Bereitstellungsstatus** im Abschnitt „Einstellungen“ in **Ein**, um den Azure AD-Bereitstellungsdienst für Apple School Manager zu aktivieren.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer und/oder Gruppen fest, die für Apple School Manager bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird.

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung

Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [SCIM-Voraussetzungen für Apple School Manager überprüfen](https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Verwendung der Personen-ID in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [SCIM zum Importieren von Benutzern in Apple School Manager verwenden](https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [SCIM-Benutzer-Account-Konflikte in Apple School Manager lösen](https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Azure AD-Accounts in Apple School Manager löschen](https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [SCIM-Aktivität in Apple School Manager anzeigen](https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Vorhandene SCIM-Token in Apple School Manager verwalten](https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [SCIM-Verbindung in Apple School Manager trennen](https://support.apple.com/guide/apple-school-manager/apd609be3a61)
* [Probleme mit der SCIM-Verbindung in Apple School Manager beheben](https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)