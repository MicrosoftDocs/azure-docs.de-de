---
title: Verwalten von Richtlinien zur Einwilligung für die App in Azure AD
description: Hier erfahren Sie, wie Sie integrierte und benutzerdefinierte Richtlinien zur Einwilligung für die App verwalten können, um zu steuern, wann eine Einwilligung erteilt werden kann.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 68ff89191a4e9b11c2d84ec016090f76b8ea11b7
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258115"
---
# <a name="manage-app-consent-policies"></a>Verwalten von Richtlinien zur Einwilligung für die App

Mit Azure AD PowerShell können Sie Richtlinien zur Einwilligung für die App anzeigen und verwalten.

Eine Richtlinie zur Einwilligung für die App besteht aus einem oder mehreren „includes“-Bedingungssätzen und null oder mehreren „excludes“-Bedingungssätzen. Damit ein Ereignis in einer Richtlinie zur Einwilligung für die App berücksichtigt wird, muss es *mindestens* einem „includes“-Bedingungssatz entsprechen und nicht *beliebigen* „excludes“-Bedingungssätze.

Jeder Bedingungssatz besteht aus mehreren Bedingungen. Damit ein Ereignis einem Bedingungssatz entspricht, müssen *alle* Bedingungen im Satz erfüllt werden.

Richtlinien zur Einwilligung für die App, bei denen die ID mit „microsoft-“ beginnt, sind integrierte Richtlinien. Einige dieser integrierten Richtlinien werden in vorhandenen integrierten Verzeichnisrollen verwendet. Beispielsweise werden in der Richtlinie `microsoft-application-admin` zur Einwilligung für die App die Bedingungen beschrieben, unter denen die Rollen „Anwendungsadministrator“ und „Cloudanwendungsadministrator“ eine mandantenweite Administratoreinwilligung erteilen dürfen. Integrierte Richtlinien können in benutzerdefinierten Verzeichnisrollen sowie zum Konfigurieren von Einstellungen für die Benutzereinwilligung verwendet, aber nicht bearbeitet oder gelöscht werden.

## <a name="pre-requisites"></a>Voraussetzungen

1. Stellen Sie sicher, dass Sie das Modul [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) verwenden. Dieser Schritt ist wichtig, wenn auf Ihrem Computer sowohl das Modul [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) als auch das Modul [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) installiert ist.

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Stellen Sie eine Verbindung mit Azure AD PowerShell her.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Auflisten vorhandener Richtlinien zur Einwilligung für die App

Es ist ratsam, sich mit den vorhandenen Richtlinien zur Einwilligung für die App in Ihrer Organisation vertraut zu machen:

1. Auflisten aller Richtlinien zur Einwilligung für die App:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Anzeigen der „includes“-Bedingungssätze einer Richtlinie:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Anzeigen der „excludes“-Bedingungssätze:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Erstellen einer benutzerdefinierten Richtlinie zur Einwilligung für die App

Führen Sie die folgenden Schritte aus, um eine benutzerdefinierte Richtlinie zur Einwilligung für die App zu erstellen:

1. Erstellen Sie eine neue leere Richtlinie zur Einwilligung für die App.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Fügen Sie „includes“-Bedingungssätze hinzu.

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Wiederholen Sie diesen Schritt, um weitere „includes“-Bedingungssätze hinzuzufügen.

1. Fügen Sie optional „excludes“-Bedingungssätze hinzu.

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   Wiederholen Sie diesen Schritt, um weitere „excludes“-Bedingungssätze hinzuzufügen.

Sobald die Richtlinie zur Einwilligung für die App erstellt wurde, können Sie eine [Benutzereinwilligung zulassen](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy), die dieser Richtlinie unterliegt.

## <a name="delete-a-custom-app-consent-policy"></a>Löschen einer benutzerdefinierten Richtlinie zur Einwilligung für die App

1. Im Folgenden wird gezeigt, wie Sie eine benutzerdefinierte Richtlinie zur Einwilligung für die App löschen können. **Diese Aktion kann nicht rückgängig gemacht werden.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> Gelöschte Richtlinien zur Einwilligung für die App können nicht wiederhergestellt werden. Wenn Sie eine benutzerdefinierte Richtlinie zur Einwilligung für die App versehentlich löschen, müssen Sie sie erneut erstellen.

---

### <a name="supported-conditions"></a>Unterstützte Bedingungen

Die folgende Tabelle enthält die Liste der unterstützten Bedingungen für Richtlinien zur Einwilligung für die App.

| Bedingung | Beschreibung|
|:---------------|:----------|
| PermissionClassification | Die [Berechtigungsklassifizierung](configure-permission-classifications.md) für die erteilte Berechtigung oder „all“, das mit jeder beliebigen Berechtigungsklassifizierung (einschließlich nicht klassifizierter Berechtigungen) abgeglichen werden soll. Der Standardwert ist „all“. |
| PermissionType | Der Berechtigungstyp der erteilten Berechtigung. Verwenden Sie „Anwendung“ für Anwendungsberechtigungen (z. B. App-Rollen) oder „delegiert“ für delegierte Berechtigungen. <br><br>**Hinweis**: Der Wert „delegatedUserConsentable“ gibt delegierte Berechtigungen an, die vom API-Herausgeber nicht zum Anfordern der Administratoreinwilligung konfiguriert wurden. Dieser Wert kann in integrierten Richtlinien für Berechtigungserteilung verwendet werden, aber nicht in benutzerdefinierten Richtlinien für Berechtigungserteilung. Erforderlich. |
| ResourceApplication | Die **AppId** der Ressourcenanwendung (z. B. die API), für die eine Berechtigung erteilt wird, oder „any“, die mit einer beliebigen Ressourcenanwendung oder API abgeglichen werden soll. Der Standardwert ist „any“. |
| Berechtigungen | Die Liste der Berechtigungs-IDs für die spezifischen Berechtigungen, mit denen abgeglichen werden soll, oder eine Liste mit dem einzelnen Wert „all“ für den Abgleich mit jeder beliebigen Berechtigung. Standard ist der einzelne Wert „all“. <ul><li>Delegierte Berechtigungs-IDs finden Sie in der Eigenschaft **OAuth2Permissions** des ServicePrincipal-Objekts der API.</li><li>Anwendungsberechtigungs-IDs finden Sie in der Eigenschaft **AppRoles** des ServicePrincipal-Objekts der API.</li></ol> |
| ClientApplicationIds | Eine Liste von **AppId**-Werten für die Clientanwendungen, mit denen abgeglichen werden soll, oder eine Liste mit dem einzelnen Wert „all“ zum Abgleichen einer beliebigen Clientanwendung. Standard ist der einzelne Wert „all“. |
| ClientApplicationTenantIds | Eine Liste der Azure Active Directory-Mandanten-IDs, in denen die Clientanwendung registriert ist, oder eine Liste mit dem einzelnen Wert „all“, die mit den in einem beliebigen Mandanten registrierten Client-Apps abgeglichen werden soll. Standard ist der einzelne Wert „all“. |
| ClientApplicationPublisherIds | Eine Liste von Microsoft Partner Network (MPN)-IDs für [verifizierte Herausgeber](../develop/publisher-verification-overview.md) der Clientanwendung, oder eine Liste mit dem einzelnen Wert „all“, die mit Client-Apps von jedem beliebigen Herausgeber abgeglichen werden soll. Standard ist der einzelne Wert „all“. |
| ClientApplicationsFromVerifiedPublisherOnly | Legen Sie diesen Wert auf `$true` fest, damit nur Clientanwendungen mit einem [verifizierten Verleger](../develop/publisher-verification-overview.md) abgeglichen werden. Legen Sie diesen Wert auf `$false` fest, damit jede beliebige Client-App sogar dann abgeglichen wird, wenn es dafür keinen verifizierten Herausgeber gibt. Der Standardwert ist `$false`. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

* [Konfigurieren von Einstellungen für die Benutzereinwilligung](configure-user-consent.md)
* [Konfigurieren des Workflows für die Administratoreinwilligung (Vorschau)](configure-admin-consent-workflow.md)
* Weitere Informationen zum [Verwalten der Einwilligung zu Anwendungen und Auswerten von Einwilligungsanforderungen](manage-consent-requests.md)
* [Erteilen einer mandantenweiten Administratoreinwilligung für eine Anwendung](grant-admin-consent.md)
* [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](../develop/v2-permissions-and-consent.md)

So erhalten Sie Hilfe oder finden Antworten auf Ihre Fragen:
* [Azure AD bei Microsoft Q&A](https://docs.microsoft.com/answers/products/)