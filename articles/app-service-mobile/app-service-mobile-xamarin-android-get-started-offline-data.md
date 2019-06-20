---
title: Aktivieren der Offlinesynchronisierung für Ihre Azure Mobile App (Xamarin Android)
description: Erfahren Sie, wie Sie mobile App Service-Apps verwenden, um Offlinedaten in Ihrer Xamarin Android-Anwendung zwischenzuspeichern und zu synchronisieren.
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 7e951b9f2c2fda3c63f154b5b144addcbf65aacf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127963"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Aktivieren der Offlinesynchronisierung für Ihre mobile Xamarin.Android-App

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Übersicht

In diesem Lernprogramm wird die Funktion zur Offlinesynchronisierung von Azure Mobile Apps für Xamarin.Android eingeführt. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (Anzeigen, Hinzufügen und Ändern von Daten) auch ohne Netzwerkverbindung. Änderungen werden in einer lokalen Datenbank gespeichert.
Sobald das Gerät wieder online ist, werden diese Änderungen mit dem Remotedienst synchronisiert.

In diesem Tutorial aktualisieren Sie das Clientprojekt aus dem Tutorial [Erstellen einer Xamarin Android-App] zur Unterstützung der Offlinefunktionen von Azure Mobile Apps. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt die Datenzugriffs-Erweiterungspakete hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)(in englischer Sprache).

Weitere Informationen zur Offlinesynchronisierungsfunktion finden Sie im Thema [Offlinedatensynchronisierung in Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualisieren der Client-App für die Unterstützung von Offlinefunktionen

Offlinefunktionen der mobilen Azure-App ermöglichen Ihnen die Interaktion mit einer lokalen Datenbank, wenn Sie sich in einem Offlineszenario befinden. Um diese Funktionen in der App zu verwenden, initialisieren Sie einen [SyncContext] in einem lokalen Speicher. Verweisen Sie dann über die [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet)-Schnittstelle auf Ihre Tabelle. Als lokaler Speicher auf dem Gerät wird SQLite verwendet.

1. Öffnen Sie in Visual Studio den NuGet-Paket-Manager in dem Projekt, das Sie im Tutorial [Erstellen einer Xamarin Android-App] abgeschlossen haben.  Suchen und installieren Sie das NuGet-Paket **Microsoft.Azure.Mobile.Client.SQLiteStore**.
2. Öffnen Sie die Datei „ToDoActivity.cs“, und heben Sie die Auskommentierung der `#define OFFLINE_SYNC_ENABLED`-Definition auf.
3. Drücken Sie in Visual Studio die Taste **F5** , um die Client-App erneut zu erstellen und auszuführen. Die App funktioniert genauso wie vor dem Aktivieren der Offlinesynchronisierung. Allerdings wird die lokale Datenbank jetzt mit Daten aufgefüllt, die in einem Offlineszenario verwendet werden können.

## <a name="update-sync"></a>Aktualisieren der App zur Trennung der Verbindung mit dem Back-End

In diesem Abschnitt trennen Sie die Verbindung mit Ihrem Mobile App-Back-End, um ein Offlineszenario zu simulieren. Wenn Sie Datenelemente hinzufügen, teilt Ihnen der Ausnahmehandler mit, dass sich die App im Offlinemodus befindet. In diesem Zustand werden neue Elemente im lokalen Speicher hinzugefügt. Sie werden mit dem Back-End für die mobile App synchronisiert, sobald nach dem Wiederherstellen der Verbindung ein Push ausgeführt wird.

1. Bearbeiten Sie „ToDoActivity.cs“ im freigegebenen Projekt. Ändern Sie die Variable **applicationURL** so, dass Sie auf eine ungültige URL verweist:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Sie können auch Offlineverhalten demonstrieren, indem Sie WLAN und Mobilfunknetze auf dem Gerät deaktivieren oder den Flugzeugmodus verwenden.
2. Drücken Sie **F5** , um die Anwendung zu erstellen und auszuführen. Beachten Sie, dass die Synchronisierung bei der Aktualisierung fehlgeschlagen ist, als die App gestartet wurde.
3. Geben Sie neue Elemente ein. Beachten Sie, dass der Pushvorgang bei jedem Klick auf **Speichern** mit dem Status [CancelledByNetworkError] fehlschlägt. Die neuen Todo-Elemente sind jedoch im lokalen Speicher vorhanden und bleiben dort, bis sie per Push zum Back-End für die mobile App übertragen werden können.  Wenn Sie in einer Produktions-App diese Ausnahmen unterdrücken, verhält sich die Client-App, als ob sie immer noch mit dem Back-End für die mobile App verbunden wäre.
4. Schließen Sie die App und starten Sie sie erneut, um zu überprüfen, ob die neuen Elemente dauerhaft im lokalen Speicher gespeichert wurden.
5. (Optional:) Öffnen Sie den **Server-Explorer**in Visual Studio. Navigieren Sie in **Azure**->**SQL-Datenbanken** zu Ihrer Datenbank. Klicken Sie mit der rechten Maustaste auf Ihre Datenbank, und wählen Sie **In SQL Server-Objekt-Explorer öffnen**aus. Jetzt können nach Ihrer SQL-Datenbanktabelle und seinen Inhalten suchen. Stellen Sie sicher, dass die Daten in der Back-End-Datenbank nicht geändert wurden.
6. (Optional) Verwenden Sie ein REST-Tool wie Fiddler oder Postman, um Ihr mobiles Back-End mit einer GET-Abfrage in Form von `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`abzufragen.

## <a name="update-online-app"></a>Aktualisieren der App zum erneuten Verbinden des mobilen App-Back-Ends

In diesem Abschnitt verbinden Sie die App erneut mit dem mobilen App-Back-End. Beim ersten Ausführen der Anwendung ruft der `OnCreate`-Ereignishandler `OnRefreshItemsSelected` auf. Mit dieser Methode wird `SyncAsync` aufgerufen, um den lokalen Speicher mit der Back-End-Datenbank zu synchronisieren.

1. Öffnen Sie „ToDoActivity.cs“ im freigegebenen Projekt, und machen Sie Ihre Änderung der **applicationURL**-Eigenschaft rückgängig.
2. Drücken Sie die Taste **F5** , um die App erneut zu erstellen und auszuführen. Die App synchronisiert die lokalen Änderungen mit dem Azure Mobile App-Back-End mithilfe von Push- und Pullvorgängen, wenn die `OnRefreshItemsSelected`-Methode ausgeführt wird.
3. (Optional) Zeigen Sie die aktualisierten Daten mithilfe von SQL Server-Objekt-Explorer oder einem REST-Tool wie Fiddler an. Beachten Sie, dass die Daten zwischen der Azure Mobile App-Back-End-Datenbank und dem lokalen Speicher synchronisiert wurden.
4. Klicken Sie in der App auf das Kontrollkästchen neben einigen Elementen, um sie im lokalen Speicher abzuschließen.

   `CheckItem` ruft `SyncAsync` auf, um jedes abgeschlossene Element mit dem Mobile App-Back-End zu synchronisieren. `SyncAsync` ruft sowohl Push- als auch Pullvorgänge auf. **Bei jeder Ausführung eines Pullvorgangs gegen eine Tabelle, die der Client geändert hat, wird immer automatisch ein Pushvorgang**. Dadurch wird sichergestellt, dass alle Tabellen im lokalen Speicher und die Beziehungen konsistent bleiben. Dieses Verhalten kann zu einem unerwarteten Pushvorgang führen. Weitere Informationen zu diesem Verhalten finden Sie unter [Offlinedatensynchronisierung in Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Überprüfen des Clientcodes für die Synchronisierung

Das Xamarin-Clientprojekt, das Sie heruntergeladen haben, nachdem Sie das Lernprogramm [Erstellen einer Xamarin Android-App] abgeschlossen haben, enthält bereits Code zur Unterstützung der Offlinesynchronisierung mithilfe einer lokalen SQLite-Datenbank. Dies ist eine kurze Übersicht darüber, was bereits im Code des Lernprogramms enthalten ist. Eine grundlegende Übersicht über die Funktion finden Sie unter [Offlinedatensynchronisierung in Azure Mobile Apps].

* Bevor Tabellenvorgänge durchgeführt werden können, muss der lokale Speicher initialisiert werden. Die lokale Datenbank wird initialisiert, wenn `ToDoActivity.InitLocalStoreAsync()` von `ToDoActivity.OnCreate()` ausgeführt wird. Mit dieser Methode wird eine lokale SQLite-Datenbank mit der `MobileServiceSQLiteStore`-Klasse erstellt, die vom Azure Mobile Apps-Client-SDK bereitgestellt wird.

    Die `DefineTable`-Methode erstellt eine Tabelle im lokalen Speicher, die mit den Feldern im bereitgestellten Typ übereinstimmt (in diesem Fall `ToDoItem`). Der Typ muss nicht alle Spalten der Remotedatenbank enthalten. Es ist möglich, nur eine Teilmenge der Spalten zu speichern.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see https://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* Das `toDoTable`-Mitglied von `ToDoActivity` ist vom Typ `IMobileServiceSyncTable` und nicht vom Typ `IMobileServiceTable`. IMobileServiceSyncTable leitet alle Erstellungs-, Lese-, Aktualisierungs- und Löschtabellenvorgänge (CRUD) an die lokale Datenbank.

    Sie legen fest, wann Änderungen per Pushvorgang durch Aufrufen von `IMobileServiceSyncContext.PushAsync()` an das Azure Mobile App-Back-End übertragen werden. Der Synchronisierungskontext hilft dabei, Tabellenbeziehungen durch das Nachverfolgen und Übertragen von Änderungen in allen Tabellen beizubehalten, die eine Clientanwendung geändert hat, wenn `PushAsync` aufgerufen wird.

    Der bereitgestellte Code ruft `ToDoActivity.SyncAsync()` zum Synchronisieren ab, wenn die TodoItem-Liste aktualisiert wird oder ein TodoItem hinzugefügt wird oder abgeschlossen ist. Der Code wird nach jeder lokalen Änderung synchronisiert.

    Im bereitgestellten Code werden alle Datensätze in der `TodoItem`-Remotetabelle abgefragt, es ist aber auch möglich, Datensätze durch Übergeben einer Abfrage-ID und Abfrage an `PushAsync` zu filtern. Weitere Informationen finden Sie unter *Offlinedatensynchronisierung in Azure Mobile Apps* im Abschnitt [Offlinedatensynchronisierung in Azure Mobile Apps].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Weitere Ressourcen

* [Offlinedatensynchronisierung in Azure Mobile Apps]
* [Verwenden des verwalteten Clients für Azure Mobile Apps][8]

<!-- URLs. -->
[Erstellen einer Xamarin Android-App]: ./app-service-mobile-xamarin-android-get-started.md
[Offlinedatensynchronisierung in Azure Mobile Apps]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Erstellen einer Xamarin Android-App]: app-service-mobile-xamarin-android-get-started.md
[Offlinedatensynchronisierung in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
