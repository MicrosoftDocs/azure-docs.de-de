---
title: Erstellen und Verwalten von Azure IoT Central-Anwendungen als CSP | Microsoft-Dokumentation
description: Sie erfahren, wie Sie als CSP eine Azure IoT Central-Anwendung im Auftrag des Kunden erstellen.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/03/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 43fe0047022173cc20f58688920fa95b95cc6c22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65473438"
---
# <a name="as-a-csp-create-and-manage-an-azure-iot-central-application-on-behalf-of-your-customer"></a>Erstellen und verwalten Sie eine Azure IoT Central-Anwendung im Auftrag des Kunden als CSP. 

Das Microsoft Cloud-Lösungsanbieter-Programm (CSP, Cloud Solution Provider) ist ein Programm für Microsoft-Vertriebspartner. Unseren Channelpartnern steht damit ein einziges Programm zum Verkauf aller kommerziellen Onlinedienste von Microsoft zur Verfügung. Erfahren Sie mehr über das [Cloud-Lösungsanbieter-Programm](https://partner.microsoft.com/cloud-solution-provider).

Als CSP können Sie Microsoft Azure IoT Central-Anwendungen im Auftrag Ihrer Kunden über die [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home) erstellen und verwalten. Wenn Azure IoT Central-Anwendungen im Auftrag von CSP-Kunden erstellt werden, verwalten CSPs die Abrechnung für Kunden wie für andere CSP-verwaltete Azure-Dienste. Eine Gebühr für Azure IoT Central wird in Ihrer Gesamtrechnung im Microsoft Partner Center angezeigt.

Melden Sie sich zum Einstieg bei Ihrem Konto im Microsoft-Partnerportal an, und wählen Sie einen Kunden aus, für den Sie eine Azure IoT Central-Anwendung erstellen möchten. Navigieren Sie vom linken Navigationsbereich zur Dienstverwaltung für den Kunden.

![Microsoft Partner Center, Kundenansicht](media/howto-create-application-csp/image1.png)

Azure IoT Central wird als zur Verwaltung verfügbarer Dienst aufgeführt. Wählen Sie auf der Seite den Azure IoT Central-Link aus, um für diesen Kunden neue Anwendungen zu erstellen oder vorhandene Anwendungen zu verwalten.

![Azure IoT Central, verfügbar zur Verwaltung](media/howto-create-application-csp/image2.png)

Sie gelangen zur „Anwendungs-Manager“-Seite von Azure IoT Central. Azure IoT Central beachtet den Kontext, dass Sie vom Microsoft Partner Center gekommen sind, um diesen bestimmten Kunden zu verwalten. Dies wird in der Kopfzeile der Seite „Anwendungs-Manager“ bestätigt. Von hier aus können Sie entweder zu einer zuvor für diesen Kunden erstellten Anwendung navigieren, um sie zu verwalten, oder eine neue Anwendung für den Kunden erstellen.

![Erstellen des Managers für CSPs](media/howto-create-application-csp/image3.png)

Um eine Azure IoT Central-Anwendung zu erstellen, wählen Sie die Kachel **Neue Anwendung** aus. Dadurch wird die Seite „Anwendungserstellung“ geladen. Sie müssen alle Felder auf dieser Seite ausfüllen und dann **Erstellen** auswählen. Unten finden Sie weitere Informationen zu den einzelnen Feldern.

![Erstellen einer Anwendungsseite für CSPs](media/howto-create-application-csp/image4.png)

![Erstellen einer Anwendungsseite für CSPs](media/howto-create-application-csp/image4-1.png)

## <a name="payment-plan"></a>Zahlungsplan

Sie können als CSP nur Anwendungen mit nutzungsbasierter Bezahlung erstellen. Um Ihrem Kunden Azure IoT Central vorzustellen, können Sie separat eine Testanwendung erstellen. Auf der Seite [IoT Central – Preise](https://azure.microsoft.com/pricing/details/iot-central/) erfahren Sie mehr über eine kostenlose Testversion und Anwendungen mit nutzungsbasierter Bezahlung.

## <a name="application-name"></a>Anwendungsname

Der Name Ihrer Anwendung wird auf der Seite **Anwendungs-Manager** und in jeder Azure IoT Central-Anwendung angezeigt. Für Ihre Azure IoT Central-Anwendung können Sie einen beliebigen Namen wählen. Wählen Sie einen Namen, der für Sie und Ihre Kollegen eindeutig ist.

## <a name="application-url"></a>Anwendungs-URL

Die Anwendungs-URL ist der Link zu Ihrer Anwendung. Sie können ein Lesezeichen in Ihrem Browser speichern oder mit anderen teilen.

Wenn Sie den Namen für Ihre Anwendung eingeben, wird die Anwendungs-URL automatisch generiert. Falls gewünscht, können Sie auch eine andere URL für Ihre Anwendung auswählen. Jede Azure IoT Central-URL muss in Azure IoT Central eindeutig sein. Eine Fehlermeldung wird angezeigt, wenn die von Ihnen gewählte URL bereits vergeben ist.

## <a name="directory"></a>Verzeichnis

Da Azure IoT Central den Kontext kennt, dass Sie den im Microsoft-Partnerportal ausgewählten Kunden verwalten möchten, sehen Sie im Feld „Verzeichnis“ nur den Azure Active Directory-Mandanten für diesen Kunden. 

Ein Azure Active Directory-Mandant enthält Benutzeridentitäten, Anmeldeinformationen und andere organisatorische Informationen. Mehrere Azure-Abonnements können einem einzelnen Azure Active Directory-Mandanten zugeordnet werden.

Weitere Informationen finden Sie unter [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Azure-Abonnement

Mit einem Azure-Abonnement können Sie Instanzen von Azure-Diensten erstellen. Azure IoT Central findet automatisch alle Azure-Abonnements des Kunden, auf die Sie Zugriff haben, und zeigt sie auf der Seite **Anwendung erstellen** in einem Dropdownmenü an. Wählen Sie ein Azure-Abonnement, um eine neue Azure IoT Central-Anwendung zu erstellen.

Wenn Sie über kein Azure-Abonnement verfügen, können Sie im Microsoft Partner Center eins erstellen. Nachdem Sie das Azure-Abonnement erstellt haben, navigieren Sie zurück zur Seite **Anwendung erstellen**. Ihr neues Abonnement wird in der Dropdownliste **Azure-Abonnement** angezeigt.

Weitere Informationen finden Sie unter [Azure-Abonnements](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Region

Wählen Sie die Region, in der Sie Ihre Azure IoT Central-Anwendung erstellen möchten. Normalerweise sollten Sie die Region wählen, die Ihren Geräten physisch am nächsten liegt, um eine optimale Leistung zu erzielen.

Weitere Informationen finden Sie unter [Azure-Regionen](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions).

Die Regionen, in denen Azure IoT Central verfügbar ist, finden Sie auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).

> [!Note]
> Sobald Sie eine Region ausgewählt haben, können Sie Ihre Anwendung später nicht mehr in eine andere Region verschieben.

## <a name="application-template"></a>Anwendungsvorlage

Sie können für Ihre neue Azure IoT Central-Anwendung eine der verfügbaren Anwendungsvorlagen auswählen. Eine Anwendungsvorlage kann vordefinierte Elemente wie Gerätevorlagen und Dashboards enthalten, die Ihnen den Einstieg erleichtern.

| Anwendungsvorlage | BESCHREIBUNG |
| -------------------- | ----------- |
| Benutzerdefinierte Anwendung   | Erstellt eine leere Anwendung, die Sie mit Ihren eigenen Gerätevorlagen und Geräten füllen können. |
| Beispiel „Contoso“       | Erstellt eine Anwendung, die eine Gerätevorlage für ein einfach angeschlossenes Gerät enthält. Verwenden Sie diese Vorlage, um mit der Erkundung von Azure IoT Central zu beginnen. |
| Beispiel-Entwickler-Kits       | Erstellt eine Anwendung mit Gerätevorlagen, an die Sie ein MXChip- oder Raspberry Pi-Gerät anschließen können. Verwenden Sie diese Vorlage, wenn Sie ein Geräteentwickler sind, der mit Code auf einem dieser Geräte experimentiert. |

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie als CSP eine Azure IoT Central-Anwendung erstellen, wird als Nächstes der folgende Schritt empfohlen:

> [!div class="nextstepaction"]
> [Verwalten Ihrer Anwendung](howto-administer.md)