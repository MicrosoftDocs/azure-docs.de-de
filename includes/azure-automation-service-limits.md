---
title: include file
description: include file
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 02/08/2021
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 3ee44509997a16fc7f06fd5a24e473d7011bde40
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99974597"
---
#### <a name="process-automation"></a>Prozessautomatisierung

| Resource | Begrenzung |Notizen|
| --- | --- |---|
| Maximale Anzahl von neuen Aufträgen, die alle 30 Sekunden pro Azure Automation-Konto (nicht geplante Aufträge) übermittelt werden können |100 |Wenn dieser Grenzwert erreicht wird, treten bei nachfolgenden Anforderungen zum Erstellen eines Auftrags Fehler auf. Der Client empfängt eine Fehlerantwort.|
| Maximale Anzahl gleichzeitig ausgeführter Aufträge pro Automation-Konto (nicht geplante Aufträge) |200 |Wenn dieser Grenzwert erreicht wird, treten bei nachfolgenden Anforderungen zum Erstellen eines Auftrags Fehler auf. Der Client empfängt eine Fehlerantwort.|
| Maximale Speichergröße der Auftragsmetadaten für einen fortlaufenden Zeitraum von 30 Tagen | 10 GB (etwa 4 Millionen Aufträge)|Wenn dieser Grenzwert erreicht wird, treten bei nachfolgenden Anforderungen zum Erstellen eines Auftrags Fehler auf. |
| Maximale Größe von Auftragsdatenströmen|1 MiB|Ein einzelner Datenstrom darf nicht größer als 1 MiB sein.|
| Maximale Anzahl von Modulen, die alle 30 Sekunden pro Automation-Konto importiert werden können |5 ||
| Maximale Größe eines Moduls |100 MB ||
| Maximale Größe einer Knotenkonfigurationsdatei | 1 MB | Gilt für die Zustandskonfiguration. |
| Auftragsausführungszeit – Free-Tarif |500 Minuten pro Abonnement pro Kalendermonat ||
| Maximal zulässiger Speicherplatz pro Sandbox<sup>1</sup> |1 GB |Betrifft nur Azure-Sandboxen.|
| Maximaler Arbeitsspeicher pro Sandbox<sup>1</sup> |400 MB |Betrifft nur Azure-Sandboxen.|
| Maximal zulässige Anzahl von Netzwerksockets pro Sandbox<sup>1</sup> |1\.000 |Betrifft nur Azure-Sandboxen.|
| Maximal zulässige Laufzeit pro Runbook <sup>1</sup> |3 Stunden |Betrifft nur Azure-Sandboxen.|
| Maximale Anzahl von Automation-Konten in einem Abonnement |Keine Begrenzung ||
| Maximale Anzahl von Hybrid Worker-Gruppen pro Automation-Konto|4\.000||
|Maximale Anzahl gleichzeitiger Aufträge, die auf einem einzelnen Hybrid Runbook Worker ausgeführt werden können|50 ||
| Maximale Größe der Runbookauftragsparameter   | 512 Kilobytes||
| Maximale Anzahl von Runbookparametern   | 50|Sie können einen JSON- oder XML-Zeichenfolge an einen Parameter übergeben und ihn mit dem Runbook analysieren, wenn Sie das Limit von 50 Parametern erreichen.|
| Maximale Größe für Webhooknutzlast |  512 Kilobytes|
| Maximale Anzahl von Tagen, die Auftragsdaten aufbewahrt werden|30 Tage|
| Maximale Größe des PowerShell-Workflowstatus |5 MB| Gilt für PowerShell-Workflow-Runbooks, wenn Prüfpunkte für Workflows eingerichtet werden.|

<sup>1</sup> Eine Sandbox ist eine freigegebene Umgebung, die von mehreren Aufträgen verwendet werden kann. Aufträge, die die gleiche Sandbox verwenden, sind an die Ressourceneinschränkungen der Sandbox gebunden.

#### <a name="change-tracking-and-inventory"></a>Änderungsnachverfolgung und Bestand

Die folgende Tabelle zeigt die Grenzwerte der nachverfolgten Elemente pro Computer für die Änderungsnachverfolgung.

| **Ressource** | **Begrenzung**| **Hinweise** |
|---|---|---|
|Datei|500||
|Registrierung|250||
|Windows-Software|250|Softwareupdates nicht inbegriffen|
|Linux-Pakete|1\.250||
|Dienste|250||
|Daemon|250||

#### <a name="update-management"></a>Updateverwaltung

In der folgenden Tabelle werden die Grenzwerte für die Updateverwaltung aufgeführt.

| **Ressource** | **Begrenzung**| **Hinweise** |
|---|---|---|
|Anzahl der Computer pro Aktualisierungsbereitstellung|1000||