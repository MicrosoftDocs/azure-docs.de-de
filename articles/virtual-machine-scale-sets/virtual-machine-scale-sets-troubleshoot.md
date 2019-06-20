---
title: Problembehandlung bei der automatischen Skalierung von VM-Skalierungsgruppen | Microsoft Docs
description: Beheben Sie Probleme bei der automatischen Skalierung von VM-Skalierungsgruppen. Sie erhalten grundlegende Informationen zu typischen Problemen sowie zu ihrer Behebung.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 3308b22606e87853aad7e3d3a3995aab8d1b5401
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60803557"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Beheben von Problemen bei der automatischen Skalierung von VM-Skalierungsgruppen
**Problem:** Sie haben im Azure Resource Manager mithilfe von VM-Skalierungsgruppen eine Infrastruktur mit automatischer Skalierung erstellt, beispielsweise durch Bereitstellung der folgenden Vorlage: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. Sie haben die Skalierungsregeln festgelegt, und alles funktioniert einwandfrei, aber auch bei hoher Auslastung der virtuellen Computer erfolgt keine automatische Skalierung.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung
Folgende Punkte sollten berücksichtigt werden:

* Über wie viele vCPUs verfügt jeder virtuelle Computer, und laden Sie jede vCPU?
  Das vorstehende Beispiel einer Azure-Schnellstartvorlage enthält das Skript „do_work.php“, das eine einzelne vCPU lädt. Wenn Sie einen größeren virtuellen Computer als eine Größe mit einer vCPU wie Standard_A1 oder D1 verwenden, müssten Sie diese Last mehrmals ausführen. Überprüfen Sie die Anzahl der vCPUs Ihrer virtuellen Computer. Lesen Sie dazu die Informationen unter [Größen für virtuelle Windows-Computer in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Wie viele virtuelle Computer sind in der VM-Skalierungsgruppe enthalten? Wird auf jedem virtuellen Computer eine Last ausgeführt?
  
    Das horizontale Hochskalieren erfolgt nur, wenn die durchschnittliche CPU auf **allen** virtuellen Computer in einer Skalierungsgruppe den Schwellenwert in dem Zeitintervall überschreitet, das in den Regeln für die automatische Skalierung festgelegt ist.
* Haben Sie Skalierungsereignisse verpasst?
  
    Überprüfen Sie die Überwachungsprotokolle im Azure-Portal auf Skalierungsereignisse. Vielleicht wurde ein Vorgang zum zentralen Hochskalieren oder zum zentralen Herunterskalieren übersehen. Sie können nach „Scale“ (Skalierung) filtern.
  
    ![Überwachungsprotokolle][audit]
* Liegen die Schwellenwerte für das horizontale Herunterskalieren und das horizontale Hochskalieren weit genug auseinander?
  
    Angenommen, Sie legen eine Regel mit folgenden Bedingungen fest: Wenn die durchschnittliche CPU-Auslastung mindestens fünf Minuten lang bei über 50 Prozent liegt, wird horizontal hochskaliert, bei einer durchschnittlichen CPU-Auslastung unter 50 Prozent wird horizontal herunterskaliert. Diese Einstellung würde zu einem Fluktuationsproblem führen, wenn sich die CPU-Nutzung in der Nähe dieses Schwellenwerts bewegt, und die Gruppe würde durch die Skalierungsaktionen fortwährend vergrößert und verkleinert. Aus diesem Grund versucht der Dienst für die automatische Skalierung, die Fluktuation zu verhindern, was dazu führen kann, dass keine Skalierung stattfindet. Achten Sie daher darauf, dass die Schwellenwerte für das horizontale Hochskalieren und das horizontale Herunterskalieren weit genug auseinander liegen, um einen gewissen Skalierungsspielraum zu erhalten.
* Haben Sie Ihre eigene JSON-Vorlage geschrieben?
  
    Fehler können ganz leicht passieren. Beginnen Sie daher mit einer Vorlage wie der obigen, die sich bewährt hat, und nehmen Sie nach und nach kleine Änderungen vor. 
* Können Sie manuell horizontal herunter- und hochskalieren?
  
    Versuchen Sie, die VM-Skalierungsgruppenressource mit einer anderen Kapazitätseinstellung erneut bereitzustellen, um die Anzahl der virtuellen Computer manuell zu ändern. Hier finden Sie eine Beispielvorlage dafür: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing. Sie müssen die Vorlage gegebenenfalls bearbeiten, damit sie die gleiche Computergröße wie die Skalierungsgruppe verwendet. Wenn Sie die Anzahl der virtuellen Computer manuell ändern können, wissen Sie, dass das Problem nur im Zusammenhang mit der automatischen Skalierung besteht.
* Überprüfen Sie im [Azure-Ressourcen-Explorer](https://resources.azure.com/)
  
    Der Azure-Ressourcen-Explorer ist ein unverzichtbares Tool für die Problembehandlung, das Aufschluss über den Zustand Ihrer Azure Resource Manager-Ressourcen gibt. Klicken Sie auf Ihr Abonnement, und sehen Sie sich die Ressourcengruppe an, für die Sie die Problembehandlung ausführen. Sehen Sie unter dem Compute-Ressourcenanbieter die von Ihnen erstellte VM-Skalierungsgruppe an, und überprüfen Sie die Instanzansicht, in der der Zustand einer Bereitstellung angezeigt wird. Überprüfen Sie außerdem die Instanzansicht der virtuellen Computer in der VM-Skalierungsgruppe. Wechseln Sie anschließend zum Microsoft.Insights-Ressourcenanbieter, und überprüfen Sie die Regeln für die automatische Skalierung auf ihre Richtigkeit.
* Funktioniert die Diagnoseerweiterung, und gibt sie Leistungsdaten aus?
  
    **Update:** Die automatische Skalierung in Azure wurde erweitert und verwendet jetzt eine hostbasierte Metrikpipeline, für die keine Diagnoseerweiterung mehr installiert werden muss. Die nächsten Abschnitte treffen daher nicht mehr zu, wenn Sie mithilfe der neuen Pipeline eine Anwendung mit automatischer Skalierung erstellen. Ein Beispiel für Azure-Vorlagen, die zur Verwendung der Hostpipeline konvertiert wurden, finden Sie hier: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Die Verwendung hostbasierter Metriken für die automatische Skalierung ist aus folgenden Gründen besser geeignet:
  
  * Es sind weniger Elemente zu berücksichtigen, da keine Diagnoseerweiterungen installiert werden müssen.
  * Die Vorlagen sind einfacher. Fügen Sie einfach einer vorhandenen Vorlage für Skalierungsgruppen Insights-Regeln für die automatische Skalierung hinzu.
  * Zuverlässigere Berichterstellung und schnellerer Start neuer virtueller Computer.
    
    Der einzige Grund, aus dem Sie möglicherweise weiterhin eine Diagnoseerweiterung verwenden möchten, liegt vor, wenn Sie Berichterstellungs-/Skalierungsfunktionen für Arbeitsspeicherdiagnosen benötigen. Hostbasierte Metriken erstellen keine Berichte zum Arbeitsspeicher.
    
    Sie müssen den Rest des Artikels nur lesen, wenn Sie Diagnoseerweiterungen für die automatische Skalierung verwenden.
    
    Die automatische Skalierung in Azure Resource Manager kann über eine VM-Erweiterung namens Diagnoseerweiterung funktionieren (dies muss aber nicht mehr der Fall sein). Sie gibt Leistungsdaten an ein Speicherkonto aus, das Sie in der Vorlage definieren. Diese Daten werden dann vom Azure Monitor-Dienst aggregiert.
    
    Wenn der Insights-Dienst keine Daten aus den VMs lesen kann, soll er Ihnen erwartungsgemäß eine E-Mail senden. Beispielsweise erhalten Sie eine E-Mail, wenn die virtuellen Computer außer Betrieb sind. Achten Sie unbedingt darauf, Ihre E-Mail unter der E-Mail-Adresse zu überprüfen, die Sie beim Erstellen Ihres Azure-Kontos angegeben haben.
    
    Sie können sich außerdem die eigentlichen Daten ansehen. Sehen Sie sich das Azure-Speicherkonto in einem Cloud-Explorer an. Melden Sie sich beispielsweise beim [Visual Studio Cloud-Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2) an, und wählen Sie das verwendete Azure-Abonnement aus. Sehen Sie sich anschließend den Namen des Diagnosespeicherkontos an, auf den die Definition der Diagnoseerweiterung in Ihrer Bereitstellungsvorlage verweist.
    
    ![Cloud-Explorer][explorer]
    
    Sie sehen eine Reihe von Tabellen, in denen die Daten aus den einzelnen VMs gespeichert sind. Sehen Sie sich beispielsweise die aktuellen Zeilen mit Linux- und CPU-Metriken an. Der Visual Studio Cloud-Explorer unterstützt eine Abfragesprache, sodass Sie eine Abfrage ausführen können. Beispielsweise können Sie eine Abfrage für „Timestamp gt datetime’2016-02-02T21:20:00Z’“ ausführen, um sicherzustellen, dass Sie die aktuellsten Ereignisse abrufen. Die Zeitzone entspricht UTC. Entsprechen die dort angezeigten Daten den von Ihnen eingerichteten Skalierungsregeln? Im folgenden Beispiel ist die CPU-Auslastung für Computer 20 in den letzten fünf Minuten auf 100 Prozent gestiegen.
    
    ![Speichertabellen][tables]
    
    Wenn keine Daten vorhanden sind, impliziert dies, dass ein Problem mit der Diagnoseerweiterung auf den virtuellen Computern vorliegt. Sind Daten vorhanden, weist dies darauf hin, dass entweder ein Problem mit den Skalierungsregeln oder mit dem Insights-Dienst vorliegt. Überprüfen Sie den [Azure-Status](https://azure.microsoft.com/status/).
    
    Sobald Sie diese Schritte absolviert haben, können Sie die folgenden Ressourcen nutzen, sollten immer noch Probleme mit der automatischen Skalierung auftreten: 
    * Lesen Sie die Foren auf [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows) oder [Stack Overflow](https://stackoverflow.com/questions/tagged/azure) 
    * Melden Sie einen Supportanruf an. Bereiten Sie das Teilen der Vorlage und einer Ansicht Ihrer Leistungsdaten vor.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
