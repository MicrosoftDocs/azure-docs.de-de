---
title: Azure Functions in Kubernetes mit KEDA
description: Erfahren Sie, wie Sie Azure Functions in Kubernetes mithilfe von KEDA (Kubernetes-based Event Driven Autoscaling, [Kubernetes-basierte ereignisgesteuerte automatische Skalierung]) in der Cloud oder lokal ausführen.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, dynamisches Computing, serverlose Architektur, Kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080464"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions in Kubernetes mit KEDA

Die Azure Functions-Runtime bietet Flexibilität im Hinblick auf Art und Ort des Hostings.  [KEDA](https://github.com/kedacore/kore) (Kubernetes-based Event Driven Autoscaling, [Kubernetes-basierte ereignisgesteuerte automatische Skalierung]) lässt sich nahtlos an die Azure Functions-Runtime und -Tools koppeln, um eine ereignisgesteuerte Skalierung in Kubernetes bereitzustellen.

## <a name="how-kubernetes-based-functions-work"></a>Funktionsweise Kubernetes-basierter Funktionen

Der Azure Functions-Dienst besteht aus zwei Komponenten: einer Runtime und einem Skalierungscontroller.  Die Functions-Runtime führt Ihren Code aus.  Die Runtime enthält Logik zum Auslösen, Protokollieren und Verwalten von Funktionsausführungen.  Die andere Komponente ist ein Skalierungscontroller.  Der Skalierungscontroller überwacht die Rate der Ereignisse, die Ihre Funktion nutzen möchten, und skaliert proaktiv die Anzahl von Instanzen, die Ihre App ausführen.  Weitere Informationen finden Sie unter [Skalierung und Hosting von Azure Functions](functions-scale.md).

Die Kubernetes-basierte Version stellt die Functions-Runtime in einem [Docker-Container](functions-create-function-linux-custom-image.md) bereit, während die ereignisgesteuerte Skalierung über KEDA erfolgt.  KEDA kann eine zentrale Herunterskalierung auf 0 Instanzen (wenn keine Ereignisse stattfinden) und eine zentrale Hochskalierung auf bis zu *n* Instanzen durchführen. Zu diesem Zweck macht KEDA benutzerdefinierte Metriken für die automatische Kubernetes-Skalierung (automatische horizontale Podskalierung) verfügbar.  Durch Verwendung von Functions-Container mit KEDA können serverlose Funktionen in jedem Kubernetes-Cluster repliziert werden.  Diese Funktionen können auch mithilfe des Features für [virtuelle AKS-Knoten (Azure Kubernetes Service)](../aks/virtual-nodes-cli.md) für serverlose Infrastrukturen bereitgestellt werden.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Verwalten von KEDA und Functions in Kubernetes

Um Functions in Ihrem Kubernetes-Cluster ausführen zu können, müssen Sie die KEDA-Komponenten installieren. Sie können diese Komponente mithilfe von [Azure Functions Core Tools](functions-run-local.md) installieren.

### <a name="installing-with-the-azure-functions-core-tools"></a>Installieren mit den Azure Functions Core Tools

Standardmäßig installieren die Core Tools sowohl die KEDA- als auch die Osiris-Komponente, die die ereignisgesteuerte bzw. die HTTP-basierte Skalierung unterstützen.  Die Installation verwendet `kubectl`, das im aktuellen Kontext ausgeführt wird.

Installieren Sie KEDA in Ihrem Cluster, indem Sie den folgenden Installationsbefehl ausführen:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Bereitstellen einer Funktions-App in Kubernetes

Sie können jede beliebige Funktions-App in einem Kubernetes-Cluster bereitstellen, in dem KEDA ausgeführt wird.  Da Ihre Funktionen in einem Docker-Container ausgeführt werden, benötigt Ihr Projekt ein `Dockerfile`.  Wenn Sie noch nicht über ein Dockerfile verfügen, können Sie eins hinzufügen, indem Sie den folgenden Befehl im Stammverzeichnis Ihres Functions-Projekts ausführen:

```cli
func init --docker-only
```

Um ein Image zu erstellen und Ihre Funktionen in Kubernetes bereitzustellen, führen Sie den folgenden Befehl aus:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Ersetzen Sie `<name-of-function-deployment>` durch den Namen der Funktions-App.

Dieser Befehl erstellt eine Kubernetes-`Deployment`-Ressource, eine `ScaledObject`-Ressource und `Secrets`, einschließlich aus Ihrer `local.settings.json`-Datei importierten Umgebungsvariablen.

## <a name="removing-a-function-app-from-kubernetes"></a>Entfernen einer Funktions-App aus Kubernetes

Nach dem Bereitstellen können Sie eine Funktion entfernen, indem Sie die zugehörigen erstellten `Deployment`-, `ScaledObject`- und `Secrets`-Elemente entfernen.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Deinstallieren von KEDA aus Kubernetes

Sie können den folgenden Core Tools-Befehl ausführen, um KEDA aus einem Kubernetes-Cluster zu entfernen:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Unterstützte Trigger in KEDA

KEDA befindet sich derzeit in der Betaversion und bietet Unterstützung für die folgenden Azure Functions-Trigger:

* [Azure Storage-Warteschlangen](functions-bindings-storage-queue.md)
* [Azure Service Bus-Warteschlangen](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Erstellen einer Funktion mit einem benutzerdefinierten Image](functions-create-function-linux-custom-image.md)
* [Lokales Codieren und Testen von Azure Functions](functions-develop-local.md)
* [So funktioniert der Azure Functions-Verbrauchstarif](functions-scale.md)