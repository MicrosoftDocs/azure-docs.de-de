---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240227"
---
1. Navigieren Sie zu der Projektmappendatei im Clientprojekt (.sln), und öffnen Sie sie mittels Visual Studio.

2. Wählen Sie in Visual Studio in der Dropdownliste neben dem Startpfeil die Plattform für die Projektmappe (Android, iOS oder Windows) aus. Wählen Sie ein bestimmtes Bereitstellungsgerät oder einen Emulator aus, indem Sie auf die Dropdownliste mit dem grünen Pfeil klicken. Sie können die standardmäßig ausgewählte Android-Plattform und den Ripple-Emulator verwenden. In komplexeren Tutorials (etwa mit Pushbenachrichtigungen) muss ein unterstütztes Gerät oder ein unterstützter Emulator ausgewählt werden.

3. Öffnen Sie die Datei `ToDoActivity.java` in diesem Ordner: „ZUMOAPPNAME/app/src/main/java/com/example/zumoappname“. Der Anwendungsname lautet `ZUMOAPPNAME`.

4. Wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und navigieren Sie zu der mobilen App, die Sie erstellt haben. Suchen Sie auf dem Blatt `Overview` nach der URL, die den öffentlichen Endpunkt für Ihre mobile App darstellt. Beispiel: der Sitename für meinen App-Namen „test123“ wird https://test123.azurewebsites.net.

5. Wechseln Sie zu der Datei `index.js` in „ZUMOAPPNAME/www/js/index.js“, und ersetzen Sie in der `onDeviceReady()`-Methode den Parameter `ZUMOAPPURL` durch den oben stehenden öffentlichen Endpunkt.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    wird zu
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Drücken Sie F5, oder klicken Sie auf den grünen Pfeil, um Ihre Cordova-App zu erstellen und auszuführen. Sollte im Emulator ein Sicherheitsdialogfeld mit einer Netzwerkzugriffsanforderung angezeigt werden, lassen Sie den Zugriff zu.

7. Nachdem die App auf dem Gerät oder im Emulator gestartet wurde, geben Sie unter **Enter new text** (Neuen Text eingeben) einen aussagekräftigen Text ein (etwa *Tutorial abschließen*), und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

Das Back-End fügt Daten aus der Anforderung in die TodoItem-Tabelle in SQL-Datenbank ein und gibt Informationen zu den neu gespeicherten Elementen an die mobile App zurück. Die mobile App zeigt diese Daten in der Liste an.

Die Schritte 3 bis 5 können für andere Plattformen wiederholt werden.