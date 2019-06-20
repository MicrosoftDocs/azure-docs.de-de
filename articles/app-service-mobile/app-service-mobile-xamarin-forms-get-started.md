---
title: Erste Schritte mit Mobile Apps unter Verwendung von Xamarin.Forms
description: In diesem Einführungstutorial erfahren Sie, wie Sie Mobile Apps für die Xamarin.Forms-Entwicklung verwenden.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: b0719f6ac2f99f9e665b1265665752dd53ccbaf0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242660"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Erstellen einer Xamarin.Forms-App mit Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

Dieses Tutorial zeigt, wie Sie einer mobilen Xamarin.Forms-App mithilfe des Mobile Apps-Features von Azure App Service als Back-End einen cloudbasierten Back-End-Dienst hinzufügen. Sie erstellen sowohl ein neues Mobile Apps-Back-End als auch eine einfache Xamarin.Forms-Aufgabenlisten-App, die App-Daten in Azure speichert.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Apps-Lernprogramme für Xamarin.Forms.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps, die Sie auch nach Ablauf der Testversion weiter nutzen können. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio-Tools für Xamarin in Visual Studio 2017 oder höher oder Visual Studio für Mac. Anweisungen finden Sie auf der [Xamarin-Installationsseite][Install Xamarin].

* (optional) Zum Erstellen einer iOS-App ist ein Mac mit Xcode 9.0 oder höher erforderlich. Für die Entwicklung von iOS-Apps kann Visual Studio für Mac oder Visual Studio 2017 oder höher kann verwendet werden (sofern der Mac im Netzwerk verfügbar ist).

## <a name="create-a-new-mobile-apps-back-end"></a>Erstellen eines neuen Mobile Apps-Back-Ends
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Erstellen einer Datenbankverbindung und Konfigurieren des Client- und Serverprojekts
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>Ausführen der Xamarin.Forms-Lösung

Die Visual Studio-Tools für Xamarin sind zum Öffnen der Projektmappe erforderlich. Informationen finden Sie in den [Xamarin-Installationsanweisungen][Install Xamarin]. Sind die Tools bereits installiert, führen Sie die folgenden Schritte aus, um die Projektmappe herunterzuladen und zu öffnen:

### <a name="visual-studio"></a>Visual Studio

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

2. Klicken Sie auf dem Blatt „Einstellungen“ für Ihre mobile App auf **Schnellstart** (unter „Bereitstellung“) > **Xamarin.Forms**. Klicken Sie unter Schritt 3 auf **Neue App erstellen** , falls noch nicht ausgewählt.  Klicken Sie dann auf die Schaltfläche **Herunterladen** .

   Dadurch wird ein Projekt heruntergeladen, das eine mit Ihrer mobilen App verbundene Clientanwendung enthält. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

3. Extrahieren Sie das Projekt, das Sie heruntergeladen haben, und öffnen Sie es dann in Visual Studio.

4. Führen Sie die nachstehenden Anweisungen aus, um die Android- oder Windows-Projekte bzw. das iOS-Projekt (sofern ein Mac-Computer im Netzwerk verfügbar ist) auszuführen.

### <a name="visual-studio-for-mac"></a>Visual Studio für Mac

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und navigieren Sie zu der mobilen App, die Sie erstellt haben. Suchen Sie auf dem Blatt `Overview` nach der URL, die den öffentlichen Endpunkt für Ihre mobile App darstellt. Beispiel: Der Websitename für meinen App-Namen „test123“ lautet https://test123.azurewebsites.net.

2. Öffnen Sie die Datei `Constants.cs` im Ordner „xamarin.forms/ZUMOAPPNAME“. Der Anwendungsname lautet `ZUMOAPPNAME`.

3. Ersetzen Sie in der Klasse `Constants.cs` die Variable `ZUMOAPPURL` durch den oben angegebenen öffentlichen Endpunkt.

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    wird zu

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. Führen Sie die nachstehenden Anweisungen aus, um die Android- oder Windows-Projekte bzw. das iOS-Projekt (sofern ein Mac-Computer im Netzwerk verfügbar ist) auszuführen.

## <a name="optional-run-the-android-project"></a>(Optional) Ausführen des Android-Projekts

In diesem Abschnitt führen Sie das Xamarin.Android-Projekt aus. Wenn Sie nicht mit Android-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

### <a name="visual-studio"></a>Visual Studio

1. Klicken Sie mit der rechten Maustaste auf das Android-Projekt (Droid), und wählen Sie **Als Startprojekt festlegen** aus.

2. Wählen Sie im Menü **Build** die Option **Konfigurations-Manager** aus.

3. Aktivieren Sie im Dialogfeld **Konfigurations-Manager** neben dem Android-Projekt die Kontrollkästchen **Erstellen** und **Bereitstellen**. Stellen Sie sicher, dass für das Projekt mit freigegebenem Code das Kontrollkästchen **Erstellen** aktiviert ist.

4. Drücken Sie **F5**, oder klicken Sie auf die Schaltfläche **Starten**, um das Projekt zu erstellen und die App in einem Android-Emulator zu starten.

### <a name="visual-studio-for-mac"></a>Visual Studio für Mac

1. Klicken Sie mit der rechten Maustaste auf das Android-Projekt, und wählen Sie **Als Startprojekt festlegen** aus.

2. Klicken Sie auf das Menü **Ausführen** und dann auf **Debuggen starten**, um das Projekt zu erstellen und die App in einem Android-Emulator zu starten.

Geben Sie in der App einen aussagekräftigen Text ein (beispielsweise *Xamarin kennenlernen*), und wählen Sie anschließend das Pluszeichen ( **+** ) aus.

![Android-To-Do-App][11]

Dadurch wird eine POST-Anforderung an das neue, in Azure gehostete Mobile Apps-Back-End gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden vom Mobile Apps-Back-End zurückgegeben, und die Daten werden in der Liste angezeigt.

> [!NOTE]
> Den Code, der auf Ihr Mobile Apps-Back-End zugreift, finden Sie in der C#-Datei **TodoItemManager.cs** des Projekts mit freigegebenem Code in der Projektmappe.
>

## <a name="optional-run-the-ios-project"></a>(Optional) Ausführen des iOS-Projekts

In diesem Abschnitt führen Sie das Xamarin.iOS-Projekt für iOS-Geräte aus. Wenn Sie nicht mit iOS-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

### <a name="visual-studio"></a>Visual Studio

1. Klicken Sie mit der rechten Maustaste auf das iOS-Projekt, und wählen Sie **Als Startprojekt festlegen** aus.

2. Wählen Sie im Menü **Build** die Option **Konfigurations-Manager** aus.

3. Aktivieren Sie im Dialogfeld **Konfigurations-Manager** neben dem iOS-Projekt die Kontrollkästchen **Erstellen** und **Bereitstellen**. Stellen Sie sicher, dass für das Projekt mit freigegebenem Code das Kontrollkästchen **Erstellen** aktiviert ist.

4. Drücken Sie **F5** , um das Projekt zu erstellen und die App im iPhone-Emulator zu starten.

### <a name="visual-studio-for-mac"></a>Visual Studio für Mac

1. Klicken Sie mit der rechten Maustaste auf das iOS-Projekt, und wählen Sie **Als Startprojekt festlegen** aus.

2. Wählen Sie im Menü **Ausführen** die Option **Debuggen starten** aus, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten.

Geben Sie in der App einen aussagekräftigen Text ein (beispielsweise *Xamarin kennenlernen*), und wählen Sie anschließend das Pluszeichen ( **+** ) aus.

![iOS-To-Do-App][10]

Dadurch wird eine POST-Anforderung an das neue, in Azure gehostete Mobile Apps-Back-End gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden vom Mobile Apps-Back-End zurückgegeben, und die Daten werden in der Liste angezeigt.

> [!NOTE]
> Den Code, der auf Ihr Mobile Apps-Back-End zugreift, finden Sie in der C#-Datei **TodoItemManager.cs** des Projekts mit freigegebenem Code in der Projektmappe.
>

## <a name="optional-run-the-windows-project"></a>(Optional) Ausführen des Windows-Projekts

In diesem Abschnitt führen Sie das Xamarin.Forms-UWP-Projekt (Universelle Windows-Plattform) für Windows-Geräte aus. Wenn Sie nicht mit Windows-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

### <a name="visual-studio"></a>Visual Studio

1. Klicken Sie mit der rechten Maustaste auf ein beliebiges UWP-Projekt, und wählen Sie **Als Startprojekt festlegen** aus.

2. Wählen Sie im Menü **Build** die Option **Konfigurations-Manager** aus.

3. Aktivieren Sie im Dialogfeld **Konfigurations-Manager** neben dem ausgewählten Windows-Projekt die Kontrollkästchen **Erstellen** und **Bereitstellen**. Stellen Sie sicher, dass für das Projekt mit freigegebenem Code das Kontrollkästchen **Erstellen** aktiviert ist.

4. Um das Projekt zu erstellen und die App in einem Windows-Emulator zu starten, drücken Sie **F5**, oder klicken Sie auf die Schaltfläche **Starten**. (Der Text **Lokaler Computer** sollte angezeigt werden.)

> [!NOTE]
> Das Windows-Projekt kann nicht unter macOS ausgeführt werden.

Geben Sie in der App einen aussagekräftigen Text ein (beispielsweise *Xamarin kennenlernen*), und wählen Sie anschließend das Pluszeichen ( **+** ) aus.

Dadurch wird eine POST-Anforderung an das neue, in Azure gehostete Mobile Apps-Back-End gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden vom Mobile Apps-Back-End zurückgegeben, und die Daten werden in der Liste angezeigt.

![UWP-To-Do-App][12]

> [!NOTE]
> Den Code, der auf Ihr Mobile Apps-Back-End zugreift, finden Sie in der C#-Datei **TodoItemManager.cs** des Portable Class Library-Projekts Ihrer Projektmappe.
>

## <a name="troubleshooting"></a>Problembehandlung

Sollten beim Erstellen der Lösung Probleme auftreten, führen Sie den NuGet-Paket-Manager aus, und aktualisieren Sie auf die neueste Version von `Xamarin.Forms`. Aktualisieren Sie außerdem im Android-Projekt die `Xamarin.Android`-Unterstützungspakete. Schnellstartprojekte enthalten unter Umständen nicht immer die neuesten Versionen.

Beachten Sie, dass alle Unterstützungspakete, auf die in Ihrem Android-Projekt verwiesen wird, die gleiche Version aufweisen müssen. Das [Azure Mobile Apps-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) hat eine `Xamarin.Android.Support.CustomTabs`-Abhängigkeit für die Android-Plattform – wenn also das Projekt neuere Unterstützungspakete verwendet, müssen Sie dieses Paket direkt mit der erforderlichen Version installieren, um Konflikte zu vermeiden.

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/