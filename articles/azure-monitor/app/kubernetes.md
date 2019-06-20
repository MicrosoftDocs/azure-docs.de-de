---
title: Verwenden von Application Insights zum Überwachen Ihres Azure Kubernetes Service (AKS) oder anderer gehosteter Kubernetes-Anwendungen – Azure Monitor | Microsoft-Dokumentation
description: Azure Monitor verwendet die Service Mesh-Technologie Istio in Ihrem Kubernetes-Cluster, um Anwendungsüberwachung für jegliche gehosteten Kubernetes-Anwendungen bereitzustellen. Dadurch können Sie Application Insights-Telemetriedaten zu eingehenden und ausgehenden Anforderungen an und von Pods sammeln, die in Ihrem Cluster ausgeführt werden.
services: application-insights
author: tokaplan
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: alkaplan
ms.openlocfilehash: c94d589875195207ec6f71c35ad077cac281fda5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65555831"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Anwendungsüberwachung ohne Instrumentierung für gehostete Kubernetes-Anwendungen

> [!IMPORTANT]
> Diese Funktion befindet sich derzeit in der Public Preview.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor nutzt jetzt die Service Mesh-Technologie in Ihrem Kubernetes-Cluster, um vorgefertigte Anwendungsüberwachung für jegliche gehosteten Kubernetes-Apps bereitzustellen. Mit standardmäßigen Application Insight-Features wie [Anwendungszuordnung](../../azure-monitor/app/app-map.md) zum Modellieren Ihrer Abhängigkeiten, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) für die Überwachung in Echtzeit, leistungsfähige Visualisierungen mit dem [Standarddashboard](../../azure-monitor/app/overview-dashboard.md), [Metrik-Explorer](../../azure-monitor/platform/metrics-getting-started.md) und [Arbeitsmappen](../../azure-monitor/app/usage-workbooks.md). Mit diesem Feature können Benutzer innerhalb eines ausgewählten Kubernetes-Namespaces Leistungsengpässe und Fehlerhotspots in allen ihren Kubernetes-Workloads erkennen. Mit Nutzung Ihrer vorhandenen Service Mesh-Investitionen in Technologien wie Istio ermöglicht Azure Monitor automatisch instrumentierte App-Überwachung ohne Änderungen des Codes Ihrer Anwendung.

> [!NOTE]
> Dies ist eine von vielen Möglichkeiten, Anwendungsüberwachung in Kubernetes durchzuführen. Sie können auch alle in Kubernetes gehosteten Apps mit dem [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.md) instrumentieren, ohne dass ein Service Mesh notwendig ist. Um Kubernetes ohne Instrumentierung der Anwendung mit einem SDK zu überwachen, können Sie die folgende Methode verwenden.

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Kubernetes-Cluster](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Konsolenzugriff auf den Cluster zum Ausführen von *kubectl*.
- Eine [Application Insights-Ressource](create-new-resource.md)
- Ein Service Mesh. Wenn Istio nicht in Ihrem Cluster bereitgestellt ist, können Sie sich über das [Installieren und Verwenden von Istio in Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/istio-install) informieren.

## <a name="capabilities"></a>Funktionen

Mithilfe der Anwendungsüberwachung ohne Instrumentierung für gehostete Kubernetes-Apps können Sie Folgendes verwenden:

- [Anwendungszuordnung](../../azure-monitor/app/app-map.md)
- [Live Stream-Metriken](../../azure-monitor/app/live-stream.md)
- [Dashboards](../../azure-monitor/app/overview-dashboard.md)
- [Metrik-Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [Verteilte Ablaufverfolgung](../../azure-monitor/app/distributed-tracing.md)
- [End-to-End-Transaktionsüberwachung](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Installationsschritte

Um die Lösung zu aktivieren, werden wir die folgenden Schritte ausführen:
- Bereitstellen der Anwendung (sofern noch nicht bereitgestellt).
- Sicherstellen, dass die Anwendung Teil des Service Mesh ist.
- Beobachten der erfassten Telemetriedaten.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Konfigurieren Ihrer App zur Funktion mit einem Service Mesh

Istio unterstützt zwei Formen der [Instrumentierung eines Pods](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
In den meisten Fällen ist es am einfachsten, den Kubernetes-Namespace, der Ihre Anwendung enthält, mit der Bezeichnung *istio-injection* zu markieren:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Da das Service Mesh Daten aus dem Kabel abruft, können wir den verschlüsselten Datenverkehr nicht abfangen. Verwenden Sie für Datenverkehr, der den Cluster nicht verlässt, ein unverschlüsseltes Protokoll (z.B. HTTP). Erwägen Sie für den externen Datenverkehr, der verschlüsselt werden muss, die [Einrichtung eines SSL-Abschlusses](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) am Eingangscontroller.

Anwendungen, die außerhalb des Service Mesh ausgeführt werden, sind nicht betroffen.

### <a name="deploy-your-application"></a>Bereitstellen der Anwendung

- Stellen Sie Ihre Anwendung im *my-app-namespace*-Namespace bereit. Wenn die Anwendung bereits bereitgestellt wird, und Sie die oben beschriebene automatische Sidecar-Injection-Methode ausgeführt haben, müssen Sie Pods neu erstellen, um sicherzustellen, dass Istio die Sidecar-Injection durchführt; entweder initiieren Sie ein paralleles Update, oder Sie löschen einzelne Pods und warten, bis sie neu erstellt werden.
- Stellen Sie sicher, dass Ihre Anwendung den [Istio-Anforderungen](https://istio.io/docs/setup/kubernetes/prepare/requirements/) entspricht.

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Bereitstellen der Anwendungsüberwachung ohne Instrumentierung für gehostete Kubernetes-Apps

1. Laden Sie ein [*Application Insights-Adapter*-Release](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/) herunter, und extrahieren Sie es.
2. Navigieren Sie im Releaseordner zu */src/kubernetes/* .
3. Bearbeiten Sie *application-insights-istio-mixer-adapter-deployment.yaml*.
    - Bearbeiten Sie den Wert der Umgebungsvariablen *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY*, sodass sie den Instrumentierungsschlüssel der Application Insights-Ressource im Azure-Portal enthält, um die Telemetriedaten zu enthalten.
    - Bearbeiten Sie ggf. den Wert der Umgebungsvariablen *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES*, sodass sie eine durch Trennzeichen getrennte Liste der Namespaces enthält, für die Sie Überwachung aktivieren möchten. Lassen sie Sie leer, um alle Namespaces zu überwachen.
4. Wenden Sie *jede* unter *src/kubernetes/* gefundene YAML-Datei durch Ausführen des Folgenden an (Sie müssen sich immer noch in */src/kubernetes/* befinden):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Überprüfen der Bereitstellung

- Stellen Sie sicher, dass der Application Insights-Adapter bereitgestellt wurde:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> In einigen Fällen ist Feinabstimmung erforderlich. Verwenden Sie zum Einschließen oder Ausschließen von Telemetriedaten, die für einen einzelnen Pod gesammelt werden, die Bezeichnung *appinsights/monitoring.enabled* für diesen Pod. Dies hat Priorität vor allen namespacebasierten Konfigurationen. Legen Sie für *appinsights/monitoring.enabled* *true* fest, um den Pod einzubeziehen, und *false*, um ihn auszuschließen.

### <a name="view-application-insights-telemetry"></a>Anzeigen von Application Insights-Telemetriedaten

- Generieren Sie eine Beispielanforderung für Ihre Anwendung, um sicherzustellen, dass die Überwachung ordnungsgemäß funktioniert.
- Innerhalb von 3 bis 5 Minuten sollten Sie Telemetriedaten im Azure-Portal sehen. Überprüfen Sie den Abschnitt *Anwendungszuordnung* Ihrer Application Insights-Ressource im Portal.

## <a name="troubleshooting"></a>Problembehandlung

Unten wird der Problembehandlungablauf gezeigt, der verwendet wird, wenn Telemetriedaten nicht wie erwartet im Azure-Portal angezeigt werden.

1. Stellen Sie sicher, dass die Anwendung ausgelastet ist und einfache HTTP-Anfragen sendet/empfängt. Da die Telemetriedaten aus dem Kabel abgerufen werden, wird der verschlüsselte Datenverkehr nicht unterstützt. Wenn keine Anforderungen ein- oder ausgehen, stehen auch keine Telemetriedaten zur Verfügung.
2. Stellen Sie sicher, dass der richtige Instrumentierungsschlüssel in der Umgebungsvariablen *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* in *application-insights-istio-mixer-adapter-deployment.yaml* bereitgestellt wird. Der Instrumentierungsschlüssel befindet sich auf der Registerkarte *Übersicht* der Application Insights-Ressource im Azure-Portal.
3. Stellen Sie sicher, dass der richtige Kubernetes-Namespace in der Umgebungsvariablen *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* in *application-insights-istio-mixer-adapter-deployment.yaml* bereitgestellt wird. Lassen sie Sie leer, um alle Namespaces zu überwachen.
4. Stellen Sie sicher, dass für die Pods Ihrer Anwendung von Istio eine Sidecar-Injection durchgeführt wurde. Überprüfen Sie, ob das Sidecar von Istio auf jedem Pod vorhanden ist.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Stellen Sie sicher, dass ein Container mit dem Namen *istio-proxy* auf dem Pod ausgeführt wird.

5. Zeigen Sie die Ablaufverfolgung des Application Insights-Adapters an.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Die Anzahl der empfangenen Telemetrieelemente wird einmal pro Minute aktualisiert. Wenn von einer Minute zur anderen Minute kein Wachstum erfolgt ist, werden keine Telemetriedaten von Istio an den Adapter gesendet.
   Suchen Sie im Protokoll nach Fehlern.
6. Wenn Sie feststellen, dass in den *Application Insights für Kubernetes*-Adapter keine Telemetriedaten eingespeist wurden, überprüfen Sie die Mixer-Protokolle von Istios, um herauszufinden, warum keine Daten an den Adapter gesendet werden:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Suchen Sie insbesondere nach Fehlern in Verbindung mit der Kommunikation mit dem *applicationinsightsadapter*-Adapter.

## <a name="faq"></a>Häufig gestellte Fragen

Die neuesten Informationen zum Verlauf dieses Projekt finden Sie in den Informationen zum [Application Insights-Adapter für Istio-Mixer-Projekte auf GitHub](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Deinstallieren

Um das Produkt zu deinstallieren, führen Sie für *jede* unter *src/Kubernetes/* gefundene YAML-Datei Folgendes aus:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Zusammenwirken von Azure Monitor und Containern finden Sie unter [Azure Monitor für Container – Übersicht](../../azure-monitor/insights/container-insights-overview.md).