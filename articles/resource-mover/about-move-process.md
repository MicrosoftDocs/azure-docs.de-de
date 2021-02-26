---
title: Informationen zum Verschiebungsprozess in Azure Resource Mover
description: Hier erfahren Sie mehr über den Prozess zum Verschieben von Ressourcen zwischen Regionen mit Azure Resource Mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 02/01/2021
ms.author: raynew
ms.openlocfilehash: facbb30201aa6bde2044ca647383cc32ecd9ba26
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980557"
---
# <a name="about-the-move-process"></a>Informationen zum Verschiebungsprozess

Mit [Azure Resource Mover](overview.md) können Sie Azure-Ressourcen zwischen Azure-Regionen verschieben. Dieser Artikel enthält eine Zusammenfassung der von Resource Mover verwendeten Komponenten sowie eine Beschreibung des Verschiebungsprozesses. 


## <a name="components"></a>Komponenten

Bei der Verschiebung in eine andere Region werden die folgenden Komponenten verwendet.

**Komponente** | **Details**
--- | ---
**Resource Mover** |  Resource Mover arbeitet mit [Azure-Ressourcenanbietern](../azure-resource-manager/management/resource-providers-and-types.md) zusammen, um die Verschiebung von Ressourcen zwischen Regionen zu organisieren. Der Dienst analysiert Ressourcenabhängigkeiten und legt den Zustand von Ressourcen während des Verschiebungsprozesses fest und verwaltet ihn. 
**Verschiebungssammlung** |  Verschiebungssammlungen sind [Azure Resource Manager](../azure-resource-manager/management/overview.md)-Objekte.<br/><br/> Sie werden während des Verschiebungsprozesses in eine andere Region für jede verknüpfte Kombination aus Quell- und Zielregionen in einem Abonnement erstellt. Die Sammlung enthält Metadaten und Konfigurationsinformationen über die Ressourcen, die Sie verschieben möchten.<br/><br/>Zu einer Verschiebungssammlung hinzugefügte Ressourcen müssen sich im selben Abonnement befinden, können jedoch zu unterschiedlichen Ressourcengruppen gehören. 
**Verschiebungsressource** | Wenn Sie eine Ressource zu einer Verschiebungssammlung hinzufügen, wird diese von Resource Mover als Verschiebungsressource überwacht.<br/><br/> Resource Mover verwaltet Informationen für alle Verschiebungsressourcen in der Verschiebungssammlung und sorgt für eine 1:1-Beziehung zwischen der Quell- und der Zielressource. 
**Abhängigkeiten** | Resource Mover überprüft die Ressourcen, die Sie zu einer Sammlung hinzufügen, und prüft, ob diese über Abhängigkeiten verfügen, die sich nicht in der Verschiebungssammlung befinden.<br/><br/> Wenn solche Abhängigkeiten für eine Ressource ermittelt wurden, können Sie diese entweder zur Verschiebungssammlung hinzufügen und ebenfalls verschieben oder andere Ressourcen auswählen, die in der Zielregion bereits vorhanden sind. Dieses Problem muss vor Beginn der Verschiebung für alle Abhängigkeiten behoben werden. 



## <a name="move-region-process"></a>Verschiebungsprozess in eine andere Region 

![Diagramm mit den Verschiebungsschritten](./media/about-move-process/move-steps.png)

Alle Verschiebungsressourcen durchlaufen die zusammengefassten Schritte.

**Schritt** | **Details** | **Zustand/Probleme**
--- | --- | --- 
**Schritt 1: Auswählen der Ressourcen** | Wählen Sie eine Ressource aus. Die Ressource wird zur Verschiebungssammlung hinzugefügt. | Der Zustand der Ressource ändert sich in *Prepare pending* (Vorbereitung ausstehend).<br/><br/> Wenn die Ressource über Abhängigkeiten verfügt, gibt *Validate dependency* (Abhängigkeit überprüfen) an, dass Sie abhängige Ressourcen zur Verschiebungssammlung hinzufügen müssen.
**Schritt 2: Überprüfen von Abhängigkeiten** | Starten Sie den Überprüfungsprozess.<br/><br/> Wenn die Überprüfung zeigt, dass noch abhängige Ressourcen fehlen, fügen Sie diese zur Verschiebungssammlung hinzu. <br/><br/> Fügen Sie alle abhängigen Ressourcen hinzu, auch wenn Sie sie nicht verschieben möchten. Sie können später angeben, dass für die Ressourcen, die Sie verschieben, in der Zielregion stattdessen andere Ressourcen verwendet werden sollen.<br/><br/> Führen Sie die Überprüfung noch mal durch, bis keine Abhängigkeiten mehr hinzugefügt werden müssen. | Wenn alle Abhängigkeiten hinzugefügt wurden und die Überprüfung erfolgreich abgeschlossen wurde, ändert sich der Zustand der Ressource in *Prepare pending* (Vorbereitung ausstehend), ohne dass Probleme auftreten.
**Schritt 3: Vorbereiten** | Starten Sie den Vorbereitungsprozess. Die Vorbereitungsschritte hängen von den Ressourcen ab, die Sie verschieben:<br/><br/> - **Zustandslose Ressourcen**: Zustandslose Ressourcen verfügen nur über Konfigurationsinformationen. Für die Verschiebung dieser Ressourcen ist keine fortlaufende Replikation von Daten erforderlich. Beispiele hierfür sind virtuelle Azure-Netzwerke (VNets), Netzwerkadapter, Lastenausgleiche und Netzwerksicherheitsgruppen. Bei diesem Ressourcentyp wird während des Vorbereitungsprozesses eine Azure Resource Manager-Vorlage generiert.<br/><br/> - **Zustandsbehaftete Ressourcen**: Zustandsbehaftete Ressourcen verfügen sowohl über Konfigurationsinformationen als auch über Daten, die verschoben werden müssen. Beispiele hierfür sind Azure-VMs und Azure SQL-Datenbanken. Der Vorbereitungsprozess ist für jede Ressource unterschiedlich. Er umfasst möglicherweise das Replizieren der Quellressource in die Zielregion. | Beim Starten des Prozesses wird der Zustand der Ressource in *Prepare in progress* (Vorbereitung läuft) geändert.<br/><br/> Nach Abschluss der Vorbereitung ändert sich der Zustand der Ressource in *Initiate move pending* (Initiieren der Verschiebung ausstehend), ohne dass Probleme auftreten.<br/><br/> Wenn der Prozess nicht erfolgreich abgeschlossen wurde, ändert sich der Zustand in *Prepare failed* (Fehler bei der Vorbereitung).
**Schritt 4: Initiieren der Verschiebung** | Starten Sie den Verschiebungsprozess. Die Verschiebungsmethode hängt vom Ressourcentyp ab:<br/><br/> - **Zustandslos**: Bei zustandslosen Ressourcen stellt der Verschiebungsprozess in der Regel eine importierte Vorlage in der Zielregion bereit. Diese Vorlage basiert auf den Quellressourceneinstellungen sowie allen manuellen Änderungen, die Sie an den Zieleinstellungen vornehmen.<br/><br/> - **Zustandsbehaftet**: Bei zustandsbehafteten Ressourcen umfasst der Verschiebungsprozess möglicherweise das Erstellen der Ressource oder Aktivieren einer Kopie in der Zielregion.<br/><br/>  Nur bei zustandsbehafteten Ressourcen kann das Initiieren einer Verschiebung zu einer Downtime der Quellressourcen führen. Beispiele sind VMs und SQL. | Beim Starten des Prozesses wird der Zustand in *Initiate move in progress* (Initiieren der Verschiebung läuft) geändert.<br/><br/> Bei einem erfolgreichen Initiieren der Verschiebung ändert sich der Zustand der Ressource in *Commit move pending* (Committen der Verschiebung ausstehend), ohne dass Probleme auftreten. <br/><br/> Bei einem nicht erfolgreichen Verschiebungsprozess ändert sich der Zustand in *Initiate move failed* (Fehler beim Initiieren der Verschiebung).
**Schritt 5, Option 1: Verwerfen der Verschiebung** | Nach der anfänglichen Verschiebung können Sie entscheiden, ob Sie mit der vollständigen Verschiebung fortfahren möchten. Wenn nicht, können Sie die Verschiebung verwerfen, und Resource Mover löscht die im Ziel erstellten Ressourcen. Nach dem Verwerfungsprozess wird der Replikationsprozess für zustandsbehaftete Ressourcen fortgesetzt. Diese Option ist nützlich für Tests. | Durch das Verwerfen von Ressourcen ändert sich der Zustand in *Discard in progress* (Verwerfung läuft).<br/><br/> Wird die Verschiebung erfolgreich verworfen, ändert sich der Zustand in *Initiate move pending* (Initiieren der Verschiebung ausstehend), ohne dass Probleme auftreten.<br/><br/> Tritt beim Verwerfen ein Fehler auf, ändert sich der Zustand in *Discard move failed* (Fehler beim Verwerfen der Verschiebung). 
**Schritt 5, Option 2: Committen der Verschiebung** | Wenn Sie nach der anfänglichen Verschiebung mit der vollständigen Verschiebung fortfahren möchten, überprüfen Sie die Ressourcen in der Zielregion, und committen Sie die Verschiebung, wenn Sie bereit sind.<br/><br/> Nur bei zustandsbehafteten Ressourcen kann ein Commit dazu führen, dass auf Quellressourcen wie VMs oder SQL nicht mehr zugegriffen werden kann. | Wenn Sie die Verschiebung committen, ändert sich der Zustand der Ressource in *Commit move in progress** (Committen der Verschiebung läuft).<br/><br/> Nach einem erfolgreichen Commit ändert sich der Zustand der Ressource in *Commit move completed* (Committen der Verschiebung abgeschlossen), ohne dass Probleme auftreten.<br/><br/> Tritt beim Committen der Verschiebung ein Fehler auf, ändert sich der Zustand in *Commit move failed* (Fehler beim Committen der Verschiebung).
**Schritt 6: Löschen der Quelle** | Nach dem Committen der Verschiebung und Überprüfen der Ressourcen in der Zielregion können Sie die Quellressource löschen. | Nach dem Committen ändert sich der Zustand der Ressource in *Delete source pending* (Löschen der Quelle ausstehend). Sie können dann die Quellressource auswählen und löschen.<br/><br/> - Nur Ressourcen im Zustand *Delete source pending* (Löschen der Quelle ausstehend) können gelöscht werden. | Das Löschen einer Ressourcengruppe oder SQL Server-Instanz im Resource Mover-Portal wird nicht unterstützt. Diese Ressourcen können nur auf der Seite mit den Ressourceneigenschaften gelöscht werden.


## <a name="move-region-states"></a>Zustände bei der Verschiebung in eine andere Region

Der Verschiebungsprozess umfasst eine Reihe von Zuständen und Problemen, die bei diesen Zuständen jeweils auftreten können. Diese sind im folgenden Flussdiagramm zusammengefasst:

![Flussdiagramm mit möglichen Zuständen und Problemen](./media/about-move-process/process.png)


### <a name="dependency-analysis"></a>Abhängigkeitsanalyse

Beim Durchlaufen des Verschiebungsprozesses werden Sie in den folgenden Fällen möglicherweise aufgefordert, Abhängigkeiten zu überprüfen:
- Eine Ressource verwendet abhängige Ressourcen, die sich nicht in der Verschiebungssammlung befinden.
- Eine abhängige Ressource in der Verschiebungssammlung verfügt über eigene Abhängigkeiten, die sich nicht in der Verschiebungssammlung befinden.
- Sie haben die Zieleinstellungen für die Ressource geändert und müssen die Abhängigkeiten erneut überprüfen.


### <a name="remove-resources"></a>Entfernen von Ressourcen

Wenn Sie eine Ressource nicht verschieben möchten, können Sie sie aus der Verschiebungssammlung entfernen. Im Allgemeinen wird die Ressource dann zusammen mit allen zugehörigen Aktionen oder Objekten, z. B. der Replikation oder gespeicherten Vorlagen, aus der Sammlung gelöscht. Was genau passiert, wenn Sie eine Ressource entfernen, hängt vom Ressourcentyp und dem Zustand der Ressource beim Löschen ab. [Weitere Informationen](remove-move-resources.md)

## <a name="move-impact"></a>Auswirkungen der Verschiebung

In der folgenden Tabelle ist zusammengefasst, worauf eine Verschiebung in eine andere Region Auswirkungen hat.

**Verhalten** | **bei Verschiebung in eine andere Region**
--- | --- | --- 
**Daten** | Ressourcendaten und Metadaten werden verschoben.<br/><br/> Metadaten werden temporär gespeichert, um den Zustand von Ressourcenabhängigkeiten und -vorgängen nachzuverfolgen.
**Ressource** | Die Quellressource bleibt intakt, um sicherzustellen, dass Apps weiterhin funktionieren, und kann optional nach der Verschiebung entfernt werden.<br/><br/> Eine Ressource in der Zielregion wird erstellt.
**Verschiebungsvorgang** | Prozess in mehreren Schritten, der ein manuelles Eingreifen und Überwachung erfordert
**Testen** | Das Testen der Verschiebung ist wichtig, da die Apps nach dem Verschieben in der Zielregion weiterhin erwartungsgemäß funktionieren sollen.
**Ausfallzeit** |  Es wird kein Datenverlust erwartet, aber Downtime zum Verschieben von Ressourcen.



## <a name="next-steps"></a>Nächste Schritte

[Verschieben](tutorial-move-region-virtual-machines.md) Sie Azure-VMs in eine andere Region.
[Verschieben](tutorial-move-region-sql.md) Sie Azure SQL-Ressourcen in eine andere Region.