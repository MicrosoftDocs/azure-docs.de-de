---
title: Authentifizierungsstrategien für den Marketplace-Messungsdienst | Azure Marketplace
description: Hier finden Sie Informationen zu den im Azure Marketplace unterstützten Authentifizierungsstrategien für den Messungsdienst.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/21/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 5ddcd7e5c3e5651062f88b261e7003fb0dd2ef3b
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435993"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Authentifizierungsstrategien für den Marketplace-Messungsdienst

Der Marketplace-Messungsdienst unterstützt zwei Authentifizierungsstrategien:

* [Azure AD-Sicherheitstoken](../../active-directory/develop/access-tokens.md)
* [Verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) 

Nachfolgend wird erläutert, wann und wie die verschiedenen Authentifizierungsstrategien verwendet werden, um benutzerdefinierte Verbrauchseinheiten mithilfe des Marketplace-Messungsdiensts sicher zu übermitteln.

## <a name="using-the-azure-ad-security-token"></a>Verwenden des Azure AD-Sicherheitstokens

Die anwendbaren Angebotstypen sind transaktionsfähige SaaS und Azure-Anwendungen mit dem Plantyp für verwaltete Anwendungen.  

Übermitteln Sie benutzerdefinierte Verbrauchseinheiten mithilfe einer vordefinierten festen Azure AD-Anwendungs-ID für die Authentifizierung.

Für SaaS-Angebote ist dies die einzige verfügbare Option. Dieser Schritt ist zum Veröffentlichen von SaaS-Angeboten obligatorisch, wie es unter [Registrieren einer SaaS-Anwendung](./pc-saas-registration.md) beschrieben ist.

Für Azure-Anwendungen mit einem Plan für verwaltete Anwendungen sollte die Verwendung dieser Strategie in folgenden Fällen in Betracht gezogen werden:

* Sie verfügen bereits über einen Mechanismus zum Kommunizieren mit Ihren Back-End-Diensten und möchten diesen Mechanismus erweitern, um benutzerdefinierte Verbrauchseinheiten von einem zentralen Dienst auszugeben.
* Sie verfügen über eine komplexe Logik für benutzerdefinierte Verbrauchseinheiten.  Führen Sie diese Logik an einem zentralen Ort anstelle der verwalteten Anwendungsressourcen aus.

Nach der Registrierung der Anwendung können Sie programmgesteuert ein Azure AD-Sicherheitstoken anfordern. Es wird erwartet, dass der Herausgeber dieses Token verwendet und eine Anforderung zu dessen Auflösung stellt.

Weitere Informationen zu diesen Token finden Sie unter [Azure Active Directory-Zugriffstoken](../../active-directory/develop/access-tokens.md).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Abrufen eines Tokens basierend auf der Azure AD-App

#### <a name="http-method"></a>HTTP-Methode

**POST**

#### <a name="request-url"></a>*Anforderungs-URL*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI-Parameter*

|  **Parametername** |  **Erforderlich**  |  **Beschreibung**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | Mandanten-ID der registrierten Azure AD-Anwendung.   |
| | | |

#### <a name="request-header"></a>*Anforderungsheader*

|  **Headername**    |  **Erforderlich**  |  **Beschreibung**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | Der Anforderung zugeordneter Inhaltstyp. Standardwert: `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Anforderungstext*

|  **Eigenschaftenname**  |  **Erforderlich**  |  **Beschreibung**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | Gewährungstyp. Verwenden Sie `client_credentials`. |
|  `Client_id`        |   True         | Der Azure AD-App zugeordneter Client-/App-Bezeichner.|
|  `client_secret`    |   True         | Der Azure AD-App zugeordnetes Geheimnis.  |
|  `Resource`         |   True         | Zielressource, für die das Token angefordert wird. Verwenden Sie `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`. |
| | | |

#### <a name="response"></a>*Antwort*

|  **Name**    |  **Typ**  |  **Beschreibung**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | Anforderung erfolgreich.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Beispiel für Antworttoken:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Verwenden des Tokens für von Azure verwaltete Identitäten

Der anwendbare Angebotstyp sind Azure-Anwendungen mit dem Plantyp für verwaltete Anwendungen.

Bei dieser Vorgehensweise kann die Identität der bereitgestellten Ressourcen authentifiziert werden, um Nutzungsereignisse für benutzerdefinierte Verbrauchseinheiten zu senden.  Sie können den Code, der die Nutzung ausgibt, innerhalb der Grenzen Ihrer Bereitstellung einbetten.

>[!Note]
>Der Herausgeber sollte sicherstellen, dass die Ressourcen, die Nutzungsdaten ausgeben, gesperrt sind, damit er nicht manipuliert wird.

Die verwaltete Anwendung kann unterschiedliche Arten von Ressourcen enthalten, von Virtual Machines bis Azure Functions.  Weitere Informationen zum Authentifizieren mit verwalteten Identitäten für verschiedene Dienste finden Sie unter [Verwenden von verwalteten Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md#how-can-i-use-managed-identities-for-azure-resources).

Führen Sie beispielsweise die folgenden Schritte aus, um eine Authentifizierung mithilfe einer Windows-VM vorzunehmen.

1. Stellen Sie sicher, dass die verwaltete Identität anhand einer der folgenden Methoden konfiguriert ist:
    * [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und dem Azure-Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
    * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
    * [PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
    * [Azure Resource Manager-Vorlage](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
    * [REST](../../active-directory/managed-identities-azure-resources/qs-configure-rest-vm.md#system-assigned-managed-identity)
    * [Azure SDKs](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

1. Rufen Sie ein Zugriffstoken für die Anwendungs-ID des Marketplace-Messungsdiensts (`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`) mithilfe der Systemidentität ab, stellen Sie eine RDP-Verbindung mit der VM her, öffnen Sie die PowerShell-Konsole und führen Sie den folgenden Befehl aus.

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Rufen Sie die ID der verwalteten App aus der Eigenschaft „ManagedBy“ der aktuellen Ressourcengruppen ab.

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Der Marketplace-Messungsdienst erfordert, dass die Nutzung für eine `resourceID` und bei einer verwalteten Anwendung für `resourceUsageId` gemeldet wird.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Verwenden Sie die [Marketplace-Messungsdienst-API](./marketplace-metering-service-apis.md) zum Ausgeben der Nutzung.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Azure-Anwendungsangebots](../create-new-azure-apps-offer.md)
* [Planen eines SaaS-Angebots](../plan-saas-offer.md)
