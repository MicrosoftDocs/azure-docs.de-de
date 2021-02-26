---
title: Ändern der Standardeinstellungen für die Lebensdauer von Token für benutzerdefinierte Azure AD-Apps
description: Aktualisieren der Richtlinien für die Tokengültigkeitsdauer für Ihre Anwendung, die Sie in Azure AD entwickeln
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: d39f378171443f028ef6b549b120b22f2a3405c4
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582940"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Ändern der Standardwerte für die Tokengültigkeitsdauer für eine benutzerdefiniert entwickelte Anwendung

In diesem Artikel wird erläutert, wie Sie Azure AD PowerShell verwenden, um eine Richtlinie für die Gültigkeitsdauer von Zugriffstoken festzulegen. Azure AD Premium ermöglicht App-Entwicklern und Mandantenadministratoren die Konfiguration der Gültigkeitsdauer von Token, die für nicht vertrauliche Clients ausgegeben werden. Die Richtlinien für die Tokengültigkeitsdauer werden mandantenübergreifend oder auf Basis des Ressourcenzugriffs festgelegt.

> [!IMPORTANT]
> Seit Mai 2020 können Mandanten die Gültigkeitsdauer von Aktualisierungs- und Sitzungstoken nicht mehr konfigurieren.  Nach dem 30. Januar 2021 berücksichtigt Azure Active Directory vorhandene Konfigurationen von Aktualisierungs- und Sitzungstoken in Richtlinien nicht mehr. Die Gültigkeitsdauer von Zugriffstoken kann jedoch auch nach der Einstellung weiterhin konfiguriert werden. Weitere Informationen hierzu finden Sie unter [Konfigurierbare Tokengültigkeitsdauer in Microsoft Identity Platform (Vorschau)](./active-directory-configurable-token-lifetimes.md).
> Wir haben [Funktionen für das Verwalten von Authentifizierungssitzungen](../conditional-access/howto-conditional-access-session-lifetime.md) für den bedingten Zugriff in Azure AD implementiert. Mithilfe dieses neuen Features können Sie die Lebensdauer von Aktualisierungstoken durch Festlegen der Anmeldehäufigkeit konfigurieren.  

Zum Festlegen einer Richtlinie für die Lebensdauer von Zugriffstoken müssen Sie das [Azure AD-PowerShell-Modul](https://www.powershellgallery.com/packages/AzureADPreview) herunterladen.
Führen Sie den Befehl **Connect-AzureAD -Confirm** aus.

In dieser Beispielrichtlinie müssen sich Benutzer häufiger in Ihrer Web-App authentifizieren. Diese Richtlinie legt die Gültigkeitsdauer für den Zugriff auf den Dienstprinzipal Ihrer Web-App fest. Erstellen Sie die Richtlinie, und weisen Sie sie Ihrem Dienstprinzipal zu. Rufen Sie außerdem die „ObjectId“ Ihres Dienstprinzipals ab.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Konfigurieren der Gültigkeitsdauer der von Azure AD ausgestellten Token finden Sie unter [Konfigurierbare Gültigkeitsdauer in Azure AD (Vorschau)](./active-directory-configurable-token-lifetimes.md). Dort erfahren Sie unter anderem, wie Sie die Tokengültigkeitsdauer für alle Apps in Ihrer Organisation, für eine mehrinstanzenfähige App oder für einen bestimmten Dienstprinzipal in Ihrer Organisation festlegen. 
* [Azure AD-Tokenreferenz](./id-tokens.md)
