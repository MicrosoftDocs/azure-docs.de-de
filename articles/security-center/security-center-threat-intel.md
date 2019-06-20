---
title: Informationen zu Bedrohungen und Karte mit Sicherheitswarnungen in Azure Security Center | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie anhand von Informationen zu Bedrohungen und der Karte mit Sicherheitswarnungen in Azure Security Center potenzielle Bedrohungen auf Ihren virtuellen und physischen Computern identifizieren.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 36201bad64e5516375afe1ec9ce141c3fd311d48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64574352"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Karte mit Sicherheitswarnungen und Informationen zu Bedrohungen
In diesem Artikel erfahren Sie, wie Sie sicherheitsbezogene Probleme mithilfe der Karte mit Sicherheitswarnungen und der auf Sicherheitsereignissen basierenden Informationen zu Bedrohungen in Azure Security Center lösen.

> [!NOTE]
> Die Kartenschaltfläche *Sicherheitsereignisse* wird am 31. Juli 2019 eingestellt. Weitere Informationen und alternative Dienste finden Sie unter [Auslaufen von Security Center-Funktionen (Juli 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Funktionsweise der Karte mit Sicherheitswarnungen
Security Center stellt Ihnen eine Karte bereit, mit deren Hilfe Sie Sicherheitsrisiken für die Umgebung identifizieren können. So können sie beispielsweise ermitteln, ob ein bestimmter Computer Teil eines Botnets ist und woher die Bedrohung stammt. Computer können zu Knoten in einem Botnet werden, wenn Angreifer illegal Schadsoftware installieren, die heimlich mit Befehls- und Steuerelementen interagiert, die das Botnet verwalten. 

Zum Erstellen dieser Karte werden in Security Center Daten aus mehreren Quellen von Microsoft herangezogen. Security Center nutzt diese Daten zur Darstellung potenzieller Bedrohungen für Ihre Umgebung. 

Einer der Schritte bei der [Reaktion auf Sicherheitsvorfälle](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) ist die Ermittlung des Schweregrads für die betroffenen Systeme. In dieser Phase sollten Sie die folgenden Aufgaben durchführen:

- Bestimmen der Art des Angriffs.
- Bestimmen des Ursprungs des Angriffs.
- Bestimmen der Absicht des Angriffs. Wurde Ihre Organisation gezielt angegriffen, um bestimmte Informationen zu erlangen, oder war es ein ungezielter Angriff?
- Ermitteln der betroffenen Systeme.
- Ermitteln der Dateien, auf die zugegriffen wurde, und Bestimmen der Vertraulichkeit dieser Dateien.

Bei diesen Aufgaben können Sie auf die Karte mit Sicherheitswarnungen in Security Center zurückgreifen.

## <a name="access-the-security-alerts-map"></a>Zugreifen auf die Karte mit Sicherheitswarnungen
Öffnen Sie die Karte mit Sicherheitswarnungen, um die aktuellen Bedrohungen für Ihre Umgebung darzustellen:

1. Öffnen Sie das Dashboard **Security Center**.
2. Wählen Sie im linken Bereich unter **Bedrohungsschutz** die Option **Sicherheitshinweise zuordnen** aus. Die Karte wird geöffnet.
3. Um weitere Informationen zu einer Warnung und Schritte zur Behebung anzuzeigen, klicken Sie auf den Punkt für die Warnung auf der Karte, und folgen Sie den Anweisungen. 
 
Die Karte mit Sicherheitswarnungen basiert auf Warnungen. Diese Warnungen basieren auf Aktivitäten, bei denen der Netzwerkkommunikation eine IP-Adresse zugeordnet wurde, die erfolgreich aufgelöst wurde, und zwar unabhängig davon, ob die IP-Adresse eine bekannte riskante IP-Adresse (z.B. ein bekannter Crypto-Miner ) oder eine IP-Adresse ist, die zuvor nicht als riskant erkannt wurde. Die Karte enthält Warnungen für alle Abonnements, die Sie zuvor in Azure ausgewählt haben. 

Die Warnungen auf der Karte werden entsprechend dem geografischen Standort angezeigt, der als deren Ursprung erkannt wurde, und sind nach Schweregrad farbcodiert. 
    ![Informationen zu Bedrohungen](./media/security-center-threat-intel/security-center-alert-map.png)

## <a name="viewing-the-event-based-threat-intelligence-dashboard"></a>Anzeigen des ereignisbasierten Dashboards mit Informationen zu Bedrohungen
Um die Karte mit Informationen zu Bedrohungen basierend auf Rohereignissen für die Sicherheit anzuzeigen, können Sie das folgende Verfahren ausführen. Auf dieser Karte werden nur Ereignisse angezeigt, bei denen eine als Risiko angesehene IP-Adresse beteiligt ist, z.B. die IP-Adresse eines bekannten Botnets.

1. Öffnen Sie das Dashboard **Security Center**.

1. Wählen Sie im linken Bereich unter **Bedrohungsschutz** die Option **Sicherheitshinweise zuordnen** aus. Die Karte wird geöffnet.
2. Klicken Sie in der oberen rechten Ecke auf **die entsprechende Option für das Wechseln zur Karte** mit Sicherheitsereignissen.
3. Wählen Sie den Arbeitsbereich aus, für den Sie das Dashboard anzeigen möchten.
4. Wählen Sie am oberen Rand der Karte **Klassische Threat Intelligence-Benutzeroberfläche** aus. Das Dashboard **Informationen zu Bedrohungen** wird geöffnet.

   > [!NOTE]
   > Wenn in der Spalte ganz rechts **UPGRADE PLAN** (PLAN UPGRADEN) angezeigt wird, wird für diesen Arbeitsbereich das kostenlose Abonnement verwendet. Führen Sie ein Upgrade auf „Standard“ durch, um das Feature verwenden zu können. Wenn in der Spalte ganz rechts **REQUIRES UPDATE** (UPDATE ERFORDERLICH) angezeigt wird, aktualisieren Sie [Azure Monitor-Protokolle](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), um das Feature verwenden zu können. Weitere Informationen zum Tarif finden Sie unter „Azure Security Center-Preise“.
   >
5. Falls Sie mehrere Arbeitsbereiche untersuchen müssen, priorisieren Sie die Untersuchung anhand der Spalte **Schädliche IP**. Sie enthält die aktuelle Anzahl schädlicher IP-Adressen in diesem Arbeitsbereich. Wählen Sie den gewünschten Arbeitsbereich aus. Das Dashboard **Informationen zu Bedrohungen** wird angezeigt.

    ![Informationen zu Bedrohungen](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

6. Das Dashboard ist in vier Kacheln unterteilt:

    a.  **Bedrohungstypen**. Hier sind die Arten von Bedrohungen zusammengefasst, die im ausgewählten Arbeitsbereich erkannt wurden.

    b.  **Ursprungsland**. Aggregiert die Datenverkehrsmenge auf der Grundlage der Quelle.

    c.  **Bedrohungsposition**. Hilft bei der Identifizierung der aktuellen Orte auf der ganzen Welt, die mit Ihrer Umgebung kommunizieren. Die Richtung des Datenverkehrs wird auf der angezeigten Karte durch farbige Pfeile (orange für eingehenden, rot für ausgehenden Datenverkehr) dargestellt. Wenn Sie auf einen der Pfeile klicken, werden die Art der Bedrohung und die Richtung des Datenverkehrs angezeigt.

    d.  **Bedrohungsdetails**. Hier werden weitere Details zu der Bedrohung angezeigt, die Sie auf der Karte ausgewählt haben.

Das angezeigte Dashboard basiert unabhängig von der ausgewählten Optionskachel auf der Protokollsuchabfrage. Der einzige Unterschied sind die Art der Abfrage und das Ergebnis.

### <a name="threat-types"></a>Bedrohungstypen
Klicken Sie auf die Kachel **Bedrohungstypen**, um das Dashboard **Protokollsuche** zu öffnen. Filteroptionen werden auf der linken, Abfrageergebnisse auf der rechten Seite angezeigt.

![Protokollsuche](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

Das Abfrageergebnis zeigt die Bedrohungen nach Name an. Im linken Bereich können Sie das Attribut auswählen, nach dem Sie filtern möchten. Wenn Sie also beispielsweise nur Bedrohungen anzeigen möchten, die momentan mit den Computern verbunden sind, klicken Sie unter **SESSIONSTATE** auf **Verbunden** > **Übernehmen**.

![Sitzungszustand](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Für virtuelle Azure-Computer werden im Dashboard **Informationen zu Bedrohungen** nur die Netzwerkdaten angezeigt, die den Agent durchlaufen. Für die Informationen zu Bedrohungen werden auch folgende Datentypen verwendet:

- CEF-Data (Type=CommonSecurityLog)
- WireData (Type=WireData)
- IIS-Protokolle (Type=W3CIISLog)
- Windows-Firewall (Type=WindowsFirewall)
- DNS-Ereignisse (Type=DnsEvents)


## <a name="see-also"></a>Weitere Informationen
In diesem Artikel wurde beschrieben, wie Sie Informationen zu Bedrohungen in Security Center beim Identifizieren von verdächtigen Aktivitäten nutzen. Weitere Informationen zu Security Center finden Sie in den folgenden Artikeln:

* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Hier erfahren Sie, wie Sie Warnungen verwalten und auf Sicherheitsvorfälle in Security Center reagieren.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md). Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verstehen der Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Hier finden Sie Informationen zu den unterschiedlichen Arten von Sicherheitswarnungen.
* [Azure Security Center – Handbuch zur Problembehandlung](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Hier erfahren Sie, wie Sie allgemeine Probleme in Security Center behandeln.
* [Azure Security Center – häufig gestellte Fragen](security-center-faq.md)festgelegt ist. Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/). Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.
