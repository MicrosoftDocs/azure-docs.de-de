---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240270"
---
1. Öffnen Sie das heruntergeladene Clientprojekt in Xcode.

2. Wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und navigieren Sie zu der mobilen App, die Sie erstellt haben. Suchen Sie auf dem Blatt `Overview` nach der URL, die den öffentlichen Endpunkt für Ihre mobile App darstellt. Beispiel: der Sitename für meinen App-Namen „test123“ wird https://test123.azurewebsites.net.

3. Für ein Swift-Projekt öffnen Sie die Datei `ToDoTableViewController.swift` in diesem Ordner: „ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift“. Der Anwendungsname lautet `ZUMOAPPNAME`.

4. Ersetzen Sie in der `viewDidLoad()`-Methode den Parameter `ZUMOAPPURL` durch den oben stehenden öffentlichen Endpunkt.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    wird zu
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Für Objective-C-Projekt öffnen Sie die Datei `QSTodoService.m` in diesem Ordner: „ZUMOAPPNAME/ZUMOAPPNAME“. Der Anwendungsname lautet `ZUMOAPPNAME`.

6. Ersetzen Sie in der `init`-Methode den Parameter `ZUMOAPPURL` durch den oben stehenden öffentlichen Endpunkt.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    wird zu
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Klicken Sie auf die Schaltfläche **Ausführen** , um das Projekt zu erstellen und die App im iOS-Simulator zu starten.

8. Klicken Sie in der App auf das Plussymbol ( **+** ), geben Sie einen sinnvollen Text ein, wie z. B. *Tutorial abschließen*, und klicken Sie dann auf die Schaltfläche „Speichern“. Damit wird eine POST-Anforderung an das Azure-Back-End gesendet, das Sie zuvor bereitgestellt haben. Das Back-End fügt Daten aus der Anforderung in die TodoItem-SQL-Tabelle ein und gibt Informationen über die neu gespeicherten Elemente an die mobile App zurück. Die mobile App zeigt diese Daten in der Liste an.

   ![Schnellstart-App unter iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
