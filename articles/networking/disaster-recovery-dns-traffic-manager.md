---
title: Notfallwiederherstellung mit Azure DNS und Traffic Manager | Microsoft-Dokumentation
description: Übersicht über die Notfallwiederherstellungs-Lösungen mit Azure DNS und Traffic Manager
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: a560cc526e73f3ce7e851f2a545f9b16fa53b423
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65501694"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Notfallwiederherstellung mit Azure DNS und Traffic Manager

Die Notfallwiederherstellung konzentriert sich auf die Wiederherstellung nach einem schwerwiegenden Ausfall der Anwendungsfunktionalität. Um sich für eine Notfallwiederherstellungs-Lösung zu entscheiden, müssen die Eigentümer von Unternehmen und Technologien zunächst den Grad der Funktionalität bestimmen, der während eines Notfalls erforderlich ist, z.B. nicht verfügbar, teilweise über reduzierte Funktionalität oder verzögerte Verfügbarkeit oder vollständig verfügbar.
Die meisten Enterprise-Kunden entscheiden sich für eine Architektur für mehrere Regionen, um die Ausfallsicherheit gegenüber einem Failover auf Anwendungs- oder Infrastrukturebene zu gewährleisten. Kunden können verschiedene Ansätze wählen, um Failover und Hochverfügbarkeit über eine redundante Architektur zu erzielen. Im Folgenden finden Sie einige der gängigen Ansätze:

- **Aktiv/Passiv mit Standbymodus „Verzögert betriebsbereit“:** Bei dieser Failoverlösung werden die in einer Standbyregion ausgeführten VMs und andere Appliances erst dann aktiv, wenn ein Failover erforderlich ist. Allerdings wird die Produktionsumgebung in Form von Sicherungen, VM-Images oder Ressourcen Manager-Vorlagen in einer anderen Region repliziert. Dieser Failovermechanismus ist kostengünstig, ein vollständiger Failovervorgang dauert jedoch länger.
 
    ![Aktiv/Passiv mit Standbymodus „Verzögert betriebsbereit“](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Abbildung: Notfallwiederherstellungs-Konfiguration Aktiv/Passiv mit Standbymodus „Verzögert betriebsbereit“*

- **Aktiv/Passiv mit Steuerlicht:** Bei dieser Failoverlösung wird die Standbyumgebung mit einer minimalen Konfiguration eingerichtet. Das Setup enthält nur die notwendigen Dienste, um nur einen minimalen und kritischen Satz von Anwendungen zu unterstützen. In seiner systemeigenen Form kann dieses Szenario nur minimale Funktionalitäten ausführen, aber es kann zusätzliche Dienste skalieren und erstellen, um einen Großteil der Produktionslast zu übernehmen, wenn ein Failover auftritt.
    
    ![Aktiv/Passiv mit Steuerlicht](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Abbildung: Notfallwiederherstellungs-Konfiguration Aktiv/Passiv mit Steuerlicht*

- **Aktiv/Passiv mit Standbymodus „Betriebsbereit“:** Bei dieser Failoverlösung wird die Standbyregion „vorgewärmt“ und kann die Grundlast verarbeiten, die automatische Skalierung ist aktiviert, und alle Instanzen sind betriebsbereit. Diese Lösung kann nicht für die Verarbeitung der vollen Produktionslast skaliert werden, ist aber funktionsfähig, und alle Dienste sind einsatzbereit. Diese Lösung ist eine erweiterte Version des Ansatzes mit Steuerlicht.
    
    ![Aktiv/Passiv mit Standbymodus „Betriebsbereit“](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Abbildung: Notfallwiederherstellungs-Konfiguration Aktiv/Passiv mit Standbymodus „Betriebsbereit“*
    
Weiter Informationen zu Failover und Hochverfügbarkeit finden Sie in [Notfallwiederherstellung für Azure-Anwendungen](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planen Ihrer Notfallwiederherstellungs-Architektur

Es gibt zwei technische Aspekte, die bei der Einrichtung Ihrer Notfallwiederherstellungs-Architektur berücksichtigt werden müssen:
-  Verwenden eines Bereitstellungsmechanismus zum Replizieren von Instanzen, Daten und Konfigurationen zwischen primären und Standbyumgebungen. Diese Art von Notfallwiederherstellung kann systemintern über Azure Site Recovery über Appliances/Dienste von Microsoft Azure-Partnern wie Veritas oder NetApp erfolgen. 
- Entwickeln einer Lösung zum Umleiten von Netzwerkdaten-/Webdatenverkehr vom primären Standort zum Standbystandort. Diese Art von Notfallwiederherstellung kann über Azure DNS, Azure Traffic Manager (DNS) oder globale Lastenausgleichsmodule von Drittanbietern erreicht werden.

Dieser Artikel ist auf Ansätze bezüglich der Weiterleitung von Netzwerkdaten- und Webdatenverkehr beschränkt. Anweisungen zur Einrichtung von Azure Site Recovery finden Sie in der [Dokumentation für Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
DNS ist einer der effizientesten Mechanismen, um den Netzwerkdatenverkehr umzuleiten, da DNS oft global ist und sich außerhalb des Rechenzentrums befindet und von Ausfällen auf regionaler oder Verfügbarkeitszonenebene (AZ) isoliert ist. Es kann ein DNS-basierter Failovermechanismus verwendet werden und in Azure können zwei DNS-Dienste in gewisser Weise dasselbe erreichen – Azure DNS (autoritatives DNS) und Azure Traffic Manager (DNS-basiertes intelligentes Datenverkehrsrouting). 

Zur Besprechung der in diesem Artikel aufgeführten Lösungen müssen Sie einige wenige DNS-Konzepte verstehen, die in großem Umfang verwendet werden:
- **DNS-A-Datensatz:** „A-Datensätze“ sind Zeiger, die eine Domäne auf eine IPv4-Adresse verweisen. 
- **CNAME oder kanonischen Namen** – Dieser Eintragstyp wird zum Verweis auf einen anderen DNS-Eintrag verwendet. CNAME antwortet nicht mit einer IP-Adresse, sondern mit dem Zeiger auf den Eintrag, der die IP-Adresse enthält. 
- **Gewichtetes Routing** – Es ist möglich, eine Gewichtung mit den Dienstendpunkten zu verknüpfen und dann den Datenverkehr auf der Grundlage der zugewiesenen Gewichtung zu verteilen. Diese Routingmethode ist eine der vier Mechanismen zum Datenverkehrsrouting, die in Traffic Manager verfügbar sind. Weitere Informationen finden Sie unter [Gewichtete Methode für das Datenverkehrsrouting](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Prioritätsbasiertes Routing** – Das prioritätsbasierte Routing erfolgt auf Grundlage von Systemdiagnosen der Endpunkte. Standardmäßig sendet Azure Traffic Manager sämtlichen Datenverkehr an den Endpunkt mit der höchsten Priorität, und bei einem Fehler oder Notfall leitet Traffic Manager den Datenverkehr an den sekundären Endpunkt weiter. Weitere Informationen finden Sie unter [Prioritätsbasierte Methode für das Datenverkehrsrouting](../traffic-manager/traffic-manager-routing-methods.md#priority).

## <a name="manual-failover-using-azure-dns"></a>Manuelles Failover mit Azure DNS
Die Azure DNS-Lösung für manuelles Failover für die Notfallwiederherstellung verwendet den Standard-DNS-Mechanismus für ein Failover zum Sicherungsstandort. Die manuelle Option über Azure DNS funktioniert am besten in Kombination mit dem Ansatz Standbymodus „Verzögert betriebsbereit“ oder dem Ansatz mit Steuerlicht. 

![Manuelles Failover mit Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Abbildung – Manuelles Failover mit Azure DNS*

Die Annahmen für die Lösung sind:
- Sowohl primäre als auch sekundäre Endpunkte haben statische IP-Adressen, die sich selten ändern. Für den primären Standort wird z.B. die IP 100.168.124.44 und für den sekundären Standort die IP 100.168.124.43 verwendet.
- Für beide Standorte gibt es ist eine Azure DNS-Zone. Für den primären Standort ist der Endpunkt prod.contoso.com und für den Sicherungsstandort dr.contoso.com. Außerdem gibt es einen DNS-Eintrag für die Hauptanwendung mit der Bezeichnung „www\.contoso.com“.   
- Die TTL entspricht der in der Organisation festgelegten RTO-SLA oder liegt darunter. Wenn ein Unternehmen beispielsweise die RTO für die Reaktion der Anwendung auf einen Notfall auf 60 Minuten setzt, dann sollte die TTL weniger als 60 Minuten betragen, am besten je niedriger, desto besser. 
  Sie können Azure DNS wie folgt für manuelles Failover einrichten:
- Erstellen einer DNS-Zone
- Erstellen von DNS-Zoneneinträgen
- Aktualisieren des CNAME-Eintrags

### <a name="step-1-create-a-dns"></a>Schritt 1: Erstellen eines DNS
Erstellen Sie eine DNS-Zone (z. B. www\.contoso.com), wie unten gezeigt:

![Erstellen einer DNS-Zone in Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Abbildung – Erstellen einer DNS-Zone in Azure*

### <a name="step-2-create-dns-zone-records"></a>Schritt 2: Erstellen von DNS-Zoneneinträgen

Erstellen Sie innerhalb dieser Zone drei Einträge (z. B. www\.contoso.com, prod.contoso.com und dr.consoto.com), wie unten gezeigt.

![Erstellen von DNS-Zoneneinträgen](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Abbildung – Erstellen von DNS-Zoneneinträgen in Azure*

In diesem Szenario hat die Website www\.contoso.com eine TTL von 30 Minuten, die unter der angegebenen RTO liegt, und zeigt auf die Produktionswebsite prod.contoso.com. Diese Konfiguration wird im normalen Geschäftsbetrieb verwendet. Die TTL von prod.contoso.com und dr.contoso.com wurde auf 300 Sekunden oder 5 Minuten festgelegt. Sie können einen Azure-Überwachungsdienst z.B. Azure Monitor oder Azure App Insights oder beliebige Überwachungslösungen von Partnern verwenden, z.B. Dynatrace. Sie können sogar selbst entwickelte Lösungen verwenden, die Ausfälle auf der Ebene von Anwendungen oder der virtuellen Infrastruktur überwachen oder erkennen können.

### <a name="step-3-update-the-cname-record"></a>Schritt 3: Aktualisieren des CNAME-Eintrags

Sobald ein Fehler erkannt wird, ändern Sie den Wert des Eintrags auf dr.contoso.com, wie unten gezeigt:
       
![Aktualisieren des CNAME-Eintrags](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Abbildung – Aktualisieren des CNAME-Eintrags in Azure*

Innerhalb von 30 Minuten, in denen die meisten Resolver die zwischengespeicherten Zonendatei aktualisieren, werden alle Abfragen an www\.contoso.com zu dr.contoso.com umgeleitet.
Sie können auch den folgenden Azure CLI-Befehl ausführen, um den CNAME-Wert zu ändern:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Dieser Schritt kann manuell oder per Automatisierung ausgeführt werden. Dies ist über die Konsole oder anhand von Azure CLI möglich. Es können das Azure SDK und die API verwendet werden, um die CNAME-Aktualisierung zu automatisieren, damit kein manueller Eingriff erforderlich ist. Die Automatisierung kann über Azure-Funktionen oder eine Überwachungsanwendung eines Drittanbieters oder auch lokal erstellt werden.

### <a name="how-manual-failover-works-using-azure-dns"></a>Funktionsweise des manuellen Failover mit Azure DNS
Da sich der DNS-Server außerhalb der Failover- oder Notfallzone befindet, ist er gegen Downtime isoliert. Dies ermöglicht es dem Benutzer, ein einfaches Failoverszenario zu entwerfen, das kosteneffektiv ist und immer funktioniert, vorausgesetzt, dass der Operator während eines Notfalls über eine Netzwerkverbindung verfügt und die Spiegelung durchführen kann. Bei einer Skriptlösung muss sichergestellt werden, dass der Server oder Dienst, auf dem das Skript ausgeführt wird, gegen das Problem isoliert ist, das die Produktionsumgebung betrifft. Beachten Sie außerdem die niedrige TTL, die für die Zone festgelegt wurde, sodass kein Resolver weltweit den Endpunkt lange zwischenspeichert und Kunden innerhalb der RTO auf die Site zugreifen können. Für den Standbymodus „Verzögert betriebsbereit“ oder den Modus mit Steuerlicht sollte aufgrund des Vorwärmens und anderer administrativer Aktivitäten vor dem Spiegeln genug Zeit eingeplant werden.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatisches Failover mit Azure Traffic Manager
Wenn Sie über komplexe Architekturen und mehrere Ressourcensätzen verfügen, die dieselbe Funktion ausführen können, können Sie den Azure Traffic Manager (basierend auf DNS) konfigurieren, um die Integrität Ihrer Ressourcen zu überprüfen und den Datenverkehr von der fehlerhaften zur fehlerfreien Ressource zu leiten. Im folgenden Beispiel verfügen sowohl die primäre als auch die sekundäre Region über eine vollständige Bereitstellung. Diese Bereitstellung umfasst die Clouddienste und eine synchronisierte Datenbank. 

![Automatisches Failover mit Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Abbildung – Automatisches Failover mit Azure Traffic Manager*

Allerdings werden nur in der primären Region aktiv Netzwerkanforderungen der Benutzer verarbeitet. Die sekundäre Region wird nur aktiv, wenn in der primären Region eine Dienstunterbrechung auftritt. In diesem Fall werden alle neuen Netzwerkanforderungen an die sekundäre Region weitergeleitet. Da die Sicherung der Datenbank nahezu sofort erfolgt, beide Lastenausgleichsmodule über IPs verfügen, deren Integrität überprüft werden kann, und auch die Instanzen immer einsatzbereit sind, bietet diese Topologie die Möglichkeit, eine niedrige RTO zu verwenden und ein Failover ohne manuelle Eingriffe durchzuführen. Die sekundäre Failoverregion muss unmittelbar nach einem Ausfall der primären Region einsatzbereit sein.
Dieses Szenario ist ideal für den Einsatz von Azure Traffic Manager, der über eingebaute Tests für verschiedene Arten von Integritätsprüfungen wie http/https und TCP verfügt. Azure Traffic Manager verfügt außerdem über eine Regel-Engine, die so konfiguriert werden kann, dass das Failover bei einem Ausfall wie unten beschrieben erfolgt. Betrachten wir die folgende Lösung mit Traffic Manager:
- Der Kunde hat den Endpunkt Region #1 mit der prod.contoso.com und einer statischen IP 100.168.124.44 und einen Endpunkt Region #2 mit der Bezeichnung dr.contoso.com und einer statischen IP 100.168.124.43. 
-   Jede dieser Umgebungen wird über eine öffentliche Eigenschaft wie z.B. ein Lastenausgleichsmodul nach außen verfügbar gemacht. Das Lastenausgleichsmodul kann mit einem DNS-basierten Endpunkt oder einem vollqualifizierten Domänenname (FQDN) konfiguriert werden, wie unten dargestellt.
-   Alle Instanzen in Region 2 sind nahezu in Echtzeit mit Region 1 repliziert. Darüber hinaus sind die Computerimages auf dem neuesten Stand, und alle Software-/Konfigurationsdaten sind gepatcht und entsprechen Region 1.  
-   Die automatische Skalierung wurde im Voraus vorkonfiguriert. 

Anhand der folgenden Schritte konfigurieren Sie das Failover mit Azure Traffic Manager:
1. Erstellen eines neuen Azure Traffic Manager-Profils
2. Erstellen von Endpunkten innerhalb des Traffic Manager-Profils
3. Einrichten der Integritätsprüfungen und der Failoverkonfiguration

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Schritt 1: Erstellen eines neuen Azure Traffic Manager-Profils
Erstellen Sie ein neues Azure Traffic Manager-Profil mit dem Namen contoso123, und wählen Sie die Routingmethode „Priorität“. Wenn Sie eine bereits existierende Ressourcengruppe haben, die Sie zuordnen möchten, können Sie eine vorhandene Ressourcengruppe auswählen, andernfalls können Sie eine neue Ressourcengruppe anlegen.

![Erstellen eines Traffic Manager-Profils](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Abbildung – Erstellen eines Traffic Manager-Profils*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Schritt 2: Erstellen von Endpunkten innerhalb des Traffic Manager-Profils

In diesem Schritt erstellen Sie Endpunkte, die auf die Produktions- und Notfallwiederherstellungsstandorten verweisen. Wählen Sie hier den **Typ** als externen Endpunkt. Wenn die Ressource allerdings in Azure gehostet wird, können Sie auch **Azure-Endpunkt** auswählen. Wenn Sie **Azure-Endpunkt** auswählen, wählen Sie anschließend eine **Zielressource**, bei der es sich entweder um einen **App Service** oder eine **Öffentliche IP** handelt, die von Azure zugewiesen wird. Die Priorität wird auf **1** festgelegt, da es sich um den primären Dienst für die Region 1 handelt.
Auf ähnliche Weise erstellen Sie auch den Notfallwiederherstellungs-Endpunkt in Traffic Manager.

![Erstellen von Notfallwiederherstellungs-Endpunkten](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Abbildung – Erstellen von Notfallwiederherstellungs-Endpunkten*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Schritt 3: Einrichten der Integritätsprüfungen und der Failoverkonfiguration

In diesem Schritt legen Sie die DNS-TTL auf 10 Sekunden, die von den meisten rekursiven Resolvern mit Internetzugriff berücksichtigt wird. Diese Konfigurationen bedeutet, dass ein DNS-Resolver die Informationen für maximal 10 Sekunden zwischenspeichert. Für die Einstellungen des Endpunktmonitors ist der Pfad aktuell auf / oder Stamm eingestellt, aber Sie können die Endpunkteinstellungen anpassen, um einen Pfad auszuwerten, z. B. prod.contoso.com/index. Das nachfolgende Beispiel ist**https** das Testprotokoll. Sie können jedoch auch **http** oder **tcp** auswählen. Die Wahl des Protokolls hängt von der Endanwendung ab. Das Testintervall ist auf 10 Sekunden festgelegt, sodass schnelle Tests möglich sind, und die Anzahl der Wiederholungen ist auf 3 gesetzt. Folglich führt Traffic Manager ein Failover auf den zweiten Endpunkt durch, wenn in drei aufeinander folgende Intervallen ein Fehler registriert wird. Die folgende Formel definiert die Gesamtzeit für ein automatisches Failover: Zeit für das Failover = Gültigkeitsdauer (TTL) + Wiederholung × Testintervall. In diesem Fall ist der Wert 10 + 3 × 10 = 40 Sekunden (max).
Wenn die Anzahl der Wiederholungen auf 1 sowie die TTL auf 10 Sekunden festgelegt ist, beträgt die Zeit für das Failover 10 + 1 * 10 = 20 Sekunden. Leben Sie die Anzahl der Wiederholungen auf einen Wert von mehr als **1** fest, um die Wahrscheinlichkeit von Failovers aufgrund von falsch positiven Werten oder kleineren Netzwerk-Blips zu vermeiden. 


![Einrichten der Integritätsprüfungen](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Abbildung – Einrichten der Integritätsprüfungen und der Failoverkonfiguration*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Funktionsweise des automatischen Failover mit Azure Traffic Manager

Während eines Notfalls wird der primäre Endpunkt getestet, der Status ändert sich in **Beeinträchtigt** und der Notfallwiederherstellungsstandort bleibt **Online**. Standardmäßig sendet Traffic Manager den gesamten Datenverkehr an den primären Endpunkt (mit der höchsten Priorität). Wenn der primäre Endpunkt als beeinträchtigt angezeigt wird, leitet Traffic Manager den Datenverkehr an den zweiten Endpunkt, solange dieser fehlerfrei ist. Sie haben die Möglichkeit, weitere Endpunkte innerhalb von Traffic Manager zu konfigurieren, die als zusätzliche Failoverendpunkte oder als Lastenausgleichsmodule zur Lastverteilung zwischen den Endpunkten dienen können.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- [Weitere Informationen zu Azure DNS](../dns/dns-overview.md).









