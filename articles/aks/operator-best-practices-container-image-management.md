---
title: Best Practices für Entwickler – Containerimageverwaltung in Azure Kubernetes Service (AKS)
description: Lernen Sie die bewährten Methoden für den Clusteroperator zum Verwalten und Absichern von Containerimages in Azure Kubernetes Service (AKS) kennen.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: ea39bceaa6b58e84def9635436d902002e33cd14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514513"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Best Practices für Containerimageverwaltung und Sicherheit in Azure Kubernetes Service (AKS)

Beim Entwickeln und Ausführen von Anwendungen in Azure Kubernetes Service (AKS) ist die Sicherheit Ihrer Container und Containerimages ein wichtiger Aspekt. Container, die veraltete Basisimages oder nicht gepatchte Anwendungsruntimes enthalten, bergen ein Sicherheitsrisiko und einen möglichen Angriffsvektor. Um diese Risiken zu minimieren, sollten Sie Tools integrieren, die Probleme in Ihren Containern zur Buildzeit und auch zur Laufzeit suchen und beheben. Je früher im Prozess die Schwachstelle oder das veraltete Basisimage erkannt wird, desto sicherer ist der Cluster. In diesem Artikel bezeichnet *Container* sowohl die in einer Containerregistrierung gespeicherten Containerimages als auch die ausgeführten Container.

In diesem Artikel wird erläutert, wie Container in AKS gesichert werden. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Überprüfen auf und Beheben von Imageschwachstellen
> * Automatisches Triggern und erneutes Bereitstellen von Containerimages, wenn ein Basisimage aktualisiert wird

Weitere Informationen finden Sie in den Best Practices für [Clustersicherheit][best-practices-cluster-security] und [Podsicherheit][best-practices-pod-security].

## <a name="secure-the-images-and-run-time"></a>Sichern von Images und Runtime

**Best Practices-Anleitung**: Überprüfen Sie Ihre Containerimages auf Schwachstellen, und stellen Sie nur Images bereit, die die Validierung bestanden haben. Aktualisieren Sie regelmäßig die Basisimages und die Anwendungsruntime, und stellen Sie die Workloads dann im AKS-Cluster erneut bereit.

Ein Problem bei der Einführung von containerbasierten Workloads ist die Überprüfung der Sicherheit von Images und Runtime, die zum Erstellen eigener Anwendungen verwendet werden. Wie stellen Sie sicher, dass Sie keine Sicherheitsschwachstellen in Ihre Bereitstellungen einführen? Ihr Bereitstellungsworkflow sollte einen Prozess zum Überprüfen von Containerimages mit Tools wie [Twistlock][twistlock] oder [Aqua][aqua] beinhalten und dann nur die Bereitstellung verifizierter Images zulassen.

![Überprüfen von Containerimages, Beheben von Schwachstellen, Validieren und Bereitstellen](media/operator-best-practices-container-security/scan-container-images-simplified.png)

In einem realistischen Beispiel können Sie eine CI/CD-Pipeline (Continuous Integration/Continuous Deployment) verwenden, um Imagescans, Verifizierung und Bereitstellung zu automatisieren. Die Azure Container Registry enthält diese Funktionen zur Überprüfung auf Schwachstellen.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatisches Erstellen neuer Images bei der Aktualisierung des Basisimages

**Best Practices-Anleitung** – Verwenden Sie bei der Nutzung von Basisimages für Anwendungsimages die Automatisierung, um neue Images zu erstellen, wenn das Basisimage aktualisiert wird. Da diese Basisimages typischerweise Sicherheitskorrekturen enthalten, aktualisieren Sie alle Downstreamcontainerimages der Anwendung.

Jedes Mal, wenn ein Basisimage aktualisiert wird, sollten auch alle nachfolgenden Containerimages aktualisiert werden. Dieser Buildprozess sollte in Validierungs- und Bereitstellungspipelines wie [Azure Pipelines][azure-pipelines] oder Jenkins integriert werden. Diese Pipelines stellen sicher, dass Ihre Anwendungen auf den aktualisierten Basisimages weiterhin ausgeführt werden. Sobald Ihre Anwendungscontainerimages validiert wurden, können die AKS-Bereitstellungen aktualisiert werden, um die neuesten, sicheren Images auszuführen.

Azure Container Registry Tasks können Containerimages auch automatisch aktualisieren, wenn das Basisimage aktualisiert wird. Dieses Feature ermöglicht es Ihnen, eine kleine Anzahl von Basisimages zu erstellen und diese regelmäßig mit Fehlerkorrekturen und Sicherheitsfixes zu aktualisieren.

Weitere Informationen zu Basisimageaktualisierungen finden Sie unter [Automatisieren von Buildvorgängen für Images nach der Aktualisierung des Basisimages mit Azure Container Registry Tasks][acr-base-image-update].

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde erläutert, wie Container gesichert werden. Wenn Sie einige dieser Best Practices implementieren möchten, lesen Sie folgende Artikel:

* [Automatisieren von Buildvorgängen für Images nach der Aktualisierung des Basisimages mit Azure Container Registry Tasks][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
