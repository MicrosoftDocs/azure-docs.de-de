---
title: 'Tutorial: Konfigurieren von Workplace by Facebook für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Workplace by Facebook konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c2e23b0d60ca242549ebf2c058ea8f44f2b1c8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60520158"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Workplace by Facebook für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in Workplace by Facebook und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in Workplace by Facebook automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Für das Konfigurieren der Azure AD-Integration mit Workplace by Facebook benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Workplace by Facebook-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="assigning-users-to-workplace-by-facebook"></a>Zuweisen von Benutzern zu Workplace by Facebook

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre Workplace by Facebook-App benötigen. Anschließend können Sie diese Benutzer Ihrer Workplace by Facebook-App zuweisen, indem Sie diese Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Workplace by Facebook

*   Es wird empfohlen, Workplace by Facebook einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

*   Wenn Sie Workplace by Facebook einen Benutzer zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

## <a name="enable-user-provisioning"></a>Aktivieren der Benutzerbereitstellung

Dieser Abschnitt führt Sie durch das Verbinden von Azure AD mit der Workplace by Facebook-API zur Bereitstellung von Benutzerkonten sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in Workplace by Facebook basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

>[!Tip]
>Sie können auch das SAML-basierte einmalige Anmelden für Workplace by Facebook aktivieren. Befolgen Sie dazu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>So konfigurieren Sie die Benutzerkontenbereitstellung für Workplace by Facebook in Azure AD:

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Workplace by Facebook aktivieren.

Azure AD unterstützt die Möglichkeit, die Kontodetails zugewiesener Benutzer mit Workplace by Facebook zu synchronisieren. Dank dieser automatischen Synchronisierung kann Workplace by Facebook die erforderlichen Daten für die Autorisierung von Benutzern für den Zugriff abrufen, bevor diese sich zum ersten Mal anmelden. Sie hebt zudem die Bereitstellung von Benutzern in Workplace by Facebook auf, wenn der Zugriff in Azure AD widerrufen wurde.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory** > **Unternehmens-Apps** > **Alle Anwendungen**.

2. Wenn Sie Workplace by Facebook bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer Workplace by Facebook-Instanz. Klicken Sie andernfalls auf **Hinzufügen**, und suchen Sie im Anwendungskatalog nach **Workplace by Facebook**. Wählen Sie Workplace by Facebook in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

3. Wählen Sie Ihre Workplace by Facebook-Instanz und dann die Registerkarte **Bereitstellung** aus.

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest. 

    ![Bereitstellung](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** das Zugriffstoken Ihres Workplace by Facebook-Administrators ein, und legen Sie die Mandanten-URL auf `https://www.facebook.com/scim/v1/` fest. Lesen Sie [diese Anweisungen](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) zum Erstellen eines Zugriffstoken für Workplace. 

6. Klicken Sie im Azure-Portal auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer Workplace by Facebook-App herstellen kann. Wenn die Verbindung nicht hergestellt werden kann, sollten Sie sicherstellen, dass Ihr Workplace by Facebook-Konto über Teamadministratorberechtigungen verfügt.

7. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen.

8. Klicken Sie auf **Speichern**.

9. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit Workplace by Facebook synchronisieren** aus.

10. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Workplace by Facebook synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Workplace by Facebook für Updatevorgänge verwendet. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

11. Ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**, um den Azure AD-Bereitstellungsdienst für Workplace by Facebook zu aktivieren.

12. Klicken Sie auf **Speichern**.

Weitere Informationen zum Konfigurieren der automatischen Bereitstellung finden Sie unter [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).

Sie können nun ein Testkonto erstellen. Warten Sie bis zu 20 Minuten, um zu überprüfen, ob das Konto mit Workplace by Facebook synchronisiert wurde.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](workplacebyfacebook-tutorial.md)
