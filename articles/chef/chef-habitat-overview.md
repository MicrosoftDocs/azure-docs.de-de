---
title: Verwenden von Habitat zum Bereitstellen Ihrer Anwendung in Azure
description: Erfahren Sie, wie Sie Ihre Anwendung auf virtuellen Azure-Computern und in Containern einheitlich bereitstellen.
keywords: Azure, Chef, Devops, virtuelle Computer, Übersicht, automatisieren, Habitat
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 2bdcd4c504822a2e60156b0ac565465e0cf23a85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60388810"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Verwenden von Habitat zum Bereitstellen Ihrer Anwendung in Azure
[Habitat](https://www.habitat.sh/) ist ein Anwendungspaket und Laufzeitsystem, die Anwendung mit ihrer Automatisierung als Einheit der Bereitstellung gemeinsam bündelt. Dies ermöglicht ultimative Portabilität für die Anwendung, Container, was wiederum deren Bereitstellung in Containern, virtuellen Computern, auf Bare-Metal oder in PaaS gestattet, ohne dass alles neu geschrieben oder gepackt werden müsste.

Dieser Artikel beschreibt die Hauptvorteile der Verwendung von Habitat.

## <a name="modernize-and-move-legacy-applications"></a>Modernisieren und Verschieben von Legacyanwendungen
Befreien Sie Legacyanwendungen von älteren Betriebssystemen und Middleware, indem Sie sie mit Habitat neu packen. Das resultierende Artefakt ist portierbar und lässt sich einfach auf einer Plattform in einer neueren Infrastruktur bereitstellen, z. B. virtuellen Computern oder Containern, die in der Cloud ausgeführt werden.

## <a name="accelerate-container-adoption"></a>Beschleunigen der Containereinführung
Habitat löst die kontinuierliche Bereitstellung komplexer, Microservice-orientierter Anwendungen durch die genaue Darstellung von Laufzeitabhängigkeiten. Gehen Sie über einfache Blau/Grün-Bereitstellung einzelner Komponenten hinaus, und entwerfen Sie die Architektur für ein anspruchsvolles Bereitstellungsverhalten, ohne komplexe Orchestrierungsflows generieren zu müssen.

## <a name="run-any-application-anywhere"></a>Ausführen einer beliebigen Anwendung – und zwar überall
Mit Habitat können Anwendungen unverändert in allen Laufzeitumgebungen ausgeführt werden. Dies umfasst alles: von Bare-Metal und virtuellen Computern bis hin zu Containern (z. B. Docker), Clusterverwaltungssystemen (z. B. Mesosphere oder Kubernetes) und PaaS-Systemen (z. B. Pivot Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integration in den Chef DevOps-Workflow
Das Habitat-Projekt ist eins der Open Source-Projekte von Chef Software, das große Unterstützung von der Community erhält. Habitat nutzt die weitreichenden Erfahrungen von Chef mit Infrastrukturautomatisierung, um beispiellose Automatisierungsmöglichkeiten für Anwendungen zu schaffen. Chef bietet kommerzielle Unterstützung für Habitat und erzeugt eine nahtlose Integration zwischen Habitat und Chef Automate, um den Anwendungsfreigabezyklus von der Entwicklung bis zur Bereitstellung vollständig zu automatisieren.

## <a name="next-steps"></a>Nächste Schritte

* [Habitat testen](https://www.habitat.sh/learn/)
