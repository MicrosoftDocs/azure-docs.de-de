---
title: Übersicht über die automatische Skalierung in Virtual Machines, Cloud Services und Web-Apps
description: Automatische Skalierung in Microsoft Azure. Gilt für Virtual Machines, VM-Skalierungsgruppen, Clouddienste und Web-Apps.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.subservice: autoscale
ms.openlocfilehash: 05f20aec536ebdb702caea37051a65af9bbc659f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787579"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Übersicht über die automatische Skalierung in Microsoft Azure Virtual Machines, Cloud Services und Web-Apps
In diesem Artikel wird beschrieben, was die automatische Skalierung von Microsoft Azure ist, welche Vorteile sie hat und wie Sie mit der Nutzung beginnen.  

Die automatische Skalierung von Azure Monitor gilt nur für [VM.Skalierungsgruppen](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Clouddienste](https://azure.microsoft.com/services/cloud-services/), [App Service – Web-Apps](https://azure.microsoft.com/services/app-service/web/) und [API Management-Dienste](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> Azure verfügt über zwei Methoden für die automatische Skalierung. Eine ältere Version der automatischen Skalierung gilt für virtuelle Computer (Verfügbarkeitsgruppen). Diese Funktion verfügt über eingeschränkten Support. Wir empfehlen die Migration zu Skalierungsgruppen für virtuelle Computer, um für einen schnelleren und zuverlässigeren Support der automatischen Skalierung zu sorgen. Dieser Artikel enthält einen Link zur Verwendung der älteren Technologie.  
>
>

## <a name="what-is-autoscale"></a>Was ist die automatische Skalierung?
Bei der automatischen Skalierung können Sie jeweils die richtige Menge an Ressourcen ausführen, um die Lasten für Ihre Anwendung zu bewältigen. Sie können Ressourcen hinzufügen, um auf einen Anstieg der Last zu reagieren, und Kosten sparen, indem Sie ungenutzte Ressourcen entfernen. Sie geben eine minimale und maximale Anzahl von auszuführenden Instanzen an und können VMs basierend auf einer Reihe von Regeln automatisch hinzufügen oder entfernen. Eine minimale Anzahl sorgt dafür, dass Ihre Anwendung auch dann immer ausgeführt wird, wenn keine Last vorhanden ist. Mit einer maximalen Anzahl können Sie eine Obergrenze für die Gesamtkosten pro Stunde festlegen. Sie bestimmen die automatische Skalierung zwischen diesen beiden Grenzwerten, indem Sie Regeln erstellen.

 ![Automatische Skalierung – Beschreibung VMs hinzufügen und entfernen](./media/autoscale-overview/AutoscaleConcept.png)

Wenn die Bedingungen der Regel erfüllt sind, werden eine oder mehrere Aktionen der automatischen Skalierung ausgelöst. Sie können VMs hinzufügen und entfernen oder andere Aktionen durchführen. Im folgenden konzeptionellen Diagramm ist dieser Prozess dargestellt.  

 ![Flussdiagramm zur automatischen Skalierung](./media/autoscale-overview/Autoscale_Overview_v4.png)

Die folgende Beschreibung bezieht sich auf die einzelnen Elemente des obigen Diagramms.   

## <a name="resource-metrics"></a>Ressourcenmetriken
Ressourcen geben Metriken aus, die später von Regeln verarbeitet werden. Metriken sind über verschiedene Methoden verfügbar.
Für Skalierungsgruppen für virtuelle Computer werden Telemetriedaten von Azure-Diagnose-Agents verwendet, während die Telemetriedaten für Web-Apps und Clouddienste direkt aus der Azure-Infrastruktur stammen. Einige häufig verwendete Statistiken sind CPU-Nutzung, Arbeitsspeichernutzung, Threadanzahl, Warteschlangenlänge und Datenträgerverwendung. Eine Liste mit den Telemetriedaten, die Sie verwenden können, finden Sie unter [Allgemeine Metriken für die automatische Skalierung](../../azure-monitor/platform/autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Benutzerdefinierte Metriken
Sie können auch eigene benutzerdefinierten Metriken verwenden, die von Ihren Anwendungen ausgegeben werden können. Wenn Sie Ihre Anwendungen für das Senden von Metriken an Application Insights konfiguriert haben, können Sie anhand dieser Metriken entscheiden, ob die Skalierung angewendet werden soll oder nicht.

## <a name="time"></a>Time
Für zeitplanbasierte Regeln gilt die UTC-Zeit. Sie müssen die Zeitzone richtig einrichten, wenn Sie Ihre Regeln festlegen.  

## <a name="rules"></a>Regeln
Im Diagramm wird nur eine Regel für die automatische Skalierung angezeigt, aber Sie können auch eine höhere Zahl von Regeln verwenden. Sie können je nach Ihren Anforderungen komplexe überlappende Regeln erstellen.  Regeltypen  

* **Metrikbasiert** : Führen Sie diese Aktion beispielsweise aus, wenn die CPU-Auslastung oberhalb von 50% liegt.
* **Zeitbasiert** : Lösen Sie einen Webhook beispielsweise jeweils samstags um 8 Uhr in einer bestimmten Zeitzone aus.

Mit metrikbasierten Regeln wird die Anwendungslast gemessen, und basierend auf dieser Last werden VMs hinzugefügt oder entfernt. Zeitplanbasierte Regeln ermöglichen die Skalierung, wenn für die Last Zeitmuster erkennbar sind und Sie die Skalierung durchführen möchten, bevor eine mögliche Zu- oder Abnahme der Last eintritt.  

## <a name="actions-and-automation"></a>Aktionen und Automation
Mit Regeln können eine oder mehrere Arten von Aktionen ausgelöst werden.

* **Skalieren** : Horizontales Herunter- oder Hochskalieren von VMs
* **E-Mail** : Senden einer E-Mail an Abonnementadministratoren, Co-Admins und weitere von Ihnen angegebene E-Mail-Adressen
* **Über Webhooks automatisieren** : Aufrufen von Webhooks, mit denen mehrere komplexe Aktionen innerhalb oder außerhalb von Azure ausgelöst werden können. In Azure können Sie ein Azure Automation-Runbook, eine Azure-Funktion oder eine Azure-Logik-App starten. Beispiele für Drittanbieter-URLs außerhalb von Azure sind Dienste wie Slack und Twilio.

## <a name="autoscale-settings"></a>Einstellungen für die automatische Skalierung
Für die automatische Skalierung wird die folgende Terminologie bzw. Struktur verwendet.

- Eine **Einstellung für die automatische Skalierung** wird von der Engine für die automatische Skalierung gelesen, um zu ermitteln, ob zentral hoch- oder herunterskaliert werden soll. Sie enthält mindestens ein Profil, Informationen zur Zielressource und Benachrichtigungseinstellungen.

  - Ein **Profil für die automatische Skalierung** ist eine Kombination aus:

    - einer **Kapazitätseinstellung**, mit der die minimalen Werte, maximalen Werte und Standardwerte für die Anzahl von Instanzen angegeben werden
    - einem **Satz von Regeln**, von denen jede einen Trigger (Zeit oder Metrik) und eine Skalierungsaktion (nach oben oder unten) enthält
    - einer **Wiederholung**, mit der angegeben wird, wann die automatische Skalierung dieses Profil anwenden soll.

      Sie können mehrere Profile verwenden, um unterschiedliche Anforderungen abzudecken, die sich ggf. überschneiden. Sie können beispielsweise unterschiedliche Autoskalierungsprofile für verschiedene Tageszeiten oder Wochentage verwenden.

  - Mit einer **Benachrichtigungseinstellung** wird definiert, welche Benachrichtigungen gesendet werden sollen, wenn ein Autoskalierungsereignis eintritt. Hierfür müssen jeweils die entsprechenden Kriterien eines Profils der Autoskalierungseinstellung erfüllt sein. Bei der automatischen Skalierung können Benachrichtigungen an eine oder mehrere E-Mail-Adressen gesendet oder Webhooks aufgerufen werden.


![Azure-Autoskalierungseinstellung, Profil und Regelstruktur](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

Die vollständige Liste mit den konfigurierbaren Feldern und Beschreibungen finden Sie unter den Informationen zur [REST-API für die automatische Skalierung](https://msdn.microsoft.com/library/dn931928.aspx).

Codebeispiele finden Sie unter:

* [Erweiterte Konfiguration der automatischen Skalierung mithilfe von Resource Manager-Vorlagen für VM Scale Sets](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [REST-API für die automatische Skalierung](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Horizontale und vertikale Skalierung
Bei der automatischen Skalierung wird nur horizontal skaliert. Dies bedeutet, dass die Anzahl von VM-Instanzen erhöht („horizontal hochskalieren“) oder verringert wird („horizontal herunterskalieren“).  Die horizontale Skalierung ist in einer Cloudsituation flexibler, da Sie bei Bedarf Tausende von VMs ausführen können, um die Last zu bewältigen.

Bei der vertikalen Skalierung ist dies anders. Hierbei wird die gleiche Anzahl von VMs beibehalten, aber die Leistungsfähigkeit der VMs wird erhöht („zentral hochskalieren“) oder verringert („zentral herunterskalieren“). Die Leistungsfähigkeit wird in Bezug auf den Arbeitsspeicher, die CPU-Geschwindigkeit, Speicherplatz auf dem Datenträger usw. gemessen.  Für die vertikale Skalierung gelten mehr Einschränkungen. Sie ist von der Verfügbarkeit größerer Hardware abhängig, die relativ schnell das obere Limit erreicht und je nach Region variieren kann. Außerdem ist für die vertikale Skalierung auch das Beenden und Starten einer VM erforderlich.

Weitere Informationen finden Sie unter [Vertikales Skalieren von virtuellen Azure-Computern mit Azure Automation](../../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Zugriffsmethoden
Zum Einrichten der automatischen Skalierung können Sie Folgendes verwenden:

* [Azure-Portal](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [Plattformübergreifende Befehlszeilenschnittstelle](../../azure-monitor/platform/cli-samples.md#autoscale)
* [Azure Monitor-REST-API](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Unterstützte Dienste für die automatische Skalierung
| Dienst | Schema und Dokumente |
| --- | --- |
| Web-Apps |[Skalieren von Web-Apps](../../azure-monitor/platform/autoscale-get-started.md) |
| Cloud Services |[Automatisches Skalieren eines Clouddiensts](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| VMs: Klassisch |[Scaling Classic Virtual Machine Availability Sets (Skalieren von klassischen VM-Verfügbarkeitsgruppen)](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| VMs: Windows-Skalierungsgruppen |[Skalieren von VM-Skalierungsgruppen in Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| VMs: Windows-Skalierungsgruppen |[Skalieren von VM-Skalierungsgruppen in Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| VMs: Windows-Beispiel |[Erweiterte Konfiguration der automatischen Skalierung mithilfe von Resource Manager-Vorlagen für VM Scale Sets](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| API Management-Dienst|[Automatisches Skalieren einer Azure API Management-Instanz](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur automatischen Skalierung können Sie den obigen exemplarischen Vorgehensweisen für die automatische Skalierung oder den folgenden Ressourcen entnehmen:

* [Allgemeine Metriken für die automatische Skalierung in Azure Monitor](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Best Practices für die automatische Skalierung in Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Verwenden von automatischen Skalierungsvorgängen zum Senden von E-Mail- und Webhook-Warnbenachrichtigungen](../../azure-monitor/platform/autoscale-webhook-email.md)
* [REST-API für die automatische Skalierung](https://msdn.microsoft.com/library/dn931953.aspx)
* [Beheben von Problemen bei der automatischen Skalierung von VM-Skalierungsgruppen](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

