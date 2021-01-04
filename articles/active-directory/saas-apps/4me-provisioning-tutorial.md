---
title: 'Tutorial: Konfigurieren von 4me für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in 4me konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: c0c428997cfba8871a29d9bfe0df0a6920a1d22f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998667"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von 4me für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in 4me und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in 4me zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen 4me-Mandanten](https://www.4me.com/trial/)
* Ein Benutzerkonto in 4me mit Administratorrechten

## <a name="add-4me-from-the-gallery"></a>Hinzufügen von 4me aus dem Katalog

Bevor Sie 4me für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie 4me aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um 4me aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **4me** ein, wählen Sie im Ergebnisbereich **4me** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![4me in der Ergebnisliste](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Zuweisen von Benutzern zu 4me

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf 4me benötigen. Anschließend können Sie diese Benutzer bzw. Gruppen 4me wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Wichtige Tipps zum Zuweisen von Benutzern zu 4me

* Es wird empfohlen, 4me einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu 4me müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Konfigurieren der automatischen Benutzerbereitstellung in 4me 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in 4me auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für 4me aktivieren. Befolgen Sie hierzu die Anweisungen im [SSO-Tutorial zu 4me](4me-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für 4me in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **4me** aus.

    ![4me-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Führen Sie die Schritte der in Schritt 6 beschriebenen exemplarischen Vorgehensweise aus, um die **Mandanten-URL** und das **geheime Token** Ihres 4me-Kontos abzurufen.

6. Melden Sie sich bei Ihrer 4me Admin-Konsole an. Navigieren zu **Einstellungen**.

    ![„Einstellungen“ in 4me](media/4me-provisioning-tutorial/4me01.png)

    Geben Sie in der Suchleiste **Apps** ein.

    ![4me-Apps](media/4me-provisioning-tutorial/4me02.png)

    Öffnen Sie die Dropdownliste **SCIM**, um das geheime Token und den SCIM-Endpunkt abzurufen.

    ![„SCIM“ in 4me](media/4me-provisioning-tutorial/4me03.png)

7. Klicken Sie nach dem Auffüllen der in Schritt 5 gezeigten Felder auf **Verbindung testen**, um sich zu vergewissern, dass Azure AD eine Verbindung mit 4me herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr 4me-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit 4me synchronisieren** aus.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-mapping.png" alt-text="Screenshot der Seite „Zuordnungen“. Unter „Name“ ist die Option „Azure Active Directory-Benutzer mit 4me synchronisieren“ hervorgehoben." border="false":::
    
11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit 4me synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in 4me für Updatevorgänge verwendet. Stellen Sie sicher, dass [4me das Filtern für das ausgewählte Attribut unterstützt](https://developer.4me.com/v1/scim/users/). Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-attributes.png" alt-text="Screenshot der Seite „Attributzuordnungen“. In einer Tabelle werden Azure Active Directory-Attribute, die entsprechenden 4me-Attribute und der Abgleichsstatus aufgeführt." border="false":::
    
12. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit 4me synchronisieren**.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-group-mapping.png" alt-text="Screenshot der Seite „Zuordnungen“. Unter „Name“ ist die Option „Azure Active Directory-Gruppen mit 4me synchronisieren“ hervorgehoben." border="false":::
    
13. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit 4me synchronisiert werden. Die als **Übereinstimmend** ausgewählten Attribute werden verwendet, um die Gruppen in 4me für Updatevorgänge abzugleichen. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Gruppenzuordnungen in 4me](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Um den Azure AD-Bereitstellungsdienst für 4me zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

16. Legen Sie die Benutzer bzw. Gruppen fest, die in 4me bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

17. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in 4me ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* 4me verfügt über verschiedene SCIM-Endpunkt-URLs für Test- und Produktionsumgebungen. Die erste endet mit **.qa** und die zweite mit **.com**.
* Von 4me generierte geheime Token weisen ein Ablaufdatum von einem Monat ab Generierung auf.
* 4me unterstützt keine **DELETE**-Vorgänge

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
