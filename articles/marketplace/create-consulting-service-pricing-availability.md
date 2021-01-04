---
title: Konfigurieren der Preise und Verfügbarkeit Ihres Beratungsdiensts im Microsoft Partner Center
description: Hier erfahren Sie, wie Sie die Preisdetails und Marktverfügbarkeit Ihres Beratungsdienstangebots im kommerziellen Marketplace von Microsoft im Partner Center konfigurieren.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 6b386238bd759714bc0c8ad81d67c29aa1774aba
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780228"
---
# <a name="how-to-configure-your-consulting-service-pricing-and-availability"></a>Konfigurieren der Preise und Verfügbarkeit Ihres Beratungsdiensts

In diesem Artikel wird erläutert, wie Sie die Marktverfügbarkeit und Preisdetails für Ihr Beratungsdienstangebot im kommerziellen Marketplace von Microsoft definieren.

> [!NOTE]
> Beratungsdienstangebote werden ausschließlich aufgelistet. Alle Transaktionen zwischen Ihnen und Ihren Kunden müssen außerhalb des kommerziellen Marketplace abgewickelt werden.

## <a name="markets"></a>Märkte

Wählen Sie im Abschnitt **Märkte** die Länder oder Regionen aus, in denen Ihr Beratungsdienst verfügbar sein soll.

1. Wählen Sie unter **Märkte** den Link **Märkte bearbeiten** aus.
2. Wählen Sie im angezeigten Dialogfeld die Marktstandorte aus, in denen Sie Ihr Angebot verfügbar machen möchten. Sie müssen mindestens einen von höchstens 141 Märkten auswählen.
3. Wählen Sie **Speichern** aus, um das Dialogfeld zu schließen.

## <a name="preview-audience"></a>Vorschauzielgruppe

Wenn Sie Ihr Beratungsdienstangebot veröffentlichen oder aktualisieren, wird im Partner Center eine Vorschauversion der Auflistung erstellt. Diese Vorschau ist nur für Benutzer sichtbar, die über einen **Ausblendschlüssel** verfügen.

Geben Sie im Feld **Schlüssel ausblenden** eine alphanumerische Zeichenfolge ein, die Sie für den Zugriff auf die Vorschauversion Ihres Angebots verwenden.

## <a name="pricing-informational-only"></a>Preise (nur zu Informationszwecken)

Legen Sie im Abschnitt **Preise** fest, ob es sich um ein kostenloses oder kostenpflichtiges Angebot handelt.

Geben Sie bei kostenpflichtigen Angeboten an, ob es sich um einen festen oder geschätzten Preis handelt. Wenn der Preis geschätzt ist, muss in der Angebotsbeschreibung angegeben werden, welche Faktoren den Preis beeinflussen.

Wenn Sie im Abschnitt **Märkte** ein einzelnes Land oder eine einzelne Region ausgewählt haben, geben Sie den Preis in einer für diesen Markt unterstützten Währung an, und wählen Sie **Entwurf speichern** aus. Eine Liste der unterstützten Währungen finden Sie unter [Geografische Verfügbarkeit und Unterstützung von Währungen für den kommerziellen Marketplace](./marketplace-geo-availability-currencies.md).

Wenn Sie im Abschnitt **Märkte** mehrere Länder oder Regionen ausgewählt haben, geben Sie den Preis in USD (US-Dollar) an und wählen **Entwurf speichern** aus. Der Preis wird im Partner Center in die lokale Währung aller ausgewählten Märkte unter Verwendung der Wechselkurse umgerechnet, die beim Speichern des Entwurfs gültig waren.

Um die Umrechnung zu überprüfen oder um benutzerdefinierte Preise für einen einzelnen Markt festzulegen, müssen Sie die Preistabelle exportieren, ändern und dann wieder importieren:

1. Wählen Sie unter **Preise** den Link **Preisdaten exportieren** aus. Dadurch wird eine Datei auf Ihr Gerät heruntergeladen.
1. Öffnen Sie die Datei „exportedPrice.xlsx“ in Microsoft Excel.
1. In der Tabelle können Sie die Preise und Währungen für jeden Markt anpassen. Eine Liste der unterstützten Währungen finden Sie unter [Geografische Verfügbarkeit und Unterstützung von Währungen für den kommerziellen Marketplace](./marketplace-geo-availability-currencies.md). Wenn Sie fertig sind, speichern Sie die Datei.
1. Wählen Sie im Partner Center unter **Preise** den Link **Preisdaten importieren** aus. Durch das Importieren der Datei werden vorherige Preisinformationen überschrieben.

> [!IMPORTANT]
> Die von Ihnen im Partner Center definierten Preise sind Festpreise und unterliegen keinen Schwankungen im Wechselkurs. Um nach der Veröffentlichung den Preis in mindestens einem Markt zu ändern, müssen Sie Ihr Angebot im Partner Center aktualisieren und erneut übermitteln.

Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="next-steps"></a>Nächste Schritte

* [Überprüfen und veröffentlichen](review-publish-offer.md)