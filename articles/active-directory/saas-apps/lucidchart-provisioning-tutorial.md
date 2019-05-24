---
title: 'Tutorial: Konfigurieren von LucidChart für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in LucidChart konfigurieren.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9540cf882af6b11f0e8624e477ad336f6d5d9ad3
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963680"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von LucidChart für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie in LucidChart und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in LucidChart automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben. 

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Einen Azure Active Directory-Mandanten
* Einen LucidChart-Mandanten, für den mindestens der [Enterprise-Tarif](https://www.lucidchart.com/user/117598685#/subscriptionLevel) aktiviert ist
* Ein Benutzerkonto in LucidChart mit Teamadministratorberechtigungen

## <a name="assigning-users-to-lucidchart"></a>Zuweisen von Benutzern zu LucidChart

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre LucidChart-App benötigen. Anschließend können Sie diese Benutzer Ihrer LucidChart-App zuweisen, indem Sie diese Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Wichtige Tipps zum Zuweisen von Benutzern zu LucidChart

* Es wird empfohlen, LucidChart einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu LucidChart müssen Sie entweder die Rolle **Benutzer** oder eine andere gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Die Rolle **Standardzugriff** funktioniert nicht für die Bereitstellung. Diese Benutzer werden übersprungen.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfigurieren der Benutzerbereitstellung in LucidChart

In diesem Abschnitt wird das Herstellen einer Verbindung von Azure AD mit der LucidChart-API zur Bereitstellung von Benutzerkonten sowie das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in LucidChart basierend auf der Benutzer- und Gruppenzuweisung in Azure AD beschrieben.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für LucidChart aktivieren. Befolgen Sie hierzu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurieren der automatische Bereitstellung von Benutzerkonten für LucidChart in Azure AD

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

2. Suchen Sie über das Suchfeld nach Ihrer LucidChart-Instanz, wenn Sie LucidChart bereits für einmaliges Anmelden konfiguriert haben. Wählen Sie andernfalls **Hinzufügen**, und suchen Sie im Anwendungskatalog nach **LucidChart**. Wählen Sie LucidChart in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

3. Wählen Sie Ihre LucidChart-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung**.

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![LucidChart-Bereitstellung](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. Geben Sie im Abschnitt **Admin Credentials** (Administratoranmeldeinformationen) das von Ihrem LucidChart-Konto generierte **geheime Token** ein. (Sie finden das Token in Ihrem Konto: **Team** > **App-Integration** > **SCIM**).

    ![LucidChart-Bereitstellung](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. Klicken Sie im Azure-Portal auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer LucidChart-App herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr LucidChart-Konto über Teamadministratorberechtigungen verfügt, und wiederholen Sie Schritt 5.

7. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen „Bei Fehler E-Mail-Benachrichtigung senden“.

8. Klicken Sie auf **Speichern**.

9. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit LucidChart synchronisieren**.

10. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit LucidChart synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in LucidChart für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

11. Um den Azure AD-Bereitstellungsdienst für LucidChart zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

12. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die LucidChart im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../manage-apps/check-status-user-account-provisioning.md)
