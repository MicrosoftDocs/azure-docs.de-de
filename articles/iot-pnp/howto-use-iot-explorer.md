---
title: Installieren und Verwenden des Azure-IoT-Explorers | Microsoft-Dokumentation
description: Installieren Sie das Azure IoT-Explorer-Tool, und verwenden Sie es, um mit den mit IoT Hub verbundenen IoT Plug & Play-Geräten zu interagieren. Obwohl sich dieser Artikel auf die Verwendung von IoT Plug & Play-Geräten konzentriert, können Sie das Tool mit jedem Gerät verwenden, das mit Ihrem Hub verbunden ist.
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: contperf-fy21q2
ms.openlocfilehash: b0a86a32148086a3b644d0bf147d12be0d812536
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030314"
---
# <a name="install-and-use-azure-iot-explorer"></a>Installieren und Verwenden des Azure-IoT-Explorers

Der Azure IoT-Explorer ist ein grafisches Tool, über das Sie mit Geräten interagieren können, die mit Ihrem IoT-Hub verbunden sind. Dieser Artikel konzentriert sich auf die Verwendung des Tools zum Testen Ihrer IoT Plug & Play-Geräte. Nach der Installation des Tools auf Ihrem lokalen Computer können Sie es zum Herstellen einer Verbindung mit einem Hub verwenden. Sie können mit diesem Tool die vom Gerät gesendeten Telemetriedaten anzeigen, mit Geräteeigenschaften arbeiten und Befehle aufrufen.

In diesem Artikel lernen Sie Folgendes:

- Installieren und Konfigurieren des Azure-IoT-Explorers
- Verwenden Sie das Tool zum Interagieren mit Ihren IoT Plug & Play-Geräten und zum Testen der Geräte.

Weitere allgemeine Informationen zur Verwendung des Tools finden Sie in der GitHub-[Infodatei](https://github.com/Azure/azure-iot-explorer/blob/master/README.md).

Um den Azure-IoT-Explorer zu verwenden, benötigen Sie Folgendes:

- Einen Azure IoT Hub. Es gibt viele Möglichkeiten, um einen IoT-Hub zu Ihrem Azure-Abonnement hinzuzufügen, z. B. das [Erstellen eines IoT-Hubs mit der Azure-Befehlszeilenschnittstelle](../iot-hub/iot-hub-create-using-cli.md). Sie benötigen die Verbindungszeichenfolge für den IoT-Hub, um den Azure-IoT-Explorer auszuführen. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Ein in Ihrem IoT-Hub registriertes Gerät. Sie können IoT-Explorer verwenden, um Geräteregistrierungen in Ihrem IoT-Hub zu erstellen und zu verwalten.

## <a name="install-azure-iot-explorer"></a>Installieren des Azure-IoT-Explorers

Navigieren Sie auf GitHub zu den [Releases von Azure-IoT-Explorer](https://github.com/Azure/azure-iot-explorer/releases), und erweitern Sie die Liste der Assets, um die neueste Version anzuzeigen. Laden Sie die neueste Version der Anwendung herunter, und installieren Sie sie.

>[!Important]
> Führen Sie ein Update auf Version 0.13.x aus, um Modelle aus Repositorys aufzulösen, die auf [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) basieren.

## <a name="use-azure-iot-explorer"></a>Verwenden des Azure-IoT-Explorers

Sie können als Gerät entweder Ihr eigenes Gerät anschließen oder eins der simulierten Beispielgeräte verwenden. Einige Beispiele für simulierte Geräte, die in verschiedenen Sprachen geschrieben wurden, finden Sie im Schnellstart [Verbinden einer IoT Plug & Play-Beispielgeräteanwendung mit IoT Hub](quickstart-connect-device.md).

### <a name="connect-to-your-hub"></a>Herstellen einer Verbindung mit Ihrem Hub

Wenn Sie den Azure-IoT-Explorer zum ersten Mal ausführen, werden Sie aufgefordert, die Verbindungszeichenfolge für den IoT-Hub einzugeben. Nachdem Sie die Verbindungszeichenfolge eingegeben haben, klicken Sie auf **Verbinden**. Sie können die Einstellungen des Tools verwenden, um durch Aktualisieren der Verbindungszeichenfolge zu einem anderen IoT-Hub zu wechseln.

Die Modelldefinition für ein IoT Plug & Play-Gerät wird im öffentlichen Repository, auf dem verbundenen Gerät oder in einem lokalen Ordner gespeichert. Standardmäßig sucht das Tool im öffentlichen Repository und auf Ihrem verbundenen Gerät nach Ihrer Modelldefinition. In den **Einstellungen** können Sie Quellen hinzufügen oder entfernen oder die Priorität der Quellen konfigurieren:

So fügen Sie eine Quelle hinzu:

1. Wechseln Sie zu **Start/IoT Plug & Play-Einstellungen**.
2. Wählen Sie **Hinzufügen** und dann Ihre Quelle aus einem Repository oder lokalen Ordner aus.

So entfernen Sie eine Quelle:

1. Wechseln Sie zu **Start/IoT Plug & Play-Einstellungen**.
2. Suchen Sie die Quelle, die Sie entfernen möchten.
3. Klicken Sie auf **X**, um sie zu entfernen.

Ändern der Prioritäten der Quellen:

Sie können eine Modelldefinitionsquelle per Drag & Drop an eine andere Position in der Rangliste ziehen.

### <a name="view-devices"></a>Anzeigen von Geräten

Nachdem das Tool eine Verbindung mit Ihrem IoT-Hub hergestellt hat, zeigt es die Listenseite **Geräte** mit allen Geräteidentitäten an, die in Ihrer IoT Hub-Instanz registriert sind. Sie können einen beliebigen Eintrag in der Liste auswählen, um weitere Informationen anzuzeigen.

Auf der Listenseite **Geräte** können Sie folgende Aktionen ausführen:

- Wählen Sie **Neu** aus, um ein neues Gerät in Ihrem Hub zu registrieren. Geben Sie dann eine Geräte-ID ein. Verwenden Sie die Standardeinstellungen, um Authentifizierungsschlüssel automatisch zu generieren und die Verbindung mit Ihrem Hub zu aktivieren.
- Wählen Sie ein Gerät und dann **Löschen** aus, um eine Geräteidentität zu löschen. Überprüfen Sie die Gerätedetails, bevor Sie diese Aktion abschließen, um sicherzustellen, dass Sie die richtige Geräteidentität löschen.

## <a name="interact-with-a-device"></a>Interagieren mit einem Gerät

Wählen Sie auf der Seite **Geräte** einen Wert in der Spalte **Geräte-ID** aus, um die Detailseite für das registrierte Gerät anzuzeigen. Für jedes Gerät gibt es zwei Abschnitte: **Gerät** und **Digitaler Zwilling**.

### <a name="device"></a>Sicherungsmedium

Dieser Abschnitt enthält die Registerkarten **Geräteidentität**, **Gerätezwilling**, **Telemetrie**, **Direkte Methode**, **Cloud-zu-Gerät-Nachricht** und **Modulkennung**.

- Sie können die Informationen zur [Geräteidentität](../iot-hub/iot-hub-devguide-identity-registry.md) auf der Registerkarte **Geräteidentität** anzeigen und aktualisieren.
- Über die Registerkarte **Gerätezwilling** können Sie auf Informationen zum [Gerätezwilling](../iot-hub/iot-hub-devguide-device-twins.md) zugreifen.
- Wenn ein Gerät verbunden ist und aktiv Daten sendet, können Sie diese [Telemetriedaten](../iot-hub/iot-hub-devguide-messages-read-builtin.md) auf der Registerkarte **Telemetrie** anzeigen.
- Über die Registerkarte **Direkte Methode** können Sie eine [direkte Methode](../iot-hub/iot-hub-devguide-direct-methods.md) für das Gerät aufrufen.
- Über die Registerkarte **Cloud-zu-Gerät-Nachrichten** können Sie eine [Cloud-zu-Gerät-Nachricht](../iot-hub/iot-hub-devguide-messages-c2d.md) senden.
- Sie können auf die Informationen zum [Modulzwilling](../iot-hub/iot-hub-devguide-module-twins.md) zugreifen.

### <a name="iot-plug-and-play-components"></a>IoT Plug & Play-Komponenten

Wenn das Gerät über eine **Modell-ID** mit dem Hub verbunden ist, zeigt das Tool die Registerkarte **IoT Plug & Play-Komponenten** an, auf der Sie die **Modell-ID** sehen können.

Wenn die **Modell-ID** in einer der konfigurierten Quellen – öffentliches Repository oder lokaler Ordner – zur Verfügung steht, wird die Liste der Komponenten angezeigt. Wenn Sie eine Komponente auswählen, werden die verfügbaren Eigenschaften, Befehle und Telemetriedaten angezeigt.

Auf der Seite **Komponente** können Sie die schreibgeschützten Eigenschaften anzeigen, schreibbare Eigenschaften aktualisieren, Befehle aufrufen und die von dieser Komponente erzeugten Telemetrienachrichten anzeigen.

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="Anzeigen von Komponenten im Azure IoT-Explorer":::

#### <a name="properties"></a>Eigenschaften

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Anzeigen von Eigenschaften in Azure IoT-Explorer":::

Auf der Registerkarte **Eigenschaften (schreibgeschützt)** können Sie die in einer Schnittstelle definierten schreibgeschützten Eigenschaften anzeigen. Auf der Registerkarte **Eigenschaften (schreibbar)** können Sie die in einer Schnittstelle definierten schreibbaren Eigenschaften mit den folgenden Schritten aktualisieren:

1. Wechseln Sie zur Registerkarte **Eigenschaften (schreibbar)** .
1. Klicken Sie auf die Eigenschaft, die Sie aktualisieren möchten.
1. Geben Sie den neuen Wert für die Eigenschaft ein.
1. Sehen Sie sich eine Vorschau der Nutzlast an, die an das Gerät gesendet wird.
1. Übermitteln Sie die Änderung.

Nachdem Sie eine Änderung übermittelt haben, können Sie den Aktualisierungsstatus nachverfolgen: **Wird synchronisiert**, **Erfolg** oder **Fehler**. Wenn die Synchronisierung abgeschlossen ist, wird der neue Wert der Eigenschaft in der Spalte **Gemeldete Eigenschaft** angezeigt. Falls Sie vor Beendigung der Synchronisierung zu anderen Seiten navigieren, werden Sie vom Tool benachrichtigt, wenn die Aktualisierung abgeschlossen ist. Sie können sich den Benachrichtigungsverlauf auch in der Mitteilungszentrale des Tools ansehen.

#### <a name="commands"></a>Befehle

Zum Senden eines Befehls an ein Gerät wechseln Sie zur Registerkarte **Befehle**:

1. Erweitern Sie in der Befehlsliste den Befehl, den Sie auslösen möchten.
1. Geben Sie alle erforderlichen Werte für den Befehl ein.
1. Sehen Sie sich eine Vorschau der Nutzlast an, die an das Gerät gesendet wird.
1. Übermitteln Sie den Befehl.

#### <a name="telemetry"></a>Telemetrie

Zum Anzeigen der Telemetrie für die ausgewählte Schnittstelle wechseln Sie zu deren Registerkarte **Telemetrie**.

#### <a name="known-issues"></a>Bekannte Probleme

Eine Liste der IoT-Features, die von der neuesten Version des Tools unterstützt werden, finden Sie unter [Funktionsliste](https://github.com/Azure/azure-iot-explorer/wiki).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie den Azure-IoT-Explorer installieren und verwenden, um mit Ihren IoT Plug & Play-Geräten zu interagieren. Ein Vorschlag für den nächsten Schritt ist, das [Installieren und Verwenden des DTDL-Erstellungstools](howto-use-dtdl-authoring-tools.md) zu lernen.
