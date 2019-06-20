---
title: Intelligente Erkennung – Von Azure Application Insights erkannte niedrige Auslastung von Cloudressourcen | Microsoft Docs
description: Überwachen Sie Anwendungen mit Azure Application Insights auf eine niedrige Auslastung von Cloudressourcen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 7cf72068b9cabceb0c5b535986ac4dfb62151b94
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61297502"
---
# <a name="low-cpu-utilization-in-cloud-resources-preview"></a>Niedrige CPU-Auslastung in Cloudressourcen (Vorschau)

Application Insights analysiert die CPU-Auslastung der einzelnen Rolleninstanzen in Ihrer Anwendung automatisch und erkennt Instanzen mit niedriger CPU-Auslastung. Diese Erkennung ermöglicht es Ihnen, Ihre Azure-Ressourcen zu verringern und Kosten zu senken, indem Sie die Anzahl der von jeder Rolle verwendeten Rolleninstanzen oder die Anzahl der Rollen verringern.

Diese Funktion erfordert keine spezielle Einrichtung, abgesehen von der [Konfiguration von Leistungsindikatoren](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) für Ihre App. Sie ist aktiv, wenn Ihre App genügend Telemetriedaten zu CPU-Leistungsindikatoren („Prozessorzeit (%)“) generiert.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wann erhalte ich diese Art von Benachrichtigung der intelligenten Erkennung?
Eine typische Benachrichtigung tritt auf, wenn viele Ihrer Web-/Workerrolleninstanzen eine niedrige CPU-Auslastung aufweisen.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>Beansprucht meine App definitiv zu viele Ressourcen?
Nein, eine Benachrichtigung bedeutet nicht zwingend, dass Ihre App definitiv zu viele Ressourcen verbraucht. Obwohl solche Muster einer niedrigen CPU-Auslastung in der Regel darauf hinweisen, dass der Ressourcenverbrauch verringert werden kann, könnte dieses Verhalten typisch für Ihre spezifische Rolle sein oder einen natürlichen geschäftliche Grund haben und ignoriert werden. Es kann beispielsweise sein, dass mehrere Instanzen für andere Ressourcen wie Arbeitsspeicher/Netzwerk und nicht für die CPU benötigt werden.

## <a name="how-do-i-fix-it"></a>Wie behebe ich das Problem?
Die Benachrichtigungen umfassen Diagnoseinformationen zur Unterstützung des Diagnoseprozesses:
1. **Selektierung.** Die Benachrichtigung gibt die Rollen in Ihrer App an, die eine niedrige CPU-Auslastung aufweisen. Dadurch können Sie dem Problem eine Priorität zuweisen.
2. **Umfang.** Wie viele Rollen wiesen eine niedrige CPU-Auslastung auf, und wie viele Instanzen jeder Rolle beanspruchen die CPU in geringem Maße? Diese Informationen können Sie der Benachrichtigung entnehmen.
3. **Diagnose.** Die Erkennung enthält den Prozentsatz der CPU-Auslastung, wobei die CPU-Auslastung für jede Instanz im Laufe der Zeit angezeigt wird. Zur weiteren Diagnose des Problems können Sie auch die entsprechenden Elemente und Berichte verwenden, die mit weiterführenden Informationen (z. B. Perzentilen der CPU-Auslastung) verknüpft sind.
