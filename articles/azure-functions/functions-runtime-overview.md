---
title: Übersicht über Azure Functions Runtime | Microsoft-Dokumentation
description: Übersicht über Azure Functions Runtime Preview
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 2af9575c50ee522d6330ddf46c75b666132b7a84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61021207"
---
# <a name="azure-functions-runtime-overview-preview"></a>Übersicht über Azure Functions-Runtime (Vorschauversion)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Azure Functions-Runtime (Vorschauversion) bietet Ihnen eine neue Möglichkeit, die Einfachheit und Flexibilität des Azure Functions-Programmiermodels lokal zu nutzen. Azure Functions Runtime basiert auf den gleichen Open Source-Grundlagen wie Azure Functions, wird allerdings lokal bereitgestellt, um eine fast identische Entwicklungsumgebung wie mit dem Clouddienst zu ermöglichen.

![Portal für Azure Functions Runtime Preview][1]

Azure Functions Runtime bietet Ihnen eine Möglichkeit, mit Azure Functions zu arbeiten, bevor Sie die Cloud einsetzen. Auf diese Weise können die Codeobjekte, die Sie erstellen, bei der Migration in die Cloud übernommen werden.  Die Runtime eröffnet außerdem neue Optionen, z.B. die Nutzung überschüssiger Rechenleistung Ihrer lokalen Computer, um Batchprozesse über Nacht auszuführen. Sie können auch Geräte in Ihrer Organisation verwenden, um bedingt Daten an andere Systeme zu senden, lokal und in der Cloud.

Azure Functions Runtime besteht aus zwei Teilen:

* Azure Functions Runtime-Verwaltungsrolle
* Azure Functions Runtime-Workerrolle

## <a name="azure-functions-management-role"></a>Azure Functions-Verwaltungsrolle

Die Azure Functions-Verwaltungsrolle stellt einen Host für die lokale Verwaltung von Funktionen bereit. Diese Rolle führt die folgenden Aufgaben aus:

* Hosten des Azure Functions-Verwaltungsportals, dieses entspricht dem Portal, das im [Azure-Portal](https://portal.azure.com) angezeigt wird. Das Portal bietet eine konsistente Umgebung, mit der Sie Ihre Funktionen auf die gleiche Weise wie im Azure-Portal entwickeln können.
* Verteilen von Funktionen auf mehrere Functions-Worker.
* Bereitstellen eines Endpunkts für die Veröffentlichung, damit Sie Ihre Funktionen direkt über Microsoft Visual Studio veröffentlichen können, indem Sie das Veröffentlichungsprofil herunterladen und importieren.

## <a name="azure-functions-worker-role"></a>Azure Functions-Workerrolle

Die Azure Functions-Workerrollen werden in Windows-Containern bereitgestellt, wo der Funktionscode ausgeführt wird.  Sie können in Ihrer Organisation mehrere Workerrollen bereitstellen. Dies ist eine wichtige Möglichkeit für Kunden, überschüssige Rechenleistung zu nutzen.  Überschüssige Rechenleistung tritt beispielsweise auf, wenn Computer in Unternehmen durchgehend eingeschaltet sind, aber für einen längeren Zeitraum nicht genutzt werden.

## <a name="minimum-requirements"></a>Mindestanforderungen

Um mit der Nutzung von Azure Functions Runtime zu beginnen, benötigen Sie einen Computer mit Windows Server 2016 oder Windows 10 Creators Update mit Zugriff auf eine SQL Server-Instanz.

## <a name="next-steps"></a>Nächste Schritte

Installieren von [Azure Functions Runtime Preview](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
