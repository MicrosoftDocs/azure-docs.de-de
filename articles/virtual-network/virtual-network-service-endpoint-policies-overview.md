---
title: Richtlinien für Azure-VNET-Dienstendpunkte | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie virtuellen Netzwerkdatenverkehr zu Azure-Dienstressourcen mithilfe von Richtlinien für Dienstendpunkte filtern.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumeet.mittal
ms.openlocfilehash: b39f365c8b66f7cab074a20bc574803e12f93422
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61033899"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Richtlinien für VNET-Dienstendpunkte (Vorschau)

Richtlinien für Dienstendpunkte in virtuellen Azure-Netzwerken (VNET, Virtual Network) ermöglichen es Ihnen, virtuellen Netzwerkdatenverkehr über Dienstendpunkte zu Azure-Diensten zu filtern, sodass nur bestimmte Azure-Dienstressourcen zugelassen werden. Endpunktrichtlinien bieten eine differenzierte Zugriffssteuerung für virtuellen Netzwerkdatenverkehr zu Azure-Diensten.

Dieses Feature ist für die folgenden Azure-Dienste und -Regionen als __Vorschauversion__ verfügbar:

__Azure Storage__: USA, Westen-Mitte; USA, Westen 2; USA, Norden-Mitte; USA, Süden-Mitte; USA, Mitte; USA, Osten 2.

Aktuelle Hinweise zur Vorschauversion finden Sie auf der Seite [Azure-Updates](https://azure.microsoft.com/updates/?product=virtual-network).

> [!NOTE]  
> Während der Vorschauphase bieten Richtlinien für VNET-Dienstendpunkte unter Umständen nicht die gleiche Verfügbarkeit und Zuverlässigkeit wie Features in Releases mit allgemeiner Verfügbarkeit. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Hauptvorteile

Richtlinien für VNET-Dienstendpunkte bieten folgende Vorteile:

- __Mehr Sicherheit für den Datenverkehr aus Ihrem virtuellen Netzwerk zu Azure-Diensten__

  [Azure-Diensttags für Netzwerksicherheitsgruppen](https://aka.ms/servicetags) ermöglichen es Ihnen, den ausgehenden virtuellen Netzwerkdatenverkehr zu bestimmten Azure-Diensten einzuschränken. Damit lassen Sie jedoch Datenverkehr zu allen Ressourcen dieses Azure-Diensts zu. 
  
  Mit Endpunktrichtlinien können Sie jetzt den Zugriff des ausgehenden virtuellen Netzwerkdatenverkehrs auf bestimmte Azure-Ressourcen zu beschränken. Damit erhalten Sie eine sehr viel differenziertere Steuerung der Sicherheit, um Daten zu schützen, auf die in Ihrem virtuellen Netzwerk zugegriffen wird. 

- __Skalierbare, hoch verfügbare Richtlinien zum Filtern des Azure-Dienstdatenverkehrs__

   Endpunktrichtlinien bieten eine horizontal skalierbare, hoch verfügbare Lösung zum Filtern des Azure-Dienstdatenverkehrs aus virtuellen Netzwerken über Dienstendpunkte. Für die Verwaltung der zentralen Netzwerkappliances für diesen Datenverkehr in Ihren virtuellen Netzwerken fällt kein zusätzlicher Aufwand an.

## <a name="configuration"></a>Konfiguration

- Sie können die Endpunktrichtlinien so konfigurieren, dass der virtuelle Netzwerkdatenverkehr zu bestimmten Azure-Dienstressourcen eingeschränkt wird. In der Vorschau unterstützen wir Endpunktrichtlinien für Azure Storage. 
- Endpunktrichtlinien werden in einem Subnetz eines virtuellen Netzwerks konfiguriert. Dienstendpunkte müssen im Subnetz für alle in der Richtlinie angegebenen Azure-Dienste zugelassen sein, damit die Richtlinie angewendet werden kann.
- Endpunktrichtlinien ermöglichen es Ihnen, mithilfe des resourceID-Formats eine Whitelist bestimmter Azure-Dienstressourcen zu erstellen. Sie können den Zugriff auf alle Ressourcen in einem Abonnement oder einer Ressourcengruppe beschränken. Sie können auch den Zugriff auf bestimmte Ressource beschränken. 
- Wenn an ein Subnetz mit Endpunkten keine Richtlinien angefügt sind, können Sie auf alle Ressourcen im Dienst zugreifen. Sobald eine Richtlinie in diesem Subnetz konfiguriert wurde, können Compute-Instanzen in diesem Subnetz nur auf die in der Richtlinie angegebenen Ressourcen zugreifen. Der Zugriff auf alle anderen Ressource wird für den angegebenen Dienst verweigert. 
- Sie können den Datenverkehr zu Azure-Dienstressourcen in den Regionen filtern, in denen ein Dienstendpunkt konfiguriert wurde. Der Zugriff auf Dienstressourcen in anderen Regionen wird standardmäßig zugelassen. 

  > [!NOTE]  
  > Um den Zugriff auf Azure-Ressourcen in Dienstendpunktregionen durch ausgehenden virtuellen Netzwerkdatenverkehr vollständig zu unterbinden, müssen Sie auch mithilfe von [Diensttags](security-overview.md#service-tags) Regeln für Netzwerksicherheitsgruppen konfigurieren, sodass Datenverkehr nur zu den Dienstendpunktregionen zugelassen ist.

- Sie können mehrere Richtlinien auf ein Subnetz anwenden. Wenn dem Subnetz mehrere Richtlinien für den gleichen Dienst zugeordnet sind, wird der virtuelle Netzwerkdatenverkehr zu allen Ressourcen zugelassen, die in einer beliebigen dieser Richtlinien angegeben sind. Der Zugriff auf alle anderen Dienstressourcen, die in keiner der Richtlinien angegeben sind, wird verweigert. 

  > [!NOTE]  
  > Eine Richtlinie lässt den Zugriff aus einem virtuellen Netzwerk nur auf angegebene Dienstressourcen zu. Jeder andere Datenverkehr zum Dienst wird automatisch verweigert, wenn Sie der Richtlinie bestimmte Ressourcen hinzufügen. Stellen Sie sicher, dass alle Abhängigkeiten von Dienstressourcen für Ihre Anwendungen in der Richtlinie identifiziert und angegeben werden können.

  > [!WARNING]  
  > Azure-Dienste, die in Ihrem virtuellen Netzwerk bereitgestellt wurden (z.B. Azure HDInsight), greifen aufgrund von Infrastrukturanforderungen auf andere Azure-Dienste zu, wie z.B. Azure Storage. Das Einschränken der Endpunktrichtlinie auf bestimmte Ressourcen könnten den Zugriff auf diese Infrastrukturressourcen für Dienste unterbrechen, die in Ihrem virtuellen Netzwerk bereitgestellt wurden. Informationen zu diesen Diensten finden Sie unter [Einschränkungen](#limitations). Während der Vorschau werden Richtlinien für Dienstendpunkte für verwaltete Azure-Dienste nicht unterstützt, die in Ihrem virtuellen Netzwerk bereitgestellt wurden.

- Für Azure Storage: 
  -  Sie können den Zugriff einschränken, indem Sie die Azure Resource Manager-*resourceId* des Speicherkontos angeben. Dies deckt den Datenverkehr zu Blobs, Tabellen, Warteschlangen, Dateien und Azure Data Lake Storage Gen2 ab.

     Azure-Speicherkonten könnten beispielsweise folgendermaßen in der Definition der Endpunktrichtlinie angegeben werden:
    
     Zum Zulassen eines bestimmten Speicherkontos:         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     Zum Zulassen aller Konten in einem Abonnement und einer Ressourcengruppe: `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     Zum Zulassen aller Konten in einem Abonnement: `/subscriptions/subscriptionId`
    
- Nur Speicherkonten, die das Azure-Ressourcenmodell verwenden, können in der Endpunktrichtlinie angegeben werden.  

  > [!NOTE]  
  > Der Zugriff auf klassische Speicherkonten wird mit Endpunktrichtlinien blockiert.

- Der primäre Speicherort für das angegebene Konto sollte sich in geografisch gekoppelten Regionen des Dienstendpunkts für das Subnetz befinden. 

  > [!NOTE]  
  > Richtlinien ermöglichen die Angabe von Dienstressourcen aus anderen Regionen. Der virtuelle Netzwerkzugriff auf die Azure-Dienste wird nur für die geografisch gekoppelten Regionen gefiltert. Wenn Netzwerksicherheitsgruppen nicht auf die geografisch gekoppelten Regionen für Azure Storage beschränkt werden, kann das virtuelle Netzwerk auf alle Speicherkonten außerhalb der geografisch gekoppelten Regionen zugreifen.

- Der Zugriff auf sekundären georedundanten Speicher mit Lesezugriff wird automatisch zugelassen, wenn das primäre Konto angegeben ist. 
- Speicherkonten können sich im selben oder einem anderen Abonnement oder Azure Active Directory-Mandanten befinden wie das virtuelle Netzwerk. 

## <a name="limitations"></a>Einschränkungen

- Sie können Richtlinien für Dienstendpunkte nur in virtuellen Netzwerken bereitstellen, die mit dem Azure Resource Manager-Bereitstellungsmodell bereitgestellt wurden.
- Virtuelle Netzwerke müssen sich in derselben Region wie die Richtlinie des Dienstendpunkts befinden.
- Sie können eine Richtlinie für Dienstendpunkte in einem Subnetz nur anwenden, wenn die Dienstendpunkte für die in der Richtlinie angegebenen Azure-Dienste konfiguriert sind.
- Sie können Richtlinien für Dienstendpunkte nicht für Datenverkehr aus Ihrem lokalen Netzwerk zu Azure-Diensten verwenden.
- Endpunktrichtlinien sollten nicht auf Subnetze mit verwalteten Azure-Diensten angewendet werden, bei denen aufgrund von Infrastrukturanforderungen eine Abhängigkeit von Azure-Diensten besteht. 

  > [!WARNING]  
  > Azure-Dienste, die in Ihrem virtuellen Netzwerk bereitgestellt wurden (z.B. Azure HDInsight), greifen aufgrund von Infrastrukturanforderungen auf andere Azure-Dienste zu, wie z.B. Azure Storage. Das Einschränken der Endpunktrichtlinie auf bestimmte Ressourcen könnten den Zugriff auf diese Infrastrukturressourcen für die Azure-Dienste unterbrechen, die in Ihrem virtuellen Netzwerk bereitgestellt wurden.
  
  - Einige Azure-Dienste können in Subnetzen mit anderen Compute-Instanzen bereitgestellt werden. Stellen Sie sicher, dass Endpunktrichtlinien nicht auf das Subnetz angewendet werden, wenn die unten aufgeführten verwalteten Dienste im Subnetz bereitgestellt wurden.
   
    - Azure HDInsight
    - Azure Batch (Azure Resource Manager)
    - Azure Active Directory Domain Services (Azure Resource Manager)
    - Azure Application Gateway (Azure Resource Manager)
    - Azure VPN Gateway (Azure Resource Manager)
    - Azure Firewall

  - Einige Azure-Dienste sind in dedizierten Subnetzen bereitgestellt. Während der Vorschau sind Endpunktrichtlinien in all diesen Diensten blockiert, die unten aufgeführt sind. 

     - Azure App Service-Umgebung
     - Azure RedisCache
     - Azure API Management
     - Verwaltete Azure SQL-Instanz
     - Azure Active Directory Domain Services
     - Azure Application Gateway (klassisch)
     - Azure VPN Gateway (klassisch)

- Azure Storage: Klassische Speicherkonten werden in Endpunktrichtlinien nicht unterstützt. Richtlinien verweigern standardmäßig den Zugriff auf alle klassischen Speicherkonten. Wenn Ihre Anwendung sowohl auf Azure Resource Manager-Speicherkonten als auch auf klassische Speicherkonten zugreifen muss, sollten für diesen Datenverkehr keine Endpunktrichtlinien verwendet werden. 

## <a name="nsgs-with-service-endpoint-policies"></a>Netzwerksicherheitsgruppen mit Richtlinien für Dienstendpunkte
- Netzwerksicherheitsgruppen lassen ausgehenden Internetdatenverkehr standardmäßig zu, einschließlich des virtuellen Netzwerkdatenverkehrs zu Azure-Diensten.
- Wenn Sie den gesamten ausgehenden Internetdatenverkehr verweigern und nur Datenverkehr zu bestimmten Azure-Diensten zulassen möchten, gehen Sie folgendermaßen vor: 

  Schritt 1: Konfigurieren Sie Netzwerksicherheitsgruppen mithilfe von *Azure-Diensttags* so, dass ausgehender Datenverkehr nur zu Azure-Diensten in Endpunktregionen zugelassen wird. Weitere Informationen finden Sie unter [Diensttags](https://aka.ms/servicetags).
      
  Regeln von Netzwerksicherheitsgruppen, die den Zugriff nur auf Endpunktregionen einschränken, sehen wie das folgende Beispiel aus:

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  Schritt 2: Wenden Sie die Richtlinie für Dienstendpunkte mit Zugriff nur auf bestimmte Azure-Dienstressourcen an.

  > [!WARNING]  
  > Wenn die Netzwerksicherheitsgruppe nicht so konfiguriert ist, dass der Zugriff eines Azure-Diensts in einem virtuellen Netzwerk auf Endpunktregionen eingeschränkt ist, können Sie auf Dienstressourcen in anderen Regionen zugreifen, auch wenn die Richtlinie für Dienstendpunkte angewendet wurde.

## <a name="scenarios"></a>Szenarien

- **Mittels Peering verknüpfte, verbundene oder mehrere virtuelle Netzwerke**: Um den Datenverkehr in virtuellen Peeringnetzwerken zu filtern, sollten Endpunktrichtlinien einzeln auf diese virtuellen Netzwerke angewendet werden.
- **Filtern von Internetdatenverkehr mit Netzwerkgeräten oder per Azure Firewall**: Hierbei wird der Datenverkehr der Azure-Dienste über Endpunkte mit Richtlinien gefiltert, und der restliche Internet- oder Azure-Datenverkehr wird über Geräte oder per Azure Firewall gefiltert. 
- **Filtern von Datenverkehr für Azure-Dienste, die in virtuellen Netzwerken bereitgestellt werden**: Während der Vorschau werden Richtlinien für Dienstendpunkte für verwaltete Azure-Dienste, die in Ihrem virtuellen Netzwerk bereitgestellt wurden, nicht unterstützt. 
 Informationen zu bestimmten Diensten finden Sie unter [Einschränkungen](#limitations).
- **Filtern von Datenverkehr aus der lokalen Umgebung für Azure-Dienste**: Dienstendpunktrichtlinien gelten nur für den Datenverkehr aus Subnetzen, die den Richtlinien zugeordnet sind. Um den Zugriff auf bestimmte Azure-Dienstressourcen aus lokalen Netzwerken zuzulassen, sollte der Datenverkehr mithilfe von virtuellen Netzwerkappliances oder Firewalls gefiltert werden.

## <a name="logging-and-troubleshooting"></a>Protokollierung und Problembehandlung
Für Richtlinien für Dienstendpunkte steht keine zentrale Protokollierung zur Verfügung. Informationen zu Dienstdiagnoseprotokollen finden Sie unter [Protokollierung für Dienstendpunkte](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Problembehandlungsszenarien
- Der Zugriff auf Speicherkonten, die in den Endpunktrichtlinien nicht aufgeführt sind, wird zugelassen.
  - Netzwerksicherheitsgruppen lassen möglicherweise den Zugriff auf das Internet oder auf Azure Storage-Konten in anderen Regionen zu.
  - Netzwerksicherheitsgruppen sollten so konfiguriert werden, dass sämtlicher Internetdatenverkehr verweigert und nur Datenverkehr zu bestimmten Azure Storage-Regionen zugelassen wird. Weitere Informationen finden Sie unter „Netzwerksicherheitsgruppen“.
- Der Zugriff wird für Konten verweigert, die in den Endpunktrichtlinien aufgeführt sind.
  - Netzwerksicherheitsgruppen oder Firewallfilter blockieren möglicherweise den Zugriff.
  - Wenn das Entfernen bzw. erneute Anwenden der Richtlinie zu Konnektivitätsverlust führt, gehen Sie folgendermaßen vor:
    - Überprüfen Sie, ob der Azure-Dienst so konfiguriert ist, dass der Zugriff aus dem virtuellen Netzwerk über Endpunkte zugelassen ist. Überprüfen Sie zudem, ob die Standardrichtlinie für die Ressource auf *Alle zulassen* festgelegt ist.
      > [!NOTE]      
      > Dienstressourcen müssen nicht in virtuellen Netzwerken gesichert werden, um Zugriff über Endpunktrichtlinien zu erhalten. Als Best Practice für die Sicherheit empfehlen wir jedoch, die Dienstressourcen über Dienstendpunkte in Ihren vertrauenswürdigen Netzwerken – z.B. Ihren virtuellen Azure-Netzwerken – oder lokal über eine IP-Firewall zu sichern.
  
    - Überprüfen Sie, ob die Dienstdiagnose den Datenverkehr über die Endpunkte anzeigt.
    - Überprüfen Sie, ob die Flowprotokolle der Netzwerksicherheitsgruppen den Zugriff anzeigen und ob die Speicherprotokolle den Zugriff wie erwartet über Dienstendpunkte anzeigen.
    - Wenden Sie sich an den Azure-Support.
- Der Zugriff wird für Konten verweigert, die in den Richtlinien für Dienstendpunkte nicht aufgeführt sind.
  - Netzwerksicherheitsgruppen oder Firewallfilter blockieren möglicherweise den Zugriff. Stellen Sie sicher, dass das *Azure Storage*-Diensttag für die Endpunktregionen zugelassen ist. Informationen zu Richtlinieneinschränkungen finden Sie unter [Einschränkungen](#limitations).
  Klassischen Speicherkonten wird z.B. der Zugriff verweigert, wenn eine Richtlinie angewendet wird.
  - Überprüfen Sie, ob der Azure-Dienst so konfiguriert ist, dass der Zugriff aus dem virtuellen Netzwerk über Endpunkte zugelassen ist. Überprüfen Sie zudem, ob die Standardrichtlinie für die Ressource auf *Alle zulassen* festgelegt ist.

## <a name="provisioning"></a>Bereitstellung

Richtlinien für Dienstendpunkte können in Subnetzen von einem Benutzer konfiguriert werden, der über Schreibzugriff auf ein virtuelles Netzwerk verfügt. Erfahren Sie mehr über [integrierte Rollen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Azure und das Zuweisen bestimmter Berechtigungen zu [benutzerdefinierten Rollen](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuelle Netzwerke und Azure-Dienstressourcen können sich in demselben oder in unterschiedlichen Abonnements oder Azure Active Directory-Mandanten befinden. 

## <a name="pricing-and-limits"></a>Preise und Einschränkungen

Für die Nutzung von Richtlinien für Dienstendpunkte fallen keine zusätzlichen Gebühren an. Das aktuelle Preismodell für Azure-Dienste (wie z.B. Azure Storage) gilt unverändert für alle Dienstendpunkte.

Folgende Grenzwerte gelten für Richtlinien für Dienstendpunkte: 

 |Resource | Standardlimit |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Konfigurieren von Richtlinien für VNET-Dienstendpunkte](virtual-network-service-endpoint-policies-portal.md).
- Erfahren Sie mehr über [VNET-Dienstendpunkte](virtual-network-service-endpoints-overview.md).

