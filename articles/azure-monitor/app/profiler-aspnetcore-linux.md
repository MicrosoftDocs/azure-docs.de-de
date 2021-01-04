---
title: Profilerstellung für ASP.NET Core-Azure Linux-Web-Apps mit Application Insights Profiler | Microsoft-Dokumentation
description: Eine konzeptionelle Übersicht und ein schrittweises Tutorial zur Verwendung von Application Insights Profiler.
ms.topic: conceptual
ms.custom: devx-track-csharp
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef52e946edb5db8074a9b4e3ce5e4a81ae0bde5
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561051"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profilerstellung für ASP.NET Core-Azure Linux-Web-Apps mit Application Insights Profiler

Diese Funktion steht derzeit als Vorschau zur Verfügung.

Ermitteln Sie durch Verwendung von [Application Insights](./app-insights-overview.md) den Zeitaufwand für die einzelnen Methoden in Ihrer Live-Webanwendung. Application Insights Profiler ist jetzt für ASP.NET Core-Web-Apps verfügbar, die in Linux auf Azure App Service gehostet werden. Dieser Leitfaden enthält eine schrittweise Anleitung zum Erfassen von Profiler-Ablaufverfolgungen für ASP.NET Core-Linux-Web-Apps.

Nach Abschluss dieser exemplarischen Vorgehensweise kann Ihre App Profiler-Ablaufverfolgungen wie die Ablaufverfolgungsdaten erfassen, die in der Abbildung dargestellt sind. In diesem Beispiel weist die Profiler-Ablaufverfolgung nach, dass eine bestimmte Webanforderung aufgrund der Wartezeit langsam ist. Der *langsamste Pfad* im Code, der die App verlangsamt, wird durch ein Flammensymbol gekennzeichnet. Die **About**-Methode im Abschnitt **HomeController** verlangsamt die Web-App, da die Methode die **Thread.Sleep**-Funktion aufruft.

![Profilerablaufverfolgungen](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Voraussetzungen
Die folgenden Anweisungen werden auf alle Windows-, Linux- und Mac-Entwicklungsumgebungen angewendet:

* Installieren Sie [.NET Core SDK 2.1.2 oder höher](https://dotnet.microsoft.com/download/archives).
* Installieren Sie Git nach den Anweisungen unter [Getting Started – Installing Git (Erste Schritte – Installieren von Git)](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Lokales Einrichten des Projekts

1. Öffnen Sie ein Eingabeaufforderungsfenster auf dem Computer. Die folgenden Anweisungen werden auf alle Windows-, Linux- und Mac-Entwicklungsumgebungen angewendet.

1. Erstellen Sie eine ASP.NET Core-MVC-Webanwendung:

   ```console
   dotnet new mvc -n LinuxProfilerTest
   ```

1. Ändern Sie das Arbeitsverzeichnis in den Stammordner des Projekts.

1. Fügen Sie das NuGet-Paket hinzu, um die Profiler-Ablaufverfolgungen zu erfassen:

   ```console
   dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
   ```

1. Aktivieren Sie Application Insights in der Datei „Program.cs“:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```

1. Aktivieren Sie den Profiler in der Datei „Program.cs“:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Fügen Sie im Abschnitt **HomeController.cs** eine Codezeile zur Verzögerung um ein paar Sekunden nach dem Zufallsprinzip hinzu:

    ```csharp
    using System.Threading;
    ...

    public IActionResult About()
        {
            Random r = new Random();
            int delay = r.Next(5000, 10000);
            Thread.Sleep(delay);
            return View();
        }
    ```

1. Speichern Sie die Änderungen, und committen Sie sie an das lokale Repository:

    ```console
    git init
    git add .
    git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Erstellen der Linux-Web-App zum Hosten Ihres Projekts

1. Erstellen Sie die Web-App-Umgebung mithilfe von App Service unter Linux:

    ![Erstellen der Linux-Web-App](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Erstellen Sie die Anmeldeinformationen für die Bereitstellung:

    > [!NOTE]
    > Notieren Sie Ihr Kennwort zur späteren Verwendung, wenn Sie Ihre Web-App bereitstellen.

    ![Erstellen der Anmeldeinformationen für die Bereitstellung](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Wählen Sie die Bereitstellungsoptionen aus. Richten Sie gemäß der Anweisungen im Azure-Portal ein lokales Git-Repository in der Web-App ein. Ein Git-Repository wird automatisch erstellt.

    ![Git-Repository einrichten](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Weitere Bereitstellungsoptionen finden Sie unter [App Service-Dokumentation](../../app-service/index.yml).

## <a name="deploy-your-project"></a>Bereitstellen des Projekts

1. Navigieren Sie im Eingabeaufforderungsfenster zum Stammordner für Ihr Projekt. Fügen Sie ein Git-Remote-Repository hinzu, sodass es auf das Repository auf App Service weist:

    ```console
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Verwenden Sie den **username (Benutzernamen)** , den Sie verwendet haben, um die Anmeldeinformationen für die Bereitstellung zu erstellen.
    * Verwenden Sie den **app-name (App-Namen)** , den Sie verwendet haben, um die Web-App mithilfe von App Service unter Linux zu erstellen.

2. Stellen Sie das Projekt durch Pushen der Änderungen in Azure bereit:

    ```console
    git push azure main
    ```

    Die Ausgabe sollte etwa folgendem Beispiel entsprechen:

    ```output
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'main'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    ...
    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Hinzufügen von Application Insights zum Überwachen Ihrer Web-Apps

1. [Erstellen Sie eine Application Insights-Ressource](./create-new-resource.md).

2. Kopieren Sie den Wert **iKey** der Application Insights-Ressource, und legen Sie die folgenden Einstellungen in Ihren Web-Apps fest:

    `APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]`

    Wenn die App-Einstellungen geändert werden, wird die Website automatisch neu gestartet. Sobald die neuen Einstellungen angewendet werden, wird der Profiler sofort für zwei Minuten ausgeführt. Anschließend wird der Profiler einmal pro Stunde für zwei Minuten ausgeführt.

3. Generieren Sie etwas Datenverkehr zu Ihrer Website. Sie können Datenverkehr generieren, indem Sie die **Info**-Seite der Website mehrmals aktualisieren.

4. Warten Sie zwei bis fünf Minuten, bis die Ereignisse auf Application Insights summiert sind.

5. Navigieren Sie zum Application Insights-**Leistungsbereich** im Azure-Portal. Sie können die Profiler-Ablaufverfolgungen unten rechts im Bereich anzeigen.

    ![Profiler-Ablaufverfolgungen anzeigen](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Nächste Schritte
Gehen Sie bei Verwendung benutzerdefinierter, von Azure App Service gehosteter Container gemäß der Anleitung unter [Enable Service Profiler for containerized ASP.NET Core application (Aktivieren von Service Profiler für die containerbasierte ASP.NET Core-Anwendung)](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) vor, um Application Insights Profiler zu aktivieren.

Senden Sie Berichte über Probleme oder Vorschläge an das GitHub-Repository für Application Insights: [ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues) (ApplicationInsights-Profiler-AspNetCore: Probleme)