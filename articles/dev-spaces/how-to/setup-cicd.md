---
title: Verwenden von CI/CD mit Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Einrichten von Continuous Integration/Continuous Deployment mithilfe von Azure DevOps mit Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, Container
ms.openlocfilehash: de409aa060034c9ba0faaaa56ce21f904b02cdac
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017779"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Verwenden von CI/CD mit Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

In diesem Artikel werden Sie durch die Einrichtung von Continuous Integration/Continuous Deployment (CI/CD) für den Azure Kubernetes Service (AKS) mit aktivierten Dev Spaces geführt. CI/CD für AKS ermöglicht die automatische Bereitstellung von App-Updates, wenn Code in Ihr Quellrepository übertragen wird, für den ein Commit ausgeführt wurde. Die Verwendung von CI/CD in Verbindung mit einem Dev Spaces-fähigen Cluster ist sinnvoll, da dadurch eine Baseline der Anwendung auf dem neuesten Stand gehalten werden kann, mit der das Team arbeiten kann.

![CI/CD-Beispieldiagramm](../media/common/ci-cd-simple.png)

Obwohl Sie in diesem Artikel mit Azure DevOps geführt werden, gelten dieselben Konzepte für CI/CD-Systeme wie Jenkins, TeamCity, usw.

## <a name="prerequisites"></a>Voraussetzungen
* AKS-Cluster (Azure Kubernetes Service) mit aktivierten Azure Dev Spaces
* [Installierte Azure Dev Spaces-Befehlszeilenschnittstelle](upgrade-tools.md)
* [Azure DevOps-Organisation mit einem Projekt](/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Verfügbare Details zum [Administratorkonto](../../container-registry/container-registry-authentication.md#admin-account) für Azure Container Registry
* [Autorisieren des AKS-Clusters zum Ausführen eines Pulls von Azure Container Registry](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Beispielcode herunterladen
Erstellen Sie eine Fork des Beispielcode-GitHub-Repositorys, um Zeit zu sparen. Wechseln Sie zu https://github.com/Azure/dev-spaces, und wählen Sie **Fork** aus. Nachdem der Fork-Prozess abgeschlossen ist, **klonen** Sie Ihre Fork-Version des Repositorys lokal. Standardmäßig wird der _Masterbranch_ ausgecheckt, aber wir haben einige zeitsparende Änderungen in den Branch _azds_updates_ einbezogen, die auch während Ihres Forks hätten übertragen werden sollen. Der Branch _azds_updates_ enthält Updates, die Sie in den Tutorialabschnitten zu Dev Spaces manuell vornehmen müssen, sowie einige vorgefertigte YAML- und JSON-Dateien zur Optimierung der Bereitstellung des CI/CD-Systems. Sie können einen Befehl wie `git checkout -b azds_updates origin/azds_updates` verwenden, um den Branch _azds_updates_ in Ihrem lokalen Repository auszuchecken.

## <a name="dev-spaces-setup"></a>Dev Spaces-Setup
Erstellen Sie einen neuen Bereich namens _dev_ mit dem Befehl `azds space select`. Der Bereich _dev_ wird von Ihrer CI/CD-Pipeline verwendet, um Ihre Codeänderungen zu pushen. Er wird auch verwendet, um _untergeordnete Bereiche_ basierend auf _dev_ zu erstellen.

```cmd
azds space select -n dev
```

Wenn Sie zum Auswählen eines übergeordneten Entwicklungsbereichs aufgefordert werden, wählen Sie _\<none\>_ aus.

Nachdem Ihr dev-Bereich erstellt wurde, müssen Sie das Hostsuffix bestimmen. Verwenden Sie den Befehl `azds show-context`, um das Hostsuffix des Eingangscontrollers von Azure Dev Spaces anzuzeigen.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

Im obigen Beispiel lautet das Hostsuffix _fedcba098.eus.azds.io_. Dieser Wert wird später beim Erstellen der Releasedefinition verwendet.

Der Bereich _dev_ enthält immer den neuesten Zustand des Repositorys, eine Baseline, sodass Entwickler _untergeordnete Bereiche_ aus _dev_ erstellen können, um ihre isolierten Änderungen im Kontext der größeren App zu testen. Dieses Konzept wird in den Tutorials zu Dev Spaces näher erläutert.

## <a name="creating-the-build-definition"></a>Erstellen der Builddefinition
Öffnen Sie Ihr Azure DevOps-Teamprojekt in einem Webbrowser, und navigieren Sie zum Abschnitt _Pipelines_. Klicken Sie zunächst in der oberen rechten Ecke der Azure DevOps-Website auf Ihr Profilfoto, öffnen Sie den Previewfunktionsbereich, und deaktivieren Sie die Option _Neue Benutzeroberfläche zum Erstellen einer YAML-Pipeline_:

![Öffnen des Previewfunktionsbereichs](../media/common/preview-feature-open.png)

Die zu deaktivierende Option:

![Option „Neue Benutzeroberfläche zum Erstellen einer YAML-Pipeline“](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Die Azure DevOps-Previewfunktion _Neue Benutzeroberfläche zum Erstellen einer YAML-Pipeline_ steht zu diesem Zeitpunkt in Konflikt mit der Erstellung vordefinierter Buildpipelines. Sie müssen sie vorerst deaktivieren, um unsere vordefinierte Buildpipeline bereitzustellen.

In den Branch _azds_updates_ haben wir eine einfache [Azure Pipeline YAML](/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) einbezogen, die die für *mywebapi* und *webfrontend* erforderlichen Buildschritte definiert.

Je nach gewählter Sprache wurde die Pipeline-YAML in einem ähnlichen Pfad wie dem folgenden eingecheckt: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

So erstellen eine Pipeline aus dieser Datei
1. Navigieren Sie auf der Hauptseite Ihres DevOps-Projekts zu „Pipelines“ > „Builds“.
1. Wählen Sie die Option zum Erstellen einer **neuen** Buildpipeline aus.
1. Wählen Sie als Quelle **GitHub** aus, autorisieren Sie sich bei Bedarf bei Ihrem GitHub-Konto, und wählen Sie den Branch _azds_updates_ in Ihrer geforkten Version des Repositorys _dev-spaces_ mit der Beispielanwendung aus.
1. Wählen Sie **Konfiguration als Code** oder **YAML** als Vorlage aus.
1. Es wird Ihnen jetzt eine Konfigurationsseite für Ihre Buildpipeline angezeigt. Navigieren Sie wie oben erwähnt über die Schaltfläche **...** zum sprachspezifischen Pfad für den **YAML-Dateipfad**. Beispiel: `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Wechseln Sie zur Registerkarte **Variablen**.
1. Fügen Sie _dockerId_ manuell als Variable hinzu, wobei es sich um den Benutzernamen Ihres [Azure Container Registry Administratorkontos](../../container-registry/container-registry-authentication.md#admin-account) handelt. (In den Voraussetzungen zum Artikel erwähnt)
1. Fügen Sie _dockerPassword_ manuell als Variable hinzu, wobei es sich um das Kennwort Ihres [Azure Container Registry Administratorkontos](../../container-registry/container-registry-authentication.md#admin-account) handelt. Stellen Sie sicher, dass Sie _dockerPassword_ aus Sicherheitsgründen als Geheimnis (durch Auswahl des Schlosssymbols) angeben.
1. Wählen Sie **Save & queue** (Speichern und in Warteschlange einreihen) aus.

Sie verfügen jetzt über eine CI-Lösung, die automatisch *mywebapi* und *webfrontend* für jedes Update erstellt, das per Push in den Branch _azds_updates_ Ihrer GitHub-Fork gesendet wird. Sie können überprüfen, ob die Docker-Images per Push übertragen wurden, indem Sie zum Azure-Portal navigieren, Ihren Azure Container Registry-Dienst auswählen und die Registerkarte **Repositorys** durchsuchen. Es kann einige Minuten dauern, bis die Images erstellt und in Ihrer Containerregistrierung angezeigt werden.

![Azure Container Registry-Repositorys](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Erstellen der Releasedefinition

1. Navigieren Sie auf der Hauptseite Ihres DevOps-Projekts zu „Pipelines > Releases“.
1. Wenn Sie an einem neuen DevOps-Projekt arbeiten, das noch keine Releasedefinition enthält, müssen Sie zuerst eine leere Releasedefinition erstellen, bevor Sie fortfahren können. Die Importoption wird auf der Benutzeroberfläche erst angezeigt, wenn Sie über eine bestehende Releasedefinition verfügen.
1. Klicken Sie links auf die Schaltfläche **+ Neu** und dann auf **Import a pipeline** (Pipeline importieren).
1. Klicken Sie auf **Durchsuchen**, und wählen Sie `samples/release.json` aus Ihrem Projekt aus.
1. Klicken Sie auf **OK**. Beachten Sie, dass der Bereich „Pipeline“ mit der Bearbeitungsseite für die Releasedefinition geladen wurde. Beachten Sie außerdem, dass einige rote Warnsymbole angezeigt werden, die auf clusterspezifische Details hinweisen, die noch konfiguriert werden müssen.
1. Klicken Sie links im Bereich „Pipeline“ auf **Artefakt hinzufügen**.
1. Wählen Sie in der Dropdownliste **Quelle** die Buildpipeline aus, die Sie zuvor erstellt haben.
1. Wählen Sie für die **Standardversion** die Option **Latest from the build pipeline default branch with tags** (Neuestes Element aus dem Standardbranch der Buildpipeline mit Tags) aus.
1. Lassen Sie **Tags** leer.
1. Legen Sie für **Quellalias** den Wert `drop` fest. Da der Wert **Quellalias** von den vordefinierten Freigabetasks verwendet wird, ist die Angabe erforderlich.
1. Klicken Sie auf **Hinzufügen**.
1. Klicken Sie jetzt auf das Blitzsymbol auf der neu erstellten `drop`-Artefaktquelle, wie unten gezeigt:

    ![Continuous Deployment-Setup für Releaseartefakt](../media/common/release-artifact-cd-setup.png)
1. Aktivieren Sie den **Continuous Deployment-Trigger**.
1. Zeigen Sie neben **Pipeline** auf die Registerkarte **Aufgaben**, und klicken Sie auf _dev_, um die _dev_-Stufenaufgaben zu bearbeiten.
1. Stellen Sie sicher, dass **Azure Resource Manager** unter **Verbindungstyp** ausgewählt ist. Die drei Dropdown-Steuerelemente werden dann rot hervorgehoben: ![Einrichten der Releasedefinition](../media/common/release-setup-tasks.png)
1. Wählen Sie das Azure-Abonnement aus, das Sie mit Azure Dev Spaces verwenden. Möglicherweise müssen Sie auch auf **Autorisieren** klicken.
1. Wählen Sie die Ressourcengruppe und den Cluster aus, die Sie mit Azure Dev Spaces verwenden.
1. Klicken Sie auf **Agent job** (Agentauftrag).
1. Wählen Sie unter **Agent pool** (Agentpool) **Hosted Ubuntu 1604** aus.
1. Zeigen Sie oben auf den Selektor **Aufgaben**, und klicken Sie auf _prod_, um die _prod_-Stufenaufgaben zu bearbeiten.
1. Stellen Sie sicher, dass **Azure Resource Manager** unter **Verbindungstyp** ausgewählt ist. Wählen Sie dann das Azure-Abonnement, die Ressourcengruppe und den Cluster aus, die Sie mit Azure Dev Spaces verwenden.
1. Klicken Sie auf **Agent job** (Agentauftrag).
1. Wählen Sie unter **Agent pool** (Agentpool) **Hosted Ubuntu 1604** aus.
1. Klicken Sie auf die Registerkarte **Variablen**, um die Variablen für Ihr Release zu aktualisieren.
1. Ändern Sie den Wert von **DevSpacesHostSuffix** von **UPDATE_ME** in Ihr Hostsuffix. Das Hostsuffix wird angezeigt, wenn Sie zuvor den Befehl `azds show-context` ausgeführt haben.
1. Klicken Sie oben rechts auf **Speichern** und dann auf **OK**.
1. Klicken Sie auf **+ Release** (neben der Schaltfläche „Speichern“), und **erstellen Sie ein Release**.
1. Vergewissern Sie sich, dass unter **Artefakte** der neueste Build aus Ihrer Buildpipeline ausgewählt ist.
1. Klicken Sie auf **Erstellen**.

Jetzt beginnt ein automatisierter Releaseprozess und stellt die Diagramme *mywebapi* und *webfrontend* in Ihrem Kubernetes-Cluster im obersten Bereich von _dev_ bereit. Sie können den Releasefortschritt im Azure DevOps-Webportal verfolgen:

1. Navigieren Sie unter **Pipelines** zum Abschnitt **Releases** (Versionen).
1. Klicken Sie auf die Releasepipeline für die Beispielanwendung.
1. Klicken Sie auf den Namen des neuesten Release.
1. Zeigen Sie unter **Stages** (Stufen) auf das Feld **dev**, und klicken Sie auf **Protokolle**.

Das Release ist erfolgt, wenn alle Aufgaben abgeschlossen sind.

> [!TIP]
> Wenn Ihr Release mit einer Fehlermeldung wie *FEHLER BEIM UPGRADE: Timeout beim Warten auf die Bedingung* fehlschlägt, versuchen Sie, die Pods in Ihrem Cluster [ mit dem Kubernetes-Dashboard](../../aks/kubernetes-dashboard.md) zu überprüfen. Wenn Sie feststellen, dass die Pods nicht mit Fehlermeldungen wie *Fehler beim Pullen von Image "azdsexample.azurecr.io/mywebapi:122": RPC-Fehler: code = Unbekannt desc = Fehlerantwort von Daemon: Abrufen von \//azdsexample.azurecr.io/v2/mywebapi/manifests/122: nicht autorisiert: Authentifizierung erforderlich* beginnen können, kann es daran liegen, dass Ihr Cluster nicht die Berechtigung erhalten hat, einen Pull aus Ihrer Azure Container Registry auszuführen. Vergewissern Sie sich, dass Sie die Voraussetzung [Autorisieren des AKS-Clusters zum Ausführen eines Pulls von Azure Container Registry](../../aks/cluster-container-registry-integration.md) abgeschlossen haben.

Sie verfügen jetzt über eine vollständig automatisierte CI/CD-Pipeline für Ihre GitHub-Fork der Dev Spaces-Beispielanwendungen. Jedes Mal, wenn Sie einen Commit und Push für Code ausführen, erstellt die Buildpipeline die Images *mywebapi* und *webfrontend* und verschiebt sie in Ihre benutzerdefinierte ACR-Instanz. Dann stellt die Releasepipeline das Helm-Diagramm für jede Anwendung im Bereich _dev_ auf Ihrem Dev Spaces-fähigen Cluster bereit.

## <a name="accessing-your-_dev_-services"></a>Zugreifen auf Ihre _dev_-Dienste
Nach der Bereitstellung kann auf die _dev_-Version von *webfrontend* mit einer öffentlichen URL wie `http://dev.webfrontend.fedcba098.eus.azds.io` zugegriffen werden. Sie können diese URL finden, indem Sie den Befehl `azds list-uri` ausführen: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Bereitstellen in der Produktionsumgebung

So stufen Sie ein bestimmtes Release mit dem in diesem Tutorial erstellten CI/CD-System manuell auf _prod_ hoch
1. Navigieren Sie unter **Pipelines** zum Abschnitt **Releases** (Versionen).
1. Klicken Sie auf die Releasepipeline für die Beispielanwendung.
1. Klicken Sie auf den Namen des neuesten Release.
1. Zeigen Sie unter **Stages** (Stufen) auf das Feld **prod**, und klicken Sie auf **Bereitstellen**.
    ![Höherstufen in die Produktion](../media/common/prod-promote.png)
1. Zeigen Sie erneut unter **Stages** (Stufen) auf das Feld **prod**, und klicken Sie auf **Protokolle**.

Das Release ist erfolgt, wenn alle Aufgaben abgeschlossen sind.

Auf der Stufe _prod_ der CI/CD-Pipeline wird ein Lastenausgleich anstelle des Eingangscontrollers von Dev Spaces verwendet, um Zugriff auf _prod_-Dienste zu gewähren. Auf Dienste, die auf der Stufe _prod_ bereitgestellt werden, kann als IP-Adressen anstatt DNS-Namen zugegriffen werden. In einer Produktionsumgebung können Sie Ihren eigenen Eingangscontroller erstellen, um Ihre Dienste basierend auf Ihrer eigenen DNS-Konfiguration zu hosten.

Klicken Sie auf den Schritt **Print webfrontend public IP** (Öffentliche Web-Front-End-IP-Adresse drucken), um die Protokollausgabe zu erweitern und die IP-Adresse des Web-Front-End-Diensts zu bestimmen. Verwenden Sie die IP-Adresse, die in der Protokollausgabe angezeigt wird, um auf die **Web-Front-End**-Anwendung zuzugreifen.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Dev Spaces-Instrumentierung in der Produktionsumgebung
Obwohl die Instrumentierung von Dev Spaces _nicht_ so konzipiert wurde, dass sie den normalen Betrieb Ihrer Anwendung behindert, wird empfohlen, Ihre Produktionsworkloads in einem Kubernetes-Namespace auszuführen, der nicht mit Dev Spaces aktiviert ist. Die Verwendung dieser Art von Kubernetes-Namespace bedeutet, dass Sie entweder Ihren Produktionsnamespace mithilfe der `kubectl`-Befehlszeilenschnittstelle erstellen oder Ihrem CI/CD-System gestatten sollten, ihn während der ersten Helm-Bereitstellung zu erstellen. Wenn Sie einen Bereich mit den Dev Spaces-Tools _auswählen_ oder anderweitig erstellen, wird die Instrumentierung von Dev Spaces zu diesem Namespace hinzugefügt.

Hier folgt eine Beispielnamespacestruktur, die die Featureentwicklung, die Entwicklungsumgebung, _und_ die Produktionsumgebung in einem einzigen Kubernetes-Cluster unterstützt:

![Beispielnamespacestruktur](../media/common/cicd-namespaces.png)

> [!Tip]
> Wenn Sie bereits einen `prod`-Bereich erstellt haben und ihn einfach von der Dev Spaces-Instrumentierung ausschließen möchten (ohne ihn zu löschen), können Sie dies mit dem folgenden Befehl der Dev Spaces-Befehlszeilenschnittstelle erreichen:
>
> `azds space remove -n prod --no-delete`
>
> Möglicherweise müssen Sie danach alle Pods im Namespace `prod` löschen, damit sie ohne Dev Spaces-Instrumentierung wiederhergestellt werden können.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Funktionsweise von Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Funktionsweise von Azure Dev Spaces](../how-dev-spaces-works.md)
