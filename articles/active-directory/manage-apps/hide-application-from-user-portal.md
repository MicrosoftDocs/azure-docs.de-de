---
title: Ausblenden einer Anwendung auf der Benutzeroberfläche in Azure Active Directory | Microsoft-Dokumentation
description: Ausblenden einer Anwendung auf der Benutzeroberfläche in Zugriffsbereichen von Azure Active Directory oder Startfeldern von Office 365
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd98aa974f2adcd363c04c10b7a10cef6ca8ce7
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824540"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Ausblenden von Anwendungen für Endbenutzer in Azure Active Directory

Enthält eine Anleitung, wie Sie Anwendungen für das Panel „MyApps“ oder das Office 365-Startprogramm von Endbenutzern ausblenden. Wenn eine Anwendung ausgeblendet ist, verfügen Benutzer trotzdem über Berechtigungen für die Anwendung. 

## <a name="prerequisites"></a>Voraussetzungen

Die Rechte eines Anwendungsadministrators sind erforderlich, um eine Anwendung im Panel „MyApps“ und Office 365-Startprogramm auszublenden.

Die Rechte eines globalen Administrators sind erforderlich, um alle Office 365-Anwendungen auszublenden.


## <a name="hide-an-application-from-the-end-user"></a>Ausblenden einer Anwendung für den Endbenutzer
Führen Sie die folgenden Schritte aus, um eine Anwendung für das Panel „MyApps“ und das Startprogramm der Office 365-Anwendung auszublenden.

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als globaler Administrator für Ihr Verzeichnis an.
2.  Wählen Sie **Azure Active Directory**.
3.  Wählen Sie **Unternehmensanwendungen**. Das Blatt **Unternehmensanwendungen – Alle Anwendungen** wird geöffnet.
4.  Wählen Sie unter **Anwendungstyp** die Option **Unternehmensanwendungen** aus, falls sie noch nicht ausgewählt ist.
5.  Suchen Sie nach der Anwendung, die Sie ausblenden möchten, und klicken Sie darauf.  Die Übersicht der Anwendung wird geöffnet.
6.  Klicken Sie auf **Eigenschaften**. 
7.  Klicken Sie für die Frage **Für Benutzer sichtbar?** auf **Nein**.
8.  Klicken Sie auf **Speichern**.


## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Ausblenden von Office 365-Anwendungen im Panel „MyApps“

Mit den folgenden Schritten können Sie alle Office 365-Anwendungen im Panel „MyApps“ ausblenden. Die Anwendungen sind im Office 365-Portal weiterhin sichtbar.

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als globaler Administrator für Ihr Verzeichnis an.
2.  Wählen Sie **Azure Active Directory**.
3.  Wählen Sie **Benutzereinstellungen** aus.
4.  Klicken Sie unter **Unternehmensanwendungen** auf **Start und Anzeige von Anwendungen durch Endbenutzer verwalten**.
5.  Klicken Sie für **Benutzer können Office 365-Apps nur im Office 365-Portal anzeigen** auf **Ja**.
6.  Klicken Sie auf **Speichern**.


## <a name="next-steps"></a>Nächste Schritte
* [Alle meine Gruppen anzeigen](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](assign-user-or-group-access-portal.md)
* [Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App](remove-user-or-group-access-portal.md)
* [Ändern des Namens oder Logos einer Unternehmens-App](change-name-or-logo-portal.md)

