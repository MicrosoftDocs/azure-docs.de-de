---
title: 'Schnellstart: Beschaffen einer Telefonnummer über Azure Communication Services'
description: Es wird beschrieben, wie Sie über das Azure-Portal eine Communication Services-Telefonnummer erwerben.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: 3c7f171a2b4957b687831ec2777ecc40b5913a15
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938646"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Schnellstart: Beschaffen einer Telefonnummer über das Azure-Portal

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Steigen Sie in Azure Communication Services ein, indem Sie über das Azure-Portal eine Telefonnummer erwerben.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Eine aktive Communication Services-Ressource.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Beschaffen einer Telefonnummer

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Communication Services-Ressource, um mit der Bereitstellung von Telefonnummern zu beginnen.

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Screenshot: Hauptseite einer Communication Services-Ressource":::

### <a name="getting-new-phone-numbers"></a>Beschaffen von neuen Telefonnummern

Navigieren Sie im Ressourcenmenü zum Blatt **Telefonnummern**.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Screenshot: Telefonnummernseite einer Communication Services-Ressource":::

Klicken Sie auf die Schaltfläche **Abrufen**, um den Assistenten zu starten. Im Assistenten werden auf dem Blatt **Telefonnummern** einige Fragen gestellt, um die Telefonnummer zu ermitteln, die für Ihr Szenario am besten geeignet ist. 

Zuerst müssen Sie eine Auswahl für **Land/Region** treffen, um anzugeben, wo die Telefonnummer bereitgestellt werden soll. Nach der Auswahl des Lands bzw. der Region müssen Sie den **Anwendungsfall** auswählen, der Ihre Anforderungen am besten erfüllt. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Screenshot: Ansicht „Telefonnummer erhalten“":::

### <a name="select-your-phone-number-features"></a>Auswählen der Telefonnummernfeatures

Das Konfigurieren Ihrer Telefonnummer ist in zwei Schritte unterteilt: 

1. Auswahl des [Nummerntyps](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Auswahl der [Telefonnummernfeatures](../../concepts/telephony-sms/plan-solution.md#phone-number-features-in-azure-communication-services)

Sie haben die Wahl zwischen zwei Telefonnummerntypen: **Geografisch** und **Gebührenfrei**. Nach der Wahl eines Nummerntyps können Sie das Feature auswählen.

In diesem Beispiel wurde der Nummerntyp **Gebührenfrei** mit den Features **Outbound calling** (Ausgehende Anrufe) und **Inbound and Outbound SMS** (Ein- und ausgehende SMS) ausgewählt.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Screenshot: Ansicht für die Featureauswahl":::

Klicken Sie hier auf die Schaltfläche **Weiter: Nummern** (unten auf der Seite), um die Telefonnummern anzupassen, die Sie bereitstellen möchten.

### <a name="customizing-phone-numbers"></a>Anpassen von Telefonnummern

Auf der Seite **Nummern** können Sie die Telefonnummern anpassen, die Sie bereitstellen möchten.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Screenshot: Seite für die Nummernauswahl":::

> [!NOTE]
> In dieser Schnellstartanleitung wird der Anpassungsvorgang für den Nummerntyp **Gebührenfrei** gezeigt. Der Vorgang unterscheidet sich etwas, wenn Sie den Nummerntyp **Geografisch** ausgewählt haben, das Endergebnis ist jedoch gleich.

Treffen Sie in der Liste mit den verfügbaren Ortsvorwahlen eine Auswahl für **Ortsvorwahl**, und geben Sie die Menge an, die Sie bereitstellen möchten. Klicken Sie anschließend auf **Suchen**, um nach Nummern zu suchen, die die ausgewählten Anforderungen erfüllen. Die Telefonnummern, die Ihre Anforderungen erfüllen, werden zusammen mit den monatlichen Kosten angezeigt.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Screenshot: Seite für die Nummernauswahl mit reservierten Nummern":::

> [!NOTE]
> Die Verfügbarkeit hängt davon ab, welche Optionen Sie für Nummerntyp, Standort und Features ausgewählt haben.
> Die Nummern werden im Zeitraum bis zum Ablauf der Transaktion für kurze Zeit reserviert. Wenn die Transaktion abläuft, müssen Sie die Nummern erneut auswählen.

Klicken Sie am unteren Rand der Seite auf die Schaltfläche **Weiter: Zusammenfassung**, um eine Zusammenfassung des Kaufs anzuzeigen und Ihre Bestellung aufzugeben.

### <a name="place-order"></a>Aufgeben der Bestellung

Auf der Seite mit der Zusammenfassung werden noch einmal der Nummerntyp, die Features, die Telefonnummern und die monatlichen Gesamtkosten für die Bereitstellung der Telefonnummern angezeigt.

> [!NOTE]
> Bei den angezeigten Preisen handelt es sich um die **monatlichen Gebühren**. Dies sind die Kosten für das Leasen der ausgewählten Telefonnummer an Sie. Nicht in dieser Anzeige enthalten sind die **Kosten für die nutzungsbasierte Bezahlung**, die beim Tätigen bzw. Empfangen von Anrufen anfallen. Die Preislisten finden Sie [hier](../../concepts/pricing.md). Diese Kosten richten sich nach dem Nummerntyp und den angerufenen Zielen. Die Preise pro Minute für einen Anruf von einer Nummer in der Region Seattle bei einer Nummer in der Region New York und für einen Anruf von derselben Nummer bei einer Mobiltelefonnummer im Vereinigten Königreich können sich beispielsweise unterscheiden.

Klicken Sie abschließend unten auf der Seite auf **Bestellen**, um die Bestellung zu bestätigen.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Screenshot: Seite mit der Zusammenfassung zu Nummerntyp, Features, Telefonnummern und monatlichen Gesamtkosten":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Suchen nach Ihren Telefonnummern im Azure-Portal

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Azure Communication Services-Ressource:

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Screenshot: Hauptseite einer Communication Services-Ressource":::

Wählen Sie im Menü das Blatt „Telefonnummern“ aus, um Ihre Telefonnummern zu verwalten.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Screenshot: Telefonnummernseite einer Communication Services-Ressource":::

> [!NOTE]
> Es kann einige Minuten dauern, bis die bereitgestellten Nummern auf dieser Seite angezeigt werden.


### <a name="customizing-phone-numbers"></a>Anpassen von Telefonnummern

Auf der Seite **Nummern** können Sie eine Telefonnummer auswählen, um sie zu konfigurieren.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="Screenshot: Seite zum Aktualisieren von Features":::

Wählen Sie aus den verfügbaren Optionen die gewünschten Features aus, und klicken Sie anschließend auf **Bestätigen**, um Ihre Auswahl anzuwenden.

## <a name="troubleshooting"></a>Problembehandlung

Häufig gestellte Fragen und Probleme:

- Derzeit wird der Erwerb von Telefonnummern nur in den USA unterstützt. Als Grundlage hierfür gilt die Rechnungsadresse des Abonnements, dem die Ressource zugeordnet ist. Derzeit ist es nicht möglich, eine Ressource in ein anderes Abonnement zu verschieben.

- Wenn eine Telefonnummer freigegeben wurde, kann sie bis zum Ende des Abrechnungszeitraums nicht freigegeben oder erneut erworben werden.

- Wenn eine Communication Services-Ressource gelöscht wird, werden die Telefonnummern, die dieser Ressource zugeordnet sind, gleichzeitig automatisch freigegeben.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erwerben einer Telefonnummer
> * Verwalten Ihrer Telefonnummer
> * Freigeben einer Telefonnummer

> [!div class="nextstepaction"]
> [Senden einer SMS](../telephony-sms/send.md)
> [Erste Schritte für das Tätigen von Anrufen](../voice-video-calling/getting-started-with-calling.md)
