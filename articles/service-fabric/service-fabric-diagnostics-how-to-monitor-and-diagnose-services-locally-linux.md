---
title: Debuggen von Azure Service Fabric-Apps unter Linux | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Service Fabric-Dienste auf einem lokalen Linux-Entwicklungscomputer überwachen und diagnostizieren.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: f0b850038a29dd0949def97b359b2b7a5ce920bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60392856"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Überwachen und Diagnostizieren von Diensten in einer Entwicklungsumgebung auf einem lokalen Computer


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Überwachung, Erkennung, Diagnose und Problembehandlung ermöglichen das Ausführen von Diensten mit minimalen Unterbrechungen für Benutzer. Überwachung und Diagnose sind in einer tatsächlichen bereitgestellten Produktionsumgebung wichtig. Die Anwendung eines ähnlichen Modells während der Entwicklung von Diensten stellt sicher, dass die Diagnosepipeline beim Wechsel zu einer Produktionsumgebung funktioniert. Service Fabric erleichtert Dienstentwicklern das Implementieren von Diagnosen, die sowohl in einer lokalen Umgebung auf einem einzelnen Computer als auch in der tatsächlichen Konfiguration in einem Produktionscluster nahtlos verwendet werden können.


## <a name="debugging-service-fabric-java-applications"></a>Debuggen von Service Fabric Java-Anwendungen

Für Java-Anwendungen sind [mehrere Protokollierungsframeworks](https://en.wikipedia.org/wiki/Java_logging_framework) verfügbar. Da `java.util.logging` die Standardoption bei JRE ist, wird dies auch für die [Codebeispiele in GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started) verwendet. Im Folgenden wir die Konfiguration des `java.util.logging` -Frameworks erklärt.

Mithilfe von „java.util.logging“ können Sie Ihre Anwendungsprotokolle zum Arbeitsspeicher, zu Ausgabestreams, zu Konsolendateien oder zu Sockets umleiten. Für jede dieser Optionen gibt es Standardhandler, die im Framework bereits bereitgestellt wurden. Sie können eine `app.properties` -Datei erstellen, um den Dateihandler für Ihre Anwendung zu konfigurieren, damit alle Protokolle zu einer lokalen Datei umgeleitet werden.

Der folgende Codeausschnitt enthält eine Beispielkonfiguration:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Der Ordner, auf den die `app.properties` -Datei verweist, muss vorhanden sein. Nachdem die `app.properties`-Datei erstellt wurde, müssen Sie auch Ihr Skript für den Einstiegspunkt, `entrypoint.sh` im `<applicationfolder>/<servicePkg>/Code/`-Ordner, ändern, um die Eigenschaft `java.util.logging.config.file` auf die `app.properties`-Datei festzulegen. Der Eintrag sollte wie der folgende Ausschnitt aussehen:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Diese Konfiguration führt zu Protokollen, die auf rotierende Weise unter `/tmp/servicefabric/logs/`erfasst werden. Die Protokolldatei in diesem Fall heißt „mysfapp%u.%g.log“, wobei
* **%u** eine eindeutige Zahl zum Lösen von Konflikten zwischen gleichzeitig ausgeführten Java-Prozessen ist.
* **%g** ist die Generationszahl, um zwischen rotierenden Protokollen zu unterscheiden.

Wenn kein Handler explizit konfiguriert ist, wird standardmäßig der Konsolen-Handler registriert. Die Protokolle können in syslog unter „/var/log/syslog“ angezeigt werden.

Weitere Informationen finden Sie bei den [Codebeispielen in GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Debuggen von Service Fabric-C#-Anwendungen


Für die Ablaufverfolgung von CoreCLR-Anwendungen unter Linux sind mehrere Frameworks verfügbar. Weitere Informationen finden Sie unter [GitHub logging](http:/github.com/aspnet/logging) (GitHub-Protokollierung).  Da C#-Entwickler mit EventSource vertraut sind, wird EventSource in diesem Artikel für die Ablaufverfolgung von CoreCLR-Beispielen unter Linux verwendet.

Der erste Schritt besteht darin, System.Diagnostics.Tracing einzuschließen, sodass Sie Ihre Protokolle in den Arbeitsspeicher, Ausgabestreams oder Konsolendateien schreiben können.  Zur Protokollierung unter Verwendung von EventSource fügen Sie das folgende Projekt zu Ihrer project.json-Datei hinzu:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Sie können einen benutzerdefinierten EventListener verwenden, um auf Dienstereignisse zu lauschen, und diese dann zu den geeigneten Ablaufverfolgungsdateien weiterleiten. Der folgende Codeausschnitt zeigt eine Beispielimplementierung der Protokollierung mit EventSource und einem benutzerdefinierten EventListener:


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


Der vorherige Codeausschnitt gibt die Protokolle an eine Datei in `/tmp/MyServiceLog.txt` aus. Der Dateiname muss entsprechend aktualisiert werden. Wenn Sie die Protokolle an die Konsole umleiten möchten, verwenden Sie den folgenden Codeausschnitt in Ihrer benutzerdefinierten EventListener-Klasse:

```csharp
public static TextWriter Out = Console.Out;
```

Die Beispiele unter [C# Samples](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) verwenden EventSource und einen benutzerdefinierten EventListener, um Ereignisse in eine Datei zu protokollieren.



## <a name="next-steps"></a>Nächste Schritte
Der Ablaufverfolgungscode, der Ihrer Anwendung hinzugefügt wurde, gilt auch für die Diagnose der Anwendung auf einem Azure-Cluster. Sehen Sie sich diese Artikel an, in denen die verschiedenen Optionen für die Tools und deren Einrichtung erläutert werden.
* [Sammeln von Protokollen mit Azure-Diagnose](service-fabric-diagnostics-how-to-setup-lad.md)
