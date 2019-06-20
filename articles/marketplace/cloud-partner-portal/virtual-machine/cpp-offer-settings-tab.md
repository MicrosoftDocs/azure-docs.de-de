---
title: Registerkarte „Angebotseinstellungen“ für virtuelle Computer im Cloud-Partnerportal für Azure Marketplace
description: Beschreibt die Registerkarte „Angebotseinstellungen“, die beim Erstellen eines VM-Angebots für den Azure Marketplace verwendet wird.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: d361b6b8b08f9556cd57215ebdf82c1bf69d372d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938149"
---
# <a name="virtual-machine-offer-settings-tab"></a>Registerkarte „Angebotseinstellungen“ für virtuelle Computer

Auf der Seite **Neues Angebot** für virtuelle Computer wird als Erstes die Registerkarte **Angebotseinstellungen** angezeigt.  

![Seite „Neues Angebot“ für virtuelle Computer](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Felder von „Angebotseinstellungen“

Auf der Registerkarte **Angebotseinstellungen** müssen folgende Felder ausgefüllt werden.  Mit einem an den Feldnamen angefügten Sternchen (*) wird angegeben, dass eine Eingabe erforderlich ist. 

|  **Feld**       |     **Beschreibung**                                                          |
|  ---------       |     ---------------                                                          |
| **Angebots-ID\***   | Ein (innerhalb eines Herausgeberprofils) eindeutiger Bezeichner für das Angebot. Dieser Bezeichner ist in Produkt-URLs, Azure Resource Manager-Vorlagen und Abrechnungsberichten sichtbar. Er hat eine maximale Länge von 50 Zeichen und darf ausschließlich alphanumerische Kleinbuchstaben und Bindestriche (-) enthalten, aber nicht mit einem Bindestrich enden. Nachdem ein Angebot live geschaltet wurde, kann dieses Feld nicht mehr geändert werden. <br> Wenn also beispielsweise Contoso ein Angebot mit der Angebots-ID **sample-vm** veröffentlicht, wird ihm die Azure Marketplace-URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` zugewiesen. |
| **Herausgeber\***  | Der eindeutige Bezeichner Ihrer Organisation im Azure Marketplace. Alle Ihre Angebote sollten mit Ihrer Herausgeber-ID verknüpft sein. Nachdem das Angebot gespeichert wurde, kann dieser Wert nicht mehr geändert werden. |
| **Name\***       | Der Anzeigenamen für Ihr Angebot. Dieser Name wird im Azure Marketplace und im Cloud-Partnerportal angezeigt. Er darf aus höchstens 50 Zeichen bestehen. Hier empfiehlt es sich, einen Markennamen mit gutem Wiedererkennungswert für Ihr Produkt zu verwenden. Verwenden Sie hier nicht den Namen Ihrer Organisation, es sei denn, das Produkt wird unter diesem Namen beworben. Wenn Sie dieses Angebot auf anderen Websites und in anderen Publikationen vermarkten, achten Sie darauf, überall exakt den gleichen Namen zu verwenden. |
|   |   |
 
Klicken Sie auf **Speichern**, nachdem Sie alle Felder ausgefüllt haben. 


## <a name="next-steps"></a>Nächste Schritte

Auf der nächsten Registerkarte fügen Sie Ihrem Angebot [SKUs](./cpp-skus-tab.md) hinzu.
