---
title: Skalierung und Hosting von Azure Functions | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie zwischen einem Azure Functions-Verbrauchstarif und -Premium-Plan (Premium-Tarif) wählen.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, Functions, Verbrauchstarif, Premium-Plan, Ereignisverarbeitung, Webhooks, dynamisches Compute, serverlose Architektur
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05fb277564f72e62a4cdacc12a16cce229b2befc
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872741"
---
# <a name="azure-functions-scale-and-hosting"></a>Skalierung und Hosting von Azure Functions

Azure Functions wird in zwei verschiedenen Plänen (Tarifen) ausgeführt: Verbrauchstarif und Premium-Plan (öffentliche Vorschau). Der Verbrauchstarif fügt automatisch Computeleistung hinzu, wenn Ihr Code ausgeführt wird. Ihre App wird horizontal hochskaliert, wenn dies zur Verarbeitung der Last erforderlich ist, und herunterskaliert, wenn die Codeausführung beendet wurde. Für virtuelle Computer im Leerlauf müssen Sie nichts bezahlen und auch keine Kapazitäten im Voraus reservieren.  Der Premium-Plan bedingt ebenfalls automatisches Skalieren und Hinzufügen von Computeleistung, wenn Ihr Code ausgeführt wird.  Zum Premium-Plan gehören zusätzliche Features wie Premium-Compute-Instanzen, die Möglichkeit, Instanzen unbegrenzt betriebsbereit zu halten, und VNET-Verbindungen.  Wenn Sie einen vorhandenen App Service-Plan haben, können Sie auch Ihre Funktions-Apps in ihnen ausführen.

> [!NOTE]  
> Sowohl der [Premium-Plan](https://aka.ms/functions-premiumplan) als auch der [Verbrauchstarif für Linux](https://azure.microsoft.com/updates/azure-functions-consumption-plan-for-linux-preview/) liegen derzeit in der Vorschauversion vor.

Wenn Sie mit Azure Functions nicht vertraut sind, informieren Sie sich in der [Übersicht über Azure Functions](functions-overview.md).

Wenn Sie eine Funktions-App erstellen, wählen Sie den Hostingplan für Funktionen in der App aus. In beiden Plänen führt eine Instanz des *Azure Functions-Hosts* die Funktionen aus. Der Plantyp steuert:

* Wie Hostinstanzen horizontal hochskaliert werden.
* Die Ressourcen, die für jeden Host verfügbar sind.
* Instanzfeatures wie die VNET-Konnektivität.

> [!NOTE]
> Sie können zwischen dem Verbrauchstarif und dem Premium-Plan wechseln, indem Sie die Plan-Eigenschaft der Funktions-App-Ressource ändern.

## <a name="consumption-plan"></a>Verbrauchsplan

Bei Verwendung des Verbrauchstarifs werden Instanzen des Azure Functions-Hosts dynamisch basierend auf der Anzahl der eingehenden Ereignisse hinzugefügt und entfernt. Dieser serverlose Plan wird automatisch skaliert, sodass Ihnen nur dann Computeressourcen berechnet werden, wenn Ihre Funktionen ausgeführt werden. In einem Verbrauchsplan tritt für eine Funktionsausführung nach einem konfigurierbaren Zeitraum ein Timeout auf.

Die Abrechnung erfolgt auf der Grundlage der Anzahl von Ausführungen, der Ausführungszeit und des verwendeten Arbeitsspeichers. Die Abrechnung wird für alle Funktionen innerhalb einer Funktions-App aggregiert. Weitere Informationen finden Sie unter [Azure Functions – Preise].

Der Verbrauchsplan ist der Standardhostingplan. Er bietet folgende Vorteile:

* Sie bezahlen nur, wenn Ihre Funktionen ausgeführt werden.
* Das horizontale Hochskalieren erfolgt automatisch – selbst in Zeiten hoher Lasten.

## <a name="premium-plan-public-preview"></a>Premium-Plan (öffentliche Vorschauversion)

Bei Verwendung des Premium-Plans werden Instanzen des Azure Functions-Hosts schnell basierend auf der Anzahl der eingehenden Ereignisse hinzugefügt und entfernt, so, wie dies im Verbrauchstarif erfolgt.  Allerdings bietet der Premium-Plan zusätzlich Folgendes:

* Immer betriebsbereite (warme) Instanzen, um jegliche Kaltstarts zu vermeiden
* VNet-Konnektivität
* Unbegrenzte Ausführungsdauer
* Premium-Instanzgrößen (Instanz mit einem Kern, mit zwei Kernen und mit vier Kernen)
* Vorhersagbare Preisoptionen
* App-Zuteilung mit hoher Dichte für Pläne mit mehrere Funktions-Apps

Informationen dazu, wie Sie diese Optionen konfigurieren, finden Sie im [Azure Functions-Premium-Plan](functions-premium-plan.md).

Anstatt pro Ausführung und genutztem Arbeitsspeicher abzurechnen, basiert die Abrechnung auf der Anzahl der Kernsekunden und Gigabytesekunden, die für benötigte und reservierte Instanzen verwendet wurden.  Mindestens eine Instanz muss ständig betriebsbereit (warm) sein, sodass es feste monatliche Kosten pro Plan gibt, der aktiv ist (unabhängig von der Anzahl der Ausführungen).

Betrachten Sie den Azure Functions-Premium-Plan in den folgenden Fällen:
* Ihre Funktions-Apps werden kontinuierlich oder nahezu kontinuierlich ausgeführt. In diesem Fall kann ein App Service-Plan kostengünstiger sein.
* Sie benötigen weitere CPU- oder Arbeitsspeicheroptionen zusätzlich zu den für den Verbrauchsplan bereitgestellten.
* Ihr Code muss länger ausgeführt werden, als im Verbrauchsplan als [maximal zulässige Ausführungsdauer](#timeout) angegeben ist.
* Sie benötigen Funktionen, die nur mit einem App Service-Plan verfügbar sind, etwa Unterstützung für App Service-Umgebung, VNET/VPN-Konnektivität und größere virtuelle Computer.

> [!NOTE]
> Die Vorschauversion des Premium-Plans unterstützt derzeit die Funktionen, die in .NET, Node oder Java über Windows-Infrastruktur ausgeführt werden.

Wenn Sie JavaScript-Funktionen im Rahmen eines Premium-Plans ausführen, sollten Sie eine Instanz auswählen, die weniger vCPUs hat. Weitere Informationen finden Sie unter [Auswählen von Premium-Plänen mit Einzelkern](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>App Service-Plan

Ihre Funktions-Apps können auf denselben dedizierten virtuellen Computern wie andere App Service-Apps ausgeführt werden (Basic-, Standard-, Premium- und isolierte SKUs). App Service-Pläne unterstützen Linux.

Entscheiden Sie sich in den folgenden Fällen für einen App Service-Plan:

* Sie verfügen über nicht ausgelastete virtuelle Computer, auf denen bereits andere App Service-Instanzen ausgeführt werden.
* Sie möchten Ihre Funktions-App unter Linux ausführen oder ein benutzerdefiniertes Image zum Ausführen Ihrer Funktionen bereitstellen.

Sie zahlen für Funktions-Apps in einem App Service-Plan das gleiche wie für andere App Service-Ressourcen, etwa Web-Apps. Weitere Informationen zur Funktionsweise von App Service-Plänen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../app-service/overview-hosting-plans.md). 

Mit einem App Service-Plan können Sie manuell horizontal hochskalieren, indem Sie weitere Instanzen von virtuellen Computern hinzufügen, oder Sie können die automatische Skalierung aktivieren. Weitere Informationen finden Sie unter [Manuelles oder automatisches Skalieren der Instanzenzahl](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Sie können auch zentral hochskalieren, indem Sie einen anderen App Service-Plan auswählen. Weitere Informationen finden Sie unter [Zentrales Hochskalieren einer App in Azure](../app-service/web-sites-scale.md). 

Wenn Sie JavaScript-Funktionen im Rahmen eines App Service-Plans ausführen, sollten Sie einen Plan mit weniger vCPUs wählen. Weitere Informationen finden Sie unter [Auswählen von Einzelkern-App Service-Plänen](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

Wenn Sie einen App Service-Plan verwenden, müssen Sie die Einstellung **Always On** aktivieren, damit Ihre Funktions-App ordnungsgemäß ausgeführt wird. Bei einem App Service-Plan geht die Functions-Runtime nach wenigen Minuten der Inaktivität in den Leerlauf über, sodass nur HTTP-Trigger Ihre Funktionen tatsächlich „reaktivieren“ können. Always On ist nur bei einem App Service-Plan verfügbar. Bei einem Verbrauchsplan aktiviert die Plattform Funktions-Apps automatisch.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="what-is-my-hosting-plan"></a>Informationen zum Hostingplan

Wenn Sie den von Ihrer Funktions-App verwendeten Hostingplan ermitteln möchten, sehen Sie im [Azure-Portal](https://portal.azure.com) auf der Registerkarte **Übersicht** für die Funktions-App unter **App Service-Plan/-Tarif** nach. Bei App Service-Plänen ist auch der Tarif angegeben. 

![Anzeigen des Skalierungsplans im Portal](./media/functions-scale/function-app-overview-portal.png)

Sie können auch die Azure-Befehlszeilenschnittstelle verwenden, um den Plan auf folgende Weise zu ermitteln:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Wenn die Ausgabe dieses Befehls `dynamic` lautet, ist Ihre Funktions-App im Verbrauchsplan enthalten. Wenn die Ausgabe dieses Befehls `ElasticPremium` lautet, ist Ihre Funktions-App im Premium-Plan enthalten.  Alle anderen Werte geben Stufen eines App Service-Plans an.

Auch bei aktiviertem Always On wird das Ausführungstimeout für einzelne Funktionen durch die Einstellung `functionTimeout` in der [host.json](functions-host-json.md#functiontimeout)-Projektdatei gesteuert.

## <a name="storage-account-requirements"></a>Anforderungen an das Speicherkonto

Bei jedem Plan erfordert eine Funktions-App ein allgemeines Azure Storage-Konto, das Azure-Blob-, Warteschlangen-, Datei- und Tabellenspeicher unterstützt. Der Grund dafür ist, dass Functions bei Vorgängen wie dem Verwalten von Triggern und Protokollieren von Funktionsausführungen auf Azure Storage zurückgreift, einige Speicherkonten jedoch keine Warteschlangen und Tabellen unterstützen. Diese Konten, zu denen reine Blobspeicherkonten (einschließlich Storage Premium) und allgemeine Speicherkonten mit zonenredundanter Speicherreplikation gehören, werden aus den vorhandenen Auswahlmöglichkeiten für **Speicherkonto** herausgefiltert, wenn Sie eine Funktions-App erstellen.

<!-- JH: Does using a Premium Storage account improve perf? -->

Weitere Informationen zu Speicherkontentypen finden Sie unter [Einführung in die Azure Storage-Dienste](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Funktionsweise von Verbrauchsplan (Verbrauchstarif) und Premium-Plan

Im Verbrauchsplan und im Premium-Plan skaliert der Skalierungscontroller CPU- und Arbeitsspeicherressourcen automatisch, indem dem Functions-Host basierend auf der Anzahl der Ereignisse, nach denen die Funktionen ausgelöst werden, weitere Instanzen hinzugefügt werden. Jede Instanz des Functions-Hosts im Verbrauchsplan ist auf 1,5 GB Arbeitsspeicher und 1 CPU beschränkt.  Eine Instanz des Hosts ist die gesamte Funktions-App, d. h., alle Funktionen innerhalb einer Funktions-App verwenden innerhalb einer Instanz dieselbe Ressource und werden gleichzeitig skaliert. Funktions-Apps, die denselben Verbrauchsplan nutzen, werden unabhängig voneinander skaliert.  Im Premium-Plan bestimmt Ihre Plangröße den verfügbaren Arbeitsspeicher und die verfügbare CPU für alle Apps in diesem Plan für diese Instanz.  

Funktionscodedateien werden in Azure Files-Freigaben im Hauptspeicherkonto der Funktion gespeichert. Wenn Sie das Hauptspeicherkonto der Funktions-App löschen, werden die Funktionscodedateien gelöscht und können nicht wiederhergestellt werden.

> [!NOTE]
> Bei Verwendung eines Blobtriggers in einem Verbrauchsplan kann es möglicherweise bis zu 10 Minuten dauern, bis neue Blobs verarbeitet werden. Diese Verzögerung tritt auf, nachdem eine Funktions-App in den Leerlauf gewechselt ist. Sobald die Funktions-App ausgeführt wird, werden die Blobs sofort verarbeitet. Um diese Kaltstartverzögerung zu vermeiden, verwenden Sie den Premium-Plan oder den [Event Grid-Trigger](functions-bindings-event-grid.md). Weitere Informationen finden Sie im Referenzartikel zur [Blobtriggerbindung](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Laufzeitskalierung

Azure Functions verwendet die Komponente *Skalierungscontroller*, um die Rate der Ereignisse zu überwachen und zu bestimmen, ob eine horizontale Hoch- oder Herunterskalierung erforderlich ist. Der Skalierungscontroller verwendet Heuristik für jeden Triggertyp. Bei Verwendung eines Triggers für Azure Queue Storage beispielsweise basieren die Skalen auf der Länge der Warteschlange und dem Alter der ältesten Nachricht in der Warteschlange.

Die Skalierungseinheit ist die Funktionen-App. Bei einer horizontalen Hochskalierung der Funktions-App werden zusätzliche Ressourcen zugeordnet, um mehrere Instanzen des Azure Functions-Hosts auszuführen. Umgekehrt entfernt der Skalierungscontroller bei abnehmenden Computeanforderungen Instanzen des Functions-Hosts. Die Anzahl der Instanzen wird schließlich zentral auf null herunterskaliert, wenn in einer Funktionen-App keine Funktionen ausgeführt werden.

![Skalierungscontroller: Überwachen von Ereignissen und Erstellen von Instanzen](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Grundlegendes zum Verhalten von Skalierungen

Die Skalierung variiert ausgehend von verschiedenen Faktoren, und die Skalierung ist je nach dem ausgewählten Auslöser und der Sprache unterschiedlich. Das System umfasst heute einige Aspekte der Skalierung:

* Eine einzelne Funktions-App wird maximal auf 200 Instanzen hochskaliert. Eine einzelne Instanz kann mehrere Meldungen oder Anforderungen gleichzeitig verarbeiten, weshalb es keine Grenze für die Anzahl gleichzeitiger Ausführungen gibt.
* Bei HTTP-Triggern werden neue Instanzen höchstens einmal pro Sekunde zugeordnet.
* Bei Nicht-HTTP-Triggern werden neue Instanzen höchstens einmal alle 30 Sekunden zugeordnet.

Für verschiedene Auslöser gelten unter Umständen unterschiedliche Grenzwerte für die Skalierung sowie die folgenden Angaben:

* [Event Hub](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Bewährte Methoden und Muster für skalierbare Apps

Es gibt viele Aspekte einer Funktions-App, die sich auf die Skalierung auswirken. Dazu zählen beispielsweise die Hostkonfiguration, der Runtimespeicherbedarf und die Ressourceneffizienz.  Weitere Informationen finden Sie im [Abschnitt zur Skalierbarkeit im Artikel zum Thema Leistung](functions-best-practices.md#scalability-best-practices). Sie sollten auch das Verhalten von Verbindungen beim Skalieren Ihrer Funktions-App beachten. Weitere Informationen finden Sie unter [How to manage connections in Azure Functions](manage-connections.md) (Verwalten von Verbindungen in Azure Functions).

### <a name="billing-model"></a>Abrechnungsmodell

Die Abrechnung des Verbrauchsplans wird detailliert auf der [Azure Functions – Preise] beschrieben. Der Verbrauch wird auf Ebene der Funktions-App zusammengefasst, wobei nur die Zeit gezählt wird, für die der Funktionscode ausführt wurde. Folgende Einheiten werden für die Abrechnung verwendet:

* **Ressourcenverbrauch in Gigabytesekunden (GB-s)** – berechnet als Kombination aus Arbeitsspeichergröße und Ausführungsdauer für alle Funktionen in einer Funktions-App. 
* **Ausführungen** – werden bei jeder Ausführung einer Funktion als Antwort auf einen Ereignisauslöser gezählt.

Nützliche Fragen und Informationen zum Verständnis Ihrer Verbrauchsrechnung finden Sie [in den häufig gestellten Fragen zur Abrechnung](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions – Preise]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Diensteinschränkungen

In der folgenden Tabelle sind die Einschränkungen aufgeführt, die für Funktions-Apps gelten, wenn sie in den verschiedenen Hostingplänen ausgeführt werden:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
