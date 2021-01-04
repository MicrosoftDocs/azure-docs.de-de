---
title: 'Tutorial: Konfigurieren von Soloinsight-CloudGate SSO für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und für das Aufheben der Bereitstellung von Benutzerkonten in Soloinsight-CloudGate SSO konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 5c42102c0704d7212411d6b86f4210ad8ecd885c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96347496"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Soloinsight-CloudGate SSO für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Soloinsight-CloudGate SSO und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Soloinsight-CloudGate SSO zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Soloinsight-CloudGate SSO-Mandanten](https://www.soloinsight.com/)
* Ein Benutzerkonto in Soloinsight-CloudGate SSO mit Administratorberechtigungen

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Zuweisen von Benutzern zu Soloinsight-CloudGate SSO

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Soloinsight-CloudGate SSO benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen Soloinsight-CloudGate SSO anhand der folgenden Anweisungen zuweisen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Soloinsight-CloudGate SSO

* Es wird empfohlen, Soloinsight-CloudGate SSO einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Soloinsight-CloudGate SSO müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Einrichten von Soloinsight-CloudGate SSO für die Bereitstellung

1. Melden Sie sich bei der [Soloinsight-CloudGate SSO-Verwaltungskonsole an](https://soloinsight.sigateway.com/login). Navigieren Sie zu **Administration > System Settings** (Verwaltung > Systemeinstellungen).

    ![Soloinsight-CloudGate SSO-Verwaltungskonsole](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Navigieren Sie zu **General** (Allgemein).

    ![Soloinsight-CloudGate SSO: Hinzufügen von SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Scrollen Sie zum Seitenende, um die Werte für **Tenant URL** (Mandanten-URL) und **Secret Token** (Geheimes Token) abzurufen. Kopieren Sie den Wert von **Secret Token** (Geheimes Token). Dieser Wert wird im Feld „Geheimes Token“ auf der Registerkarte „Bereitstellung“ der Soloinsight-CloudGate SSO-Anwendung im Azure-Portal eingegeben.

    ![Soloinsight-CloudGate SSO: Tokenerstellung](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Hinzufügen von Soloinsight-CloudGate SSO aus dem Katalog

Vor dem Konfigurieren von Soloinsight-CloudGate SSO für die automatische Benutzerbereitstellung mit Azure AD müssen Sie Soloinsight-CloudGate SSO aus dem Azure AD-Anwendungskatalog zur Liste der verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um Soloinsight-CloudGate SSO aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Soloinsight-CloudGate SSO** ein, wählen Sie im Ergebnisbereich **Soloinsight-CloudGate SSO** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Soloinsight-CloudGate SSO in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Konfigurieren der automatischen Benutzerbereitstellung in Soloinsight-CloudGate SSO 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Soloinsight-CloudGate SSO auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Soloinsight-CloudGate SSO aktivieren. Befolgen Sie dazu die Anweisungen im [SSO-Tutorial zu Soloinsight-CloudGate SSO](./soloinsight-cloudgate-sso-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Gehen Sie wie folgt vor, um die automatische Benutzerbereitstellung für Soloinsight-CloudGate SSO in Azure AD zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **Soloinsight-CloudGate SSO**.

    ![Soloinsight-CloudGate SSO-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://sigateway.com/scim/v2/sync/serviceproviderconfig` ein. Geben Sie den Wert des **SCIM-Authentifizierungstokens** ein, den Sie zuvor unter **geheimes Token** abgerufen haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Soloinsight-CloudGate SSO herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Soloinsight-CloudGate SSO-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Soloinsight-CloudGate SSO synchronisieren** aus.

    ![Soloinsight-CloudGate SSO: Benutzerzuordnungen](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Soloinsight-CloudGate SSO synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden zum Abgleichen der Benutzerkonten in Soloinsight-CloudGate SSO für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Soloinsight-CloudGate SSO: Benutzerattribute](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Soloinsight-CloudGate SSO synchronisieren** aus.

    ![Soloinsight-CloudGate SSO: Gruppenzuordnungen](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Soloinsight-CloudGate SSO synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden zum Abgleichen der Gruppen in Soloinsight-CloudGate SSO für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Soloinsight-CloudGate SSO: Gruppenattribute](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Soloinsight-CloudGate SSO zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Soloinsight-CloudGate SSO bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Bereitstellungsaktivitätsberichten aufrufen. In diesen Berichten sind alle vom Azure AD-Bereitstellungsdienst in Soloinsight-CloudGate SSO ausgeführten Aktionen aufgeführt.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)