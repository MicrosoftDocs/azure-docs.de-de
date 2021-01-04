---
title: Überwachen von Apps ohne Codeänderungen – automatische Instrumentierung für Azure Monitor Application Insights | Microsoft-Dokumentation
description: Übersicht über die automatische Instrumentierung für Azure Monitor Application Insights – Anwendungsleistungsverwaltung ohne Code
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: aa5c2a0070ea0c3a0963f97fc1a04670eeaa4827
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701890"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Was ist automatische Instrumentierung oder Anfügung ohne Code – Azure Monitor Application Insights?

Mit der automatischen Instrumentierung oder dem Anfügen ohne Code können Sie die Anwendungsüberwachung mit Application Insights aktivieren, ohne Ihren Code zu ändern.  

Application Insights ist mit verschiedenen Ressourcenanbietern integriert und funktioniert in unterschiedlichen Umgebungen. Im Wesentlichen müssen Sie lediglich den Agent aktivieren und in einigen Fällen konfigurieren, der von Hause aus die Telemetrie sammelt. In kürzester Zeit werden die Metriken, Daten und Abhängigkeiten in Ihrer Application Insights-Ressource angezeigt, sodass Sie die Quelle potenzieller Probleme vor ihrem Auftreten erkennen und die Grundursache mit der End-to-End-Transaktionsansicht analysieren können.

## <a name="supported-environments-languages-and-resource-providers"></a>Unterstützte Umgebungen, Sprachen und Ressourcenanbieter

Da wir ständig weitere Integrationen hinzufügen, wird die Funktionsmatrix für die automatische Instrumentierung zunehmend komplex. Die nachstehende Tabelle zeigt den aktuellen Status der Unterstützung verschiedener Ressourcenanbieter, Sprachen und Umgebungen.

|Umgebung/Ressourcenanbieter          | .NET            | .NET Core       | Java            | Node.js         |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service unter Windows           | GA, st. akt.*       | GA, Anmeldung      | Private Vorschau | Private Vorschau |
|Azure App Service unter Linux             | –             | Nicht unterstützt   | Private Vorschau | Öffentliche Vorschau  |
|Azure App Service unter AKS               | –             | Im Entwurf       | Im Entwurf       | Im Entwurf       |
|Azure Functions: grundlegend                | GA, st. akt.*       | GA, st. akt.*       | GA, st. akt.*       | GA, st. akt.*       |
|Azure Functions Windows – Abhängigkeiten | Nicht unterstützt   | Nicht unterstützt   | Öffentliche Vorschau  | Nicht unterstützt   |
|Azure Kubernetes Service               | –             | Im Entwurf       | Über den-Agent   | Im Entwurf       |
|Azure-VMs unter Windows                      | Öffentliche Vorschau  | Nicht unterstützt   | Nicht unterstützt   | Nicht unterstützt   |
|Lokale VMs unter Windows                | GA, Anmeldung      | Nicht unterstützt   | Über den-Agent   | Nicht unterstützt   |
|Eigenständiger Agent: alle Umg.            | Nicht unterstützt   | Nicht unterstützt   | Allgemein verfügbar              | Nicht unterstützt   |

\* „St. akt.“ steht für „standardmäßig aktiviert“. Application Insights wird automatisch aktiviert, sobald Sie die App in unterstützten Umgebungen bereitstellen. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

Die [Anwendungsüberwachung in Azure App Service](./azure-web-apps.md?tabs=net) ist für .NET-Anwendungen verfügbar und standardmäßig aktiviert. .NET Core kann mit einem Mausklick aktiviert werden, und Java und Node.js befinden sich in der privaten Vorschauphase.

### <a name="linux"></a>Linux 

Die Überwachung von Java- und Node.js-Anwendungen in App Service ist als Public Preview in allen Regionen verfügbar und kann im Azure-Portal aktiviert werden.

## <a name="azure-functions"></a>Azure-Funktionen

Die grundlegende Überwachung für Azure Functions ist standardmäßig für die Sammlung von Protokoll-, Leistungs- und Fehlerdaten sowie HTTP-Anforderungen aktiviert. Für Java-Anwendungen können Sie eine umfassendere Überwachung mit verteilter Ablaufverfolgung aktivieren und End-to-End-Transaktionsdetails erhalten. Diese Funktionalität für Java befindet sich in der Public Preview, und Sie können sie [im Azure-Portal aktivieren](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Die Instrumentierung von Azure Kubernetes Service ohne Code ist zurzeit für Java-Anwendungen über den [eigenständigen Agent](./java-in-process-agent.md) verfügbar. 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure-Windows-VMs und VM-Skalierungsgruppen

[Die automatische Instrumentierung für Azure-VMs und VM-Skalierungsgruppen](./azure-vm-vmss-apps.md) ist für .NET-Anwendungen verfügbar. 

## <a name="on-premises-servers"></a>Lokale Server
Sie können die Überwachung für Ihre [lokalen Windows-Server für .NET-Anwendungen](./status-monitor-v2-overview.md) und für [Java-Apps](./java-in-process-agent.md) ganz einfach aktivieren.

## <a name="other-environments"></a>Andere Umgebungen
Der flexible eigenständige Java-Agent funktioniert in jeder Umgebung, und Ihr Code muss nicht instrumentiert werden. [Befolgen Sie die Anleitung](./java-in-process-agent.md) zum Aktivieren von Application Insights, und informieren Sie sich über die fantastischen Funktionen des Java-Agents. Der Agent befindet sich in der Public Preview und ist in allen Regionen verfügbar. 

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Application Insights](./app-insights-overview.md)
* [Anwendungszuordnung](./app-map.md)
* [End-to-End-Leistungsüberwachung](../learn/tutorial-performance.md)

