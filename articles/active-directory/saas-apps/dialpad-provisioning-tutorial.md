---
title: 'Tutorial: Konfigurieren von Dialpad für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Dialpad konfigurieren.
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
ms.openlocfilehash: b88e618da3f8a23c0517aaeb251e54bf559fc468
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014515"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Dialpad für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Dialpad und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Dialpad zu konfigurieren.

> [!NOTE]
>  In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

> Dieser Connector befindet sich derzeit in der Vorschauversion. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* [Einen Dialpad-Mandanten](https://www.dialpad.com/pricing/).
* Ein Benutzerkonto in Dialpad mit Administratorberechtigungen.

## <a name="assign-users-to-dialpad"></a>Zuweisen von Benutzern zu Dialpad
Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Dialpad benötigen. Anschließend können Sie diese Benutzer bzw. Gruppen Dialpad wie folgt zuweisen:
 
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Dialpad

 * Es wird empfohlen, Dialpad einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Dialpad müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen.

## <a name="setup-dialpad-for-provisioning"></a>Einrichten von Dialpad für die Bereitstellung

Bevor Sie Dialpad für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie einige Bereitstellungsinformationen von Dialpad abrufen.

1. Melden Sie sich bei der [Dialpad-Verwaltungskonsole](https://dialpadbeta.com/login) an, und klicken Sie auf **Administratoreinstellungen**. Stellen Sie sicher, dass **My Company** (Mein Unternehmen) in der Dropdownliste ausgewählt ist. Navigieren Sie zu **Authentifizierung > API-Schlüssel**.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad01.png" alt-text="Screenshot der Dialpad-Verwaltungskonsole mit hervorgehobenem Symbol „Einstellungen“ sowie „My Company“, „Authentication“ und API-Schlüsseln. „My Company“ ist ausgewählt." border="false":::

2. Generieren Sie einen neuen Schlüssel, indem Sie auf **Add a key** (Schlüssel hinzufügen) klicken und die Eigenschaften des geheimen Tokens konfigurieren.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad02.png" alt-text="Screenshot der Seite „API keys“ (API-Schlüssel) in der Dialpad-Verwaltungskonsole. „Add a key“ (Schlüssel hinzufügen) ist hervorgehoben." border="false":::

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad03.png" alt-text="Screenshot der Seite „Edit API keys“ (API-Schlüssel bearbeiten) in der Dialpad-Verwaltungskonsole. Die Schaltfläche „Speichern“ ist hervorgehoben." border="false":::

3. Klicken Sie für den soeben erstellten API-Schlüssel auf die Schaltfläche **Click to show value** (Klicken, um Wert anzuzeigen), und kopieren Sie den angezeigten Wert. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Dialpad-Anwendung in das Feld **Geheimes Token** eingegeben. 

    ![Erstellen eines Tokens in Dialpad](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Hinzufügen von Dialpad aus dem Katalog

Sie müssen Dialpad aus dem Azure AD-Anwendungskatalog Ihrer Liste mit den verwalteten SaaS-Anwendungen hinzufügen, um Dialpad für die automatische Benutzerbereitstellung mit Azure AD zu konfigurieren.

**Führen Sie die folgenden Schritte aus, um Dialpad aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Dialpad** ein, und klicken Sie im Ergebnisbereich auf **Dialpad**.
    ![Dialpad in der Ergebnisliste](common/search-new-app.png)

5. Navigieren Sie in einem separaten Browserfenster zu der unten hervorgehobenen **URL**. 

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad05.png" alt-text="Screenshot einer Seite mit Informationen zur Dialpad-App. Unter „URL“ ist eine Adresse aufgeführt und hervorgehoben." border="false":::

6. Klicken Sie in der oberen rechten Ecke auf **Anmelden > Use Dialpad online** (Dialpad online verwenden).

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad06.png" alt-text="Screenshot der Dialpad-Website. „Log in“ (Anmelden) ist hervorgehoben, und die Registerkarte „Log in“ ist geöffnet. „Use Dialpad online“ (Dialpad online verwenden) ist ebenfalls hervorgehoben." border="false":::

7. Da Dialpad eine OpenIDConnect-App ist, können Sie sich mit Ihrem Microsoft-Geschäftskonto bei Dialpad anmelden.

    :::image type="content" source="media/dialpad-provisioning-tutorial/loginpage.png" alt-text="Screenshot der Seite „Start making calls“ (Anrufe beginnen) auf der Dialpad-Website. Die Schaltfläche „Log in with Office 365“ (Mit Office 365 anmelden) ist hervorgehoben." border="false":::

8. Akzeptieren Sie nach der erfolgreichen Authentifizierung auf der Zustimmungsseite die Zustimmungsaufforderung. Die Anwendung wird dann automatisch Ihrem Mandanten hinzugefügt, und Sie werden zu Ihrem Dialpad-Konto umgeleitet.

    :::image type="content" source="media/dialpad-provisioning-tutorial/redirect.png" alt-text="Screenshot einer Microsoft-Authentifizierungsseite, auf der angezeigt wird, dass die Dialpad-App Zugriff auf einige Daten angefordert hat. Die Schaltfläche „Accept“ (Annehmen) ist hervorgehoben." border="false":::

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Konfigurieren der automatischen Benutzerbereitstellung in Dialpad

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Dialpad auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Dialpad in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Klicken Sie in der Anwendungsliste auf **Dialpad**.

    ![Dialpad-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://dialpad.com/scim` ein. Geben Sie im Feld **Geheimes Token** den Wert ein, den Sie zuvor aus Dialpad abgerufen und gespeichert haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Dialpad herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Dialpad-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch einmal.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Users to Dialpad** (Azure Active Directory-Benutzer mit Dialpad synchronisieren) aus.

    ![Benutzerzuordnungen in Dialpad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Dialpad synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Dialpad für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Dialpad-Benutzerattribute](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**, um den Azure AD-Bereitstellungsdienst für Dialpad zu aktivieren.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer bzw. Gruppen fest, die in Dialpad bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Dialpad ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Reporting on automatic user account provisioning (Tutorial: Meldung zur automatischen Benutzerkontobereitstellung)](../app-provisioning/check-status-user-account-provisioning.md).
##  <a name="connector-limitations"></a>Connector-Einschränkungen
* Das Umbenennen von Gruppen wird derzeit nicht von Dialpad unterstützt. Das bedeutet, dass Änderungen des **Anzeigenamens** einer Gruppe in Azure AD nicht in Dialpad übernommen werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
