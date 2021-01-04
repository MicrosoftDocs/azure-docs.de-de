---
title: Migrieren zu Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Beschreibt den Migrationsprozess von Azure Dev Spaces zu Kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Bridge to Kubernetes
ms.openlocfilehash: d48814df30c17f9b51d8642efa0960a26bbd24f4
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888520"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrieren zu Bridge to Kubernetes

> [!IMPORTANT]
> Azure Dev Spaces wird am 31. Oktober 2023 eingestellt. Kunden sollten zu Bridge to Kubernetes wechseln, einem Cliententwicklertool.
>
> Der Zweck von Azure Dev Spaces war es, Benutzer bei der Entwicklung auf Kubernetes zu unterstützen. Ein gravierender Nachteil des Ansatzes von Azure Dev Spaces ist, dass Benutzer zusätzlichen Aufwand hatten, da sie sich in die Konfiguration von Docker und Kubernetes sowie Kubernetes-Bereitstellungskonzepte einarbeiten mussten. Im Laufe der Zeit zeigte sich außerdem, dass der Ansatz von Azure Dev Spaces die Geschwindigkeit der Entwicklung der inneren Schleife auf Kubernetes nicht wirklich verringert hat. Bridge to Kubernetes verringert die Geschwindigkeit der Entwicklung der inneren Schleife und vermeidet unnötigen Benutzeraufwand.
>
> Das Hauptziel bleibt gleich: Die besten Umgebungen zum Entwickeln, Testen und Debuggen von Code im Kontext der größeren Anwendung anzubieten.

Bridge to Kubernetes ist eine einfachere Alternative zu vielen Entwicklungsszenarien, die mit Azure Dev Spaces funktionieren. Bridge zu Kubernetes ist eine ausschließlich clientseitige Erfahrung mit Verwendung von Erweiterungen in [Visual Studio][vs] und [Visual Studio Code][vsc].  

Bridge to Kubernetes unterstützt Ihre Entwicklungsumgebung, indem eine etablierte Kubernetes-Anwendung einen Dienst beinhalten kann, der auf Ihrer lokalen Entwicklungsarbeitsstation ausgeführt wird. Im Gegensatz zu Dev Spaces reduziert Bridge to Kubernetes die Komplexität innerer Schleifen, indem die Notwendigkeit umgangen wird, Docker- und Kubernetes-Konfigurationen zu erstellen, sodass Entwickler sich ausschließlich auf die Geschäftslogik Ihres Microservice-Codes konzentrieren können.

Bridge to Kubernetes unterstützt Sie bei der Iteration von Code, der auf dem Entwicklungscomputer ausgeführt wird, während Abhängigkeiten und vorhandene Konfigurationen aus Ihrer Kubernetes-Umgebung genutzt werden. Im Gegensatz dazu stellt Azure Dev Spaces Ihren Microservice in der Kubernetes-Umgebung bereit, bevor Sie den Dienst remote debuggen und Iterationen auf den Code anwenden können.

Dieser Artikel enthält einen Vergleich zwischen Azure Dev Spaces und Bridge to Kubernetes sowie Anweisungen für die Migration von Azure Dev Spaces zu Bridge to Kubernetes.

## <a name="development-approaches"></a>Entwicklungsansätze

![Entwicklungsansätze](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces unterstützte Kubernetes-Entwickler beim Arbeiten mit direkt in ihrem AKS-Cluster ausgeführten Code, sodass keine lokale Umgebung erforderlich war, die nicht der bereitgestellten Umgebung entsprach. Dieser Ansatz verbesserte bestimmte Aspekte der Entwicklung, setzte aber auch voraus, das Sie zusätzliche Konzepte wie Docker, Kubernetes und Helm erlernten und verwalteten, bevor Sie Azure Dev Spaces verwenden konnten.

Mithilfe von Bridge to Kubernetes können Entwickler direkt auf ihren Entwicklungscomputern arbeiten, während sie mit dem restlichen Cluster interagieren. Dieser Ansatz nutzt die Vertrautheit und Geschwindigkeit der direkten Codeausführung auf den Entwicklungscomputern, während die von ihrem Cluster bereitgestellten Abhängigkeiten und die Umgebung gemeinsam genutzt werden. Dieser Ansatz nutzt auch die Genauigkeit und Skalierung, die von der Ausführung in Kubernetes ausgeht.

## <a name="feature-comparison"></a>Featurevergleich

Azure Dev Spaces und Bridge to Kubernetes haben ähnliche Features, aber unterscheiden sich auch in verschiedenen Bereichen:

| Anforderung  | Azure Dev Spaces  | Bridge to Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | In 15 Azure-Regionen | Jede AKS-Dienstregion    |
| **Security** |
| Auf Ihrem Cluster benötigter Sicherheitszugriff  | AKS-Cluster-Mitwirkender  | Kubernetes RBAC-Update für die Bereitstellung   |
| Auf Ihrem Entwicklungscomputer benötigter Sicherheitszugriff  | –  | Lokaler Administrator/sudo   |
| **Benutzerfreundlichkeit** |
| Unabhängig von Kubernetes- und Docker-Artefakten  | Nein  | Ja   |
| Automatisches Rollback von Änderungen nach dem Debuggen  | Nein  | Ja   |
| **Unterstützte Clienttools** |
| Funktioniert mit Visual Studio 2019  | Ja  | Ja   |
| Funktioniert mit Visual Studio Code  | Ja  | Ja   |
| Funktioniert mit einer CLI  | Ja  | Nein   |
| **Betriebssystemkompatibilität** |
| Funktioniert unter Windows 10  | Ja  | Ja  |
| Funktioniert unter Linux  | Ja  | Ja  |
| Funktioniert unter macOS  | Ja  | Ja  |
| **Capabilities** |
| Entwicklerisolation oder Teamentwicklung  | Ja  | Ja  |
| Selektives Überschreiben von Umgebungsvariablen  | Nein  | Ja  |
| Erstellen von Dockerfile und Helm-Diagramm  | Ja  | Nein  |
| Persistente Bereitstellung von Code für Kubernetes  | Ja  | Nein  |
| Remotedebuggen in einem Kubernetes-Pod  | Ja  | Nein  |
| Lokales Debuggen, verbunden mit Kubernetes  | Nein  | Ja  |
| Gleichzeitiges Debuggen mehrerer Dienste auf derselben Arbeitsstation  | Ja  | Ja  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes-Entwicklung der inneren Schleife

Der größte Unterschied zwischen Azure Dev Spaces und Bridge to Kubernetes ist der Ort der Codeausführung. Azure Dev Spaces unterstützt das Entwickeln und Debuggen Ihres Microservice-Codes, setzt jedoch voraus, dass Sie diesen Code in Ihrem Cluster ausführen. Mithilfe von Bridge to Kubernetes können Sie Ihren Microservice-Code direkt auf Ihrem Entwicklungscomputer entwickeln und debuggen und bleiben dabei im Kontext der größeren, in Kubernetes ausgeführten Anwendung. Bridge to Kubernetes erweitert den Umkreis des Kubernetes-Clusters und ermöglicht lokalen Prozessen, die Konfiguration von Kubernetes zu erben.

![Entwicklung der inneren Schleife](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Wenn Sie Bridge to Kubernetes verwenden, wird eine Netzwerkverbindung zwischen dem Entwicklungscomputer und dem Cluster hergestellt. Für die Lebensdauer dieser Verbindung wird Ihrem Cluster anstelle der Kubernetes-Bereitstellung ein Proxy hinzugefügt, der die Anforderungen an den Dienst an ihren Entwicklungscomputer umleitet. Wenn Sie die Verbindung trennen, wird die ursprüngliche, auf dem Cluster ausgeführte Version der Bereitstellung von der Anwendungsbereitstellung wiederverwendet. Diese Vorgehensweise unterscheidet sich von der Funktionsweise von Azure Dev Spaces, bei der der Code mit dem Cluster synchronisiert, erstellt und anschließend ausgeführt wird. Azure Dev Spaces führt auch keinen Rollback Ihres Codes durch.

Bridge to Kubernetes bietet die Flexibilität, unabhängig von der Bereitstellungsmethode mit Anwendungen zu arbeiten, die in Kubernetes ausgeführt werden. Wenn Sie Ihre Anwendung mit CI/CD erstellen und ausführen, können Sie unabhängig davon, ob Sie die eingerichteten Tools oder benutzerdefinierte Skripts verwenden, weiterhin mit Bridge to Kubernetes Ihren Code entwickeln und debuggen.

> [!TIP]
> Mit der  [Microsoft Kubernetes-Erweiterung][kubernetes-extension] können Sie schnell Kubernetes-Manifeste mit IntelliSense entwickeln und Helm-Diagramme erstellen.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Umstieg von Azure Dev Spaces auf Bridge to Kubernetes

1. Wenn Sie Visual Studio verwenden, aktualisieren Sie Ihre Visual Studio-IDE auf Version 16.7 oder höher, und installieren Sie die Bridge to Kubernetes-Erweiterung aus dem [Visual Studio Marketplace][vs-marketplace]. Wenn Sie Visual Studio Code verwenden, installieren Sie die [Bridge to Kubernetes-Erweiterung][vsc-marketplace].
1. Deaktivieren Sie den Azure Dev Spaces-Controller mithilfe des Azure-Portals oder der [Azure Dev Spaces-CLI][azds-delete].
1. Verwenden Sie [Azure Cloud Shell](https://shell.azure.com). Auf dem Mac und unter Linux oder Windows mit installiertem Bash öffnen Sie eine Bash-Shell-Eingabeaufforderung. Stellen Sie sicher, dass die folgenden Tools in der Befehlszeilenumgebung verfügbar sind: Azure CLI, Docker, kubectl, curl, tar und gunzip.
1. Erstellen Sie eine Containerregistrierung, oder verwenden Sie eine vorhandene. Sie können eine Containerregistrierung in Azure mit [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) oder [Docker Hub](https://hub.docker.com/) erstellen. Wenn Sie Azure Cloud Shell verwenden, steht nur Azure Container Registry für das Hosten von Docker-Images zur Verfügung.
1. Führen Sie das Migrationsskript aus, um Azure Dev Spaces-Ressourcen in Bridge to Kubernetes-Ressourcen zu konvertieren. Das Skript erstellt ein neues Image, das mit Bridge to Kubernetes kompatibel ist, lädt es in die angegebene Registrierung hoch und verwendet dann [Helm](https://helm.sh), um den Cluster mit dem Image zu aktualisieren. Sie müssen die Ressourcengruppe, den Namen des AKS-Clusters und eine Containerregistrierung angeben. Weitere verfügbare Befehlszeilenoptionen sind hier aufgeführt:

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   Das Skript unterstützt die folgenden Flags:

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. Migrieren Sie Anpassungen (z. b. Umgebungsvariableneinstellungen) in *azds.yaml* manuell in die Datei *values.yml* Ihres Projekts.
1. (Optional) Entfernen Sie die Datei `azds.yaml` aus dem Projekt.
1. Konfigurieren Sie Bridge to Kubernetes in Ihrer bereitgestellten Anwendung. Weitere Informationen zur Verwendung von Bridge to Kubernetes in Visual Studio finden Sie unter [Herstellen einer Clusterverbindung und Debuggen eines Diensts in Visual Studio][use-btk-vs]. VS-Code finden Sie unter [Verwenden von Bridge to Kubernetes in VS-Code][use-btk-vsc].
1. Starten Sie das Debuggen mithilfe des neu erstellten Bridge to Kubernetes-Debug-/-Startprofils.
1. Sie können das Skript bei Bedarf erneut ausführen, um die Bereitstellung für Ihren Cluster zu wiederholen.

## <a name="team-development-in-a-shared-cluster"></a>Teamentwicklung in einem freigegebenen Cluster

Sie können auch entwicklerspezifisches Routing mit Bridge to Kubernetes verwenden. Im Teamentwicklungsszenario von Azure Dev Spaces werden mehrere Kubernetes-Namespaces verwendet, um einen Dienst vom Rest der Anwendung zu isolieren, indem das Konzept von übergeordneten und untergeordneten Namespaces verwendet wird. Bridge to Kubernetes bietet die gleiche Funktion, aber mit verbesserten Leistungsmerkmalen und innerhalb desselben Anwendungsnamespace.

Sowohl Bridge to Kubernetes als auch Azure Dev Spaces erfordert, dass HTTP-Header vorhanden sind und in der gesamten Anwendung verteilt werden. Wenn Sie die Anwendung bereits so konfiguriert haben, dass die Headerverteilung für Azure Dev Spaces behandelt wird, muss der Header aktualisiert werden. Aktualisieren Sie für den Übergang von Azure Dev Spaces zu Bridge to Kubernetes den konfigurierten Header von *azds-route-as* auf *kubernetes-route-as*.

## <a name="evaluate-bridge-to-kubernetes"></a>Auswerten von Bridge to Kubernetes

Die einfachste Möglichkeit, mit Bridge to Kubernetes zu experimentieren, bevor Sie Azure Dev Spaces in Ihrem Cluster deaktivieren, ist die Verwendung eines neuen Clusters. Wenn Sie versuchen, Azure Dev Spaces und Bridge to Kubernetes gleichzeitig auf demselben Cluster zu verwenden, treten Probleme auf, wenn Sie die Routingfeatures für beide verwenden.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Verwenden von Visual Studio zum Bewerten von Bridge to Kubernetes

1. Aktualisieren Sie die Visual Studio-IDE mindestens auf Version 16.7, und installieren Sie die Bridge to Kubernetes-Erweiterung aus dem [Visual Studio Marketplace][vs-marketplace].
1. Erstellen Sie einen neuen AKS-Cluster, und stellen Sie die Anwendung bereit. Sie können auch eine [Beispielanwendung][btk-sample-app] verwenden.
1. Konfigurieren Sie Bridge to Kubernetes in Ihrer bereitgestellten Anwendung. Weitere Informationen zur Verwendung von Bridge to Kubernetes in Visual Studio finden Sie unter [Herstellen einer Clusterverbindung und Debuggen eines Diensts][use-btk-vs].
1. Starten Sie das Debuggen in Visual Studio mithilfe des neu erstellten Bridge to Kubernetes-Debugprofils.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Verwenden von Visual Studio Code zum Bewerten von Bridge to Kubernetes

1. Installieren Sie die [Bridge to Kubernetes-Erweiterung][vsc-marketplace].
1. Erstellen Sie einen neuen AKS-Cluster, und stellen Sie die Anwendung bereit. Sie können auch eine [Beispielanwendung][btk-sample-app] verwenden.
1. Konfigurieren Sie Bridge to Kubernetes in Ihrer bereitgestellten Anwendung. Weitere Informationen zur Verwendung von Bridge to Kubernetes in Visual Studio Code finden Sie unter [Herstellen einer Clusterverbindung und Debuggen eines Diensts][use-btk-vsc].
1. Starten Sie das Debuggen in Visual Studio mithilfe des neu erstellten Bridge to Kubernetes-Startprofils.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Funktionsweise von Bridge to Kubernetes.

> [!div class="nextstepaction"]
> [Funktionsweise von Bridge to Kubernetes][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
