---
title: Behandeln von Wartungsbenachrichtigungen für virtuelle Windows-Computer in Azure | Microsoft-Dokumentation
description: Zeigen Sie Wartungsbenachrichtigungen für in Azure ausgeführte virtuelle Windows-Computer an, und starten Sie eine Self-Service-Wartung.
services: virtual-machines-windows
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/30/2019
ms.author: shants
ms.openlocfilehash: fccc99f78d038a5f96b9dfe01b575dedcdcb4cdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65405608"
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Behandeln von Benachrichtigungen zu geplanten Wartungen für virtuelle Windows-Computer

Azure führt regelmäßig Updates durch, um die Zuverlässigkeit, Leistung und Sicherheit der Hostinfrastruktur für virtuelle Computer zu verbessern. Zu Updates zählen Änderungen wie das Patchen der Hostumgebung oder das Upgraden und die Außerbetriebnahme von Hardware. Die meisten dieser Updates werden ohne Auswirkungen auf die gehosteten virtuellen Computer durchgeführt. Es gibt jedoch Ausnahmen:

- Wenn die Wartung keinen Neustart erfordert, verwendet Azure eine direkte Migration, um den virtuellen Computer anzuhalten, während der Host aktualisiert wird. Diese Wartungsvorgänge ohne Neustart werden einzeln für jede Fehlerdomäne angewendet, und das Fortschreiten wird beendet, wenn Warnsignale zur Integrität empfangen werden. 

- Wenn die Wartung einen Neustart erfordert, werden Sie in einer Benachrichtigung über den geplanten Wartungstermin informiert. In diesen Fällen steht Ihnen ein Zeitfenster zur Verfügung (normalerweise 30 Tage), in dem Sie die Wartung zu einem für Sie günstigen Zeitpunkt selbst starten können.


Geplante Wartungen, die einen Neustart erfordern, werden in Wellen geplant. Jede Welle hat einen anderen Umfang (Regionen).

- Eine Welle beginnt mit einer Kundenbenachrichtigung. Die Benachrichtigung wird standardmäßig an Abonnementbesitzer und -mitbesitzer gesendet. Für die Benachrichtigungen können mit Azure-[Aktivitätsprotokollwarnungen](../../azure-monitor/platform/activity-logs-overview.md) weitere Empfänger und Nachrichtenoptionen wie E-Mail, SMS und Webhooks hinzugefügt werden.  
- Zum Zeitpunkt der Benachrichtigung wird ein *Self-Service-Zeitfenster* verfügbar gemacht. Während dieses Zeitfensters von normalerweise 30 Tagen können Sie ermitteln, welche Ihrer virtuellen Computer von der Welle betroffen sind, und die Wartung gemäß eigenen Planungsanforderungen starten.
- Im Anschluss an das Self-Service-Zeitfenster beginnt das *Zeitfenster für die geplante Wartung*. Irgendwann in diesem Zeitfenster plant Azure die erforderliche Wartung und wendet sie auf Ihren virtuellen Computer an. 

Mit den beiden Zeitfenstern möchten wir Ihnen einerseits genügend Zeit geben, um die Wartung zu initiieren und Ihren virtuellen Computer neu zu starten, und Sie andererseits darüber informieren, wann Azure die Wartung automatisch startet.


Sie können das Azure-Portal, PowerShell, die REST-API und die Befehlszeilenschnittstelle verwenden, um die Wartungszeitfenster für Ihre virtuellen Computer abzufragen und eine Self-Service-Wartung zu starten.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Sollten Sie die Wartung während des Self-Service-Fensters starten?  

Die folgenden Hinweise sollen Sie bei der Entscheidung unterstützen, ob Sie diese Funktion nutzen und die Wartung zu einem selbst gewählten Zeitpunkt starten sollten.

> [!NOTE] 
> Die Self-Service-Wartung ist unter Umständen nicht für alle virtuellen Computer verfügbar. Um zu ermitteln, ob die proaktive erneute Bereitstellung für Ihren virtuellen Computer verfügbar ist, überprüfen Sie, ob als Wartungsstatus **Jetzt starten** angezeigt wird. Die Self-Service-Wartung ist derzeit nicht für Cloud Services (Web-/Workerrolle) und Service Fabric verfügbar.


Self-Service-Wartung wird für Bereitstellungen mit **Verfügbarkeitsgruppen** nicht empfohlen, da es sich dabei um hoch verfügbare Setups handelt, bei denen jeweils nur immer eine Updatedomäne betroffen ist. 
- Überlassen Sie Azure das Auslösen der Wartung. Beachten Sie bei einer Wartung, die einen Neustart erfordert, dass die Wartung für jede Updatedomäne einzeln erfolgt, die Updatedomänen die Wartung nicht unbedingt in sequenzieller Reihenfolge erhalten und zwischen den Updatedomänen eine Pause von 30 Minuten liegt. 
- Wenn ein vorübergehender Kapazitätsverlust (1/Updatedomänenanzahl) ein Problem darstellt, können Sie zum Ausgleich während der Wartung einfach zusätzliche Instanzen zuweisen.
- Bei einer Wartung, die keinen Neustart erfordert, werden Updates auf der Fehlerdomänenebene angewendet.
    
Verwenden Sie Self-Service-Wartung **nicht** in den folgenden Szenarien: 
- Wenn Sie Ihre virtuellen Computer häufig herunterfahren (entweder manuell, mithilfe von DevTest Labs, mit der Funktion zum automatischen Herunterfahren oder gemäß eines Zeitplans), wird dadurch unter Umständen der Wartungsstatus zurückgesetzt, was zusätzliche Ausfallzeiten zur Folge haben kann.
- Bei virtuellen Computern mit kurzer Lebensdauer, die vor Ende der Wartungsaktion gelöscht werden 
- Bei Workloads, für die umfangreiche Zustandsinformationen auf dem lokalen (flüchtigen) Datenträger gespeichert sind, der bei einem Update erhalten bleiben soll 
- Bei häufiger Änderung der Größe Ihrer virtuellen Computer, da dadurch der Wartungsstatus wiederhergestellt werden kann
- Wenn Sie 15 Minuten vor dem Herunterfahren für die Wartung geplante Ereignisse übernommen haben, die ein proaktives Failover oder das ordnungsgemäße Herunterfahren Ihrer Workload ermöglichen

**Verwenden** Sie die Self-Service-Wartung, wenn Sie den virtuellen Computer während der geplanten Wartungsphase unterbrechungsfrei ausführen möchten und keiner der oben aufgeführten Punkte zutrifft. 

Verwenden Sie Self-Service-Wartung am besten in den folgenden Fällen:

- Sie müssen den Vorgesetzten oder Endkunden ein exaktes Wartungsfenster mitteilen. 
- Sie müssen die Wartung bis zu einem bestimmten Termin abgeschlossen haben. 
- Sie müssen die Reihenfolge der Wartung steuern (etwa bei einer Anwendung mit mehreren Ebenen), um die Wiederherstellung gewährleisten zu können.
- Sie benötigen für den virtuellen Computer mehr als 30 Minuten Wiederherstellungszeit zwischen zwei Updatedomänen (UDs). Um die Zeit zwischen Updatedomänen zu steuern, müssen Sie auf Ihren virtuellen Computern die Wartung der Updatedomänen jeweils nacheinander auslösen.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Überprüfen des Wartungsstatus mithilfe von PowerShell

Sie können auch mithilfe von Azure PowerShell prüfen, wann die Wartung Ihrer virtuellen Computer geplant ist. Informationen zur geplanten Wartung können mit dem Cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) unter Verwendung des Parameters `-status` ermittelt werden.
 
Wartungsinformationen werden nur zurückgegeben, wenn eine Wartung geplant ist. Ist keine Wartung für den virtuellen Computer geplant, gibt das Cmdlet keine Wartungsinformationen zurück. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
Get-AzVM -ResourceGroupName rgName -Name vmName -Status
```

Unter „MaintenanceRedeployStatus“ werden folgende Eigenschaften zurückgegeben: 

| Wert | BESCHREIBUNG   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Gibt an, ob Sie zum aktuellen Zeitpunkt die Wartung für den virtuellen Computer starten können. |
| PreMaintenanceWindowStartTime         | Der Anfang des Self-Service-Wartungszeitfensters, in dem Sie die Wartung für Ihren virtuellen Computer initiieren können. |
| PreMaintenanceWindowEndTime           | Das Ende des Self-Service-Wartungszeitfensters, in dem Sie die Wartung für Ihren virtuellen Computer initiieren können. |
| MaintenanceWindowStartTime            | Der Anfang der geplanten Wartung, zu dem Azure die Wartung für Ihren virtuellen Computer initiiert |
| MaintenanceWindowEndTime              | Das Ende des geplanten Wartungszeitfensters, in dem Azure die Wartung für Ihren virtuellen Computer initiiert |
| LastOperationResultCode               | Das Ergebnis des letzten Wartungsinitiierungsversuchs für den virtuellen Computer. |



Sie können auch den Wartungsstatus für alle virtuellen Computer in einer Ressourcengruppe abrufen. Verwenden Sie hierzu [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) ohne Angabe eines virtuellen Computers.
 
```powershell
Get-AzVM -ResourceGroupName rgName -Status
```

Die folgende PowerShell-Funktion gibt auf der Grundlage Ihrer Abonnement-ID eine Liste mit virtuellen Computern aus, für die eine Wartung geplant ist.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Starten der Wartung für Ihren virtuellen Computer mithilfe von PowerShell

Der folgende Befehl enthält Informationen aus der Funktion des vorherigen Abschnitts und startet die Wartung für einen virtuellen Computer, sofern **IsCustomerInitiatedMaintenanceAllowed** auf „true“ festgelegt ist.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klassische Bereitstellungen

Wenn Sie ältere virtuelle Computer besitzen, die mit dem klassischen Bereitstellungsmodell bereitgestellt wurden, können Sie mit PowerShell virtuelle Computer abfragen und die Wartung initiieren.

Geben Sie zum Abrufen des Wartungsstatus eines virtuellen Computers Folgendes ein:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Geben Sie zum Starten der Wartung auf dem klassischen virtuellen Computer Folgendes ein:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>Häufig gestellte Fragen


**F: Wozu müssen Sie meine virtuellen Computer jetzt neu starten?**

**A:** Während die Mehrzahl der Updates und Upgrades für die Azure-Plattform die Verfügbarkeit des virtuellen Computers nicht beeinflusst, gibt es Fälle, in denen wir den Neustart in Azure gehosteter virtueller Computer nicht vermeiden können. Bei uns sind mehrere Änderungen zusammengekommen, die den Neustart unserer Server erfordern und damit auch zum Neustart virtueller Computer führen.

**F: Bin ich auf der sicheren Seite, wenn ich ihre Empfehlungen für Hochverfügbarkeit mit Verwendung einer Verfügbarkeitsgruppe befolge?**

**A:** In einer Verfügbarkeitsgruppe oder VM-Skalierungsgruppe bereitgestellte virtuelle Computer werden wie Updatedomänen (UD) behandelt. Bei der Wartung berücksichtigt Azure die UD-Einschränkung und startet virtuelle Computer nicht von einer anderen UD (innerhalb derselben Verfügbarkeitsgruppe) aus neu.  Azure wartet auch mindestens 30 Minuten vor dem Wechsel zur nächsten Gruppe von virtuellen Computern. 

Weitere Informationen zu Hochverfügbarkeit finden Sie unter [Regionen und Verfügbarkeit für virtuelle Computer in Azure](regions-and-availability.MD).

**F: Wie werde ich über eine geplante Wartung benachrichtigt?**

**A:** Eine geplante Wartungsaktion beginnt mit dem Festlegen eines Zeitplans für eine oder mehrere Azure-Regionen. Bald danach wird eine E-Mail-Benachrichtigung an die Abonnementbesitzer gesendet (pro Abonnement eine E-Mail). Zusätzliche Kanäle und Empfänger für diese Benachrichtigung können mit Aktivitätsprotokollwarnungen konfiguriert werden. Falls Sie einen virtuellen Computer in einer Region bereitstellen, wo bereits Wartung geplant ist, erhalten Sie die Benachrichtigung nicht, sondern müssen vielmehr den Wartungsstatus des virtuellen Computers überprüfen.

**F: Mir wird weder im Portal noch über PowerShell oder über die Befehlszeilenschnittstelle ein Hinweis auf eine geplante Wartung angezeigt. Was ist schiefgelaufen?**

**A:** Informationen im Zusammenhang mit geplanter Wartung stehen während einer geplanten Wartungsaktion nur für die virtuellen Computer zur Verfügung, die davon betroffen sind. D.h., wenn Ihnen keine Daten angezeigt werden, ist es möglich, dass die Wartungsaktion bereits abgeschlossen (oder nicht gestartet) wurde, bzw. Ihr virtueller Computer bereits auf einem aktualisierten Server gehostet wird.

**F: Kann ich feststellen, wann genau mein virtueller Computer betroffen sein wird?**

**A:** Bei Festlegung des Zeitplans definieren wir ein Zeitfenster von einigen Tagen. Allerdings ist die genaue Reihenfolge von Servern (und VMs) innerhalb dieses Zeitfensters unbekannt. Kunden, die die genaue Zeit für ihre virtuellen Computer wissen möchten, können [geplante Ereignisse](scheduled-events.md) verwenden, aus dem virtuellen Computer heraus abfragen und erhalten 15 Minuten vor dem VM-Neustart eine Benachrichtigung.

**F: Wie lange dauert es, meinen virtuellen Computer neu zu starten?**

**A:**  Je nach Größe Ihres virtuellen Computers kann der Neustart während des Self-Service-Wartungsfensters mehrere Minuten dauern. Die von Azure im geplanten Wartungsfenster initiierten Neustarts dauern in der Regel etwa 25 Minuten. Beachten Sie, dass Ihnen bei Verwendung von Cloud Services (Web-/Workerrolle), VM-Skalierungsgruppen oder Verfügbarkeitsgruppen während des geplanten Wartungsfensters zwischen den einzelnen Gruppen von virtuellen Computern (UD) 30 Minuten zur Verfügung stehen. 

**F: Wie sind die Erfahrungen mit VM-Skalierungsgruppen?**

**A:** Es ist nun eine geplante Wartung für VM-Skalierungsgruppen verfügbar. Anleitungen zum Initiieren der Self-Service-Wartung finden Sie im Dokument [zur geplanten VMSS-Wartung](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md).

**F: Wie sind die Erfahrungen mit Cloud Services (Web-/Workerrolle) und Service Fabric?**

**A:** Diese Plattformen sind zwar von geplanten Wartungen betroffen, die Sicherheit der Kunden, die diese Plattformen verwenden, ist jedoch gewährleistet, da nur jeweils virtuelle Computer in einer einzelnen Upgradedomäne (UD) betroffen sind. Die Self-Service-Wartung ist derzeit nicht für Cloud Services (Web-/Workerrolle) und Service Fabric verfügbar.

**F: Auf meinen virtuellen Computern werden keine Wartungsinformationen angezeigt. Welche Probleme sind aufgetreten?**

**A:** Es gibt verschiedene Gründe, warum Sie keine Wartungsinformationen auf Ihren virtuellen Computern sehen:
1.  Sie verwenden ein Abonnement, das als Microsoft-intern markiert ist.
2.  Ihre virtuellen Computer sind nicht für die Wartung eingeplant. Es könnte sein, dass die Wartungsaktion beendet, abgebrochen oder so geändert wurde, dass Ihre virtuellen Computer nicht mehr davon betroffen sind.
3.  Sie haben Ihrer VM-Listenansicht nicht die Spalte **Wartung** hinzugefügt. Obwohl wir diese Spalte der Standardansicht hinzugefügt haben, müssen Kunden, die die Anzeige nicht standardmäßiger Spalten konfiguriert haben, die Spalte **Wartung** ihrer VM-Listenansicht manuell hinzufügen.

**F: Meine VM ist zum zweiten Mal für die Wartung eingeplant. Warum?**

**A:** In mehreren Anwendungsfällen wird Ihr virtueller Computer zur Wartung eingeplant, nachdem Sie die erneute Bereitstellung nach der Wartung bereits abgeschlossen haben:
1.  Wir haben die Wartungsaktion abgebrochen und einen Neustart mit anderer Nutzlast vorgenommen. Es könnte sein, dass wir fehlerhafte Nutzlast erkannt haben und einfach eine zusätzliche Nutzlast bereitstellen müssen.
2.  Die *Dienstreparatur* Ihres virtuellen Computers wurde aufgrund eines Hardwarefehlers auf einem anderen Knoten durchgeführt.
3.  Sie haben ausgewählt, den virtuellen Computer zu beenden (freizugeben) und neu zu starten.
4.  Sie haben **automatisches Herunterfahren** für den virtuellen Computer aktiviert.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie sich über einen virtuellen Computer mithilfe von [geplanten Ereignissen](scheduled-events.md) für Wartungsereignisse registrieren.
