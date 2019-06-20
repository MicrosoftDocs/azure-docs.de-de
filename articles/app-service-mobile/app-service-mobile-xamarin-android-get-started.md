---
title: Erste Schritte mit Azure Mobile Apps für Xamarin.Android-Apps
description: Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile Apps für die Xamarin Android-Entwicklung.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: f3e8ca4f9736dffe4928fc8920b0890dff87367b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236028"
---
# <a name="create-a-xamarinandroid-app"></a>Erstellen einer Xamarin.Android-App
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Übersicht
In diesem Tutorial erfahren Sie, wie Sie einer Xamarin.Android-App einen cloudbasierten Back-End-Dienst hinzufügen. Weitere Informationen finden Sie unter [Was sind Mobile Apps?](app-service-mobile-value-prop.md)

Screenshot der fertigen App:

![][0]

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Apps-Lernprogramme für Xamarin Android-Apps.

## <a name="prerequisites"></a>Voraussetzungen
Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto haben, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio mit Xamarin. Anweisungen finden Sie unter [Setup und Installation für Visual Studio und Xamarin](/visualstudio/cross-platform/setup-and-install) .

## <a name="create-an-azure-mobile-app-backend"></a>Erstellen eines Azure Mobile App-Back-Ends
Führen Sie die folgenden Schritte aus, um ein Mobile App-Back-End zu erstellen.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Sie haben nun ein Azure-Back-End für mobile Apps bereitgestellt, das von Ihren mobilen Clientanwendungen verwendet werden kann. Als Nächstes laden Sie ein Serverprojekt für ein einfaches Back-End für „Aufgabenlisten“ herunter und veröffentlichen es in Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Erstellen einer Datenbankverbindung und Konfigurieren des Client- und Serverprojekts
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Ausführen der Xamarin.Android-App
1. Öffnen Sie das Projekt „Xamarin.Android“.

2. Wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und navigieren Sie zu der mobilen App, die Sie erstellt haben. Suchen Sie auf dem Blatt `Overview` nach der URL, die der öffentliche Endpunkt für Ihre mobile App ist. Beispiel: der Sitename für meinen App-Namen „test123“ wird https://test123.azurewebsites.net.

3. Öffnen Sie die Datei `ToDoActivity.cs` in diesem Ordner: „xamarin.android/ZUMOAPPNAME/ToDoActivity.cs“. Der Anwendungsname lautet `ZUMOAPPNAME`.

4. Ersetzen Sie in der `ToDoActivity`-Klasse die Variable `ZUMOAPPURL` durch den oben stehenden öffentlichen Endpunkt.

    `const string applicationURL = @"ZUMOAPPURL";`

    wird zu
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Drücken Sie die Taste F5, um die App bereitzustellen und auszuführen.

6. Geben Sie in der App einen sinnvollen Text wie z.B. *Tutorial fertigstellen* ein, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

    ![][10]

    Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen App-Back-End zurückgegeben, und die Daten werden in der Liste angezeigt.

   > [!NOTE]
   > Sie können den Code überprüfen, der zum Abfragen und Einfügen von Daten auf das mobile App-Back-End zugreift. Der Code befindet sich in der C#-Datei "ToDoActivity.cs".
   
## <a name="troubleshooting"></a>Problembehandlung
Sollten beim Erstellen der Lösung Probleme auftreten, führen Sie den NuGet-Paket-Manager aus, und aktualisieren Sie auf die `Xamarin.Android`-Unterstützungspakete. Schnellstartprojekte enthalten unter Umständen nicht immer die neuesten Versionen.

Beachten Sie, dass alle Unterstützungspakete, auf die in Ihrem Projekt verwiesen wird, die gleiche Version aufweisen müssen. Das [Azure Mobile Apps-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) hat eine `Xamarin.Android.Support.CustomTabs`-Abhängigkeit für die Android-Plattform – wenn also das Projekt neuere Unterstützungspakete verwendet, müssen Sie dieses Paket direkt mit der erforderlichen Version installieren, um Konflikte zu vermeiden.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
