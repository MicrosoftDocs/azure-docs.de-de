---
title: Region oder SKU-Serien nicht verfügbar
description: Einige SKU-Serien sind für das ausgewählte Abonnement für diese Region nicht verfügbar. Daher ist unter Umständen eine Supportanfrage für die Abonnementverwaltung erforderlich.
author: stevendotwang
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: 2bbe1dff6bbf8fed481300db95482c46b0e8a7b7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745280"
---
# <a name="region-or-sku-unavailable"></a>Region oder SKU nicht verfügbar

In diesem Artikel wird beschrieben, wie Sie das Problem beheben können, dass ein Azure-Abonnement keinen Zugriff auf eine Region oder eine VM-SKU hat.

## <a name="symptoms"></a>Symptome

Wenn Sie einen virtuellen Computer bereitstellen, erhalten Sie eine der folgenden Fehlermeldungen:

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

Wenn Sie reservierte VM-Instanzen erwerben, erhalten Sie eine der folgenden Fehlermeldungen:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

Bei der Erstellung einer Supportanfrage zur Erhöhung des Kontingents für Computekerne steht eine Region oder eine SKU-Familie nicht zur Auswahl.

## <a name="solution"></a>Lösung

Wir empfehlen Ihnen zunächst, eine alternative Region oder SKU auszuwählen, die Ihren Geschäftsanforderungen entspricht.

Wenn Sie keine passende Region oder SKU finden können, erstellen Sie eine [Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) für die **Abonnementverwaltung**, indem Sie die folgenden Schritte ausführen:

1. Wählen Sie im Menü des [Azure-Portals](https://portal.azure.com) die Option **Hilfe und Support** aus. Klicken Sie anschließend auf **Neue Supportanfrage**.

1. Wählen Sie unter **Grundlegende Einstellungen** für **Problemtyp** die Option **Abonnementverwaltung** aus.

1. Wählen Sie ein **Abonnement** aus, und geben Sie unter **Zusammenfassung** eine kurze Beschreibung ein.

   ![Registerkarte „Grundlegende Einstellungen“ unter „Neue Supportanfrage“](./media/SKU-series-unavailable/support-request-basics.png)

1. Wählen Sie unter **Problemtyp** die Option **Problemtyp auswählen** aus.

1. Wählen Sie unter **Problemtyp auswählen** eine Option aus, beispielsweise **Auf mein Abonnement bzw. meine Ressource konnte nicht zugegriffen werden** > **My issue is not listed above** (Mein Problem ist oben nicht aufgeführt.). Wählen Sie **Speichern** aus.

   ![Angeben eines Problems für die Anfrage](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Klicken Sie auf **Weiter: Lösungen**, um mögliche Lösungen zu untersuchen. Wählen Sie ggf. **Weiter: Details** aus, um fortzufahren.

1. Geben Sie ggf. weitere Informationen sowie Ihre Kontaktinformationen ein.

1. Klicken Sie auf **Überprüfen + erstellen**. Nachdem Sie Ihre Informationen überprüft haben, wählen Sie **Erstellen** aus, um die Anfrage zu erstellen.

## <a name="send-us-your-suggestions"></a>Senden von Vorschlägen

Wir sind stets offen für Feedback und Vorschläge. Senden Sie uns Ihre [Vorschläge](https://feedback.azure.com/forums/266794-support-feedback). Außerdem erreichen Sie uns über [Twitter](https://twitter.com/azuresupport) oder die [Seite mit häufig gestellten Fragen von Microsoft](/answers/products/azure).

## <a name="learn-more"></a>Weitere Informationen

[Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq)