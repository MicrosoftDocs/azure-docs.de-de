---
title: 'Tutorial: Konfigurieren von Apple Business Manager für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Benutzerkonten aus Azure AD automatisch für Apple Business Manager bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: b4f24c9beffcd67fb84940c2e159da615496d9aa
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180369"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Apple Business Manager für die automatische Benutzerbereitstellung



In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in Apple Business Manager als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD automatisch mithilfe des Azure AD-Bereitstellungsdiensts Benutzer für [Apple Business Manager](https://business.apple.com/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Apple Business Manager
> * Entfernen von Benutzern aus Apple Business Manager, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Apple Business Manager

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator).
* Ein Apple Business Manager-Konto mit der Rolle „Administrator“ oder „People Manager“.

> [!NOTE]
> Die Tokenübertragung an Azure AD und das Herstellen einer erfolgreichen Verbindung müssen in 4 Kalendertagen abgeschlossen sein, oder der Prozess muss erneut gestartet werden.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und Apple Business Manager zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Apple Business Manager für die Unterstützung der Bereitstellung mit Azure AD

1. Melden Sie sich in Apple Business Manager mit einem Konto mit der Rolle „Administrator“ oder „People Manager“ an.
2. Klicken Sie unten in der Seitenleiste auf „Settings“ (Einstellungen), klicken Sie unter „Organization Settings“ (Organisationseinstellungen) auf „Data Source“ (Datenquelle), und klicken Sie dann auf „Connect to Data Source“ (Verbindung mit Datenquelle herstellen).
3. Klicken Sie neben „SCIM“ auf „Connect“ (Verbinden), lesen Sie die Warnung sorgfältig durch, klicken Sie auf „Copy“ (Kopieren) und dann auf „Close“ (Schließen).
[Das Fenster „Connect to SCIM“ (Mit SCIM verbinden) mit einem Token und der Schaltfläche „Kopieren“ darunter] Lassen Sie dieses Fenster geöffnet, um die Mandanten-URL von Apple School Manager in Azure AD zu kopieren. Dies ist „https://federation.apple.com/feeds/business/scim“.

    ![Apple Business Manager](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Das geheime Token darf nicht mit anderen Personen als dem Azure AD-Administrator gemeinsam genutzt werden.

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Apple Business Manager aus dem Azure AD-Anwendungskatalog

Fügen Sie Apple Business Manager aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Apple Business Manager zu beginnen. Wenn Sie Apple Business Manager zuvor für das einmalige Anmelden (SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern zu Apple Business Manager müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für Apple Business Manager

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Apple Business Manager** aus.

    ![Apple Business Manager in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“ – automatisch](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die zuvor von Apple Business Manager abgerufenen Werte für **„SCIM 2.0-Basis-URL“ und „Zugriffstoken“** in die Felder **Mandanten-URL** und **Geheimes Token** ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Apple Business Manager herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Apple Business Manager-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Wenn die Verbindung erfolgreich hergestellt wurde, zeigt Apple Business Manager die SCIM-Verbindung als aktiv an. Dieser Vorgang kann bis zu 60 Sekunden dauern, bis Apple Business Manager den aktuellen Verbindungsstatus widerspiegelt.

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Apple Business Manager synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Apple Business Manager synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Apple Business Manager für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

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

11. Ändern Sie den **Bereitstellungsstatus** im Abschnitt „Einstellungen“ in **Ein**, um den Azure AD-Bereitstellungsdienst für Apple Business Manager zu aktivieren.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer und/oder Gruppen fest, die in Apple Business Manager bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

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
* [SCIM-Voraussetzungen für Apple Business Manager überprüfen](https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [Verwendung der Personen-ID in Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [SCIM zum Importieren von Benutzern in Apple Business Manager verwenden](https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [SCIM-Benutzer-Account-Konflikte in Apple Business Manager lösen](https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [Azure AD-Accounts in Apple Business Manager löschen](https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [SCIM-Aktivität in Apple Business Manager anzeigen](https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [Vorhandene SCIM-Token in Apple Business Manager verwalten](https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [Trennen der SCIM-Verbindung in Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd609be3a61)
* [Probleme mit der SCIM-Verbindung in Apple Business Manager beheben](https://support.apple.com/guide/apple-business-manager/apd403a0f3bd/web)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)