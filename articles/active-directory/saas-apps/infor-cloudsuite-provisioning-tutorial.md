---
title: 'Tutorial: Konfigurieren von Infor CloudSuite für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Infor CloudSuite konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 8fdd2c8a326fbdc68d1aec65377f4c465c5ee4c1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353900"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Infor CloudSuite für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Infor CloudSuite und Azure Active Directory (Azure AD) durchgeführt werden müssen, um das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern bzw. Gruppen in Infor CloudSuite durch Azure AD zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Infor CloudSuite-Mandanten](https://www.infor.com/products/infor-os)
* Ein Benutzerkonto in Infor CloudSuite mit Administratorberechtigungen

## <a name="assigning-users-to-infor-cloudsuite"></a>Zuweisen von Benutzern zu Infor CloudSuite

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Infor CloudSuite benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen Infor CloudSuite wie folgt zuweisen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Infor CloudSuite

* Es wird empfohlen, Infor CloudSuite einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Infor CloudSuite müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Einrichten von Infor CloudSuite für die Bereitstellung

1. Melden Sie sich bei ihrer [Infor CloudSuite-Verwaltungskonsole](https://www.infor.com/customer-center) an. Klicken Sie auf das Benutzersymbol, und navigieren Sie dann zu **Benutzerverwaltung**.

    ![Infor CloudSuite-Verwaltungskonsole](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Klicken Sie auf das Menüsymbol in der linken oberen Ecke des Bildschirms. Klicken Sie auf **Verwalten**.

    ![Infor CloudSuite – Hinzufügen von SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navigieren Sie zu den **SCIM-Konten**.

    ![Infor CloudSuite – SCIM-Konto](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Fügen Sie einen Administratorbenutzer hinzu, indem Sie auf das Pluszeichen klicken. Geben Sie ein **SCIM-Kennwort** an, und geben Sie dasselbe Kennwort unter **Kennwort bestätigen** ein. Klicken Sie auf das Ordnersymbol, um das Kennwort zu speichern. Daraufhin wird eine **Benutzer-ID** angezeigt, die für den Administratorbenutzer generiert wurde.

    ![Infor CloudSuite-Administratorbenutzer](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite-Kennwort](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Der Screenshot zeigt die Infor CloudSuite-Administratorkonsole mit einer hervorgehobenen Tabellenzeile. Die Zeile enthält eine Benutzer-ID, Kennwörter und einen Zeitstempel." border="false":::

5. Um das Bearertoken zu generieren, kopieren Sie die **Benutzer-ID** und das **SCIM-Kennwort**. Fügen Sie sie durch einen Doppelpunkt getrennt in den Editor (Notepad++) ein. Codieren Sie den Zeichenfolgenwert, indem Sie zu **Plug-Ins > MIME-Tools > Basic64-Codierung** navigieren. 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Der Screenshot zeigt ein Notepad++-Dokument. Im Menü „Plug-Ins“ ist „MIME-Tools“ hervorgehoben. Im Menü „MIME-Tools“ ist „Base64“ hervorgehoben." border="false":::

3.  Kopieren Sie das Bearertoken. Dieser Wert wird auf der Registerkarte „Bereitstellung“ für Ihre Infor CloudSuite-Anwendung im Azure-Portal in das Feld „Geheimes Token“ eingegeben.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Hinzufügen von Infor CloudSuite aus dem Katalog

Bevor Sie Infor CloudSuite für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Infor CloudSuite aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie zum Hinzufügen von Infor CloudSuite aus dem Azure AD-Anwendungskatalog die folgenden Schritte aus:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Infor CloudSuite** ein, wählen Sie im Ergebnisbereich **Infor CloudSuite** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Infor CloudSuite in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Konfigurieren der automatischen Benutzerbereitstellung in Infor CloudSuite 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Infor CloudSuite auf der Grundlage von Benutzer- bzw. Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Infor CloudSuite aktivieren. Befolgen Sie hierzu die Anweisungen unter [Tutorial: Azure Active Directory-Integration mit Infor CloudSuite](./infor-cloud-suite-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

> [!NOTE]
> Weitere Informationen zum SCIM-Endpunkt von Infor CloudSuite finden Sie [hier](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Infor CloudSuite in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **Infor CloudSuite** aus.

    ![Infor CloudSuite-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` ein. Geben Sie den Wert für Bearertoken ein, den Sie zuvor unter **Geheimes Token** abgerufen haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Infor CloudSuite herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Infor CloudSuite-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Infor CloudSuite synchronisieren**.

    ![Infor CloudSuite – Benutzerzuordnungen](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Infor CloudSuite synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Infor CloudSuite für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Infor CloudSuite – Benutzerattribute](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Infor CloudSuite synchronisieren**.

    ![Infor CloudSuite– Gruppenzuordnungen](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenerattribute, die von Azure AD mit Infor CloudSuite synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Infor CloudSuite für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Infor CloudSuite– Gruppenattribute](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Infor CloudSuite zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Infor CloudSuite bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronization Details** (Synchronisierungsdetails) können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Infor CloudSuite ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)