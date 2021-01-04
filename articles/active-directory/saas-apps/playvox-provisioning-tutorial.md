---
title: 'Tutorial: Konfigurieren von Playvox für die automatische Benutzerbereitstellung mithilfe von Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Benutzerkonten aus Azure AD automatisch in Playvox bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862478"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Playvox für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in Playvox als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Nach der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer oder Gruppen in [Playvox](https://www.playvox.com) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie Antworten auf häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Playvox
> * Entfernen von Benutzern aus Playvox, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Playvox

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial vorgestellte Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) zum Konfigurieren der Bereitstellung, zum Beispiel Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder globaler Administrator.
* Ein Benutzerkonto in [Playvox](https://www.playvox.com) mit Superadministratorberechtigungen

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung

1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).

2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.

3. Legen Sie fest, welche Daten [zwischen Azure AD und Playvox zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>Schritt 2: Konfigurieren von Playvox für die Unterstützung der Bereitstellung mit Azure AD

1. Melden Sie sich bei der Playvox-Verwaltungskonsole an, und navigieren Sie zu **Einstellungen > API-Schlüssel**.

2. Wählen Sie **API-Schlüssel erstellen** aus.

    ![Partieller Screenshot mit der Stelle, an der sich die Schaltfläche „API-Schlüssel erstellen“ auf der Playvox-Benutzeroberfläche befindet.](media/playvox-provisioning-tutorial/create.png)

3. Geben Sie einen aussagekräftigen Namen für den API-Schlüssel ein, und wählen Sie **Speichern** aus. Wählen Sie nach dem Generieren des API-Schlüssels **Schließen** aus.

4. Wählen Sie für den erstellten API-Schlüssel das Symbol **Details** aus.

    ![Partieller Screenshot mit der Stelle, an der sich das Symbol zum Anzeigen von Details (ein Vergrößerungsglas) auf der Playvox-Benutzeroberfläche befindet.](media/playvox-provisioning-tutorial/api.png)

5. Kopieren und speichern Sie den Wert **BASE64-Schlüssel**. Dieser Wert wird später im Azure-Portal auf der Registerkarte **Bereitstellung** der Playvox-Anwendung im Textfeld **Geheimes Token** eingegeben.

    ![Screenshot des Meldungsfelds „Details zum API-Schlüssel" mit hervorgehobenem BASE64-Schlüsselwert.](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Playvox aus dem Azure AD-Anwendungskatalog

Um mit der Verwaltung der Bereitstellung für Playvox zu beginnen, fügen Sie Playvox aus dem Anwendungskatalog zu Ihrem Azure AD-Mandanten hinzu. Weitere Informationen finden Sie unter [Schnellstart: Hinzufügen einer Anwendung zu Ihrem Azure Active Directory (Azure AD)-Mandanten](../manage-apps/add-application-portal.md).

Wenn Sie Playvox zuvor für einmaliges Anmelden (SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Wir empfehlen jedoch, beim erstmaligen Testen der Integration eine separate App zu erstellen.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4: Definieren der Benutzer für den Bereitstellungsbereich

Mit dem Azure AD-Bereitstellungsdienst legen Sie anhand der Zuweisung zur Anwendung oder anhand von Attributen für den Benutzer oder die Gruppe fest, wer in die Bereitstellung einbezogen werden soll. Um festzulegen, wer anhand der Zuweisung für Ihre App bereitgestellt werden soll, lesen Sie [Verwalten von Benutzerzuweisungen für eine App in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md). Hier erfahren Sie, wie Sie der Anwendung Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe festlegen möchten, wer bereitgestellt wird, können Sie einen der unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden.

Beachten Sie die folgenden Punkte:

* Beim Zuweisen von Benutzern zu Playvox müssen Sie eine andere Rolle als Standardzugriff auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen.

* Fangen Sie klein an. Testen Sie die Bereitstellung mit wenigen Benutzern oder Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer oder Gruppen festgelegt ist, können Sie der App nur ein oder zwei Benutzer oder Gruppen zuweisen und so die Größe des Bereichs steuern. Wenn der Bereitstellungsbereich alle Benutzer und Gruppen umfasst, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben, um die Größe des Tests einzuschränken.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in Playvox

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen anhand von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

So konfigurieren Sie die automatische Benutzerbereitstellung für Playvox in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen** aus.

    ![Partieller Screenshot des Azure-Portals mit den hervorgehobenen Elementen „Unternehmensanwendungen“ und „Alle Anwendungen“](common/enterprise-applications.png)

2. Suchen Sie in der Anwendungsliste nach **Playvox**, und wählen Sie diese Anwendung aus.

    ![Partieller Screenshot der Anwendungsliste, in der das Feld für die Suche nach Anwendungen hervorgehoben ist.](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Partieller Screenshot mit dem Menüelement „Bereitstellung“.](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Partieller Screenshot der Registerkarte „Bereitstellung“, auf der im Dropdown-Listenfeld „Bereitstellungsmodus“ die Option „Automatisch“ ausgewählt ist.](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** Ihre Playvox-**Mandanten-URL** wie folgt ein:

    `https://{tenant}.playvox.com/scim/v1`

    Geben Sie das **geheime Token** ein, das Sie zuvor in Schritt 2 kopiert haben. Wählen Sie **Verbindung testen** aus, um sicherzustellen, dass Azure AD eine Verbindung mit Playvox herstellen kann. Vergewissern Sie sich im Fall eines Verbindungsfehlers, dass Ihr Playvox-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Partieller Screenshot des Abschnitts „Administratoranmeldeinformationen“ mit den Textfeldern „Mandanten-URL“ und „Geheimnis Token“ und dem hervorgehobenen Link „Verbindung testen“.](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Textfeld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen bei Bereitstellungsfehlern erhalten soll. Aktivieren Sie dann das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Partieller Screenshot mit dem Textfeld „Benachrichtigungs-E-Mail“ und dem Kontrollkästchen zum Aktivieren der E-Mail-Benachrichtigung.](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Playvox synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Playvox synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden verwendet, um die Benutzerkonten in Playvox für Updatevorgänge abzugleichen. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die Playvox-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

   |Attribut|Typ|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;|
   |aktiv|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|

10. Wenn Sie Bereichsfilter konfigurieren möchten, folgen Sie den Anleitungen im Tutorial [Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für Playvox zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Partieller Screenshot des Abschnitts „Einstellungen“, in dem die Option „Bereitstellungsstatus“ auf „Ein“ eingestellt ist.](common/provisioning-toggle-on.png)

12. Definieren Sie unter **Einstellungen** auch noch die Benutzer oder Gruppen, die für Playvox bereitgestellt werden sollen, indem Sie unter **Bereich** die gewünschten Werte auswählen.

    ![Partieller Screenshot des Abschnitts „Einstellungen“ mit dem Dropdown-Listenfeld „Bereich“.](common/provisioning-scope.png)

13. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Partieller Screenshot mit den Optionen „Speichern“ und „Verwerfen“.](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als spätere Zyklen. Später erfolgen die Zyklen etwa alle 40 Minuten, sofern der Azure AD-Bereitstellungsdienst ausgeführt wird.

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung

Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

* Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
* Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
* Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zum Quarantänestatus finden Sie unter [Anwendungsbereitstellung im Quarantänestatus](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)