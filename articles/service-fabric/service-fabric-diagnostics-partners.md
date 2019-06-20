---
title: Überwachung von Azure Service Fabric mit Partnerlösungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Service Fabric mithilfe von Überwachungslösungen von Partnern überwachen.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: c2f953c98e41291951f07556bd0cd441d2793d1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60946591"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Partnerlösungen für die Überwachung von Azure Service Fabric

Dieser Artikel veranschaulicht, wie Sie Ihre Service Fabric-Anwendungen, -Cluster und -Infrastruktur mit einer Reihe von Partnerlösungen überwachen können. Wir arbeiten mit jedem der unten genannten Partner zusammen, um integrierte Angebote für Service Fabric zu erstellen.

## <a name="dynatrace"></a>Dynatrace

Unsere Integration in Dynatrace bietet viele sofort einsatzfähige Features für die Überwachung Ihrer Service Fabric-Cluster. Installieren Sie Dynatrace OneAgent auf Ihren VMSS-Instanzen, um Leistungsindikatoren und eine Topologie Ihrer Service Fabric-Bereitstellung bis hinunter auf die App-Ebene zu erhalten. Dynatrace ist auch eine ausgezeichnete Wahl für die Überwachung lokaler Systeme. Sehen Sie sich die in der [Ankündigung](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) aufgeführten Features und die [Anweisungen](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) an, um Dynatrace in Ihrem Cluster zu aktivieren. 

## <a name="datadog"></a>Datadog

Datadog besitzt eine Erweiterung für VMSS sowohl auf Windows- als auch auf Linux-Instanzen. Mit Datadog können Sie Windows-Ereignisprotokolle und damit auch Service Fabric-Plattformereignisse unter Windows erfassen. Lesen Sie [hier](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric) die Anweisungen zum Senden von Diagnosedaten an Datadog.

## <a name="appdynamics"></a>AppDynamics

Die Service Fabric-Integration in AppDynamics erfolgt auf Anwendungsebene. Indem Sie Umgebungsvariablen aktualisieren und AppDynamics NuGets verwenden, können Sie Anwendungstelemetriedaten an AppDynamics senden. Lesen Sie diese [Anweisungen](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric), um zu erfahren, wie Sie Ihre .NET Service Fabric-Anwendungen in AppDynamics integrieren.

## <a name="new-relic"></a>New Relic

New Relic ist ebenfalls ein Tool für die Verwaltung der Anwendungsleistung, das sich sehr gut in Service Fabric-Anwendungen integrieren lässt. Sie können die NuGet-Pakete für New Relic installieren und spezifische Umgebungsvariablen zu Ihren Manifestdateien hinzufügen, um Telemetriedaten Ihrer Anwendungen an New Relic zu senden. Lesen Sie diese [Anweisungen](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric), um die New Relic-Telemetrie für Ihre .NET Service Fabric-Anwendungen zu aktivieren.

## <a name="elk"></a>ELK 

Der ELK-Stapel ist eine Sammlung aus Open-Source-Technologien: Elasticsearch, Logstash und Kibana. Nutzen Sie eine Kombination dieser Technologien, um Überwachungs- und -Diagnosedaten aus Service Fabric zu sammeln, zu speichern und zu analysieren. [Hier](service-fabric-tutorial-java-elk.md) finden Sie ein Tutorial zur Vorgehensweise bei nativen Service Fabric-Anwendungen mit Java. 

## <a name="humio"></a>Humio

Humio ist ein Protokollsammlungsdienst, der Protokolle von Ihren Anwendungen und Ereignissen aus Service Fabric in Echtzeit aus der Cloud oder lokal abrufen kann. Zusätzlich zur Möglichkeit der Liveüberwachung bietet Humio hochmoderne Funktionen zur Analyse und Visualisierung, um Erkenntnisse aus Ihren Diagnosen gewinnen zu können. Humio bietet kosteneffiziente Preismodelle und ist skalierbar, ohne Einbußen bei der Geschwindigkeit. Es kann direkt in Service Fabric-Plattformereignisse und in die Anwendungstelemetrie integriert werden. [Hier](https://github.com/humio/service-fabric-humio) erhalten Sie weitere Informationen zu Humio und die Integration in Service Fabric.

## <a name="next-steps"></a>Nächste Schritte

* Verschaffen Sie sich einen [Überblick über die Überwachung und Diagnose](service-fabric-diagnostics-overview.md) in Service Fabric.
* Erfahren Sie mehr über das [Diagnostizieren häufiger Szenarien](service-fabric-diagnostics-common-scenarios.md) mit unseren Erstanbietertools.
