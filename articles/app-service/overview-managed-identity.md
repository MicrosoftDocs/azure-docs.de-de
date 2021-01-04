---
title: Verwaltete Identitäten
description: Erfahren Sie, wie verwaltete Identitäten in Azure App Service und Azure Functions funktionieren, wie Sie eine verwaltete Identität konfigurieren und ein Token für eine Back-End-Ressource erstellen.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: c734c0ceb9c4d5418edc51a2c3ad3c052637ad31
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696981"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Verwenden verwalteter Identitäten für App Service und Azure Functions

In diesem Thema erfahren Sie, wie eine verwaltete Identität für App Service- und Azure Functions-Anwendungen erstellt und für den Zugriff auf andere Ressourcen verwendet wird. 

> [!Important] 
> Verwaltete Identitäten für App Service und Azure Functions verhalten sich nicht wie erwartet, wenn Ihre App abonnement- bzw. mandantenübergreifend migriert wird. Die App muss eine neue Identität abrufen. Zu diesem Zweck deaktivieren Sie die Funktion und aktivieren Sie anschließend erneut. Weitere Informationen finden Sie im Abschnitt [Entfernen einer Identität](#remove) weiter unten. Für nachgeschaltete Ressourcen müssen außerdem die Zugriffsrichtlinien für die Verwendung der neuen Identität aktualisiert werden.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Hinzufügen einer systemseitig zugewiesenen Identität

Für die Erstellung einer App mit einer systemseitig zugewiesenen Identität muss eine zusätzliche Eigenschaft für die Anwendung festgelegt werden.

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Um eine verwaltete Entität im Portal einzurichten, erstellen Sie wie gewohnt zuerst eine Anwendung und aktivieren dann das Feature.

1. Erstellen Sie wie gewohnt eine App im Portal. Navigieren Sie im Portal zu dieser App.

2. Wenn Sie eine Funktionen-App verwenden, navigieren Sie zu **Plattformfeatures**. Scrollen Sie bei anderen App-Typen in der linken Navigationsleiste nach unten zur Gruppe **Einstellungen**.

3. Wählen Sie **Identität** aus.

4. Ändern Sie auf der Registerkarte **Systemseitig zugewiesen** den **Status** in **Ein**. Klicken Sie auf **Speichern**.

    ![Screenshot, der zeigt, wo Sie den Status in „Ein“ ändern und „Speichern“ auswählen](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Um die verwaltete Identität für Ihre Web-App oder Slot-App im Azure-Portal zu finden, sehen Sie unter **Unternehmensanwendungen** im Abschnitt **Benutzereinstellungen** nach. In der Regel lautet der Slotname ähnlich wie `<app name>/slots/<slot name>`.


### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Um mithilfe der Azure CLI eine verwaltete Entität einzurichten, müssen Sie für eine vorhandene Anwendung den Befehl `az webapp identity assign` ausführen. Für die Ausführung der Beispiele in diesem Abschnitt gibt es drei Optionen:

- Verwenden Sie [Azure Cloud Shell](../cloud-shell/overview.md) über das Azure-Portal.
- Verwenden Sie die eingebettete Azure Cloud Shell über die Schaltfläche „Ausprobieren“ in der rechten oberen Ecke der unten aufgeführten Codeblöcke.
- [Installieren Sie die neueste Version der Azure CLI](/cli/azure/install-azure-cli) (2.0.31 oder höher), wenn Sie lieber eine lokale CLI-Konsole verwenden möchten. 

In den folgenden Schritten werden Sie durch das Erstellen einer Web-App und das Zuweisen einer Identität zur App mithilfe der CLI geleitet:

1. Melden Sie sich bei Verwendung der Azure CLI in einer lokalen Konsole zunächst mit [az login](/cli/azure/reference-index#az-login) bei Azure an. Verwenden Sie ein Konto, das dem Azure-Abonnement zugeordnet ist, unter dem Sie die Anwendung bereitstellen möchten:

    ```azurecli-interactive
    az login
    ```

2. Erstellen Sie eine Webanwendung mithilfe der CLI. Weitere Beispiele zum Verwenden der CLI mit App Service finden Sie unter [Azure CLI-Beispiele](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Führen Sie den `identity assign` Befehl aus, um die Identität für diese Anwendung zu erstellen:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In den folgenden Schritten werden Sie durch das Erstellen einer App und das Zuweisen einer Identität zur App mithilfe von Azure PowerShell geleitet. Die Anweisungen zum Erstellen einer Web-App und einer Funktions-App sind unterschiedlich.

#### <a name="using-azure-powershell-for-a-web-app"></a>Verwenden von Azure PowerShell für eine Web-App

1. Installieren Sie bei Bedarf Azure PowerShell mithilfe der Anleitungen in der [Azure PowerShell-Dokumentation](/powershell/azure/), und führen Sie dann `Login-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

2. Erstellen Sie eine Webanwendung mithilfe von Azure PowerShell. Weitere Beispiele zum Verwenden von Azure PowerShell mit App Service finden Sie unter [Azure PowerShell-Beispiele](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Führen Sie den `Set-AzWebApp -AssignIdentity` Befehl aus, um die Identität für diese Anwendung zu erstellen:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>Verwenden von Azure PowerShell für eine Funktions-App

1. Installieren Sie bei Bedarf Azure PowerShell mithilfe der Anleitungen in der [Azure PowerShell-Dokumentation](/powershell/azure/), und führen Sie dann `Login-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

2. Erstellen Sie eine Funktions-App mit Azure PowerShell. Weitere Beispiele zur Verwendung von Azure PowerShell mit Azure Functions finden Sie in der [Az-Funktionenreferenz](/powershell/module/az.functions/?view=azps-4.1.0#functions):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

Sie können eine vorhandene Funktions-App stattdessen auch mit `Update-AzFunctionApp` aktualisieren.

### <a name="using-an-azure-resource-manager-template"></a>Verwenden einer Azure Resource Manager-Vorlage

Mithilfe einer Azure Resource Manager-Vorlage kann die Bereitstellung Ihrer Azure-Ressourcen automatisiert werden. Weitere Informationen zum Bereitstellen von App Service und Azure Functions finden Sie unter [Automatisieren der Ressourcenbereitstellung in App Service](../app-service/deploy-complex-application-predictably.md) und [Automatisieren der Ressourcenbereitstellung in Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Ressourcen vom Typ `Microsoft.Web/sites` können mit einer Identität erstellt werden, indem die folgende Eigenschaft in der Ressourcendefinition eingeschlossen wird:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Eine Anwendung kann gleichzeitig sowohl über systemseitig als auch über benutzerseitig zugewiesene Identitäten verfügen. In diesem Fall erhält die `type`-Eigenschaft den Wert `SystemAssigned,UserAssigned`.

Durch das Hinzufügen des systemseitig zugewiesenen Typs wird Azure angewiesen, die Identität für Ihre Anwendung zu erstellen und zu verwalten.

Eine Web-App kann beispielsweise wie folgt aussehen:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Wenn die Website erstellt wurde, weist sie folgende zusätzliche Eigenschaften auf:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Die tenantId-Eigenschaft gibt an, zu welchem Azure AD-Mandanten die Identität gehört. Die Eigenschaft „principalId“ ist ein eindeutiger Bezeichner für die neue Identität der Anwendung. In Azure AD weist der Dienstprinzipal den gleichen Namen auf, den Sie für Ihre App Service- oder Azure Functions-Instanz vergeben haben.

Wenn Sie in einer späteren Phase in der Vorlage auf diese Eigenschaften verweisen müssen, können Sie dies über die [`reference()`-Vorlagenfunktion](../azure-resource-manager/templates/template-functions-resource.md#reference) mit dem Flag `'Full'` erledigen, wie in diesem Beispiel gezeigt:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Hinzufügen einer benutzerseitig zugewiesenen Identität

Für das Erstellen einer App mit einer benutzerseitig zugewiesenen Identität müssen Sie die Identität erstellen und dann den Ressourcenbezeichner der Identität zu Ihrer App-Konfiguration hinzufügen.

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Zunächst müssen Sie eine Ressource für eine benutzerseitig zugewiesene Identität erstellen.

1. Folgen Sie zum Erstellen einer benutzerseitig verwalteten Identitätsressource [diesen Anweisungen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Erstellen Sie wie gewohnt eine App im Portal. Navigieren Sie im Portal zu dieser App.

3. Wenn Sie eine Funktionen-App verwenden, navigieren Sie zu **Plattformfeatures**. Scrollen Sie bei anderen App-Typen in der linken Navigationsleiste nach unten zur Gruppe **Einstellungen**.

4. Wählen Sie **Identität** aus.

5. Klicken Sie auf der Registerkarte **Benutzerseitig zugewiesen** auf **Hinzufügen**.

6. Suchen Sie nach der zuvor erstellten Identität, und wählen Sie sie aus. Klicken Sie auf **Hinzufügen**.

    ![Verwaltete Identität in App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In den folgenden Schritten werden Sie durch das Erstellen einer App und das Zuweisen einer Identität zur App mithilfe von Azure PowerShell geleitet.

> [!NOTE]
> Die aktuelle Version der Azure PowerShell-Cmdlets für Azure App Service unterstützt keine vom Benutzer zugewiesenen Identitäten. Die folgenden Anweisungen gelten für Azure Functions.

1. Installieren Sie bei Bedarf Azure PowerShell mithilfe der Anleitungen in der [Azure PowerShell-Dokumentation](/powershell/azure/), und führen Sie dann `Login-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

2. Erstellen Sie eine Funktions-App mit Azure PowerShell. Weitere Beispiele zur Verwendung von Azure PowerShell mit Azure Functions finden Sie in der [Az-Funktionenreferenz](/powershell/module/az.functions/?view=azps-4.1.0#functions). Im folgenden Skript wird auch `New-AzUserAssignedIdentity` verwendet, die gemäß [Erstellen, Auflisten oder Löschen einer vom Benutzer zugewiesenen verwalteten Identität mit Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) separat installiert werden muss.

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

Sie können eine vorhandene Funktions-App stattdessen auch mit `Update-AzFunctionApp` aktualisieren.

### <a name="using-an-azure-resource-manager-template"></a>Verwenden einer Azure Resource Manager-Vorlage

Mithilfe einer Azure Resource Manager-Vorlage kann die Bereitstellung Ihrer Azure-Ressourcen automatisiert werden. Weitere Informationen zum Bereitstellen von App Service und Azure Functions finden Sie unter [Automatisieren der Ressourcenbereitstellung in App Service](../app-service/deploy-complex-application-predictably.md) und [Automatisieren der Ressourcenbereitstellung in Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Ressourcen vom Typ `Microsoft.Web/sites` können mit einer Identität erstellt werden, indem den folgenden Block in die Ressourcendefinition einschließen. Ersetzen Sie hierbei `<RESOURCEID>` durch die Ressourcen-ID der gewünschten Identität:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Eine Anwendung kann gleichzeitig sowohl über systemseitig als auch über benutzerseitig zugewiesene Identitäten verfügen. In diesem Fall erhält die `type`-Eigenschaft den Wert `SystemAssigned,UserAssigned`.

Durch das Hinzufügen des benutzerseitig zugewiesenen Typs wird Azure angewiesen, die vom Benutzer zugewiesene Identität zu verwenden, die für Ihre Anwendung angegeben ist.

Eine Web-App kann beispielsweise wie folgt aussehen:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Wenn die Website erstellt wurde, weist sie folgende zusätzliche Eigenschaften auf:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

„principalId“ ist ein eindeutiger Bezeichner für die Identität, der bei der Azure AD-Verwaltung verwendet wird. „clientId“ ist ein eindeutiger Bezeichner für die neue Identität der Anwendung, der bei Runtimeaufrufen angibt, welche Identität verwendet werden soll.

## <a name="obtain-tokens-for-azure-resources"></a>Abrufen von Tokens für Azure-Ressourcen

Eine App kann mithilfe ihrer verwalteten Identität Token für den Zugriff auf andere durch Azure AD geschützte Ressourcen wie z. B. Azure Key Vault abrufen. Diese Tokens stellen die Anwendung dar, die auf die Ressource zugreift, nicht einen bestimmten Benutzer der Anwendung. 

Sie müssen die Zielressource möglicherweise für den Zugriff über die Anwendung konfigurieren. Wenn Sie beispielsweise ein Token für den Zugriff auf Key Vault anfordern, müssen Sie sicherstellen, dass Sie eine Zugriffsrichtlinie hinzugefügt haben, die die Identität Ihrer Anwendung enthält. Andernfalls werden Ihre Aufrufe von Key Vault abgelehnt, auch wenn diese das Token enthalten. Informationen zu den Ressourcen, die Azure Active Directory-Token unterstützen, finden Sie unter [Azure-Dienste, die die Azure AD-Authentifizierung unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> Die Back-End-Dienste für verwaltete Identitäten behalten pro Ressourcen-URI für ca. 8 Stunden einen Cache bei. Wenn Sie die Zugriffsrichtlinie einer bestimmten Zielressource aktualisieren und sofort ein Token für diese Ressource abrufen, erhalten Sie möglicherweise weiterhin ein zwischengespeichertes Token mit veralteten Berechtigungen, bis dieses Token abläuft. Zurzeit gibt es keine Möglichkeit, eine Tokenaktualisierung zu erzwingen.

Zum Abrufen eines Tokens in App Service und Azure Functions ist ein einfaches REST-Protokoll verfügbar. Dies kann für alle Anwendungen und Sprachen verwendet werden. Für .NET und Java bietet das Azure SDK eine Abstraktion über dieses Protokoll und bietet so die Möglichkeit zur lokalen Entwicklung.

### <a name="using-the-rest-protocol"></a>Verwenden des REST-Protokolls

> [!NOTE]
> Eine ältere Version dieses Protokolls, die die API-Version 2017-09-01 verwendet, verwendete den Header `secret` anstelle von `X-IDENTITY-HEADER` und akzeptierte nur die `clientid`-Eigenschaft für benutzerseitige Zuweisungen. Außerdem wurde `expires_on` in einem Zeitstempelformat zurückgegeben. MSI_ENDPOINT kann als Alias für IDENTITY_ENDPOINT verwendet werden, und MSI_SECRET kann als Alias für IDENTITY_HEADER verwendet werden. Diese Version des Protokolls ist derzeit für Hostingpläne des Typs Linux-Verbrauch erforderlich.

Für eine App mit einer verwalteten Identität sind zwei Umgebungsvariablen definiert:

- IDENTITY_ENDPOINT: die URL zum lokalen Tokendienst
- IDENTITY_HEADER: ein Header, der als Maßnahme gegen SSRF-Angriffe (Server-Side Request Forgery) verwendet wird. Der Wert wird von der Plattform rotiert.

Bei **IDENTITY_ENDPOINT** handelt es sich um eine lokale URL, über die Ihre App Token anfordern kann. Um ein Token für eine Ressource abzurufen, senden Sie eine HTTP-GET-Anforderung mit folgenden Parametern an diesen Endpunkt:

> | Parametername    | Geben Sie in     | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | Abfrage  | Der Azure AD-Ressourcen-URI der Ressource, für die ein Token abgerufen werden soll. Dies kann einer der [Azure-Dienste, die die Azure AD-Authentifizierung unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), oder ein anderer Ressourcen-URI sein.    |
> | api-version       | Abfrage  | Die Version der zu verwendenden Token-API. Verwenden Sie mindestens 2019-08-01 (es sei denn, Sie verwenden den Tarif Linux-Verbrauch, der zurzeit nur 2017-09-01 bietet, siehe Hinweis weiter oben).                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | Header | Der Wert der Umgebungsvariable IDENTITY_HEADER. Dieser Header wird als Maßnahme gegen SSRF-Angriffe (Server-Side Request Forgery) verwendet.                                                                                                                                                                                                    |
> | client_id         | Abfrage  | (Optional:) Die Client-ID der benutzerseitig zugewiesenen Identität, die verwendet werden soll. Kann nicht für eine Anforderung verwendet werden, die `principal_id`, `mi_res_id` oder `object_id` einschließt. Wenn keiner der ID-Parameter (`client_id`, `principal_id`, `object_id`, `mi_res_id`) angegeben ist, wird die systemseitig zugewiesene Identität verwendet.                                             |
> | principal_id      | Abfrage  | (Optional:) Die Prinzipal-ID der benutzerseitig zugewiesenen Identität, die verwendet werden soll. `object_id` ist ein Alias, der stattdessen verwendet werden kann. Kann nicht für eine Anforderung verwendet werden, die client_id, mi_res_id oder object_id einschließt. Wenn keiner der ID-Parameter (`client_id`, `principal_id`, `object_id`, `mi_res_id`) angegeben ist, wird die systemseitig zugewiesene Identität verwendet. |
> | mi_res_id         | Abfrage  | (Optional:) Die Azure-Ressourcen-ID der benutzerseitig zugewiesenen Identität, die verwendet werden soll. Kann nicht für eine Anforderung verwendet werden, die `principal_id`, `client_id` oder `object_id` einschließt. Wenn keiner der ID-Parameter (`client_id`, `principal_id`, `object_id`, `mi_res_id`) angegeben ist, wird die systemseitig zugewiesene Identität verwendet.                                      |

> [!IMPORTANT]
> Wenn Sie Token für benutzerseitig zugewiesene Identitäten abrufen möchten, müssen Sie eine der optionalen Eigenschaften einschließen. Andernfalls versucht der Tokendienst, ein Token für eine vom System zugewiesene Identität abzurufen, die möglicherweise nicht vorhanden ist.

Eine erfolgreiche 200 OK-Antwort enthält einen JSON-Text mit folgenden Eigenschaften:

> | Eigenschaftenname | BESCHREIBUNG                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Das angeforderte Zugriffstoken. Der aufrufende Webdienst kann dieses Token verwenden, um die Authentifizierung für den empfangenden Webdienst durchzuführen.                                                                                                                               |
> | client_id     | Die Client-ID der verwendeten Identität                                                                                                                                                                                                       |
> | expires_on    | Der Zeitpunkt, zu dem das Zugriffstoken abläuft. Das Datum wird als Anzahl von Sekunden ab „1970-01-01T0:0:0Z UTC“ (entspricht dem Anspruch `exp` des Tokens) dargestellt.                                                                                |
> | not_before    | Der Zeitpunkt, ab dem das Zugriffstoken wirksam ist und akzeptiert werden kann. Das Datum wird als Anzahl von Sekunden ab „1970-01-01T0:0:0Z UTC“ (entspricht dem Anspruch `nbf` des Tokens) dargestellt.                                                      |
> | resource      | Die Ressource, für die das Zugriffstoken angefordert wurde (Übereinstimmung mit dem Abfragezeichenfolgenparameter `resource` der Anforderung).                                                                                                                               |
> | token_type    | Gibt den Wert des Tokentyps an. Bearertoken ist der einzige Typ, den Azure AD unterstützt. Weitere Informationen zu Bearertoken finden Sie unter [OAuth 2.0-Autorisierungsframework: Verwendung von Bearertoken (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Diese Antwort ist mit der [Antwort auf die Zugriffstokenanforderung zwischen zwei Azure AD-Diensten](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response) identisch.

### <a name="rest-protocol-examples"></a>Beispiele für REST-Protokolle

Eine Beispielanforderung könnte folgendermaßen aussehen:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Eine Beispielantwort könnte folgendermaßen aussehen:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Codebeispiele

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> Für .NET-Sprachen können Sie auch [Microsoft.Azure.Services.AppAuthentication](#asal) verwenden, statt diese Anforderung selbst zu entwerfen.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Verwendung der Microsoft.Azure.Services.AppAuthentication-Bibliothek für .NET

Bei .NET-Anwendungen und -Funktionen stellt das Microsoft.Azure.Services.AppAuthentication-Paket die einfachste Methode für das Arbeiten mit einer verwalteten Identität dar. Mithilfe dieser Bibliothek können Sie zudem Ihren Code lokal auf dem Entwicklungscomputer testen. Hierzu verwenden Sie Ihr Benutzerkonto aus Visual Studio, aus der [Azure CLI](/cli/azure) oder der integrierten Active Directory-Authentifizierung. Beim Hosting in der Cloud wird standardmäßig eine vom System zugewiesene Identität verwendet. Sie können dieses Verhalten jedoch mithilfe einer Umgebungsvariable für die Verbindungszeichenfolge anpassen, die auf die Client-ID einer vom Benutzer zugewiesenen Identität verweist. Weitere Informationen zu Entwicklungsoptionen mit dieser Bibliothek finden Sie in der [Microsoft.Azure.Services.AppAuthentication reference]. In diesem Abschnitt werden die ersten Schritte mit der Bibliothek in Ihrem Code erläutert.

1. Fügen Sie Ihrer Anwendung Verweise auf [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) und alle weiteren erforderlichen NuGet-Pakete hinzu. Im folgenden Beispiel wird auch [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) verwendet.

2. Fügen Sie Ihrer Anwendung den folgenden Code hinzu, und ändern Sie ihn so, dass er die richtige Ressource als Ziel verwendet. Dieses Beispiel zeigt zwei Möglichkeiten für die Arbeit mit Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Wenn Sie eine vom Benutzer zugewiesene verwaltete Identität verwenden möchten, können Sie die Anwendungseinstellung `AzureServicesAuthConnectionString` auf `RunAs=App;AppId=<clientId-guid>` festlegen. Ersetzen Sie `<clientId-guid>` durch die Client-ID der Identität, die Sie verwenden möchten. Sie können mehrere dieser Verbindungszeichenfolgen definieren. Dazu verwenden Sie benutzerdefinierte Anwendungseinstellungen und übergeben deren Werte an den Konstruktor AzureServiceTokenProvider.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Weitere Informationen zum Konfigurieren von AzureServiceTokenProvider und zu den dadurch bereitgestellten Vorgängen finden Sie in der [Microsoft.Azure.Services.AppAuthentication reference] und im [Beispiel zu App Service und KeyVault mit MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Verwenden des Azure SDK für Java

Für Java-Anwendungen und -Funktionen besteht die einfachste Methode für die Arbeit mit einer verwalteten Identität in der Verwendung des [Azure SDK für Java](https://github.com/Azure/azure-sdk-for-java). In diesem Abschnitt werden die ersten Schritte mit der Bibliothek in Ihrem Code erläutert.

1. Fügen Sie einen Verweis zur [Azure SDK-Bibliothek](https://mvnrepository.com/artifact/com.microsoft.azure/azure) hinzu. Bei Maven-Projekten können Sie den folgenden Codeausschnitt zum `dependencies`-Abschnitt der POM-Datei des Projekts hinzufügen:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Verwenden Sie das `AppServiceMSICredentials`-Objekt für die Authentifizierung. In diesem Beispiel wird gezeigt, wie dieser Mechanismus mit Azure Key Vault verwendet werden kann:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Entfernen einer Identität

Eine systemseitig zugewiesene Identität kann entfernt werden, indem das Feature über das Portal, PowerShell oder die Befehlszeilenschnittstelle auf die gleiche Weise wie bei der Erstellung deaktiviert wird. Benutzerseitig zugewiesene Identitäten können einzeln entfernt werden. Um alle Identitäten zu entfernen, legen Sie den Identitätstyp auf „Kein“ fest.

Bei dieser Methode zum Entfernen einer systemseitig zugewiesenen Identität wird diese auch aus Azure AD gelöscht. Systemseitig zugewiesene Identitäten werden automatisch aus Azure AD entfernt, wenn die App-Ressource gelöscht wird.

So entfernen Sie alle Identitäten in einer [ARM-Vorlage](#using-an-azure-resource-manager-template):

```json
"identity": {
    "type": "None"
}
```

So entfernen Sie alle Identitäten in Azure PowerShell (nur Azure Functions):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Es gibt auch eine Anwendungseinstellung, die festgelegt werden kann (WEBSITE_DISABLE_MSI). Hierdurch wird nur der lokale Tokendienst deaktiviert. Die Identität bleibt jedoch erhalten, und in den Tools wird die verwaltete Identität weiterhin als „Ein“ oder „Aktiviert“ angezeigt. Daher wird die Verwendung dieser Einstellung nicht empfohlen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Sicheres Zugreifen auf eine SQL-Datenbank mit einer verwalteten Identität](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication-Referenz]: ../key-vault/general/service-to-service-authentication.md
