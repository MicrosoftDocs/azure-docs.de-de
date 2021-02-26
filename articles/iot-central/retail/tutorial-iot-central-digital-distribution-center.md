---
title: Tutorial zum IoT Digital Distribution Center | Microsoft-Dokumentation
description: Ein Tutorial der Anwendungsvorlage für digitale Verteilzentren für IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 9d7c4d2afa8eaf197ec05bcb0aed654153fb8f0d
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831296"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Tutorial: Bereitstellen und Durchlaufen einer Anwendungsvorlage für digitale Verteilzentren

In diesem Tutorial wird der Einstieg durch das Bereitstellen einer IoT Central-Anwendungsvorlage für **digitale Verteilzentren** gezeigt. Sie erfahren, wie Sie die Vorlage bereitstellen, was im Lieferumfang enthalten ist und welche nächsten Schritte sinnvoll sind.

In diesem Tutorial lernen Sie Folgendes: 

> [!div class="checklist"]
> Erstellen einer Anwendung für digitale Verteilzentren und Durchlaufen der Anwendung 

## <a name="prerequisites"></a>Voraussetzungen
* Zum Bereitstellen dieser App sind keine besonderen Voraussetzungen erforderlich.
* Ein Azure-Abonnement ist empfehlenswert, Sie können es aber auch ohne versuchen.

## <a name="create-digital-distribution-center-application-template"></a>Erstellen einer Anwendungsvorlage für digitale Verteilzentren

Mithilfe der folgenden Schritte können Sie eine Anwendung erstellen.

1. Navigieren Sie zur Anwendungs-Manager-Website von Azure IoT Central. Wählen Sie auf der linken Navigationsleiste **Erstellen** aus, und klicken Sie dann auf die Registerkarte **Einzelhandel**.

    :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-homepage.png" alt-text="Anwendungsvorlage für digitale Verteilzentren":::
1. Wählen Sie die Registerkarte **Einzelhandel** aus, und wählen Sie unter **Digital Distribution Center-Anwendung** die Option **App erstellen** aus.

1. Mit **App erstellen** wird ein neues Anwendungsformular geöffnet, wobei die erforderlichen Details wie unten dargestellt ausgefüllt werden.
   **Anwendungsname:** Sie können den vorgeschlagenen Standardnamen verwenden oder einen eigenen Anwendungsnamen eingeben.
   **URL:** Sie können die vorgeschlagene Standard-URL verwenden oder eine eigene, einfach merkbare und benutzerfreundliche URL eingeben. Als Nächstes wird die Standardeinstellung empfohlen, wenn Sie bereits über ein Azure-Abonnement verfügen. Sie können mit dem Tarif für die kostenlose 7-Tage-Testversion beginnen und jederzeit vor Ablauf der kostenlosen Testversion auf einen Standard-Tarif umstellen.
   **Abrechnungsinfo**: Die Detailangaben zum Verzeichnis, dem Azure-Abonnement und der Region sind für die Bereitstellung der Ressourcen erforderlich.
   **Erstellen**: Wählen Sie unten auf der Seite „Erstellen“ aus, um Ihre Anwendung bereitzustellen.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create.png" alt-text="Screenshot: Erstellen einer App auf der Grundlage der Anwendungsvorlage für digitale Verteilzentren":::

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create-billinginfo.png" alt-text="Screenshot: Abrechnungsoptionen beim Erstellen der Anwendung":::

## <a name="walk-through-the-application-dashboard"></a>Einführung in das Anwendungsdashboard 

Nach erfolgreicher Bereitstellung der App-Vorlage ist Ihr Standarddashboard ein Portal für den Betreiber eines Verteilzentrums. Northwind Trader ist ein fiktiver Lösungsanbieter für Verteilzentren, der Transportsysteme verwaltet. 

Auf diesem Dashboard sehen Sie ein Gateway und eine Kamera als IoT-Gerät. Das Gateway stellt Telemetriedaten zu Paketen bereit, z. B. gültig, ungültig, nicht identifiziert und Größe, sowie zugehörige Eigenschaften des Gerätezwillings. Alle Downstreambefehle werden auf IoT-Geräten ausgeführt, z. B. auf einer Kamera. Dieses Dashboard ist vorkonfiguriert, sodass die Aktivität von kritischen Vorgängen auf Geräten im Verteilzentrum hervorgehoben wird.

Das Dashboard ist logisch organisiert, um die Geräteverwaltungsfunktionen des Azure-IoT-Gateways und des IoT-Geräts anzuzeigen.  
   * Sie können Gatewaybefehle und -steuerungsaufgaben ausführen.
   * Verwalten Sie alle Kameras, die zu der Lösung gehören. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Dashboard für das digitale Verteilzentrum](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Gerätevorlage

Klicken Sie auf die Registerkarte „Gerätevorlagen“, dann wird das Gatewayfunktionsmodell angezeigt. Ein Funktionsmodell ist um zwei unterschiedliche Schnittstellen strukturiert: **Kamera** und **Digital Distribution Gateway**.

> [!div class="mx-imgBorder"]
> ![Screenshot: Gerätevorlage „Digital Distribution Gateway“ in der Anwendung](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Kamera:** Diese Schnittstelle organisiert alle kameraspezifischen Befehlsfunktionen. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Kameraschnittstelle in der Gerätevorlage „Digital Distribution Gateway“](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Digital Distribution Gateway:** Diese Schnittstelle stellt die Telemetriedaten von der Kamera, für die Cloud definierte Gerätezwillingseigenschaften und Gatewayinformationen dar.

> [!div class="mx-imgBorder"]
> ![Screenshot: Schnittstelle „Digital Distribution Gateway“ in der Gerätevorlage „Digital Distribution Gateway“](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Gatewaybefehle
Diese Schnittstelle organisiert alle Gatewaybefehlsfunktionen.

> [!div class="mx-imgBorder"]
> ![Screenshot: Schnittstelle für Gatewaybefehle in der Gerätevorlage „Digital Distribution Gateway“](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Regeln
Wählen Sie die Registerkarte „Regeln“ aus, um zwei verschiedene Regeln anzuzeigen, die in dieser Anwendungsvorlage vorhanden sind. Diese Regeln sind so konfiguriert, dass sie zwecks weiterer Untersuchungen E-Mail-Benachrichtigungen an die Operatoren senden.

 **Warnung über zu viele ungültige Pakete:** Diese Regel wird ausgelöst, wenn die Kamera eine große Anzahl von ungültigen Paketen erkennt, die sich durch das Transportsystem ziehen.
 
**Großes Paket:** Diese Regel wird auslöst, wenn die Kamera ein extrem großes Paket erkennt, das nicht auf Qualität überprüft werden kann. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Liste der Regeln in der Anwendung für digitale Verteilzentren](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Aufträge
Wählen Sie die Registerkarte „Aufträge“ aus, um fünf verschiedene Aufträge in dieser Anwendungsvorlage anzuzeigen: Sie können die Funktion für Aufträge nutzen, um lösungsweite Vorgänge auszuführen. Hier verwenden die Aufträge des digitalen Verteilzentrums die Funktion für Gerätebefehle und -zwillinge zum Ausführen von Aufgaben wie:
   * Kalibrieren der Kamera vor dem Initiieren der Paketerkennung 
   * Regelmäßiges Aktualisieren der Kamerafirmware
   * Ändern des Telemetrieintervalls zum Verwalten von Datenuploads

> [!div class="mx-imgBorder"]
> ![Screenshot: Liste der Aufträge in der Anwendung für digitale Verteilzentren](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie diese Anwendung nicht mehr verwenden möchten, löschen Sie die Anwendungsvorlage, indem Sie **Verwaltung** > **Anwendungseinstellungen** besuchen und auf **Löschen** klicken.

> [!div class="mx-imgBorder"]
> ![Screenshot: Löschen der Anwendung, wenn Sie sie nicht mehr benötigen](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Lösungsarchitektur digitaler Verteilzentren finden Sie hier:

> [!div class="nextstepaction"]
> [Architektur der IoT Central-Anwendungsvorlage für digitale Verteilzentren](./architecture-digital-distribution-center.md)
