---
title: Verwenden von Azure Backup als Ersatz für Ihre Bandinfrastruktur
description: Hier erfahren Sie, wie Azure Backup eine Semantik ähnlich wie bei Bändern bereitstellt, damit Sie Ihre Daten in Azure sichern und wiederherstellen können.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/30/2017
ms.author: dacurwin
ms.openlocfilehash: d768f0fae9487a555f6ace12303f8a4bd7cb8bd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146018"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Verschieben langfristiger Speicher von Bändern in die Azure-Cloud
Kunden von Azure Backup und System Center Data Protection Manager haben folgende Möglichkeiten:

* Sichern von Daten gemäß Zeitplänen, die den Anforderungen ihrer Organisation am besten entsprechen
* Beibehalten der Sicherungsdaten für längere Zeiträume
* Integrieren von Azure in ihre Anforderungen an die langfristige Aufbewahrung (anstelle von Bändern)

In diesem Artikel wird erläutert, wie Kunden Sicherungs- und Aufbewahrungsrichtlinien aktivieren können. Kunden, die für ihre Anforderungen an eine langfristige Aufbewahrung Bänder verwenden, steht jetzt dank dieses Features eine leistungsstarke und geeignete Alternative zur Verfügung. Das Feature ist in der neuesten Version von Azure Backup aktiviert (die [hier](https://aka.ms/azurebackup_agent)erhältlich ist). System Center DPM-Kunden müssen mindestens auf DPM 2012 R2 UR5 aktualisieren, bevor sie DPM mit dem Azure Backup-Dienst verwenden können.

## <a name="what-is-the-backup-schedule"></a>Was ist ein Sicherungszeitplan?
Der Sicherungszeitplan gibt die Häufigkeit des Sicherungsvorgangs an. Die Einstellungen auf dem folgenden Bildschirm geben beispielsweise an, dass Sicherungen täglich um 18:00 Uhr und um Mitternacht ausgeführt werden.

![Täglicher Zeitplan](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Kunden können auch eine wöchentliche Sicherung planen. Die Einstellungen auf dem folgenden Bildschirm geben beispielsweise an, dass Sicherungen jeden zweiten Sonntag und Mittwoch um 9:30 Uhr und um 1 Uhr morgens erstellt werden.

![Wöchentlicher Zeitplan](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Worum handelt es sich bei der Aufbewahrungsrichtlinie?
Die Aufbewahrungsrichtlinie gibt an, wie lange die Sicherung gespeichert werden muss. Anstatt nur eine "flache Richtlinie" für alle Sicherungspunkte anzugeben, können Kunden je nach Sicherungszeitpunkt verschiedene Aufbewahrungsrichtlinien festlegen. Zum Beispiel kann der täglich erstellte Sicherungspunkt, der als Wiederherstellungspunkt für den alltäglichen Betrieb dient, 90 Tage lang aufbewahrt werden. Der Sicherungspunkt, der zu Überwachungszwecken am Ende jedes Quartals erstellt wird, kann wesentlich länger aufbewahrt werden.

![Aufbewahrungsrichtlinie](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Die Gesamtanzahl der in dieser Richtlinie angegebenen "Aufbewahrungspunkte" ist 90 (tägliche Punkte) + 40 (einer pro Quartal für 10 Jahre) = 130.

## <a name="example--putting-both-together"></a>Beispiel – Kombination aus beidem
![Beispielbildschirm](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Tägliche Aufbewahrungsrichtlinie**: Täglich erstellte Sicherungen werden sieben Tage lang gespeichert.
2. **Wöchentliche Aufbewahrungsrichtlinie**: Sicherungen, die jeden Tag um Mitternacht und jeden Samstag um 18 Uhr erstellt werden, werden vier Wochen lang aufbewahrt.
3. **Monatliche Aufbewahrungsrichtlinie**: Sicherungen, die um Mitternacht und am letzten Samstag im Monat um 18:00 Uhr erstellt werden, werden zwölf Monate lang aufbewahrt.
4. **Jährliche Aufbewahrungsrichtlinie**: Sicherungen, die jedes Jahr am letzten Samstag im März um Mitternacht erstellt werden, werden zehn Jahre lang aufbewahrt.

Die Gesamtanzahl der „Aufbewahrungspunkte“ (Punkte, von denen ein Kunde Daten wiederherstellen kann) in der obigen Abbildung wird wie folgt berechnet:

* Zwei Punkte pro Tag, sieben Tage lang = 14 Wiederherstellungspunkte
* Zwei Punkte pro Woche, vier Wochen lang = 8 Wiederherstellungspunkte
* Zwei Punkte pro Monat, 12 Monate lang = 24 Wiederherstellungspunkte
* Ein Punkt pro Jahr, 10 Jahre lang = 10 Wiederherstellungspunkte

Die Gesamtzahl der Wiederherstellungspunkte beträgt 56.

> [!NOTE]
> Pro geschützter Instanz können mit Azure Backup bis zu 9.999 Wiederherstellungspunkte erstellt werden. Geschützte Instanzen sind Computer, Server (physisch oder virtuell) oder Workloads, die in Azure gesichert werden.
>

## <a name="advanced-configuration"></a>Erweiterte Konfiguration
Durch Klicken auf **Ändern** im Bildschirm oben können Kunden noch flexiblere Aufbewahrungszeitpläne angeben.

![Ändern](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Backup finden Sie hier:

* [Einführung in Azure Backup](backup-introduction-to-azure-backup.md)
* [Azure Backup testen](backup-try-azure-backup-in-10-mins.md)
