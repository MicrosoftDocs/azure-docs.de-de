---
title: Includedatei
description: Includedatei
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 40c1be20df845b975c023616e38cbb932c985735
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157852"
---
# <a name="team-development-with-azure-dev-spaces"></a>Teamentwicklung mit Azure Dev Spaces

In diesem Tutorial erfahren Sie, wie ein Team von Entwicklern über Dev Spaces gleichzeitig im selben Kubernetes-Cluster zusammenarbeiten kann.

## <a name="learn-about-team-development"></a>Weitere Informationen zur Teamentwicklung
Bisher haben Sie den Code Ihrer Anwendung so ausgeführt, als wären Sie der einzige Entwickler, der an der App arbeitet. In diesem Abschnitt erfahren Sie, wie Azure Dev Spaces die Teamentwicklung optimiert:
* Ein Team von Entwicklern kann in der gleichen Umgebung arbeiten und je nach Bedarf einen gemeinsamen oder einen getrennten Entwicklungsbereich nutzen.
* Jeder Entwickler kann seinen Code isoliert und ohne Beeinträchtigung der anderen Entwickler durchlaufen.
* Testen Sie Ihren Code vollständig, bevor Sie ihn committen, ohne Modelle erstellen oder Abhängigkeiten simulieren zu müssen.

### <a name="challenges-with-developing-microservices"></a>Herausforderungen bei der Entwicklung von Microservices
Ihre Beispielanwendung ist im Moment nicht sehr komplex. Bei der realen Entwicklung stoßen Sie jedoch bald auf Herausforderungen, wenn Sie mehr Dienste hinzufügen und das Entwicklungsteam wächst. Die lokale Ausführung aller Komponenten für die Entwicklung kann daher unrealistisch werden.

* Ihr Entwicklungscomputer verfügt möglicherweise nicht über genügend Ressourcen, um alle benötigten Dienste parallel auszuführen.
* Einige Dienste müssen ggf. öffentlich erreichbar sein. So kann es beispielsweise sein, dass ein Dienst einen Endpunkt benötigt, der auf einen Webhook reagiert.
* Wenn Sie eine Teilmenge der Dienste ausführen möchten, müssen Sie mit der gesamten Abhängigkeitshierarchie zwischen allen Ihren Diensten vertraut sein. Das ist unter Umständen nicht ganz einfach – insbesondere bei zunehmender Anzahl von Diensten.
* Einige Entwickler greifen auf die Simulation zahlreicher Dienstabhängigkeiten zurück. Dies ist zwar hilfreich, die Verwaltung der Simulationen kann jedoch schnell die Entwicklungskosten in die Höhe treiben. Darüber hinaus führt dieser Ansatz dazu, dass sich Ihre Entwicklungsumgebung erheblich von der Produktionsumgebung unterscheidet, wodurch sich kleinere Fehler einschleichen können.
* Integrationstests können daher schwierig werden. Integrationstests sind nur nach einem Commit realistisch. Das bedeutet, dass Ihnen Probleme erst später im Entwicklungszyklus auffallen.

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Arbeiten in einem gemeinsamen Entwicklungsbereich
Mit Azure Dev Spaces können Sie einen *gemeinsamen* Entwicklungsbereich in Azure einrichten. Jeder Entwickler kann sich auf seinen Teil der Anwendung konzentrieren und *Code vor dem Commit* iterativ in einem Entwicklungsbereich entwickeln, der bereits alle anderen Dienste und Cloudressourcen enthält, die der Entwickler für seine Szenarien benötigt. Abhängigkeiten sind immer aktuell, und Entwickler arbeiten wie in einer Produktionsumgebung.

### <a name="work-in-your-own-space"></a>Arbeiten im eigenen Bereich
Während der Entwicklung von Code für Ihren Dienst und vor dem Einchecken ist Code häufig nicht fehlerfrei. Sie strukturieren und testen den Code weiterhin iterativ und experimentieren mit Lösungen. In einem **Bereich** – einem Konzept von in Azure Dev Spaces – können Sie isoliert arbeiten, ohne Ihre Teammitglieder zu beeinträchtigen.

## <a name="use-dev-spaces-for-team-development"></a>Verwenden von Dev Spaces für die Teamentwicklung
Betrachten wir diese Konzepte konkret am Beispiel unserer Anwendung *webfrontend* -> *mywebapi*. In unserem Beispielszenario muss der Entwickler Scott eine Änderung an dem Dienst *mywebapi* vornehmen (und zwar *nur* an diesem Dienst). *webfrontend* muss für Scotts Update nicht geändert werden.

_Ohne_ Dev Spaces stehen Scott verschiedene Möglichkeiten zur Verfügung, um sein Update zu entwickeln und zu testen. Diese sind jedoch alle nicht optimal:
* Er kann ALLE Komponenten lokal ausführen. Dazu benötigt er einen leistungsfähigeren Entwicklungscomputer mit Docker-Installation und ggf. MiniKube.
* Er kann ALLE Komponenten in einem isolierten Namespace im Kubernetes-Cluster ausführen. Da *webfrontend* unverändert bleibt, wäre das eine Verschwendung von Clusterressourcen.
* Er kann NUR *mywebapi* ausführen und zu Testzwecken manuelle REST-Aufrufe verwenden. Dadurch wird nicht der gesamte Ablauf getestet.
* Er kann *webfrontend* entwicklungsspezifischen Code hinzufügen, der es dem Entwickler ermöglicht, Anforderungen an eine andere Instanz von *mywebapi* zu senden. Das verkompliziert allerdings den Dienst *webfrontend*.

### <a name="set-up-your-baseline"></a>Einrichten Ihrer Baseline
Als Erstes müssen wir eine Baseline unserer Dienste bereitstellen. Diese Bereitstellung stellt den letzten als funktionierend bekannten Zustand dar, sodass Sie das Verhalten Ihres lokalen Codes problemlos mit der eingecheckten Version vergleichen können. Als Nächstes erstellen wir auf der Grundlage dieser Baseline einen untergeordneten Bereich, um unsere Änderungen an *mywebapi* im Kontext der übergeordneten Anwendung testen zu können.

1. Klonen Sie die [Dev Spaces-Beispielanwendung](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Checken Sie den Remotebranch *azds_updates* aus: `git checkout -b azds_updates origin/azds_updates`
1. Wählen Sie den Bereich _dev_ aus: `azds space select --name dev`. Wenn Sie zum Auswählen eines übergeordneten Entwicklungsbereichs aufgefordert werden, wählen Sie _\<Kein\>_ aus.
1. Navigieren Sie zum Verzeichnis _mywebapi_, und führen Sie Folgendes aus: `azds up -d`
1. Navigieren Sie zum Verzeichnis _webfrontend_, und führen Sie Folgendes aus: `azds up -d`
1. Führen Sie `azds list-uris` aus, um den öffentlichen Endpunkt für _webfrontend_ anzuzeigen.

> [!TIP]
> Die oben aufgeführten Schritte dienen zum manuellen Einrichten einer Baseline. Teams sollten jedoch CI/CD verwenden, um Ihre Baseline mit committetem Code auf dem neuesten Stand zu halten.
>
> In unserer [Anleitung zum Einrichten von CI/CD mit Azure DevOps](../articles/dev-spaces/how-to/setup-cicd.md) erfahren Sie, wie Sie einen Workflow erstellen, der in etwa der Darstellung im folgenden Diagramm entspricht:
>
> ![CI/CD-Beispieldiagramm](../articles/dev-spaces/media/common/ci-cd-complex.png)

Ihre Baseline sollte nun ausgeführt werden. Führen Sie den Befehl `azds list-up --all` aus, um eine Ausgabe wie die folgende zu erhalten:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

Die Spalte „DevSpace“ zeigt, dass beide Dienste in einem Bereich namens _dev_ ausgeführt werden. Jeder Benutzer, der die öffentliche URL öffnet und zur Web-App navigiert, ruft den eingecheckten Codepfad auf, der beide Dienste durchläuft. Nehmen wir nun an, Sie möchten mit der Entwicklung von _mywebapi_ fortfahren. Wie nehmen Sie Änderungen am Code vor und testen diese, ohne andere Entwickler zu unterbrechen, die die Entwicklungsumgebung ebenfalls verwenden? Die Lösung: Sie richten Ihren eigenen Bereich ein.

### <a name="create-a-dev-space"></a>Erstellen eines Entwicklungsbereichs (Dev Space)
Wenn Sie Ihre eigene Version von _mywebapi_ in einem anderen Bereich als _dev_ ausführen möchten, müssen Sie mithilfe des folgenden Befehls einen eigenen Bereich erstellen:

```cmd
azds space select --name scott
```

Wählen Sie bei entsprechender Aufforderung _dev_ als **übergeordneten Entwicklungsbereich** aus. Dadurch wird unser neuer Bereich (_dev/scott_) vom Bereich _dev_ abgeleitet. Wir werden gleich sehen, wie uns dies beim Testen behilflich ist.

Für den neuen Bereich haben wir aufgrund unseres eingangs angenommenen Beispielszenarios den Namen _scott_ verwendet, damit die Kollegen wissen, wer damit arbeitet. Der Name kann jedoch frei gewählt werden und natürlich auch andere Informationen vermitteln. Beispiele wären etwa _sprint4_ und _demo_. _dev_ fungiert in jedem Fall als Baseline für alle Entwickler, die an einem Teil dieser Anwendung arbeiten:

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

Führen Sie den Befehl `azds space list` aus, um eine Liste aller Bereiche in der Entwicklungsumgebung anzuzeigen. Die Spalte _Selected_ (Ausgewählt) gibt mit „true“ oder „false“ an, welcher Bereich momentan ausgewählt ist. In unserem Fall wurde der Bereich _dev/scott_ automatisch ausgewählt, als er erstellt wurde. Sie können jederzeit mit dem Befehl `azds space select` einen anderen Bereich auswählen.

Sehen Sie sich die Anwendung in Aktion an.