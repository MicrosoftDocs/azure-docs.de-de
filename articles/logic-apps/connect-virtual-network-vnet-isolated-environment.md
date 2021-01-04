---
title: Herstellen einer Verbindung mit virtuellen Azure-Netzwerken mithilfe einer Integrationsdienstumgebung
description: Erstellen einer Integrationsdienstumgebung (ISE), die auf virtuelle Azure-Netzwerke (VNETs) von Azure Logic Apps zugreifen kann
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 6c5badf4760bff559fb050278df84c7ad6e703bd
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616942"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Herstellen einer Verbindung mit virtuellen Azure-Netzwerken in Azure Logic Apps mithilfe einer Integrationsdienstumgebung

Für Szenarios, in denen Ihre Logik-Apps und Integrationskonten Zugriff auf ein [virtuelles Azure-Netzwerk](../virtual-network/virtual-networks-overview.md) benötigen, erstellen Sie eine [*Integrationsdienstumgebung* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Eine Integrationsdienstumgebung ist eine dedizierte Umgebung, die dedizierten Speicher und andere Ressourcen verwendet, die vom „globalen“ mehrinstanzenfähigen Logic Apps-Dienst getrennt bleiben. Diese Trennung trägt auch dazu bei, jegliche Auswirkungen anderer Azure-Mandanten auf die Leistung Ihrer Apps zu verringern. Eine ISE stellt Ihnen außerdem Ihre eigenen statischen IP-Adressen bereit. Diese IP-Adressen sind gesondert von den statischen IP-Adressen, die von den Logik-Apps im öffentlichen, mehrinstanzenfähigen Dienst gemeinsam verwendet werden.

Bei der Erstellung einer ISE wird diese ISE von Azure in Ihr virtuelles Azure-Netzwerk *injiziert*, sodass dann der Logic Apps-Dienst in Ihrem virtuellen Netzwerk bereitgestellt wird. Wählen Sie Ihre Integrationsdienstumgebung beim Erstellen einer Logik-App oder eines Integrationskontos als Speicherort aus. Ihre Logik-App bzw. Ihr Integrationskonto kann dann direkt auf Ressourcen wie virtuelle Computer (VMs), Server, Systeme und Dienste in Ihrem virtuellen Netzwerk zugreifen.

![Auswählen der Integrationsdienstumgebung](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Damit Logik-Apps und Integrationskonten in einer ISE zusammenarbeiten können, muss jeweils *dieselbe ISE* als Speicherort genutzt werden.

Eine ISE weist auch höhere Grenzwerte für Laufzeiten, Speicheraufbewahrung, Durchsatz, Zeitlimits für HTTP-Anforderungen und -Antworten, Nachrichtengrößen und benutzerdefinierte Connectoranforderungen auf. Weitere Informationen finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Weitere Informationen zu ISEs finden Sie unter [Zugreifen auf Ressourcen virtueller Azure-Netzwerke über Azure Logic Apps mit Integrationsdienstumgebungen (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

In diesem Artikel wird gezeigt, wie die folgenden Aufgaben mithilfe des Azure-Portals ausgeführt werden:

* Aktivieren Sie den Zugriff für Ihre ISE.
* Erstellen Sie Ihre ISE.
* Fügen Sie Ihrer ISE zusätzliche Kapazität hinzu.

Sie können eine ISE auch erstellen, indem Sie das [Beispiel für die Azure Resource Manager-Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) verwenden, oder indem Sie die Logic Apps-REST-API verwenden, einschließlich der Einrichtung kundenseitig verwalteter Schlüssel:

* [Erstellen einer Integrationsdienstumgebung (Integration Service Environment, ISE) mithilfe der Logic Apps-REST-API](../logic-apps/create-integration-service-environment-rest-api.md)
* [Einrichten von kundenseitig verwalteten Schlüsseln zum Verschlüsseln von ruhenden Daten für Integrationsdienstumgebungen (Integration Service Environment, ISE) in Azure Logic Apps](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Für Logik-Apps, integrierte Trigger, integrierte Aktionen und Connectors, die in Ihrer ISE ausgeführt werden, gilt ein anderer als der nutzungsbasierte Tarif. Weitere Informationen zur Preisgestaltung und Abrechnung für ISEs finden Sie unter [Feststehendes Preismodell](../logic-apps/logic-apps-pricing.md#fixed-pricing). Eine Preisübersicht finden Sie unter [Logic Apps – Preise](../logic-apps/logic-apps-pricing.md).

* Ein [virtuelles Azure-Netzwerk](../virtual-network/virtual-networks-overview.md). Ihr virtuelles Netzwerk muss vier *leere* Subnetze enthalten. Diese sind zum Erstellen und Bereitstellen von Ressourcen in Ihrer ISE erforderlich und werden von den folgenden internen und ausgeblendeten Komponenten verwendet:

  * Logic Apps Compute
  * Interne App Service-Umgebung (Connectors)
  * Internes API Management (Connectors)
  * Interner Redis-Cache für Zwischenspeicherung und Leistung
  
  Die Subnetze können vorab oder bei der ISE-Erstellung erstellt werden. Überprüfen Sie jedoch vor dem Erstellen Ihrer Subnetze die [Subnetzanforderungen](#create-subnet).

  > [!IMPORTANT]
  >
  > Die folgenden IP-Adressräume können von Azure Logic Apps nicht aufgelöst werden und dürfen daher nicht für das virtuelle Netzwerk oder für die Subnetze verwendet werden:<p>
  > 
  > * 0.0.0.0/8
  > * 100.64.0.0/10
  > * 127.0.0.0/8
  > * 168.63.129.16/32
  > * 169.254.169.254/32

  * Stellen Sie sicher, dass Ihr virtuelles Netzwerk [den Zugriff für Ihre ISE ermöglicht](#enable-access), damit Ihre ISE ordnungsgemäß funktioniert und zugänglich ist.

  * Wenn Sie [ExpressRoute](../expressroute/expressroute-introduction.md) und [Tunnelerzwingung](../firewall/forced-tunneling.md) verwenden oder verwenden möchten, müssen Sie [eine Routingtabelle erstellen](../virtual-network/manage-route-table.md). Diese muss die folgende spezifische Route enthalten und mit jedem Subnetz verknüpft werden, das von Ihrer ISE verwendet wird:

    **Name**: <*Routenname*><br>
    **Adresspräfix**: 0.0.0.0/0<br>
    **Nächster Hop:** Internet
    
    Diese spezielle Routingtabelle ist erforderlich, damit Logic Apps-Komponenten mit anderen abhängigen Azure-Diensten wie Azure Storage und Azure SQL-Datenbank kommunizieren können. Weitere Informationen zu dieser Route finden Sie unter [Adresspräfix 0.0.0.0/0](../virtual-network/virtual-networks-udr-overview.md#default-route). Falls Sie keine Tunnelerzwingung mit ExpressRoute verwenden, ist diese spezielle Routingtabelle nicht erforderlich.
    
    ExpressRoute ermöglicht die Erweiterung Ihrer lokalen Netzwerke in die Microsoft-Cloud sowie die Verbindungsherstellung mit Microsoft Cloud Services über eine private, durch den Konnektivitätsanbieter bereitgestellte Verbindung. Bei ExpressRoute handelt es sich um ein virtuelles privates Netzwerk, das Datenverkehr nicht über das öffentliche Internet, sondern über ein privates Netzwerk weiterleitet. Von Ihren Logik-Apps kann eine Verbindung mit lokalen Ressourcen im gleichen virtuellen Netzwerk hergestellt werden, wenn sie eine Verbindung über ExpressRoute oder über ein virtuelles privates Netzwerk herstellen.
   
  * Achten Sie bei Verwendung eines [virtuellen Netzwerkgeräts (Network Virtual Appliance, NVA)](../virtual-network/virtual-networks-udr-overview.md#user-defined) darauf, nicht die TLS/SSL-Terminierung zu aktivieren oder den ausgehenden TLS/SSL-Datenverkehr zu ändern. Achten Sie außerdem darauf, die Überprüfung für Datenverkehr aus dem Subnetz Ihrer ISE nicht zu aktivieren. Weitere Informationen finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md).

  * Wenn Sie benutzerdefinierte DNS-Server für Ihr virtuelles Azure-Netzwerk verwenden möchten, [richten Sie diese Server gemäß dieser Schritte ein](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md), bevor Sie Ihre ISE in Ihrem virtuellen Netzwerk bereitstellen. Weitere Informationen zum Verwalten von DNS-Servereinstellungen finden Sie unter [Erstellen, Ändern oder Löschen eines virtuellen Netzwerks](../virtual-network/manage-virtual-network.md#change-dns-servers).

    > [!NOTE]
    > Wenn Sie Ihren DNS-Server oder die DNS-Servereinstellungen ändern, müssen Sie die ISE neu starten, damit die Änderungen übernommen werden. Weitere Informationen finden Sie unter [Neustarten der ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Aktivieren des Zugriffs für die ISE

Wenn Sie eine ISE mit einem virtuellen Azure-Netzwerk verwenden, besteht ein häufiges Einrichtungsproblem darin, dass ein oder mehrere Ports blockiert sind. Die Connectors, die Sie zum Herstellen von Verbindungen zwischen Ihrer ISE und den Zielsystemen verwenden, können ebenfalls eigene Portanforderungen aufweisen. Wenn Sie z. B. über den FTP-Connector mit einem FTP-System kommunizieren, muss der Port, den Sie auf Ihrem FTP-System verwenden, verfügbar sein, z. B. Port 21 zum Senden von Befehlen.

Um sicherzustellen, dass Ihre ISE zugänglich ist und dass die Logik-Apps in dieser ISE über jedes Subnetz in Ihrem virtuellen Netzwerk kommunizieren können, [öffnen Sie die in dieser Tabelle für jedes Subnetz beschriebenen Ports](#network-ports-for-ise). Wenn erforderliche Ports nicht verfügbar sind, funktioniert Ihre ISE nicht ordnungsgemäß.

* Wenn Sie über mehrere ISE-Instanzen verfügen, die Zugriff auf andere Endpunkte mit IP-Einschränkungen benötigen, stellen Sie eine [Azure Firewall-Instanz](../firewall/overview.md) oder ein [virtuelles Netzwerkgerät](../virtual-network/virtual-networks-overview.md#filter-network-traffic) in Ihrem virtuellen Netzwerk bereit, und leiten Sie ausgehenden Datenverkehr über die Firewall oder das virtuelle Netzwerkgerät weiter. Sie können dann [eine einzelne, ausgehende, öffentliche, statische und vorhersagbare IP-Adresse einrichten](connect-virtual-network-vnet-set-up-single-ip-address.md), die alle ISE-Instanzen in Ihrem virtuellen Netzwerk verwenden können, um mit Zielsystemen zu kommunizieren. Auf diese Weise müssen Sie nicht für jede ISE an den Zielsystemen zusätzliche Firewallzugänge einrichten.

   > [!NOTE]
   > Sie können diesen Ansatz auch für eine einzelne ISE verwenden, wenn in Ihrem Szenario die Anzahl der IP-Adressen eingeschränkt werden muss, die Zugriff benötigen. Wägen Sie ab, ob die zusätzlichen Kosten für die Firewall oder das virtuelle Netzwerkgerät für Ihr Szenario sinnvoll sind. Erfahren Sie mehr über [Azure Firewall-Preise](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Wenn Sie ein neues virtuelles Azure-Netzwerk und Subnetze ohne Einschränkungen erstellt haben, müssen Sie keine [Netzwerksicherheitsgruppen (NSG)](../virtual-network/network-security-groups-overview.md#network-security-groups) in Ihrem virtuellen Netzwerk einrichten, um den Datenverkehr über Subnetze zu steuern.

* *Optional* können Sie für ein vorhandenes virtuelles Netzwerk [Netzwerksicherheitsgruppen (NSGs)](../virtual-network/network-security-groups-overview.md#network-security-groups) einrichten, indem Sie den [Netzwerkdatenverkehr über Subnetze filtern](../virtual-network/tutorial-filter-network-traffic.md). Wenn Sie diese Route verwenden möchten, oder wenn Sie bereits NSGs verwenden, stellen Sie sicher, dass Sie für diese NSGs die [in dieser Tabelle angegebenen Ports öffnen](#network-ports-for-ise).

  Wenn Sie [NSG-Sicherheitsregeln](../virtual-network/network-security-groups-overview.md#security-rules) einrichten, müssen Sie *sowohl* **TCP**- als auch **UDP**-Protokolle verwenden, oder Sie können stattdessen **Beliebig** auswählen, damit Sie nicht für jedes Protokoll separate Regeln erstellen müssen. NSG-Sicherheitsregeln beschreiben die Ports, die Sie für die IP-Adressen öffnen müssen, die Zugriff auf diese Ports benötigen. Stellen Sie sicher, dass alle Firewalls, Router oder andere Elemente, die zwischen diesen Endpunkten vorhanden sind, diese Ports auch für diese IP-Adressen zugänglich halten.

* Wenn Sie erzwungenes Tunneln durch Ihre Firewall einrichten, um internetgebundenen Datenverkehr umzuleiten, lesen Sie die [zusätzlichen Anforderungen für erzwungenes Tunneln](#forced-tunneling).

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Von ihrer ISE verwendete Netzwerkports

In dieser Tabelle werden die Ports beschrieben, die für Ihre ISE erforderlich sind, und der Zweck für diese Ports. Um die Komplexität beim Festlegen von Sicherheitsregeln zu reduzieren, werden in der Tabelle [Diensttags](../virtual-network/service-tags-overview.md) verwendet, die Gruppen von IP-Adresspräfixen für einen bestimmten Azure-Dienst darstellen. Wo vermerkt, verweisen die *interne ISE* und die *externe ISE* auf den [Zugriffsendpunkt, der bei der ISE-Erstellung ausgewählt wird](connect-virtual-network-vnet-isolated-environment.md#create-environment). Weitere Informationen finden Sie unter [Endpunktzugriff](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access).

> [!IMPORTANT]
> Stellen Sie für alle Regeln sicher, dass Sie die Quellports auf `*` festlegen, weil Quellports kurzlebig sind.

#### <a name="inbound-security-rules"></a>Eingangssicherheitsregeln

| Zweck | Quelldiensttag oder IP-Adressen | Quellports | Zieldiensttag oder IP-Adressen | Zielports | Notizen |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Kommunikation zwischen Subnetzen innerhalb des virtuellen Netzwerks | Adressraum für das virtuelle Netzwerk mit ISE-Subnetzen | * | Adressraum für das virtuelle Netzwerk mit ISE-Subnetzen | * | Erforderlich für die Übertragung von Datenverkehr *zwischen* den Subnetzen in Ihrem virtuellen Netzwerk. <p><p>**Wichtig**: Damit Datenverkehr zwischen den *Komponenten* in den einzelnen Subnetzen übertragen werden kann, müssen alle Ports innerhalb der einzelnen Subnetze geöffnet werden. |
| Beide: <p>Kommunikation mit Ihrer Logik-App <p><p>Ausführungsverlauf der Logik-App| Internes ISE: <br>**VirtualNetwork** <p><p>Externes ISE: **Internet** oder siehe **Hinweise** | * | **VirtualNetwork** | 443 | Anstatt das **Internet**-Diensttag zu verwenden, können Sie die Quell-IP-Adresse für diese Elemente angeben: <p><p>– Der Computer oder Dienst, der beliebige Anforderungstrigger oder Webhooks in Ihrer Logik-App aufruft <p>– Der Computer oder Dienst, von dem aus Sie auf den Ausführungsverlauf der Logik-App zugreifen möchten <p><p>**Wichtig**: Wenn Sie diesen Port schließen oder blockieren, werden Aufrufe für Logik-Apps mit Anforderungstriggern oder Webhooks verhindert. Außerdem wird Ihr Zugriff auf Eingaben und Ausgaben für jeden Schritt im Ausführungsverlauf verhindert. Ihr Zugriff auf den Ausführungsverlauf von Logik-Apps wird jedoch nicht verhindert.|
| Logic Apps-Designer: dynamische Eigenschaften | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | Anforderungen werden von den [eingehenden IP-Adressen](../logic-apps/logic-apps-limits-and-config.md#inbound) des Logic Apps-Zugriffsendpunkts für diese Region gesendet. |
| Bereitstellen von Connectors | **AzureConnectors** | * | **VirtualNetwork** | 454 | Erforderlich zum Bereitstellen und Aktualisieren von Connectors. Wenn Sie diesen Port schließen oder blockieren, führt dies zu Fehlern bei ISE-Bereitstellungen, und es können keine Connectorupdates und -fixes durchgeführt werden. |
| Überprüfen der Netzwerkintegrität | **LogicApps** | * | **VirtualNetwork** | 454 | Anforderungen werden von den [eingehenden](../logic-apps/logic-apps-limits-and-config.md#inbound) und [ausgehenden IP-Adressen](../logic-apps/logic-apps-limits-and-config.md#outbound) des Logic Apps-Zugriffsendpunkts für diese Region gesendet. |
| Abhängigkeit von der App Service-Verwaltung | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Kommunikation über Azure Traffic Manager | **AzureTrafficManager** | * | **VirtualNetwork** | Internes ISE: 454 <p><p>Externes ISE: 443 ||
| Beide: <p>Connectorrichtlinienbereitstellung <p>API Management: Verwaltungsendpunkt | **APIManagement** | * | **VirtualNetwork** | 3443 | Für die Bereitstellung der Connectorrichtlinie ist der Portzugriff zum Bereitstellen und Aktualisieren von Connectors erforderlich. Wenn Sie diesen Port schließen oder blockieren, führt dies zu Fehlern bei ISE-Bereitstellungen, und es können keine Connectorupdates und -fixes durchgeführt werden. |
| Zugriff auf Azure Cache for Redis-Instanzen zwischen Rolleninstanzen | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383, siehe außerdem **Hinweise**| Damit die ISE mit Azure Cache for Redis funktioniert, müssen Sie die [Ports für ausgehenden und eingehenden Datenverkehr öffnen, die in den häufig gestellten Fragen zu Azure Cache for Redis beschrieben werden](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

#### <a name="outbound-security-rules"></a>Ausgangssicherheitsregeln

| Zweck | Quelldiensttag oder IP-Adressen | Quellports | Zieldiensttag oder IP-Adressen | Zielports | Notizen |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Kommunikation zwischen Subnetzen innerhalb des virtuellen Netzwerks | Adressraum für das virtuelle Netzwerk mit ISE-Subnetzen | * | Adressraum für das virtuelle Netzwerk mit ISE-Subnetzen | * | Erforderlich für die Übertragung von Datenverkehr *zwischen* den Subnetzen in Ihrem virtuellen Netzwerk. <p><p>**Wichtig**: Damit Datenverkehr zwischen den *Komponenten* in den einzelnen Subnetzen übertragen werden kann, müssen alle Ports innerhalb der einzelnen Subnetze geöffnet werden. |
| Kommunikation von Ihrer Logik-App | **VirtualNetwork** | * | Variiert abhängig vom Ziel | 80, 443 | Das Ziel variiert je nach den Endpunkten für den externen Dienst, mit dem Ihre Logik-App kommunizieren muss. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Azure Storage-Abhängigkeit | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Verbindungsverwaltung | **VirtualNetwork** | * | **AppService** | 443 ||
| Veröffentlichen von Diagnoseprotokollen und Metriken | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL-Abhängigkeiten | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Zum Veröffentlichen des Integritätsstatus in Resource Health erforderlich. |
| Abhängigkeit von Richtlinie zum Anmelden bei Event Hub und Überwachungs-Agent | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Zugriff auf Azure Cache for Redis-Instanzen zwischen Rolleninstanzen | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383, siehe außerdem **Hinweise**| Damit die ISE mit Azure Cache for Redis funktioniert, müssen Sie die [Ports für ausgehenden und eingehenden Datenverkehr öffnen, die in den häufig gestellten Fragen zu Azure Cache for Redis beschrieben werden](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| DNS-Namensauflösung | **VirtualNetwork** | * | IP-Adressen für beliebige benutzerdefinierte DNS-Server (Domain Name System) in Ihrem virtuellen Netzwerk | 53 | Nur erforderlich, wenn Sie benutzerdefinierte DNS-Server in Ihrem virtuellen Netzwerk verwenden |
|||||||

Darüber hinaus müssen Ausgangsregeln für die [App Service-Umgebung (App Service Environment, ASE)](../app-service/environment/intro.md) hinzugefügt werden:

* Bei Verwendung von Azure Firewall muss Ihre Firewall mit dem [FQDN-Tag](../firewall/fqdn-tags.md#current-fqdn-tags) (Fully Qualified Domain Name, vollqualifizierter Domänenname) der App Service-Umgebung (App Service Environment, ASE) eingerichtet werden, um ausgehenden Zugriff auf den Datenverkehr der ASE-Plattform zuzulassen.

* Bei Verwendung einer Azure Firewall-fremden Firewallappliance muss Ihre Firewall mit *allen* unter [Abhängigkeiten](../app-service/environment/firewall-integration.md#dependencies) aufgeführten Regeln eingerichtet werden, die für die App Service-Umgebung erforderlich sind.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Anforderungen für erzwungenes Tunneln

Wenn Sie [erzwungenes Tunneln](../firewall/forced-tunneling.md) durch Ihre Firewall einrichten oder verwenden, müssen Sie zusätzliche externe Abhängigkeiten für Ihre ISE zulassen. Mit erzwungenem Tunneln können Sie internetgebundenen Datenverkehr an einen bestimmten nächsten Hop, z. B. Ihr virtuelles privates Netzwerk (VPN) oder an eine virtuelle Appliance, anstatt ins Internet umleiten, sodass Sie den ausgehenden Datenverkehr im Netzwerk untersuchen und prüfen können.

Normalerweise muss der gesamte ausgehende Abhängigkeitsdatenverkehr der ISE über die VIP (virtuelle IP-Adresse) verlaufen, die für die ISE bereitgestellt wurde. Wenn Sie jedoch den Datenverkehr entweder zu oder von Ihrer ISE ändern, müssen Sie die folgenden ausgehenden Abhängigkeiten von Ihrer Firewall zulassen, indem Sie deren nächsten Hop auf `Internet` festlegen. Wenn Sie Azure Firewall verwenden, befolgen Sie die [Anweisungen zum Einrichten Ihrer Firewall mit Ihrer App Service-Umgebung](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).

Wenn Sie den Zugriff für diese Abhängigkeiten nicht zulassen, tritt ein Fehler bei der ISE-Bereitstellung auf und Ihre bereitgestellte ISE funktioniert nicht mehr:

* [App Service-Umgebung Management-Adressen](../app-service/environment/management-addresses.md)

* [Azure API Management-Adressen](../api-management/api-management-using-with-vnet.md#control-plane-ips)

* [Azure Traffic Manager-Verwaltungsadressen](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)

* [Eingehende und ausgehende Logic Apps-Adressen für die ISE-Region](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)

* [Azure-IP-Adressen für Connectors in der ISE-Region, die sich in dieser Downloaddatei befinden](https://www.microsoft.com/download/details.aspx?id=56519)

* Sie müssen Dienstendpunkte für Azure SQL, Storage, Service Bus und Event Hub aktivieren, da Sie keinen Datenverkehr durch eine Firewall an diese Dienste senden können.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Erstellen Ihrer Integrationsdienstumgebung

1. Geben Sie im Hauptsuchfeld von Azure im [Azure-Portal](https://portal.azure.com) `integration service environments` als Filter ein, und wählen Sie **Integrationsdienstumgebungen** aus.

   ![Suchen und Auswählen von „Integrationsdienstumgebungen“](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Wählen Sie im Bereich **Integrationsdienstumgebungen** den Befehl **Hinzufügen** aus.

   ![Wählen Sie „Hinzufügen“ aus, um eine Integrationsdienstumgebung zu erstellen.](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Geben Sie diese Details für Ihre Umgebung an, und wählen Sie dann **Überprüfen und erstellen** aus, z. B.:

   ![Angeben von Umgebungsdetails](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Eigenschaft | Erforderlich | Wert | Beschreibung |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Name des Azure-Abonnements*> | Das für Ihre Umgebung zu verwendende Azure-Abonnement |
   | **Ressourcengruppe** | Ja | <*Name der Azure-Ressourcengruppe*> | Eine neue oder vorhandene Azure-Ressourcengruppe, in der Sie Ihre Umgebung erstellen möchten. |
   | **Name der Integrationsdienstumgebung** | Ja | <*Umgebungsname*> | Ihr ISE-Name, der nur Buchstaben, Ziffern, Bindestriche (`-`), Unterstriche (`_`) und Punkte (`.`) enthalten kann. |
   | **Location** | Ja | <*Azure-Datencenterregion*> | Die Azure-Datencenterregion, in der Sie Ihre Umgebung bereitstellen. |
   | **SKU** | Ja | **Premium** oder **Developer (keine SLA)** | Die ISE-SKU, die erstellt und verwendet werden soll. Informationen zu den Unterschieden zwischen diesen SKUs finden Sie unter [ISE-SKUs](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Wichtig**: Diese Option ist nur bei der ISE-Erstellung verfügbar und kann später nicht mehr geändert werden. |
   | **Zusätzliche Kapazität** | Premium: <br>Ja <p><p>Developer: <br>Nicht verfügbar | Premium: <br>0 bis 10 <p><p>Developer: <br>Nicht verfügbar | Die Anzahl der für diese ISE-Ressource zu verwendenden zusätzlichen Verarbeitungseinheiten. Weitere Informationen zum Hinzufügen von Kapazität nach dem Erstellen finden Sie im Abschnitt [Hinzufügen von ISE-Kapazität](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Zugriffsendpunkt** | Ja | **Intern** oder **Extern** | Der für Ihre ISE zu verwendende Typ von Zugriffsendpunkten. Diese Endpunkte bestimmen, ob Anforderungs- oder Webhooktrigger für Logik-Apps in Ihrer ISE Aufrufe von außerhalb Ihres virtuellen Netzwerks empfangen können. <p><p>Wenn Sie z. B. die folgenden webhook-basierten Trigger verwenden möchten, stellen Sie sicher, dass Sie **Extern** auswählen: <p><p>– Azure DevOps <br>– Azure Event Grid <br>– Common Data Service <br>– Office 365 <br>– SAP (ISE-Version) <p><p>Ihre Auswahl hat auch Einfluss auf die Methode, mit der Sie Eingaben und Ausgaben im Ausführungsverlauf Ihrer Logik-App anzeigen und darauf zugreifen können. Weitere Informationen finden Sie unter [ISE-Endpunktzugriff](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Wichtig**: Sie können den Zugriffsendpunkt nur während der Erstellung der ISE auswählen und diese Option später nicht mehr ändern. |
   | **Virtuelles Netzwerk** | Ja | <*Azure-virtual-network-name*> | Das virtuelle Azure-Netzwerk, in das Sie Ihre Umgebung einfügen möchten, damit Logik-Apps in dieser Umgebung auf Ihr virtuelles Netzwerk zugreifen können. Wenn Sie nicht über ein Netzwerk verfügen, [erstellen Sie zunächst ein virtuelles Azure-Netzwerk](../virtual-network/quick-create-portal.md). <p><p>**Wichtig**: Sie können diese Einfügung *nur* einmalig durchführen, wenn Sie Ihre ISE erstellen. |
   | **Subnetze** | Ja | <*subnet-resource-list*> | Für eine ISE werden vier *leere* Subnetze benötigt. Diese sind zum Erstellen und Bereitstellen von Ressourcen in Ihrer ISE erforderlich und werden von internen Logic Apps-Komponenten (etwa Connectors und Zwischenspeicherung zur Leistungsverbesserung) verwendet. <p>**Wichtig**: [Überprüfen Sie unbedingt die Subnetzanforderungen, bevor Sie mit diesen Schritten zum Erstellen Ihrer Subnetze fortfahren.](#create-subnet) |
   |||||

   <a name="create-subnet"></a>

   **Erstellen von Subnetzen**

   Ihre ISE benötigt vier *leere* Subnetze, die zum Erstellen und Bereitstellen von Ressourcen in Ihrer ISE erforderlich sind und von internen Logic Apps-Komponenten (etwa Connectors und Zwischenspeicherung zur Leistungsverbesserung) verwendet werden. Sie können diese Subnetzadressen *nicht mehr ändern*, nachdem Sie Ihre Umgebung erstellt haben. Stellen Sie bei der Erstellung und Bereitstellung Ihrer ISE über das Azure-Portal sicher, dass Sie diese Subnetze nicht an Azure-Dienste delegieren. Wenn Sie die ISE jedoch über die REST-API, über Azure PowerShell oder eine Azure Resource Manager-Vorlage erstellen und bereitstellen, müssen Sie ein leeres Subnetz an `Microsoft.integrationServiceEnvironment` [delegieren](../virtual-network/manage-subnet-delegation.md). Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen einer Subnetzdelegierung](../virtual-network/manage-subnet-delegation.md).

   Jedes Subnetz muss folgende Anforderungen erfüllen:

   * Verwenden Sie einen Namen, der mit einem alphabetischen Zeichen oder mit einem Unterstrich beginnt (keine Zahlen) und keines der folgenden Zeichen enthält: `<`, `>`, `%`, `&`, `\\`, `?`, `/`.

   * Das Format [Classless Inter-Domain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) und einen Class B-Adressraum
   
     > [!IMPORTANT]
     >
     > Die folgenden IP-Adressräume können von Azure Logic Apps nicht aufgelöst werden und dürfen daher nicht für das virtuelle Netzwerk oder für die Subnetze verwendet werden:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * Im Adressbereich wird `/27` verwenden, da jedes Subnetz 32 Adressen benötigt. `10.0.0.0/27` hat z. B. 32 Adressen, da 2<sup>(32 – 27)</sup> 2<sup>5</sup> oder 32 ist. Zusätzliche Adressen haben keine weiteren Vorteile. Weitere Informationen zum Berechnen der Adressen finden Sie unter [Übersicht für IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Wenn Sie [ExpressRoute](../expressroute/expressroute-introduction.md) verwenden, müssen Sie [eine Routentabelle](../virtual-network/manage-route-table.md) erstellen, die die folgende Route enthält, und diese Tabelle mit jedem von Ihrer ISE verwendeten Subnetz verknüpfen:

     **Name**: <*Routenname*><br>
     **Adresspräfix**: 0.0.0.0/0<br>
     **Nächster Hop:** Internet

   1. Wählen Sie unter der Liste **Subnetze** den Eintrag **Subnetzkonfiguration verwalten** aus.

      ![Verwalten der Subnetzkonfiguration](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Wählen Sie im Bereich **Subnetze** den Eintrag **Subnetz** aus.

      ![Hinzufügen von vier leeren Subnetzen](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Geben Sie im Bereich **Subnetz hinzufügen** diese Informationen an.

      * **Name**: Der Name für Ihr Subnetz.
      * **Adressbereich (CIDR-Block)** : Der Bereich Ihres Subnetzes in Ihrem virtuellen Netzwerk und im CIDR-Format

      ![Hinzufügen von Subnetzdetails](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Wenn Sie fertig sind, wählen Sie **OK**.

   1. Wiederholen Sie diese Schritte für drei weitere Subnetze.

      > [!NOTE]
      > Wenn die Subnetze, die Sie zu erstellen versuchen, nicht gültig sind, wird im Azure-Portal eine Meldung angezeigt, der Fortschritt aber nicht behindert.

   Weitere Informationen zum Erstellen von Subnetzen finden Sie unter [Hinzufügen von Subnetzen virtueller Netzwerke](../virtual-network/virtual-network-manage-subnet.md).

1. Nachdem Azure Ihre ISE-Informationen erfolgreich überprüft hat, wählen Sie **Erstellen** aus, z. B.:

   ![Wählen Sie nach der erfolgreicher Überprüfung „Erstellen“ aus.](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure beginnt mit der Bereitstellung Ihrer Umgebung, die in der Regel innerhalb von zwei Stunden abgeschlossen wird. Gelegentlich kann die Bereitstellung bis zu vier Stunden dauern. Um den Bereitstellungsstatus zu überprüfen, wählen Sie in Ihrer Azure-Symbolleiste das Symbol „Benachrichtigungen“ aus, wodurch der Benachrichtigungsbereich geöffnet wird.

   ![Überprüfen des Bereitstellungsstatus](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Wenn die Bereitstellung erfolgreich abgeschlossen wurde, zeigt Azure diese Benachrichtigung an:

   ![Bereitstellung erfolgreich](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Beachten Sie andernfalls die Anweisungen auf dem Azure-Portal zur Problembehandlung bei der Bereitstellung.

   > [!NOTE]
   > Wenn die Bereitstellung nicht erfolgreich war oder Sie Ihre ISE löschen, kann es bis zu einer Stunde (in seltenen Fällen auch länger) dauern, bis Ihre Subnetze freigegeben werden. Daher müssen Sie möglicherweise eine Weile warten, bevor Sie diese Subnetze in einer anderen ISE wiederverwenden können.
   >
   > Wenn Sie Ihr virtuelles Netzwerk löschen, dauert es in der Regel bis zu zwei Stunden, bis Azure Ihre Subnetze freigibt, dieser Vorgang kann aber auch länger dauern. 
   > Stellen Sie beim Löschen virtueller Netzwerke sicher, dass keine Ressourcen mehr verbunden sind. 
   > Beachten Sie hierzu die Anleitung [Löschen eines virtuellen Netzwerks](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Um Ihre Umgebung anzuzeigen, wählen Sie **Zu Ressource wechseln** aus, wenn Azure nach Abschluss der Bereitstellung nicht automatisch zu Ihrer Umgebung wechselt.

1. Für eine ISE, die Zugriff auf einen *externen* Endpunkt hat, müssen Sie eine Netzwerksicherheitsgruppe erstellen, wenn Sie noch keine besitzen, und eine Eingangssicherheitsregel hinzufügen, um Datenverkehr von IP-Adressen für ausgehenden Datenverkehr des verwalteten Connectors zuzulassen. Eine Regel kann wie folgt eingerichtet werden:

   1. Wählen Sie im ISE-Menü unter **Einstellungen** die Option **Eigenschaften** aus.

   1. Kopieren Sie unter **Ausgehende IP-Adressen des Connectors** die öffentlichen IP-Adressbereiche, die auch im Artikel [IP-Adressen für die ausgehende Richtung](../logic-apps/logic-apps-limits-and-config.md#outbound) aufgeführt werden.

   1. Erstellen Sie eine Netzwerksicherheitsgruppe, falls noch keine vorhanden ist.
   
   1. Fügen Sie basierend auf den folgenden Informationen eine Eingangssicherheitsregel für die öffentlichen IP-Adressen für ausgehenden Datenverkehr hinzu, die Sie kopiert haben. Weitere Informationen finden Sie im [Tutorial: Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe über das Azure-Portal](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group).

      | Zweck | Quelldiensttag oder IP-Adressen | Quellports | Zieldiensttag oder IP-Adressen | Zielports | Notizen |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Zulassen von Datenverkehr von ausgehenden IP-Adressen des Connectors | <*connector-public-outbound-IP-addresses*> | * | Adressraum für das virtuelle Netzwerk mit ISE-Subnetzen | * | |
      |||||||

1. Informationen zum Überprüfen der Netzwerkintegrität für Ihre ISE finden Sie unter [Verwalten Ihrer Integrationsdienstumgebung](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

   > [!CAUTION]
   > Wenn das Netzwerk Ihrer ISE fehlerhaft ist, kann die interne ASE (App Service-Umgebung), die von Ihrer ISE verwendet wird, ebenfalls fehlerhaft sein. Wenn die ASE länger als sieben Tage fehlerhaft ist, wird die ASE gesperrt. Überprüfen Sie das Setup Ihres virtuellen Netzwerks, um diesen Zustand aufzulösen. Beheben Sie alle Probleme, die Sie finden, und starten Sie Ihre ISE dann neu. Andernfalls wird die gesperrte ASE nach 90 Tagen gelöscht, und Ihre ISE wird unbrauchbar. Stellen Sie also sicher, dass Sie Ihre ISE fehlerfrei halten, um den erforderlichen Datenverkehr zuzulassen.
   > 
   > Weitere Informationen finden Sie in den folgenden Themen:
   >
   > * [Übersicht: Azure App Service-Diagnose](../app-service/overview-diagnostics.md)
   > * [Nachrichtenprotokollierung für eine Azure App Service-Umgebung](../app-service/environment/using-an-ase.md#logging)

1. Informationen zum Erstellen von Logik-Apps und anderen Artefakten in Ihrer ISE finden Sie unter [Hinzufügen von Ressourcen zu Ihrer Integrationsdienstumgebung (Integration Service Environment, ISE) in Azure Logic Apps](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Nachdem Sie Ihre ISE erstellt haben, können Sie verwaltete ISE-Connectors verwenden. Diese werden jedoch nicht automatisch in der Connectorauswahl des Logik-App-Designers angezeigt. Bevor Sie diese ISE-Connectors verwenden können, müssen Sie diese Connectors [manuell Ihrer ISE hinzufügen und bereitstellen](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment), damit diese im Logik-App-Designer angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen von Ressourcen zu Ihrer Integrationsdienstumgebung (Integration Service Environment, ISE) in Azure Logic Apps](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Verwalten von Integrationsdienstumgebungen](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Erfahren Sie mehr über [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Erfahren Sie mehr über die [Integration virtueller Netzwerke für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md).
