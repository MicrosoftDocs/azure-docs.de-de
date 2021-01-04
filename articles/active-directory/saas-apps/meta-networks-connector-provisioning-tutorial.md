---
title: 'Tutorial: Konfigurieren von Meta Networks Connector für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Meta Networks Connector konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: b6a8f192cd26639431cc9fcb6b43e1bc5e8e2843
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353628"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Meta Networks Connector für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Meta Networks Connector und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Meta Networks Connector zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Meta Networks Connector-Mandanten](https://www.metanetworks.com/)
* Ein Benutzerkonto in Meta Networks Connector mit Administratorberechtigungen.

## <a name="assigning-users-to-meta-networks-connector"></a>Zuweisen von Benutzern zu Meta Networks Connector

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Meta Networks Connector benötigen. Anschließend können Sie Meta Networks Connector diese Benutzer und/oder Gruppen zuweisen, indem Sie den folgenden Anweisungen folgen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Meta Networks Connector

* Es wird empfohlen, Meta Networks Connector einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Meta Networks Connector müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Einrichten von Meta Networks Connector für die Bereitstellung

1. Melden Sie sich mit Ihrem Organisationsnamen bei der [Meta Networks Connector-Verwaltungskonsole](https://login.metanetworks.com/login/) an. Navigieren Sie zu **Administration > API Keys** (Verwaltung > API-Schlüssel).

    ![Meta Networks Connector-Verwaltungskonsole](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Klicken Sie oben rechts auf dem Bildschirm auf das Pluszeichen, um einen neuen **API-Schlüssel** zu erstellen.

    ![Meta Networks Connector, Plussymbol](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Legen Sie unter **API Key Name** und **API Key Description** den Namen und die Beschreibung des API-Schlüssels fest.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/keyname.png" alt-text="Screenshot der Verwaltungskonsole von Meta Networks Connector mit hervorgehobenen Werten für API-Schlüsselname und API-Schlüsselbeschreibung für den Azure AD- und API-Schlüssel" border="false":::

4.  Aktivieren Sie die Schreibberechtigungen (Option **Write**) für **Gruppen** und **Benutzer**.

    ![Meta Networks Connector-Berechtigungen](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Klicken Sie auf **Hinzufügen**. Kopieren Sie den Wert von **SECRET** (Geheimnis), und speichern Sie ihn, weil er nur zu diesem Zeitpunkt angezeigt wird. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Meta Networks Connector-Anwendung in das Feld „Geheimes Token“ eingegeben.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/token.png" alt-text="Screenshot eines Fensters, in dem der Benutzer informiert wird, dass der API-Schlüssel hinzugefügt wurde. Das Feld „Secret“ (Geheimnis) enthält einen nicht lesbaren Wert und ist hervorgehoben." border="false":::

6.  Fügen Sie einen IdP hinzu, indem Sie zu **Administration > Settings > IdP > Create New** (Verwaltung > Einstellungen > IdP > Neu erstellen) navigieren.

    ![Meta Networks Connector, IdP hinzufügen](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Auf der Seite **IdP Configuration** (IdP-Konfiguration) können Sie einen **Namen** für Ihre IdP-Konfiguration eingeben und ein **Symbol** auswählen.

    ![Meta Networks Connector, IdP-Name](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Meta Networks Connector, IdP-Symbol](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  Wählen Sie unter **Configure SCIM** (SCIM konfigurieren) den in den vorherigen Schritten erstellten API-Schlüssel aus. Klicken Sie auf **Speichern**.

    ![Meta Networks Connector, SCIM konfigurieren](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Navigieren Sie zu **Administration > Settings > IdP** (Verwaltung > Einstellungen > Registerkarte „IdP“). Klicken Sie auf den Namen der in den vorherigen Schritten erstellten IdP-Konfiguration, um die **IdP-ID** anzuzeigen. Diese **ID** wird am Ende der **Mandanten-URL** hinzugefügt, wenn Sie im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Meta Networks Connector-Anwendung den entsprechenden Wert in das Feld **Mandanten-URL** eingeben.

    ![Meta Networks Connector, IdP-ID](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Hinzufügen von Meta Networks Connector aus dem Katalog

Vor dem Konfigurieren von Meta Networks Connector für die automatische Benutzerbereitstellung mit Azure AD müssen Sie Ihrer Liste der verwalteten SaaS-Anwendungen Meta Networks Connector aus dem Azure AD-Anwendungskatalog hinzufügen.

**Führen Sie die folgenden Schritte aus, um Meta Networks Connector aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld den Namen **Meta Networks Connector** ein, wählen Sie im Ergebnisbereich die Anwendung **Meta Networks Connector** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Meta Networks Connector in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Konfigurieren der automatischen Benutzerbereitstellung für Meta Networks Connector 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Meta Networks Connector auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Meta Networks Connector aktivieren. Folgen Sie dazu den Anweisungen unter [Tutorial: Azure Active Directory-Integration von Meta Networks Connector](./metanetworksconnector-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Meta Networks Connector in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Meta Networks Connector** aus.

    ![Der Meta Networks Connector-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://api.metanetworks.com/v1/scim/<IdP ID>` ein. Geben Sie den Wert des **SCIM-Authentifizierungstokens** ein, den Sie zuvor unter **geheimes Token** abgerufen haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Meta Networks Connector herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Meta Networks Connector-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch mal.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Meta Networks Connector synchronisieren** aus.

    ![Meta Networks Connector-Benutzerzuordnungen](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Meta Networks Connector synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Meta Networks Connector für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Meta Networks Connector-Benutzerattribute](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Meta Networks Connector synchronisieren** aus.

    ![Meta Networks Connector-Gruppenzuordnungen](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Meta Networks Connector synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Meta Networks Connector für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Meta Networks Connector-Gruppenattribute](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Meta Networks Connector zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Meta Networks Connector bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zum Bereitstellungsaktivitätsbericht folgen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst für Meta Networks Connector ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)