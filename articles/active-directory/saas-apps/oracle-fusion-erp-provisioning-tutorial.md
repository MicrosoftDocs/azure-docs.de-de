---
title: 'Tutorial: Konfigurieren von Oracle Fusion ERP für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Oracle Fusion ERP konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: da6e1a8ba31f8f4991bde4803191598a015a68b3
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358438"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Oracle Fusion ERP für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Oracle Fusion ERP und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Oracle Fusion ERP zu konfigurieren.

> [!NOTE]
>  In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Vorschauversion. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Vorschaufunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* Einen [Oracle Fusion ERP-Mandanten](https://www.oracle.com/applications/erp/)
* Ein Benutzerkonto in Oracle Fusion ERP mit Administratorberechtigungen

## <a name="assign-users-to-oracle-fusion-erp"></a>Zuweisen von Benutzern zu Oracle Fusion ERP 
Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Oracle Fusion ERP benötigen. Anschließend können Sie Oracle Fusion ERP diese Benutzer und/oder Gruppen zuweisen, indem Sie den folgenden Anweisungen folgen:
 
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Oracle Fusion ERP 

 * Es wird empfohlen, Oracle Fusion ERP einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Oracle Fusion ERP müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Einrichten von Oracle Fusion ERP für die Bereitstellung

Bevor Sie Oracle Fusion ERP für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie in Oracle Fusion ERP die SCIM-Bereitstellung aktivieren.

1. Melden Sie sich bei Ihrer [Oracle Fusion ERP-Verwaltungskonsole](https://cloud.oracle.com/sign-in) an.

2. Klicken Sie in der oberen linken Ecke auf den Navigator. Wählen Sie unter **Tools** die Option **Sicherheitskonsole** aus.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/login.png" alt-text="Screenshot der Seite „Navigator“ in der Oracle Fusion ERP-Verwaltungskonsole. „Tools“ und „Sicherheitskonsole“ sind hervorgehoben." border="false":::

3. Navigieren Sie zu **Benutzer**.
    
    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user.png" alt-text="Screenshot eines Panels in der Oracle Fusion ERP-Verwaltungskonsole. Das Element „Benutzer“ ist hervorgehoben." border="false":::

4. Speichern Sie den Benutzernamen und das Kennwort für das Administratorbenutzerkonto, das Sie für die Anmeldung bei der Oracle Fusion ERP-Verwaltungskonsole verwenden. Diese Werte müssen im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Oracle Fusion ERP-Anwendung in die Felder **Administratorbenutzername** und **Administratorkennwort** eingegeben werden.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Hinzufügen von Oracle Fusion ERP aus dem Katalog

Um Oracle Fusion ERP für die automatische Benutzerbereitstellung mit Azure AD konfigurieren zu können, müssen Sie Ihrer Liste der verwalteten SaaS-Anwendungen Oracle Fusion ERP aus dem Azure AD-Anwendungskatalog hinzufügen.

**Führen Sie die folgenden Schritte aus, um Oracle Fusion ERP aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld den Namen **Oracle Fusion ERP** ein, und wählen Sie im Ergebnisbereich die Anwendung **Oracle Fusion ERP** aus.

    ![Oracle Fusion ERP in der Ergebnisliste](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Konfigurieren der automatischen Benutzerbereitstellung für Oracle Fusion ERP 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Oracle Fusion ERP auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Oracle Fusion ERP aktivieren. Folgen Sie dazu den Anweisungen unter [Tutorial: Azure Active Directory-Integration von Oracle Fusion ERP](oracle-fusion-erp-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

> [!NOTE]
> Weitere Informationen zum SCIM-Endpunkt von Oracle Fusion ERP finden Sie unter [REST-API für allgemeine Features in Oracle Applications Cloud](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Oracle Fusion ERP in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **Oracle Fusion ERP** aus.

    ![Der Oracle Fusion ERP-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` ein. Geben Sie den Administratorbenutzernamen und das Kennwort, den/das Sie zuvor abgerufen haben, in die Felder **Administratorbenutzername** und **Administratorkennwort** ein. Klicken Sie auf **Verbindung testen** , um die Verbindung zwischen Azure AD und Oracle Fusion ERP zu testen. 

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/admin.png" alt-text="Screenshot des Abschnitts „Administratoranmeldeinformationen“. Eine Schaltfläche „Verbindung testen“ und Felder für eine Mandanten-URL, einen Administratorbenutzernamen und ein Administratorkennwort sind sichtbar." border="false":::

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Oracle Fusion ERP synchronisieren** aus.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png" alt-text="Screenshot des Abschnitts „Zuordnungen“. Unter „Name“ ist „Azure Active Directory-Benutzer mit Oracle Fusion ERP synchronisieren“ sichtbar." border="false":::

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Oracle Fusion ERP synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Oracle Fusion ERP für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png" alt-text="Screenshot der Seite „Attributzuordnungen“. In einer Tabelle werden Attribute für „Azure Active Directory“ und „Oracle Fusion ERP“ sowie die entsprechende Rangfolge aufgelistet." border="false":::

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Oracle Fusion ERP synchronisieren** aus.

    ![Oracle Fusion ERP: Gruppenzuordnungen](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit Oracle Fusion ERP synchronisiert werden. Die als **Abgleichseigenschaften** ausgewählten Attribute werden zum Abgleich der Gruppen in Oracle Fusion ERP für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Oracle Fusion ERP: Gruppenattribute](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Oracle Fusion ERP zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Oracle Fusion ERP bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

    Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Bereitstellungsaktivitätsberichten aufrufen. In diesen Berichten sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Oracle Fusion ERP ausgeführt werden.

    Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Oracle Fusion ERP unterstützt nur die Standardauthentifizierung für den zugehörigen SCIM-Endpunkt.
* Oracle Fusion ERP unterstützt keine Gruppenbereitstellung.
* Rollen in Oracle Fusion ERP werden Gruppen in Azure AD zugeordnet. Wenn Sie Benutzern in Oracle Fusion ERP Rollen aus Azure AD zuweisen möchten, müssen Sie Benutzer den gewünschten Azure AD-Gruppen zuweisen, die nach Rollen in Oracle Fusion ERP benannt sind.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
