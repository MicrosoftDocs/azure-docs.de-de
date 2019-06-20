---
title: Was ist Azure Monitor für VMs (Vorschauversion)? | Microsoft-Dokumentation
description: Azure Monitor for VMs ist ein Feature von Azure Monitor, das Integritäts- und Leistungsüberwachung des Azure VM-Betriebssystems mit der automatischen Erkennung von Anwendungskomponenten und Abhängigkeiten mit anderen Ressourcen kombiniert und die Kommunikation unter ihnen als Zuordnung darstellt. Dieser Artikel bietet eine Übersicht.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: 7d86b3fe9aeddd603d0c40b1c760cabdee42e396
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522108"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Was ist Azure Monitor für VMs (Vorschauversion)?

Azure Monitor für VMs überwacht Ihre virtuellen Azure-Computer (VM) und VM-Skalierungsgruppen bedarfsorientiert. Der Dienst analysiert die Leistung und Integrität Ihrer Windows- und Linux-VMs und überwacht deren Prozesse und Abhängigkeiten von anderen Ressourcen und externen Prozessen. 

Als Lösung umfasst Azure Monitor für VMs die Unterstützung für die Überwachung von Leistungs- und Anwendungsabhängigkeiten für VMs, die lokal oder bei einem anderen Cloudanbieter gehostet sind. Drei wichtige Features bieten tiefgreifende Einblicke:

* **Logische Komponenten von virtuellen Azure-Computern mit Windows und Linux**: Werden an vorkonfigurierten Integritätskriterien gemessen und benachrichtigen Sie, wenn die ausgewertete Bedingung erfüllt ist.  

* **Vordefinierte Diagramme zu Leistungstrends**: Zeigen Kernleistungsmetriken des Gast-VM-Betriebssystems an.

* **Abhängigkeitsdiagramm**: Zeigt die mit der VM verbundenen Komponenten aus verschiedenen Ressourcengruppen und Abonnements an.  

Die Features sind in Form von drei Perspektiven geordnet:

* Health
* Leistung
* Map

>[!NOTE]
>Aktuell wird das Integritätsfeature nur für Azure-VMs angeboten. Die Features „Leistung“ und „Zuordnung“ unterstützen Azure-VMs, Azure-VM-Skalierungsgruppen und virtuelle Computer, die in Ihrer Umgebung oder von anderen Cloudanbietern gehostet werden.

Die Integration in Azure Monitor-Protokollen ermöglicht die leistungsstarke Aggregation und Filterung und bietet die Möglichkeit zum Analysieren von Datentrends im zeitlichen Verlauf. Eine derartig umfassende Überwachung Ihrer Workloads kann nicht mit Azure Monitor oder der Dienstzuordnung allein erreicht werden.  

Sie können diese Daten direkt in einem einzelnen virtuellen Computer anzeigen, oder Sie können eine aggregierte Ansicht Ihrer virtuellen Computer mit Azure Monitor bereitstellen. Diese Ansicht basiert auf der Perspektive der einzelnen Features:

* **Integrität**: Die virtuellen Computer beziehen sich auf eine Ressourcengruppe.
* **Zuordnung** und **Leistung**: Die virtuellen Computer sind für das Berichten an einen bestimmten Log Analytics-Arbeitsbereich konfiguriert.

![VM-Insights-Perspektive im Azure-Portal](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure Monitor für VMs kann eine vorhersehbare Leistung und Verfügbarkeit wichtiger Anwendungen bieten. Es identifiziert kritische Ereignisse des Betriebssystems, Leistungsengpässe und Netzwerkprobleme. Azure Monitor für VMs kann außerdem Aufschluss darüber geben, ob ein Problem mit weiteren Abhängigkeiten zusammenhängt.  

## <a name="data-usage"></a>Datennutzung 

Wenn Sie Azure Monitor für VMs bereitstellen, werden die von Ihren virtuellen Computern gesammelten Daten erfasst und in Azure Monitor gespeichert. Metriken zu Integritätskriterien werden in Azure Monitor in einer Zeitreihendatenbank gespeichert, gesammelte Leistungs- und Abhängigkeitsdaten in einem Log Analytics-Arbeitsbereich. Basierend auf den Preisen, die auf der [Seite mit der Azure Monitor-Preisübersicht](https://azure.microsoft.com/pricing/details/monitor/) veröffentlicht sind, wird Azure Monitor für VMs für Folgendes abgerechnet:

* Die erfassten und gespeicherten Daten.
* Die Anzahl der auf Integritätskriterien bezogenen metrischem Zeitreihen, die überwacht werden.
* Die erstellten Warnungsregeln.
* Die gesendeten Benachrichtigungen. 

Die Protokollgröße unterscheidet sich je nach Zeichenfolgenlänge der Leistungsindikatoren und kann mit der Anzahl der logischen Datenträger und Netzwerkadapter anwachsen, die der VM zugeordnet sind. Wenn Sie bereits über einen Arbeitsbereich verfügen und diese Indikatoren sammeln, erfolgt keine doppelte Belastung. Wenn Sie bereits die Dienstzuordnung verwenden, besteht die einzige für Sie sichtbare Veränderung in den zusätzlichen Verbindungsdaten, die an Azure Monitor gesendet werden.

## <a name="next-steps"></a>Nächste Schritte
Informationen zu den Anforderungen und Methoden, die Ihnen beim Überwachen Ihrer virtuellen Computer helfen, finden Sie unter [Bereitstellen von Azure Monitor für VMs](vminsights-enable-overview.md).
