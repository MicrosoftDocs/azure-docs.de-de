---
title: 'Tutorial: Konfigurieren von myPolicies für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in myPolicies konfigurieren.
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
ms.openlocfilehash: 221f63ab9a7eb3f71a4c730a11565dda64c9edc9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353583"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von myPolicies für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in myPolicies und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in myPolicies zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* Einen [myPolicies-Mandanten](https://mypolicies.com/index.html#section10)
* Ein Benutzerkonto in myPolicies mit Administratorberechtigungen

## <a name="assigning-users-to-mypolicies"></a>Zuweisen von Benutzern zu myPolicies

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf myPolicies benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen myPolicies wie folgt zuweisen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Wichtige Tipps zum Zuweisen von Benutzern zu myPolicies

* Es wird empfohlen, myPolicies einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu myPolicies müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="setup-mypolicies-for-provisioning"></a>Einrichten von myPolicies für die Bereitstellung

Bevor Sie myPolicies für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie in myPolicies die SCIM-Bereitstellung aktivieren.

1. Fordern Sie das geheime Token, das für die Konfiguration der SCIM-Bereitstellung erforderlich ist, unter **support@mypolicies.com** von dem für Sie zuständigen myPolicies-Vertreter an.

2.  Speichern Sie den vom myPolicies-Vertreter bereitgestellten Tokenwert. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer myPolicies-Anwendung in das Feld **Geheimes Token** eingegeben.

## <a name="add-mypolicies-from-the-gallery"></a>Hinzufügen von myPolicies aus dem Katalog

Bevor Sie myPolicies für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie myPolicies aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um myPolicies aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **myPolicies** ein, wählen Sie im Ergebnisbereich **myPolicies** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![myPolicies in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Konfigurieren der automatischen Benutzerbereitstellung in myPolicies 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in myPolicies auf Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für myPolicies aktivieren. Befolgen Sie dazu die Anweisungen unter [Tutorial: Azure Active Directory-Integration mit myPolicies](mypolicies-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für myPolicies in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **myPolicies** aus.

    ![myPolicies-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** in das Feld **Mandanten-URL** die URL `https://<myPoliciesCustomDomain>.mypolicies.com/scim` ein. Dabei steht `<myPoliciesCustomDomain>` für Ihre benutzerdefinierte myPolicies-Domäne. Sie können Ihre myPolicies-Kundendomäne über Ihre URL abrufen.
Beispiel: `<demo0-qa>`.mypolicies.com

6. Geben Sie in das Feld **Geheimes Token** den zuvor abgerufenen Tokenwert ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit myPolicies herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr myPolicies-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

8. Klicken Sie auf **Speichern**.

9. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit myPolicies synchronisieren** aus.

    :::image type="content" source="media/mypolicies-provisioning-tutorial/usermapping.png" alt-text="Screenshot des Abschnitts „Zuordnungen“. Unter „Name“ ist die Option „Azure Active Directory-Benutzer mit customappsso synchronisieren“ hervorgehoben." border="false":::

10. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit myPolicies synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in myPolicies für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|
   |---|---|
   |userName|String|
   |aktiv|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|
   |addresses[type eq "work"].country|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Verweis|


11. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Um den Azure AD-Bereitstellungsdienst für myPolicies zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

13. Legen Sie die Benutzer und/oder Gruppen fest, die in myPolicies bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

14. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in myPolicies ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Für myPolicies ist immer **userName**, **email** und **externalId** erforderlich.
* myPolicies unterstützt das endgültige Löschen für Benutzerattribute nicht.

## <a name="change-log"></a>Änderungsprotokoll

* 15.9.2020: Unterstützung für das Attribut „country“ für Benutzer wurde hinzugefügt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
