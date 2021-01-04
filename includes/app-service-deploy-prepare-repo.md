---
title: include file
description: include file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004343"
---
## <a name="prepare-your-repository"></a>Vorbereiten Ihres Repositorys

Um automatische Builds vom Azure App Service-Kudu-Buildserver zu erhalten, muss das Stammverzeichnis Ihres Repositorys die richtigen Dateien enthalten.

| Typ | Stammverzeichnisdateien |
|-|-|
| ASP.NET (nur Windows) | _*.sln_, _*.csproj_ oder _default.aspx_ |
| ASP.NET Core | _*.sln_ oder _*.csproj_ |
| PHP | _index.php_ |
| Ruby (nur Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ oder _package.json_ mit einem Startskript |
| Python | _\*.py_, _requirements.txt_ oder _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ oder _iisstart.htm_ |
| WebJobs | _\<job_name>/run.\<extension>_ unter _App\_Data/jobs/continuous_ (für fortlaufende WebJobs) oder _App\_Data/jobs/triggered_ (für ausgelöste WebJobs). Weitere Informationen finden Sie in der [Kudu-Dokumentation zu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Siehe [Kontinuierliche Bereitstellung für Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Zum Anpassen Ihrer Bereitstellung schließen Sie eine Datei vom Typ *.deployment* im Repositorystamm ein. Weitere Informationen finden Sie unter [Anpassen von Bereitstellungen](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) und [Benutzerdefiniertes Bereitstellungsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Wenn Sie in Visual Studio entwickeln, kann [Visual Studio ein Repository für Sie erstellen](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Das Projekt kann sofort über Git bereitgestellt werden.
>

