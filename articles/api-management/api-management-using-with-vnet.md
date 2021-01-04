---
title: Verwenden von Azure API Management mit virtuellen Netzwerken
description: Erfahren Sie, wie Sie eine Verbindung zu einem virtuellen Netzwerk in Azure API Management einrichten, und dadurch auf Webdienste zugreifen.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/22/2020
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 7af15552a489f36d87204bfefe47e579cc19f6dc
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778768"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Verwenden von Azure API Management mit virtuellen Netzwerken
Mit Azure Virtual Networks (VNets) können Sie alle Ihre Azure-Ressourcen in einem Netzwerk platzieren, das nicht über das Internet geroutet werden kann, und zu dem Sie den Zugang kontrollieren. Diese Netzwerke können dann durch verschiedene VPN-Technologien mit Ihren lokalen Netzwerken verbunden werden. Beginnen Sie mit dem folgenden Thema, um weitere Informationen zu Azure Virtual Networks zu erhalten: [Übersicht über Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Azure API Management kann im virtuellen Netzwerk (VNET) bereitgestellt werden, damit der Dienst auf Back-End-Dienste im Netzwerk zugreifen kann. Das Entwicklerportal und das API-Gateway können so konfiguriert werden, dass darauf entweder über das Internet oder nur vom virtuellen Netzwerk aus zugegriffen werden kann.

> [!NOTE]
> Die URL des API-Importdokuments muss unter einer öffentlich zugänglichen Internetadresse gehostet werden.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der in diesem Artikel beschriebenen Schritte benötigen Sie Folgendes:

+ Ein aktives Azure-Abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Eine APIM-Instanz. Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>VNet-Verbindung Aktivieren

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Aktivieren der VNET-Konnektivität über das Azure-Portal

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre API Management-Instanz zu suchen. Suchen Sie die **API Management-Dienste**, und wählen Sie sie aus.

2. Wählen Sie Ihre API Management-Instanz aus.

3. Wählen Sie **Virtuelles Netzwerk** aus.
4. Konfigurieren Sie die API Management-Instanz so, dass sie in einem virtuellen Netzwerk bereitgestellt wird.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Auswählen eines virtuellen Netzwerks im Azure-Portal":::
    
5. Wählen Sie den gewünschten Zugriffstyp aus:

    * **Off**: Dies ist die Standardoption. API Management wird in einem virtuellen Netzwerk nicht bereitgestellt.

    * **Extern:** Auf das API Management-Gateway und Entwicklerportal kann vom öffentlichen Internet über einen externen Lastenausgleich zugegriffen werden. Das Gateway kann auf Ressourcen innerhalb des öffentlichen Netzwerks zugreifen.

        ![Öffentliches Peering][api-management-vnet-public]

    * **Intern:** Auf das API Management-Gateway und Entwicklerportal kann nur aus dem virtuellen Netzwerk heraus über einen internen Lastenausgleich zugegriffen werden. Das Gateway kann auf Ressourcen innerhalb des öffentlichen Netzwerks zugreifen.

        ![Privates Peering][api-management-vnet-private]

6. Wenn Sie **Extern** oder **Intern** ausgewählt haben, wird eine Liste aller Regionen angezeigt, in denen Ihr API Management-Dienst bereitgestellt wird. Wählen Sie einen **Standort** aus, und wählen Sie dann sein **Virtuelles Netzwerk** und **Subnetz** aus. Die Liste der virtuellen Netzwerke enthält sowohl klassische als auch virtuelle Ressourcen-Manager-Netzwerke, die in Ihrem Azure-Abonnement für die Region zur Verfügung stehen, die Sie konfigurieren.

    > [!IMPORTANT]
    > Beim Bereitstellen einer Azure API Management-Instanz in einem Ressourcen-Manager-VNet muss sich der Dienst in einem dedizierten Subnetz befinden, das außer den Azure API Management-Instanzen keine anderen Ressourcen enthält. Wenn versucht wird, eine Azure API Management-Instanz in einem Subnetz eines Ressourcen-Manager-VNet bereitzustellen, das andere Ressourcen enthält, misslingt die Bereitstellung.

    Wählen Sie anschließend **Anwenden** aus. Die Seite **Virtuelles Netzwerk** Ihrer API Management-Instanz wird mit Ihren neuen Optionen für das virtuelle Netzwerk und Subnetz aktualisiert.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="Einstellungen für das virtuelle Netzwerk im Portal":::

7. Wählen Sie in der oberen Navigationsleiste **Speichern** aus und dann **Netzwerkkonfiguration anwenden**.

> [!NOTE]
> Die VIP-Adresse der API Management-Instanz ändert sich bei jeder Aktivierung oder Deaktivierung des VNet.
> Die VIP-Adresse ändert sich auch, wenn API Management von **Extern** in **Intern** geändert wird, oder umgekehrt.
>

> [!IMPORTANT]
> Wenn Sie den API Management-Dienst aus einem VNET entfernen oder das VNET ändern, in dem er bereitgestellt wird, kann das zuvor verwendete VNET bis zu sechs Stunden gesperrt bleiben. Während dieses Zeitraums ist es nicht möglich, das VNET zu löschen oder in ihm eine neue Ressource bereitzustellen. Dieses Verhalten gilt für Clients, die api-version 2018-01-01 oder früher verwenden. Bei Clients, die api-version 2019-01-01 oder höher verwenden, wird das VNET freigegeben, sobald der zugehörige API Management-Dienst gelöscht wird.

## <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>Bereitstellen von API Management in einem externen VNET

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* **Erstellen eines API Management-Diensts in einem VNET**: Verwenden Sie das Cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) zum Erstellen eines Azure API Management-Diensts in einem VNET.

* **Bereitstellen eines vorhandenen API Management-Diensts in einem VNET**: Verwenden Sie das Cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion), um einen vorhandenen Azure API Management-Dienst innerhalb eines virtuellen Netzwerks zu verschieben.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Herstellen einer Verbindung mit einem innerhalb eines virtuellen Netzwerk gehosteten Webdienst
Nachdem Ihr API Management-Dienst mit dem VNet verbunden wurde, unterscheidet sich der Zugriff auf Back-Ende-Dienste innerhalb dieses Netzwerks nicht vom Zugriff auf öffentliche Dienste. Geben Sie einfach die lokale IP-Adresse oder den Hostnamen Ihres Webdiensts (falls der DNS-Server für das VNet konfiguriert wurde) im Feld **Webdienst-URL** ein, wenn Sie eine neue API erstellen oder eine vorhandene API bearbeiten.

![API über VPN hinzufügen][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Allgemeine Probleme mit der Netzwerkkonfiguration
Es folgt eine Liste gängiger Konfigurationsprobleme, die beim Bereitstellen des API Management-Diensts in einem virtuellen Netzwerk auftreten können.

* **Setup eines benutzerdefinierten DNS-Servers**: Der API Management-Dienst hängt von mehreren Azure-Diensten ab. Wenn API Management in einem VNet mit einem benutzerdefiniertem DNS-Server gehostet wird, muss es die Hostnamen dieser Azure-Dienste auflösen können. Orientieren Sie sich beim benutzerdefinierten DNS-Setup an [diesen](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) Anweisungen. Sehen Sie sich zur Bezugnahme die nachstehende Tabelle mit den Ports und anderen Netzwerkanforderungen an.

> [!IMPORTANT]
> Wenn Sie benutzerdefinierte DNS-Server für das VNET verwenden möchten, muss dieser **vor** dem Bereitstellen eines API Management-Diensts eingerichtet werden. Andernfalls müssen Sie den API Management-Dienst jedes Mal aktualisieren, wenn Sie die DNS-Server durch Ausführen der [Operation „Netzwerkkonfiguration anwenden“](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates) ändern.

* **Für API Management erforderliche Ports**: Ein- und ausgehender Datenverkehr im Subnetz, in dem API Management bereitgestellt ist, kann mithilfe einer [Netzwerksicherheitsgruppe][Network Security Group] gesteuert werden. Wenn diese Ports nicht verfügbar sind, funktioniert API Management möglicherweise nicht ordnungsgemäß und kann möglicherweise nicht mehr aufgerufen werden. Eine bestehende Sperre für mindestens einen dieser Ports ist eine weitere gängige Fehlkonfiguration, die beim Verwenden von API Management in einem VNet auftritt.

<a name="required-ports"> </a> Beim Hosten einer API Management-Dienstinstanz in einem VNET werden die in der folgenden Tabelle angegebenen Ports verwendet.

| Quell-/Zielport(s) | Direction          | Transportprotokoll |   [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags) <br> Quelle/Ziel   | Zweck (\*)                                                 | Typ des virtuellen Netzwerks |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Eingehend            | TCP                | INTERNET/VIRTUAL_NETWORK            | Kommunikation zwischen Clients und API Management                      | Extern             |
| */3443                     | Eingehend            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Verwaltungsendpunkt für Azure-Portal und PowerShell         | Extern & Intern  |
| * / 443                  | Ausgehend           | TCP                | VIRTUAL_NETWORK/Storage             | **Abhängigkeit von Azure Storage**                             | Extern & Intern  |
| * / 443                  | Ausgehend           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) (falls zutreffend)                   | Extern & Intern  |
| * / 1433                     | Ausgehend           | TCP                | VIRTUAL_NETWORK/SQL                 | **Zugriff auf Azure SQL-Endpunkte**                           | Extern & Intern  |
| * / 5671, 5672, 443          | Ausgehend           | TCP                | VIRTUAL_NETWORK/EventHub            | Abhängigkeit für [Richtlinie zum Anmelden bei Event Hub](api-management-howto-log-event-hubs.md) und Überwachungs-Agent | Extern & Intern  |
| */445                      | Ausgehend           | TCP                | VIRTUAL_NETWORK/Storage             | Abhängigkeit von Azure-Dateifreigabe für [GIT](api-management-configuration-repository-git.md)                      | Extern & Intern  |
| * / 443, 12000                     | Ausgehend           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Erweiterung für Integrität und Überwachung         | Extern & Intern  |
| * / 1886, 443                     | Ausgehend           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Veröffentlichen von [Diagnoseprotokollen und Metriken](api-management-howto-use-azure-monitor.md), [Ressourcenintegrität](../service-health/resource-health-overview.md) und [Application Insights](api-management-howto-app-insights.md)                   | Extern & Intern  |
| * / 25, 587, 25028                       | Ausgehend           | TCP                | VIRTUAL_NETWORK/INTERNET            | Verbinden mit SMTP-Relay zum Senden von E-Mails                    | Extern & Intern  |
| * / 6381 - 6383              | Ein- und ausgehend | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Zugreifen auf den Redis-Dienst für [Cache](api-management-caching-policies.md)richtlinien zwischen Computern         | Extern & Intern  |
| * / 4290              | Ein- und ausgehend | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Synchronisieren von Zählern für [Ratenbegrenzung](api-management-access-restriction-policies.md#LimitCallRateByKey)srichtlinien zwischen Computern         | Extern & Intern  |
| * / *                        | Eingehend            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Lastenausgleich von Azure-Infrastruktur                          | Extern & Intern  |

>[!IMPORTANT]
> Die Ports, deren *Zweck* **fett** formatiert ist, sind zur erfolgreichen Bereitstellung des API Management-Diensts erforderlich. Die Blockierung der anderen Ports **beeinträchtigt** jedoch die Möglichkeit, den **ausgeführten Dienst zu verwenden und zu überwachen sowie die verbindliche SLA bereitzustellen**.

+ **TLS-Funktionalität**: Zum Aktivieren der Erstellung und Prüfung von TLS/SSL-Vertrauensketten benötigt der API Management-Dienst eine ausgehende Netzwerkverbindung zu ocsp.msocsp.com, mscrl.microsoft.com und crl.microsoft.com. Diese Abhängigkeit ist nicht erforderlich, wenn Zertifikate, die Sie in API Management hochladen, die vollständige Kette zum Stamm der Zertifizierungsstelle enthalten.

+ **DNS-Zugriff**: Ausgehender Zugriff über Port 53 wird für die Kommunikation mit DNS-Servern benötigt. Wenn ein benutzerdefinierter DNS-Server am anderen Ende eines VPN-Gateways vorhanden ist, muss der DNS-Server über das Subnetz, in der sich API Management befindet, erreichbar sein.

+ **Metriken und Systemüberwachung**: Ausgehende Netzwerkverbindung zu Azure Monitoring-Endpunkten, die unter folgenden Domänen aufgelöst werden. Wie in der Tabelle gezeigt, werden diese URLs unter dem AzureMonitor-Diensttag zur Verwendung mit Netzwerksicherheitsgruppen angezeigt.

    | Azure-Umgebung | Endpunkte                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure – Öffentlich      | <ul><li>gcs.prod.monitoring.core.windows.net (**neu**)</li><li>prod.warmpath.msftcloudes.com (**als veraltet markiert**)</li><li>global.prod.microsoftmetrics.com(**neu**)</li><li>global.metrics.nsatc.net(**wird eingestellt**)</li><li>shoebox2.prod.microsoftmetrics.com(**neu**)</li><li>shoebox2.metrics.nsatc.net(**wird eingestellt**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**neu**)</li><li>prod3.metrics.nsatc.net(**wird eingestellt**)</li><li>prod3-black.prod.microsoftmetrics.com(**neu**)</li><li>prod3-black.prod3.metrics.nsatc.net(**wird eingestellt**)</li><li>prod3-red.prod.microsoftmetrics.com(**neu**)</li><li>prod3-red.prod3.metrics.nsatc.net(**wird eingestellt**)</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com(**neu**)</li><li>global.metrics.nsatc.net(**wird eingestellt**)</li><li>shoebox2.prod.microsoftmetrics.com(**neu**)</li><li>shoebox2.metrics.nsatc.net(**wird eingestellt**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**neu**)</li><li>prod3.metrics.nsatc.net(**wird eingestellt**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com(**neu**)</li><li>global.metrics.nsatc.net(**wird eingestellt**)</li><li>shoebox2.prod.microsoftmetrics.com(**neu**)</li><li>shoebox2.metrics.nsatc.net(**wird eingestellt**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**neu**)</li><li>prod3.metrics.nsatc.net(**wird eingestellt**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > Bei der Änderung der oben genannten Cluster mit der DNS-Zone **.nsatc.net** in **.microsoftmetrics.com** handelt es sich größtenteils um eine DNS-Änderung. Die IP-Adresse des Clusters ändert sich nicht.

+ **Regionale Diensttags**: NSG-Regeln, die ausgehende Verbindungen mit den Diensttags von Storage, SQL und Event Hubs zulassen, können die regionalen Versionen der Tags verwenden, die der Region entsprechen, die die API Management-Instanz enthält (z. B. Storage.WestUS für eine API Management-Instanz in der Region „USA, Westen“). In Bereitstellungen mit mehreren Regionen sollte die NSG in den einzelnen Regionen Datenverkehr zu den Diensttags für diese Region und die primäre Region zulassen.

+ **SMTP-Relay**: Ausgehende Netzwerkverbindungen für das SMTP-Relay. Die Auflösung erfolgt unter den Hosts `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` und `ies.global.microsoft.com`.

+ **CAPTCHA des Entwicklerportals:** Ausgehende Netzwerkverbindungen für das CAPTCHA des Entwicklerportals. Die Auflösung erfolgt unter den Hosts `client.hip.live.com` und `partner.hip.live.com`.

+ **Diagnose im Azure-Portal**: Wenn Sie bei Verwendung der API Management-Erweiterung in einem virtuellen Netzwerk die Übermittlung von Diagnoseprotokollen aus dem Azure-Portal ermöglichen möchten, wird ausgehender Zugriff auf `dc.services.visualstudio.com` am Port 443 benötigt. Dies ermöglicht die Behandlung von Problemen, die bei der Verwendung von Erweiterungen auftreten können.

+ **Azure Load Balancer**: Das Zulassen einer eingehenden Anforderung vom Diensttag `AZURE_LOAD_BALANCER` ist keine Voraussetzung für die SKU `Developer`, da wir nur eine Computeeinheit dahinter bereitstellen. Eingehend von [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) wird jedoch kritisch, wenn die Skalierung auf eine höhere SKU wie `Premium` (als Fehler beim Integritätstest von Load Balancer) zu einem Fehler bei der Bereitstellung führt.

+ **Application Insights**: Wenn die [Azure Application Insights](api-management-howto-app-insights.md)-Überwachung für API Management aktiviert ist, müssen wir die ausgehende Konnektivität mit dem [Telemetrieendpunkt](../azure-monitor/app/ip-addresses.md#outgoing-ports) aus dem virtuellen Netzwerk zulassen. 

+ **Tunnelerzwingung für Datenverkehr zur lokalen Firewall per Express Route oder virtuellem Netzwerkgerät**: Eine häufige Kundenkonfiguration ist das Definieren einer eigenen Standardroute (0.0.0.0/0). Der gesamte Datenverkehr aus dem per API Management delegierten Subnetz fließt dann über eine lokale Firewall oder an ein virtuelles Netzwerkgerät. Bei diesem Datenverkehr funktioniert die Verbindung mit Azure API Management nicht mehr, da ausgehender Datenverkehr entweder lokal blockiert oder mittels NAT in eine nicht mehr nachvollziehbare Gruppe von Adressen übersetzt wird, die nicht mehr mit verschiedenen Azure-Endpunkten funktionieren. Für die Lösung ist es erforderlich, dass Sie einige Schritte ausführen:

  * Aktivieren Sie Dienstendpunkte in dem Subnetz, in dem der API Management-Dienst bereitgestellt wird. [Dienstendpunkte][ServiceEndpoints] müssen für Azure SQL, Azure Storage, Azure EventHub and Azure ServiceBus aktiviert werden. Das Aktivieren von Endpunkten direkt aus dem per API Management delegierten Subnetz für diese Dienste ermöglicht hierfür die Nutzung des Microsoft Azure-Backbonenetzwerks, um ein optimales Routing für den Datenverkehr von Diensten bereitzustellen. Bei Verwendung von Dienstendpunkten mit API Management mit Tunnelerzwingung erfolgt für den Datenverkehr der obigen Azure-Dienste keine Tunnelerzwingung. Für den anderen Datenverkehr der API Management-Dienstabhängigkeiten wird die Tunnelerzwingung genutzt, und er kann nicht verloren gehen, da der API Management-Dienst ansonsten nicht richtig funktionieren würde.
    
  * Der gesamte Datenverkehr auf Steuerungsebene, der aus dem Internet an den Verwaltungsendpunkt Ihres API Management-Diensts fließt, wird über einen spezifischen Satz mit IP-Adressen für eingehenden Datenverkehr geleitet, die von API Management gehostet werden. Wenn für den Datenverkehr die Tunnelerzwingung verwendet wird, werden die Antworten nicht symmetrisch wieder diesen IP-Quelladressen für die eingehende Richtung zugeordnet. Zur Überwindung dieser Einschränkung müssen wir die folgenden benutzerdefinierten Routen ([UDRs][UDRs]) hinzufügen. Hiermit wird der Datenverkehr zurück an Azure geleitet, indem das Ziel dieser Hostrouten auf „Internet“ festgelegt wird. Die IP-Adressen für eingehenden Datenverkehr auf Steuerungsebene werden im Artikel [IP-Adressen der Steuerungsebene](#control-plane-ips) aufgeführt.

  * Für die anderen API Management-Dienstabhängigkeiten, für die die Tunnelerzwingung genutzt wird, sollte es eine Möglichkeit geben, den Hostnamen aufzulösen und den Endpunkt zu erreichen. Dies sind:
      - Metriken und Systemüberwachung
      - Diagnose im Azure-Portal
      - SMTP-Relay
      - CAPTCHA des Entwicklerportals

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Problembehandlung
* **Erste Einrichtung**: Wenn die erste Bereitstellung des API Management-Diensts in einem Subnetz nicht erfolgreich ist, sollte zuerst ein virtueller Computer in demselben Subnetz bereitgestellt werden. Stellen Sie als Nächstes eine Remotedesktop-Verbindung mit dem virtuellen Computer her, und überprüfen Sie die Konnektivität mit jeweils einer Ressource der unten aufgeführten Arten in Ihrem Azure-Abonnement.
    * Azure Storage-Blob
    * Azure SQL-Datenbank
    * Azure Storage-Tabelle

  > [!IMPORTANT]
  > Nachdem Sie die Konnektivität überprüft haben, entfernen Sie sämtliche im Subnetz bereitgestellten Ressourcen, bevor Sie API Management im Subnetz bereitstellen.

* **Überprüfen des Netzwerkkonnektivitätsstatus:** Nach dem Bereitstellen von API Management im Subnetz können Sie im Portal die Konnektivität Ihrer Instanz mit Abhängigkeiten wie Azure Storage überprüfen. Wählen Sie im Portal im linken Menü unter **Deployment and infrastructure** (Bereitstellung und Infrastruktur) die Option **Netzwerkkonnektivitätsstatus** aus.

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Überprüfen des Netzwerkkonnektivitätsstatus im Portal":::

    * Wählen Sie **Erforderlich** aus, um die Konnektivität mit den erforderlichen Azure-Diensten für API Management zu überprüfen. Ein Fehler weist darauf hin, dass die Instanz keine Kernvorgänge zum Verwalten von APIs durchführen kann.
    * Wählen Sie **Optional** aus, um die Konnektivität mit optionalen Diensten zu überprüfen. Ein möglicher Fehler gibt nur an, dass die spezifische Funktion nicht ausgeführt wird (z. B. SMTP). Fehler können zu einer Beeinträchtigung der Möglichkeit führen, die API Management-Instanz zu verwenden und zu überwachen und die verbindliche SLA zu erfüllen.

Sehen Sie sich zum Beheben von Konnektivitätsproblemen [Allgemeine Probleme mit der Netzwerkkonfiguration](#network-configuration-issues) an, und korrigieren Sie die entsprechenden Netzwerkeinstellungen.

* **Inkrementelle Updates**: Wenn Sie Änderungen an Ihrem Netzwerk vornehmen, sollten Sie sich mithilfe der [NetworkStatus-API](/rest/api/apimanagement/2019-12-01/networkstatus) vergewissern, dass der API Management-Dienst weiterhin Zugriff auf wichtige Ressourcen hat, von denen er abhängig ist. Der Konnektivitätsstatus sollte alle 15 Minuten aktualisiert werden.

* **Ressourcennavigationslinks**: Bei der Bereitstellung in einem VNET-Subnetz im Resource Manager-Stil reserviert API Management das Subnetz durch Erstellen eines Ressourcennavigationslinks. Wenn das Subnetz bereits eine Ressource von einem anderen Hersteller enthält, tritt bei der Bereitstellung ein **Fehler** auf. Wenn Sie einen API Management-Dienst in ein anderes Subnetz verschieben oder ihn löschen, wird dieser Ressourcennavigationslink entsprechend entfernt.

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> Größenanforderungen für Subnetze
Einige IP-Adressen innerhalb jedes Subnetzes sind in Azure reserviert und können deshalb nicht genutzt werden. Die erste und letzte IP-Adresse der Subnetze sind aus Gründen der Protokollkonformität reserviert. Darüber hinaus sind drei weitere für Azure-Dienste verwendete IP-Adressen reserviert. Weitere Informationen finden Sie unter [Unterliegen die in den Subnetzen verwendeten IP-Adressen bestimmten Beschränkungen?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Zusätzlich zu den IP-Adressen, die von der Azure-VNET-Infrastruktur verwendet werden, nutzt jede API Management-Instanz im Subnetz zwei IP-Adressen pro Premium-SKU-Einheit oder eine IP-Adresse für die Entwickler-SKU. Jede Instanz reserviert eine zusätzliche IP-Adresse für den externen Lastenausgleich. Bei der Bereitstellung im internen virtuellen Netzwerk ist eine zusätzliche IP-Adresse für den internen Lastenausgleich erforderlich.

Ausgehend von der obigen Berechnung ist /29 die minimale Größe des Subnetzes, in dem API Management bereitgestellt werden kann. Dies entspricht drei verwendbaren IP-Adressen.

Jede zusätzliche Skalierungseinheit von API Management erfordert zwei weitere IP-Adressen.

## <a name="routing"></a><a name="routing"> </a> Routing
+ Eine öffentliche IP-Adresse (VIP) mit Lastenausgleich wird auch reserviert, um den Zugriff auf alle Dienstendpunkte zu ermöglichen.
+ Eine IP-Adresse aus einem Subnetz-IP-Bereich (DIP) wird für den Zugriff auf Ressourcen innerhalb des VNet und eine öffentliche IP-Adresse (VIP) für den Zugriff auf Ressourcen außerhalb des VNet verwendet.
+ Die öffentliche IP-Adresse mit Lastenausgleich finden Sie auf dem Blatt „Übersicht/Essentials“ im Azure-Portal.

## <a name="limitations"></a><a name="limitations"> </a>Einschränkungen
* Ein Subnetz, das API Management-Instanzen enthält, kann keine anderen Azure-Ressourcentypen enthalten.
* Das Subnetz und der API Management-Dienst müssen sich im selben Abonnement befinden.
* Ein Subnetz, das API Management-Instanzen enthält, kann nicht zwischen Abonnements verschoben werden.
* Bei API Management-Bereitstellungen in mehreren Regionen mit Konfiguration im Modus interner virtueller Netzwerke sind die Benutzer dafür verantwortlich, den Lastenausgleich über mehrere Regionen zu verwalten, da sie Besitzer des Routings sind.
* Die Konnektivität zwischen einer Ressource in einem VNET mit globalem Peering in einer anderen Region und dem API Management-Dienst im internen Modus funktioniert aufgrund einer Plattformbeschränkung nicht. Weitere Informationen finden Sie unter den Informationen zum Thema [Ressourcen in einem virtuellen Netzwerk können nicht mit dem internen Azure-Lastenausgleich in einem per Peering verbundenen virtuellen Netzwerk kommunizieren](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints).

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> IP-Adressen der Steuerungsebene

Die IP-Adressen werden auf die **Azure-Umgebung** aufgeteilt. Wenn IP-Adressen eingehender Anforderungen zugelassen werden, die mit **Global** gekennzeichnet sind, müssen diese zusammen mit der spezifischen IP-Adresse der **Region** zugelassen werden.

| **Azure-Umgebung**|   **Region**|  **IP-Adresse**|
|-----------------|-------------------------|---------------|
| Azure – Öffentlich| USA, Süden-Mitte (Global)| 104.214.19.224|
| Azure – Öffentlich| USA, Norden-Mitte (Global)| 52.162.110.80|
| Azure – Öffentlich| USA, Westen-Mitte| 52.253.135.58|
| Azure – Öffentlich| Korea, Mitte| 40.82.157.167|
| Azure – Öffentlich| UK, Westen| 51.137.136.0|
| Azure – Öffentlich| Japan, Westen| 40.81.185.8|
| Azure – Öffentlich| USA Nord Mitte| 40.81.47.216|
| Azure – Öffentlich| UK, Süden| 51.145.56.125|
| Azure – Öffentlich| Indien, Westen| 40.81.89.24|
| Azure – Öffentlich| East US| 52.224.186.99|
| Azure – Öffentlich| Europa, Westen| 51.145.179.78|
| Azure – Öffentlich| Japan, Osten| 52.140.238.179|
| Azure – Öffentlich| Frankreich, Mitte| 40.66.60.111|
| Azure – Öffentlich| Kanada, Osten| 52.139.80.117|
| Azure – Öffentlich| Vereinigte Arabische Emirate, Norden| 20.46.144.85|
| Azure – Öffentlich| Brasilien Süd| 191.233.24.179|
| Azure – Öffentlich| Brasilien, Südosten| 191.232.18.181|
| Azure – Öffentlich| Asien, Südosten| 40.90.185.46|
| Azure – Öffentlich| Südafrika, Norden| 102.133.130.197|
| Azure – Öffentlich| Kanada, Mitte| 52.139.20.34|
| Azure – Öffentlich| Korea, Süden| 40.80.232.185|
| Azure – Öffentlich| Indien, Mitte| 13.71.49.1|
| Azure – Öffentlich| USA (Westen)| 13.64.39.16|
| Azure – Öffentlich| Australien, Südosten| 20.40.160.107|
| Azure – Öffentlich| Australien, Mitte| 20.37.52.67|
| Azure – Öffentlich| Indien (Süden)| 20.44.33.246|
| Azure – Öffentlich| USA (Mitte)| 13.86.102.66|
| Azure – Öffentlich| Australien (Osten)| 20.40.125.155|
| Azure – Öffentlich| USA, Westen 2| 51.143.127.203|
| Azure – Öffentlich| USA, Osten 2 (EUAP)| 52.253.229.253|
| Azure – Öffentlich| USA, Mitte (EUAP)| 52.253.159.160|
| Azure – Öffentlich| USA Süd Mitte| 20.188.77.119|
| Azure – Öffentlich| USA (Ost) 2| 20.44.72.3|
| Azure – Öffentlich| Nordeuropa| 52.142.95.35|
| Azure – Öffentlich| Asien, Osten| 52.139.152.27|
| Azure – Öffentlich| Frankreich, Süden| 20.39.80.2|
| Azure – Öffentlich| Schweiz, Westen| 51.107.96.8|
| Azure – Öffentlich| Australien, Mitte 2| 20.39.99.81|
| Azure – Öffentlich| VAE, Mitte| 20.37.81.41|
| Azure – Öffentlich| Schweiz, Norden| 51.107.0.91|
| Azure – Öffentlich| Südafrika, Westen| 102.133.0.79|
| Azure – Öffentlich| Deutschland, Westen-Mitte| 51.116.96.0|
| Azure – Öffentlich| Deutschland, Norden| 51.116.0.0|
| Azure – Öffentlich| Norwegen, Osten| 51.120.2.185|
| Azure – Öffentlich| Norwegen, Westen| 51.120.130.134|
| Azure China 21Vianet| China, Norden (Global)| 139.217.51.16|
| Azure China 21Vianet| China, Osten (Global)| 139.217.171.176|
| Azure China 21Vianet| China, Norden| 40.125.137.220|
| Azure China 21Vianet| China, Osten| 40.126.120.30|
| Azure China 21Vianet| China, Norden 2| 40.73.41.178|
| Azure China 21Vianet| China, Osten 2| 40.73.104.4|
| Azure Government| US Government, Virginia (Global)| 52.127.42.160|
| Azure Government| US Government, Texas (Global)| 52.127.34.192|
| Azure Government| US Government, Virginia| 52.227.222.92|
| Azure Government| US Government, Iowa| 13.73.72.21|
| Azure Government| US Gov Arizona| 52.244.32.39|
| Azure Government| US Gov Texas| 52.243.154.118|
| Azure Government| USDoD, Mitte| 52.182.32.132|
| Azure Government| USDoD, Osten| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Verwandte Inhalte
* [Verbinden eines virtuellen Netzwerks mit dem Back-End über VPN Gateway](../vpn-gateway/design.md#s2smulti)
* [Herstellen einer Verbindung mit einem virtuellen Netzwerk in verschiedenen Bereitstellungsmodellen](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Verwenden des API-Inspektors zur Verfolgung von Aufrufen in Azure API Management](api-management-howto-api-inspector.md)
* [Häufig gestellte Fragen zu Virtual Network](../virtual-network/virtual-networks-faq.md)
* [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
