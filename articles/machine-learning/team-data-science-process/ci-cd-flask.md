---
title: 'Erstellen einer CI/CD-Pipeline mit Azure Pipelines: Team Data Science-Prozess'
description: Erstellen einer Continuous Integration- und Continuous Delivery-Pipeline für Anwendungen der Künstlichen Intelligenz (KI) mit Docker und Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: af7900faff18d526686b80f23b9de3b3bce5ec5c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309614"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Erstellen von CI/CD-Pipelines für KI-Apps mit Azure Pipelines, Docker und Kubernetes

Eine Anwendung für Künstliche Intelligenz (KI) bedeutet, dass Anwendungscode in ein vortrainiertes Machine Learning-Modell (ML) eingebettet wird. Es gibt immer zwei Aufgabenschwerpunkte für eine KI-Anwendung: Data Scientists erstellen das ML-Modell, und App-Entwickler erstellen die App und stellen Sie für die Nutzung durch Endbenutzer bereit. In diesem Artikel wird beschrieben, wie Sie eine CI/CD-Pipeline (Continuous Integration und Continuous Delivery) für eine KI-Anwendung implementieren, die das ML-Modell in den Quellcode der APP einbettet. Im Beispielcode und Tutorial wird eine Python Flask-Webanwendung verwendet, und es wird ein vortrainiertes Modell aus einem privaten Azure Blob Storage-Konto abgerufen. Sie können auch ein AWS S3-Speicherkonto verwenden.

> [!NOTE]
> Der folgende Prozess ist eine von mehreren Möglichkeiten für CI/CD. Es gibt Alternativen zu diesen Tools und den Voraussetzungen.

## <a name="source-code-tutorial-and-prerequisites"></a>Quellcode, Tutorial und Voraussetzungen

Sie können [Quellcode](https://github.com/Azure/DevOps-For-AI-Apps) und ein [detailliertes Tutorial](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) von GitHub herunterladen. Befolgen Sie die Schritte im Tutorial zum Implementieren einer CI/CD-Pipeline für Ihre eigene Anwendung.

Für die Verwendung des heruntergeladenen Quellcodes und dieses Tutorial müssen die folgenden Voraussetzungen erfüllt sein: 

- Das [Quellcoderepository](https://github.com/Azure/DevOps-For-AI-Apps) muss zu Ihrem GitHub-Konto geforkt werden
- Eine [Azure DevOps-Organisation](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
- Ein [Azure Container Service-Cluster (AKS) für Kubernetes](/previous-versions/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) zum Ausführen von Befehlen und Abrufen der Konfiguration aus dem AKS-Cluster 
- Ein [Azure Container Registry-Konto (ACR)](../../container-registry/container-registry-get-started-portal.md)

## <a name="cicd-pipeline-summary"></a>Zusammenfassung der CI/CD-Pipeline

Jeder neue Git-Commit startet die Buildpipeline. Der Build pullt das neueste ML-Modell sicher aus einem Blobspeicherkonto und verpackt es mit dem App-Code in einem einzelnen Container. Diese Entkopplung der Anwendungsentwicklung und der Data Science-Arbeitsströme stellt sicher, dass die Produktions-App immer den neuesten Code mit dem aktuellsten ML-Modell ausführt. Wenn die App Tests besteht, speichert die Pipeline das Buildimage sicher in einem Docker-Container in ACR. Die Releasepipeline stellt den Container dann mithilfe von AKS bereit. 

## <a name="cicd-pipeline-steps"></a>Schritte der CI/CD-Pipeline

In der folgenden Abbildung und in den Schritten wird die Architektur der CI/CD-Pipeline beschrieben:

![Architektur der CI/CD-Pipeline](./media/ci-cd-flask/architecture.png)

1. Entwickler arbeiten in der IDE ihrer Wahl am Anwendungscode.
2. Die Entwickler committen den Code an Azure Repos, GitHub oder einen anderen Git-Quellcodeverwaltungsanbieter. 
3. Data Scientists arbeiten separat davon an der Entwicklung ihres ML-Modells.
4. Die Data Scientists veröffentlichen das fertige Modell in einem Modellrepository (in diesem Fall ein Blobspeicherkonto). 
5. Azure Pipelines startet einen Build basierend auf dem Git-Commit.
6. Die Buildpipeline pullt das neueste ML-Modell aus dem Blobspeicher und erstellt einen Container.
7. Die Pipeline pusht das Buildimage in das private Imagerepository in ACR.
8. Die Releasepipeline wird basierend auf dem erfolgreichen Build gestartet.
9. Die Pipeline pullt das neueste Image aus ACR und stellt es über den Kubernetes-Cluster in AKS bereit.
10. Benutzeranforderungen für die App durchlaufen den DNS-Server.
11. Der DNS-Server übergibt die Anforderung an einen Load Balancer und sendet Antworten zurück an den Server.

## <a name="see-also"></a>Weitere Informationen

- [Team Data Science-Prozess (TDSP)](./index.yml)
- [Azure Machine Learning (AML)](../index.yml)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)