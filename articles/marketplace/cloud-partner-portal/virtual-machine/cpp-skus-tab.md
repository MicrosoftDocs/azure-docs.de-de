---
title: Registerkarte für VM-SKUs im Cloud-Partnerportal für Azure Marketplace
description: Enthält eine Beschreibung der Registerkarte „SKUs“, die zum Erstellen eines VM-Angebots im Azure Marketplace verwendet wird.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: e1a110abf8e057034043da34455bf678277c6cb4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799942"
---
# <a name="virtual-machine-skus-tab"></a>Registerkarte für VM-SKUs

Mit der Registerkarte **SKUs** auf der Seite **Neues Angebot** können Sie eine oder mehrere SKUs erstellen und Ihrem neuen Angebot zuordnen.  Mit unterschiedlichen SKUs kann eine Lösung nach Featuregruppen, VM-Imagetypen, Durchsatz oder Skalierbarkeit, Abrechnungsmodellen oder einem anderen Merkmal unterschieden werden.


## <a name="create-a-sku"></a>SKU erstellen

Anfänglich verfügt ein neues Angebot nicht über zugeordnete SKUs, sodass Sie eine SKU erstellen müssen, indem Sie auf **Neue SKU** klicken.

![Schaltfläche „Neue SKU“ auf der Registerkarte „Neues Angebot“ für virtuelle Computer](./media/publishvm_005.png)

<br/>

Das Dialogfeld **Neue SKU** wird angezeigt.  Geben Sie den Bezeichner für die neue SKU ein, und klicken Sie anschließend auf **OK**. (Unten ist die Namenskonvention für Bezeichner angegeben.)  Auf der Registerkarte **SKUs** werden jetzt die Felder angezeigt, die bearbeitet werden können.    Mit einem an den Feldnamen angefügten Sternchen (*) wird angegeben, dass eine Eingabe erforderlich ist.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Registerkarte „SKU“ des Formulars „Neues Angebot“ für virtuelle Computer](./media/publishvm_006.png)

In der folgenden Tabelle werden der Zweck, der Inhalt und die Formatierung dieser Felder beschrieben.  Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Feld**       |     **Beschreibung**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU-Einstellungen*   |    |
| **SKU-ID\***       | Der Bezeichner für diese SKU.  Dieser Name kann maximal 50 Zeichen lang sein und aus alphanumerischen Kleinbuchstaben oder Bindestrichen (-) bestehen. (Er darf allerdings nicht mit einem Bindestrich enden.)  Der Name kann nach der Veröffentlichung des Angebots nicht mehr geändert werden.  |
|  *SKU-Details*   |  |
| **Titel\***        | Der Anzeigename des Angebots für die Anzeige im Marketplace. Die maximale Länge beträgt 50 Zeichen. |
| **Zusammenfassung\***      | Eine kurze Beschreibung des Angebots für die Anzeige im Marketplace. Die maximale Länge beträgt 100 Zeichen. |
| **Beschreibung\***  | Beschreibender Text zur ausführlicheren Erläuterung des Angebots.  <!-- TD: max len/guidance? 3k characters -->  |
| **Diese SKU ausblenden\*** | Gibt an, ob die SKU im Marketplace für Kunden sichtbar sein soll.  Sie können die SKU ausblenden, wenn sie nur über Lösungsvorlagen verfügbar und nicht einzeln erhältlich sein soll.  Diese Option kann auch für anfängliche Tests oder für temporäre oder saisonale Angebote nützlich sein. |
| **Cloudverfügbarkeit\*** | Legt fest, in welchen Clouds die SKU verfügbar sein soll.  Der Standardwert ist die öffentliche Version von Azure.  Microsoft Azure Government ist eine Cloud mit Zugriffskontrolle in den USA, die für Regierungsbehörden auf Bundes-, Bundesstaats-, Kommunal- oder Stammesebene und ihre zertifizierten Partnerunternehmen bestimmt ist.  Weitere Informationen zur Government-Cloud finden Sie unter [What is Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) (Was ist Azure Government?). |
| **Ist dies eine private SKU?\*** | Gibt an, ob es sich um eine private oder öffentliche SKU handelt. Der Standardwert lautet **Nein** (öffentlich).  Weitere Informationen finden Sie im Artikel zu [öffentlichen und privaten SKUs](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Verfügbarkeit nach Land/Region\*** | Legt fest, in welchen Ländern oder Regionen der Welt Ihre SKU käuflich erworben werden kann. Wählen Sie mindestens eine Region bzw. ein Land aus. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Preise*   |  |
| **Lizenzmodell\***| Gibt das standardisierte Abrechnungsmodell an, das verwendet werden soll.  Wenn Sie **Usage-based monthly billed SKU** (SKU mit nutzungsbasierter und monatlicher Abrechnung) wählen, wird ein Accordion-Abschnitt geöffnet. Darin können Sie Details zum Preis pro Kern angeben und festlegen, ob Sie eine kostenlose Testversion für einen bestimmten Zeitraum anbieten möchten.  In diesem Abschnitt können Sie den Preisplan auch nach Excel exportieren bzw. importieren. Weitere Informationen finden Sie unter [Abrechnungsoptionen im Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *VM-Images*   |  |
| **Betriebssystemfamilie\*** | Gibt an, ob die Lösungs-VM Windows- oder Linux-basiert ist. |
| **Select Operating System Type** (Betriebssystemtyp auswählen) | Enthält Informationen zu einem bestimmten Anbieter oder der Version des angegebenen Betriebssystems. |
| **Betriebssystem-Anzeigename\*** | Der Name des Betriebssystems, wie er für Kunden angezeigt wird.  |
| **Empfohlene VM-Größen\*** | Ermöglicht die Auswahl von bis zu sechs empfohlenen VM-Größen aus einer standardisierten Liste.  Diese Liste wird an das Azure-Portal und die Microsoft-Marketplaces weitergegeben.  Die erste gültige VM-Größe in dieser Liste (für das betreffende Kundenabonnement, die betreffende Region, die betreffende Zone usw.) wird als Standard für diesen potenziellen Kunden festgelegt.  Der Benutzer kann diese Größe in jede mit dem Lösungsimage kompatible Größe ändern. | 
| **Offene Ports**| Gibt die zu öffnenden Ports und das Protokoll an, das für die SKU unterstützt werden muss.  Diese Konfigurationen müssen mit dem virtuellen Netzwerk übereinstimmen, das Sie für das Netzwerk der Lösungs-VM konfiguriert haben. Diese Einstellungen werden während der VM-Bereitstellung wirksam. Die Porteinstellungen können aber geändert werden, nachdem Sie eine SKU veröffentlicht haben. Weitere Informationen finden Sie unter [Öffnen von Ports zu einem virtuellen Computer mit dem Azure-Portal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Die folgenden Standardnetzwerkzuordnungen werden allen VMs hinzugefügt. &emsp; Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Disk Version** (Datenträgerversion)  | Die zugeordnete Lösungs-VM, die anhand der Datenträgerversionsnummer und Datenträger-URL angegeben wird. Die Datenträgerversion muss das [semantische Versionsformat](https://semver.org/) aufweisen: `<major>.<minor>.<patch>`.  Die URL ist der Shared Access Signature-URI, der für die Betriebssystem-VHD erstellt wurde.  Sie können zwar bis zu acht Datenträgerversionen pro SKU hinzufügen, aber nur die höchste Datenträgerversionsnummer für eine SKU wird im Azure Marketplace angezeigt. Die anderen Versionen sind nur über APIs sichtbar.  <!--TD: Add more specific link to API --> <br/> Im Accordion-Abschnitt **New data disk** (Neuer Datenträger) können Sie bis zu 15 Datenträger an Ihre VM anfügen.  Nachdem Sie eine SKU mit einer bestimmten VM-Version und den zugeordneten Datenträgern veröffentlicht haben, kann diese Konfiguration nicht geändert werden.  Wenn der SKU zusätzliche VM-Versionen hinzugefügt werden, müssen diese auch die gleiche Anzahl von Datenträgern unterstützen. <br/> Falls Sie Ihre Azure-basierten VM-Images noch nicht erstellt haben, können Sie dieses Feld später aktualisieren.  Informationen zum Erstellen der zugeordneten VM-Ressource finden Sie im Abschnitt [Erstellen technischer VM-Ressourcen](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Klicken Sie auf **Speichern**, um Ihren Fortschritt zu speichern. Auf der nächsten Registerkarte geben Sie an, ob Ihr Angebot eine [Testversion](./cpp-test-drive-tab.md) unterstützt.


## <a name="additional-pricing-considerations"></a>Weitere Überlegungen zu Preisen

Beim oben beschriebenen Preismodell handelt es sich um eine grundlegende Beschreibung.  Es werden laufend Änderungen vorgenommen, und es können sich Auswirkungen aufgrund von lokalen oder regionalen Steuerbestimmungen und Microsoft-Preisrichtlinien ergeben. 

### <a name="simplified-currency-pricing"></a>Vereinfachte Währungspreise

Ab dem 1. September 2018 steht im Portal ein neuer Abschnitt mit dem Namen **Vereinfachte Währungspreise** zur Verfügung. Microsoft optimiert das Azure Marketplace-Geschäft dadurch, dass besser prognostizierbare Preise und Auflistungen von Ihren Kunden auf der ganzen Welt ermöglicht werden. Zu dieser Optimierung gehört auch, dass weniger Währungen verwendet werden, in denen Microsoft Ihren Kunden Rechnungen stellt.  Weitere Informationen finden Sie unter [Aktualisieren eines vorhandenen VM-Angebots im Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Weitere Informationen zu Steuern und Preisen

* Microsoft klassifiziert einige Länder/Regionen als *Steuererlassländer*.  In diesen Ländern/Regionen nimmt Microsoft die Steuern von den Kunden ein und zahlt (überweist) diese dann an den Staat.  In anderen Ländern/Regionen sind normalerweise die Partner für die Einnahme der Steuern bei ihren Kunden und die Steuerzahlung an den Staat verantwortlich. Wenn Sie in letzteren Ländern/Regionen als Verkäufer tätig werden möchten, müssen Sie in der Lage sein, die dortigen Steuern zu berechnen und zu zahlen.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Die Preise können nicht mehr geändert werden, nachdem ein Angebot online geschaltet wird. Sie können aber weiterhin unterstützte Regionen hinzufügen oder entfernen. 
* Microsoft berechnet dem Kunden zusätzlich zu Ihren festgelegten SKU-Gebühren Standardnutzungsgebühren für die Azure-VM.
* Die Preise werden für alle Regionen in der lokalen Währung mit den zum Zeitpunkt der Preisfestlegung verfügbaren Wechselkursen festgelegt.  <!-- TD: Meaning? - Offer created, published, other? -->
* Um die Preise der jeweiligen Regionen einzeln festzulegen, exportieren Sie die Preiskalkulationstabelle, fügen die benutzerdefinierten Preise ein und führen anschließend den Import durch. 


## <a name="next-steps"></a>Nächste Schritte

Optional können Sie [Testversion](./cpp-test-drive-tab.md)-Informationen angeben, wenn Sie dieses Feature unterstützen; andernfalls stellen Sie [Marketplace](./cpp-marketplace-tab.md)-Daten für Ihr Angebot bereit.
