---
title: Best Practices für Azure Kubernetes Service (AKS)
description: Sammlung der bewährten Methoden für Clusterbetreiber und Entwickler zum Erstellen und Verwalten von Anwendungen in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: e8f9c1946567ac3c3ea1045a216d6b0486fd959a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686258"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Best Practices für Clusterbetreiber und -entwickler zum Erstellen und Verwalten von Anwendungen in Azure Kubernetes Service (AKS)

Um Anwendungen im Azure Kubernetes Service (AKS) erfolgreich zu entwickeln und auszuführen, müssen Sie einige wichtige Aspekte verstehen und implementieren. Hierzu gehören Mehrinstanzenfähigkeit und Scheduler-Features, Cluster- und Podsicherheit oder Business Continuity & Disaster Recovery. Die folgenden bewährten Methoden wurden in Gruppen zusammengefasst, um Clusterbetreibern und Entwicklern zu helfen, die Überlegungen die verschiedenen Bereiche zu verstehen und die entsprechenden Features zu implementieren.

Diese Artikel zu bewährten Methoden und Konzepten wurden in Zusammenarbeit mit der AKS-Produktgruppe, den technischen Teams und den Außendienstteams, einschließlich der Global Black Belts (GBBs), verfasst.

## <a name="cluster-operator-best-practices"></a>Best Practices für Clusterbetreiber

Arbeiten Sie als Clusterbetreiber mit Anwendungsbesitzern und -entwicklern zusammen, um deren Anforderungen zu verstehen. Sie können Ihre AKS-Cluster dann unter Anwendung der folgenden bewährten Methoden entsprechend konfigurieren.

**Mehrinstanzenfähigkeit**

* [Best Practices für die Clusterisolierung](operator-best-practices-cluster-isolation.md)
    * Enthält Kernkomponenten für Mehrinstanzenfähigkeit und logische Isolation mit Namespaces.
* [Best Practices für grundlegende Scheduler-Features](operator-best-practices-scheduler.md)
    * Umfasst die Verwendung von Ressourcenkontingenten und Podunterbrechungsbudgets.
* [Best Practices für erweiterte Scheduler-Features](operator-best-practices-advanced-scheduler.md)
    * Umfasst die Verwendung von Markierungen und Toleranzen, Knotenselektoren und -affinität sowie Inter-Pod-Affinität und Anti-Affinität.
* [Best Practices für Authentifizierung und Autorisierung](operator-best-practices-identity.md)
    * Umfasst die Azure Active Directory-Integration, die Verwendung der rollenbasierten Zugriffssteuerung für Kubernetes (Kubernetes Role-Based Access Control. Kubernetes RBAC), die Verwendung von Azure RBAC sowie Podidentitäten.

**Security**

* [Best Practices für Clustersicherheit und -upgrades](operator-best-practices-cluster-security.md)
    * Umfasst das Absichern des Zugriffs auf API-Server, das Beschränken des Containerzugriffs und das Verwalten von Upgrades und Knotenneustarts.
* [Best Practices für die Verwaltung von Containerimages und Sicherheit](operator-best-practices-container-image-management.md)
    * Umfasst das Sichern von Image und Runtimes sowie automatisierte Builds auf Basisimageupdates.
* [Best Practices für Podsicherheit](developer-best-practices-pod-security.md)
    * Umfasst das Absichern des Zugriffs auf Ressourcen, das Beschränken der Offenlegung von Anmeldeinformationen sowie das Verwenden von Podidentitäten und digitalen Schlüsseltresorinstanzen.

**Netzwerk und Speicher**

* [Best Practices für die Netzwerkkonnektivität](operator-best-practices-network.md)
    * Umfasst verschiedene Netzwerkmodelle, das Verwenden von eingehenden und Web Application Firewalls (WAF) und das Absichern des SSH-Knotenzugriffs.
* [Best Practices für Speicher und Sicherungen](operator-best-practices-storage.md)
    * Umfasst das Auswählen des geeigneten Speichertyps und der geeigneten Knotengröße, das dynamische Bereitstellen von Volumes sowie Datensicherungen.

**Ausführen von Workloads für Unternehmen**

* [Best Practices für Geschäftskontinuität und Notfallwiederherstellung](operator-best-practices-multi-region.md)
    * Umfasst die Verwendung von Regionspaaren, mehrere Cluster mit Azure Traffic Manager und die Georeplikation von Containerimages.

## <a name="developer-best-practices"></a>Bewährte Entwicklermethoden

Als Entwickler oder Anwendungsbesitzer können Sie Ihre Entwicklungsumgebung vereinfachen und Anforderungen an die Anwendungsleistung definieren.

* [Best Practices für Anwendungsentwickler zum Verwalten von Ressourcen](developer-best-practices-resource-management.md)
    * Umfasst das Definieren von Podressourcenanforderungen und-grenzwerten, das Konfigurieren von Entwicklungstools und das Überprüfen auf Anwendungsprobleme.
* [Best Practices für Podsicherheit](developer-best-practices-pod-security.md)
    * Umfasst das Absichern des Zugriffs auf Ressourcen, das Beschränken der Offenlegung von Anmeldeinformationen sowie das Verwenden von Podidentitäten und digitalen Schlüsseltresorinstanzen.

## <a name="kubernetes--aks-concepts"></a>Kubernetes/AKS-Konzepte

Um einige der Features und Komponenten dieser bewährten Methoden besser zu verstehen, finden Sie weitere Informationen in den folgenden konzeptionellen Artikeln für Cluster in Azure Kubernetes Service (AKS):

* [Wichtige Konzepte zu Kubernetes](concepts-clusters-workloads.md)
* [Zugriff und Identität](concepts-identity.md)
* [Sicherheitskonzepte](concepts-security.md)
* [Netzwerkkonzepte](concepts-network.md)
* [Speicheroptionen](concepts-storage.md)
* [Skalierungsoptionen](concepts-scale.md)

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mit AKS beginnen müssen, verwenden Sie eine der Schnellstartanleitungen, um einen Azure Kubernetes Service-Cluster (AKS) mit der [Azure CLI](kubernetes-walkthrough.md) oder über das [Azure-Portal](kubernetes-walkthrough-portal.md) bereitzustellen.
