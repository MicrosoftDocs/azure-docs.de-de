---
title: Azure Service Fabric-Ereignisse | Microsoft-Dokumentation
description: Erfahren Sie, wie Service Fabric-Ereignisse vorkonfiguriert bereitgestellt werden, um Sie bei der Überwachung Ihres Azure Service Fabric-Clusters zu unterstützen.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: b4270b9438a397ec09537c9d6343515ebc21af98
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393026"
---
# <a name="service-fabric-events"></a>Service Fabric-Ereignisse 

Die Service Fabric-Plattform schreibt mehrere strukturierte Ereignisse für die wichtigsten operativen Aktivitäten, die innerhalb des Clusters stattfinden. Diese reichen von Cluster-Upgrades bis hin zu Replikatplatzierungsentscheidungen. Jedes Ereignis, das Service Fabric verfügbar macht, ist einer der folgenden Entitäten im Cluster zugeordnet:
* Cluster
* Anwendung
* Dienst
* Partition
* Replikat 
* Container

Eine vollständige Liste der von der Plattform verfügbar gemachten Ereignisse finden Sie unter [Auflistung der Service Fabric-Ereignisse](service-fabric-diagnostics-event-generation-operational.md).

Nachfolgend sind Sie einige Beispiele für Szenarien aufgeführt, für die Sie Ereignisse in Ihrem Cluster anzeigen sollten. 
* Lebenszyklusereignisse von Knoten: Ereignisse wie das Erstellen, Entfernen, Herunter-/Hochskalieren, Aktivieren, Deaktivieren oder Neustarten von Knoten werden verfügbar gemacht und geben Ihnen einen Überblick über das, was passiert ist. Mit ihrer Hilfe können Sie feststellen, ob es ein Problem mit dem Computer gibt, oder ob eine über Service Fabric aufgerufene API den Status eines Knotens geändert hat.
* Clusterupgrade: Bei einem Clusterupgrade (SF-Version oder Konfigurationsänderung) sehen Sie, wie das Upgrade initiiert, in den einzelnen Upgradedomänen angewendet und abgeschlossen wird (oder ein Rollback erfolgt). 
* Anwendungsupgrades: Wie bei Clusterupgrades gibt es eine komplexe Reihe von Ereignissen während der Anwendung des Upgrades. Es kann hilfreich sein, diese Ereignisse zu verstehen: wann ein Upgrade geplant wurde, den aktuellen Stand des Upgrades und die allgemeine Abfolge der Ereignisse. Damit können Sie auf praktische Weise zurückverfolgen, welche Upgrades erfolgreich angewandt wurden oder ob ein Rollback ausgelöst wurde.
* Bereitstellen/Löschen von Anwendungen/Diensten: Es gibt Ereignisse für jede Anwendung, jeden Dienst und jeden Container, die bzw. der erstellt oder gelöscht wird. Dies ist hilfreich beim horizontalen Herunter- oder Hochskalieren, d. h. beim Erhöhen der Anzahl der Replikate.
* Partitionsverschiebungen (Neukonfiguration): Jedes Mal, wenn eine statusbehaftete Partition neu konfiguriert wird (Änderung in der Replikatgruppe), wird ein Ereignis protokolliert. Dies ist hilfreich, wenn Sie verstehen möchten, wie oft die Replikatgruppe der Partition geändert wird oder wie oft ein Failover erfolgt, bzw. um nachzuverfolgen, in welchem Knoten das primäre Replikat zu einem beliebigen Zeitpunkt ausgeführt wurde.
* Chaos-Ereignisse: Wenn Sie den [Chaos](service-fabric-controlled-chaos.md)-Dienst von Service Fabric nutzen, werden bei jedem Start oder Anhalten des Diensts oder beim Einfügen eines Fehlers in das System Ereignisse protokolliert.
* Integritätsereignisse: Service Fabric macht jedes Mal Integritätsereignisse verfügbar, wenn ein Integritätsbericht mit dem Ergebnis „Warning“ oder „Error“ erstellt wird, eine Entität wieder in den Integritätsstatus „OK“ wechselt oder ein Integritätsbericht abläuft. Diese Ereignisse sind zum Nachverfolgen von Integritätsverlaufsstatistiken für eine Entität sehr hilfreich. 

## <a name="how-to-access-events"></a>Zugriff auf Ereignisse

Für den Zugriff auf Service Fabric-Ereignisse gibt es einige unterschiedliche Möglichkeiten:
* Die Ereignisse werden über Standardkanäle wie ETW-Protokolle/Windows-Ereignisprotokolle protokolliert und können von jedem Überwachungstool, das diese unterstützt (z. B. Azure Monitor-Protokolle), visualisiert werden. Standardmäßig ist bei im Portal erstellten Clustern die Diagnose aktiviert, und der Windows Azure-Diagnose-Agent sendet die Ereignisse an Azure Table Storage. Dennoch müssen Sie dies in Ihre Log Analytics-Ressource integrieren. Erfahren Sie mehr dazu, wie Sie den [Azure-Diagnose-Agent](service-fabric-diagnostics-event-aggregation-wad.md) so konfigurieren, dass er die Diagnosekonfiguration des Clusters ändert, um mehr Protokolle oder Leistungsindikatoren aufzunehmen und [Azure Monitor-Protokolle zu integrieren](service-fabric-diagnostics-event-analysis-oms.md).
* Über die REST-API des EventStore-Diensts, über die Sie den Cluster direkt abfragen können, oder über die Service Fabric-Clientbibliothek. Lesen Sie hierzu [Abfragen von EventStore-APIs nach Clusterereignissen](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Überwachen Ihres Clusters finden Sie unter [Überwachen des Clusters und der Plattform](service-fabric-diagnostics-event-generation-infra.md).
* Unter [Übersicht über den EventStore-Dienst](service-fabric-diagnostics-eventstore.md) erfahren Sie mehr über den EventStore-Dienst.
