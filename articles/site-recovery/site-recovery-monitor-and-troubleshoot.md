---
title: Überwachen von Azure Site Recovery | Microsoft-Dokumentation
description: Überwachung und Problembehandlung bei Replikationsproblemen und Vorgängen in Azure Site Recovery mithilfe des Portals
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 5a659da4bcc86544c31d7a789779253a0f571f34
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497547"
---
# <a name="monitor-site-recovery"></a>Überwachen von Azure Site Recovery

In diesem Artikel erfahren Sie, wie Sie die integrierten Überwachungsfunktionen von Azure Site Recovery für Überwachung und Problembehandlung nutzen. 

## <a name="use-the-dashboard"></a>Verwenden des Dashboards

1. Klicken Sie im Tresor auf **Übersicht**, um das Site Recovery-Dashboard zu öffnen. Es gibt Dashboardseiten für Site Recovery und Backup, und Sie können zwischen diesen Seiten wechseln.

    ![Site Recovery-Dashboard](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2.  Im Dashboard werden alle Überwachungsinformationen für den Tresor an einem zentralen Ort zusammengefasst. Über das Dashboard können Sie Detailinformationen für verschiedene Bereiche anzeigen. 

    ![Site Recovery-Dashboard](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. Klicken Sie unter **Replizierte Elemente** auf **Alle anzeigen**, um alle Server im Tresor anzuzeigen.
4. Zeigen Sie Detailinformationen an, indem Sie in jedem Abschnitt auf die Statusdetails klicken. Unter **Infrastrukturansicht** können Sie die Überwachungsinformationen nach dem Typ der replizierten Computer sortieren.

## <a name="monitor-replicated-items"></a>Überwachen replizierter Elemente

Im Abschnitt „Replizierte Elemente“ wird die Integrität aller Computer angezeigt, für die die Replikation im Tresor aktiviert ist.

**State** | **Details**
--- | ---
Healthy | Die Replikation verläuft normal. Es werden keine Fehler oder Warnungssymptome erkannt.
Warnung | Mindestens ein Warnungssymptom wird erkannt, das sich auf die Replikation auswirken kann.
Kritisch | Mindestens ein Symptom eines schwerwiegenden Replikationsfehlers wurde erkannt.<br/><br/> Diese Fehlersymptome weisen in der Regel darauf hin, dass die Replikation hängen geblieben ist oder nicht so schnell verläuft, wie die Datenänderung fortschreitet.
Nicht zutreffend | Server, für die derzeit keine Replikation erwartet wird. Dazu können Computer gehören, für die ein Failover ausgeführt wurde.

## <a name="monitor-test-failovers"></a>Überwachen der Testfailover

Sie können den Status des Testfailovers für Computer im Tresor anzeigen.

- Es wird empfohlen, mindestens einmal alle sechs Monate ein Testfailover für replizierte Computer auszuführen. So können Sie ohne Unterbrechungen in der Produktionsumgebung überprüfen, ob das Failover wie erwartet funktioniert. 
- Ein Testfailover wird erst als erfolgreich betrachtet, nachdem das Failover und die Bereinigung nach dem Failover erfolgreich abgeschlossen wurden.

**State** | **Details**
--- | ---
Test empfohlen | Computer, für die seit Aktivierung des Schutzes kein Testfailover ausgeführt wurde
Erfolgreich ausgeführt | Computer mit mindestens einem erfolgreichen Testfailover
Nicht zutreffend | Computer, die derzeit nicht zu einem Testfailover berechtigt sind. Für Computer, für die ein Failover ausgeführt wird, wird beispielsweise gerade die erste Replikation/ein Testfailover/ein Failover ausgeführt.

## <a name="monitor-configuration-issues"></a>Überwachen von Konfigurationsproblemen

Im Abschnitt **Konfigurationsprobleme** wird eine Liste der Probleme angezeigt, die Ihre Fähigkeit, ein erfolgreiches Failover auszuführen, beeinträchtigen können.

- Konfigurationsprobleme (mit Ausnahme der Verfügbarkeit von Softwareupdates) werden durch einen periodischen Validierungssteuerelement-Vorgang erkannt, der standardmäßig alle zwölf Stunden ausgeführt wird. Sie können erzwingen, dass der Validierungssteuerelementvorgang sofort ausgeführt wird, indem Sie auf das Aktualisierungssymbol neben der Abschnittsüberschrift **Konfigurationsprobleme** klicken.
- Klicken Sie auf die Links, um weitere Details zu erhalten. Klicken Sie bei Problemen, die Auswirkungen auf bestimmte Computer haben, in der Spalte **Target configurations** (Zielkonfigurationen) auf **Eingreifen erforderlich**. In den Details sind Empfehlungen zur Behebung angegeben.

**State** | **Details**
--- | ---
Fehlende Konfigurationen | Eine erforderliche Einstellung fehlt, etwa ein Wiederherstellungsnetzwerk oder eine Ressourcengruppe.
Missing resources (Fehlende Ressourcen) | Eine angegebene Ressource wurde nicht gefunden oder ist nicht im Abonnement verfügbar. Beispielsweise wurde die Ressource gelöscht oder migriert. Zu überwachten Ressourcen gehören die Zielressourcengruppe, Ziel-VNET/-Subnetz, Protokoll-/Zielspeicherkonto, Zielverfügbarkeitsgruppe und Ziel-IP-Adresse.
Abonnementkontingent |  Die verfügbare Abonnementressourcenkontingent-Bilanz wird mit der Bilanz verglichen, die zum Ausführen eines Failovers aller Computer im Tresor erforderlich ist.<br/><br/> Wenn nicht genügend Ressourcen vorhanden sind, wird nicht genügend Kontingentbilanz gemeldet.<br/><br/> Mit Kontingenten werden die Anzahl von VM-Kernen, die Anzahl von Kernen der VM-Familien und die Anzahl von Netzwerkadaptern (Network Interface Card, NIC) überwacht.
Softwareupdates | Die Verfügbarkeit neuer Softwareupdates sowie Informationen zu ablaufenden Softwareversionen


## <a name="monitoring-errors"></a>Nachverfolgen von Fehlern 
Im Abschnitt **Fehlerzusammenfassung** werden die derzeit aktiven Fehlersymptome, die die Replikation von Servern im Tresor beeinträchtigen könnten, sowie die Anzahl der betroffenen Computer angezeigt.

- Am Anfang des Abschnitts sind Fehler aufgeführt, die Auswirkungen auf lokale Infrastrukturkomponenten haben. Hierzu zählt beispielsweise das Nichtempfangen eines Heartbeats von dem Azure Site Recovery-Anbieter, der auf dem lokalen Konfigurationsserver, VMM-Server oder Hyper-V-Host ausgeführt wird.
- Danach sind Replikationsfehlersymptome aufgeführt, die Auswirkungen auf replizierte Server haben.
- Die Tabelleneinträge werden in absteigender Reihenfolge nach dem Schweregrad des Fehlers und dann in absteigender Reihenfolge nach der Anzahl der betroffenen Computer sortiert.
- Die Anzahl der betroffenen Server ist eine gute Möglichkeit, zu verstehen, ob ein einzelnes zugrunde liegendes Problem unter Umständen mehrere Computer beeinträchtigt. Beispielsweise kann sich eine Netzwerkstörung potenziell auf alle Computer auswirken, die in Azure repliziert werden. 
- Auf einem einzelnen Server können mehrere Replikationsfehler auftreten. In diesem Fall wird in der Liste der betroffenen Server jedes Fehlersymptom dieses Servers einzeln gezählt. Nach der Behebung des Problems verbessern sich die Replikationsparameter, und der Fehler wird vom Computer gelöscht.

## <a name="monitor-the-infrastructure"></a>Überwachen der Infrastruktur

Die **Infrastrukturansicht** enthält die an der Replikation beteiligten Infrastrukturkomponenten und die Integrität der Konnektivität zwischen Servern und Azure-Diensten.

- Eine grüne Linie gibt an, dass die Verbindung fehlerfrei ist.
- Eine rote Linie mit dem überlagerten Fehlersymbol weist auf mindestens ein Fehlersymptom hin, das Auswirkungen auf die Konnektivität hat.
-  Wenn Sie den Mauszeiger auf das Fehlersymbol bewegen, werden der Fehler und die Anzahl der betroffenen Entitäten angezeigt. Klicken Sie auf das Symbol, um eine gefilterte Liste der betroffenen Entitäten anzuzeigen.

    ![Site Recovery-Infrastrukturansicht (Tresor)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

## <a name="tips-for-monitoring-the-infrastructure"></a>Tipps zum Überwachen der Infrastruktur

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

### <a name="common-questions"></a>Häufig gestellte Fragen


**Warum unterscheidet sich die Anzahl der virtuellen Computer in der Tresorinfrastrukturansicht von der Gesamtzahl, die in den replizierten Elementen angezeigt wird?**

Die Tresorinfrastrukturansicht ist nach Replikationsszenarien begrenzt. Nur Computer im aktuell ausgewählten Replikationsszenario werden in die Anzahl für die Ansicht einbezogen. Darüber hinaus werden nur virtuelle Computer gezählt, die für die Replikation in Azure konfiguriert sind. Computer, für die ein Failover ausgeführt wurde, oder Computer, die zurück am lokalen Standort repliziert werden, werden nicht in die Ansicht aufgenommen.

**Warum unterscheidet sich die Anzahl der replizierten Elemente im Zusammenfassungsfach von der Gesamtzahl der auf dem Dashboard angezeigten replizierten Elemente?**

Nur die Computer, für die die erste Replikation abgeschlossen wurde, sind in der im Zusammenfassungsfach angezeigten Anzahl enthalten. Die replizierten Elemente enthalten insgesamt alle Computer im Tresor, einschließlich der Computer, für die die erste Replikation gerade ausgeführt wird.


## <a name="monitor-recovery-plans"></a>Überwachen von Wiederherstellungsplänen

Im Abschnitt **Wiederherstellungspläne** können Sie die Anzahl von Plänen überprüfen, neue Pläne erstellen und vorhandene Pläne ändern.  

## <a name="monitor-jobs"></a>Überwachen von Aufträgen

Der Abschnitt **Aufträge** enthält den Status der Site Recovery-Vorgänge.

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

Darüber hinaus können Sie Computer auf der Seite „Virtuelle Computer“ überwachen. 

1. Klicken Sie im Tresor auf **Replizierte Elemente**, um eine Liste der replizierten Computer abzurufen.  Alternativ können Sie durch Klicken auf die bereichsbezogenen Verknüpfungen auf der Dashboardseite eine gefilterte Liste der geschützten Elemente abrufen.

    ![Listenansicht der replizierten Elemente in Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Auf der Seite **Replizierte Elemente** können Sie Informationen anzeigen und filtern. Über das Aktionsmenü im oberen Bereich können Sie Aktionen für einen bestimmten Computer ausführen und u.a. ein Testfailover ausführen oder bestimmte Fehler anzeigen.
3. Klicken Sie auf **Spalten**, um zusätzliche Spalten einzublenden und beispielsweise RPO, Zielkonfigurationsprobleme und Replikationsfehler anzuzeigen.
4. Klicken Sie auf **Filter**, um Informationen basierend auf bestimmten Parametern wie Replikationsintegrität oder basierend auf einer bestimmten Replikationsrichtlinie anzuzeigen.
5. Klicken Sie mit der rechten Maustaste auf einen Computer, um für ihn Vorgänge wie Testfailover zu initiieren oder bestimmte Fehlerdetails anzuzeigen.
6. Klicken Sie auf einen Computer, um weitere Details zu ihm anzuzeigen. Zu den Details gehören:
   - **Replikationsinformationen:** Aktueller Status und Integrität des Computers.
   - **RPO** (Recovery Point Objective): Aktuelles RPO für den virtuellen Computer und die Uhrzeit, an dem das RPO zuletzt berechnet wurde.
   - **Wiederherstellungspunkte:** Letzte verfügbare Wiederherstellungspunkte für den Computer.
   - **Failoverbereitschaft:** Gibt an, ob ein Testfailover für den Computer ausgeführt wurde, und zeigt die auf dem Computer ausgeführte Agent-Version (für Computer mit dem Mobilitätsdienst) und etwaige Konfigurationsprobleme an.
   - **Fehler:** Liste der Replikationsfehlersymptome, die derzeit auf dem Computer beobachtet werden, sowie mögliche Ursachen und empfohlene Aktionen.
   - **Ereignisse:** Chronologische Liste der aktuellen Ereignisse, die den Computer beeinträchtigen. Während Fehlerdetails die derzeit auf dem Computer feststellbaren Fehlersymptome anzeigen, sind die Ereignisse eine historische Aufzeichnung von Problemen, die Auswirkungen auf den Computer gehabt haben.
   - **Infrastrukturansicht:** Zeigt den Status der Infrastruktur für das Szenario an, wenn Computer in Azure repliziert werden.

     ![Details/Übersicht der replizierten Elemente in Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)


### <a name="common-questions"></a>Häufig gestellte Fragen

**Wie unterscheidet sich die RPO vom letzten verfügbaren Wiederherstellungspunkt?**


- Site Recovery verwendet einen mehrstufigen asynchronen Prozess zum Replizieren von Computern in Azure.
- Im vorletzten Schritt der Replikation werden aktuelle Änderungen auf dem Computer sowie Metadaten in ein Protokoll-/Cachespeicherkonto kopiert.
- Diese Änderungen werden zusammen mit dem Tag zum Identifizieren eines wiederherstellbaren Punkts in das Speicherkonto in der Zielregion geschrieben.
-  Site Recovery kann nun einen wiederherstellbaren Punkt für den virtuellen Computer generieren.
- An diesem Punkt ist die RPO für die Änderungen erfüllt, die bisher in das Speicherkonto hochgeladen wurden. Mit anderen Worten: Die RPO für den Computer entspricht an diesem Punkt der Zeitspanne, die seit dem Zeitstempel verstrichen ist, der dem wiederherstellbaren Punkt entspricht.
- Site Recovery wählt nun die hochgeladenen Daten im Speicherkonto aus und wendet sie auf die Replikatdatenträger an, die für den Computer erstellt wurden.
- Site Recovery generiert dann einen Wiederherstellungspunkt und macht diesen Punkt beim Failover für eine Wiederherstellung verfügbar. Daher gibt der letzte verfügbare Wiederherstellungspunkt den Zeitstempel an, der dem letzten Wiederherstellungspunkt entspricht, der bereits verarbeitet und auf die Replikatdatenträger angewendet wurde.

> [!NOTE]
> Eine falsche Systemzeit auf dem replizierenden Quellcomputer oder auf den lokalen Infrastrukturservern verfälscht den berechneten RPO-Wert. Um sicherzustellen, dass die RPO-Werte genau berichtet werden, achten Sie darauf, dass die Systemuhr auf allen Servern und Computern korrekt eingestellt ist. 

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
