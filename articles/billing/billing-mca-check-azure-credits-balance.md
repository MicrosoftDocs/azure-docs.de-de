---
title: Nachverfolgen des Azure-Gutschriftensaldos für die Microsoft-Kundenvereinbarung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Azure-Gutschriftensaldo für die Microsoft-Kundenvereinbarung überprüfen können.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: amberb
ms.openlocfilehash: 1e8c3e6863b9cd8f2f5ced18a57918c32c865e75
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60372267"
---
# <a name="track-azure-credit-balance-for-microsoft-customer-agreement"></a>Nachverfolgen des Azure-Gutschriftensaldos für die Microsoft-Kundenvereinbarung

Sie können den Azure-Gutschriftensaldo für die Microsoft-Kundenvereinbarung im Azure-Portal überprüfen. Sie verwenden Guthaben, um für Produkte zu bezahlen, die durch die Guthaben abgedeckt sind.

Die Verwendung anderer Produkte, die durch diese Guthaben nicht abgedeckt sind, wird Ihnen in Rechnung gestellt. Ebenso wird Ihnen die Nutzung berechnet, wenn diese Ihr Guthabensaldo überschreitet. Weitere Informationen finden Sie unter [Von Azure-Gutschriften nicht abgedeckte Produkte](#products-that-arent-covered-by-azure-credits).

Dieser Artikel bezieht sich auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. [Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Kundenvereinbarung haben](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-credit-balance-in-the-azure-portal"></a>Überprüfen des Guthabensaldos im Azure-Portal

1. Melden Sie sich beim [Azure-Portal]( https://portal.azure.com) an.

2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot: Suche nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Navigieren Sie zum Abrechnungsprofil. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungskonto auswählen. Wählen Sie im Abrechnungskonto die Option **Abrechnungsprofile** und dann ein Abrechnungsprofil aus.

4. Klicken Sie auf **Azure credits** (Azure-Gutschriften).

5. Auf dieser Seite werden die folgenden Informationen angezeigt:

   ![Screenshot: Guthabensaldo und Transaktionen eines Abrechnungsprofils](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Begriff               | Definition                           |
   |--------------------|--------------------------------------------------------|
   | Geschätzter Saldo  | Geschätztes Guthaben unter Einbeziehung aller in Rechnung gestellten und ausstehenden Transaktionen |
   | Aktueller Saldo    | Guthaben seit der letzten Rechnung, ohne Berücksichtigung der ausstehenden Transaktionen |
   | Transaktionen       | Alle Abrechnungstransaktionen, die sich auf Ihren Azure-Gutschriftensaldo ausgewirkt haben |

   Wenn Ihr geschätzter Saldo auf 0 fällt, wird Ihnen jegliche Nutzung in Rechnung gestellt, auch die der Produkte, die von Guthaben abgedeckt werden.

6. Klicken Sie auf **Credits list** (Guthabenliste), um eine Liste der Guthaben für das Abrechnungsprofil anzuzeigen. Diese Guthabenliste enthält die folgenden Informationen:

   ![Screenshot: Guthabenliste für ein Abrechnungsprofil](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Begriff                 | Definition                           |
   |----------------------|--------------------------------------------------------|
   | `Source`               | Die Erwerbsquelle des Guthabens |
   | Startdatum           | Das Datum, an dem Sie die Gutschrift erhalten haben |
   | Ablaufdatum      | Das Datum, an dem das Guthaben verfällt |
   | Balance              | Der Saldo seit Ihrer letzten Rechnung |
   | Ursprünglicher Betrag      | Der ursprüngliche Guthabenbetrag |
   | Status               | Der aktuelle Status des Guthabens. Die möglichen Status sind „Aktiv“, „Used“ (Verwendet), „Expired“ (Abgelaufen) und „Läuft ab“. |

## <a name="how-credits-are-used-in-microsoft-customer-agreement"></a>Verwendung von Guthaben bei der Microsoft-Kundenvereinbarung

In einem Abrechnungskonto für eine Microsoft-Kundenvereinbarung verwenden Sie Abrechnungsprofile, um Ihre Rechnungen und Zahlungsmethoden zu verwalten. Für jedes Abrechnungsprofil wird eine monatliche Rechnung erstellt. Die Zahlungsmethoden verwenden Sie zum Bezahlen der Rechnung.

Azure-Gutschriften sind eine der Zahlungsmethoden. Sie erhalten von Microsoft Gutschriften wie Werbe- und Servicelevelgutschriften. Diese Gutschriften werden einem Abrechnungsprofil zugewiesen. Wenn für das Abrechnungsprofil eine Rechnung gestellt wird, werden Gutschriften automatisch mit dem in Rechnung gestellten Gesamtbetrag verrechnet. Sie bezahlen den Restbetrag über eine andere Zahlungsmethode wie Scheck oder Überweisung.

## <a name="products-that-arent-covered-by-azure-credits"></a>Von Azure-Gutschriften nicht abgedeckte Produkte

 Die folgenden Produkte werden nicht von Ihren Azure-Gutschriften abgedeckt. Die Abrechnung erfolgt für die Verwendung dieser Produkte unabhängig von Ihrem Guthabensaldo:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop 
- Registrierter Benutzer
- OpenLogic
- Registrierter Benutzer von Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (monatlich)
- Visual Studio Enterprise (jährlich)
- Visual Studio Professional (monatlich)
- Visual Studio Professional (jährlich)
- Azure Marketplace-Produkte
- Azure-Supportpläne

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

- [Understand billing account for Microsoft Customer Agreement (Grundlegendes zum Abrechnungskonto für eine Microsoft-Kundenvereinbarung)](billing-mca-overview.md)
- [Understand terms on your Microsoft Customer Agreement invoice (Grundlegendes zu den Begriffen auf der Rechnung für eine Microsoft-Kundenvereinbarung)](billing-mca-understand-your-invoice.md)