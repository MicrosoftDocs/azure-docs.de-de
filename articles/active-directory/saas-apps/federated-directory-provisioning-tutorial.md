---
title: 'Tutorial: Konfigurieren von Federated Directory für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Federated Directory konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 8ca7654d930247f70d85cbc20fbbeb961223f05f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998361"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Federated Directory für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Federated Directory und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Federated Directory zu konfigurieren.

> [!NOTE]
>  In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* [Ein Federated Directory](https://www.federated.directory/pricing).
* Ein Benutzerkonto in Federated Directory mit Administratorberechtigungen.

## <a name="assign-users-to-federated-directory"></a>Zuweisen von Benutzern zu Federated Directory
Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Federated Directory benötigen. Anschließend können Sie diese Benutzer bzw. Gruppen zu Federated Directory wie folgt zuweisen:

 * [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Federated Directory
 * Es wird empfohlen, Federated Directory einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Federated Directory müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Einrichten von Federated Directory für die Bereitstellung

Bevor Sie Federated Directory für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie in Federated Directory die SCIM-Bereitstellung aktivieren.

1. Melden Sie sich bei Ihrer [-Verwaltungskonsole](https://federated.directory/of) an.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="Screenshot der Federated Directory-Verwaltungskonsole mit einem Feld für die Eingabe eines Firmennamens. Anmeldeschaltflächen sind ebenfalls sichtbar." border="false":::

2. Navigieren Sie zu **Verzeichnisse > Benutzerverzeichnisse**, und wählen Sie Ihren Mandanten aus. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text="Screenshot der Federated Directory-Verwaltungskonsole mit hervorgehobenen Verzeichnissen und der Option zum Testen von Federated Directory mit Azure AD" border="false":::

3.  Um ein dauerhaftes Bearertoken zu generieren, navigieren Sie zu **Verzeichnisschlüssel > Neuen Schlüssel erstellen**. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="Screenshot der Seite mit den Verzeichnisschlüsseln in der Federated Directory-Verwaltungskonsole. Die Schaltfläche „Create new key“ (Neuen Schlüssel erstellen) ist hervorgehoben." border="false":::

4. Erstellen Sie einen Verzeichnisschlüssel. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="Screenshot der Seite „Create directory key“ (Verzeichnisschlüssel erstellen) der Federated Directory-Verwaltungskonsole mit den Feldern „Name“ und „Description“ (Beschreibung) und der Schaltfläche „Create key“ (Schlüssel erstellen)" border="false":::
    

5. Kopieren Sie den Wert von **Zugriffstoken**. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Federated Directory-Anwendung in das Feld **Geheimes Token** eingegeben. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="Screenshot einer Seite in der Federated Directory-Verwaltungskonsole. Ein Platzhalter für ein Zugriffstoken sowie der Name, die Beschreibung und der Aussteller eines Schlüssels werden angezeigt." border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>Hinzufügen von Federated Directory aus dem Katalog

Bevor Sie Federated Directory für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Federated Directory aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um Federated Directory aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Federated Directory** ein, und wählen Sie im Ergebnisbereich **Federated Directory** aus.

    ![Federated Directory in der Ergebnisliste](common/search-new-app.png)

5. Navigieren Sie in einem separaten Browserfenster zu der unten hervorgehobenen **URL**. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="Screenshot einer Seite im Azure-Portal mit Informationen zu Federated Directory. Der URL-Wert ist hervorgehoben." border="false":::

6. Klicken Sie auf **ANMELDEN**.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text="Screenshot des Hauptmenüs auf der Federated Directory-Website. Die Schaltfläche „Log in“ (Anmelden) ist hervorgehoben." border="false":::

7.  Da Federated Directory eine OpenIDConnect-App ist, können Sie sich mit Ihrem Microsoft-Geschäftskonto bei Federated Directory anmelden.
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="Screenshot der SCIM AD-Testseite auf der Federated Directory-Website. „Log in with your Microsoft account“ (Mit Microsoft-Konto anmelden) ist hervorgehoben." border="false":::
 
8. Akzeptieren Sie nach der erfolgreichen Authentifizierung auf der Zustimmungsseite die Zustimmungsaufforderung. Die Anwendung wird dann automatisch Ihrem Mandanten hinzugefügt, und Sie werden zu Ihrem Federated Directory-Konto umgeleitet.

    ![Federated Directory Hinzufügen von SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Konfigurieren der automatischen Benutzerbereitstellung in Federated Directory 

In diesem Abschnitt werden die Schritte erläutert, mit denen Sie den Azure AD-Bereitstellungsdienst zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Federated Directory auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD konfigurieren.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Federated Directory in Azur AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Federated Directory** aus.

    ![Federated Directory-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld „Mandanten-URL“ `https://api.federated.directory/v2/` ein. Geben Sie im Feld **Geheimes Token** den Wert ein, den Sie zuvor aus Federated Directory abgerufen und gespeichert haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Federated Directory herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Federated Directory-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Federated Directory synchronisieren** aus.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="Screenshot des Abschnitts „Zuordnungen“. Unter „Name“ ist die Option „Azure Active Directory-Benutzer mit Federated Directory synchronisieren“ hervorgehoben." border="false":::
    
    
11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Federated Directory synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Federated Directory für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="Screenshot der Seite „Attributzuordnungen“. In einer Tabelle werden Attribute für Azure Active Directory und Federated Directory sowie die entsprechende Rangfolge aufgelistet." border="false":::
    

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Federated Directory zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer bzw. Gruppen fest, die in Federated Directory bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Federated Directory ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Reporting on automatic user account provisioning (Tutorial: Meldung zur automatischen Benutzerkontobereitstellung)](../app-provisioning/check-status-user-account-provisioning.md).
## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
