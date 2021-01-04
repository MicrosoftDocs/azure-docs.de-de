---
title: Erstellen eines Datencontrollers unter Verwendung von Kubernetes-Tools
description: Erstellen eines Datencontrollers unter Verwendung von Kubernetes-Tools
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 051a7f506d351a17764e38c760ffba06d224cc38
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422568"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Erstellen eines Azure Arc-Datencontrollers unter Verwendung von Kubernetes-Tools

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die Übersicht im Thema [Erstellen des Azure Arc-Datencontrollers](create-data-controller.md).

Zum Erstellen des Azure Arc-Datencontrollers mithilfe von Kubernetes-Tools müssen die Kubernetes-Tools installiert sein.  In den Beispielen in diesem Artikel wird `kubectl` verwendet, allerdings können auch ähnliche Ansätze mit anderen Kubernetes-Tools verfolgt werden, z. B. dem Kubernetes-Dashboard, `oc` oder `helm`, wenn Sie mit diesen Tools und Kubernetes-YAML/JSON-Dateien vertraut sind.

[Installieren des kubectl-Tools](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Einige der unten aufgeführten Schritte zum Erstellen des Azure Arc-Datencontrollers erfordern Administratorberechtigungen für Kubernetes-Cluster.  Wenn Sie kein Kubernetes-Clusteradministrator sind, müssen Sie den Kubernetes-Clusteradministrator bitten, diese Schritte für Sie auszuführen.

### <a name="cleanup-from-past-installations"></a>Bereinigen früherer Installationen

Wenn Sie in der Vergangenheit den Azure Arc-Datencontroller auf demselben Cluster installiert und den Azure Arc-Datencontroller mit dem Befehl `azdata arc dc delete` gelöscht haben, sind möglicherweise noch Objekte auf Clusterebene vorhanden, die noch gelöscht werden müssten. Führen Sie die folgenden Befehle aus, um Objekte des Azure Arc-Datencontrollers auf Clusterebene zu löschen:

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete postgresql-11s.arcdata.microsoft.com 
kubectl delete postgresql-12s.arcdata.microsoft.com
kubectl delete clusterroles azure-arc-data:cr-arc-metricsdc-reader
kubectl delete clusterrolebindings azure-arc-data:crb-arc-metricsdc-reader
```

## <a name="overview"></a>Übersicht

Das Erstellen des Azure Arc-Datencontrollers umfasst die folgenden allgemeinen Schritte:
1. Erstellen Sie die benutzerdefinierten Ressourcendefinitionen für den Arc-Datencontroller, die Azure SQL Managend Instance-Instanz und für PostgreSQL Hyperscale. **[Erfordert Administratorberechtigungen für Kubernetes-Cluster]**
2. Erstellen Sie einen Namespace, in dem der Datencontroller erstellt wird. **[Erfordert Administratorberechtigungen für Kubernetes-Cluster]**
3. Erstellen Sie den Bootstrapperdienst, einschließlich der Replikatgruppe, des Dienstkontos, der Rolle und der Rollenbindung.
4. Erstellen Sie ein Geheimnis für den Benutzernamen und das Kennwort des Datencontrolleradministrators.
5. Erstellen Sie den Datencontroller.
   
## <a name="create-the-custom-resource-definitions"></a>Erstellen der benutzerdefinierten Ressourcendefinitionen

Führen Sie den folgenden Befehl aus, um die benutzerdefinierten Ressourcendefinitionen zu erstellen.  **[Erfordert Administratorberechtigungen für Kubernetes-Cluster]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Erstellen eines Namespace, in dem der Datencontroller erstellt wird

Führen Sie einen mit dem folgenden vergleichbaren Befehl aus, um einen neuen dedizierten Namespace zu erstellen, in dem der Datencontroller erstellt wird.  In diesem Beispiel und den restlichen Beispielen in diesem Artikel wird der Namespacename `arc` verwendet. Wenn Sie sich für einen anderen Namen entscheiden, müssen Sie diesen durchgehend verwenden.

```console
kubectl create namespace arc
```

Wenn dieser Namespace von anderen Personen verwendet wird, bei denen es sich nicht um Clusteradministratoren handelt, empfiehlt es sich, eine Namespace-Administratorrolle zu erstellen und diesen Benutzern die Rolle mithilfe einer Rollenbindung zuzuweisen.  Der Namespaceadministrator sollte über vollständige Berechtigungen für den Namespace verfügen.  Weitere Informationen dazu, wie Benutzern ein präziser abgestimmter rollenbasierter Zugriff erteilt werden kann, folgen später.

## <a name="create-the-bootstrapper-service"></a>Erstellen des Bootstrapperdiensts

Der Bootstrapperdienst verarbeitet eingehende Anforderungen zum Erstellen, Bearbeiten und Löschen benutzerdefinierter Ressourcen, beispielsweise eines Datencontrollers, einer SQL Managed Instance-Instanz oder einer PostgreSQL Hyperscale-Servergruppe.

Führen Sie den folgenden Befehl aus, um einen Bootstrapperdienst, ein Dienstkonto für den Bootstrapperdienst sowie eine Rolle und eine Rollenbindung für das Konto des Bootstrapperdiensts zu erstellen.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Überprüfen Sie mithilfe des folgenden Befehls, ob der Bootstrapperpod ausgeführt wird.  Sie müssen den Befehl möglicherweise mehrmals ausführen, bis sich der Status in `Running` ändert.

```console
kubectl get pod --namespace arc
```

Mit der Vorlagendatei „bootstrapper.yaml“ wird standardmäßig das Bootstrapper-Containerimage aus der Microsoft Container Registry (MCR) gepullt.  Wenn von Ihrer Umgebung nicht direkt auf die Microsoft Container Registry zugegriffen werden kann, können Sie wie folgt verfahren:
- Befolgen Sie die Schritte, mit denen Sie [Containerimages aus der Microsoft Container Registry pullen und in eine private Containerregistrierung pushen](offline-deployment.md) können.
- [Erstellen Sie ein Geheimnis für Imagepullvorgänge](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) für Ihre private Containerregistrierung.
- Fügen Sie dem Bootstrappercontainer ein Geheimnis für Imagepullvorgänge hinzu. Ein Beispiel sehen Sie unten.
- Ändern Sie den Imagespeicherort für das Bootstrapperimage. Ein Beispiel sehen Sie unten.

Im folgenden Beispiel wird davon ausgegangen, dass Sie ein Geheimnis für Imagepullvorgänge namens `regcred` erstellt haben, wie in der Kubernetes-Dokumentation angegeben.

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-oct-2020  <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>Erstellen eines Geheimnis für den Datencontrolleradministrator

Der Benutzername und das Kennwort des Datencontrolleradministrators werden zur Authentifizierung bei der Datencontroller-API verwendet, um Verwaltungsfunktionen auszuführen.  Wählen Sie ein sicheres Kennwort aus, und geben Sie es nur an Personen weiter, die Clusteradministratorberechtigungen benötigen.

Ein Kubernetes-Geheimnis wird als Base64-codierte Zeichenfolge gespeichert – eine für den Benutzernamen und eine für das Kennwort.

Sie können ein Onlinetool verwenden, um den gewünschten Benutzernamen und das zugehörige Kennwort mit einer Base64-Codierung zu versehen, oder Sie können abhängig von Ihrer Plattform integrierte CLI-Tools verwenden.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

Nachdem Sie den Benutzernamen und das Kennwort codiert haben, können Sie basierend auf der [Vorlagendatei](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/controller-login-secret.yaml) eine Datei erstellen und die Werte für Benutzername und Kennwort durch Ihre eigenen Werte ersetzen.

Führen Sie dann den folgenden Befehl aus, um das Geheimnis zu erstellen.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>Erstellen des Datencontrollers

Nun können Sie den Datencontroller selbst erstellen.

Erstellen Sie zunächst eine lokale Kopie der [Vorlagendatei](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/data-controller.yaml) auf Ihrem Computer, damit Sie einige Einstellungen ändern können.

Bearbeiten Sie die folgenden Einstellungen nach Bedarf:

**ERFORDERLICH**
- **location** : Ändern Sie diese Einstellung in den Azure-Standort, an dem die _Metadaten_ zum Datencontroller gespeichert werden.  Die Liste der verfügbaren Azure-Standorte finden Sie im Artikel mit der [Übersicht zum Erstellen eines Datencontrollers](create-data-controller.md).
- **resourceGroup** : Die Azure-Ressourcengruppe, in der Sie die Azure-Ressource für den Datencontroller im Azure Resource Manager erstellen möchten.  Diese Ressourcengruppe sollte in der Regel bereits vorhanden sein, wird jedoch erst benötigt, wenn Sie die Daten in Azure hochladen.
- **subscription** : Die Azure-Abonnement-GUID für das Abonnement, in dem Sie die Azure-Ressourcen erstellen möchten.

**ÜBERPRÜFUNG UND GGF. ÄNDERUNG DER STANDARDWERTE EMPFOHLEN**
- **storage..className** : Die Speicherklasse, die für die Daten- und Protokolldateien des Datencontrollers verwendet werden soll.  Wenn Sie nicht sicher sind, welche Speicherklassen in Ihrem Kubernetes-Cluster verfügbar sind, können Sie den folgenden Befehl ausführen: `kubectl get storageclass`.  Der Standardwert ist `default`. Dabei wird davon ausgegangen, dass eine Speicherklasse mit dem Namen `default` vorhanden ist, und _nicht_ , dass eine Speicherklasse vorhanden ist, die der Standardspeicherklasse entspricht.  Hinweis: Es gibt zwei className-Einstellungen, die auf die gewünschte Speicherklasse festgelegt werden müssen – eine für Daten und eine für Protokolle.
- **serviceType** : Ändern Sie den Diensttyp in `NodePort`, wenn Sie keinen LoadBalancer verwenden.  Hinweis: Es gibt zwei serviceType-Einstellungen, die geändert werden müssen.

**OPTIONAL**
- **name:** Der Standardname des Datencontrollers lautet `arc`. Sie können ihn jedoch ändern.
- **displayName** : Legen Sie diesen Wert auf denselben Wert fest wie das name-Attribut am Anfang der Datei.
- **registry** : Die Microsoft Container Registry ist die Standardregistrierung.  Wenn Sie die Images aus der Microsoft Container Registry pullen und [in eine private Containerregistrierung pushen](offline-deployment.md), geben Sie hier die IP-Adresse oder den DNS-Namen Ihrer Registrierung ein.
- **dockerRegistry** : Das Geheimnis für Imagepullvorgänge, das verwendet wird, um die Images ggf. aus einer privaten Containerregistrierung zu pullen.
- **repository** : Das Standardrepository der Microsoft Container Registry lautet `arcdata`.  Wenn Sie eine private Containerregistrierung verwenden, geben Sie den Pfad für den Ordner/das Repository mit den Containerimages der Azure Arc-fähigen Datendienste ein.
- **imageTag** : Das aktuelle Tag der letzten Version wird standardmäßig in der Vorlage verwendet, Sie können es jedoch ändern, wenn Sie eine ältere Version verwenden möchten.

Beispiel einer vollständigen YAML-Datei für einen Datencontroller:
```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: mssql-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-oct-2020 
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

Speichern Sie die bearbeitete Datei auf Ihrem lokalen Computer, und führen Sie den folgenden Befehl aus, um den Datencontroller zu erstellen:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>Überwachen des Erstellungsstatus

Das Erstellen des Controllers dauert einige Minuten. Mithilfe der folgenden Befehle können Sie den Status in einem anderen Terminalfenster überwachen:

> [!NOTE]
>  Bei den folgenden Beispielbefehlen wird davon ausgegangen, dass Sie einen Datencontroller und Kubernetes-Namespace mit dem Namen `arc` erstellt haben.  Wenn Sie einen anderen Namespace-/Datencontrollernamen verwendet haben, können Sie `arc` durch diesen Namen ersetzen.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Sie können auch den Erstellungsstatus eines bestimmten Pods überprüfen, indem Sie einen Befehl wie den folgenden ausführen.  Dies ist besonders bei der Problembehandlung hilfreich.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

Die Azure Arc-Erweiterung für Azure Data Studio bietet ein Notebook, das veranschaulichen soll, wie Sie Azure Arc-fähiges Kubernetes einrichten und anschließend für die Überwachung eines Git-Repositorys konfigurieren, das ein Beispiel für eine SQL Managed Instance-YAML-Datei enthält. Nachdem alle Verbindungen hergestellt wurden, wird eine neue SQL Managed Instance-Instanz in Ihrem Kubernetes-Cluster bereitgestellt.

Weitere Informationen finden Sie im Notebook unter **Bereitstellen einer SQL Managed Instance-Instanz mithilfe von Azure Arc-fähigem Kubernetes und Flux** in der Azure Arc-Erweiterung für Azure Data Studio.

## <a name="troubleshooting-creation-problems"></a>Beheben von Problemen bei der Erstellung

Wenn Probleme bei der Erstellung auftreten, finden Sie weitere Informationen im [Handbuch zur Problembehandlung](troubleshoot-guide.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer verwalteten SQL-Instanz mithilfe nativer Kubernetes-Tools](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Erstellen einer PostgreSQL Hyperscale-Servergruppe mithilfe nativer Kubernetes-Tools](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
