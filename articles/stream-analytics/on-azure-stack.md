---
title: Ausführen von Azure Stream Analytics auf Azure Stack (Vorschau)
description: Erstellen Sie einen Azure Stream Analytics-Edgeauftrag, und stellen Sie ihn über die IoT Edge-Runtime auf dem Azure Stack Hub bereit.
ms.service: stream-analytics
author: raan
ms.author: raan
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: 21cf432576829b575d70a94227f28df373a4d899
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126157"
---
# <a name="run-azure-stream-analytics-on-azure-stack-preview"></a>Ausführen von Azure Stream Analytics auf Azure Stack (Vorschau)

> [!IMPORTANT]
> Dieses Feature befindet sich in der Vorschauphase. Die Verwendung in einer Produktionsumgebung wird nicht empfohlen.

Sie können Azure Stream Analytics auf Azure Stack Hub als IoT Edge-Modul ausführen. Dem IoT Edge-Modul wurden Konfigurationen hinzugefügt, die es ihm ermöglichen, mit Blobspeicher, Event Hubs und IoT Hubs zu interagieren, die in einem Azure Stack Hub-Abonnement ausgeführt werden, indem benutzerdefinierte URLs zugelassen werden, die in jeder Azure Stack Hub-Anwendung zu finden sind.

Mit Stream Analytics auf Azure Stack können Sie tatsächliche Hybridarchitekturen für die Streamverarbeitung in Ihrer eigenen privaten, autonomen Cloud erstellen, die mit cloudnativen Apps über konsistente Azure-Dienste lokal verbunden oder getrennt werden können. 

In diesem Artikel erfahren Sie, wie Sie in einem Azure Stack Hub-Abonnement Daten von einem IoT Hub oder Event Hub zu einem anderen Event Hub oder Blobspeicher streamen und mit Stream Analytics verarbeiten können.

## <a name="set-up-environments"></a>Einrichten von Umgebungen

Azure Stream Analytics ist ein Hybriddienst auf Azure Stack Hub. Es handelt sich um ein IoT Edge-Modul, das in Azure konfiguriert ist, aber auf Azure Stack Hub ausgeführt werden kann.  

Wenn Sie neu bei Azure Stack Hub oder IoT Edge sind, befolgen Sie die folgenden Anweisungen zum Einrichten von Umgebungen.

### <a name="prepare-the-azure-stack-hub-environment"></a>Vorbereiten der Azure Stack Hub-Umgebung

Erstellen Sie ein Azure Stack Hub-Abonnement. Weitere Informationen finden Sie im [Tutorial zum Erstellen eines Azure Stack Hub-Abonnements.](/azure-stack/user/azure-stack-subscribe-services/)

Wenn Sie Azure Stack Hub auf Ihrem eigenen Server bewerten möchten, können Sie das Azure Stack Development Kit (ASDK) verwenden.  Weitere Informationen zum ASDK finden Sie in der [ASDK-Übersicht](/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

Um Azure Stream Analytics auf Azure Stack Hub ausführen zu können, muss Ihr Gerät über die IoT Edge-Runtime verfügen und eine Netzwerkverbindung zum Azure Stack Hub aufweisen oder eine VM sein, die auf dem Azure Stack Hub ausgeführt wird. Die IoT Edge-Laufzeit ermöglicht die Integration von Stream Analytics Edge-Aufträgen in Azure Storage und Azure Event Hubs, die auf Ihrem Azure Stack Hub ausgeführt werden. Weitere Informationen finden Sie unter [Azure Stream Analytics auf IoT Edge](stream-analytics-edge.md). 

Zusätzlich zum Netzwerkzugriff auf die Azure Stack Hub-Ressourcen benötigt das IoT Edge-Gerät (oder die VM) Zugriff auf den Azure IoT Hub in der öffentlichen Azure-Cloud, um das Stream Analytics-Modul zu konfigurieren. 

Die folgenden Leitfäden zeigen, wie Sie die IoT Edge Runtime auf Ihrem Gerät oder Ihrer VM einrichten:

* [Installieren der Azure IoT Edge-Runtime unter Windows](../iot-edge/how-to-install-iot-edge.md)
* [Installieren der Azure IoT Edge-Runtime auf Debian-basierten Linux-Systemen](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Erstellen eines Azure Stream Analytics-Edge-Auftrags

ASA Edge-Aufträge werden in Containern ausgeführt, die auf Azure IoT Edge-Geräten bereitgestellt sind. Sie bestehen aus zwei Teilen:
* Einem Cloudteil, der für die Auftragsdefinition zuständig ist: Benutzer definieren Eingaben, Ausgaben, Abfragen und andere Einstellungen (Ereignisse in falscher Reihenfolge usw.) in der Cloud.
* Einem Modul, das auf Ihren IoT-Geräten ausgeführt wird. Es enthält die ASA-Engine und empfängt die Auftragsdefinition aus der Cloud.

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Wenn Sie einen Azure Stream Analytics-Auftrag zur Ausführung auf einem IoT Edge-Gerät erstellen, muss er so gespeichert werden, dass er vom Gerät aufgerufen werden kann. Sie können ein bereits vorhandenes Azure Storage-Konto verwenden oder ein neues erstellen.
1. Wechseln Sie im Azure-Portal zu **Ressource erstellen > Storage > Speicherkonto – Blob, Datei, Tabelle, Warteschlange**.
2. Geben Sie die folgenden Werte an, um Ihr Speicherkonto zu erstellen:

   | Feld | Wert |
   | --- | --- |
   | Name | Geben Sie einen eindeutigen Namen für Ihr Speicherkonto an. |
   | Standort | Wählen Sie einen Standort in Ihrer Nähe aus.|
   | Subscription | Wählen Sie das gleiche Abonnement wie für Ihren IoT Hub.|
   | Ressourcengruppe | Es wird empfohlen, die gleiche Ressourcengruppe für alle Testressourcen zu verwenden, die Sie während der [IoT Edge-Schnellstarts](../iot-edge/quickstart.md) und -Tutorials erstellen. Beispielsweise **IoTEdgeResources**. |

3. Behalten Sie in den restlichen Feldern die Standardwerte bei, und wählen Sie **Erstellen**.


### <a name="create-a-new-job"></a>Erstellen eines neuen Auftrags

1. Navigieren Sie im Azure-Portal zu **Ressource erstellen > Internet der Dinge > Stream Analytics-Auftrag**.
2. Geben Sie die folgenden Werte an, um Ihr Speicherkonto zu erstellen:

   | Feld | value |
   | --- | --- |
   | Auftragsname | Geben Sie einen Namen für Ihren Auftrag an. Beispielsweise **IoTEdgeJob** |
   | Subscription | Wählen Sie das gleiche Abonnement wie für Ihren IoT Hub.|
   | Ressourcengruppe | Es wird empfohlen, die gleiche Ressourcengruppe für alle Testressourcen zu verwenden, die Sie während der [IoT Edge-Schnellstarts](../iot-edge/quickstart.md) und -Tutorials erstellen. Beispielsweise **IoTEdgeResources**. |
   | Standort | Wählen Sie einen Standort in Ihrer Nähe aus. |
   | Hostumgebung | Wählen Sie **Edge** aus. |

3. Klicken Sie auf **Erstellen**.

### <a name="configure-your-job"></a>Konfigurieren des Auftrags

Nachdem Ihr Stream Analytics-Auftrag im Azure-Portal erstellt wurde, können Sie ihn mit einer Eingabe, einer Ausgabe und einer Abfrage konfigurieren, die für die Daten ausgeführt wird, die er durchläuft. Sie können manuell Eingaben von einem IoT Hub oder einem Event Hub in einem Azure Stack Hub-Abonnement angeben.

1. Navigieren Sie im Azure-Portal zu Ihrem Stream Analytics-Auftrag.
2. Wählen Sie unter **Konfigurieren** die Option **Speicherkontoeinstellungen** aus, und wählen Sie dann das Speicherkonto aus, das Sie im vorherigen Schritt erstellt haben.
   > [!div class="mx-imgBorder"]
   > [ ![Einstellung des Auftragsspeicherkontos](media/on-azure-stack/storage-account-settings.png) ](media/on-azure-stack/storage-account-settings.png#lightbox)
3. Wählen Sie unter **Auftragstopologie** die Option **Eingaben** und dann **Datenstromeingabe hinzufügen.**
4. Wählen Sie **IoT Hub** , **Event Hub** oder **Edge Hub** aus der Dropdownliste aus. 
5. Wenn es sich bei der Eingabe um einen Event Hub oder IoT Hub in einem Azure Stack Hub-Abonnement handelt, geben Sie die Informationen wie unten dargestellt manuell ein.

   #### <a name="event-hub"></a>Event Hub

   | Feld | Wert |
   | --- | --- |
   | Eingabealias | Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen. |
   | Service Bus-Namespace | Der Namespace ist ein Container für einen Satz mit Nachrichtenentitäten. Sie haben bei der Erstellung einer neuen Event Hub-Instanz auch den Namespace erstellt. (Beispiel: *sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com* ) |
   | Event Hub-Name | Der Name des Event Hubs, der als Eingabe verwendet wird. |
   | Event Hub-Richtlinienname | Die SAS-Richtlinie, die Zugriff auf den Event Hub ermöglicht. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Diese Option wird automatisch ausgefüllt, sofern Sie nicht die Option zum manuellen Festlegen der Event-Hub-Einstellungen wählen. |
   | Event Hub-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Autorisierung des Zugriffs auf Event Hub verwendet wird. Diese Option wird automatisch ausgefüllt, es sei denn, Sie wählen die Option zum manuellen Festlegen der Event-Hub-Einstellungen. Sie finden sie in den Event Hub-Einstellungen. |
   | Event Hub-Consumergruppe (optional) | Es wird dringend empfohlen, für jeden Stream Analytics-Auftrag eine eigene Consumergruppe zu verwenden. Diese Zeichenfolge identifiziert die Consumergruppe, die zum Erfassen von Daten aus dem Event Hub verwendet werden soll. Wenn keine Consumergruppe angegeben wird, verwendet der Stream Analytics-Auftrag die $Default-Consumergruppe. |
   | Partitionsanzahl | Die Partitionsanzahl ist die Anzahl der Partitionen in einem Event Hub. |

   > [!div class="mx-imgBorder"]
   > [ ![Event Hub-Eingabe](media/on-azure-stack/event-hub-input.png) ](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT Hub

   | Feld | Wert |
   | --- | --- |
   | Eingabealias | Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen. |
   | IoT Hub | Der Name von IoT Hub, der als Eingabe verwendet wird. (Beispiel: *<IoT Hub Name>.shanghai.azurestack.corp.microsoft.com* ) |
   | Name der SAS-Richtlinie | Die SAS-Richtlinie, die Zugriff auf IoT Hub ermöglicht. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
   | Schlüssel für SAS-Richtlinie | Der Schlüssel für den gemeinsamen Zugriff, der für die Autorisierung des Zugriffs auf IoT Hub verwendet wird. Diese Option wird automatisch ausgefüllt, es sei denn, Sie wählen die Option zum manuellen Festlegen der IoT Hub-Einstellungen. |
   | Consumergruppe (optional) | Es wird dringend empfohlen, für jeden Stream Analytics-Auftrag eine andere Consumergruppe zu verwenden. Die Consumergruppe, die zum Erfassen von Daten aus IoT Hub verwendet werden soll. Stream Analytics verwendet die $Default-Consumergruppe, sofern nicht anders angegeben. |
   | Partitionsanzahl | Die Partitionsanzahl ist die Anzahl der Partitionen in einem Event Hub. |

   > [!div class="mx-imgBorder"]
   > [ ![IoT Hub-Eingabe](media/on-azure-stack/iot-hub-input.png) ](media/on-azure-stack/iot-hub-input.png#lightbox)

6. Behalten Sie in den restlichen Feldern die Standardwerte bei, und wählen Sie Speichern.
7. Öffnen Sie unter Auftragstopologie die Option Ausgaben, und wählen Sie dann Hinzufügen.
8. Wählen Sie Blob Storage, Event Hub oder Edge Hub aus der Dropdownliste aus.
9. Wenn es sich bei der Ausgabe um Event Hub oder Blob Storage in einem Azure Stack Hub-Abonnement handelt, geben Sie die Informationen wie unten dargestellt manuell ein.

   #### <a name="event-hub"></a>Event Hub

   | Feld | value |
   | --- | --- |
   | Ausgabealias | Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Event Hub weiterzuleiten. |
   | Service Bus-Namespace | Ein Container für einen Satz von Nachrichtenentitäten. Beim Erstellen eines neuen Event Hub haben Sie auch einen Service Bus-Namespace erstellt (Beispiel: *sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com* ) |
   | Event Hub-Name | Der Name Ihrer Event Hub-Ausgabe. |
   | Event Hub-Richtlinienname | Die Richtlinie für den gemeinsamen Zugriff, die Sie auf der Registerkarte Konfigurieren des Event Hubs erstellen können. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
   | Event Hub-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Event Hub-Namespace verwendet wird. |

   > [!div class="mx-imgBorder"]
   > [ ![Event Hub-Ausgabe](media/on-azure-stack/event-hub-output.png) ](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob Storage 

   | Feld | value |
   | --- | --- |
   | Ausgabealias | Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Blobspeicher weiterzuleiten. |
   | Speicherkonto | Der Name des Speicherkontos, an das Sie Ihre Ausgabe senden (Beispiel: *<Storage Account Name>.blob.shanghai.azurestack.corp.microsoft.com* ) |
   | Speicherkontoschlüssel | Der geheime Schlüssel, der dem Speicherkonto zugeordnet ist. Diese Option wird automatisch ausgefüllt, es sei denn, Sie wählen die Option zum manuellen Festlegen der Blob Storage-Einstellungen. |

> [!NOTE]
> Das Parquet-Format wird für Edgeaufträge auf Azure Stack Hub nicht unterstützt. Für minimale Zeilen und maximale Zeit verwenden Sie 0 oder lassen Sie sie leer.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Bereitstellen von Stream Analytics auf einer VM oder einem Gerät, das mit Azure Stack verbunden ist

1. Öffnen Sie IoT Hub im Azure-Portal. Navigieren Sie zu **IoT Edge** , und klicken Sie auf das Gerät (VM), auf das Sie diese Bereitstellung ausrichten möchten.
2. Wählen Sie **Module festlegen** aus. Wählen Sie dann **+ Hinzufügen** und anschließend **Azure Stream Analytics-Modul** aus. 
3. Wählen Sie das Abonnement und den erstellten Stream Analytics Edge-Auftrag aus. Klicken Sie auf **Speichern** , und wählen Sie **Weiter:Routen** aus.

   > [!div class="mx-imgBorder"]
   > [ ![Hinzufügen von Modulen](media/on-azure-stack/edge-modules.png) ](media/on-azure-stack/edge-modules.png#lightbox)

4. Klicken Sie auf **Überprüfen + erstellen >** .
5. Wählen Sie im Schritt **Überprüfen + erstellen** die Option **Erstellen** aus. 
   > [!div class="mx-imgBorder"]
   > [ ![Manifest](media/on-azure-stack/module-content.png) ](media/on-azure-stack/module-content.png#lightbox)
6. Vergewissern Sie sich, dass das Modul zur Liste hinzugefügt wurde.
   > [!div class="mx-imgBorder"]
   > [ ![Bereitstellungsseite](media/on-azure-stack/edge-deployment.png) ](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte
- [Azure Stream Analytics auf IoT Edge](./stream-analytics-edge.md)
- [Entwickeln von Stream Analytics Edge-Aufträgen](/stream-analytics-query/stream-analytics-query-language-reference)