---
title: 'Tutorial: Konfigurieren von MediusFlow für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Benutzerkonten aus Azure AD für MediusFlow automatisch bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: 881309c040f6c1bdff758d17ab7f51e935437192
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607880"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von MediusFlow für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in MediusFlow als auch in Azure Active Directory (Azure AD) ausführen müssen, um automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD automatisch mithilfe des Azure AD-Bereitstellungsdiensts Benutzer und Gruppen für [MediusFlow](https://www.mediusflow.com/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in MediusFlow
> * Entfernen von Benutzern aus MediusFlow, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und MediusFlow
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in MediusFlow
> * Einmaliges Anmelden (SSO) für MediusFlow (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Ein aktives Media Flow-Abonnement mit einem Qualitätssicherungs- oder Produktionsmandanten.
* Ein Benutzerkonto in MediusFlow mit Administratorrugriffsrechten, um die Konfiguration in MediusFlow ausführen zu können.
* Die Unternehmen, die im MediusFlow-Mandanten hinzugefügt werden, in dem die Benutzer bereitgestellt werden sollen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Bestimmen Sie, welche Daten [zwischen Azure AD und MediusFlow zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von MediusFlow für die Unterstützung der Bereitstellung mit Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Aktivieren der Microsoft 365-App in MediusFlow
Beginnen Sie, indem Sie die folgenden Schritte ausführen, um den Zugriff auf die Azure AD-Anmeldung und die Azure AD-Konfigurationsfunktion in MediusFlow zu aktivieren:

#### <a name="user-login"></a>Benutzeranmeldung
Informationen zum Aktivieren des Anmeldeflows für Microsoft 365/Azure AD finden Sie in [diesem] Artikel (https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup).

#### <a name="user-transfer-configuration"></a>Benutzerübertragungskonfiguration
Informationen zum Aktivieren des Konfigurationsportals der Benutzer für die Bereitstellung aus Azure AD finden Sie in [diesem Artikel](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup).

#### <a name="configure-user-provisioning"></a>Benutzerbereitstellung konfigurieren

1.  Melden Sie sich bei der [MediusFlow-Verwaltungskonsole](https://office365.cloudapp.mediusflow.com/) an, indem Sie die Mandanten-ID angeben.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="Screenshot: MediusFlow-Verwaltungskonsole. Im ersten Integrationsschritt sind das Feld für den MediusFlow-Mandantennamen und die Schaltfläche „Authenticate“ (Authentifizieren) hervorgehoben." border="false":::

2. Überprüfen Sie die Verbindung mit Azure MediusFlow.

    ![Überprüfen](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Geben Sie die Azure AD-Mandanten-ID an.

    ![Angeben der Mandanten-ID](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Weitere Informationen zum Ermitteln dieser ID finden Sie unter [Häufig gestellte Fragen](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id).

4. Speichern Sie die Konfiguration.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="Screenshot: MediusFlow-Verwaltungskonsole mit dem vierten Integrationsschritt. Die Schaltfläche zum Speichern der Konfiguration ist hervorgehoben." border="false":::

5. Wählen Sie „Benutzerbereitstellung“ aus, und klicken Sie auf **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="Screenshot: MediusFlow-Verwaltungskonsole mit dem fünften Integrationsschritt. Die Schaltflächen „Use user provisioning“ (Benutzerbereitstellung verwenden) und „OK“ sind hervorgehoben." border="false":::

6. Klicken Sie auf **Geheimen Schlüssel generieren**. Kopieren und speichern Sie diesen Wert. Er wird im Azure-Portal auf der Registerkarte **Bereitstellung** Ihrer MediusFlow-Anwendung in das Feld **Geheimes Token** eingegeben.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="Screenshot: Registerkarte „User provisioning configuration“ (Konfiguration der Benutzerbereitstellung) in der MediusFlow-Verwaltungskonsole. Die Schaltflächen „Generate secret key“ (Geheimen Schlüssel generieren) und „Copy“ (Kopieren) sind hervorgehoben." border="false":::

7. Klicken Sie auf **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="Screenshot: MediusFlow-Verwaltungskonsole mit der Benachrichtigung, dass Benutzer zum Generieren eines neuen geheimen Schlüssels auf „OK“ klicken müssen. Die Schaltfläche „OK“ ist hervorgehoben." border="false":::

8. Damit die Benutzer mit einem vordefinierten Satz von Rollen, Unternehmen und anderen allgemeinen Konfigurationen in MediusFlow importiert werden können, müssen Sie dies zuerst konfigurieren. Fügen Sie zunächst die Konfiguration hinzu, indem Sie auf **Neue Konfiguration hinzufügen** klicken.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="Screenshot: Registerkarte „User provisioning configuration“ (Konfiguration der Benutzerbereitstellung) in der MediusFlow-Verwaltungskonsole. Die Schaltfläche „Add new configuration“ (Neue Konfiguration hinzufügen) ist hervorgehoben." border="false":::

9. Geben Sie die Standardeinstellungen für die Benutzer an. In dieser Ansicht ist es möglich, das Standardattribut festzulegen. Wenn die Standardeinstellungen in Ordnung sind, genügt es, nur einen gültigen Firmennamen bereitzustellen. Da diese Konfigurationseinstellungen aus MediusFlow abgerufen werden, müssen Sie zuerst konfiguriert werden. Weitere Informationen finden Sie im Abschnitt **Voraussetzungen** in diesem Artikel.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="Screenshot: Fenster „Add new configuration“ (Neue Konfiguration hinzufügen) in MediusFlow. Viele Einstellungen sind sichtbar, einschließlich Gebietsschemaeinstellungen, ein Filter und Benutzerrollen." border="false":::

10. Klicken Sie auf **Speichern**, um die Konfiguration zu speichern.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="Screenshot: Registerkarte „User provisioning configuration“ (Konfiguration der Benutzerbereitstellung) in der MediusFlow-Verwaltungskonsole. Die Schaltfläche „Speichern“ ist hervorgehoben." border="false":::

11. Um den Link zur Benutzerbereitstellung abzurufen, klicken Sie auf **SCIM-Link kopieren**. Kopieren und speichern Sie diesen Wert. Diese Werte werden im Azure-Portal auf der Registerkarte **Bereitstellung** Ihrer MediusFlow-Anwendung in das Feld **Mandanten-URL** eingegeben.
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="Screenshot: Registerkarte „User provisioning configuration“ (Konfiguration der Benutzerbereitstellung) in der MediusFlow-Verwaltungskonsole. Die Schaltfläche „Copy SCIM link“ (SCIM-Schaltfläche kopieren) ist hervorgehoben." border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von MediusFlow aus dem Azure AD-Anwendungskatalog

Fügen Sie MediusFlow aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung für MediusFlow zu beginnen. Wenn Sie MediusFlow zuvor für das einmalige Anmelden (SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu MediusFlow müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in MediusFlow 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in ServiceNow auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für MediusFlow in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **MediusFlow** aus.

    ![MediusFlow-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen**  den Wert für die Mandanten-URL ein, den Sie zuvor in **Mandanten-URL** abgerufen haben. Geben Sie den Wert für das geheime Token ein, den Sie zuvor unter **Geheimes Token** abgerufen haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit MediusFlow herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr MediusFlow-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

      ![Screenshot des Dialogfelds „Administratoranmeldeinformationen“, in dem Sie Ihre Mandanten-URL und das geheime Token eingeben können.](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit MediusFlow synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit MediusFlow synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute zum Abgleichen der Benutzerkonten in MediusFlow bei Updatevorgängen verwendet werden. Wenn Sie sich dafür entscheiden, das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) zu ändern, müssen Sie sicherstellen, dass die MediusFlow-API das Filtern von Benutzern anhand dieses Attributs unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|
   |---|---|
   |userName|String|
   |emails[type eq "work"].value|String|
   |name.displayName|String|
   |aktiv|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalID|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Verweis|


10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit MediusFlow synchronisieren** aus.

11. Überprüfen Sie im Abschnitt **Gruppenattribute** die Benutzerattribute, die von Azure AD mit MediusFlow synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in MediusFlow für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    | attribute | type |
    |--|--|
    | displayName | String |
    | externalID | String |
    | members | Verweis |

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für MediusFlow zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in MediusFlow bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

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