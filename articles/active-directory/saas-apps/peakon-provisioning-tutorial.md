---
title: 'Tutorial: Konfigurieren von Peakon für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Peakon konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: c04bbd5459690262b484582e807569b965a0439b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349887"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Peakon für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Peakon und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Peakon zu konfigurieren.

> [!NOTE]
>  In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Vorschauversion. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* [Einen Peakon-Mandanten](https://peakon.com/us/pricing/).
* Ein Benutzerkonto in Peakon mit Administratorberechtigungen.

## <a name="assigning-users-to-peakon"></a>Zuweisen von Benutzern zu Peakon

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Peakon benötigen. Anschließend können Sie diese Benutzer bzw. Gruppen Peakon wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Peakon 

* Es wird empfohlen, Peakon einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Peakon müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-peakon-for-provisioning"></a>Einrichten von Peakon für die Bereitstellung

1.  Melden Sie sich bei Ihrer [Peakon-Verwaltungskonsole](https://app.Peakon.com/login) an. Klicken Sie auf **Konfiguration**. 

    ![Peakon-Verwaltungskonsole](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Wählen Sie **Integrationen** aus.
    
    ![Screenshot der Konfigurationsoptionen mit aufgerufener Option „Integrationen“](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Aktivieren Sie **Mitarbeiterbereitstellung**.

    ![Screenshot des Abschnitts „Mitarbeiterbereitstellung“ mit aufgerufener Option „Aktivieren“](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Kopieren Sie die Werte für **SCIM 2.0-URL** und **OAuth-Bearertoken**. Diese Werte werden in das Feld **Mandanten-URL** und **Geheimes Token** auf der Registerkarte „Bereitstellung“ Ihrer Peakon-Anwendung im Azure-Portal eingegeben.

    ![Peakon: Token erstellen](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Hinzufügen von Peakon aus dem Katalog

Um Peakon für die automatische Benutzerbereitstellung mit Azure AD zu konfigurieren, müssen Sie Peakon aus dem Azure AD-Anwendungskatalog Ihrer Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Peakon** ein, wählen Sie im Ergebnisbereich **Peakon** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Peakon in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Konfigurieren der automatischen Benutzerbereitstellung in Peakon 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Peakon auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Peakon aktivieren. Befolgen Sie hierzu die Anweisungen im [SSO-Tutorial zu Peakon](peakon-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Peakon in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **Peakon** aus.

    ![Peakon-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Werte für **SCIM 2.0-URL** und **OAuth-Bearertoken** ein, die zuvor unter **Mandanten-URL** bzw. **Geheimes Token** abgerufen wurden. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Peakon herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Peakon-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch mal.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

8. Klicken Sie auf **Speichern**.

9. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Peakon synchronisieren** aus.

    ![Benutzerzuordnungen in Peakon](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Peakon synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Peakon für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Peakon-Benutzerattribute](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, verwenden Sie die Anweisungen, die im [Tutorial zu Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) bereitgestellt werden.
    
    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Peakon ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Alle benutzerdefinierten Benutzerattribute in Peakon müssen aus der benutzerdefinierten SCIM-Benutzererweiterung `urn:ietf:params:scim:schemas:extension:peakon:2.0:User` von Peakon erweitert werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)