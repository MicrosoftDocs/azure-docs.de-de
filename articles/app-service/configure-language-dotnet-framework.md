---
title: Konfigurieren von ASP.NET-Apps
description: Erfahren Sie, wie Sie in Azure App Service eine ASP.NET-App konfigurieren. In diesem Artikel werden die gängigsten Konfigurationsaufgaben vorgestellt.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 8ed6835583cc4881b19eee14ed392b193324535e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744164"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Konfigurieren einer ASP.NET-App für Azure App Service

> [!NOTE]
> ASP.NET Core betreffende Informationen finden Sie unter [Konfigurieren einer ASP.NET Core-App für Azure App Service](configure-language-dotnetcore.md).

ASP.NET-Apps müssen in Azure App Service als kompilierte Binärdateien bereitgestellt werden. Das Veröffentlichungstool für Visual Studio erstellt die Projektmappe und stellt dann die kompilierten Binärdateien direkt bereit, während die App Service-Bereitstellungs-Engine zuerst das Coderepository und dann die Binärdateien kompiliert.

Dieser Leitfaden enthält wichtige Konzepte und Anweisungen für ASP.NET-Entwickler. Wenn Sie Azure App Service noch nie verwendet haben, folgen Sie zunächst dem [ASP.NET-Schnellstart](quickstart-dotnet-framework.md) und dem [Tutorial zu ASP.NET mit SQL-Datenbank](app-service-web-tutorial-dotnet-sqldatabase.md).

## <a name="show-supported-net-framework-runtime-versions"></a>Anzeigen unterstützter .NET Framework-Runtimeversionen

In App Service sind auf den Windows-Instanzen bereits alle unterstützten .NET Framework-Versionen installiert. Navigieren Sie zu `https://<app-name>.scm.azurewebsites.net/DebugConsole`, und führen Sie den entsprechenden Befehl in der browserbasierten Konsole aus, um die für Sie verfügbaren .NET Framework-Runtime- und -SDK-Versionen anzuzeigen:

Für CLR 4-Runtimeversionen (.NET Framework 4 und höher):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

Die neueste .NET Framework-Version ist möglicherweise nicht sofort verfügbar.

Für CLR 2-Runtimeversionen (.NET Framework 3.5 und früher):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>Anzeigen der aktuellen .NET Framework-Runtimeversion

Führen Sie in der [Cloud Shell](https://shell.azure.com)-Instanz folgenden Befehl aus:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

Der Wert `v4.0` bedeutet, dass die neueste Version von CLR 4 (.NET Framework 4.x) verwendet wird. Der Wert `v2.0` bedeutet, dass eine CLR 2-Version (.NET Framework 3.5) verwendet wird.

## <a name="set-net-framework-runtime-version"></a>Festlegen der .NET Framework-Runtimeversion

Standardmäßig verwendet App Service die neueste unterstützte .NET Framework-Version, um Ihre ASP.NET-App auszuführen. Um Ihre App stattdessen mit .NET Framework 3.5 auszuführen, führen Sie den folgenden Befehl in der [Cloud Shell](https://shell.azure.com) aus (v2.0 steht für CLR 2):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>Zugreifen auf Umgebungsvariablen

In App Service können Sie [App-Einstellungen](configure-common.md#configure-app-settings) und Verbindungszeichenfolgen außerhalb des App-Codes festlegen. Anschließend können Sie unter Verwendung des ASP.NET-Standardmusters in einer beliebigen Klasse darauf zugreifen:

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

Wenn Sie eine App-Einstellung mit demselben Namen in App Service und in *web.config* konfigurieren, hat der App Service-Wert Vorrang vor dem *web.config* -Wert. Mit dem lokalen *web.config* -Wert können Sie die App lokal debuggen, mit dem App Service-Wert können Sie die App hingegen im Produkt mit Produktionseinstellungen ausführen. Verbindungszeichenfolgen funktionieren auf dieselbe Weise. Auf diese Weise können Sie Ihre Anwendungsgeheimnisse außerhalb Ihres Coderepositorys aufbewahren und auf die entsprechenden Werte zugreifen, ohne Ihren Code zu ändern.

## <a name="deploy-multi-project-solutions"></a>Bereitstellen von Projektmappen mit mehreren Projekten

Wenn eine Visual Studio-Projektmappe mehrere Projekte enthält, umfasst der Veröffentlichungsprozess von Visual Studio bereits das Auswählen des bereitzustellenden Projekts. Wenn Sie für die App Service-Bereitstellungs-Engine bereitstellen, z. B. mit Git oder einer ZIP-Bereitstellung, und dabei die Buildautomatisierung aktiviert ist, wählt die App Service-Bereitstellungs-Engine das erste Website- oder Webanwendungsprojekt aus, das als App Service-App gefunden wird. Sie können angeben, welches Projekt App Service verwenden soll, indem Sie die App-Einstellung `PROJECT` angeben. Führen Sie beispielsweise Folgendes in der [Cloud Shell](https://shell.azure.com) aus:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>Abrufen einer detaillierten Ausnahmenseite

Wenn Ihre ASP. NET-App eine Ausnahme im Visual Studio-Debugger erzeugt, zeigt der Browser eine detaillierte Ausnahmeseite an, in App Service wird diese Seite hingegen durch eine allgemeine Fehlermeldung ersetzt. Öffnen Sie zum Anzeigen der detaillierten Ausnahmeseite in App Service die Datei *Web.config* , und fügen Sie das Element `<customErrors mode="Off"/>` unter dem Element `<system.web>` hinzu. Beispiel:

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

Stellen Sie Ihre App mit der aktualisierten Datei *Web.config* erneut bereit. Nun sollte dieselbe detaillierte Ausnahmeseite angezeigt werden.

## <a name="access-diagnostic-logs"></a>Zugreifen auf Diagnoseprotokolle

Mit [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) können Sie Diagnosemeldungen in Ihrem Anwendungscode hinzufügen. Beispiel: 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Erstellen einer ASP.NET-App in Azure mit SQL-Datenbank](app-service-web-tutorial-dotnet-sqldatabase.md)
