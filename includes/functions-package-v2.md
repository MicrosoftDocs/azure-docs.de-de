---
title: include file
description: include file
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: bcc55156ca1d03614a4ff9767d6cf3f2603c06ca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025488"
---
Fügen Sie Ihrer bevorzugten Entwicklungsumgebung mithilfe der folgenden Methoden Unterstützung hinzu.

| Entwicklungsumgebung  | Anwendungstyp      | So fügen Sie Unterstützung hinzu |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C#-Klassenbibliothek      | [Installieren des NuGet-Pakets](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Basierend auf [Kerntools](../articles/azure-functions/functions-run-local.md) | [Registrieren des Erweiterungspakets](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Die Installation der [Azure Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) wird empfohlen. |
| Beliebiger anderer Editor/IDE     | Basierend auf [Kerntools](../articles/azure-functions/functions-run-local.md) | [Registrieren des Erweiterungspakets](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure-Portal             | Nur online im Portal | Wird beim Hinzufügen einer Bindung installiert.<br /><br /> Informationen zum Aktualisieren vorhandener Bindungserweiterungen, ohne Ihre Funktions-App erneut veröffentlichen zu müssen, finden Sie unter [Aktualisieren Ihrer Erweiterungen](../articles/azure-functions/functions-bindings-register.md). |