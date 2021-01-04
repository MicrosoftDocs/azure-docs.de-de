---
title: Verbinden eines Rigado Cascade 500-Geräts in Azure IoT Central | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein Rigado Cascade 500-Gatewaygerät mit ihrer IoT Central-Anwendung verbinden.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 0000e7690ab92f469a7417e82cb375c524e0b343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014761"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Verbinden eines Rigado Cascade 500-Gatewaygeräts mit Ihrer Azure IoT Central-Anwendung

*Dieser Artikel richtet sich an Lösungsentwickler.*

In diesem Artikel wird beschrieben, wie Sie als Lösungsersteller ein Rigado Cascade 500-Gatewaygerät mit Ihrer Microsoft Azure IoT Central-Anwendung verbinden können. 

## <a name="what-is-cascade-500"></a>Was ist Cascade 500?

Cascade 500 IoT-Gateway ist ein Hardwareangebot von Rigado, das in deren Lösung „Cascade Edge-as-a-Service“ enthalten ist. Es bietet kommerziellen IoT-Projekt- und -Produktteams flexible Edge-Computeleistung, eine stabile Anwendungsumgebung in Containern sowie eine Vielzahl von Konnektivitätsoptionen für drahtlose Geräte, darunter Bluetooth 5, LTE und WLAN.

Cascade 500 ist für Azure IoT Plug & Play (Vorschauversion) vorab zertifiziert und ermöglicht es unseren Lösungsentwicklern, das Gerät problemlos in End-to-End-Lösungen zu integrieren. Mit dem Cascade-Gateway können Sie eine drahtlose Verbindung zu einer Vielzahl von Sensoren für die Zustandsüberwachung herstellen, die sich in der Nähe des Gatewaygeräts befinden. Diese Sensoren können über das Gatewaygerät in IoT Central integriert werden.

## <a name="prerequisites"></a>Voraussetzungen
Zum schrittweisen Durcharbeiten dieser Anleitung benötigen Sie die folgenden Ressourcen:

* Ein Rigado Cascade 500-Gerät. Weitere Informationen finden Sie unter [Rigado](https://www.rigado.com/).
* Eine Azure IoT Central-Anwendung. Weitere Informationen finden Sie unter [Erstellen einer neuen Anwendung](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Hinzufügen einer Gerätevorlage

Zum Integrieren eines Cascade 500-Gatewaygeräts in Ihre Azure IoT Central-Anwendungsinstanz müssen Sie eine entsprechende Gerätevorlage in Ihrer Anwendung konfigurieren.

So fügen Sie eine Cascade 500-Gerätevorlage hinzu: 

1. Navigieren Sie im linken Bereich zur Registerkarte ***Gerätevorlagen** _, und wählen Sie aus _*+ Neu**: ![Neue Gerätevorlage erstellen](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. Auf der Seite gibt es eine Option zum **_Erstellen einer benutzerdefinierten Vorlage_* _ oder zum _*_Verwenden einer vorkonfigurierten Gerätevorlage_*_.
1. Wählen Sie die Gerätevorlage „C500“ aus der Liste von vorkonfigurierten Gerätevorlagen aus, wie nachstehend gezeigt wird: ![Auswählen der Gerätevorlage „C500“](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Wählen Sie _*_Weiter: Anpassen_*_ aus, um mit dem nächsten Schritt fortzufahren. 
1. Wählen Sie auf dem nächsten Bildschirm _*_Erstellen_*_ aus, um das Onboarding der C500-Gerätevorlage in Ihrer IoT Central-Anwendung durchzuführen.

## <a name="retrieve-application-connection-details"></a>Abrufen von Details zur Anwendungsverbindung

Jetzt müssen Sie die _ *Bereichs-ID** und den **Primärschlüssel** für Ihre Azure IoT Central-Anwendung abrufen, um eine Verbindung mit dem Cascade 500-Gerät herzustellen. 

1. Navigieren Sie im linken Bereich zu **Verwaltung**, und klicken Sie auf **Geräteverbindung**. 
2. Notieren Sie sich die **Bereichs-ID** für Ihre IoT Central-Anwendung.
![Anwendungs-Bereichs-ID](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Klicken Sie jetzt auf **Schlüssel anzeigen**, und notieren Sie sich den **Primärschlüssel**
![Primärschlüssel](./media/howto-connect-rigado-cascade-500/primary-key-sas.png).  

## <a name="contact-rigado-to-connect-the-gateway"></a>Kontaktieren von Rigado, um eine Verbindung mit dem Gateway herzustellen 

Zum Herstellen einer Verbindung zwischen dem Cascade 500-Gerät und Ihrer IoT Central-Anwendung müssen Sie Rigado kontaktieren und ihnen die Details zur Anwendungsverbindung aus den vorstehenden Schritten mitteilen. 

Sobald das Gerät mit dem Internet verbunden ist, kann Rigado über einen sicheren Kanal ein Konfigurationsupdate per Pushdown auf das Cascade 500-Gatewaygerät herunterladen. 

Nachdem dieses Update die IoT Central-Verbindungsdetails auf das Cascade 500-Gerät angewendet hat, wird es in Ihrer Geräteliste angezeigt. 

![Geräteliste](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Jetzt können Sie Ihr C500-Gerät in Ihrer IoT Central-Anwendung einsetzen!

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie ein Geräteentwickler sind, werden einige der folgenden nächsten Schritte empfohlen:

- Informieren Sie sich zu [Gerätekonnektivität in Azure IoT Central](./concepts-get-connected.md).
- Informieren Sie sich zu [Überwachen der Gerätekonnektivität per Azure CLI](./howto-monitor-devices-azure-cli.md).
