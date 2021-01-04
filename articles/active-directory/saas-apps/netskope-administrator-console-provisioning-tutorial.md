---
title: 'Tutorial: Konfigurieren von Netskope User Authentication für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Netskope User Authentication konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: 46766a7439185714648572f3f1b9d51ef96abba6
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357469"
---
# <a name="tutorial-configure-netskope-user-authentication-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Netskope User Authentication für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Netskope User Authentication und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Netskope User Authentication zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Netskope User Authentication-Mandanten](https://www.netskope.com/)
* Ein Benutzerkonto in Netskope User Authentication mit Administratorberechtigungen.

## <a name="assigning-users-to-netskope-user-authentication"></a>Zuweisen von Benutzern zu Netskope User Authentication

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Netskope User Authentication benötigen. Anschließend können Sie Netskope User Authentication diese Benutzer und/oder Gruppen zuweisen, indem Sie den folgenden Anweisungen folgen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-user-authentication"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Netskope User Authentication

* Es wird empfohlen, Netskope User Authentication einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Netskope User Authentication müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-netskope-user-authentication-for-provisioning"></a>Einrichten von Netskope User Authentication für die Bereitstellung

1. Melden Sie sich bei der [Netskope User Authentication-Verwaltungskonsole](https://netskope.goskope.com/) an. Navigieren Sie zu **Startseite > Einstellungen**.

    ![Netskope User Authentication-Verwaltungskonsole](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navigieren Sie zu **Tools**. Navigieren Sie unter dem Menü **Tools** zu **Verzeichnistools > SCIM-INTEGRATION**.

    ![Netskope User Authentication: Tools](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope User Authentication: SCIM hinzufügen](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Scrollen Sie nach unten, und klicken Sie auf die Schaltfläche **Token hinzufügen**. Geben Sie im Dialogfeld **OAuth-Clientname hinzufügen** einen **Clientnamen** an, und klicken Sie auf die Schaltfläche **Speichern**.

    ![Netskope User Authentication: Token hinzufügen](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope User Authentication: Clientname](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Kopieren Sie die Werte für **SCIM Server URL** und **TOKEN**. Diese Werte werden im Azure-Portal auf der Registerkarte „Bereitstellung“ der Netskope User Authentication-Anwendung in das Feld „Mandanten-URL“ bzw. „Geheimes Token“ eingegeben.

    ![Netskope User Authentication: Token erstellen](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Hinzufügen von Netskope User Authentication aus dem Katalog

Vor dem Konfigurieren von Netskope User Authentication für die automatische Benutzerbereitstellung mit Azure AD müssen Sie Ihrer Liste der verwalteten SaaS-Anwendungen Netskope User Authentication aus dem Azure AD-Anwendungskatalog hinzufügen.

**Führen Sie die folgenden Schritte aus, um Netskope User Authentication aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld den Namen **Netskope User Authentication** ein, wählen Sie im Ergebnisbereich die Anwendung **Netskope User Authentication** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Netskope User Authentication in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-user-authentication"></a>Konfigurieren der automatischen Benutzerbereitstellung für Netskope User Authentication 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Netskope User Authentication auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Netskope User Authentication aktivieren. Folgen Sie dazu den Anweisungen im [SSO-Tutorial zu Netskope User Authentication](./netskope-cloud-security-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

> [!NOTE]
> Weitere Informationen zum SCIM-Endpunkt von Netskope User Authentication finden Sie [hier](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-user-authentication-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Netskope User Authentication in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **Netskope User Authentication** aus.

    ![Netskope User Authentication-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen**  den Wert für **SCIM Server URL** ein, den Sie zuvor in **Mandanten-URL** abgerufen haben. Geben Sie den Wert für **TOKEN** ein, den Sie zuvor unter **Geheimes Token** abgerufen haben. Klicken Sie auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Netskope User Authentication herstellen kann. Wenn die Verbindung nicht hergestellt werden kann, vergewissern Sie sich, dass Ihr Netskope User Authentication-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch mal.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Netskope User Authentication synchronisieren** aus.

    ![Netskope User Authentication: Benutzerzuordnungen](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Netskope User Authentication synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Netskope User Authentication für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Netskope User Authentication: Benutzerattribute](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Netskope User Authentication synchronisieren** aus.

    ![Netskope User Authentication: Gruppenzuordnungen](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit Netskope User Authentication synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Netskope User Authentication für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Netskope User Authentication: Gruppenattribute](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Netskope User Authentication zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Netskope User Authentication bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zum Bereitstellungsaktivitätsbericht folgen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst für Netskope User Authentication ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)