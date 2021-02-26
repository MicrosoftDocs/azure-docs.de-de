---
title: 'Tutorial: Erstellen einer App für die ständige Überwachung von Patienten mit Azure IoT Central | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie mit Azure IoT Central-Anwendungsvorlagen eine Anwendung für die ständige Überwachung von Patienten erstellen.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: f98482a43516683499e83bebafe4eeadc5b7a8b7
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832503"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Tutorial: Bereitstellen einer App für die ständige Überwachung von Patienten und exemplarische Vorgehensweise für die zugehörige Vorlage

In diesem Tutorial wird für Sie als Lösungsentwickler veranschaulicht, wie Sie in dieses Thema einsteigen können, indem Sie eine IoT Central-Vorlage für eine Anwendung für die ständige Überwachung von Patienten bereitstellen. Sie erfahren, wie Sie die Vorlage bereitstellen und verwenden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Anwendungsvorlage
> * Exemplarische Vorgehensweise für die Anwendungsvorlage

## <a name="prerequisites"></a>Voraussetzungen

Es wird ein Azure-Abonnement empfohlen. Alternativ können Sie eine kostenlose 7-Tage-Testversion verwenden. Wenn Sie kein Azure-Abonnement besitzen, können Sie auf der Seite [Azure-Anmeldeseite](https://aka.ms/createazuresubscription) eines erstellen.

## <a name="create-an-application-template"></a>Erstellen einer Anwendungsvorlage

Navigieren Sie zur [Anwendungs-Manager-Website von Azure IoT Central](https://apps.azureiotcentral.com/). Wählen Sie auf der linken Navigationsleiste **Erstellen** und anschließend die Registerkarte **Gesundheitswesen** aus.

:::image type="content" source="media/app-manager-health.png" alt-text="App-Vorlage für das Gesundheitswesen":::

Wählen Sie die Schaltfläche **App erstellen** aus, um mit der Erstellung Ihrer Anwendung zu beginnen, und melden Sie sich dann mit einem persönlichen Microsoft-Konto oder mit einem Geschäfts-, Schul- oder Unikonto an. Sie gelangen zur Seite **Neue Anwendung**.

![Erstellen einer Anwendung: Gesundheitswesen](media/app-manager-health-create.png)

![Erstellen einer Anwendung: Abrechnungsinformationen im Bereich Gesundheitswesen](media/app-manager-health-create-billinginfo.png)

Erstellung Sie Ihre Anwendung wie folgt:

1. Azure IoT Central schlägt entsprechend der von Ihnen ausgewählten Vorlage automatisch einen Anwendungsnamen vor. Sie können diesen Namen übernehmen oder einen eigenen aussagekräftigen Anwendungsnamen eingeben, z. B. **Ständige Überwachung von Patienten**. Azure IoT Central generiert für Sie außerdem entsprechend dem Anwendungsnamen ein Präfix mit einer eindeutigen URL. Sie können dieses URL-Präfix in einen einprägsameren Wert ändern.

2. Sie können auswählen, ob Sie die Anwendung unter dem Tarif *Free* oder unter einem der Tarife vom Typ *Standard* erstellen möchten. Anwendungen, die Sie unter dem Free-Tarif erstellen, sind sieben Tage lang kostenlos, bevor sie ablaufen, und es sind bis zu fünf kostenlose Geräte zulässig. Sie können eine Anwendung vor dem Ablauf jederzeit vom Free-Tarif auf einen Standard-Tarif umstellen. Bei Wahl des Free-Tarifs müssen Sie Ihre Kontaktdaten eingeben und auswählen, ob Sie Informationen und Tipps von Microsoft erhalten möchten. Anwendungen, die Sie unter einem Standard-Tarif erstellen, unterstützen bis zu zwei kostenlose Geräte, und Sie müssen Ihre Informationen zum Azure-Abonnement für die Abrechnung eingeben.

3. Wählen Sie unten auf der Seite die Option **Erstellen** aus, um Ihre Anwendung bereitzustellen.

## <a name="walk-through-the-application-template"></a>Exemplarische Vorgehensweise für die Anwendungsvorlage

### <a name="dashboards"></a>Dashboards

Nach der Bereitstellung der App-Vorlage gelangen Sie zunächst zum **Lamna-Dashboard für die Überwachung interner Patienten**. Lamna Healthcare ist ein fiktives Krankenhaus mit zwei Einrichtungen: Woodgrove Hospital und Burkville Hospital. Das Operatordashboard für Woodgrove Hospital bietet folgende Möglichkeiten:

* Zeigen Sie Gerätetelemetriedaten und -eigenschaften an, z. B. den **Akkustand** oder den **Konnektivitätsstatus** Ihres Geräts.

* Zeigen Sie den **Raumplan** und den Standort des Smart Vitals Patch-Geräts an.

* Führen Sie die **erneute Bereitstellung** des Smart Vitals Patch-Geräts für einen neuen Patienten durch.

* Zeigen Sie ein Beispiel für ein **Anbieterdashboard** an, das von einem Pflegeteam des Krankenhauses ggf. zum Nachverfolgen der Patienten genutzt wird.

* Ändern Sie den **Patientenstatus** Ihres Geräts, um anzugeben, ob das Gerät für einen internen oder externen Patienten verwendet wird.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Status stationärer Patienten":::

Sie können auch **Go to remote patient dashboard** (Zu Dashboard für externe Patienten wechseln) auswählen, um das zweite Operatordashboard für Burkville Hospital anzuzeigen. Dieses Dashboard enthält ähnliche Aktionen, Telemetriedaten und Informationen. Darüber hinaus werden mehrere verwendete Geräte angezeigt, für die Sie jeweils die **Firmware aktualisieren** können.

:::image type="content" source="media/lamna-remote.png" alt-text="Remote-Operatordashboard":::

### <a name="device-templates"></a>Gerätevorlagen

Wenn Sie **Gerätevorlagen** auswählen, werden die beiden Gerätetypen in der Vorlage angezeigt:

* **Smart Vitals Patch**: Bei diesem Gerät handelt es sich um ein Pflaster zur Messung verschiedener Vitalwerte. Es kann für die Überwachung von Patienten innerhalb und außerhalb des Krankenhauses verwendet werden. Wenn Sie die Vorlage auswählen, sehen Sie, dass das Pflaster Gerätedaten wie Akkustand und Gerätetemperatur sowie Daten zum Gesundheitszustand des Patienten wie Atemfrequenz und Blutdruck sendet.

* **Smart Knee Brace**: Bei diesem Gerät handelt es sich um eine Stützmanschette für das Knie, die von Patienten mit einem neuen Kniegelenk verwendet wird. Wenn Sie auf diese Vorlage klicken, werden Funktionen wie Gerätedaten, Bewegungsumfang und Beschleunigung angezeigt.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Smart Patch-Vorlage":::

### <a name="device-groups"></a>Gerätegruppen

Verwenden Sie Gerätegruppen, um mehrere Geräte logisch zu gruppieren und anschließend Massenabfragen oder -vorgänge für sie ausführen zu können.

Wenn Sie die Registerkarte „Gerätegruppen“ auswählen, wird für jede Gerätevorlage in der Anwendung eine Standardgerätegruppe angezeigt. Es wurden auch zwei zusätzliche Beispielgerätegruppen erstellt: eine für bereitzustellende Geräte (**Provision devices**) und eine für Geräte mit veralteter Firmware (**Devices with outdated firmware**). Diese Beispielgerätegruppen können als Eingaben für die Ausführung einiger [Aufträge](#jobs) in der Anwendung verwendet werden.

### <a name="rules"></a>Regeln

Wenn Sie **Regeln** auswählen, werden die drei Regeln in der Vorlage angezeigt:

* **Brace temperature high** (Hohe Manschettentemperatur): Diese Regel wird ausgelöst, wenn die Gerätetemperatur der intelligenten Kniemanschette fünf Minuten lang über 95 &deg;F (35 °C) liegt. Verwenden Sie diese Regel, um den Patienten und das Pflegeteam zu informieren und das Gerät per Remotesteuerung herunterzukühlen.

* **Fall detected** (Sturz erkannt): Diese Regel wird ausgelöst, wenn ein Sturz des Patienten erkannt wird. Verwenden Sie diese Regel, um eine Aktion zum Alarmieren eines Teams zu konfigurieren, das dem gestürzten Patienten zur Hilfe eilt.

* **Patch battery low** (Niedriger Akkustand des Patchgeräts): Diese Regel wird ausgelöst, wenn der Akkustand des Geräts unter zehn Prozent sinkt. Verwenden Sie diese Regel, um für den Patienten eine Benachrichtigung mit dem Hinweis auszulösen, dass er sein Gerät aufladen sollte.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Regeln":::

### <a name="jobs"></a>Aufträge

Aufträge ermöglichen die Ausführung von Massenvorgängen für eine Gruppe von Geräten. Dazu werden [Gerätegruppen](#device-groups) als Eingabe verwendet. Die Anwendungsvorlage verfügt über zwei Beispielaufträge, die ein Operator ausführen kann:

* **Update knee brace firmware** (Firmware der Kniemanschette aktualisieren): Dieser Auftrag ermittelt Geräte in der Gerätegruppe **Devices with outdated firmware** (Geräte mit veralteter Firmware) und führt einen Befehl aus, um diese Geräte auf die neueste Firmwareversion zu aktualisieren. Bei diesem Beispielauftrag wird davon ausgegangen, dass die Geräte einen **Aktualisierungsbefehl** verarbeiten und die Firmwaredateien anschließend aus der Cloud abrufen können.  

* **Re-provision devices** (Geräte erneut bereitstellen): Sie verfügen über eine Reihe von Geräten, die kürzlich an das Krankenhaus zurückgegeben wurden. Dieser Auftrag ermittelt Geräte in der Gerätegruppe **Provision Devices** (Geräte bereitstellen) und führt einen Befehl aus, um sie für die nächsten Patienten bereitzustellen.

### <a name="devices"></a>Geräte

Wählen Sie die Registerkarte **Geräte** und anschließend eine Instanz von **Smart Knee Brace** aus. Es sind drei Ansichten verfügbar, in denen Sie sich Informationen zum jeweils ausgewählten Gerät ansehen können. Diese Ansichten werden erstellt und veröffentlicht, wenn Sie die Gerätevorlage für Ihr Gerät erstellen. Daher sind diese Ansichten für alle Geräte, die Sie verbinden oder simulieren, einheitlich.

Die Ansicht **Dashboard** bietet eine Übersicht über für Operatoren relevante Telemetriedaten und Eigenschaften des Geräts.

Auf der Registerkarte **Eigenschaften** können Sie Cloudeigenschaften bearbeiten und Geräteeigenschaften lesen/schreiben.

Auf der Registerkarte **Befehle** können Sie Befehle auf dem Gerät ausführen.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Dashboard Kniemanschette":::

### <a name="data-export"></a>Datenexport

Mit dem Datenexport können Sie Ihre Gerätedaten fortlaufend in andere Azure-Dienste exportieren – einschließlich in [Azure API for FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht mehr verwenden möchten, sollten Sie sie löschen, indem Sie unter **Verwaltung > Anwendungseinstellungen** auf **Löschen** klicken.

:::image type="content" source="media/admin-delete.png" alt-text="Bereinigen der Ressourcen":::

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, um sich über die Erstellung eines Anbieterdashboards zu informieren, mit dem eine Verbindung mit Ihrer IoT Central-Anwendung hergestellt wird.

> [!div class="nextstepaction"]
> [Erstellen eines Power BI-Anbieterdashboards](howto-health-data-triage.md)