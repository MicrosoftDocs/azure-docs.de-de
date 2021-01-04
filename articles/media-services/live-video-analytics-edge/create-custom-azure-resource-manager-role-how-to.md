---
title: 'Erstellen einer benutzerdefinierten Azure Resource Manager-Rolle und Zuweisen der Rolle an einen Dienstprinzipal: Azure'
description: Dieser Artikel enthält Anleitungen zum Erstellen einer benutzerdefinierten Azure Resource Manager-Rolle und zum Zuweisen der Rolle an den Dienstprinzipal für Live Video Analytics in IoT Edge mithilfe der Azure-Befehlszeilenschnittstelle.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 40bf0f60a718d512e02481d977b8208112ed1a55
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425734"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Erstellen einer benutzerdefinierten Azure Resource Manager-Rolle und Zuweisen der Rolle an einen Dienstprinzipal

Die Instanz von Live Video Analytics im IoT Edge-Modul benötigt ein aktives Azure Media Services-Konto für die ordnungsgemäße Funktion. Die Beziehung zwischen Live Video Analytics im IoT Edge-Modul und dem Azure Media Service-Konto wird über einen Satz von Eigenschaften des Modulzwillings hergestellt. Eine dieser Eigenschaften des Modulzwillings ist ein [Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object), der es der Modulinstanz ermöglicht, mit dem Media Services-Konto zu kommunizieren und erforderliche Operationen für es auszulösen. Um potenziellen Missbrauch und/oder versehentliche Offenlegung von Daten auf dem Edge-Gerät zu minimieren, sollte dieser Dienstprinzipal über die geringsten möglichen Rechte verfügen.

In diesem Artikel werden die Schritte zum Erstellen einer benutzerdefinierten Azure Resource Manager-Rolle mit Azure Cloud Shell erläutert, die anschließend zum Erstellen eines Dienstprinzipals verwendet wird.

## <a name="prerequisites"></a>Voraussetzungen  

Für diesen Artikel gelten folgende Voraussetzungen:

* Azure-Abonnement mit Besitzerabonnement.
* Ein Azure Active Directory mit Berechtigungen zum Erstellen einer App und Zuweisen eines Dienstprinzipals zu einer Rolle.

Die einfachste Möglichkeit zum Überprüfen, ob Ihr Konto über die erforderlichen Berechtigungen verfügt, ist über das Portal. Siehe [Überprüfen der erforderlichen Berechtigung](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Übersicht  

Wir erörtern die Schritte zum Erstellen einer benutzerdefinierten Rolle und zu ihrer Verknüpfung mit einem Dienstprinzipal in der folgenden Reihenfolge:

1. Erstellen eines Media Service-Kontos, wenn Sie noch keins besitzen.
1. Erstellen eines Dienstprinzipals
1. Erstellen einer benutzerdefinierten Azure Resource Manager-Rolle mit eingeschränkten Rechten.
1. „Einschränken“ der Rechte des Dienstprinzipals mithilfe der erstellten benutzerdefinierten Rolle.
1. Ausführen eines einfachen Tests, um festzustellen, ob die Einschränkung des Dienstprinzipals erfolgreich möglich ist.
1. Erfassen der Parameter, die in den IoT Edge-Bereitstellungsmanifesten verwendet werden sollen.

### <a name="create-a-media-services-account"></a>Erstellen eines Media Services-Kontos  

Sollten Sie nicht über ein Media Service-Konto verfügen, gehen Sie wie folgt vor, um eins zu erstellen:

1. Navigieren Sie zur [Cloud Shell](https://shell.azure.com/).
1. Wählen Sie im Dropdownmenü auf der linken Seite des Shell-Fensters als Umgebung „Bash“ aus.

    ![Screenshot: „Bash“ im Shell-Fenster](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Legen Sie Ihr Azure-Abonnement als Standardkonto fest, und verwenden Sie dazu die folgende Befehlsvorlage:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Erstellen Sie eine [Ressourcengruppe](/cli/azure/group?view=azure-cli-latest#az-group-create) und ein [Speicherkonto](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create).
1. Erstellen Sie jetzt ein Azure Media Services-Konto, indem Sie die folgende Befehlsvorlage in Cloud Shell verwenden:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Erstellen eines Dienstprinzipals  

Wir erstellen jetzt einen neuen Dienstprinzipal und verknüpfen ihn mit Ihrem Media Service-Konto.

Ohne jegliche Authentifizierungsparameter wird die kennwortbasierte Authentifizierung mit einem zufälligen Kennwort für Ihren Dienstprinzipal verwendet. Verwenden Sie in Cloud Shell die folgende Befehlsvorlage:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Dieser Befehl erzeugt eine Antwort wie diese:

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. Die Ausgabe für einen Dienstprinzipal mit Kennwortauthentifizierung schließt den Kennwortschlüssel ein, in diesem Fall ist das der Parameter „AadSecret“. 

    Denken Sie unbedingt daran, diesen Wert zu kopieren – er kann nicht abgerufen werden. Wenn Sie das Kennwort vergessen haben, [setzen Sie die Anmeldeinformationen des Dienstprinzipals zurück](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#reset-credentials).
1. Die App-ID und der Mandantenschlüssel werden in der Ausgabe als „AadClientId“ bzw. „AadTenantId“ angezeigt. Diese werden bei der Authentifizierung des Dienstprinzipals verwendet. Notieren Sie die Werte. Sie können jedoch auch jederzeit mit [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) abgerufen werden.

### <a name="create-a-custom-role-definition"></a>Erstellen einer Definition einer benutzerdefinierten Rolle  

Im Folgenden werden die Schritte erläutert, die Sie zum Erstellen einer benutzerdefinierten Rolle befolgen sollten:

1. Erstellen Sie eine JSON-Datei zur Rollendefinition auf Ihrem lokalen System, und speichern Sie den folgenden Text in der Datei. 
    1. Ersetzen Sie <yourSubscriptionId> durch Ihre Azure-Abonnement-ID.
    1. Die einzigen für diese Rolle erlaubten Aktionen sind:
        * listContainerSas: unterstützt das Modul beim Auflisten der URLs von Speichercontainern mit Shared Access Signatures (SAS) zum Hoch- und Herunterladen von Medienobjektinhalten.
        * Schreiben von Medienobjekten: unterstützt das Modul beim Erstellen oder Aktualisieren beliebiger Medienobjekte
        * listEdgePolicies: listet die Richtlinien auf, die auf das Edge-Gerät angewendet werden  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. Führen Sie nach der Erstellung die folgende Befehlsvorlage aus, um die neue Rollendefinition im Abonnement zu erstellen:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    Nach erfolgreicher Ausführung des Befehls wird die folgende Ausgabe angezeigt:
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>Erstellen von Rollenzuweisungen  

Zum Hinzufügen einer Rollenzuweisung benötigen Sie die ObjectID des Dienstprinzipals, dem Sie die soeben erstellte benutzerdefinierte Rolle zuweisen möchten.

Verwenden Sie den folgenden Befehl in Cloud Shell, um die ObjectID abzurufen:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` kann aus der Ausgabe des Schritts [Dienstprinzipal erstellen](#create-service-principal) abgerufen werden.

Der Befehl oben gibt die ObjectID des Dienstprinzipals aus. 

```
“objectId” : “<yourObjectId>”,
```

Verwenden Sie die Vorlage [az role assignment create command](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) (Befehl „AZ-Rollenzuweisung erstellen“), um die benutzerdefinierte Rolle mit dem Dienstprinzipal zu verknüpfen:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parameter:

|Parameter|BESCHREIBUNG| 
|---|---|
|--role |Name oder ID der benutzerdefinierten Rolle. In unserem Fall: „LVAEdge User“.|
|--assignee-object-id|Die Objekt-ID des verwendeten Dienstprinzipals.|

Das Ergebnis sieht so aus:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>Bestätigen, dass die Rollenzuweisung erfolgt ist

Führen Sie den folgenden Befehl aus, um zu bestätigen, dass der Dienstprinzipal jetzt mit der soeben erstellten Rolle verknüpft ist:

```
az role assignment list  --assignee < objectId>
```

Das Ergebnis sollte wie folgt aussehen:

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
Suchen Sie nach „roleDefinitionName“, und überzeugen Sie sich, dass der Wert auf „LVAEdge User“ festgelegt ist. 

Dadurch wird bestätigt, dass die benutzerdefinierte Benutzerrolle mit dem Dienstprinzipal verknüpft ist, der für die Anwendung verwendet wird.

### <a name="test-the-service-principal-access-control"></a>Testen der Zugriffssteuerung für den Dienstprinzipal

1. Melden Sie sich mithilfe des Dienstprinzipals an. Dazu benötigen Sie drei Informationen, damit Azure Active Directory uns das richtige Zugriffstoken erteilen kann, die wir aus der Ausgabe des Schritts [Dienstprinzipal erstellen](#create-service-principal) abrufen können:
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. Versuchen wir jetzt, uns mithilfe der Befehlsvorlage unten anzumelden:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Überprüfen wir nun, ob die Anmeldung auf den Dienstprinzipal mit der Rolle „LVAEdge User“ beschränkt ist, indem wir versuchen, eine Ressourcengruppe zu erstellen, um uns zu vergewissern, dass dies fehlschlägt. Führen Sie den folgenden Befehl in Cloud Shell aus:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Dieser Befehl sollte fehlschlagen, und die Ausgabe sieht dann so aus:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Nächste Schritte  

Notieren Sie sich die folgenden Werte aus diesem Artikel. Diese Werte sind erforderlich, damit Sie die Zwillingseigenschaften des Live Video Analytics-Moduls in IoT Edge konfigurieren können. Siehe dazu [JSON-Schema des Modulzwillings](module-twin-configuration-schema.md).

| Variable aus diesem Artikel|Name der Zwillingseigenschaft für Live Video Analytics in ioT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
