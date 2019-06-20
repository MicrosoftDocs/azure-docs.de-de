---
title: Aktivieren der Offlinesynchronisierung mit mobilen iOS-Apps | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mobile Azure App Service-Apps verwenden, um Offlinedaten in iOS-Anwendungen zwischenzuspeichern und zu synchronisieren.
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 1283f812799fe71ef6987dbc7fab092aed4d3417
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62112649"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Aktivieren der Offlinesynchronisierung mit mobilen iOS-Apps
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Übersicht
In diesem Tutorial wird die Offlinesynchronisierung mit der Mobile Apps-Funktion von Azure App Service für iOS behandelt. Durch die Offlinesynchronisierung können Endbenutzer mit einer mobilen App interagieren, um Daten anzuzeigen, hinzuzufügen oder zu ändern, auch wenn keine Verbindung mit dem Netzwerk besteht. Änderungen werden in einer lokalen Datenbank gespeichert. Sobald das Gerät wieder online ist, werden die Änderungen mit dem Remote-Back-End synchronisiert.

Falls Sie noch keine Erfahrung mit Mobile Apps haben, sollten Sie zunächst das Tutorial [Erstellen einer iOS-App] abschließen. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt die Datenzugriffs-Erweiterungspakete hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)(in englischer Sprache).

Weitere Informationen zur Offlinesynchronisierungsfunktion finden Sie unter [Offlinedatensynchronisierung in Mobile Apps].

## <a name="review-sync"></a>Überprüfen des Clientcodes für die Synchronisierung
Das Clientprojekt, das Sie für das Tutorial [Erstellen einer iOS-App] heruntergeladen haben, enthält bereits Code zur Unterstützung der Offlinesynchronisierung mithilfe einer lokalen auf Kerndaten basierenden Datenbank. In diesem Abschnitt wird zusammengefasst, was bereits im Code des Tutorials enthalten ist. Eine grundlegende Übersicht über die Funktion finden Sie unter [Offlinedatensynchronisierung in Mobile Apps].

Mit der Funktion zur Offlinedatensynchronisierung von Mobile Apps können Endbenutzer mit einer lokalen Datenbank interagieren, auch wenn das Netzwerk nicht verfügbar ist. Um diese Funktionen in Ihrer App zu verwenden, initialisieren Sie den Synchronisierungskontext von `MSClient` , und verweisen Sie auf einen lokalen Datenspeicher. Erstellen Sie dann über die **MSSyncTable**-Schnittstelle einen Verweis auf die Tabelle.

Beachten Sie, dass in **QSTodoService.m** (Objective-C) oder **ToDoTableViewController.swift** (Swift) der Typ des Mitglieds **syncTable** gleich **MSSyncTable** ist. Bei der Offlinesynchronisierung wird diese Synchronisierungstabellen-Schnittstelle anstelle von **MSTable** verwendet. Wenn eine Synchronisierungstabelle verwendet wird, werden alle Vorgänge im lokalen Speicher gespeichert und nur mit expliziten Push- und Pullvorgängen mit dem Remote-Back-End synchronisiert.

 Um einen Verweis auf eine Synchronisierungstabelle zu erstellen, wenden Sie die Methode **syncTableWithName** auf `MSClient` an. Zum Entfernen der Funktion zur Offlinesynchronisierung verwenden Sie stattdessen **tableWithName**.

Bevor Tabellenvorgänge durchgeführt werden können, muss der lokale Speicher initialisiert werden. Hier ist der entsprechende Code:

* **Objective-C**. In der Methode **QSTodoService.init**:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**. In der Methode **ToDoTableViewController.viewDidLoad**:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Diese Methode erstellt einen lokalen Speicher mithilfe der `MSCoreDataStore`-Schnittstelle, die vom Mobile Apps SDK bereitgestellt wird. Alternativ können Sie auch einen anderen lokalen Speicher bereitstellen, indem Sie das `MSSyncContextDataSource`-Protokoll implementieren. Außerdem wird der erste Parameter von **MSSyncContext** zum Angeben eines Konflikthandlers verwendet. Da wir `nil` weitergegeben haben, verwenden wir den Standardkonflikthandler, der bei jedem Konflikt einen Fehler ausgibt.

Nun führen wir die eigentliche Synchronisierung aus und rufen Daten vom Remote-Back-End ab:

* **Objective-C**. `syncData` überträgt zuerst neue Änderungen und ruft dann **pullData** zum Abrufen von Daten vom Remote-Back-End auf. Die Methode **pullData** wiederum ruft neue Daten ab, die einer Abfrage entsprechen:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Swift:**
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc. via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

In der Objective-C-Version rufen wir in `syncData` zuerst **pushWithCompletion** im Synchronisierungskontext ab. Diese Methode ist ein Mitglied von `MSSyncContext` (und nicht der Synchronisierungstabelle selbst), da sie Änderungen auf alle Tabellen überträgt. Nur Datensätze, die lokal geändert wurden (mit CRUD-Operationen), werden an den Server gesendet. Dann wird das Hilfsobjekt **pullData** aufgerufen, das **MSSyncTable.pullWithQuery** aufruft, um Remotedaten abzurufen und in der lokalen Datenbank zu speichern.

In der Swift-Version wird **pushWithCompletion** nicht aufgerufen, da der Pushvorgang nicht unbedingt erforderlich ist. Wenn im Synchronisierungskontext der Tabelle, die einen Pushvorgang durchführt, Änderungen ausstehen, wird durch den Pullvorgang immer zuerst ein Pushvorgang ausgeführt. Wenn Sie über mehr als eine Synchronisierungstabelle verfügen, ist es jedoch am besten, explizit einen Pushvorgang auszuführen, um sicherzustellen, dass die verknüpften Tabellen konsistent sind.

Sowohl in der Objective-C- als auch in der Swift-Version können Sie mit der Methode **pullWithQuery** eine Abfrage zum Filtern der Datensätze angeben, die Sie abrufen möchten. In diesem Beispiel ruft die Abfrage alle Datensätze in der Remotetabelle `TodoItem` ab.

Der zweite Parameter für **pullWithQuery** ist eine Abfrage-ID, die für die *inkrementelle Synchronisierung* verwendet wird. Die inkrementelle Synchronisierung ruft nur die Datensätze ab, die seit der letzten Synchronisierung geändert wurden. Dafür wird der Zeitstempel `UpdatedAt` des Datensatzes (im lokalen Speicher `updatedAt`) verwendet. Die Abfrage-ID muss eine beschreibende Zeichenfolge sein, die für jede logische Abfrage in Ihrer App eindeutig ist. Um die inkrementelle Synchronisierung zu deaktivieren, übergeben Sie als Abfrage-ID `nil`. Dieser Ansatz ist möglicherweise ineffizient, da bei jedem Pullvorgang alle Datensätze abgerufen werden.

Die Objective-C-App führt eine Synchronisierung durch, wenn Sie Daten ändern oder hinzufügen, ein Benutzer die Aktualisierungsbewegung ausführt, sowie beim Starten.

Die Swift-App führt eine Synchronisierung durch, wenn der Benutzer die Aktualisierungsbewegung ausführt, sowie beim Starten.

Da die App immer dann eine Synchronisierung durchführt, sobald Daten geändert werden (Objective-C) oder die App gestartet wird (Objective-C und Swift), setzt die App voraus, dass der Benutzer online ist. In einem späteren Abschnitt werden Sie die App modifizieren, sodass Benutzer auch im Offlinemodus Daten bearbeiten können.

## <a name="review-core-data"></a>Überprüfen des Core-Datenmodells
Bei Verwendung des Offlinespeichers für die Kerndaten müssen Sie bestimmte Tabellen und Felder in Ihrem Datenmodell definieren. Die Beispiel-App enthält bereits ein Datenmodell mit dem richtigen Format. In diesem Abschnitt werden die Tabellen und ihre Verwendung erläutert.

Öffnen Sie **QSDataModel.xcdatamodeld**. Vier Tabellen sind definiert – drei, die durch das SDK verwendet werden, und eine, die für die To-do-Elemente selbst verwendet wird:
  * MS_TableOperations: Zum Nachverfolgen der Elemente, die mit dem Server synchronisiert werden müssen.
  * MS_TableOperationErrors: Zum Nachverfolgen von Fehlern, die während der Offlinesynchronisierung auftreten.
  * MS_TableConfig: Zum Nachverfolgen des letzten Aktualisierungszeitpunkts für den letzten Synchronisierungsvorgang für alle Pullvorgänge.
  * TodoItem: Zum Speichern der To-do-Elemente. Die Systemspalten **createdAt**, **updatedAt** und **version** sind optionale Systemeigenschaften.

> [!NOTE]
> Das Mobile Apps-SDK reserviert Spaltennamen, die mit **``** beginnen. Verwenden Sie dieses Präfix ausschließlich für Systemspalten. Andernfalls werden die Spaltennamen bei Verwendung des Remote-Back-Ends geändert.
>
>

Wenn Sie die Funktion für die Offlinesynchronisierung verwenden, definieren Sie die drei Systemtabellen und die Datentabelle.

### <a name="system-tables"></a>Systemtabellen

**MS_TableOperations**  

![MS_TableOperations-Tabellenattribute][defining-core-data-tableoperations-entity]

| Attribut | Type |
| --- | --- |
| id | Integer 64 |
| itemId | string |
| properties | Binärdaten |
| table | string |
| tableKind | Integer 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors-Tabellenattribute][defining-core-data-tableoperationerrors-entity]

| Attribut | Type |
| --- | --- |
| id |string |
| operationId |Integer 64 |
| properties |Binärdaten |
| tableKind |Integer 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Attribut | Type |
| --- | --- |
| id |string |
| key |string |
| keyType |Integer 64 |
| table |string |
| value |string |

### <a name="data-table"></a>Datentabelle

**TodoItem**

| Attribut | Type | Hinweis |
| --- | --- | --- |
| id | Zeichenfolge, als erforderlich gekennzeichnet |Primärschlüssel im Remotespeicher |
| complete | Boolean | To-do-Elementfeld |
| text |string |To-do-Elementfeld |
| createdAt | Date | (optional) Zuordnung zur **createdAt**-Systemeigenschaft |
| updatedAt | Date | (optional) Zuordnung zur **updatedAt**-Systemeigenschaft |
| version | string | (optional) Zum Erkennen von Konflikten, Zuordnung zu „version“ |

## <a name="setup-sync"></a>Ändern des Synchronisierungsverhaltens der App
In diesem Abschnitt ändern Sie die App, sodass beim App-Start oder beim Einfügen und Aktualisieren von Elementen keine Synchronisierung erfolgt. Es wird nur synchronisiert, wenn die Aktualisierungsschaltfläche betätigt wird.

**Objective-C:**

1. Ändern Sie in **QSTodoListViewController.m** die **viewDidLoad**-Methode, um den Aufruf von `[self refresh]` am Ende der Methode zu entfernen. Nun werden die Daten beim App-Start nicht mit dem Server synchronisiert. Stattdessen werden sie mit dem Inhalt des lokalen Speichers synchronisiert.
2. Ändern Sie in **QSTodoService.m** die Definition von`addItem` , sodass nach dem Einfügen des Elements keine Synchronisierung erfolgt. Entfernen Sie den `self syncData` -Block, und ersetzen Sie ihn durch Folgendes:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Ändern Sie die Definition von `completeItem`, wie bereits erwähnt. Entfernen Sie den Block für `self syncData`, und ersetzen Sie ihn durch Folgendes:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift:**

Kommentieren Sie in `viewDidLoad` in **ToDoTableViewController.swift** diese beiden Zeilen aus, wie hier gezeigt, um die Synchronisierung beim App-Start zu beenden. Als dieses Dokument verfasst wurde, hat die Swift-Todo-App den Dienst nicht aktualisiert, wenn ein Element hinzufügt oder abgeschlossen wird. Der Dienst wird nur beim App-Start aktualisiert.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testen der App
In diesem Abschnitt stellen Sie eine Verbindung mit einer ungültigen URL her, um ein Offlineszenario zu simulieren. Wenn Sie Datenelemente hinzufügen, werden diese im lokalen Kerndatenspeicher vorgehalten, jedoch nicht mit dem Back-End der mobilen App synchronisiert.

1. Ändern Sie die URL der mobilen App in **QSTodoService.m** in eine ungültige URL, und führen Sie sie erneut aus:

   **Objective-C**. In QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**. In ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Fügen Sie einige To-do-Elemente hinzu. Beenden Sie den Simulator (oder Erzwingen Sie das Schließen der App), und starten Sie ihn dann neu. Überprüfen Sie, ob die Änderungen erhalten geblieben sind.

3. Zeigen Sie den Inhalt der **TodoItem**-Remotetabelle an:
   * Ein Node.js-Back-End finden Sie im [Azure-Portal](https://portal.azure.com/). Dort klicken Sie im Back-End der mobilen App auf **Easy Tables** > **TodoItem**.  
   * Verwenden Sie für ein .NET-Back-End entweder ein SQL-Tool wie SQL Server Management Studio oder einen REST-Client wie Fiddler oder Postman.  

4. Stellen Sie sicher, dass die neuen Elemente *nicht* mit dem Server synchronisiert wurden.

5. Berichtigen Sie die URL in **QSTodoService.m** nun wieder, und führen Sie die App dann erneut aus.

6. Führen Sie die Aktualisierungsbewegung aus, indem Sie die Elementliste nach unten ziehen.  
Ein Statusdrehfeld wird angezeigt.

7. Zeigen Sie die **TodoItem**-Daten erneut an. Die neuen und geänderten To-do-Elemente sollten jetzt angezeigt werden.

## <a name="summary"></a>Zusammenfassung
Um die Offlinesynchronisierungsfunktion zu unterstützen, wurde die Schnittstelle `MSSyncTable` verwendet und `MSClient.syncContext` mit einem lokalen Speicher initialisiert. In diesem Fall war der lokale Speicher eine auf Kerndaten basierende Datenbank.

Wenn Sie einen lokalen Kerndatenspeicher verwenden, müssen Sie mehrere Tabellen mit [korrekten Systemeigenschaften](#review-core-data) definieren.

Die normalen CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren und Löschen) für mobile Apps funktionieren so, als ob die App immer noch verbunden wäre. Alle Vorgänge werden jedoch nur im lokalen Speicher ausgeführt.

Beim Synchronisieren des lokalen Speichers mit dem Server haben wir die Methode **MSSyncTable.pullWithQuery** verwendet.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Offlinedatensynchronisierung in Mobile Apps]
* [Cloud Cover: Offlinesynchronisierung in Azure Mobile Services]\(Im Video geht es zwar um Mobile Services, aber die Mobile Apps-Offlinesynchronisierung funktioniert auf ähnliche Weise.\)

<!-- URLs. -->


[Erstellen einer iOS-App]: app-service-mobile-ios-get-started.md
[Offlinedatensynchronisierung in Mobile Apps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Offlinesynchronisierung in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
