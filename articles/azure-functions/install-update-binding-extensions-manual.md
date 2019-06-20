---
title: Manuelles Installieren oder Aktualisieren von Azure Functions-Bindungserweiterungen
description: Erfahren Sie, wie Azure Functions-Bindungserweiterungen für bereitgestellte Funktions-App installiert oder aktualisiert werden.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, Funktionen, Bindungserweiterungen, NuGet, Aktualisierungen
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: cda977ba59070c3ddaac05784277d6c0b5109f0f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035728"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Manuelles Installieren oder Aktualisieren von Azure Functions-Bindungserweiterungen aus dem Portal

Die Azure Functions Runtime-Version 2.x verwendet Bindungserweiterungen zum Implementieren von Code für Trigger und Bindungen. Bindungserweiterungen stehen in Form von NuGet-Paketen zur Verfügung. Zum Registrieren einer Erweiterung installieren Sie im Wesentlichen ein Paket. Die Art, wie Sie beim Entwickeln von Funktionen Bindungserweiterungen installieren, hängt von der Entwicklungsumgebung ab. Weitere Informationen finden Sie unter [Registrieren von Bindungserweiterungen](./functions-bindings-register.md) in den Artikeln zu Triggern und Bindungen.

Manchmal müssen Sie Ihre Bindungserweiterungen im Azure-Portal manuell installieren oder aktualisieren. Beispielsweise kann es erforderlich sein, eine registrierte Bindung auf eine neuere Version zu aktualisieren. Möglicherweise müssen Sie auch eine unterstützte Bindung registrieren, die nicht über die Registerkarte **Integrieren** im Portal installiert werden kann.

## <a name="install-a-binding-extension"></a>Installieren von Bindungserweiterungen

Verwenden Sie die folgenden Schritte, um Erweiterungen manuell aus dem Portal zu installieren oder aktualisieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Funktions-App, und wählen Sie sie aus. Wählen Sie die Registerkarte **Overview** (Übersicht) und dort **Stop** (Beenden) aus.  Durch das Beenden der Funktions-App werden Dateien entsperrt, sodass Änderungen vorgenommen werden können.

1. Schließen Sie die Registerkarte **Platform features** (Plattformfeatures), und wählen Sie unter **Development tools** (Entwicklungstools) **Advanced Tools (Kudu)** (Erweiterte Tools (Kudu)) aus. Der Kudu-Endpunkt (`https://<APP_NAME>.scm.azurewebsites.net/`) wird in einem neuen Fenster geöffnet.

1. Wählen Sie im Kudu-Fenster **Debug Console** > **CMD** (Debugkonsole > CMD) aus.  

1. Navigieren Sie im Befehlsfenster zu `D:\home\site\wwwroot`, und wählen Sie das Löschen-Symbol neben `bin` aus, um den Ordner zu löschen. Wählen Sie **OK** aus, um den Löschvorgang zu bestätigen.

1. Wählen Sie das Bearbeiten-Symbol neben der `extensions.csproj`-Datei aus, in der die Bindungserweiterungen für die Funktions-App definiert sind. Die Projektdatei wird im Online-Editor geöffnet.

1. Nehmen Sie die erforderlichen Ergänzungen und Aktualisierungen an **PackageReference**-Elementen in der **ItemGroup** vor, und wählen Sie dann **Speichern** aus. Eine aktuelle Liste der unterstützten Paketversionen finden Sie im Wiki-Artikel [What packages do I need?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) (Welche Pakete benötige ich?). Für alle drei Azure Storage-Bindungen ist das Microsoft.Azure.WebJobs.Extensions.Storage-Paket erforderlich.

1. Führen Sie im Ordner `wwwroot` den folgenden Befehl aus, um die Assemblys im Ordner `bin`, auf die verwiesen wird, neu zu erstellen.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Wählen Sie nach Rückkehr zur Registerkarte **Overview** (Übersicht) im Portal **Start** aus, um die Funktions-App neu zu starten.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)
