---
title: 'Tutorial: Benutzerbereitstellung – LinkedIn Sales Navigator, Azure AD'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in LinkedIn Sales Navigator konfigurieren.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 458b527194c1123e266bd6abedf25de18e0cee09
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359356"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von LinkedIn Sales Navigator für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in LinkedIn Sales Navigator und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in LinkedIn Sales Navigator automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Azure Active Directory-Mandant
* Einen LinkedIn Sales Navigator-Mandanten 
* Ein Administratorkonto in LinkedIn Sales Navigator mit Zugriff auf das LinkedIn Account Center

> [!NOTE]
> Die Integration von Azure Active Directory mit LinkedIn Sales Navigator erfolgt über das [SCIM](http://www.simplecloud.info/)-Protokoll.

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Zuweisen von Benutzern zu LinkedIn Sales Navigator

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf LinkedIn Sales Navigator benötigen. Anschließend können Sie diese Benutzer LinkedIn Sales Navigator anhand der folgenden Anweisungen zuweisen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Wichtige Tipps zum Zuweisen von Benutzern zu LinkedIn Sales Navigator

* Es wird empfohlen, LinkedIn Sales Navigator einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu LinkedIn Sales Navigator müssen Sie im Dialogfeld „Zuweisung“ die Rolle **Benutzer** auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Konfigurieren der Benutzerbereitstellung in LinkedIn Sales Navigator

Dieser Abschnitt führt Sie durch das Herstellen einer Verbindung von Azure AD mit der API zur Bereitstellung von SCIM-Benutzerkonten von LinkedIn Sales Navigator sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in LinkedIn Sales Navigator basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für LinkedIn Sales Navigator aktivieren. Befolgen Sie dazu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>So konfigurieren Sie die automatische Bereitstellung von Benutzerkonten für LinkedIn Sales Navigator in Azure AD

Der erste Schritt besteht aus dem Abrufen Ihres LinkedIn-Zugriffstokens. Wenn Sie ein Enterprise-Administrator sind, können Sie das Zugriffstoken selbst bereitstellen. Gehen Sie in Ihrem Kontocenter auf **Einstellungen &gt; Globale Einstellungen** , und öffnen Sie den Bereich **SCIM Setup**.

> [!NOTE]
> Wenn Sie direkt auf das Kontocenter zugreifen und nicht über einen Link, können Sie es über die folgenden Schritte erreichen.

1. Melden Sie sich am Kontocenter an.

2. Wählen Sie **Administrator &gt; Administratoreinstellungen** aus.

3. Klicken Sie auf **Advanced Integrations** (Erweiterte Integrationen) auf der linken Seitenleiste. Sie werden zum Kontocenter weitergeleitet.

4. Klicken Sie auf **+ Add new SCIM configuration** (+ Neue SCIM-Konfiguration hinzufügen), und befolgen Sie das Verfahren, indem Sie jedes Feld ausführen.

    > [!NOTE]
    > Wenn die automatische Zuweisung von Lizenzen nicht aktiviert ist, bedeutet das, dass nur Benutzerdaten synchronisiert werden.

    ![Screenshot der globalen Einstellungen im LinkedIn-Kontocenter.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > Wenn die automatische Zuweisung von Lizenzen aktiviert ist, müssen Sie die Anwendungsinstanz und den Lizenztyp notieren. Lizenzen werden nach dem Prinzip „Wer zuerst kommt, malt zuerst“ zugewiesen, bis alle Lizenzen ausgeführt sind.

    ![Screenshot der Seite „SCIM Setup“ (SCIM-Einrichtung).](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Klicken Sie dann auf **Token generieren**. Ihr Zugriffstoken sollte unter dem Feld **Zugriffstoken** angezeigt werden.

6. Speichern Sie Ihren Zugriffstoken in Ihrer Zwischenablage oder auf dem Computer, bevor Sie diese Seite verlassen.

7. Wechseln Sie als Nächstes im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

8. Wenn Sie LinkedIn Sales Navigator bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer LinkedIn Sales Navigator-Instanz. Wählen Sie andernfalls **Hinzufügen** aus, und suchen Sie im Anwendungskatalog nach **LinkedIn Sales Navigator**. Wählen Sie „LinkedIn Sales Navigator“ in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

9. Wählen Sie Ihre LinkedIn Sales Navigator-Instanz und dann die Registerkarte **Bereitstellung** aus.

10. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Seite für die LinkedIn Elevate-Bereitstellung.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Füllen Sie die folgenden Felder unter **Administratoranmeldeinformationen** aus:

    * Geben Sie im Feld **Mandanten-URL** die URL https://developer.linkedin.com ein.

    * Geben Sie im Feld **Geheimes Token** den im Schritt 1 generierten Zugriffstoken ein, und klicken Sie auf **Verbindung testen**.

    * Nun sollten Sie oben rechts im Portal eine Benachrichtigung über die erfolgreiche Ausführung sehen.

12. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das unten gezeigte Kontrollkästchen.

13. Klicken Sie auf **Speichern**.

14. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzer- und Gruppenattribute, die von Azure AD mit LinkedIn Sales Navigator synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten und Gruppen in LinkedIn Sales Navigator für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

    ![Screenshot der Zuordnungen einschließlich der Attributzuordnungen.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Um den Azure AD-Bereitstellungsdienst für LinkedIn Sales Navigator zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

16. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die LinkedIn Sales Navigator im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Beachten Sie, dass die Erstsynchronisierung länger dauert als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer LinkedIn Sales Navigator-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Weitere Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
