---
title: Ausblenden einer Unternehmensanwendung auf der Benutzeroberfläche in Azure AD
description: Ausblenden einer Unternehmensanwendung auf der Benutzeroberfläche in Zugriffsbereichen von Azure Active Directory oder Microsoft 365-Anwendungsstartern.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1abd1d72e4435aa59bbb9a99f3cc423f11268e
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858297"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Ausblenden von Unternehmensanwendung für Endbenutzer in Azure Active Directory

Enthält eine Anleitung, wie Sie Anwendungen im Panel „Meine Apps“ oder im Microsoft 365-Anwendungsstarter von Endbenutzern ausblenden. Wenn eine Anwendung ausgeblendet ist, verfügen Benutzer trotzdem über Berechtigungen für die Anwendung. 

## <a name="prerequisites"></a>Voraussetzungen

Die Rechte eines Anwendungsadministrators sind erforderlich, um eine Anwendung im Panel „Meine Apps“ und im Microsoft 365-Anwendungsstarter auszublenden.

Die Rechte eines globalen Administrators sind erforderlich, um alle Microsoft 365-Anwendungen auszublenden.


## <a name="hide-an-application-from-the-end-user"></a>Ausblenden einer Anwendung für den Endbenutzer
Führen Sie die folgenden Schritte aus, um eine Anwendung im Panel „Meine Apps“ und im Microsoft 365-Anwendungsstarter auszublenden.

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als globaler Administrator für Ihr Verzeichnis an.
2.  Wählen Sie **Azure Active Directory** aus.
3.  Wählen Sie **Unternehmensanwendungen**. Das Blatt **Unternehmensanwendungen – Alle Anwendungen** wird geöffnet.
4.  Wählen Sie unter **Anwendungstyp** die Option **Unternehmensanwendungen** aus, falls sie noch nicht ausgewählt ist.
5.  Suchen Sie nach der Anwendung, die Sie ausblenden möchten, und klicken Sie darauf.  Die Übersicht der Anwendung wird geöffnet.
6.  Klicken Sie auf **Eigenschaften**. 
7.  Klicken Sie für die Frage **Für Benutzer sichtbar?** auf **Nein**.
8.  Klicken Sie auf **Speichern**.

> [!NOTE]
> Diese Anweisungen gelten nur für Unternehmensanwendungen.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Ausblenden einer Anwendung mit Azure AD PowerShell

Wenn Sie eine Anwendung im Bereich „MyApps“ ausblenden möchten, können Sie dem Dienstprinzipal für die Anwendung das HideApp-Tag manuell hinzufügen. Führen Sie die folgenden [AzureAD PowerShell](/powershell/module/azuread/#service_principals)-Befehle aus, um die Eigenschaft **Für Benutzer sichtbar?** auf **Nein** festzulegen. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Ausblenden von Microsoft 365-Anwendungen im Panel „Meine Apps“

Mit den folgenden Schritten können Sie alle Microsoft 365-Anwendungen im Panel „Meine Apps“ ausblenden. Die Anwendungen sind im Office 365-Portal weiterhin sichtbar.

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als globaler Administrator für Ihr Verzeichnis an.
2.  Wählen Sie **Azure Active Directory** aus.
3.  Wählen Sie **Benutzer** aus.
4.  Wählen Sie **Benutzereinstellungen** aus.
5.  Klicken Sie unter **Unternehmensanwendungen** auf **Start und Anzeige von Anwendungen durch Endbenutzer verwalten**.
6.  Klicken Sie für **Benutzer können Office 365-Apps nur im Office 365-Portal anzeigen** auf **Ja**.
7.  Klicken Sie auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte
* [Alle meine Gruppen anzeigen](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](assign-user-or-group-access-portal.md)
* [Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App](./assign-user-or-group-access-portal.md)
* [Ändern des Namens oder Logos einer Unternehmens-App](./add-application-portal-configure.md)
