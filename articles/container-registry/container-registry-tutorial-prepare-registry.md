---
title: 'Tutorial: Erstellen einer Registrierung mit Georeplikation'
description: Es wird beschrieben, wie Sie eine Azure-Containerregistrierung erstellen, die Georeplikation konfigurieren, ein Docker-Image vorbereiten und es für die Registrierung bereitstellen. Dieses Tutorial ist der erste Teil einer dreiteiligen Reihe.
ms.topic: tutorial
ms.date: 06/30/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 804f07762bef596f4631fbc5f694ecc6b308bfad
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027226"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Tutorial: Vorbereiten einer georeplizierten Azure-Containerregistrierung

Eine Azure-Containerregistrierung ist eine private Docker-Registrierung, die in Azure bereitgestellt wird und die Sie netzwerknah zu Ihren Bereitstellungen anordnen können. In diesen drei Tutorialartikeln wird beschrieben, wie Sie die Georeplikation zum Bereitstellen einer ASP.NET Core-Webanwendung, die in einem Linux-Container ausgeführt wird, auf zwei Instanzen vom Typ [Web-Apps für Container](../app-service/index.yml) verwenden. Es wird veranschaulicht, wie das Image von Azure aus dem jeweils nächstgelegenen georeplizierten Repository automatisch auf den einzelnen Web-App-Instanzen bereitgestellt wird.

Dieses Tutorial ist der erste Teil einer dreiteiligen Serie:

> [!div class="checklist"]
> * Erstellen einer georeplizierten Azure-Containerregistrierung
> * Klonen des Anwendungsquellcodes von GitHub
> * Erstellen eines Docker-Containerimage aus der Anwendungsquelle
> * Übertragen des Containerimage per Push an Ihre Registrierung

In nachfolgenden Tutorials stellen Sie den Container aus Ihrer privaten Registrierung für eine Web-App bereit, die in zwei Azure-Regionen ausgeführt wird. Anschließend aktualisieren Sie den Code in der Anwendung und aktualisieren beide Web-App-Instanzen mit einem einzelnen `docker push`-Element für Ihre Registrierung.

## <a name="before-you-begin"></a>Voraussetzungen

Für dieses Tutorial wird eine lokale Installation der Azure CLI (ab Version 2.0.31) benötigt. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

Sie sollten mit zentralen Docker-Konzepten wie Containern und Containerimages sowie mit grundlegenden Docker CLI-Befehlen vertraut sein. Eine Einführung in Container finden Sie bei Bedarf unter [Get started with Docker]( https://docs.docker.com/get-started/) (Erste Schritte mit Docker).

Für dieses Tutorial wird eine lokale Docker-Installation benötigt. Docker-Installationsanleitungen stehen für Systeme unter [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung.

Azure Cloud Shell umfasst keine Docker-Komponenten, die zum Abschließen der einzelnen Schritte dieses Tutorials erforderlich sind. Aus diesem Grund empfehlen wir Ihnen eine lokale Installation der Azure CLI und der Docker-Entwicklungsumgebung.

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

Für dieses Tutorial benötigen Sie eine Azure-Containerregistrierung auf der Dienstebene „Premium“. Führen Sie die Schritte in diesem Abschnitt aus, um eine neue Azure-Containerregistrierung zu erstellen.

> [!TIP]
> Falls Sie bereits eine Registrierung erstellt haben und ein Upgrade durchführen müssen, helfen Ihnen die Informationen unter [Wechseln von Tarifen](container-registry-skus.md#changing-tiers) weiter. 

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

Klicken Sie auf **Ressource erstellen** > **Container** > **Azure Container Registry**.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-01.png" alt-text="Erstellen einer Containerregistrierung über das Azure-Portal":::

Konfigurieren Sie Ihre neue Registrierung mit den folgenden Einstellungen. Auf der Registerkarte **Grundlagen**:

* **Registrierungsname:** Erstellen Sie einen Registrierungsnamen, der in Azure global eindeutig ist und 5 bis 50 alphanumerische Zeichen enthält.
* **Ressourcengruppe**: **Neu erstellen** > `myResourceGroup`
* **Standort**: `West US`
* **SKU**: `Premium` (für die Georeplikation erforderlich)

Wählen Sie **Bewerten + erstellen** und dann **Erstellen** aus, um die Registrierungsinstanz zu erstellen.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-02.png" alt-text="Konfigurieren einer Containerregistrierung über das Azure-Portal":::

Im restlichen Teil dieses Tutorials verwenden wir `<acrName>` als Platzhalter für den von Ihnen gewählten **Registrierungsnamen** des Containers.

> [!TIP]
> Da es sich bei Azure-Containerregistrierungen normalerweise um Ressourcen mit langer Lebensdauer handelt, die übergreifend für mehrere Containerhosts verwendet werden, empfehlen wir Ihnen, Ihre Registrierung in einer eigenen Ressourcengruppe zu erstellen. Wenn Sie georeplizierte Registrierungen und Webhooks konfigurieren, werden diese zusätzlichen Ressourcen in derselben Ressourcengruppe angeordnet.

## <a name="configure-geo-replication"></a>Konfigurieren der Georeplikation

Nachdem Sie nun über eine Premium-Registrierung verfügen, können Sie die Georeplikation konfigurieren. Ihre Web-App, die Sie im nächsten Tutorial für die Ausführung in zwei Regionen konfigurieren, kann ihre Containerimages dann per Pullvorgang aus der nächstgelegenen Registrierung beziehen.

Navigieren Sie im Azure-Portal zu Ihrer neuen Containerregistrierung, und wählen Sie unter **Dienste** die Option **Replikationen** aus:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-03.png" alt-text="Replikationen auf der Benutzeroberfläche für Containerregistrierungen im Azure-Portal":::

Es wird eine Karte mit grünen Sechsecken angezeigt, die für die Azure-Regionen stehen, für die eine Georeplikation möglich ist:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-map-01.png" alt-text="Karte mit Regionen im Azure-Portal":::

Replizieren Sie Ihre Registrierung in der Region „USA, Osten“, indem Sie das grüne Sechseck auswählen, und wählen Sie dann unter **Replikation erstellen** die Option **Erstellen**:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-04.png" alt-text="Benutzeroberfläche zum Erstellen von Replikaten im Azure-Portal":::

Wenn die Replikation abgeschlossen ist, wird im Portal für beide Regionen *Bereit* angezeigt. Verwenden Sie die Schaltfläche **Aktualisieren**, um den Status der Replikation zu aktualisieren. Es kann ca. eine Minute dauern, bis die Replikate erstellt und synchronisiert wurden.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-05.png" alt-text="Benutzeroberfläche für den Replikatstatus im Azure-Portal":::


## <a name="enable-admin-account"></a>Aktivieren des Administratorkontos

In den nachfolgenden Tutorials stellen Sie ein Containerimage aus der Registrierung direkt für Web-App für Container bereit. Zum Aktivieren dieser Funktion müssen Sie auch das [Administratorkonto](container-registry-authentication.md#admin-account) der Registrierung aktivieren.

Navigieren Sie im Azure-Portal zu Ihrer neuen Containerregistrierung, und wählen Sie unter **Einstellungen** die Option **Zugriffsschlüssel** aus. Klicken Sie unter **Administratorbenutzer** auf **Aktivieren**.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-06.png" alt-text="Aktivieren des Administratorkontos im Azure-Portal":::


## <a name="container-registry-login"></a>Anmeldung bei der Containerregistrierung

Nachdem Sie nun die Georeplikation konfiguriert haben, erstellen Sie ein Containerimage und übertragen es per Pushvorgang in Ihre Registrierung. Sie müssen sich zuerst bei Ihrer Registrierung anmelden, bevor Sie dafür Images per Pushvorgang übertragen können.

Verwenden Sie den Befehl [az acr login](/cli/azure/acr#az-acr-login), um sich zu authentifizieren und die Anmeldeinformationen für Ihre Registrierung zwischenzuspeichern. Ersetzen Sie `<acrName>` durch den Namen der Registrierung, die Sie zuvor erstellt haben.

```azurecli
az acr login --name <acrName>
```

Der Befehl gibt `Login Succeeded` zurück, wenn der Vorgang abgeschlossen ist.

## <a name="get-application-code"></a>Abrufen von Anwendungscode

Das Beispiel in diesem Tutorial enthält eine kleine Webanwendung, die mit [ASP.NET Core][aspnet-core] erstellt wurde. Die App stellt eine HTML-Seite bereit, auf der die Region angezeigt wird, aus der das Image von Azure Container Registry bereitgestellt wurde.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-app-01.png" alt-text="Tutorial-App im Browser":::

Verwenden Sie Git, um das Beispiel in ein lokales Verzeichnis herunterzuladen, und wechseln Sie mit `cd` in das Verzeichnis:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Sollte `git` nicht installiert sein, können Sie das [ZIP-Archiv direkt von GitHub herunterladen][acr-helloworld-zip].

## <a name="update-dockerfile"></a>Aktualisieren der Dockerfile

Mit der im Beispiel enthaltenen Dockerfile wird veranschaulicht, wie der Container erstellt wird. Den Ausgangspunkt bildet ein offizielles ASP.NET Core-Runtimeimage. Anschließend werden die Anwendungsdateien in den Container kopiert und die Abhängigkeiten installiert, die Ausgabe wird mit dem offiziellen .NET Core SDK-Image kompiliert, und zuletzt wird ein optimiertes aspnetcore-Image erstellt.

Die [Dockerfile][dockerfile] befindet sich in der geklonten Quelle unter `./AcrHelloworld/Dockerfile`.

```Dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

Die Anwendung im Image *acr-helloworld* versucht, die Region zu ermitteln, aus der der Container bereitgestellt wurde. Hierfür werden Informationen zum Anmeldeserver der Registrierung vom DNS abgefragt. Geben Sie in der Dockerfile in der Umgebungsvariablen `DOCKER_REGISTRY` den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) des Anmeldeservers Ihrer Registrierung an.

Rufen Sie zunächst mithilfe des Befehls `az acr show` den Anmeldeserver Ihrer Registrierung ab. Ersetzen Sie `<acrName>` durch den Namen der Registrierung, die Sie in den vorherigen Schritten erstellt haben.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Ausgabe:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Aktualisieren Sie als Nächstes die Zeile `ENV DOCKER_REGISTRY` mit dem FQDN des Anmeldeservers Ihrer Registrierung. In diesem Beispiel wird der Beispielregistrierungsname *uniqueregistryname* verwendet:

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Erstellen des Containerimage

Nachdem Sie die Dockerfile mit dem FQDN des Anmeldeservers Ihrer Registrierung aktualisiert haben, können Sie `docker build` verwenden, um das Containerimage zu erstellen. Führen Sie den folgenden Befehl aus, um das Image zu erstellen und mit der URL Ihrer privaten Registrierung zu kennzeichnen. Ersetzen Sie `<acrName>` wieder durch den Namen Ihrer Registrierung:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Beim Erstellen des Docker-Image werden mehrere Ausgabezeilen angezeigt (hier gekürzt angegeben):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS base
2.2: Pulling from mcr.microsoft.com/dotnet/core/aspnet
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Verwenden Sie `docker images`, um das erstellte und markierte Image anzuzeigen:

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Übertragen des Images zu Azure Container Registry mithilfe von Push

Verwenden Sie als Nächstes den Befehl `docker push`, um das Image *acr-helloworld* an Ihre Registrierung zu pushen. Ersetzen Sie `<acrName>` durch den Namen Ihrer Registrierung.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Da Sie Ihre Registrierung für die Georeplikation konfiguriert haben, wird Ihr Image mit diesem einzelnen `docker push`-Befehl automatisch sowohl in der Region *USA, Westen* als auch in *USA, Osten* repliziert.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine private, georeplizierte Containerregistrierung und ein Containerimage erstellt und dieses Image anschließend per Pushvorgang in Ihre Registrierung übertragen.

Im nächsten Tutorial erfahren Sie, wie Sie Ihren Container in mehreren Instanzen vom Typ „Web-App für Container“ bereitstellen, indem Sie die Georeplikation für die lokale Bereitstellung der Images verwenden.

> [!div class="nextstepaction"]
> [Bereitstellen einer Web-App über Azure Container Registry](container-registry-tutorial-deploy-app.md)

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile
