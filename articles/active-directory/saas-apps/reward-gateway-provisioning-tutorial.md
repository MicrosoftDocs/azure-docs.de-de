---
title: 'Tutorial: Konfigurieren von Reward Gateway für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Reward Gateway konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2d51903aff6f3fd1cd53d85a980f1b5dc2a893e9
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94354267"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Reward Gateway für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Reward Gateway und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Reward Gateway zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* Einen [Reward Gateway-Mandanten](https://www.rewardgateway.com/)
* Ein Benutzerkonto in Reward Gateway mit Administratorrechten

## <a name="assigning-users-to-reward-gateway"></a>Zuweisen von Benutzern zu Reward Gateway 

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Reward Gateway benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen Reward Gateway zuweisen, indem Sie die Anleitung unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) befolgen.


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Reward Gateway 

* Es wird empfohlen, Reward Gateway einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Reward Gateway müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="setup-reward-gateway--for-provisioning"></a>Einrichten von Reward Gateway für die Bereitstellung
Bevor Sie Reward Gateway für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie in Reward Gateway die SCIM-Bereitstellung aktivieren.

1. Melden Sie sich bei Ihrer [Reward Gateway-Verwaltungskonsole](https://rewardgateway.photoshelter.com/login/) an. Klicken Sie auf **Integrations**.

    ![Screenshot der Reward Gateway-Verwaltungskonsole mit hervorgehobener Option „Integrations“ (Integrationen).](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Wählen Sie **My Integrations** (Meine Integrationen) aus.

    ![Screenshot der beiden Integrationsoptionen mit hervorgehobener Option „My Integrations“ (Meine Integrationen).](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Kopieren Sie die Werte für **SCIM URL (v2)** (SCIM-URL (v2)) und **OAuth Bearer Token** (OAuth-Bearertoken). Diese Werte werden im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Reward Gateway-Anwendung in die Felder „Mandanten-URL“ und „Geheimes Token“ eingegeben.

    ![Screenshot des Bereichs „My Integrations“ (Meine Integrationen) mit hervorgehobenem Textfeld „OAuth Bearer Token“ (OAuth-Bearertoken).](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Hinzufügen von Reward Gateway aus dem Katalog

Um Reward Gateway für die automatische Benutzerbereitstellung mit Azure AD konfigurieren zu können, müssen Sie Ihrer Liste der verwalteten SaaS-Anwendungen Reward Gateway aus dem Azure AD-Anwendungskatalog hinzufügen.

**Führen Sie zum Hinzufügen von Reward Gateway aus dem Azure AD-Anwendungskatalog die folgenden Schritte aus:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Reward Gateway** ein, wählen Sie im Ergebnisbereich **Reward Gateway** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Reward Gateway in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Konfigurieren der automatischen Benutzerbereitstellung für Reward Gateway  

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Reward Gateway auf Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Reward Gateway aktivieren. Befolgen Sie hierzu die Anweisungen unter [Tutorial: Azure Active Directory-Integration mit Reward Gateway](reward-gateway-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Reward Gateway in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Reward Gateway** aus.

    ![Link „Reward Gateway“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die zuvor abgerufenen Werte für **SCIM URL (v2)** (SCIM-URL (v2)) und **OAuth Bearer Token** (OAuth-Bearertoken) in die Felder **Mandanten-URL** und **Geheimes Token** ein. Klicken Sie auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Reward Gateway herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Reward Gateway-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Reward Gateway synchronisieren** aus.

    ![Screenshot des Abschnitts „Zuordnungen“ mit hervorgehobener Option „Azure Active Directory-Benutzer mit Reward Gateway synchronisieren“.](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Reward Gateway synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Reward Gateway für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Screenshot des Abschnitts „Attributzuordnungen“ mit sechs Zuordnungen.](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für Reward Gateway zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer und/oder Gruppen fest, die in Reward Gateway bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Reward Gateway ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

Die Gruppenbereitstellung wird von Reward Gateway derzeit nicht unterstützt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)