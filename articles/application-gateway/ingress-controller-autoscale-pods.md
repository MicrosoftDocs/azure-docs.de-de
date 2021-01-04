---
title: Automatische Skalierung von AKS-Pods mit Azure Application Gateway-Metriken
description: Dieser Artikel enthält Anweisungen, wie Sie Ihre AKS-Back-End-Pods mit Application Gateway-Metriken und Azure Kubernetes Metric Adapter skalieren können.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a8f015085baa8fffa6f208e9d8dd749e397c76c3
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397432"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Automatische Skalierung Ihrer AKS-Pods mit Application Gateway-Metriken (Betaversion)

Mit zunehmendem eingehenden Datenverkehr wird es immer wichtiger, Ihre Anwendungen bedarfsgerecht hochzuskalieren.

Im folgenden Tutorial wird erläutert, wie Sie die `AvgRequestCountPerHealthyHost`-Metrik des Application Gateway verwenden können, um Ihre Anwendung hochzuskalieren. `AvgRequestCountPerHealthyHost` misst die durchschnittlichen Anforderungen, die an eine bestimmte Back-End-Pool- und eine bestimmte Back-End-HTTP-Einstellungskombination gesendet werden.

Wir werden die folgenden beiden Komponenten verwenden:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter): Wir werden den Metrikadapter verwenden, um Application Gateway-Metriken über den Metrikserver verfügbar zu machen. Der Azure Kubernetes Metric Adapter ist ein Open-Source-Projekt unter Azure, ähnlich dem Application Gateway-Eingangscontroller. 
* [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler): Wir werden mit HPA die Application Gateway-Metriken verwenden und eine Bereitstellung zur Skalierung anstreben.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Einrichten des Azure Kubernetes Metric Adapters

1. Zuerst erstellen wir einen Azure AAD-Dienstprinzipal und weisen ihm `Monitoring Reader`-Zugriff für die Application Gateway-Ressourcengruppe zu. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Jetzt werden wir den [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) unter Verwendung des oben erstellten AAD-Dienstprinzipals bereitstellen.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principal created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Wir werden eine `ExternalMetric`-Ressource mit dem Namen `appgw-request-count-metric` erstellen. Diese Ressource weist den Metrikadapter an, die `AvgRequestCountPerHealthyHost`-Metrik für die `myApplicationGateway`-Ressource in der `myResourceGroup`-Ressourcengruppe verfügbar zu machen. Sie können das Feld `filter` verwenden, um einen bestimmten Back-End-Pool und eine bestimmte Back-End-HTTP-Einstellung im Application Gateway als Ziel auszuwählen.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Sie können jetzt eine Anforderung an den Metrikserver richten, um zu prüfen, ob die neue Metrik verfügbar gemacht wird:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Verwendung der neuen Metrik zum Hochskalieren der Bereitstellung

Nachdem wir in der Lage sind, `appgw-request-count-metric` über den Metrikserver verfügbar zu machen, sind wir bereit, unsere Zielbereitstellung mit [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) hochzuskalieren.

Im folgenden Beispiel wird eine Beispielbereitstellung von `aspnet` als Ziel verwendet. Die Pods werden hochskaliert, wenn `appgw-request-count-metric` &gt; 200 pro Pod bis zu einem Maximum von `10` Pods.

Ersetzen Sie den Namen Ihrer Zielbereitstellung, und wenden Sie die folgende automatische Skalierungskonfiguration an:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Testen Sie Ihr Setup mit einem Auslastungstesttool wie Apache Bench:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Nächste Schritte
- [**Problembehandlung für den Eingangscontroller**](ingress-controller-troubleshoot.md): Beheben Sie alle Probleme beim Eingangscontroller.