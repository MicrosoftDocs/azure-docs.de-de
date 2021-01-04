---
title: Bereinigen von Tags und Manifesten
description: Verwenden Sie einen Bereinigungsbefehl zum Löschen mehrerer Tags und Manifeste aus einer Azure-Containerregistrierung, basierend auf dem Alter und einem Tagfilter, und planen Sie optional Bereinigungsvorgänge.
ms.topic: article
ms.date: 11/10/2020
ms.openlocfilehash: 406a1f231af57407e9475a8888b68aad9d88dcb3
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445114"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Automatisches Bereinigen von Images aus einer Azure-Containerregistrierung

Wenn Sie eine Azure-Containerregistrierung als Teil eines Entwicklungsworkflows verwenden, kann die Registrierung schnell mit Bildern oder anderen Artefakten aufgefüllt werden, die nach kurzer Zeit nicht mehr benötigt werden. Sie können alle Tags löschen, die älter als eine bestimmte Dauer sind oder einem angegebenen Namensfilter entsprechen. Zum schnellen Löschen mehrerer Artefakte wird in diesem Artikel der Befehl `acr purge` eingeführt, den Sie als bedarfsgesteuerte oder [geplante](container-registry-tasks-scheduled.md) ACR-Aufgabe ausführen können. 

Der Befehl `acr purge` wird zurzeit in einem öffentlichen Containerimage (`mcr.microsoft.com/acr/acr-cli:0.3`) verteilt, das aus Quellcode im Repository [acr-cli](https://github.com/Azure/acr-cli) auf GitHub erstellt wurde.

Sie können die Azure Cloud Shell oder eine lokale Installation der Azure CLI verwenden, um die ACR-Aufgabenbeispiele in diesem Artikel auszuführen. Für die lokale Verwendung ist mindestens Version 2.0.76 erforderlich. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install]. 

> [!IMPORTANT]
> Diese Funktion steht derzeit als Vorschau zur Verfügung. Vorschauversionen werden Ihnen zur Verfügung gestellt, wenn Sie die [zusätzlichen Nutzungsbedingungen][terms-of-use] akzeptieren. Einige Aspekte dieses Features werden bis zur allgemeinen Verfügbarkeit unter Umständen noch geändert.

> [!WARNING]
> Verwenden Sie den Befehl `acr purge` mit Vorsicht: Gelöschte Imagedaten sind NICHT WIEDERHERSTELLBAR. Wenn Sie Systeme haben, die Images nach dem Manifesthash pullen (und nicht nach dem Imagenamen), sollten Sie keine Images ohne Tags bereinigen. Wenn Sie Images ohne Tags löschen, hindern Sie diese Systeme daran, die Images aus Ihrer Registrierung zu löschen. Erwägen Sie statt des Pullens nach Manifest die Einführung eines *eindeutigen Tagging*-Schemas. Dies ist eine [bewährte Methode](container-registry-image-tag-version.md).

Informationen zum Löschen einzelner Imagetags oder Manifeste mithilfe von Azure CLI-Befehlen finden Sie unter [Löschen von Containerimages in Azure Container Registry](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Verwenden des Bereinigungsbefehls

Der Containerbefehl `acr purge` löscht Images nach Tag in einem Repository, die einem Namensfilter entsprechen und älter als eine angegebene Dauer ist. Standardmäßig werden nur Tagverweise gelöscht, nicht die zugrunde liegenden [Manifeste](container-registry-concepts.md#manifest) und Ebenendaten. Der Befehl verfügt über eine Option, um auch Manifeste zu löschen. 

> [!NOTE]
> `acr purge` löscht kein Imagetag oder Repository, wo das Attribut `write-enabled` auf `false` festgelegt ist. Informationen hierzu finden Sie unter [Sperren von Containerimages in einer Azure-Containerregistrierung](container-registry-image-lock.md).

`acr purge` ist darauf ausgelegt, als Containerbefehl in einer [ACR-Aufgabe](container-registry-tasks-overview.md) ausgeführt zu werden, sodass er sich automatisch bei der Registrierung authentifiziert, in der die Aufgabe ausgeführt wird, und dort Aktionen ausführt. Die Aufgabenbeispiele in diesem Artikel verwenden den [Befehlsalias](container-registry-tasks-reference-yaml.md#aliases)`acr purge` anstelle eines vollqualifizierten Containerimagebefehls.

Geben Sie beim Ausführen des Befehls `acr purge` mindestens Folgendes an:

* `--filter`: Ein Repository und ein *regulärer Ausdruck* zum Filtern von Tags im Repository. Beispiele: `--filter "hello-world:.*"` gleicht alle Tags im Repository `hello-world` ab, und `--filter "hello-world:^1.*"` gleicht Tags ab, die mit `1` beginnen. Übergeben Sie mehrere `--filter`-Parameter, um mehrere Repositorys zu bereinigen.
* `--ago`: Eine Go-artige [Dauerzeichenfolge](https://golang.org/pkg/time/), die eine Dauer angibt, nach der Images gelöscht werden sollen. Die Dauer besteht aus einer Abfolge von einer oder mehreren Dezimalzahlen, von denen jede über ein Einheitensuffix verfügt. Gültige Zeiteinheiten sind z. B. „d“ für Tage, „h“ für Stunden und „m“ für Minuten. `--ago 2d3h6m` beispielsweise wählt alle gefilterten Images aus, die zuletzt vor mehr als 2 Tagen, 3 Stunden und 6 Minuten geändert wurden, und `--ago 1.5h` wählt Images aus, die zuletzt vor mehr als 1,5 Stunden geändert wurden.

`acr purge` unterstützt mehrere optionale Parameter. Die folgenden zwei werden in Beispielen in diesem Artikel verwendet:

* `--untagged`: Gibt an, dass Manifeste ohne zugeordnete Tags (*nicht markierte Manifeste*) gelöscht werden.
* `--dry-run`: Gibt an, dass keine Daten gelöscht werden, aber die Ausgabe ist identisch mit der, als ob der Befehl ohne dieses Flag ausgeführt wird. Dieser Parameter ist hilfreich beim Testen eines Bereinigungsbefehls, um sicherzustellen, dass er nicht versehentlich Daten löscht, die Sie beibehalten möchten.
* `--keep`: Dieser Parameter gibt an, dass die aktuelle Anzahl x der zu löschenden Tags beibehalten wird.

Zur Anzeige weiterer Parameter führen Sie `acr purge --help` aus. 

Der Befehl `acr purge` unterstützt weitere Funktionen von ACR Tasks-Befehlen (einschließlich [Run-Variablen](container-registry-tasks-reference-yaml.md#run-variables) und gestreamte und für den späteren Abruf gespeicherte [Ausführungsprotokolle für Aufgaben](container-registry-tasks-logs.md)).

### <a name="run-in-an-on-demand-task"></a>Ausführen einer bedarfsgesteuerten Aufgabe

Das folgende Beispiel verwendet den Befehl [az acr run][az-acr-run], um den Befehl `acr purge` bedarfsgesteuert auszuführen. In diesem Beispiel werden alle Imagetags und Manifeste im Repository `hello-world` in *myregistry* gelöscht, die vor mehr als 1 Tag geändert wurden. Der Containerbefehl wird mithilfe einer Umgebungsvariablen übergeben. Die Aufgabe wird ohne Quellkontext ausgeführt.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Ausführen in einer geplanten Aufgabe

Im folgenden Beispiel wird der Befehl [az acr task create][az-acr-task-create] verwendet, um eine täglich [geplante ACR-Aufgabe](container-registry-tasks-scheduled.md) zu erstellen. Die Aufgabe bereinigt Tags, die vor mehr als 7 Tagen im Repository `hello-world` geändert wurden. Der Containerbefehl wird mithilfe einer Umgebungsvariablen übergeben. Die Aufgabe wird ohne Quellkontext ausgeführt.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Führen Sie den Befehl [az acr task show][az-acr-task-show] aus, um zu sehen, ob der Timertrigger konfiguriert ist.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Bereinigen einer großen Anzahl von Tags und Manifesten

Das Bereinigen einer großen Anzahl von Tags und Manifesten kann mehrere Minuten oder länger dauern. Um Tausende von Tags und Manifesten zu bereinigen, muss der Befehl möglicherweise länger als die Standardtimeoutzeit von 600 Sekunden für eine bedarfsgesteuerte Aufgabe oder von 3600 Sekunden für eine geplante Aufgabe ausgeführt werden. Wenn die Timeoutzeit überschritten wird, wird nur eine Teilmenge der Tags und Manifeste gelöscht. Um sicherzustellen, dass eine umfangreiche Bereinigung abgeschlossen wird, übergeben Sie den Parameter `--timeout`, um den Wert zu erhöhen. 

Beispielsweise wird mit der folgenden bedarfsgesteuerten Aufgabe eine Timeoutzeit von 3600 Sekunden (1 Stunde) festgelegt:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Beispiel: Geplantes Bereinigen mehrerer Repositorys in einer Registrierung

In diesem Beispiel wird die Verwendung von `acr purge` zum regelmäßigen bereinigen mehrerer Repositorys in einer Registrierung durchlaufen. Angenommen, Sie verfügen über eine Entwicklungspipeline, mit der Bilder in Repositorys `samples/devimage1` und `samples/devimage2` per Push übertragen werden. Sie importieren regelmäßig Sie Entwicklungsimages in ein Produktionsrepository für Ihre Bereitstellungen, sodass Sie die Entwicklungsimages nicht mehr benötigen. Wöchentlich bereinigen Sie die Repositorys `samples/devimage1` und `samples/devimage2`, um die Arbeit der nächsten Woche vorzubereiten.

### <a name="preview-the-purge"></a>Anzeigen einer Vorschau der Bereinigung

Vor dem Löschen von Daten empfiehlt es sich, eine bedarfsgesteuerte Bereinigungsaufgabe mithilfe des-Parameters `--dry-run` durchzuführen. Diese Option gestattet es Ihnen, die Tags und Manifeste anzuzeigen, die vom Befehl bereinigt werden, ohne Daten zu entfernen. 

Im folgenden Beispiel wählt der Filter in jedem Repository alle Tags aus. Der Parameter `--ago 0d` gleicht Images jeden Alters in den Repositorys ab, die den Filtern entsprechen. Ändern Sie die Auswahlkriterien nach Bedarf für Ihr Szenario. Der Parameter `--untagged` gibt an, dass zusätzlich zu Tags auch Manifeste gelöscht werden sollen. Der Containerbefehl wird mithilfe einer Umgebungsvariablen an den Befehl [az acr run][az-acr-run] übergeben.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Überprüfen Sie die Befehlsausgabe, um die Tags und Manifeste anzuzeigen, die den Auswahlparametern entsprechen. Da der Befehl mit `--dry-run` ausgeführt wird, werden keine Daten gelöscht.

Beispielausgabe:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Planen der Bereinigung

Nachdem Sie den Trockenlauf überprüft haben, erstellen Sie eine geplante Aufgabe, um die Bereinigung zu automatisieren. Im folgenden Beispiel wird eine wöchentliche Aufgabe für Sonntag um 1:00 UTC geplant, um den vorherigen Bereinigungsbefehl auszuführen:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Führen Sie den Befehl [az acr task show][az-acr-task-show] aus, um zu sehen, ob der Timertrigger konfiguriert ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu weiteren Optionen zum [Löschen von Imagedaten](container-registry-delete.md) in Azure Container Registry.

Weitere Informationen zum Speichern von Images finden Sie unter [Containerimagespeicher in Azure Container Registry](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

