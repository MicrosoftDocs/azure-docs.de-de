---
title: 'Tutorial: Konfigurieren von SAP Cloud Platform Identity Authentication für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in SAP Cloud Platform Identity Authentication konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 419f25ee3df471bc2fc4526254f5677b8bd71856
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342732"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von SAP Cloud Platform Identity Authentication für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in SAP Cloud Platform Identity Authentication und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in SAP Cloud Platform Identity Authentication zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen SAP Cloud Platform Identity Authentication-Mandanten](https://cloudplatform.sap.com/pricing.html)
* Ein Benutzerkonto in SAP Cloud Platform Identity Authentication mit Administratorberechtigungen.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Zuweisen von Benutzern zu SAP Cloud Platform Identity Authentication

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf SAP Cloud Platform Identity Authentication benötigen. Anschließend können Sie SAP Cloud Platform Identity Authentication diese Benutzer und/oder Gruppen zuweisen, indem Sie den folgenden Anweisungen folgen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Wichtige Tipps zum Zuweisen von Benutzern zu SAP Cloud Platform Identity Authentication

* Es wird empfohlen, SAP Cloud Platform Identity Authentication einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu SAP Cloud Platform Identity Authentication müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Einrichten von SAP Cloud Platform Identity Authentication für die Bereitstellung

1. Melden Sie sich bei Ihrer [SAP Cloud Platform Identity Authentication-Verwaltungskonsole](https://sapmsftintegration.accounts.ondemand.com/admin) an. Navigieren Sie zu **Benutzer & Autorisierungen > Administratoren**.

    ![SAP Cloud Platform Identity Authentication-Verwaltungskonsole](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Klicken Sie im linken Bereich auf die Schaltfläche **+Hinzufügen**, um der Liste einen neuen Administrator hinzuzufügen. Wählen Sie **System hinzufügen** aus, und geben Sie den Namen des Systems ein.   

> [!NOTE]
> Der Administratorbenutzer in SAP Cloud Platform Identity Authentication muss vom Typ **System** sein. Die Erstellung eines Standardadministrators kann bei der Bereitstellung zu Fehlern vom Typ *Nicht autorisiert* führen.   

3.  Aktivieren Sie unter „Autorisierungen konfigurieren“ die Umschaltfläche für **Benutzer verwalten** und **Gruppen verwalten**.

    ![SAP Cloud Platform Identity Authentication, SCIM hinzufügen](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Sie erhalten eine E-Mail, um Ihr Konto zu aktivieren und ein Kennwort für den **SAP Cloud Platform Identity Authentication-Dienst** festzulegen.

4.  Kopieren Sie die **Benutzer-ID** und das **Kennwort**. Diese Werte werden im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer SAP Cloud Platform Identity Authentication-Anwendung in das Feld „Administratorbenutzername“ bzw. „Administratorkennwort“ eingegeben.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Hinzufügen von SAP Cloud Platform Identity Authentication aus dem Katalog

Vor dem Konfigurieren von SAP Cloud Platform Identity Authentication für die automatische Benutzerbereitstellung mit Azure AD müssen Sie Ihrer Liste der verwalteten SaaS-Anwendungen SAP Cloud Platform Identity Authentication aus dem Azure AD-Anwendungskatalog hinzufügen.

**Führen Sie die folgenden Schritte aus, um SAP Cloud Platform Identity Authentication aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld den Namen **SAP Cloud Platform Identity Authentication** ein, wählen Sie im Ergebnisbereich die Anwendung **SAP Cloud Platform Identity Authentication** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![SAP Cloud Platform Identity Authentication in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Konfigurieren der automatischen Benutzerbereitstellung für SAP Cloud Platform Identity Authentication 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in SAP Cloud Platform Identity Authentication auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für SAP Cloud Platform Identity Authentication aktivieren. Folgen Sie dazu den Anweisungen im [Tutorial für einmaliges Anmelden mit SAP Cloud Platform Identity Authentication](./sap-hana-cloud-platform-identity-authentication-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Gehen Sie wie folgt vor, um die automatische Benutzerbereitstellung für SAP Cloud Platform Identity Authentication in Azure AD zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **SAP Cloud Platform Identity Authentication** aus.

    ![Der Link „SAP Cloud Platform Identity Authentication“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://<tenantID>.accounts.ondemand.com/service/scim ` ein. Geben Sie die zuvor abgerufenen Werte von **Benutzer-ID** und **Kennwort** in **Administratorbenutzername** bzw. **Administratorkennwort** ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit SAP Cloud Platform Identity Authentication herstellen kann. Wenn die Verbindung nicht hergestellt werden kann, stellen Sie sicher, dass Ihr SAP Cloud Platform Identity Authentication-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch einmal.

    ![Mandanten-URL + Token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit SAP Cloud Platform Identity Authentication synchronisieren** aus.

    ![SAP Cloud Platform Identity Authentication-Benutzerzuordnungen](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit SAP Cloud Platform Identity Authentication synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in SAP Cloud Platform Identity Authentication für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![SAP Cloud Platform Identity Authentication-Benutzerattribute](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für SAP Cloud Platform Identity Authentication zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer und/oder Gruppen fest, die in SAP Cloud Platform Identity Authentication bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zum Bereitstellungsaktivitätsbericht folgen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst für SAP Cloud Platform Identity Authentication ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Der SCIM-Endpunkt von SAP Cloud Platform Identity Authentication erfordert, dass bestimmte Attribute ein spezifisches Format aufweisen. Weitere Informationen zu diesen Attributen und deren spezifischem Format finden Sie [hier](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)