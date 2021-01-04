---
title: Erstellen einer Ressource für den plastischen Reader
titleSuffix: Azure Cognitive Services
description: In diesem Artikel wird beschrieben, wie Sie eine neue Plastischer Reader-Ressource mit einer benutzerdefinierten Unterdomäne erstellen und dann Azure AD in Ihrem Azure-Mandanten konfigurieren.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: b012da0b2aea4a50002e9adbc0876396ddd4b5e7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368728"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Erstellen einer Plastischer Reader-Ressource und Konfigurieren der Azure Active Directory-Authentifizierung

In diesem Artikel wird ein Skript bereitgestellt, mit dem eine Plastischer Reader-Ressource erstellt und die Azure AD-Authentifizierung (Azure Active Directory) konfiguriert wird. Jedes Mal, wenn eine Plastischer Reader-Ressource mit diesem Skript oder im Portal erstellt wird, muss sie unabhängig davon auch mit Azure AD-Berechtigungen konfiguriert werden. Dieses Skript unterstützt Sie dabei.

Das Skript ist darauf ausgelegt, alle notwendigen Plastischer Reader- und Azure AD-Ressourcen für Sie in einem Schritt zu erstellen und zu konfigurieren. Sie können jedoch auch nur die Azure AD-Authentifizierung für eine vorhandene Plastischer Reader-Ressource konfigurieren, wenn Sie beispielsweise bereits eine im Azure-Portal erstellt haben.

Einige Kunden müssen möglicherweise mehrere Plastischer Reader-Ressourcen für Entwicklung und Produktion erstellen, oder vielleicht für mehrere verschiedene Regionen, in denen ihr Dienst bereitgestellt wird. In diesen Fällen können Sie das Skript mehrmals verwenden, um unterschiedliche Plastischer Reader-Ressourcen zu erstellen und sie mit den Azure AD-Berechtigungen zu konfigurieren.

Das Skript ist flexibel ausgelegt. Zuerst wird nach vorhandenen Plastischer Reader- und Azure AD-Ressourcen in Ihrem Abonnement gesucht, und sie werden nur nach Bedarf erstellt, wenn sie nicht bereits vorhanden sind. Wenn Sie zum ersten Mal eine Plastischer Reader-Ressource erstellen, führt das Skript alles aus, was Sie benötigen. Wenn Sie es nur zum Konfigurieren von Azure AD für eine vorhandene Plastischer Reader-Ressource verwenden möchten, die im Portal erstellt wurde, wird dies ebenfalls durchgeführt. Es kann auch verwendet werden, um mehrere Plastischer Reader-Ressourcen zu erstellen und zu konfigurieren.

## <a name="set-up-powershell-environment"></a>Einrichten der PowerShell-Umgebung

1. Öffnen Sie als Erstes die [Azure Cloud Shell](../../cloud-shell/overview.md). Stellen Sie in der Dropdownliste oben links oder durch Eingabe von `pwsh` sicher, dass Cloud Shell auf PowerShell festgelegt ist.

1. Kopieren Sie den folgenden Codeausschnitt, und fügen Sie ihn in die Shell ein.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecretExpiration
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --end-date "$AADAppClientSecretExpiration" --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully."
            Write-Host "NOTE: To manage your Active Directory app client secrets after this Immersive Reader Resource has been created please visit https://portal.azure.com and go to Home -> Azure Active Directory -> App Registrations -> $AADAppDisplayName -> Certificates and Secrets blade -> Client Secrets section" -ForegroundColor Yellow
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Führen Sie die Funktion `Create-ImmersiveReaderResource` aus, und stellen Sie die Parameter entsprechend bereit.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
      -AADAppClientSecretExpiration '<AAD_APP_CLIENT_SECRET_EXPIRATION>'
    ```

    | Parameter | Kommentare |
    | --- | --- |
    | SubscriptionName |Der Name des Azure-Abonnements, das für Ihre Plastischer Reader-Ressource verwendet werden soll. Sie müssen über ein Abonnement verfügen, um eine Ressource zu erstellen. |
    | Ressourcenname |  Muss alphanumerisch sein und darf „-“ enthalten, solange „-“ nicht das erste oder letzte Zeichen ist. Die Länge darf 63 Zeichen nicht überschreiten.|
    | ResourceSubdomain |Eine benutzerdefinierte Unterdomäne ist für Ihre Plastischer Reader-Ressource erforderlich. Die Unterdomäne wird vom SDK verwendet, wenn der Plastischer Reader-Dienst aufgerufen wird, um den Reader zu starten. Die Unterdomäne muss global eindeutig sein. Die Unterdomäne muss alphanumerisch sein und darf „-“ enthalten, solange „-“ nicht das erste oder letzte Zeichen ist. Die Länge darf 63 Zeichen nicht überschreiten. Dieser Parameter ist optional, wenn die Ressource bereits vorhanden ist. |
    | ResourceSKU |Optionen: `S0`. Besuchen Sie unsere Seite [Preise für Cognitive Services – Plastischer Reader](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/), um mehr über jede verfügbare SKU zu erfahren. Dieser Parameter ist optional, wenn die Ressource bereits vorhanden ist. |
    | ResourceLocation |Optionen: `eastus`, `eastus2`, `southcentralus`, `westus`, `westus2`, `australiaeast`, `southeastasia`, `centralindia`, `japaneast`, `northeurope`, `uksouth`, `westeurope`. Dieser Parameter ist optional, wenn die Ressource bereits vorhanden ist. |
    | ResourceGroupName |Ressourcen werden in Ressourcengruppen in Abonnements erstellt. Geben Sie den Namen einer vorhandenen Ressourcengruppe an. Wenn die Ressourcengruppe nicht bereits vorhanden ist, wird eine neue mit diesem Namen erstellt. |
    | ResourceGroupLocation |Wenn die Ressourcengruppe nicht vorhanden ist, müssen Sie einen Speicherort angeben, an dem die Gruppe erstellt werden soll. Verwenden Sie `az account list-locations` zum Abrufen einer Speicherortliste. Verwenden Sie die Eigenschaft *Name* (ohne Leerzeichen) des zurückgegebenen Ergebnisses. Dieser Parameter ist optional, wenn die Ressource bereits vorhanden ist. |
    | AADAppDisplayName |Der Anzeigename der Azure Active Directory-Anwendung. Wenn eine vorhandene Azure AD-Anwendung nicht gefunden wird, wird eine neue mit diesem Namen erstellt. Dieser Parameter ist optional, wenn die Azure AD-Anwendung bereits vorhanden ist. |
    | AADAppIdentifierUri |Der URI für die Azure AD-App. Wenn eine vorhandene Azure AD-App nicht gefunden wird, wird eine neue mit diesem URI erstellt. Beispiel: `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Ein von Ihnen erstelltes Kennwort, das später beim Abrufen eines Tokens zum Starten des plastischen Readers zum Authentifizieren verwendet wird. Das Kennwort muss mindestens 16 Zeichen lang sein, mindestens 1 Sonderzeichen sowie mindestens 1 numerisches Zeichen enthalten. Um geheime Clientschlüssel von Azure AD-Anwendungen zu verwalten, nachdem Sie diese Ressource erstellt haben, besuchen Sie https://portal.azure.com, und navigieren Sie zu „Home“ -> „Azure Active Directory“ -> „App-Registrierungen“ -> `[AADAppDisplayName]` -> Blatt „Zertifikate und Geheimnisse“ -> Abschnitt „Geheime Clientschlüssel“ (siehe Screenshot „Verwalten Ihrer Azure AD-Anwendungsgeheimnisse“ weiter unten). |
    | AADAppClientSecretExpiration |Datum oder Datetime, wonach Ihr `[AADAppClientSecret]` abläuft (z. B. „2020-12-31T11:59:59+00:00“ oder „2020-12-31“). |

    Verwalten Ihrer Azure AD-Anwendungsgeheimnisse

    ![Azure-Portal-Zertifikate und Blatt „Geheimnisse“](./media/client-secrets-blade.png)

1. Kopieren Sie die JSON-Ausgabe zur späteren Verwendung in eine Textdatei. Die Ausgabe sollte wie im folgenden Beispiel aussehen.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich die [Node.js-Schnellstartanleitung](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) an, um zu erfahren, welche weiteren Möglichkeiten das SDK für den plastischen Reader in Verbindung mit Node.js bietet.
* Sehen Sie sich das [Android-Tutorial](./tutorial-android.md) an, um zu erfahren, welche weiteren Möglichkeiten das SDK für den plastischen Reader in Verbindung mit Java oder Kotlin für Android bietet.
* Sehen Sie sich das [iOS-Tutorial](./tutorial-ios.md) an, um zu erfahren, welche weiteren Möglichkeiten das SDK für den plastischen Reader in Verbindung mit Swift für iOS bietet.
* Sehen Sie sich das [Python-Tutorial](./tutorial-python.md) an, um zu erfahren, welche weiteren Möglichkeiten das SDK für den plastischen Reader in Verbindung mit Python bietet.
* Machen Sie sich mit dem [SDK für Plastischer Reader](https://github.com/microsoft/immersive-reader-sdk) und der [zugehörigen Referenz](./reference.md) vertraut.