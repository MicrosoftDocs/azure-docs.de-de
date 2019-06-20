---
title: include file
description: include file
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7c994f85d90e94d514bb4e4f91a6644ed45432c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66455130"
---
1. Laden Sie die Client-SDK-Schnellstarts für die folgenden Plattformen herunter:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Wenn Sie das iOS-Projekt verwenden, müssen Sie „azuresdk-iOS-\*.zip“ aus dem [neuesten GitHub-Release](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest) herunterladen. Entzippen Sie die Datei, und fügen Sie die Datei `MicrosoftAzureMobile.framework` dem Stamm des Projekts hinzu.
    >

2. Sie müssen eine Datenbankverbindung hinzufügen oder eine Verbindung mit einer vorhandenen Verbindung herstellen. Legen Sie zunächst fest, ob Sie einen Datenspeicher erstellen oder einen bereits vorhandenen verwenden möchten.

    - **Erstellen eines neuen Datenspeichers**: Wenn Sie vorhaben, einen Datenspeicher zu erstellen, verwenden Sie den folgenden Schnellstart:

        [Schnellstart: Erste Schritte mit Einzeldatenbanken in Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Vorhandene Datenquelle**: Befolgen Sie die nachstehenden Anweisungen, wenn Sie eine vorhandene Verbindung verwenden möchten.

        1. Format der SQL-Datenbank-Verbindungszeichenfolge: `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** : Name des Servers. Diesen finden Sie auf der Übersichtsseite für Ihre Datenbank, in der Regel im Format „server_name.database.windows.net“.
            **{port}** : Normalerweise 1433.
            **{your_catalogue}** : Name der Datenbank.
            **{your_username}** : Benutzername für den Zugriff auf Ihre Datenbank.
            **{your_password}** : Kennwort für den Zugriff auf Ihre Datenbank.

            [Weitere Informationen zum Format der SQL-Verbindungszeichenfolge](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Fügen Sie die Verbindungszeichenfolge Ihrer **mobilen App** hinzu. In App Service können Sie Verbindungszeichenfolgen für Ihre Anwendung über die Option **Konfiguration** im Menü verwalten.

            So fügen Sie eine Verbindungszeichenfolge hinzu:

            1. Klicken Sie auf die Registerkarte **Anwendungseinstellungen**.

            2. Klicken Sie auf **[+] Neue Verbindungszeichenfolge**.

            3. Sie müssen den **Namen**, **Wert** und **Typ** Ihrer Verbindungszeichenfolge angeben.

            4. Geben Sie als **Name** `MS_TableConnectionString` ein.

            5. Der Wert muss der Verbindungszeichenfolge entsprechen, die Sie im vorherigen Schritt zusammengestellt haben.

            6. Wenn Sie eine Verbindungszeichenfolge einer SQL Azure-Datenbank hinzufügen, wählen Sie unter **Typ** die Option **SQLAzure**.

3. Azure Mobile Apps umfasst SDKs für .NET- und Node.js-Back-Ends.

   - **Node.js-Back-End**
    
     Wenn Sie die Node.js-Schnellstart-App verwenden möchten, befolgen Sie die nachstehenden Anweisungen.

     1. Wechseln Sie im Azure-Portal zu **Einfache Tabellen**. Der folgende Bildschirm wird angezeigt.
      
        ![Node: Einfache Tabellen](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Stellen Sie auf der Registerkarte **Konfiguration** sicher, dass die SQL-Verbindungszeichenfolge bereits hinzugefügt wurde. Aktivieren Sie dann das Kontrollkästchen **Ich bestätige, dass durch diese Aktion alle Websiteinhalte überschrieben werden**, und klicken Sie auf die Schaltfläche **TodoItem-Tabelle erstellen**.
     
        ![Node: Konfiguration einfacher Tabellen](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. Klicken Sie in **Einfache Tabellen** auf die Schaltfläche **+ Hinzufügen**.
    
        ![Node: Schaltfläche „Hinzufügen“ für einfache Tabellen](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Erstellen Sie eine `TodoItem`-Tabelle mit anonymem Zugriff.
      
        ![Node: Einfache Tabelle hinzufügen](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET-Back-End**
    
        Wenn Sie die .NET-Schnellstart-App verwenden möchten, befolgen Sie die nachstehenden Anweisungen.

        1. Laden Sie das .NET-Serverprojekt für Azure Mobile Apps aus dem Repository [azure-mobile-apps-quickstarts](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart) herunter.

        2. Erstellen Sie das .NET-Serverprojekt lokal in Visual Studio.

        3. Öffnen Sie in Visual Studio den Projektmappen-Explorer, klicken Sie mit der rechten Maustaste auf das Projekt `ZUMOAPPNAMEService`, und klicken Sie auf **Veröffentlichen**. Das Fenster `Publish to App Service` wird angezeigt. Wenn Sie an einem Mac arbeiten, finden Sie [hier](https://docs.microsoft.com/azure/app-service/deploy-local-git) andere Möglichkeiten zum Bereitstellen der App.
        
           ![Visual Studio-Veröffentlichung](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Wählen Sie **App Service** als Ziel für die Veröffentlichung aus, klicken Sie auf **Vorhandene auswählen**, und klicken Sie dann im unteren Fensterbereich auf **Veröffentlichen**.

        5. Sie müssen sich zuerst mit Ihrem Azure-Abonnement bei Visual Studio anmelden. Wählen Sie `Subscription`, `Resource Group` und dann den Namen Ihrer App aus. Klicken Sie schließlich auf **OK**. Dadurch wird das lokale .NET-Serverprojekt im App Service-Back-End bereitgestellt. Nach Abschluss der Bereitstellung werden Sie im Browser an `http://{zumoappname}.azurewebsites.net/` umgeleitet.
        
           ![Das Back-End ist aktiv.](./media/app-service-mobile-configure-new-backend/backend-is-up.png)
