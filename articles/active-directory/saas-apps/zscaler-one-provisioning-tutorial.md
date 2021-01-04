---
title: 'Tutorial: Konfigurieren von Zscaler One für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Zscaler One konfigurieren.
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
ms.openlocfilehash: b8b6383c7808fd6c298d7776fc10572631bc6ddc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006216"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Zscaler One für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die Sie in Zscaler One und Azure Active Directory (Azure AD) ausführen müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und Gruppen in Zscaler One zu konfigurieren.

> [!NOTE]
> Das Tutorial enthält die Beschreibung eines Connectors, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Informationen zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen (Software as a Service) mit Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial beschriebene Szenario setzt voraus, dass Sie über folgende Elemente verfügen:

* Einen Azure AD-Mandanten.
* Einen Zscaler One-Mandanten.
* Ein Benutzerkonto in Zscaler One mit Administratorberechtigungen.

> [!NOTE]
> Die Azure AD-Bereitstellungsintegration basiert auf der Zscaler One-SCIM-API. Diese API steht für Zscaler One-Entwickler für Konten mit dem Enterprise-Paket zur Verfügung.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Hinzufügen von Zscaler One aus dem Azure Marketplace

Bevor Sie Zscaler One für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, fügen Sie Zscaler One aus dem Azure Marketplace der Liste mit den verwalteten SaaS-Anwendungen hinzu.

Führen Sie zum Hinzufügen von Zscaler One aus dem Azure Marketplace die folgenden Schritte aus.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory** aus.

    ![Azure Active Directory-Symbol](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie oben im Dialogfeld **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Zscaler One** ein, und wählen Sie **Zscaler One** im Ergebnisbereich aus. Wählen Sie dann **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![Zscaler One in der Ergebnisliste](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Zuweisen von Benutzern zu Zscaler One

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Zusammenhang mit der automatischen Benutzerbereitstellung werden nur die Benutzer oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Entscheiden Sie vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung, welche Benutzer oder Gruppen in Azure AD Zugriff auf Zscaler One benötigen. Zum Zuweisen dieser Benutzer oder Gruppen zu Zscaler One befolgen Sie die Anleitung unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Zscaler One

* Wir empfehlen, zuerst nur einen einzelnen Azure AD-Benutzer zu Zscaler One zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können Sie dann weitere Benutzer oder Gruppen zuweisen.

* Beim Zuweisen eines Benutzers zu Zscaler One müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle (sofern verfügbar) auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Konfigurieren der automatischen Benutzerbereitstellung in Zscaler One

Dieser Abschnitt führt Sie durch die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts. Verwenden Sie diese zum Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in Zscaler One anhand von Benutzer- oder Gruppenzuweisungen in Azure AD.

> [!TIP]
> Sie können auch SAML-basiertes einmaliges Anmelden für Zscaler One aktivieren. Befolgen Sie hierfür die Anleitung im [Tutorial für einmaliges Anmelden mit Zscaler One](zscaler-One-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Konfigurieren der automatischen Benutzerbereitstellung für Zscaler One in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen** > **Zscaler One** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Zscaler One** aus.

    ![Zscaler One-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot: Bereitstellung von Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Bereitstellungmodus von Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** und **Geheimes Token** die entsprechenden Einstellungen für Ihr Zscaler One-Konto ein, wie in Schritt 6 beschrieben.

6. Navigieren Sie zum Abrufen der Mandanten-URL und des geheimen Tokens auf der Benutzeroberfläche im Zscaler One-Portal zu **Verwaltung** > **Authentifizierungseinstellungen**. Wählen Sie unter **Authentifizierungstyp** die Option **SAML** aus.

    ![Zscaler One-Authentifizierungseinstellungen](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Wählen Sie **SAML konfigurieren** aus, um die Optionen unter **SAML konfigurieren** zu öffnen.

    ![SAML konfigurieren in Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Wählen Sie **SCIM-basierte Bereitstellung aktivieren** aus, um die Einstellungen für **Basis-URL** und **Bearertoken** abzurufen. Speichern Sie anschließend die Einstellungen. Kopieren Sie die Einstellung für **Basis-URL** in das Feld **Mandanten-URL** im Azure-Portal. Kopieren Sie die Einstellung für **Bearertoken** in das Feld **Geheimes Token** im Azure-Portal.

7. Nachdem Sie die in Schritt 5 gezeigten Felder ausgefüllt haben, wählen Sie **Testverbindung** aus, um sicherzustellen, dass Azure AD eine Verbindung mit Zscaler One herstellen kann. Falls der Verbindungsaufbau fehlschlägt, sollten Sie sicherstellen, dass Ihr Zscaler One-Konto über Administratorberechtigungen verfügt, und den Vorgang dann wiederholen.

    ![Zscaler One-Testverbindung](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Zscaler One-Benachrichtigungs-E-Mail](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Wählen Sie **Speichern** aus.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Users to Zscaler One** (Azure Active Directory-Benutzer mit Zscaler One synchronisieren) aus.

    ![Zscaler One-Benutzersynchronisierung](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Zscaler One synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Zscaler One für Updatevorgänge verwendet werden. Um alle Änderungen zu speichern, wählen Sie **Speichern** aus.

    ![Übereinstimmende Benutzerattribute in Zscaler One](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Groups to Zscaler One** (Azure Active Directory-Gruppen mit Zscaler One synchronisieren) aus.

    ![Zscaler One-Gruppensynchronisierung](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Zscaler One synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Zscaler One für Updatevorgänge verwendet. Um alle Änderungen zu speichern, wählen Sie **Speichern** aus.

    ![Übereinstimmende Gruppenattribute in Zscaler One](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Wenn Sie Bereichsfilter konfigurieren möchten, befolgen Sie die Anleitung unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Um den Azure AD-Bereitstellungsdienst für Zscaler One zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Zscaler One-Bereitstellungsstatus](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definieren Sie die Benutzer oder Gruppen, die in Zscaler One bereitgestellt werden sollen. Wählen Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Zscaler One-Bereich](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Speichern in Zscaler One](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Dadurch wird die Erstsynchronisierung aller Benutzer oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die folgenden Synchronisierungen. Diese erfolgen etwa alle 40 Minuten, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und über Links zum Bereitstellungsaktivitätsbericht navigieren. Der Bericht beschreibt alle vom Azure AD-Bereitstellungsdienst in Zscaler One ausgeführten Aktionen.

Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png