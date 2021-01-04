---
title: 'Tutorial: Konfigurieren von Box für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Box konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: jeedes
ms.openlocfilehash: e22738f1fff813e5a928b76f8049e810847fe548
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358149"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Box für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in Box und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in Box automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration in Box benötigen Sie Folgendes:

- Einen Azure AD-Mandanten
- Mindestens einen Box Business-Plan

> [!NOTE]
> Es wird *nicht* empfohlen, zum Testen der Schritte in diesem Tutorial eine Produktionsumgebung zu verwenden.

> [!NOTE]
> Apps müssen zuerst in der Box-Anwendung aktiviert werden.

Beachten Sie beim Testen der Schritte in diesem Tutorial die folgenden Empfehlungen:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Zuweisen von Benutzern zu Box 

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre Box-App benötigen. Anschließend können Sie diese Benutzer Ihrer Box-App zuweisen, indem Sie diese Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="assign-users-and-groups"></a>Zuweisen von Benutzern und Gruppen
Auf der Registerkarte **Box > Benutzer und Gruppen** im Azure-Portal können Sie angeben, welchen Benutzern und Gruppen Zugriff auf Box gewährt werden soll. Durch die Zuweisung eines Benutzers oder einer Gruppe geschieht Folgendes:

* Azure AD ermöglicht es dem zugewiesenen Benutzer (entweder durch direkte Zuweisung oder durch eine Gruppenmitgliedschaft), sich bei Box zu authentifizieren. Wenn ein Benutzer nicht zugewiesen ist, lässt Azure AD eine Anmeldung bei Box nicht zu und gibt auf der Azure AD-Anmeldeseite einen Fehler zurück.
* Eine App-Kachel für Box wird dem [Anwendungsstartprogramm](../manage-apps/end-user-experiences.md)des Benutzers hinzugefügt.
* Wenn die automatische Bereitstellung aktiviert ist, werden die zugewiesenen Benutzer und/oder Gruppen der Bereitstellungswarteschlange hinzugefügt, damit sie automatisch bereitgestellt werden.
  
  * Wenn nur Benutzerobjekte zur Bereitstellung konfiguriert wurden, werden alle direkt zugewiesenen Benutzer in der Bereitstellungswarteschlange platziert. Zudem werden alle Benutzer, die Mitglieder von zugewiesenen Gruppen sind, in die Bereitstellungswarteschlange aufgenommen. 
  * Wenn Gruppenobjekte zur Bereitstellung konfiguriert wurden, werden alle zugewiesenen Gruppenobjekte in Box bereitgestellt. Außerdem werden alle Benutzer bereitgestellt, die Mitglieder dieser Gruppen sind. Die Gruppen- und Benutzermitgliedschaften bleiben erhalten, nachdem sie an Box übertragen wurden.

Auf der Registerkarte **Attribute > Einmaliges Anmelden** können Sie konfigurieren, welche Benutzerattribute (oder Ansprüche) in Box während der SAML-basierten Authentifizierung angezeigt werden. Auf der Registerkarte **Attribute > Bereitstellung** können Sie konfigurieren, wie Benutzer- und Gruppenattribute während der Bereitstellung von Azure AD an Box übertragen werden.

### <a name="important-tips-for-assigning-users-to-box"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Box 

*   Es wird empfohlen, Box einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

*   Wenn Sie Box einen Benutzer zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

## <a name="enable-automated-user-provisioning"></a>Aktivieren der automatisierten Benutzerbereitstellung

Dieser Abschnitt führt Sie durch das Herstellen einer Verbindung von Azure AD mit der Box-API zur Bereitstellung von Benutzerkonten sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in Box basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

Wenn die automatische Bereitstellung aktiviert ist, werden die zugewiesenen Benutzer und/oder Gruppen der Bereitstellungswarteschlange hinzugefügt, damit sie automatisch bereitgestellt werden.
    
 * Wenn nur Benutzerobjekte zur Bereitstellung konfiguriert werden, werden direkt zugewiesene Benutzer in der Bereitstellungswarteschlange platziert. Zudem werden alle Benutzer, die Mitglieder von zugewiesenen Gruppen sind, in die Bereitstellungswarteschlange aufgenommen. 
    
 * Wenn Gruppenobjekte zur Bereitstellung konfiguriert wurden, werden alle zugewiesenen Gruppenobjekte in Box bereitgestellt. Außerdem werden alle Benutzer bereitgestellt, die Mitglieder dieser Gruppen sind. Die Gruppen- und Benutzermitgliedschaften bleiben erhalten, nachdem sie an Box übertragen wurden.

> [!TIP] 
> Sie können auch das SAML-basierte einmalige Anmelden für Box aktivieren. Befolgen Sie hierzu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-account-provisioning"></a>So konfigurieren Sie die automatische Bereitstellung von Benutzerkonten:

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Box aktivieren.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

2. Wenn Sie Box bereits für das einmalige Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer Box-Instanz. Wählen Sie andernfalls **Hinzufügen** , und suchen Sie im Anwendungskatalog nach **Box**. Wählen Sie „Box“ in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

3. Wählen Sie Ihre Box-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung** aus.

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest. 

    ![Screenshot der Registerkarte „Bereitstellung“ für Box im Azure-Portal. Der Bereitstellungsmodus ist auf „Automatisch“ festgelegt, und unter „Administratoranmeldeinformationen“ ist „Autorisieren“ hervorgehoben.](./media/box-userprovisioning-tutorial/provisioning.png)

5. Klicken Sie im Abschnitt **Administratoranmeldeinformationen** auf **Autorisieren** , um ein Dialogfeld für die Anmeldung bei Box in einem neuen Browserfenster zu öffnen.

6. Stellen Sie auf der Seite **Anmelden, um Zugriff auf Box zu gewähren** die erforderlichen Anmeldeinformationen bereit, und klicken Sie dann auf **Autorisieren**. 
   
    ![Screenshot der Anmeldung zum Gewähren des Zugriffs auf den Box-Bildschirm, der einen Eintrag für E-Mail und Kennwort sowie die Schaltfläche „Autorisieren“ zeigt](./media/box-userprovisioning-tutorial/IC769546.png "Automatische Benutzerbereitstellung aktivieren")

7. Klicken Sie auf **Zugriff gewähren auf Box** , um diesen Vorgang zu autorisieren und zum Azure-Portal zurückzukehren. 
   
    ![Screenshot des Bildschirms „Zugriff gewähren“ in Box mit einer erklärenden Meldung und die Schaltfläche „Zugriff gewähren auf Box“](./media/box-userprovisioning-tutorial/IC769549.png "Automatische Benutzerbereitstellung aktivieren")

8. Klicken Sie im Azure-Portal auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer Box-App herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Box-Konto über Teamadministratorberechtigungen verfügt, und wiederholen Sie den Schritt **Autorisieren**.

9. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen.

10. Klicken Sie auf **Speichern**.

11. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit Box synchronisieren** aus.

12. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Box synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Box für Updatevorgänge verwendet. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

13. Um den Azure AD-Bereitstellungsdienst für Box zu aktivieren, ändern Sie im Abschnitt „Einstellungen“ den **Bereitstellungsstatus** in **Ein**.

14. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die Box im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer Box-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

In Ihrem Box-Mandanten werden synchronisierte Benutzer in der **Verwaltungskonsole** unter **Verwaltete Benutzer** aufgelistet.

![Integrationsstatus](./media/box-userprovisioning-tutorial/IC769556.png "Integrationsstatus")


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](box-tutorial.md)