---
title: Bewährte Methoden für die Ressourcenverwaltung
titleSuffix: Azure Kubernetes Service
description: Lernen Sie bewährte Anwendungsentwicklermethoden für die Ressourcenverwaltung in Azure Kubernetes Service (AKS) kennen.
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: fbbd5dbbc51cdb3b0d3c3783fa6ed72b76d26284
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900365"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Bewährte Anwendungsentwicklermethoden zum Verwalten von Ressourcen in Azure Kubernetes Service (AKS)

Beim Entwickeln und Ausführen von Anwendungen in Azure Kubernetes Service (AKS) sind jedoch einige wichtige Aspekte zu berücksichtigen. Die Art, in der Sie Ihre Anwendungsbereitstellungen verwalten, kann die Benutzerfreundlichkeit der Dienste beeinträchtigen, die Sie bereitstellen. Um erfolgreich zu sein, beachten Sie einige bewährte Methoden, wenn Sie Anwendungen entwickeln und in AKS ausführen.

Dieser Artikel zu bewährten Methoden konzentriert sich darauf, wie Sie Ihre Cluster und Workloads aus der Perspektive des Anwendungsentwicklers ausführen. Weitere Informationen zu bewährten Verwaltungsmethoden finden Sie unter [Bewährte Methoden der Clusterisolierung in Azure Kubernetes Service (AKS)][operator-best-practices-isolation]. In diesem Artikel wird Folgendes behandelt:

> [!div class="checklist"]
> * Podressourcenanforderungen und -grenzwerte
> * Methoden zum Entwickeln und Bereitstellen von Anwendungen mit Bridge to Kubernetes und Visual Studio Code
> * Verwenden des `kube-advisor`-Tools, um nach Problemen mit Bereitstellungen zu suchen

## <a name="define-pod-resource-requests-and-limits"></a>Definieren von Podressourcenanforderungen und -grenzwerten

**Best Practice-Anleitung** : Legen Sie Podanforderungen und -grenzwerte für alle Pods in Ihren YAML-Manifesten fest. Wenn der AKS-Cluster *Ressourcenkontingente* verwendet, wird Ihre Bereitstellung möglicherweise abgelehnt, wenn Sie diese Werte nicht definieren.

Eine primäre Methode zum Verwalten der Computeressourcen in einem AKS-Cluster ist die Verwendung von Podanforderungen und -grenzwerten. Diese Anforderungen und Grenzwerte teilen dem Kubernetes-Planer mit, welche Computeressourcen einem Pod zugewiesen werden sollten.

* **Podanforderungen für CPU/Arbeitsspeicher** definieren eine vom Pod regelmäßig benötigte festgelegte CPU-Leistung und Arbeitsspeichermenge.
    * Wenn der Kubernetes-Planer versucht, einen Pod auf einem Knoten zu platzieren, wird anhand der Podanforderungen bestimmt, auf welchem Knoten genügend Ressourcen für die Planung verfügbar wird.
    * Wenn Sie keine Podanforderung festlegen, wird diese standardmäßig auf den definierten Grenzwert festgelegt.
    * Es ist sehr wichtig, die Leistung Ihrer Anwendung zu überwachen, um diese Anforderungen anzupassen. Wenn nicht genügend Anforderungen gestellt werden, kann die Leistung der Anwendung aufgrund der Überplanung eines Knotens beeinträchtigt werden. Wenn Anforderungen überschätzt werden, kann es zu erhöhten Schwierigkeiten bei der Planung Ihrer Anwendung kommen.
* **Podgrenzwerte für CPU/Arbeitsspeicher** sind das Maximum an CPU-Leistung und Arbeitsspeichermenge, das ein Pod verwenden kann. Mithilfe von Speichergrenzwerten können Sie definieren, welche Pods bei Knoteninstabilität aufgrund unzureichender Ressourcen beendet werden sollen. Ohne geeignete Grenzwerte werden Pods beendet, bis der Ressourcendruck sich erhöht. Unabhängig davon, ob ein Pod den CPU-Grenzwert für einen bestimmten Zeitraum überschreiten kann, wird er nicht wegen Überschreitung des CPU-Limits beendet. 
    * Mithilfe von Podgrenzwerten definieren Sie, wann ein Pod die Kontrolle über den Ressourcenverbrauch verloren hat. Wird ein Grenzwert überschritten, wird der Pod zum Beenden priorisiert, um die Knotenintegrität beizubehalten und die Auswirkungen auf Pods zu minimieren, die den Knoten gemeinsam nutzen.
    * Wenn Sie keinen Podgrenzwert festlegen, wird standardmäßig der höchste verfügbare Wert auf einem bestimmten Knoten festgelegt.
    * Legen Sie keinen Podgrenzwert fest, der höher ist, als Ihre Knoten unterstützen können. Jeder AKS-Knoten reserviert eine bestimmte Menge von CPU-Leistung und Arbeitsspeichermenge für die Kubernetes-Kernkomponenten. Ihre Anwendung versucht möglicherweise, zu viele Ressourcen auf dem Knoten für die erfolgreiche Ausführung anderer Pods zu beanspruchen.
    * Auch hier ist es sehr wichtig, die Leistung Ihrer Anwendung zu verschiedenen Zeitpunkten während des Tags oder der Woche zu überwachen. Bestimmen Sie die Spitzennachfrage, und passen Sie die Podgrenzwerte den Ressourcen an, die erforderlich sind, um die maximalen Anforderungen der Anwendung zu erfüllen.

Es ist eine **bewährte Methode und sehr wichtig** , diese Anforderungen und Grenzwerte in Ihren Podspezifikationen basierend auf den oben genannten Informationen zu definieren. Wenn Sie diese Werte nicht angeben, kann der Kubernetes-Planer nicht die Ressourcen berücksichtigen, die Ihre Anwendungen für die Planung von Entscheidungen benötigen.

Wenn der Planer einen Pod auf einem Knoten mit unzureichenden Ressourcen platziert, wird die Anwendungsleistung beeinträchtigt. Es wird Clusteradministratoren dringend empfohlen, *Ressourcenkontingente* auf einem Namespace festzulegen, der von Ihnen das Festlegen von Ressourcenanforderungen und -grenzwerten anfordert. Weitere Informationen finden Sie unter [Ressourcenkontingente auf AKS-Clustern][resource-quotas].

Beim Definieren einer CPU-Anforderung oder eines Grenzwerts wird der Wert in CPU-Einheiten gemessen. 
* *1.0* Die CPU entspricht einem zugrunde liegenden virtuellen CPU-Kern auf dem Knoten. 
* Dieselbe Maßeinheit wird für GPUs verwendet.
* Sie können Anteile, gemessen in Millicore, definieren. Beispiel: *100m* ist *0,1* eines zugrunde liegenden vCPU-Kerns.

Im folgenden grundlegenden Beispiel für einen einzelnen NGINX-Pod fordert der Pod *100m* der CPU-Zeit und *128Mi* des Arbeitsspeichers an. Die Ressourcengrenzwerte für den Pod sind auf *250m* CPU und *256Mi* Arbeitsspeicher festgelegt:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Weitere Informationen zu Ressourcenverwaltung und Zuweisungen finden Sie unter [Verwalten von Computeressourcen für Container][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Entwickeln und Debuggen von Anwendungen für einen AKS-Cluster

**Best Practice-Anleitung** : Entwicklungsteams sollten einen AKS-Cluster mithilfe von Bridge to Kubernetes bereitstellen und debuggen.

Mit Bridge to Kubernetes können Sie Anwendungen direkt für einen AKS-Cluster entwickeln, debuggen und testen. Entwickler in einem Team arbeiten zusammen, um während des gesamten Lebenszyklus der Anwendung zu erstellen und zu testen. Sie können weiterhin vorhandene Tools wie Visual Studio oder Visual Studio Code verwenden. Es wird eine Erweiterung für Bridge to Kubernetes installiert, mit der Sie direkt in einem AKS-Cluster entwickeln können.

Dieser integrierte Entwicklungs- und Testprozess mit Bridge to Kubernetes verringert den Bedarf an lokalen Testumgebungen wie [minikube][minikube]. Stattdessen entwickeln und testen Sie anhand eines AKS-Clusters. Dieser Cluster kann gesichert und isoliert werden, wie im vorherigen Abschnitt zur Verwendung von Namespaces zum logischen Isolieren eines Clusters angemerkt.

Bridge to Kubernetes ist für die Verwendung mit auf Linux-Pods und -Knoten ausgeführten Anwendungen vorgesehen.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Verwenden der Visual Studio Code-Erweiterung für Kubernetes

**Best Practice-Anleitung** : Installieren und verwenden Sie die VS Code-Erweiterung für Kubernetes beim Schreiben von YAML-Manifesten. Sie können die Erweiterung auch für integrierte Bereitstellungslösungen verwenden, was Besitzern von Anwendungen helfen kann, die nur selten mit dem AKS-Cluster interagieren.

Die [Visual Studio Code-Erweiterung für Kubernetes][vscode-kubernetes] hilft Ihnen, Anwendungen in AKS zu entwickeln und bereitzustellen. Die Erweiterung bietet Intellisense für Kubernetes-Ressourcen sowie Helm-Diagramme und Vorlagen. Sie können Kubernetes-Ressourcen auch in Visual Studio Code durchsuchen, bereitstellen und bearbeiten. Die Erweiterung bietet auch eine Intellisense-Überprüfung für Ressourcenanforderungen oder Grenzwerte, die in den Podspezifikationen festgelegt werden:

![VS Code-Erweiterung für Kubernetes-Warnung zu fehlenden Arbeitsspeicherlimits](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Regelmäßiges Überprüfen auf Anwendungsprobleme mit dem Kube-Advisor

**Best Practices-Anleitung:** Führen Sie regelmäßig die neueste Version des Open-Source-Tools `kube-advisor` aus, um Probleme in Ihrem Cluster zu erkennen. Wenn Sie Ressourcenkontingente auf einen bestehenden AKS-Cluster anwenden, führen Sie zuerst `kube-advisor` aus, um Pods zu finden, die keine Ressourcenanforderungen und -grenzwerte definiert haben.

Das Tool [kube-advisor][kube-advisor] ist ein verwandtes Open-Source-Projekt für AKS, das einen Kubernetes-Cluster scannt und gefundene Probleme meldet. Eine nützliche Überprüfung ist die Identifizierung von Pods, bei denen keine Ressourcenanforderungen und -grenzwerte angegeben sind.

Das kube-advisor-Tool kann Berichte zur Ressourcenanforderung und zu Grenzwerten erstellen, die in PodSpecs für Windows- und Linux-Anwendungen fehlen, das kube-advisor-Tool selbst muss jedoch auf einem Linux-Pod geplant werden. Sie können einen Pod mit einem [Knoten-Selektor][k8s-node-selector] in der Konfiguration des Pods so planen, dass er in einem Knotenpool mit einem bestimmten Betriebssystem ausgeführt wird.

In einem AKS-Cluster, der mehrere Entwicklungsteams und Anwendungen hostet, kann es schwierig sein, Pods zu verfolgen, bei denen diese Ressourcenanforderungen und -grenzwerte nicht festgelegt wurden. Führen Sie als Best Practice regelmäßig `kube-advisor` in Ihren AKS-Clustern aus.

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel zu bewährten Methoden konzentriert sich darauf, wie Sie Ihre Cluster und Workloads aus der Perspektive des Clusteroperators ausführen. Weitere Informationen zu bewährten Verwaltungsmethoden finden Sie unter [Bewährte Methoden der Clusterisolierung in Azure Kubernetes Service (AKS)][operator-best-practices-isolation].

Um einige dieser Best Practices zu implementieren, lesen Sie folgende Artikel:

* [Entwickeln mit Bridge to Kubernetes][btk]
* [Überprüfen auf Probleme mit dem Kube-Advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
