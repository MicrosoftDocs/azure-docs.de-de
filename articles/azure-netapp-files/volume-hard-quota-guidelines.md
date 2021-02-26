---
title: Auswirkungen der Änderungen am festen Volumekontingent auf Ihren Azure NetApp Files-Dienst | Microsoft-Dokumentation
description: In diesem Artikel werden Änderungen an harten Kontingentgrenzen für Volumes, die Planung dieser Änderungen und die Überwachung und Verwaltung von Kapazitäten erläutert.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 69a970b7b088795602dfb842b20ad6457d5f27c4
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808002"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Auswirkungen der Änderungen am festen Volumekontingent auf Ihren Azure NetApp Files-Dienst

Seit der Veröffentlichung des Azure NetApp Files-Diensts werden zwei Mechanismen eingesetzt: die Kapazitätspoolbereitstellung und die automatische Vergrößerung. Azure NetApp Files-Volumes werden einem zugrunde liegenden, vom Kunden bereitgestellten Kapazitätspool mit einer bestimmten Dienstebene und Größe schlank zugewiesen. Die Volumegrößen (Kontingente) stellen die Leistung und Kapazität bereit und können jederzeit dynamisch angepasst werden. Das bedeutet, dass das Volumekontingent derzeit die Leistung – also die Bandbreite – kontrolliert, die das Volume nutzen kann. Die zugrunde liegenden Kapazitätspools werden derzeit automatisch vergrößert, wenn die Kapazität ausgelastet ist.   

> [!IMPORTANT] 
> Das Verhalten von Azure NetApp Files für die Volume- und Kapazitätspoolbereitstellung wird in einen *manuellen*, *kontrollierbaren* Mechanismus geändert. **Ab dem 15. März 2021 entscheidet die Volumegröße (Kontingent) über die Bandbreite (Leistung) und die bereitgestellte Kapazität. Außerdem werden zugrunde liegende Kapazitätspools nicht mehr automatisch vergrößert.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Gründe für die Änderung des festen Volumekontingents

Viele Kunden haben drei Hauptprobleme mit dem *ursprünglichen* Verhalten gemeldet:
* VM-Clients wird nur eine schlank zugewiesene Kapazität (100 TiB) eines bestimmten Volumes angezeigt, wenn der Betriebssystemspeicher oder Kapazitätsüberwachungstools verwendet wurden. Dadurch wird die client- oder anwendungsseitige Kapazität ungenau repräsentiert.
* Anwendungsbesitzer können den Speicherplatz für bereitgestellte Kapazitätspools (und die damit einhergehenden Kosten) nicht kontrollieren, da der Kapazitätspool automatisch vergrößert wird. Diese Situation machte sich insbesondere in Umgebungen negativ bemerkbar, in denen Endlosprozesse ausgeführt wurden, die die bereitgestellte Kapazität schnell ausgelastet haben, sodass diese vergrößert wurden und die Kosten stiegen.
* Die Kunden haben jedoch darum gebeten, eine direkte Korrelation zwischen Volumegröße (Kontingent) und Leistung zu schaffen. Durch das aktuelle Verhalten, das die Volumekapazität implizit überabonniert und den Pool automatisch vergrößert, ist für Kunden keine direkte Korrelation erkennbar, bis das Volumekontingent aktiv festgelegt oder zurückgesetzt wird. 

Viele Kunden haben die direkte Kontrolle über die bereitgestellte Kapazität gefordert. Sie möchten die Speicherkapazität und -auslastung kontrollieren und ausgleichen. Zudem möchten Sie die Kosten, die anwendungs- und clientseitige Sichtbarkeit verfügbarer, genutzter und bereitgestellter Kapazität und die Leistung ihrer Anwendungsvolumes kontrollieren. 

## <a name="what-is-the-volume-hard-quota-change"></a>Änderung am festen Volumekontingent   

Mit der Änderung am festen Volumekontingent werden Azure NetApp Files-Volumes nicht mehr schlank mit maximal 100 TiB zugewiesen. Die Volumes werden mit der tatsächlich konfigurierten Größe (Kontingent) bereitgestellt. Darüber hinaus werden die zugrunde liegenden Kapazitätspools nicht mehr automatisch vergrößert, wenn die Kapazität vollständig ausgelastet ist. Durch diese Änderung wird das Verhalten an verwaltete Azure-Datenträger angeglichen, die ebenfalls unverändert und ohne automatische Kapazitätserhöhung bereitgestellt werden.

Nehmen Sie beispielsweise an, dass ein Azure NetApp Files-Volume mit einer Größe von 1 TiB (Kontingent) in einem 4 TiB großen Kapazitätspool der Dienstebene „Ultra“ konfiguriert wurde. Eine Anwendung schreibt kontinuierlich Daten auf das Volume.

*Ursprüngliches* Verhalten:  
* Erwartete Bandbreite: 128 MiB/s
* Gesamte nutzbare und für den Client sichtbare Bandbreite: ca. 100 TiB   
    Sie können keine weiteren Daten auf Volumes schreiben, die diese Größe überschreiten.
* Kapazitätspool: Wird automatisch in Inkrementen von 1 TiB vergrößert, wenn er ausgelastet ist
* Änderung des Volumekontingents: Ändert nur die Leistung (Bandbreite) des Volumes, nicht die Sichtbarkeit für den Client oder die nutzbare Kapazität

*Geändertes* Verhalten:  
* Erwartete Bandbreite: 128 MiB/s
* Gesamte nutzbare und für den Client sichtbare Bandbreite: 1 TiB, und Sie können keine weiteren Daten auf Volumes schreiben, die diese Größe überschreiten.
* Kapazitätspool: Weiterhin 4 TiB, jedoch ohne automatische Vergrößerung 
* Änderung des Volumekontingents: Die Leistung (Bandbreite) und die für den Client sichtbare oder nutzbare Kapazität des Volumes werden geändert.

Sie müssen die Auslastung von Azure NetApp Files-Volumes und -Kapazitätspools proaktiv überwachen. Sie müssen die Volume- und Poolauslastung explizit ändern, wenn das Kontingent fast erreicht ist. Azure NetApp Files ermöglicht weiterhin die [dynamische Größenänderung für Volumes und Kapazitätspools](azure-netapp-files-resize-capacity-pools-or-volumes.md).

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Operationalisieren der Änderung des festen Volumekontingents

In diesem Abschnitt erfahren Sie, wie Sie die Änderung des festen Volumekontingents operationalisieren und für einen reibungslosen Übergang sorgen können. Sie erhalten auch Informationen zum Umgang mit derzeit bereitgestellten Volumes und Kapazitätspools, zur kontinuierlichen Überwachung und zu den Optionen für die Warnungs- und Kapazitätsverwaltung.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Bereitgestellte Volumes und Kapazitätspools

Da das feste Volumekontingent geändert wurde, sollten Sie auch Ihr Betriebsmodell ändern. Für die bereitgestellten Volumes und Kapazitätspools ist eine kontinuierliche Kapazitätsverwaltung erforderlich.  Da das geänderte Verhalten sofort in Kraft tritt, empfiehlt das Azure NetApp Files-Teams einige einmalige Korrekturmaßnahmen für vorhandene, zuvor bereitgestellte Volumes und Kapazitätspools. Diese werden im folgenden Abschnitt erläutert.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Empfehlungen für einmalige Korrektur- oder Präventivmaßnahmen  

Die Änderung des festen Volumekontingents führt zu Änderungen an der bereitgestellten und verfügbaren Kapazität für zuvor bereitgestellte Volumes und Pools. Deshalb können Schwierigkeiten bei der Kapazitätszuordnung auftreten. Sie können vermeiden, dass kurzfristig nicht genügend Speicherplatz für Kunden verfügbar ist, indem Sie die folgenden einmaligen Korrektur- bzw. Präventivmaßnahmen umsetzen, die vom Azure NetApp Files-Team empfohlen werden: 

* **Bereitgestellte Volumegrößen:**   
    Ändern Sie die Größe aller bereitgestellten Volumes so, dass je nach Änderungsrate und Verarbeitungszeit für Warnungen oder Größenanpassungen ein angemessener Puffer vorhanden ist (z. B. 20 % bei typischen Workloadeigenschaften). Dabei liegt das Maximum bei 100 TiB, dem [Limit für Volumegrößen](azure-netapp-files-resource-limits.md#resource-limits). Diese neue Volumegröße (einschließlich der Pufferkapazität) sollte auf den folgenden Faktoren basieren:
    * **Bereitgestellte** Volumekapazität, falls die verwendete Kapazität kleiner als das bereitgestellte Volumekontingent ist
    * **Genutzte** Volumekapazität, falls die verwendete Kapazität größer als das bereitgestellte Volumekontingent ist  
    Für Kapazitätserhöhungen auf Volumeebene fallen keine zusätzlichen Gebühren an, wenn der zugrunde liegende Kapazitätspool nicht vergrößert werden muss. Eine Auswirkung dieser Änderung ist die *Erhöhung* des Bandbreitenlimits für das Volume, falls der [automatische QoS-Kapazitätspooltyp](azure-netapp-files-understand-storage-hierarchy.md#qos_types) verwendet wird.

* **Größen bereitgestellter Kapazitätspools:**   
    Nach den Anpassungen der Volumegrößen gilt Folgendes: Wenn die Summe der Volumegrößen die Größe des hostenden Kapazitätspools überschreitet, muss der Kapazitätspool vergrößert werden, bis er größer oder gleich der Volumesumme ist. Dabei liegt das Maximum bei 500 TiB, dem [Größenlimit für Kapazitätspools](azure-netapp-files-resource-limits.md#resource-limits). Darüber hinausgehende Kapazitäten für den Kapazitätspool unterliegen den üblichen ACR-Gebühren.

Arbeiten Sie bei der Validierung Ihrer Umgebung mit Azure NetApp Files-Experten zusammen, wenn Sie Hilfe bei der Einrichtung der in den folgenden Abschnitten beschriebenen Überwachung oder Warnungen benötigen.

### <a name="ongoing-capacity-management"></a>Kontinuierliche Kapazitätsverwaltung  

Nachdem Sie die einmaligen Korrekturmaßnahmen angewendet haben, sollten Sie laufende Prozesse für die Überwachung und Verwaltung der Kapazitäten zusammenlegen. In den folgenden Abschnitten finden Sie Vorschläge und Alternativen zur Kapazitätsüberwachung und -verwaltung.

### <a name="monitor-capacity-utilization"></a>Überwachen der Kapazitätsauslastung

Sie können die Kapazitätsauslastung auf unterschiedlichen Ebenen überwachen. 

#### <a name="vm-level-monitoring"></a>Überwachung auf VM-Ebene 

Die Anwendungs-VM stellt die höchste Überwachungsebene dar, also die der Anwendung am nächsten liegende. Das Verhalten der Berichterstattung zur Kapazität über das VM-Clientbetriebssystem hat sich ebenfalls geändert.

Nehmen Sie für die folgenden beiden Szenarios an, dass ein Azure NetApp Files-Volume mit einer Größe von 1 TiB (Kontingent) in einem 4 TiB großen Kapazitätspool der Dienstebene „Ultra“ konfiguriert wurde. 

##### <a name="windows"></a>Windows

Windows-Clients können die verwendete und verfügbare Kapazität eines Volumes über die Eigenschaften des Laufwerks überprüfen, das dem Netzwerk zugeordnet ist. Verwenden Sie die Option **Explorer** -> **Laufwerk** -> **Eigenschaften**.  

Mithilfe der folgenden Beispiele wird die Berichterstattung zur Volumekapazität unter Windows veranschaulicht, die *vor* der Änderung galt:

![Screenshots: Beispiel für die Speicherkapazität eines Volumes vor dem Behavior Change](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

Sie können auch wie im Folgenden veranschaulicht den Befehl `dir` in der Eingabeaufforderung verwenden:

![Screenshot: Befehl zum Anzeigen der Speicherkapazität eines Volumes vor dem Behavior Change](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

Mithilfe der folgenden Beispiele wird die Berichterstattung zur Volumekapazität unter Windows veranschaulicht, die *nach* der Änderung gilt:

![Screenshots: Beispiel für die Speicherkapazität eines Volumes nach dem Behavior Change](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

Das folgende Beispiel zeigt die Ausgabe des Befehls `dir`:  

![Screenshot: Befehl zum Anzeigen der Speicherkapazität eines Volumes nach dem Behavior Change](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Linux-Clients können die verwendete und verfügbare Kapazität eines Volumes mithilfe des [Befehls `df`](https://linux.die.net/man/1/df) überprüfen. Die Option `-h` zeigt die Größe, den verwendeten und den verfügbaren Speicherplatz in einem lesbaren Format an. Hierbei werden die Einheitengrößen M, G und T verwendet.

Mithilfe der folgenden Beispiele wird die Berichterstattung zur Volumekapazität unter Linux veranschaulicht, die *vor* der Änderung galt:  

![Screenshot: Anzeigen der Speicherkapazität eines Volumes unter Linux vor dem Behavior Change](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

Mithilfe der folgenden Beispiele wird die Berichterstattung zur Volumekapazität unter Linux veranschaulicht, die *nach* der Änderung galt:  

![Screenshot: Anzeigen der Speicherkapazität eines Volumes unter Linux nach dem Behavior Change](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Konfigurieren von Warnungen mithilfe von ANFCapacityManager

Sie können das von der Community unterstützte Logic Apps-Tool ANFCapacityManager verwenden, um die Kapazität von Azure NetApp Files zu überwachen und angepasste Warnungen zu erhalten. Sie können das Tool ANFCapacityManager über die [zugehörige GitHub-Seite](https://github.com/ANFTechTeam/ANFCapacityManager) beziehen.

ANFCapacityManager ist ein Azure Logic Apps-Tool, das kapazitätsbasierte Warnungsregeln verwaltet. Es erhöht die Volumegröße automatisch, damit stets genügend Speicherplatz für Azure NetApp Files-Volumes vorhanden ist. Die Bereitstellung ist unkompliziert, und die folgenden Funktionen für die Warnungsverwaltung sind enthalten:

* Wenn ein Azure NetApp Files-Kapazitätspool oder -Volume erstellt wird, erstellt ANFCapacityManager auf Grundlage des Auslastungsschwellenwerts (in Prozent) eine Metrikwarnungsregel.
* Wenn die Größe eines Azure NetApp Files-Kapazitätspools oder -Volumes geändert wird, ändert ANFCapacityManager die Metrikwarnungsregel auf Grundlage des Auslastungsschwellenwerts (in Prozent). Falls die Warnungsregel nicht vorhanden ist, wird sie erstellt.
* Wenn ein Azure NetApp Files-Kapazitätspool oder -Volume gelöscht wird, wird auch die zugehörige Metrikwarnungsregel gelöscht.

Sie können die folgenden essenziellen Warnungseinstellungen konfigurieren:  

* **Capacity Pool % Full Threshold** (Schwellenwert für Auslastung des Kapazitätspools in Prozent): Diese Einstellung legt den Auslastungsschwellenwert fest, ab dem eine Warnung für Kapazitätspools ausgelöst wird. Ein Wert von 90 würde bewirken, dass eine Warnung ausgelöst wird, wenn der Kapazitätspool zu 90 % ausgelastet ist.
* **Volume % Full Threshold** (Schwellenwert für Auslastung des Volumes in Prozent): Diese Einstellung legt den Auslastungsschwellenwert fest, ab dem eine Warnung für Volumes ausgelöst wird. Ein Wert von 80 würde bewirken, dass eine Warnung ausgelöst wird, wenn das Volume zu 80 % ausgelastet ist.
* **Existing Action Group for Capacity Notifications** (Vorhandene Aktionsgruppe für Kapazitätsbenachrichtigungen): Diese Einstellung legt die Aktionsgruppe fest, die für kapazitätsbasierte Warnungen ausgelöst wird. Diese Einstellung sollte von Ihnen vorab erstellt werden. Die Aktionsgruppe kann E-Mails, SMS oder andere Benachrichtigungen senden.

Die folgende Abbildung veranschaulicht die Warnungskonfiguration:  

![Abbildung: Warnungskonfiguration mithilfe von ANFCapacityManager](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Nach der Installation von ANFCapacityManager sollte das folgende Verhalten auftreten: Wenn ein Azure NetApp Files-Kapazitätspool oder -Volume erstellt, geändert oder gelöscht wird, dann erstellt, ändert oder löscht die Logik-App automatisch eine kapazitätsbasierte Metrikwarnungsregel namens `ANF_Pool_poolname` oder `ANF_Volume_poolname_volname`. 

### <a name="manage-capacity"></a>Verwalten der Kapazität

Zusätzlich zu Überwachung und Warnungen sollten Sie auch ein Verfahren für die Anwendungskapazität etablieren, um die (ggf. erhöhte) Kapazitätsauslastung von Azure NetApp Files zu verwalten. Wenn ein Azure NetApp Files-Volume oder -Kapazitätspool fast vollständig ausgelastet ist, kann [zusätzliche Kapazität dynamisch bereitgestellt werden](azure-netapp-files-resize-capacity-pools-or-volumes.md), sodass es nicht zu Anwendungsunterbrechungen kommt. In diesem Abschnitt werden manuelle und automatisierte Ansätze für die bedarfsorientierte Erhöhung des bereitgestellten Speicherplatzes für Volumes und Kapazitätspools erläutert.
 
#### <a name="manual"></a>Manuell 

Sie können die Volume- oder Kapazitätspoolgröße manuell über das Portal oder die CLI erhöhen. 

##### <a name="portal"></a>Portal 

Sie können die [Größe eines Volumes](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) nach Bedarf ändern. Der Kapazitätsverbrauch eines Volumes wird mit der bereitgestellten Kapazität des dazugehörigen Pools verrechnet.

1. Klicken Sie auf dem Blatt „Manage NetApp Account“ auf **Volumes**.  
2. Klicken Sie mit der rechten Maustaste auf den Namen des Volumes, dessen Größe Sie anpassen möchten, oder klicken Sie am Ende der Zeile mit dem Volume auf das Symbol `…`, um das Kontextmenü anzuzeigen. 
3. Mithilfe der Kontextmenüoptionen können Sie die Größe des Volumes ändern oder es löschen.   

   ![Screenshot: Kontextmenüoptionen für ein Volume](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Screenshot: Fenster „Volumekontingent aktualisieren“](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

Manchmal ist auf dem hostenden Kapazitätspool nicht genügend Kapazität vorhanden, um die Volumegröße zu erhöhen. Sie können die [Größe des Kapazitätspools](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) jedoch in 1-TiB-Inkrementen erhöhen oder verringern. Ein Kapazitätspool darf allerdings nicht kleiner als 4 TiB sein. *Durch eine Größenänderung des Kapazitätspools wird die erworbene Azure NetApp Files-Kapazität geändert.*

1. Klicken Sie auf dem Blatt „Manage NetApp Account“ (NetApp-Konto verwalten) auf den Kapazitätspool, dessen Größe Sie anpassen möchten.
2. Klicken Sie mit der rechten Maustaste auf den Namen des Kapazitätspools, oder klicken Sie am Ende der Zeile mit dem Kapazitätspool auf das Symbol `…`, um das Kontextmenü anzuzeigen.
3. Mithilfe der Kontextmenüoptionen können Sie die Größe des Kapazitätspools ändern oder ihn löschen.    

   ![Screenshot: Kontextmenüoptionen für einen Kapazitätspool](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Screenshot: Fenster „Größe des Pools ändern“](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI oder PowerShell

Sie können die [CLI-Tools von Azure NetApp Files](azure-netapp-files-sdk-cli.md#cli-tools) wie die Azure CLI und Azure PowerShell verwenden, um die Volume- oder Kapazitätspoolgröße manuell zu ändern.  Mithilfe der folgenden beiden Befehle können Sie Azure NetApp Files-Volume- und -Poolressourcen verwalten:  

* [`az netappfiles pool`](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest&preserve-view=true)
* [`az netappfiles volume`](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest&preserve-view=true)

Öffnen Sie das Azure-Portal, und klicken Sie oben in der Menüleiste auf den Link **Azure Cloud Shell**, um Azure NetApp Files-Ressourcen mithilfe der Azure CLI zu verwalten: 

[ ![Screenshot: Zugriff auf den Link „Cloud Shell“](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png) ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Durch diese Aktion wird Azure Cloud Shell geöffnet:

[ ![Screenshot: Cloud Shell-Fenster](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png) ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

In den folgenden Beispielen werden die Befehle zum [Anzeigen](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show&preserve-view=true) und [Ändern](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-update&preserve-view=true) der Volumegröße verwendet:
 
[ ![Screenshot: Anzeigen der Volumegröße über PowerShell](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png) ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[ ![Screenshot: Ändern der Volumegröße über PowerShell](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png) ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

In den folgenden Beispielen werden die Befehle zum [Anzeigen](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-show&preserve-view=true) und [Ändern](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-update&preserve-view=true) der Kapazitätspoolgröße verwendet:

[ ![Screenshot: Anzeigen der Kapazitätspoolgröße über PowerShell](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png) ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[ ![Screenshot: Ändern der Kapazitätspoolgröße über PowerShell](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png) ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatisiert  

Sie können einen automatisierten Prozess für die Verwaltung des geänderten Verhaltens erstellen.

##### <a name="rest-api"></a>REST-API   

Die REST-API für den Azure NetApp Files-Dienst definiert HTTP-Vorgänge für Ressourcen, wie etwa das NetApp-Konto, den Kapazitätspool, die Volumes und Momentaufnahmen. Die REST-API-Spezifikation für Azure NetApp Files wird über die GitHub-Seite für den [Ressourcen-Manager für Azure NetApp Files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) veröffentlicht. Auch den [Beispielcode für die Nutzung der REST-APIs](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) finden Sie auf GitHub.

Weitere Informationen finden Sie unter [Entwickeln für Azure NetApp Files mit REST-API](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>REST-API mit PowerShell  

Die REST-API für den Azure NetApp Files-Dienst definiert HTTP-Vorgänge für Ressourcen, wie etwa das NetApp-Konto, den Kapazitätspool, die Volumes und Momentaufnahmen. Die REST-API-Spezifikation für Azure NetApp Files wird über [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) veröffentlicht.

Weitere Informationen finden Sie unter [Entwickeln für Azure NetApp Files mithilfe der REST-API und PowerShell](develop-rest-api-powershell.md).

##### <a name="capacity-management-using-anfcapacitymanager"></a>Kapazitätsverwaltung mit ANFCapacityManager

ANFCapacityManager ist ein Azure Logic Apps-Tool, das kapazitätsbasierte Warnungsregeln verwaltet. Es erhöht die Volumegröße automatisch, damit stets genügend Speicherplatz für Azure NetApp Files-Volumes vorhanden ist. Nicht nur das Senden von Warnungen ist möglich, sondern auch die automatische Erhöhung von Volume- und Kapazitätspoolgrößen, um sicherzustellen, dass Ihren Azure NetApp Files-Volumes stets genügend Speicherplatz zur Verfügung steht: 

* Wenn ein Azure NetApp Files-Volume den angegebenen Schwellenwert für die Auslastung in Prozent erreicht, kann optional das Volumekontingent (Größe) um eine Angabe zwischen 10 und 100 % erhöht werden.  
* Wenn durch die Erhöhung der Volumegröße die Kapazität des Kapazitätspools überschritten wird, in dem das Volume enthalten ist, wird auch der Kapazitätspool entsprechend vergrößert.

Sie können die folgenden essenziellen Einstellungen für die Kapazitätsverwaltung konfigurieren:  

* **AutoGrow Percent Increase** (AutoGrow-Erhöhung in Prozent): Um diese Prozentangabe soll das vorhandene Volume automatisch vergrößert werden, wenn ein festgelegter Wert für **% Full Threshold** (Schwellenwert für Auslastung in Prozent) erreicht wird. Wenn Sie den Wert 0 (null) angeben, wird das AutoGrow-Feature deaktiviert. Es wird empfohlen, einen Wert zwischen 10 und 100 anzugeben.

    ![Screenshot: Fenster „Set Volume Auto Growth Percent“ (Automatische Volumevergrößerung in Prozent festlegen)](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Häufig gestellte Fragen 

In diesem Abschnitt werden einige Fragen zur Änderung des festen Volumekontingents beantwortet. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Zählt der Speicherplatz für Momentaufnahmen zur nutzbaren oder zur bereitgestellten Kapazität eines Volumes?

Ja, die für Momentaufnahmen genutzte Kapazität zählt zum bereitgestellten Speicherplatz im Volume. Wenn das Volume vollständig ausgelastet ist, kommen zwei Maßnahmen infrage:

* Ändern Sie die Volumegröße wie in diesem Artikel beschrieben.
* Entfernen Sie ältere Momentaufnahmen, um Speicherplatz auf dem hostenden Volume freizugeben.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Hat diese Änderung zur Folge, dass Azure NetApp Files-Volumes nicht mehr automatisch vergrößert werden?

Es handelt sich um ein häufiges Missverständnis, dass *Azure NetApp Files-Volumes* automatisch vergrößert werden, wenn sie voll sind. Volumes werden unabhängig vom festgelegten Kontingent schlank mit einer Größe von 100 TiB zugewiesen. Der zugrunde liegende *Kapazitätspool* wird jedoch in Inkrementen von 1 TiB automatisch vergrößert. Diese Änderung wirkt sich auf die (sichtbare und nutzbare) *Volumegröße* für das festgelegte Kontingent aus, und *Kapazitätspools* werden nicht mehr automatisch vergrößert. Diese Änderung führt dazu, dass die clientseitige Berichterstattung zu Speicherplatz und Kapazität so präzise wie gewünscht erfolgen kann. Sie verhindert einen Endlosverbrauch von Kapazitäten.

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Wirkt sich diese Änderung auf Volumes aus, die regionsübergreifend repliziert (Vorschauphase) wurden? 

Das feste Volumekontingent wird nicht für Zielvolumes einer Replikation erzwungen.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Wirkt sich diese Änderung auf die derzeit in Azure Monitor verfügbaren Metriken aus?

Die Portalmetriken und die Azure Monitor-Statistik entsprechen exakt dem neuen Zuordnungs- und Auslastungsmodell.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Wirkt sich diese Änderung auf die Ressourcengrenzwerte für Azure NetApp Files aus?

Die Ressourcengrenzwerte für Azure NetApp Files ändern sich nicht über die in diesem Artikel beschriebenen Kontingentänderungen hinaus.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Gibt es einen Beispielworkflow für ANFCapacityManager?  

Ja. Sie finden diesen auf der GitHub-Seite [Volume AutoGrow Workflow Example (Workflowbeispiel für das AutoGrow-Feature für Volumes)](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Wird ANFCapacityManager von Microsoft unterstützt?  

[Die Logik-App ANFCapacityManager wird wie besehen bereitgestellt und nicht von NetApp oder Microsoft unterstützt.](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer) Sie können diese nach Belieben an Ihre Umgebung oder Anforderungen anpassen. Sie sollten die Funktionalität jedoch vor der Bereitstellung in unternehmenskritischen Umgebungen oder Produktionsumgebungen testen.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Wie kann ich für ANFCapacityManger einen Fehler melden oder ein Feature beantragen?
Sie können Fehler melden und Features beantragen, indem Sie auf der GitHub-Seite für [ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager/issues) auf **New Issue** (Neues Issue) klicken.

## <a name="next-steps"></a>Nächste Schritte
* [Ändern der Größe eines Kapazitätspools oder Volumes](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md) 