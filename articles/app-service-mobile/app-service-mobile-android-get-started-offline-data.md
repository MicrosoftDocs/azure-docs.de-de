---
title: Aktivieren der Offlinesynchronisierung für Ihre mobile Azure-App (Android)
description: Erfahren Sie, wie Sie mobile App Service-Apps verwenden, um Offlinedaten in Ihrer Android-Anwendung zwischenzuspeichern und zu synchronisieren.
documentationcenter: android
author: conceptdev
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: a20c79acce8c9dc9051651a0473fd07b8e62f5de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126901"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Aktivieren der Offlinesynchronisierung für Ihre mobile Android-App
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Übersicht
In diesem Tutorial wird die Offlinesynchronisierungsfunktion von Azure Mobile Apps für Android behandelt. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (also das &mdash;Anzeigen, Hinzufügen oder Ändern von Daten&mdash;), auch wenn keine Netzwerkverbindung besteht. Änderungen werden in einer lokalen Datenbank gespeichert. Sobald das Gerät wieder online ist, werden diese Änderungen mit dem Remote-Back-End synchronisiert.

Falls Sie noch keine Erfahrung mit Azure Mobile Apps haben, sollten Sie zunächst das Tutorial [Erstellen einer Android-App]abschließen. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt die Datenzugriffs-Erweiterungspakete hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)(in englischer Sprache).

Weitere Informationen zur Offlinesynchronisierungsfunktion finden Sie im Thema [Offlinedatensynchronisierung in Azure Mobile Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Aktualisieren der App für die Unterstützung von Offlinesynchronisierung
Bei der Offlinesynchronisierung wird in eine *Synchronisierungstabelle* geschrieben und aus dieser gelesen (mit der *IMobileServiceSyncTable*-Schnittstelle). Diese ist Teil einer **SQLite**-Datenbank auf Ihrem Gerät.

Zum Übertragen von Änderungen per Push und Pull zwischen dem Gerät und Azure Mobile Services verwenden Sie einen *Synchronisierungskontext* (*MobileServiceClient.SyncContext*), den Sie mit der lokalen Datenbank initialisieren, in der Sie die Daten lokal speichern.

1. Kommentieren Sie in `TodoActivity.java` die vorhandene Definition von `mToDoTable` und entfernen Sie den Kommentar für die Version der Synchronisierungstabelle:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. Kommentieren Sie in der `onCreate`-Methode die vorhandene Initialisierung `mToDoTable` und entfernen Sie den Kommentar für diese Definition:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. Kommentieren Sie in `refreshItemsFromTable` die Definition von `results` und heben Sie den Kommentar für diese Definition auf:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Kommentieren Sie die Definition von `refreshItemsFromMobileServiceTable`.
5. Enfernen Sie den Kommentar der Definition von `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Enfernen Sie den Kommentar der Definition von `sync`:
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Testen der App
In diesem Abschnitt testen Sie das Verhalten bei aktiviertem WLAN und deaktivieren anschließend WLAN, um ein Offlineszenario zu erzeugen.

Wenn Sie Datenelemente hinzufügen, werden diese im lokalen SQLite-Speicher gespeichert, jedoch erst mit dem mobilen Dienst synchronisiert, wenn Sie auf die Schaltfläche **Aktualisieren** klicken. Bei anderen Apps müssen Daten möglicherweise zu anderen Zeitpunkten synchronisiert werden, zur Veranschaulichung muss jedoch in diesem Lernprogramm der Benutzer die Synchronisierung explizit anfordern.

Wenn Sie auf diese Schaltfläche klicken, wird eine neue Hintergrundaufgabe gestartet. Sie überträgt zuerst alle Änderungen am lokalen Speicher unter Verwendung eines Synchronisierungskontexts und ruft dann alle geänderten Daten aus Azure in die lokale Tabelle ab.

### <a name="offline-testing"></a>Offlinetest
1. Schalten Sie das Gerät oder den Simulator in den *Flugzeugmodus*. Dies erzeugt ein Offlineszenario.
2. Fügen Sie einige *ToDo*-Elemente hinzu, oder markieren Sie einige Elemente als abgeschlossen. Schalten Sie das Gerät oder den Simulator aus (oder erzwingen Sie das Schließen der App), und starten Sie das Gerät bzw. den Simulator neu. Vergewissern Sie sich, dass die Änderungen auf dem Gerät beibehalten wurden, da sie im lokalen SQLite-Speicher gespeichert sind.
3. Zeigen Sie die Inhalte der Azure-*TodoItem*-Tabelle entweder mit einem SQL-Tool wie *SQL Server Management Studio* oder einen REST-Client wie *Fiddler* oder *Postman* an. Stellen Sie sicher, dass die neuen Elemente *nicht* auf dem Server synchronisiert wurden.
   
       + Ein Node.js-Back-End finden Sie im [Azure-Portal](https://portal.azure.com/). Dort klicken Sie in Ihrem mobilen App-Back-End auf **Easy Tables** >  **TodoItem**, um den Inhalt der `TodoItem`-Tabelle anzuzeigen.
       + Zeigen Sie für einen .NET-Back-End den Inhalt der Tabelle entweder mit einem SQL-Tool wie *SQL Server Management Studio* oder einen REST-Client wie *Fiddler* oder *Postman* an.
4. Aktivieren Sie auf dem Gerät oder Simulator WLAN. Klicken Sie anschließend auf die Schaltfläche **Aktualisieren** .
5. Zeigen Sie die TodoItem-Daten erneut im Azure-Portal an. Es sollten jetzt die neuen und geänderten TodoItems angezeigt werden.

## <a name="additional-resources"></a>Weitere Ressourcen
* [Offlinedatensynchronisierung in Azure Mobile Apps]
* [Cloud Cover: Offlinesynchronisierung in Azure Mobile Services]\(Hinweis: Im Video geht es zwar um Mobile Services, aber die Offlinesynchronisierung in Azure Mobile Apps funktioniert auf ähnliche Weise.\)

<!-- URLs. -->

[Offlinedatensynchronisierung in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Erstellen einer Android-App]: app-service-mobile-android-get-started.md

[Cloud Cover: Offlinesynchronisierung in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

