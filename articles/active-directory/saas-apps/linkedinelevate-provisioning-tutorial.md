---
title: 'Tutorial: Benutzerbereitstellung für LinkedIn Elevate – Azure AD'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in LinkedIn Elevate konfigurieren.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 5e972475530ad36a188f73990bb9eca35748c36c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358948"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von LinkedIn Elevate für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in LinkedIn Elevate und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in LinkedIn Elevate automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Azure Active Directory-Mandant
* Ein LinkedIn Elevate-Mandant
* Ein Administratorkonto in LinkedIn Elevate mit Zugriff auf das LinkedIn Account Center

> [!NOTE]
> Integration von Azure Active Directory mit LinkedIn Elevate mit dem [SCIM](http://www.simplecloud.info/)-Protokoll

## <a name="assigning-users-to-linkedin-elevate"></a>Zuweisen von Benutzern zu LinkedIn Elevate

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf LinkedIn Elevate benötigen. Anschließend können Sie diese Benutzer LinkedIn Elevate zuweisen, indem Sie diese Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Wichtige Tipps zum Zuweisen von Benutzern zu LinkedIn Elevate

* Es wird empfohlen, LinkedIn Elevate einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu LinkedIn Elevate müssen Sie im Dialogfeld „Zuweisung“ die Rolle **Benutzer** auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Konfigurieren der Benutzerbereitstellung in LinkedIn Elevate

Dieser Abschnitt führt Sie durch das Herstellen einer Verbindung von Azure AD mit der API zur Bereitstellung von SCIM-Benutzerkonten von LinkedIn Elevate sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in LinkedIn Elevate basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

**Tipp:** Optional können Sie das SAML-basierte einmalige Anmelden für LinkedIn Elevate aktivieren. Befolgen Sie dazu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>So konfigurieren Sie die automatische Bereitstellung von Benutzerkonten für LinkedIn Elevate in Azure AD:

Der erste Schritt besteht aus dem Abrufen Ihres LinkedIn-Zugriffstokens. Wenn Sie ein Enterprise-Administrator sind, können Sie das Zugriffstoken selbst bereitstellen. Gehen Sie in Ihrem Kontocenter auf **Einstellungen &gt; Globale Einstellungen** , und öffnen Sie den Bereich **SCIM Setup**.

> [!NOTE]
> Wenn Sie direkt auf das Kontocenter zugreifen und nicht über einen Link, können Sie es über die folgenden Schritte erreichen.

1. Melden Sie sich am Kontocenter an.

2. Wählen Sie **Administrator &gt; Administratoreinstellungen** aus.

3. Klicken Sie auf **Advanced Integrations** (Erweiterte Integrationen) auf der linken Seitenleiste. Sie werden zum Kontocenter weitergeleitet.

4. Klicken Sie auf **+ Add new SCIM configuration** (+ Neue SCIM-Konfiguration hinzufügen), und befolgen Sie das Verfahren, indem Sie jedes Feld ausführen.

    > [!NOTE]
    > Wenn die automatische Zuweisung von Lizenzen nicht aktiviert ist, bedeutet das, dass nur Benutzerdaten synchronisiert werden.

    ![Screenshot der globalen Einstellungen im LinkedIn-Kontocenter.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Wenn die automatische Zuweisung von Lizenzen aktiviert ist, müssen Sie die Anwendungsinstanz und den Lizenztyp notieren. Lizenzen werden nach dem Prinzip „Wer zuerst kommt, malt zuerst“ zugewiesen, bis alle Lizenzen ausgeführt sind.

    ![Screenshot der Seite „SCIM Setup“ (SCIM-Einrichtung).](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Klicken Sie dann auf **Token generieren**. Ihr Zugriffstoken sollte unter dem Feld **Zugriffstoken** angezeigt werden.

6. Speichern Sie Ihr Zugriffstoken in der Zwischenablage oder auf dem Computer, bevor Sie diese Seite verlassen.

7. Wechseln Sie als Nächstes im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

8. Wenn Sie LinkedIn Elevate bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer LinkedIn Elevate-Instanz. Wählen Sie andernfalls **Hinzufügen** , und suchen Sie im Anwendungskatalog nach **LinkedIn Elevate**. Wählen Sie „LinkedIn Elevate“ in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

9. Wählen Sie Ihre LinkedIn Elevate-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung**.

10. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Seite für die LinkedIn Elevate-Bereitstellung.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Füllen Sie die folgenden Felder unter **Administratoranmeldeinformationen** aus:

    * Geben Sie im Feld **Mandanten-URL** die URL `https://api.linkedin.com` ein.

    * Geben Sie im Feld **Geheimes Token** den im Schritt 1 generierten Zugriffstoken ein, und klicken Sie auf **Verbindung testen**.

    * Nun sollten Sie oben rechts im Portal eine Benachrichtigung über die erfolgreiche Ausführung sehen.

12. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das unten gezeigte Kontrollkästchen.

13. Klicken Sie auf **Speichern**.

14. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzer- und Gruppenattribute, die von Azure AD mit LinkedIn Elevate synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten und Gruppen in LinkedIn Elevate für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

    ![Screenshot der Zuordnungen einschließlich der Attributzuordnungen.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Um den Azure AD-Bereitstellungsdienst für LinkedIn Elevate zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

16. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die LinkedIn Elevate im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Beachten Sie, dass die Erstsynchronisierung länger dauert als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer LinkedIn Elevate-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Weitere Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
