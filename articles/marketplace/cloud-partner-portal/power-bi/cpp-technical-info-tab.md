---
title: Technische Informationen für ein Power BI-App-Angebot | Azure Marketplace
description: Informationen zum Konfigurieren von Feldern des Typs „Technische Informationen“ für ein Power BI-App-Angebot für den Marketplace von Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 15f4e2a76724a70c15411dea767cc9bc433e4d4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943216"
---
# <a name="power-bi-apps-technical-info-tab"></a>Registerkarte „Technische Informationen“ für Power BI-Apps

Verwenden Sie auf der Seite **Neues Angebot** die Registerkarte **Technische Informationen**, um die URL des Power BI-Installationspakets und andere Informationen bereitzustellen, die Sie zum Überprüfen des neuen Angebots benötigen.  Bei ihrer Erstveröffentlichung sind alle Power BI-Apps kostenlos und stehen in AppSource zum Download bereit. Aus diesem Grund können Sie keine Stock Keeping Einheiten (SKUs) für diesen Angebotstyp definieren.

![Registerkarte „Technische Informationen“](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Felder auf der Registerkarte „Technische Informationen“ 

Füllen Sie auf der Registerkarte **Technische Informationen** die in der folgenden Tabelle beschriebenen Felder aus. Ein Sternchen (*) am Ende einer Feldbezeichnung bedeutet, dass das Feld erforderlich ist.

|        Feld          |  BESCHREIBUNG                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Installations-URL\***     | Power BI generiert diese URL, wenn Sie die App veröffentlichen und in die Produktion höherstufen.  Weitere Informationen finden Sie unter [Veröffentlichen von Apps mit Dashboards und Berichten in Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Überprüfungsanweisungen**  |  Fügen Sie wahlweise Anweisungen (bis zu 3.000 Zeichen) hinzu, um das Microsoft-Überprüfungsteam beim Konfigurieren und Testen Ihrer App sowie beim Herstellen einer Verbindung zu Ihrer App zu unterstützen. Fügen Sie typische Konfigurationseinstellungen, Konten, Parameter oder andere Informationen ein, die zum Testen der Option „Daten verbinden“ verwendet werden können. Diese Informationen werden nur dem Überprüfungsteam angezeigt und dienen nur zu Überprüfungszwecken.  |
| **Wird diese App als Power BI-Inhaltspaket erstellt?** | Dieses Feld wird derzeit nur intern verwendet. Übernehmen Sie die Standardeinstellung **Nein**. Wenn Sie die Einstellung in **Ja** ändern, beenden Sie möglicherweise den Veröffentlichungsprozess.  |  
|  |  |


## <a name="next-steps"></a>Nächste Schritte

Stellen Sie auf der nächsten Registerkarte [Storefrontdetails](./cpp-storefront-details-tab.md) Marketinginformationen und rechtliche Hinweise zu Ihrer App bereit.

