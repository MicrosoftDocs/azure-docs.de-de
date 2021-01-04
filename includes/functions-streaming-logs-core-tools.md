---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020349"
---
#### <a name="built-in-log-streaming"></a>Integriertes Protokollstreaming

Verwenden Sie die Option `logstream`, um den Empfang von Streamingprotokollen einer bestimmten in Azure ausgeführten Funktions-App zu starten (siehe folgendes Beispiel):

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>Integriertes Protokollstreaming ist in Core Tools für Funktions-Apps noch nicht aktiviert, die unter Linux in einem Verbrauchsplan ausgeführt werden. Für diese Hostingpläne müssen Sie stattdessen Live Metrics Stream verwenden, um die Protokolle nahezu in Echtzeit anzuzeigen.

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Sie können auch [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) für Ihre Funktions-App in einem neuen Browserfenster anzeigen, indem Sie die Option `--browser` wie im folgenden Beispiel einschließen:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
