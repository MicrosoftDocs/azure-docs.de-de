---
title: 'Tutorial: Konfigurieren von Brivo Onair Identity Connector für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Informieren Sie sich, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Brivo Onair Identity Connector konfigurieren.
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
ms.openlocfilehash: 4f230a70ae87c7c0ec9db347e7cf975c0252bb18
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998088"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Brivo Onair Identity Connector für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Brivo Onair Identity Connector und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Brivo Onair Identity Connector zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [einen Brivo Onair Identity Connector-Mandanten](https://www.brivo.com/lp/quote)
* ein Benutzerkonto in Brivo Onair Identity Connector mit Senior Administrator-Berechtigungen

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Zuweisen von Benutzern zu Brivo Onair Identity Connector

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Brivo Onair Identity Connector benötigen. Anschließend können Sie Brivo Onair Identity Connector diese Benutzer und/oder Gruppen zuweisen, indem Sie die folgenden Anweisungen befolgen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Brivo Onair Identity Connector

* Es wird empfohlen, Brivo Onair Identity Connector einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Brivo Onair Identity Connector müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Einrichten von Brivo Onair Identity Connector für die Bereitstellung

1. Melden Sie sich bei der [Brivo Onair Identity Connector-Administratorkonsole](https://acs.brivo.com/login/) an. Navigieren Sie zu **Konto > Kontoeinstellungen**.

   ![Brivo Onair Identity Connector-Administratorkonsole](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2. Klicken Sie auf die Registerkarte **Azure AD**. Geben Sie auf der **Azure AD**-Detailseite nochmal das Kennwort für das leitende Administratorkonto ein. Klicken Sie auf **Submit** (Senden).

   ![Azure AD in Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3. Klicken Sie auf die Schaltfläche **Copy Token** (Token kopieren), und speichern Sie das **Geheime Token**. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Brivo Onair Identity Connector-Anwendung in das Feld „Geheimes Token“ eingegeben.

   ![Brivo Onair Identity Connector-Token](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Hinzufügen von Brivo Onair Identity Connector aus dem Katalog

Vor dem Konfigurieren von Brivo Onair Identity Connector für die automatische Benutzerbereitstellung mit Azure AD müssen Sie Ihrer Liste der verwalteten SaaS-Anwendungen Brivo Onair Identity Connector aus dem Azure AD-Anwendungskatalog hinzufügen.

**Führen Sie die folgenden Schritte aus, um Brivo Onair Identity Connector aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Brivo Onair Identity Connector** ein, wählen Sie im Ergebnisbereich **Brivo Onair Identity Connector** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Brivo Onair Identity Connector in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Konfigurieren der automatischen Benutzerbereitstellung für Brivo Onair Identity Connector 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Brivo Onair Identity Connector auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Konfigurieren der automatischen Benutzerbereitstellung für Brivo Onair Identity Connector in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Klicken Sie in der Anwendungsliste auf **Brivo Onair Identity Connector**.

    ![Brivo Onair Identity Connector-Verknüpfung in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://scim.brivo.com/ActiveDirectory/v2/` ein. Geben Sie den Wert des **SCIM-Authentifizierungstokens** ein, den Sie zuvor unter **geheimes Token** abgerufen haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Brivo Onair Identity Connector herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Brivo Onair Identity Connector-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch mal.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Klicken Sie im Abschnitt **Zuordnungen** auf die Option **Synchronize Azure Active Directory Users to Brivo Onair Identity Connector** (Azure Active Directory-Benutzer mit Brivo Onair Identity Connector synchronisieren).

    ![Brivo Onair Identity Connector-Benutzerzuordnungen](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Brivo Onair Identity Connector synchronisiert werden. Die als **Matching** (übereinstimmende) Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Brivo Onair Identity Connector für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Brivo Onair Identity Connector-Benutzerattribute](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. Klicken Sie im Abschnitt **Zuordnungen** auf die Option **Synchronize Azure Active Directory Groups to Brivo Onair Identity Connector** (Azure Active Directory-Gruppen mit Brivo Onair Identity Connector synchronisieren).

    ![Brivo Onair Identity Connector-Gruppenzuordnungen](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Brivo Onair Identity Connector synchronisiert werden. Die als **Matching** (übereinstimmende) Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Brivo Onair Identity Connector für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Brivo Onair Identity Connector-Gruppenattribute](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**, um den Azure AD-Bereitstellungsdienst für Brivo Onair Identity Connector zu aktivieren.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Brivo Onair Identity Connector bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zum Bereitstellungsaktivitätsbericht folgen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst für Brivo Onair Identity Connector ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

