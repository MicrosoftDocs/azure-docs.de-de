---
title: Problembehandlung
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Beheben und Lösen häufiger Probleme beim Aktivieren und Verwenden von Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s '
ms.openlocfilehash: bf8c4d2040445fa3417fce02fb4b66216b21f3b5
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548867"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Problembehandlung für Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Dieses Handbuch enthält Informationen über allgemeine Probleme, die bei Verwendung von Azure Dev Spaces auftreten können.

Wenn Sie bei der Verwendung von Azure Dev Spaces ein Problem haben, erstellen Sie [im GitHub-Repository für Azure Dev Spaces ein Problem (New Issue)](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Voraussetzungen

Um Probleme effektiver zu behandeln, kann es hilfreich sein, ausführlichere Protokolle zur Überprüfung zu erstellen.

Bei Visual Studio erreichen Sie dies durch Festlegen der Umgebungsvariable `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` auf 1. Achten Sie darauf, Visual Studio neu zu starten, damit die Umgebungsvariable übernommen wird. Nach der Aktivierung werden detaillierte Protokolle in das Verzeichnis `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` geschrieben.

An der Befehlzeilenschnittstelle (CLI) können Sie während der Befehlsausführung weitere Informationen ausgeben. Verwenden Sie dazu den Schalter `--verbose`. Sie können `%TEMP%\Azure Dev Spaces` auch nach ausführlicheren Protokollen durchsuchen. Auf einem Mac finden Sie das Verzeichnis *TEMP* durch Ausführen von `echo $TMPDIR` in einem Terminalfenster. Auf einem Linux-Computer befindet sich das Verzeichnis *TEMP* für gewöhnlich unter `/tmp`. Vergewissern Sie sich außerdem, dass die Protokollierung in der [Azure CLI-Konfigurationsdatei](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables) aktiviert ist.

Azure Dev Spaces funktioniert außerdem am besten, wenn eine einzelne Instanz (oder ein Pod) gedebuggt wird. Die Datei `azds.yaml` enthält die Einstellung *replicaCount*, die die Anzahl der in Kubernetes für den Dienst ausgeführten Pods angibt. Wenn Sie die Einstellung *replicaCount* ändern, um Ihre Anwendung so zu konfigurieren, dass mehrere Pods für einen bestimmten Dienst ausgeführt werden, wird der Debugger an den ersten Pod angefügt (bei alphabetischer Auflistung). Der Debugger fügt sich einem anderen Pod an, wenn der ursprüngliche Pod recycelt wird, was möglicherweise zu einem unerwarteten Verhalten führt.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Häufige Probleme beim Aktivieren von Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Fehler „Failed to create Azure Dev Spaces controller“ (Azure Dev Spaces-Controller kann nicht erstellt werden)

Dieser Fehler kann angezeigt werden, wenn beim Erstellen des Controllers ein Fehler auftritt. Wenn es sich um einen vorübergehenden Fehler handelt, löschen Sie den Controller und erstellen Sie ihn dann erneut.

Sie können auch versuchen, den Controller zu löschen:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Verwenden Sie zum Löschen eines Controllers die Azure Dev Spaces-Befehlszeilenschnittstelle. Es ist nicht möglich, einen Controller aus Visual Studio zu löschen. Sie können die Azure Dev Spaces-Befehlszeilenschnittstelle auch nicht in Azure Cloud Shell installieren und daher einen Controller auch nicht aus Azure Cloud Shell löschen.

Wenn die Azure Dev Spaces-Befehlszeilenschnittstelle nicht installiert ist, können Sie sie zunächst mit dem folgenden Befehl installieren und anschließend Ihren Controller löschen:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>Fehler bei der Controllererstellung aufgrund der Länge des Controllernamens

Der Name eines Azure Dev Spaces-Controllers darf nicht länger als 31 Zeichen sein. Wenn beim Aktivieren von Dev Spaces in einem AKS-Cluster oder beim Erstellen eines Controllers der Name des Controllers länger als 31 Zeichen ist, wird eine Fehlermeldung angezeigt. Beispiel:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Erstellen Sie zum Beheben des Problems einen Controller mit einem anderen Namen. Beispiel:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Fehler beim Aktivieren von Dev Spaces, wenn Windows-Knotenpools einem AKS-Cluster hinzugefügt werden

Zurzeit ist Azure Dev Spaces nur für die Ausführung auf Linux-Pods und -Knoten vorgesehen. Wenn Sie einen AKS-Cluster mit einem Windows-Knotenpool verwenden, müssen Sie sicherstellen, dass Azure Dev Spaces-Pods nur auf Linux-Knoten geplant werden. Wenn die Ausführung eines Azure Dev Spaces-Pods auf einem Windows-Knoten geplant wird, wird dieser Pod nicht gestartet und bei der Aktivierung von Dev Spaces treten Fehler auf.

Fügen Sie zum Beheben des Problems dem AKS-Cluster einen [Taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) hinzu, um sicherzustellen, dass Linux-Pods nicht auf einem Windows-Knoten ausgeführt werden.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Fehler „Found no untainted Linux nodes in Ready state on the cluster. There needs to be at least one untainted Linux node in Ready state to deploy pods in 'azds' namespace.“ (Es wurden keine Linux-Knoten ohne Taint im Zustand "Bereit" auf dem Cluster gefunden. Es muss mindestens ein Linux-Knoten ohne Taint im Zustand "Bereit" vorhanden sein, damit Pods im Namespace "azds" bereitgestellt werden können.)

Azure Dev Spaces konnte keinen Controller im AKS-Cluster erstellen, da kein Knoten ohne Taint mit dem Status *Bereit* gefunden wurde, für den Pods geplant werden können. Azure Dev Spaces erfordert mindestens einen Linux-Knoten in einem Zustand *Bereit*, der die Planung von Pods ohne Angabe von Toleranzen ermöglicht.

Um das Problem zu beheben, [aktualisieren Sie die Taintkonfiguration](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) im AKS-Cluster, um sicherzustellen, dass mindestens ein Linux-Knoten die Planung von Pods ohne Angabe von Toleranzen ermöglicht. Stellen Sie außerdem sicher, dass sich mindestens ein Linux-Knoten, der die Planung von Pods ohne Angabe von Toleranzen ermöglicht, im Zustand *Bereit* befindet. Wenn Ihr Knoten sehr lange braucht, um den Zustand *Bereit* zu erreichen, können Sie versuchen, Ihren Knoten neu zu starten.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Fehler „Azure Dev Spaces CLI not installed properly“ (Azure Dev Spaces CLI ist nicht ordnungsgemäß installiert) bei der Ausführung von az aks use-dev-spaces

Ein Update der Azure Dev Spaces CLI hat den Installationspfad geändert. Wenn Sie eine frühere Version von Azure CLI als 2.0.63 verwenden, wird dieser Fehler möglicherweise angezeigt. Verwenden Sie `az --version` zum Anzeigen Ihrer Version der Azure CLI.

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Trotz der Fehlermeldung bei der Ausführung von `az aks use-dev-spaces` mit einer Version der Azure CLI vor 2.0.63 ist die Installation erfolgreich. Sie können `azds` ohne Probleme weiterhin verwenden.

Aktualisieren Sie zum Beheben des Problems die Installation von [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) mindestens auf Version 2.0.63. Mit diesem Update wird die Fehlermeldung behoben, die bei der Ausführung von `az aks use-dev-spaces` angezeigt wird. Alternativ können Sie auch weiterhin Ihre aktuelle Version der Azure CLI und der Azure Dev Spaces CLI verwenden.

### <a name="error-unable-to-reach-kube-apiserver"></a>Fehler „kube-apiserver kann nicht erreicht werden“

Dieser Fehler wird möglicherweise angezeigt, wenn Azure Dev Spaces keine Verbindung mit dem API-Server Ihres AKS-Clusters herstellen kann.

Wenn der Zugriff auf den API-Server Ihres AKS-Clusters gesperrt ist, oder wenn Sie für Ihren AKS-Cluster [von einem API-Server autorisierte IP-Adressbereiche ](../aks/api-server-authorized-ip-ranges.md) aktiviert haben, müssen Sie Ihren Cluster auch so [erstellen](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) oder [aktualisieren](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges), dass [zusätzliche Bereiche auf Grundlage Ihrer Region zugelassen werden](configure-networking.md#aks-cluster-network-requirements).

Stellen Sie sicher, dass der API-Server verfügbar ist, indem Sie kubectl-Befehle ausführen. Wenn der API-Server nicht verfügbar ist, wenden Sie sich an den AKS-Support, und versuchen Sie es noch mal, wenn der API-Server funktioniert.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Häufige Probleme beim Vorbereiten des Projekts für Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Warnung „Dockerfile could not be generated due to unsupported language“ (Aufgrund einer nicht unterstützten Sprache konnte kein Dockerfile generiert werden)
Azure Dev Spaces bietet systeminternen Support für C# und Node.js. Wenn Sie `azds prep` in einem Verzeichnis ausführen, das Code in einer dieser Sprachen enthält, erstellt Azure Dev Spaces automatisch eine entsprechende Dockerfile.

Sie können Azure Dev Spaces auch mit Code verwenden, der in anderen Sprachen geschrieben ist. Dann müssen Sie jedoch vor der erstmaligen Ausführung von `azds up` die Dockerfile manuell erstellen.

Wenn die Anwendung in einer Sprache geschrieben ist, die Azure Dev Spaces nicht nativ unterstützt, müssen Sie zum Erstellen eines Containerimages für das Ausführen des Codes eine entsprechende Dockerfile bereitstellen. Docker stellt eine [Liste der bewährten Methoden für das Schreiben von Dockerfile-Dateien](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) sowie eine [Dockerfile-Referenz](https://docs.docker.com/engine/reference/builder/) bereit, die Ihnen beim Schreiben einer für Ihre Bedürfnisse geeigneten Dockerfile-Datei behilflich sein können.

Nachdem Sie eine entsprechende Dockerfile erstellt haben, können Sie die Ausführung von `azds up` fortsetzen, um die Anwendung in Azure Dev Spaces auszuführen.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Häufige Probleme beim Starten oder Beenden von Diensten mit Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Fehler „Config file not found“ (Die Konfigurationsdatei wurde nicht gefunden):

Dieser Fehler wird möglicherweise bei der Ausführung von `azds up` angezeigt. Sowohl `azds up` als auch `azds prep` müssen über das Stammverzeichnis des Projekts ausgeführt werden, das Sie im Entwicklungsbereich ausführen möchten.

So beheben Sie dieses Problem:
1. Ändern Sie das aktuelle Verzeichnis in den Stammordner, der Dienstcode enthält. 
1. Wenn der Codeordner nicht die Datei _azds.yaml_ enthält, führen Sie `azds prep` aus, um Docker-, Kubernetes- und Azure Dev Spaces-Objekte zu generieren.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout beim Schritt „Waiting for container image build“ (Warten auf Containerimageerstellung) mit virtuellen AKS-Knoten

Dieses Timeout tritt auf, wenn Sie versuchen, mit Dev Spaces einen Dienst auszuführen, der für die Ausführung auf einem [virtuellen AKS-Knoten](../aks/virtual-nodes-portal.md) konfiguriert ist. Dev Spaces unterstützt derzeit nicht das Erstellen oder Debuggen von Diensten auf virtuellen Knoten.

Wenn Sie `azds up` mit dem `--verbose`-Schalter ausführen oder ausführliche Protokollierung in Visual Studio aktivieren, finden Sie weitere Informationen unter:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Der obige Befehl zeigt, dass der Pod des Diensts zu *virtual-node-aci-linux*, einem virtuellen Knoten, zugewiesen wurde.

Aktualisieren Sie zum Beheben des Problems das Helm-Chart für den Dienst, um alle *nodeSelector*- oder *tolerations*-Werte zu entfernen, die die Ausführung des Diensts auf einem virtuellen Knoten zulassen. Diese Werte werden in der Regel in der `values.yaml`-Datei des Diagramms definiert.

Sie können immer noch einen AKS-Cluster verwenden, auf dem das Feature des virtuellen Knotens aktiviert ist, wenn der Dienst, den Sie über Dev Spaces erstellen oder debuggen möchten, auf einem VM-Knoten ausgeführt wird. Das Ausführen eines Diensts mit Dev Spaces auf einem VM-Knoten ist die Standardkonfiguration.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Fehler „could not find a ready tiller pod“ (Es konnte kein bereiter Tiller-Pod gefunden werden) beim Starten von Dev Spaces

Dieser Fehler tritt auf, wenn der Helm-Client nicht mehr mit dem im Cluster ausgeführten Tiller-Pod kommunizieren kann.

Starten Sie die Agentknoten im Cluster neu, um das Problem zu beheben.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Fehler „release azds-\<identifier\>-\<spacename\>-\<servicename\> failed: services '\<servicename\>' already exists“ (Fehler beim Freigeben von azds-\<identifier\>-\<spacename\>-\<servicename\>: Dienst ‚\<servicename\>‘ ist bereits vorhanden) oder „Pull access denied for \<servicename\>, repository does not exist or may require 'docker login'“ (Pullzugriff für \<servicename\> verweigert, Repository ist nicht vorhanden oder erfordert ggf. „docker login“)

Diese Fehler können auftreten, wenn Sie die Ausführung von direkten Helm-Befehlen (z. B. `helm install`, `helm upgrade` oder `helm delete`) in demselben Entwicklungsbereich mit Dev Spaces-Befehlen (z. B. `azds up` und `azds down`) mischen. Sie treten auf, weil Dev Spaces über eine eigene Tiller-Instanz verfügt, die zu einem Konflikt mit Ihrer eigenen Tiller-Instanz in demselben Entwicklungsbereich führt.

Es ist kein Problem, Helm-Befehle und Dev Spaces-Befehle in demselben AKS-Cluster zu verwenden, aber für jeden Dev Spaces-fähigen Namespace sollte entweder die eine oder die andere Option gewählt werden.

Angenommen, Sie nutzen einen Helm-Befehl zum Ausführen Ihrer gesamten Anwendung in einem übergeordneten Entwicklungsbereich. Sie können aus diesem übergeordneten Bereich untergeordnete Entwicklungsbereiche erstellen, Dev Spaces zum Ausführen von einzelnen Diensten in den untergeordneten Bereichen nutzen und die Dienste zusammen testen. Wenn Sie zum Einchecken Ihrer Änderungen bereit sind, können Sie einen Helm-Befehl verwenden, um den aktualisierten Code im übergeordneten Entwicklungsbereich bereitzustellen. Vermeiden Sie die Nutzung von `azds up` zum Ausführen des aktualisierten Diensts im übergeordneten Entwicklungsbereich, da sich sonst ein Konflikt mit dem Dienst ergibt, der ursprünglich mit Helm ausgeführt wurde.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Vorhandene Dockerfile wurde nicht zum Erstellen eines Containers verwendet

Azure Dev Spaces kann so konfiguriert werden, dass es auf eine bestimmte _Dockerfile_ in Ihrem Projekt verweist. Wenn es den Anschein hat, dass Azure Dev Spaces zum Erstellen Ihrer Container nicht die erwartete _Dockerfile_-Dateiverwendet, müssen Sie Azure Dev Spaces möglicherweise explizit mitteilen, welche Dockerfile-Datei verwendet werden soll. 

Öffnen Sie zum Beheben des Problems die Datei _azds.yaml_, die mit Azure Dev Spaces in Ihrem Projekt generiert wurde. Aktualisieren Sie *configurations: develop: build: dockerfile*, sodass auf die gewünschte Dockerfile verwiesen wird. Beispiel:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Fehler „Nicht autorisiert: Authentifizierung erforderlich“ beim Versuch, ein Docker-Image aus einer privaten Registrierung zu verwenden.

Sie verwenden ein Docker-Image aus einer privaten Registrierung, die eine Authentifizierung erfordert.

Zum Beheben des Problems können Sie mithilfe von [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets) festlegen, dass in Dev Spaces das Authentifizieren und Abrufen von Images aus dieser privaten Registrierung zulässig ist. [Erstellen Sie ein Kubernetes-Geheimnis](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) in dem Namespace, in dem Sie das Image verwenden, um „imagePullSecrets“ zu verwenden. Geben Sie dann das Geheimnis in `azds.yaml` als „imagePullSecret“ an.

Nachfolgend finden Sie ein Beispiel für die Angabe von „imagePullSecrets“ in `azds.yaml`.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Durch das Festlegen von „imagePullSecrets“ in `azds.yaml` wird das „imagePullSecrets“ überschrieben, das in der `values.yaml` angegeben ist.

### <a name="error-service-cannot-be-started"></a>Fehler „Der Dienst kann nicht gestartet werden“

Dieser Fehler kann angezeigt werden, wenn der Dienstcode nicht gestartet wird. Die Ursache liegt häufig im Benutzercode. Um weitere Diagnoseinformationen zu erhalten, aktivieren Sie die ausführliche Protokollierung beim Starten des Diensts.

Verwenden Sie in der Befehlszeile den Befehl `--verbose`, um die ausführlichere Protokollierung zu aktivieren. Sie können außerdem mit dem Befehl `--output` ein Ausgabeformat angeben. Beispiel:

```cmd
azds up --verbose --output json
```

In Visual Studio:

1. Öffnen Sie **Tools > Optionen**, und wählen Sie unter **Projekte und Projektmappen** die Option **Erstellen und ausführen** aus.
2. Ändern Sie die Einstellungen für **Ausführlichkeit der MSBuild-Projektbuildausgabe:** zu **Detailliert** oder **Diagnose**.

    ![Screenshot des Dialogfelds „Tools > Optionen“](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Erneutes Ausführen eines Diensts nach der Neuerstellung des Controllers

Sie erhalten eine *Dienst kann nicht gestartet werden*-Fehlermeldung, wenn Sie versuchen, einen Dienst erneut auszuführen, nachdem Sie den Azure Dev Spaces-Controller entfernt und dann neu erstellt haben, der diesem Cluster zugeordnet ist. In diesem Fall enthält die ausführliche Ausgabe den folgenden Text:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Dieser Fehler tritt auf, da das Entfernen des Dev Spaces-Controllers keine zuvor von diesem Controller installierten Dienste entfernt. Die Neuerstellung des Controllers und der anschließende Versuch, die Dienste mithilfe des neuen Controllers auszuführen, schlagen fehl, da die alten Diensten immer noch vorhanden sind.

Um dieses Problem zu beheben, verwenden Sie den Befehl `kubectl delete`, um die alten Dienste manuell aus Ihrem Cluster zu entfernen, und führen Sie dann Dev Spaces erneut aus, um die neuen Dienste zu installieren.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Fehler „Der Dienst kann nicht gestartet werden“ bei Verwendung mehrstufiger Dockerfiles

Bei Verwendung einer mehrstufigen Dockerfile-Datei erhalten Sie eine *Dienst kann nicht gestartet werden*-Fehlermeldung. In diesem Fall enthält die ausführliche Ausgabe den folgenden Text:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Dieser Fehler tritt auf, da Azure Dev Spaces derzeit keine mehrstufigen Builds unterstützt. Um mehrstufige Builds zu vermeiden, müssen Sie Ihre Dockerfile-Datei neu schreiben.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Der Netzwerkdatenverkehr wird beim Verbinden Ihres Entwicklungscomputers nicht an Ihren AKS-Cluster weitergeleitet.

Wenn Sie [Azure Dev Spaces verwenden, um Ihren AKS-Cluster mit Ihrem Entwicklungscomputer zu verbinden](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes), kann ein Problem auftreten, bei dem der Netzwerkverkehr zwischen Ihrem Entwicklungscomputer und Ihrem AKS-Cluster nicht weitergeleitet wird.

Wenn Sie Ihren Entwicklungscomputer mit Ihrem AKS-Cluster verbinden, leitet Azure Dev Spaces den Netzwerkdatenverkehr zwischen Ihrem AKS-Cluster und Ihrem Entwicklungscomputer weiter, indem die Datei `hosts` Ihres Entwicklungscomputers geändert wird. Azure Dev Spaces erstellt in der Datei `hosts` einen Eintrag mit der Adresse des Kubernetes-Diensts, den Sie als Hostnamen ersetzen. Dieser Eintrag wird bei der Portweiterleitung verwendet, um Netzwerkdatenverkehr zwischen Ihrem Entwicklungscomputer und dem AKS-Cluster zu leiten. Wenn ein Dienst auf Ihrem Entwicklungscomputer mit dem Port des Kubernetes-Diensts, den Sie ersetzen, in Konflikt steht, kann Azure Dev Spaces keinen Netzwerkdatenverkehr für den Kubernetes-Dienst weiterleiten. Der *Windows BranchCache*-Dienst ist beispielsweise normalerweise an *0.0.0.0:80* gebunden, was einen Konflikt für Port 80 bei allen lokalen IPs verursacht.

Um dieses Problem zu beheben, müssen Sie alle Dienste oder Prozesse beenden, die mit dem Port des zu ersetzenden Kubernetes-Diensts in Konflikt stehen. Sie können Tools wie *netstat* verwenden, um zu untersuchen, welche Dienste oder Prozesse auf Ihrem Entwicklungscomputer in Konflikt stehen.

Um beispielsweise den *Windows BranchCache*-Dienst zu beenden und zu deaktivieren:
* Führen Sie `services.msc` an einer Eingabeaufforderung aus.
* Klicken Sie mit der rechten Maustaste auf *BranchCache*, und wählen Sie *Eigenschaften* aus.
* Klicken Sie auf *Beenden*.
* Optional können Sie dies deaktivieren, indem Sie den *Starttyp* auf *Deaktiviert* festlegen.
* Klicken Sie auf *OK*.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Fehler: „Keine AzureAssignedIdentity für pod:azds/azds-webhook-deployment-\<id\> in zugewiesenem Zustand gefunden“

Beim Ausführen eines Diensts unter Azure Dev Spaces in einem AKS-Cluster mit einer [verwalteten Identität](../aks/use-managed-identity.md) und installierten [verwalteten Podidentitäten](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) reagiert der Prozess nach dem Schritt für die *Diagramminstallation* ggf. nicht mehr. Wenn Sie sich *azds-injector-webhook* im Namespace *azds* ansehen, stoßen Sie ggf. auf diesen Fehler.

Für die Dienste, die von Azure Dev Spaces in Ihrem Cluster ausgeführt werden, wird die verwaltete Identität des Clusters genutzt, um mit den Back-End-Diensten von Azure Dev Spaces außerhalb des Clusters zu kommunizieren. Wenn die vom Pod verwaltete Identität installiert ist, werden Netzwerkregeln auf den Knoten Ihres Clusters konfiguriert, um alle Aufrufe von Anmeldeinformationen für die verwaltete Identität an ein [im Cluster installiertes Node Managed Identity (NMI) DaemonSet](https://github.com/Azure/aad-pod-identity#node-managed-identity) umzuleiten. Mit diesem NMI DaemonSet wird der aufrufende Pod identifiziert und sichergestellt, dass er für den Zugriff auf die angeforderte verwaltete Identität richtig bezeichnet wurde. Azure Dev Spaces kann nicht erkennen, ob in einem Cluster eine per Pod verwaltete Identität installiert ist, und auch nicht die erforderliche Konfiguration durchführen, mit der für Azure Dev Spaces-Dienste der Zugriff auf die verwaltete Identität des Clusters zugelassen wird. Da die Azure Dev Spaces-Dienste nicht für den Zugriff auf die verwaltete Identität des Clusters konfiguriert wurden, wird das Abrufen eines Azure AD-Tokens für die verwaltete Identität vom NMI DaemonSet nicht zugelassen, und die Kommunikation mit den Back-End-Diensten von Azure Dev Spaces ist nicht möglich.

Wenden Sie zum Beheben dieses Problems eine [AzurePodIdentityException](https://azure.github.io/aad-pod-identity/docs/configure/application_exception) für *azds-injector-webhook* an, und aktualisieren Sie die von Azure Dev Spaces instrumentierten Pods für den Zugriff auf die verwaltete Identität.

Erstellen Sie eine Datei mit dem Namen *webhookException.yaml*, und kopieren Sie die folgende YAML-Definition:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

Mit der obigen Datei wird ein *AzurePodIdentityException*-Objekt für *azds-injector-webhook* erstellt. Verwenden Sie `kubectl`, um dieses Objekt bereitzustellen:

```cmd
kubectl apply -f webhookException.yaml
```

Gehen Sie wie folgt vor, um die von Azure Dev Spaces instrumentierten Pods für den Zugriff auf die verwaltete Identität zu aktualisieren: Aktualisieren Sie den *Namespace* in der unten angegebenen YAML-Definition, und verwenden Sie `kubectl`, um ihn auf jeden Entwicklungsbereich anzuwenden.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

Alternativ können Sie die Objekte *AzureIdentity* und *AzureIdentityBinding* erstellen und die Podbezeichnungen für Workloads aktualisieren, die in den von Azure Dev Spaces instrumentierten Bereichen ausgeführt werden, damit der Zugriff auf die vom AKS-Cluster erstellte verwaltete Identität ermöglicht wird.

Führen Sie zum Auflisten der Details zur verwalteten Identität den folgenden Befehl für Ihren AKS-Cluster aus:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

Mit dem obigen Befehl werden die *clientId* und die *resourceId* für die verwaltete Identität ausgegeben. Beispiel:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

Gehen Sie wie folgt vor, wenn Sie ein *AzureIdentity*-Objekt erstellen möchten: Erstellen Sie eine Datei mit dem Namen *clusteridentity.yaml*, und verwenden Sie die folgende YAML-Definition, indem Sie sie mit den Details Ihrer verwalteten Identität aus dem vorherigen Befehl aktualisieren:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

Erstellen Sie für die Erstellung eines *AzureIdentityBinding*-Objekts eine Datei mit dem Namen *clusteridentitybinding.yaml*, und verwenden Sie die folgende YAML-Definition:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

Verwenden Sie `kubectl`, um die Objekte *AzureIdentity* und *AzureIdentityBinding* bereitzustellen:

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

Nach der Bereitstellung der Objekte *AzureIdentity* und *AzureIdentityBinding* besteht für alle Workloads mit der Bezeichnung *aadpodidbinding: my-label-value* Zugriff auf die verwaltete Identität des Clusters. Fügen Sie diese Bezeichnung hinzu, und stellen Sie alle Workloads erneut bereit, die in einem Entwicklungsbereich ausgeführt werden. Beispiel:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

### <a name="error-cannot-get-connection-details-for-azure-dev-spaces-controller-abc-because-it-is-in-the-failed-state-something-wrong-might-have-happened-with-your-controller"></a>Fehler „Cannot get connection details for Azure Dev Spaces Controller 'ABC' because it is in the 'Failed' state. Something wrong might have happened with your controller.“ (Für den Azure Dev Spaces-Controller „ABC“ konnten keine Verbindungsdetails abgerufen werden, da er sich in einem Fehlerzustand befindet. Möglicherweise liegt ein Fehler mit Ihrem Controller vor.)

Löschen Sie zur Behebung dieses Problems den Azure Dev Spaces-Controller aus dem Cluster, und installieren Sie ihn neu:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Azure Dev Spaces wird außerdem eingestellt. Daher empfehlen wir die [Migration zu Bridge to Kubernetes](migrate-to-bridge-to-kubernetes.md), was diverse Vorteile bietet.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Häufige Probleme bei der Verwendung von Visual Studio und Visual Studio Code mit Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Fehler „Required tools and configurations are missing“ (Erforderliche Tools und Konfigurationen fehlen)

Dieser Fehler kann beim Starten von Visual Studio Code auftreten: „[Azure Dev Spaces] Erforderliche Tools und Konfigurationen zum Erstellen und Debuggen von ‚[Projektname]‘ fehlen.“
Der Fehler weist darauf hin, dass „azds.exe“ nicht in der PATH-Umgebungsvariablen angegeben ist, wie in Visual Studio Code zu sehen ist.

Starten Sie Visual Studio Code über eine Eingabeaufforderung, bei der die PATH-Umgebungsvariable ordnungsgemäß eingerichtet ist.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Fehler „Erforderliche Tools zum Erstellen und Debuggen von ‚Projektname‘ sind veraltet.“

Dieser Fehler wird in Visual Studio Code angezeigt, wenn Sie über eine neuere Version der Visual Studio Code-Erweiterung für Azure Dev Spaces verfügen, aber eine ältere Version der Azure Dev Spaces-Befehlszeilenschnittstelle (CLI).

Laden Sie die neueste Version der Azure Dev Spaces-Befehlszeilenschnittstelle herunter, und installieren Sie sie:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Error: „Failed to find debugger extension for type:coreclr“ (Debugger-Erweiterung für Typ „coreclr“ wurde nicht gefunden)

Dieser Fehler wird möglicherweise beim Ausführen des Visual Studio Code-Debuggers angezeigt. Auf Ihrem Entwicklungscomputer ist möglicherweise die VS Code-Erweiterung für C# nicht installiert. Die Erweiterung für C# enthält Debug-Unterstützung für .NET Core (CoreCLR).

Installieren Sie zum Beheben des Problems die [VS Code-Erweiterung für C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Fehler „Der konfigurierte Debugtyp ‚coreclr‘ wird nicht unterstützt“

Dieser Fehler wird möglicherweise beim Ausführen des Visual Studio Code-Debuggers angezeigt. Auf Ihrem Entwicklungscomputer ist möglicherweise die VS Code-Erweiterung für Azure Dev Spaces nicht installiert.

Installieren Sie zum Beheben des Problems die VS Code-Erweiterung für Azure Dev Spaces.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Fehler „Ungültiger ‚cwd‘-Wert ‚/src‘. Das System kann die angegebene Datei nicht finden.“ oder „launch: program ‚/src/[Pfad zur Projektbinärdatei]‘ ist nicht vorhanden“.

Dieser Fehler wird möglicherweise beim Ausführen des Visual Studio Code-Debuggers angezeigt. Standardmäßig verwendet die VS Code-Erweiterung `src` als Arbeitsverzeichnis für das Projekt im Container. Wenn Sie Ihre `Dockerfile` aktualisiert haben, um ein anderes Arbeitsverzeichnis anzugeben, kann dieser Fehler angezeigt werden.

Aktualisieren Sie zum Beheben des Problems die Datei `launch.json` im Unterverzeichnis `.vscode` Ihres Projektordners. Ändern Sie die `configurations->cwd`-Anweisung so, dass sie auf das selbe Verzeichnis verweist, wie die `WORKDIR`-Variable, die in der `Dockerfile` Ihres Projekts definiert ist. Möglicherweise müssen Sie auch die `configurations->program`-Anweisung aktualisieren.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fehler „Das Pipe-Programm ‚azds‘ wurde unerwartet mit Code 126 beendet.“

Dieser Fehler wird möglicherweise beim Ausführen des Visual Studio Code-Debuggers angezeigt.

Um dieses Problem zu beheben, schließen und öffnen Sie dann Visual Studio Code wieder. Starten Sie den Debugger neu.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Fehler „Internal watch failed: watch ENOSPC“ beim Anfügen eines Debuggers an eine Node.js-Anwendung

Dieser Fehler tritt auf, wenn der Knoten, auf dem der Pod mit der Node.js-Anwendung ausgeführt wird, an die Sie einen Debugger anfügen möchten, den Wert *fs.inotify.max_user_watches* überschritten hat. In einigen Fällen ist möglicherweise der [Standardwert für *fs.inotify.max_user_watches* zu klein, um das direkte Anfügen eines Debuggers an einen Pod zu verarbeiten](https://github.com/Azure/AKS/issues/772).

Als vorübergehende Problemumgehung können Sie den Wert für *fs.inotify.max_user_watches* auf jedem Knoten im Cluster erhöhen und den Knoten neu starten, damit die Änderungen wirksam werden.

## <a name="other-common-issues"></a>Andere häufige Probleme

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Fehler: „azds“ wird nicht als interner oder externer Befehl, ausführbares Programm oder Batchdatei erkannt

Dieser Fehler kann auftreten, wenn `azds.exe` nicht installiert oder nicht ordnungsgemäß konfiguriert ist.

So beheben Sie dieses Problem:

1. Überprüfen Sie, ob `azds.exe` in „%ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI“ vorhanden ist. Wenn ja, fügen Sie diesen Speicherort der PATH-Umgebungsvariablen hinzu.
2. Wenn `azds.exe` nicht installiert ist, führen Sie den folgenden Befehl aus:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Autorisierungsfehler „Microsoft.DevSpaces/register/action“

Sie müssen in Ihrem Azure-Abonnement zum Verwalten von Azure Dev Spaces Zugriff als *Besitzer* oder *Mitwirkender* haben. Wenn Sie versuchen, Dev Spaces zu verwalten, und nicht über Zugriff als *Besitzer* oder *Mitwirkender* auf das zugehörige Azure-Abonnement verfügen, wird möglicherweise ein Autorisierungsfehler angezeigt. Beispiel:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Um das Problem zu beheben, registrieren Sie unter Verwendung eines Kontos mit Zugriff als *Besitzer* oder *Mitwirkender* auf das Azure-Abonnement den Namespace `Microsoft.DevSpaces` manuell:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Neue Pods werden nicht gestartet

Der Kubernetes-Initialisierer kann die PodSpec wegen Änderungen an der rollenbasierten Kubernetes-Zugriffssteuerung (RBAC) bei der Rolle *cluster-admin* (Clusteradministrator) im Cluster nicht auf neue Pods anwenden. Der neue Pod kann auch eventuell eine ungültige PodSpec besitzen, z. B. dass das dem Pod zugeordnete Dienstkonto nicht mehr vorhanden ist. Um die Pods anzuzeigen, die sich aufgrund eines Initialisiererproblems im Zustand *Ausstehend* befinden, verwenden Sie den Befehl `kubectl get pods`:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Dieses Problem kann sich auf Pods in *allen Namespaces* im Cluster auswirken, einschließlich Namespaces, in denen Azure Dev Spaces nicht aktiviert sind.

Um das Problem zu beheben, [aktualisieren Sie die Dev Spaces-Befehlszeilenschnittstelle auf die neueste Version](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools), und löschen Sie anschließend *azds InitializerConfiguration* aus dem Azure Dev Spaces-Controller:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Sobald Sie die *Azds-Initialisiererkonfiguration* aus dem Azure Dev Spaces-Controller entfernt haben, verwenden Sie `kubectl delete`, um alle Pods im Zustand *Ausstehend* zu entfernen. Nachdem alle ausstehenden Pods entfernt wurden, stellen Sie Ihre Pods erneut bereit.

Wenn auch neue Pods nach einer erneuten Bereitstellung weiterhin im Zustand *Ausstehend* verharren, verwenden Sie `kubectl delete`, um alle Pods im Zustand *Ausstehend* zu entfernen. Nachdem alle ausstehenden Pods entfernt wurden, löschen Sie den Controller aus dem Cluster, und installieren Sie ihn erneut:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Nachdem Ihr Controller neu installiert wurde, stellen Sie Ihre Pods erneut bereit.

### <a name="incorrect-azure-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Fehlerhafte Azure RBAC-Berechtigungen zum Aufrufen von Dev Spaces-Controller und -APIs

Der Benutzer, der auf den Azure Dev Spaces-Controller zugreift, benötigt Lesezugriff auf die Administrator-*kubeconfig* im AKS-Cluster. Diese Berechtigung ist zum Beispiel in der [integrierten Administratorrolle für Azure Kubernetes Service-Cluster](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions) verfügbar. Der Benutzer, der auf den Azure Dev Spaces-Controller zugreift, muss außerdem die Azure-Rolle *Mitwirkender* oder *Besitzer* für den Controller besitzen. Weitere Informationen zum Aktualisieren der Berechtigungen eines Benutzers für einen AKS-Cluster finden Sie [hier](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

So aktualisieren Sie die Azure-Rolle des Benutzers für den Controller:

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1. Navigieren Sie zu der Ressourcengruppe, die den Controller enthält, die in der Regel mit Ihrem AKS-Cluster identisch ist.
1. Aktivieren Sie das Kontrollkästchen *Ausgeblendete Typen anzeigen*.
1. Klicken Sie auf den Controller.
1. Öffnen Sie den Bereich *Zugriffssteuerung (IAM)* .
1. Klicken Sie auf die Registerkarte *Rollenzuweisungen*.
1. Klicken Sie auf *Hinzufügen* und dann auf  *Rollenzuweisung hinzufügen*.
    * Wählen Sie als *Rolle* entweder *Mitwirkender* oder *Besitzer* aus.
    * Wählen Sie unter *Zugriff zuweisen zu* die Option *Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal* aus.
    * Suchen Sie für *Auswählen* nach dem Benutzer, dem Sie Berechtigungen erteilen möchten.
1. Klicken Sie auf *Speichern*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Bei der DNS-Namensauflösung tritt für eine öffentliche URL, die einem Dev Spaces-Dienst zugeordnet ist, ein Fehler auf.

Sie können einen öffentlichen URL-Endpunkt für Ihren Dienst durch Angabe des `--enable-ingress`-Schalters zum `azds prep`-Befehl oder Auswahl des `Publicly Accessible`-Kontrollkästchens in Visual Studio konfigurieren. Der öffentliche DNS-Name wird automatisch beim Ausführen Ihres Diensts in Dev Spaces registriert. Wenn dieser DNS-Name nicht registriert ist, erhalten Sie beim Herstellen der Verbindung mit der öffentlichen URL in Ihrem Webbrowser eine *Seite kann nicht angezeigt werden*- oder *Website kann nicht erreicht werden*-Fehlermeldung.

So beheben Sie dieses Problem:

* Überprüfen Sie den Status aller URLs, die Ihren Dev Spaces-Diensten zugeordnet sind:

  ```console
  azds list-uris
  ```

* Der Status *Ausstehend* für eine URL bedeutet, dass Dev Spaces auf den Abschluss der DNS-Registrierung wartet. Manchmal dauert es einige Minuten, bis die Registrierung abgeschlossen ist. Dev Spaces öffnet zudem für die einzelnen Dienste einen localhost-Tunnel, den Sie beim Warten auf die DNS-Registrierung verwenden können.
* Wird für eine URL länger als fünf Minuten der Status *Ausstehend* angezeigt, deutet dies unter Umständen auf ein Problem mit dem externen DNS-Pod hin, der den öffentlichen Endpunkt erstellt, oder mit dem Pod des Nginx-Eingangscontrollers, der den öffentlichen Endpunkt abruft. Verwenden Sie die folgenden Befehle, um diese Pods zu löschen und festzulegen, dass sie in AKS automatisch neu erstellt werden können:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fehler „upstream connect error or disconnect/reset before headers“ (Upstream-Verbindungsfehler oder Trennung/Reset vor Headern)

Dieser Fehler kann angezeigt werden, wenn Sie versuchen, auf den Dienst zuzugreifen, beispielsweise, wenn Sie in einem Browser zur URL des Diensts wechseln. Diese Fehlermeldung gibt an, dass der Containerport nicht verfügbar ist. Dies kann aus folgenden Gründen auftreten:

* Erstellung und Bereitstellung des Containers sind noch nicht abgeschlossen. Das Problem kann vorkommen, wenn Sie `azds up` ausführen oder den Debugger starten, und dann versuchen, auf den Container zuzugreifen, bevor er erfolgreich bereitgestellt wurde.
* Die Portkonfiguration ist nicht konsistent über _Dockerfile_, Helm-Diagramm und sämtlichen Servercode hinweg, durch den ein Port geöffnet wird.

So beheben Sie dieses Problem:

1. Wenn der Container gerade erstellt/bereitgestellt wird, können Sie 2 bis 3 Sekunden warten und dann erneut versuchen, auf den Dienst zuzugreifen. 
1. Überprüfen Sie Ihre Portkonfiguration in den folgenden Objekten:
    * **[Helm-Chart](https://docs.helm.sh):** Wird durch `service.port` und `deployment.containerPort` in „values.yaml“ mit dem Befehl `azds prep` festgelegt.
    * Alle Ports, die im Anwendungscode geöffnet werden (z. B. in Node.js): `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Der Typ- oder Namespacename „MyLibrary“ konnte nicht gefunden werden

Ein verwendetes Bibliotheksprojekt konnte nicht gefunden werden. In Dev Spaces befindet sich der Buildkontext standardmäßig auf Projekt- oder Dienstebene.  

So beheben Sie dieses Problem:

1. Ändern Sie die Datei `azds.yaml`, um den Buildkontext auf die Projektmappenebene festzulegen.
2. Ändern Sie die `Dockerfile` und die `Dockerfile.develop`-Dateien so, dass sie relativ zum neuen Buildkontext ordnungsgemäß auf die Projektdateien verweisen, z. B. `.csproj`.
3. Fügen Sie eine Datei `.dockerignore` im Verzeichnis der `.sln`-Datei hinzu.
4. Aktualisieren Sie `.dockerignore` bei Bedarf mit weiteren Einträgen.

Ein Beispiel finden Sie [hier](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Die automatische horizontale Podskalierung funktioniert nicht in einem Entwicklungsbereich

Wenn Sie einen Dienst in einem Entwicklungsbereich ausführen, wird der Pod dieses Diensts [mit zusätzlichen Containern für die Instrumentierung eingefügt](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster), und für alle Container in einem Pod müssen Ressourcengrenzwerte und -anforderungen für die horizontale automatische Podskalierung festgelegt werden.

Um das Problem zu beheben, wenden Sie eine Ressourcenanforderung an, und begrenzen Sie sie auf die eingefügten Dev Spaces-Container. Sie können Ressourcenanforderungen und -grenzwerte auf den eingefügten Container (devspaces-proxy-Container) anwenden, indem Sie Ihrer Podspezifikation die Anmerkung `azds.io/proxy-resources` hinzufügen. Der Wert sollte auf ein JSON-Objekt festgelegt werden, das den Ressourcenabschnitt der Containerspezifikation für den Proxy darstellt.

Nachstehend finden Sie ein Beispiel für eine „proxy-resources“-Anmerkung, die auf Ihre Podspezifikation angewendet werden soll.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Aktivieren von Azure Dev Spaces für einen vorhandenen Namespace mit laufenden Pods

Möglicherweise verfügen Sie über einen vorhandenen AKS-Cluster und Namespace mit laufenden Pods, bei denen Sie Azure Dev Spaces aktivieren möchten.

Um Azure Dev Spaces für einen vorhandenen Namespace in einem AKS-Cluster zu aktivieren, führen Sie `use-dev-spaces` aus, und verwenden Sie `kubectl`, um alle Pods in diesem Namespace neu zu starten.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Nachdem Ihre Pods neu gestartet wurden, können Sie mit der Verwendung Ihres vorhandenen Namespace mit Azure Dev Spaces beginnen.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Aktivieren von Azure Dev Spaces in dem AKS-Cluster mit eingeschränktem ausgehendem Datenverkehr für Clusterknoten

Um Azure Dev Spaces in einem AKS-Cluster zu aktivieren, für den der ausgehende Datenverkehr von Clusterknoten eingeschränkt ist, müssen Sie die folgenden FQDNs zulassen:

| FQDN                                    | Port      | Zweck      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS: 443 | Pullen von Linux Alpine und anderen Azure Dev Spaces-Images |
| gcr.io | HTTP:443 | Pullen von Helm-/Tiller-Images|
| storage.googleapis.com | HTTP:443 | Pullen von Helm-/Tiller-Images|

Aktualisieren Sie die Firewall- oder Sicherheitskonfiguration, um Netzwerkdatenverkehr zu und von allen oben genannten vollqualifizierten Domänennamen und [Azure Dev Spaces-Infrastrukturdiensten](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations) zuzulassen.

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Fehler „Cluster \<cluster\> konnte im Abonnement \<subscriptionId\> nicht gefunden werden.“

Dieser Fehler wird möglicherweise angezeigt, wenn Ihre kubeconfig-Datei auf einen anderen Cluster oder ein anderes Abonnement abzielt, als Sie mit den clientseitigen Tools von Azure Dev Spaces verwenden möchten. Die clientseitigen Tools von Azure Dev Spaces replizieren das Verhalten von *kubectl*, bei dem über [kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)-Dateien der Cluster ausgewählt und mit ihm kommuniziert wird.

So beheben Sie dieses Problem:

* Verwenden Sie `az aks use-dev-spaces -g <resource group name> -n <cluster name>`, um den aktuellen Kontext zu aktualisieren. Dieser Befehl aktiviert auch Azure Dev Spaces in Ihrem AKS-Cluster, falls dies noch nicht geschehen ist. Alternativ können Sie `kubectl config use-context <cluster name>` verwenden, um den aktuellen Kontext zu aktualisieren.
* Verwenden Sie `az account show`, um das aktuelle Azure-Abonnement anzuzeigen, das Sie als Ziel verwenden, und zu bestätigen, dass es sich um das richtige handelt. Sie können das als Ziel verwendete Abonnement mit `az account set` ändern.

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Fehler bei Verwenden von Dev Spaces nach Rotation von AKS-Zertifikaten

Nach [Rotation der Zertifikate in Ihrem AKS-Cluster](../aks/certificate-rotation.md) schlagen bestimmte Vorgänge wie `azds space list` und `azds up` fehl. Sie müssen auch die Zertifikate für Ihren Azure Dev Spaces-Controller aktualisieren, nachdem Sie die Zertifikate auf Ihrem Cluster rotiert haben.

Um dieses Problem zu beheben, stellen Sie sicher, dass Ihre *kubeconfig* mithilfe von `az aks get-credentials` über die aktualisierten Zertifikate verfügt, und führen Sie dann den Befehl `azds controller refresh-credentials` aus. Beispiel:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
