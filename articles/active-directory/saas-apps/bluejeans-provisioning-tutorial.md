---
title: 'Tutorial: Konfigurieren von BlueJeans für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in BlueJeans konfigurieren.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 204cdc689d5a117df428bb314a81a35081f7b13c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357639"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von BlueJeans für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in BlueJeans und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in BlueJeans zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial beschriebene Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Einen Azure AD-Mandanten
* Ein BlueJeans-Mandant mit aktiviertem Plan [Mein Unternehmen](https://www.BlueJeans.com/pricing) oder besser
* Ein Benutzerkonto in BlueJeans mit Administratorrechten

> [!NOTE]
> Die Integration der Azure AD-Bereitstellung basiert auf der [BlueJeans-API](https://BlueJeans.github.io/developer), die für BlueJeans-Teams mit dem Standard-Tarif oder einem höheren Tarif zur Verfügung steht.

## <a name="adding-bluejeans-from-the-gallery"></a>Hinzufügen von BlueJeans aus dem Katalog

Bevor Sie BlueJeans für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie BlueJeans aus dem Azure AD-Anwendungskatalog der Liste der verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie zum Hinzufügen von BlueJeans aus dem Azure AD-Anwendungskatalog die folgenden Schritte aus:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **BlueJeans** ein, wählen Sie im Ergebnisbereich **BlueJeans** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![BlueJeans in der Ergebnisliste](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Zuweisen von Benutzern zu BlueJeans

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf BlueJeans benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen BlueJeans anhand der folgenden Anweisungen zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Wichtige Tipps zum Zuweisen von Benutzern zu BlueJeans

* Es wird empfohlen, BlueJeans einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu BlueJeans müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Konfigurieren der automatischen Benutzerbereitstellung für BlueJeans

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in BlueJeans auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für BlueJeans aktivieren. Befolgen Sie hierzu die Anweisungen im [Tutorial: Azure Active Directory-Integration in BlueJeans](bluejeans-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für BlueJeans in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Unternehmensanwendungen** , **Alle Anwendungen** und dann **BlueJeans** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **BlueJeans** aus.

    ![BlueJeans-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Randleiste der BlueJeans-Unternehmensanwendung mit hervorgehobener und aufgerufener Option „Bereitstellung“](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Seite „Bereitstellung“ mit den aufgerufenen Abschnitten „Bereitstellungsmodus“ und „Administratoranmeldeinformationen“](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** einen Wert für **Administratorbenutzername** und **Administratorkennwort** Ihres BlueJeans-Kontos ein. Beispiele für diese Werte:

   * Geben Sie im Feld **Administratorbenutzername** den Benutzernamen des Administratorkontos im BlueJeans-Mandanten ein. Beispiel: admin@contoso.com.

   * Geben Sie im Feld **Administratorkennwort** das Kennwort für den Administratorbenutzernamen ein.

6. Klicken Sie nach dem Auffüllen der in Schritt 5 gezeigten Felder auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit BlueJeans herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr BlueJeans-Konto über Administratorrechte verfügt, und wiederholen Sie den Vorgang.

    ![Screenshot des Abschnitts „Administratoranmeldeinformationen“ mit der aufgerufenen Option „Verbindung testen“](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Screenshot des Textfelds „Benachrichtigungs-E-Mail“](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Klicken Sie auf **Speichern**.

9. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit BlueJeans synchronisieren** aus.

    ![Screenshot des Abschnitts „Zuordnungen“ mit hervorgehobener Option „Azure Active Directory-Benutzer mit BlueJeans synchronisieren“](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit BlueJeans synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in BlueJeans für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Screenshot: Abschnitt „Attributzuordnungen“ mit sieben Zuordnungen](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Um den Azure AD-Bereitstellungsdienst für BlueJeans zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Screenshot des Abschnitts „Einstellungen“, in dem die Option „Bereitstellungsstatus“ auf „Ein“ festgelegt ist](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Legen Sie die Benutzer und/oder Gruppen fest, die in BlueJeans bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Screenshot der Einstellung „Bereich“ mit hervorgehobener Option „Nur zugewiesene Benutzer und Gruppen synchronisieren“](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Screenshot der Randleiste der BlueJeans-Unternehmensanwendung mit aufgerufener Option „Speichern“](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in BlueJeans ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* In BlueJeans sind Benutzernamen mit mehr als 30 Zeichen nicht zulässig.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
