---
title: 'Azure Data Box Edge: Gerätezugriff, Energieeinstellungen und Konnektivitätsmodus | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie den Zugriff, die Energieeinstellungen und den Konnektivitätsmodus für das Azure Data Box Edge-Gerät verwalten, mit dem Daten in Azure übertragen werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 5fbe8f3eb05ac60918e488c68869c3fe44051a3f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924366"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Verwalten des Zugriffs, der Energieeinstellungen und des Konnektivitätsmodus für Azure Data Box Edge

In diesem Artikel wird beschrieben, wie Sie den Zugriff, die Energieeinstellungen und den Konnektivitätsmodus für Azure Data Box Edge verwalten. Diese Vorgänge werden über die lokale Webbenutzeroberfläche oder das Azure-Portal ausgeführt.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Verwalten des Gerätezugriffs
> * Verwalten des Konnektivitätsmodus
> * Verwalten der Energieeinstellungen


## <a name="manage-device-access"></a>Verwalten des Gerätezugriffs

Der Zugriff auf Ihr Data Box Edge-Gerät wird mithilfe eines Gerätekennworts gesteuert. Sie können das Kennwort über die lokale Webbenutzeroberfläche ändern. Das Gerätekennwort kann auch über das Azure-Portal zurückgesetzt werden.

### <a name="change-device-password"></a>Ändern des Gerätekennworts

Führen Sie auf der lokalen Webbenutzeroberfläche die folgenden Schritte aus, um das Gerätekennwort zu ändern.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung > Password change** (Kennwortänderung).
2. Geben Sie das aktuelle Kennwort und dann das neue Kennwort ein. Das angegebene Kennwort muss zwischen 8 und 16 Zeichen umfassen. Das Kennwort muss drei der folgenden Zeichen enthalten: Großbuchstaben, Kleinbuchstaben, Ziffern und Sonderzeichen. Bestätigen Sie das neue Kennwort.

    ![Kennwort ändern](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Wählen Sie **Kennwort ändern** aus.
 
### <a name="reset-device-password"></a>Zurücksetzen des Gerätekennworts

Beim Workflow zum Zurücksetzen muss der Benutzer das alte Kennwort nicht kennen. Dies ist nützlich, wenn das Kennwort verloren gegangen ist. Dieser Workflow wird im Azure-Portal ausgeführt.

1. Navigieren Sie im Azure-Portal zu **Übersicht > Administratorkennwort zurücksetzen**.

    ![Kennwort zurücksetzen](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Geben Sie das neue Kennwort ein, und bestätigen Sie es. Das angegebene Kennwort muss zwischen 8 und 16 Zeichen umfassen. Das Kennwort muss drei der folgenden Zeichen enthalten: Großbuchstaben, Kleinbuchstaben, Ziffern und Sonderzeichen. Klicken Sie auf **Zurücksetzen**.

    ![Kennwort zurücksetzen](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Verwalten des Konnektivitätsmodus

Ihr Gerät kann nicht nur im Standardmodus „Vollständig verbunden“, sondern auch im Modus „Teilweise getrennt“ oder im Modus „Vollständig getrennt“ ausgeführt werden. Die einzelnen Modi sind nachfolgend beschrieben:

- **Vollständig verbunden**: Dies ist der Standardmodus, in dem das Gerät betrieben wird. In diesem Modus ist sowohl der Hochladen von Daten in die Cloud als auch das Herunterladen von Daten aus der Cloud möglich. Sie können das Gerät im Azure-Portal oder auf der lokalen Webbenutzeroberfläche verwalten.

- **Teilweise getrennt**: In diesem Modus kann das Gerät keine freigegebenen Daten hochladen oder herunterladen, jedoch über das Azure-Portal verwaltet werden.

    Dieser Modus kommt in der Regel in einem getakteten Satellitennetzwerk zum Einsatz und dient zum Minimieren der Netzwerkbandbreitennutzung. Bei Geräteüberwachungsvorgängen kommt es aber unter Umständen dennoch zu einer geringfügigen Netzwerknutzung.

- **Getrennt**: In diesem Modus ist das Gerät vollständig von der Cloud getrennt, und Clouduploads und -downloads sind deaktiviert. Das Gerät kann nur über die lokale Weboberfläche verwaltet werden.

    Dieser Modus wird normalerweise verwendet, wenn Sie Ihr Gerät offline schalten möchten.

Führen Sie zum Ändern des Gerätemodus die folgenden Schritte aus:

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zu **Konfiguration > Cloudeinstellungen**.
2. Wählen Sie in der Dropdownliste den Betriebsmodus des Geräts aus. Sie können zwischen **Vollständig verbunden**, **Teilweise verbunden** und **Vollständig getrennt** wählen. Wenn Sie das Gerät im Modus „Teilweise getrennt“ ausführen möchten, aktivieren Sie **Azure portal management** (Verwaltung im Azure-Portal).

    ![Konnektivitätsmodus](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Verwalten der Energieeinstellungen

Sie können Ihr physisches Gerät über die lokale Webbenutzeroberfläche herunterfahren und neu starten. Wir empfehlen, vor dem Neustart die Freigaben auf dem Datenserver und dann auf dem Gerät offline zu schalten. Dadurch wird das Risiko einer Datenbeschädigung minimiert.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung > Power settings** (Energieeinstellungen).
2. Klicken Sie abhängig von der gewünschten Aktion auf **Herunterfahren** oder **Neu starten**.

    ![Energieeinstellungen](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**, um fortzufahren.

> [!NOTE]
> Wenn Sie das physische Gerät herunterfahren, müssen Sie den Netzschalter am Gerät drücken, um es einzuschalten.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Verwaltung von Freigaben](data-box-edge-manage-shares.md).