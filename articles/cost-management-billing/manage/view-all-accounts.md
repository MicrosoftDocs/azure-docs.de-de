---
title: Anzeigen Ihrer Abrechnungskonten im Azure-Portal
description: Hier erfahren Sie, wie Sie Ihre Abrechnungskonten im Azure-Portal anzeigen. Lesen Sie Bereichsinformationen zu Enterprise Agreements, Microsoft-Kundenvereinbarungen und Microsoft-Partnervereinbarungen.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: banders
ms.openlocfilehash: a9e3297ad055157d552424ecc48db6801f43b220
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929954"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Abrechnungskonten und -bereiche im Azure-Portal

Ein Abrechnungskonto wird erstellt, wenn Sie sich für die Nutzung von Azure registrieren. Sie verwenden Ihr Abrechnungskonto zum Verwalten Ihrer Rechnungen und Zahlungen sowie zum Nachverfolgen von Kosten. Sie können über Zugriff auf mehrere Abrechnungskonten verfügen. Es kann beispielsweise sein, dass Sie sich für Ihre persönlichen Projekte für Azure registriert haben. Unter Umständen können Sie auch über das Enterprise Agreement Ihrer Organisation oder über eine Microsoft-Kundenvereinbarung zugreifen. In diesen Szenarien verfügen Sie dann jeweils über ein separates Abrechnungskonto.

Für das Azure-Portal werden die folgenden Arten von Abrechnungskonten unterstützt:

- **Microsoft Online Services-Programm**: Ein Abrechnungskonto für ein Microsoft Online Services-Programm wird erstellt, wenn Sie sich über die Azure-Website für Azure registrieren. Beispiele hierfür sind die Registrierung für ein [Kostenloses Azure-Konto](https://azure.microsoft.com/offers/ms-azr-0044p/), ein [Angebot mit nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) oder als [Visual Studio-Abonnent](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise Agreement**: Ein Abrechnungskonto für ein Enterprise Agreement wird erstellt, wenn Ihre Organisation ein [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) für die Nutzung von Azure unterzeichnet. Ein Enterprise Agreement unterstützt maximal 2.000 Abonnements.

- **Microsoft-Kundenvereinbarung**: Ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung wird erstellt, wenn Ihre Organisation eine Microsoft-Kundenvereinbarung über einen Microsoft-Vertreter unterzeichnet. Einige Kunden in ausgewählten Regionen, die sich über die Azure-Website für ein [Angebot mit nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) oder ein [kostenloses Azure-Konto](https://azure.microsoft.com/offers/ms-azr-0044p/) registrieren, verfügen möglicherweise auch über ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. Eine Microsoft-Kundenvereinbarung für eine Einzelperson unterstützt maximal 20 Abonnements. Bei einer Microsoft-Kundenvereinbarung für ein Unternehmen gilt keine Beschränkung für die Anzahl von Abonnements. Weitere Informationen finden Sie unter [Erste Schritte mit Ihrem Abrechnungskonto für eine Microsoft-Kundenvereinbarung](../understand/mca-overview.md).

- **Microsoft-Partnervereinbarung**: Ein Abrechnungskonto für eine Microsoft-Partnervereinbarung wird für CSP-Partner (Cloud Solution Provider) erstellt, mit dem sie ihre Kunden in der neuen Handelsumgebung verwalten können. Partner benötigen mindestens einen Kunden mit einem [Azure-Plan](/partner-center/purchase-azure-plan), damit sie das Abrechnungskonto im Azure-Portal verwalten können. Weitere Informationen finden Sie unter [Erste Schritte mit Ihrem Abrechnungskonto für eine Microsoft-Partnervereinbarung](../understand/mpa-overview.md).

Informationen zur Ermittlung des Typs Ihres Abrechnungskontos finden Sie unter [Überprüfen des Typs Ihres Abrechnungskontos](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Bereiche für Abrechnungskonten
Ein Bereich ist ein Knoten in einem Abrechnungskonto, den Sie zum Anzeigen und Verwalten der Abrechnung verwenden. Dort verwalten Sie Abrechnungsdaten, Zahlungen und Rechnungen und führen allgemeine Kontoverwaltungsaufgaben aus.

Wenn Sie keinen Zugriff zum Anzeigen oder Verwalten von Abrechnungskonten haben, haben Sie wahrscheinlich keine Zugriffsberechtigung. Sie können Ihren Abrechnungskontoadministrator bitten, Ihnen Zugriff zu gewähren. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Verwalten des Zugriffs auf Abrechnungsinformationen für Azure](manage-billing-access.md)
- [Grundlegendes zu Verwaltungsrollen für Microsoft-Kundenvereinbarungen in Azure](understand-mca-roles.md)
- [Verwalten von Azure Enterprise-Rollen](understand-ea-roles.md)

### <a name="microsoft-online-services-program"></a>Microsoft Online Services-Programm

![Screenshot von MOSP-Hierarchie](./media/view-all-accounts/mosp-hierarchy.png)

|`Scope`  |Definition  |
|---------|---------|
|Abrechnungskonto     | Eine Vereinbarung, die ein Kunde für die Verwendung von Azure akzeptiert. Sie umfasst mindestens ein Abonnement.  |
|Subscription     |  Stellt eine Gruppierung von Azure-Ressourcen dar. In diesem Bereich wird die Rechnung generiert. Diesem Bereich werden weitere Abrechnungsinformationen wie Zahlungsmethoden und Nutzungsadresse zugeordnet.|

### <a name="enterprise-agreement"></a>Enterprise Agreement

![Screenshot von EA-Hierarchie](./media/view-all-accounts/ea-hierarchy.png)

|`Scope`  |Definition  |
|---------|---------|
|Abrechnungskonto    | Stellt eine Enterprise Agreement-Registrierung dar. Sie umfasst mindestens eine Abteilung und ein Konto. In diesem Bereich wird die Rechnung generiert. |
|Department     |  Optionale Gruppierung von Konten zum Segmentieren von Kosten in logische Gruppierungen und Festlegen des Budgets.     |
|Konto     |  Stellt einen einzigen Kontobesitzer dar. Kontobesitzer sind zum Erstellen und Verwalten von Azure-Abonnements berechtigt, die für die Registrierung abgerechnet werden. |

### <a name="microsoft-customer-agreement"></a>Microsoft-Kundenvereinbarung

![Screenshot von MCA-Hierarchie](./media/view-all-accounts/mca-hierarchy.png)

|`Scope`  |Aufgaben  |
|---------|---------|
|Abrechnungskonto     |   Stellt eine Vereinbarung dar, die ein Kunde für die Verwendung von Produkten und Diensten von Microsoft akzeptiert. Sie umfasst mindestens ein Abrechnungsprofil. |
|Abrechnungsprofil     |   Stellt eine Rechnung und die zugehörigen Abrechnungsinformationen wie Zahlungsmethoden und Abrechnungsadresse dar. Es enthält mindestens einen Rechnungsabschnitt. |
|Rechnungsabschnitt     |   Stellt eine Gruppierung von Kosten in einer Rechnung dar. Diesem Bereich werden Azure-Abonnements und andere Käufe, z. B. von Azure Marketplace-Produkten und App-Quellprodukten, zugeordnet.    |

### <a name="microsoft-partner-agreement"></a>Microsoft-Partnervereinbarung

![Screenshot von MPA-Hierarchie](./media/view-all-accounts/mpa-hierarchy.png)

|`Scope`  |Aufgaben  |
|---------|---------|
|Abrechnungskonto     |   Eine Partnervereinbarung, mit der die Microsoft-Produkte und -Dienste von Kunden in der neuen Handelsumgebung verwaltet werden. Sie umfasst mindestens ein Abrechnungsprofil und einen Kunden.   |
|Abrechnungsprofil     |   Eine Rechnung für eine Währung.     |
|Kunde    |   Ein Kunde für einen Cloud Solution Provider-Partner (CSP).  Diesem Bereich werden Azure-Abonnements und andere Käufe, z. B. von Azure Marketplace-Produkten und App-Quellprodukten, zugeordnet.  |
|Reseller    |   Reseller, der Dienste für einen Kunden bereitstellt. Dies ist ein optionales Feld für ein Abonnement, das nur für indirekte Anbieter im CSP-Modell mit zwei Ebenen anwendbar ist.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Wechseln des Abrechnungsbereichs im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Wählen Sie auf der Übersichtsseite **Wechseln Sie den Bereich** aus.

   ![Screenshot von Abrechnungsbereichen](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Wenn Sie nur auf einen Bereich zugreifen können, wird die Option „Wechseln Sie den Bereich“ nicht angezeigt.

4. Wählen Sie einen Bereich aus, um Details dazu anzuzeigen.

   ![Screenshot von Abrechnungsbereichen, die Sie auswählen können, um Details anzuzeigen](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Überprüfen Sie den Typ Ihres Abrechnungskontos.
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie mit dem [Analysieren von Kosten](../costs/quick-acm-cost-analysis.md) beginnen.