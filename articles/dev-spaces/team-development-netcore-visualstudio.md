---
title: Teamentwicklung mit Azure Dev Spaces mit .NET Core und Visual Studio
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Schnelle Kubernetes-Entwicklung mit Containern und Microservices in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s '
ms.openlocfilehash: c3a988a831ad1069e5988f9c67e92a85a7a44840
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65765201"
---
# <a name="team-development-with-azure-dev-spaces"></a>Teamentwicklung mit Azure Dev Spaces

In diesem Tutorial erfahren Sie, wie ein Team von Entwicklern über Dev Spaces gleichzeitig im selben Kubernetes-Cluster zusammenarbeiten kann.

## <a name="learn-about-team-development"></a>Weitere Informationen zur Teamentwicklung

Bisher haben Sie den Code der Anwendung so ausgeführt, als wären Sie der einzige Entwickler, der an der App arbeitet. In diesem Abschnitt erfahren Sie, wie Azure Dev Spaces die Teamentwicklung optimiert:
* Ein Team von Entwicklern kann in der gleichen Umgebung arbeiten und je nach Bedarf einen gemeinsamen oder einen getrennten Entwicklungsbereich nutzen.
* Jeder Entwickler kann seinen Code isoliert und ohne Beeinträchtigung der anderen Entwickler durchlaufen.
* Testen Sie Ihren Code vollständig, bevor Sie ihn committen, ohne Modelle erstellen oder Abhängigkeiten simulieren zu müssen.

### <a name="challenges-with-developing-microservices"></a>Herausforderungen bei der Entwicklung von Microservices
Ihre Beispielanwendung ist im Moment nicht komplex. Bei der realen Entwicklung stoßen Sie jedoch bald auf Herausforderungen, wenn Sie mehr Dienste hinzufügen und das Entwicklungsteam wächst.

* Ihr Entwicklungscomputer verfügt möglicherweise nicht über genügend Ressourcen, um alle benötigten Dienste parallel auszuführen.
* Einige Dienste müssen ggf. öffentlich erreichbar sein. So kann es beispielsweise sein, dass ein Dienst einen Endpunkt benötigt, der auf einen Webhook reagiert.
* Wenn Sie eine Teilmenge der Dienste ausführen möchten, müssen Sie mit der gesamten Abhängigkeitshierarchie zwischen allen Ihren Diensten vertraut sein. Das Bestimmen dieser Hierarchie ist unter Umständen nicht ganz einfach – insbesondere bei zunehmender Anzahl von Diensten.
* Einige Entwickler greifen auf die Simulation zahlreicher Dienstabhängigkeiten zurück. Dies ist zwar hilfreich, die Verwaltung der Simulationen kann jedoch schnell die Entwicklungskosten in die Höhe treiben. Darüber hinaus führt dieser Ansatz dazu, dass sich Ihre Entwicklungsumgebung von der Produktionsumgebung unterscheidet, wodurch sich kleinere Fehler einschleichen können.
* Integrationstests können daher schwierig werden. Integrationstests sind nur nach einem Commit realistisch. Das bedeutet, dass Ihnen Probleme erst später im Entwicklungszyklus auffallen.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Arbeiten in einem gemeinsamen Entwicklungsbereich
Mit Azure Dev Spaces können Sie einen *gemeinsamen* Entwicklungsbereich in Azure einrichten. Jeder Entwickler kann sich auf seinen Teil der Anwendung konzentrieren und *Code vor dem Commit* iterativ in einem Entwicklungsbereich entwickeln, der bereits alle anderen Dienste und Cloudressourcen enthält, die der Entwickler für seine Szenarien benötigt. Abhängigkeiten sind immer aktuell, und Entwickler arbeiten wie in einer Produktionsumgebung.

### <a name="work-in-your-own-space"></a>Arbeiten im eigenen Bereich
Während der Entwicklung von Code für Ihren Dienst und vor dem Einchecken ist Code häufig nicht fehlerfrei. Sie strukturieren und testen den Code weiterhin iterativ und experimentieren mit Lösungen. In einem **Bereich** – einem Konzept von in Azure Dev Spaces – können Sie isoliert arbeiten, ohne Ihre Teammitglieder zu beeinträchtigen.

## <a name="use-dev-spaces-for-team-development"></a>Verwenden von Dev Spaces für die Teamentwicklung
Betrachten wir diese Konzepte konkret am Beispiel unserer Anwendung *webfrontend* -> *mywebapi*. In unserem Beispielszenario muss der Entwickler Scott eine Änderung an dem Dienst *mywebapi* vornehmen (und zwar *nur* an diesem Dienst). *webfrontend* muss für Scotts Update nicht geändert werden.

_Ohne_ Dev Spaces stehen Scott verschiedene Möglichkeiten zur Verfügung, um sein Update zu entwickeln und zu testen. Diese sind jedoch alle nicht optimal:
* Er kann ALLE Komponenten lokal ausführen. Dazu benötigt er einen leistungsfähigeren Entwicklungscomputer mit Docker-Installation und ggf. MiniKube.
* Er kann ALLE Komponenten in einem isolierten Namespace im Kubernetes-Cluster ausführen. Da *webfrontend* unverändert bleibt, wäre die Verwendung eines isolierten Namespaces eine Verschwendung von Clusterressourcen.
* Er kann NUR *mywebapi* ausführen und zu Testzwecken manuelle REST-Aufrufe verwenden. Bei Tests dieser Art wird nicht der End-to-End-Flow getestet.
* Er kann *webfrontend* entwicklungsspezifischen Code hinzufügen, der es dem Entwickler ermöglicht, Anforderungen an eine andere Instanz von *mywebapi* zu senden. Das Hinzufügen dieses Codes verkompliziert allerdings den Dienst *webfrontend*.

### <a name="set-up-your-baseline"></a>Einrichten Ihrer Baseline
Als Erstes müssen wir eine Baseline unserer Dienste bereitstellen. Diese Bereitstellung stellt den letzten als funktionierend bekannten Zustand dar, sodass Sie das Verhalten Ihres lokalen Codes problemlos mit der eingecheckten Version vergleichen können. Als Nächstes erstellen wir auf der Grundlage dieser Baseline einen untergeordneten Bereich, um unsere Änderungen an *mywebapi* im Kontext der übergeordneten Anwendung testen zu können.

1. Klonen Sie die [Dev Spaces-Beispielanwendung](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Checken Sie den Remotebranch *azds_updates* aus: `git checkout -b azds_updates origin/azds_updates`
1. Schließen Sie für beide Dienste alle F5-/Debugsitzungen, lassen Sie jedoch die Projekte in den jeweiligen Visual Studio-Fenstern geöffnet.
1. Wechseln Sie zum Visual Studio-Fenster mit dem Projekt _mywebapi_.
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.
1. Klicken Sie links auf die Registerkarte **Debuggen**, um die Azure Dev Spaces-Einstellungen anzuzeigen.
1. Wählen Sie **Ändern** aus, um den Bereich zu erstellen, der verwendet wird, wenn Sie den Dienst mit F5 oder STRG+F5 ausführen.
1. Wählen Sie in der Dropdownliste „Bereich“ die Option **\<Neuen Bereich erstellen\>** aus.
1. Stellen Sie sicher, dass der übergeordnete Bereich auf **\<keiner\>** festgelegt ist, und geben Sie den Bereichsnamen **dev** ein. Klicken Sie auf OK.
1. Drücken Sie STRG+F5, um _mywebapi_ ohne den angefügten Debugger auszuführen.
1. Wechseln Sie zum Visual Studio-Fenster mit dem Projekt _webfrontend_, und drücken Sie STRG+F5, um den Dienst ebenfalls auszuführen.

> [!Note]
> Manchmal muss der Browser aktualisiert werden, wenn die Webseite nach dem Drücken von STRG+F5 zum ersten Mal angezeigt wird.

> [!TIP]
> Die oben aufgeführten Schritte dienen zum manuellen Einrichten einer Baseline. Teams sollten jedoch CI/CD verwenden, um Ihre Baseline mit dem committeten Code auf dem neuesten Stand zu halten.
>
> In unserer [Anleitung zum Einrichten von CI/CD mit Azure DevOps](how-to/setup-cicd.md) erfahren Sie, wie Sie einen Workflow erstellen, der in etwa der Darstellung im folgenden Diagramm entspricht:
>
> ![CI/CD-Beispieldiagramm](media/common/ci-cd-complex.png)

Jeder, der die öffentliche URL öffnet und zur Web-App navigiert, ruft den von Ihnen geschriebenen Codepfad auf, der unter Verwendung des Standardbereichs _dev_ beide Dienste durchläuft. Nehmen wir nun an, Sie möchten *mywebapi* weiterentwickeln: Wie können Sie vorgehen, ohne andere Entwickler zu beeinträchtigen, die den Entwicklungsbereich nutzen? Die Lösung: Sie richten Ihren eigenen Bereich ein.

### <a name="create-a-new-dev-space"></a>Erstellen eines neuen Entwicklungsbereichs
In Visual Studio können Sie zusätzliche Bereiche erstellen, die verwendet werden, wenn Sie für Ihren Dienst F5 oder STRG+F5 drücken. Sie können einem Bereich einen beliebigen Namen mit einer beliebigen Bedeutung geben (etwa _sprint4_ oder _demo_).

Gehen Sie wie folgt vor, um einen neuen Bereich zu erstellen:
1. Wechseln Sie zum Visual Studio-Fenster mit dem Projekt *mywebapi*.
2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.
3. Klicken Sie links auf die Registerkarte **Debuggen**, um die Azure Dev Spaces-Einstellungen anzuzeigen.
4. Hier können Sie den Cluster/Bereich ändern oder erstellen, der beim Drücken von F5 oder STRG+F5 verwendet wird. *Stellen Sie sicher, dass die zuvor erstellte Azure Dev Space-Instanz ausgewählt ist.*
5. Wählen Sie in der Dropdownliste „Bereich“ die Option **\<Neuen Bereich erstellen\>** aus.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Legen Sie im Dialogfeld **Bereich hinzufügen** den übergeordneten Bereich auf **dev** fest, und geben Sie einen Namen für den neuen Bereich ein. Sie können Ihren Namen (z.B. „scott“) für den neuen Bereich verwenden, damit Ihre Kollegen erkennen können, in welchem Bereich Sie arbeiten. Klicken Sie auf **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Auf der Seite mit den Projekteigenschaften sollten nun Ihr AKS-Cluster und der neue Bereich ausgewählt sein.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Aktualisieren von Code für *mywebapi*

1. Ändern Sie im Projekt *mywebapi* in der Datei `Controllers/ValuesController.cs` den Code der `string Get(int id)`-Methode wie folgt:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Legen Sie einen Breakpoint in diesem aktualisierten Codeblock fest. (Unter Umständen wurde zuvor bereits ein Breakpoint festgelegt.)
3. Drücken Sie F5, um den Dienst _mywebapi_ zu starten. Dadurch wird der Dienst in Ihrem Cluster im ausgewählten Bereich gestartet. Der ausgewählte Bereich ist in diesem Fall _scott_.

Hier sehen Sie ein Diagramm, in dem die Funktionsweise der verschiedenen Bereiche erläutert wird. Der violette Pfad zeigt eine Anforderung über den Bereich _dev_. Dabei handelt es sich um den Standardpfad, der verwendet wird, wenn der URL kein Bereich vorangestellt wird. Der rosafarbene Pfad zeigt eine Anforderung über den Bereich _dev/scott_.

![](media/common/Space-Routing.png)

Mit dieser integrierten Funktion von Azure Dev Spaces können Sie End-to-End-Tests für Code in einer gemeinsamen Umgebung ausführen, ohne dass die einzelnen Entwickler den kompletten Dienststapel in ihrem Bereich neu erstellen müssen. Für dieses Routing müssen Weitergabeheader in Ihrem App-Code weitergeleitet werden, wie im vorherigen Schritt in diesem Leitfaden erläutert.

### <a name="test-code-running-in-the-devscott-space"></a>Testen von Code, der im Bereich _dev/scott_ ausgeführt wird
Wenn Sie Ihre neue Version von *mywebapi* in Verbindung mit *webfrontend* testen möchten, öffnen Sie in Ihrem Browser die URL des öffentlichen Zugriffspunkts für *webfrontend* (beispielsweise http://dev.webfrontend.123456abcdef.eus.azds.io), und navigieren Sie zur Seite „Info“. Die ursprüngliche Nachricht „Hello from webfrontend and Hello from mywebapi“ sollte angezeigt werden.

Fügen Sie nun den Teil „scott.s.“ zur URL hinzu, sodass sie ungefähr wie folgt lautet: http://scott.s.dev.webfrontend.123456abcdef.eus.azds.io. Aktualisieren Sie anschließend den Browser. Der im Projekt *mywebapi* festgelegte Breakpoint sollte erreicht werden. Klicken Sie auf F5, um den Vorgang fortzusetzen. Im Browser sollte jetzt die neue Nachricht „Hello from webfrontend and mywebapi now says something new.“ angezeigt werden. Der Grund hierfür ist, dass der Pfad zum aktualisierten Code in *mywebapi* im Bereich _dev/scott_ ausgeführt wird.

Sobald Sie einen Bereich _dev_ haben, der immer die aktuellen Änderungen enthält, und vorausgesetzt, Ihre Anwendung ist zur Nutzung des in diesem Abschnitt des Tutorials beschriebenen bereichsbasierten Routings von Dev Spaces konzipiert, sollte es leicht nachvollziehbar sein, von welch großem Nutzen Dev Spaces beim Testen neuer Features im Kontext der größeren Anwendung sein kann. Anstatt _alle_ Dienste in Ihrem privaten Bereich bereitstellen zu müssen, können Sie einen von _dev_ abgeleiteten privaten Bereich erstellen und darin nur die Dienste bereitstellen, an denen Sie tatsächlich arbeiten. Die Routinginfrastruktur von Dev Spaces übernimmt dann den Rest: Sie nutzt alle Dienste, die Sie in Ihrem privaten Bereich findet, und verwendet standardmäßig wieder die aktuelle Version im Bereich _dev_. Und besser noch: _Mehrere_ Entwickler können zur gleichen Zeit aktiv unterschiedliche Dienste in ihrem eigenen Bereich entwickeln, ohne einander zu stören.

### <a name="well-done"></a>Gut gemacht!
Sie haben den Leitfaden zu den ersten Schritten abgeschlossen. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Einrichten von Azure Dev Spaces mit einem verwalteten Kubernetes-Cluster in Azure
> * Iteratives Entwickeln von Code in Containern
> * Unabhängiges Entwickeln von zwei separaten Diensten und Verwenden der DNS-Dienstermittlung von Kubernetes, um einen anderen Dienst aufzurufen
> * Produktives Entwickeln und Testen Ihres Codes in einer Teamumgebung
> * Einrichten einer Baseline der Funktionen mithilfe von Dev Spaces, um isolierte Änderungen im Kontext einer größeren Microserviceanwendung mühelos zu testen

Nachdem Sie sich mit Azure Dev Spaces vertraut gemacht haben, können Sie [Ihren Entwicklungsbereich für Teammitglieder freigeben](how-to/share-dev-spaces.md) und ihnen zeigen, wie dies die Zusammenarbeit vereinfacht.

## <a name="clean-up"></a>Bereinigen
Um eine Azure Dev Spaces-Instanz in einem Cluster vollständig zu löschen, einschließlich aller Entwicklungsbereiche und der darin ausgeführten Dienste, können Sie den Befehl `az aks remove-dev-spaces` verwenden. Beachten Sie, dass diese Aktion nicht rückgängig gemacht werden kann. Sie können dem Cluster die Unterstützung für Azure Dev Spaces wieder hinzufügen, aber dies entspricht einem kompletten Neubeginn. Ihre alten Dienste und Bereiche werden nicht wiederhergestellt.

Im folgenden Beispiel werden die Azure Dev Spaces-Controller Ihres aktiven Abonnements aufgelistet, und anschließend wird der Azure Dev Spaces-Controller gelöscht, der dem AKS-Cluster „myaks“ in der Ressourcengruppe „myaks-rg“ zugeordnet ist.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
