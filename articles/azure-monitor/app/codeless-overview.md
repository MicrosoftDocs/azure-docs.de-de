---
title: Überwachen von Apps ohne Codeänderungen – automatische Instrumentierung für Azure Monitor Application Insights | Microsoft-Dokumentation
description: Übersicht über die automatische Instrumentierung für Azure Monitor Application Insights – Anwendungsleistungsverwaltung ohne Code
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: fe57174f1b090cbaa2196930f5ddd252074f1978
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526448"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Was ist automatische Instrumentierung oder Anfügung ohne Code – Azure Monitor Application Insights?

Mit der automatischen Instrumentierung oder dem Anfügen ohne Code können Sie die Anwendungsüberwachung mit Application Insights aktivieren, ohne Ihren Code zu ändern.  

Application Insights ist mit verschiedenen Ressourcenanbietern integriert und funktioniert in unterschiedlichen Umgebungen. Im Wesentlichen müssen Sie lediglich den Agent aktivieren und in einigen Fällen konfigurieren, der automatisch die Telemetrie erfasst. In kürzester Zeit werden die Metriken, Daten und Abhängigkeiten in Ihrer Application Insights-Ressource angezeigt, sodass Sie die Quelle potenzieller Probleme vor ihrem Auftreten erkennen und die Grundursache mit der End-to-End-Transaktionsansicht analysieren können.

## <a name="supported-environments-languages-and-resource-providers"></a>Unterstützte Umgebungen, Sprachen und Ressourcenanbieter

Da ständig weitere Integrationen hinzugefügt werden, wird die Funktionsmatrix für die automatische Instrumentierung zunehmend komplex. Die nachstehende Tabelle zeigt den aktuellen Status der Unterstützung verschiedener Ressourcenanbieter, Sprachen und Umgebungen.

|Umgebung/Ressourcenanbieter          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service unter Windows           | GA, st. akt.*       | GA, Anmeldung      | In Bearbeitung     | In Bearbeitung     | Nicht unterstützt   |
|Azure App Service unter Linux             | –             | Nicht unterstützt   | In Bearbeitung     | Öffentliche Vorschau  | Nicht unterstützt   |
|Azure App Service unter AKS               | –             | Im Entwurf       | Im Entwurf       | Im Entwurf       | Nicht unterstützt   |
|Azure Functions: grundlegend                | GA, st. akt.*       | GA, st. akt.*       | GA, st. akt.*       | GA, st. akt.*       | GA, st. akt.*       |
|Azure Functions Windows – Abhängigkeiten | Nicht unterstützt   | Nicht unterstützt   | Öffentliche Vorschau  | Nicht unterstützt   | Nicht unterstützt   |
|Azure Kubernetes Service               | –             | Im Entwurf       | Über den-Agent   | Im Entwurf       | Nicht unterstützt   |
|Azure-VMs unter Windows                      | Öffentliche Vorschau  | Nicht unterstützt   | Nicht unterstützt   | Nicht unterstützt   | Nicht unterstützt   |
|Lokale VMs unter Windows                | GA, Anmeldung      | Nicht unterstützt   | Über den-Agent   | Nicht unterstützt   | Nicht unterstützt   |
|Eigenständiger Agent: alle Umg.            | Nicht unterstützt   | Nicht unterstützt   | Allgemein verfügbar              | Nicht unterstützt   | Nicht unterstützt   |

\* „St. akt.“ steht für „standardmäßig aktiviert“. Application Insights wird automatisch aktiviert, sobald Sie die App in unterstützten Umgebungen bereitstellen. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
Die Anwendungsüberwachung für Azure App Service unter Windows ist für [.NET-Anwendungen](./azure-web-apps.md?tabs=net) verfügbar und standardmäßig aktiviert.

#### <a name="netcore"></a>.NET Core
Die Überwachung für [.NET Core-Anwendungen](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=netcore) kann mit nur einem Klick aktiviert werden.

#### <a name="java"></a>Java
Die Portalintegration für die Überwachung von Java-Anwendungen in App Service unter Windows ist derzeit nicht verfügbar, jedoch können Sie den [eigenständigen Java 3.0-Agent](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) von Application Insights zu Ihrer Anwendung hinzufügen, ohne Änderungen am Code vornehmen zu müssen, bevor Sie die Apps in App Service bereitstellen. Der Java 3.0-Agent von Application Insights ist allgemein verfügbar.

#### <a name="nodejs"></a>Node.js
Die Überwachung für Node.js-Anwendungen unter Windows kann derzeit nicht über das Portal aktiviert werden. Verwenden Sie zum Überwachen von Node.js-Anwendungen das [SDK](https://docs.microsoft.com/azure/azure-monitor/app/nodejs).

### <a name="linux"></a>Linux

#### <a name="netcore"></a>.NET Core
Verwenden Sie zum Überwachen von .NET Core-Anwendungen, die unter Linux ausgeführt werden, das [SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

#### <a name="java"></a>Java 
Das Aktivieren der Java-Anwendungsüberwachung für App Service für Linux ist nicht über das Portal verfügbar, aber Sie können den [Java 3.0-Agent von Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) zu Ihrer App hinzufügen, bevor Sie sie in App Service bereitstellen. Der Java 3.0-Agent von Application Insights ist allgemein verfügbar.

#### <a name="nodejs"></a>Node.js
Die [Überwachung von Node.js-Anwendungen in App Service für Linux](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=nodejs) ist in allen Regionen in der öffentlichen Vorschauphase verfügbar und kann im Azure-Portal aktiviert werden. 

#### <a name="python"></a>Python
Verwenden Sie zum [Überwachen Ihrer Python-App](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python) das SDK. 

## <a name="azure-functions"></a>Azure-Funktionen

Die grundlegende Überwachung für Azure Functions ist standardmäßig für die Sammlung von Protokoll-, Leistungs- und Fehlerdaten sowie HTTP-Anforderungen aktiviert. Für Java-Anwendungen können Sie eine umfassendere Überwachung mit verteilter Ablaufverfolgung aktivieren und End-to-End-Transaktionsdetails erhalten. Diese Funktionalität für Java befindet sich in der Public Preview, und Sie können sie [im Azure-Portal aktivieren](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Die Instrumentierung von Azure Kubernetes Service ohne Code ist zurzeit für Java-Anwendungen über den [eigenständigen Agent](./java-in-process-agent.md) verfügbar. 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure-Windows-VMs und VM-Skalierungsgruppen

Die automatische Instrumentierung für Azure-VMs und VM-Skalierungsgruppen ist für [.NET](./azure-vm-vmss-apps.md) und [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) verfügbar.  

## <a name="on-premises-servers"></a>Lokale Server
Sie können die Überwachung für Ihre [lokalen Windows-Server für .NET-Anwendungen](./status-monitor-v2-overview.md) und für [Java-Apps](./java-in-process-agent.md) ganz einfach aktivieren.

## <a name="other-environments"></a>Andere Umgebungen
Der flexible eigenständige Java-Agent funktioniert in jeder Umgebung, und Ihr Code muss nicht instrumentiert werden. [Befolgen Sie die Anleitung](./java-in-process-agent.md) zum Aktivieren von Application Insights, und informieren Sie sich über die fantastischen Funktionen des Java-Agents. Der Agent befindet sich in der Public Preview und ist in allen Regionen verfügbar. 

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Application Insights](./app-insights-overview.md)
* [Anwendungszuordnung](./app-map.md)
* [End-to-End-Leistungsüberwachung](../learn/tutorial-performance.md)

