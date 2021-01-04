---
title: 'Tutorial: Konfigurieren von Bonusly für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Bonusly konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d8c3f64e5cb5269bfe7e555615f874ac3443c6eb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357826"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Bonusly für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Bonusly und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Bonusly zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial beschriebene Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Einen Azure AD-Mandanten
* Einen [Bonusly-Mandanten](https://bonus.ly/pricing)
* Ein Benutzerkonto in Bonusly mit Administratorberechtigungen

> [!NOTE]
> Die Azure AD-Bereitstellungsintegration basiert auf der [Bonusly-REST-API](https://konghq.com/solutions/gateway/), die für Bonusly-Entwickler verfügbar ist.

## <a name="adding-bonusly-from-the-gallery"></a>Hinzufügen von Bonusly aus dem Katalog

Bevor Sie Bonusly für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Bonusly aus dem Azure AD-Anwendungskatalog zu Ihrer Liste verwalteter SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um Bonusly aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Bonusly** ein, wählen Sie im Ergebnisbereich **Bonusly** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Bonusly in der Ergebnisliste](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Zuweisen von Benutzern zu Bonusly

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden. 

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Bonusly benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen Bonusly wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Bonusly

* Es wird empfohlen, Bonusly einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Bonusly müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Konfigurieren der automatischen Benutzerbereitstellung in Bonusly

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Bonusly auf Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Bonusly aktivieren. Befolgen Sie dazu die Anweisungen im [SSO-Tutorial zu Bonusly](bonus-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Bonusly in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Unternehmensanwendungen** , **Alle Anwendungen** und dann **Bonusly** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **Bonusly** aus.

    ![Bonusly-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningTab.png" alt-text="Screenshot: Bonusly – Registerkarte „Bereitstellung“. Unter „Verwalten“ ist „Bereitstellung“ hervorgehoben." border="false":::

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png" alt-text="Screenshot: Listenfeld „Bereitstellungsmodus“, „Automatisch“ ist ausgewählt und hervorgehoben." border="false":::

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** wie in Schritt 6 beschrieben das **geheime Token** Ihres Bonusly-Kontos ein.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/secrettoken.png" alt-text="Screenshot des Abschnitts „Administratoranmeldeinformationen“. Das Feld „Geheimes Token“ ist leer, aber hervorgehoben." border="false":::

6. Das **geheime Token** für Ihr Bonusly-Konto finden Sie unter **Admin > Company > Integrations** (Administrator > Unternehmen > Integrationen). Klicken Sie im Abschnitt **If you want to code** (Wenn Sie programmieren möchten) auf **API > Create New API Access Token** (API > Neues API-Zugriffstoken erstellen), um ein neues geheimes Token zu erstellen.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png" alt-text="Screenshot des Bonusly-Menüs. Unter „Admin“ (Administrator) ist „Company“ (Unternehmen) hervorgehoben. Unter „Company“ (Unternehmen) ist „Integrations“ (Integrationen) hervorgehoben." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonsulyRestApi.png" alt-text="Screenshot: Abschnitt „If you want to code“ (Wenn Sie programmieren möchten) der Bonusly-Website, in dem „API“ hervorgehoben ist" border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/CreateToken.png" alt-text="Screenshot der Bonusly-Website. Die Registerkarte „Dienste“ ist geöffnet. Unter „Your API access tokens“ (Ihre API-Zugriffstoken) ist „Create new API access token“ (Neues API-Zugriffstoken erstellen) hervorgehoben." border="false":::

7. Geben Sie auf dem folgenden Bildschirm einen Namen für das Zugriffstoken in das bereitgestellte Textfeld ein, und klicken Sie dann auf **Create Api Key** (API-Schlüssel erstellen). Das neue Zugriffstoken wird für einige Sekunden in einem Popupfenster angezeigt.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token01.png" alt-text="Screenshot: Seite „New access token“ (Neues Zugriffstoken) der Bonusly-Website. Ein Feld ohne Beschriftung enthält die Zeichenfolge „My Token“, und die Schaltfläche „Create Api key“ (API-Schlüssel erstellen) ist hervorgehoben." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token02.png" alt-text="Screenshot der Bonusly-Website. Die Benachrichtigung „New access token created“ (Neues Zugriffstoken erstellt) gefolgt von einem nicht lesbaren Token wird angezeigt." border="false":::

8. Klicken Sie nach dem Auffüllen der in Schritt 5 gezeigten Felder auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Bonusly herstellen kann. Falls der Verbindungsaufbau fehlschlägt, stellen Sie sicher, dass Ihr Bonusly-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch einmal.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/TestConnection.png" alt-text="Screenshot: Abschnitt „Administratoranmeldeinformationen“ des Azure-Portals. Die Schaltfläche „Verbindung testen“ ist hervorgehoben." border="false":::

9. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/EmailNotification.png" alt-text="Screenshot: Leeres Feld „Benachrichtigungs-E-Mail“. Die Option „Bei Fehler E-Mail-Benachrichtigung senden“ wird angezeigt." border="false":::

10. Klicken Sie auf **Speichern**.

11. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Bonusly synchronisieren** aus.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserMappings.png" alt-text="Screenshot des Abschnitts „Zuordnungen“. Unter „Name“ ist die Option „Azure Active Directory-Benutzer mit Bonusly synchronisieren“ hervorgehoben." border="false":::

12. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Bonusly synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Bonusly für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserAttributeMapping.png" alt-text="Screenshot der Seite „Attributzuordnungen“. In einer Tabelle werden Azure Active Directory-Attribute, die entsprechenden Bonusly-Attribute und der Abgleichsstatus aufgeführt." border="false":::

13. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Um den Azure AD-Bereitstellungsdienst für Bonusly zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningStatus.png" alt-text="Screenshot der Seite „Einstellungen“. Der Umschalter „Bereitstellungsstatus“ ist auf „Aus“ festgelegt." border="false":::

15. Legen Sie die Benutzer und/oder Gruppen fest, die in Bonusly bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ScopeSync.png" alt-text="Screenshot: Listenfeld „Bereich“. Im Feld ist „Nur zugewiesene Benutzer und Gruppen synchronisieren“ ausgewählt." border="false":::

16. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/SaveProvisioning.png" alt-text="Screenshot: Bonusly – Seite „Provisioning“ (Bereitstellung), auf der die Schaltfläche „Save“ (Speichern) hervorgehoben ist" border="false":::

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Bonusly ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png