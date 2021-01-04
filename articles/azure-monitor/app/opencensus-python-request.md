---
title: Nachverfolgung eingehender Anforderungen in Azure Application Insights mit OpenCensus Python | Microsoft-Dokumentation
description: Überwachen von Anforderungsaufrufen für Ihre Python-Apps über OpenCensus Python
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 4b88550ad489607bb66eb737067190d45a466a43
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607074"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Nachverfolgen eingehender Anforderungen mit OpenCensus Python

Eingehende Anforderungsdaten werden mithilfe von OpenCensus Python und dessen verschiedenen Integrationen erfasst. Verfolgen Sie eingehende Anforderungsdaten, die an Ihre auf den gängigen Webframeworks `django`, `flask` und `pyramid` basierenden Webanwendungen gesendet werden. Die Daten werden dann als `requests`-Telemetriedaten an Application Insights unter Azure Monitor gesendet.

Instrumentieren Sie zunächst Ihre Python-Anwendung mit dem aktuellen [OpenCensus Python SDK](./opencensus-python.md).

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Blacklist*, der von Microsoft nicht mehr verwendet wird. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

## <a name="tracking-django-applications"></a>Verfolgen von Django-Anwendungen

1. Laden Sie `opencensus-ext-django` von [PyPI](https://pypi.org/project/opencensus-ext-django/) herunter, installieren Sie das Paket, und instrumentieren Sie Ihre Anwendung mit der `django`-Middleware. An Ihre `django`-Anwendung gesendete eingehende Anforderungen werden nachverfolgt.

2. Fügen Sie `opencensus.ext.django.middleware.OpencensusMiddleware` in Ihrer Datei `settings.py` unter `MIDDLEWARE` ein.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Stellen Sie sicher, dass AzureExporter in Ihrer Datei `settings.py` unter `OPENCENSUS` richtig konfiguriert ist. Fügen Sie Anforderungen von URLs, die Sie nicht nachverfolgen möchten, unter `BLACKLIST_PATHS` hinzu.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Nachverfolgen von Flask-Anwendungen

1. Laden Sie `opencensus-ext-flask` von [PyPI](https://pypi.org/project/opencensus-ext-flask/) herunter, installieren Sie das Paket, und instrumentieren Sie Ihre Anwendung mit der `flask`-Middleware. An Ihre `flask`-Anwendung gesendete eingehende Anforderungen werden nachverfolgt.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. Sie können Ihre `flask` Anwendung auch über `app.config` konfigurieren. Fügen Sie Anforderungen von URLs, die Sie nicht nachverfolgen möchten, unter `BLACKLIST_PATHS` hinzu.

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Nachverfolgen von Pyramid-Anwendungen

1. Laden Sie `opencensus-ext-django` von [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) herunter, installieren Sie das Paket, und instrumentieren Sie Ihre Anwendung mit dem `pyramid`-Tween. An Ihre `pyramid`-Anwendung gesendete eingehende Anforderungen werden nachverfolgt.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Sie können Ihr `pyramid`-Tween direkt im Code konfigurieren. Fügen Sie Anforderungen von URLs, die Sie nicht nachverfolgen möchten, unter `BLACKLIST_PATHS` hinzu.

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>Nachverfolgen von FastAPI-Anwendungen

OpenCensus bietet keine Erweiterung für FastAPI. Um selbst FastAPI-Middleware zu schreiben, führen Sie die folgenden Schritte aus:

1. Die folgenden Abhängigkeiten sind erforderlich: 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. Fügen Sie [FastAPI-Middleware](https://fastapi.tiangolo.com/tutorial/middleware/) hinzu. Stellen Sie sicher das Sie den SpanKind-Server festlegen: `span.span_kind = SpanKind.SERVER`.

3. Führen Sie Ihre Anwendung aus. Aufrufe an Ihre FastAPI-Anwendung sollten automatisch nachverfolgt und Telemetrie sollte direkt in Azure Monitor protokolliert werden.

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Anwendungszuordnung](./app-map.md)
* [Verfügbarkeit](./monitor-web-app-availability.md)
* [Suchen,](./diagnostic-search.md)
* [Protokollabfragen](../log-query/log-query-overview.md)
* [Transaktionsdiagnose](./transaction-diagnostics.md)

