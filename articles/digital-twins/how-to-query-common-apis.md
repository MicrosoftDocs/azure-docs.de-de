---
title: Azure Digital Twins – gängige Abfragemuster | Microsoft-Dokumentation
description: Lernen Sie gängige Abfragemuster der Azure Digital Twins-Verwaltungs-APIs kennen.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: ff8638042fa10c939ff9c5fa7af99a660fcdc753
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60924735"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Gewusst wie: Abfragen von Azure Digital Twins-APIs für allgemeine Aufgaben

Dieser Artikel zeigt Abfragemuster, die Ihnen helfen, allgemeine Szenarien für Ihre Azure Digital Twins-Instanz auszuführen. Dies setzt voraus, dass Ihre Digital Twins-Instanz bereits ausgeführt wird. Sie können einen beliebigen REST-Client wie Postman verwenden. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Abfragen nach Räumen und Typen

Dieser Abschnitt zeigt Beispiele für Abfragen zum Abrufen von Informationen zu den von Ihnen bereitgestellten Räumen. Stellen Sie authentifizierte GET HTTP-Anforderungen mit den Beispielabfragen, und ersetzen Sie die Platzhalter durch Werte aus Ihrem Setup. 

- Rufen Sie Räume ab, die Stammknoten sind.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Rufen Sie einen Raum nach Namen ab, und beziehen Sie Geräte, Sensoren, berechnete Werte und Sensorwerte ein. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Rufen Sie Räume und deren Geräte-/Sensorinformationen ab, deren übergeordnetes Element der angegebenen Raum-ID entspricht und die sich auf den Ebenen zwei bis fünf [relativ zum angegebenen Raum](how-to-navigate-apis.md#api-navigation) befinden. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Rufen Sie den Raum mit der angegebenen ID ab, und beziehen Sie berechnete und Sensorwerte ein.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Rufen Sie Eigenschaftenschlüssel für einen bestimmten Raum ab.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Rufen Sie Räume mit dem Eigenschaftenschlüssel namens *AreaInSqMeters* mit dem Wert 30 ab. Sie können auch Zeichenfolgenoperationen ausführen, z.B. Räume mit einem Eigenschaftenschlüssel mit `name = X contains Y` abrufen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Rufen Sie alle Namen mit dem Namen *Temperature* und zugehörigen Abhängigkeiten und Ontologien ab.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Abfragen von Rollen und Rollenzuweisungen

Dieser Abschnitt zeigt einige Abfragen, um weitere Informationen zu Rollen und ihren Zuweisungen zu erhalten. 

- Rufen Sie alle Rollen ab, die von Azure Digital Twins unterstützt werden.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Rufen Sie alle Rollenzuweisungen in Ihrer Digital Twins-Instanz ab. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Rufen Sie Rollenzuweisungen für einen bestimmten Pfad ab.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Abfragen für Geräte

Dieser Abschnitt zeigt einige Beispiele, wie Sie die Verwaltungs-APIs verwenden können, um spezifische Informationen über Ihre Geräte zu erhalten. Alle API-Aufrufe müssen authentifizierte GET HTTP-Anforderungen sein.

- Rufen Sie alle Geräte ab.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Suchen Sie alle Gerätestatus.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Rufen Sie ein bestimmtes Gerät ab.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Rufen Sie alle Geräte ab, die dem Stammraum angefügt sind.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Rufen Sie alle Geräte ab, die Räumen der Ebenen 2 oder 4 angefügt sind.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Rufen Sie alle Geräte ab, die direkt mit einer bestimmten Raum-ID verbunden sind.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Rufen Sie alle Geräte ab, die einem bestimmten Raum und seinen Nachfolgerelementen angefügt sind.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Rufen Sie alle Geräte ab, die Nachfolgerelementen eines Raums angefügt sind, dieser Raum ausgenommen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Rufen Sie alle Geräte ab, die direkten untergeordneten Elementen eines Raums angefügt sind.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Rufen Sie alle Geräte ab, die einem der Vorgängerelemente eines Raums angefügt sind.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Rufen Sie alle Geräte ab, die Nachfolgerelementen eines Raums angefügt sind, die auf einer Ebene gleich 5 oder kleiner liegen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Rufen Sie alle Geräte ab, die Räumen angefügt sind, die sich auf der gleichen Ebene wie der Raum mit ID *YOUR_SPACE_ID* befinden.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Rufen Sie die IoT Hub-Geräte-Verbindungszeichenfolge für Ihr Gerät ab.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Rufen Sie das Gerät mit der angegebenen Hardware-ID einschließlich der angefügten Sensoren ab.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Rufen Sie Sensoren für bestimmte Datentypen ab, in diesem Fall *Motion* und *Temperature*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Abfragen für Matcher und benutzerdefinierte Funktionen 

- Rufen Sie alle bereitgestellten Matcher sowie deren IDs ab.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Rufen Sie Details zu einem bestimmten Matcher ab, einschließlich der damit verknüpften Räume und benutzerdefinierten Funktion.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Werten Sie einen Matcher anhand eines Sensors aus, und aktivieren Sie die Protokollierung für das Debuggen. Die Rückgabe dieser HTTP GET-Nachricht gibt Ihnen Aufschluss darüber, ob der Matcher und der Sensor zu dem Datentyp gehören. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Rufen Sie die ID der benutzerdefinierten Funktionen ab. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Rufen Sie den Inhalt einer bestimmten benutzerdefinierten Funktion ab. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Abfragen für Benutzer

Dieser Abschnitt zeigt einige API-Beispielabfragen für die Verwaltung von Benutzern in Azure Digital Twins. Stellen Sie eine HTTP GET-Anforderung, die die Platzhalter durch Werte aus Ihrem Setup ersetzt. 

- Rufen Sie alle Benutzer ab. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Rufen Sie einen bestimmten Benutzer ab.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Nächste Schritte

Informationen dazu, wie ein Authentifizieren über Ihre Verwaltungs-API erfolgt, finden Sie unter [Verbinden mit und Authentifizieren über APIs](./security-authenticating-apis.md).

Wie Sie alle API-Endpunkte anzeigen, erfahren Sie in der [Referenzdokumentation: Azure Digital Twins-Swagger](./how-to-use-swagger.md).
