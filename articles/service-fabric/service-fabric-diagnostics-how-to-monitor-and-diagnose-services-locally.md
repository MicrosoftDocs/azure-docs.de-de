---
title: Debuggen von Azure Service Fabric-Apps unter Windows | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Microsoft Azure Service Fabric erstellte Dienste auf einem lokalen Entwicklungscomputer überwachen und diagnostizieren.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 31c559c1ab314b7e1f29bd96f74d6d82cfcc0420
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60392829"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Überwachen und Diagnostizieren von Diensten in einer Entwicklungsumgebung auf einem lokalen Computer
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Überwachung, Erkennung, Diagnose und Problembehandlung ermöglichen das Ausführen von Diensten mit minimalen Unterbrechungen für Benutzer. Die Überwachung und Diagnose sind in einer tatsächlichen bereitgestellten Produktionsumgebung zwar wichtig, die Wirksamkeit hängt aber davon ab, ob bei der Entwicklung von Diensten ein ähnliches Modell verwendet wird. Nur so ist sichergestellt, dass die Dienste später tatsächlich funktionieren. Service Fabric erleichtert Dienstentwicklern das Implementieren von Diagnosen, die sowohl in einer lokalen Umgebung auf einem einzelnen Computer als auch in der tatsächlichen Konfiguration in einem Produktionscluster nahtlos verwendet werden können.

## <a name="event-tracing-for-windows"></a>Ereignisablaufverfolgung für Windows
[Ereignisablaufverfolgung für Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (Event Tracing for Windows, ETW) ist die empfohlene Technologie für das Nachverfolgen von Meldungen in Service Fabric. Einige Vorteile der Verwendung von ETW sind wie folgt:

* **ETW ist schnell.** ETW wurde als Technologie für die Ablaufverfolgung erstellt, die minimale Auswirkungen auf die Codeausführungszeiten hat.
* **Die ETW-Ablaufverfolgung funktioniert nahtlos in lokalen Entwicklungsumgebungen und in realen Clusterkonfigurationen.** Das heißt, Sie müssen den Ablaufverfolgungscode nicht neu schreiben, wenn Sie den Code in einem echten Cluster bereitstellen.
* **Der Service Fabric-Systemcode verwendet ETW auch für die interne Ablaufverfolgung.** Auf diese Weise können Sie Ihre Anwendungsablaufverfolgung zusammen mit Service Fabric-Systemablaufverfolgungen anzeigen. Außerdem können Sie die Sequenzen und Beziehungen zwischen dem Anwendungscode und den Ereignissen im zugrunde liegenden System besser nachvollziehen.
* **Die Anzeige von ETW-Ereignissen wird von den Visual Studio-Tools für Service Fabric unterstützt.** ETW-Ereignisse werden in der Ansicht „Diagnoseereignisse“ von Visual Studio angezeigt, sobald Visual Studio ordnungsgemäß mit Service Fabric konfiguriert ist. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Anzeigen von Service Fabric-Ereignissen in Visual Studio
Service Fabric gibt ETW-Ereignisse aus, um die Abläufe in der Plattform für Anwendungsentwickler sichtbar zu machen. Führen Sie jetzt die Schritte unter [Erstellen Ihrer ersten Anwendung in Visual Studio](service-fabric-tutorial-create-dotnet-app.md)aus, falls Sie dies noch nicht getan haben. Mit diesen Informationen können Sie eine Anwendung so einrichten, dass in der Anzeige von Diagnoseereignissen die Ablaufverfolgungsmeldungen erscheinen.

1. Falls das Diagnoseereignisfenster nicht automatisch angezeigt wird, wechseln Sie in Visual Studio zur Registerkarte **Ansicht**, wählen Sie **Weitere Fenster** und dann **Diagnoseereignisanzeige**.
2. Jedes Ereignis verfügt über Standard-Metadateninformationen, mit deren Hilfe Sie den Knoten, die Anwendung und den Dienst ablesen können, aus denen das Ereignis stammt. Sie können die Liste mit den Ereignissen auch filtern, indem Sie oben im Ereignisfenster das Feld **Ereignisse filtern** verwenden. Beispielsweise können Sie nach **Knotenname** oder **Dienstname** filtern. Wenn Sie sich die Ereignisdetails ansehen, können Sie oben im Ereignisfenster auch die Schaltfläche **Pause** verwenden und den Vorgang später fortsetzen, ohne dass Ereignisse verloren gehen.
   
   ![Visual Studio-Anzeige von Diagnoseereignissen](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Hinzufügen eigener benutzerdefinierter Ablaufverfolgungen zum Anwendungscode
Die Service Fabric-Visual Studio-Projektvorlagen enthalten Beispielcode. Der Code zeigt, wie dem Anwendungscode benutzerdefinierte ETW-Ablaufverfolgungen hinzugefügt werden. Diese werden in der ETW-Anzeige in Visual Studio zusammen mit den Systemablaufverfolgungen von Service Fabric angezeigt. Der Vorteil dieser Methode liegt darin, dass Metadaten automatisch den Ablaufverfolgungen hinzugefügt werden und dass die Visual Studio-Diagnoseereignisanzeige bereits für die Anzeige der Ablaufverfolgungen konfiguriert ist.

Suchen Sie bei Projekten, die mit **Dienstvorlagen** (zustandslos oder zustandsbehaftet) erstellt wurden, einfach nach der `RunAsync`-Implementierung:

1. Der Aufruf an `ServiceEventSource.Current.ServiceMessage` in the `RunAsync` -Methode zeigt ein Beispiel für eine benutzerdefinierte ETW-Ablaufverfolgung aus dem Anwendungscode.
2. Die Datei **ServiceEventSource.cs** enthält eine Überladung für die `ServiceEventSource.ServiceMessage`-Methode, die aus Leistungsgründen für häufige Ereignisse verwendet werden sollte.

Für Projekte, die mit **Actor-Vorlagen** (zustandsbehaftet oder zustandslos) erstellt wurden:

1. Öffnen Sie die Datei **ProjectName.cs** , wobei *ProjectName* der Name Ihres Visual Studio-Projekts ist.  
2. Suchen Sie den Code `ActorEventSource.Current.ActorMessage(this, "Doing Work");` in der Methode *DoWorkAsync* .  Dies ist ein Beispiel für eine benutzerdefinierte ETW-Ablaufverfolgung, die aus dem Anwendungscode geschrieben wird.  
3. Die Datei **ActorEventSource.cs** enthält eine Überladung für die `ActorEventSource.ActorMessage`-Methode, die aus Leistungsgründen für häufige Ereignisse verwendet werden sollte.

Nachdem Sie die benutzerdefinierte ETW-Ablaufverfolgung dem Dienstcode hinzugefügt haben, können Sie die Anwendung erneut erstellen, bereitstellen und ausführen, um die Ereignisse in der Diagnoseereignisanzeige anzuzeigen. Beim Debuggen der Anwendung mit **F5**wird die Diagnoseereignisanzeige automatisch geöffnet.

## <a name="next-steps"></a>Nächste Schritte
Der Ablaufverfolgungscode, den Sie Ihrer Anwendung für die lokale Diagnose hinzugefügt haben, funktioniert auch mit den Tools für die Anzeige dieser Ereignisse, wenn Sie Ihre Anwendung in einem Azure-Cluster ausführen. Sehen Sie sich diese Artikel an, in denen die verschiedenen Optionen für die Tools erläutert werden und beschrieben wird, wie Sie sie einrichten können.

* [Sammeln von Protokollen mit Azure-Diagnose](service-fabric-diagnostics-how-to-setup-wad.md)
* [Ereignisaggregation und -sammlung mithilfe von EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

