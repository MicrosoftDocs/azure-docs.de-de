---
title: 'Tutorial: Konfigurieren von Proxyclick für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Proxyclick konfigurieren.
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
ms.openlocfilehash: f7d2a6f01e891a7fb1c14cde552d66679e474139
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359169"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Proxyclick für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Proxyclick und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Proxyclick zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Proxyclick-Mandanten](https://www.proxyclick.com/pricing)
* Ein Benutzerkonto in Proxyclick mit Administratorberechtigungen

## <a name="add-proxyclick-from-the-gallery"></a>Hinzufügen von Proxyclick aus dem Katalog

Bevor Sie Proxyclick für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Proxyclick aus dem Azure AD-Anwendungskatalog zu Ihrer Liste verwalteter SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um Proxyclick aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Proxyclick** ein, wählen Sie im Ergebnisbereich **Proxyclick** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Proxyclick in der Ergebnisliste](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Zuweisen von Benutzern zu Proxyclick

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Proxyclick benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen Proxyclick wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Proxyclick

* Es wird empfohlen, Proxyclick einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Proxyclick müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Konfigurieren der automatischen Benutzerbereitstellung in Proxyclick 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Proxyclick auf Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Proxyclick aktivieren. Befolgen Sie dazu die Anweisungen im [SSO-Tutorial zu Proxyclick](proxyclick-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Proxyclick in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Proxyclick** aus.

    ![Proxyclick-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Führen Sie die Schritte der in Schritt 6 beschriebenen exemplarischen Vorgehensweise aus, um die **Mandanten-URL** und das **geheime Token** Ihres Proxyclick-Kontos abzurufen.

6. Melden Sie sich bei Ihrer [Proxyclick-Verwaltungskonsole](https://app.proxyclick.com/login//?destination=%2Fdefault) an. Navigieren Sie zu **Einstellungen** > **Integrationen** > **Marketplace durchsuchen**.

    ![„Einstellungen“ in Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![„Integrationen“ in Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![„Marketplace durchsuchen“ in Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Wählen Sie **Azure AD**. Klicken Sie auf **Jetzt installieren**.

    ![Azure AD in Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![„Jetzt installieren“ in Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Wählen Sie **Benutzerbereitstellung** aus, und klicken Sie auf **Start integration** (Integration starten). 

    ![„Benutzerbereitstellung“ in Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    Die entsprechende Benutzeroberfläche für die Konfiguration der Einstellungen sollte nun unter **Einstellungen** > **Integrationen** angezeigt werden. Klicken Sie unter **Azure AD (Benutzerbereitstellung)** auf **Einstellungen**.

    ![Erstellen in Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Hier finden Sie die **Mandanten-URL** und das **geheime Token**.

    ![Token für das Erstellen in Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Klicken Sie nach dem Auffüllen der in Schritt 5 gezeigten Felder auf **Verbindung testen** , um sich zu vergewissern, dass Azure AD eine Verbindung mit Proxyclick herstellen kann. Stellen Sie sicher, dass Ihr Proxyclick-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch einmal, falls der Verbindungsaufbau fehlschlägt.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Users to Proxyclick** (Azure Active Directory-Benutzer mit Proxyclick synchronisieren) aus.

    ![Benutzerzuordnungen in Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Proxyclick synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Proxyclick für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Benutzerattribute in Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein** , um den Azure AD-Bereitstellungsdienst für Proxyclick zu aktivieren.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

15. Legen Sie die Benutzer und/oder Gruppen fest, die in Proxyclick bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

16. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Proxyclick ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Proxyclick erfordert, dass **emails** und **userName** den gleichen Quellwert haben. Updates für beide Attribute ändern den anderen Wert.
* Die Bereitstellung für Gruppen wird von Proxyclick nicht unterstützt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

