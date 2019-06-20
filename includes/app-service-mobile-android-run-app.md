---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240313"
---
1. Öffnen Sie das Projekt in **Android Studio** mithilfe von **Import project (Eclipse ADT, Gradle, etc.)** (Projekt importieren (Eclipse ADT, Gradle usw.)). Achten Sie auf diese wichtige Importauswahl, um JDK-Fehler zu vermeiden.

2. Öffnen Sie die Datei `ToDoActivity.java` in diesem Ordner: „ZUMOAPPNAME/app/src/main/java/com/example/zumoappname“. Der Anwendungsname lautet `ZUMOAPPNAME`.

3. Wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und navigieren Sie zu der mobilen App, die Sie erstellt haben. Suchen Sie auf dem Blatt `Overview` nach der URL, die den öffentlichen Endpunkt für Ihre mobile App darstellt. Beispiel: der Sitename für meinen App-Namen „test123“ wird https://test123.azurewebsites.net.

4. Ersetzen Sie in der `onCreate()`-Methode den Parameter `ZUMOAPPURL` durch den oben stehenden öffentlichen Endpunkt.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    wird zu
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Klicken Sie auf die Schaltfläche **Run 'app'** , um das Projekt zu erstellen und die App im Android-Simulator zu starten.

4. Geben Sie in der App einen sinnvollen Text wie z.B. *Tutorial fertigstellen* ein, und klicken Sie dann auf die Schaltfläche „Hinzufügen“. Damit wird eine POST-Anforderung an das Azure-Back-End gesendet, das Sie zuvor bereitgestellt haben. Das Back-End fügt Daten aus der Anforderung in die TodoItem-SQL-Tabelle ein und gibt Informationen über die neu gespeicherten Elemente an die mobile App zurück. Die mobile App zeigt diese Daten in der Liste an.
    ![Schnellstart für Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)