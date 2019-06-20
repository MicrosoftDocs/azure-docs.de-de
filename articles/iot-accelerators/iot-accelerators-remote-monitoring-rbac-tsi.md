---
title: Remoteüberwachung-Datenzugriffssteuerung – Azure | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie die Zugriffssteuerung für den Time Series Insights-Telemetrie-Explorer im Solution Accelerator der Remoteüberwachung konfigurieren können.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827210"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Konfigurieren der Zugriffssteuerung für den Time Series Insights-Telemetrie-Explorer

In diesem Artikel erfahren Sie, wie Sie die Zugriffssteuerung für den Time Series Insights-Explorer im Solution Accelerator der Remoteüberwachung konfigurieren. Damit die Benutzer des Solution Accelerators auf den Time Series Insights-Explorer zugreifen können, müssen Sie jedem Benutzer Datenzugriff gewähren.

Datenzugriffsrichtlinien gewähren Berechtigungen für die Erstellung von Datenabfragen, für die Bearbeitung von Verweisdaten in der Umgebung und für die Freigabe gespeicherter Abfragen und Perspektiven, die der Umgebung zugeordnet sind.

## <a name="grant-data-access"></a>Gewähren des Datenzugriffs

Führen Sie die folgenden Schritte aus, um einem Benutzerprinzipal Datenzugriff zu gewähren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie Ihre Time Series Insights-Umgebung. Geben Sie **Time Series** im **Suchfeld** ein. Wählen Sie **Time Series Environment** (Time Series-Umgebung) in den Suchergebnissen aus. 

3. Wählen Sie Ihre Time Series Insights-Umgebung in der Liste aus.

4. Wählen Sie **Datenzugriffsrichtlinien** und dann **+ Hinzufügen** aus.
    ![Verwalten der Time Series Insights-Quelle – Umgebung](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Wählen Sie **Benutzer auswählen** aus.  Suchen Sie den Benutzernamen oder die E-Mail-Adresse des Benutzers, den Sie hinzufügen möchten. Klicken Sie auf **Auswählen**, um die Auswahl zu bestätigen. 

    ![Verwalten der Time Series Insights-Quelle – Hinzufügen](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Wählen Sie **Rolle auswählen** aus. Wählen Sie die geeignete Zugriffsrolle für den Benutzer aus:
   - Wählen Sie **Mitwirkender** aus, wenn Sie dem Benutzer das Ändern von Verweisdaten und das Freigeben gespeicherter Abfragen und Perspektiven für andere Benutzer der Umgebung ermöglichen möchten. 
   - Wählen Sie andernfalls **Leser** aus, um dem Benutzer das Abfragen von Daten und das Speichern persönlicher (nicht freigegebener) Abfragen in der Umgebung zu ermöglichen.

     Wählen Sie **OK** aus, um die Rollenauswahl zu bestätigen.

     ![Verwalten der Time Series Insights-Quelle – Benutzer auswählen](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Wählen Sie auf der Seite **Benutzerrolle auswählen** die Option **OK** aus.

    ![Verwalten der Time Series Insights-Quelle – Rolle auswählen](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. Auf der Seite **Datenzugriffsrichtlinien** sind die Benutzer und die Rollen der einzelnen Benutzer aufgeführt.

    ![Verwalten der Time Series Insights-Quelle – Ergebnisse](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie die Zugriffssteuerung für den Time Series Insights-Explorer im Solution Accelerator der Remoteüberwachung gewähren.

Weitere konzeptuelle Informationen zum Solution Accelerator für die Remoteüberwachung finden Sie unter [Remoteüberwachungsarchitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md).

Weitere Informationen zum Anpassen der Remoteüberwachungslösung finden Sie unter [Anpassen und erneutes Bereitstellen eines Microservices](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->