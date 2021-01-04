---
title: 'Tutorial: Konfigurieren von Keeper Password Manager & Digital Vault für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Keeper Password Manager & Digital Vault konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2670dc0cb56805a2afa966bee1d2aa52b6c8e46a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358979"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Keeper Password Manager & Digital Vault für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Keeper Password Manager & Digital Vault und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern bzw. Gruppen in Keeper Password Manager & Digital Vault zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Ein Keeper Password Manager & Digital Vault-Mandant](https://keepersecurity.com/pricing.html?t=e)
* Ein Benutzerkonto mit Administratorrechten in Keeper Password Manager & Digital Vault.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Hinzufügen von Keeper Password Manager & Digital Vault aus dem Katalog

Bevor Sie Keeper Password Manager & Digital Vault für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Keeper Password Manager & Digital Vault aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um Keeper Password Manager & Digital Vault aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **Keeper Password Manager & Digital Vault** in das Suchfeld ein, wählen Sie im Ergebnisbereich **Keeper Password Manager & Digital Vault** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Keeper Password Manager & Digital Vault in der Ergebnisliste](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Zuweisen von Benutzern zu Keeper Password Manager & Digital Vault

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer bzw. Gruppen in Azure AD Zugriff auf Keeper Password Manager & Digital Vault benötigen. Anschließend können Sie diese Benutzer bzw. Gruppen Keeper Password Manager & Digital Vault anhand der folgenden Anweisungen zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Keeper Password Manager & Digital Vault

* Es wird empfohlen, Keeper Password Manager & Digital Vault einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Keeper Password Manager & Digital Vault müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Konfigurieren der automatischen Benutzerbereitstellung für Keeper Password Manager & Digital Vault 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Keeper Password Manager & Digital Vault auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Außerdem können Sie wahlweise SAML-basiertes einmaliges Anmelden für Keeper Password Manager & Digital Vault aktivieren, indem Sie die Anweisungen im [Tutorial: Azure Active Directory-Integration in Keeper Password Manager & Digital Vault](keeperpasswordmanager-tutorial.md) befolgen. Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Keeper Password Manager & Digital Vault in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Keeper Password Manager & Digital Vault**.

    ![Keeper Password Manager & Digital Vault-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** wie in Schritt 6 beschrieben die **Mandanten-URL** und das **geheime Token** Ihres Keeper Password Manager & Digital Vault-Kontos ein.

6. Melden Sie sich bei Ihrer [Keeper Admin-Konsole](https://keepersecurity.com/console/#login) an. Klicken Sie auf **Admin** (Administrator), und wählen Sie einen vorhandenen Knoten aus, oder erstellen Sie einen neuen. Navigieren Sie zur Registerkarte **Provisioning** (Bereitstellung), und wählen Sie **Add Method** (Methode hinzufügen) aus.

    ![Keeper Admin-Konsole](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Wählen Sie **SCIM (System for Cross-Domain Identity Management)** – System für domänenübergreifendes Identitätsmanagement – aus.

    ![Keeper – SCIM hinzufügen](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Klicken Sie auf **Create Provisioning Token** (Bereitstellungstoken erstellen).

    ![Keeper – Endpunkt erstellen](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Kopieren Sie die Werte für **URL** und **Token** , und fügen Sie sie in Azure AD in **Mandanten-URL** und **Geheimes Token** ein. Klicken Sie auf **Save** (Speichern), um die Bereitstellungseinrichtung unter Keeper abzuschließen.

    ![Keeper – Token erstellen](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Klicken Sie nach dem Auffüllen der in Schritt 5 gezeigten Felder auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Keeper Password Manager & Digital Vault herstellen kann. Wenn die Verbindung nicht möglich ist, sollten Sie sicherstellen, dass Ihr Keeper Password Manager & Digital Vault-Konto über Administratorberechtigungen verfügt, und den Vorgang wiederholen.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Keeper Password Manager & Digital Vault synchronisieren**.

    ![Keeper – Benutzerzuordnungen](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Keeper Password Manager & Digital Vault synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Keeper Password Manager & Digital Vault für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Keeper – Benutzerattribute](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Keeper Password Manager & Digital Vault synchronisieren**.

    ![Keeper – Gruppenzuordnungen](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Keeper Password Manager & Digital Vault synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Gruppen in Keeper Password Manager & Digital Vault für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Keeper – Gruppenattribute](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Um den Azure AD-Bereitstellungsdienst für Keeper Password Manager & Digital Vault zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

16. Legen Sie die Benutzer bzw. Gruppen fest, die in Keeper Password Manager & Digital Vault bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

17. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Keeper Password Manager & Digital Vault ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Keeper Password Manager & Digital Vault erfordert, dass **emails** und **userName** den gleichen Quellenwert haben, da alle Updates für eines der Attribute den jeweils anderen Wert ändern.
* Keeper Password Manager & Digital Vault unterstützt nicht das Löschen von Benutzern, nur das Deaktivieren. Deaktivierte Benutzer werden in der Keeper Admin-Konsole als gesperrt angezeigt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

