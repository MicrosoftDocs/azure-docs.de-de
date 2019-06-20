---
title: Suchen nach Pool- und Knotenfehlern – Azure Batch
description: Erfahren Sie mehr über das Suchen nach Fehlern und Vermeiden dieser Fehler bei der Erstellung von Pools und Knoten.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 05/28/2019
ms.topic: conceptual
ms.openlocfilehash: b0a9d04fccce7ccbacb700f7af5126c6ae05140a
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357760"
---
# <a name="check-for-pool-and-node-errors"></a>Suchen nach Pool- und Knotenfehlern

Wenn Sie Azure Batch-Pools erstellen und verwalten, erfolgen einige Vorgänge sofort. Allerdings sind einige Vorgänge asynchron und werden im Hintergrund ausgeführt. Diese können einige Minuten dauern.

Das Erkennen von Fehlern bei unmittelbar erfolgenden Vorgängen, ist einfach, da sämtliche Fehler direkt über die API, CLI oder Benutzeroberfläche zurückgegeben werden.

In diesem Artikel werden die Hintergrundvorgänge behandelt, die für Pools und Poolknoten erfolgen können. Es wird erörtert, wie Sie Fehler erkennen und vermeiden.

## <a name="pool-errors"></a>Poolfehler

### <a name="resize-timeout-or-failure"></a>Timeout oder Fehler bei Größenänderung

Bei Erstellung eines neuen Pools oder Änderung der Größe eines vorhandenen Pools geben Sie die geplante Anzahl der Knoten an.  Der Vorgang wird unmittelbar abgeschlossen, aber die tatsächliche Zuordnung der neuen bzw. das Entfernen vorhandener Knoten kann einige Minuten dauern.  Sie geben das Timeout bei Größenänderung in der API [create](https://docs.microsoft.com/rest/api/batchservice/pool/add) oder [resize](https://docs.microsoft.com/rest/api/batchservice/pool/resize) an. Wenn Azure Batch die Zielanzahl der Knoten innerhalb des Timeoutzeitraums für die Größenänderung nicht abrufen kann, wird der Vorgang beendet. Der Pool wechselt in einen stabilen Zustand und meldet Größenänderungsfehler.

Die [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror)-Eigenschaft für die letzte Auswertung meldet ein Timeout bei der Größenänderung und listet alle aufgetretenen Fehler auf.

Zu den häufigen Ursachen für Timeouts bei Größenänderungen zählen die folgenden:

- Der Timeout für die Größenänderung ist zu kurz.
  - In den meisten Fällen ist das standardmäßige Timeout von 15 Minuten lang genug, um Poolknoten zuzuordnen oder zu entfernen.
  - Wenn Sie eine große Anzahl von Knoten zuordnen, empfehlen wir, das Timeout für die Größenänderung auf 30 Minuten festzulegen. Das gilt beispielsweise, wenn Sie die Größe über ein Azure Marketplace-Image auf mehr als 1.000 Knoten oder über ein benutzerdefinierte VM-Image auf mehr als 300 Knoten ändern.
- Kernkontingent nicht ausreichend
  - Ein Azure Batch-Konto weist eine Begrenzung für die Anzahl von Kernen auf, die poolübergreifend zugeordnet werden können. Sobald dieses Kontingent erschöpft ist, werden von Azure Batch keine weiteren Knoten zugeordnet. Sie können das Kernkontingent [erhöhen](https://docs.microsoft.com/azure/batch/batch-quota-limit), sodass Azure Batch mehr Knoten zuordnen kann.
- Nicht genügend Subnetz-IP-Adressen, wenn sich ein [Pool in einem virtuellen Netzwerk](https://docs.microsoft.com/azure/batch/batch-virtual-network) befindet
  - Das Subnetz eines virtuellen Netzwerks muss über ausreichend nicht zugewiesene IP-Adressen verfügen, die allen angeforderten Poolknoten zugeordnet werden können. Andernfalls können die Knoten nicht erstellt werden.
- Nicht genügend Ressourcen, wenn sich ein [Pool in einem virtuellen Netzwerk](https://docs.microsoft.com/azure/batch/batch-virtual-network) befindet
  - Sie können Ressourcen, wie z. B. Lastenausgleichsmodule, öffentliche IP-Adressen und Netzwerksicherheitsgruppen, im selben Abonnement wie das Azure Batch-Konto erstellen. Vergewissern Sie sich, dass die Abonnementkontingente für diese Ressourcen ausreichend sind.
- Große Pools mit benutzerdefinierten VM-Images
  - Die Zuordnung großer Pools, die benutzerdefinierte Images verwenden, kann länger dauern und zu Timeouts bei Größenänderungen führen.  Empfehlungen zu Grenzwerten und zur Konfiguration finden Sie unter [Verwenden eines benutzerdefinierten Images zum Erstellen eines VM-Pools](https://docs.microsoft.com/azure/batch/batch-custom-images).

### <a name="automatic-scaling-failures"></a>Fehler bei der automatischen Skalierung

Sie können Azure Batch auch so einrichten, dass die Anzahl der Knoten in einem Pool automatisch skaliert wird. Sie definieren die Parameter für die [automatische Skalierungsformel für einen Pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). Der Azure Batch-Dienst verwendet die Formel, um die Anzahl der Knoten im Pool regelmäßig auszuwerten und eine neue Zielanzahl festzulegen. Die folgenden Arten von Problemen können auftreten:

- Die Auswertung für die automatische Skalierung schlägt fehl.
- Die resultierende Größenänderung schlägt fehl und führt zu einem Timeout.
- Ein Problem mit der automatischen Skalierungsformel führt zu falschen Zielwerten für die Knoten. Die Größenänderung funktioniert, oder ein Timeout tritt auf.

Informationen zur letzten Auswertung zur automatischen Skalierung erhalten Sie mithilfe der [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun)-Eigenschaft. Diese Eigenschaft meldet die Auswertungszeit, die Werte und das Ergebnis sowie eventuelle Leistungsfehler.

Das [Ereignis zum Abschluss der Größenänderung von Pools](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) erfasst Informationen zu sämtlichen Auswertungen.

### <a name="delete"></a>Löschen

Wenn Sie einen Pool löschen, der Knoten enthält, löscht Azure Batch zuerst die Knoten. Anschließend wird das Poolobjekt selbst gelöscht. Es kann einige Minuten dauern, bis die Poolknoten gelöscht wurden.

Der [Poolstatus](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) wird von Azure Batch während des Löschvorgangs auf **Wird gelöscht** festgelegt. Die aufrufende Anwendung kann mithilfe der Eigenschaften **state** und **stateTransitionTime** erkennen, ob der Löschvorgang im Pool zu lange dauert.

## <a name="pool-compute-node-errors"></a>Fehler in Pool-Computeknoten

Selbst wenn Azure Batch Knoten in einem Pool erfolgreich zuweist, können verschiedene Probleme dazu führen, dass einige der Knoten fehlerhaft und unbrauchbar sind. Diese Knoten verursachen Kosten. Es ist wichtig, Probleme zu erkennen, damit Sie nicht für unbrauchbare Knoten zahlen.

### <a name="start-task-failure"></a>Fehler bei Starttask

Für einen Pool kann ein optionaler [Starttask](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) angegeben werden. Wie bei jedem Task können eine Befehlszeile und aus dem Speicher herunterzuladende Ressourcendateien angegeben werden. Der Starttask wird für jeden Knoten ausgeführt, nachdem er gestartet wurde. Die **waitForSuccess**-Eigenschaft gibt an, ob Azure Batch wartet, bis der Starttask erfolgreich abgeschlossen ist, ehe weitere Tasks für einen Knoten geplant werden.

Was passiert, wenn Sie den Knoten so konfiguriert haben, dass er auf die erfolgreiche Beendigung des Starttasks wartet, aber der Starttask fehlschlägt? In diesem Fall ist der Knoten unbrauchbar, verursacht aber dennoch Kosten.

Starttaskfehler können Sie mithilfe der Eigenschaften [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) und [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) der Knoteneigenschaft [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) der obersten Ebene erkennen.

Ein fehlgeschlagener Starttask führt auch dazu, dass Azure Batch den Knoten [state](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) auf **starttaskfailed** festlegt, jedoch nur, wenn Sie **waitForSuccess** auf **TRUE** festgelegt haben.

Wie bei jedem anderen Task kann es für das Fehlschlagen eines Starttasks viele Ursachen geben.  Zur Fehlerbehebung sollten Sie „stdout“, „stderr“ und weitere taskspezifische Protokolldateien überprüfen.

### <a name="application-package-download-failure"></a>Fehler beim Herunterladen des Anwendungspakets

Sie können ein oder mehrere Anwendungspakete für einen Pool angeben. Die angegebenen Paketdateien werden von Azure Batch auf die einzelnen Knoten heruntergeladen und nach dem Starten der Knoten, aber vor Planung der Tasks, dekomprimiert. Normalerweise wird eine Befehlszeile für Starttasks in Verbindung mit Anwendungspaketen verwendet. Beispielsweise, um Dateien an einen anderen Speicherort zu kopieren oder ein Setup auszuführen.

Die Knoteneigenschaft [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) meldet einen Fehler beim Herunterladen und Dekomprimieren eines Anwendungspakets. Azure Batch legt den Knotenstatus auf **Nicht verwendbar** fest.

### <a name="container-download-failure"></a>Fehler beim Herunterladen von Containern

Sie können in einem Pool eine oder mehrere Containerreferenzen angeben. Batch lädt die angegebenen Container auf die einzelnen Knoten herunter. Die [Fehler](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)-Eigenschaft des Knotens meldet einen Fehler beim Herunterladen eines Containers und legt den Knotenstatus auf **Nicht verwendbar** fest.

### <a name="node-in-unusable-state"></a>Knoten mit dem Status „Nicht verwendbar“

Es kann verschiedene Ursachen dafür geben, dass Azure Batch den [Knotenstatus](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) auf **Nicht verwendbar** festlegt. Wenn der Knotenstatus auf **Nicht verwendbar** festgelegt ist, können für den Knoten zwar keine Tasks geplant werden, es fallen jedoch weiterhin Gebühren für ihn an.

Knoten mit dem Zustand **Nicht verwendbar**, aber ohne [Fehler](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)-Zustand bedeuten, dass Batch nicht mit dem virtuellen Computer kommunizieren kann. In diesem Fall versucht Batch immer, den virtuellen Computer wiederherzustellen. Batch versucht nicht automatisch, virtuelle Computer wiederherzustellen, die keine Anwendungspakete oder Container installiert haben, obwohl ihr Zustand **Nicht verwendbar** ist.

Wenn Azure Batch die Ursache bestimmen kann, wird sie von der Knoteneigenschaft [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) gemeldet.

Einige weitere Beispiele für Ursachen für **nicht verwendbare** Knoten sind u. a.:

- Ein benutzerdefiniertes VM-Image ist ungültig. Beispielsweise, wenn ein Image nicht ordnungsgemäß vorbereitet wurde.

- Eine VM wird aufgrund eines Infrastrukturausfalls oder eines niedrigstufigen Upgrades verschoben. Azure Batch stellt den Knoten wieder her.

- Ein VM-Image wurde auf Hardware bereitgestellt, von der es nicht unterstützt wird. Beispiel: Ein „HPC“-VM-Image, das auf Nicht-HPC-Hardware ausgeführt wird. Beispiel: Der Versuch, ein CentOS-HPC-Image auf einem virtuellen [Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series)-Computer auszuführen.

- Die VMs befinden sich in einem [virtuellen Azure-Netzwerk](batch-virtual-network.md), und der Datenverkehr zu den Schlüsselports wurde blockiert.

### <a name="node-agent-log-files"></a>Protokolldateien des Knoten-Agents

Der Batch-Agent-Prozess, der auf jedem Poolknoten ausgeführt wird, kann Protokolldateien bereitstellen, die hilfreich sein können, wenn Sie den Support bei einem Problem mit einem Poolknoten kontaktieren müssen. Protokolldateien für einen Knoten können über das Azure-Portal, Batch Explorer oder eine [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs) hochgeladen werden. Es ist sinnvoll, die Protokolldateien hochzuladen und zu speichern. Anschließend können Sie den Knoten oder Pool löschen, um die Kosten für die ausgeführten Knoten zu sparen.

## <a name="next-steps"></a>Nächste Schritte

Überprüfen Sie, ob Sie Ihre Anwendung so konfiguriert haben, dass eine umfassende Fehlerprüfung implementiert ist, insbesondere für asynchrone Vorgänge. Es kann entscheidend sein, Probleme frühzeitig erkennen und diagnostizieren zu können.
