---
title: Diagnostizieren und Behandeln von Problemen bei der Trennung von Geräteverbindungen mit Azure IoT Hub DPS
description: Hier erfahren Sie, wie Sie häufig auftretende Fehler im Zusammenhang mit Gerätekonnektivität beim Azure IoT Hub Device Provisioning-Dienst (Azure IoT Hub Device Provisioning Service, DPS) diagnostizieren und beheben können.
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 2d68314aab636180ff17e330c49c9859b8851b06
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950685"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Problembehandlung beim Azure IoT Hub Device Provisioning-Dienst

Konnektivitätsprobleme bei IoT-Geräten können schwierig zu beheben sein, weil es viele mögliche Fehlerursachen gibt, wie z.B. Nachweisfehler, Registrierungsfehler usw. Dieser Artikel enthält Anleitungen zum Erkennen und Beheben von Problemen bei der Gerätekonnektivität über [Azure Monitor](../azure-monitor/overview.md).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Verwenden von Azure Monitor zum Anzeigen von Metriken und Einrichten von Warnungen

Im folgenden Verfahren wird beschrieben, wie Sie eine Warnung zur Metrik des IoT Hub Device Provisioning-Diensts anzeigen und einrichten können. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie zu Ihrem IoT Hub Device Provisioning-Dienst.

3. Klicken Sie auf **Metriken**.

4. Wählen Sie die gewünschte Metrik aus. 
   <br />Derzeit gibt es drei Metriken für DPS:

    | Metrikname | BESCHREIBUNG |
    |-------|------------|
    | Nachweisversuche | Anzahl der Geräte, die versucht haben, sich beim Device Provisioning-Dienst zu authentifizieren|
    | Registrierungsversuche | Anzahl der Geräte, die versucht haben, sich nach erfolgreicher Authentifizierung bei IoT Hub zu registrieren|
    | Zugewiesenes Gerät | Anzahl der Geräte, die IoT Hub erfolgreich zugewiesen wurden|

5. Wählen Sie die gewünschte Aggregationsmethode aus, um eine visuelle Ansicht der Metrik zu erstellen. 

6. Wenn Sie eine Warnung zu einer Metrik einrichten möchten, wählen Sie oben rechts im Blatt „Metrik“ **Neue Warnungsregeln** aus. Entsprechend können Sie zum Blatt **Warnung** wechseln und **Neue Warnungsregeln** auswählen.

7. Wählen Sie **Bedingung hinzufügen** und dann die gewünschte Metrik und den Schwellenwert aus, indem Sie den Aufforderungen folgen.

Weitere Informationen finden Sie unter [Was sind klassische Warnungen in Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Verwenden von Log Analytics zum Anzeigen und Beheben von Fehlern

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie zu Ihrer IoT Hub-Instanz.

3. Wählen Sie **Diagnoseeinstellungen** aus.

4. Wählen Sie **Diagnose aktivieren**.

5. Aktivieren Sie die gewünschten Protokolle, die gesammelt werden sollen.

    | Protokollname | BESCHREIBUNG |
    |-------|------------|
    | DeviceOperations | Protokolle im Zusammenhang mit Geräteverbindungsereignissen |
    | ServiceOperations | Ereignisprotokolle im Zusammenhang mit der Verwendung des Dienst-SDKs (z.B. Erstellen oder Aktualisieren von Registrierungsgruppen)|

6. Aktivieren Sie **An Log Analytics senden** ([siehe Preise](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. Wechseln Sie im Azure-Portal unter der Ressource „Device Provisioning-Dienst“ zu **Protokolle**.

8. Klicken Sie auf **Ausführen**, um die aktuellen Ereignisse anzuzeigen.

9. Wenn Ergebnisse vorliegen, suchen Sie nach `OperationName`, `ResultType`, `ResultSignature` und `ResultDescription` (Fehlermeldung), um weitere Details zum Fehler anzuzeigen.


## <a name="common-error-codes"></a>Häufige Fehlermeldungen
Verwenden Sie diese Tabelle, um häufig auftretende Fehler zu verstehen und zu beheben.

| Fehlercode| BESCHREIBUNG | HTTP-Statuscode |
|-------|------------|------------|
| 400 | Der Text der Anforderung ist ungültig; er kann beispielsweise nicht analysiert werden, oder das Objekt kann nicht überprüft werden.| 400 Ungültiges Format |
| 401 | Das Autorisierungstoken kann nicht überprüft werden: es ist beispielsweise abgelaufen oder gilt nicht für den URI der Anforderung. Dieser Fehlercode wird auch im Rahmen des TPM-Nachweisvorgangs an Geräte zurückgegeben. | 401 – Nicht autorisiert|
| 404 | Die Device Provisioning-Dienstinstanz oder eine Ressource (z.B. eine Registrierung) ist nicht vorhanden. |404 – Nicht gefunden |
| 412 | Gemäß RFC7232 entspricht das ETag in der Anforderung nicht dem ETag der vorhandenen Ressource. | 412 Fehler bei Vorbedingung |
| 429 | Vorgänge werden vom Dienst gedrosselt. Informationen zu bestimmten Dienstgrenzwerten finden Sie unter [Grenzwerte für den IoT Hub Device Provisioning-Dienst](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | 429 Zu viele Anforderungen |
| 500 | Ein interner Fehler ist aufgetreten. | 500 Interner Serverfehler|