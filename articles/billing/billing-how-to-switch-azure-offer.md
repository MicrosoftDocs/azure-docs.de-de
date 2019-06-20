---
title: Ändern des Azure-Abonnementangebots | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihr Azure-Abonnement ändern und über das Azure-Kontocenter zu einem anderen Angebot wechseln.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 11d3deb029ef0d8478844b64c54db7982ed1091e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64570982"
---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>Ändern Ihres Azure-Abonnements mit nutzungsbasierter Bezahlung in ein anderes Angebot

Als Benutzer mit [nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) können Sie Ihr Abonnement im [Kontocenter](https://account.windowsazure.com/Subscriptions) auf ein anderes Angebot umstellen. Mithilfe dieses Features können Sie beispielsweise von den [monatlichen Gutschriften für Visual Studio-Abonnenten](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) profitieren. 

**Sie möchten nur Ihre kostenlose Testversion aktualisieren?** Informationen dazu finden Sie unter [Aktualisieren auf nutzungsbasierte Bezahlung](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Unterstützte Umstellungen:

| From | To |
| --- | --- |
| Nutzungsbasierte Bezahlung |[Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| Nutzungsbasierte Bezahlung |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| Nutzungsbasierte Bezahlung |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| Nutzungsbasierte Bezahlung |[MSDN-Plattformen](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| Nutzungsbasierte Bezahlung |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| Nutzungsbasierte Bezahlung |[Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Um Informationen zu weiteren Angebotsänderungen zu erhalten, [wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Umstellen des Abonnementangebots

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Melden Sie sich beim [Azure-Kontocenter](https://account.windowsazure.com/Subscriptions)an.
1. Wählen Sie Ihr Abonnement mit nutzungsbasierter Bezahlung aus.
1. Klicken Sie auf **Zu einem anderen Angebot wechseln**. Die Schaltfläche ist nur verfügbar, wenn Sie die nutzungsbasierte Bezahlung verwenden und der erste Abrechnungszeitraum abgelaufen ist.

   ![Beachten Sie rechts auf der Seite die Schaltfläche „Angebot wechseln“.](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Wählen Sie das gewünschte Angebot** aus der Liste der Angebote aus, auf die Ihr Abonnement umgestellt werden kann. Diese Liste variiert je nach den Mitgliedschaften, denen Ihr Konto zugeordnet ist. Ist kein Angebot verfügbar, sehen Sie sich die [Liste mit verfügbaren Angeboten an, auf die Sie umstellen können](#whats-supported), und vergewissern Sie sich, dass Sie über die richtigen Mitgliedschaften verfügen. 

   ![Wählen Sie ein Angebot, zu dem Sie wechseln möchten.](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. Abhängig von dem Angebot, zu dem Sie wechseln möchten, wird möglicherweise ein Hinweis zu den Auswirkungen dieses Wechsels angezeigt. Lesen Sie diese Liste aufmerksam durch, und befolgen Sie die Anweisungen, bevor Sie fortfahren.

   ![Lesen Sie die Hinweise.](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Sie können Ihr Abonnement umbenennen. Standardmäßig verwenden wir den Namen des neuen Angebots. Klicken Sie auf **Angebot wechseln** , um den Vorgang abzuschließen.

   ![Klicken Sie auf die grüne Schaltfläche.](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Erfolg! Ihr Abonnement wird jetzt auf das neue Angebot umgestellt.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="what-is-an-azure-offer"></a>Was ist ein Azure-Angebot?

Ein Azure-Angebot ist der *Typ* von Azure-Abonnement, das Sie besitzen. Beispielsweise sind [Nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/) und [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) Azure-Angebote. Für jedes Angebot gelten andere [Bedingungen](https://azure.microsoft.com/support/legal/offer-details/), und einige weisen besondere Vorteile auf. Das Angebot für Ihr Abonnement finden Sie auf der Abonnementseite des Kontocenters. Klicken Sie auf den Angebotsnamen, um weitere Details anzuzeigen.

   ![Klicken Sie im Kontocenter auf den Link für Angebote, um weitere Details anzuzeigen](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Warum wird die Schaltfläche nicht angezeigt?

Wenn die Schaltfläche **Zu einem anderen Angebot wechseln** nicht angezeigt wird, kommen folgende Gründe infrage:

* Sie verwenden nicht die [nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/). Derzeit können nur Abonnements mit nutzungsbasierter Bezahlung auf ein anderes Angebot umgestellt werden.
  * Wenn Sie eine [kostenlose Testversion](https://azure.microsoft.com/free/) besitzen, erfahren Sie, wie Sie ein [Upgrade auf eine nutzungsbasierte Version](billing-upgrade-azure-subscription.md) durchführen.
  * Wenn Sie von einem anderen Abonnement wechseln möchten, [wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Sie befinden sich weiterhin im ersten Abrechnungszeitraum und müssen das Ende des ersten Abrechnungszeitraums abwarten, bevor Sie zwischen Angeboten wechseln können.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Warum wird die Meldung „Zurzeit sind keine Angebote in Ihrer Region bzw. Ihrem Land verfügbar.“ angezeigt?

* Möglicherweise sind Sie nicht für Angebotsumstellungen berechtigt. Überprüfen Sie die [Liste der verfügbaren Umstellungen](#whats-supported), und stellen Sie sicher, dass Sie die richtigen Vorteile mit Visual Studio oder Bizspark aktiviert haben.
* Einige Angebote sind möglicherweise nicht in allen Ländern/Regionen verfügbar.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Welche Auswirkungen hat ein Wechsel zwischen Azure-Angeboten auf meinen Dienst und meine Abrechnung?

Hier wird ausführlich erläutert, was geschieht, wenn Sie Azure-Angebote im Kontocenter wechseln.

#### <a name="no-service-downtime"></a>Keine Dienstunterbrechung

Für die dem Abonnement zugeordneten Benutzer entstehen keine Ausfallzeiten. Allerdings können für das Angebot, zu dem Sie wechseln möchten, Einschränkungen gelten. Manche Angebote können beispielsweise nicht in der Produktion verwendet werden. Daher müssten Sie Produktionsressourcen in ein anderes Abonnement verschieben.

#### <a name="quota-increases-are-reset"></a>Zurücksetzen von Kontingenterhöhungen

Beim Wechseln von Angeboten werden alle [Grenzwerte oder Kontingenterhöhungen oberhalb des Standardlimits](../azure-supportability/resource-manager-core-quotas-request.md) zurückgesetzt. Es tritt selbst dann keine Dienstunterbrechung auf, wenn Ihre Ressourcen über dem Standardgrenzwert liegen. Wenn Sie beispielsweise 200 Kerne für Ihr Abonnement verwenden, wird Ihr Kernkontingent durch einen Angebotswechsel wieder auf den Standardwert von 20 Kernen zurückgesetzt. Die VMs, die 200 Kerne verwenden, sind nicht betroffen und würden weiterhin ausgeführt werden. Wenn Sie allerdings keine weitere Anfrage zur Kontingenterhöhung stellen, können Sie keine weiteren Kerne bereitstellen.

#### <a name="billing"></a>Abrechnung

An dem Tag, an dem Sie das Angebot wechseln, wird für alle ausstehenden Gebühren eine Rechnung generiert. Anschließend wird Ihr Abonnement gemäß den Preisinformationen für das neue Angebot abgerechnet. Der Stichtag Ihrer Abonnementabrechnung wird in das Datum geändert, an dem Sie das Angebot gewechselt haben. Die Nutzungs- und Abrechnungsdaten von vor der Angebotsänderung werden nicht beibehalten. Daher empfiehlt es sich, vor dem Wechsel eine Kopie davon herunterzuladen.

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Kann ich von der nutzungsbasierten Bezahlung zu [Cloudlösungsanbieter](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (Cloud Solution Provider, CSP) oder [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA) migrieren?

* Informationen zum Migrieren zu CSP finden Sie unter [Migrieren von Azure-Abonnements von nutzungsbasierter Bezahlung zu Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Um zu EA zu migrieren, muss Ihr Registrierungsadministrator Ihr Konto in EA hinzufügen. Führen Sie die Anweisungen in der Einladungs-E-Mail aus, um Ihre Abonnements in die EA-Registrierung zu verschieben. Weitere Informationen finden Sie unter [Associate an Existing Account](https://ea.azure.com/helpdocs/associateExistingAccount) (Zuordnen eines vorhandenen Kontos) im EA-Portal.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Kann ich Daten und Dienste in ein neues Abonnement migrieren?

* Sie können die Ressourcen direkt in das neue Abonnement migrieren. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/resource-group-move-resources.md).
* Informationen zum Übertragen eines Azure-Abonnements und des gesamten Inhalts an eine andere Person finden Sie unter [Übertragen eines Azure-Abonnements](billing-subscription-transfer.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
