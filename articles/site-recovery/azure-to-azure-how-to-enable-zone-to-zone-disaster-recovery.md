---
title: Aktivieren der Notfallwiederherstellung zwischen Zonen für virtuelle Azure-Computer
description: In diesem Artikel wird beschrieben, wann und wie die Notfallwiederherstellung zwischen Zone für virtuelle Azure-Computer verwendet wird.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 6a7d29d8487c6be6915ae4171b3759f0980f5448
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874355"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Aktivieren der Notfallwiederherstellung für virtuelle Azure-Computer zwischen Verfügbarkeitszonen

In diesem Artikel wird beschrieben, wie Sie Replikation, Failover und Failback für virtuelle Azure-Computer aus einer Verfügbarkeitszone in eine andere in der gleichen Azure-Region durchführen.

>[!NOTE]
>
>- Unterstützung für Notfallwiederherstellung zwischen Zonen ist derzeit auf zwei Regionen beschränkt: „Asien, Südosten“ und „Vereinigtes Königreich, Süden“.  
>- Wenn der Kunde die Notfallwiederherstellung zwischen Zonen verwendet, speichert Site Recovery Kundendaten nur in der Region, in sie bereitgestellt wurden, und verschiebt sie nicht aus dieser Region. Kunden können auf Wunsch einen Recovery Services-Tresor aus einer anderen Region auswählen. Der Recovery Services-Tresor enthält Metadaten, aber keine tatsächlichen Kundendaten.

Der Site Recovery-Dienst unterstützt Ihre Strategien für Geschäftskontinuität und Notfallwiederherstellung, indem die Verfügbarkeit Ihrer Geschäftsanwendungen bei geplanten und ungeplanten Ausfällen gewährleistet wird. Es handelt sich dabei um die empfohlene Notfallwiederherstellungsoption, um Ihre Anwendungen bei regionalen Ausfällen aktiv und betriebsbereit zu halten.

Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone verfügt über mindestens ein Rechenzentrum. 

Wenn Sie virtuelle Computer in eine Verfügbarkeitszone in einer anderen Region verschieben möchten, [lesen Sie diesen Artikel](../resource-mover/move-region-availability-zone.md).

## <a name="using-availability-zones-for-disaster-recovery"></a>Verwenden von Verfügbarkeitszonen für die Notfallwiederherstellung 

In der Regel werden Verfügbarkeitszonen zum Bereitstellen von VMs in einer Hochverfügbarkeitskonfiguration verwendet. Sie befinden sich möglicherweise zu nah beieinander, um im Falle einer Naturkatastrophe als Notfallwiederherstellungslösung fungieren zu können.

In einigen Szenarien können Verfügbarkeitszonen jedoch für die Notfallwiederherstellung genutzt werden:

- Viele Kunden, die eine Strategie für Metro-Notfallwiederherstellung hatten, während Anwendungen lokal gehostet werden, versuchen manchmal, diese Strategie nachzuahmen, sobald sie Anwendungen zu Azure migrieren. Diese Kunden wissen um die Tatsache, dass die Metro-Notfallwiederherstellungsstrategie bei einem umfangreichen physischen Notfall möglicherweise nicht funktioniert und akzeptieren dieses Risiko. Bei solchen Kunden kann die Notfallwiederherstellung zwischen Zonen als Option für die Notfallwiederherstellung verwendet werden.

- Viele andere Kunden verwenden eine komplizierte Netzwerkinfrastruktur und möchten diese aufgrund der damit verbundenen Kosten und Komplexität nicht in einer sekundären Region neu erstellen. Notfallwiederherstellung zwischen Zonen reduziert die Komplexität, da redundante Netzwerkkonzepte über Verfügbarkeitszonen hinweg genutzt werden, die die Konfiguration erheblich vereinfachen. Diese Kunden bevorzugen Einfachheit und können auch Verfügbarkeitszonen für die Notfallwiederherstellung verwenden.

- In einigen Regionen, die nicht über eine Regionspaar mit derselben Rechtsprechung verfügen (z. B. „Asien, Südosten“), kann die Notfallwiederherstellung zwischen Zonen als De-facto-Lösung für Notfallwiederherstellung dienen, weil sie die gesetzliche Konformität gewährleistet, da Ihre Anwendungen und Daten nicht über nationale Grenzen verschoben werden. 

- Notfallwiederherstellung zwischen Zonen impliziert die Replikation von Daten über kürzere Entfernungen im Vergleich zur Azure-zu-Azure-Notfallwiederherstellung. Daher kann es zu niedrigerer Latenz und einem niedrigeren RPO-Wert kommen.

Obwohl es sich hierbei um starke Vorteile handelt, besteht die Möglichkeit, dass die Notfallwiederherstellung zwischen Zonen bei einer regionsweiten Naturkatastrophe die Resilienzanforderungen nicht erfüllt.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Netzwerke für Notfallwiederherstellung zwischen Zonen

Wie bereits oben gesagt wurde: Notfallwiederherstellung zwischen Zonen reduziert die Komplexität, da redundante Netzwerkkonzepte über Verfügbarkeitszonen hinweg genutzt werden, die die Konfiguration erheblich vereinfachen. Das Verhalten von Netzwerkkomponenten im Szenario für Notfallwiederherstellung zwischen Zonen wird unten beschrieben: 

- Virtuelles Netzwerk: Sie können dasselbe virtuelle Netzwerk wie das Quellnetzwerk für tatsächliche Failover verwenden. Verwenden Sie für Testfailover ein anderes virtuelles Netzwerk als das Quellnetzwerk.

- Subnetz: Ein Failover in das gleiche Subnetz wird unterstützt.

- Private IP-Adresse: Wenn Sie statische IP-Adressen verwenden, können Sie die gleichen IP-Adressen in der Zielzone verwenden, wenn Sie diese auf solche Weise konfigurieren.

- Beschleunigter Netzwerkbetrieb: Ähnlich wie bei der Azure-zu-Azure-Notfallwiederherstellung können Sie beschleunigten Netzwerkbetrieb aktivieren, wenn die VM-SKU dies unterstützt.

- Öffentliche IP-Adresse: Sie können eine zuvor erstellte öffentliche IP-Standardadresse in der gleichen Region an die Ziel-VM anfügen. Öffentliche IP-Basisadressen unterstützen keine Szenarien mit Verfügbarkeitszonen.

- Lastenausgleich: Der Standardlastenausgleich ist eine regionale Ressource, daher kann die Ziel-VM an den Back-End-Pool desselben Lastenausgleichs angefügt werden. Ein neuer Lastenausgleich ist nicht erforderlich.

- Netzwerksicherheitsgruppe: Sie können die gleichen Netzwerksicherheitsgruppen verwenden, die auf die Quell-VM angewendet werden.

## <a name="pre-requisites"></a>Voraussetzungen

Vor dem Bereitstellen von Notfallwiederherstellung zwischen Zonen für Ihre VMs muss sichergestellt werden, dass andere auf der VM aktivierte Features mit Notfallwiederherstellung zwischen Zonen interoperabel sind.

|Funktion  | Supporthinweis  |
|---------|---------|
|Klassische virtuelle Computer   |     Nicht unterstützt    |
|ARM-VMs    |    Unterstützt    |
|Azure Disk Encryption v1 (zwei Durchläufe, mit Azure Active Directory (Azure AD))     |     Unterstützt   |
|Azure Disk Encryption v2 (Einzeldurchlauf, ohne Azure AD)    |    Unterstützt    |
|Nicht verwaltete Datenträger    |    Nicht unterstützt    |
|Verwaltete Datenträger    |    Unterstützt    |
|Vom Kunden verwaltete Schlüssel    |    Unterstützt    |
|Näherungsplatzierungsgruppen    |    Unterstützt    |
|Sicherungsinteroperabilität    |    Sicherung und Wiederherstellung auf Dateiebene wird unterstützt. Sicherung und Wiederherstellung auf Datenträger- und VM-Ebene wird nicht unterstützt.    |
|Hinzufügen/Entfernen von Datenträgern im laufenden Betrieb    |    Datenträger können hinzugefügt werden, nachdem die Replikation zwischen Zonen aktiviert wurde. Das Entfernen von Datenträgern nach dem Aktivieren der Replikation zwischen Zonen wird nicht unterstützt.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Einrichten von Site Recovery-Notfallwiederherstellung zwischen Zonen

### <a name="log-in"></a>Anmelden

Melden Sie sich beim Azure-Portal an.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Aktivieren der Replikation für den zonalen virtuellen Azure-Computer

1. Wählen Sie im Menü des Azure-Portals die Option „Virtuelle Computer“ aus, oder suchen Sie auf einer beliebigen Seite nach „Virtuelle Computer“, und wählen Sie diese Option anschließend aus. Wählen Sie den virtuellen Computer aus, den Sie replizieren möchten. Diese VM muss sich bereits in einer Verfügbarkeitszone befinden, damit Notfallwiederherstellung zwischen Zonen erfolgen kann.

2. Wählen Sie unter „Vorgänge“ die Option „Notfallwiederherstellung“ aus.

3. Wählen Sie wie unten gezeigt auf der Registerkarte „Grundlagen“ die Option „Ja“ für „Notfallwiederherstellung zwischen Verfügbarkeitszonen?“ aus.

    ![Seite mit den Grundeinstellungen](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Wenn Sie alle Standardwerte akzeptieren, klicken Sie auf „Replikation überprüfen und starten“, gefolgt von „Replikation starten“.

5. Wenn Sie Änderungen an den Replikationseinstellungen vornehmen möchten, klicken Sie auf „Weiter: Erweiterte Einstellungen“.

6. Ändern Sie die Standardeinstellung bei Bedarf. Benutzer von Azure-zu-Azure-Notfallwiederherstellung sind mit dieser Seite möglicherweise bereits vertraut. Weitere Einzelheiten zu den auf diesem Blatt verfügbaren Optionen finden Sie [hier](./azure-to-azure-tutorial-enable-replication.md).

    ![Seite „Erweiterte Einstellungen“](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Klicken Sie auf „Weiter: Replikation überprüfen und starten“ und dann auf „Replikation starten“.

## <a name="faqs"></a>Häufig gestellte Fragen

**1. Wie funktioniert die Preisgestaltung für Notfallwiederherstellung zwischen Zonen?**
Die Preise für Notfallwiederherstellung zwischen Zonen sind identisch mit den Preisen für Azure-zu-Azure-Notfallwiederherstellung. Weitere Einzelheiten dazu finden Sie auf der Seite mit den Preisen [hier](https://azure.microsoft.com/pricing/details/site-recovery/) und [hier](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/). Beachten Sie, dass die Gebühren für ausgehenden Datenverkehr, die Sie in Notfallwiederherstellung zwischen Zonen sehen würden, niedriger sind als für Notfallwiederherstellung zwischen Regionen.

**2. Was ist die SLA für RTO und RPO?**
Die RTO-SLA ist identisch mit der SLA für Site Recovery insgesamt. Wir versprechen eine RTO von bis zu 2 Stunden. Es gibt keine definierte SLA für RPO.

**3. Wird Kapazität in der sekundären Zone garantiert?**
Das Site Recovery-Team und das Azure-Kapazitätsverwaltungsteam planen eine ausreichende Infrastrukturkapazität. Wenn Sie ein Failover starten, können die Teams auch dabei helfen, sicherzustellen, dass die durch Site Recovery geschützten VM-Instanzen in der Zielzone bereitgestellt werden.

**4. Welche Betriebssysteme werden unterstützt?**
Notfallwiederherstellung zwischen Zonen unterstützt dieselben Betriebssysteme wie Azure-zu-Azure-Notfallwiederherstellung. Weitere Informationen finden Sie [hier](./azure-to-azure-support-matrix.md) in der Unterstützungsmatrix.

**5. Können Quell- und Zielressourcengruppen identisch sein?**
Nein, Sie müssen ein Failover auf eine andere Ressourcengruppe durchführen.

## <a name="next-steps"></a>Nächste Schritte

Die Schritte, die für eine Notfallwiederherstellungsübung, Failover, erneuten Schutz und Failback ausgeführt werden müssen, entsprechen den Schritten im Szenario für Azure-zu-Azure-Notfallwiederherstellung.

Um eine Notfallwiederherstellungsübung auszuführen, befolgen Sie die [hier](./azure-to-azure-tutorial-dr-drill.md) beschriebenen Schritte.

Um ein Failover durchzuführen und VMs in der sekundären Zone erneut zu schützen, folgen Sie den [hier](./azure-to-azure-tutorial-failover-failback.md) beschriebenen Schritten.

Um ein Failback zur primären Zone auszuführen, führen Sie die [hier](./azure-to-azure-tutorial-failback.md) beschriebenen Schritte aus.
