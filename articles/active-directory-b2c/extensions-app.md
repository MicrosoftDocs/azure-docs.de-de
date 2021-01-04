---
title: Erweiterungs-App in Azure Active Directory B2C | Microsoft-Dokumentation
description: Wiederherstellen der App „b2c-extensions-app“
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc536fa4292d794e8d89a2564ad10a3c10dd0a3d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560853"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: Erweiterungs-App

Wenn ein Azure AD B2C-Verzeichnis erstellt wird, wird automatisch eine App namens `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` im neuen Verzeichnis erstellt. Diese als **b2c-extensions-app** bezeichnete App wird unter *App-Registrierungen* angezeigt. Sie wird vom Azure AD B2C-Dienst zum Speichern von Informationen zu Benutzern und benutzerdefinierten Attributen verwendet. Wenn die App gelöscht wird, funktioniert Azure AD B2C nicht ordnungsgemäß, und Ihre Produktionsumgebung ist beeinträchtigt.

> [!IMPORTANT]
> Löschen Sie die b2c-extensions-app nur, wenn Sie vorhaben, sofort Ihren Mandanten zu löschen. Wenn die App länger als 30 Tage gelöscht bleibt, gehen die Benutzerinformationen dauerhaft verloren.

## <a name="verifying-that-the-extensions-app-is-present"></a>Überprüfen des Vorhandenseins der Erweiterungs-App

So überprüfen Sie das Vorhandensein der b2c-extensions-app

1. Klicken Sie in Ihrem Azure AD B2C-Mandanten im Navigationsmenü auf der linken Seite auf **Alle Dienste**.
1. Suchen Sie nach **App-Registrierungen**, und öffnen Sie sie.
1. Suchen Sie nach einer App, die mit **b2c-extensions-app** beginnt.

## <a name="recover-the-extensions-app"></a>Wiederherstellen der Erweiterungs-App

Wenn Sie die b2c-extensions-app versehentlich gelöscht haben, haben Sie 30 Tage Zeit, sie wiederherzustellen. Sie können die App mithilfe der Graph-API wiederherstellen:

1. Navigieren Sie zu [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Melden Sie sich bei der Website als globaler Administrator für das Azure AD B2C-Verzeichnis an, für das die gelöschte App wiederhergestellt werden soll. Dieser globale Administrator muss eine E-Mail-Adresse besitzen, die etwa wie folgt aussieht: `username@{yourTenant}.onmicrosoft.com`.
1. Verwenden Sie eine HTTP-GET-Methode für die URL `https://graph.windows.net/myorganization/deletedApplications` mit dem Wert „1.6“ für „api-version“. Bei diesem Vorgang werden alle Anwendungen aufgelistet, die in den letzten 30 Tagen gelöscht wurden.
1. Suchen Sie in der Liste nach der Anwendung, deren Name mit „b2c-extensions-app“ beginnt, und kopieren Sie den zugehörigen Eigenschaftswert `objectid`.
1. Verwenden Sie eine HTTP-POST-Methode für die URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Ersetzen Sie den Teil `{OBJECTID}` der URL durch die `objectid` aus dem vorherigen Schritt.

Sie sollten jetzt [die wiederhergestellte App im Azure-Portal sehen](#verifying-that-the-extensions-app-is-present) können.

> [!NOTE]
> Eine Anwendung kann nur wiederhergestellt werden, wenn sie in den letzten 30 Tagen gelöscht wurde. Wenn mehr als 30 Tage vergangen sind, gehen die Daten dauerhaft verloren. Um weitere Unterstützung zu erhalten, erstellen Sie ein Supportticket.
