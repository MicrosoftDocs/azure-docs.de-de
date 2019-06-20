---
title: Verwenden von kubectl mit Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Schnelle Kubernetes-Entwicklung mit Containern und Microservices in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s '
ms.openlocfilehash: 0dfe88966deeb4dcf0196aa1f1584a06794b36a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60686323"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Verwenden von kubectl mit einem Azure Dev Space

Sie können auf den Kubernetes-Cluster in einem Azure Dev Space zugreifen und vorhandene Kubernetes-Tools wie `kubectl` verwenden.

Beim Ausführen des Befehls `az aks use-dev-spaces` wird automatisch ein `kubectl`-Konfigurationskontext hinzugefügt, sodass kubectl bereits mit Ihrem Azure Dev Spaces-Kubernetes-Cluster verbunden sein sollte. Beispiele:
- Bestätigen des aktuellen Kontexts: `kubectl config current-context`
- Auflisten aller verfügbaren Kontexte: `kubectl config get-contexts` 
- Ändern des Kontexts: `kubectl config use-context <context-name>`
- Anzeigen des Kubernetes-Dashboards: Führen Sie `kubectl proxy` aus, und öffnen Sie dann in Ihrem Browser die Adresse, die dieser Befehl ausgibt (fügen Sie `/ui` an die URL an, um zum Kubernetes-Dashboard zu navigieren).
- Auflisten der ausgeführten Dienste im Azure Dev Spaces-Standardbereich namens *default*: `kubectl get services --namespace=default`

