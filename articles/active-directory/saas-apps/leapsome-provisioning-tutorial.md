---
title: 'Tutorial: Konfigurieren von Leapsome für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten für Leapsome konfigurieren können.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: b7c1c995ead9a8d66cd11fb4579cc49e12b487df
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351587"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Leapsome für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Leapsome und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen für Leapsome zu konfigurieren.

> [!NOTE]
>  In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Vorschauversion. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* Einen [Leapsome](https://www.Leapsome.com/en/pricing)-Mandanten
* Ein Benutzerkonto in Leapsome mit Administratorberechtigungen

## <a name="assigning-users-to-leapsome"></a>Zuweisen von Benutzern zu Leapsome

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Leapsome benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen wie folgt Leapsome zuweisen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Wichtige Tipps zum Zuweisen von Benutzern an Leapsome

* Es wird empfohlen, Leapsome einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers an Leapsome müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.


## <a name="setup-leapsome-for-provisioning"></a>Einrichten von Leapsome für die Bereitstellung

1. Melden Sie sich bei Ihrer [Leapsome-Administratorkonsole](https://www.Leapsome.com/app/#/login) an. Navigieren Sie zu **Einstellungen > Administratoreinstellungen**.

    ![Leapsome-Administratorkonsole](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Navigieren Sie zu **Integrationen > SCIM User provisioning** (SCIM-Benutzerbereitstellung).

    ![Leapsome: SCIM hinzufügen](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Kopieren Sie das **SCIM-Authentifizierungstoken**. Dieser Wert wird auf der Registerkarte „Bereitstellung“ Ihrer Leapsome-Anwendung im Azure-Portal in das Feld „Geheimes Token“ eingegeben.

    ![Leapsome: Token erstellen](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Hinzufügen von Leapsome aus dem Katalog

Bevor Sie Leapsome für die automatische Benutzerbereitstellung mit Azure AD konfigurieren können, müssen Sie Leapsome aus dem Azure AD-Anwendungskatalog zur Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um Leapsome aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Leapsome** ein, klicken Sie im Ergebnisbereich erst auf **Leapsome** und dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Leapsome in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Konfigurieren der automatischen Benutzerbereitstellung für Leapsome 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Leapsome auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Leapsome aktivieren. Befolgen Sie hierzu die im [Leapsome-Tutorial zum einmaligen Anmelden](Leapsome-tutorial.md) erläuterten Anweisungen. Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Leapsome in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Leapsome** aus.

    ![Leapsome-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://www.leapsome.com/api/scim` ein. Geben Sie den Wert des **SCIM-Authentifizierungstokens** ein, den Sie zuvor unter **geheimes Token** abgerufen haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Leapsome herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Leapsome-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Klicken Sie im Abschnitt **Zuordnungen** auf **Synchronize Azure Active Directory Users to Leapsome** (Azure Active Directory-Benutzer mit Leapsome synchronisieren).

    ![Benutzerzuordnungen in Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Leapsome synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Leapsome für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Leapsome-Benutzerattribute](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. Klicken Sie im Abschnitt **Zuordnungen** auf **Synchronize Azure Active Directory Groups to Leapsome** (Azure Active Directory-Gruppen mit Leapsome synchronisieren).

    ![Gruppenzuordnungen in Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Leapsome synchronisiert werden. Die als **Übereinstimmend** ausgewählten Attribute werden verwendet, um die Gruppen in Leapsome für Updatevorgänge abzugleichen. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Gruppenattribute in Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Sie können Azure AD-Bereitstellungsdienst für Leapsome aktivieren, indem Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** auf **On** (Ein) festlegen.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Leapsome bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. In diesen Berichten sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Leapsome ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Der Benutzername (**userName**) für Leapsome muss eindeutig sein.
* In Leapsome können nur geschäftliche E-Mail-Adressen gespeichert werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
