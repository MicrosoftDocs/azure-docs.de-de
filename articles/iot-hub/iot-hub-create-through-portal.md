---
title: Erstellen eines IoT Hubs über das Azure-Portal | Microsoft Docs
description: Erstellen, Verwalten und Löschen von Azure IoT Hubs über das Azure-Portal. Enthält Informationen zu Tarifen, Skalierung, Sicherheit und die Messagingkonfiguration.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 0a54d0c85902915d2ee62acd8a1d38b8db8b221c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536044"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Erstellen eines IoT Hubs über das Portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

In diesem Artikel wird das Erstellen und Verwalten von IoT-Hubs über das [Azure-Portal](https://portal.azure.com) beschrieben.

Sie benötigen ein Azure-Abonnement, um die Schritte in diesem Tutorial durchzuführen. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Ändern der Einstellungen des IoT Hubs

Sie können die Einstellungen eines vorhandenen IoT-Hubs nach seiner Erstellung im Bereich „IoT Hub“ ändern.

![Screenshot: Einstellungen für den IoT Hub](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Hier werden einige der Eigenschaften aufgeführt, die Sie für einen IoT-Hub festlegen können:

**Tarif und Skalierung** : Mit dieser Eigenschaft können Sie die Migration zu einem anderen Tarif durchführen oder die Anzahl von IoT Hub-Einheiten festlegen. 

**Vorgangsüberwachung** : Aktivieren oder deaktivieren Sie die verschiedenen Überwachungskategorien, beispielsweise die Protokollierung von Ereignissen im Zusammenhang mit Gerät-zu-Cloud-Nachrichten oder Cloud-zu-Gerät-Nachrichten.

**IP-Filter** : Hier können Sie einen IP-Adressbereich angeben, der vom IoT-Hub akzeptiert oder abgelehnt wird.

**Properties:** Enthält die Liste der Eigenschaften, die Sie kopieren und an anderer Stelle verwenden können, z.B. die Ressourcen-ID, die Ressourcengruppe, den Standort usw.

### <a name="shared-access-policies"></a>Freigegebene Zugriffsrichtlinien

Sie können die Liste der freigegebenen Zugriffsrichtlinien auch anzeigen oder ändern, indem Sie im Abschnitt **Einstellungen** auf **Richtlinien für gemeinsamen Zugriff** klicken. Diese Richtlinien definieren die Berechtigungen für Geräte und Dienste zum Herstellen einer Verbindung mit dem IoT-Hub. 

Klicken Sie auf **Hinzufügen** , um das Blatt **Richtlinie für den gemeinsamen Zugriff hinzufügen** zu öffnen.  Sie können den Namen der neuen Richtlinie und die Berechtigungen, die dieser Richtlinie zugeordnet werden sollen, wie in der folgenden Abbildung gezeigt eingeben:

![Screenshot: Hinzufügen einer SAS-Richtlinie](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* Die Richtlinien **Registrierung lesen** und **In Registrierung schreiben** berechtigen zum Lesen und Schreiben in der Identitätsregistrierung. Diese Berechtigungen werden von Back-End-Clouddiensten zum Verwalten von Geräteidentitäten verwendet. Beim Auswählen der Schreiboption wird die Leseoption automatisch ausgewählt.

* Die Richtlinie **Dienstverbindung** erteilt die Berechtigung für den Zugriff auf Dienstendpunkte. Diese Berechtigung wird von Back-End-Cloud-Diensten verwendet, um Nachrichten von Geräten zu senden und zu empfangen sowie Gerätezwillings- und Modulzwillingsdaten zu aktualisieren und zu lesen.

* Die Richtlinie **Geräteverbindung** gewährt Berechtigungen zum Senden und Empfangen von Nachrichten mit den geräteseitigen IoT Hub-Endpunkten. Diese Berechtigung wird von Geräten verwendet, um Nachrichten von einem IoT-Hub zu senden und zu empfangen, Gerätezwillings- und Modulzwillingsdaten zu aktualisieren und zu lesen und Dateien hochzuladen.

Klicken Sie auf **Erstellen** , um der vorhandenen Liste diese neu erstellte Richtlinie hinzuzufügen.

Detaillierte Informationen zu dem durch bestimmte Berechtigungen gewährten Zugriff finden Sie unter [IoT Hub-Berechtigungen](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Nachrichtenrouting für einen IoT-Hub

Klicken Sie unter **Messaging** auf **Nachrichtenrouting** , um den Bereich „Nachrichtenrouting“ anzuzeigen. Hier definieren Sie die Routen und benutzerdefinierten Endpunkte für den Hub. Über [Nachrichtenrouting](iot-hub-devguide-messages-d2c.md) können Sie verwalten, wie Daten von Ihren Geräten an die Endpunkte gesendet werden. Der erste Schritt besteht darin, eine neue Route hinzuzufügen. Anschließend können Sie der Route einen vorhandenen Endpunkt hinzufügen oder einen neuen mit einem der unterstützten Typen erstellen, z.B. Blobspeicher. 

![Bereich „Nachrichtenrouting“](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Routen

„Routen“ ist die erste Registerkarte im Bereich „Nachrichtenrouting“. Um eine neue Route hinzuzufügen, klicken Sie auf + **Hinzufügen**. Der folgende Bildschirm wird angezeigt. 

![Screenshot: Hinzufügen einer neuen Route](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Benennen Sie Ihre Route. Der Routenname muss in der Liste der Routen für diesen Hub eindeutig sein. 

Als **Endpunkt** können Sie einen Eintrag aus der Dropdownliste auswählen oder einen neuen hinzufügen. In diesem Beispiel sind ein Speicherkonto und ein Container bereits verfügbar. Um sie als Endpunkt hinzuzufügen, klicken Sie neben der Dropdownliste „Endpunkt“ auf + **Hinzufügen** , und wählen Sie **Blob Storage** aus. Der folgende Bildschirm zeigt, wo das Speicherkonto und der Container angegeben werden.

![Screenshot: Hinzufügen eines Speicherendpunkts für die Routingregel](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Klicken Sie auf **Container auswählen** , um das Speicherkonto und den Container auswählen. Wenn Sie diese Felder ausgewählt haben, gelangen Sie zurück zum Bereich „Endpunkt“. Verwenden Sie die Standardeinstellungen für die übrigen Felder und dann **Erstellen** , um den Endpunkt für das Speicherkonto zu erstellen und den Routingregeln hinzuzufügen.

Wählen Sie für **Datenquelle** die Option „Gerätetelemetriemeldungen“ aus. 

Fügen Sie anschließend eine Routingabfrage hinzu. In diesem Beispiel werden die Nachrichten, die eine Anwendungseigenschaft namens `level` mit einem Wert von `critical` aufweisen, an das Speicherkonto weitergeleitet.

![Screenshot: Speichern einer neuen Routingregel](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Klicken Sie auf **Speichern** , um die Routingregel zu speichern. Sie kehren zum Bereich „Nachrichtenrouting“ zurück, und Ihre neue Routingregel wird angezeigt.

### <a name="custom-endpoints"></a>Benutzerdefinierte Endpunkte

Klicken Sie auf die Registerkarte **Benutzerdefinierte Endpunkte**. Bereits erstellte benutzerdefinierte Endpunkte werden angezeigt. Von hier aus können Sie neue Endpunkte hinzufügen oder vorhandene Endpunkte löschen. 

> [!NOTE]
> Wenn Sie eine Route löschen, werden nicht die Endpunkte gelöscht, die dieser Route zugewiesen sind. Um einen Endpunkt zu löschen, klicken Sie auf die Registerkarte „Benutzerdefinierte Endpunkte“, wählen Sie den Endpunkt aus, den Sie löschen möchten, und klicken Sie auf „Löschen“.
>

In [Referenz: IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md) erfahren Sie mehr über benutzerdefinierte Endpunkte.

Sie können bis zu 10 benutzerdefinierte Endpunkte für einen IoT-Hub definieren. 

Ein vollständiges Beispiel zur Verwendung benutzerdefinierter Endpunkte mit der Routingfunktion finden Sie unter [Nachrichtenrouting mit IoT Hub](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Suchen eines bestimmten IoT-Hubs

Es gibt zwei Möglichkeiten zum Finden eines bestimmten IoT-Hubs in Ihrem Abonnement:

1. Wenn Sie die Ressourcengruppe kennen, zu der der IoT-Hub gehört, klicken Sie auf **Ressourcengruppen** , und wählen Sie dann die Ressourcengruppe aus der Liste aus. Der Bildschirm „Ressourcengruppe“ zeigt alle Ressourcen in dieser Gruppe, einschließlich der IoT-Hubs. Klicken Sie auf den Hub, nach dem Sie suchen.

2. Klicken Sie auf **Alle Ressourcen**. Im Bereich **Alle Ressourcen** gibt es eine Dropdownliste mit dem Standardwert `All types`. Klicken Sie auf die Dropdownliste, und deaktivieren Sie `Select all`. Suchen Sie nach `IoT Hub`, und überprüfen Sie den Eintrag. Klicken Sie auf das Dropdown-Listenfeld, um es zu schließen. Die Einträge werden gefiltert und zeigen nur Ihre IoT-Hubs an.

## <a name="delete-the-iot-hub"></a>Löschen des IoT Hubs

Um einen IoT-Hub zu löschen, suchen Sie den betreffenden IoT-Hub, und klicken Sie dann unter dem Namen des IoT-Hubs auf die Schaltfläche **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Folgen Sie diesen Links, um mehr über das Verwalten von Azure IoT Hub zu erfahren:

* [Nachrichtenrouting mit IoT Hub](tutorial-routing.md)
* [Überwachen Ihres IoT-Hubs](monitor-iot-hub.md)
