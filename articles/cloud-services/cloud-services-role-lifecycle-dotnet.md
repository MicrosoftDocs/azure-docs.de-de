---
title: Behandlung von Lebenszyklusereignissen für Clouddienste | Microsoft-Dokumentation
description: Erfahren Sie, wie die Lebenszyklusmethoden einer Clouddienstrolle in .NET verwendet werden können.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 13f500b32bb85bdc0f84b812ef4ef9188a257771
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60406417"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Anpassen des Lebenszyklus einer Web- oder Workerrolle in .NET
Wenn Sie eine Workerrolle erstellen, erweitern Sie die [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) -Klasse, die Methoden zum Außerkraftsetzen bereitstellt, sodass Sie auf Lebenszyklusereignisse reagieren können. Für Webrollen ist diese Klasse optional, daher wird sie in erster Linie für die Reaktion auf Lebenszyklusereignisse verwendet.

## <a name="extend-the-roleentrypoint-class"></a>Erweitern der RoleEntryPoint-Klasse
Die [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100))-Klasse enthält Methoden, die von Azure beim **Starten**, **Ausführen** oder **Beenden** einer Web- oder Workerrolle aufgerufen werden. Sie können diese Methoden optional außer Kraft setzen, um die Rolleninitialisierung, Herunterfahrsequenzen für Rollen oder den Ausführungsthread der Rolle zu verwalten. 

Berücksichtigen Sie beim Erweitern von **RoleEntryPoint**das folgende Verhalten der Methoden:

* Die [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100))- und die [OnStop](/previous-versions/azure/reference/ee772844(v=azure.100))-Methode geben einen booleschen Wert zurück, daher kann von diesen Methoden **false** zurückgegeben werden.
  
   Wenn Ihr Code **false**zurückgibt, wird der Rollenvorgang abrupt beendet, ohne dass eine möglicherweise eingerichtete Herunterfahrsequenz ausgeführt wird. Im Allgemeinen sollte das Zurückgeben von **false** durch die **OnStart**-Methode vermieden werden.
* Jede nicht abgefangene Ausnahme in einer Überladung einer **RoleEntryPoint** -Methode wird als nicht behandelte Ausnahme behandelt.
  
   Wenn in einer der Lebenszyklusmethoden eine Ausnahme auftritt, löst Azure das [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) -Ereignis aus, und der Prozess wird beendet. Nachdem die Rolle offline geschaltet wurde, wird sie von Azure neu gestartet. Wenn eine nicht behandelte Ausnahme auftritt, wird das [Stopping](/previous-versions/azure/reference/ee758136(v=azure.100)) -Ereignis nicht ausgelöst, und die **OnStop** -Methode wird nicht aufgerufen.

Wenn Ihre Rolle nicht gestartet wird oder zwischen den Zuständen „Initialisieren“, „Ausgelastet“ und „Beenden“ wechselt, wirft Ihr Code bei jedem Neustart der Rolle möglicherweise eine nicht behandelte Ausnahme in einem der Lebenszyklusereignisse aus. Verwenden Sie in diesem Fall das [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) -Ereignis, um die Ursache der Ausnahme zu ermitteln und die Ausnahme entsprechend zu behandeln. Ihre Rolle kann auch von der [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) -Methode zurückgegeben werden, wodurch ein Neustart der Rolle veranlasst wird. Weitere Informationen zu den Bereitstellungszuständen finden Sie unter [Allgemeine Probleme, durch die Rollen zyklisch ausgeführt werden](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Wenn Sie zum Entwickeln Ihrer Anwendung die **Azure-Tools für Microsoft Visual Studio** verwenden, erweitern die Rollenprojektvorlagen automatisch die **RoleEntryPoint**-Klasse in den Dateien *WebRole.cs* und *WorkerRole.cs*.
> 
> 

## <a name="onstart-method"></a>OnStart-Methode
Die **OnStart** -Methode wird aufgerufen, wenn die Rolleninstanz von Azure online geschaltet wird. Während der OnStart-Code ausgeführt wird, wird die Rolleninstanz als **Ausgelastet** gekennzeichnet, und es wird vom Lastenausgleich kein externer Datenverkehr an sie gesendet. Sie können diese Methode außer Kraft setzen, um Initialisierungsaufgaben wie das Implementieren von Ereignishandlern und Starten der [Azure-Diagnose](cloud-services-how-to-monitor.md)auszuführen.

Wenn **OnStart** das Ergebnis **true** zurückgibt, wurde die Instanz erfolgreich initialisiert, und Azure ruft die **RoleEntryPoint.Run**-Methode auf. Wenn **OnStart** das Ergebnis **false** zurückgibt, wird die Rolle sofort beendet, ohne dass gegebenenfalls. geplante Herunterfahrsequenzen ausgeführt werden.

Im folgenden Codebeispiel wird veranschaulicht, wie die **OnStart** -Methode außer Kraft gesetzt wird. Diese Methode konfiguriert und startet einen Diagnosemonitor, wenn die Rolleninstanz gestartet wird, und richtet die Übertragung von Protokollierungsdaten an ein Speicherkonto ein:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop-Methode
Die **OnStop** -Methode wird aufgerufen, nachdem eine Rolleninstanz von Azure offline geschaltet wurde und bevor der Prozess beendet wird. Sie können diese Methode zum Aufrufen von Code außer Kraft setzen, der für das saubere Herunterfahren der Rolleninstanz erforderlich ist.

> [!IMPORTANT]
> Die Codeausführung in der **OnStop**-Methode muss innerhalb eines begrenzten Zeitraums abgeschlossen werden, wenn er nicht im Rahmen des vom Benutzer initiierten Herunterfahrens, sondern aus anderen Gründen aufgerufen wird. Nach diesem Zeitraum wird der Prozess beendet. Sie müssen also sicherstellen, dass der Code in der **OnStop**-Methode schnell ausgeführt werden kann oder die nicht vollständige Ausführung toleriert. Die **OnStop**-Methode wird nach dem Auslösen des **Stopping**-Ereignisses aufgerufen.
> 
> 

## <a name="run-method"></a>Run-Methode
Sie können die **Run** -Methode außer Kraft setzen, um einen Thread mit langer Ausführungsdauer für die Rolleninstanz zu implementieren.

Die Außerkraftsetzung der **Run**-Methode ist nicht erforderlich. Die Standardimplementierung startet einen Thread, der sich unbegrenzt im Ruhezustand befindet. Die Außerkraftsetzung der **Run**-Methode hat eine Codeblockierung auf unbestimmte Zeit zur Folge. Wenn die **Run**-Methode wieder aktiv ist, wird die Rolle automatisch ordnungsgemäß wiederverwendet. Anders ausgedrückt: Azure löst das **Stopping**-Ereignis aus und ruft die **OnStop**-Methode auf, sodass Ihre Herunterfahrsequenzen ausgeführt werden, bevor die Rolle offline geschaltet wird.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementieren der ASP.NET-Lebenszyklusmethoden für eine Webrolle
Sie können die ASP.NET-Lebenszyklusmethoden zusätzlich zu den Methoden der **RoleEntryPoint** -Klasse verwenden, um Initialisierungs- und Herunterfahrsequenzen für eine Webrolle zu verwalten. Dies kann für die Kompatibilität nützlich sein, wenn Sie eine vorhandene ASP.NET-Anwendung in Azure portieren. Die ASP.NET-Lebenszyklusmethoden werden in den **RoleEntryPoint** -Methoden aufgerufen. Die **Application\_Start**-Methode wird nach Ausführung der **RoleEntryPoint.OnStart**-Methode aufgerufen. Die **Application\_End**-Methode wird vor dem Aufrufen der **RoleEntryPoint.OnStop**-Methode aufgerufen.

## <a name="next-steps"></a>Nächste Schritte
Hier erfahren Sie, wie Sie ein [Clouddienstpaket erstellen](cloud-services-model-and-package.md).

