---
title: Überwachen von Azure Site Recovery | Microsoft-Dokumentation
description: Überwachung und Problembehandlung bei Replikationsproblemen und Vorgängen in Azure Site Recovery mithilfe des Portals
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: d441284b265ab11dd5ece42ec3737e455d662435
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023566"
---
# <a name="monitor-site-recovery"></a>Überwachen von Azure Site Recovery

In diesem Artikel erfahren Sie, wie Sie Azure [Site Recovery](site-recovery-overview.md) mithilfe der integrierten Site Recovery-Überwachung überwachen.  Folgendes kann überwacht werden:

- Integrität und Status der durch Site Recovery replizierten Computer
- Testfailoverstatus von Computern
- Probleme und Fehler mit Auswirkungen auf die Konfiguration und Replikation
- Infrastrukturkomponenten (beispielsweise lokale Server)


## <a name="before-you-start"></a>Vorbereitung

Machen Sie sich ggf. zunächst mit [allgemeinen Fragen zur Überwachung](monitoring-common-questions.md) vertraut.

## <a name="monitor-in-the-dashboard"></a>Überwachen mithilfe des Dashboards

1. Klicken Sie im Tresor auf **Übersicht**. Auf dem Recovery Services-Dashboard werden alle Überwachungsinformationen für den Tresor an einem zentralen Ort zusammengefasst. Sie können zwischen Seiten für Site Recovery und den Azure Backup-Dienst wechseln.

    ![Site Recovery-Dashboard](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Zeigen Sie über das Dashboard Detailinformationen zu verschiedenen Bereichen an. 

    ![Screenshot der Bereiche im Dashboard, in denen Sie einen Drilldown ausführen können](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)erforderlich.

3. Klicken Sie unter **Replizierte Elemente** auf **Alle anzeigen**, um alle Server im Tresor anzuzeigen.
4. Klicken Sie in den einzelnen Abschnitten auf die Statusdetails, um Detailinformationen anzuzeigen.
5. Sortieren Sie in der **Infrastrukturansicht** die Überwachungsinformationen nach der Art der replizierten Computer.

## <a name="monitor-replicated-items"></a>Überwachen replizierter Elemente

Überwachen Sie im Abschnitt **Replizierte Elemente** die Integrität aller Computer im Tresor, für die die Replikation aktiviert ist.

**State** | **Details**
--- | ---
Healthy | Die Replikation verläuft normal. Es werden keine Fehler oder Warnungssymptome erkannt.
Warnung | Mindestens ein Warnungssymptom wird erkannt, das sich auf die Replikation auswirken kann.
Kritisch | Mindestens ein Symptom eines schwerwiegenden Replikationsfehlers wurde erkannt.<br/><br/> Diese Fehlersymptome weisen in der Regel darauf hin, dass die Replikation hängen geblieben ist oder nicht so schnell verläuft, wie die Datenänderung fortschreitet.
Nicht verfügbar | Server, für die derzeit keine Replikation erwartet wird. Dazu können Computer gehören, für die ein Failover ausgeführt wurde.

## <a name="monitor-test-failovers"></a>Überwachen der Testfailover

Überwachen Sie unter **Failovertest erfolgreich** den Failoverstatus für Computer im Tresor.

- Es wird empfohlen, mindestens einmal alle sechs Monate ein Testfailover für replizierte Computer auszuführen. So können Sie ohne Unterbrechungen in der Produktionsumgebung überprüfen, ob das Failover wie erwartet funktioniert. 
- Ein Testfailover wird erst als erfolgreich betrachtet, nachdem das Failover und die Bereinigung nach dem Failover erfolgreich abgeschlossen wurden.

**State** | **Details**
--- | ---
Test empfohlen | Computer, für die seit Aktivierung des Schutzes kein Testfailover ausgeführt wurde
Erfolgreich ausgeführt | Computer mit mindestens einem erfolgreichen Testfailover
Nicht verfügbar | Computer, die derzeit nicht zu einem Testfailover berechtigt sind. Für Computer, für die ein Failover ausgeführt wird, wird beispielsweise gerade die erste Replikation/ein Testfailover/ein Failover ausgeführt.

## <a name="monitor-configuration-issues"></a>Überwachen von Konfigurationsproblemen

Überwachen Sie unter **Konfigurationsprobleme** alle Probleme, die unter Umständen das Failover beeinträchtigen.

- Konfigurationsprobleme (mit Ausnahme der Verfügbarkeit von Softwareupdates) werden durch einen periodischen Validierungssteuerelement-Vorgang erkannt, der standardmäßig alle zwölf Stunden ausgeführt wird. Sie können erzwingen, dass der Validierungssteuerelementvorgang sofort ausgeführt wird, indem Sie auf das Aktualisierungssymbol neben der Abschnittsüberschrift **Konfigurationsprobleme** klicken.
- Klicken Sie auf die Links, um weitere Details zu erhalten. Klicken Sie bei Problemen, die Auswirkungen auf bestimmte Computer haben, in der Spalte **Zielkonfigurationen** auf **Eingreifen erforderlich**. In den Details sind Empfehlungen zur Behebung angegeben.

**State** | **Details**
--- | ---
Fehlende Konfigurationen | Eine erforderliche Einstellung fehlt, etwa ein Wiederherstellungsnetzwerk oder eine Ressourcengruppe.
Missing resources (Fehlende Ressourcen) | Eine angegebene Ressource wurde nicht gefunden oder ist nicht im Abonnement verfügbar. Beispielsweise wurde die Ressource gelöscht oder migriert. Zu überwachten Ressourcen gehören die Zielressourcengruppe, Ziel-VNET/-Subnetz, Protokoll-/Zielspeicherkonto, Zielverfügbarkeitsgruppe und Ziel-IP-Adresse.
Abonnementkontingent |  Die verfügbare Abonnementressourcenkontingent-Bilanz wird mit der Bilanz verglichen, die zum Ausführen eines Failovers aller Computer im Tresor erforderlich ist.<br/><br/> Wenn nicht genügend Ressourcen vorhanden sind, wird nicht genügend Kontingentbilanz gemeldet.<br/><br/> Mit Kontingenten werden die Anzahl von VM-Kernen, die Anzahl von Kernen der VM-Familien und die Anzahl von Netzwerkadaptern (Network Interface Card, NIC) überwacht.
Softwareupdates | Die Verfügbarkeit neuer Softwareupdates sowie Informationen zu ablaufenden Softwareversionen


## <a name="monitor-errors"></a>Überwachen von Fehlern

Überwachen Sie unter **Fehlerzusammenfassung** die derzeit aktiven Fehlersymptome, die ggf. die Replikation von Servern im Tresor beeinträchtigen, sowie die Anzahl betroffener Computer.

- Fehler, die Auswirkungen auf lokale Infrastrukturkomponenten haben, werden am Anfang des Abschnitts angezeigt. Hierzu zählt beispielsweise das Ausbleiben der Übermittlung eines Heartbeats vom Azure Site Recovery-Anbieter auf dem lokalen Konfigurationsserver oder Hyper-V-Host.
- Danach sind Replikationsfehlersymptome aufgeführt, die Auswirkungen auf replizierte Server haben.
- Die Tabelleneinträge werden in absteigender Reihenfolge nach dem Schweregrad des Fehlers und dann in absteigender Reihenfolge nach der Anzahl der betroffenen Computer sortiert.
- Die Anzahl betroffener Server gibt Aufschluss darüber, ob sich ein einzelnes zugrunde liegendes Problem unter Umständen auf mehrere Computer auswirkt. Eine Netzwerkstörung kann sich beispielsweise potenziell auf alle Computer auswirken, die in Azure repliziert werden. 
- Auf einem einzelnen Server können mehrere Replikationsfehler auftreten. In diesem Fall wird in der Liste der betroffenen Server jedes Fehlersymptom dieses Servers einzeln gezählt. Nach der Behebung des Problems verbessern sich die Replikationsparameter, und der Fehler wird vom Computer gelöscht.

## <a name="monitor-the-infrastructure"></a>Überwachen der Infrastruktur

Überwachen Sie in der **Infrastrukturansicht** die an der Replikation beteiligten Infrastrukturkomponenten und die Konnektivität zwischen Servern und Azure-Diensten.

- Eine grüne Linie gibt an, dass die Verbindung fehlerfrei ist.
- Eine rote Linie mit dem überlagerten Fehlersymbol weist auf mindestens ein Fehlersymptom hin, das Auswirkungen auf die Konnektivität hat.
-  Wenn Sie den Mauszeiger auf das Fehlersymbol bewegen, werden der Fehler und die Anzahl der betroffenen Entitäten angezeigt. Klicken Sie auf das Symbol, um eine gefilterte Liste der betroffenen Entitäten anzuzeigen.

    ![Site Recovery-Infrastrukturansicht (Tresor)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Tipps zum Überwachen der Infrastruktur

- Stellen Sie sicher, dass auf den lokalen Infrastrukturkomponenten (Konfigurationsserver, Prozessserver, VMM-Server, Hyper-V-Hosts, VMware-Computer) die neueste Version des Site Recovery-Anbieters und/oder der Agents ausgeführt wird.
- Um alle Funktionen in der Infrastrukturansicht zu verwenden, müssen Sie [Updaterollup 22](https://support.microsoft.com/help/4072852) für diese Komponenten verwenden.
- Wählen Sie zum Verwenden der Infrastrukturansicht das entsprechende Replikationsszenario in Ihrer Umgebung aus. Sie können einen Drilldown in der Ansicht ausführen, um weitere Details anzuzeigen. Die folgende Tabelle enthält die dargestellten Szenarien.

    **Szenario** | **State**  | **Ansicht verfügbar?**
    --- |--- | ---
    **Replikation zwischen lokalen Standorten** | Alle Status | Nein 
    **Azure-VM-Replikation zwischen Azure-Regionen**  | Replikation aktiviert/derzeitige Ausführung der ersten Replikation | Ja
    **Azure-VM-Replikation zwischen Azure-Regionen** | Failover ausgeführt/Failback | Nein   
    **VMware-Replikation in Azure** | Replikation aktiviert/derzeitige Ausführung der ersten Replikation | Ja     
    **VMware-Replikation in Azure** | Failover/Failback ausgeführt | Nein      
    **Hyper-V-Replikation in Azure** | Failover/Failback ausgeführt | Nein

- Wenn Sie die Infrastrukturansicht für einen einzelnen replizierenden Computer anzeigen möchten, klicken Sie im Menü des Tresors auf **Replizierte Elemente**, und wählen Sie einen Server aus.  




## <a name="monitor-recovery-plans"></a>Überwachen von Wiederherstellungsplänen

Überwachen Sie unter **Wiederherstellungspläne** die Anzahl von Plänen, erstellen Sie neue Pläne, und ändern Sie bereits vorhandene Pläne.  

## <a name="monitor-jobs"></a>Überwachen von Aufträgen

Überwachen Sie unter **Aufträge** den Status der Site Recovery-Vorgänge.

- Die meisten Vorgänge in Azure Site Recovery werden asynchron ausgeführt, wobei ein Nachverfolgungsauftrag erstellt wird, der den Fortschritt des Vorgangs verfolgt. 
- Das Auftragsobjekt verfügt über alle Informationen, die Sie zum Nachverfolgen von Status und Fortschritt des Vorgangs benötigen. 

Aufträge werden wie folgt überwacht:

1. Auf dem Dashboard im Abschnitt **Aufträge** wird eine Zusammenfassung der Aufträge der letzten 24 Stunden angezeigt, die abgeschlossen sind, die gerade ausgeführt werden oder die auf eine Eingabe warten. Sie können auf einen beliebigen Status klicken, um weitere Informationen zu den entsprechenden Aufträgen abzurufen.
2. Klicken Sie auf **Alle anzeigen**, um alle Aufträge der letzten 24 Stunden anzuzeigen.

    > [!NOTE]
    > Sie können auf die Auftragsinformationen auch über **Site Recovery-Aufträge** im Tresormenü zugreifen. 

2. Die Liste **Site Recovery-Aufträge** enthält eine Liste der Aufträge. Über das obere Menü können Sie Fehlerdetails für bestimmte Aufträge abrufen, die Aufträge basierend auf bestimmten Kriterien filtern und ausgewählte Auftragsdetails in Excel exportieren.
3. Durch Klicken auf einen bestimmten Auftrag können Sie seine Detailinformationen anzeigen. 

## <a name="monitor-virtual-machines"></a>Überwachen virtueller Maschinen

Unter **Replizierte Elemente** erhalten Sie eine Liste der replizierten Computer. 
    ![Listenansicht der replizierten Elemente in Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Sie können Informationen betrachten und filtern. Über das Aktionsmenü im oberen Bereich können Sie Aktionen für einen bestimmten Computer ausführen und u.a. ein Testfailover ausführen oder bestimmte Fehler anzeigen.
3. Klicken Sie auf **Spalten**, um zusätzliche Spalten einzublenden und beispielsweise RPO, Zielkonfigurationsprobleme und Replikationsfehler anzuzeigen.
4. Klicken Sie auf **Filter**, um Informationen basierend auf bestimmten Parametern wie Replikationsintegrität oder basierend auf einer bestimmten Replikationsrichtlinie anzuzeigen.
5. Klicken Sie mit der rechten Maustaste auf einen Computer, um für ihn Vorgänge wie Testfailover zu initiieren oder bestimmte Fehlerdetails anzuzeigen.
6. Klicken Sie auf einen Computer, um weitere Details zu ihm anzuzeigen. Zu den Details gehören:
   - **Replikationsinformationen:** Aktueller Status und Integrität des Computers.
   - **RPO** (Recovery Point Objective): Aktuelle RPO für den virtuellen Computer und die Uhrzeit, an dem die RPO zuletzt berechnet wurde.
   - **Wiederherstellungspunkte**: Neueste verfügbare Wiederherstellungspunkte für den Computer.
   - **Failoverbereitschaft**: Gibt an, ob ein Testfailover für den Computer ausgeführt wurde, und zeigt die auf dem Computer ausgeführte Agent-Version (für Computer mit dem Mobilitätsdienst) und etwaige Konfigurationsprobleme an.
   - **Fehler**: Liste der Replikationsfehlersymptome, die derzeit auf dem Computer beobachtet werden, sowie mögliche Ursachen und empfohlene Aktionen.
   - **Ereignisse**: Eine chronologische Liste der aktuellen Ereignisse, die den Computer beeinträchtigen. Während Fehlerdetails die derzeit auf dem Computer feststellbaren Fehlersymptome anzeigen, sind die Ereignisse eine historische Aufzeichnung von Problemen, die Auswirkungen auf den Computer gehabt haben.
   - **Infrastrukturansicht**: Zeigt den Status der Infrastruktur für das Szenario an, wenn Computer in Azure repliziert werden.

     ![Details/Übersicht der replizierten Elemente in Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Abonnieren von E-Mail-Benachrichtigungen

Sie können E-Mail-Benachrichtigungen für die folgenden kritischen Ereignisse abonnieren:
 
- Kritischer Zustand für den replizierten Computer
- Keine Konnektivität zwischen den lokalen Infrastrukturkomponenten und dem Site Recovery-Dienst. Die Konnektivität zwischen Site Recovery und den in einem Tresor registrierten lokalen Servern wird mithilfe eines Heartbeatmechanismus erkannt.
- Fehler beim Failover

Benachrichtigungen werden wie folgt abonniert:

Klicken Sie im Tresor im Abschnitt **Überwachung** auf **Site Recovery-Ereignisse**.
1. Klicken Sie auf **E-Mail-Benachrichtigungen**.
1. Aktivieren Sie unter **E-Mail-Benachrichtigung** die Benachrichtigungen, und geben Sie an, an wen sie gesendet werden sollen. Sie können festlegen, dass Benachrichtigungen an alle Abonnementadministratoren gesendet werden sollen, und optional bestimmte E-Mail-Adressen angeben.

    ![E-Mail-Benachrichtigungen](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit der [Überwachen von Site Recovery mit Azure Monitor-Protokollen](monitor-log-analytics.md) vertraut.
