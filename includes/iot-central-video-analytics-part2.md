---
title: include file
description: Includedatei
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 472c1770e2793d8da4e8fc76fafbf3b9073b746d
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763424"
---
## <a name="deploy-and-configure-azure-media-services"></a>Bereitstellen und Konfigurieren von Azure Media Services

Für die Lösung wird ein Azure Media Services-Konto verwendet, um die Videoclips zur Objekterkennung zu speichern, die vom IoT Edge-Gatewaygerät aufgezeichnet werden.

Bei der Erstellung des Media Services-Kontos:

- Sie müssen einen Kontonamen, ein Azure-Abonnement, einen Standort, eine Ressourcengruppe und ein Speicherkonto angeben. Erstellen Sie ein neues Speicherkonto mit den Standardeinstellungen, während Sie das Media Services-Konto erstellen.

- Wählen Sie als Standort die Region **USA, Osten** aus.

- Erstellen Sie für das Media Services- und das Speicherkonto eine neue Ressourcengruppe mit dem Namen *lva-rg* in der Region **USA, Osten**. Nachdem Sie die Tutorials durchgearbeitet haben, können Sie alle Ressourcen leicht entfernen, indem Sie die Ressourcengruppe *lva-rg* löschen.

Erstellen Sie das [Media Services-Konto im Azure-Portal](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Verwenden Sie in diesen Tutorials für alle Beispiele die Region **USA, Osten**. Sie können Ihre nächstgelegene Region verwenden, falls Sie dies möchten.

Notieren Sie sich den Namen Ihres **Media Services**-Kontos in der Datei *scratchpad.txt*.

Öffnen Sie nach Abschluss der Bereitstellung Cloud Shell, und führen Sie den folgenden Befehl aus, um die **Ressourcen-ID** für Ihr Media Services-Konto abzurufen:

```azurecli
az resource list --resource-group lva-rg --resource-type microsoft.media/mediaservices --output table --query "[].{ResourceID:id}"
```

:::image type="content" source="media/iot-central-video-analytics-part2/get-resource-id.png" alt-text="Abrufen der Ressourcen-ID mithilfe von Cloud Shell":::

Notieren Sie sich die **Ressourcen-ID** in der Datei *scratchpad.txt*. Sie benötigen diesen Wert später beim Konfigurieren des IoT Edge-Moduls.

Konfigurieren Sie als Nächstes einen Azure Active Directory-Dienstprinzipal für Ihre Media Services-Ressource. Wählen Sie die Option **API-Zugriff** und dann **Dienstprinzipalauthentifizierung** aus. Erstellen Sie eine neue Azure Active Directory-App, die den gleichen Namen wie Ihre Media Services-Ressource hat, und erstellen Sie dann ein Geheimnis mit der Beschreibung *IoT Edge-Zugriff*.

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Konfigurieren der Azure AD-App für Azure Media Services":::

Scrollen Sie nach der Generierung des Geheimnisses nach unten zum Abschnitt **Kopieren Sie die Anmeldeinformationen, um eine Verbindung mit Ihrer Dienstprinzipalanwendung herzustellen**. Wählen Sie anschließend **JSON** aus. Sie können alle Anmeldeinformationen auf einmal kopieren. Notieren Sie sich diese Informationen in der Datei *scratchpad.txt*. Sie benötigen sie später beim Konfigurieren des IoT Edge-Geräts.

> [!WARNING]
> Dies ist die einzige Möglichkeit, das Geheimnis anzuzeigen und zu speichern. Wenn Sie es verlieren, müssen Sie ein neues Geheimnis generieren.

## <a name="create-the-azure-iot-central-application"></a>Erstellen der Azure IoT Central-Anwendung

In diesem Abschnitt erstellen Sie eine neue Azure IoT Central-Anwendung aus einer Vorlage. Sie verwenden diese Anwendung in der gesamten Tutorialreihe, um eine umfassende Lösung zu erstellen.

So erstellen Sie eine neue Azure IoT Central-Anwendung:

1. Navigieren Sie zur Website des [Azure IoT Central-Anwendungs-Managers](https://aka.ms/iotcentral).

1. Melden Sie sich mit den Anmeldeinformationen an, die Sie zum Zugreifen auf Ihr Azure-Abonnement verwenden.

1. Wählen Sie auf der Seite **Erstellen** die Option **Neue Anwendung** aus, um mit der Erstellung einer neuen Azure IoT Central-Anwendung zu beginnen.

1. Wählen Sie **Einzelhandel** aus. Auf der Seite „Einzelhandel“ werden verschiedene Vorlagen für Einzelhandelsanwendungen angezeigt.

Erstellen Sie wie folgt eine neue Videoanalyseanwendung:

1. Wählen Sie die Anwendungsvorlage **Videoanalyse: Objekt- und Bewegungserkennung** aus. Diese Vorlage enthält Gerätevorlagen für die im Tutorial verwendeten Geräte. Die Vorlage enthält Beispieldashboards, mit den Operatoren Aufgaben wie Überwachen und Verwalten von Kameras ausführen können.

1. Wählen Sie optional einen benutzerfreundlichen **Anzeigenamen** aus. Diese Anwendung basiert auf einem fiktiven Einzelhandelsgeschäft mit dem Namen „Northwind Traders“. In diesem Tutorial wird *Northwind Traders video analytics* (Northwind Traders-Videoanalyse) als **Anwendungsname** verwendet.

    > [!NOTE]
    > Wenn Sie einen benutzerfreundlichen **Anwendungsnamen** verwenden, müssen Sie trotzdem einen eindeutigen Wert für die **URL** der Anwendung angeben.

1. Wählen Sie bei Verwendung eines Azure-Abonnements Ihr **Verzeichnis**, **Azure-Abonnement** und als **Standort** die Option **USA** aus. Wenn Sie kein Abonnement haben, können Sie eine **7-tägige kostenlose Testversion** aktivieren und die erforderlichen Kontaktinformationen eingeben. In diesem Tutorial werden drei Geräte verwendet: zwei Kameras und ein IoT Edge-Gerät. Wenn Sie nicht die kostenlose Testversion nutzen, werden Ihnen also Kosten für die Nutzung berechnet.

    Weitere Informationen zu Verzeichnissen, Abonnements und Standorten finden Sie in der Schnellstartanleitung unter [Erstellen einer Azure IoT Central-Anwendung](../articles/iot-central/core/quick-deploy-iot-central.md).

1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Azure IoT Central-Seite „Anwendung erstellen“":::

### <a name="retrieve-the-configuration-data"></a>Abrufen der Konfigurationsdaten

Später in diesem Tutorial beim Konfigurieren des IoT Edge-Gateways benötigen Sie einige Konfigurationsdaten aus der IoT Central-Anwendung. Das IoT Edge-Gerät benötigt diese Informationen, um die Registrierung bei der Anwendung und die zugehörige Verbindungsherstellung durchzuführen.

Wählen Sie im Abschnitt **Verwaltung** die Option **Ihre Anwendung** aus, und notieren Sie sich die **Anwendungs-URL** und die **Anwendungs-ID** in der Datei *scratchpad.txt*:

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="Screenshot: Bereich „Verwaltung“ einer Videoanalyseseite mit hervorgehobener Anwendungs-URL und Anwendungs-ID":::

Wählen Sie **API-Token** aus, und generieren Sie ein neues Token mit dem Namen **LVAEdgeToken** für die Rolle **Operator**:

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="Token generieren":::

Notieren Sie sich das Token in der Datei *scratchpad.txt* zur späteren Verwendung. Nachdem das Dialogfeld geschlossen wurde, können Sie das Token nicht noch einmal anzeigen.

Wählen Sie im Abschnitt **Verwaltung** die Option **Geräteverbindung** und dann **SAS-IoT-Devices** aus.

Notieren Sie sich den **Primärschlüssel** für Geräte in der Datei *scratchpad.txt*. Sie verwenden dieses *SAS-Token (Shared Access Signature) für die primäre Gruppe* später beim Konfigurieren des IoT Edge-Geräts.
