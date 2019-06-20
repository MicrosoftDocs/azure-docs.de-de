---
title: Erstellen von mobilen Anwendungen mit Xamarin und Azure Cosmos DB
description: Ein Tutorial, in dem eine Xamarin iOS-, Android- oder Forms-Anwendung mit Azure Cosmos DB erstellt wird. Azure Cosmos DB ist eine schnelle, globale Clouddatenbank für mobile Apps.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2018
ms.author: sngun
ms.openlocfilehash: a9b1952ed981b0ec522ff3bd607bbef275ff93b8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479631"
---
# <a name="build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Erstellen von mobilen Anwendungen mit Xamarin und Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Die meisten mobilen Apps müssen Daten in der Cloud speichern, und Azure Cosmos DB ist eine Clouddatenbank für mobile Apps. Sie besitzt alle Komponenten, die Entwickler von mobilen Produkten benötigen. Es handelt sich um eine vollständig verwaltete Database-as-a-Service-Lösung, die bei Bedarf skaliert werden kann. Diese Lösung kann Benutzern Ihrer Anwendung Daten unabhängig vom Standort weltweit transparent bereitstellen. Mit dem [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md) können Sie erreichen, dass mobile Xamarin-Apps ohne mittlere Ebene direkt mit Azure Cosmos DB interagieren.

Dieser Artikel enthält ein Tutorial zur Erstellung mobiler Apps mit Xamarin und Azure Cosmos DB. Sie finden den vollständigen Quellcode für das Tutorial und Informationen zum Verwalten von Benutzern und Berechtigungen unter [Xamarin und Azure Cosmos DB auf GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Azure Cosmos DB-Funktionen für mobile Apps
Azure Cosmos DB enthält die folgenden Hauptfunktionen für Entwickler von mobilen Apps:

![Azure Cosmos DB-Funktionen für mobile Apps](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Umfassende Abfragen für schemalose Daten. Azure Cosmos DB speichert Daten als schemalose JSON-Dokumente in heterogenen Sammlungen. Die Lösung bietet [umfassende und schnelle Abfragen](how-to-sql-query.md), ohne sich um Schemas oder Indizes kümmern zu müssen.
* Schneller Durchsatz. Mit Azure Cosmos DB dauert das Lesen und Schreiben von Dokumenten nur wenige Millisekunden. Entwickler können den benötigten Durchsatz angeben, und Azure Cosmos DB bietet dafür eine SLA mit einer Verfügbarkeit von 99,99 Prozent für alle Konten mit einer einzelnen Region und für alle Konten mit mehreren Regionen und gelockerter Konsistenz sowie eine Leseverfügbarkeit von 99,999 Prozent für alle Datenbankkonten mit mehreren Regionen.
* Unbegrenzte Skalierung. Ihre Azure Cosmos DB-Sammlungen [wachsen mit Ihrer App mit](partition-data.md). Sie können mit einer kleinen Datenmenge und einem Durchsatz von Hunderten von Anforderungen pro Sekunde beginnen. Ihre Sammlungen oder Datenbanken können auf eine Größe im Petabytebereich anwachsen, und der Durchsatz kann auf Hunderte Millionen Anforderungen pro Sekunde vergrößert werden.
* Globale Verteilung. Benutzer mobiler Apps sind (häufig weltweit) unterwegs. Azure Cosmos DB ist eine [global verteilte Datenbank](distribute-data-globally.md). Klicken Sie auf die Karte, um Ihre Daten den Benutzern zugänglich machen.
* Integrierte Autorisierung. Mit Azure Cosmos DB können Sie gängige Muster wie [benutzerspezifische Daten](https://aka.ms/documentdb-xamarin-todouser) oder von mehreren Benutzern gemeinsam genutzte Daten ohne benutzerdefinierten komplexen Autorisierungscode leicht implementieren.
* Geoabfragen. Viele mobile Apps verfügen heutzutage über Geokontextfunktionen. Dank der erstklassigen Unterstützung von [Geotypen](geospatial.md) ist das Erstellen dieser Funktionen mit Azure Cosmos DB einfach zu erreichen.
* Binäre Anlagen. Ihre App-Daten enthalten häufig binäre Blobs. Die native Unterstützung von Anlagen vereinfacht die Nutzung von Azure Cosmos DB als zentrale Anwendung für Ihre App-Daten.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Tutorial zu Azure Cosmos DB und Xamarin
Im folgenden Tutorial wird veranschaulicht, wie Sie eine mobile Anwendung mit Xamarin und Azure Cosmos DB erstellen. Den vollständigen Quellcode für das Tutorial finden Sie unter [Xamarin und Azure Cosmos DB auf GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Erste Schritte
Die ersten Schritte mit Azure Cosmos DB sind ganz einfach. Erstellen Sie im Azure-Portal ein neues Azure Cosmos DB-Konto. Klicken Sie auf die Registerkarte **Schnellstart**. Laden Sie ein Xamarin Forms-Beispiel für eine Aufgabenliste herunter, für das bereits eine Verbindung mit Ihrem Azure Cosmos DB-Konto besteht. 

![Azure Cosmos DB-Schnellstart für mobile Apps](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

Falls Sie bereits über eine Xamarin-App verfügen, können Sie einfach das [Azure Cosmos DB NuGet-Paket](sql-api-sdk-dotnet-core.md) hinzufügen. Azure Cosmos DB unterstützt freigegebene Xamarin.IOS-, Xamarin.Android- und Xamarin Forms-Bibliotheken.

### <a name="work-with-data"></a>Arbeiten mit Daten
Ihre Datensätze werden in Azure Cosmos DB als schemalose JSON-Dokumente in heterogenen Sammlungen gespeichert. Sie können Dokumente mit verschiedenen Strukturen in derselben Sammlung speichern:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

In Ihren Xamarin-Projekten können Sie in die Sprache integrierte Abfragen für schemalose Daten verwenden:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Hinzufügen von Benutzern
Wie bei vielen anderen Beispielen für die ersten Schritte auch, wird für das Azure Cosmos DB-Beispiel, das Sie heruntergeladen haben, eine Authentifizierung für den Dienst mit dem Masterschlüssel durchgeführt, der im Code der App hartcodiert ist. Diese Standardvorgehensweise ist für eine App, die Sie nicht nur in Ihrem lokalen Emulator ausführen möchten, nicht zu empfehlen. Wenn ein nicht autorisierter Benutzer in den Besitz des Masterschlüssels gelangt, kann die Sicherheit aller Daten in Ihrem Azure Cosmos DB-Konto gefährdet sein. Stattdessen soll Ihre App nur auf die Datensätze des angemeldeten Benutzers zugreifen. Mit Azure Cosmos DB können Entwickler für Anwendungen Lese- oder Lese-/Schreibberechtigung für eine Sammlung, eine anhand eines Partitionsschlüssels gruppierte Dokumentgruppe oder ein bestimmtes Dokument erteilen. 

Führen Sie diese Schritte aus, um die Aufgabenlisten-App in eine Aufgabenlisten-App für mehrere Benutzer zu ändern: 

  1. Fügen Sie die Anmeldung bei Ihrer App mithilfe von Facebook, Active Directory oder eines anderen Anbieters hinzu.

  2. Erstellen Sie eine Azure Cosmos DB-UserItems-Sammlung mit **/userId** als Partitionsschlüssel. Indem Sie den Partitionsschlüssel für Ihre Sammlung angeben, kann Azure Cosmos DB beliebig skaliert werden, wenn die Zahl der App-Benutzer zunimmt, während weiterhin schnelle Abfragen möglich sind.

  3. Fügen Sie den Azure Cosmos DB-Ressourcentokenbroker hinzu. Mit dieser einfachen Web-API werden Benutzer authentifiziert und Token mit kurzer Gültigkeitsdauer für angemeldete Benutzer ausgestellt, wobei nur Zugriff auf die Dokumente in ihrer Partition besteht. In diesem Beispiel wird der Ressourcentokenbroker in App Service gehostet.

  4. Ändern Sie die App so, dass der Ressourcentokenbroker mit Facebook authentifiziert wird, und fordern Sie die Ressourcentoken für den angemeldeten Facebook-Benutzer an. Greifen Sie anschließend auf die Benutzerdaten in der UserItems-Sammlung zu.  

Sie finden ein vollständiges Codebeispiel für dieses Muster unter [Resource Token Broker auf GitHub](https://aka.ms/documentdb-xamarin-todouser). In diesem Diagramm ist die Lösung dargestellt:

![Azure Cosmos DB-Benutzer und Berechtigungsbroker](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Wenn zwei Benutzer Zugriff auf dieselbe Aufgabenliste haben sollen, können Sie dem Zugriffstoken im Ressourcentokenbroker weitere Berechtigungen hinzufügen.

### <a name="scale-on-demand"></a>Bedarfsabhängige Skalierung
Azure Cosmos DB ist eine verwaltete Datenbank in Form eines Diensts (Database as a Service). Wenn die Zahl Ihrer Benutzer zunimmt, fällt für Sie kein Aufwand in Bezug auf die Bereitstellung von VMs oder die Erhöhung der Anzahl von Kernen an. Sie müssen Azure Cosmos DB lediglich mitteilen, wie viele Vorgänge pro Sekunde (Durchsatz) Ihre App benötigt. Sie können den Durchsatz auf der Registerkarte **Skalieren** angeben, indem Sie für den Durchsatz die Kennzahl „Anforderungseinheiten pro Sekunde“ (Request Units per Second, RUs) verwenden. Für den Lesevorgang eines Dokuments mit 1 KB wird z.B. 1 RU benötigt. Sie können der Metrik **Durchsatz** auch Warnungen hinzufügen, um die Zunahme des Datenverkehrs zu überwachen und den Durchsatz bei der Auslösung von Warnungen programmgesteuert zu ändern.

![Bedarfsabhängiger Azure Cosmos DB-Skalierungsdurchsatz](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>Weltweite Verfügbarkeit
Wenn die Beliebtheit Ihrer App zunimmt, gewinnen Sie ggf. Benutzer auf der ganzen Welt hinzu. Oder vielleicht möchten Sie auf unvorhergesehene Ereignisse vorbereitet sein. Öffnen Sie im Azure-Portal Ihr Azure Cosmos DB-Konto. Klicken Sie auf die Karte, um Ihre Daten laufend in eine beliebige Anzahl von Regionen weltweit zu replizieren. Auf diese Weise stehen Ihre Daten dort zur Verfügung, wo Ihre Benutzer sind. Sie können auch Failoverrichtlinien hinzufügen, um auf Eventualitäten vorbereitet zu sein.

![Azure Cosmos DB-Skalierung über geografische Regionen hinweg](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

Herzlichen Glückwunsch. Sie haben die Lösung erstellt und verfügen nun über eine mobile App mit Xamarin und Azure Cosmos DB. Führen Sie ähnliche Schritte zum Erstellen von Cordova-Apps aus, indem Sie das Azure Cosmos DB JavaScript SDK verwenden. Erstellen Sie native iOS-/Android-Apps, indem Sie Azure Cosmos DB REST-APIs einsetzen.

## <a name="next-steps"></a>Nächste Schritte
* Sehen Sie sich den Quellcode für [Xamarin und Azure Cosmos DB auf GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) an.
* Laden Sie das [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md) herunter.
* Sehen Sie sich weitere Codebeispiele für [.NET-Anwendungen](sql-api-dotnet-samples.md) an.
* Informieren Sie sich über die [umfassenden Abfragefunktionen von Azure Cosmos DB](how-to-sql-query.md).
* Erfahren Sie mehr über die [Unterstützung von Geodaten in Azure Cosmos DB](geospatial.md).



