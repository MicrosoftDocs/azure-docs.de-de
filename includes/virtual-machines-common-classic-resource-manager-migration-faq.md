---
title: include file
description: include file
services: virtual-machines
author: jpconnock
ms.service: virtual-machines
ms.topic: include
ms.date: 05/18/2018
ms.author: jeconnoc
ms.custom: include file
ms.openlocfilehash: 74496cd3d4cd01be326baae870b075eb923983af
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178066"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Häufig gestellte Fragen zur Migration vom klassischen Bereitstellungsmodell zum Azure Resource Manager-Bereitstellungsmodell

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Wirkt sich dieser Migrationsplan auf meine vorhandenen Dienste oder Anwendungen aus, die auf virtuellen Azure-Computern ausgeführt werden? 

Nein. Die virtuellen Computer (klassisch) sind vollständig unterstützte Dienste mit allgemeiner Verfügbarkeit. Sie können diese Ressourcen weiterhin verwenden, um Ihre Nutzung von Microsoft Azure zu erweitern.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Was passiert mit meinen VMs, wenn ich für die nahe Zukunft keine Migration plane? 

Die vorhandenen klassischen APIs und das klassische Ressourcenmodell werden nicht eingestellt. In Anbetracht der erweiterten Features, die im Resource Manager-Bereitstellungsmodell zur Verfügung stehen, möchten wir die Migration möglichst einfach gestalten. Wir empfehlen Ihnen dringend, sich über [einige Weiterentwicklungen](../articles/azure-resource-manager/resource-manager-deployment-model.md) zu informieren, die Teil von IaaS unter Resource Manager sind.

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Was bedeutet dieser Migrationsplan für meine vorhandenen Tools? 

Die Aktualisierung Ihrer Tools auf das Resource Manager-Bereitstellungsmodell ist eine der wichtigsten Änderungen, die Sie in Ihren Migrationsplänen berücksichtigen sollten.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Wie lange dauert die Downtime der Verwaltungsebene? 

Dies hängt davon ab, wie viele Ressourcen migriert werden. Bei kleineren Bereitstellungen (einige Dutzend virtueller Computer) sollte der gesamte Migrationsprozess weniger als eine Stunde dauern. Bei größeren Bereitstellungen (Hunderte virtueller Computer) kann die Migration einige Stunden dauern.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Kann ich einen Rollback durchführen, nachdem für meine migrierten Ressourcen in Resource Manager ein Commit durchgeführt wurde? 

Sie können die Migration abbrechen, solange sich die Ressourcen im Zustand „Vorbereitet“ befinden. Der Rollback wird nicht unterstützt, nachdem die Ressourcen per Commitvorgang erfolgreich migriert wurden.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Kann ich für meine Migration einen Rollback durchführen, wenn beim Commitvorgang ein Fehler auftritt? 

Sie können die Migration nicht abbrechen, wenn für den Commitvorgang ein Fehler auftritt. Alle Migrationsvorgänge, einschließlich des Commitvorgangs, sind idempotent. Daher wird empfohlen, den Vorgang nach einer kurzen Wartezeit zu wiederholen. Sollten weiterhin Fehler auftreten, erstellen Sie ein Supportticket, oder posten Sie unter Verwendung des Tags ClassicIaaSMigration in unserem [VM-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Muss ich eine weitere ExpressRoute-Verbindung erwerben, wenn ich IaaS unter Resource Manager verwenden muss? 

Nein. Vor Kurzem haben wir das [Umstellen von ExpressRoute-Verbindungen vom klassischen Bereitstellungsmodell auf das Resource Manager-Bereitstellungsmodell](../articles/expressroute/expressroute-move.md)ermöglicht. Sie müssen keine neue ExpressRoute-Verbindung erwerben, wenn Sie bereits eine besitzen.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Was passiert, wenn ich für meine klassischen IaaS-Ressourcen Richtlinien für die rollenbasierte Zugriffssteuerung konfiguriert habe? 

Während der Migration wird für die Ressourcen die Transformation vom klassischen Bereitstellungsmodell zu Resource Manager durchgeführt. Es ist also ratsam, die Aktualisierungen der RBAC-Richtlinien zu planen, die nach der Migration durchgeführt werden müssen.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Ich habe meine klassischen virtuellen Computer in einem Tresor gesichert. Kann ich meine virtuellen Computer vom klassischen Modus in den Resource Manager-Modus migrieren und diese in einem Recovery Services-Tresor schützen?

<a name="vault">Wenn</a> Sie einen virtuellen Computer vom klassischen in den Resource Manager-Modus verschieben, werden Sicherungen, die vor der Migration aufgezeichnet wurden, nicht auf die neu migrierte Resourcen Manager-VM migriert. Wenn Sie die Sicherungen der klassischen VMs Jedoch aufbewahren möchten, gehen Sie vor der Migration folgendermaßen vor. 

1. Wechseln Sie im Recovery Services-Tresor zur Registerkarte **Geschützte Elemente**, und wählen Sie den virtuellen Computer aus. 
2. Klicken Sie auf „Schutz beenden“. Aktivieren Sie die Option *Zugeordnete Sicherungsdaten löschen***nicht**.

> [!NOTE]
> Ihnen werden Kosten für die Sicherungsinstanz berechnet, bis Sie die Daten beibehalten. Sicherungskopien werden gemäß der Beibehaltungsdauer gelöscht. Die letzte Sicherungskopie wird jedoch immer beibehalten, bis Sie Sicherungsdaten explizit löschen. Es wird empfohlen, die Beibehaltungsdauer des virtuellen Computers zu überprüfen und „Sicherungsdaten löschen“ für das geschützte Element im Tresor auszulösen, sobald die Beibehaltungsdauer überschritten wurde. 
>
>

So migrieren Sie den virtuellen Computer zum Resource Manager-Modus 

1. Löschen Sie die Sicherungs-/Momentaufnahmenerweiterung vom virtuellen Computer.
2. Migrieren Sie den virtuellen Computer vom klassischen Modus zum Resource Manager-Modus. Stellen Sie sicher, dass die Speicher- und Netzwerkinformationen des virtuellen Computers ebenfalls zum Resource Manager-Modus migriert werden.

Wenn Sie den migrierten virtuellen Computer darüber hinaus sichern möchten, wechseln Sie zum Blatt „Verwaltung virtueller Computer“, um [die Sicherung zu aktivieren](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Kann ich mein Abonnement oder meine Ressourcen überprüfen, um zu ermitteln, ob sie für die Migration geeignet sind? 

Ja. Bei der Option für die plattformgestützte Migration besteht der erste Schritt zum Vorbereiten der Migration darin, zu überprüfen, ob die Ressourcen für die Migration geeignet sind. Falls beim Überprüfungsvorgang ein Fehler auftritt, erhalten Sie alle Meldungen über alle Gründe, aus denen die Migration nicht abgeschlossen werden kann.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Was passiert, wenn ein Kontingentfehler auftritt, während ich die IaaS-Ressourcen für die Migration vorbereite? 

Wir empfehlen Ihnen, die Migration abzubrechen und anschließend eine Supportanfrage zu erstellen, um die Kontingente in der Region zu erhöhen, zu der Sie die virtuellen Computer migrieren. Nachdem die Kontingentanfrage genehmigt wurde, können Sie wieder mit dem Ausführen der Migrationsschritte beginnen.

## <a name="how-do-i-report-an-issue"></a>Wie melde ich ein Problem? 

Posten Sie Ihre Probleme und Fragen zur Migration mit dem Schlüsselwort ClassicIaaSMigration in unserem [VM-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows). Wir empfehlen, all Ihre Fragen in diesem Forum zu posten. Wenn Sie einen Supportvertrag haben, können Sie auch gerne ein Supportticket erstellen.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Was passiert, wenn mir die Namen der Ressourcen nicht gefallen, die von der Plattform während der Migration ausgewählt wurden? 

Alle Ressourcen, für die Sie unter dem klassischen Bereitstellungsmodell explizit Namen angeben, werden während der Migration beibehalten. In einigen Fällen werden neue Ressourcen erstellt. Beispiel: Für jeden virtuellen Computer wird eine Netzwerkschnittstelle erstellt. Derzeit wird das Steuern der Namen dieser neuen Ressourcen, die während der Migration erstellt werden, nicht unterstützt. Besuchen Sie das [Azure-Feedbackforum](http://feedback.azure.com), um für dieses Feature abzustimmen.

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Kann ich ExpressRoute-Verbindungen mit abonnementübergreifenden Autorisierungslinks migrieren? 

ExpressRoute-Verbindungen mit abonnementübergreifenden Autorisierungslinks können ohne Ausfallzeiten nicht automatisch migriert werden. Für deren Migration steht eine Anleitung mit manuellen Schritten zur Verfügung. Die entsprechenden Schritte sowie weitere Informationen finden Sie unter [Migrieren von ExpressRoute-Verbindungen und zugeordneten virtuellen Netzwerken vom klassischen Bereitstellungsmodell zum Resource Manager-Bereitstellungsmodell](../articles/expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Ich erhalte die Fehlermeldung *Die VM meldet den Gesamtstatus des Agents als ,Nicht bereit‘. Daher kann die VM nicht migriert werden. Stellen Sie sicher, dass der VM-Agent den Gesamtstatus des Agents als ,Bereit‘ meldet“* oder *„Die VM enthält eine Erweiterung, deren Status nicht von der VM gemeldet wird. Daher kann diese VM nicht migriert werden.*

Diese Meldung wird empfangen, wenn der virtuelle Computer keine ausgehende Verbindung mit dem Internet aufweist. Der VM-Agent verwendet ausgehende Verbindungen, um das Azure-Speicherkonto für die Aktualisierung des Agent-Status alle fünf Minuten zu erreichen.
