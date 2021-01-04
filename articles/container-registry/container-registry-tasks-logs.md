---
title: 'Anzeigen von Ausführungsprotokollen für Aufgaben: Tasks'
description: Es wird beschrieben, wie Sie von ACR Tasks generierte Ausführungsprotokolle anzeigen und verwalten.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: b2a10d4a3a2746acf38445673af994c6317c77de
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027175"
---
# <a name="view-and-manage-task-run-logs"></a>Anzeigen und Verwalten von Ausführungsprotokollen für Aufgaben

Bei jeder Aufgabenausführung in [Azure Container Registry Tasks](container-registry-tasks-overview.md) wird eine Protokollausgabe erzeugt, mit der Sie überprüfen können, ob die Aufgabenschritte erfolgreich ausgeführt wurden. 

In diesem Artikel wird beschrieben, wie Sie Ausführungsprotokolle für Aufgaben anzeigen und verwalten.

## <a name="view-streamed-logs"></a>Anzeigen von gestreamten Protokollen

Wenn Sie eine Aufgabe manuell auslösen, wird die Protokollausgabe direkt an die Konsole gestreamt. Wenn Sie eine Aufgabe beispielsweise manuell mit dem Befehl [az acr build](/cli/azure/acr#az-acr-build), [az acr run](/cli/azure/acr#az-acr-run) oder [az acr task run](/cli/azure/acr/task#az-acr-task-run) auslösen, sehen Sie, dass die Protokollausgabe an die Konsole gestreamt wird. 

Im folgenden Beispiel wird mit dem Befehl [az acr run](/cli/azure/acr#az-acr-run) eine Aufgabe manuell ausgelöst, für die ein aus derselben Registrierung gepullter Container ausgeführt wird:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Gestreamtes Protokoll:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Anzeigen von gespeicherten Protokollen 

In Azure Container Registry werden Ausführungsprotokolle für alle Aufgaben gespeichert. Sie können gespeicherte Ausführungsprotokolle im Azure-Portal anzeigen. Alternativ können Sie den Befehl [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) verwenden, um ein ausgewähltes Protokoll anzuzeigen. Aktivitätsprotokolle werden standardmäßig 30 Tage lang aufbewahrt.

Bei der automatischen Auslösung einer Aufgabe, z. B. durch die Aktualisierung eines Quellcodes, ist der Zugriff auf die gespeicherten Protokolle die *einzige* Möglichkeit, um die Ausführungsprotokolle anzuzeigen. Zu den automatischen Auslösern von Aufgaben gehören Quellcode-Commits oder Pull Requests, Basisimageaktualisierungen und Zeitgebertrigger.

Zeigen Sie Ausführungsprotokolle im Portal wie folgt an:

1. Navigieren Sie zu Ihrer Containerregistrierung.
1. Wählen Sie unter **Dienste** die Option **Aufgaben** > **Ausführungen** aus.
1. Wählen Sie eine **Ausführungs-ID** aus, um den Ausführungsstatus und die Ausführungsprotokolle anzuzeigen. Das Protokoll enthält die gleichen Informationen wie das gestreamte Protokoll (falls es generiert wird).

![Anzeigen von Aufgabenausführungen: Portalanmeldung](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Führen Sie zum Anzeigen eines Protokolls mit der Azure CLI den Befehl [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) aus, und geben Sie eine Ausführungs-ID, einen Aufgabennamen oder ein bestimmtes Image an, das von einer Buildaufgabe erstellt wurde. Wenn Sie einen Aufgabennamen angeben, wird mit dem Befehl das Protokoll für die letzte erstellte Ausführung angezeigt.

Im folgenden Beispiel wird das Protokoll für die Ausführung mit der ID *cf4* ausgegeben:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternativer Protokollspeicher

Sie können Ausführungsprotokolle für Aufgaben auf einem lokalen Dateisystem speichern oder eine andere Archivierungslösung verwenden, z. B. Azure Storage.

Erstellen Sie beispielsweise das lokale Verzeichnis *tasklogs* , und leiten Sie die Ausgabe von [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) in eine lokale Datei um:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Sie können auch lokale Protokolldateien in Azure Storage speichern. Verwenden Sie beispielsweise die [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md), das [Azure-Portal](../storage/blobs/storage-quickstart-blobs-portal.md) oder andere Verfahren, um Dateien in ein Speicherkonto hochzuladen.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr zu [Azure Container Registry Tasks](container-registry-tasks-overview.md).


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
