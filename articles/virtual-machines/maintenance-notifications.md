---
title: Wartungsbenachrichtigungen
description: Übersicht über Wartungsbenachrichtigungen für virtuelle Computer, die in Azure ausgeführt werden.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 8/12/2020
ms.author: shants
ms.openlocfilehash: 53cde1178a4faae0fbd11222e4219f70be29145d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560807"
---
# <a name="handling-planned-maintenance-notifications"></a>Behandeln von Benachrichtigungen zu geplanten Wartungen

Azure führt regelmäßig Updates durch, um die Zuverlässigkeit, Leistung und Sicherheit der Hostinfrastruktur für virtuelle Computer zu verbessern. Zu Updates zählen Änderungen wie das Patchen der Hostumgebung oder das Upgraden und die Außerbetriebnahme von Hardware. Die meisten dieser Updates werden ohne Auswirkungen auf die gehosteten virtuellen Computer durchgeführt. Es gibt jedoch Ausnahmen:

- Wenn die Wartung keinen Neustart erfordert, hält Azure den virtuellen Computer für einige Sekunden an, während der Host aktualisiert wird. Wartungsvorgänge dieser Art werden nacheinander auf die einzelnen Fehlerdomänen angewendet. Die Vorgänge werden beendet, wenn ein Integritätswarnungssignal empfangen wird.

- Wenn die Wartung einen Neustart erfordert, werden Sie in einer Benachrichtigung über den geplanten Wartungstermin informiert. Ihnen steht ein Zeitfenster von 35 Tagen zur Verfügung, in dem Sie die Wartung zu einem für Sie günstigen Zeitpunkt selbst starten können.


Geplante Wartungen, die einen Neustart erfordern, werden in Wellen geplant. Jede Welle hat einen anderen Umfang (Regionen).

- Eine Welle beginnt mit einer Kundenbenachrichtigung. Die Benachrichtigung wird standardmäßig an den Administrator und Co-Admin des Abonnements gesendet. Sie können weitere Empfänger und Nachrichtenoptionen wie E-Mail, SMS und Webhooks mithilfe von [Aktivitätsprotokollwarnungen](../service-health/alerts-activity-log-service-notifications-portal.md) hinzufügen.  
- Sobald eine Benachrichtigung erfolgt, wird ein *Self-Service-Zeitfenster* bereitgestellt. In diesem Fenster können Sie abfragen, welche Ihrer virtuellen Computer betroffen sind, und die Wartung basierend auf Ihren eigenen Planungsanforderungen starten. Das Self-Service-Zeitfenster umfasst in der Regel ca. 35 Tage.
- Im Anschluss an das Self-Service-Zeitfenster beginnt das *Zeitfenster für die geplante Wartung*. Irgendwann in diesem Zeitfenster plant Azure die erforderliche Wartung und wendet sie auf Ihren virtuellen Computer an. 

Mit den beiden Zeitfenstern möchten wir Ihnen einerseits genügend Zeit geben, um die Wartung zu initiieren und Ihren virtuellen Computer neu zu starten, und Sie andererseits darüber informieren, wann Azure die Wartung automatisch startet.


Sie können das Azure-Portal, PowerShell, die REST-API und die Befehlszeilenschnittstelle verwenden, um die Wartungszeitfenster für Ihre virtuellen Computer abzufragen und eine Self-Service-Wartung zu starten.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Sollten Sie die Wartung während des Self-Service-Fensters starten?  

Die folgenden Hinweise sollen Sie bei der Entscheidung unterstützen, ob Sie diese Funktion nutzen und die Wartung zu einem selbst gewählten Zeitpunkt starten sollten. 

> [!NOTE] 
> Die Self-Service-Wartung ist unter Umständen nicht für alle virtuellen Computer verfügbar. Um zu ermitteln, ob die proaktive erneute Bereitstellung für Ihren virtuellen Computer verfügbar ist, überprüfen Sie, ob als Wartungsstatus **Jetzt starten** angezeigt wird. Die Self-Service-Wartung ist derzeit nicht für Cloud Services (Web-/Workerrolle) und Service Fabric verfügbar.


Self-Service-Wartung wird für Bereitstellungen, die **Verfügbarkeitsgruppen** verwenden, nicht empfohlen. Für Verfügbarkeitsgruppen wird bereits jeweils nur eine Updatedomäne nach der anderen aktualisiert. 

- Überlassen Sie Azure das Auslösen der Wartung. Für eine Wartung, die einen Neustart erfordert, wird die Wartung auf eine Updatedomäne nach der anderen angewendet. Die Updatedomänen werden nicht unbedingt sequenziell gewartet, und zwischen den Updatedomänen gibt es eine Pause von 30 Minuten. 
- Wenn ein vorübergehender Kapazitätsverlust (eine Updatedomäne) ein Problem darstellt, können Sie während des Wartungszeitraums Instanzen hinzufügen. 
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


## <a name="faq"></a>Häufig gestellte Fragen


**F: Wozu müssen Sie meine virtuellen Computer jetzt neu starten?**

**A:** Während die Mehrzahl der Updates und Upgrades für die Azure-Plattform die Verfügbarkeit des virtuellen Computers nicht beeinflusst, gibt es Fälle, in denen wir den Neustart in Azure gehosteter virtueller Computer nicht vermeiden können. Bei uns sind mehrere Änderungen zusammengekommen, die den Neustart unserer Server erfordern und damit auch zum Neustart virtueller Computer führen.

**F: Bin ich auf der sicheren Seite, wenn ich ihre Empfehlungen für Hochverfügbarkeit mit Verwendung einer Verfügbarkeitsgruppe befolge?**

**A:** In einer Verfügbarkeitsgruppe oder VM-Skalierungsgruppe bereitgestellte virtuelle Computer werden wie Updatedomänen (UD) behandelt. Bei der Wartung berücksichtigt Azure die UD-Einschränkung und startet virtuelle Computer nicht von einer anderen UD (innerhalb derselben Verfügbarkeitsgruppe) aus neu.  Azure wartet auch mindestens 30 Minuten vor dem Wechsel zur nächsten Gruppe von virtuellen Computern. 

Weitere Informationen zu Hochverfügbarkeit finden Sie unter [Verfügbarkeit für virtuelle Computer in Azure](availability.md).

**F: Wie werde ich über eine geplante Wartung benachrichtigt?**

**A:** Eine geplante Wartungsaktion beginnt mit dem Festlegen eines Zeitplans für eine oder mehrere Azure-Regionen. Kurz darauf wird eine E-Mail-Benachrichtigung an Abonnementadministratoren, Co-Admins, Besitzer und Mitwirkende gesendet (jeweils eine E-Mail pro Abonnement). Zusätzliche Kanäle und Empfänger für diese Benachrichtigung können mit Aktivitätsprotokollwarnungen konfiguriert werden. Falls Sie einen virtuellen Computer in einer Region bereitstellen, wo bereits Wartung geplant ist, erhalten Sie die Benachrichtigung nicht, sondern müssen vielmehr den Wartungsstatus des virtuellen Computers überprüfen.

**F: Mir wird weder im Portal noch über PowerShell oder die Befehlszeilenschnittstelle ein Hinweis auf eine geplante Wartung angezeigt. Was ist schiefgelaufen?**

**A:** Informationen im Zusammenhang mit geplanter Wartung stehen während einer geplanten Wartungsaktion nur für die virtuellen Computer zur Verfügung, die davon betroffen sind. D.h., wenn Ihnen keine Daten angezeigt werden, ist es möglich, dass die Wartungsaktion bereits abgeschlossen (oder nicht gestartet) wurde, bzw. Ihr virtueller Computer bereits auf einem aktualisierten Server gehostet wird.

**F: Kann ich feststellen, wann genau mein virtueller Computer betroffen sein wird?**

**A:** Bei Festlegung des Zeitplans definieren wir ein Zeitfenster von einigen Tagen. Allerdings ist die genaue Reihenfolge von Servern (und VMs) innerhalb dieses Zeitfensters unbekannt. Kunden, die die genaue Zeit für ihre virtuellen Computer wissen möchten, können [geplante Ereignisse](./linux/scheduled-events.md) verwenden, aus dem virtuellen Computer heraus abfragen und erhalten 15 Minuten vor dem VM-Neustart eine Benachrichtigung.

**F: Wie lange dauert es, meinen virtuellen Computer neu zu starten?**

**A:**  Je nach Größe Ihres virtuellen Computers kann der Neustart während des Self-Service-Wartungsfensters mehrere Minuten dauern. Die von Azure im geplanten Wartungsfenster initiierten Neustarts dauern in der Regel etwa 25 Minuten. Beachten Sie, dass Ihnen bei Verwendung von Cloud Services (Web-/Workerrolle), VM-Skalierungsgruppen oder Verfügbarkeitsgruppen während des geplanten Wartungsfensters zwischen den einzelnen Gruppen von virtuellen Computern (UD) 30 Minuten zur Verfügung stehen.

**F: Wie sind die Erfahrungen mit VM-Skalierungsgruppen?**

**A:** Es ist nun eine geplante Wartung für VM-Skalierungsgruppen verfügbar. Anleitungen zum Initiieren der Self-Service-Wartung finden Sie im Dokument [zur geplanten Wartung für VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md).

**F: Wie sind die Erfahrungen mit Cloud Services (Web-/Workerrolle) und Service Fabric?**

**A:** Diese Plattformen sind zwar von geplanten Wartungen betroffen, die Sicherheit der Kunden, die diese Plattformen verwenden, ist jedoch gewährleistet, da nur jeweils virtuelle Computer in einer einzelnen Upgradedomäne (UD) betroffen sind. Die Self-Service-Wartung ist derzeit nicht für Cloud Services (Web-/Workerrolle) und Service Fabric verfügbar.

**F: Auf meinen virtuellen Computern werden keine Wartungsinformationen angezeigt. Welche Probleme sind aufgetreten?**

**A:** Es gibt verschiedene Gründe, warum Sie keine Wartungsinformationen auf Ihren virtuellen Computern sehen:
1.  Sie verwenden ein Abonnement, das als Microsoft-intern markiert ist.
2.  Ihre virtuellen Computer sind nicht für die Wartung eingeplant. Es könnte sein, dass die Wartungsaktion beendet, abgebrochen oder so geändert wurde, dass Ihre virtuellen Computer nicht mehr davon betroffen sind.
3. Sie haben die Zuordnung der VM aufgehoben und dann die VM gestartet. Dies kann dazu führen, dass die VM an einen Speicherort verschoben wird, für den keine geplante Wartungsaktion terminiert wurde. Daher werden für die VM keine Wartungsinformationen mehr angezeigt. 
4.  Sie haben Ihrer VM-Listenansicht nicht die Spalte **Wartung** hinzugefügt. Obwohl wir diese Spalte der Standardansicht hinzugefügt haben, müssen Kunden, die die Anzeige nicht standardmäßiger Spalten konfiguriert haben, die Spalte **Wartung** ihrer VM-Listenansicht manuell hinzufügen.

**F: Meine VM ist zum zweiten Mal für die Wartung eingeplant. Warum?**

**A:** In mehreren Anwendungsfällen wird Ihr virtueller Computer zur Wartung eingeplant, nachdem Sie die erneute Bereitstellung nach der Wartung bereits abgeschlossen haben:
1.  Wir haben die Wartungsaktion abgebrochen und einen Neustart mit anderer Nutzlast vorgenommen. Es könnte sein, dass wir fehlerhafte Nutzlast erkannt haben und einfach eine zusätzliche Nutzlast bereitstellen müssen.
2.  Die *Dienstreparatur* Ihres virtuellen Computers wurde aufgrund eines Hardwarefehlers auf einem anderen Knoten durchgeführt.
3.  Sie haben ausgewählt, den virtuellen Computer zu beenden (freizugeben) und neu zu starten.
4.  Sie haben **automatisches Herunterfahren** für den virtuellen Computer aktiviert.



## <a name="next-steps"></a>Nächste Schritte

Sie können die geplante Wartung mithilfe der [Azure-Befehlszeilenschnittstelle](maintenance-notifications-cli.md), mit [Azure PowerShell](maintenance-notifications-powershell.md) oder über das [Portal](maintenance-notifications-portal.md) vornehmen.
