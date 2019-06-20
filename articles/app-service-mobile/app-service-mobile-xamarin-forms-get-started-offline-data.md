---
title: Aktivieren der Offlinesynchronisierung für Ihre Azure Mobile App (Xamarin.Forms) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mobile App Service-Apps verwenden, um Offlinedaten in Ihrer Xamarin.Forms-Anwendung zwischenzuspeichern und zu synchronisieren.
documentationcenter: xamarin
author: conceptdev
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: crdun
ms.openlocfilehash: 506c59ca24aeafbac59b1508bb78142051302765
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127878"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Aktivieren der Offlinesynchronisierung für Ihre mobile Xamarin.Forms-App
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Übersicht
In diesem Tutorial wird die Funktion zur Offlinesynchronisierung von Azure Mobile Apps für Xamarin.Forms eingeführt. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (Anzeigen, Hinzufügen und Ändern von Daten) auch ohne Netzwerkverbindung. Änderungen werden in einer lokalen Datenbank gespeichert. Sobald das Gerät wieder online ist, werden diese Änderungen mit dem Remotedienst synchronisiert.

Dieses Tutorial basiert auf der Xamarin.Forms-Schnellstartlösung für Mobile Apps, die Sie beim Durcharbeiten des Tutorials [Erstellen einer Xamarin iOS-App] erstellen. Die Schnellstartlösung für Xamarin.Forms enthält den Code zur Unterstützung der Offlinesynchronisierung, die lediglich aktiviert werden muss. In diesem Tutorial aktualisieren Sie die Schnellstartlösung, um die Offlinefunktionen von Azure Mobile Apps zu aktivieren. Außerdem gehen wir speziell auf den offlinespezifischen Code in der App ein. Wenn Sie die heruntergeladene Schnellstartlösung nicht verwenden, müssen Sie Ihrem Projekt die Datenzugriffs-Erweiterungspakete hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Arbeiten Sie mit dem .NET-Back-End-Server SDK für Azure Mobile Apps][1].

Weitere Informationen zur Offlinesynchronisierungsfunktion finden Sie im Thema [Offlinedatensynchronisierung in Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Aktivieren der Funktionen für die Offlinesynchronisierung in der Schnellstartlösung
Der Code für die Offlinesynchronisierung wird mit C#-Präprozessordirektiven in das Projekt eingefügt. Wenn das Symbol **OFFLINE\_SYNC\_ENABLED** definiert wird, werden diese Codepfade in den Build eingefügt. Für Windows-Apps müssen Sie auch die SQLite-Plattform installieren.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten für Projektmappe...** . Suchen Sie anschließend nach dem NuGet-Paket **Microsoft.Azure.Mobile.Client.SQLiteStore**, und installieren Sie es für alle Projekte der Projektmappe.
2. Öffnen Sie im Projektmappen-Explorer die Datei „TodoItemManager.cs“ aus dem Projekt mit **Portable** im Namen, also das Projekt „Portable Klassenbibliothek“. Heben Sie anschließend die Auskommentierung der folgenden Präprozessordirektive auf:

        #define OFFLINE_SYNC_ENABLED
3. (Optional) Um Windows-Geräte zu unterstützen, können Sie eines der folgenden SQLite-Laufzeitpakete installieren:

   * **Windows 8.1-Runtime:** Installieren Sie [SQLite für Windows 8.1][3].
   * **Windows Phone 8.1:** Installieren Sie [SQLite für Windows Phone 8.1][4].
   * **Universelle Windows-Plattform:** Installieren Sie [SQLite für die universelle Windows-Plattform][5].

     Obwohl die Schnellstartlösung kein universelles Windows-Projekt enthält, wird die universelle Windows-Plattform mit Xamarin.Forms unterstützt.
4. (Optional) Klicken Sie in jedem Windows-App-Projekt mit der rechten Maustaste auf **Verweise** > **Verweis hinzufügen...** , und erweitern Sie dann den Ordner **Windows** > **Erweiterungen**.
    Aktivieren Sie das entsprechende **SQLite für Windows** SDK zusammen mit dem **Visual C++ 2013 Runtime für Windows** SDK.
    Die Namen der SQLite-SDKs unterscheiden sich bei den einzelnen Windows-Plattformen.

## <a name="review-the-client-sync-code"></a>Überprüfen des Clientcodes für die Synchronisierung
Hier erhalten Sie eine kurze Übersicht darüber, was bereits im Code des Tutorials in den `#if OFFLINE_SYNC_ENABLED` -Direktiven enthalten ist. Die Funktionen der Offlinesynchronisierung befinden sich in der Projektdatei „TodoItemManager.cs“ im Projekt „Portable Klassenbibliothek“. Eine grundlegende Übersicht über die Funktion finden Sie unter [Offlinedatensynchronisierung in Azure Mobile Apps][2].

* Bevor Tabellenvorgänge durchgeführt werden können, muss der lokale Speicher initialisiert werden. Die lokale Speicherdatenbank wird im **TodoItemManager** -Klassenkonstruktor mit dem folgenden Code initialisiert:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Dieser Code erstellt eine neue lokale SQLite-Datenbank mit der **MobileServiceSQLiteStore**-Klasse.

    Die **DefineTable**-Methode erstellt eine Tabelle im lokalen Speicher, die mit den Feldern im bereitgestellten Typ übereinstimmt.  Der Typ muss nicht alle Spalten der Remotedatenbank enthalten. Es ist möglich, eine Teilmenge der Spalten zu speichern.
* Das Feld **todoTable** in **TodoItemManager** hat den Typ **IMobileServiceSyncTable** und nicht den Typ **IMobileServiceTable**. Diese Klasse verwendet die lokale Datenbank für alle Tabellenvorgänge vom Typ Erstellen, Lesen, Aktualisieren und Löschen (CRUD). Sie entscheiden, wann diese Änderungen auf das mobile App-Back-End übertragen werden, indem Sie **PushAsync** im **IMobileServiceSyncContext** aufrufen. Der Synchronisierungskontext hilft dabei, Tabellenbeziehungen durch das Nachverfolgen und Übertragen von Änderungen in allen Tabellen beizubehalten, die eine Clientanwendung geändert hat, wenn **PushAsync** aufgerufen wird.

    Die folgende **SyncAsync** -Methode wird für die Synchronisierung mit dem mobilen App-Back-End aufgerufen:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    In diesem Beispiel wird die einfache Fehlerbehandlung mit dem Standardhandler für die Synchronisierung verwendet. In einer echten Anwendung werden die verschiedenen Fehler, z.B. Netzwerkbedingungen und Serverkonflikte, mit einer benutzerdefinierten **IMobileServiceSyncHandler**-Implementierung behandelt.

## <a name="offline-sync-considerations"></a>Aspekte der Offlinesynchronisierung
Im Beispiel wird die **SyncAsync**-Methode nur beim Starten und bei einer Anforderung einer Synchronisierung aufgerufen.  Öffnen Sie zum Auslösen der Synchronisierung in einer Android- oder iOS-App die Elementliste. Verwenden Sie für Windows die Schaltfläche **Synchronisierung**. In einer echten Anwendung können Sie dies auch so einrichten, dass diese Synchronisierung ausgelöst wird, wenn sich der Netzwerkstatus ändert.

Bei einem Pullvorgang für eine Tabelle mit ausstehenden lokalen Updates, die durch den Kontext verfolgt werden, löst dieser Pullvorgang automatisch einen vorherigen Kontextpush aus. Beim Aktualisieren, Hinzufügen und Abschließen von Elementen in diesem Beispiel können Sie den expliziten **PushAsync**-Aufruf weglassen.

Im bereitgestellten Code werden alle Datensätze in der TodoItem-Remotetabelle abgefragt, es ist aber auch möglich, Datensätze durch Übergeben einer Abfrage-ID und Abfrage an **PushAsync**zu filtern. Weitere Informationen finden Sie unter [Offlinedatensynchronisierung in Azure Mobile Apps][2] im Abschnitt *Inkrementelle Synchronisierung*.

## <a name="run-the-client-app"></a>Ausführen der Client-App
Nach dem Aktivieren der Offlinesynchronisierung führen Sie die Clientanwendung mindestens einmal auf jeder Plattform aus, um die lokale Speicherdatenbank aufzufüllen. Weiter unten simulieren Sie ein Offlineszenario und ändern die Daten im lokalen Speicher, während die App offline ist.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Aktualisieren des Synchronisierungsverhaltens der Client-App
In diesem Abschnitt ändern Sie das Clientprojekt, um ein Offlineszenario mithilfe einer ungültigen Anwendungs-URL für Ihr Back-End zu simulieren. Alternativ können Sie die Netzwerkverbindungen deaktivieren, indem Sie das Gerät in den Flugmodus versetzen.  Beim Hinzufügen oder Ändern von Datenelementen werden diese Änderungen im lokalen Speicher gespeichert, aber nicht mit dem Back-End-Datenspeicher synchronisiert, bis die Verbindung wiederhergestellt ist.

1. Öffnen Sie im Projektmappen-Explorer die Projektdatei „Constants.cs“ des Projekts **Portable**, und ändern Sie den Wert von `ApplicationURL` so, dass er auf eine ungültige URL zeigt:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Öffnen Sie die Datei „TodoItemManager.cs“ im Projekt **Portable**, und fügen Sie dem **try...catch**-Block in **SyncAsync** dann ein **catch**-Element für die **Exception**-Basisklasse hinzu. Mit diesem **catch** -Block wird die Ausnahmemeldung wie folgt in die Konsole geschrieben:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Erstellen Sie die Client-App und führen Sie sie aus.  Fügen Sie einige neue Elemente hinzu. Beachten Sie, dass in der Konsole für jede versuchte Synchronisierung mit dem Back-End eine Ausnahme protokolliert wird. Diese neuen Elemente sind nur im lokalen Speicher vorhanden, bis sie per Push an das mobile Back-End übertragen werden können. Die Client-App verhält sich so, als ob eine Verbindung mit dem mobilen Back-End vorhanden wäre, und unterstützt alle Vorgänge zum Erstellen, Lesen, Aktualisieren und Löschen (CRUD-Vorgänge).
4. Schließen Sie die App und starten Sie sie erneut, um zu überprüfen, ob die neuen Elemente dauerhaft im lokalen Speicher gespeichert wurden.
5. (Optional) Verwenden Sie Visual Studio zum Anzeigen der Azure SQL-Datenbanktabelle, um festzustellen, ob sich die Daten in der Back-End-Datenbank nicht geändert haben.

    Öffnen Sie den **Server-Explorer**in Visual Studio. Navigieren Sie in **Azure**->**SQL-Datenbanken** zu Ihrer Datenbank. Klicken Sie mit der rechten Maustaste auf Ihre Datenbank, und wählen Sie **In SQL Server-Objekt-Explorer öffnen**aus. Jetzt können nach Ihrer SQL-Datenbanktabelle und seinen Inhalten suchen.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Aktualisieren der Client-App, um erneut eine Verbindung mit dem mobilen Back-End herzustellen
In diesem Abschnitt stellen Sie erneut eine Verbindung zwischen der App und dem mobilen Back-End her, um zu simulieren, dass die App wieder im Onlinezustand ist. Wenn Sie die Aktualisierungsbewegung durchführen, werden Daten mit Ihrem mobilen Back-End synchronisiert.

1. Öffnen Sie „Constants.cs“ erneut. Korrigieren Sie die `applicationURL` , sodass sie auf die richtige URL verweist.
2. Erstellen Sie die Client-App erneut, und führen Sie sie aus. Die App versucht, sich nach dem Start mit dem mobilen App-Back-End zu synchronisieren. Vergewissern Sie sich, dass in der Debugkonsole keine Ausnahmen protokolliert werden.
3. (Optional) Zeigen Sie die aktualisierten Daten mithilfe von SQL Server-Objekt-Explorer oder einem REST-Tool wie Fiddler oder [Postman][6] an. Beachten Sie, dass die Daten zwischen der Back-End-Datenbank und dem lokalen Speicher synchronisiert wurden.

    Beachten Sie, dass die Daten zwischen der Datenbank und dem lokalen Speicher synchronisiert wurden und die Elemente enthalten, die Sie hinzugefügt haben, während Ihre App von der Verbindung getrennt war.

## <a name="additional-resources"></a>Weitere Ressourcen
* [Offlinedatensynchronisierung in Azure Mobile Apps][2]
* [Verwenden des verwalteten Clients für Azure Mobile Apps][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
