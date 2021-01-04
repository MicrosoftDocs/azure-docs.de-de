---
title: Tutorial für intelligente IoT-Bestandsverwaltung | Microsoft-Dokumentation
description: Ein Tutorial für die IoT Central-Anwendungsvorlage für intelligente Bestandsverwaltung
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: b1b7069cb723bffd758c3603cc957329dd6d2a9b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463278"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Tutorial: Bereitstellen und Durchlaufen einer Anwendungsvorlage für intelligente Bestandsverwaltung



Dieses Tutorial zeigt, wie Sie durch Bereitstellen einer IoT Central-Vorlage in die **intelligente Bestandsverwaltung** einsteigen. Sie erfahren, wie Sie die Vorlage bereitstellen, was im Lieferumfang enthalten ist und welche nächsten Schritte sinnvoll sind.

In diesem Tutorial lernen Sie Folgendes: 
* Erstellen einer Anwendung für intelligente Bestandsverwaltung 
* Einführung in die Anwendung 

## <a name="prerequisites"></a>Voraussetzungen

* Zum Bereitstellen dieser App sind keine besonderen Voraussetzungen erforderlich.
* Ein Azure-Abonnement ist empfehlenswert, Sie können es aber auch ohne versuchen.

## <a name="create-smart-inventory-management-application-template"></a>Erstellen einer Anwendungsvorlage für intelligente Bestandsverwaltung

Mithilfe der folgenden Schritte können Sie eine Anwendung erstellen.

1. Navigieren Sie zur Anwendungs-Manager-Website von Azure IoT Central. Wählen Sie auf der linken Navigationsleiste **Erstellen** aus, und klicken Sie dann auf die Registerkarte **Einzelhandel**.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png" alt-text="Screenshot: Auswählen der Anwendungsvorlage für intelligente Bestandsverwaltung":::

2. Wählen Sie die Registerkarte **Einzelhandel** und unter **Intelligente Bestandsverwaltung** die Option **App erstellen** aus.

3. Mit **App erstellen** wird ein neues Anwendungsformular geöffnet, wobei die erforderlichen Details wie unten dargestellt ausgefüllt werden.
    **Anwendungsname:** Sie können den vorgeschlagenen Standardnamen verwenden oder einen eigenen Anwendungsnamen eingeben.
    **URL:** Sie können die vorgeschlagene Standard-URL verwenden oder eine eigene, einfach merkbare und benutzerfreundliche URL eingeben. Als Nächstes wird die Standardeinstellung empfohlen, wenn Sie bereits über ein Azure-Abonnement verfügen. Sie können mit dem Tarif für die kostenlose 7-Tage-Testversion beginnen und jederzeit vor Ablauf der kostenlosen Testversion auf einen Standard-Tarif umstellen.
    **Abrechnungsinfo**: Die Detailangaben zum Verzeichnis, dem Azure-Abonnement und der Region sind für die Bereitstellung der Ressourcen erforderlich.
    **Erstellen**: Wählen Sie unten auf der Seite „Erstellen“ aus, um Ihre Anwendung bereitzustellen.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png" alt-text="Screenshot: Erstellen einer App auf der Grundlage der Anwendungsvorlage für intelligente Bestandsverwaltung":::

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png" alt-text="Screenshot: Abrechnungsoptionen beim Erstellen der Anwendung":::

## <a name="walk-through-the-application"></a>Einführung in die Anwendung 

### <a name="dashboard"></a>Dashboard 

Nach erfolgreicher Bereitstellung der App-Vorlage ist das Standarddashboard ein Portal für den Betreiber intelligenter Bestandsverwaltung. Northwind Trader ist ein fiktiver Anbieter von intelligenter Bestandsverwaltung, der Lagerräume mit Bluetooth Low Energy (BLE) und Einzelhandelsfilialen mit RFID (Radio Frequency Identification) verwaltet. Auf diesem Dashboard sehen Sie zwei verschiedene Gateways, die Telemetrie zum Bestand zusammen mit zugeordneten Befehlen, Aufträgen und Aktionen bereitstellen, die Sie ausführen können. Dieses Dashboard ist vorkonfiguriert, sodass die Aktivität von kritischen Vorgängen bei der intelligenten Bestandsverwaltung hervorgehoben wird.
Das Dashboard ist logisch in zwei verschiedene Gateway-Geräteverwaltungsvorgänge unterteilt. 
   * Das Lager wird mit einem festen BLE-Gateway und BLE-Tags auf den Paletten bereitgestellt, um den Bestand in einer größeren Einrichtung nachverfolgen zu können.
   * Die Einzelhandelsfiliale wird mit einem festen RFID-Gateway und RFID-Tags auf Ebene der einzelnen Posten implementiert, um den Bestand in einem Outlet nachzuverfolgen.
   * Anzeigen von Gatewaystandort, Status und zugehörigen Details 

> [!div class="mx-imgBorder"]
> ![Screenshot: obere Hälfte des Dashboards für die intelligente Bestandsverwaltung](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Sie können die Gesamtanzahl der Gateways, der aktiven und der unbekannten Tags einfach nachverfolgen.
   * Sie können Vorgänge der Geräteverwaltung durchführen, beispielsweise Firmwareupdates, Deaktivieren und Aktivieren von Sensoren sowie Aktualisieren von Sensorschwellenwerten, Telemetrieintervallen und Geräteserviceverträgen.
   * Gatewaygeräte können mithilfe eines vollständigen oder inkrementellen Scans bedarfsgesteuert die Bestandsverwaltung durchführen.

> [!div class="mx-imgBorder"]
> ![Screenshot: untere Hälfte des Dashboards für die intelligente Bestandsverwaltung](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Gerätevorlage
Klicken Sie auf die Registerkarte „Gerätevorlagen“, dann wird das Gatewayfunktionsmodell angezeigt. Ein Funktionsmodell ist um zwei unterschiedliche Schnittstellen strukturiert: **Gatewaytelemetrie und -eigenschaften** und **Gatewaybefehle**.

**Gatewaytelemetrie und -eigenschaften:** Diese Schnittstelle stellt die gesamte Telemetrie im Zusammenhang mit Sensor-, Standort- und Geräteinformationen sowie Gerätezwillings-Eigenschaftsfunktionen wie Gatewayschwellenwerten und Aktualisierungsintervallen dar.

> [!div class="mx-imgBorder"]
> ![Screenshot: Gatewaygerätevorlage für den Bestand in der Anwendung](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Gatewaybefehle:** Diese Schnittstelle organisiert alle Gatewaybefehlsfunktionen.

> [!div class="mx-imgBorder"]
> ![Screenshot: Schnittstelle für Gatewaybefehle in der Gatewaygerätevorlage für den Bestand](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Regeln
Wählen Sie die Registerkarte „Regeln“ aus, um zwei verschiedene Regeln anzuzeigen, die in dieser Anwendungsvorlage vorhanden sind. Diese Regeln sind so konfiguriert, dass sie zwecks weiterer Untersuchungen E-Mail-Benachrichtigungen an die Operatoren senden.

**Gateway offline:** Diese Regel wird ausgelöst, wenn das Gateway für einen längeren Zeitraum keine Meldung an die Cloud sendet. Das Gateway reagiert möglicherweise aufgrund eines niedrigen Akkuladestands, eines Verbindungsverlusts oder des Gerätezustands nicht.

**Unbekannte Tags:** Es ist wichtig, alle RFID- und BLE-Tags nachzuverfolgen, die einer Ressource zugeordnet sind. Wenn das Gateway zu viele unbekannte Tags erkennt, deutet dies auf Probleme bei der Synchronisierung in den Tagbeschaffungsanwendungen hin.

> [!div class="mx-imgBorder"]
> ![Screenshot: Liste der Regeln in der Anwendung für intelligente Bestandsverwaltung](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Aufträge
Wählen Sie die Registerkarte „Aufträge“ aus, um fünf verschiedene Aufträge in dieser Anwendungsvorlage anzuzeigen: Sie können die Funktion für Aufträge nutzen, um lösungsweite Vorgänge auszuführen. Hier verwenden die Aufträge der Bestandsverwaltung die Funktion für Gerätebefehle und -zwillinge zum Ausführen von Aufgaben wie:
   * Deaktivieren von Lesern auf allen Gateways
   * Ändern des Schwellenwerts für die Telemetrie 
   * Durchführen von Bestandsüberprüfung in der gesamten Lösung nach Bedarf

> [!div class="mx-imgBorder"]
> ![Screenshot: Liste der Aufträge in der Anwendung für intelligente Bestandsverwaltung](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht mehr verwenden möchten, löschen Sie die Anwendungsvorlage, indem Sie **Verwaltung** > **Anwendungseinstellungen** besuchen und auf **Löschen** klicken.

> [!div class="mx-imgBorder"]
> ![Screenshot: Löschen der Anwendung, wenn Sie sie nicht mehr benötigen](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich ausführlicher über die intelligente Bestandsverwaltung: 
> [!div class="nextstepaction"]
> [Architektur der IoT Central-Anwendungsvorlage für intelligente Bestandsverwaltung](./architecture-smart-inventory-management.md)
* Weitere Informationen zu anderen [IoT Central-Einzelhandelsvorlagen](./overview-iot-central-retail.md)
* Lesen Sie die [Übersicht über IoT Central](../core/overview-iot-central.md).