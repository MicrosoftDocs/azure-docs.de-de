---
title: include file
description: include file
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177857"
---
Erweiterungsbündel machen alle Bindungen, die vom Azure Functions-Team veröffentlicht wurden, durch eine Einstellung in der Datei *host.json* verfügbar. Stellen Sie für die lokale Entwicklung sicher, dass Sie über die neueste Version der [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools) verfügen.

Um die Erweiterungsbündel zu verwenden, aktualisieren Sie die Datei *host.json* so, dass sie den folgenden Eintrag für `extensionBundle` enthält:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- Die `id`-Eigenschaft verweist auf den Namespace für Microsoft Azure Functions-Erweiterungsbündel.
- Die `version` verweist auf die Version des Pakets.

Bündelversionen werden erhöht, wenn sich Pakete im Bündel ändern. Hauptversionsänderungen erfolgen nur, wenn sich Pakete im Bündel um eine Hauptversion ändern. Die `version`-Eigenschaft verwendet die [Intervallnotation, um Versionsbereiche anzugeben](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). Die Functions Runtime wählt immer die zulässige Höchstversion aus, die durch den Versionsbereich oder das Intervall definiert ist.

Wenn Sie in Ihrem Projekt auf die Erweiterungsbündel verweisen, stehen Ihren Funktionen alle Standardbindungen zur Verfügung. Folgende Bindungen sind im [Erweiterungsbündel](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) verfügbar:

|Paket  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|