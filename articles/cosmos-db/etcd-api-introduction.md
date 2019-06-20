---
title: Einführung in die etcd-API von Azure Cosmos DB
description: Dieser Artikel enthält grundlegende Informationen zur etcd-API in Azure Cosmos DB und eine Übersicht über deren Hauptvorteile
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205802"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Einführung in die etcd-API von Azure Cosmos DB (Vorschauversion)

Azure Cosmos DB ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen für unternehmenskritische Anwendungen. Der Dienst bietet sofort einsetzbare globale Verteilung, flexible Skalierung von Durchsatz und Speicher, Wartezeiten im einstelligen Millisekundenbereich beim 99. Perzentil sowie garantierte Hochverfügbarkeit, gestützt durch branchenführende Vereinbarungen zum Servicelevel (SLAs, Service Level Agreements).

[Etcd](https://github.com/etcd-io/etcd) ist ein verteilter Schlüssel-Wert-Speicher. Etcd wird in [Kubernetes](https://kubernetes.io/) verwendet, um den Zustand und die Konfiguration von Kubernetes-Clustern zu speichern. Die Verfügbarkeit, Zuverlässigkeit und Leistung von etcd sicherzustellen, ist für die allgemeine Integrität, Skalierbarkeit, Flexibilität, Verfügbarkeit und Leistung eines Kubernetes-Clusters essenziell. 

Dank der etcd-API in Azure Cosmos DB können Sie Azure Cosmos DB als Back-End-Speicher für [Azure Kubernetes Service](../aks/index.yml) verwenden. Die etcd-API in Azure Cosmos DB befindet sich aktuell in der Vorschauphase. Azure Cosmos DB implementiert das etcd-Wire Protocol. Mit der etcd-API in Azure Cosmos DB können Entwickler automatisch eine hochgradig zuverlässige, [verfügbare](high-availability.md), [global verteilte](distribute-data-globally.md) Kubernetes-Plattform verwenden. Dank dieser API können Entwickler die Kubernetes-Zustandsverwaltung in einem vollständig verwalteten, cloudnativen PaaS-Dienst skalieren. 

> [!NOTE]
> Im Gegensatz zu anderen APIs in Azure Cosmos DB können Sie ein etcd-API-Konto nicht über das Azure-Portal, die CLI oder SDKs bereitstellen. Sie können ein etcd-API-Konto nur über eine Resource Manager-Vorlage bereitstellen. Ausführliche Schritte finden Sie unter [How to use Azure Kubernetes with Azure Cosmos DB (Verwenden von Azure Kubernetes mit Azure Cosmos DB)](bootstrap-kubernetes-cluster.md). Die Azure Cosmos DB-etcd-API ist derzeit als eingeschränkte Vorschauversion verfügbar. Sie können sich [für die Vorschauversion registrieren](https://aka.ms/cosmosetcdapi-signup), indem Sie das Registrierungsformular ausfüllen.

## <a name="wire-level-compatibility"></a>Kompatibilität auf Leitungsebene

Azure Cosmos DB implementiert das Wire Protocol von etcd Version 3 und lässt zu, dass die API-Server des [Masterknotens](https://kubernetes.io/docs/concepts/overview/components/) Azure Cosmos DB verwenden können, genauso wie in einer lokal installierten etcd-Umgebung. Die etcd-API unterstützt gegenseitige TLS-Authentifizierung. 

Das folgende Diagramm zeigt die Komponenten eines Kubernetes-Clusters. Der API-Server verwendet die etcd-API in Azure Cosmos DB statt einer lokal installierten etcd-Instanz im Clustermaster. 

![Implementierung des etcd-Wire Protocol durch Azure Cosmos DB](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Hauptvorteile

### <a name="no-etcd-operations-management"></a>Keine etcd-Vorgangsverwaltung erforderlich

Der vollständig verwaltete native Clouddienst Azure Cosmos DB macht das Einrichten und Verwalten von etcd für Kubernetes-Entwickler überflüssig. Die etcd-API in Azure Cosmos DB ist skalierbar, hochverfügbar, fehlertolerant und bietet eine hohe Leistung. Das Einrichten von Replikationen in mehreren Knoten, das Durchführen paralleler Updates und das Durchführen von Sicherheitspatches sowie das Überwachen der etcd-Integrität werden von Azure Cosmos DB übernommen.

### <a name="global-distribution--high-availability"></a>Globale Verteilung und Hochverfügbarkeit 

Wenn Sie die etcd-API verwenden, garantiert Azure Cosmos DB eine Verfügbarkeit von 99,99 % für Datenlese- und -schreibvorgänge in einer Region und 99,999 % Verfügbarkeit in mehreren Regionen. 

### <a name="elastic-scalability"></a>Elastische Skalierbarkeit

Azure Cosmos DB bietet flexible Skalierbarkeit für regionsübergreifende Lese- und Schreibanforderungen.
Das etcd-API-Konto in Azure Cosmos DB wird mit einem wachsenden Kubernetes-Cluster flexibel skaliert, ohne dass es dabei zu Ausfallzeiten kommt. Wenn Sie etcd-Daten in Azure Cosmos DB statt im Kubernetes-Masterknoten speichern, können Sie den Masterknoten flexibler skalieren. 

### <a name="security--enterprise-readiness"></a>Sicherheit- und Unternehmensbereitschaft

Wenn etcd-Daten in Azure Cosmos DB gespeichert werden, können Kubernetes-Entwickler automatisch die [integrierte Verschlüsselung ruhender Daten](database-encryption-at-rest.md), [Zertifizierungen und Compliance](compliance.md) und [Sicherungs- und Wiederherstellungsfunktionen](online-backup-and-restore.md) nutzen, die von Azure Cosmos DB unterstützt werden. 

## <a name="next-steps"></a>Nächste Schritte

* [How to use Azure Kubernetes with Azure Cosmos DB (Verwenden von Azure Kubernetes mit Azure Cosmos DB)](bootstrap-kubernetes-cluster.md)
* [Hauptvorteile von Azure Cosmos DB](introduction.md)
* [Schnellstartanleitung: AKS-Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)