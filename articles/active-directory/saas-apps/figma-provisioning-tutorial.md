---
title: 'Tutorial: Konfigurieren von Figma für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Figma konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: ec509cc04db87602b3e29230253d9b363a5e30d5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353917"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Figma für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Figma und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Figma zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* [Einen Figma-Mandanten](https://www.figma.com/pricing/).
* Ein Benutzerkonto in Figma mit Administratorrechten.

## <a name="assign-users-to-figma"></a>Zuweisen von Benutzern zu Figma
Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Figma benötigen. Anschließend können Sie diese Benutzer bzw. Gruppen Figma wie folgt zuweisen:
 
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Figma

 * Es wird empfohlen, Figma einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Figma müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-figma-for-provisioning"></a>Einrichten von Figma für die Bereitstellung

Bevor Sie Figma für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie einige Bereitstellungsinformationen von Figma abrufen.

1. Melden Sie sich bei Ihrer [Figma-Verwaltungskonsole](https://www.Figma.com/) an. Klicken Sie auf das Zahnradsymbol neben Ihrem Mandanten.

    :::image type="content" source="media/Figma-provisioning-tutorial/image0.png" alt-text="Screenshot der Figma-Verwaltungskonsole. Ein Mandant mit dem Namen „AAD Scim Test“ wird angezeigt. Neben dem Mandanten ist ein Zahnradsymbol hervorgehoben." border="false":::

2. Navigieren Sie zu **General > Update Log in Settings** (Allgemein > Einstellungen für die Anmeldung aktualisieren).

    :::image type="content" source="media/Figma-provisioning-tutorial/figma03.png" alt-text="Screenshot der Registerkarte „Allgemein“ der Figma-Verwaltungskonsole. Unter „Anmeldung und Bereitstellung“ ist die Einstellung „Einstellungen für die Anmeldung aktualisieren“ hervorgehoben." border="false":::

3. Kopieren Sie die **Tenant ID** (Mandanten-ID). Dieser Wert wird verwendet, um die SCIM-Endpunkt-URL zu erstellen, die im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Figma-Anwendung im Feld **Mandanten-ID** eingegeben werden soll.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma-tenantid.png" alt-text="Screenshot des Abschnitts „SAML SSO“ der Figma-Verwaltungskonsole. Die Bezeichnung „Mandanten-ID“ und der Link „Kopieren“ sind hervorgehoben" border="false":::

4. Scrollen Sie nach unten, und klicken Sie auf **Generate API Token** (API-Token generieren).

    :::image type="content" source="media/Figma-provisioning-tutorial/token.png" alt-text="Screenshot des Abschnitts „SCIM-Bereitstellung“ der Figma-Verwaltungskonsole. Ein Link mit der Bezeichnung „API-Token generieren“ ist hervorgehoben." border="false":::

5. Kopieren Sie den Wert von **API Token**. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Figma-Anwendung in das Feld **Geheimes Token** eingegeben. 

    :::image type="content" source="media/Figma-provisioning-tutorial/figma04.png" alt-text="Screenshot einer Seite der Figma-Verwaltungskonsole. Unter „Mein Bereitstellungs-API-Token“ ist ein Platzhalter für das Token hervorgehoben." border="false":::

## <a name="add-figma-from-the-gallery"></a>Hinzufügen von Figma aus dem Katalog

Bevor Sie Figma für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Figma aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Figma** ein, wählen Sie im Ergebnisbereich **Figma** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Figma in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurieren der automatischen Benutzerbereitstellung in Figma 

In diesem Abschnitt werden die Schritte erläutert, mit denen Sie den Azure AD-Bereitstellungsdienst zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Figma auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD konfigurieren.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Figma aktivieren. Befolgen Sie hierzu die Anweisungen im [SSO-Tutorial zu Figma](figma-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Figma in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Figma** aus.

    ![Figma-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die URL `https://www.figma.com/scim/v2/<TenantID>` als **Mandanten-URL** ein. **TenantID** ist hierbei der Wert, den Sie zuvor von Figma abgerufen haben. Geben Sie den Wert für das **API-Token** im Feld **Geheimes Token** ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Figma herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Figma-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Figma synchronisieren** aus.

    ![Benutzerzuordnungen in Figma](media/Figma-provisioning-tutorial/figma05.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Figma synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Figma für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Benutzerattribute von Figma](media/Figma-provisioning-tutorial/figma06.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Figma zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** zu **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer bzw. Gruppen fest, die in Figma bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Figma ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)