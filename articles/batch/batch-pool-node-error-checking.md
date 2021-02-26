---
title: Suchen nach Pool- und Knotenfehlern
description: In diesem Artikel werden die Hintergrundvorgänge behandelt, die auftreten können, sowie Fehler, auf die geprüft werden sollte, und wie diese beim Erstellen von Pools und Knoten vermieden werden können.
author: mscurrell
ms.author: markscu
ms.date: 02/03/2020
ms.topic: how-to
ms.openlocfilehash: 2b67eada5dfa89f95e2c9ae045c6bbe3fa0bb1ce
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576311"
---
# <a name="check-for-pool-and-node-errors"></a>Suchen nach Pool- und Knotenfehlern

Wenn Sie Azure Batch-Pools erstellen und verwalten, erfolgen einige Vorgänge sofort. Das Erkennen von Fehlern für diese Vorgänge ist in der Regel einfach, da diese sofort von der API, CLI oder Benutzeroberfläche zurückgegeben werden. Einige Vorgänge werden aber asynchron und im Hintergrund ausgeführt. Es kann einige Minuten dauern, bis der Vorgang abgeschlossen ist.

Überprüfen Sie, ob Sie Ihre Anwendungen so konfiguriert haben, dass eine umfassende Fehlerprüfung implementiert ist, insbesondere für asynchrone Vorgänge. Dies kann Ihnen helfen, Probleme schnell zu identifizieren und zu diagnostizieren.

In diesem Artikel wird beschrieben, wie Sie Fehler in den Hintergrundvorgängen erkennen und vermeiden können, die im Zusammenhang mit Pools und Poolknoten auftreten können.

## <a name="pool-errors"></a>Poolfehler

### <a name="resize-timeout-or-failure"></a>Timeout oder Fehler bei Größenänderung

Bei Erstellung eines neuen Pools oder Änderung der Größe eines vorhandenen Pools geben Sie die geplante Anzahl der Knoten an. Der Vorgang für die Erstellung oder Änderung der Größe wird unmittelbar abgeschlossen, aber die tatsächliche Zuordnung der neuen bzw. die Entfernung vorhandener Knoten kann einige Minuten dauern. Sie können das Timeout für die Größenänderung in der API [create](/rest/api/batchservice/pool/add) oder [resize](/rest/api/batchservice/pool/resize) angeben. Falls Batch die Zielanzahl von Knoten während des Timeouts für die Größenänderung nicht abrufen kann, wird der Pool in einen stabilen Zustand versetzt und meldet Größenänderungsfehler.

Mit der [ResizeError](/rest/api/batchservice/pool/get#resizeerror)-Eigenschaft für die aktuellen Evaluierungslisten werden die aufgetretenen Fehler aufgelistet.

Häufige Gründe für Größenänderungsfehler sind:

- Der Timeout für die Größenänderung ist zu kurz.
  - In den meisten Fällen ist das standardmäßige Timeout von 15 Minuten lang genug, um Poolknoten zuzuordnen oder zu entfernen.
  - Wenn Sie eine große Anzahl von Knoten zuordnen, empfehlen wir, das Timeout für die Größenänderung auf 30 Minuten festzulegen. Das gilt beispielsweise, wenn Sie die Größe über ein Azure Marketplace-Image auf mehr als 1.000 Knoten oder über ein benutzerdefinierte VM-Image auf mehr als 300 Knoten ändern.
- Kernkontingent nicht ausreichend
  - Ein Azure Batch-Konto weist eine Begrenzung für die Anzahl von Kernen auf, die poolübergreifend zugeordnet werden können. Sobald dieses Kontingent erschöpft ist, werden von Azure Batch keine weiteren Knoten zugeordnet. Sie können das Kernkontingent [erhöhen](./batch-quota-limit.md), sodass Azure Batch mehr Knoten zuordnen kann.
- Nicht genügend Subnetz-IP-Adressen, wenn sich ein [Pool in einem virtuellen Netzwerk](./batch-virtual-network.md) befindet
  - Das Subnetz eines virtuellen Netzwerks muss über ausreichend nicht zugewiesene IP-Adressen verfügen, die allen angeforderten Poolknoten zugeordnet werden können. Andernfalls können die Knoten nicht erstellt werden.
- Nicht genügend Ressourcen, wenn sich ein [Pool in einem virtuellen Netzwerk](./batch-virtual-network.md) befindet
  - Sie können Ressourcen, wie z. B. Lastenausgleichsmodule, öffentliche IP-Adressen und Netzwerksicherheitsgruppen, im selben Abonnement wie das Azure Batch-Konto erstellen. Vergewissern Sie sich, dass die Abonnementkontingente für diese Ressourcen ausreichend sind.
- Große Pools mit benutzerdefinierten VM-Images
  - Die Zuordnung großer Pools, die benutzerdefinierte Images verwenden, kann länger dauern und zu Timeouts bei Größenänderungen führen.  Empfehlungen zu Grenzwerten und zur Konfiguration finden Sie unter [Erstellen eines Pools mit Shared Image Gallery](batch-sig-images.md).

### <a name="automatic-scaling-failures"></a>Fehler bei der automatischen Skalierung

Sie können Azure Batch so einrichten, dass die Anzahl der Knoten in einem Pool automatisch skaliert wird. Sie definieren die Parameter für die [automatische Skalierungsformel für einen Pool](./batch-automatic-scaling.md). Der Azure Batch-Dienst verwendet dann die Formel, um die Anzahl der Knoten im Pool regelmäßig auszuwerten und eine neue Zielanzahl festzulegen.

Bei Verwendung der automatischen Skalierung können die folgenden Arten von Problemen auftreten:

- Die Auswertung für die automatische Skalierung schlägt fehl.
- Die resultierende Größenänderung schlägt fehl und führt zu einem Timeout.
- Ein Problem mit der automatischen Skalierungsformel führt zu falschen Zielwerten für die Knoten. Die Größenänderung funktioniert, oder ein Timeout tritt auf.

Verwenden Sie die Eigenschaft [autoScaleRun](/rest/api/batchservice/pool/get#autoscalerun), um Informationen zur letzten Auswertung für eine automatische Skalierung abzurufen. Diese Eigenschaft meldet die Auswertungszeit, die Werte und das Ergebnis sowie eventuelle Leistungsfehler.

Das [Ereignis zum Abschluss der Größenänderung von Pools](./batch-pool-resize-complete-event.md) erfasst Informationen zu sämtlichen Auswertungen.

### <a name="pool-deletion-failures"></a>Fehler beim Löschen von Pools

Wenn Sie einen Pool löschen, der Knoten enthält, löscht Azure Batch zuerst die Knoten. Das kann einige Minuten dauern. Danach löscht Batch das Poolobjekt selbst.

Der [Poolstatus](/rest/api/batchservice/pool/get#poolstate) wird von Azure Batch während des Löschvorgangs auf **Wird gelöscht** festgelegt. Die aufrufende Anwendung kann mithilfe der Eigenschaften **state** und **stateTransitionTime** erkennen, ob der Löschvorgang im Pool zu lange dauert.

## <a name="node-errors"></a>Knotenfehler

Selbst wenn Azure Batch Knoten in einem Pool erfolgreich zuweist, können verschiedene Probleme dazu führen, dass einige Knoten fehlerhaft sind und keine Tasks ausführen können. Da für diese Knoten weiterhin Gebühren anfallen, ist es wichtig, Probleme zu erkennen. So wird verhindert, dass für Knoten bezahlt werden muss, die nicht genutzt werden können. Neben häufigen Knotenfehlern ist es hilfreich, den aktuellen [Auftragsstatus](/rest/api/batchservice/job/get#jobstate) zu kennen.

### <a name="start-task-failures"></a>Fehler bei Starttask

Für einen Pool kann ein optionaler [Starttask](/rest/api/batchservice/pool/add#starttask) angegeben werden. Wie bei jedem Task können eine Befehlszeile und aus dem Speicher herunterzuladende Ressourcendateien angegeben werden. Der Starttask wird für jeden Knoten ausgeführt, nachdem er gestartet wurde. Die **waitForSuccess**-Eigenschaft gibt an, ob Azure Batch wartet, bis der Starttask erfolgreich abgeschlossen ist, ehe weitere Tasks für einen Knoten geplant werden.

Was passiert, wenn Sie den Knoten so konfiguriert haben, dass er auf die erfolgreiche Beendigung des Starttasks wartet, aber der Starttask fehlschlägt? In diesem Fall kann der Knoten nicht verwendet werden, aber es fallen weiterhin Gebühren dafür an.

Starttaskfehler können Sie mithilfe der Eigenschaften [result](/rest/api/batchservice/computenode/get#taskexecutionresult) und [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) der Knoteneigenschaft [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) der obersten Ebene erkennen.

Ein fehlgeschlagener Starttask führt auch dazu, dass Azure Batch den Knoten [state](/rest/api/batchservice/computenode/get#computenodestate) auf **starttaskfailed** festlegt, wenn **waitForSuccess** auf **TRUE** festgelegt wurde.

Wie bei jedem anderen Task kann es viele Ursachen für einen Fehler bei Starttasks geben. Zur Fehlerbehebung sollten Sie „stdout“, „stderr“ und weitere taskspezifische Protokolldateien überprüfen.

Starttasks müssen eintrittsinvariant sein, da es möglich ist, dass der Starttask mehrere Male auf demselben Knoten ausgeführt wird. Der Starttask wird ausgeführt, wenn für einen Knoten das Reimaging oder ein Neustart durchgeführt wird. In seltenen Fällen wird ein Starttask ausgeführt, nachdem ein Ereignis den Neustart eines Knotens verursacht hat, bei dem für einen der Betriebssystem- oder temporären Datenträger ein Reimaging durchgeführt wird, während dies für den anderen nicht der Fall war. Da Batch-Starttasks (wie alle Batch-Tasks) vom temporären Datenträger ausgeführt werden, ist dies normalerweise kein Problem. Aber in einigen Fällen, in denen der Starttask eine Anwendung auf dem Betriebssystemdatenträger installiert und andere Daten weiterhin auf dem temporären Datenträger aufbewahrt, kann dies zu Problemen führen, weil die Synchronisierung nicht mehr gewahrt ist. Schützen Sie Ihre Anwendung entsprechend, falls Sie beide Datenträger verwenden.

### <a name="application-package-download-failure"></a>Fehler beim Herunterladen des Anwendungspakets

Sie können ein oder mehrere Anwendungspakete für einen Pool angeben. Die angegebenen Paketdateien werden von Azure Batch auf die einzelnen Knoten heruntergeladen und nach dem Starten der Knoten, aber vor der Planung der Tasks, dekomprimiert. Normalerweise wird eine Befehlszeile für Starttasks in Verbindung mit Anwendungspaketen verwendet. Beispielsweise, um Dateien an einen anderen Speicherort zu kopieren oder ein Setup auszuführen.

Die [errors](/rest/api/batchservice/computenode/get#computenodeerror)-Eigenschaft des Knotens meldet einen Fehler beim Herunterladen und Entkomprimieren eines Anwendungspakets. Der Knotenstatus wird auf **Nicht verwendbar** festgelegt.

### <a name="container-download-failure"></a>Fehler beim Herunterladen von Containern

Sie können in einem Pool eine oder mehrere Containerreferenzen angeben. Batch lädt die angegebenen Container auf die einzelnen Knoten herunter. Die [Fehler](/rest/api/batchservice/computenode/get#computenodeerror)-Eigenschaft des Knotens meldet einen Fehler beim Herunterladen eines Containers und legt den Knotenstatus auf **Nicht verwendbar** fest.

### <a name="node-os-updates"></a>Betriebssystemupdates für Knoten

Für Windows-Pools ist `enableAutomaticUpdates` standardmäßig auf `true` festgelegt. Es wird empfohlen, automatische Updates zuzulassen, aber diese können den Fortschritt von Tasks unterbrechen, insbesondere wenn diese zeitintensiv sind. Sie können diesen Wert auf `false` festlegen, wenn Sie sicherstellen müssen, dass ein Betriebssystemupdate nicht unerwartet durchgeführt werden soll.

### <a name="node-in-unusable-state"></a>Knoten mit dem Status „Nicht verwendbar“

Es kann verschiedene Ursachen dafür geben, dass Azure Batch den [Knotenstatus](/rest/api/batchservice/computenode/get#computenodestate) auf **Nicht verwendbar** festlegt. Wenn der Knotenstatus auf **Nicht verwendbar** festgelegt ist, können für den Knoten zwar keine Tasks geplant werden, es fallen jedoch weiterhin Gebühren für ihn an.

Knoten mit dem Zustand **Nicht verwendbar** – aber ohne [Fehler](/rest/api/batchservice/computenode/get#computenodeerror) – bedeuten, dass Batch nicht mit dem virtuellen Computer kommunizieren kann. In diesem Fall versucht Batch immer, den virtuellen Computer wiederherzustellen. Batch versucht nicht automatisch, virtuelle Computer wiederherzustellen, die keine Anwendungspakete oder Container installiert haben, obwohl ihr Zustand **Nicht verwendbar** ist.

Wenn Azure Batch die Ursache bestimmen kann, wird sie von der Knoteneigenschaft [errors](/rest/api/batchservice/computenode/get#computenodeerror) gemeldet.

Einige weitere Beispiele für Ursachen für **nicht verwendbare** Knoten sind u. a.:

- Ein benutzerdefiniertes VM-Image ist ungültig. Beispielsweise, wenn ein Image nicht ordnungsgemäß vorbereitet wurde.

- Eine VM wird aufgrund eines Infrastrukturausfalls oder eines niedrigstufigen Upgrades verschoben. Azure Batch stellt den Knoten wieder her.

- Ein VM-Image wurde auf Hardware bereitgestellt, von der es nicht unterstützt wird. Beispiel: Der Versuch, ein CentOS-HPC-Image auf einem virtuellen [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md)-Computer auszuführen.

- Die VMs befinden sich in einem [virtuellen Azure-Netzwerk](batch-virtual-network.md), und der Datenverkehr zu den Schlüsselports wurde blockiert.

- Die VMs befinden sich in einem virtuellen Netzwerk, aber der ausgehende Datenverkehr an Azure Storage ist blockiert.

- Die VMs befinden sich in einem virtuellen Netzwerk mit einer DNS-Kundenkonfiguration, und der DNS-Server kann Azure Storage nicht auflösen.

### <a name="node-agent-log-files"></a>Protokolldateien des Knoten-Agents

Der Batch-Agent-Prozess, der auf jedem Poolknoten ausgeführt wird, kann Protokolldateien bereitstellen, die hilfreich sein können, wenn Sie den Support bei einem Problem mit einem Poolknoten kontaktieren müssen. Protokolldateien für einen Knoten können über das Azure-Portal, Batch Explorer oder eine [API](/rest/api/batchservice/computenode/uploadbatchservicelogs) hochgeladen werden. Es ist sinnvoll, die Protokolldateien hochzuladen und zu speichern. Anschließend können Sie den Knoten oder Pool löschen, um die Kosten für die ausgeführten Knoten zu sparen.

### <a name="node-disk-full"></a>Knotendatenträger voll

Das temporäre Laufwerk für eine Poolknoten-VM wird von Batch für Auftragsdateien, Taskdateien und gemeinsam genutzte Dateien wie die folgenden verwendet:

- Anwendungspaketdateien
- Taskressourcendateien
- Anwendungsspezifische Dateien, die in einen Batch-Ordner heruntergeladen wurden
- stdout- und stderr-Dateien für jede Ausführung von Taskanwendungen
- Anwendungsspezifische Ausgabedateien

Einige dieser Dateien werden nur einmal geschrieben, wenn die Poolknoten erstellt werden, z. B. Poolanwendungspakete oder Poolstarttask-Ressourcendateien. Auch wenn diese Dateien nur einmal bei der Knotenerstellung geschrieben werden, können sie den gesamten Speicherplatz des temporären Laufwerks belegen, falls sie zu groß sind.

Andere Dateien werden für jeden Task geschrieben, der auf einem Knoten ausgeführt wird, z. B. stdout and stderr. Wenn eine große Zahl von Tasks auf demselben Knoten ausgeführt wird bzw. die Taskdateien zu groß sind, ist das temporäre Laufwerk damit unter Umständen bereits gefüllt.

Die Größe des temporären Laufwerks hängt von der Größe des virtuellen Computers ab. Ein wichtiger Aspekt bei der Wahl der VM-Größe besteht darin, sich zu vergewissern, dass auf dem temporären Laufwerk genügend Platz ist.

- Im Azure-Portal kann beim Hinzufügen eines Pools die vollständige Liste mit VM-Größen angezeigt werden, und die Spalte „Ressourcen-Datenträgergröße“ ist vorhanden.
- Die Artikel, in denen die gesamten VM-Größen beschrieben werden, verfügen über Tabellen mit der Spalte „Temporärer Speicher“, z. B. [Compute-optimierte VM-Größen](../virtual-machines/sizes-compute.md).

Für Dateien, die von den einzelnen Tasks geschrieben werden, kann für jeden Task eine Aufbewahrungsdauer angegeben werden. Hiermit wird festgelegt, wie lange die Taskdateien aufbewahrt werden, bevor sie automatisch bereinigt werden. Die Aufbewahrungszeit kann reduziert werden, um den Speicherbedarf zu verringern.

Wenn auf dem temporären Datenträger nicht genügend Speicherplatz zur Verfügung steht (oder der Speicherplatz knapp wird), wechselt der Knoten in den Zustand [Nicht verwendbar](/rest/api/batchservice/computenode/get#computenodestate), und es wird ein Knotenfehler mit dem Hinweis gemeldet, dass der Datenträger voll ist.

Sollten Sie nicht sicher sein, wodurch der Speicherplatz auf dem Knoten beansprucht wird, versuchen Sie, eine Remoteverbindung mit dem Knoten herzustellen und die Speicherplatzbeanspruchung manuell zu untersuchen. Sie können auch die [Batch-API zum Auflisten von Dateien](/rest/api/batchservice/file/listfromcomputenode) verwenden, um Dateien in von Batch verwalteten Ordnern zu untersuchen (beispielsweise Aufgabenausgaben). Beachten Sie, dass diese API nur Dateien in den von Batch verwalteten Verzeichnissen auflistet. Wenn Ihre Tasks Dateien an einem anderen Speicherort erstellt haben, werden diese nicht angezeigt.

Stellen Sie sicher, dass alle benötigten Daten von dem Knoten abgerufen oder in einen permanenten Speicher hochgeladen wurden, und löschen Sie dann nach Bedarf die Daten, um Speicherplatz freizugeben.

Sie können alte abgeschlossene Aufträge oder alte abgeschlossene Tasks löschen, deren Taskdaten sich noch auf den Knoten befinden. Suchen Sie in der [Sammlung „RecentTasks“](/rest/api/batchservice/computenode/get#taskinformation) auf dem Knoten oder in den [Dateien auf dem Knoten](/rest/api/batchservice/file/listfromcomputenode). Durch das Löschen eines Auftrags werden alle Tasks im Auftrag gelöscht. Durch das Löschen der Tasks im Auftrag wird das Löschen der Daten in den Taskverzeichnissen auf dem Knoten ausgelöst, wodurch Speicherplatz freigegeben wird. Starten Sie den Knoten neu, nachdem Sie genügend Speicherplatz freigegeben haben. Daraufhin sollte der Zustand von „Nicht verwendbar“ wieder in „Leerlauf“ übergehen.

Sie können mithilfe der [API zum Entfernen von Knoten](/rest/api/batchservice/pool/removenodes) einen Knoten aus dem Pool entfernen, um einen nicht verwendbaren Knoten in [VirtualMachineConfigurationn](/rest/api/batchservice/pool/add#virtualmachineconfiguration)-Pools wiederherzustellen. Anschließend können Sie den Pool wieder vergrößern, um den fehlerhaften Knoten durch einen neuen Knoten zu ersetzen. Für [CloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration)-Pools können Sie über die [Batch-API für das Erstellen neuer Images](/rest/api/batchservice/computenode/reimage) ein neues Image des Knotens erstellen. Dadurch wird der gesamte Datenträger bereinigt. Für Pools vom Typ [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) wird das Reimaging derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Überprüfung auf Auftrags- und Taskfehler](batch-job-task-error-checking.md).
- Informieren Sie sich über [bewährte Methoden](best-practices.md) für die Arbeit mit Azure Batch.
