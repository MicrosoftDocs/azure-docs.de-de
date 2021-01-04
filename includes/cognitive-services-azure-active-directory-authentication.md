---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 05/11/2020
ms.openlocfilehash: 1085daca153431a28fdcc2583d0e31308214bf91
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562083"
---
## <a name="authenticate-with-azure-active-directory"></a>Authentifizieren mit Azure Active Directory

> [!IMPORTANT]
> 1. Derzeit unterstützen **nur** Maschinelles Sehen-API, Gesichtserkennungs-API, Textanalyse-API, Plastischer Reader, Formularerkennung, Anomalieerkennung und alle Bing-Dienste mit Ausnahme der benutzerdefinierten Bing-Suche die Authentifizierung mit Azure Active Directory (AAD).
> 2. Die AAD-Authentifizierung muss immer mit dem Namen der benutzerdefinierten Unterdomäne Ihrer Azure-Ressource verwendet werden. [Regionale Endpunkte](../articles/cognitive-services/cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) unterstützen die AAD-Authentifizierung nicht.

In den vorherigen Abschnitten haben wir Ihnen gezeigt, wie die Authentifizierung bei Azure Cognitive Services mit einem Abonnementschlüssel für einen einzelnen Dienst oder für mehrere Dienste funktioniert. Diese Schlüssel ermöglichen zwar einen schnellen und einfachen Einstieg in die Entwicklung, eignen sich aber nicht für komplexere Szenarien, die eine rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) erfordern. Sehen wir uns an, was für die Authentifizierung über Azure Active Directory (AAD) erforderlich ist.

In den folgenden Abschnitten verwenden Sie entweder die Azure Cloud Shell-Umgebung oder die Azure CLI, um eine Unterdomäne zu erstellen, Rollen zuzuweisen und ein Bearertoken für den Aufruf der Azure Cognitive Services abzurufen. Falls Sie einmal nicht weiterkommen, finden Sie in jedem Abschnitt Links mit sämtlichen verfügbaren Optionen für jeden Befehl in der Azure Cloud Shell bzw. Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Erstellen einer Ressource mit einer benutzerdefinierten Unterdomäne

Der erste Schritt besteht darin, eine benutzerdefinierte Unterdomäne zu erstellen. Wenn Sie eine vorhandene Cognitive Services-Ressource verwenden möchten, die nicht über einen benutzerdefinierten Unterdomänennamen verfügt, befolgen Sie die Anweisungen in [Benutzerdefinierte Unterdomänennamen für Cognitive Services](../articles/cognitive-services/cognitive-services-custom-subdomains.md#how-does-this-impact-existing-resources), um die benutzerdefinierte Unterdomäne für Ihre Ressource zu aktivieren.

1. Öffnen Sie als Erstes die Azure Cloud Shell. [Wählen Sie dann ein Abonnement aus](/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0):

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Als Nächstes [erstellen Sie eine Cognitive Services-Ressource](/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) mit einer benutzerdefinierten Unterdomäne. Der Name der Unterdomäne muss global eindeutig sein und darf einige Zeichen nicht enthalten, wie z.B. „.“, „!“ oder „,“.

   ```powershell-interactive
   $account = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Wenn die Erstellung erfolgreich verläuft, zeigt der **Endpunkt** den Unterdomänennamen an, der für Ihre Ressource eindeutig ist.


### <a name="assign-a-role-to-a-service-principal"></a>Zuweisen einer Rolle zu einem Dienstprinzipal

Nachdem Sie nun über eine Unterdomäne verfügen, die Ihrer Ressource zugeordnet ist, müssen Sie einem Dienstprinzipal eine Rolle zuweisen.

> [!NOTE]
> Denken Sie daran, dass die Weitergabe von Azure-Rollenzuweisungen bis zu fünf Minuten dauern kann.

1. Als Erstes registrieren Sie eine [AAD-Anwendung](/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   $app = New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Im nächsten Schritt benötigen Sie die **ApplicationId**.

2. Als Nächstes müssen Sie einen [Dienstprinzipal](/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) für die AAD-Anwendung erstellen.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Wenn Sie eine Anwendung im Azure-Portal registrieren, wird dieser Schritt für Sie ausgeführt.

3. Der letzte Schritt besteht darin, dem Dienstprinzipal (im Bereich der Ressource) [die Rolle „Cognitive Services-Benutzer“ zuzuweisen](/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0). Durch Zuweisen einer Rolle gewähren Sie dem Dienstprinzipal Zugriff auf diese Ressource. Sie können einem Dienstprinzipal Zugriff auf mehrere Ressourcen in Ihrem Abonnement gewähren.
   >[!NOTE]
   > Es wird die ObjectId des Dienstprinzipals verwendet, nicht die ObjectId der Anwendung.
   > Die ACCOUNT_ID entspricht der Azure-Ressourcen-ID des Cognitive Services-Kontos, das Sie erstellt haben. Die Azure-Ressourcen-ID finden Sie unter den Eigenschaften der jeweiligen Ressource im Azure-Portal.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Beispiel für eine Anforderung

In diesem Beispiel wird ein Kennwort für die Authentifizierung des Dienstprinzipals verwendet. Das bereitgestellte Token wird dann für den Aufruf der Maschinelles Sehen-API verwendet.

1. Rufen Sie Ihre **TenantId** ab:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Rufen Sie ein Token ab:
   > [!NOTE]
   > Wenn Sie Azure Cloud Shell verwenden, ist die `SecureClientSecret`-Klasse nicht verfügbar. 

   #### <a name="powershell"></a>[PowerShell](#tab/powershell)
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
   
   #### <a name="azure-cloud-shell"></a>[Azure Cloud Shell](#tab/azure-cloud-shell)
   ```Azure Cloud Shell
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, <YOUR_PASSWORD>
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ``` 

   ---

3. Aufrufen der Maschinelles Sehen-API:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Alternativ dazu kann der Dienstprinzipal auch mit einem Zertifikat authentifiziert werden. Neben dem Dienstprinzipal wird auch der Benutzerprinzipal unterstützt, indem Berechtigungen durch eine andere AAD-Anwendung delegiert werden. In diesem Fall verwenden Benutzer keine Kennwörter oder Zertifikate, sondern werden beim Abrufen des Tokens zur zweistufigen Authentifizierung aufgefordert.

## <a name="authorize-access-to-managed-identities"></a>Autorisieren des Zugriffs auf verwaltete Identitäten
 
Cognitive Services unterstützen die Azure AD-Authentifizierung (Azure Active Directory) mit [verwalteten Identitäten für Azure-Ressourcen](../articles/active-directory/managed-identities-azure-resources/overview.md). Sie können verwaltete Identitäten für Azure-Ressourcen verwenden, um den Zugriff auf Cognitive Services-Ressourcen mithilfe von Azure AD-Anmeldeinformationen über Anwendungen zu autorisieren, die auf virtuellen Azure-Computern, in Funktions-Apps, in VM-Skalierungsgruppen und anderen Diensten ausgeführt werden. Durch Verwendung von verwalteten Identitäten für Azure-Ressourcen zusammen mit der Azure AD-Authentifizierung können Sie vermeiden, dass Anmeldeinformationen mit den in der Cloud ausgeführten Anwendungen gespeichert werden.  

### <a name="enable-managed-identities-on-a-vm"></a>Aktivieren von verwalteten Identitäten auf einem virtuellen Computer

Damit Sie verwaltete Identitäten für Azure-Ressourcen zum Autorisieren des Zugriffs auf Cognitive Services-Ressourcen über Ihren virtuellen Computer verwenden können, müssen Sie verwaltete Identitäten für Azure-Ressourcen auf dem virtuellen Computer aktivieren. Informationen zum Aktivieren von verwalteten Identitäten für Azure-Ressourcen finden Sie unter:

- [Azure portal](../articles/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../articles/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](../articles/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-Vorlage](../articles/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-Clientbibliotheken](../articles/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../articles/active-directory/managed-identities-azure-resources/overview.md).