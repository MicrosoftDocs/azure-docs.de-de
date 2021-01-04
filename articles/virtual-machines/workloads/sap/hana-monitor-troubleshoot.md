---
title: Überwachung und Problembehandlung von SAP HANA in Azure (große Instanzen) auf der HANA-Seite | Microsoft-Dokumentation
description: Überwachung und Problembehandlung von SAP HANA in Azure (große Instanzen) auf der HANA-Seite
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e560fc996393969eecb45a3fdda24bc940436dc0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967719"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Überwachung und Problembehandlung auf HANA-Seite

Um Probleme im Zusammenhang mit SAP HANA in Azure (große Instanzen) effektiv zu analysieren, ist es nützlich, die Ursache eines Problems einzugrenzen. SAP hat eine große Menge an Dokumentation veröffentlicht, die Ihnen helfen kann.

Häufig gestellte Fragen im Zusammenhang mit der SAP HANA-Leistung finden Sie in den folgenden SAP Notes:

- [SAP Note #2222200 – FAQ: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Note #2100040 – FAQ: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Note #199997 – FAQ: SAP HANA Memory](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – FAQ: SAP HANA Performance Optimization](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Note #199930 – FAQ: SAP HANA I/O Analysis](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 – FAQ: SAP HANA Service Restart and Crashes](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA-Warnungen

Überprüfen Sie im ersten Schritt die aktuellen SAP HANA-Warnungsprotokolle. Wechseln Sie in SAP HANA Studio zu **Administration Console: Alerts: Show: All alerts**. Diese Registerkarte zeigt alle SAP HANA-Warnungen zu bestimmten Werten (freier physischer Arbeitsspeicher, CPU-Auslastung usw.), die außerhalb der festgelegten minimalen und maximalen Grenzwerte liegen. Standardmäßig werden die Überprüfungen alle 15 Minuten automatisch aktualisiert.

![Wechseln Sie in SAP HANA Studio zu „Administration Console: Alerts: Show: All alerts“](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

Für eine Warnung, die aufgrund einer nicht ordnungsgemäßen Schwellenwerteinstellung ausgelöst wird, besteht eine Lösung entweder im Zurückzusetzen auf den Standardwert oder im Festlegen eines geeigneteren Schwellenwerts.

![Zurücksetzen auf den Standardwert oder einen geeigneteren Schwellenwert](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Die folgenden Warnungen können ein Hinweis auf CPU-Ressourcenprobleme sein:

- Host CPU Usage (Alert 5)
- Most recent savepoint operation (Alert 28)
- Savepoint duration (Alert 54)

Anhand der folgenden Angaben können Sie ggf. eine hohe CPU-Auslastung für Ihre SAP HANA-Datenbank feststellen:

- „Alert 5 (Host CPU Usage)“ wird für die aktuelle oder bisherige CPU-Auslastung ausgelöst
- Die angezeigte CPU-Auslastung auf dem Übersichtsbildschirm

![Auf dem Übersichtsbildschirm angezeigte CPU-Auslastung](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Das Diagramm „Load“ kann eine hohe CPU-Auslastung bzw. hohe Auslastung in der Vergangenheit anzeigen:

![Das Diagramm „Load“ kann eine hohe CPU-Auslastung bzw. eine hohe Auslastung in der Vergangenheit anzeigen](./media/troubleshooting-monitoring/image4-load-graph.png)

Eine Warnung, die aufgrund einer hohen CPU-Auslastung ausgelöst wird, kann mehrere Ursachen haben. Dazu zählen u.a. die Ausführung bestimmter Transaktionen, das Laden von Daten, Aufträge, die nicht reagieren, zeitintensive SQL-Anweisungen und eine schwache Abfrageleistung (z.B. bei BW in HANA-Cubes).

Auf der Website [SAP HANA Troubleshooting: CPU Related Causes and Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) finden Sie ausführliche Schritte zur Problembehandlung.

## <a name="operating-system"></a>Betriebssystem

Eine der wichtigsten Überprüfungen für SAP HANA unter Linux ist, ob Transparent Huge Pages deaktiviert sind. Siehe hierzu [SAP Note #2131662 – Transparent Huge Pages (THP) on SAP HANA Servers](https://launchpad.support.sap.com/#/notes/2131662).

- Sie können prüfen, ob Transparent Huge Pages aktiviert sind, indem Sie den folgenden Linux-Befehl ausführen: **cat /sys/kernel/mm/transparent\_hugepage/enabled**
- Wenn _always_ wie unten in eckige Klammer gesetzt ist, bedeutet dies, dass Transparent Huge Pages aktiviert sind: [always] madvise never. Wenn _never_ wie unten in eckige Klammer gesetzt ist, bedeutet dies, dass Transparent Huge Pages deaktiviert sind: always madvise [never]

Die folgende Linux-Befehl sollte nichts zurückgeben: **rpm -qa | grep ulimit**. Sollte _ulimit_ installiert sein, führen Sie sofort eine Deinstallation durch.

## <a name="memory"></a>Arbeitsspeicher

Sie bemerken möglicherweise, dass der von der SAP HANA-Datenbank reservierte Arbeitsspeicher größer als erwartet ist. Die folgenden Warnungen sind Anzeichen von Problemen aufgrund einer hohen Speicherauslastung:

- Host physical memory usage (Alert 1)
- Memory usage of name server (Alert 12)
- Total memory usage of Column Store tables (Alert 40)
- Memory usage of services (Alert 43)
- Memory usage of main storage of Column Store tables (Alert 45)
- Runtime dump files (Alert 46)

Auf der Website [SAP HANA Troubleshooting: Memory Problems](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) finden Sie ausführliche Schritte zur Problembehandlung.

## <a name="network"></a>Netzwerk

Führen Sie die Schritte zur Behandlung von Netzwerkproblemen aus, die in [SAP Note #2081065 – Troubleshooting SAP HANA Network](https://launchpad.support.sap.com/#/notes/2081065) beschrieben werden.

1. Analysieren Sie die Roundtripzeit zwischen Server und Client.
  A. Führen Sie das SQL-Skript [_HANA\_Network\_Clients_](https://launchpad.support.sap.com/#/notes/1969700) _aus._
  
2. Analysieren Sie die Kommunikation zwischen Knoten.
  A. Führen Sie das SQL-Skript [_HANA\_Network\_Services_](https://launchpad.support.sap.com/#/notes/1969700) _aus._

3. Führen Sie den Linux-Befehl **ifconfig** aus (die Ausgabe zeigt, ob Paketverluste auftreten).
4. Führen Sie den Linux-Befehl **tcpdump** aus.

Verwenden Sie außerdem ein Open-Source-Tool wie [IPERF](https://iperf.fr/), um die tatsächliche Netzwerkleistung der Anwendung zu messen.

Auf der Website [SAP HANA Troubleshooting: Networking Performance and Connectivity Problems](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) finden Sie ausführliche Schritte zur Problembehandlung.

## <a name="storage"></a>Storage

Wenn es Probleme mit der E/A-Leistung gibt, kann eine Anwendung aus Sicht der Endbenutzer langsam ausgeführt werden, nicht reagieren oder sich sogar aufhängen. Auf der Registerkarte **Volumes** in SAP HANA Studio können Sie sehen, welche Volumes angefügt sind und vom jeweiligen Dienst verwendet werden.

![Auf der Registerkarte „Volumes“ in SAP HANA Studio können Sie sehen, welche Volumes angeschlossen sind und vom jeweiligen Dienst verwendet werden](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Für angefügte Volumes werden unten auf dem Bildschirm Details angezeigt, z.B. Datei- und E/A-Statistiken.

![Für angefügte Volumes werden unten auf dem Bildschirm Details angezeigt, z.B. Datei- und E/A-Statistiken](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Auf den Websites [SAP HANA Troubleshooting: I/O Related Root Causes and Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) und [SAP HANA Troubleshooting: Disk Related Root Causes and Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) finden Sie ausführliche Anweisungen zur Problembehandlung.

## <a name="diagnostic-tools"></a>Diagnosetools

Führen Sie über „HANA\_Configuration\_Minichecks“ eine SAP HANA-Integritätsprüfung durch. Dieses Tool gibt potenzielle technische Probleme zurück, die in SAP HANA Studio bereits als Warnungen hätten ausgelöst werden sollen.

Lesen Sie [SAP Note #1969700 – SQL statement collection for SAP HANA](https://launchpad.support.sap.com/#/notes/1969700), und laden Sie die an diesen Hinweis angefügte Datei „SQL Statements.zip“ herunter. Speichern Sie diese ZIP-Datei auf der lokalen Festplatte.

Klicken Sie in SAP HANA Studio auf der Registerkarte **System Information** mit der rechten Maustaste auf die Spalte **Name**, und wählen Sie **Import SQL Statements** aus.

![Klicken Sie in SAP HANA Studio auf der Registerkarte „System Information“ mit der rechten Maustaste auf die Spalte „Name“, und wählen Sie „Import SQL Statements“ aus](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Wählen Sie die lokal gespeicherte Datei „SQL-Statements.zip“ und ein Ordner aus, in den die entsprechenden SQL-Anweisungen importiert werden. An diesem Punkt können viele verschiedene Diagnoseprüfungen mit diesen SQL-Anweisungen ausgeführt werden.

Um z.B. die Bandbreitenanforderungen für die SAP HANA-Systemreplikation zu testen, klicken Sie mit der rechten Maustaste unter **Replication: Bandwidth** auf die Anweisung **Bandwidth** und wählen in der SQL Console **Open** aus.

Die vollständige SQL-Anweisung wird geöffnet, sodass Sie Eingabeparameter (im Abschnitt „modification“) ändern und anschließend ausführen können.

![Die vollständige SQL-Anweisung wird geöffnet, sodass Sie Eingabeparameter (im Abschnitt „modification“) ändern und anschließend ausführen können](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Ein weiteres Beispiel ist das Klicken mit der rechten Maustaste auf die Anweisungen unter **Replication: Overview**. Wählen Sie im Kontextmenü **Execute** aus:

![Ein weiteres Beispiel ist das Klicken mit der rechten Maustaste auf die Anweisungen unter „Replication: Overview“. Wählen Sie im Kontextmenü „Execute“ aus](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Daraufhin werden Informationen angezeigt, die bei der Problembehandlung helfen:

![Dies führt zu Informationen, die bei der Problembehandlung helfen](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Führen Sie dieselben Schritte für „HANA\_Configuration\_Minichecks“ aus, und suchen Sie nach _X_ in der Spalte _C_ (Critical).

Beispielausgaben:

**HANA\_Configuration\_MiniChecks\_Rev102.01+1** für allgemeine SAP HANA-Prüfungen.

![HANA\_Configuration\_MiniChecks\_Rev102.01+1 für allgemeine SAP HANA-Prüfungen](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_Services\_Overview** für eine Übersicht der derzeit ausgeführten SAP HANA-Dienste.

![HANA\_Services\_Overview für eine Übersicht der derzeit ausgeführten SAP HANA-Dienste](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_Services\_Statistics** für Informationen zu SAP HANA-Diensten (CPU, Arbeitsspeicher usw.).

![HANA\_Services\_Statistics für Informationen zu SAP HANA-Diensten](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_Configuration\_Overview\_Rev110+** für allgemeine Informationen zur SAP HANA-Instanz.

![HANA\_Configuration\_Overview\_Rev110+ für allgemeine Informationen zur SAP HANA-Instanz](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_Configuration\_Parameters\_Rev70+** für eine Überprüfung von SAP HANA-Parametern.

![HANA\_Configuration\_Parameters\_Rev70+ für eine Überprüfung von SAP HANA-Parametern](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Nächste Schritte**

- Lesen Sie [Einrichten von Hochverfügbarkeit unter SUSE mit STONITH](ha-setup-with-stonith.md).