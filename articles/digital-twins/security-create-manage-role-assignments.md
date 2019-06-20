---
title: Erstellen und Verwalten von Rollenzuweisungen in Azure Digital Twins | Microsoft-Dokumentation
description: Erstellen und Verwalten von Rollenzuweisungen in Azure Digital Twins.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: lyrana
ms.custom: seodec18
ms.openlocfilehash: 72155799971760e9ddc93746dceafb1ea554d88b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66162125"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Erstellen und Verwalten von Rollenzuweisungen in Azure Digital Twins

In Azure Digital Twins wird rollenbasierte Zugriffssteuerung ([Role-Based Access Control, RBAC](./security-role-based-access-control.md)) verwendet, um Zugriffe auf Ressourcen zu verwalten.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="role-assignments-overview"></a>Übersicht der Rollenzuweisungen

Jede Rollenzuweisung entspricht der folgenden Definition:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

In der folgenden Tabelle werden die einzelnen Attribute beschrieben:

| Attribut | NAME | Erforderlich | Typ | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| roleId | Bezeichner der Rollendefinition | Ja | string | Die eindeutige ID der gewünschten Rollenzuweisung. Sie können Rollendefinitionen und deren Bezeichner (IDs) durch das Abfragen der System-API ermitteln, oder die unten stehende Tabelle überprüfen. |
| objectId | Objektbezeichner | Ja | string | Eine Azure Active Directory-ID, eine Dienstprinzipalobjekt-ID oder ein Domänenname. Wem die Rollenzuweisung zugewiesen ist (Objekt oder Person). Die Rollenzuweisung muss gemäß ihrem zugeordneten Typ formatiert sein. Für den `DomainName`-objectIdType muss objectId mit dem `“@”`-Zeichen beginnen. |
| objectIdType | Objektbezeichnertyp | Ja | string | Die Art des verwendeten Objektbezeichners. Siehe unten unter **Unterstützte ObjektIdTypes**. |
| path | Raumpfad | Ja | string | Der vollständige Zugriffspfad für das `Space`-Objekt. Ein Beispiel ist `/{Guid}/{Guid}`. Wenn für einen Bezeichner die Rollenzuweisung für den gesamten Graphen erforderlich ist, geben Sie `"/"` an. Mit diesem Zeichen wird der Stamm angegeben, aber von der Verwendung wird abgeraten. Befolgen Sie immer das Prinzip der geringsten Rechte. |
| tenantId | Mandanten-ID | Variabel | string | In den meisten Fällen eine Azure Active Directory-Mandanten-ID. Nicht zulässig für den `DeviceId`- und den `TenantId`-objectIdType. Erforderlich für den `UserId`- und den `ServicePrincipalId`-objectIdType. Optional für den DomainName-objectIdType. |

### <a name="supported-role-definition-identifiers"></a>Unterstützte Rollendefinitionsbezeichner

Jede Rollenzuweisung ordnet einer Entität in Ihrer Azure Digital Twins-Umgebung eine Rollendefinition zu.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Unterstützte Objektbezeichnertypen

Zuvor wurde das Attribut **objectIdType** eingeführt.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Rollenzuweisungsvorgänge

Azure Digital Twins unterstützt vollständige *ERSTELLEN*, *LESEN* und *LÖSCHEN*-Vorgänge für Rollenzuweisungen. *AKTUALISIEREN*-Vorgänge werden verarbeitet, indem Rollenzuweisungen hinzugefügt oder entfernt werden oder der Knoten [Raumintelligenzgraph](./concepts-objectmodel-spatialgraph.md), auf den Rollenzuweisungen Zugriff gewähren, geändert wird.

![Rollenzuweisungs-Endpunkte][1]

Die bereitgestellte Swagger-Referenzdokumentation enthält weitere Informationen zu allen verfügbaren API-Endpunkten, Anforderungsvorgängen und Definitionen.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>Gewähren von Berechtigungen für Ihren Dienstprinzipal

Das Gewähren von Berechtigungen für Ihren Dienstprinzipal ist häufig einer der ersten Schritte, die Sie bei der Arbeit mit Azure Digital Twins ausführen. Er umfasst:

1. Anmelden bei Ihrer Azure-Instanz mithilfe von PowerShell.
1. Abrufen Ihrer Dienstprinzipalinformationen.
1. Zuweisen der gewünschten Rolle zu Ihrem Dienstprinzipal.

Ihre Anwendungs-ID wird Ihnen von Azure Active Directory bereitgestellt. Weitere Informationen zum Konfigurieren und Bereitstellen eines Azure Digital Twins in Active Directory finden Sie im [Schnellstart](./quickstart-view-occupancy-dotnet.md).

Nachdem Sie über die Anwendungs-ID verfügen, führen Sie die folgenden PowerShell-Befehle aus:

```shell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId  <ApplicationId>
```

Ein Benutzer mit der Rolle **Administrator** kann einem Benutzer die Rolle „Raumadministrator“ zuweisen, indem er eine authentifizierte HTTP-POST-Anforderung an folgende URL stellt:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Mit folgendem JSON-Text:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

<div id="all"></div>

### <a name="retrieve-all-roles"></a>Abrufen aller Rollen

![Systemrollen][2]

Um alle verfügbaren Rollen (Rollendefinitionen) aufzulisten, stellen Sie eine authentifizierte HTTP-GET-Anforderung an:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Eine erfolgreiche Anforderung gibt ein JSON-Array mit Einträgen für jede Rolle zurück, die zugewiesen werden kann:

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

<div id="check"></div>

### <a name="check-a-specific-role-assignment"></a>Überprüfen einer bestimmten Rollenzuweisung

Um eine bestimmte Rollenzuweisung zu überprüfen, stellen Sie eine authentifizierte HTTP-GET-Anforderung an:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Parameterwert** | **Erforderlich** |  **Typ** |  **Beschreibung** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | string |   Die objectId für objectIdType „UserId“. |
| YOUR_PATH | True | string |   Der ausgewählte Pfad, für den der Zugriff überprüft werden solle. |
| YOUR_ACCESS_TYPE |  True | string |   Der Zugriffstyp, auf den überprüft werden soll. |
| YOUR_RESOURCE_TYPE | True | string |  Die zu überprüfende Ressource. |

Eine erfolgreiche Anforderung gibt einen booleschen `true`- oder `false`-Wert zurück, um anzuzeigen, ob dem Benutzer der Zugriffstyp für den angegebenen Pfad und die Ressource zugewiesen wurde.

### <a name="get-role-assignments-by-path"></a>Abrufen von Rollenzuweisungen nach Pfad

Um alle Rollenzuweisungen für einen Pfad abzurufen, stellen Sie eine authentifizierte HTTP-GET-Anforderung an:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Wert | Ersetzen durch |
| --- | --- |
| YOUR_PATH | Der vollständige Pfad zum Raum |

Eine erfolgreiche Anforderung gibt ein JSON-Array mit jeder Rollenzuweisung zurück, die dem ausgewählten Parameter **Pfad** zugeordnet ist:

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Widerrufen einer Berechtigung

Um eine Berechtigung eines Empfängers zu widerrufen, löschen Sie die Rollenzuweisung, indem Sie eine authentifizierte HTTP-DELETE-Anforderung stellen:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parameter | Ersetzen durch |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | Die **ID** der zu entfernenden Rollenzuweisung |

Eine erfolgreicher DELETE-Anforderung gibt einen Antwortstatus „204“ zurück. Überprüfen Sie das Entfernen der Rollenzuweisung, indem Sie [überprüfen](#check), ob die Rollenzuweisung noch gültig ist.

### <a name="create-a-role-assignment"></a>Erstellen einer Rollenzuweisung

Um eine Rollenzuweisung zu erstellen, stellen Sie eine authentifizierte HTTP-POST-Anforderung an folgende URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Überprüfen Sie, ob der JSON-Text das folgende Schema einhält:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Eine erfolgreiche Anforderung gibt einen Antwortstatus „201“ zurück, zusammen mit der **ID** der neu erstellten Rollenzuweisung:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Konfigurationsbeispiele

Die folgenden Beispiele veranschaulichen, wie Sie Ihren JSON-Text in verschiedenen, häufig auftretenden Rollenzuweisungsszenarios konfigurieren sollten.

* **Beispiel**: Ein Benutzer benötigt Administratorzugriff auf eine Ebene eines Mandantenraums.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Beispiel**: Eine Anwendung führt Testszenarien aus, in denen Geräte und Sensoren simuliert werden.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Beispiel**: Alle Benutzer, die Teil einer Domäne sind, erhalten Lesezugriff für Räume, Sensoren und Benutzer. Dieser Zugriff umfasst auch die entsprechenden zugehörigen Objekte.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Überprüfen der rollenbasierten Zugriffssteuerung (RBAC) in Azure Digital Twins finden Sie unter [Rollenbasierte Zugriffssteuerung (RBAC)](./security-authenticating-apis.md).

- Informationen zur API-Authentifizierung in Azure Digital Twins finden Sie unter [API-Authentifizierung](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
