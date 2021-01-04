---
title: 'Tutorial: Konfigurieren von Workteam für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Workteam konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: f465438a6be80a1b487a4df7d3ee8bd38be9c102
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94354205"
---
# <a name="tutorial-configure-workteam--for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Workteam für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Workteam und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Workteam zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* [Einen Workteam-Mandanten](https://workte.am/pricing.html)
* Ein Benutzerkonto in Workteam mit Administratorberechtigungen.

## <a name="assigning-users-to-workteam"></a>Zuweisen von Benutzern zu Workteam 

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Workteam benötigen. Anschließend können Sie Workteam diese Benutzer und/oder Gruppen zuweisen, indem Sie den folgenden Anweisungen folgen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workteam"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Workteam 

* Es wird empfohlen, Workteam einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Workteam müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="setup-workteam--for-provisioning"></a>Einrichten von Workteam für die Bereitstellung

Bevor Sie Workteam für die automatische Benutzerbereitstellung in Azure AD konfigurieren, müssen Sie in Workteam die SCIM-Bereitstellung aktivieren.

1. Melden Sie sich bei [Workteam](https://app.workte.am/account/signin) an. Klicken Sie auf **Organization settings** (Organisationseinstellungen) > **SETTINGS** (EINSTELLUNGEN).

    ![Screenshot der Workteam-Benutzeroberfläche mit den hervorgehobenen Optionen „Organization settings“ (Organisationseinstellungen) und „SETTINGS“ (EINSTELLUNGEN).](media/workteam-provisioning-tutorial/settings.png)

2. Scrollen Sie nach unten, und aktivieren Sie die Bereitstellungsfunktionen von Workteam.

    ![Screenshot des unteren Bereichs des Abschnitts „SETTINGS“ (EINSTELLUNGEN) mit hervorgehobenem Zahnradsymbol „SCIM User Provisioning“ (SCIM-Benutzerbereitstellung).](media/workteam-provisioning-tutorial/icon.png)

3. Kopieren Sie die **Basis-URL** und das **Bearertoken**. Diese Werte werden im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Workteam-Anwendung in das Feld **Mandanten-URL** bzw. **Geheimes Token** eingegeben.

    ![Screenshot des Dialogfelds „SCIM Settings“ (SCIM-Einstellungen), in dem die Textfelder „BASE URL“ (Basis-URL) und „BEARER TOKEN“ (BEARERTOKEN) hervorgehoben sind.](media/workteam-provisioning-tutorial/scim.png)


## <a name="add-workteam--from-the-gallery"></a>Hinzufügen von Workteam aus dem Katalog

Um Workteam für die automatische Benutzerbereitstellung in Azure AD konfigurieren zu können, müssen Sie Ihrer Liste der verwalteten SaaS-Anwendungen Workteam aus dem Azure AD-Anwendungskatalog hinzufügen.

**Führen Sie die folgenden Schritte aus, um Workteam aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld den Namen **Workteam** ein, wählen Sie im Ergebnisbereich die Anwendung **Workteam** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Workteam in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workteam"></a>Konfigurieren der automatischen Benutzerbereitstellung für Workteam  

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Workteam auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Workteam aktivieren. Folgen Sie dazu den Anweisungen im [SSO-Tutorial zu Workteam](workteam-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-workteam--in-azure-ad"></a>Gehen Sie wie folgt vor, um die automatische Benutzerbereitstellung für Workteam in Azure AD zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Workteam** aus.

    ![Workteam-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt „Administratoranmeldeinformationen“ die zuvor abgerufenen Werte für **Basis-URL** und **Bearertoken** in das Feld **Mandanten-URL** bzw. **Geheimes Token** ein. Klicken Sie auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Workteam herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Workteam-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch mal.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Workteam synchronisieren** aus.

    ![Workteam-Benutzerzuordnungen](media/workteam-provisioning-tutorial/usermapping.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Workteam synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Workteam für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Workteam-Benutzerattribute](media/workteam-provisioning-tutorial/userattribute.png)

11. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Um den Azure AD-Bereitstellungsdienst für Workteam zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

13. Legen Sie die Benutzer und/oder Gruppen fest, die in Workteam bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

14. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die nachfolgenden Synchronisierungen. Unter [Überprüfen des Status der Benutzerbereitstellung](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) finden Sie weitere Informationen dazu, wie lange das Bereitstellen von Benutzern und/oder Gruppen dauert.

Im Abschnitt **Aktueller Status** können Sie den Fortschritt überwachen und Links zu Ihrem Bereitstellungsaktivitätsbericht folgen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst für Workteam ausgeführt werden. Weitere Informationen finden Sie unter [Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Informationen zum Lesen der Azure AD-Bereitstellungsprotokolle finden Sie unter [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
