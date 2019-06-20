---
title: Verwenden der Abhängigkeitsinjektion in Azure Functions (.NET)
description: Verwenden von Abhängigkeitsinjektionen für die Registrierung und Verwendung von Diensten in .NET-Funktionen
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure-Funktionen, Funktionen, serverlose Architektur
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan, glenga, cshoe
ms.openlocfilehash: 5aa3ebee251e51be19b2d260825226194b678159
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242221"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Verwenden der Abhängigkeitsinjektion in Azure Functions (.NET)

Azure Functions unterstützt das Softwareentwurfsmuster „Abhängigkeitsinjektion“ (Dependency Injection, DI). Damit kann eine [Umkehrung der Steuerung (Inversion of Control, IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) zwischen Klassen und ihren Abhängigkeiten erreicht werden.

Azure Functions basiert auf den Abhängigkeitsinjektionsfunktionen von ASP.NET Core.  Sie sollten mit den Diensten, der Lebensdauer und den Entwurfsmustern der [ASP.NET Core-Abhängigkeitsinjektion](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) vertraut sein, bevor Sie diese in Funktionen verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Abhängigkeitsinjektion verwenden können, müssen Sie das NuGet-Paket [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) installieren. Sie können dieses Paket installieren, indem Sie den folgenden Befehl aus der Paketkonsole ausführen:

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```
Sie müssen außerdem Version 1.0.28 des [Microsoft.NET.SDK.Functions-Pakets](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) oder eine höhere Version verwenden.

## <a name="registering-services"></a>Registrieren von Diensten

Sie können eine Configure-Methode erstellen und einer `IFunctionsHostBuilder`-Instanz Komponenten hinzufügen, um Dienste zu registrieren.  Der Azure Functions-Host erstellt eine `IFunctionsHostBuilder`-Instanz und übergibt diese direkt in Ihre konfigurierte Methode.

Zum Registrieren Ihrer Configure-Methode müssen Sie ein Assembly-Attribut hinzufügen, das den Typ Ihrer Configure-Methode mithilfe des `FunctionsStartup`-Attributs angibt.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="service-lifetimes"></a>Dienstlebensdauer

Azure Funktions-Apps bieten dieselbe Dienstlebensdauer wie die [ASP.NET-Abhängigkeitsinjektion](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes), sind kurzlebig, bereichsbezogen und Singleton.

In einer Funktions-App entspricht eine bereichsbezogene Lebensdauer der Ausführungslebensdauer einer Funktion. Bereichsbezogene Dienste werden einmal pro Ausführung erstellt.  Höhere Anforderungen für diesen Dienst während der Ausführung verwenden diese Instanz erneut.  Die Lebensdauer eines Singletondiensts entspricht der Lebensdauer des Hosts und wird für Funktionsausführungen dieser Instanz wiederverwendet.

Dienste mit Singleton-Lebensdauer werden für Verbindungen und Clients, wie z. B. die Klassen `SqlConnection`, `CloudBlobClient` oder `HttpClient`, empfohlen.

Ansicht oder Download eines [Beispiels für verschiedene Lebensdauern](https://aka.ms/functions/di-sample).

## <a name="logging-services"></a>Protokollierungsdienste

Wenn Sie Ihren eigenen Protokollanbieter benötigen, empfiehlt sich das Registrieren der `ILoggerProvider`-Schnittstelle.  Für Application Insights fügt Functions Application Insights automatisch für Sie hinzu.  

> [!WARNING]
> Fügen Sie `AddApplicationInsightsTelemetry()` nicht zur Sammlung der Dienste hinzu, da sonst Dienste registriert werden, durch die Konflikte mit den von der Umgebung bereitgestellten Diensten auftreten können. 
 
## <a name="function-app-provided-services"></a>Dienste, die von Funktions-App bereitgestellt werden

Der Funktionshost selbst registriert viele Dienste.  Die folgenden Dienste sind sicher für das Erstellen von Abhängigkeiten.  Andere Hostdienste werden nicht für das Registrieren oder Erstellen einer Abhängigkeit unterstützt.  [Erstellen Sie ein Issue und eine Diskussion auf GitHub](https://github.com/azure/azure-functions-host), wenn Sie eine Abhängigkeit von anderen Diensten erstellen möchten.

|Service Type|Gültigkeitsdauer|BESCHREIBUNG|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Laufzeitkonfiguration|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Verantwortlich für die Bereitstellung der Hostinstanz-ID|

### <a name="overriding-host-services"></a>Überschreiben von Hostdiensten

Das Überschreiben der vom Host bereitgestellten Dienste wird derzeit nicht unterstützt.  [Erstellen Sie ein Issue und eine Diskussion auf GitHub](https://github.com/azure/azure-functions-host), wenn Sie andere Dienste überschreiben möchten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Überwachen Ihrer Funktions-App](functions-monitoring.md)
* [Bewährte Methoden für Funktionen](functions-best-practices.md)
