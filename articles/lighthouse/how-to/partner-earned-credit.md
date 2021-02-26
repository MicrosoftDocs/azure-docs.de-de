---
title: Verknüpfen der Partner-ID, um die Auswirkungen auf delegierte Ressourcen nachzuverfolgen
description: Hier erfahren Sie, wie Sie Ihre Partner-ID zuordnen, um vom Partner erworbenes Guthaben (Partner Earned Credit, PEC) für Kundenressourcen zu erhalten, die Sie über Azure Lighthouse verwalten.
ms.date: 01/28/2021
ms.topic: how-to
ms.openlocfilehash: 46338b925b1ad44019c9cc95e4b7f8c0963c07c4
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985970"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Verknüpfen der Partner-ID, um die Auswirkungen auf delegierte Ressourcen nachzuverfolgen 

Wenn Sie Mitglied des [Microsoft Partner Network](https://partner.microsoft.com/) sind, können Sie Ihre Partner-ID mit den Anmeldeinformationen verknüpfen, die für die Verwaltung delegierter Kundenressourcen verwendet werden, sodass Microsoft die Partner ermitteln und würdigen kann, die zum Erfolg von Azure-Kunden beitragen. Dieser Link ermöglicht es [CSP](/partner-center/csp-overview)-Partnern (Cloud Solution Provider), [vom Partner erworbenes Guthaben (Partner Earned Credit, PEC)](/partner-center/partner-earned-credit) für Kunden zu erhalten, die die [Microsoft-Kundenvereinbarung (MCA) unterschrieben](/partner-center/confirm-customer-agreement) haben und den [Azure-Plan](/partner-center/azure-plan-get-started) verwenden.

Wenn Sie das [Onboarding von Kunden mit Angeboten für verwaltete Dienste im Azure Marketplace durchführen](publish-managed-services-offers.md), erfolgt diese Verknüpfung automatisch unter Verwendung der MPN-ID des Partner Center-Kontos, mit dem die Angebote veröffentlicht wurden. Es sind keine weiteren Schritte erforderlich, um die Auswirkungen für diese Kunden nachzuverfolgen.

Wenn Sie das [Onboarding von Kunden mit Azure Resource Manager-Vorlagen durchführen](onboard-customer.md), müssen Sie diese Verknüpfung selbst erstellen. Dazu ist eine [Verknüpfung Ihrer MPN-ID](../../cost-management-billing/manage/link-partner-id.md) mit mindestens einem Benutzerkonto in Ihrem Verwaltungsmandanten erforderlich, das Zugriff auf alle Abonnements hat, für die Sie das Onboarding durchgeführt haben.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Zuordnen Ihrer Partner-ID beim Durchführen des Onboardings für neue Kunden

Wenn Sie das Onboarding von Kunden über Azure Resource Manager-Vorlagen (ARM-Vorlagen) durchführen, müssen Sie wie folgt vorgehen, um Ihre Partner-ID zu verknüpfen (und vom Partner ggf. erworbenes Guthaben zu aktivieren). Für diese Schritte benötigen Sie Ihre [MPN-Partner-ID](/partner-center/partner-center-account-setup#locate-your-mpn-id). Verwenden Sie die in Ihrem Partnerprofil angezeigte **zugeordnete MPN-ID**.

Der Einfachheit halber empfiehlt es sich, ein Dienstprinzipalkonto in Ihrem Mandanten zu erstellen, es mit Ihrer **zugehörigen MPN-ID** zu verknüpfen und dem Konto dann Zugriff auf jeden Kunden zu gewähren, für den Sie mit einer [integrierten Azure-Rolle, die für PEC berechtigt ist](/partner-center/azure-roles-perms-pec), ein Onboarding durchführen.

1. [Erstellen Sie ein Dienstprinzipalkonto](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) in Ihrem Verwaltungsmandanten. In diesem Beispiel verwenden wir den Namen *Provider Automation Account* für diesen Dienstprinzipal.
1. Verwenden Sie dieses Dienstprinzipalkonto, um in Ihrem Verwaltungsmandanten eine [Verknüpfung mit Ihrer zugehörigen MPN-ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) zu erstellen. Dies ist nur einmal erforderlich.
1. Schließen Sie beim [Durchführen des Onboardings eines Kunden mit ARM-Vorlagen](onboard-customer.md) eine Autorisierung ein, die „Provider Automation Account“ als Benutzer mit einer [integrierten Azure-Rolle, die für PEC berechtigt ist](/partner-center/azure-roles-perms-pec), enthält.

Durch diese Schritte wird jeder von Ihnen verwaltete Kundenmandant mit Ihrer Partner-ID verknüpft. „Provider Automation Account“ muss sich nicht authentifizieren oder Aktionen im Kundenmandanten ausführen.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagramm zur Darstellung des Partner-ID-Verknüpfungsvorgangs mit Azure Lighthouse.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Hinzufügen Ihrer Partner-ID zu Kunden, für die bereits ein Onboarding durchgeführt wurde

Wenn Sie bereits ein Onboarding für einen Kunden durchgeführt haben, möchten Sie unter Umständen keine weitere Bereitstellung vornehmen, um den Dienstprinzipal „Provider Automation Account“ hinzuzufügen. Stattdessen können Sie die **zugehörige MPN-ID** einem Benutzerkonto zuordnen, das bereits Zugriff auf Arbeit im Mandanten dieses Kunden hat. Achten Sie darauf, dass dem Konto eine [integrierte Azure-Rolle, die für PEC berechtigt ist](/partner-center/azure-roles-perms-pec), zugewiesen wurde.

Nachdem das Konto in Ihrem Verwaltungsmandanten [mit Ihrer zugehörigen MPN-ID verknüpft](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) wurde, können Sie die Anerkennung für Ihre Auswirkungen auf diesen Kunden nachverfolgen.

## <a name="confirm-partner-earned-credit"></a>Bestätigen des vom Partner erworbenen Guthabens

Sie können [PEC-Details im Azure-Portal anzeigen](/partner-center/partner-earned-credit-explanation#azure-cost-management) und überprüfen, auf welche Kosten der PEC-Vorteil angewendet wurde. Beachten Sie, dass PEC nur für CSP-Kunden gilt, die die MCA unterschrieben haben und den Azure-Plan verwenden.

Falls Sie die obigen Schritte ausgeführt haben und die erwartete Zuordnung nicht angezeigt wird, erstellen Sie eine Supportanfrage im Azure-Portal.

Sie können auch das [Partner Center SDK](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) verwenden und nach `rateOfPartnerEarnedCredit` filtern, um die PEC-Überprüfung für ein Abonnement zu automatisieren.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Microsoft Partner Network](/partner-center/mpn-overview).
- Informieren Sie sich über die [Berechnung und Zahlung von PEC](/partner-center/partner-earned-credit-explanation).
- Führen Sie das [Onboarding von Kunden](onboard-customer.md) in Azure Lighthouse durch.