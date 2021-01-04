---
title: Verwalten Ihrer Rechnung und Umstellen vom kostenlosen Testtarif in der Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Administrator Ihre Rechnung verwalten und in Ihrer Azure IoT Central-Anwendung vom kostenlosen Tarif zu einem Standardtarif wechseln.
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 50d0119b08d2c76a5f6111e485408ebcdace83c6
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549020"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Verwalten Ihrer Rechnung in einer IoT Central-Anwendung

In diesem Artikel wird beschrieben, wie Sie als Administrator Ihre Azure IoT Central-Abrechnung verwalten können. Sie können Ihre Anwendung vom kostenlosen Tarif auf einen Standardtarif umstellen und außerdem für Ihren Tarif ein Upgrade oder Downgrade durchführen.

Damit Sie auf den Abschnitt **Verwaltung** zugreifen können, muss Ihnen die Rolle *Administrator* zugewiesen sein, oder Sie müssen eine *benutzerdefinierte Benutzerrolle* haben, die Ihnen das Anzeigen der Abrechnung erlaubt. Wenn Sie eine Azure IoT Central-Anwendung erstellen, wird Ihnen die Rolle **Administrator** automatisch zugewiesen.

## <a name="move-from-free-to-standard-pricing-plan"></a>Wechseln vom Tarif „Free“ zu „Standard“

- Anwendungen, die den kostenlosen Tarif nutzen, sind sieben Tage lang kostenlos und laufen dann ab. Zur Vermeidung von Datenverlusten können Sie sie jederzeit auf einen Standardtarif umstellen, bevor sie ablaufen.
- Anwendungen, die einen Standardtarif nutzen, werden pro Gerät abgerechnet. Dabei sind die ersten beiden Geräte pro Anwendung kostenlos.

Weitere Informationen zu den Preisen finden Sie unter [Azure IoT Central – Preise](https://azure.microsoft.com/pricing/details/iot-central/).

Im Preisabschnitt können Sie Ihre Anwendung vom Tarif „Free“ auf den Tarif „Standard“ umstellen.

Um diesen Self-Service-Prozess durchzuführen, gehen Sie folgendermaßen vor:

1. Wechseln Sie zur Seite **Preise** im Abschnitt **Verwaltung**.

    :::image type="content" source="media/howto-view-bill/freetrialbilling.png" alt-text="Status der Testversion":::

1. Wählen Sie **In kostenpflichtigen Plan konvertieren** aus.

    :::image type="content" source="media/howto-view-bill/convert.png" alt-text="Umwandeln der Testversion":::

1. Wählen Sie die entsprechende Azure Active Directory-Instanz und dann das Azure-Abonnement aus, das für Ihre Anwendung mit kostenpflichtigem Tarif verwendet werden soll.

1. Nach dem Auswählen von **Konvertieren** wird Ihre Anwendung auf einen kostenpflichtigen Tarif umgestellt, und Sie erhalten ab diesem Zeitpunkt Rechnungen.

> [!Note]
> Standardmäßig werden Sie auf den Tarif *Standard 2* umgestellt.

## <a name="how-to-change-your-application-pricing-plan"></a>Ändern des Tarifs für Ihre Anwendung

Anwendungen, die einen Standardtarif nutzen, werden pro Gerät abgerechnet. Dabei sind die ersten beiden Geräte pro Anwendung kostenlos.

Im Preisabschnitt können Sie jederzeit ein Upgrade oder Downgrade Ihres Azure IoT-Tarifs durchführen.

1. Wechseln Sie zur Seite **Preise** im Abschnitt **Verwaltung**.

    :::image type="content" source="media/howto-view-bill/pricing.png" alt-text="Upgrade des Tarifs":::

1. Wählen Sie für das Upgrade oder Downgrade den **Tarif** und dann **Speichern** aus.

## <a name="view-your-bill"></a>Anzeigen Ihrer Rechnung

1. Wählen Sie die entsprechende Azure Active Directory-Instanz und dann das Azure-Abonnement aus, das für Ihre Anwendung mit kostenpflichtigem Tarif verwendet werden soll.

1. Nach dem Auswählen von **Konvertieren** wird Ihre Anwendung auf einen kostenpflichtigen Tarif umgestellt, und Sie erhalten ab diesem Zeitpunkt Rechnungen.

> [!Note]
> Standardmäßig werden Sie auf den Tarif *Standard 2* umgestellt.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie etwas über das Verwalten Ihrer Rechnung in der Azure IoT Central-Anwendung erfahren haben, wird als nächster Schritt empfohlen, dass Sie sich mit dem [Anpassen der Anwendungsbenutzeroberfläche](howto-customize-ui.md) in Azure IoT Central vertraut machen.