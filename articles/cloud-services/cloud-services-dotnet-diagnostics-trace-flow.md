---
title: Verfolgen des Ablaufs einer Cloud Services-Anwendung mit Azure-Diagnose | Microsoft-Dokumentation
description: Fügen Sie in einer Azure-Anwendung Ablaufverfolgungsmeldungen hinzu, um Debuggen, Leistungsmessung, Überwachung, Datenverkehrsanalysen und mehr zu ermöglichen.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: jeconnoc
ms.openlocfilehash: f597bc760a3f3825416912642ee66a53dfb91696
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60336863"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Verfolgen des Ablaufs einer Cloud Services-Anwendung mit der Azure-Diagnose
Die Ablaufverfolgung ist eine Möglichkeit, die Ausführung der Anwendung zu überwachen, während sie ausgeführt wird. Sie können die Klassen [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace), [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) und [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) verwenden, um Informationen zu Fehlern und zur Anwendungsausführung in Protokollen, Textdateien oder auf anderen Geräten zur späteren Analyse aufzuzeichnen. Weitere Informationen zur Ablaufverfolgung finden Sie unter [Ablaufverfolgung und Instrumentation von Anwendungen](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Verwenden von Ablaufverfolgungsanweisungen und -schaltern
Implementieren Sie die Ablaufverfolgung in der Cloud Services-Anwendung durch Hinzufügen von [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) zur Anwendungskonfiguration und durch Aufrufe von „System.Diagnostics.Trace“ oder „System.Diagnostics.Debug“ in Ihrem Anwendungscode. Verwenden Sie die Konfigurationsdatei *app.config* für Workerrollen und *web.config* für Webrollen. Wenn Sie einen neuen gehosteten Dienst mit einer Visual Studio-Vorlage erstellen, wird die Azure-Diagnose dem Projekt automatisch hinzugefügt, und „DiagnosticMonitorTraceListener“ wird in die entsprechende Konfigurationsdatei für die Rollen, die Sie hinzufügen, aufgenommen.

Informationen zum Platzieren von Ablaufverfolgungsanweisungen finden Sie unter [Vorgehensweise: Hinzufügen von Ablaufverfolgungsanweisungen zu Anwendungscode](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Durch die Platzierung von [Ablaufverfolgungsschaltern](/dotnet/framework/debug-trace-profile/trace-switches) in Ihrem Code können Sie steuern, ob und in welchem Umfang Ablaufverfolgung durchgeführt wird. Auf diese Weise können Sie den Status der Anwendung in einer Produktionsumgebung überwachen. Dies ist besonders wichtig in einer Geschäftsanwendung, die mehrere Komponenten auf mehreren Computern verwendet. Weitere Informationen finden Sie unter [Gewusst wie: Konfigurieren von Ablaufverfolgungsschaltern](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurieren des Ablaufverfolgungslisteners in einer Azure-Anwendung
Für „Trace“, „Debug“ und „TraceSource“ müssen Sie „Listener“ einrichten, die Meldungen sammeln und aufzeichnen, die gesendet werden. Listener sammeln und speichern Ablaufverfolgungsmeldungen und leiten sie weiter. Sie leiten die Ablaufverfolgungsausgabe an ein entsprechendes Ziel weiter, beispielsweise ein Protokoll, ein Fenster oder eine Textdatei. Die Azure-Diagnose verwendet die [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) -Klasse.

Bevor Sie das folgende Verfahren ausführen, müssen Sie den Azure-Diagnosemonitor initialisieren. Informationen hierzu finden Sie unter [Aktivieren der Diagnose in Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Beachten Sie, dass die Konfiguration des Listeners automatisch hinzugefügt wird, wenn Sie die von Visual Studio bereitgestellten Vorlagen verwenden.

### <a name="add-a-trace-listener"></a>Hinzufügen eines Ablaufverfolgungslisteners
1. Öffnen Sie die Datei „web.config“ oder „app.config“ für Ihre Rolle.
2. Fügen Sie der Datei den folgenden Code hinzu. Geben Sie für das Version-Attribut die Versionsnummer der referenzierten Assembly an. Die Assemblyversion wird nicht unbedingt mit jeder Azure-SDK-Version geändert, es sei denn, es liegen Updates für sie vor.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Stellen Sie sicher, dass ein Projektverweis auf die Assembly Microsoft.WindowsAzure.Diagnostics vorhanden ist. Aktualisieren Sie die Versionsnummer im obigen XML auf die Version der referenzierten Microsoft.WindowsAzure.Diagnostics-Assembly.
   > 
   > 
3. Speichern Sie die Konfigurationsdatei.

Weitere Informationen zu Listenern finden Sie unter [Ablaufverfolgungslistener](/dotnet/framework/debug-trace-profile/trace-listeners).

Nachdem Sie die Schritte zum Hinzufügen des Listeners abgeschlossen haben, können Sie Ihrem Code Ablaufverfolgungsanweisungen hinzufügen.

### <a name="to-add-trace-statement-to-your-code"></a>So fügen Sie dem Code eine Ablaufverfolgungsanweisung hinzu
1. Öffnen Sie eine Quelldatei für die Anwendung. Beispielsweise die Datei „\<RoleName>.cs“ für die Workerrolle oder die Webrolle.
2. Fügen Sie die folgende using-Anweisung hinzu, wenn sie noch nicht vorhanden ist:
    ```
        using System.Diagnostics;
    ```
3. Fügen Sie Ablaufverfolgungsanweisungen überall ein, wo Sie Informationen über den Zustand der Anwendung erfassen möchten. Sie können eine Vielzahl von Methoden zum Formatieren der Ausgabe der Ablaufverfolgungsanweisung verwenden. Weitere Informationen finden Sie unter [Gewusst wie: Hinzufügen von Ablaufverfolgungsanweisungen zu Anwendungscode](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Speichern Sie die Quelldatei.

