---
title: Verwenden von Lastenausgleichsdiensten in Azure | Microsoft-Dokumentation
description: 'In diesem Tutorial erfahren Sie, wie Sie ein Szenario mithilfe des Lastenausgleichportfolios von Azure erstellen: Traffic Manager, Application Gateway und Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: duau
ms.openlocfilehash: 431eaff9da95063648d3e80acb54be9cc5c25bc5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021689"
---
# <a name="using-load-balancing-services-in-azure"></a>Verwenden von Lastenausgleichsdiensten in Azure

## <a name="introduction"></a>Einführung

Microsoft Azure bietet zahlreiche Dienste für das Verwalten der Verteilung von Netzwerkdatenverkehr und dem Lastenausgleich. Sie können diese Dienste Ihren Anforderungen entsprechend einzeln verwenden oder die Methoden kombinieren, um die optimale Lösung zu erstellen.

In diesem Tutorial definieren wir zunächst einen Anwendungsfall beim Kunden und finden heraus, wie dieser stabiler und leistungsstärker gestaltet werden kann, indem wir das folgende Lastenausgleichportfolio von Azure verwenden: Traffic Manager, Application Gateway und Load Balancer. Anschließend erhalten Sie Schritt-für-Schritt-Anleitungen zum Erstellen einer geografisch redundanten Bereitstellung, die Datenverkehr an mehrere virtuelle Computer verteilt und mit der verschiedene Anforderungstypen verwaltet werden können.

Auf konzeptioneller Ebene spielt jeder dieser Dienste eine wichtige Rolle in der Lastenausgleichshierarchie.

* **Traffic Manager** enthält einen globalen DNS-Lastenausgleich. Der Dienst untersucht eingehende DNS-Anforderungen und antwortet mit einem fehlerfrei funktionierenden Endpunkt. Dies geschieht in Übereinstimmung mit der Routingrichtlinie, die der Kunde ausgewählt hat. Es gibt die folgenden Routingmethoden:
  * Leistungsorientiertes Routing, um den Anforderer mit Blick auf Latenz zum nächstgelegenen Endpunkt zu verweisen.
  * Prioritätsorientiertes Routing, um den gesamten Datenverkehr zu einem Endpunkt zu leiten, mit anderen Endpunkten als Sicherung.
  * Gewichtetes Roundrobin-Routing, das den Datenverkehr basierend auf der Gewichtung verteilt, die jedem Endpunkt zugewiesen ist.
  * Geografiebasiertes Routing zum Verteilen des Datenverkehrs an die Anwendungsendpunkte basierend auf dem geografischen Standort des Benutzers.
  * Subnetzbasiertes Routing zum Verteilen des Datenverkehrs an die Anwendungsendpunkte basierend auf dem Subnetz (IP-Adressbereich) des Benutzers.
  * MultiValue-Routing, mit dem Sie IP-Adressen von mehreren Anwendungsendpunkten in einer einzelnen DNS-Antwort senden können.

  Der Client stellt eine direkte Verbindung mit dem von Traffic Manager zurückgegebenen Endpunkt her. Azure Traffic Manager erkennt, wenn ein Endpunkt fehlerhaft ist, und leitet den Client dann an eine andere fehlerfreie Instanz weiter. Weitere Informationen zu diesem Dienst finden Sie in der [Dokumentation zu Azure Traffic Manager](traffic-manager-overview.md).
* **Application Gateway** verfügt über einen ADC (Application Delivery Controller) als Dienst und bietet damit verschiedene Lastenausgleichsfunktionen auf Ebene 7 für Ihre Anwendung. So wird Kunden die Optimierung der Webfarmproduktivität ermöglicht, indem die CPU-intensive TLS-Terminierung zum Application Gateway verlagert wird. Andere Routingfunktionen für Ebene 7 umfassen Roundrobin-Verteilung des eingehenden Datenverkehrs, cookiebasierte Sitzungsaffinität, Routing auf URL-Pfadbasis, und Möglichkeit zum Hosten von mehreren Websites hinter einem einzelnen Application Gateway. Application Gateway kann als Gateway mit Internetanbindung, rein internes Gateway oder als Kombination dieser beiden Optionen konfiguriert werden. Application Gateway wird vollständig über Azure verwaltet und ist skalierbar und hoch verfügbar. Die Anwendung umfasst viele Diagnose- und Protokollierungsfunktionen zur Verbesserung der Verwaltbarkeit.
* **Load Balancer** ist ein integraler Bestandteil des Azure SDN-Stapels, äußerst leistungsfähig und bietet Lastenausgleichsdienste der Ebene 4 mit niedriger Latenz für alle UDP- und TCP-Protokolle. Der Dienst verwaltet eingehende und ausgehende Verbindungen. Sie können öffentliche und interne Lastenausgleichs-Endpunkte konfigurieren und Regeln definieren, um eingehende Verbindungen zu Back-End-Pool-Zielen mit TCP und HTTP Integritätstest-Optionen zuzuordnen, damit die Dienstverfügbarkeit verwaltet werden kann.

## <a name="scenario"></a>Szenario

In diesem Beispielszenario verwenden wir eine einfache Website, die zwei Inhaltstypen unterstützt: Images und dynamisch dargestellte Websites. Diese Website muss geografisch redundant sein und ihre Benutzer von ihrem nächstgelegenen Standort aus (niedrigste Latenz) mit Daten versorgen. Der Anwendungsentwickler hat entschieden, dass alle URLs, die dem Muster „/images/*“ entsprechen, über einem dedizierten Pool von VMs bereitgestellt werden. Dies unterscheidet sich zum Rest der Webfarm.

Außerdem muss der standardmäßige VM-Pool, der den dynamischen Inhalt bereitstellt, mit einer Back-End-Datenbank kommunizieren, die auf einem Cluster mit hoher Verfügbarkeit gehostet wird. Die gesamte Bereitstellung wird mit Azure Resource Manager eingerichtet.

Das Verwenden von Traffic Manager, Application Gateway und Load Balancer ermöglicht der Website, diese Entwurfsziele zu erreichen:

* **Multi-Georedundanz:** Durch die Verwendung von Traffic Manager wird der Datenverkehr bei Ausfall einer Region nahtlos zur nächsten passenden Region weitergeleitet. Es ist kein Eingreifen durch den Anwendungsbesitzer notwendig.
* **Geringere Latenz:** Da Traffic Manager den Kunden automatisch zur nächstgelegenen Region weiterleitet, ist die Latenz beim Anfordern der Webseiteninhalte geringer.
* **Unabhängige Skalierbarkeit:** Dadurch dass die Workload der Webanwendung nach Inhaltstyp getrennt wird, kann der Anwendungsbesitzer die Anforderungsworkloads unabhängig voneinander skalieren. Durch Application Gateway wird sichergestellt, dass der Verkehr zu den richtigen Pools geleitet wird. Dies hängt von den angegebenen Regeln und dem Zustand der Anwendungsintegrität ab.
* **Interner Lastenausgleich:** Da sich Load Balancer vor dem Hochverfügbarkeitscluster befindet, steht für die Anwendung nur der aktive und fehlerfreie Endpunkt für eine Datenbank zur Verfügung. Darüber hinaus kann ein Datenbankadministrator die Workload durch das clusterweite Verteilen aktiver und passiver Replikate optimieren. Dies geschieht unabhängig von der Front-End-Anwendung. Load Balancer bietet Verbindungen zum Cluster mit hoher Verfügbarkeit und stellt sicher, dass nur fehlerfreie Datenbanken Verbindungsanforderungen erhalten.

Das folgende Diagramm zeigt die Architektur dieses Szenarios:

![Diagramm der Lastenausgleichsarchitektur](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Dieses Beispiel zeigt nur eine von vielen möglichen Konfigurationen, die Azure für die Lastenausgleichsdienste bietet. Traffic Manager, Application Gateway und Load Balancer können kombiniert werden, um alle Ihre Anforderungen an den Lastenausgleich optimal zu erfüllen. Wenn beispielsweise die TLS-Auslagerung oder Verarbeitung der Ebene 7 nicht notwendig ist, kann Load Balancer anstatt Application Gateway verwendet werden.

## <a name="setting-up-the-load-balancing-stack"></a>Einrichten des Lastenausgleichsstapels

### <a name="step-1-create-a-traffic-manager-profile"></a>Schritt 1: Erstellen eines Traffic Manager-Profils

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** > **Netzwerk** > **Traffic Manager-Profil** > **Erstellen**.
2. Geben Sie die folgenden grundlegenden Informationen ein:

   * **Name**: Geben Sie Ihren Traffic Manager-Profilen einen DNS-Präfixnamen.
   * **Routingmethode:** Wählen Sie die Routingmethode für Datenverkehr in der Richtlinie aus. Weitere Informationen zu den Methoden finden Sie unter [Informationen zu Traffic Manager-Routingmethoden für Datenverkehr](traffic-manager-routing-methods.md).
   * **Abonnement**: Wählen Sie das Abonnement mit dem Profil aus.
   * **Ressourcengruppe**: Wählen Sie die Ressourcengruppe aus, die das Profil enthält. Dies kann eine neue oder vorhandene Ressourcengruppe sein.
   * **Ressourcengruppenstandort:** Der Traffic Manager-Dienst ist global und nicht an einen Standort gebunden. Allerdings müssen Sie eine Region für die Gruppe angeben, in der sich die dem Traffic Manager-Profil zugeordneten Metadaten befinden. Dieser Standort hat keine Auswirkung auf die Laufzeitverfügbarkeit des Profils.

3. Klicken Sie zum Generieren des Traffic Manager-Profils auf **Erstellen**.

   ![Blatt „Erstellen“ von Traffic Manager](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Schritt 2: Erstellen des Anwendungsgateways

1. Klicken Sie im Azure-Portal im linken Bereich auf **Ressource erstellen** > **Netzwerk** > **Application Gateway**.
2. Geben Sie die folgenden allgemeinen Informationen zum Anwendungsgateway an:

   * **Name**: Der Name des Anwendungsgateways.
   * **SKU-Größe:** Die Größe des Anwendungsgateways. Sie haben die Wahl zwischen „Klein“, „Mittel“ und „Groß“.
   * **Instanzenanzahl:** Die Anzahl der Instanzen (ein Wert von 2 bis 10).
   * **Ressourcengruppe**: Die Ressourcengruppe, die das Anwendungsgateway enthält. Dies kann eine neue oder vorhandene Ressourcengruppe sein.
   * **Standort**: Die Region für das Anwendungsgateway. Dieser Standort ist mit dem Standort der Ressourcengruppe identisch. Der Standort ist wichtig, da das virtuelle Netzwerk und die öffentliche IP-Adresse den gleichen Standort wie das Gateway aufweisen müssen.
3. Klicken Sie auf **OK**.
4. Definieren Sie das virtuelle Netzwerk, Subnetz, die Front-End-IP-Adresse und Listenerkonfigurationen für das Anwendungsgateway. In diesem Szenario ist die Front-End-IP-Adresse **Öffentlich**, um das spätere Hinzufügen als Endpunkt zum Traffic Manager-Profil zu ermöglichen.
5. Konfigurieren Sie den Listener mit einer der folgenden Optionen:
    * Wenn Sie HTTP verwenden, muss nichts konfiguriert werden. Klicken Sie auf **OK**.
    * Wenn Sie HTTPS verwenden, sind weitere Konfigurationsschritte erforderlich. Siehe [Erstellen eines Anwendungsgateways](../application-gateway/application-gateway-create-gateway-portal.md) beginnend bei Schritt 9. Wenn Sie die Konfiguration abgeschlossen haben, klicken Sie auf **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurieren des URL-Routings für Anwendungsgateways

Ein Anwendungsgateway, das mit einer pfadbasierten Regel konfiguriert wird, benötigt bei Wahl eines Back-End-Pools zusätzlich zur Roundrobin-Verteilung ein Pfadmuster der Anforderungs-URL. In diesem Szenario fügen wir eine pfadbasierte Regel hinzu, um alle URLs mit "/images/\*" zum Imageserverpool zu leiten. Weitere Informationen zum Konfigurieren von auf dem URL-Pfad basierendem Routing für ein Anwendungsgateway finden Sie unter [Erstellen einer pfadbasierten Regel für ein Application Gateway über das Portal](../application-gateway/application-gateway-create-url-route-portal.md).

![Application Gateway: Diagramm der Webebenen](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Wechseln Sie von Ihrer Ressourcengruppe zur Instanz des Anwendungsgateways, das Sie im vorherigen Abschnitt erstellt haben.
2. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools** aus, und klicken Sie dann auf **Hinzufügen**, um die VMs hinzuzufügen, die Sie den Back-End-Pools der Webebene zuordnen möchten.
3. Geben Sie den Namen des Back-End-Pools und alle IP-Adressen der Computer im Pool ein. In diesem Szenario stellen wir eine Verbindung mit zwei Back-End-Serverpools mit virtuellen Computern her.

   ![Application Gateway: „Back-End-Pool hinzufügen“](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Wählen Sie anschließend in den **Einstellungen** des Anwendungsgateways **Regeln** aus, und klicken Sie dann auf die Schaltfläche **Pfadbasiert**, um eine Regel hinzuzufügen.

   ![Application Gateway-Regeln: Schaltfläche „Pfadbasiert“](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Geben Sie die folgenden Informationen an, um die Regel zu konfigurieren.

   Grundlegende Einstellungen:

   + **Name**: Der Anzeigename der Regel, auf die Sie im Portal zugreifen können.
   + **Listener**: Der Listener, der für die Regel verwendet wird.
   + **Standard-Back-End-Pool:** Der Back-End-Pool, der mit der Standardregel verwendet werden soll.
   + **Standard-HTTP-Einstellungen:** Die HTTP-Einstellungen, die mit der Standardregel verwendet werden sollen.

   Pfadbasierte Regeln:

   + **Name**: Der Anzeigename der pfadbasierten Regel.
   + **Pfade**: Die Pfadregel, die für das Weiterleiten von Datenverkehr verwendet wird.
   + **Back-End-Pool:** Der Back-End-Pool, der mit dieser Regel verwendet werden soll.
   + **HTTP-Einstellungen:** Die HTTP-Einstellungen, die mit dieser Regel verwendet werden sollen.

   > [!IMPORTANT]
   > Pfade: Gültige Pfade müssen mit „/“ beginnen. Der Platzhalter „\*“ darf nur am Ende verwendet werden. Gültige Beispiele sind „/xyz“, „/xyz\*“ oder „/xyz/“\*.

   ![Application Gateway-Blatt „Pfadbasierte Regel hinzufügen“](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Schritt 3: Hinzufügen von Anwendungsgateways zu Traffic Manager-Endpunkten

In diesem Szenario wird Traffic Manager mit Anwendungsgateways verbunden (die entsprechend den vorherigen Schritten konfiguriert sind), die sich in verschiedenen Regionen befinden. Da die Anwendungsgateways jetzt konfiguriert sind, ist der nächste Schritt, sie mit Ihrem Traffic Manager-Profil zu verbinden.

1. Öffnen Sie Ihr Traffic Manager-Profil. Durchsuchen Sie hierfür Ihre Ressourcengruppe, oder suchen Sie den Namen des Traffic Manager-Profils in **Alle Ressourcen**.
2. Wählen Sie im linken Bereich **Endpunkte** aus, und klicken Sie dann auf **Hinzufügen**, um einen Endpunkt hinzuzufügen.

   ![Traffic Manager-Endpunkte: Schaltfläche „Hinzufügen“](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Erstellen Sie einen Endpunkt durch Eingabe der folgenden Informationen:

   * **Typ:** Wählen Sie den Typ des Endpunkts für den Lastenausgleich aus. Wählen Sie in diesem Szenario **Azure-Endpunkt**, da wir diesen mit den Anwendungsgatewayinstanzen verbinden, die wir zuvor konfiguriert haben.
   * **Name**: Geben Sie den Namen des Endpunkts ein.
   * **Zielressourcentyp:** Wählen Sie **Öffentliche IP-Adresse** aus. Wählen Sie dann unter **Zielressource** die öffentliche IP des zuvor konfigurierten Anwendungsgateways aus.

   ![Traffic Manager: „Endpunkt hinzufügen“](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Sie können nun Ihre Einrichtung überprüfen, indem Sie mit dem DNS Ihres Traffic Manager-Profils darauf zugreifen (in diesem Beispiel: TrafficManagerScenario.trafficmanager.net). Sie können Anforderungen erneut senden, VMs und Webserver hoch- oder herunterfahren, die in verschiedenen Regionen erstellt wurden, und die Einstellungen für Ihr Traffic Manager-Profil ändern, um Ihre Einrichtung zu testen.

### <a name="step-4-create-a-load-balancer"></a>Schritt 4: Einrichten eines Load Balancers

In diesem Szenario verteilt Load Balancer Verbindungen von der Webebene zu den Datenbanken innerhalb eines Clusters mit hoher Verfügbarkeit.

Wenn Ihr Cluster mit hoher Verfügbarkeit SQL Server AlwaysOn verwendet, finden Sie Schritt-für-Schritt-Anleitungen unter [Konfigurieren von Always On-Verfügbarkeitsgruppenlistenern](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md).

Weitere Informationen zum Konfigurieren eines internen Lastenausgleichs finden Sie unter [Erstellen eines internen Lastenausgleichs über das Azure-Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. Klicken Sie im Azure-Portal im linken Bereich auf **Ressource erstellen** > **Netzwerk** > **Lastenausgleich**.
2. Wählen Sie einen Namen für den Lastenausgleich aus.
3. Legen Sie **Typ** auf **Intern** fest, und wählen Sie das entsprechende virtuelle Netzwerk und Subnetz aus, in dem sich der Lastenausgleich befinden soll.
4. Wählen Sie unter **IP-Adresszuweisung** entweder **Dynamisch** oder **Statisch** aus.
5. Wählen Sie unter **Ressourcengruppe** die Ressourcengruppe für den Lastenausgleich aus.
6. Wählen Sie unter **Standort** die entsprechende Region für den Lastenausgleich aus.
7. Klicken Sie auf **Erstellen**, um den Lastenausgleich zu generieren.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Verbinden einer Back-End-Datenbankebene mit dem Lastenausgleich

1. Suchen Sie in der Ressourcengruppe den Lastenausgleich, den Sie in den vorherigen Schritten erstellt haben.
2. Klicken Sie unter **Einstellungen** auf **Back-End-Pools** und dann auf **Hinzufügen**, um einen neuen Back-End-Pool hinzuzufügen.

   ![Lastenausgleich: „Back-End-Pool hinzufügen“](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Geben Sie einen Namen für den Back-End-Pool ein.
4. Fügen Sie dem Back-End-Pool entweder einzelne VMs oder eine Verfügbarkeitsgruppe hinzu.

#### <a name="configure-a-probe"></a>Konfigurieren eines Tests

1. Wählen Sie in Ihrem Lastenausgleich unter **Einstellungen** den Punkt **Tests** aus, und klicken Sie dann auf **Hinzufügen**, um einen Test hinzuzufügen.

   ![Lastenausgleich: „Test hinzufügen“](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Geben Sie einen Namen für den Test ein.
3. Wählen Sie **Protokoll** für den Test aus. Bei einer Datenbank ist es vermutlich besser, einen TCP-Test statt eines HTTP-Tests durchzuführen. Weitere Informationen zu Lastenausgleichstests finden Sie unter [Grundlegendes zu Lastenausgleichstests](../load-balancer/load-balancer-custom-probe-overview.md).
4. Geben Sie den **Port** Ihrer Datenbank ein, der beim Zugreifen auf den Test verwendet werden soll.
5. Geben Sie unter **Intervall** an, wie häufig die Anwendung getestet werden soll.
6. Geben Sie unter **Fehlerhafter Schwellenwert** die Anzahl von aufeinanderfolgenden fehlgeschlagenen Tests ein, die auftreten muss, damit die Back-End-VM als fehlerhaft betrachtet wird.
7. Klicken Sie auf **OK**, um den Test zu erstellen.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurieren der Lastenausgleichsregeln

1. Wählen Sie unter **Einstellungen** in Ihrem Lastenausgleich **Lastenausgleichsregeln** aus, und klicken Sie auf **Hinzufügen**, um eine Regel zu erstellen.
2. Geben Sie einen **Namen** für die Lastenausgleichsregel ein.
3. Wählen Sie **Front-End-IP-Adresse des Lastenausgleichs**, **Protokoll** und **Port** aus.
4. Geben Sie unter **Back-End-Port** den Port an, der im Back-End-Pool verwendet werden soll.
5. Wählen Sie den **Back-End-Pool** und den **Test** aus, den Sie in den vorherigen Schritten erstellt haben, um die Regel darauf anzuwenden.
6. Wählen Sie unter **Sitzungspersistenz** aus, wie Sitzungen beibehalten werden sollen.
7. Geben Sie unter **Leerlauftimeouts** die Anzahl von Minuten vor einem Leerlauftimeout an.
8. Wählen Sie für **Floating IP** entweder **Deaktiviert** oder **Aktiviert** aus.
9. Klicken Sie auf **OK** , um die Regel zu erstellen.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Schritt 5: Verbinden von virtuellen Computern der Webebene mit Load Balancer

Jetzt konfigurieren wir die IP-Adresse und den Load Balancer-Front-End-Port in den Anwendungen, die auf Ihren VMs der Webebene ausgeführt werden, für alle Datenbankverbindungen. Diese Konfiguration ist spezifisch für die Anwendung, die auf diesen VMs ausgeführt wird. Informationen zum Konfigurieren der IP-Zieladresse und des Ports finden Sie in der Dokumentation der Anwendung. Um die IP-Adresse des Front-Ends zu suchen, navigieren Sie im Azure-Portal unter **Einstellungen für Lastenausgleich** zum Front-End-IP-Pool.

![Load Balancer-Navigationsbereich „Front-End-IP-Adresspool“](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Nächste Schritte

* [Was ist Traffic Manager?](traffic-manager-overview.md)
* [Übersicht über Application Gateway](../application-gateway/application-gateway-introduction.md)
* [Übersicht über Azure Load Balancer](../load-balancer/load-balancer-overview.md)
