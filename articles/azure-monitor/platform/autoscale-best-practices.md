---
title: Bewährte Methoden für die automatische Skalierung
description: Autoskalierungsmuster in Azure für Web-Apps, VM-Skalierungsgruppen und Clouddienste
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 7fdb3588833dd9bcf989e020cd1dd861c6e28f37
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95745315"
---
# <a name="best-practices-for-autoscale"></a>Bewährte Methoden für die automatische Skalierung
Die automatische Skalierung von Azure Monitor gilt nur für [VM.Skalierungsgruppen](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Clouddienste](https://azure.microsoft.com/services/cloud-services/), [App Service – Web-Apps](https://azure.microsoft.com/services/app-service/web/) und [API Management-Dienste](../../api-management/api-management-key-concepts.md).

## <a name="autoscale-concepts"></a>Konzepte der automatischen Skalierung

* Eine Ressource kann nur *eine* Einstellung für die automatische Skalierung haben.
* Eine Einstellung für die automatische Skalierung kann ein oder mehrere Profile beinhalten, und jedes Profil kann eine oder mehrere Regeln für die automatische Skalierung beinhalten.
* Eine Einstellung für die automatische Skalierung skaliert Instanzen horizontal *hoch*, indem die Anzahl der Instanzen erhöht, und *herunter*, indem die Anzahl der Instanzen verringert wird.
  Eine Einstellung für die automatische Skalierung hat einen Höchst-, Mindest- und Standardwert von Instanzen.
* Eine automatische Skalierungsaufgabe liest die zugeordnete Skalierungsmetrik und überprüft dabei, ob die konfigurierten Schwellenwerte zum horizontalen Hoch- oder Herunterskalieren überschritten wurden. Eine Liste der Metriken, nach der die automatische Skalierung vorgenommen werden kann, finden Sie unter [Übliche Metriken für die automatische Skalierung in Azure Monitor](autoscale-common-metrics.md).
* Alle Schwellenwerte werden auf Instanzebene berechnet. Beispielsweise besagt „Skaliere um eine Instanz horizontal hoch, wenn die durchschnittliche CPU-Auslastung &gt; 2 % und die Instanzenanzahl 80 ist“, dass aufskaliert werden soll, wenn die durchschnittliche CPU-Auslastung über alle Instanzen hinweg größer als 80 % ist.
* Alle Fehler bei der automatischen Skalierung werden im Aktivitätsprotokoll protokolliert. Anschließend können Sie eine [Warnung für das Aktivitätsprotokoll](./activity-log-alerts.md) konfigurieren, damit Sie per E-Mail, SMS oder Webhook benachrichtigt werden, wenn bei der automatischen Skalierung ein Fehler auftritt.
* Auf ähnliche Weise werden alle erfolgreichen Skalierungsaktionen im Aktivitätsprotokoll erfasst. Anschließend können Sie eine Warnung für das Aktivitätsprotokoll konfigurieren, damit Sie per E-Mail, SMS oder Webhook benachrichtigt werden, wenn die automatischen Skalierung erfolgreich abgeschlossen wurde. Sie können auf der Registerkarte „Benachrichtigungen“ in den Einstellungen für die automatische Skalierung auch E-Mail- oder Webhook-Benachrichtigungen konfigurieren, um bei erfolgreichen Skalierungsaktionen informiert zu werden.

## <a name="autoscale-best-practices"></a>Empfohlene Methoden für die automatische Skalierung

Verwenden Sie die folgenden Best Practices für die automatische Skalierung.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Stellen Sie sicher, dass sich die Höchst- und Mindestwerte unterscheiden und eine angemessene Spanne zwischen ihnen liegt.

Falls Ihre Einstellung ein Minimum von „2“ und ein Maximum von „2“ hat und dabei die Anzahl der aktuellen Instanzen „2“ ist, kann keine automatische Skalierung durchgeführt werden. Behalten Sie eine passende Spanne zwischen der maximalen und der minimalen Instanzenanzahl bei. Beide Werte gelten dabei als inklusive. Die automatische Skalierung skaliert immer zwischen diesen Grenzwerten.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Die manuelle Skalierung wird durch die Mindest- und Höchstwerte der automatischen Skalierung außer Kraft gesetzt.

Wenn Sie die Anzahl der Instanzen manuell auf einen Wert oberhalb oder unterhalb dieser Grenzen aktualisieren, skaliert die Engine für die automatische Skalierung automatisch auf den Mindestwert (sofern der Wert niedriger ist) oder den Höchstwert (sofern er höher ist). Angenommen, Sie legen den Bereich zwischen 3 und 6 fest. Wenn Sie eine ausgeführte Instanz haben, skaliert die Engine für die automatische Skalierung bei der nächsten Ausführung auf drei Instanzen. Wenn Sie manuell die Skalierung auf acht Instanzen festlegen, wird ebenso bei der nächsten Ausführung der automatischen Skalierung wieder auf sechs Instanzen herunterskaliert.  Die manuelle Skalierung ist temporär, sofern Sie nicht auch die Regeln für die automatische Skalierung zurücksetzen.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Verwenden Sie immer eine Regelkombination für das horizontale Hoch- und Herunterskalieren, die eine Erhöhung und Verringerung durchführt.
Wenn Sie nur einen Teil der Kombination verwenden, wird die Autoskalierung nur in eine Richtung (aufskalieren oder abskalieren) wirken, bis sie die im Profil definierte maximale oder minimale Anzahl von Instanzen erreicht. Dies ist nicht optimal, da Sie Ihre Ressource in Zeiten hoher Auslastung vorzugsweise aufskalieren möchten, um die Verfügbarkeit sicherzustellen. Ebenso möchten Sie in Zeiten geringer Auslastung, dass Ihre Ressource abskaliert wird, damit Sie Kosteneinsparungen realisieren können.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Wählen Sie die passende Statistik für Ihre Diagnosemetrik aus
Für die Diagnosemetriken können Sie zwischen *Durchschnitt*, *Minimum*, *Maximum* und *Gesamt* als Metrik, nach der skaliert werden soll, auswählen. Die am häufigsten verwendete Statistik ist *Durchschnitt*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Wählen Sie die Schwellenwerte für alle Metriktypen sorgfältig
Wir empfehlen die sorgfältige Auswahl verschiedener Schwellenwerte für das horizontale Hoch- und Herunterskalieren, je nach Praxissituation.

Wir empfehlen Ihnen, die Einstellungen für die Autoskalierung *nicht* wie in den unten gezeigten Beispielen vorzunehmen. In diesen sind die Schwellenwerte für die Bedingungen des horizontalen Hoch- und Herunterskalierens gleich oder ähnlich:

* Erhöhe die Anzahl der Instanzen um 1, wenn Threadanzahl >= 600
* Verringere die Anzahl der Instanzen um 1, wenn Threadanzahl <= 600

Sehen wir uns nun ein Beispiel an, das zu möglicherweise verwirrendem Verhalten führen kann. Gehen Sie dabei von der folgenden Abfolge aus.

1. Nehmen Sie an, es gibt anfangs zwei Instanzen, und die durchschnittliche Anzahl der Threads pro Instanz wächst auf 625.
2. Die automatische Skalierung skaliert horizontal hoch und fügt eine dritte Instanz hinzu.
3. Nehmen Sie jetzt an, dass sich die durchschnittliche Threadanzahl über alle Instanzen hinweg auf 575 verringert.
4. Bevor herunterskaliert wird, versucht die automatische Skalierung, den Endzustand nach ausgeführtem Herunterskalieren abzuschätzen. Falls beispielsweise 575 x 3 (aktuelle Instanzenanzahl) = 1.725 / 2 (Anzahl der Instanzen, wenn zentral herunterskaliert wird) = 862,5 Threads. Das bedeutet, dass die Autoskalierung nach dem horizontalen Herunterskalieren sofort wieder aufskalieren muss, falls die durchschnittliche Threadanzahl gleich bleibt oder sich nur leicht verringert. Falls jedoch wieder horizontal hochskaliert wird, würde sich der ganze Vorgang wiederholen, was zu einer Endlosschleife führen würde.
5. Um diese Fluktuation zu vermeiden, wird gar nicht erst herunterskaliert. Stattdessen wird diese Bedingung übersprungen und beim nächsten Ausführen dieses Dienstauftrags neu bewertet. Die Fluktuation kann viele Benutzer verwirren, da die Autoskalierung scheinbar bei einer durchschnittlichen Threadanzahl von 575 nicht funktioniert.

Die Schätzung während eines horizontalen Herunterskalierens soll „Pendelsituationen“ vermeiden, in denen horizontales Herunter- und Hochskalieren ständig wechseln. Behalten Sie dieses Verhalten im Hinterkopf , wenn Sie für das horizontale Hoch- und Herunterskalieren die gleichen Schwellenwerte auswählen.

Wir empfehlen eine angemessene Spanne zwischen den Schwellenwerten für das horizontale Hoch- und Herunterskalieren. Ziehen Sie stattdessen beispielsweise diese bessere Regelkombination in Betracht:

* Erhöhe die Anzahl der Instanzen um 1, wenn prozentuale CPU-Auslastung >= 80
* Verringere die Anzahl der Instanzen um 1, wenn prozentuale CPU-Auslastung <= 60

In diesem Fall  

1. Angenommen, es gibt zu Anfang 2 Instanzen.
2. Falls die durchschnittliche prozentuale CPU-Auslastung über alle Instanzen 80 erreicht, wird automatisch horizontal hochskaliert und eine dritte Instanz hinzugefügt.
3. Nehmen Sie jetzt an, dass im Laufe der Zeit die prozentuale CPU-Auslastung auf 60 fällt.
4. Die Regel für das automatische horizontale Herunterskalieren schätzt den Endzustand, falls horizontal herunterskaliert werden sollte. Falls beispielsweise 60 x 3 (aktuelle Instanzenanzahl) = 180 / 2 (Anzahl der Instanzen wenn herunterskaliert wird) = 90 Die automatische Skalierung skaliert also nicht horizontal herunter, da sie sofort wieder horizontal hochskalieren müsste. Stattdessen wird das horizontale Herunterskalieren übersprungen. Nehmen Sie jetzt an, dass bei der nächsten Überprüfung die CPU-Auslastung auf 50 fällt.
5. Bei der nächsten Prüfung durch die automatische Skalierung fällt die CPU-Auslastung weiter auf 50. Jetzt schätzt sie wieder – 50 x 3 Instanzen = 150 / 2 Instanzen = 75, was unter dem Schwellenwert von 80 für das horizontale Hochskalieren liegt, daher wird erfolgreich horizontal auf 2 Instanzen herunterskaliert.

> [!NOTE]
> Wenn die Engine für die automatische Skalierung erkennt, dass als Folge der Skalierung auf die Zielanzahl von Instanzen eine Fluktuation auftreten könnte, wird zudem versucht, eine Skalierung auf eine andere Anzahl von Instanzen durchzuführen, die zwischen der aktuellen Anzahl und der Zielanzahl liegt. Wenn innerhalb dieses Bereichs keine Fluktuation auftritt, setzt die automatische Skalierung den Vorgang mit dem neuen Ziel fort.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Überlegungen zu Skalierungsschwellenwerten für spezielle Metriken
 Für spezielle Metriken, wie die Metrik für die Länge der Speicher- oder Service Bus-Warteschlange, ist der Schwellenwert die durchschnittliche Anzahl der Nachrichten, die pro aktueller Anzahl von Instanzen verfügbar ist. Wählen Sie den Schwellenwert für diese Metrik sorgfältig aus.

Veranschaulichen wir diesen Punkt mit einem Beispiel, um sicherzustellen, dass Sie dieses Verhalten besser verstehen.

* Erhöhe die Anzahl der Instanzen um 1, wenn Anzahl der Nachrichten in Speicherwarteschlange >= 50
* Verringere die Anzahl der Instanzen um 1, wenn Anzahl der Nachrichten in Speicherwarteschlange <= 10

Gehen Sie dabei von der folgenden Abfolge aus:

1. Es gibt zwei Instanzen der Speicherwarteschlange.
2. Es treffen weiterhin Nachrichten ein, und die Anzahl beim Ansehen der Speicherwarteschlange liegt bei 50. Sie könnten davon ausgehen, dass die automatische Skalierung eine horizontale Hochskalierungsaktion durchführt. Beachten Sie jedoch, dass es sich immer noch um 50 / 2 = 25 Nachrichten pro Instanz handelt. Daher wird keine horizontale Hochskalierung ausgeführt. Damit die erste horizontale Hochskalierung ausgeführt wird, sollte die Gesamtanzahl der Nachrichten in der Speicherwarteschlange 100 sein.
3. Nehmen Sie jetzt an, dass die Gesamtanzahl der Nachrichten 100 erreicht.
4. Eine dritte Instanz der Speicherwarteschlange wird aufgrund einer horizontalen Skalierungsaktion hinzugefügt.  Die nächste horizontale Hochskalierungsaktion wird erst durchgeführt, wenn die Gesamtzahl der Nachrichten in der Warteschlange 150 erreicht, da 150 / 3 = 50.
5. Jetzt nimmt die Anzahl der Nachrichten in der Warteschlange ab. Bei drei Instanzen wird die erste horizontale Herunterskalierungsaktion durchgeführt, wenn die Gesamtzahl der Nachrichten in der Warteschlange 30 beträgt, da 30/3 = 10 Nachrichten pro Instanz ergeben – den Schwellenwert zum horizontalen Herunterskalieren.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Überlegungen zur Skalierung, wenn mehrere Profile in einer Einstellung für die automatische Skalierung konfiguriert sind
In einer Einstellung für die automatische Skalierung können Sie ein Standardprofil wählen, das immer angewendet wird, unabhängig von Zeitplänen oder der Zeit. Alternativ können Sie aber auch ein periodisches Profil oder ein Profil für eine festgelegte Periode mit Datums- und Zeitbereich wählen.

Bei einer Verarbeitung durch automatische Skalierungsdienste werden sie immer in der folgenden Reihenfolge überprüft:

1. Profil für ein festgelegtes Datum
2. Periodisches Profil
3. Standardprofil („immer“)

Sobald die Bedingung eines Profils erfüllt wird, überprüft die automatische Skalierung nicht mehr zusätzlich die nächste Profilbedingung auf der nächstuntersten Ebene. Die automatische Skalierung verarbeitet immer nur ein Profil gleichzeitig. Das bedeutet, dass Sie, wenn Sie eine Verarbeitungsbedingung von einer niedrigeren Ebene beachtet haben wollen, diese Regeln auch im aktuellen Profil festlegen müssen.

Sehen wir uns dies mithilfe eines Beispiels an:

Die Abbildung unten zeigt eine Einstellung für die automatische Skalierung mit einem Standardprofil mit einem Instanzenminimum von 2 und einem Instanzenmaximum von 10. In diesem Beispiel wurden die Regeln so konfiguriert, dass aufskaliert wird, wenn die Anzahl der Meldungen in der Warteschlange größer als 10 ist, und abskaliert wird, wenn die Anzahl der Meldungen in der Warteschlange kleiner als drei ist. Die Ressource kann jetzt also zwischen zwei und zehn Instanzen hoch- bzw. herunterskalieren.

Zusätzlich wurde ein periodisches Profil für Montag eingerichtet. Für dieses wurde ein Instanzenminimum von 3 und ein Instanzenmaximum von 10 festgelegt. Das heißt, dass am Montag bei der erstmaligen Überprüfung dieser Bedingung durch die automatische Skalierung, diese auf das neue Minimum von drei horizontal hochskaliert, falls die Instanzenanzahl bereits zwei ist. Solange die automatische Skalierung bei der Überprüfung feststellt, dass diese Profilbedingung erfüllt ist (Montag), wird sie nur die CPU-basierten Regeln für das horizontale Skalieren (hoch/herunter) ausführen, die für dieses Profil konfiguriert wurden. Zu diesem Zeitpunkt wird die Länge der Warteschlange nicht überprüft. Falls Sie jedoch wollen, dass die Bedingung der Länge der Warteschlange ebenfalls überprüft wird, sollten Sie diese Regeln aus dem Standardprofil auch in Ihr Montagsprofil übernehmen.

Wenn die automatische Skalierung zurück zum Standardprofil wechselt, wird ebenfalls zuerst überprüft, ob die Höchst- und Mindestbedingungen erfüllt werden. Falls die Anzahl der Instanzen zu diesem Zeitpunkt 12 ist, wird auf 10, den für das Standardprofil zulässigen Höchstwert, herunterskaliert.

![Einstellungen für die automatische Skalierung](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Skalierungsüberlegungen, wenn in einem Profil mehrere Regeln konfiguriert sind

Es gibt Fälle, in denen Sie möglicherweise mehrere Regeln innerhalb eines Profils festlegen müssen. Die folgenden Regeln für die Autoskalierung werden von der Autoskalierungs-Engine verwendet, wenn mehrere Regeln festgelegt sind.

Beim *horizontalen Hochskalieren* wird die Autoskalierung durchgeführt, sobald eine Regel erfüllt wird.
Beim *horizontalen Herunterskalieren* wird die automatische Skalierung nur ausgeführt, wenn alle Regeln erfüllt werden.

Nehmen Sie an, Sie hätten die folgenden vier Regeln für die automatische Skalierung:

* Falls CPU-Auslastung < 30 %, skaliere horizontal um 1 herunter
* Falls Arbeitsspeicherauslastung < 50 %, skaliere horizontal um 1 herunter
* Falls CPU-Auslastung > 75 %, skaliere horizontal um 1 hoch
* Falls Arbeitsspeicherauslastung > 75 %, skaliere horizontal um 1 hoch

Daraufhin erfolgt Folgendes:

* Falls die CPU-Auslastung 76 % und die Arbeitsspeicherauslastung 50 % beträgt, erfolgt eine Aufskalierung.
* Falls die CPU-Auslastung 50 % und die Arbeitsspeicherauslastung 76 % beträgt, erfolgt eine Aufskalierung.

Andererseits wird, wenn die CPU-Auslastung 25 % und die Arbeitsspeicherauslastung 51 % beträgt, **nicht** automatisch horizontal herunterskaliert. Um horizontal herunterzuskalieren, muss die CPU-Auslastung 29 % und die Arbeitsspeicherauslastung 49 % betragen.

### <a name="always-select-a-safe-default-instance-count"></a>Wählen Sie als Standard immer eine sichere Anzahl an Instanzen
Die Standardinstanzenanzahl ist wichtig, da die Autoskalierung Ihren Dienst auf diese Instanzenanzahl skaliert, wenn keine Metriken zur Verfügung stehen. Wählen Sie daher eine Standardanzahl an Instanzen, die für Ihre Workloads sicher ist.

### <a name="configure-autoscale-notifications"></a>Konfigurieren der Benachrichtigungen für das automatische Skalieren
Die automatische Skalierung schreibt in das Aktivitätsprotokoll, wenn eine der folgenden Bedingungen eintritt:

* Die Autoskalierung gibt einen Skalierungsvorgang aus.
* Der Autoskalierungsdienst schließt eine Skalierungsaktion erfolgreich ab.
* Bei einer Skalierungsaktion der automatischen Skalierung tritt ein Fehler auf.
* Für den Autoskalierungsdienst stehen keine Metriken zur Verfügung, auf deren Grundlage eine Skalierungsentscheidung getroffen werden kann.
* Metriken stehen wieder zur Verfügung (Wiederherstellung), um eine Skalierungsentscheidung zu treffen.
* Die automatische Skalierung erkennt eine Fluktuation und bricht den Skalierungsversuch ab. In dieser Situation wird der Protokolltyp `Flapping` angezeigt. Wenn das der Fall ist, sollten Sie prüfen, ob die Schwellenwerte zu dicht beieinander liegen.
* Die automatische Skalierung erkennt eine Fluktuation, kann aber immer noch erfolgreich skalieren. In dieser Situation wird der Protokolltyp `FlappingOccurred` angezeigt. Wenn das der Fall ist, hat die Engine für die automatische Skalierung versucht, eine Skalierung durchzuführen (z. B. von vier Instanzen auf zwei), jedoch festgestellt, dass dies zu Fluktuation führt. Stattdessen hat die Engine für die automatische Skalierung auf eine andere Anzahl von Instanzen skaliert (z. B. auf drei Instanzen anstelle von zwei), wodurch keine Fluktuation mehr verursacht wird, sodass die Skalierung auf diese Anzahl von Instanzen erfolgte.

Sie können auch eine Aktivitätsprotokollwarnung zur Überwachung der Integrität der Engine für die automatische Skalierung verwenden. Es folgen Beispiele zum [Erstellen einer Aktivitätsprotokollwarnung zum Überwachen aller Vorgänge der Engine für die automatische Skalierung in Ihrem Abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) oder [Erstellen einer Aktivitätsprotokollwarnung zum Überwachen aller fehlerhaften Vorgänge zum automatischen Abskalieren und zum automatischen Aufskalieren in Ihrem Abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Neben der Verwendung von Warnungen zu Aktivitätsprotokollen können Sie auf der Registerkarte „Benachrichtigungen“ in den Einstellungen für die automatische Skalierung auch E-Mail- oder Webhook-Benachrichtigungen konfigurieren, um bei erfolgreichen Skalierungsaktionen informiert zu werden.

## <a name="next-steps"></a>Nächste Schritte
- [Erstellen Sie eine Aktivitätsprotokollwarnung, um alle Vorgänge der Engine für die automatische Skalierung für Ihr Abonnement zu überwachen.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Erstellen Sie eine Aktivitätsprotokollwarnung, um alle Autoskalierungsvorgänge zum Abskalieren und Aufskalieren in Ihrem Abonnement, bei denen Fehler aufgetreten sind, zu überwachen.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

