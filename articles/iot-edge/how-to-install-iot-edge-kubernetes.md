---
title: Installieren von IoT Edge unter Kubernetes | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie IoT Edge unter Kubernetes mit einer lokalen Entwicklungsclusterumgebung installieren können.
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65158014"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Installieren von IoT Edge unter Kubernetes (Vorschauversion)

IoT Edge kann mit Kubernetes integriert werden und es als robuste, hochverfügbare Infrastrukturschicht nutzen. Es registriert eine *benutzerdefinierte IoT Edge-Ressourcendefinition* (CRD) mit dem Kubernetes-API-Server. Darüber hinaus bietet es einen *Operator* (IoT Edge-Agent), der den von der Cloud verwalteten Sollzustand mit dem lokalen Clusterzustand abgleicht. 

Die Lebensdauer der Module wird vom Kubernetes-Scheduler verwaltet, der die Verfügbarkeit der Module aufrechterhält und deren Platzierung wählt. IoT Edge verwaltet die darüber laufende Edge-Anwendungsplattform und gleicht kontinuierlich den im IoT Hub spezifizierten Sollzustand mit dem Zustand auf dem Edge-Cluster ab. Das Edge-Anwendungsmodell ist nach wie vor das bekannte Modell, das auf IoT Edge-Modulen und -Routen basiert. Der Operator des IoT Edge-Agents führt die *automatische* Übersetzung zu den nativen Kubernetes-Konstrukten wie Pods, Bereitstellungen, Dienste usw. durch.

Hier ist ein allgemeines Architekturdiagramm:

![Kubernetes-Bogen](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Jede Komponente der Edge-Bereitstellungen bezieht sich auf einen gerätespezifischen Kubernetes-Namespace, sodass es möglich ist, die gleichen Clusterressourcen für mehrere Edge-Geräte und deren Bereitstellungen zu verwenden.

>[!NOTE]
>IoT Edge auf Kubernetes befindet sich in der [öffentlichen Vorschauversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Lokale Installation für eine schnelle Testumgebung

### <a name="prerequisites"></a>Voraussetzungen

* Kubernetes 1.10 oder höher. Wenn Sie kein bestehendes Cluster-Setup haben, können Sie [Minikube](https://kubernetes.io/docs/setup/minikube/) für eine lokale Clusterumgebung verwenden. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), der Kubernetes-Paket-Manager.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) zum Anzeigen und Interagieren mit dem Cluster.

### <a name="setup-steps"></a>Einrichtungsschritte

1. Starten Sie **Minikube**.

    ``` shell
    minikube start
    ```

1. Initialisieren Sie die **Helm**-Serverkomponente (*tiller*) in Ihrem Cluster.

    ``` shell
    helm init
    ```

1. Fügen Sie das IoT Edge-Repo hinzu, und aktualisieren Sie die Helm-Installation.

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Erstellen Sie einen IoT Hub](../iot-hub/iot-hub-create-through-portal.md), [registrieren sie ein IoT Edge-Gerät](how-to-register-device-portal.md) und notieren sie die Verbindungszeichenfolge.

1. Installieren Sie den iotedged- und IoT Edge-Agent in Ihrem Cluster.

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Öffnen Sie das Kubernetes-Dashboard im Browser.

    ```shell
    minikube dashboard
    ```

    Unter den Cluster-Namespaces sehen Sie einen für das IoT Edge-Gerät nach der Konvention *msiot-\<iothub-name>-\<edgedevice-name>* . Der IoT Edge-Agent und die iotedged-Pods sollten in diesem Namespace installiert und ausgeführt werden.

1. Fügen Sie ein simuliertes Temperatursensormodul hinzu, indem Sie die Schritte im Abschnitt [Bereitstellung eines Moduls](quickstart-linux.md#deploy-a-module) des Schnellstarts ausführen. Die Verwaltung der IoT Edge-Module erfolgt über das IoT Hub-Portal wie bei jedem anderen IoT Edge-Gerät auch. Lokale Änderungen an der Modulkonfiguration über Kubernetes-Tools werden nicht empfohlen, da sie überschrieben werden können.

1. Wenn Sie die Seite **Pods** unter dem Namespace für Edge-Geräte im Dashboard aktualisieren, werden der IoT Edge-Hub und die simulierten Sensor-Pods so aufgelistet, wie sie mit dem IoT Edge-Hub-Pod ausgeführt werden, der Daten in den IoT Hub einliest.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um alle Ressourcen zu entfernen, die durch die Edge-Bereitstellung erstellt wurden, verwenden Sie den folgenden Befehl mit dem in Schritt 5 des vorherigen Abschnitts verwendeten Namen.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Nächste Schritte

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Bereitstellen eines hochverfügbaren Edge-Gateways 

Das Edge-Gerät in einem Kubernetes-Cluster kann als IoT-Gateway für nachgeschaltete Geräte genutzt werden. Es kann sicher gegen Knotenausfälle konfiguriert werden, und bietet somit eine hohe Verfügbarkeit für Edge-Bereitstellungen. Hier finden Sie eine [ausführliche exemplarische Vorgehensweise](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) zur Verwendung von IoT Edge in diesem Szenario.