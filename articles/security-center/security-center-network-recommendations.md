---
title: Schützen Ihrer Netzwerkressourcen in Azure Security Center | Microsoft-Dokumentation
description: Dieses Dokument behandelt Empfehlungen in Azure Security Center, die zum Schutz Ihrer Azure-Netzwerkressourcen sowie zur Einhaltung von Sicherheitsrichtlinien beitragen.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: v-mohabe
ms.openlocfilehash: 6b3cef32cf79c2448d2e254e27c332e01ea83c62
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428362"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Schützen Ihrer Netzwerkressourcen in Azure Security Center
Azure Security Center analysiert ständig den Sicherheitsstatus Ihrer Azure-Ressourcen anhand bewährter Methoden für Netzwerksicherheit. Werden potenzielle Sicherheitslücken erkannt, erstellt Security Center Empfehlungen, die Sie beim Konfigurieren der erforderlichen Steuerelemente zum Sichern und Schützen Ihrer Ressourcen unterstützen.

Dieser Artikel behandelt Empfehlungen, die aus Sicht der Netzwerksicherheit auf Ihre Azure-Ressourcen zutreffen. Bei Netzwerkempfehlungen stehen Firewalls der nächsten Generation, Netzwerksicherheitsgruppen, Regeln für den eingehenden Datenverkehr mit übermäßigem JIT-VM-Zugriff und vieles mehr im Mittelpunkt. Eine Liste von Netzwerkempfehlungen und Aktionen zur Problembehebung finden Sie unter [Verwalten von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md).

> [!NOTE]
> Die Seite **Netzwerk** ermöglicht Ihnen detaillierte Einblicke in die Integrität Ihrer Azure-Ressourcen aus Netzwerksicht. Die Netzwerkübersicht und adaptive Netzwerksteuerelemente stehen nur für Azure Security Center im Standard-Tarif zur Verfügung. [Wenn Sie den Free-Tarif verwenden, können Sie auf die Schaltfläche **Legacynetzwerk anzeigen** klicken und Empfehlungen für Netzwerkressourcen erhalten](#legacy-networking).
>

Die Seite **Netzwerk** bietet eine Übersicht über die Abschnitte, die Sie genauer prüfen können, um weitere Informationen über die Integrität Ihrer Netzwerkressourcen zu erhalten:

- Netzwerkübersicht (nur im Standard-Tarif von Azure Security Center)
- Adaptives Erhöhen des Netzwerkschutzes
- Empfehlungen für die Netzwerksicherheit
- Blatt **Legacynetzwerk** (das frühere Blatt „Netzwerk“) 
 
![Bereich „Netzwerk“](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Netzwerkübersicht
Die interaktive Netzwerkübersicht bietet eine grafische Darstellung mit Angaben zu Sicherheitsaspekten, die Empfehlungen und Einblicke zum Absichern Ihrer Netzwerkressourcen umfassen. Anhand der Übersicht können Sie die Netzwerktopologie Ihrer Azure-Workloads sowie Verbindungen zwischen Ihren virtuellen Computern und Subnetzen erkennen. Außerdem können Sie von der Übersicht aus genauere Informationen zu bestimmten Ressourcen und die Empfehlungen für diese Ressourcen anzeigen.

So öffnen Sie die Netzwerkübersicht:

1. Wählen Sie in Security Center unter „Ressourcensicherheit“ die Option **Netzwerk** aus.
2. Klicken Sie unter **Netzwerkübersicht** auf **Topologie anzeigen**.
 
Die Standardansicht der Topologie umfasst Folgendes:
- Abonnements, die Sie in Azure ausgewählt haben. Die Übersicht unterstützt mehrere Abonnements.
- Virtuelle Computer, Subnetze und VNets vom Resource Manager-Ressourcentyp (klassische Azure-Ressourcen werden nicht unterstützt).
- VNets mit Peering
- Nur Ressourcen, die [Netzwerkempfehlungen](security-center-recommendations.md) mit einem hohen oder mittleren Schweregrad aufweisen.  
- Ressourcen mit Internetzugriff.
- Die Übersicht ist für die in Azure ausgewählten Abonnements optimiert. Wenn Sie die Auswahl ändern, wird die Übersicht neu berechnet und entsprechend den neuen Einstellungen optimiert.  

![Übersicht über die Netzwerktopologie](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Grundlegendes zur Netzwerkübersicht

In der Netzwerkübersicht können Ihre Azure-Ressourcen in der Ansicht **Topologie** und der Ansicht **Datenverkehr** angezeigt werden.

### <a name="the-topology-view"></a>Topologieansicht

Die Ansicht **Topologie** der Netzwerkübersicht bietet Ihnen folgende Einblicke in Ihre Netzwerkressourcen:
- Im inneren Kreis sehen Sie alle VNETs in den ausgewählten Abonnements, der nächste Kreis zeigt alle Subnetze und der äußere Kreis alle virtuellen Computer.
- Die Verbindungslinien zwischen den Ressourcen in der Übersicht geben an, welche Ressourcen miteinander verknüpft sind und wie Ihr Azure-Netzwerk strukturiert ist. 
- Verwenden Sie die Indikatoren für den Schweregrad, um schnell einen Überblick über die Ressourcen zu erhalten, für die offene Empfehlungen aus Security Center vorhanden sind.
- Sie können auf einzelne Ressourcen klicken, um genauere Informationen aufzurufen und die Details zu dieser Ressource sowie die jeweiligen Empfehlungen direkt und im Kontext der Netzwerkübersicht anzuzeigen.  
- Wenn zu viele Ressourcen in der Übersicht angezeigt werden, verwendet Azure Security Center einen eigenen Algorithmus zum intelligenten Gruppieren Ihrer Ressourcen und hebt diejenigen hervor, die den kritischsten Zustand und die meisten Empfehlungen mit höchstem Schweregrad aufweisen. 

Da die Übersicht interaktiv und dynamisch ist, kann auf jeden Knoten geklickt und die Ansicht anhand der Filter geändert werden:

1. Sie können die Anzeige in der Netzwerkübersicht mithilfe der Filter am oberen Rand ändern. Sie können als Schwerpunkt der Übersicht Folgendes festlegen:
   -  **Sicherheitsintegrität**: Sie können die Übersicht nach Schweregrad (Hoch, Mittel, Niedrig) Ihrer Azure-Ressourcen filtern.
   - **Empfehlungen**: Sie können die Anzeige von Ressourcen basierend auf den Empfehlungen auswählen, die für diese Ressourcen aktiv sind. Sie können beispielsweise nur Ressourcen anzeigen, für die Security Center das Aktivieren von Netzwerksicherheitsgruppen empfiehlt.
   - **Netzwerkzonen**: Standardmäßig werden in der Übersicht nur Ressourcen mit Internetzugriff angezeigt, Sie können jedoch auch interne virtuelle Computer auswählen.
 
2. Sie können jederzeit auf **Zurücksetzen** in der oberen linken Ecke klicken, um die Übersicht in den Standardzustand zurückzusetzen.

So zeigen Sie Detailinformationen zu einer Ressource an:
1. Wenn Sie eine bestimmte Ressource in der Übersicht auswählen, wird der rechte Bereich geöffnet und zeigt allgemeine Informationen zur Ressource, verbundene Sicherheitslösungen (sofern vorhanden) und die für die Ressource relevanten Empfehlungen an. Dieses Verhalten ist für jeden ausgewählten Ressourcentyp gleich. 
2. Wenn Sie auf einen Knoten in der Übersicht zeigen, können Sie allgemeine Informationen zur Ressource sehen, einschließlich Abonnement, Ressourcentyp und Ressourcengruppe.
3. Verwenden Sie den Link, um die QuickInfo zu vergrößern und die Übersicht auf diesen bestimmten Knoten neu zu fokussieren. 
4. Um die Fokussierung der Übersicht auf einen bestimmten Knoten aufzuheben, verkleinern Sie die Anzeige.

### <a name="the-traffic-view"></a>Datenverkehrsansicht

Die Ansicht **Datenverkehr** bietet Ihnen eine Übersicht über den gesamten möglichen Datenverkehr zwischen Ihren Ressourcen. Dadurch erhalten Sie eine visuellen Überblick über alle konfigurierten Regeln, die festlegen, welche Ressourcen mit wem kommunizieren können. Sie können so die vorhandene Konfiguration der Netzwerksicherheitsgruppen sehen und auch schnell mögliche riskante Konfigurationen innerhalb Ihrer Workloads erkennen.

### <a name="uncover-unwanted-connections"></a>Entdecken unerwünschter Verbindungen

Die Stärke dieser Ansicht ist die Fähigkeit, diese zulässigen Verbindungen zusammen mit den bestehenden Sicherheitsrisiken anzuzeigen, sodass Sie diesen Querschnitt der Daten für die erforderliche Verstärkung der Sicherheit Ihrer Ressourcen verwenden können. 

Sie können beispielsweise zwei Computer erkennen, von denen Sie gar nicht wussten, dass diese miteinander kommunizieren können, und so die Workloads und Subnetze besser isolieren.

### <a name="investigate-resources"></a>Untersuchen von Ressourcen

So zeigen Sie Detailinformationen zu einer Ressource an:
1. Wenn Sie eine bestimmte Ressource in der Übersicht auswählen, wird der rechte Bereich geöffnet und zeigt allgemeine Informationen zur Ressource, verbundene Sicherheitslösungen (sofern vorhanden) und die für die Ressource relevanten Empfehlungen an. Dieses Verhalten ist für jeden ausgewählten Ressourcentyp gleich. 
2. Klicken Sie auf **Datenverkehr**, um die Liste des möglichen ausgehenden und eingehenden Datenverkehrs für die Ressource anzuzeigen. Dabei handelt es sich um eine umfassende Liste, wer mit der Ressource kommunizieren kann, mit wem die Ressource kommunizieren kann und über welche Protokolle und Ports die Kommunikation erfolgt. Wenn Sie beispielsweise eine VM auswählen, werden alle VMs angezeigt, mit denen diese kommunizieren kann, und wenn Sie ein Subnetz auswählen, werden alle Subnetze angezeigt, mit denen dieses kommunizieren kann.

**Diese Daten basieren auf einer Analyse der Netzwerksicherheitsgruppen sowie erweiterten Machine Learning-Algorithmen, die mehrere Regeln analysieren, um deren Überkreuzungen und Interaktionen zu verstehen.** 

![Übersicht über den Netzwerkdatenverkehr](./media/security-center-network-recommendations/network-map-traffic.png)

## Legacynetzwerk <a name ="legacy-networking"></a>

Wenn Sie nicht über Security Center im Standard-Tarif verfügen, wird in diesem Abschnitt erläutert, wie Sie kostenlose Netzwerkempfehlungen anzeigen können.

Um auf diese Informationen zuzugreifen, klicken Sie auf dem Blatt „Netzwerk“ auf **Legacynetzwerk anzeigen**. 

![Legacynetzwerk](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Abschnitt „Endpunkte mit Internetanbindung“
Im Abschnitt **Endpunkte mit Internetzugriff** werden die virtuellen Computer, die derzeit mit einem Endpunkt mit Internetzugriff konfiguriert sind, und der jeweilige Status angezeigt.

Diese Tabelle enthält den Endpunktnamen, die Internet-IP-Adresse und den aktuellen Status der Netzwerksicherheitsgruppe und die NGFW-Empfehlungen. Die Tabelle ist nach Schweregrad sortiert.

### <a name="networking-topology-section"></a>Abschnitt „Netzwerktopologie“
Der Abschnitt **Netzwerktopologie** enthält eine hierarchische Ansicht der Ressourcen.

Diese Tabelle ist nach Schweregrad sortiert (virtuelle Computer und Subnetze).

In dieser Topologieansicht werden auf der ersten Ebene VNETs angezeigt. Die zweite Ebene zeigt Subnetze und die dritte Ebene die virtuellen Computer dieser Subnetze. Die rechte Spalte zeigt den aktuellen Status der Empfehlungen zu Netzwerksicherheitsgruppen für diese Ressourcen.

Die dritte Ebene zeigt virtuelle Computer, ähnlich wie in der Beschreibung oben. Sie können auf eine beliebige Ressource klicken, um weitere Informationen zu erhalten oder die erforderliche Sicherheitskontrolle/-konfiguration anzuwenden.

## <a name="network-recommendations"></a>Netzwerkempfehlungen

|Ressourcentyp|Sicherheitsbewertung|Empfehlung|BESCHREIBUNG|
|----|----|----|----|
|Machine|40|Netzwerksicherheitsgruppen für virtuelle Computer sollten aktiviert werden.|Aktivieren Sie Netzwerksicherheitsgruppen zum Steuern des Netzwerkzugriffs für Ihre virtuellen Computer.|
|Subnetz|35|Netzwerksicherheitsgruppen auf Subnetzebene sollten aktiviert werden.|Aktivieren Sie Netzwerksicherheitsgruppen zum Steuern des Netzwerkzugriffs für in Ihren Subnetzen bereitgestellte Ressourcen.|
|Machine|30|Die Just-In-Time-Netzwerkzugriffssteuerung sollte auf virtuelle Computer angewendet werden.|Wenden Sie die JIT-VM-Zugriffssteuerung an, um den Zugriff auf ausgewählte Ports dauerhaft zu sperren und autorisierten Benutzern zu ermöglichen, sie über den gleichen Mechanismus und für eine begrenzte Zeitspanne zu öffnen.|
|Machine|20|Zugriff über Endpunkt mit Internetzugriff einschränken|Stärken Sie die Netzwerksicherheitsgruppen Ihrer VMs mit Internetzugriff, indem Sie den Zugriff auf Ihre bestehenden Zulassungsregeln einschränken.|
|Machine|10|Hinzufügen einer Firewall der nächsten Generation|Fügen Sie eine Lösung für eine Firewall der nächsten Generation (NGFW) hinzu, um Ihre virtuellen Computer mit Internetzugriff besser zu schützen.|
|Machine|5|Datenverkehr nur über die Firewall der Netzwerkgateway leiten|Um die Bereitstellung Ihrer Firewalllösung der nächsten Generation abzuschließen, sollte der Datenverkehr zu Ihren geschützten VMs mit Internetzugriff nur über die Firewalllösung der nächsten Generation geleitet werden.|
|VNet|5|DDoS Protection Standard aktivieren|Anwendungen mit öffentlichen IPs in diesen virtuellen Netzwerken sind nicht mit dem DDOS Protection-Dienststandard geschützt. Es wird empfohlen, diesen zu aktivieren, um die Eindämmung von volumetrischen Netzwerk- und Protokollangriffen zu ermöglichen.|

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Empfehlungen für andere Arten von Azure-Ressourcen finden Sie in den folgenden Themen:

* [Schützen Ihrer virtuellen Computer in Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Schützen Ihrer Anwendungen in Azure Security Center](security-center-application-recommendations.md)
* [Schützen Ihres Azure SQL-Diensts in Azure Security Center](security-center-sql-service-recommendations.md)

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center:](tutorial-security-policy.md) Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center:](security-center-managing-and-responding-alerts.md) Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md) : Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
