---
title: 'Tutorial: Konfigurieren von StarLeaf für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in StarLeaf konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 07c476c0de644ac63c577d466f4691b5cf415334
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357932"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von StarLeaf für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in StarLeaf und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in StarLeaf zu konfigurieren.

> [!NOTE]
>  In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Vorschauversion. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* Einen [StarLeaf-Mandanten](https://starleaf.com/).
* Ein Benutzerkonto in StarLeaf mit Administratorberechtigungen.

## <a name="assign-users-to-starleaf"></a>Zuweisen von Benutzern zu StarLeaf
Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und Gruppen in Azure AD Zugriff auf StarLeaf benötigen. Anschließend können Sie die Benutzer und Gruppen StarLeaf anhand [dieser Anweisungen](../manage-apps/assign-user-or-group-access-portal.md) zuweisen.

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Wichtige Tipps zum Zuweisen von Benutzern zu StarLeaf

* Es wird empfohlen, StarLeaf einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu StarLeaf müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-starleaf-for-provisioning"></a>Einrichten von StarLeaf für die Bereitstellung

Bevor Sie StarLeaf für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie in StarLeaf die SCIM-Bereitstellung konfigurieren:

1. Melden Sie sich bei Ihrer [StarLeaf-Verwaltungskonsole](https://portal.starleaf.com/#page=login) an. Navigieren Sie zu **Integrations** > **Add integration** (Integrationen > Integration hinzufügen).

    ![Screenshot der StarLeaf-Administratorkonsole mit den aufgerufenen Optionen „Integrations“ und „Add integration“](media/starleaf-provisioning-tutorial/image00.png)

2. Wählen Sie für **Type** (Typ) Microsoft Azure Active Directory aus. Geben Sie einen geeigneten Namen in das Feld **Name** ein. Klicken Sie auf **Anwenden**.

    ![Screenshot des Dialogfelds „Add integration“ mit den aufgerufenen Textfeldern für Typ und Name](media/starleaf-provisioning-tutorial/image01.png)

3.  Daraufhin werden die Werte für **SCIM base URL** (SCIM-Basis-URL) und **Access token** (Zugriffstoken) angezeigt. Diese Werte werden im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer StarLeaf-Anwendung in das Feld **Mandanten-URL** bzw. **Geheimes Token** eingegeben. 

    ![Screenshot des Dialogfelds „Edit integration“ mit den aufgerufenen Textfeldern für Typ, Name und SCIM-Basis-URL](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Hinzufügen von StarLeaf aus dem Katalog

Bevor Sie StarLeaf für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie StarLeaf aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie zum Hinzufügen von StarLeaf aus dem Azure AD-Anwendungskatalog die folgenden Schritte aus:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **StarLeaf** ein, und klicken Sie im Ergebnisbereich auf **StarLeaf**.
    ![StarLeaf in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Konfigurieren der automatischen Benutzerbereitstellung in StarLeaf

In diesem Abschnitt werden die Schritte erläutert, mit denen Sie den Azure AD-Bereitstellungsdienst zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in StarLeaf auf der Grundlage von Benutzer- bzw. Gruppenzuweisungen in Azure AD konfigurieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **StarLeaf** aus.

    ![StarLeaf-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt „Administratoranmeldeinformationen“ die zuvor abgerufenen Werte für **SCIM Base URL** (SCIM-Basis-URL) und **Access Token** (Zugriffstoken) in die Felder **Mandanten-URL** bzw. **Geheimes Token** ein. Klicken Sie auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit StarLeaf herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr StarLeaf-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit StarLeaf synchronisieren** aus.

    ![Screenshot des Abschnitts „Zuordnungen“ mit der Option „Azure Active Directory-Benutzer mit StarLeaf synchronisieren“](media/starleaf-provisioning-tutorial/usermapping.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit StarLeaf synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in StarLeaf für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Screenshot des Abschnitts „Attributzuordnungen“ mit neun Zuordnungen](media/starleaf-provisioning-tutorial/userattribute.png)


10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Um den Azure AD-Bereitstellungsdienst für StarLeaf zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer und/oder Gruppen fest, die in StarLeaf bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in StarLeaf ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Reporting on automatic user account provisioning (Tutorial: Meldung zur automatischen Benutzerkontobereitstellung)](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* StarLeaf unterstützt derzeit keine Gruppenbereitstellung. 
* StarLeaf erfordert, dass Werte für **email** und **userName** den gleichen Quellwert haben.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen](../app-provisioning/check-status-user-account-provisioning.md).
