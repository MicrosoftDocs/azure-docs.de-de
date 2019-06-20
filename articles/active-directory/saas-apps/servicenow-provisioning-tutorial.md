---
title: 'Tutorial: Konfigurieren von ServiceNow für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie Benutzerkonten aus Azure AD für ServiceNow automatisch bereitstellen bzw. die Bereitstellung aufheben.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b3e4cc5ba4bc0173721947bd1e1a680ca7b3a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60869829"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Tutorial: Konfigurieren von ServiceNow für die automatische Benutzerbereitstellung in Azure Active Directory

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in ServiceNow und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in ServiceNow automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit ServiceNow konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Für ServiceNow eine Instanz oder einen Mandanten von ServiceNow, Calgary-Version oder höher
- Für ServiceNow Express eine Instanz von ServiceNow Express, Helsinki-Version oder höher

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).


## <a name="assigning-users-to-servicenow"></a>Zuweisen von Benutzern zu ServiceNow

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre ServiceNow-App benötigen. Anschließend können Sie diese Benutzer Ihrer ServiceNow-App zuweisen, indem Sie diese Anweisungen befolgen: [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Es wird empfohlen, ServiceNow einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.
>*   Wenn Sie ServiceNow einen Benutzer zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

## <a name="enable-automated-user-provisioning"></a>Aktivieren der automatisierten Benutzerbereitstellung

Dieser Abschnitt führt Sie durch das Herstellen einer Verbindung von Azure AD mit der ServiceNow-API zur Bereitstellung von Benutzerkonten sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in ServiceNow basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

> [!TIP]
>Sie können auch das SAML-basierte einmalige Anmelden für ServiceNow aktivieren. Befolgen Sie dazu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurieren der automatischen Bereitstellung von Benutzerkonten

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

1. Wenn Sie ServiceNow bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer ServiceNow-Instanz. Wählen Sie andernfalls **Hinzufügen** aus, und suchen Sie im Anwendungskatalog nach **ServiceNow**. Wählen Sie ServiceNow in den Suchergebnissen aus, und fügen Sie die Anwendung Ihrer Anwendungsliste hinzu.

1. Wählen Sie Ihre ServiceNow-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung**.

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest. 

    ![Bereitstellung](./media/servicenow-provisioning-tutorial/provisioning.png)

1. Führen Sie im Abschnitt „Administratoranmeldeinformationen“ die folgenden Schritte aus:
   
    a. Geben Sie im Textfeld **ServiceNow-Instanzname** den Namen des ServiceNow-Instanz ein.

    b. Geben Sie im Textfeld **ServiceNow-Administratorbenutzername** den Benutzernamen eines Administrators ein.

    c. Geben Sie im Textfeld **ServiceNow-Administratorkennwort** das Kennwort des Administrators ein.

1. Klicken Sie im Azure-Portal auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer ServiceNow-App herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr ServiceNow-Konto über Teamadministratorberechtigungen verfügt, und führen Sie den Schritt **Administratoranmeldeinformationen** erneut aus.

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen.

1. Klicken Sie auf **Speichern**.

1. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit ServiceNow synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit ServiceNow synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute zum Abgleichen der Benutzerkonten in ServiceNow bei Updatevorgängen verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

1. Um den Azure AD-Bereitstellungsdienst für ServiceNow zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt „Einstellungen“ in **Ein**.

1. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die ServiceNow im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen über Bereitstellungsaktivitäten aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer ServiceNow-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](servicenow-tutorial.md)


