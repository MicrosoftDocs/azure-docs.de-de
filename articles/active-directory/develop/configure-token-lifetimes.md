---
title: Festlegen der Lebensdauer von Token
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie die Gültigkeitsdauer für Token festlegen, die von Microsoft Identity Platform ausgestellt werden. Erfahren Sie, wie Sie die Standardrichtlinie einer Organisation verwalten, eine Richtlinie für die Webanmeldung erstellen, eine Richtlinie für eine native App erstellen, die eine Web-API aufruft, und eine erweiterte Richtlinie verwalten.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 2815041f32ebd7c2dae235229d1ca19aad253f7d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503620"
---
# <a name="configure-token-lifetime-policies-preview"></a>Konfigurieren von Richtlinien zur Tokenlebensdauer (Vorschau)
In Azure AD sind viele Szenarien möglich, was das Erstellen und Verwalten von Tokengültigkeitsdauern für Apps, Dienstprinzipale und Ihre gesamte Organisation betrifft.  

> [!IMPORTANT]
> Nach dem 30. Januar 2021 können Mandanten die Lebensdauer von Aktualisierungs- und Sitzungstoken nicht mehr konfigurieren, und Azure AD berücksichtigt vorhandene Konfigurationen von Aktualisierungs- und Sitzungstoken in Richtlinien nach diesem Datum nicht mehr. Die Gültigkeitsdauer von Zugriffstoken kann jedoch auch nach der Einstellung weiterhin konfiguriert werden.  Weitere Informationen finden Sie unter [Konfigurierbare Tokengültigkeitsdauer in Microsoft Identity Platform](active-directory-configurable-token-lifetimes.md).
> Wir haben [Funktionen für das Verwalten von Authentifizierungssitzungen](../conditional-access/howto-conditional-access-session-lifetime.md) für den bedingten Zugriff in Azure AD implementiert. Mithilfe dieses neuen Features können Sie die Lebensdauer von Aktualisierungstoken durch Festlegen der Anmeldehäufigkeit konfigurieren.


In diesem Abschnitt werden einige allgemeine Richtlinienszenarien beschrieben, die es ermöglichen, neue Regeln für folgende Festlegungen vorzugeben:

* Lebensdauer von Token
* Max. Inaktivitätszeit von Token
* Max. Alter von Token

Anhand der Beispiele wird Folgendes veranschaulicht:

* Verwalten der Standardrichtlinie einer Organisation
* Erstellen einer Richtlinie für die Webanmeldung
* Erstellen einer Richtlinie für eine native App, die eine Web-API aufruft
* Verwalten einer erweiterten Richtlinie

## <a name="prerequisites"></a>Voraussetzungen
In den folgenden Beispielen wird gezeigt, wie Sie Richtlinien für Apps, Dienstprinzipale und Ihre gesamte Organisation erstellen, aktualisieren, verknüpfen und löschen. Wenn Azure AD neu für Sie ist, ist es ratsam, sich über das [Erhalten eines Azure AD-Mandanten](quickstart-create-new-tenant.md) zu informieren, bevor Sie mit diesen Beispielen fortfahren.  

Führen Sie die folgenden Schritte aus, um zu beginnen:

1. Laden Sie die aktuelle [öffentliche Vorschauversion des Azure AD PowerShell-Moduls](https://www.powershellgallery.com/packages/AzureADPreview) herunter.
2. Führen Sie den Befehl `Connect` aus, um sich an Ihrem Azure AD-Administratorkonto anzumelden. Führen Sie diesen Befehl bei jedem Start einer neuen Sitzung aus.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Führen Sie den folgenden Befehl aus, um alle Richtlinien anzuzeigen, die in Ihrer Organisation erstellt wurden. Führen Sie diesen Befehl nach den meisten Vorgängen in den folgenden Szenarien aus. Wenn Sie den Befehl ausführen, können Sie auch die ** ** Ihrer Richtlinien erhalten.

    ```powershell
    Get-AzureADPolicy
    ```

## <a name="manage-an-organizations-default-policy"></a>Verwalten der Standardrichtlinie einer Organisation
Anhand dieses Beispiels können Sie eine Richtlinie erstellen, die es den Benutzern ermöglicht, sich in Ihrer gesamten Organisation weniger häufig anmelden zu müssen. Erstellen Sie hierzu eine Richtlinie für die Lebensdauer von Single-Factor-Aktualisierungstoken, die auf Ihre gesamte Organisation angewandt wird. Die Richtlinie wird auf jede Anwendung in Ihrer Organisation und jeden Dienstprinzipal angewendet, für die bzw. den noch keine Richtlinie festgelegt wurde.

1. Erstellen Sie eine Tokengültigkeitsdauer-Richtlinie.

    1. Legen Sie das Single-Factor-Aktualisierungstoken auf „Bis zum Widerruf“ fest. Das Token läuft erst ab, nachdem der Zugriff widerrufen wurde. Erstellen Sie die folgende Richtliniendefinition:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Um die Richtlinie zu erstellen, führen Sie das Cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) aus:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Um Leerzeichen zu entfernen, führen Sie das Cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) aus:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Führen Sie den folgenden Befehl aus, um Ihre neue Richtlinie anzuzeigen und deren **ObjectId** abzurufen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Aktualisieren Sie die Richtlinie.

    Unter Umständen möchten Sie erreichen, dass die erste Richtlinie, die Sie in diesem Beispiel festlegen, nicht so streng ist, wie es für den Dienst eigentlich erforderlich ist. Führen Sie den folgenden Befehl aus, um für Ihr Single-Factor-Aktualisierungstoken festzulegen, dass es nach zwei Tagen abläuft:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

## <a name="create-a-policy-for-web-sign-in"></a>Erstellen einer Richtlinie für die Webanmeldung

In diesem Beispiel erstellen Sie eine Richtlinie, bei der es erforderlich ist, dass sich Benutzer häufiger für Ihre Web-App authentifizieren. Mit dieser Richtlinie wird die Gültigkeitsdauer der Zugriffs-/ID-Token und das maximale Alter eines Multi-Factor-Sitzungstokens auf den Dienstprinzipal Ihrer Web-App festgelegt.

1. Erstellen Sie eine Tokengültigkeitsdauer-Richtlinie.

    Mit dieser Richtlinie für die Webanmeldung werden die Gültigkeitsdauer des Zugriffs-/ID-Tokens und das maximale Alter des Single-Factor-Sitzungstokens auf zwei Stunden festgelegt.

    1. Um die Richtlinie zu erstellen, führen Sie das Cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) aus:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Führen Sie das Cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) aus, um Ihre neue Richtlinie anzuzeigen und deren **ObjectId** abzurufen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Weisen Sie die Richtlinie Ihrem Dienstprinzipal zu. Rufen Sie außerdem die **ObjectId** Ihres Dienstprinzipals ab.

    1. Verwenden Sie das Cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal), um alle Dienstprinzipale Ihrer Organisation oder einen einzelnen Dienstprinzipal anzuzeigen.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Wenn Sie über den Dienstprinzipal verfügen, führen Sie das Cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) aus:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Erstellen einer Richtlinie für eine native App, die eine Web-API aufruft
In diesem Beispiel erstellen Sie eine Richtlinie, bei der sich Benutzer weniger häufig authentifizieren müssen. Mit der Richtlinie wird außerdem der Zeitraum verlängert, über den ein Benutzer inaktiv sein kann, bevor er sich erneut authentifizieren muss. Die Richtlinie wird auf die Web-API angewendet. Wenn die native App die Web-API als Ressource anfordert, wird diese Richtlinie angewendet.

1. Erstellen Sie eine Tokengültigkeitsdauer-Richtlinie.

    1. Führen Sie das Cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) aus, um eine strenge Richtlinie für eine Web-API zu erstellen:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Führen Sie den folgenden Befehl aus, um Ihre neue Richtlinie abzurufen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Weisen Sie die Richtlinie Ihrer Web-API zu. Außerdem müssen Sie die **ObjectId** Ihrer Anwendung abrufen. Verwenden Sie das Cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) oder das [Azure-Portal](https://portal.azure.com/), um die **ObjectId** Ihrer App zu finden.

    Rufen Sie wie im folgenden Codebeispiel die **ObjectId** Ihrer App ab, und weisen Sie die Richtlinie zu:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

## <a name="manage-an-advanced-policy"></a>Verwalten einer erweiterten Richtlinie
Anhand dieses Beispiels können Sie einige Richtlinien erstellen, um zu erfahren, wie das Prioritätssystem funktioniert. Außerdem erfahren Sie, wie Sie mehrere Richtlinien verwalten, die auf verschiedene Objekte angewendet werden.

1. Erstellen Sie eine Tokengültigkeitsdauer-Richtlinie.

    1. Führen Sie das Cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) aus, um eine Standardrichtlinie für die Organisation zu erstellen, mit der die Lebensdauer des Single-Factor-Aktualisierungstokens auf 30 Tage festgelegt wird:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Um Ihre neue Richtlinie anzuzeigen, führen Sie das Cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) aus:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Weisen Sie die Richtlinie einem Dienstprinzipal zu.

    Sie verfügen jetzt über eine Richtlinie, die für die gesamte Organisation gilt. Es kann beispielsweise sein, dass diese 30-Tage-Richtlinie für einen bestimmten Dienstprinzipal beibehalten werden soll, während die Organisationsstandardrichtlinie so geändert wird, dass sie als Obergrenze für „Bis zum Widerruf“ fungiert.

    1. Verwenden Sie das Cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal), um alle Dienstprinzipale Ihrer Organisation abzurufen.

    1. Wenn Sie über den Dienstprinzipal verfügen, führen Sie das Cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) aus:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Legen Sie das Flag `IsOrganizationDefault` auf „false“ fest:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Erstellen Sie eine neue Organisationsstandardrichtlinie:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Sie haben jetzt die ursprüngliche Richtlinie mit Ihrem Dienstprinzipal verknüpft und die neue Richtlinie als Organisationsstandardrichtlinie festgelegt. Beachten Sie, dass Richtlinien für Dienstprinzipale Priorität vor Organisationsstandardrichtlinien haben.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie etwas über [Funktionen für das Verwalten von Authentifizierungssitzungen](../conditional-access/howto-conditional-access-session-lifetime.md) des bedingten Zugriffs in Azure AD.