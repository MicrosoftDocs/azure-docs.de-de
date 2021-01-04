---
title: Verfügbarkeit der Service Fabric-Dienste
description: Beschreibt Fehlererkennung, Failover und Wiederherstellung eines Diensts in einer Azure Service Fabric-Anwendung.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018901"
---
# <a name="availability-of-service-fabric-services"></a>Verfügbarkeit der Service Fabric-Dienste
Dieser Artikel bietet eine Übersicht darüber, wie Azure Service Fabric die Verfügbarkeit eines Diensts aufrechterhält.

## <a name="availability-of-service-fabric-stateless-services"></a>Verfügbarkeit zustandsloser Service Fabric-Dienste
Service Fabric-Dienste können zustandsbehaftet oder zustandslos sein. Ein zustandsloser Dienst ist ein Anwendungsdienst, der keinen [lokalen Zustand](service-fabric-concepts-state.md) aufweist, welcher hoch verfügbar oder zuverlässig sein muss.

Für das Erstellen eines zustandslosen Diensts muss eine `InstanceCount` definiert werden. Dieser Wert legt die Anzahl von Instanzen für die Anwendungslogik des statusfreien Diensts fest, die im Cluster ausgeführt werden sollen. Das Erhöhen der Anzahl der Instanzen ist die empfohlene Vorgehensweise für das Skalieren zustandsloser Dienste.

Wird in einer Instanz eines zustandslosen benannten Diensts ein Fehler erkannt, wird auf einem anderen geeigneten Knoten im Cluster eine neue Instanz erstellt. Eine zustandslose Dienstinstanz kann z.B. auf Knoten1 ausfallen und auf Knoten5 neu erstellt werden.

## <a name="availability-of-service-fabric-stateful-services"></a>Verfügbarkeit zustandsbehafteter Service Fabric-Dienste
Einem zustandsbehafteten Dienst ist ein Zustand zugeordnet. In Service Fabric ist ein zustandsbehafteter Dienst als eine Replikatgruppe modelliert. Jedes Replikat ist eine ausgeführte Instanz des Codes des Diensts. Das Replikat weist auch eine Kopie des Zustands für diesen Dienst auf. Lese- und Schreibvorgänge erfolgen im selben Replikat, dem *primären Replikat*. Änderungen des Zustands aufgrund von Schreibvorgängen werden in andere Replikate der Replikatgruppe (*aktive sekundäre Replikate*) *repliziert* und angewendet. 

Es kann nur ein primäres Replikat geben, aber es können mehrere aktive sekundäre Replikate vorhanden sein. Die Anzahl der aktiven sekundären Replikate ist konfigurierbar. Eine höhere Anzahl von Replikaten kann eine höhere Anzahl von gleichzeitigen Software- und Hardwarefehlern tolerieren.

Wenn das primäre Replikat ausfällt, macht Service Fabric eines der aktiven sekundären Replikate zum neuen primären Replikat. Dieses aktive sekundäre Replikat verfügt bereits über die aktualisierte Version des Zustands (durch *Replikation*) und kann die Verarbeitung von Lese-/Schreibvorgängen fortsetzen. Dieser Prozess wird als *Neukonfiguration* bezeichnet und im Artikel [Neukonfiguration](service-fabric-concepts-reconfiguration.md) ausführlich beschrieben.

Das Konzept, bei dem ein Replikat ein primäres oder ein aktives sekundäres Replikat ist, wird *Replikatrolle* genannt. Diese Replikate werden im Artikel [Replikate und Instanzen](service-fabric-concepts-replica-lifecycle.md) ausführlich beschrieben. 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Service Fabric-Konzepten finden Sie in den folgenden Artikeln:

- [Skalieren von Service Fabric-Diensten](service-fabric-concepts-scalability.md)
- [Partitionieren von Service Fabric-Diensten](service-fabric-concepts-partitioning.md)
- [Definieren und Verwalten von Zuständen](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

