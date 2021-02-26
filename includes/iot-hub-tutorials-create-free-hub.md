---
title: include file
description: include file
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9151a84aa03e9b87b02278672622d458fbc5281e
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538710"
---
So erstellen Sie eine IoT Hub-Instanz über das Azure-Portal:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie auf der Azure-Startseite **Ressource erstellen** aus, und geben Sie dann unter **Marketplace durchsuchen** den Begriff *IoT Hub* ein.

1. Wählen Sie in den Suchergebnissen **IoT Hub** und dann **Erstellen** aus.

1. Füllen Sie auf der Registerkarte **Grundlegende Einstellungen** die Felder wie folgt aus:

   - **Abonnement**: Wählen Sie das Abonnement aus, das Sie für Ihren Hub verwenden möchten.

   - **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. Wählen Sie zum Erstellen einer neuen Ressourcengruppe die Option **Neu erstellen** aus, und geben Sie den Namen ein, den Sie verwenden möchten. Wenn Sie eine vorhandene Ressourcengruppe verwenden möchten, wählen Sie die entsprechende Ressourcengruppe aus. Weitere Informationen finden Sie unter [Verwalten von Azure Resource Manager-Ressourcengruppen](../articles/azure-resource-manager/management/manage-resource-groups-portal.md). In diesem Tutorial wird der Name **tutorials-iot-hub-rg** verwendet.

   - **Region**: Wählen Sie die Region aus, in der sich Ihr Hub befinden soll. Wählen Sie den Standort aus, der Ihnen am nächsten ist. Einige Features, etwa [IoT Hub-Gerätestreams](../articles/iot-hub/iot-hub-device-streams-overview.md), sind nur in bestimmten Regionen verfügbar. Für diese eingeschränkten Features müssen Sie eine der unterstützten Regionen auswählen. In diesem Tutorial wird die Region **USA, Westen** verwendet.

   - **IoT Hub-Name**: Geben Sie einen Namen für den Hub ein. Dieser Name muss global eindeutig sein. In diesem Tutorial wird **tutorials-iot-hub** verwendet. Sie müssen einen eigenen eindeutigen Namen wählen, wenn Sie Ihren Hub erstellen.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="Erstellen eines Hubs im Azure-Portal":::

1. Klicken Sie auf **Weiter: Netzwerk**, um die Erstellung Ihres Hubs fortzusetzen.

   Wählen Sie die Endpunkte aus, die eine Verbindung mit IoT Hub herstellen können. Sie können die Standardeinstellung **Öffentlicher Endpunkt (alle Netzwerke)** auswählen oder die Option **Öffentlicher Endpunkt (ausgewählte IP-Adressbereiche)** oder **Privater Endpunkt** festlegen. Übernehmen Sie für dieses Tutorial die Standardeinstellung.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="Auswählen der Endpunkte, die eine Verbindung herstellen können":::

1. Klicken Sie auf **Weiter: Verwaltung**, um die Erstellung Ihres Hubs fortzusetzen.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="Festlegen der Größe und Skalierung für einen neuen Hub über das Azure-Portal":::

   Sie können hier die Standardeinstellungen übernehmen. Sie können bei Bedarf die folgenden Felder anpassen:

   - **Tarif und Skalierung**: Ihre ausgewählte Ebene. Wählen Sie den Free-Tarif aus. Der kostenlose Tarif ist für Test und Bewertung vorgesehen. Damit kann für 500 Geräte eine Verbindung mit dem Hub hergestellt werden, und bis zu 8.000 Nachrichten pro Tag sind möglich. Jedes Azure-Abonnement kann einen IoT-Hub im kostenlosen Tarif erstellen.

   - **IoT Hub-Einheiten**: Die Anzahl der pro Einheit und Tag zulässigen Nachrichten hängt von Ihrem Hubtarif ab. Beispiel: Wenn der Hub 700.000 eingehende Nachrichten unterstützen soll, wählen Sie zwei Einheiten des Tarifs S1.
   Jedes Azure-Abonnement kann einen IoT-Hub im kostenlosen Tarif erstellen. Einzelheiten zu den anderen Tarifoptionen finden Sie unter [Skalieren einer IoT Hub-Lösung](../articles/iot-hub/iot-hub-scaling.md).

   - **Defender für IoT:** Aktivieren Sie diese Option, um IoT und Ihren Geräten eine zusätzliche Ebene für den Bedrohungsschutz hinzuzufügen. Diese Option steht nicht für Hubs im Free-Tarif zur Verfügung. Weitere Informationen zu diesem Feature finden Sie in der [Dokumentation zu Azure Security Center für IoT](/azure/asc-for-iot/).

   - **Erweiterte Einstellungen** > **Gerät-zu-Cloud-Partitionen**: Diese Eigenschaft setzt die Gerät-zu-Cloud-Nachrichten in Relation zur Anzahl von gleichzeitigen Lesern der Nachrichten. Die meisten Hubs benötigen nur vier Partitionen. Ein Hub im Free-Tarif ist auf zwei Partitionen beschränkt.

1. Klicken Sie auf **Weiter: Tags**, um mit dem nächsten Bildschirm fortzufahren.

   Tags sind Name-Wert-Paare. Sie können das gleiche Tag mehreren Ressourcen und Ressourcengruppen zuweisen, um Ressourcen zu kategorisieren und die Abrechnung zu konsolidieren. Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../articles/azure-resource-manager/management/tag-resources.md).

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="Zuweisen von Tags für den Hub über das Azure-Portal":::

1. Klicken Sie auf **Weiter: Überprüfen + erstellen**, um Ihre Auswahl zu überprüfen. Die Anzeige entspricht in etwa dem folgenden Bildschirm, allerdings werden die Werte verwendet, die Sie beim Erstellen des Hubs ausgewählt haben.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="Überprüfen von Informationen bei der Erstellung des neuen Hubs":::

1. Notieren Sie sich den gewählten IoT Hub-Namen. Sie verwenden diesen Wert später im Tutorial.
