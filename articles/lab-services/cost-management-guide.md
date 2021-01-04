---
title: Leitfaden zur Kostenverwaltung für Azure Lab Services
description: Informieren Sie sich über die verschiedenen Möglichkeiten, die Kosten für Lab Services anzuzeigen.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 29f6be5319c5a142ad3ea0d73deb2f95d8cb0d7a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659725"
---
# <a name="cost-management-for-azure-lab-services"></a>Kostenverwaltung für Azure Lab Services

Für Azure Lab Services kann die Kostenverwaltung in zwei Bereiche aufgeteilt werden: Kostenschätzung und Kostenanalyse. Die Kostenschätzung erfolgt beim Einrichten des Labs, um sicherzustellen, dass die anfängliche Struktur des Labs zum geplanten Budget passt. Die Kostenanalyse erfolgt in der Regel am Ende des Monats, um die für den nächsten Monat erforderlichen Aktionen zu bestimmen.

## <a name="estimate-the-lab-costs"></a>Schätzen der Labkosten

Jedes Lab-Dashboard verfügt über einen Abschnitt **Kosten und Abrechnung**, in dem eine grobe Schätzung der Kosten des Labs für den Monat festgelegt wird. Zur Kostenschätzung wird die stündliche Nutzung mit der maximalen Anzahl von Benutzern anhand der geschätzten Kosten pro Stunde zusammengerechnet. Damit die Schätzung so genau wie möglich ist, richten Sie das Lab einschließlich [Zeitplan](how-to-create-schedules.md) ein. Das Dashboard spiegelt die geschätzten Kosten wider. 

Diese Schätzung weist möglicherweise nicht alle möglichen Kosten aus. Einige Ressourcen sind nicht inbegriffen:

- Die Kosten zur Vorbereitung der Vorlage. Der Zeitaufwand zum Erstellen der Vorlage kann erheblich variieren. Die Kosten für die Ausführung der Vorlage sind mit den gesamten Labkosten pro Stunde identisch. 
- Kosten für [Kataloge mit freigegebenen Images](how-to-use-shared-image-gallery.md), da ein Katalog von mehreren Labs gemeinsam genutzt werden kann. 
- Beim Starten einer VM durch den Labersteller anfallende Stunden.

> [!div class="mx-imgBorder"]
> ![Screenshot des Dashboards mit der Kostenschätzung](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>Analysieren der Nutzung im Vormonat

Die Kostenanalyse dient zum Analysieren der Nutzung im Vormonat, um ggf. Anpassungen für das Lab zu ermitteln. Die Aufschlüsselung bisheriger Kosten finden Sie in der [Abonnementkostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md). Im Azure-Portal können Sie **Abonnements** in das Suchfeld eingeben und dann die Option **Abonnements** auswählen. 

> [!div class="mx-imgBorder"]
> ![Screenshot mit dem Suchfeld und der Schaltfläche „Abonnements“](./media/cost-management-guide/subscription-search.png)

Wählen Sie das Abonnement aus, das Sie überprüfen möchten.

> [!div class="mx-imgBorder"]
> ![Screenshot mit dem ausgewählten Abonnement](./media/cost-management-guide/subscription-select.png)

Wählen Sie im linken Bereich unter **Kostenverwaltung** die Option **Kostenanalyse** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot mit einer Abonnementkostenanalyse in einem Graph](./media/cost-management-guide/subscription-cost-analysis.png)

Dieses Dashboard ermöglicht eine eingehende Kostenanalyse, einschließlich der Möglichkeit zum Export in verschiedene Dateitypen nach einem Zeitplan. Weitere Informationen finden Sie unter [Was ist „Azure Cost Management + Abrechnung“?](../cost-management-billing/cost-management-billing-overview.md).

Sie können nach Ressourcentyp filtern. Bei Verwenden von `microsoft.labservices/labaccounts` werden nur die Kosten angezeigt, die Lab-Diensten zugeordnet sind.

## <a name="understand-the-usage"></a>Erläuterungen der Auslastung

Der folgende Screenshot zeigt ein Beispiel einer Kostenanalyse.

> [!div class="mx-imgBorder"]
> ![Screenshot mit einer Beispielkostenanalyse für ein Abonnement](./media/cost-management-guide/cost-analysis.png)

Standardmäßig gibt es sechs Spalten: **Ressource**, **Ressourcentyp**, **Speicherort**, **Ressourcengruppenname**, **Tags** und **Kosten**. Die Spalte **Ressource** enthält Informationen zum Labkonto, Labnamen und der VM. Die Zeilen mit dem Labkonto, Labnamen und Standard (die zweite und dritte Zeile) enthalten die Kosten für das Lab. Die verwendeten VMs verursachen Kosten, die in den Zeilen mit dem Labkonto, Labnamen, Standard und VM-Namen ausgewiesen sind. 

Wenn Sie in diesem Beispiel die erste Zeile zur zweiten Zeile addieren(beide beginnen mit **aaalab/dockerlab**) erhalten Sie die Gesamtkosten für das Lab „dockerlab“ im Labkonto „aaalab“.

Um die Gesamtkosten für den Imagekatalog zu erhalten, ändern Sie den Ressourcentyp in `Microsoft.Compute/Galleries`. Je nach Speicherort eines Katalogs mit freigegebenen Images wird dieser in den Kosten möglicherweise nicht angegeben.

> [!NOTE]
> Ein Katalog mit freigegebenen Images ist mit dem Labkonto verbunden. Dies bedeutet, dass mehrere Labs dasselbe Image verwenden könnten.

## <a name="separate-the-costs"></a>Trennen der Kosten

Einige Universitäten haben das Labkonto und die Ressourcengruppe als Möglichkeit zum Trennen der Kurse verwendet. Jeder Kurs verfügt über ein eigenes Labkonto und eine eigene Ressourcengruppe. 

Fügen Sie im Kostenanalysebereich einen Filter auf Basis des Ressourcengruppenamens mit dem entsprechenden Ressourcengruppenamen für den Kurs hinzu. Dann sind nur die Kosten für diesen Kurs sichtbar. Dies ermöglicht beim Anzeigen der Kosten eine klarere Abgrenzung zwischen den Kursen. Das Feature [Geplanter Export](../cost-management-billing/costs/tutorial-export-acm-data.md) der Kostenanalyse ermöglicht, die Kosten der einzelnen Kurse in separate Dateien herunterzuladen.

## <a name="manage-costs"></a>Verwalten von Kosten

Je nach Art des Kurses gibt es Möglichkeiten, Kosten zu verwalten, um Instanzen von VMs zu reduzieren, die ausgeführt werden, ohne dass ein Kursteilnehmer sie nutzt.

### <a name="automatic-shutdown-settings-for-cost-control"></a>Einstellungen für automatisches Herunterfahren zur Kostenkontrolle

Mithilfe von Features zum automatischen Herunterfahren können Sie eine unnötige VM-Nutzungszeit in den Labs verhindern. Mit den folgenden Einstellungen werden die meisten Fälle erfasst, in denen Benutzer ihre virtuellen Computer versehentlich in Betrieb lassen:

> [!div class="mx-imgBorder"]
> ![Screenshot mit den drei Einstellungen für automatisches Herunterfahren](./media/cost-management-guide/auto-shutdown-disconnect.png)

Sie können diese Einstellungen sowohl auf Labkonto- als auch auf Labebene konfigurieren. Wenn Sie sie auf Labkontoebene aktivieren, gelten sie für alle Labs im Labkonto. Für alle neuen Labkonten sind diese Einstellungen standardmäßig aktiviert. 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>Automatisches Trennen von Benutzern von virtuellen Computern, die vom Betriebssystem als im Leerlauf erkannt werden

> [!NOTE]
> Diese Einstellung ist nur für virtuelle Windows-Computer verfügbar.

Wenn die Einstellung **Benutzer trennen, wenn virtuelle Computer im Leerlauf sind** aktiviert ist, wird der Benutzer von allen Computern im Lab getrennt, wenn das Windows-Betriebssystem die Sitzung als im Leerlauf erachtet (einschließlich der Vorlagen-VMs). Für die [Definition von Leerlauf durch das Windows-Betriebssystem](/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) werden zwei Kriterien verwendet: 

* Benutzerabwesenheit: keine Tastatur- oder Mauseingabe.
* Kein Ressourcenverbrauch: Alle Prozessoren und alle Datenträger befanden sich für einen bestimmten Zeitraum (in Prozent) im Leerlauf.

Bevor Benutzer getrennt werden, wird auf der VM eine Meldung wie die folgende angezeigt: 

> [!div class="mx-imgBorder"]
> ![Screenshot mit der Warnmeldung, dass eine Sitzung über einen bestimmten Zeitraum im Leerlauf ist und getrennt wird.](./media/cost-management-guide/idle-timer-expired.png)
 
Der virtuelle Computer wird immer noch ausgeführt, wenn der Benutzer getrennt wurde. Wenn der Benutzer durch Anmelden die Verbindung mit dem virtuellen Computer wiederherstellt, sind Fenster oder Dateien, die vor der Trennung geöffnet waren, oder Inhalte, die vor der Trennung nicht gespeichert wurden, noch vorhanden. Da der virtuelle Computer ausgeführt wird, gilt er in diesem Status weiterhin als aktiv, und es fallen Kosten an. 
 
Verwenden Sie zum automatischen Herunterfahren virtueller Windows-Computer, die nicht getrennt sind, die beiden Einstellungen **Benutzerverbindung bei Leerlauf der VMs trennen** und **VMs bei Trennen der Benutzerverbindung herunterfahren**.

Angenommen, Sie konfigurieren die Einstellungen wie folgt:
 
* **Benutzerverbindung bei Leerlauf der VMs trennen**: 15 Minuten nach Feststellung des Leerlaufzustands
* **VMs bei Trennen der Benutzerverbindung herunterfahren**: 5 Minuten nach Trennen der Verbindung durch den Benutzer.
 
Die virtuellen Windows-Computer werden automatisch 20 Minuten nach der letzten Nutzung durch den Benutzer heruntergefahren. 
 
> [!div class="mx-imgBorder"]
> ![Diagramm, das die Kombination von Einstellungen veranschaulicht, die zum automatischen Herunterfahren virtueller Computer führen.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>Herunterfahren beim Trennen aktivieren
 
Die Einstellung **Herunterfahren beim Trennen aktivieren** wird für virtuelle Windows- und Linux-Computer unterstützt. Wenn sie aktiviert ist, erfolgt das automatische Herunterfahren unter folgenden Bedingungen:
 
* Für Windows: die Remotedesktopverbindung (RDP) wird getrennt.
* Für Linux: eine SSH-Verbindung wird getrennt.
 
> [!NOTE]
> Es werden nur [bestimmte Distributionen und Versionen von Linux](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions) unterstützt.
 
Sie können angeben, wie lange die VMs auf eine erneute Verbindungsherstellung des Benutzers warten sollen, bevor sie automatisch heruntergefahren werden. 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>Automatisches Herunterfahren von virtuellen Computern, die gestartet wurden, mit denen jedoch von den Benutzern keine Verbindung hergestellt wurde
 
Möglicherweise startet ein Benutzer in einem Lab einen virtuellen Computer und stellt keine Verbindung mit ihm her. Beispiel:
 
* Durch einen Zeitplan im Lab werden alle virtuellen Computer für eine Unterrichtssitzung gestartet, einige Kursteilnehmer bleiben jedoch dem Unterricht fern und stellen keine Verbindung mit ihren Computern her. 
* Ein Benutzer startet einen virtuellen Computer, vergisst aber, eine Verbindung herzustellen. 
 
Durch die Einstellung **VMs herunterfahren, wenn Benutzer keine Verbindung herstellen** werden diese Fälle abgefangen und die virtuellen Computer automatisch heruntergefahren. 
 
Informationen zum Konfigurieren und Aktivieren des automatischen Herunterfahrens von virtuellen Computern bei Verbindungstrennung finden Sie in den folgenden Artikeln:

* [Konfigurieren des automatischen Herunterfahrens von virtuellen Computern für ein Labkonto](how-to-configure-lab-accounts.md)
* [Konfigurieren des automatischen Herunterfahrens von virtuellen Computern für ein Lab](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>Vergleich zwischen geplanter Zeit und Kontingentzeit

Wenn Sie den Unterschied zwischen [geplanter Zeit](classroom-labs-concepts.md#schedules) und [Kontingentzeit](classroom-labs-concepts.md#quota) verstehen, können Sie ein Lab so konfigurieren, dass es den Bedürfnissen von Professor und Kursteilnehmern besser entspricht. 

Die geplante Zeit ist eine festgelegte Zeit, in der alle Kursteilnehmer-VMs gestartet wurden und für Verbindungen verfügbar sind. Geplante Zeit wird verwendet, wenn alle Kursteilnehmer ihren eigenen VMs haben und die Anweisungen des Professors zu einer festgelegten Zeit während des Tages befolgen, wie z. B. in Unterrichtsstunden. Der Nachteil ist, dass alle Kursteilnehmer-VMs gestartet werden und auch dann Kosten anfallen, wenn sich ein Kursteilnehmer nicht bei einer VM angemeldet hat. 

Die Kontingentzeit ist die jedem Kursteilnehmer zugeteilte Zeit, die er nach eigenem Ermessen nutzen kann und häufig für das Selbststudium verwendet wird. Die VM wird erst gestartet, wenn der Kursteilnehmer sie startet. 

Für ein Lab kann entweder Kontingentzeit, geplante Zeit oder eine Kombination aus beidem verwendet werden. Wenn ein Kurs die geplante Zeit nicht benötigt, sollten Sie nur die Kontingentzeit verwenden, um die VMs am effektivsten zu nutzen.

### <a name="scheduled-event-stop-only"></a>Geplantes Ereignis: „Nur beenden“

Dem Zeitplan können Sie den Ereignistyp „Nur beenden“ hinzufügen, mit dem alle Computer zu einem bestimmten Zeitpunkt beendet werden. Einige Labbesitzer haben für jeden Tag um Mitternacht ein „Nur beenden“-Ereignis festgelegt, um Kosten und Kontingentnutzung zu reduzieren, wenn ein Kursteilnehmer vergessen hat, die von ihm verwendete VM herunterzufahren. Dieser Ereignistyp hat den Nachteil, dass alle VMs heruntergefahren werden, auch wenn ein Kursteilnehmer eine VM gerade nutzt.

### <a name="other-costs-related-to-labs"></a>Andere Kosten im Zusammenhang mit Labs 

Es gibt Kosten, die nicht Lab Services zugeordnet sind, aber an einen Labdienst gebunden werden können. Sie können einen Katalog für freigegebene Images mit einem Lab verbinden, der jedoch nicht unter den Lab Services-Kosten ausgewiesen wird und Kosten verursacht. Wenn Sie die Gesamtkosten senken möchten, sollten Sie nicht verwendete Images aus dem Katalog entfernen, da für die Images inhärente Speicherkosten anfallen. 

Labs können über ein virtuelles Netzwerk mit anderen Azure-Ressourcen verbunden sein. Wenn ein Lab entfernt wird, müssen Sie auch das virtuelle Netzwerk und die anderen Ressourcen entfernen.

## <a name="conclusion"></a>Zusammenfassung

Wir hoffen, dass die Informationen in diesem Artikel Ihnen ein besseres Verständnis der Tools vermittelt haben, mit denen Sie die Nutzungskosten senken können.