---
title: Deaktivieren der Überwachung mit Azure Monitor für VMs (Vorschauversion) | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Überwachung Ihrer virtuellen Computer mit Azure Monitor für VMs beenden können.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: 0f35ea3e35277ee7f1afd8278a31f45ed20c6995
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522118"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>Deaktivieren der Überwachung Ihrer virtuellen Computer mit Azure Monitor für VMs (Vorschauversion)

Wenn Sie nach dem Aktivieren der Überwachung Ihrer virtuellen Computer mit Azure Monitor für VMs entscheiden, dass Sie diese nicht mehr überwachen möchten, können Sie die Überwachung deaktivieren. Dieser Artikel erläutert die Vorgehensweise für einen oder mehrere virtuelle Computer.  

Zurzeit unterstützt Azure Monitor für VMs das selektive Deaktivieren der Überwachung Ihrer virtuellen Computer nicht. Wenn Ihr Log Analytics-Arbeitsbereich für die Unterstützung dieser und weiterer Lösungen sowie für das Sammeln anderer Überwachungsdaten konfiguriert ist, müssen Sie die unten beschriebenen Auswirkungen und Methoden genau kennen, bevor Sie fortfahren.

Azure Monitor für VMs nutzt die folgenden Komponenten:

* Einen Log Analytics-Arbeitsbereich, in dem die von VMs und anderen Quellen gesammelten Überwachungsdaten gespeichert werden.
* Die im Arbeitsbereich konfigurierte Sammlung von Leistungsindikatoren, wodurch die Überwachungskonfiguration auf allen mit dem Arbeitsbereich verbundenen VMs aktualisiert wird.
* Zwei im Arbeitsbereich konfigurierte Überwachungslösungen – **InfrastructureInsights** und **ServiceMap** –, wodurch die Überwachungskonfiguration auf allen mit dem Arbeitsbereich verbundenen VMs aktualisiert wird.
* Zwei Azure-VM-Erweiterungen – **MicrosoftMonitoringAgent** und **DependencyAgent** –, die Daten sammeln und an den Arbeitsbereich senden.

Berücksichtigen Sie folgende Aspekte, wenn Sie die Deaktivierung der Überwachung Ihrer virtuellen Computer mit Azure Monitor für VMs vorbereiten:

* Wenn Sie das Verfahren nur für eine einzelne VM ausführen möchten und den vorab ausgewählten Log Analytics-Arbeitsbereich akzeptiert haben, können Sie die Überwachung deaktivieren, indem Sie den Abhängigkeits-Agent auf der VM deinstallieren und den Log Analytics-Agent von diesem Arbeitsbereich trennen. Dieses Verfahren ist geeignet, wenn Sie die VM für andere Zwecke verwenden und später möglicherweise mit einem anderen Arbeitsbereich verbinden möchten.
* Wenn Sie den Log Analytics-Arbeitsbereich verwenden, um andere Überwachungslösungen und die Sammlung von Daten aus anderen Quellen zu unterstützen, können Sie Azure Monitor für VMs-Lösungskomponenten aus dem Arbeitsbereich entfernen, ohne dass dies zu Unterbrechungen oder anderen Auswirkungen auf den Arbeitsbereich führt.  

>[!NOTE]
> Nach dem Entfernen der Lösungskomponenten aus dem Arbeitsbereich werden in den jeweiligen Ansichten des Portals weiterhin Daten zum Integritätszustand Ihrer Azure-VMs sowie Leistungs- und Zuordnungsdaten angezeigt. In den Ansichten für Leistung und Zuordnung werden nach einiger Zeit keine Daten mehr angezeigt, in der Integritätsansicht dagegen wird weiterhin der Integritätsstatus Ihrer VMs angezeigt. Auf den VMs steht die Option **Jetzt testen** zur Verfügung, die es Ihnen ermöglicht, die Überwachung zu einem späteren Zeit wieder zu aktivieren.  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>Vollständige Entfernung von Azure Monitor für VMs

Die folgenden Schritte beschreiben, wie Sie Azure Monitor für VMs vollständig entfernen, wenn Sie den Log Analytics-Arbeitsbereich weiterhin benötigen. Sie werden die Lösungen **InfrastructureInsights** und **ServiceMap** aus dem Arbeitsbereich entfernen.  

>[!NOTE]
>Wenn Sie die ServiceMap-Überwachungslösung zuvor zum Aktivieren von Azure Monitor für VMs verwendet haben und die Lösung weiterhin benötigen, entfernen Sie sie nicht (wie in Schritt 6 beschrieben).  
>

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Klicken Sie im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen „Log Analytics“ ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics**.
3. Wählen Sie in der Liste der Log Analytics-Arbeitsbereiche den Arbeitsbereich aus, den Sie beim Integrieren von Azure Monitor für VMs ausgewählt haben.
4. Wählen Sie im linken Bereich **Lösungen** aus.  
5. Wählen Sie in der Liste der Lösungen den Eintrag **InfrastructureInsights(Arbeitsbereichsname)** aus, und klicken Sie dann auf der Seite **Übersicht** der Lösung auf **Löschen**.  Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Ja**.  
6. Wählen Sie in der Liste der Lösungen den Eintrag **ServiceMap(Arbeitsbereichsname)** aus, und klicken Sie dann auf der Seite **Übersicht** der Lösung auf **Löschen**.  Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Ja**.  

Wenn Sie vor der Integration von Azure Monitor für VMs die [aktivierten Leistungsindikatoren](vminsights-enable-overview.md#performance-counters-enabled) für die Windows- oder Linux-basierten VMs in Ihrem Arbeitsbereich nicht gesammelt haben, müssen Sie diese Regeln deaktivieren. Führen Sie dazu die [hier](../platform/data-sources-performance-counters.md#configuring-performance-counters) beschriebenen Schritte für Windows bzw. Linux aus.

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>Deaktivieren der Überwachung für eine Azure-VM und Beibehalten des Arbeitsbereichs  

Die folgenden Schritte beschreiben, wie Sie die Überwachung für einen virtuellen Computer deaktivieren, der für die Auswertung von Azure Monitor für VMs aktiviert war, wenn der Log Analytics-Arbeitsbereich weiterhin zur Unterstützung der Überwachung von Daten aus anderen Quellen benötigt wird. Wenn es sich um einen virtuellen Azure-Computer handelt, entfernen Sie die VM-Erweiterungen für den Abhängigkeits-Agent und den Log Analytics-Agent für Windows bzw. Linux direkt vom virtuellen Computer. 

>[!NOTE]
>Wenn der virtuelle Computer zur Orchestrierung von Prozessen und zur Verwaltung von Konfiguration und Updates von Azure Automation verwaltet wird oder wenn die Sicherheitsverwaltung und Bedrohungserkennung für den virtuellen Computer über Azure Security Center erfolgt, darf der Log Analytics-Agent nicht entfernt werden. Andernfalls verhindern Sie eine proaktive Verwaltung Ihrer VM durch diese Dienste und Lösungen. 

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an. 
2. Wählen Sie im Azure-Portal die Option **Virtual Machines** aus. 
3. Wählen Sie einen virtuellen Computer in der Liste aus. 
4. Wählen Sie im linken Bereich **Erweiterungen** aus, und klicken Sie auf der Seite **Erweiterungen** auf **DependencyAgent**.
5. Klicken Sie auf der Eigenschaftenseite der Erweiterung auf **Deinstallieren**.
6. Wählen Sie auf der Seite **Erweiterungen** den Eintrag **MicrosoftMonitoringAgent** aus, und klicken Sie auf der Eigenschaftenseite der Erweiterung auf **Deinstallieren**.  
