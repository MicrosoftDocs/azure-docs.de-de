---
title: Registrieren von Bindungserweiterungen von Azure Functions
description: Lernen Sie, wie Sie eine Azure Functions-Bindungserweiterung abhängig von Ihrer Umgebung registrieren.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 53eb5fc9389d913ecacec3729a06e47a1c2bf56b
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864545"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrieren von Bindungserweiterungen von Azure Functions

In Azure Functions Version 2.x sind [Bindungen](./functions-triggers-bindings.md) als separate Pakete aus der Functions-Runtime verfügbar. Während .NET-Funktionen über NuGet-Pakete auf Bindungen zugreifen, ermöglichen Erweiterungsbundles anderen Funktionen den Zugriff auf alle Bindungen über eine Konfigurationseinstellung.

Beachten Sie die folgenden Punkte im Zusammenhang mit Bindungserweiterungen:

- Bindungserweiterungen werden in Functions 1.x nicht explizit registriert, ausgenommen beim [Erstellen einer C#-Klassenbibliothek mithilfe von Visual Studio 2019](#local-csharp).

- HTTP und Zeitgebertrigger werden standardmäßig unterstützt und erfordern keine Erweiterung.

In der folgenden Tabelle ist angegeben, wann und wie Sie Bindungen registrieren.

| Entwicklungsumgebung |Registrierung<br/> in Functions 1.x  |Registrierung<br/> in Functions 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure-Portal|Automatisch|Automatisch|
|Nicht zu .NET gehörende Sprachen oder lokale Azure Core Tools-Entwicklung|Automatisch|[Verwenden von Azure Functions Core Tools und Erweiterungsbundles](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|C#-Klassenbibliothek mit Visual Studio 2019|[Verwendung von NuGet-Tools](#c-class-library-with-visual-studio-2019)|[Verwendung von NuGet-Tools](#c-class-library-with-visual-studio-2019)|
|C#-Klassenbibliothek mit Visual Studio Code|–|[Verwendung der .NET Core-CLI](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Lokale Entwicklung mit Azure Functions Core Tools und Erweiterungsbundles

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2019"></a>C#-Klassenbibliothek mit Visual Studio 2019

In **Visual Studio 2019** können Sie Pakete mithilfe des Befehls [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) aus der Paket-Manager-Konsole installieren, wie es im folgenden Beispiel gezeigt wird:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Der Name des Pakets, das für eine bestimmte Bindung verwendet wird, ist im Referenzartikel für diese Bindung angegeben. Ein Beispiel finden Sie im [Referenzartikel zur Service Bus-Bindung im Abschnitt „Pakete“](functions-bindings-service-bus.md#packages---functions-1x).

Ersetzen Sie `<TARGET_VERSION>` im Beispiel durch eine bestimmte Version des Pakets, z.B. `3.0.0-beta5`. Gültige Versionen sind auf den Seiten der einzelnen Pakete auf [NuGet.org](https://nuget.org) aufgeführt. Die Hauptversionen, die Functions Runtime 1.x oder 2.x entsprechen, sind im Referenzartikel für die Bindung angegeben.

## <a name="c-class-library-with-visual-studio-code"></a>C#-Klassenbibliothek mit Visual Studio Code

In **Visual Studio Code** können Sie Pakete über die Eingabeaufforderung installieren. Verwenden Sie dazu den Befehl [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) in der .NET Core-CLI, wie es im folgenden Beispiel gezeigt wird:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

Die .NET Core-CLI kann nur für die Entwicklung in Azure Functions 2.x verwendet werden.

Der Name des Pakets, das für eine bestimmte Bindung verwendet werden muss, ist im Referenzartikel für diese Bindung angegeben. Ein Beispiel finden Sie im [Referenzartikel zur Service Bus-Bindung im Abschnitt „Pakete“](functions-bindings-service-bus.md#packages---functions-1x).

Ersetzen Sie `<TARGET_VERSION>` im Beispiel durch eine bestimmte Version des Pakets, z.B. `3.0.0-beta5`. Gültige Versionen sind auf den Seiten der einzelnen Pakete auf [NuGet.org](https://nuget.org) aufgeführt. Die Hauptversionen, die Functions Runtime 1.x oder 2.x entsprechen, sind im Referenzartikel für die Bindung angegeben.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Trigger- und Bindungsbeispiel für Azure Functions](./functions-bindings-example.md)

