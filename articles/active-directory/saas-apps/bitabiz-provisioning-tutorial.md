---
title: 'Tutorial: Konfigurieren von BitaBIZ für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in BitaBIZ konfigurieren.
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
ms.openlocfilehash: 8eccc3be7da201ee1e2af046c6b515871ef05adc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350771"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von BitaBIZ für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in BitaBIZ und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in BitaBIZ zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* [Einen BitaBIZ-Mandanten](https://bitabiz.dk/en/price/).
* Ein Benutzerkonto in BitaBIZ mit Administratorberechtigungen.

## <a name="assigning-users-to-bitabiz"></a>Zuweisen von Benutzern zu BitaBIZ

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf BitaBIZ benötigen. Anschließend können Sie BitaBIZ diese Benutzer und/oder Gruppen zuweisen, indem Sie den folgenden Anweisungen folgen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Wichtige Tipps zum Zuweisen von Benutzern zu BitaBIZ

* Es wird empfohlen, BitaBIZ einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu BitaBIZ müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle (sofern verfügbar) auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="setup-bitabiz-for-provisioning"></a>Einrichten von BitaBIZ für die Bereitstellung

Bevor Sie BitaBIZ für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie in BitaBIZ die SCIM-Bereitstellung aktivieren.

1. Melden Sie sich bei Ihrer [BitaBIZ-Verwaltungskonsole](https://www.bitabiz.com/login?lang=en) an. Klicken Sie auf **SETUP ADMIN** (ADMINISTRATOR EINRICHTEN).

    :::image type="content" source="media/bitabiz-provisioning-tutorial/setup-admin.png" alt-text="Screenshot der BitaBIZ-Verwaltungskonsole mit hervorgehobener Option „Setup admin“ (Administrator festlegen)" border="false":::

2.  Navigieren Sie zu **INTEGRATION**.

    :::image type="content" source="media/bitabiz-provisioning-tutorial/integration.png" alt-text="Screenshot der BitaBIZ-Verwaltungskonsole mit hervorgehobener Option „Integration“" border="false":::

2.  Navigieren Sie zu **Microsoft Azure AD-Bereitstellung**.  Wählen Sie für die automatische Benutzerbereitstellung die Option **Aktiviert** aus. Kopieren Sie die Werte von **URL des SCIM-Bereitstellungsendpunkts** und **Bearertoken**. Diese Werte werden im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer BitaBIZ-Anwendung in das Feld „Mandanten-URL“ bzw. „Geheimes Token“ eingegeben.

    ![BitaBIZ, SCIM hinzufügen](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Hinzufügen von BitaBIZ aus dem Katalog

Um BitaBIZ für die automatische Benutzerbereitstellung mit Azure AD konfigurieren zu können, müssen Sie Ihrer Liste der verwalteten SaaS-Anwendungen BitaBIZ aus dem Azure AD-Anwendungskatalog hinzufügen.

**Führen Sie die folgenden Schritte aus, um BitaBIZ aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld den Namen **BitaBIZ** ein, wählen Sie im Ergebnisbereich die Anwendung **BitaBIZ** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![BitaBIZ in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Konfigurieren der automatischen Benutzerbereitstellung für BitaBIZ 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in BitaBIZ auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für BitaBIZ aktivieren. Folgen Sie dazu den Anweisungen unter [Tutorial: Azure Active Directory-Integration von BitaBIZ](BitaBIZ-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für BitaBIZ in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **BitaBIZ** aus.

    ![BitaBIZ-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt „Administratoranmeldeinformationen“ die zuvor abgerufenen Werte von **URL des SCIM-Bereitstellungsendpunkts** und **Bearertoken** in das Feld „Mandanten-URL“ bzw. „Geheimes Token“ ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit BitaBIZ herstellen kann. Wenn die Verbindung nicht hergestellt werden kann, stellen Sie sicher, dass Ihr BitaBIZ-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch einmal.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit BitaBIZ synchronisieren** aus.

    ![BitaBIZ-Benutzerzuordnungen](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit BitaBIZ synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in BitaBIZ für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![BitaBIZ-Benutzerattribute](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für BitaBIZ zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer und/oder Gruppen fest, die in BitaBIZ bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Bereitstellungsaktivitätsberichten aufrufen. In diesen Berichten sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in BitaBIZ ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* BitaBIZ erfordert **userName**, **email**, **firstName** und **lastName** als erforderliche Attribute. 
* Das endgültige Löschen wird von BitaBIZ derzeit nicht unterstützt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen](../app-provisioning/check-status-user-account-provisioning.md).
