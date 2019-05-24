---
title: Deaktivieren von Benutzeranmeldungen für eine Unternehmens-App in Azure Active Directory | Microsoft-Dokumentation
description: Deaktivieren einer Unternehmens-App in Azure Active Directory, damit sich Benutzer nicht mehr bei der App anmelden können
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90000f34ff247fdd5939dc19971c170aa4b70386
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824659"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Deaktivieren von Benutzeranmeldungen für eine Unternehmens-App in Azure Active Directory
In Azure Active Directory (Azure AD) können Sie auf einfache Weise eine Unternehmensanwendung deaktivieren, damit sich keine Benutzer mehr bei der Anwendung anmelden können. Sie müssen über die entsprechenden Berechtigungen zum Verwalten der Unternehmens-App verfügen. Außerdem müssen Sie ein globaler Administrator für das Verzeichnis sein.

## <a name="how-do-i-disable-user-sign-ins"></a>Wie deaktiviere ich Benutzeranmeldungen?
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) über ein Konto an, das als globaler Administrator für das Verzeichnis konfiguriert ist.
1. Wählen Sie **Alle Dienste** aus, geben Sie **Azure Active Directory** in das Textfeld ein, und drücken Sie die **EINGABETASTE**.
1. Wählen Sie im Bereich **Azure Active Directory** -  ***Verzeichnisname*** (also dem Azure AD-Bereich für das Verzeichnis, das Sie verwalten) die Option **Unternehmensanwendungen** aus.
1. Im Bereich **Unternehmensanwendungen – Alle Anwendungen** wird eine Liste der Apps angezeigt, die Sie verwalten können. Wählen Sie eine App aus.
1. Wählen Sie im Bereich Blatt ***App-Name*** (dem Bereich mit dem Namen der ausgewählten App im Titel) die Option **Eigenschaften** aus.
1. Wählen Sie im Bereich ***App-Name*** - **Eigenschaften** für die Option **Aktiviert für die Benutzeranmeldung?** die Einstellung **Nein** aus.
1. Klicken Sie auf **Speichern** .

## <a name="next-steps"></a>Nächste Schritte
* [Alle meine Gruppen anzeigen](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](assign-user-or-group-access-portal.md)
* [Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App](remove-user-or-group-access-portal.md)
* [Ändern des Namens oder Logos einer Unternehmens-App](change-name-or-logo-portal.md)
