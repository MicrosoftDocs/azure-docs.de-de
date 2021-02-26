---
title: 'Schnellstart: Erstellen einer Azure IoT Central-Anwendung | Microsoft-Dokumentation'
description: 'Schnellstart: Erstellen einer neuen Azure IoT Central-Anwendung Erstellen Sie die Anwendung entweder im Free-Tarif oder in einem der Standard-Tarife.'
author: viv-liu
ms.author: viviali
ms.date: 12/28/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c5e357824652077acab299e0e27678f528f23b9a
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833897"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>Schnellstart: Erstellen einer Azure IoT Central-Anwendung

In dieser Schnellstartanleitung wird gezeigt, wie Sie eine Azure IoT Central-Anwendung erstellen.


[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="create-an-application"></a>Erstellen einer Anwendung

Navigieren Sie zur Buildwebsite für [Azure IoT Central](https://aka.ms/iotcentral). Melden Sie sich dann mit einem persönlichen Microsoft-Konto oder mit einem Geschäfts-, Schul- oder Unikonto an.

Sie erstellen eine neue Anwendung entweder anhand der Liste mit den branchenrelevanten IoT Central-Vorlagen, um schnell einzusteigen, oder Sie starten von Grund auf neu, indem Sie eine Vorlage für **benutzerdefinierte Apps** verwenden. In dieser Schnellstartanleitung verwenden Sie die Vorlage **Benutzerdefinierte Anwendung**.

So erstellen Sie eine neue Azure IoT Central-Anwendung über die Vorlage **Benutzerdefinierte Anwendung**:

1. Navigieren Sie zur Seite **Erstellen**:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="Seite „Eigene IoT-Anwendung erstellen“":::

1. Wählen Sie **Benutzerdefinierte Apps** aus, und vergewissern Sie sich, dass die Vorlage **Benutzerdefinierte Anwendung** ausgewählt ist.

1. Azure IoT Central schlägt automatisch einen **Anwendungsnamen** entsprechend der von Ihnen ausgewählten Anwendungsvorlage vor. Sie können diesen Namen übernehmen oder einen eigenen aussagekräftigen Namen eingeben.

1. Azure IoT Central generiert außerdem für Sie entsprechend dem Anwendungsnamen ein Präfix mit einer eindeutigen **Anwendungs-URL**. Über diese URL können Sie auf Ihre Anwendung zugreifen. Sie können dieses URL-Präfix in einen einprägsameren Wert ändern.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Azure IoT Central-Seite „Anwendung erstellen“":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Abrechnungsinformationen zu Azure IoT Central":::

    > [!Tip]
    > Wenn Sie auf der vorherigen Seite **Benutzerdefinierte App** ausgewählt haben, wird das Dropdownmenü **Anwendungsvorlage** angezeigt. Im Dropdownmenü werden unter Umständen andere Vorlagen angezeigt, die von Ihrer Organisation für Sie zur Verfügung gestellt wurden.

1. Erstellen Sie die Anwendung entweder im Tarif für die kostenlose 7-Tage-Testversion oder in einem der Standard-Tarife.

    - Im Tarif *Free* erstellte Anwendungen sind sieben Tage lang kostenlos und unterstützen bis zu fünf Geräte. Sie können bis zum Ablauftermin jederzeit in Anwendungen mit Standard-Tarif konvertiert werden.
        > [!NOTE]
        > Da für Anwendungen, die mit dem Tarif *Free* erstellt wurden, keine Azure-Abonnements erforderlich sind, werden sie auch nicht in Ihrem Azure-Abonnement im Azure-Portal aufgeführt. Kostenlose Apps können nur über das IoT Central-Portal angezeigt und verwaltet werden.          
    - Mit einem Tarif vom Typ *Standard* erstellte Anwendungen werden pro Gerät abgerechnet. Sie können zwischen **Standard 0**, **Standard 1** und **Standard 2** wählen. Die ersten beiden Geräte sind kostenlos. Weitere Informationen zu den Tarifen „Free“ und „Standard“ finden Sie auf der Seite [Azure IoT Central – Preise](https://azure.microsoft.com/pricing/details/iot-central/). Wenn Sie eine Anwendung im Standard-Tarif erstellen, müssen Sie Ihr *Verzeichnis*, Ihr *Azure-Abonnement* und Ihren *Standort* auswählen:
        - Bei *Verzeichnis* handelt es sich um die Azure Active Directory-Instanz, in der Sie Ihre Anwendung erstellen. Eine Azure Active Directory-Instanz enthält Benutzeridentitäten, Anmeldeinformationen und andere organisatorische Informationen. Wenn Sie keine Azure Active Directory-Instanz besitzen, wird beim Erstellen eines Azure-Abonnements eine für Sie angelegt.
        - Mit einem *Azure-Abonnement* können Sie Instanzen von Azure-Diensten erstellen. IoT Central stellt Ressourcen in Ihrem Abonnement bereit. Wenn Sie kein Azure-Abonnement besitzen, können Sie auf der [Azure-Anmeldeseite](https://aka.ms/createazuresubscription) kostenlos eines erstellen. Nachdem Sie das Azure-Abonnement erstellt haben, navigieren Sie zurück zur Seite **Neue Anwendung**. Ihr neues Abonnement wird nun in der Dropdownliste **Azure-Abonnement** angezeigt.
        - Der *Standort* beschreibt die [geografische Region](https://azure.microsoft.com/global-infrastructure/geographies/), in der Sie Ihre Anwendung erstellen möchten. Normalerweise wählen Sie den Standort aus, der Ihren Geräten physisch am nächsten liegt, um eine optimale Leistung zu erzielen. Nachdem Sie einen Standort ausgewählt haben, können Sie Ihre Anwendung nicht zu einem späteren Zeitpunkt an einen anderen Standort verschieben.

1. Lesen Sie die Geschäftsbedingungen, und wählen Sie **Erstellen** im unteren Bereich der Seite aus. Die IoT Central-Anwendung kann nach einigen Minuten verwendet werden.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Azure IoT Central-Anwendung":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine IoT Central-Anwendung erstellt. Wenn Sie sich weiter über IoT Central informieren möchten, sehen Sie sich die folgende Schnellstartanleitung an:

> [!div class="nextstepaction"]
> [Hinzufügen eines simulierten Geräts zu Ihrer IoT Central-Anwendung](./quick-create-simulated-device.md)

Wenn Sie Geräteentwickler sind und sich ausführlicher mit dem Code beschäftigen möchten, sehen Sie sich das folgende Tutorial an:
> [!div class="nextstepaction"]
> [Tutorial: Erstellen einer Node.js-Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung (Node.js)](./tutorial-connect-device.md)