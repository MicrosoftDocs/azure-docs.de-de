---
title: OpenCensus Python-Ablaufverfolgung mit Azure Application Insights | Microsoft-Dokumentation
description: Enthält Anweisungen zur Verknüpfung der OpenCensus Python-Ablaufverfolgung in die lokale Weiterleitung und Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae9db483e15197e6cdaaaa5981410630184cc6ca
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957248"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Sammeln verteilter Ablaufverfolgungsdaten von Python (Vorschau)

Application Insights unterstützt jetzt die verteilte Ablaufverfolgung von Python-Anwendungen durch die Integration in [OpenCensus](https://opencensus.io) und unsere neue [lokale Weiterleitung](./../../azure-monitor/app/opencensus-local-forwarder.md). Dieser Artikel führt Sie Schritt für Schritt durch den Prozess der Einrichtung von OpenCensus für Python und die Weiterleitung Ihrer Ablaufverfolgungsdaten an Application Insights.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein Azure-Abonnement.
- Python muss installiert sein. In diesem Artikel wird [Python 3.7.0](https://www.python.org/downloads/) verwendet, aber auch frühere Versionen können mit kleineren Anpassung wahrscheinlich verwendet werden.
- Führen Sie die Anweisungen zum Installieren der [lokalen Weiterleitung als Windows-Dienst](./../../azure-monitor/app/opencensus-local-forwarder.md) aus.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-application-insights-resource"></a>Erstellen einer Application Insights-Ressource

Zuerst müssen Sie eine Application Insights-Ressource erstellen, die einen Instrumentierungsschlüssel (ikey) generiert. Der ikey wird dann verwendet, um die lokale Weiterleitung so zu konfigurieren, dass verteilte Ablaufverfolgungsdaten aus Ihrer mit OpenCensus instrumentierten Anwendung an Application Insights gesendet werden.   

1. Klicken Sie auf **Ressource erstellen** > **Entwicklertools** > **Application Insights**.

   ![Hinzufügen einer Application Insights-Ressource](./media/opencensus-python/0001-create-resource.png)

   Ein Dialogfeld für die Konfiguration wird geöffnet. Füllen Sie die Eingabefelder anhand der Informationen in der folgenden Tabelle aus:

    | Einstellungen        | Wert           | BESCHREIBUNG  |
   | ------------- |:-------------|:-----|
   | **Name**      | Global eindeutiger Wert | Der Name, der die zu überwachende App identifiziert. |
   | **Anwendungstyp** | Allgemein | Der Typ der zu überwachenden App. |
   | **Ressourcengruppe**     | myResourceGroup      | Der Name der neuen Ressourcengruppe, die Application Insights-Daten hosten soll. |
   | **Location** | USA (Ost) | Wählen Sie einen Standort in Ihrer Nähe oder in der Nähe des Standorts, in dem Ihre App gehostet wird. |

2. Klicken Sie auf **Create**.

## <a name="configure-local-forwarder"></a>Konfigurieren der lokalen Weiterleitung

1. Wählen Sie **Übersicht** > **Grundlagen** aus, und kopieren Sie den**Instrumentierungsschlüssel** Ihrer Anwendung.

   ![Screenshot des Instrumentierungsschlüssels](./media/opencensus-python/0003-instrumentation-key.png)

2. Bearbeiten Sie Ihre `LocalForwarder.config`-Datei, und fügen Sie Ihren Instrumentierungsschlüssel hinzu. Wenn Sie den Anweisungen unter [Voraussetzungen](./../../azure-monitor/app/opencensus-local-forwarder.md) gefolgt sind, befindet die Datei sich unter `C:\LF-WindowsServiceHost`.

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Starten Sie den Anwendungsdienst **lokale Weiterleitung** neu.

## <a name="opencensus-python-package"></a>OpenCensus Python-Paket

1. Installieren Sie das OpenCensus-Paket für Python und den Exporter mit „pip“ oder „pipenv“ über die Befehlszeile:

    ```console
    python -m pip install opencensus
    python -m pip install opencensus-ext-ocagent

    # pip env install opencensus
    ```

    > [!NOTE]
    > `python -m pip install opencensus` geht davon aus, dass Sie eine PATH-Umgebungsvariable für die Python-Installation festgelegt haben. Wenn Sie diese nicht konfiguriert haben, müssen Sie den vollständigen Verzeichnispfad zu Ihrer ausführbaren Python-Datei angeben, wodurch der Befehl in etwa so aussehen würde: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus`.

2. Zuerst generieren wir lokal einige Ablaufverfolgungsdaten. Geben Sie in Python IDLE oder einem Editor Ihrer Wahl den folgenden Code ein:

    ```python
    from opencensus.trace.tracer import Tracer

    def main():
        while True:
            valuePrompt()

    def valuePrompt():
        tracer = Tracer()
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    if __name__ == "__main__":
        main()

    ```

3. Durch Ausführen des Codes werden Sie wiederholt aufgefordert, einen Wert einzugeben. Mit jedem Eintrag wird der Wert in die Shell gedruckt, und vom OpenCensus Python-Modul wird ein entsprechendes **SpanData**-Element generiert. Das OpenCensus-Projekt definiert eine [_Ablaufverfolgung als Struktur von Spannen (Spans)_ ](https://opencensus.io/core-concepts/tracing/).
    
    ```python
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=1f07f062ac394c50925f2ae61e635e14, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='5c17a4ad6ba14299', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:15.847292Z', end_time='2018-09-15T20:42:17.615664Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=c71b4e88a22a495da61df52ce3eee3e1, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='51547c0af5f046eb', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:17.615664Z', end_time='2018-09-15T20:48:11.160314Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=b4cdcc9e6df44a8fbb6e8ddeccc1351c, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f2caacf7892744d1', parent_span_id=None, attributes={}, start_time='2018-09-15T20:48:11.175931Z', end_time='2018-09-15T20:48:12.629178Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

4. Obwohl dies für Demonstrationszwecke hilfreich ist, sollen die SpanData letztlich so ausgegeben werden, dass sie von unserem **lokalen Weiterleitungsdienst** übernommen und an Application Insights gesendet werden können. Ändern Sie Ihren Code aus dem vorherigen Schritt folgendermaßen:

    ```python
    from opencensus.trace.tracer import Tracer
    from opencensus.trace import config_integration
    from opencensus.ext.ocagent.trace_exporter import TraceExporter
    from opencensus.trace import tracer as tracer_module

    import os

    def main():
        while True:
            valuePrompt()

    def valuePrompt():
        export_LocalForwarder = TraceExporter(
        service_name=os.getenv('SERVICE_NAME', 'python-service'),
        endpoint=os.getenv('OCAGENT_TRACE_EXPORTER_ENDPOINT'))

        tracer = Tracer(exporter=export_LocalForwarder)
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    if __name__ == "__main__":
        main()

    ```

5. Wenn Sie das oben genannte Modul speichern und wieder ausführen, erhalten Sie möglicherweise einen `ModuleNotFoundError` für `grpc`. Führen Sie in diesem Fall den folgenden Befehl zum Installieren des [grpcio-Pakets](https://pypi.org/project/grpcio/) aus:

    ```console
    python -m pip install grpcio
    ```

6. Wenn Sie das oben stehende Python-Skript jetzt ausführen, sollten Sie weiterhin aufgefordert werden, Werte einzugeben, aber jetzt wird nur der Wert in die Shell gedruckt.

7. Um sicherzustellen, dass die **lokale Weiterleitung** die Überprüfungen übernimmt, überprüfen Sie die `LocalForwarder.config`-Datei. Wenn Sie die Schritte unter [Voraussetzungen](https://docs.microsoft.com/azure/application-insights/local-forwarder) befolgt haben, befindet sie sich im Verzeichnis `C:\LF-WindowsServiceHost`.

    In der Abbildung unten in der Protokolldatei sehen Sie, dass vor der Ausführung des zweiten Skripts, in dem wir einen Exporter hinzugefügt haben, `OpenCensus input BatchesReceived` gleich 0 war. Sobald wir mit der Ausführung des aktualisierten Skripts begonnen haben, wurde `BatchesReceived` um die Anzahl der eingegebenen Werte erhöht:
    
    ![Formular für neue App Insights-Ressource](./media/opencensus-python/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Starten der Überwachung im Azure-Portal

1. Sie können jetzt im Azure-Portal erneut die Seite **Übersicht** für Application Insights öffnen, um Details zu Ihrer aktuell ausgeführten Anwendung anzuzeigen. Wählen Sie **Live Metrics Stream** aus.

   ![Screenshot des Bereichs "Übersicht" mit Live Metrics Stream in einem roten Kasten](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Wenn Sie das zweite Python-Skript erneut ausführen und mit der Eingabe von Werten beginnen, werden die Ablaufverfolgungsdaten, die vom lokalen Weiterleitungsdienst in Application Insights eingehen, in Echtzeit angezeigt.

   ![Screenshot von Live Metrics Stream mit Leistungsdaten](./media/opencensus-python/0006-stream.png)

3. Navigieren Sie zurück zur Seite **Übersicht**, wählen Sie **Anwendungsübersicht** aus, um ein visuelles Layout der Abhängigkeitsbeziehungen und der zeitlichen Steuerung der Aufrufe zwischen den Komponenten Ihrer Anwendung zu erhalten.

    ![Screenshot der Basisanwendungsübersicht](./media/opencensus-python/0007-application-map.png)

    Da nur ein Methodenaufruf überwacht wurde, ist die Anwendungsübersicht nicht so interessant. Die Anwendungsübersicht kann jedoch skaliert werden, um wesentlich mehr verteilte Anwendungen zu visualisieren:

   ![Anwendungszuordnung](media/opencensus-python/application-map.png)

4. Wählen Sie **Leistung untersuchen** aus, um detaillierte Leistungsanalysen durchzuführen und die Hauptursache von Leistungseinbußen zu ermitteln.

    ![Screenshot des Leistungsbereichs](./media/opencensus-python/0008-performance.png)

5. Indem Sie **Beispiele** auswählen und dann klicken auf eines der im rechten Bereich angezeigten Beispiele klicken, wird die Detailansicht der End-to-End-Transaktionen gestartet. Von unserer Beispiel-App wird zwar nur ein einzelnes Ereignis angezeigt, eine komplexere Anwendung ermöglicht Ihnen jedoch, die End-to-End-Transaktion bis zur Ebene der Aufrufliste eines einzelnen Ereignisses zu untersuchen.

     ![Screenshot der Schnittstelle der End-to-End-Transaktion](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-python"></a>OpenCensus-Ablaufverfolgung für Python

Wir haben nur die Grundlagen der Verknüpfung von OpenCensus für Python mit der lokalen Weiterleitung und Application Insights behandelt. Die offizielle Gebrauchsanweisung umfasst weiterführende Themen wie:

* [Sampler](https://opencensus.io/api/python/trace/usage.html#samplers)
* [Flask-Integration](https://opencensus.io/api/python/trace/usage.html#flask)
* [Django-Integration](https://opencensus.io/api/python/trace/usage.html#django)
* [MySQL-Integration](https://opencensus.io/api/python/trace/usage.html#service-integration)
* [PostgreSQL](https://opencensus.io/api/python/trace/usage.html#postgresql)
  
## <a name="next-steps"></a>Nächste Schritte

* [OpenCensus Python – Benutzerhandbuch](https://opencensus.io/api/python/trace/usage.html)
* [Anwendungszuordnung](./../../azure-monitor/app/app-map.md)
* [End-to-End-Leistungsüberwachung](./../../azure-monitor/learn/tutorial-performance.md)
