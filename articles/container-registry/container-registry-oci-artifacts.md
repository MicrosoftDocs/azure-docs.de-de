---
title: Push- und Pull-Vorgänge für OCI-Artefakte
description: Pushen und Pullen von OCI-Artefakten (Open Container Initiative) unter Verwendung einer privaten Containerregistrierung in Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 02/03/2021
ms.author: stevelas
ms.openlocfilehash: 8a73f295999888dab20531ffdd0fb042790a5357
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988232"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Pushen und Pullen eines OCI-Artefakts unter Verwendung einer Azure-Containerregistrierung

Sie können eine Azure-Containerregistrierung verwenden, um [OCI-Artefakte (Open Container Initiative)](container-registry-image-formats.md#oci-artifacts) sowie Docker-Containerimages und mit Docker kompatible Images zu speichern und zu verwalten.

Zur Veranschaulichung dieser Funktion wird in diesem Artikel gezeigt, wie Sie das Tool [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) verwenden, um ein Beispielartefakt (eine Textdatei) in eine Azure-Containerregistrierung zu pushen. Anschließend wird das Artefakt aus der Registrierung gepullt. In einer Azure-Containerregistrierung können verschiedene OCI-Artefakte mithilfe verschiedener, für das jeweilige Artefakt geeigneter Befehlszeilentools verwaltet werden.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Containerregistrierung**: Erstellen Sie in Ihrem Azure-Abonnement eine Containerregistrierung. Verwenden Sie beispielsweise das [Azure-Portal](container-registry-get-started-portal.md) oder die [Azure CLI](container-registry-get-started-azure-cli.md).
* **ORAS-Tool:** Laden Sie aus dem [GitHub-Repository](https://github.com/deislabs/oras/releases) ein aktuelles ORAS-Release für Ihr Betriebssystem herunter, und installieren Sie es. Das Tool wird als komprimierter Tarball (Datei vom Typ `.tar.gz`) veröffentlicht. Extrahieren und installieren Sie die Datei mithilfe der Standardverfahren für Ihr Betriebssystem.
* **Azure Active Directory-Dienstprinzipal (optional):** Erstellen Sie zur direkten Authentifizierung mit ORAS einen [Dienstprinzipal](container-registry-auth-service-principal.md) für den Zugriff auf Ihre Registrierung. Dem Dienstprinzipal muss eine Rolle wie „AcrPush“ zugewiesen sein, damit er über Berechtigungen zum Pushen und Pullen von Artefakten verfügt.
* **Azure CLI (optional):** Für die Verwendung einer individuellen Identität ist eine lokale Azure CLI-Installation erforderlich. Empfohlen wird mindestens die Version 2.0.71. Führen Sie `az --version ` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).
* **Docker (optional):** Für die Verwendung einer individuellen Identität benötigen Sie auch eine lokale Docker-Installation zur Authentifizierung mit der Registrierung. Für Docker sind Pakete erhältlich, mit denen Docker auf einem [macOS][docker-mac]-, [Windows][docker-windows]- oder [Linux][docker-linux]-System problemlos konfiguriert werden kann.


## <a name="sign-in-to-a-registry"></a>Anmelden bei einer Registrierung

In diesem Abschnitt werden zwei vorgeschlagene Workflows für die Anmeldung bei der Registrierung gezeigt (abhängig von der verwendeten Identität). Wählen Sie die für Ihre Umgebung geeignete Methode.

### <a name="sign-in-with-oras"></a>Anmelden mit ORAS

Führen Sie den Befehl `oras login` unter Verwendung eines [Dienstprinzipals](container-registry-auth-service-principal.md) mit Pushrechten aus, um sich mit der Anwendungs-ID und dem Kennwort des Dienstprinzipals bei der Registrierung anzumelden. Geben Sie den vollqualifizierten Registrierungsnamen (nur Kleinbuchstaben) an – in diesem Fall: *myregistry.azurecr.io*. Die Anwendungs-ID des Dienstprinzipals wird in der Umgebungsvariablen `$SP_APP_ID` und das Kennwort in der Variablen `$SP_PASSWD` übergeben.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Verwenden Sie `--password-stdin`, um das Kennwort aus STDIN zu lesen.

### <a name="sign-in-with-azure-cli"></a>Anmelden mit der Azure CLI

[Melden Sie sich mit Ihrer Identität bei der Azure CLI an](/cli/azure/authenticate-azure-cli), um Artefakte in die Containerregistrierung zu pushen und daraus zu pullen.

Greifen Sie dann mithilfe des Azure CLI-Befehls [az acr login](/cli/azure/acr#az-acr-login) auf die Registrierung zu. Beispiel für die Authentifizierung bei einer Registrierung namens *myregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` verwendet den Docker-Client, um ein Azure Active Directory-Token in der Datei `docker.config` festzulegen. Der Docker-Client muss installiert sein und ausgeführt werden, um den individuellen Authentifizierungsablauf durchführen zu können.

## <a name="push-an-artifact"></a>Pushen eines Artefakts

Erstellen Sie in einem lokalen Arbeitsverzeichnis eine Textdatei mit etwas Beispieltext. Beispiel in einer Bash-Shell:

```bash
echo "Here is an artifact" > artifact.txt
```

Pushen Sie diese Textdatei mithilfe des Befehls `oras push` in Ihre Registrierung. Im folgenden Beispiel wird die Beispieltextdatei in das Repository `samples/artifact` gepusht. Die Registrierung wird mit dem vollqualifizierten Registrierungsnamen *myregistry.azurecr.io* (nur Kleinbuchstaben) angegeben. Das Artefakt wird als `1.0` markiert. Das Artefakt hat einen undefinierten Typ. Dieser wird standardmäßig durch die *Medientypzeichenfolge* nach dem Dateinamen `artifact.txt` angegeben. Weitere Typen finden Sie unter [OCI Artifacts](https://github.com/opencontainers/artifacts) (OCI-Artefakte). 

**Linux oder macOS**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

Die Ausgabe für einen erfolgreichen Pushvorgang sieht in etwa wie folgt aus:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Wenn Sie die Azure CLI verwenden und Artefakte in Ihrer Registrierung verwalten möchten, können Sie Standardbefehle vom Typ `az acr` für die Imageverwaltung ausführen. Verwenden Sie also beispielsweise den Befehl [az acr repository show][az-acr-repository-show], um die Attribute des Artefakts abzurufen:

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

Die Ausgabe sieht in etwa wie folgt aus:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Pullen eines Artefakts

Führen Sie den Befehl `oras pull` aus, um das Artefakt aus Ihrer Registrierung abzurufen.

Entfernen Sie zuerst die Textdatei aus Ihrem lokalen Arbeitsverzeichnis:

```bash
rm artifact.txt
```

Führen Sie `oras pull` aus, um das Artefakt zu pullen, und geben Sie den Medientyp an, der beim Pushen des Artefakts verwendet wurde:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Vergewissern Sie sich, dass der Pullvorgang erfolgreich war:

```bash
$ cat artifact.txt
Here is an artifact
```

## <a name="remove-the-artifact-optional"></a>Entfernen des Artefakts (optional)

Das Artefakt kann mithilfe des Befehls [az acr repository delete][az-acr-repository-delete] aus der Azure-Containerregistrierung entfernt werden. Im folgenden Beispiel wird das dort gespeicherte Artefakt entfernt:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>Beispiel: Erstellen eines Docker-Images anhand eines OCI-Artefakts

Quellcode und Binärdateien zum Erstellen eines Containerimages können als OCI-Artefakte in Azure Container Registry gespeichert werden. Sie können auf ein Quellartefakt als Buildkontext für einen [ACR-Task](container-registry-tasks-overview.md) verweisen. Dieses Beispiel zeigt, wie Sie eine Dockerfile als OCI-Artefakt speichern und dann auf das Artefakt verweisen, um ein Containerimage zu erstellen.

Erstellen Sie z. B. eine einzeilige Dockerfile:

```bash
echo "FROM mcr.microsoft.com/hello-world" > hello-world.dockerfile
```

Melden Sie sich bei der Zielcontainerregistrierung an.

```azurecli
az login
az acr login --name myregistry
```

Erstellen und pushen Sie ein neues OCI-Artefakt mit dem Befehl `oras push` in die Zielregistrierung. In diesem Beispiel wird der Standardmedientyp für das Artefakt festgelegt.

```bash
oras push myregistry.azurecr.io/dockerfile:1.0 hello-world.dockerfile
```

Führen Sie den Befehl [az acr build](/cli/azure/acr#az-acr-build) aus, um das Image „hello-world“ unter Verwendung des neuen Artefakts als Buildkontext zu erstellen:

```azurecli
az acr build --registry myregistry --image builds/hello-world:v1 \
  --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/dockerfile:1.0
```

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich ausführlicher über die [ORAS-Bibliothek](https://github.com/deislabs/oras/tree/master/docs), und erfahren Sie unter anderem, wie Sie ein Manifest für ein Artefakt konfigurieren.
* Besuchen Sie das Repository [OCI Artifacts](https://github.com/opencontainers/artifacts) (OCI-Artefakte), und machen Sie sich mit Referenzinformationen zu neuen Artefakttypen vertraut.



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
