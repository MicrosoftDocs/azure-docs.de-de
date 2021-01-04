---
title: Azure-Funktion zur sofortigen Wiederherstellung
description: Azure-Funktion zur sofortigen Wiederherstellung und häufig gestellte Fragen für VM-Sicherungsstapel, Resource Manager-Bereitstellungsmodell
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 147fadc92429157ed2f9ba3eb68297a3e1d08d24
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96014447"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Verbesserte Sicherungs- und Wiederherstellungsleistung mit der Azure Backup-Funktion zur sofortigen Wiederherstellung

> [!NOTE]
> Aufgrund des Feedbacks von Benutzern haben wir **VM-Sicherungsstapel V2** in **Sofortige Wiederherstellung** umbenannt, um Verwechslungen mit Azure Stack-Funktionen zu vermeiden.
> Für alle Azure Backup-Benutzer wurde nun ein Upgrade auf **sofortige Wiederherstellung** ausgeführt.

Das neue Modell für die sofortige Wiederherstellung bietet die folgenden Featureverbesserungen:

* Die als Teil eines Sicherungsauftrags erstellte Momentaufnahme kann genutzt werden, um für die Wiederherstellung verfügbar zu sein, ohne das Ende der Datenübertragung an den Tresor abzuwarten. Die Wartezeit, bis Momentaufnahmen vor dem Auslösen einer Wiederherstellung in den Tresor kopiert werden, wird reduziert.
* Die Sicherungs- und Wiederherstellungszeiten werden reduziert, indem Momentaufnahmen standardmäßig zwei Tage lang lokal gespeichert werden. Dieser Standardwert für die Aufbewahrung von Momentaufnahmen kann auf einen beliebigen Wert zwischen 1 und 5 Tagen konfiguriert werden.
* Es werden Datenträgergrößen von bis zu 32 TB unterstützt. Ein Ändern der Datenträgergröße wird von Azure Backup nicht empfohlen.
* Unterstützt SSD Standard-Datenträger sowie HDD Standard-Datenträger und SSD Premium-Datenträger.
* Die Möglichkeit zur Verwendung der ursprünglichen Speicherkonten (pro Datenträger) eines nicht verwalteten virtuellen Computers bei der Wiederherstellung. Diese Funktion ist auch dann vorhanden, wenn die VM Datenträger enthält, die an mehrere Speicherkonten verteilt werden. Sie beschleunigt Wiederherstellungsvorgänge für verschiedene VM-Konfigurationen.
* Wenn Sie virtuelle Computer, die nicht verwaltete Premium-Datenträger in Speicherkonten verwenden, mit der sofortigen Wiederherstellung sichern, empfiehlt es sich, *50 Prozent* freien Speicherplatz des gesamten zugeordneten Speicherplatzes zuzuweisen. Dies ist **nur** für die erste Sicherung erforderlich. Der freie Speicherplatz von 50 % ist keine Voraussetzung für Sicherungen, die nach Abschluss der ersten Sicherung ausgeführt werden.

## <a name="whats-new-in-this-feature"></a>Was ist neu in diesem Feature?

Der Sicherungsauftrag besteht derzeit aus zwei Phasen:

1. Erstellen einer VM-Momentaufnahme.
2. Übertragen der Momentaufnahme eines virtuellen Computers in den Azure Recovery Services-Tresor.

Ein Wiederherstellungspunkt gilt erst dann als erstellt, wenn die Phasen 1 und 2 abgeschlossen sind. Als Teil dieses Upgrades wird ein Wiederherstellungspunkt erstellt, sobald die Momentaufnahme abgeschlossen ist. Dieser Wiederherstellungspunkt vom Typ Momentaufnahme kann auch für eine Wiederherstellung mit demselben Wiederherstellungsablauf verwendet werden. Sie können diesen Wiederherstellungspunkt im Azure-Portal mit „Momentaufnahme“ als Wiederherstellungspunkttyp identifizieren. Nach Übertragung der Momentaufnahme in den Tresor ändert sich der Wiederherstellungspunkttyp in „Momentaufnahme und Tresor“.

![Sicherungsauftrag im VM-Sicherungsstapel, Resource Manager-Bereitstellungsmodell – Speicher und Tresor](./media/backup-azure-vms/instant-rp-flow.png)

Standardmäßig werden Momentaufnahmen zwei Tage lang aufbewahrt. Mit diesem Feature kann die Wiederherstellung über diese Momentaufnahmen mit reduzierten Wiederherstellungszeiten durchgeführt werden. Es reduziert die erforderliche Zeit zum Transformieren und Zurückkopieren von Daten aus dem Tresor.

## <a name="feature-considerations"></a>Überlegungen zum Feature

* Momentaufnahmen werden zur Verbesserung der Erstellung eines Wiederherstellungspunkts und zur Beschleunigung von Wiederherstellungsvorgängen zusammen mit den Datenträgern gespeichert. Aus diesem Grund werden Ihnen Speicherkosten angezeigt, die den Momentaufnahmen aus diesem Zeitraum entsprechen.
* Inkrementelle Momentaufnahmen werden als Seitenblobs gespeichert. Für alle Benutzer, die nicht verwaltete Datenträger verwenden, werden die Momentaufnahmen berechnet, die in ihrem lokalen Speicherkonto gespeichert sind. Da die Wiederherstellungspunktsammlungen für Sicherungen von verwalteten virtuellen Computern Blobmomentaufnahmen auf der zugrunde liegenden Speicherebene verwenden, werden für verwaltete Datenträger Kosten angezeigt, die Preisen für Blobmomentaufnahmen entsprechen, und inkrementell sind.
* Bei Premium-Speicherkonten werden die für die Momentaufnahmen, die für die Punkte erstellt werden, die zur sofortigen Wiederherstellung dienen, zu den 10 TB des zugeordneten Speicherplatzes gezählt.
* Sie haben die Möglichkeit, die Aufbewahrung von Momentaufnahmen entsprechend den Wiederherstellungsanforderungen zu konfigurieren. Je nach Anforderungen können Sie die Aufbewahrung von Momentaufnahmen im Bereich „Sicherungsrichtlinie“ auf ein Minimum von einem Tag festlegen, wie es weiter unten beschrieben ist. Dadurch können Sie Kosten für die Aufbewahrung von Momentaufnahmen sparen, wenn Wiederherstellungen nicht häufig ausgeführt werden.
* Dabei handelt es sich um ein unwiderrufliches Upgrade. Sie können das Upgrade auf die sofortige Wiederherstellung nicht rückgängig machen.

>[!NOTE]
>Mit diesem Upgrade für die sofortige Wiederherstellung wird die Aufbewahrungsdauer für Momentaufnahmen für alle Kunden (**sowohl neue als auch bestehende**) auf einen Standardwert von zwei Tagen festgelegt. Sie können die Dauer aber auch nach Bedarf auf einen beliebigen Wert zwischen einem und fünf Tagen festlegen.

## <a name="cost-impact"></a>Kostenauswirkung

Die inkrementellen Momentaufnahmen werden im Speicherkonto des virtuellen Computers gespeichert, der für die sofortige Wiederherstellung verwendet wird. Inkrementelle Momentaufnahme bedeutet, dass der durch eine Momentaufnahme belegte Speicherplatz dem Platz entspricht, der von Seiten belegt wird, die nach der Erstellung der Momentaufnahme geschrieben werden. Die Abrechnung erfolgt weiterhin pro GB für den durch die Momentaufnahme belegten Speicherplatz, und der Preis pro GB entspricht dem Preis auf der [Preisseite](https://azure.microsoft.com/pricing/details/managed-disks/). Bei virtuellen Computern, die nicht verwaltete Datenträger verwenden, können die Momentaufnahmen im Menü für die VHD-Datei der einzelnen Datenträger angezeigt werden. Bei verwalteten Datenträgern werden Momentaufnahmen in einer RPC-Ressource (Wiederherstellungspunktsammlung) in einer festgelegten Ressourcengruppe gespeichert, und die Momentaufnahmen selbst sind nicht direkt sichtbar.

>[!NOTE]
> Die Aufbewahrung einer Momentaufnahme ist für wöchentliche Richtlinien auf fünf Tage festgelegt.

## <a name="configure-snapshot-retention"></a>Konfigurieren der Aufbewahrung einer Momentaufnahme

### <a name="using-azure-portal"></a>Verwenden des Azure-Portals

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Im Azure-Portal wird ein zusätzliches Feld im Bereich **VM-Sicherungsrichtlinie** unter dem Abschnitt **Sofortige Wiederherstellung** angezeigt. Sie können die Aufbewahrungsdauer für Momentaufnahmen im Bereich **VM-Sicherungsrichtlinie** für alle virtuellen Computer ändern, die der jeweiligen Sicherungsrichtlinie zugeordnet sind.

![Funktion zur sofortigen Wiederherstellung](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>PowerShell

>[!NOTE]
> Ab Azure PowerShell, Version 1.6.0, können Sie die Aufbewahrungsdauer von Momentaufnahmen für sofortige Wiederherstellung in Richtlinien mithilfe von PowerShell aktualisieren.

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

Die Standardaufbewahrungsdauer von Momentaufnahmen für jede Richtlinie ist auf zwei Tage festgelegt. Sie können den Wert auf mindestens einen Tag und maximal fünf Tage ändern. Die Aufbewahrungsdauer von Momentaufnahmen für wöchentliche Richtlinien ist auf fünf Tage festgelegt.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Welche Kosten sind mit der sofortigen Wiederherstellung verbunden?

Momentaufnahmen werden zur Beschleunigung der Erstellung eines Wiederherstellungspunkts sowie von Wiederherstellungsvorgängen zusammen mit den Datenträgern gespeichert. Daher werden Ihnen Speicherkosten angezeigt, die der Vermerkdauer für Momentaufnahmen entsprechen, die im Rahmen der VM-Sicherungsrichtlinie ausgewählt wurde.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Halten die Momentaufnahmen, die für Punkte zur sofortigen Wiederherstellung erstellt werden, in Storage Premium-Konten die 10 TB-Grenze für Momentaufnahmen ein?

Ja, Momentaufnahmen, die für Punkte zur sofortigen Wiederherstellung erstellt werden, halten in Premium-Speicherkonten die 10-TB-Grenze für Momentaufnahmen ein.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Wie funktioniert die Aufbewahrung von Momentaufnahmen im Zeitraum von fünf Tagen?

Jeden Tag wird eine neue Momentaufnahme erstellt, sodass dann fünf einzelne Momentaufnahmen vorhanden sind. Die Größe der Momentaufnahme hängt von der Datenänderungsrate ab, die in den meisten Fällen etwa 2–7 % beträgt.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Handelt es sich bei einer Momentaufnahme für die sofortige Wiederherstellung um eine inkrementelle Momentaufnahme oder eine vollständige Momentaufnahme?

Momentaufnahmen, die im Rahmen der Funktion für die sofortige Wiederherstellung erstellt werden, sind inkrementelle Momentaufnahmen.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Wie kann ich den ungefähren Kostenanstieg durch die Funktion zur sofortigen Wiederherstellung berechnen?

Dies hängt von der Datenänderungsrate des virtuellen Computers ab. Bei einem stabilen Zustand können Sie von folgender Rechnung ausgehen: Kostenanstieg = Aufbewahrungszeitraum der Momentaufnahme tägliche Änderungsrate pro virtuellem Computer Speicherkosten pro GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Wenn der Wiederherstellungstyp für einen Wiederherstellungspunkt „Momentaufnahme und Tresor“ ist, und ich einen Wiederherstellungsvorgang durchführe, welcher Wiederherstellungstyp wird dann verwendet?

Wenn der Wiederherstellungstyp „Momentaufnahme und Tresor“ ist, erfolgt die Wiederherstellung automatisch aus der lokalen Momentaufnahme. Dieser Vorgang ist verglichen mit einer Wiederherstellung aus dem Tresor viel schneller.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Was passiert, wenn ich für den Aufbewahrungszeitraum des Wiederherstellungspunkts (Tarif 2) einen geringeren Wert als für den Aufbewahrungszeitraum der Momentaufnahme (Tarif 1) auswähle?

Das neue Modell lässt das Löschen des Wiederherstellungspunkts (Tarif 2) nur zu, wenn die Momentaufnahme (Tarif 1) gelöscht wird. Es empfiehlt sich, den Aufbewahrungszeitraum für den Wiederherstellungspunkt (Tarif2) auf einen höheren Wert als den Aufbewahrungszeitraum der Momentaufnahme festzulegen.

### <a name="why-does-my-snapshot-still-exist-even-after-the-set-retention-period-in-backup-policy"></a>Warum ist meine Momentaufnahme auch nach Ablauf des in der Sicherungsrichtlinie festgelegten Aufbewahrungszeitraums noch vorhanden?

Wenn der Wiederherstellungspunkt eine Momentaufnahme aufweist und diese der letzte verfügbare Wiederherstellungspunkt ist, wird sie bis zur nächsten erfolgreichen Sicherung aufbewahrt. Dies entspricht der festgelegten Richtlinie für die Garbage Collection (GC). Diese gibt an, dass immer mindestens ein letzter Wiederherstellungspunkt vorhanden sein muss, falls alle nachfolgenden Sicherungen aufgrund eines Problems auf der VM zu Fehlern führen. In normalen Szenarien werden Wiederherstellungspunkte spätestens 24 Stunden nach ihrem Ablauf bereinigt.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Ich benötige keine sofortige Wiederherstellung. Kann ich sie deaktivieren?

Das Feature für die sofortige Wiederherstellung ist für alle Benutzer aktiviert und kann nicht deaktiviert werden. Die Aufbewahrungsdauer der Momentaufnahme kann auf bis zu einen Tag verkürzt werden.

### <a name="is-it-safe-to-restart-the-vm-during-the-transfer-process-which-can-take-many-hours-will-restarting-the-vm-interrupt-or-slow-down-the-transfer"></a>Ist es sicher, die VM während des Übertragungsprozesses (der viele Stunden dauern kann) neu zu starten? Wird der Neustart der VM die Übertragung unterbrechen oder verlangsamen?

Ja, es ist sicher, und es gibt absolut keine Auswirkungen auf die Datenübertragungsgeschwindigkeit.

