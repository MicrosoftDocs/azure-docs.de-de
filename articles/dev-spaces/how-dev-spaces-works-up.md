---
title: Funktionsweise der Ausführung von Code mit Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beschreibt die Prozesse bei der Ausführung von Code unter Azure Kubernetes Service mit Azure Dev Spaces
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014430"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Funktionsweise der Ausführung von Code mit Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces bietet Ihnen mehrere Möglichkeiten, Kubernetes-Anwendungen schnell zu durchlaufen und zu debuggen und mit Ihrem Team in einem Azure Kubernetes Service-Cluster (AKS) zusammenzuarbeiten. Wenn Ihr [Projekt für die Ausführung in einem Entwicklungsbereich vorbereitet][how-it-works-prep] ist, können Sie Dev Spaces verwenden, um Ihr Projekt in Ihrem AKS-Cluster zu erstellen und auszuführen.

In diesem Artikel wird beschrieben, was geschieht, wenn Sie Ihren Code in AKS mit Dev Spaces ausführen.

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


## <a name="starting-a-service"></a>Starten eines Diensts

Wenn Sie einen Dienst in einem Entwicklungsbereich starten, arbeiten die clientseitigen Tools und der Controller zusammen, um Ihre Quelldateien zu synchronisieren, Ihren Container und Ihre Kubernetes-Objekte zu erstellen und Ihre Anwendung auszuführen.

In ausführlicherer Beschreibung passiert Folgendes, wenn Sie `azds up` ausführen:

1. Dateien werden vom Computer des Benutzers mit einem Azure-Dateispeicher [synchronisiert][sync-section], der nur für den AKS-Cluster des Benutzers verfügbar ist. Der Quellcode, das Helm-Chart und die Konfigurationsdateien werden hochgeladen.
1. Der Controller erstellt eine Anforderung, um eine neue Sitzung zu starten. Diese Anforderung enthält mehrere Eigenschaften, wozu eine eindeutige ID, ein Bereichsname, ein Pfad zum Quellcode und ein Debuggen-Flag gehören.
1. Der Controller ersetzt den *$(tag)* -Platzhalter im Helm-Chart durch die eindeutige Sitzungs-ID und installiert das Helm-Chart für Ihren Dienst. Dadurch, dass ein Verweis auf die eindeutige Sitzungs-ID zum Helm-Chart hinzugefügt wird, wird es ermöglicht, den Container, der im AKS-Cluster bereitgestellt wird, für diese spezielle Sitzung wieder an die Sitzungsanforderung und die zugehörigen Informationen zu binden.
1. Während der Installation des Helm-Charts fügt der Kubernetes-Webhook-Zulassungsserver dem Pod Ihrer Anwendung weitere Container für die Instrumentierung und für Zugriff auf den Quellcode Ihres Projekts hinzu. Der devspaces-proxy- und der devspaces-proxy-init-Container werden hinzugefügt, um HTTP-Ablaufverfolgung und -Bereichsrouting bereitzustellen. Der devspaces-build-Container wird hinzugefügt, damit der Pod auf die Docker-Instanz und den Projektquellcode zugreifen kann, um den Container Ihrer Anwendung zu erstellen.
1. Wenn der Pod der Anwendung gestartet wird, werden der devspaces-build-Container und der devspaces-proxy-init-Container verwendet, um den Anwendungscontainer zu erstellen. Anschließend werden der Anwendungscontainer und die devspaces-proxy-Container gestartet.
1. Nachdem der Anwendungscontainer gestartet wurde, wird auf der Clientseite die *port-forward*-Funktionalität von Kubernetes verwendet, um HTTP-Zugriff auf Ihre Anwendung über http://localhost bereitzustellen. Über diese Portweiterleitung wird Ihr Entwicklungscomputer mit dem Dienst in Ihrem Entwicklungsbereich verbunden.
1. Wenn alle Container im Pod gestartet sind, wird der Dienst ausgeführt. An diesem Punkt beginnt die clientseitige Funktionalität damit, die HTTP-Ablaufverfolgungen sowie „stdout“ und „stderr“ zu streamen. Diese Informationen werden für den Entwickler durch die clientseitige Funktionalität angezeigt.

## <a name="updating-a-running-service"></a>Aktualisieren eines aktiven Diensts

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

Die Art und Weise, wie *devhostagent* die vorherigen Schritte ausführt, ist [ in `azds.yaml`][azds-yaml-section] konfiguriert.

Aktualisierungen an Projektdateien, etwa Dockerfile-Dateien, CSPROJ-Dateien oder irgendein Teil des Helm-Charts, bedingen, dass der Container der Anwendung neu erstellt und neu bereitgestellt werden muss. Wird eine dieser Dateien mit dem Entwicklungsbereich synchronisiert, führt der Controller den [helm upgrade][helm-upgrade]-Befehl aus, und der Container der Anwendung wird neu erstellt und neu bereitgestellt.

## <a name="file-synchronization"></a>Dateisynchronisierung

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

## <a name="how-running-your-code-is-configured"></a>So wird das Ausführen Ihres Codes konfiguriert

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

Im obigen Beispiel teilt die *install.set.replicaCount*-Eigenschaft dem Controller mit, wie viele Instanzen Ihrer Anwendung in Ihrem Entwicklungsbereich ausgeführt werden sollen. Je nachdem, wie Ihr Szenario aussieht, können diesen Wert erhöhen. Dies wirkt sich jedoch auf das Einbinden eines Debuggers in den Pod Ihrer Anwendung aus. Weitere Informationen finden Sie im [Artikel zur Problembehandlung][troubleshooting].

Im generierten Helm-Chart wird das Containerimage auf *{{ .Values.image.repository }}:{{ .Values.image.tag }}* festgelegt. In der `azds.yaml`-Datei ist die *install.set.image.tag*-Eigenschaft standardmäßig als *$(tag)* definiert. Dieser Wert wird als der Wert für *{{ .Values.image.tag }}* verwendet. Durch Festlegen der *install.set.image.tag*-Eigenschaft auf diese Weise wird es ermöglicht, dass das Containerimage für Ihre Anwendung auf eindeutige Weise gekennzeichnet werden kann, wenn Azure Dev Spaces ausgeführt wird. In diesem speziellen Fall wird das Image als *\<value from image.repository>:$(tag)* gekennzeichnet. Sie müssen die *$(tag)* -Variable als Wert von *install.set.image.tag* verwenden, damit Dev Spaces den Container im AKS-Cluster erkennen und finden kann.

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

Wenn Sie Ihren Code mit dem `azds prep`-Befehl vorbereiten, haben die Möglichkeit, das `--enable-ingress`-Flag hinzuzufügen. Wird das `--enable-ingress`-Flag hinzugefügt, wird eine öffentlich zugängliche URL für Ihre Anwendung erstellt. Wenn Sie dieses Flag nicht angeben, kann nur im Cluster oder über den localhost-Tunnel auf die Anwendung zugegriffen werden. Nachdem Sie den `azds prep`-Befehl ausgeführt haben, können Sie diese Einstellung ändern, indem Sie die *ingress.enabled*-Eigenschaft in `charts/APPNAME/values.yaml` ändern:

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Netzwerken und zum Weiterleiten von Anforderungen in Azure Dev Spaces finden Sie unter [Funktionsweise von Routing mit Azure Dev Spaces][how-it-works-routing].

Weitere Informationen zur schnellen Iteration und Entwicklung mit Kubernetes finden Sie unter [Funktionsweise von Bridge to Kubernetes][how-it-works-bridge-to-kubernetes] und [So funktioniert das Remotedebuggen Ihres Codes mit Azure Dev Spaces][how-it-works-remote-debugging].


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md