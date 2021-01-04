---
title: 'Tutorial: Konfigurieren von Cornerstone OnDemand für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Cornerstone OnDemand konfigurieren.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 59c599167089d222324ed880c18e68d763f5e468
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358455"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Cornerstone OnDemand für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die Sie in Cornerstone OnDemand und Azure Active Directory (Azure AD) ausführen müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern oder Gruppen in Cornerstone OnDemand zu konfigurieren.

> [!NOTE]
> Das Tutorial enthält die Beschreibung eines Connectors, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Informationen zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen (Software as a Service) mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial beschriebene Szenario setzt voraus, dass Sie über folgende Elemente verfügen:

* Einen Azure AD-Mandanten.
* Einen Cornerstone OnDemand-Mandanten.
* Ein Benutzerkonto in Cornerstone OnDemand mit Administratorrechten.

> [!NOTE]
> Die Azure AD-Bereitstellungsintegration basiert auf dem [Cornerstone OnDemand-Webdienst](https://www.cornerstoneondemand.com/). Dieser Dienst ist für Cornerstone OnDemand-Teams verfügbar.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Hinzufügen von Cornerstone OnDemand aus dem Azure Marketplace

Bevor Sie Cornerstone OnDemand für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, fügen Sie Cornerstone OnDemand aus dem Marketplace zur Liste der verwalteten SaaS-Anwendungen hinzu.

Führen Sie zum Hinzufügen von Cornerstone OnDemand aus dem Azure Marketplace die folgenden Schritte aus.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory** aus.

    ![Azure Active Directory-Symbol](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie oben im Dialogfeld **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Cornerstone OnDemand** ein, und wählen Sie **Cornerstone OnDemand** aus dem Ergebnisbereich aus. Wählen Sie dann **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![Cornerstone OnDemand in der Ergebnisliste](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Zuweisen von Benutzern zu Cornerstone OnDemand

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Zusammenhang mit der automatischen Benutzerbereitstellung werden nur die Benutzer oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Entscheiden Sie vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung, welche Benutzer oder Gruppen in Azure AD Zugriff auf Cornerstone OnDemand benötigen. Zum Zuweisen dieser Benutzer oder Gruppen zu Cornerstone OnDemand befolgen Sie die Anleitung unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Cornerstone OnDemand

* Wir empfehlen, zuerst nur einen einzelnen Azure AD-Benutzer zu Cornerstone OnDemand zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können Sie dann weitere Benutzer oder Gruppen zuweisen.

* Beim Zuweisen eines Benutzers zu Cornerstone OnDemand müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle (sofern verfügbar) auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurieren der automatischen Benutzerbereitstellung für Cornerstone OnDemand

Dieser Abschnitt führt Sie durch die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts. Verwenden Sie diese zum Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in Cornerstone OnDemand anhand von Benutzer- oder Gruppenzuweisungen in Azure AD.

Zum Konfigurieren der automatischen Benutzerbereitstellung für Cornerstone OnDemand in Azure AD führen Sie die folgenden Schritte aus.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen** > **Cornerstone OnDemand** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Cornerstone OnDemand** aus.

    ![Cornerstone OnDemand-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Cornerstone OnDemand-Bereitstellung](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Legen Sie **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Cornerstone OnDemand-Bereitstellungsmodus](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** den Administratorbenutzernamen, das Administratorkennwort und die Domäne Ihres Cornerstone OnDemand-Kontos ein:

    * Geben Sie im Feld **Administratorbenutzername** den Domänen- oder Benutzernamen des Administratorkontos im Cornerstone OnDemand-Mandanten ein. Beispiel: contoso\admin.

    * Geben Sie im Feld **Administratorkennwort** das Kennwort für den Administratorbenutzernamen ein.

    * Geben Sie im Feld **Domäne** die Webdienst-URL des Cornerstone OnDemand-Mandanten ein. Beispiel: Der Dienst befindet sich unter `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, und für Contoso lautet die Domäne `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Weitere Informationen zum Abrufen der Webdienst-URL finden Sie [in dieser PDF-Datei](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Nachdem Sie die in Schritt 5 gezeigten Felder ausgefüllt haben, wählen Sie **Testverbindung** aus, um sicherzustellen, dass Azure AD eine Verbindung mit Cornerstone OnDemand herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Cornerstone OnDemand-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Verbindung testen in Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Cornerstone OnDemand-Benachrichtigungs-E-Mail](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Wählen Sie **Speichern** aus.

9. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Cornerstone OnDemand synchronisieren**.

    ![Cornerstone OnDemand-Synchronisierung](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Cornerstone OnDemand synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Cornerstone OnDemand für Updatevorgänge verwendet werden. Um alle Änderungen zu speichern, wählen Sie **Speichern** aus.

    ![Cornerstone OnDemand-Attributzuordnungen](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Wenn Sie Bereichsfilter konfigurieren möchten, befolgen Sie die Anleitung unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Um den Azure AD-Bereitstellungsdienst für Cornerstone OnDemand zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Cornerstone OnDemand-Bereitstellungsstatus](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definieren Sie die Benutzer oder Gruppen, die in Cornerstone OnDemand bereitgestellt werden sollen. Wählen Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Cornerstone OnDemand-Bereich](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Speichern in Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die folgenden Synchronisierungen. Diese erfolgen etwa alle 40 Minuten, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und über Links zum Bereitstellungsaktivitätsbericht navigieren. Der Bericht beschreibt alle vom Azure AD-Bereitstellungsdienst in Cornerstone OnDemand ausgeführten Aktionen.

Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

Das Cornerstone OnDemand-Attribut **Position** erwartet einen Wert, der den Rollen im Cornerstone OnDemand-Portal entspricht. Zum Abrufen einer Liste gültiger Werte für **Position** navigieren Sie im Cornerstone OnDemand-Portal zu **Benutzerdatensatz bearbeiten > Organisationsstruktur > Position**.

![Cornerstone OnDemand-Bereitstellung: Benutzerdatensatz bearbeiten](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Cornerstone OnDemand-Bereitstellung: Position](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Cornerstone OnDemand-Bereitstellung: Positionsliste](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
