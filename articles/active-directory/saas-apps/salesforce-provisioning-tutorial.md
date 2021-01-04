---
title: 'Tutorial: Konfigurieren von Salesforce für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, welche Schritte in Salesforce und Azure AD ausgeführt werden müssen, um Benutzerkonten von Azure AD automatisch in Salesforce bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 2a4d3f3f9465b8813cdf6ee26760d819d73a08c1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94353103"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Salesforce für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in Salesforce und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in Salesforce automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Einen Azure Active Directory-Mandanten
* Einen Salesforce.com-Mandanten

> [!IMPORTANT]
> Bei Verwendung eines Salesforce.com-Testkontos können Sie die automatisierte Benutzerbereitstellung nicht konfigurieren. Bei Testkonten ist der erforderliche API-Zugriff erst nach dem Erwerb aktiviert. Sie können diese Einschränkung umgehen, indem Sie für dieses Tutorial ein [kostenloses Entwicklerkonto](https://developer.salesforce.com/signup) verwenden.

Wenn Sie eine Salesforce Sandbox-Umgebung verwenden, rufen Sie das [Tutorial: Azure Active Directory-Integration in Salesforce Sandbox](./salesforce-sandbox-tutorial.md)auf.

## <a name="assigning-users-to-salesforce"></a>Zuweisen von Benutzern zu Salesforce

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer oder Gruppen in Azure AD Zugriff auf Ihre Salesforce-App benötigen. Nach dieser Entscheidung können Sie die entsprechenden Benutzer und Gruppen gemäß der Anleitung unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) Ihrer Salesforce-App zuweisen.

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Salesforce

* Es wird empfohlen, Salesforce einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Wenn Sie einen Benutzer zu Salesforce zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

    > [!NOTE]
    > Diese App importiert als Teil des Bereitstellungsprozesses Profile aus Salesforce, die der Kunde beim Zuweisen von Benutzern in Azure AD auswählen kann. Beachten Sie, dass die aus Salesforce importierten Profile in Azure AD als Rollen angezeigt werden.

## <a name="enable-automated-user-provisioning"></a>Aktivieren der automatisierten Benutzerbereitstellung

Dieser Abschnitt führt Sie durch das Herstellen einer Verbindung von Azure AD mit der [Salesforce-API v40 zur Bereitstellung von Benutzerkonten](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm) sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in Salesforce basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

> [!Tip]
> Sie können auch das SAML-basierte einmalige Anmelden für Salesforce aktivieren. Befolgen Sie dazu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurieren der automatischen Bereitstellung von Benutzerkonten

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Salesforce aktivieren.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

2. Wenn Sie Salesforce bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer Salesforce-Instanz. Wählen Sie andernfalls **Hinzufügen** aus, und suchen Sie im Anwendungskatalog nach **Salesforce**. Wählen Sie „Salesforce“ in den Suchergebnissen aus, und fügen Sie die Anwendung Ihrer Anwendungsliste hinzu.

3. Wählen Sie Ihre Salesforce-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung** aus.

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Seite für die Salesforce-Bereitstellung mit dem Bereitstellungsmodus „Automatisch“ und weiteren Werten, die Sie festlegen können](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die folgenden Konfigurationseinstellungen an:

    a. Geben Sie im Textfeld **Administratorbenutzername** den Namen eines Salesforce-Kontos ein, dem das Profil **Systemadministrator** in „Salesforce.com“ zugewiesen ist.

    b. Geben Sie im Textfeld **Administratorkennwort** das Kennwort für dieses Konto ein.

6. Um Ihr Salesforce-Sicherheitstoken abzurufen, öffnen Sie eine neue Registerkarte, und melden Sie sich mit dem gleichen Salesforce-Administratorkonto an. Klicken Sie in der rechten oberen Ecke der Seite auf Ihren Namen und dann auf **Meine Einstellungen**.

    ![Screenshot: Ausgewählter Link „Einstellungen“](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Automatische Benutzerbereitstellung aktivieren")

7. Klicken Sie im linken Navigationsbereich auf **Meine persönlichen Informationen** , um den entsprechenden Abschnitt zu erweitern, und dann auf **Mein Sicherheitstoken zurücksetzen**.
  
    ![Screenshot: Ausgewählte Option „Reset My Security Token“ (Mein Sicherheitstoken zurücksetzen) unter „My Personal Information“ (Meine persönlichen Informationen)](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Automatische Benutzerbereitstellung aktivieren")

8. Klicken Sie auf der Seite **Sicherheitstoken zurücksetzen** auf die Schaltfläche **Sicherheitstoken zurücksetzen**.

    ![Screenshot: Seite „Reset Security Token“ (Sicherheitstoken zurücksetzen) mit erklärendem Text und der Option „Reset Security Token“ (Sicherheitstoken zurücksetzen)](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Automatische Benutzerbereitstellung aktivieren")

9. Überprüfen Sie den E-Mail-Posteingang dieses Administratorkontos. Achten Sie auf eine E-Mail von Salesforce.com, die das neue Sicherheitstoken enthält.

10. Kopieren Sie das Token, wechseln Sie zu Ihrem Azure AD-Fenster, und fügen Sie es in das Feld **Geheimes Token** ein.

11. Die **Mandanten-URL** muss eingegeben werden, wenn sich die Instanz von Salesforce in der Salesforce Government Cloud befindet. Andernfalls ist die Angabe optional. Geben Sie die Mandanten-URL im Format „https://\<your-instance\>.my.salesforce.com“ ein. Ersetzen Sie dabei \<your-instance\> durch den Namen Ihrer Salesforce-Instanz.

12. Klicken Sie im Azure-Portal auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer Salesforce-App herstellen kann.

13. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das unten gezeigte Kontrollkästchen.

14. Klicken Sie auf **Speichern**.  

15. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit Salesforce synchronisieren** aus.

16. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Salesforce synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Salesforce für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

17. Um den Azure AD-Bereitstellungsdienst für Salesforce zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt „Einstellungen“ in **Ein**.

18. Klicken Sie auf **Speichern**.

> [!NOTE]
> Nachdem die Benutzer in der Salesforce-Anwendung bereitgestellt wurden, muss der Administrator die sprachspezifischen Einstellungen für diese Benutzer konfigurieren. Weitere Informationen zur Sprachkonfiguration finden Sie in [diesem Artikel](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5).

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die Salesforce im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Beachten Sie, dass die Erstsynchronisierung länger dauert als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer Salesforce-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Häufige Probleme
* Wenn beim Autorisieren des Zugriff auf Salesforce Probleme auftreten, stellen Sie Folgendes sicher:
    * Die verwendeten Anmeldeinformationen bieten Administratorzugriff auf Salesforce.
    * Die von Ihnen verwendete Version von Salesforce unterstützt Webzugriff (z. B. die Salesforce-Editionen Developer, Enterprise, Sandbox und Unlimited).
    * Der Web-API-Zugriff ist für den Benutzer aktiviert.
* Der Azure AD-Bereitstellungsdienst unterstützt die Sprache, das Gebietsschema und die Zeitzone der Bereitstellung für einen Benutzer. Diese Attribute befinden sich in den Standardattributzuordnungen, verfügen jedoch über kein Standardquellattribut. Stellen Sie sicher, dass Sie das Standardquellattribut auswählen und dass das Quellattribut das Format hat, das von Salesforce erwartet wird. Beispielsweise hat „localeSidKey“ für „english(UnitedStates)“ den Wert „en_US“. Überprüfen Sie die [hier](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) bereitgestellte Anleitung, um das richtige localeSidKey-Format zu ermitteln. Die languageLocaleKey-Formate finden Sie [hier](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5). Zusätzlich zum richtigen Format müssen Sie möglicherweise auch sicherstellen, dass die Sprache für die Benutzer aktiviert ist, wie [hier](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) beschrieben. 
* **SalesforceLicenseLimitExceeded:** Der Benutzer konnte in der Zielanwendung nicht erstellt werden, da für diesen Benutzer keine Lizenzen verfügbar sind. Erwerben Sie entweder zusätzliche Lizenzen für die Zielanwendung, oder überprüfen Sie die Konfiguration der Benutzerzuweisungen und Attributzuordnungen, um sicherzustellen, dass die richtigen Attribute den richtigen Benutzern zugewiesen sind.
* **SalesforceDuplicateUserName:** Der Benutzer kann nicht bereitgestellt werden, da sein Salesforce.com-Benutzername bereits in einem anderen Salesforce.com-Mandanten vorhanden ist.  In Salesforce.com müssen Werte für das Username-Attribut für alle Salesforce.com-Mandanten eindeutig sein.  Standardmäßig wird der „userPrincipalName“ eines Benutzers in Azure Active Directory sein „Username“ in Salesforce.com.   Sie haben zwei Möglichkeiten.  Eine Möglichkeit besteht darin, den Benutzer mit dem doppelten „Username“ im anderen Salesforce.com-Mandanten zu suchen und umzubenennen, wenn Sie diesen anderen Mandanten ebenfalls verwalten.  Die andere Möglichkeit ist das Entfernen des Zugriffs des Azure Active Directory-Benutzers auf den Salesforce.com-Mandanten, in dem Ihr Verzeichnis integriert ist. Dieser Vorgang wird beim nächsten Synchronisierungsversuch wiederholt. 
* **SalesforceRequiredFieldMissing:** Für Salesforce müssen bestimmte Attribute des Benutzers vorhanden sein, damit der Benutzer erfolgreich erstellt oder aktualisiert werden kann. Für diesen Benutzer fehlt eines der erforderlichen Attribute. Stellen Sie sicher, dass Attribute wie „email“ und „alias“ für alle Benutzer aufgefüllt werden, die Sie in Salesforce bereitstellen möchten. Sie können Benutzer, für die diese Attribute nicht vorhanden sind, mithilfe [attributbasierter Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ausschließen. 
* Die Standardattributzuordnung für die Bereitstellung in Salesforce enthält den Ausdruck „SingleAppRoleAssignments“, um „appRoleAssignments“in Azure AD zu „ProfileName“ in Salesforce zuzuordnen. Stellen Sie sicher, dass die Benutzer nicht über mehrere Anwendungsrollenzuweisungen in Azure AD verfügen, da die Attributzuordnung nur die Bereitstellung einer Rolle unterstützt. 
* Salesforce erfordert, dass E-Mail-Updates manuell genehmigt werden, bevor sie geändert werden. Folglich werden möglicherweise mehrere Einträge in den Bereitstellungsprotokollen angezeigt, um die E-Mail des Benutzers zu aktualisieren (bis die E-Mail-Änderung genehmigt wurde).


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](./salesforce-tutorial.md)