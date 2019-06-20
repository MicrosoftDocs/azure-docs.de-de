---
title: Funktionsweise und Konfiguration von Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 03/04/2019
ms.topic: conceptual
description: Beschreibt die Prozesse, die zur Funktionsweise von Azure Dev Spaces beitragen, und beschreibt, wie diese in der Konfigurationsdatei „azds.yaml“ konfiguriert werden.
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container
ms.openlocfilehash: 083ed45a8fa95ac5fc26af6547b5765acf80e43b
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154556"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Funktionsweise und Konfiguration von Azure Dev Spaces

Das Entwickeln einer Kubernetes-Anwendung kann eine Herausforderung sein. Sie benötigen Docker und Kubernetes-Konfigurationsdateien. Sie müssen feststellen, wie Sie Ihre Anwendung lokal testen können und wie sie mit anderen abhängigen Diensten zusammenarbeitet. Möglicherweise müssen Sie das Entwickeln und Testen für mehrere Dienste gleichzeitig und mit einem Team von Entwicklern durchführen.

Azure Dev Spaces unterstützt Sie dabei, Kubernetes-Anwendungen direkt in Azure Kubernetes Service (AKS) zu entwickeln, bereitzustellen und zu debuggen. Azure Dev Spaces ermöglicht es einem Team außerdem, einen Entwicklungsbereich (Dev Space) gemeinsam zu nutzen. Gemeinsame Nutzung eines Entwicklungsbereichs in einem Team versetzt einzelne Teammitglieder in die Lage, isoliert von den anderen zu entwickeln, ohne Abhängigkeiten oder andere Anwendungen im Cluster replizieren oder modellieren zu müssen.

Für Azure Dev Spaces wird eine Konfigurationsdatei erstellt und verwendet, um Kubernetes-Anwendungen in AKS bereitzustellen, auszuführen und zu debuggen. Diese Konfigurationsdatei befindet sich bei dem Code Ihrer Anwendung und kann Ihrem Versionskontrollsystem hinzugefügt werden.

In diesem Artikel sind die Prozesse beschrieben, die zur Funktionsweise von Azure Dev Spaces beitragen, und ist beschrieben, wie diese Prozesse in der Azure Dev Spaces-Konfigurationsdatei konfiguriert werden. Um Azure Dev Spaces schnell zur Ausführung zu bringen und in der Praxis zu erleben, führen Sie einen der Schnellstarts aus:

* [Java mit CLI and Visual Studio Code](quickstart-java.md)
* [.NET Core mit CLI and Visual Studio Code](quickstart-netcore.md)
* [.NET Core mit Visual Studio](quickstart-netcore-visualstudio.md)
* [Node.js mit CLI and Visual Studio Code](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Funktionsweise von Azure Dev Spaces

Azure Dev Spaces hat zwei verschiedene Komponenten, mit denen Sie interagieren: der Controller und die clientseitigen Tools.

![Azure Dev Spaces-Komponenten](media/how-dev-spaces-works/components.svg)

Der Controller führt folgende Aktionen aus:

* Er verwaltet das Erstellen und Auswählen des Entwicklungsbereichs (Dev Space).
* Er installiert das Helm-Chart Ihrer Anwendung und erstellt Kubernetes-Objekte.
* Er erstellt das Containerimage Ihrer Anwendung.
* Er stellt Ihre Anwendung in AKS bereit.
* Er erstellt inkrementelle Builds und führt einen Neustart aus, wenn sich Ihr Quellcode geändert hat.
* Er verwaltet Protokolle und HTTP-Überwachungen.
* Er sendet Standardausgabe (stdout) und Standardfehlerausgabe (stderr) an die clientseitigen Tools.
* Ermöglichen, dass Teammitglieder untergeordnete Entwicklungsbereiche erstellen können, die aus einem übergeordneten Entwicklungsbereich abgeleitet wurden
* Konfigurieren des Routings für Anwendungen in einem Entwicklungsbereich als auch zwischen übergeordneten und untergeordneten Bereichen

Der Controller befindet sich außerhalb von AKS. Er steuert das Verhalten und die Kommunikation zwischen den clientseitigen Tools und dem AKS-Cluster. Der Controller wird über die Azure CLI aktiviert, wenn Sie Ihren Cluster für die Verwendung von Azure Dev Spaces vorbereiten. Sobald der Controller aktiviert ist, können Sie über die clientseitigen Tools mit ihm interagieren.

Die clientseitigen Tools eröffnen dem Benutzer folgende Möglichkeiten:
* Generieren einer Dockerfile-Datei, eines Helm-Charts und einer Azure Dev Spaces-Konfigurationsdatei für die Anwendung
* Erstellen von übergeordneten und untergeordneten Entwicklungsbereichen
* Anweisen des Controllers, dass er Ihre Anwendung erstellen zu starten soll

Während Ihre Anwendung ausgeführt wird, führen die clientseitigen Tools außerdem folgende Aktionen aus:
* Empfangen und Anzeigen von Standardausgabe (stdout) und Standardfehlerausgabe (stderr) aus Ihrer Anwendung, die in AKS ausgeführt wird
* Verwenden von [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/), um Webzugriff auf Ihre Anwendung über „http:\//localhost“ zu ermöglichen
* Einbinden eines Debuggers in Ihre aktive Anwendung in AKS
* Synchronisieren des Quellcodes mit Ihrem Entwicklungsbereich, wenn eine Änderung für inkrementelle Builds erkannt wurde, wodurch eine schnelle Iteration unterstützt wird

Sie können die clientseitigen Tools über die Befehlszeile als Bestandteil des `azds`-Befehls verwenden. Sie können die clientseitigen Tools auch verwenden mit:

* Visual Studio Code über die [Azure Dev Spaces-Erweiterung](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)
* Visual Studio mit [Visual Studio-Tools für Kubernetes](https://aka.ms/get-vsk8stools)

Die grundlegende Vorgehensweise zum Einrichten und Verwenden von Azure Dev Spaces sieht wie folgt aus:
1. Vorbereiten Ihres AKS-Clusters für Azure Dev Spaces
1. Vorbereiten Ihres Codes für das Ausführen in Azure Dev Spaces
1. Ausführen Ihres Codes in einem Entwicklungsbereich (Dev Space)
1. Debuggen Ihres Codes in einem Entwicklungsbereich
1. Freigeben eines Entwicklungsbereichs

Ausführlichere Informationen zur Funktionsweise von Azure Dev Spaces sind in den folgenden Abschnitten zu finden.

## <a name="prepare-your-aks-cluster"></a>Vorbereiten Ihres AKS-Clusters

Ein Vorbereiten Ihres AKS-Clusters umfasst Folgendes:
* Überprüfen, ob sich Ihr AKS-Cluster in einer Region befindet, die von [Azure Dev Spaces unterstützt wird][supported-regions].
* Überprüfen, ob Sie Kubernetes 1.10.3 oder höher ausführen
* Aktivieren von Azure Dev Spaces in Ihrem Cluster über `az aks use-dev-spaces`

Weitere Informationen darüber, wie ein AKS-Cluster für Azure Dev Spaces erstellt und konfiguriert wird, finden Sie in den Erste-Schritte-Anleitungen:
* [Erste Schritte in Azure Dev Spaces mit Java](get-started-java.md)
* [Erste Schritte in Azure Dev Spaces mit .NET Core und Visual Studio](get-started-netcore-visualstudio.md)
* [Erste Schritte in Azure Dev Spaces mit .NET Core](get-started-netcore.md)
* [Erste Schritte in Azure Dev Spaces mit Node.js](get-started-nodejs.md)

Wenn Sie Azure Dev Spaces in Ihrem AKS-Cluster aktivieren, wird der Controller für den Cluster installiert. Der Controller ist eine separate Azure-Ressource außerhalb Ihres Clusters und führt für Ressourcen in Ihrem Cluster die folgenden Aktionen aus:

* Erstellen oder Bestimmen eines Kubernetes-Namespace, der als Entwicklungsbereich verwendet werden soll
* Entfernen aller Kubernetes-Namespaces namens *azds* (sofern vorhanden) und Erstellen eines neuen Namespace
* Stellt eine Kubernetes-Webhook-Konfiguration bereit.
* Stellt einen Webhook-Zulassungsserver bereit.
    

Außerdem wird derselbe Dienstprinzipal wie in Ihrem AKS-Cluster verwendet, um Dienstaufrufe an andere Azure Dev Spaces-Komponenten durchzuführen.

![Clustervorbereitung mit Azure Dev Spaces](media/how-dev-spaces-works/prepare-cluster.svg)

Damit Azure Dev Spaces verwendet werden kann, muss mindestens ein Entwicklungsbereich (Dev Space) vorhanden sein. Azure Dev Spaces verwendet Kubernetes-Namespaces in Ihrem AKS-Cluster für Entwicklungsbereiche. Wenn ein Controller installiert wird, werden Sie aufgefordert, einen neuen Kubernetes-Namespace zu erstellen oder einen vorhandenen Namespace auszuwählen, der als erster Entwicklungsbereich verwendet werden soll. Wird ein Namespace als Entwicklungsbereich bestimmt, fügt der Controller die Bezeichnung *azds.io/space=true* zu diesem Namespace hinzu, um ihn als Entwicklungsbereich zu kennzeichnen. Der Erstentwicklungsbereich, den Sie erstellen oder bestimmen, wird standardmäßig ausgewählt, nachdem Sie Ihren Cluster vorbereitet haben. Wenn ein Bereich ausgewählt ist, wird er von Azure Dev Spaces für das Erstellen neuer Workloads verwendet.

Standardmäßig erstellt der Controller einen Entwicklungsbereich namens *default*, indem er den vorhandenen *default*-Kubernetes-Namespace aktualisiert. Sie können die clientseitigen Tools verwenden, um neue Entwicklungsbereiche zu erstellen und vorhandene Entwicklungsbereiche zu entfernen. Aufgrund einer Einschränkung in Kubernetes kann der *default*-Entwicklungsbereich nicht entfernt werden. Der Controller entfernt auch alle vorhandenen Kubernetes-Namespaces namens *azds*, um Konflikte mit dem `azds`-Befehl zu vermeiden, der von den clientseitigen Tools verwendet wird.

Das Kubernetes-Webhook-Zulassungsserver wird verwendet, um Pods mit drei Containern während der Bereitstellung für die Instrumentierung einzufügen: ein devspaces-proxy-init-Container, ein devspaces-proxy-init-Container und ein devspaces-build-Container. **Diese drei Container werden alle mit Root-Zugriff in Ihrem AKS-Cluster ausgeführt.** Sie verwenden außerdem derselben Dienstprinzipal wie in Ihrem AKS-Cluster, um Dienstaufrufe an andere Azure Dev Spaces-Komponenten durchzuführen.

![Azure Dev Spaces Kubernetes-Webhook-Zulassungsserver](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Der devspaces-proxy-Container ist ein unterstützender Container, in dem der gesamte ein- und ausgehende TCP-Datenverkehr des Anwendungscontainers verarbeitet und das Routing unterstützt wird. Der devspaces-proxy-Container leitet HTTP-Nachrichten um, wenn bestimmte Bereiche verwendet werden. Beispielsweise kann er dabei unterstützen, HTTP-Nachrichten zwischen Anwendungen in über- und untergeordneten Bereichen weiterzuleiten. Jeglicher Nicht-HTTP-Datenverkehr durchläuft den devspaces-proxy-Container ungeändert. Im devspaces-proxy-Container werden auch alle eingehenden und ausgehenden HTTP-Nachrichten protokolliert und als Überwachungen an die clientseitigen Tools gesendet. Der Entwickler kann diese Überwachungen dann anzeigen, um das Verhalten der Anwendung zu untersuchen.

Der devspaces-proxy-init-Container ist ein [Init-Container](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/), für den zusätzliche Routingregeln entsprechend der Bereichshierarchie zum Container Ihrer Anwendung hinzugefügt werden. Die Routingregeln werden vor dem Start des Containers hinzugefügt, indem die */etc/resolv.conf*-Datei des Anwendungcontainers und die iptables-Konfiguration aktualisiert werden. Die Aktualisierungen an */etc/resolv.conf* ermöglichen die DNS-Auflösung von Diensten in übergeordneten Bereichen. Die Aktualisierungen an der iptables-Konfiguration stellen sicher, dass der gesamte ein- und ausgehende TCP-Datenverkehr des Containers der Anwendung über den devspaces-proxy-Container weitergeleitet wird. Alle Aktualisierungen aus dem devspaces-proxy-init-Container erfolgen zusätzlich zu den Regeln, die von Kubernetes hinzugefügt werden.

Der devspaces-build-Container ist ein Init-Container, und in ihm sind der Projektquellcode und der Docker-Socket eingebunden. Der Projektquellcode und der Zugriff auf Docker ermöglichen, dass der Anwendungscontainer direkt durch den Pod erstellt werden kann.

> [!NOTE]
> Azure Dev Spaces verwendet denselben Knoten, um den Container Ihrer Anwendung zu erstellen und auszuführen. Daher ist für Azure Dev Spaces keine externe Containerregistrierung erforderlich, um Ihre Anwendung zu erstellen und auszuführen.

Der Kubernetes-Webhook-Zulassungsserver lauscht auf jeden neuen Pod, der im AKS-Cluster erstellt wird. Wird dieser Pod in irgendeinem Namespace mit der Bezeichnung *azds.io/space=true* bereitgestellt, fügt das Objekt diesen Pod mit den zusätzlichen Containern ein. Der devspaces-build-Container wird nur eingefügt, wenn der Container der Anwendung über die clientseitigen Tools ausgeführt wird.

Nachdem Sie Ihren AKS-Cluster vorbereitet haben, können Sie die clientseitigen Tools verwenden, um Ihren Code in Ihrem Entwicklungsbereich (Dev Space) vorzubereiten und auszuführen.

## <a name="prepare-your-code"></a>Vorbereiten Ihres Codes

Um Ihre Anwendung in einem Entwicklungsbereich ausführen zu können, muss sie containerisiert sein, und Sie müssen definieren, wie sie in Kubernetes bereitgestellt werden soll. Um Ihre Anwendung zu containerisieren, benötigen Sie eine Dockerfile-Datei. Um zu definieren, wie Ihre Anwendung in Kubernetes bereitgestellt wird, benötigen Sie ein [Helm-Chart](https://docs.helm.sh/). Als Unterstützung für ein Erstellen der Dockerfile-Datei und des Helm-Charts für Ihre Anwendung stellen die clientseitigen Tools den `prep`-Befehl bereit:

```cmd
azds prep --public
```

Der `prep`-Befehl analysiert die Dateien in Ihrem Projekt und versucht, die Dockerfile-Datei und das Helm-Chart zu erstellen, um Ihre Anwendung in Kubernetes auszuführen. Derzeit generiert der `prep`-Befehl eine Dockerfile-Datei und ein Helm-Chart mit den folgenden Sprachen:

* Java
* Node.js
* .NET Core

Sie *müssen* den `prep`-Befehl aus einem Verzeichnis ausführen, das Quellcode enthält. Wird der `prep`-Befehl aus dem richtigen Verzeichnis ausgeführt, können die clientseitigen Tools die Sprache erkennen und eine entsprechende Dockerfile-Datei erstellen, um Ihre Anwendung zu containerisieren. Sie können den `prep`-Befehl auch aus einem Verzeichnis ausführen, das eine *pom.xml*-Datei für Java-Projekte enthält.

Wenn Sie den `prep`-Befehl aus einem Verzeichnis ausführen, das keinen Quellcode enthält, erstellen die clientseitigen Tools keine Dockerfile-Datei. Außerdem wird die folgende Fehlermeldung angezeigt: *Aufgrund einer nicht unterstützten Sprache konnte keine Dockerfile generiert werden*. Dieser Fehler tritt auch auf, wenn die clientseitigen Tools den Projekttyp nicht erkennen können.

Wenn Sie den `prep`-Befehl ausführen, können Sie das Flag `--public` angeben. Dieses Flag weist den Controller an, einen über das Internet zugänglichen Endpunkt für diesen Dienst zu erstellen. Wenn Sie dieses Flag nicht angeben, kann nur aus dem Cluster oder über den localhost-Tunnel, der von den clientseitigen Tools erstellt wurde, auf den Dienst zugegriffen werden. Sie können dieses Verhalten nach dem Ausführen des `prep`-Befehls aktivieren oder deaktivieren, indem Sie das generierte Helm-Chart aktualisieren.

Der `prep`-Befehl ersetzt keine vorhandenen Dockerfile-Dateien oder Helm-Charts, die es in Ihrem Projekt gibt. Wurde für eine vorhandene Dockerfile-Datei oder ein vorhandenes Helm-Chart dieselbe Namenskonvention verwendet wie für die Dateien, die vom `prep`-Befehl generiert werden, überspringt der `prep`-Befehl diese Dateien. Andernfalls generiert der `prep`-Befehl seine eigene Dockerfile-Datei oder sein eigenes Helm-Chart neben den vorhandenen Dateien.

Außerdem generiert der `prep`-Befehl eine `azds.yaml`-Datei im Stammverzeichnis Ihres Projekts. In Azure Dev Spaces wird diese Datei verwendet, um Ihre Anwendung zu erstellen, zu konfigurieren und auszuführen. In dieser Konfigurationsdatei werden der Speicherort Ihrer Dockerfile-Datei und Ihres Helm-Charts aufgelistet sowie aufsetzend auf diesen Artefakten weitere Konfigurationsoptionen bereitgestellt.

Es folgt eine „azds.yaml“-Beispieldatei, die mit einer [.NET Core-Beispielanwendung](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend) erstellt wurde:

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

Die `azds.yaml`-Datei, die der `prep`-Befehl generiert hat, sollte für ein einfaches Einzelprojekt-Entwicklungsszenario problemlos funktionieren. Ist Ihr spezielles Projekt deutlich komplexer, müssen Sie diese Datei möglicherweise aktualisieren, nachdem Sie den `prep`-Befehl ausgeführt haben. Beispielsweise kann es sein, dass für Ihr Projekt entsprechend Ihren Entwicklungs- oder Debugging-Anforderungen eine Anpassung Ihres Build- oder Startprozesses erforderlich ist. Außerdem haben Sie möglicherweise mehrere Anwendungen in Ihrem Projekt, die mehrere Buildprozesse oder unterschiedliche Buildinhalte erfordern.

## <a name="run-your-code"></a>Ausführen Ihres Codes

Um Ihren Code in einem Entwicklungsbereich (Dev Space) auszuführen, geben Sie den `up`-Befehl für das Verzeichnis aus, in dem sich Ihre `azds.yaml`-Datei befindet:

```cmd
azds up
```

Der `up`-Befehl lädt die Quelldateien Ihrer Anwendung und andere Artefakte, die zum Erstellen und Ausführen Ihres Projekts erforderlich sind, in den Entwicklungsbereich hoch. Der Controller führt dann im Entwicklungsbereich folgende Aktionen aus:

1. Er erstellt die Kubernetes-Objekte, um Ihre Anwendung bereitzustellen.
1. Er erstellt den Container für Ihre Anwendung.
1. Er stellt Ihre Anwendung im Entwicklungsbereich bereit.
1. Er erstellt einen öffentlich zugänglichen DNS-Namen für den Anwendungsendpunkt, sofern ein solcher konfiguriert ist.
1. Er verwendet *port-forward*, um Zugriff auf Ihren Anwendungsendpunkt über http://localhost zu bieten.
1. Er sendet Standardausgabe (stdout) und Standardfehlerausgabe (stderr) an die clientseitigen Tools.


### <a name="starting-a-service"></a>Starten eines Diensts

Wenn Sie einen Dienst in einem Entwicklungsbereich starten, arbeiten die clientseitigen Tools und der Controller zusammen, um Ihre Quelldateien zu synchronisieren, Ihren Container und Ihre Kubernetes-Objekte zu erstellen und Ihre Anwendung auszuführen.

In ausführlicherer Beschreibung passiert Folgendes, wenn Sie `azds up` ausführen:

1. Dateien werden vom Computer des Benutzers mit einem Azure-Dateispeicher synchronisiert, der nur für den AKS-Cluster des Benutzers verfügbar ist. Der Quellcode, das Helm-Chart und die Konfigurationsdateien werden hochgeladen. Weitere Informationen zu dem Synchronisierungsprozess enthält der nächste Abschnitt.
1. Der Controller erstellt eine Anforderung, um eine neue Sitzung zu starten. Diese Anforderung enthält mehrere Eigenschaften, wozu eine eindeutige ID, ein Bereichsname, ein Pfad zum Quellcode und ein Debuggen-Flag gehören.
1. Der Controller ersetzt den *$(tag)* -Platzhalter im Helm-Chart durch die eindeutige Sitzungs-ID und installiert das Helm-Chart für Ihren Dienst. Dadurch, dass ein Verweis auf die eindeutige Sitzungs-ID zum Helm-Chart hinzugefügt wird, wird es ermöglicht, den Container, der im AKS-Cluster bereitgestellt wird, für diese spezielle Sitzung wieder an die Sitzungsanforderung und die zugehörigen Informationen zu binden.
1. Während der Installation des Helm-Charts fügt der Kubernetes-Webhook-Zulassungsserver dem Pod Ihrer Anwendung weitere Container für die Instrumentierung und für Zugriff auf den Quellcode Ihres Projekts hinzu. Der devspaces-proxy- und der devspaces-proxy-init-Container werden hinzugefügt, um HTTP-Ablaufverfolgung und -Bereichsrouting bereitzustellen. Der devspaces-build-Container wird hinzugefügt, damit der Pod auf die Docker-Instanz und den Projektquellcode zugreifen kann, um den Container Ihrer Anwendung zu erstellen.
1. Wenn der Pod der Anwendung gestartet wird, werden der devspaces-build-Container und der devspaces-proxy-init-Container verwendet, um den Anwendungscontainer zu erstellen. Anschließend werden der Anwendungscontainer und die devspaces-proxy-Container gestartet.
1. Nachdem der Anwendungscontainer gestartet wurde, wird auf der Clientseite die *port-forward*-Funktionalität von Kubernetes verwendet, um HTTP-Zugriff auf Ihre Anwendung über http://localhost bereitzustellen. Über diese Portweiterleitung wird Ihr Entwicklungscomputer mit dem Dienst in Ihrem Entwicklungsbereich verbunden.
1. Wenn alle Container im Pod gestartet sind, wird der Dienst ausgeführt. An diesem Punkt beginnt die clientseitige Funktionalität damit, die HTTP-Ablaufverfolgungen sowie „stdout“ und „stderr“ zu streamen. Diese Informationen werden für den Entwickler durch die clientseitige Funktionalität angezeigt.

### <a name="updating-a-running-service"></a>Aktualisieren eines aktiven Diensts

Während ein Diensts ausgeführt wird, kann Azure Dev Spaces diesen Dienst aktualisieren, wenn irgendeine der Quelldateien des Projekts geändert wurde. Dev Spaces nimmt die Aktualisierung des Diensts je nach dem Typ der Datei, die geändert wurde, unterschiedlich vor. Es gibt drei Möglichkeiten, wie Dev Spaces einen aktiven Dienst aktualisieren kann:

* Direktes Aktualisieren einer Datei
* Neuerstellen und Neustarten des Prozesses der Anwendung im Container der aktiven Anwendung
* Neuerstellen und Neubereitstellen des Containers der Anwendung

![Azure Dev Spaces-Dateisynchronisierung](media/how-dev-spaces-works/file-sync.svg)

Bestimmte Projektdateien, die statische Objekte sind, etwa HTML-, CSS- und CSHTML-Dateien, können direkt im Container der Anwendung aktualisiert werden, ohne dass irgendetwas neu gestartet wird. Wurde ein statisches Objekt geändert, wird die neue Datei mit dem Entwicklungsbereich synchronisiert und dann für den aktiven Container verwendet.

Änderungen an Dateien, z. B. Quellcode- oder Anwendungskonfigurationsdateien, können angewendet werden, indem der Prozess der Anwendung im aktiven Container neu gestartet wird. Sobald diese Dateien synchronisiert sind, wird der Prozess der Anwendung über den *devhostagent*-Prozess im aktiven Container neu gestartet. Beim ersten Erstellen des Containers der Anwendung ersetzt der Controller den Startbefehl für die Anwendung durch einen anderen Prozess namens *devhostagent*. Der tatsächliche Prozess der Anwendung wird dann als untergeordneter Prozess unter *devhostagent* ausgeführt, und seine Ausgabe wird über Verkettung (Pipe) mit der *devhostagent*-Ausgabe ausgegeben. Der *devhostagent*-Prozess gehört ebenfalls zu Dev Spaces und kann Befehle im Auftrag von Dev Spaces im aktiven Container ausführen. Beim Ausführen eines Neustarts geht der *devhostagent*-Prozess wie folgt vor:

* Er beendet die aktuellen Prozesse, die der Anwendung zugeordnet sind.
* Er erstellt die Anwendung neu.
* Er startet die Prozesse, die der Anwendung zugeordnet sind.

Die Art und Weise, wie *devhostagent* die vorherigen Schritte ausführt, ist in der Konfigurationsdatei `azds.yaml` konfiguriert. Diese Konfiguration ist in einem späteren Abschnitt beschrieben.

Aktualisierungen an Projektdateien, etwa Dockerfile-Dateien, CSPROJ-Dateien oder irgendein Teil des Helm-Charts, bedingen, dass der Container der Anwendung neu erstellt und neu bereitgestellt werden muss. Wird eine dieser Dateien mit dem Entwicklungsbereich synchronisiert, führt der Controller den [helm upgrade](https://helm.sh/docs/helm/#helm-upgrade)-Befehl aus, und der Container der Anwendung wird neu erstellt und neu bereitgestellt.

### <a name="file-synchronization"></a>Dateisynchronisierung

Wird eine Anwendung erstmals in einem Entwicklungsbereich gestartet, werden alle Quelldateien der Anwendung hochgeladen. Während die Anwendung ausgeführt wird, und bei späteren Neustarts werden nur die geänderten Dateien hochgeladen. Ein solcher Vorgang wird über zwei Dateien koordiniert: eine Datei auf Clientseite eine Datei auf Controllerseite.

Die Datei auf Clientseite wird in einem temporären Verzeichnis gespeichert und wird ausgehend von einem Hash des Projektverzeichnisses benannt, das Sie in Dev Spaces ausführen. Unter Windows haben Sie beispielsweise eine Datei wie *Benutzer\BENUTZERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* für Ihr Projekt. Unter Linux wird die Datei auf Clientseite im Verzeichnis */tmp* gespeichert. Unter macOS finden Sie das Verzeichnis, indem Sie den `echo $TMPDIR`-Befehl ausführen.

Diese Datei liegt im JSON-Format vor und enthält Folgendes:

* Einen Eintrag für jede Projektdatei, die mit dem Entwicklungsbereich synchronisiert wird
* Eine Synchronisierungs-ID
* Den Zeitstempel des letzten Synchronisierungsvorgangs

Jeder Projektdateieintrag enthält einen Pfad zur Datei und deren Zeitstempel.

Die Datei auf Controllerseite wird im AKS-Cluster gespeichert. Sie enthält die Synchronisierungs-ID und den Zeitstempel der letzten Synchronisierung.

Eine Synchronisierung erfolgt, wenn die Synchronisierungszeitstempel der Datei auf Clientseite und der Datei auf Controllerseite nicht übereinstimmen. Während einer Synchronisierung durchlaufen die clientseitigen Tools die Dateieinträge in der Datei auf Clientseite. Wurde der Zeitstempel der Datei später erstellt als der Synchronisierungszeitstempel, wird diese Datei mit dem Entwicklungsbereich synchronisiert. Sobald die Synchronisierung abgeschlossen ist, werden die Synchronisierungszeitstempel in der Datei auf Clientseite und in der Datei auf Controllerseite aktualisiert.

Alle Projektdateien werden synchronisiert, wenn die Datei auf Clientseite nicht vorhanden ist. Durch dieses Verhalten können Sie eine vollständige Synchronisierung erzwingen, indem Sie die Datei auf Clientseite löschen.

### <a name="how-routing-works"></a>Funktionsweise des Routings (Weiterleitung)

Ein Entwicklungsbereich (Dev Spac) wird auf AKS erstellt, und für ihn werden die gleichen [Netzwerkkonzepte](../aks/concepts-network.md) verwendet. Azure Dev Spaces hat außerdem einen zentralisierten *ingressmanager*-Dienst und stellt dem AKS-Cluster seinen eigenen Eingangscontroller bereit. Der *ingressmanager*-Dienst überwacht AKS-Cluster mit Entwicklungsbereichen und erweitert den Azure Dev Spaces-Eingangscontroller im Cluster durch Eingangsobjekte zum Routing an Anwendungs-Pods. Der devspaces-proxy-Container in jedem Pod fügt einem Entwicklungsbereich auf Basis der URL einen `azds-route-as`-HTTP-Header für HTTP-Datenverkehr hinzu. Zum Beispiel würde eine Anforderung an die URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* zu einem HTTP-Header mit `azds-route-as: azureuser` führen. Der devspaces-proxy-Container fügt keinen `azds-route-as`-Header hinzu, wenn bereits einer vorhanden ist.

Wird eine HTTP-Anforderung von außerhalb des Clusters an einen Dienst gesendet, gelangt die Anforderung an den Eingangscontroller. Der Eingangscontroller leitet die Anforderung anhand seiner Eingangsobjekte und -regeln direkt an den entsprechenden Pod weiter. Der devspaces-proxy-Container im Pod empfängt die Anforderung, fügt den `azds-route-as`-Header anhand der URL hinzu und leitet die Anforderung dann an den Anwendungscontainer weiter.

Wird eine HTTP-Anforderung von einem anderen Dienst im Cluster an einen Dienst gesendet, durchläuft die Anforderung zunächst den devspaces-proxy-Container des aufrufenden Diensts. Der devspaces-proxy-Container analysiert die HTTP-Anforderung und überprüft den `azds-route-as`-Header. Ausgehend vom Header sucht der devspaces-proxy-Container nach der IP-Adresse des Diensts, der dem Headerwert zugeordnet ist. Wird eine IP-Adresse gefunden, leitet der devspaces-proxy-Container die Anforderung an diese IP-Adresse um. Wird keine IP-Adresse gefunden, leitet der devspaces-proxy-Container die Anforderung an den übergeordneten Anwendungscontainer weiter.

Angenommen, die Anwendungen *serviceA* und *serviceB* werden in einem übergeordneten Entwicklungsbereich namens *default* bereitgestellt. *serviceA* setzt auf *serviceB* auf und sendet HTTP-Aufrufe an ihn. Ein Azure-Benutzer erstellt auf Basis des *default*-Bereichs einen untergeordneten Entwicklungsbereich namens *azureuser*. Außerdem stellt der Azure-Benutzer seine eigene Version von *serviceA* in seinem untergeordneten Bereich bereit. Wenn eine Anforderung an *http://azureuser.s.default.serviceA.fedcba09...azds.io* gesendet wird, passiert Folgendes:

![Azure Dev Spaces-Routing](media/how-dev-spaces-works/routing.svg)

1. Der Eingangscontroller (Ingress-Controller) sucht nach der IP-Adresse für den Pod, der der URL, die *serviceA.azureuser* lautet, zugeordnet ist.
1. Der Eingangscontroller findet die IP-Adresse für den Pod im Entwicklungsbereich des Azure-Benutzers und leitet die Anforderung an den *serviceA.azureuser*-Pod weiter.
1. Der devspaces-proxy-Container im *serviceA.azureuser*-Pod empfängt die Anforderung und fügt `azds-route-as: azureuser` als HTTP-Header hinzu.
1. Der devspaces-proxy-Container im *serviceA.azureuser*-Pod leitet die Anforderung an den *serviceA*-Anwendungscontainer im *serviceA.azureuser*-Pod weiter.
1. Die *serviceA*-Anwendung im *serviceA.azureuser*-Pod sendet einen Aufruf an *serviceB*. Die *serviceA*-Anwendung enthält auch Code zum Beibehalten des vorhandenen `azds-route-as`-Headers, der in diesem Fall `azds-route-as: azureuser` lautet.
1. Der devspaces-proxy-Container im *serviceA.azureuser*-Pod empfängt die Anforderung und sucht nach der IP-Adresse von *serviceB* anhand des Werts des `azds-route-as`-Headers.
1. Der devspaces-proxy-Container im *serviceA.azureuser*-Pod findet keine IP-Adresse für *serviceB.azureuser*.
1. Der devspaces-proxy-Container im *serviceA.azureuser*-Pod sucht nach der IP-Adresse für *serviceB* im übergeordneten Bereich, der *serviceB.default* entspricht.
1. Der devspaces-proxy-Container im *serviceA.azureuser*-Pod findet die IP-Adresse für *serviceB.default* und leitet die Anforderung an den *serviceB.default*-Pod weiter.
1. Der devspaces-proxy-Container im *serviceB.default*-Pod empfängt die Anforderung und leitet diese an den *serviceB*-Anwendungscontainer im *serviceB.default*-Pod weiter.
1. Die *serviceB*-Anwendung im *serviceB.default*-Pod gibt eine Antwort an den *serviceA.azureuser*-Pod zurück.
1. Der devspaces-proxy-Container im *serviceA.azureuser*-Pod empfängt die Antwort und leitet sie an den *serviceA*-Anwendungscontainer im *serviceA.azureuser*-Pod weiter.
1. Die *serviceA*-Anwendung empfängt die Antwort und gibt dann ihre eigene Antwort zurück.
1. Der devspaces-proxy-Container im *serviceA.azureuser*-Pod empfängt die Antwort vom *serviceA*-Anwendungscontainer und leitet sie an den ursprünglichen Aufrufer außerhalb des Clusters weiter.

Jeglicher sonstiger TCP-Datenverkehr, der kein HTTP-Datenverkehr ist, durchläuft den Eingangscontroller und den devspaces-proxy-Container ungeändert.

### <a name="how-running-your-code-is-configured"></a>So wird das Ausführen Ihres Codes konfiguriert

Azure Dev Spaces verwendet die `azds.yaml`-Datei, um Ihren Dienst zu installieren und zu konfigurieren. Der Controller verwendet die `install`-Eigenschaft in der `azds.yaml`-Datei, um das Helm-Chart zu installieren und die Kubernetes-Objekte zu erstellen:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Der `prep`-Befehl generiert standardmäßig das Helm-Chart. Außerdem legt er die *install.chart*-Eigenschaft auf das Verzeichnis des Helm-Charts fest. Möchten Sie ein Helm-Chart verwenden, das sich an einem anderen Speicherort befindet, können Sie diese Eigenschaft so ändern, dass der gewünschte Speicherort verwendet wird.

Beim Installieren der Helm-Charts bietet Azure Dev Spaces eine Möglichkeit, Werte im Helm-Chart zu überschreiben. Die Standardwerte für das Helm-Chart befinden sich in `charts/APP_NAME/values.yaml`.

Über die *install.values*-Eigenschaft können Sie Dateien auflisten, in denen die Werte definiert sind, Sie im Helm-Chart ersetzen möchten. Diese Überschreibungsfunktionalität können Sie z. B. verwenden, wenn Sie eine Hostname- oder Datenbankkonfiguration speziell dann benötigen, wenn Ihre Anwendung in einem Entwicklungsbereich ausgeführt wird. Sie können auch ein *?* am Ende eines Dateinamens hinzufügen, um ihn als optional festzulegen.

Über die *install.set*-Eigenschaft können Sie Werte konfigurieren, die Sie im Helm-Chart ersetzen möchten. Alle in *install.set* konfigurierten Werte werden dazu verwendet, Werte zu überschreiben, die in Dateien konfiguriert sind, die in *install.values* aufgelistet werden. Die Eigenschaften unter *install.set* hängen von den Werten im Helm-Chart ab und können je nach dem generierten Helm-Chart unterschiedlich sein.

Im obigen Beispiel teilt die *install.set.replicaCount*-Eigenschaft dem Controller mit, wie viele Instanzen Ihrer Anwendung in Ihrem Entwicklungsbereich ausgeführt werden sollen. Je nachdem, wie Ihr Szenario aussieht, können diesen Wert erhöhen. Dies wirkt sich jedoch auf das Einbinden eines Debuggers in den Pod Ihrer Anwendung aus. Weitere Informationen finden Sie im [Artikel zur Problembehandlung](troubleshooting.md).

Im generierten Helm-Chart wird das Containerimage auf *{{ .Values.image.repository }}:{{ .Values.image.tag }}* festgelegt. In der `azds.yaml`-Datei ist die *install.set.image.tag*-Eigenschaft standardmäßig als *$(tag)* definiert. Dieser Wert wird als der Wert für *{{ .Values.image.tag }}* verwendet. Durch Festlegen der *install.set.image.tag*-Eigenschaft auf diese Weise wird es ermöglicht, dass das Containerimage für Ihre Anwendung auf eindeutige Weise gekennzeichnet werden kann, wenn Azure Dev Spaces ausgeführt wird. In diesem speziellen Fall wird das Image als *\<Wert von Image.repository>:$(tag)* gekennzeichnet. Sie müssen die *$(tag)* -Variable als Wert von *install.set.image.tag* verwenden, damit Dev Spaces den Container im AKS-Cluster erkennen und finden kann.

Im obigen Beispiel ist *install.set.ingress.hosts* in `azds.yaml` definiert. Die *install.set.ingress.hosts*-Eigenschaft definiert das Hostnameformat für öffentliche Endpunkte. Auch für diese Eigenschaft werden *$(spacePrefix)* , *$(rootSpacePrefix)* und *$(hostSuffix)* verwendet, die Werte sind, die vom Controller bereitgestellt werden. 

*$(spacePrefix)* ist der Name des untergeordneten Entwicklungsbereichs, der in der Form *BEREICHSNAME.s* vorliegt. *$(rootSpacePrefix)* ist der Name des übergeordneten Bereichs. Ist *azureuser* z. B. ein untergeordneter Bereich von *default*, hat *$(rootSpacePrefix)* den Wert *default* und *$(spacePrefix)* den Wert *azureuser.s*. Ist der Bereich kein untergeordneter Bereich, ist *$(spacePrefix)* leer. Hat der *default*-Bereich z. B. keinen übergeordneten Bereich, hat *$(rootSpacePrefix)* den Wert *default*, und *$(spacePrefix)* ist leer. *$(hostSuffix)* ist ein DNS-Suffix, das auf den Azure Dev Spaces-Eingangscontroller verweist, der in Ihrem AKS-Cluster ausgeführt wird. Dieses DNS-Suffix entspricht einem Platzhalter-DNS-Eintrag, z. B. *\*.ZUFALLSWERT.eus.azds.IO*, der erstellt wurde, als der Azure Dev Spaces-Controller Ihrem AKS-Cluster hinzugefügt wurde.

In der obigen `azds.yaml`-Datei könnten Sie auch *install.set.ingress.hosts* aktualisieren, um den Hostnamen Ihrer Anwendung zu ändern. Angenommen, Sie möchten den Hostnamen Ihrer Anwendung von *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* in *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)* vereinfachen.

Um den Container für Ihre Anwendung zu erstellen, verwendet der Controller die folgenden Abschnitte der `azds.yaml`-Konfigurationsdatei:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

Der Controller verwendet eine Dockerfile-Datei, um Ihre Anwendung zu erstellen und auszuführen.

Die *build.context*-Eigenschaft listet das Verzeichnis auf, in dem sich die Dockerfile-Dateien befinden. Die *build.dockerfile*-Eigenschaft definiert den Namen der Dockerfile-Datei, mit der die Produktionsversion der Anwendung erstellt wird. Die *configurations.develop.build.dockerfile*-Eigenschaft konfiguriert den Namen der Dockerfile-Datei für die Entwicklungsversion der Anwendung.

Wenn Sie mit unterschiedlichen Dockerfile-Dateien für Entwicklung und Produktion arbeiten, können Sie bestimmte Elemente für die Entwicklung aktivieren und diese Elemente für Produktionsbereitstellungen deaktivieren. Zum Beispiel können Sie für die Entwicklung Debuggen oder eine ausführlichere Protokollierung aktivieren und dies in einer Produktionsumgebung deaktivieren. Außerdem können Sie diese Eigenschaften aktualisieren, wenn Ihre Dockerfile-Dateien unterschiedlich benannt sind oder sich in einem anderen Speicherort befinden.

Damit Sie während der Entwicklung schnell durchlaufen können, synchronisiert Azure Dev Spaces Änderungen aus Ihrem lokalen Projekt und aktualisiert inkrementell Ihre Anwendung. Der folgende Abschnitt in der `azds.yaml`-Konfigurationsdatei wird verwendet, um das Synchronisieren und Aktualisieren zu konfigurieren:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

Die Dateien und Verzeichnisse, die mit Änderungen synchronisiert werden, werden in der *configurations.develop.container.sync*-Eigenschaft aufgelistet. Diese Verzeichnisse werden zunächst synchronisiert, wenn Sie den `up`-Befehl ausführen und wenn Änderungen erkannt werden. Gibt es zusätzliche oder andere Verzeichnisse, die Sie mit Ihrem Entwicklungsbereich synchronisieren möchten, können Sie diese Eigenschaft ändern.

Die *configurations.develop.container.iterate.buildCommands*-Eigenschaft gibt an, wie die Anwendung in einem Entwicklungsszenario erstellt werden soll. Die *configurations.develop.container.command*-Eigenschaft stellt den Befehl bereit, mit dem die Anwendung in einem Entwicklungsszenario ausgeführt wird. Sie können jede dieser Eigenschaften aktualisieren, wenn es zusätzliche Build- oder Laufzeitflags oder Parameter gibt, die Sie während der Entwicklung verwenden möchten.

Die *configurations.develop.container.iterate.processesToKill*-Eigenschaft listet die Prozesse auf, die beendet werden müssen, um die Anwendung zu beenden. Sie können diese Eigenschaft aktualisieren, wenn Sie das Verhalten ändern möchten, wie Ihre Anwendung während der Entwicklung einen Neustart ausführt. Wenn Sie z. B. die *configurations.develop.container.iterate.buildCommands*- oder die *configurations.develop.container.command*-Eigenschaften aktualisiert haben, um die Art und Weise zu ändern, wie die Anwendung erstellt oder gestartet wird, müssen Sie möglicherweise ändern, welche Prozesse zu beenden sind.

Wenn Sie Ihren Code mit dem `azds prep`-Befehl vorbereiten, haben die Möglichkeit, das `--public`-Flag hinzuzufügen. Wird das `--public`-Flag hinzugefügt, wird eine öffentlich zugängliche URL für Ihre Anwendung erstellt. Wenn Sie dieses Flag nicht angeben, kann nur im Cluster oder über den localhost-Tunnel auf die Anwendung zugegriffen werden. Nachdem Sie den `azds prep`-Befehl ausgeführt haben, können Sie diese Einstellung ändern, indem Sie die *ingress.enabled*-Eigenschaft in `charts/APPNAME/values.yaml` ändern:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Debuggen Ihres Codes

Bei einer Java-, .NET- oder Node.js-Anwendung können Sie diese, wenn sie direkt in Ihrem Entwicklungsbereich ausgeführt wird, mit Visual Studio Code oder Visual Studio debuggen. Visual Studio Code und Visual Studio stellen Tools bereit, mit denen eine Verbindung mit Ihrem Entwicklungsbereich hergestellt, Ihre Anwendung gestartet und ein Debugger eingebunden wird. Nachdem Sie `azds prep` ausgeführt haben, können Sie Ihr Projekt in Visual Studio Code oder Visual Studio öffnen. Visual Studio Code oder Visual Studio erstellt eigene Konfigurationsdateien für die Verbindungsherstellung, die sich von einem Ausführen von `azds prep` unterscheidet. Aus Visual Studio Code oder Visual Studio können Sie Breakpoints festlegen und Ihre Anwendung im Entwicklungsbereich starten.

![Debuggen von Code](media/get-started-node/debug-configuration-nodejs2.png)

Wenn Sie Ihre Anwendung über Visual Studio Code oder Visual Studio für ein Debuggen starten, erfolgen das Starten sowie das Verbinden mit Ihrem Entwicklungsbereich auf die gleiche Weise wie bei einem Ausführen von `azds up`. Die clientseitigen Tools in Visual Studio Code und Visual Studio stellen auch einen zusätzlichen Parameter mit speziellen Informationen für das Debuggen bereit. Der Parameter enthält den Namen des Debugger-Image, den Speicherort des Debuggers im Image des Debuggers und den Zielspeicherort im Container der Anwendung, um den Debugger-Ordner bereitzustellen.

Das Debugger-Image wird automatisch durch die clientseitigen Tools bestimmt. Dazu wird eine Methode verwendet, die derjenigen ähnelt, die bei der Generierung der Dockerfile-Datei und des Helm-Charts beim Ausführen von `azds prep` verwendet wird. Sobald der Debugger im Image der Anwendung bereitgestellt ist, wird er mit `azds exec` ausgeführt.

## <a name="sharing-a-dev-space"></a>Freigeben eines Entwicklungsbereichs (Dev Space)

Wenn Sie in einem Team arbeiten, können Sie [einen Entwicklungsbereich für das gesamte Team freigeben](how-to/share-dev-spaces.md) und abgeleitete Entwicklungsbereiche erstellen. Ein Entwicklungsbereich kann von jeder Person verwendet werden, die Zugriff für Mitwirkende auf die Ressourcengruppe des Entwicklungsbereichs hat.

Sie können außerdem einen neuen Entwicklungsbereich erstellen, der aus einem anderen Entwicklungsbereich abgeleitet ist. Wenn Sie einen abgeleiteten Entwicklungsbereich erstellen, wird dem Namespace dieses abgeleiteten Entwicklungsbereichs die Bezeichnung *azds.io/parent-space=NAME-ÜBERGEORDNETER-BEREICH* hinzugefügt. Darüber hinaus werden alle Anwendungen aus dem übergeordneten Entwicklungsbereich für den abgeleiteten Entwicklungsbereich freigegeben. Wenn Sie eine aktualisierte Version einer Anwendung im abgeleiteten Entwicklungsbereich bereitstellen, ist diese Version nur im abgeleiteten Entwicklungsbereich vorhanden, und der übergeordnete Entwicklungsbereich ist nicht betroffen. Sie können mit bis zu drei Ebenen abgeleiteter Entwicklungsbereiche oder *über-übergeordneter* Bereiche arbeiten.

In einem abgeleiteten Entwicklungsbereich werden Anforderungen zwischen dessen eigenen Anwendungen und den Anwendungen, die aus seinem übergeordneten Bereich freigegeben sind, auf intelligente Weise weitergeleitet. Das Weiterleiten (Routing) funktioniert, indem versucht wird, eine Anforderung an eine Anwendung im abgeleiteten Entwicklungsbereich weiterzuleiten, und ggf. auf die freigegebene Anwendung aus dem übergeordneten Entwicklungsbereich zurückgegriffen wird. Für das Routing wird auf die freigegebene Anwendung im über-übergeordneten Bereich zurückgegriffen, wenn die Anwendung im übergeordneten Bereich nicht vorhanden ist.

Beispiel:
* Im Entwicklungsbereich *default* gibt es die Anwendungen *serviceA* und *serviceB*.
* Der Entwicklungsbereich *azureuser* ist aus *default* abgeleitet.
* Eine aktualisierte Version von *serviceA* wird in *azureuser* bereitgestellt.

Wird *azureuser* verwendet, werden alle Anforderungen an *serviceA* an die aktualisierte Version in *azureuser* weitergeleitet. Für eine Anforderung an *serviceB* wird zuerst versucht, sie an die *azureuser*-Version von *serviceB* weiterzuleiten. Da diese nicht vorhanden ist, wird die Anforderung an die *default*-Version von *serviceB* weitergeleitet. Wurde die *azureuser*-Version von *serviceA* entfernt, erfolgt für alle Anforderungen an *serviceA* ein Rückgriff auf die *default*-Version von *serviceA*.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Einstieg in Azure Dev Spaces finden Sie in den folgenden Schnellstarts:

* [Java mit CLI and Visual Studio Code](quickstart-java.md)
* [.NET Core mit CLI and Visual Studio Code](quickstart-netcore.md)
* [.NET Core mit Visual Studio](quickstart-netcore-visualstudio.md)
* [Node.js mit CLI and Visual Studio Code](quickstart-nodejs.md)

Informationen zum Einstieg in die Entwicklung im Team finden Sie in der folgenden Anleitungen:

* [Teamentwicklung – Java mit CLI and Visual Studio Code](team-development-java.md)
* [Teamentwicklung – .NET Core mit CLI and Visual Studio Code](team-development-netcore.md)
* [Teamentwicklung – .NET Core mit Visual Studio 2017](team-development-netcore-visualstudio.md)
* [Teamentwicklung – Node.js mit CLI and Visual Studio Code](team-development-nodejs.md)



[supported-regions]: about.md#supported-regions-and-configurations
