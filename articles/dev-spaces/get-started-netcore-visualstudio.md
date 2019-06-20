---
title: Erstellen eines Kubernetes-Entwicklungsbereichs in der Cloud mit .NET Core und Visual Studio
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Schnelle Kubernetes-Entwicklung mit Containern und Microservices in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s
ms.openlocfilehash: 9b529780387e1129dd7827e4d9c805d185a459d0
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399254"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core-and-visual-studio"></a>Erste Schritte in Azure Dev Spaces mit .NET Core und Visual Studio

In diesem Leitfaden lernen Sie Folgendes:

- Einrichten von Azure Dev Spaces mit einem verwalteten Kubernetes-Cluster in Azure
- Iteratives Entwickeln von Code in Containern mit Visual Studio
- Unabhängiges Entwickeln von zwei separaten Diensten und Verwenden der DNS-Dienstermittlung von Kubernetes, um einen anderen Dienst aufzurufen
- Produktives Entwickeln und Testen Ihres Codes in einer Teamumgebung

> [!Note]
> **Sollten Sie einmal nicht weiterkommen**, lesen Sie den Abschnitt [Problembehandlung](troubleshooting.md).


## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Erstellen eines Kubernetes-Clusters mit Aktivierung für Azure Dev Spaces

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1. Wählen Sie **Ressource erstellen**, suchen Sie nach **Kubernetes**, und wählen Sie dann **Kubernetes Service** > **Erstellen**.

   Führen Sie die folgenden Schritte unter den Überschriften des Formulars *Erstellen eines Kubernetes-Clusters* aus, und vergewissern Sie sich, dass [die gewählte Region Azure Dev Spaces unterstützt][supported-regions].

   - **PROJEKTDETAILS**: Wählen Sie ein Azure-Abonnement und eine neue oder vorhandene Azure-Ressourcengruppe aus.
   - **CLUSTERDETAILS**: Geben Sie Namen, Region, Version und DNS-Namenspräfix für den AKS-Cluster ein.
   - **SKALIERUNG**: Wählen Sie eine VM-Größe für die AKS-Agent-Knoten und die Anzahl von Knoten aus. Beim Einstieg in Azure Dev Spaces reicht ein Knoten aus, um alle Features zu erkunden. Sie können die Knotenanzahl jeweils auf einfache Weise anpassen, nachdem der Cluster bereitgestellt wurde. Beachten Sie, dass die VM-Größe nicht mehr geändert werden kann, nachdem ein AKS-Cluster erstellt wurde. Nach der Bereitstellung eines AKS-Clusters können Sie aber leicht einen neuen AKS-Cluster mit größeren virtuellen Computern erstellen und Dev Spaces für die erneute Bereitstellung in diesem größeren Cluster nutzen, wenn Sie zentral hochskalieren müssen.

   ![Einstellungen für Kubernetes-Konfiguration](media/common/Kubernetes-Create-Cluster-2.PNG)


   Klicken Sie auf **Weiter: Authentifizierung**, wenn der Vorgang abgeschlossen ist.

1. Wählen Sie die gewünschte Einstellung für die rollenbasierte Zugriffssteuerung (RBAC). Azure Dev Spaces unterstützt Cluster mit aktivierter und deaktivierter RBAC.

    ![RBAC-Einstellung](media/common/k8s-RBAC.PNG)

1. Wählen Sie **Überprüfen + erstellen** und danach **Erstellen**.

## <a name="get-the-visual-studio-tools"></a>Abrufen der Visual Studio-Tools
Installieren Sie die aktuelle Version von [Visual Studio](https://www.visualstudio.com/vs/). Für Visual Studio 2019 unter Windows müssen Sie die Workload für die Azure-Entwicklung installieren. Für Visual Studio 2017 unter Windows müssen Sie die Workload für ASP.NET und für die Webentwicklung sowie die [Visual Studio-Tools für Kubernetes](https://aka.ms/get-azds-visualstudio) installieren.

## <a name="create-a-web-app-running-in-a-container"></a>Erstellen einer Web-App, die in einem Container ausgeführt wird

In diesem Abschnitt erstellen Sie eine ASP.NET Core-Web-App und bereiten sie für die Ausführung in einem Container in Kubernetes vor.

### <a name="create-an-aspnet-web-app"></a>Erstellen einer ASP.NET-Web-App

Erstellen Sie in Visual Studio ein neues Projekt. Derzeit muss das Projekt eine **ASP.NET Core-Webanwendung** sein. Nennen Sie das Projekt **webfrontend**.

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Wählen Sie die Vorlage **Webanwendung (Model-View-Controller)** aus, und legen Sie oben im Dialogfeld in den beiden Dropdownmenüs **.NET Core** und **ASP.NET Core 2.0** fest. Klicken Sie auf **OK**, um das Projekt zu erstellen.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Aktivieren von Dev Spaces für einen AKS-Cluster

Wählen Sie für das soeben erstellte Projekt wie nachfolgend gezeigt im Dropdownmenü mit den Starteinstellungen die Option **Azure Dev Spaces**.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Vergewissern Sie sich im nächsten angezeigten Dialogfeld, dass Sie mit dem entsprechenden Konto angemeldet sind, und wählen Sie dann einen vorhandenen Kubernetes-Cluster aus.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Übernehmen Sie für die Dropdownliste **Space** (Bereich) vorerst den Standardwert `default`. Später erfahren Sie mehr über diese Option. Aktivieren Sie das Kontrollkästchen **Publicly Accessible** (Öffentlich zugänglich), damit auf die Web-App über einen öffentlichen Endpunkt zugegriffen werden kann. Diese Einstellung ist nicht erforderlich, ist aber später in dieser exemplarischen Vorgehensweise hilfreich für die Erläuterung einiger Konzepte. Aber keine Sorge: Sie können Ihre Website in beiden Fällen mit Visual Studio debuggen.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Klicken Sie auf **OK**, um den Cluster auszuwählen oder zu erstellen.

Wenn Sie einen Cluster auswählen, der nicht für die Verwendung mit Azure Dev Spaces vorbereitet wurde, wird eine Meldung mit der Frage angezeigt, ob Sie ihn konfigurieren möchten.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Klicken Sie auf **OK**.

> [!IMPORTANT]
> Bei der Azure Dev Spaces-Konfiguration wird der Namespace `azds` im Cluster entfernt, sofern er vorhanden ist.

 Dazu wird eine Hintergrundaufgabe gestartet. Diese nimmt einige Minuten in Anspruch. Um zu überprüfen, ob der Cluster noch erstellt wird, zeigen Sie mit der Maus unten links auf der Statusleiste auf das Symbol **Hintergrundaufgaben**, wie in der folgenden Abbildung gezeigt:

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> Sie können die Anwendung erst debuggen, wenn Ihr Entwicklungsbereich erstellt wurde.

### <a name="look-at-the-files-added-to-project"></a>Anzeigen der zum Projekt hinzugefügten Dateien
Während Sie auf die Erstellung des Entwicklungsbereichs warten, sehen Sie sich die Dateien an, die Ihrem Projekt hinzugefügt wurden, als Sie sich für die Verwendung eines Entwicklungsbereichs entschieden haben.

Sie sehen zuerst einen Ordner namens `charts`, der hinzugefügt wurde. In diesem Ordner wurde ein [Helm-Diagramm](https://docs.helm.sh) für Ihre Anwendung erstellt. Diese Dateien werden zum Bereitstellen der Anwendung im Entwicklungsbereich verwendet.

Sie sehen, dass die Datei `Dockerfile` hinzugefügt wurde. Diese Datei enthält Informationen, die zum Packen Ihrer Anwendung im Docker-Standardformat erforderlich sind.

Außerdem wurde die Datei `azds.yaml` hinzugefügt. Sie enthält die Konfiguration der Entwicklungszeit, die für den Entwicklungsbereich erforderlich ist.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Debuggen eines Containers in Kubernetes
Wenn der Entwicklungsbereich erstellt wurde, können Sie die Anwendung debuggen. Legen Sie einen Breakpoint im Code fest, etwa in Zeile 20 in der Datei `HomeController.cs`, in der die Variable `Message` festgelegt wird. Drücken Sie **F5**, um das Debuggen zu starten. 

Visual Studio kommuniziert zum Erstellen und Bereitstellen der Anwendung mit dem Entwicklungsbereich und öffnet dann einen Browser mit der ausgeführten Web-App. Es sieht unter Umständen so aus, als würde der Container lokal ausgeführt, tatsächlich wird er jedoch im Entwicklungsbereich in Azure ausgeführt. Die localhost-Adresse wird verwendet, da Azure Dev Spaces einen temporären SSH-Tunnel zu dem in AKS ausgeführten Container erstellt.

Klicken Sie oben auf der Seite auf den Link **Info**, um den Breakpoint auszulösen. Sie besitzen wie bei der lokalen Ausführung des Codes Vollzugriff auf Debuginformationen, etwa Aufrufliste, lokale Variablen, Ausnahmeinformationen usw.

## <a name="iteratively-develop-code"></a>Iteratives Entwickeln von Code

Bei Azure Dev Spaces geht es nicht nur um die Ausführung von Code in Kubernetes: Mit diesem Dienst sollen Codeänderungen in einer Kubernetes-Umgebung in der Cloud schnell und iterativ sichtbar gemacht werden.

### <a name="update-a-content-file"></a>Aktualisieren einer Inhaltsdatei
1. Navigieren Sie zur Datei `./Views/Home/Index.cshtml`, und ändern Sie die HTML. Beispiel: Ändern Sie Zeile 70 (`<h2>Application uses</h2>`) ungefähr wie folgt: `<h2>Hello k8s in Azure!</h2>`
1. Speichern Sie die Datei .
1. Aktualisieren Sie die Anzeige im Browser. Daraufhin sollte auf der Webseite die aktualisierte HTML angezeigt werden.

Was ist passiert? Für Änderungen an Inhaltsdateien (z.B. HTML und CSS) ist keine erneute Kompilierung in einer .NET Core-Web-App erforderlich. Eine aktive F5-Sitzung synchronisiert daher automatisch alle geänderten Inhaltsdateien im ausgeführten Container in AKS, sodass alle Inhaltsänderungen direkt angezeigt werden.

### <a name="update-a-code-file"></a>Aktualisieren einer Codedatei
Die Aktualisierung von Codedateien ist etwas aufwendiger, da eine .NET Core-App aktualisierte Anwendungsbinärdateien neu erstellen und generieren muss.

1. Beenden Sie den Debugger in Visual Studio.
1. Öffnen Sie die Codedatei mit dem Namen `Controllers/HomeController.cs`, und bearbeiten Sie die Nachricht, die auf der Seite „Info“ angezeigt wird: `ViewData["Message"] = "Your application description page.";`.
1. Speichern Sie die Datei .
1. Drücken Sie **F5**, um das Debuggen wieder zu starten. 

Das Neuerstellen und erneute Bereitstellen eines neuen Containerimages bei jeder vorgenommenen Codeänderung kann geraume Zeit in Anspruch nehmen. Daher kompiliert Azure Dev Spaces Code im vorhandenen Container inkrementell, um den Bearbeitungs-/Debugging-Kreislauf zu beschleunigen.

Aktualisieren Sie die Web-App im Browser, und wechseln Sie zur Seite „Info“. Daraufhin sollte Ihre benutzerdefinierte Meldung auf der Benutzeroberfläche angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zur Entwicklung mit mehreren Diensten](multi-service-netcore-visualstudio.md)


[supported-regions]: about.md#supported-regions-and-configurations