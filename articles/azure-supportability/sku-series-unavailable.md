---
title: SKU-Serien nicht verfügbar | Microsoft Docs
description: Einige SKU-Serien sind für das ausgewählte Abonnement für diese Region nicht verfügbar.
services: Azure Supportability
documentationcenter: ''
author: stevendotwang
manager: rajatk
editor: ''
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: a57899e36a6716a6fd59cb018119c225b7396c0d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60649110"
---
# <a name="region-or-sku-unavailable"></a>Region oder SKU nicht verfügbar
In diesem Artikel wird beschrieben, wie Sie das Problem beheben können, dass ein Azure-Abonnement keinen Zugriff auf eine Region oder eine VM-SKU hat.

## <a name="symptoms"></a>Symptome

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Wenn Sie einen virtuellen Computer bereitstellen, erhalten Sie eine der folgenden Fehlermeldungen:
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Wenn Sie reservierte VM-Instanzen erwerben, erhalten Sie eine der folgenden Fehlermeldungen:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Bei der Erstellung einer Supportanfrage zur Erhöhung des Kontingents für Computekerne steht eine Region oder eine SKU-Familie nicht zur Auswahl.

## <a name="solution"></a>Lösung
Wir empfehlen Ihnen zunächst, eine alternative Region oder SKU auszuwählen, die Ihren Geschäftsanforderungen entspricht. Wenn Sie keine passende Region oder SKU finden können, erstellen Sie eine [Supportanfrage](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) für die „Abonnementverwaltung“, indem Sie die folgenden Schritte ausführen:


- Wählen Sie auf der Seite „Grundlagen“ als Problemtyp „Abonnementverwaltung“ und dann das Abonnement aus, und klicken Sie auf „Weiter“.

![Blatt "Grundlagen"](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Wählen Sie auf der Seite „Problem“ den Problemtyp „Sonstige allgemeine Fragen“ aus.
- Im Abschnitt „Details“:
  - Geben Sie an, ob Sie virtuelle Computer bereitstellen oder reservierte VM-Instanzen erwerben möchten.
  - Geben Sie die Region, die SKU und die Anzahl der Instanzen virtueller Computer an, die Sie bereitstellen oder erwerben möchten.


![Problem](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Geben Sie Ihre Kontaktdaten ein, und klicken Sie auf „Erstellen“.

![Kontaktinformationen](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Feedback
Wir sind stets offen für Feedback und Vorschläge. Senden Sie uns Ihre [Vorschläge](https://feedback.azure.com/forums/266794-support-feedback). Außerdem erreichen Sie uns über [Twitter](https://twitter.com/azuresupport) oder die [MSDN-Foren](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Weitere Informationen
[Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq)

