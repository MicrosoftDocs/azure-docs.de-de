---
title: Aktivieren der Offlinesynchronisierung bei einer App für die universelle Windows-Plattform (UWP) mit Mobile Apps | Microsoft Docs
description: Erfahren Sie, wie Sie mit einer Azure Mobile App Offlinedaten in Ihrer App für die universelle Windows-Plattform (UWP) zwischenspeichern und synchronisieren.
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 69ee9e7101a2b7337e1e42ff5ae09954fbfd50b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128048"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Aktivieren der Offlinesynchronisierung für Ihre Windows-App
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Übersicht
In diesem Tutorial wird das Hinzufügen der Offlineunterstützung zu einer App für die universelle Windows-Plattform (UWP) mithilfe eines Azure Mobile App-Back-Ends erläutert. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (Anzeigen, Hinzufügen und Ändern von Daten) auch ohne Netzwerkverbindung. Änderungen werden in einer lokalen Datenbank gespeichert. Sobald das Gerät wieder online ist, werden diese Änderungen mit dem Remote-Back-End synchronisiert.

In diesem Tutorial aktualisieren Sie das UWP-App-Projekt aus dem Tutorial [Erstellen einer Windows-App], damit die Offlinefunktionen von Azure Mobile Apps unterstützt werden. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt die Datenzugriffs-Erweiterungspakete hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)(in englischer Sprache).

Weitere Informationen zur Offlinesynchronisierungsfunktion finden Sie im Thema [Offlinedatensynchronisierung in Azure Mobile Apps].

## <a name="requirements"></a>Requirements (Anforderungen)  
Für dieses Tutorial gelten die folgenden Voraussetzungen:

* Visual Studio 2013 unter Windows 8.1 oder höher.
* Durchführung des Lernprogramms [Erstellen einer Windows-App][Erstellen einer Windows-App] \(in englischer Sprache).
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite for Universal Windows Platform Development](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualisieren der Client-App für die Unterstützung von Offlinefunktionen
Offlinefunktionen der mobilen Azure-App ermöglichen Ihnen die Interaktion mit einer lokalen Datenbank, wenn Sie sich in einem Offlineszenario befinden. Um diese Funktionen in der App zu verwenden, initialisieren Sie [SyncContext][synccontext] in einem lokalen Speicher. Erstellen Sie dann für die Tabelle einen Verweis über die Schnittstelle [IMobileServiceSyncTable][IMobileServiceSyncTable] . Als lokaler Speicher auf dem Gerät wird SQLite verwendet.

1. Installieren Sie die [SQLite-Laufzeit für die Universelle Windows-Plattform](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. Öffnen Sie in Visual Studio den NuGet-Paket-Manager für das UWP-App-Projekt, das Sie im Tutorial [Erstellen einer Windows-App] abgeschlossen haben.
    Suchen und installieren Sie das NuGet-Paket **Microsoft.Azure.Mobile.Client.SQLiteStore**.
3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise** > **Verweis hinzufügen...** >**Universal Windows**>**Erweiterungen**, und aktivieren Sie dann sowohl **SQLite for Universal Windows Platform** (SQLite für die universelle Windows-Plattform) als auch **Visual C++ 2015 Runtime for Universal Windows Platform apps** (Visual C++ 2015-Laufzeit für Apps der universellen Windows-Plattform).

    ![Referenz zum Hinzufügen von SQLite für UWP][1]
4. Öffnen Sie die Datei „MainPage.xaml.cs“, und heben Sie die Auskommentierung der Definition `#define OFFLINE_SYNC_ENABLED` auf.
5. Drücken Sie in Visual Studio die Taste **F5** , um die Client-App erneut zu erstellen und auszuführen. Die App funktioniert genauso wie vor dem Aktivieren der Offlinesynchronisierung. Allerdings wird die lokale Datenbank jetzt mit Daten aufgefüllt, die in einem Offlineszenario verwendet werden können.

## <a name="update-sync"></a>Aktualisieren der App zur Trennung der Verbindung mit dem Back-End
In diesem Abschnitt trennen Sie die Verbindung mit Ihrem Mobile App-Back-End, um ein Offlineszenario zu simulieren. Wenn Sie Datenelemente hinzufügen, teilt Ihnen der Ausnahmehandler mit, dass sich die App im Offlinemodus befindet. In diesem Zustand werden neue Elemente im lokalen Speicher hinzugefügt. Sie werden mit dem Back-End für die mobile App synchronisiert, sobald nach dem Wiederherstellen der Verbindung der nächste Push ausgeführt wird.

1. Bearbeiten Sie "App.xaml.cs" im freigegebenen Projekt. Kommentieren Sie die Initialisierung von **MobileServiceClient** aus, und fügen Sie die folgende Zeile hinzu, in der eine ungültige mobile App-URL verwendet wird:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Sie können auch Offlineverhalten demonstrieren, indem Sie WLAN und Mobilfunknetze auf dem Gerät deaktivieren oder den Flugzeugmodus verwenden.
2. Drücken Sie **F5** , um die Anwendung zu erstellen und auszuführen. Beachten Sie, dass die Synchronisierung bei der Aktualisierung fehlgeschlagen ist, als die App gestartet wurde.
3. Geben Sie neue Elemente ein. Beachten Sie, dass der Pushvorgang bei jedem Klick auf **Speichern** mit dem Status [CancelledByNetworkError] fehlschlägt. Die neuen Todo-Elemente sind jedoch im lokalen Speicher vorhanden und bleiben dort, bis sie per Push zum Back-End für die mobile App übertragen werden können.  Wenn Sie in einer Produktions-App diese Ausnahmen unterdrücken, verhält sich die Client-App, als ob sie immer noch mit dem Back-End für die mobile App verbunden wäre.
4. Schließen Sie die App und starten Sie sie erneut, um zu überprüfen, ob die neuen Elemente dauerhaft im lokalen Speicher gespeichert wurden.
5. (Optional:) Öffnen Sie den **Server-Explorer**in Visual Studio. Navigieren Sie in **Azure**->**SQL-Datenbanken** zu Ihrer Datenbank. Klicken Sie mit der rechten Maustaste auf Ihre Datenbank, und wählen Sie **In SQL Server-Objekt-Explorer öffnen**aus. Jetzt können nach Ihrer SQL-Datenbanktabelle und seinen Inhalten suchen. Stellen Sie sicher, dass die Daten in der Back-End-Datenbank nicht geändert wurden.
6. (Optional) Verwenden Sie ein REST-Tool wie Fiddler oder Postman, um Ihr mobiles Back-End mit einer GET-Abfrage in Form von `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`abzufragen.

## <a name="update-online-app"></a>Aktualisieren der App zum erneuten Verbinden des mobilen App-Back-Ends
In diesem Abschnitt verbinden Sie die App erneut mit dem mobilen App-Back-End. Diese Änderungen simulieren eine erneute Herstellung der Netzwerkverbindung für die App.

Beim ersten Ausführen der Anwendung ruft der `OnNavigatedTo`-Ereignishandler `InitLocalStoreAsync` auf. Mit dieser Methode wird wiederum `SyncAsync` aufgerufen, um den lokalen Speicher mit der Back-End-Datenbank zu synchronisieren. Die App versucht, die Synchronisierung beim Starten durchzuführen.

1. Öffnen Sie „App.xaml.cs“ im freigegebenen Projekt, und heben Sie die Auskommentierung der vorherigen Initialisierung von `MobileServiceClient` auf, um die korrekte URL für die mobile App zu verwenden.
2. Drücken Sie die Taste **F5** , um die App erneut zu erstellen und auszuführen. Die App synchronisiert die lokalen Änderungen mit dem Azure Mobile App-Back-End mithilfe von Push- und Pullvorgängen, wenn der `OnNavigatedTo`-Ereignishandler ausgeführt wird.
3. (Optional) Zeigen Sie die aktualisierten Daten mithilfe von SQL Server-Objekt-Explorer oder einem REST-Tool wie Fiddler an. Beachten Sie, dass die Daten zwischen der Azure Mobile App-Back-End-Datenbank und dem lokalen Speicher synchronisiert wurden.
4. Klicken Sie in der App auf das Kontrollkästchen neben einigen Elementen, um sie im lokalen Speicher abzuschließen.

   `UpdateCheckedTodoItem` ruft `SyncAsync` auf, um jedes abgeschlossene Element mit dem Mobile App-Back-End zu synchronisieren. `SyncAsync` ruft sowohl Push- als auch Pullvorgänge auf. **Bei jeder Ausführung eines Pullvorgangs für eine Tabelle, die der Client geändert hat, wird aber immer automatisch ein Pushvorgang ausgeführt**. Durch dieses Verhalten wird sichergestellt, dass alle Tabellen im lokalen Speicher und die Beziehungen konsistent bleiben. Dieses Verhalten kann zu einem unerwarteten Pushvorgang führen.  Weitere Informationen zu diesem Verhalten finden Sie unter [Offlinedatensynchronisierung in Azure Mobile Apps].

## <a name="api-summary"></a>Zusammenfassung für die API
Zur Unterstützung der Offlinefunktionen mobiler Dienste haben Sie die Schnittstelle [IMobileServiceSyncTable] verwendet und [MobileServiceClient.SyncContext][synccontext] mit einer lokalen SQLite-Datenbank initialisiert. In einer Offlinesituation funktionieren die normalen CRUD-Operationen für Mobile Apps so, als ob die App immer noch verbunden wäre. Alle Operationen werden jedoch nur im lokalen Speicher ausgeführt. Zur Synchronisierung des lokalen Speichers mit dem Server werden die folgenden Methoden verwendet:

* **[PushAsync]** Da diese Methode [IMobileServicesSyncContext] angehört, werden Änderungen an allen Tabellen per Pushvorgang an das Back-End übertragen. Nur Datensätze mit lokalen Änderungen werden an den Server gesendet.
* **[PullAsync]** Ein Pullvorgang wird von einem [IMobileServiceSyncTable]-Element aus gestartet. Wenn verfolgte Änderungen in der Tabelle vorhanden sind, wird ein impliziter Pushvorgang ausgeführt, um sicherzustellen, dass alle Tabellen im lokalen Speicher gemeinsam mit den Beziehungen konsistent bleiben. Der *pushOtherTables* -Parameter steuert, ob bei einem impliziten Pushvorgang auch andere Tabellen per Push übertragen werden. Der *query*-Parameter akzeptiert [IMobileServiceTableQuery<T>][IMobileServiceTableQuery] oder eine OData-Abfragezeichenfolge, um die zurückgegebenen Daten zu filtern. Der *queryId* -Parameter dient zum Definieren der inkrementellen Synchronisierung. Weitere Informationen finden Sie unter [Synchronisierung von Offlinedaten in Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** Ihre App sollte diese Methode in regelmäßigen Abständen aufrufen, um veraltete Daten endgültig aus dem lokalen Speicher zu löschen. Verwenden Sie den *force* -Parameter, wenn Sie noch nicht synchronisierte Änderungen löschen müssen.

Weitere Informationen zu diesen Konzepten finden Sie unter [Synchronisierung von Offlinedaten in Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Weitere Informationen
In den folgenden Themen finden Sie weitere Hintergrundinformationen zum Offlinesynchronisierungsfeature von Mobile Apps:

* [Offlinedatensynchronisierung in Azure Mobile Apps]
* [Verwenden des verwalteten Clients für Azure Mobile Apps][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Offlinedatensynchronisierung in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Erstellen einer Windows-App]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
