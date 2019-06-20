---
title: (VERALTET) Verwenden von Draft mit Azure Container Service und Azure Container Registry
description: Erstellen Sie einen ACS Kubernetes-Cluster und eine Azure Container Registry-Instanz, um Ihre erste Anwendung in Azure mit Draft zu erstellen.
services: container-service
author: squillace
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: fb34be09ec08957621517c957b3570cdbcfc0468
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60712669"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>(VERALTET) Verwenden von Draft mit Azure Container Service und Azure Container Registry zum Erstellen und Bereitstellen einer Anwendung in Kubernetes

> [!TIP]
> Die aktualisierte Version dieses Artikels, in der Azure Kubernetes Service verwendet wird, finden Sie unter [Verwenden von Draft mit Azure Kubernetes Service (AKS)](../../aks/kubernetes-draft.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Draft](https://aka.ms/draft) ist ein neues Open-Source-Tool, mit dem containerbasierte Anwendungen auf einfache Weise entwickelt und in Kubernetes-Clustern bereitgestellt werden können, ohne dass umfassende Kenntnisse über Docker und Kubernetes erforderlich sind oder diese installiert werden müssen. Durch Verwendung von Tools wie Draft können Sie und Ihre Teams sich auf die Erstellung der Anwendung mit Kubernetes konzentrieren und der Infrastruktur dabei etwas weniger Beachtung schenken.

Sie können Draft mit sämtlichen Docker-Imageregistrierungen und sämtlichen Kubernetes-Clustern – und auch lokal – verwenden. Dieses Tutorial zeigt, wie Sie ACS mit Kubernetes und ACR verwenden, um eine aktive, aber sichere Entwicklerpipeline in Kubernetes mit Draft zu erstellen, und wie Sie Azure DNS verwenden können, um diese Entwicklerpipeline für andere Benutzer in einer Domäne verfügbar zu machen.


## <a name="create-an-azure-container-registry"></a>Erstellen einer Azure-Containerregistrierung
Sie können [eine neue Azure Container Registry-Instanz](../../container-registry/container-registry-get-started-azure-cli.md) mühelos mit den folgenden Schritten erstellen:

1. Erstellen Sie eine Azure-Ressourcengruppe zum Verwalten der ACR-Registrierung und des Kubernetes-Clusters in ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Erstellen Sie eine ACR-Imageregistrierung mit [az acr create](/cli/azure/acr#az-acr-create), und stellen Sie sicher, dass die Option `--admin-enabled` auf `true` festgelegt ist.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Erstellen einer Azure Container Service-Instanz mit Kubernetes

Nun können Sie mit [az acs create](/cli/azure/acs#az-acs-create) einen ACS-Cluster mit „Kubernetes“ als Wert für `--orchestrator-type` erstellen.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
```

> [!NOTE]
> Da Kubernetes nicht der standardmäßige Orchestratortyp ist, müssen Sie den Switch `--orchestrator-type kubernetes` verwenden.

Wenn erfolgreich, sieht die Ausgabe in etwa wie folgt aus.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Damit verfügen Sie über einen Cluster und können nun mit dem Befehl [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) die Anmeldeinformationen importieren. Damit verfügen Sie über eine lokale Konfigurationsdatei für den Cluster. Diese ist erforderlich, damit Helm und Draft ausgeführt werden können.

## <a name="install-and-configure-draft"></a>Installieren und Konfigurieren von Draft


1. Laden Sie Draft für Ihre Umgebung von „ https://github.com/Azure/draft/releases “ herunter, und installieren Sie Draft in PATH, damit der Befehl verwendet werden kann.
2. Laden Sie Helm für Ihre Umgebung von „ https://github.com/kubernetes/helm/releases “ herunter, und [installieren Sie Helm in PATH, damit der Befehl verwendet werden kann](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Konfigurieren Sie Draft so, dass Ihre Registrierung verwendet wird und für jedes erstellte Helm-Diagramm Unterdomänen erstellt werden. Für die Konfiguration von Draft benötigen Sie Folgendes:
   - Ihren Azure Container Registry-Namen (in diesem Beispiel `draftacsdemo`)
   - Ihren Registrierungsschlüssel oder Ihr Kennwort aus `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`

   Rufen Sie `draft init` auf. Bei der Konfiguration werden Sie aufgefordert, die oben genannten Werte anzugeben. Beachten Sie, dass das URL-Format für die Registrierungs-URL der Registrierungsname (in diesem Beispiel `draftacsdemo`) plus `.azurecr.io` ist. Ihr Benutzername ist ein eigener Registrierungsname. Der Prozess sieht bei der ersten Ausführung etwa wie folgt aus:
   ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
   ```

Nun können Sie eine Anwendung bereitstellen.


## <a name="build-and-deploy-an-application"></a>Erstellen und Bereitstellen einer Anwendung

Das Draft-Repository enthält [sechs einfache Beispielanwendungen](https://github.com/Azure/draft/tree/master/examples). Klonen Sie das Repository, und verwenden Sie das [Java-Beispiel](https://github.com/Azure/draft/tree/master/examples/example-java). Wechseln Sie in das Verzeichnis „examples/java“, und geben Sie `draft create` ein, um die Anwendung zu erstellen. Sie sollte wie das folgende Beispiel aussehen.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

Die Ausgabe enthält eine Dockerfile-Datei und ein Helm-Diagramm. Für die Erstellung und Bereitstellung geben Sie einfach `draft up` ein. Die Ausgabe ist umfangreich und sollte wie im folgenden Beispiel aussehen.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Sicheres Anzeigen der Anwendung

Der Container wird jetzt in ACS ausgeführt. Verwenden Sie zum Anzeigen den Befehl `draft connect`, der eine sichere Verbindung mit der Cluster-IP mit einem bestimmten Port für Ihre Anwendung erstellt, sodass Sie sie lokal anzeigen können. Ist die Erstellung erfolgreich, suchen Sie in der ersten Zeile neben dem Indikator **SUCCESS** nach der URL für die Verbindung mit Ihrer App.

> [!NOTE]
> Wenn eine Meldung angezeigt wird, die besagt, dass keine Pods bereit waren, warten Sie einen Moment, und wiederholen Sie den Vorgang. Sie können auch mit `kubectl get pods -w` überwachen, wie die Pods in den Bereitschaftszustand versetzt werden, und anschließend den Vorgang wiederholen.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Im vorherigen Beispiel könnten Sie `curl -s http://localhost:46143` eingeben, um die Antwort `Hello World, I'm Java!` zu empfangen. Wenn Sie STRG+ oder CMD+C (je nach Betriebssystemumgebung) drücken, wird der sichere Tunnel abgebrochen, und Sie können die Iterationen fortsetzen.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Freigeben der Anwendung durch das Konfigurieren einer Bereitstellungsdomäne mit Azure DNS

Sie haben bereits die Entwickleriterationsschleife ausgeführt, die Draft in den vorherigen Schritten erstellt hat. Sie können Ihre Anwendung aber auch mit folgenden Schritten über das Internet freigeben:
1. Installieren eines Eingangs in Ihrem ACS-Cluster (um für die Anzeige der App eine öffentliche IP-Adresse bereitzustellen)
2. Delegieren Ihrer benutzerdefinierten Domäne an Azure DNS und Zuordnen Ihrer Domäne zur IP-Adresse, die ACS dem Eingangscontroller zuweist

### <a name="use-helm-to-install-the-ingress-controller"></a>Verwenden Sie Helm, um den Eingangscontroller zu installieren.
Verwenden Sie **Helm** zum Suchen und Installieren von `stable/traefik` (ein Eingangscontroller), um eingehende Anforderungen für Ihre Builds zu ermöglichen.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Legen Sie eine Überwachung für den `ingress`-Controller fest, um bei dessen Bereitstellung den externen IP-Wert zu erfassen. Bei dieser IP-Adresse handelt es sich um die Adresse, die im nächsten Abschnitt Ihrer Bereitstellungsdomäne zugeordnet wird.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

In diesem Fall lautet die externe IP für die Bereitstellungsdomäne `13.64.108.240`. Sie können nun Ihre Domäne dieser IP zuordnen.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Zuordnen der Eingangs-IP-Adresse zu einer benutzerdefinierten Unterdomäne

Draft erstellt eine Version für jedes erstellte Helm-Diagramm, d.h. jede Anwendung, an der Sie arbeiten. Jeder wird ein generierter Name zugewiesen, der in **Draft** als _Unterdomäne_ verwendet wird, zusätzlich zu der von Ihnen gesteuerten _Stammbereitstellungsdomäne_. (In diesem Beispiel wird `squillace.io` als Bereitstellungsdomäne verwendet.) Um dieses Unterdomänenverhalten zu ermöglichen, müssen Sie einen A-Datensatz für `'*.draft'` in den DNS-Einträgen für die Bereitstellungsdomäne erstellen, sodass jede generierte Unterdomäne an den Eingangscontroller des Kubernetes-Clusters weitergeleitet wird. 

Ihr Domänenanbieter weist DNS-Server auf eigene Weise zu. Um [Ihre Domänennamenserver an Azure DNS zu delegieren](../../dns/dns-delegate-domain-azure-dns.md), gehen Sie wie folgt vor:

1. Erstellen Sie eine Ressourcengruppe für Ihre Zone.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Erstellen Sie eine DNS-Zone für Ihre Domäne.
   Verwenden Sie den Befehl [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) zum Abrufen der Namenserver, für die die DNS-Steuerung für Ihre Domäne an Azure DNS delegiert wird.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Fügen Sie die abgerufenen DNS-Server dem Domänenanbieter für Ihre Bereitstellungsdomäne hinzu. So können Sie mithilfe von Azure DNS Ihre Domäne nach Wunsch umleiten. Ihre Vorgehensweise hängt vom Domänenanbieter ab. [Delegieren von Domänen an Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) enthält einige Details, die Sie kennen sollten. 
4. Sobald die Domäne an Azure DNS delegiert wurde, erstellen Sie einen A-Datensatzeintrag für Ihre Bereitstellungsdomäne, der der `ingress`-IP aus Schritt 2 des vorherigen Abschnitts zugeordnet ist.
   ```azurecli
   az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
   ```
   Die Ausgabe sieht in etwa wie folgt aus:
   ```json
   {
    "arecords": [
      {
        "ipv4Address": "13.64.108.240"
      }
    ],
    "etag": "<guid>",
    "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
    "metadata": null,
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
   }
   ```
5. Installieren Sie **Draft** neu.

   1. Entfernen Sie **Draft** aus dem Cluster, indem Sie `helm delete --purge draft` eingeben. 
   2. Installieren Sie **Draft** mit dem gleichen `draft-init`-Befehl neu, jedoch mit der Option `--ingress-enabled`:
      ```bash
      draft init --ingress-enabled
      ```
      Reagieren Sie auf die Eingabeaufforderungen wie zuvor. Allerdings müssen Sie eine weitere Frage beantworten. Verwenden Sie den vollständigen Domänenpfad, den Sie mit Azure DNS konfiguriert haben.

6. Geben Sie Ihre Domäne der obersten Ebene für eingehenden Datenverkehr (z.B. draft.example.com) ein: draft.squillace.io.
7. Wenn Sie `draft up` dieses Mal aufrufen, wird Ihre Anwendung an der URL in der Form `<appname>.draft.<domain>.<top-level-domain>` angezeigt (oder Sie können `curl` verwenden). In diesem Beispiel: `http://handy-labradoodle.draft.squillace.io`. 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>Nächste Schritte

Sie haben einen ACS Kubernetes-Cluster erstellt und können nun mithilfe von [Azure Container Registry](../../container-registry/container-registry-intro.md) weitere verschiedene Bereitstellungen dieses Szenarios erstellen. Beispielsweise können Sie einen _draft.basedomain.toplevel_-Domänen-DNS-Datensatz erstellen, mit dem Elemente einer Unterdomäne auf tieferer Ebene für bestimmte ACS-Bereitstellungen gesteuert werden.






