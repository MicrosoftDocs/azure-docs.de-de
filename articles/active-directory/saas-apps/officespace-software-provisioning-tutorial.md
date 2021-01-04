---
title: 'Tutorial: Konfigurieren von OfficeSpace Software für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in OfficeSpace Software konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: fc67d649e3a7cd094eb2c3d633244077abcab308
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359921"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von OfficeSpace Software für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in OfficeSpace Software und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in OfficeSpace Software zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* Einen [OfficeSpace Software-Mandanten](https://www.officespacesoftware.com/)
* Ein Benutzerkonto in OfficeSpace Software mit Administratorberechtigungen.

## <a name="assigning-users-to-officespace-software"></a>Zuweisen von Benutzern zu OfficeSpace Software

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf OfficeSpace Software benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen OfficeSpace Software anhand der folgenden Anweisungen zuweisen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Wichtige Tipps zum Zuweisen von Benutzern zu OfficeSpace Software

* Es wird empfohlen, OfficeSpace Software einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers an OfficeSpace Software müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-officespace-software-for-provisioning"></a>Einrichten von OfficeSpace Software für die Bereitstellung

1. Melden Sie sich bei Ihrer [OfficeSpace Software-Verwaltungskonsole](https://support.officespacesoftware.com/hc) an. Navigieren Sie zu **Einstellungen > Connectors**.

    ![OfficeSpace Software-Verwaltungskonsole](media/officespace-software-provisioning-tutorial/settings.png)

2.  Navigieren Sie zu **Verzeichnissynchronisierung > SCIM**.

    ![OfficeSpace Software: SCIM hinzufügen](media/officespace-software-provisioning-tutorial/scim.png)

3.  Kopieren Sie das **SCIM-Authentifizierungstoken**. Dieser Wert wird auf der Registerkarte „Bereitstellung“ für Ihre OfficeSpace Software-Anwendung im Azure-Portal in das Feld „Geheimes Token“ eingegeben.

    ![OfficeSpace Software: Token erstellen](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>Hinzufügen von OfficeSpace Software aus dem Katalog

Bevor Sie OfficeSpace Software für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie OfficeSpace Software aus dem Azure AD-Anwendungskatalog zur Liste der verwalteten SaaS-Anwendungen hinzufügen.

**Um OfficeSpace Software aus dem Azure AD-Anwendungskatalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **OfficeSpace Software** ein, wählen Sie im Ergebnisbereich **OfficeSpace Software** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![OfficeSpace Software in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Konfigurieren der automatischen Benutzerbereitstellung in OfficeSpace Software 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in OfficeSpace Software auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für OfficeSpace Software aktivieren. Befolgen Sie hierzu die Anweisungen im [SSO-Tutorial zu OfficeSpace Software](./officespace-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>So konfigurieren Sie OfficeSpace Software für die automatische Benutzerbereitstellung in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **OfficeSpace Software** aus.

    ![OfficeSpace Software-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** das URL-Format `https://<subdomain>.officespacesoftware.com/api/scim/v2/` ein. Beispiel: `https://contoso.officespacesoftware.com/api/scim/v2/`. Geben Sie den Wert des **SCIM-Authentifizierungstokens** ein, den Sie zuvor unter **geheimes Token** abgerufen haben. Klicken Sie auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit OfficeSpace Software herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr OfficeSpace Software-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit OfficeSpace Software synchronisieren** aus.

    ![OfficeSpace Software: Benutzerzuordnungen](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit OfficeSpace Software synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in OfficeSpace Software für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![OfficeSpace Software: Benutzerattribute](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Um den Azure AD-Bereitstellungsdienst für OfficeSpace Software zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

13. Legen Sie die Benutzer und/oder Gruppen fest, die in OfficeSpace Software bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

14. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. In diesen Berichten sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in OfficeSpace Software ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)