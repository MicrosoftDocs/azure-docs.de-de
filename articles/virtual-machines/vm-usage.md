---
title: Grundlegendes zur Verwendung virtueller Azure-Computer
description: Grundlegendes zu Nutzungsdetails virtueller Computer
services: virtual-machines
documentationcenter: ''
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 07/28/2020
ms.openlocfilehash: b845d547224fb173d2a4b156575778783e0281fa
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488564"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Grundlegendes zur Verwendung virtueller Azure-Computer
Durch das Analysieren Ihre Azure-Nutzungsdaten können aufschlussreiche Einblicke in die Verwendung gewonnen werden – Einblicke, die eine bessere Kostenverwaltung und Verteilung in Ihrer Organisation ermöglichen können. Dieses Dokument enthält fundierte Einblicke in die Nutzungsdetails von Azure Compute. Weitere Informationen zur allgemeinen Azure-Nutzung finden Sie unter [Grundlegendes zu Ihrer Rechnung](../cost-management-billing/understand/review-individual-bill.md).

## <a name="download-your-usage-details"></a>Herunterladen Ihrer Nutzungsdetails
Um zu beginnen, [laden Sie Ihre Nutzungsdetails herunter](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Die folgende Tabelle enthält Definitionen und Beispielwerte zur Nutzung virtueller Computer, die über den Azure Resource Manager bereitgestellt wurden. Dieses Dokument enthält keine Detailinformationen zu virtuellen Computern, die über das klassische Modell bereitgestellt wurden.


| Feld | Bedeutung | Beispielwerte | 
|---|---|---|
| Usage Date | Das Datum, an dem die Ressource verwendet wurde. | `11/23/2017` |
| Meter ID | Gibt den Dienst der obersten Ebene an, zu dem diese Nutzung gehört.| `Virtual Machines`|
| Meter Sub-Category | Der Bezeichner der berechneten Messung. <br><br> Für die Nutzung „Computestunde“ gibt es eine Verbrauchseinheit für jede Kombination aus VM-Größe + Betriebssystem (Windows, nicht Windows) + Region. <br><br> Für die Nutzung von Premium-Software gibt es eine Verbrauchseinheit für jeden Softwaretyp. Bei den meisten Premium-Softwareimages gelten für einzelnen Kerngrößen unterschiedliche Verbrauchseinheiten. Weitere Informationen finden Sie auf der Seite [Azure-Preise](https://azure.microsoft.com/pricing/details/virtual-machines/).</li></ul>| `2005544f-659d-49c9-9094-8e0aea1be3a5`|
| Meter Name| Dieser Name ist für jeden Dienst in Azure unterschiedlich. Bei Compute ist es immer „Computestunden“.| `Compute Hours`|
| Meter Region| Gibt den Standort des Rechenzentrums für bestimmte Dienste an, die basierend auf dem Standort des Rechenzentrums berechnet werden.|  `JA East`|
| Einheit| Gibt die Einheit an, in der der Dienst in Rechnung gestellt wird. Computeressourcen werden stundenweise abgerechnet.| `Hours`|
| Consumed| Die Menge der Ressource, die an diesem Tag verbraucht wurde. Bei Compute werden alle Minuten berechnet, die der virtuelle Computer für eine bestimmte Stunde ausgeführt wurde (bis zu einer Genauigkeit von 6 Dezimalstellen).| `1, 0.5`|
| Resource Location  | Gibt das Rechenzentrum an, in dem die Ressource ausgeführt wird.| `JA East`|
| Consumed Service | Der verwendete Azure-Plattformdienst.| `Microsoft.Compute`|
| Ressourcengruppe | Die Ressourcengruppe, in der die bereitgestellte Ressource ausgeführt wird. Weitere Informationen finden Sie unter [Übersicht über den Azure Resource Manager](../azure-resource-manager/management/overview.md).|`MyRG`|
| Instance ID | Der Bezeichner für die Ressource. Der Bezeichner enthält den Namen, den Sie für die Ressource bei der Erstellung angegeben haben. Für virtuelle Computer enthält die Instanz-ID die Abonnement-ID, den Ressourcengruppennamen und den Namen des virtuellen Computers (oder den Skalierungsgruppennamen beim Verbrauch von Skalierungsgruppen).| `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1`<br><br>oder<br><br>`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1`|
| `Tags`| Tag, den Sie der Ressource zuweisen. Verwenden Sie Tags zum Gruppieren von Abrechnungsdatensätzen. Erfahren Sie, wie Sie Ihre virtuellen Computer mithilfe der [Befehlszeilenschnittstelle](./tag-cli.md) oder von [PowerShell](./tag-portal.md) kennzeichnen. Dies ist nur für Resource Manager-VMs verfügbar.| `{"myDepartment":"RD","myUser":"myName"}`|
| Zusätzliche Informationen | Dienstspezifische Metadaten. Für virtuelle Computer werden folgende Daten im Feld „Additional Info“ angegeben: <br><br> „ImageType“: das spezifische Image, das Sie ausgeführt haben. Die vollständige Liste der unterstützten Zeichenfolgen finden Sie unten unter „Imagetyp“.<br><br> „ServiceType“: die Größe, die Sie bereitgestellt haben<br><br> „VMName“: der Name Ihres virtuellen Computers. Dieses Feld wird nur bei Skalierungsgruppen-VMs ausgefüllt. Wenn Sie den VM-Namen von Skalierungsgruppen-VMs benötigen, finden Sie diesen in der Zeichenfolge der Instanz-ID oben.<br><br> UsageType: gibt den Typ der dargestellten Nutzung an.<br><br> ComputeHR ist die Nutzung „Computestunde“ für den zugrunde liegenden virtuellen Computer, z.B. Standard_D1_v2.<br><br> ComputeHR_SW ist die Gebühr für Premium-Software, wenn der virtuelle Computer Premium-Software wie Microsoft R Server verwendet. | Virtual Machines<br>`{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}`<br><br>Virtual Machine Scale Sets<br> `{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}`<br><br>Premium-Software<br> `{"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"}` |

## <a name="image-type"></a>Imagetyp
Für einige Images im Azure-Katalog wird der Imagetyp im Feld „Additional Info“ angegeben. Dadurch können Benutzer erkennen und nachverfolgen, was auf ihrem virtuellen Computer bereitgestellt wurde. In diesem Feld werden die folgenden Werte basierend auf dem von Ihnen bereitgestellten Image angegeben:
- BitRock 
- Canonical FreeBSD 
- Open Logic 
- Oracle 
- SLES für SAP 
- SQL Server 14 Preview on Windows Server 2012 R2 Preview 
- SUSE
- SUSE Premium
- StorSimple Cloud Appliance 
- Red Hat
- Red Hat for SAP Business Applications     
- Red Hat for SAP HANA 
- Windows Client BYOL 
- Windows Server BYOL 
- Windows Server Preview 

## <a name="service-type"></a>Diensttyp
Das Feld „ServiceType“ im Feld „Additional Info“ entspricht der genauen VM-Größe, die Sie bereitgestellt haben. Storage Premium-VMs (SSD-basiert) und Nicht-Storage Premium-VMs (HDD-basiert) werden gleich berechnet. Wenn Sie eine Größe auf SSD-Basis bereitstellen (z. B. „Standard\_DS2\_v2“), wird die Größe von VMs ohne SSD (`Standard\_D2\_v2 VM`) in der Spalte „Meter Sub-Category“ und von VMs mit SSD (`Standard\_DS2\_v2`) im Feld „Additional Info“ angezeigt.

## <a name="region-names"></a>Regionsnamen
Der Name der Region im Feld „Resource Location“ der Nutzungsdetails variiert je nach dem Regionsnamen, der in Azure Resource Manager verwendet wird. Im Folgenden finden Sie eine Zuordnung zwischen den Werten für die Region:

| **Resource Manager-Regionsname** | **Ressourcenstandort in den Nutzungsdetails** |
|---|---|
| australiaeast |Australien, Osten|
| australiasoutheast | Australien, Südosten|
| brazilsouth | Brasilien, Süden|
| CanadaCentral | Kanada, Mitte|
| CanadaEast | Kanada, Osten|
| CentralIndia | Indien, Mitte|
| centralus | USA (Mitte)|
| chinaeast | China, Osten|
| chinanorth | China, Norden|
| eastasia | Asien, Osten|
| eastus | East US|
| eastus2 | USA (Ost) 2|
| GermanyCentral | Deutschland, Mitte|
| GermanyNortheast | Deutschland, Nordosten|
| japaneast | Japan, Osten|
| japanwest | Japan, Westen|
| KoreaCentral | Südkorea, Mitte|
| KoreaSouth | Südkorea, Süden|
| northcentralus | USA Nord Mitte|
| northeurope | Nordeuropa|
| southcentralus | USA Süd Mitte|
| southeastasia | Asien, Südosten|
| SouthIndia | Indien, Süden|
| UKNorth | Vereinigtes Königreich, Norden|
| uksouth | UK, Süden|
| UKSouth2 | Vereinigtes Königreich, Süden 2|
| ukwest | UK, Westen|
| USDoDCentral | US DoD, Mitte|
| USDoDEast | US DoD, Osten|
| USGovArizona | US Gov Arizona|
| usgoviowa | US Government, Iowa|
| USGovTexas | US Gov Texas|
| usgovvirginia | US Government, Virginia|
| westcentralus | USA, Westen-Mitte|
| westeurope | Europa, Westen|
| WestIndia | Indien, Westen|
| westus | USA (Westen)|
| westus2 | USA, Westen 2|


## <a name="virtual-machine-usage-faq"></a>Häufig gestellte Fragen zur Nutzung virtueller Computer
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Welche Ressourcen werden beim Bereitstellen einer VM in Rechnung gestellt?    
VMs verursachen Kosten für den virtuellen Computer selbst, für Premium-Software, die auf dem virtuellen Computer ausgeführt wird, für das Speicherkonto und die verwalteten Datenträger, die dem virtuellen Computer zugeordnet sind, und für die Netzwerkbandbreite, die durch Übertragungen vom virtuellen Computer verbraucht wird.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Woran erkenne ich in der Nutzungs-CSV, ob eine VM den Azure-Hybridvorteil verwendet?
Bei einer Bereitstellung mit dem [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) wird Ihnen die Nicht-Windows-VM-Gebühr berechnet, da Sie Ihre eigene Lizenz in der Cloud nutzen. In Ihrer Rechnung können Sie die Resource Manager-VMs mit dem Azure-Hybridvorteil unterscheiden, da bei diesen in der Spalte „ImageType“ entweder „Windows\_Server BYOL“ oder „Windows\_Client BYOL“ angezeigt wird.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Wie werden Basic- und Standard-VM-Typen in der Nutzungs-CSV unterschieden?
Es werden sowohl Basic- als auch Standard-VMs der A-Serie angeboten. Wenn Sie eine Basic-VM bereitstellen, wird in „Meter Sub-Category“ die Zeichenfolge „Basic“ angezeigt. Wenn Sie eine Standard-VM der A-Serie bereitstellen, wird als VM-Größe „A1 VM“ angezeigt, da Standard der Standardwert ist. Weitere Informationen zu den Unterschieden zwischen Basic und Standard finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Was bedeuten die Größen ExtraSmall, Small, Medium, Large und ExtraLarge?
ExtraSmall bis ExtraLarge sind die alten Namen für Standard\_A0 bis Standard\_A4. In den klassischen VM-Nutzungseinträgen werden möglicherweise diese Bezeichnungen verwendet, wenn Sie diese Größen bereitgestellt haben.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Was ist der Unterschied zwischen „Meter Region“ und „Resource Location“?
„Meter Region“ (Region der Verbrauchseinheit) bezieht sich auf die Verbrauchseinheit. Bei einigen Azure-Diensten, die einen Preis für alle Regionen verwenden, kann das Feld „Meter Region“ leer sein. Da bei Virtual Machines für virtuelle Computer pro Region unterschiedliche Preise gelten, wird dieses Feld jedoch ausgefüllt. Entsprechend ist „Resource Location“ (Ressourcenstandort) bei Virtual Machines der Standort, an dem der virtuelle Computer bereitgestellt wird. Die Azure-Regionen in beiden Feldern sind identisch, obwohl für den Regionsnamen möglicherweise andere Zeichenfolgenkonventionen gelten.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Warum ist der Wert von „ImageType“ im Feld „Additional Info“ leer?
Das Feld „ImageType“ wird nur für einen Teil der Images ausgefüllt. Wenn Sie keines der oben genannten Images bereitgestellt haben, bleibt das Feld „ImageType“ leer.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Warum ist der VMName im Feld „Additional Info“ leer?
Der VMName im Feld „Additional Info“ wird nur bei virtuellen Computern in einer Skalierungsgruppe ausgefüllt. Das Feld „InstanceID“ enthält den VM-Namen für VMs, die keiner Skalierungsgruppe angehören.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Was bedeutet ComputeHR im Feld „UsageType“ in „Additional Info“?
ComputeHR steht für Compute Hour (Computestunde) und stellt das Nutzungsereignis für die zugrunde liegenden Infrastrukturkosten dar. Wenn der UsageType „ComputeHR\_SW“ lautet, stellt das Nutzungsereignis die Premium-Softwaregebühr für den virtuellen Computer dar.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Wie erkenne ich, ob mir Premium-Software in Rechnung gestellt wurde?
Wenn Sie untersuchen, welches VM-Image Ihren Anforderungen am besten entspricht, sehen Sie auch im [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute) nach. Das Image hat den Software-Tarif. Wenn als Tarif „Free“ angezeigt wird, fallen keine zusätzlichen Kosten für die Software an. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Was ist der Unterschied zwischen Microsoft.ClassicCompute und Microsoft.Compute beim genutzten Dienst?
Microsoft.ClassicCompute steht für klassische Ressourcen, die über den Azure Service Manager bereitgestellt wurden. Bei einer Resource Manager-Bereitstellung wird als genutzter Dienst Microsoft.Compute angegeben. Erfahren Sie mehr über die [Azure-Bereitstellungsmodelle](../azure-resource-manager/management/deployment-models.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Warum ist das Feld „InstanceID“ für die Nutzung meiner virtuellen Computer leer?
Bei einer Bereitstellung mit dem klassischen Bereitstellungsmodell ist keine Zeichenfolge für InstanceID verfügbar.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Warum werden die Tags für meine virtuellen Computer nicht in die Nutzungsdetails übertragen?
Tags fließen nur in die Nutzungs-CSV von Resource Manager-VMs ein. Tags klassischer Ressourcen sind in den Nutzungsdetails nicht verfügbar.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Wie kann die verbrauchte Menge mehr als 24 Stunden für einen Tag betragen?
Im klassischen Modell wird die Abrechnung für Ressourcen auf der Ebene des Clouddiensts aggregiert. Wenn Sie über mehrere virtuelle Computer in einem Clouddienst verfügen, die dieselben Verbrauchseinheiten für die Abrechnung verwenden, wird ihre Nutzung zusammengefasst. VMs, die über Resource Manager bereitgestellt werden, werden auf VM-Ebene abgerechnet, sodass keine Aggregation erfolgt.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Warum sind auf der Preisseite keine Preise für die Größen DS/FS/GS/LS verfügbar?
Storage Premium-fähige virtuelle Computer werden mit derselben Gebühr wie VMs, die nicht Storage Premium-fähig sind, berechnet. Nur die Speicherkosten unterscheiden sich. Weitere Informationen finden Sie auf der Seite mit der [Storage-Preisübersicht](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Nutzungsdetails finden Sie unter [Informationen zu Ihrer Rechnung für Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md).