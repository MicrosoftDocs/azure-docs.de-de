---
title: Einstellungen für ein Power BI-App-Angebot | Azure Marketplace
description: Informationen zum Konfigurieren von Angebotseinstellungen für ein Power BI-App-Angebot für den Marketplace von Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: be2c2b4f5d9461aa0fdc6dde89931ed4b6418ced
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943458"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Registerkarte „Angebotseinstellungen“ für Power BI

Beim Öffnen der Seite **Neues Angebot** für Dienst-Apps wird zuerst die Registerkarte **Angebotseinstellungen** angezeigt. Auf dieser Registerkarte geben Sie die primären Bezeichner und den Namen für Ihr Angebot ein. Ein Sternchen (*) kennzeichnet ein erforderliches Feld.

![Registerkarte „Angebotseinstellungen“](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Felder von „Angebotseinstellungen“ 

Auf der Registerkarte **Angebotseinstellungen** müssen Sie Informationen in folgende Felder eingeben. Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

|  Feld        |  BESCHREIBUNG                                                               |
|---------------|----------------------------------------------------------------------------|
| **Angebots-ID\***  | Ein (innerhalb eines Herausgeberprofils) eindeutiger Bezeichner für das Angebot. Dieser Bezeichner ist in Produkt-URLs, Azure Resource Manager-Vorlagen und Abrechnungsberichten sichtbar. Die maximale Länge beträgt 50 Zeichen. Er darf nur aus klein geschriebenen alphanumerischen Zeichen und Bindestrichen (-) bestehen. Er darf nicht mit einem Bindestrich enden. Dieser Bezeichner kann nicht mehr geändert werden, nachdem das Angebot live geschaltet wurde. Wenn Contoso ein Angebot mit der Angebots-ID `sample-SvcApp` veröffentlicht, wird dem Angebot die AppSource-URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp` zugewiesen.      |
| **Herausgeber\*** | Der eindeutige Bezeichner Ihrer Organisation in [AppSource](https://appsource.microsoft.com). Alle Ihre Angebote sollten mit Ihrer Herausgeber-ID verknüpft sein. Dieser Wert kann nicht mehr geändert werden, nachdem das Angebot gespeichert wurde.                         |
| **Name\***      | Der Anzeigenamen für Ihr Angebot. Dieser Name wird in AppSource und im Cloud-Partnerportal angezeigt. Die maximale Länge beträgt 50 Zeichen. Verwenden Sie einen Markennamen mit Wiedererkennungswert für Ihr Produkt. Geben Sie hier nicht den Namen Ihrer Organisation ein, es sei denn, die App wird unter diesem Namen vermarktet. Verwenden Sie immer denselben Namen, wenn Sie dieses Angebot auf anderen Websites und in Veröffentlichungen bereitstellen.    <br/>Wenn Sie ein Angebot während des Vorschauzeitraums von Power BI-Apps freigeben, fügen Sie die Zeichenfolge `(Preview)` an den Namen Ihrer Anwendung an, z.B. `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Nächste Schritte

Auf der nächsten Registerkarte geben Sie [technische Informationen](./cpp-technical-info-tab.md) für Ihr Angebot an.
