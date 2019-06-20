---
title: Aktualisieren eines Clouddiensts | Microsoft Docs
description: Hier erfahren Sie, wie Sie Clouddienste in Azure aktualisieren. Erfahren Sie, wie die Aktualisierung eines Clouddiensts ausgeführt wird, damit die Verfügbarkeit sichergestellt ist.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: ff4dd571911719e4f2ec27952785432960a56d42
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60653890"
---
# <a name="how-to-update-a-cloud-service"></a>Aktualisieren eines Clouddiensts

Das Aktualisieren eines Clouddiensts einschließlich Rollen und Gastbetriebssysteme besteht aus drei Schritten. Zuerst müssen die Binär- und Konfigurationsdateien für den neuen Clouddienst oder die Version des Betriebssystems hochgeladen werden. Dann reserviert Azure Compute- und Netzwerkressourcen für den Clouddienst je nach den Erfordernissen der neuen der Clouddienstversion. Und zuletzt führt Azure ein paralleles Update aus, um den Mandanten inkrementell auf die neue Version oder das Gastbetriebssystem zu aktualisieren und Ihre Verfügbarkeit dabei beizubehalten. Dieser Artikel behandelt diesen letzten Schritt – das parallele Upgrade.

## <a name="update-an-azure-service"></a>Aktualisieren eines Azure-Diensts
Azure organisiert Ihre Rolleninstanzen in logischen Gruppen, die als Upgradedomänen (UD) bezeichnet werden. Upgradedomänen (UD) sind logische Sätze von Rolleninstanzen, die als Gruppe aktualisiert werden.  Azure aktualisiert einen Clouddienst mit je einer UD. Dadurch können Instanzen in anderen UDs den Datenverkehr weiter bearbeiten.

Die Standardanzahl von Upgradedomänen ist 5. Sie können eine andere Anzahl von Upgradedomänen festlegen, indem Sie das Attribut „upgradeDomainCount“ in die Dienstdefinitionsdatei (.CSDEF) einschließen. Weitere Informationen über das Attribut „upgradeDomainCount“ finden Sie unter [WebRole-Schema](/previous-versions/azure/reference/gg557553(v=azure.100)) oder [WorkerRole-Schema](/previous-versions/azure/reference/gg557552(v=azure.100)).

Wenn Sie eine direkte Aktualisierung einer oder mehrerer Rollen in Ihrem Dienst durchführen, aktualisiert Azur Sätze von Rolleninstanz je nach der Upgradedomäne, der sie angehören. Azure aktualisiert alle Instanzen in einer bestimmten Upgradedomäne (beendet sie, aktualisiert sie und schaltet sie wieder online) und fährt dann mit der nächsten Domäne fort. Dadurch, dass nur die Instanzen beendet werden, die in der aktuellen Upgradedomäne ausgeführt werden, stellt Azure sicher, dass sich die Aktualisierung so wenig wie möglich auf den ausgeführten Dienst auswirkt. Weitere Informationen finden Sie unter [Vorgehensweise bei der Aktualisierung](#howanupgradeproceeds) weiter unten in diesem Artikel.

> [!NOTE]
> Die Begriffe **Aktualisierung** und **Update** haben für Azure eine etwas unterschiedliche Bedeutung. In Bezug auf die Prozesse und Beschreibungen der Features in diesem Dokument können Sie jedoch synonym verwendet werden.
>
>

Ihr Dienst muss mindestens zwei Instanzen einer Rolle definieren, damit diese Rolle direkt und ohne Ausfallzeiten aktualisiert wird. Wenn der Dienst aus nur einer Instanz einer Rolle besteht, ist der Dienst nicht verfügbar, bis die direkte Aktualisierung abgeschlossen ist.

Dieses Thema behandelt die folgenden Informationen zu Azure-Aktualisierungen:

* [Zulässige Dienständerungen während einer Aktualisierung](#AllowedChanges)
* [Vorgehensweise beim Upgrade](#howanupgradeproceeds)
* [Zurücksetzen eines Updates](#RollbackofanUpdate)
* [Initiieren mehrerer Änderungsvorgänge für eine laufende Bereitstellung](#multiplemutatingoperations)
* [Verteilung von Rollen über Upgradedomänen](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Zulässige Dienständerungen während einer Aktualisierung
Die folgende Tabelle zeigt die zulässigen Änderungen an einen Dienst während einer Aktualisierung an:

| Zulässige Änderungen an Hosting, Diensten und Rollen | Direktes Update | Gestaffelt (VIP-Austausch) | Löschen und erneut Bereitstellen |
| --- | --- | --- | --- |
| Betriebssystemversion |Ja |Ja |Ja |
| .NET-Vertrauensebene |Ja |Ja |Ja |
| Größe des virtuellen Computers <sup>1</sup> |Ja<sup>2</sup> |Ja |Ja |
| Einstellungen für den lokalen Speicher |Nur Erhöhen <sup>2</sup> |Ja |Ja |
| Rollen in einem Dienst hinzufügen oder entfernen |Ja |Ja |Ja |
| Anzahl der Instanzen einer bestimmten Rolle |Ja |Ja |Ja |
| Anzahl oder Typ der Endpunkte für einen Dienst |Ja<sup>2</sup> |Nein |Ja |
| Namen und Werte von Konfigurationseinstellungen |Ja |Ja |Ja |
| Werte (aber keine Namen) von Konfigurationseinstellungen |Ja |Ja |Ja |
| Neue Zertifikate hinzufügen |Ja |Ja |Ja |
| Vorhandene Zertifikate ändern |Ja |Ja |Ja |
| Neuen Code bereitstellen |Ja |Ja |Ja |

<sup>1</sup>Größenänderung beschränkt auf die Teilmenge der Größen, die für den Clouddienst verfügbar sind.

<sup>2</sup>Erfordert Azure SDK 1.5 oder höher.

> [!WARNING]
> Wenn die Größe des virtuellen Computers geändert wird, werden dadurch lokale Daten zerstört.
>
>

Die folgenden Elemente werden während einer Aktualisierung nicht unterstützt:

* Ändern des Namens einer Rolle Entfernen einer Rolle und erneutes Hinzufügen mit neuem Namen
* Ändern der Anzahl der Upgradedomänen
* Verringern der Größe der lokalen Ressourcen

Wenn Sie andere Aktualisierungen an der Serverdefinition vornehmen (z. B. die Größe der lokalen Ressource verringern), müssen Sie eine VIP-Austausch-Aktualisierung vornehmen. Weitere Informationen finden Sie in unter [Swap Deployment](/previous-versions/azure/reference/ee460814(v=azure.100)) (Tauschen der Bereitstellung).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Vorgehensweise beim Upgrade
Sie können entweder alle oder eine einzelne Rolle in Ihrem Dienst aktualisieren. In beiden Fällen werden alle Instanzen jeder Rolle, die aktualisiert wird und der ersten Upgradedomäne angehört, beendet, aktualisiert und dann wieder online geschaltet. Sobald sie wieder online sind, werden die Instanzen in der zweiten Upgradedomäne beendet, aktualisiert und wieder online geschaltet. In einem Clouddienst kann jeweils höchstens eine Aktualisierung aktiv sein. Das Upgrade erfolgt immer auf die letzte Version des Clouddiensts.

Das folgende Diagramm zeigt, wie das Upgrade durchgeführt wird, wenn Sie alle Rollen im Dienst aktualisieren:

![Dienst aktualisieren](media/cloud-services-update-azure-service/IC345879.png "Dienst aktualisieren")

Das nächste Diagramm zeigt, wie die Aktualisierung durchgeführt wird, wenn Sie nur eine Rolle aktualisieren:

![Rolle aktualisieren](media/cloud-services-update-azure-service/IC345880.png "Rolle aktualisieren")  

Während einer automatischen Aktualisierung wertet der Azure Fabric Controller in regelmäßigen Abständen die Integrität des Clouddiensts aus, um zu bestimmen, wann die nächste UD durchlaufen werden kann. Diese Bewertung der Integrität erfolgt pro Rolle und berücksichtigt nur die Instanzen in der neuesten Version (d. h. Instanzen von UDs, die bereits durchlaufen wurden). Sie überprüft, ob eine Mindestanzahl von Rolleninstanzen für jede Rolle einen zufriedenstellende Endzustand erreicht hat.

### <a name="role-instance-start-timeout"></a>Starttimeout für Rolleninstanz
Der Fabric Controller wartet bei jeder Rolleninstanz 30 Minuten darauf, dass der Zustand „Gestartet“ erreicht wird. Nach Ablauf des Zeitlimits fährt der Fabric Controller mit der nächsten Rolleninstanz fort.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Auswirkung auf Laufwerksdaten während eines Clouddienstupgrades

Beim Aktualisieren eines Diensts aus einer einzelnen Instanz auf mehrere Instanzen wird der Dienst während des Upgrades beendet. Dies liegt an der Art und Weise, wie Upgrades von Diensten in Azure durchgeführt werden. Die Vereinbarung zum Servicelevel mit gewährleisteter Dienstverfügbarkeit gilt nur für Dienste, die mit mehr als eine Instanz bereitgestellt werden. Die folgende Liste beschreibt, wie sich jedes Upgradeszenario eines Azure-Diensts auf die Daten auf jedem Laufwerk auswirkt:

|Szenario|Laufwerk C|Laufwerk D|Laufwerk E|
|--------|-------|-------|-------|
|VM-Neustart|Wird beibehalten|Wird beibehalten|Wird beibehalten|
|Portalneustart|Wird beibehalten|Wird beibehalten|Wird zerstört|
|Reimaging für das Portal|Wird beibehalten|Wird zerstört|Wird zerstört|
|Direktes Upgrade|Wird beibehalten|Wird beibehalten|Wird zerstört|
|Knotenmigration|Wird zerstört|Wird zerstört|Wird zerstört|

Bitte beachten Sie, dass das Laufwerk E: in der Liste oben das Stammlaufwerk der Rolle darstellt und nicht hartcodiert werden sollte. Verwenden Sie stattdessen die Umgebungsvariable **% RoleRoot%** , um das Laufwerk darzustellen.

Stellen Sie einen neuen Dienst mit mehreren Instanzen auf dem Staging-Server bereit, und führen Sie einen VIP-Austausch aus, um die Ausfallzeit bei der Aktualisierung eines Einzelinstanzdiensts zu minimieren.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Zurücksetzen eines Updates
Azure ist bei der Verwaltung von Diensten während einer Aktualisierung flexibel, da Sie weitere Vorgänge für einen Dienst initiieren können, nachdem die ursprüngliche Aktualisierungsanforderung vom Azure Fabric Controller akzeptiert wurde. Eine Zurücksetzung ist nur möglich, wenn eine Aktualisierung (Konfigurationsänderung) oder ein Upgrade in der Bereitstellung den Status **In Bearbeitung** hat. Eine Aktualisierung oder ein Upgrade gilt als in Bearbeitung, wenn mindestens eine Instanz des Diensts noch nicht auf die neue Version aktualisiert wurde. Um zu testen, ob eine Zurücksetzung zulässig ist, prüfen Sie, ob der Wert des Flags „RollbackAllowed“, der von den Vorgängen [Bereitstellung abrufen](/previous-versions/azure/reference/ee460804(v=azure.100)) und [Clouddiensteigenschaften abrufen](/previous-versions/azure/reference/ee460806(v=azure.100)) zurückgegeben wird, auf „true“ festgelegt ist.

> [!NOTE]
> Eine Zurücksetzung ist nur für eine **direkte** Aktualisierung (bzw. ein direktes Upgrade) sinnvoll, da ein VIP-Austausch das Ersetzen einer gesamten ausgeführten Instanz Ihres Diensts durch eine andere beinhaltet.
>
>

Das Zurücksetzen einer Aktualisierung in Bearbeitung wirkt sich folgendermaßen auf die Bereitstellung aus:

* Alle Rolleninstanzen, für die noch keine Aktualisierung/kein Upgrade auf die neue Version durchgeführt wurde, werden nicht aktualisiert/upgegradet, da diese Instanzen bereits die Zielversion des Diensts ausführen.
* Rolleninstanzen, die bereits aktualisiert oder für die ein Upgrde auf die neue Version der Dienstpaketdatei (\*.cspkg) oder der Dienstkonfigurationsdatei (\*.cscfg) (oder beide Dateien) durchgeführt wurde, werden auf die Version dieser Dateien vor dem Upgrade zurückgesetzt.

Diese Funktion wird durch die folgenden Features bereitgestellt:

* Der Vorgang zum [Zurücksetzen einer Aktualisierung oder eines Upgrades](/previous-versions/azure/reference/hh403977(v=azure.100)) kann bei einer Konfigurationsaktualisierung (ausgelöst durch Aufrufen von [Bereitstellungskonfiguration ändern](/previous-versions/azure/reference/ee460809(v=azure.100))) oder bei einem Upgrade (ausgelöst durch Aufrufen von [Upgrade für Bereitstellung durchführen](/previous-versions/azure/reference/ee460793(v=azure.100))) aufgerufen werden, solange zumindest eine Instand im Dienst noch nicht auf die neue Version aktualisiert wurde.
* Als Teil des Antworttexts der Vorgänge [Bereitstellung abrufen](/previous-versions/azure/reference/ee460804(v=azure.100)) und [Clouddiensteigenschaften abrufen](/previous-versions/azure/reference/ee460806(v=azure.100)) werden die Elemente „Locked“ und „RollbackAllowed“ zurückgegeben:

  1. Das Element „Locked“ gibt an, wann für eine bestimmte Bereitstellung ein Änderungsvorgang aufgerufen werden kann.
  2. Das Element „RollbackAllowed“ gib an, wann der Vorgang [Zurücksetzen der Aktualisierung oder des Upgrades](/previous-versions/azure/reference/hh403977(v=azure.100)) für eine bestimmte Bereitstellung aufgerufen werden kann.

  Zum Zurücksetzen müssen Sie nicht beide Elemente, „Locked“ und „RollbackAllowed“ prüfen. Es ist ausreichend, wenn Sie sicherstellen, dass „RollbackAllowed“ auf „true“ festgelegt ist. Diese Elemente werden nur ausgegeben, wenn diese Methoden mithilfe des Anforderungsheaders aufgerufen werden und dieser auf „x-ms-version: 2011-10-01“ oder höher festgelegt ist. Weitere Informationen zu Versionsverwaltungsheadern finden Sie unter [Dienstverwaltungs-Versionsverwaltung](/previous-versions/azure/gg592580(v=azure.100)).

In den folgenden Situationen wird ein Zurücksetzen einer Aktualisierung oder eines Upgrades nicht unterstützt:

* Verringerung lokaler Ressourcen – Wenn die Aktualisierung die lokalen Ressourcen für eine Rolle vergrößert, ermöglicht die Azure-Plattform kein Zurücksetzen.
* Kontingentgrenzen – Wenn die Aktualisierung ein Vorgang zum zentralen Herunterskalieren war, ist Ihr Berechnungskontingent für das Zurücksetzen unter Umständen nicht mehr ausreichend. Jedem Azure-Abonnement ist ein Kontingent zugeordnet, das die maximale Anzahl von Kernen angibt, die von allen gehosteten Diensten, die zu diesem Abonnement gehören, genutzt werden können Wenn durch das Zurücksetzen einer Aktualisierung das Kontingent für Ihr Abonnement überschritten werden würde, ist das Zurücksetzen nicht möglich.
* Racebedingung – Wenn die ursprüngliche Aktualisierung abgeschlossen ist, ist ein Zurücksetzen nicht möglich.

Die Zurücksetzung einer Aktualisierung ist beispielsweise nützlich, wenn Sie den Vorgang [Upgrade für Bereitstellung durchführen](/previous-versions/azure/reference/ee460793(v=azure.100)) im manuellen Modus ausführen, um die Rate zu kontrollieren, mit der ein umfangreiches direktes Upgrade für Ihren von Azure gehosteten Dienst zurückgesetzt wird.

Rufen Sie während der Zurücksetzung des Upgrades im manuellen Modus [Upgrade für Bereitstellung durchführen](/previous-versions/azure/reference/ee460793(v=azure.100)) auf, und gehen Sie die Upgradedomänen durch. Falls während der Überwachung des Upgrades einige Rolleninstanzen in den ersten Upgradedomänen, die Sie prüfen, nicht mehr reagieren, können Sie den Vorgang [Zurücksetzen der Aktualisierung oder des Upgrades](/previous-versions/azure/reference/hh403977(v=azure.100)) für die Bereitstellung ausführen. Die Instanzen, für die noch kein Upgrade durchgeführt wurde, sind davon nicht betroffen, und nur die Instanzen, die bereits aktualisiert wurden, werden auf das vorherige Dienstpaket und die vorherige Konfiguration zurückgesetzt.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Initiieren mehrerer Änderungsvorgänge für eine laufende Bereitstellung
In einigen Fällen sollen möglicherweise mehrere Änderungsvorgänge gleichzeitig für eine laufende Bereitstellung initiiert werden. Sie führen beispielsweise eine Dienstaktualisierung aus, und während die Aktualisierung auf den gesamten Dienst angewandt wird, möchten Sie einige Änderungen vornehmen, z. B. die Aktualisierung zurücksetzen, eine andere Aktualisierung anwenden oder die Bereitstellung löschen. Dies ist möglicherweise erforderlich, wenn ein Dienstupgrade fehlerhaften Code enthält, und die aktualisierte Rolleninstanz daher wiederholt abstürzt. In diesem Fall macht der Azure Fabric Controller keine Fortschritte beim Anwenden des Upgrades, da eine nicht ausreichende Anzahl an Instanzen in der aktualisierten Domäne fehlerfrei sind. Dieser Status wird als *unterbrochene Bereitstellung* bezeichnet. Sie können die Bereitstellung fortsetzen, indem Sie die Aktualisierung zurücksetzen oder eine neue Aktualisierung über das fehlerhafte Aktualisierung anwenden.

Sobald Azure Fabric Controller die ursprüngliche Aktualisierungs- oder Upgradeanforderung des Diensts erhalten hat, können Sie Änderungen vornehmen. Das heißt, Sie müssen nicht warten, bis der erste Vorgang abgeschlossen ist, bevor Sie einen anderen Änderungsvorgang starten können.

Wenn Sie einen zweiten Aktualisierungsvorgangs initiieren, während die erste Aktualisierung noch ausgeführt wird, dann wird dies ähnlich wie bei der Zurücksetzung ausgeführt. Wenn Sie die zweite Aktualisierung im automatischen Modus ausführen, wird die erste Upgradedomäne sofort aktualisiert. Dadurch sind Instanzen mehrerer Upgradedomänen möglicherweise gleichzeitig offline.

Verfügbare Änderungsvorgänge: [Bereitstellungskonfiguration ändern](/previous-versions/azure/reference/ee460809(v=azure.100)), [Upgrade für Bereitstellung durchführen](/previous-versions/azure/reference/ee460793(v=azure.100)), [Bereitstellungsstatus aktualisieren](/previous-versions/azure/reference/ee460808(v=azure.100)), [Bereitstellung löschen](/previous-versions/azure/reference/ee460815(v=azure.100)) und [Zurücksetzen der Aktualisierung oder des Upgrades](/previous-versions/azure/reference/hh403977(v=azure.100)).

Zwei Vorgänge ([Bereitstellung abrufen](/previous-versions/azure/reference/ee460804(v=azure.100)) und [Clouddiensteigenschaften abrufen](/previous-versions/azure/reference/ee460806(v=azure.100))) geben das Locked-Flag zurück, das Aufschluss darüber gibt, ob für eine bestimmte Bereitstellung ein Änderungsvorgang aufgerufen werden kann.

Wenn Sie die Version dieser Methoden aufrufen möchten, die die Kennzeichnung „Locked“ ausgibt, müssen Sie den Anforderungsheader auf „x-ms-version: 2011-10-01“ oder höher setzen. Weitere Informationen zu Versionsverwaltungsheadern finden Sie unter [Dienstverwaltungs-Versionsverwaltung](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Verteilung von Rollen über Upgradedomänen
Azure verteilt Instanzen einer Rolle gleichmäßig über eine festgelegte Anzahl von Upgradedomänen, die als Teil der Dienstdefinitionsdatei (.csdef) konfiguriert werden können. Die maximale Anzahl von Upgradedomänen ist 20, der Standardwert ist 5. Weitere Informationen zum Ändern der Dienstdefinitionsdatei finden Sie unter [Azure-Dienstdefinitionsschema (CSDEF-Datei)](cloud-services-model-and-package.md#csdef).

Wenn Ihre Rolle beispielsweise zehn Instanzen umfasst, enthält jede Upgraadedomäne standardmäßig zwei Instanzen. Wenn Ihre Rolle 14 Instanzen umfasst, dann enthalten vier Upgradedomänen drei Instanzen, und eine fünfte Domäne enthält zwei.

Upgradedomänen werden anhand eines nullbasierten Index identifiziert: Die erste Upgradedomäne hat die ID „0“, die zweite Upgradedomäne hat die ID „1“ und so weiter.

Das folgende Diagramm zeigt, wie ein Dienst mit zwei Rollen verteilt wird, wenn der Dienst zwei Upgradedomänen definiert. Der Dienst führt acht Instanzen der Webrolle und neun Instanzen der Workerrolle aus.

![Verteilung von Upgradedomänen](media/cloud-services-update-azure-service/IC345533.png "Verteilung von Upgradedomänen")

> [!NOTE]
> Beachten Sie, dass Azure steuert, wie Instanzen Upgradedomänen zugeordnet werden. Es kann nicht festgelegt werden, welche Instanzen welcher Domäne zugeordnet werden.
>
>

## <a name="next-steps"></a>Nächste Schritte
[Verwalten von Clouddiensten](cloud-services-how-to-manage-portal.md)  
[Überwachen von Clouddiensten](cloud-services-how-to-monitor.md)  
[Konfigurieren von Clouddiensten](cloud-services-how-to-configure-portal.md)  
