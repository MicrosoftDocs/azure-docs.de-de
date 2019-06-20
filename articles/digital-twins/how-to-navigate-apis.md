---
title: Navigieren in Azure Digital Twins-APIs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie gängige Muster der Abfrage der Azure Digital Twins-Verwaltungs-APIs verwenden können.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: 9cfcdc879e36b93e21bff6f91886536d799553bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60924493"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Verwenden der Azure Digital Twins-Verwaltungs-APIs

Die Azure Digital Twins-Verwaltungs-APIs bieten leistungsstarke Funktionen für Ihre IoT-Apps. In diesem Artikel wird die Navigation durch die API-Struktur erläutert.  

## <a name="api-summary"></a>API-Zusammenfassung

Die folgende Liste enthält die Komponenten der Digital Twins-APIs.

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Diese APIs interagieren mit den physischen Speicherorten in Ihrem Setup. Sie helfen Ihnen, die digitalen Mappings Ihrer physischen Speicherorte in Form eines [räumlichen Graphs](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph) zu erstellen, zu löschen und zu verwalten.

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Diese APIs interagieren mit den Geräten in Ihrem Setup. Diese Geräte können einen oder mehrere Sensoren verwalten. Ein Gerät kann beispielsweise Ihr Telefon sein, ein Raspberry Pi-Sensorhalter oder ein Lora-Gateway usw.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Diese APIs helfen Ihnen bei der Kommunikation mit den Sensoren, die mit Ihren Geräten und Ihren physischen Speicherorten verbunden sind. Die Sensoren erfassen und senden Umgebungswerte, mit denen Sie Ihre räumliche Umgebung manipulieren können.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Diese APIs helfen Ihnen, Ressourcen, wie beispielsweise einen IoT-Hub, für Ihre Digital Twins-Instanz einzurichten.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Diese APIs ermöglichen es Ihnen, erweiterte Typen mit Ihren Digital Twins -Objekten zu verknüpfen, um diesen Objekten spezifische Merkmale hinzuzufügen. Diese Typen ermöglichen eine einfache Filterung und Gruppierung von Objekten in der Benutzeroberfläche und den benutzerdefinierten Funktionen, die Ihre Telemetriedaten verarbeiten. Beispiele für erweiterte Typen sind *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType* usw.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Diese APIs helfen Ihnen bei der Verwaltung von Ontologien, d.h. Sammlungen von erweiterten Typen. Ontologien stellen Namen für Objekttypen entsprechend dem physischen Raum, den sie repräsentieren, bereit. Die Ontologie *BACnet* bietet beispielsweise spezifische Namen für *sensor types*, *datatypes*, *datasubtypes* und *dataunittypes*. Ontologien werden vom Dienst verwaltet und erstellt. Benutzer können Ontologien laden und entladen. Wenn eine Ontologie geladen wird, sind alle zugehörigen Typnamen aktiviert und können in Ihrem räumlichen Graphen bereitgestellt werden. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Mit diesen APIs können Sie benutzerdefinierte Eigenschaften für *spaces*, *devices*, *users* und *sensors* erstellen. Diese Eigenschaften werden als Schlüssel/Wert-Paare erstellt. Sie können den Datentyp für diese Eigenschaften durch Festlegen von *PrimitiveDataType* definieren. Sie können beispielsweise eine Eigenschaft namens *BasicTemperatureDeltaProcessingRefreshTime* vom Typ *uint* für Ihre Sensoren definieren und dann für jeden Ihrer Sensoren einen Wert für diese Eigenschaft vergeben. Sie können auch Einschränkungen für diese Werte während der Erstellung der Eigenschaft hinzufügen, wie z.B. *Min*- und *Max*-Bereiche, sowie zulässige Werte wie *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Diese APIs ermöglichen es Ihnen, die Bedingungen anzugeben, die Sie anhand Ihrer eingehenden Gerätedaten auswerten möchten. Weitere Informationen hierzu finden Sie in [diesem Artikel](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Diese APIs ermöglichen es Ihnen, eine benutzerdefinierte Funktion zu erstellen, zu löschen oder zu aktualisieren, die ausgeführt wird, wenn Bedingungen auftreten, die von den *Matchern* definiert wurden, um Daten aus Ihrem Setup zu verarbeiten. Weitere Informationen zu diesen benutzerdefinierten Funktionen, auch *benutzerdefinierte Funktionen* genannt, finden Sie in [diesem Artikel](concepts-user-defined-functions.md#user-defined-functions). 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Mit diesen APIs können Sie Endpunkte erstellen, damit Ihre Digital Twins-Lösung mit anderen Azure-Diensten zur Datenspeicherung und -analyse kommunizieren kann. Weitere Informationen finden Sie [in diesem Artikel](concepts-events-routing.md). 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Mit diesen APIs können Sie Sicherheitsschlüsselspeicher für Ihre Spaces verwalten. Diese Speicher können eine Sammlung von Sicherheitsschlüsseln enthalten und ermöglichen es Ihnen, die neuesten gültigen Schlüssel einfach abzurufen.

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Diese APIs ermöglichen es Ihnen, Benutzer Ihren Spaces zuzuordnen und diese Personen bei Bedarf zu finden. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Diese APIs ermöglichen es Ihnen, systemweite Einstellungen zu verwalten, wie z.B. die Standardtypen von Spaces und Sensoren. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Diese APIs ermöglichen es Ihnen, Rollen mit Einheiten wie Benutzer-ID, benutzerdefinierte Funktions-ID usw. zu verknüpfen. Jede Rollenzuweisung enthält die ID der zu verknüpfenden Entität, den Entitätstyp, die ID der zu verknüpfenden Rolle, die Mandanten-ID und einen Pfad, der die Obergrenze der Ressource definiert, auf die die Entität mit dieser Zuweisung zugreifen kann. Weitere Informationen finden Sie [in diesem Artikel](security-role-based-access-control.md).


## <a name="api-navigation"></a>API-Navigation

Die Digital Twins-APIs unterstützen die Filterung und Navigation durch Ihren räumlichen Graph mit den folgenden Parametern:

- **spaceId**: Die API filtert die Ergebnisse nach der angegebenen Space-ID. Zusätzlich ist das boolesche Flag **useParentSpace** auf die APIs [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) anwendbar, was bedeutet, dass sich die angegebene Space-ID auf den übergeordneten Space anstelle des aktuellen Space bezieht. 

- **minLevel** und **maxLevel**: Es wird davon ausgegangen, dass Stammspaces auf Ebene 1 sind. Spaces mit einem übergeordneten Space auf Ebene *n* sind auf Ebene *n+1*. Wenn diese Werte festgelegt sind, können Sie die Ergebnisse auf bestimmten Ebenen filtern. Nach dem Festlegen sind dies Inklusivwerte. Geräte, Sensoren und andere Objekte werden als auf der gleichen Ebene wie ihr nächstgelegener Space betrachtet. Um alle Objekte auf einer bestimmten Ebene zu erhalten, setzen Sie sowohl **minLevel** als auch **maxLevel** auf den gleichen Wert.

- **minRelative** und **maxRelative**: Wenn diese Filter angegeben werden, ist die entsprechende Ebene relativ zur Ebene der angegebenen Space-ID:
   - Die relative Ebene *0* ist gleich der gleichen Ebene wie die angegebene Space-ID.
   - Die relative Ebene *1* repräsentiert Spaces auf der gleichen Ebene wie der untergeordnete Space der angegebenen Space-ID. Die relative Ebene *n* repräsentiert Spaces, die um *n* Ebenen niedriger sind als der angegebene Space.
   - Die relative Ebene *-1* repräsentiert Spaces auf der gleichen Ebene wie der übergeordnete Space des angegebenen Space.

- **traverse**: Ermöglicht es Ihnen, den Space von einer gegebenen Space-ID aus in beide Richtungen zu durchlaufen, wie durch die folgenden Werte angegeben.
   - **Keine:** Dieser Standardwert filtert nach der angegebenen Space-ID.
   - **Down**: Dieser filtert nach der angegebenen Space-ID und deren Nachfolger. 
   - **Up**: Dieser filtert nach der angegebenen Space-ID und deren Vorgänger. 
   - **Span**: Dies filtert einen horizontalen Abschnitt des räumlichen Graphs auf der gleichen Ebene wie die angegebene Space-ID. Dies muss entweder **minRelative** oder **maxRelative** sein, oder auf „True“ gesetzt werden. 


### <a name="examples"></a>Beispiele

Die folgende Liste zeigt einige Beispiele für die Navigation durch die [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices)-APIs. Beachten Sie, dass sich der Platzhalter `YOUR_MANAGEMENT_API_URL` auf die URI der Digital Twins-APIs im Format `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/` bezieht, wobei `YOUR_INSTANCE_NAME` der Name Ihrer Azure Digital Twins-Instanz ist und `YOUR_LOCATION` die Region ist, in der Ihre Instanz gehostet wird.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` gibt alle Geräte zurück, die an Stamm-Spaces angefügt sind.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` gibt alle Geräte zurück, die an Spaces der Ebenen 2, 3 oder 4 angefügt sind.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` gibt alle Geräte zurück, die direkt an mySpaceId angefügt sind.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` gibt alle Geräte zurück, die an mySpaceId oder einen seiner Nachfolger angefügt sind.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` gibt alle Geräte zurück, die an Nachfolger von mySpaceId angefügt sind, mit Ausnahme von mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` gibt alle Geräte zurück, die an unmittelbare untergeordnete IDs von mySpaceId angefügt sind.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` gibt alle Geräte zurück, die an einen der Vorgänger von mySpaceId angefügt sind.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` gibt alle Geräte zurück, die an Nachfolger von mySpaceId angefügt sind, die auf einer Ebene kleiner oder gleich 5 sind.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` gibt alle Geräte zurück, die an Spaces angefügt sind, die sich auf der gleichen Ebene wie mySpaceId befinden.


## <a name="odata-support"></a>OData-Unterstützung
Die meisten APIs, die Sammlungen zurückgeben, wie z.B. ein GET-Aufruf auf /spaces, unterstützen die folgende Teilmenge der generischen [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData)-Systemabfrageoptionen:  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** – Wenn Sie beabsichtigen, die gesamte Sammlung anzuzeigen, sollten Sie sie als Gesamtmenge in einem einzigen Aufruf anfordern und dann in Ihrer Anwendung ein Paging durchführen. 

Beachten Sie, dass andere Abfrageoptionen, z B. „$count“, „$expand“, „$search“, nicht unterstützt werden.

### <a name="examples"></a>Beispiele

Die folgende Liste zeigt einige Beispiele für Abfragen mit den Systemabfrageoptionen von OData:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 

## <a name="next-steps"></a>Nächste Schritte

Einige häufige API-Abfragemuster finden Sie in [Abfragen von Azure Digital Twins-APIs für gängige Aufgaben](how-to-query-common-apis.md).


