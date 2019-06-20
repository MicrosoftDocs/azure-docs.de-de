---
title: Wartungsbenachrichtigungen für VM-Skalierungsgruppen in Azure | Microsoft Docs
description: Anzeigen von Wartungsbenachrichtigungen für VM-Skalierungsgruppen in Azure und Starten einer Self-Service-Wartung.
services: virtual-machine-scale-sets
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: shants
ms.openlocfilehash: 31d4829c6adaf4bd5392ef393dcaefbeb7dc6255
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60618454"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Benachrichtigungen zu geplanten Wartungen für VM-Skalierungsgruppen


Azure führt regelmäßig Updates aus, um die Zuverlässigkeit, Leistung und Sicherheit der Hostinfrastruktur für virtuelle Computer (VMs) zu verbessern. Zu Updates zählen z.B. Änderungen wie das Patchen der Hostingumgebung oder das Upgraden und die Außerbetriebnahme von Hardware. Die meisten Updates wirken sich nicht auf die gehosteten virtuellen Computer aus. Updates wirken sich jedoch in den folgenden Szenarien auf virtuelle Computer aus:

- Wenn die Wartung keinen Neustart erfordert, verwendet Azure eine direkte Migration, um den virtuellen Computer anzuhalten, während der Host aktualisiert wird. Wartungsvorgänge, die keinen Neustart erfordern, werden auf eine Fehlerdomäne nach der anderen angewendet. Die Vorgänge werden beendet, wenn ein Integritätswarnungssignal empfangen wird.

- Wenn die Wartung einen Neustart erfordert, erhalten Sie eine Benachrichtigung, die Sie über den geplanten Wartungstermin informiert. In diesen Fällen steht Ihnen ein Zeitfenster zur Verfügung, in dem Sie die Wartung zu einem für Sie günstigen Zeitpunkt selbst starten können.


Geplante Wartungen, die einen Neustart erfordern, werden in Wellen geplant. Jede Welle bezieht sich auf einen anderen Umfang (Regionen):

- Eine Welle beginnt mit einer Kundenbenachrichtigung. Die Benachrichtigung wird standardmäßig an den Abonnementbesitzer und die -mitbesitzer gesendet. Den Benachrichtigungen können mit Azure-[Aktivitätsprotokollwarnungen](../azure-monitor/platform/activity-logs-overview.md) Empfänger und Nachrichtenoptionen wie E-Mail, SMS und Webhooks hinzugefügt werden.  
- Mit der Benachrichtigung wird ein *Self-Service-Zeitfenster* bereitgestellt. Während dieses Zeitfensters können Sie ermitteln, welche Ihrer virtuellen Computer in der Welle enthalten sind. Sie können die Wartung proaktiv gemäß ihren eigenen Zeitplanungsanforderurngen starten.
- Im Anschluss an das Self-Service-Zeitfenster beginnt das *Zeitfenster für die geplante Wartung*. Irgendwann in diesem Zeitfenster plant Azure die erforderliche Wartung und wendet sie auf Ihren virtuellen Computer an. 

Mit den beiden Zeitfenstern möchten wir Ihnen einerseits genügend Zeit geben, um die Wartung zu initiieren und Ihren virtuellen Computer neu zu starten, und Sie andererseits darüber informieren, wann Azure die Wartung automatisch startet.

Sie können das Azure-Portal, PowerShell, die REST-API oder die Azure CLI verwenden, um Wartungszeitfenster für Ihre Skalierungsgruppen-VMs abzufragen und eine Self-Service-Wartung zu starten.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Sollten Sie die Wartung während des Self-Service-Fensters starten?  

Die folgenden Richtlinien können Sie bei der Entscheidung unterstützen, ob Sie die Wartung zu einem Zeitpunkt starten, den Sie auswählen.

> [!NOTE] 
> Die Self-Service-Wartung ist unter Umständen nicht für alle virtuellen Computer verfügbar. Um zu ermitteln, ob die proaktive erneute Bereitstellung für Ihren virtuellen Computer verfügbar ist, überprüfen Sie, ob als Wartungsstatus **Jetzt starten** angezeigt wird. Die Self-Service-Wartung ist zurzeit nicht für Azure Cloud Services (Web-/Workerrolle) und Azure Service Fabric verfügbar.


Self-Service-Wartung wird für Bereitstellungen, die *Verfügbarkeitsgruppen* verwenden, nicht empfohlen. Bei Verfügbarkeitsgruppen handelt es sich um hoch verfügbare Setups, in denen nur eine Updatedomäne zu einem beliebigen Zeitpunkt betroffen ist. Für Verfügbarkeitsgruppen gilt Folgendes:

- Überlassen Sie Azure das Auslösen der Wartung. Für eine Wartung, die einen Neustart erfordert, wird die Wartung auf eine Updatedomäne nach der anderen angewendet. Für Updatedomänen erfolgt die Wartung nicht unbedingt sequenziell. Zwischen den einzelnen Updatedomänen liegt eine Pause von 30 Minuten.
- Wenn ein vorübergehender Kapazitätsverlust (1/Updatedomänenanzahl) ein Problem darstellt, können Sie zum Ausgleich während der Wartung einfach zusätzliche Instanzen zuweisen.
- Bei einer Wartung, die keinen Neustart erfordert, werden Updates auf der Fehlerdomänenebene angewendet. 
    
Verwenden Sie Self-Service-Wartung **nicht** in den folgenden Szenarien: 

- Wenn Sie Ihre virtuellen Computer häufig entweder manuell, mithilfe von DevTest Labs, mit der Funktion zum automatischen Herunterfahren oder gemäß einem Zeitplan herunterfahren. Die Self-Service-Wartung kann in diesen Szenarien den Wartungsstatus zurücksetzen und zusätzliche Ausfallzeiten verursachen.
- Bei virtuellen Computern mit kurzer Lebensdauer, die vor Ende der Wartungsaktion gelöscht werden 
- Bei Workloads, für die umfangreiche Zustandsinformationen auf dem lokalen (flüchtigen) Datenträger gespeichert sind, die bei einem Update erhalten bleiben sollen. 
- Wenn Sie die Größe Ihres virtuellen Computers häufig ändern. Dieses Szenario kann den Wartungsstatus zurücksetzen. 
- Wenn Sie 15 Minuten vor dem Herunterfahren für die Wartung geplante Ereignisse übernommen haben, die ein proaktives Failover oder das ordnungsgemäße Herunterfahren Ihres Workloads ermöglichen.

**Verwenden** Sie die Self-Service-Wartung, wenn Sie den virtuellen Computer während der geplanten Wartungsphase unterbrechungsfrei ausführen möchten und keiner der oben aufgeführten Punkte zutrifft. 

Verwenden Sie Self-Service-Wartung am besten in den folgenden Fällen:

- Sie müssen den Vorgesetzten oder Kunden ein genaues Wartungsfenster mitteilen. 
- Sie müssen die Wartung bis zu einem bestimmten Termin abgeschlossen haben. 
- Sie müssen die Reihenfolge der Wartung steuern (etwa bei einer Anwendung mit mehreren Ebenen), um die Wiederherstellung gewährleisten zu können.
- Sie benötigen für den virtuellen Computer mehr als 30 Minuten Wiederherstellungszeit zwischen zwei Updatedomänen. Um die Zeit zwischen Updatedomänen zu steuern, müssen Sie auf Ihren virtuellen Computern die Wartung der Updatedomänen jeweils nacheinander auslösen.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Anzeigen von VM-Skalierungsgruppen im Portal, die von der Wartung betroffen sind

Nachdem eine geplante Wartungsaktion geplant wurde, können Sie über das Azure-Portal die Liste mit den VM-Skalierungsgruppen anzeigen, die von der anstehenden Wartungsaktion betroffen sind. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im linken Menü **Alle Dienste** aus, und wählen Sie dann **VM-Skalierungsgruppen** aus.
3. Wählen Sie unter **VM-Skalierungsgruppen** die Option **Spalten bearbeiten** aus, um die Liste der verfügbaren Spalten zu öffnen.
4. Wählen Sie im Abschnitt **Verfügbare Spalten** die Option **Self-Service-Wartung** aus, und verschieben Sie diese in die Liste **Ausgewählte Spalten**. Wählen Sie **Übernehmen**.  

    Um das Element **Self-Service-Wartung** leichter zu finden, können Sie die Dropdownoption im Abschnitt **Verfügbare Spalten** von **Alle** in **Eigenschaften** ändern.

Die Spalte **Self-Service-Wartung** wird nun in der Liste der VM-Skalierungsgruppen angezeigt. Jede VM-Skalierungsgruppe kann einen der folgenden Werte für die Spalte „Self-Service-Wartung“ aufweisen:

| Wert | BESCHREIBUNG |
|-------|-------------|
| Ja | Mindestens ein virtueller Computer in Ihrer VM-Skalierungsgruppe befindet sich in einem Self-Service-Wartungsfenster. Sie können die Wartung jederzeit in diesem Self-Service-Zeitfenster starten. | 
| Nein | Es gibt keine virtuellen Computer in einem Self-Service-Fenster in der betroffenen VM-Skalierungsgruppe. | 
| - | Ihre VM-Skalierungsgruppen sind nicht Teil einer geplanten Wartungsaktion.| 

## <a name="notification-and-alerts-in-the-portal"></a>Benachrichtigungen und Warnungen im Portal

Azure sendet eine E-Mail an die Gruppe der Abonnementbesitzer und -mitbesitzer, um sie über den Zeitplan für eine geplante Wartung zu informieren. Sie können Azure-Aktivitätsprotokollwarnungen erstellen, um Empfänger und Kanäle hinzuzufügen. Weitere Informationen finden Sie unter [Überwachen der Abonnementaktivität mit dem Azure-Aktivitätsprotokoll](../azure-monitor/platform/activity-logs-overview.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie die Option **Überwachen** im Menü auf der linken Seite aus. 
3. Wählen Sie im Bereich **Überwachen – Warnungen (klassisch)** die Option **+ Aktivitätsprotokollwarnung hinzufügen** aus.
4. Wählen Sie auf der Seite **Aktivitätsprotokollwarnung hinzufügen** die angeforderten Informationen aus, oder geben Sie sie ein. Stellen Sie unter **Kriterien** sicher, dass Sie die folgenden Werte festgelegt haben:
   - **Ereigniskategorie**: Wählen Sie **Service Health**.
   - **Dienste**: Wählen Sie **Virtual Machine Scale Sets und Virtual Machines** aus.
   - **Typ**: Wählen Sie **Geplante Wartung** aus. 
    
Weitere Informationen zum Konfigurieren von Aktivitätsprotokollwarnungen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen](../azure-monitor/platform/activity-log-alerts.md).
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Starten der Wartung für Ihre VM-Skalierungsgruppe über das Portal

In der Übersicht über VM-Skalierungsgruppen können Sie auch weitere wartungsbezogene Details anzeigen. Wenn mindestens ein virtueller Computer in der VM-Skalierungsgruppe in der geplanten Wartungsaktion enthalten ist, wird im oberen Bereich der Seite ein neues Benachrichtigungsmenüband hinzugefügt. Wählen Sie das Benachrichtigungsmenüband aus, um zur Seite **Wartung** zu navigieren. 

Auf der Seite **Wartung** können Sie erkennen, welche VM-Instanz von der geplanten Wartung betroffen ist. Aktivieren Sie zum Starten der Wartung das Kontrollkästchen, das dem betroffenen virtuellen Computer entspricht. Wählen Sie dann **Wartung starten** aus.

Nach dem Starten der Wartung werden die betroffenen virtuellen Computer in Ihrer VM-Skalierungsgruppe gewartet und stehen vorübergehend nicht zur Verfügung. Wenn Sie das Self-Service-Zeitfenster verpasst haben, wird das Zeitfenster, in dem Ihre VM-Skalierungsgruppe durch Azure gewartet wird, weiterhin angezeigt.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Überprüfen des Wartungsstatus mithilfe von PowerShell

Sie können mithilfe von Azure PowerShell einsehen, wann die Wartung von virtuellen Computern in Ihrer VM-Skalierungsgruppe geplant ist. Informationen zur geplanten Wartung können mit dem Cmdlet [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) unter Verwendung des Parameters `-InstanceView` ermittelt werden.
 
Wartungsinformationen werden nur zurückgegeben, wenn eine Wartung geplant ist. Ist keine Wartung geplant, die Auswirkungen auf die VM-Instanz besitzt, gibt das Cmdlet keine Wartungsinformationen zurück. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Unter **MaintenanceRedeployStatus** werden folgende Eigenschaften zurückgegeben: 

| Wert | Beschreibung   |

|-------|---------------| | IsCustomerInitiatedMaintenanceAllowed | Zeigt an, ob Wartung auf der VM zu diesem Zeitpunkt gestartet werden kann. | | PreMaintenanceWindowStartTime         | Der Anfang des Self-Service-Wartungszeitfensters, in dem Sie die Wartung für Ihren virtuellen Computer initiieren können. | | PreMaintenanceWindowEndTime           | Das Ende des Self-Service-Wartungszeitfensters, in dem Sie die Wartung für Ihren virtuellen Computer initiieren können. | | MaintenanceWindowStartTime            | Der Anfang der geplanten Wartung, zu dem Azure die Wartung für Ihren virtuellen Computer initiiert. | | MaintenanceWindowEndTime              | Das Ende des geplanten Wartungszeitfensters, in dem Azure die Wartung für Ihren virtuellen Computer initiiert. | | LastOperationResultCode               | Das Ergebnis des letzten Wartungsinitiierungsversuchs für den virtuellen Computer. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Starten der Wartung für Ihre VM-Instanz mithilfe von PowerShell

Sie können die Wartung eines virtuellen Computers starten, wenn **IsCustomerInitiatedMaintenanceAllowed** auf **TRUE** festgelegt wurde. Verwenden Sie das Cmdlet [Set-AzVmss](/powershell/module/az.compute/set-azvmss) mit dem Parameter `-PerformMaintenance`.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Überprüfen des Wartungsstatus mithilfe der CLI

Informationen zu geplanten Wartungen können mithilfe von [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances) angezeigt werden.
 
Wartungsinformationen werden nur zurückgegeben, wenn eine Wartung geplant ist. Ist keine Wartung geplant, die Auswirkungen auf die VM-Instanz besitzt, gibt das Cmdlet keine Wartungsinformationen zurück. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Unter **MaintenanceRedeployStatus** für die einzelnen VM-Instanzen werden die folgenden Eigenschaften zurückgegeben: 

| Wert | Beschreibung   |

|-------|---------------| | IsCustomerInitiatedMaintenanceAllowed | Zeigt an, ob Wartung auf der VM zu diesem Zeitpunkt gestartet werden kann. | | PreMaintenanceWindowStartTime         | Der Anfang des Self-Service-Wartungszeitfensters, in dem Sie die Wartung für Ihren virtuellen Computer initiieren können. | | PreMaintenanceWindowEndTime           | Das Ende des Self-Service-Wartungszeitfensters, in dem Sie die Wartung für Ihren virtuellen Computer initiieren können. | | MaintenanceWindowStartTime            | Der Anfang der geplanten Wartung, zu dem Azure die Wartung für Ihren virtuellen Computer initiiert. | | MaintenanceWindowEndTime              | Das Ende des geplanten Wartungszeitfensters, in dem Azure die Wartung für Ihren virtuellen Computer initiiert. | | LastOperationResultCode               | Das Ergebnis des letzten Wartungsinitiierungsversuchs für den virtuellen Computer. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Starten der Wartung für Ihre VM-Instanz mithilfe der CLI

Der folgende Aufruf initiiert die Wartung für eine VM-Instanz, wenn `IsCustomerInitiatedMaintenanceAllowed` auf **TRUE** festgelegt ist:

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Häufig gestellte Fragen

**F: Warum müssen Sie meine virtuellen Computer jetzt neu starten?**

**A:** Obwohl die meisten Updates und Upgrades der Azure-Plattform die VM-Verfügbarkeit nicht beeinträchtigen, können wir in einigen Fällen einen Neustart der in Azure gehosteten virtuellen Computer nicht vermeiden. Bei uns haben sich mehrere Änderungen angesammelt, die den Neustart unserer Server erfordern und damit auch zum Neustart virtueller Computer führen.

**F: Bin ich auf der sicheren Seite, wenn ich ihre Empfehlungen für Hochverfügbarkeit mit Verwendung einer Verfügbarkeitsgruppe befolge?**

**A:** In einer Verfügbarkeitsgruppe oder VM-Skalierungsgruppe bereitgestellte virtuelle Computer verwenden Updatedomänen. Bei der Wartung berücksichtigt Azure die Updatedomäneneinschränkung und startet virtuelle Computer aus einer anderen Updatedomäne (innerhalb derselben Verfügbarkeitsgruppe) nicht neu. Azure wartet auch mindestens 30 Minuten bis zum Wechsel zur nächsten Gruppe von virtuellen Computern. 

Weitere Informationen zu Hochverfügbarkeit finden Sie unter [Regionen und Verfügbarkeit für virtuelle Computer in Azure](../virtual-machines/windows/regions-and-availability.md).

**F: Wie kann ich über eine geplante Wartung benachrichtigt werden?**

**A:** Eine geplante Wartungsaktion beginnt mit dem Festlegen eines Zeitplans für eine oder mehrere Azure-Regionen. Bald danach wird eine E-Mail-Benachrichtigung an die Abonnementbesitzer gesendet (pro Abonnement eine E-Mail). Sie können Kanäle und Empfänger für diese Benachrichtigung mit Aktivitätsprotokollwarnungen hinzufügen. Wenn Sie einen virtuellen Computer in einer Region bereitstellen, in der eine Wartung bereits geplant ist, erhalten Sie die Benachrichtigung nicht. Überprüfen Sie stattdessen den Wartungsstatus des virtuellen Computers.

**F: Mir wird weder im Portal noch über PowerShell oder über die CLI ein Hinweis auf eine geplante Wartung angezeigt. Woran kann das liegen?**

**A:** Informationen im Zusammenhang mit geplanter Wartung stehen während einer geplanten Wartungsaktion nur für die virtuellen Computer zur Verfügung, die davon betroffen sind. Wenn keine Daten angezeigt werden, ist die Wartungswelle möglicherweise bereits beendet (oder wurde noch nicht gestartet), oder Ihr virtueller Computer wird bereits auf einem aktualisierten Server gehostet.

**F: Gibt es eine Möglichkeit zum genauen Feststellen, wann mein virtueller Computer betroffen ist?**

**A:** Bei der Festlegung des Zeitplans definieren wir ein Zeitfenster von einigen Tagen. Die genaue Reihenfolge von Servern (und virtuellen Computern) innerhalb dieses Zeitfensters ist nicht bekannt. Wenn Sie die genaue Zeit wissen möchten, zu der Ihre virtuellen Computer aktualisiert werden, können Sie [geplante Ereignisse](../virtual-machines/windows/scheduled-events.md) verwenden. Wenn Sie geplante Ereignisse verwenden, können Sie eine Abfrage aus dem virtuellen Computer ausführen und 15-Minuten vor dem Neustart eines virtuellen Computers eine Benachrichtigung erhalten.

**F: Wie lange dauert es, meinen virtuellen Computer neu zu starten?**

**A:**  Je nach Größe Ihres virtuellen Computers kann der Neustart während des Self-Service-Wartungsfensters mehrere Minuten dauern. Die von Azure im geplanten Wartungsfenster initiierten Neustarts dauern in der Regel etwa 25 Minuten. Wenn Sie Cloud Services (Web-/Workerrolle), VM-Skalierungsgruppen oder Verfügbarkeitsgruppen verwenden, stehen Ihnen während des geplanten Wartungsfensters zwischen den einzelnen Gruppen von virtuellen Computern 30 Minuten zur Verfügung. 

**F: Auf meinen virtuellen Computern werden keine Wartungsinformationen angezeigt. Welche Probleme sind aufgetreten?**

**A:** Es gibt verschiedene Gründe, warum keine Wartungsinformationen zu Ihren virtuellen Computern angezeigt werden:
   - Sie verwenden ein Abonnement, das als *Microsoft-intern* markiert ist.
   - Für Ihre virtuellen Computer ist keine Wartung geplant. Es kann sein, dass die Wartungsaktion beendet, abgebrochen oder so geändert wurde, dass Ihre virtuellen Computer nicht mehr davon betroffen sind.
   - Sie haben Ihrer VM-Listenansicht nicht die Spalte **Wartung** hinzugefügt. Obwohl wir diese Spalte der Standardansicht hinzugefügt haben, müssen Sie die Spalte **Wartung** manuell zur VM-Listenansicht hinzufügen, wenn Sie Ihre Ansicht so konfigurieren, dass sie Nichtstandardspalten anzeigt.

**F: Meine VM ist zum zweiten Mal für die Wartung eingeplant. Warum?**

**A:** In mehreren Anwendungsfällen wird Ihr virtueller Computer zur Wartung eingeplant, nachdem Sie die erneute Bereitstellung nach der Wartung bereits abgeschlossen haben:
   - Wir haben die Wartungsaktion abgebrochen und einen Neustart mit anderer Nutzlast vorgenommen. Es kann sein, dass wir eine fehlerhafte Nutzlast erkannt haben und einfach eine zusätzliche Nutzlast bereitstellen müssen.
   - Die *Dienstreparatur* Ihres virtuellen Computers wurde aufgrund eines Hardwarefehlers auf einem anderen Knoten durchgeführt.
   - Sie haben ausgewählt, den virtuellen Computer zu beenden (freizugeben) und neu zu starten.
   - Sie haben **automatisches Herunterfahren** für den virtuellen Computer aktiviert.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie sich über einen virtuellen Computer mithilfe von [geplanten Ereignissen](../virtual-machines/windows/scheduled-events.md) für Wartungsereignisse registrieren.
