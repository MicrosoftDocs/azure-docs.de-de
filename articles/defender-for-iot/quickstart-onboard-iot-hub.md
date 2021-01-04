---
title: 'Schnellstart: Aktivieren des Diensts'
description: Hier erfahren Sie, wie Sie in Ihrer Azure IoT Hub-Instanz das Onboarding für den Sicherheitsdienst „Defender für IoT“ durchführen und den Dienst aktivieren.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 786fcd1a0c6d7df2c38a086a830a63f7179d7d40
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352506"
---
# <a name="quickstart-onboard-azure-defender-for-iot-service-in-iot-hub"></a>Schnellstart: Durchführen des Onboardings für den Dienst „Azure Defender für IoT“ in IoT Hub

In diesem Artikel erfahren Sie, wie Sie den Dienst „Defender für IoT“ in Ihrer IoT Hub-Instanz aktivieren. Besitzen Sie derzeit keine IoT Hub-Instanz, lesen Sie zum Einstieg die Informationen unter [Erstellen eines IoT Hubs über das Portal](../iot-hub/iot-hub-create-through-portal.md).

> [!NOTE]
> Von Defender für IoT werden derzeit nur IoT Hub-Instanzen im Standard-Tarif unterstützt.

## <a name="prerequisites-for-enabling-the-service"></a>Voraussetzungen für die Dienstaktivierung

- Log Analytics-Arbeitsbereich
  - Von Defender für IoT werden in Ihrem Log Analytics-Arbeitsbereich standardmäßig zwei Arten von Informationen gespeichert: **Sicherheitswarnungen** und **Empfehlungen**.
  - Sie können noch einen weiteren Informationstyp hinzufügen: **Rohereignisse**. Hinweis: Für die Speicherung von **Rohereignissen** in Log Analytics fallen zusätzliche Speicherkosten an.
- IoT Hub (Standard-Tarif)
- Erfüllen aller [Dienstvoraussetzungen](service-prerequisites.md)

## <a name="enable-defender-for-iot-on-your-iot-hub"></a>Aktivieren von Defender für IoT für Ihre IoT Hub-Instanz

So aktivieren Sie die Sicherheit für Ihre IoT Hub-Instanz:

1. Öffnen Sie im Azure-Portal Ihre Instanz von **IoT Hub**.
1. Klicken Sie im Menü **Sicherheit** auf **Secure your IoT solution** (IoT-Lösung schützen).

Glückwunsch! Sie haben Defender für IoT in Ihrer IoT Hub-Instanz aktiviert.

### <a name="geolocation-and-ip-address-handling"></a>Geolocation und Verarbeitung von IP-Adressen

Zum Schutz Ihrer IoT-Lösung werden standardmäßig IP-Adressen von eingehenden und ausgehenden Verbindungen für Ihre IoT-Geräte, für IoT Edge und für IoT-Hubs erfasst und gespeichert. Diese Informationen werden für die Erkennung ungewöhnlicher Verbindungen von verdächtigen IP-Quellen benötigt. Beispiele wären etwa Verbindungsversuche von einer IP-Quelle eines bekannten Botnets oder von einer IP-Quelle außerhalb Ihres geografischen Standorts. Mit dem Dienst „Defender für IoT“ kann die Erfassung von IP-Adressdaten jederzeit flexibel aktiviert oder deaktiviert werden.

So aktivieren oder deaktivieren Sie die Erfassung von IP-Adressdaten:

1. Öffnen Sie Ihren IoT Hub, und wählen Sie im Menü **Sicherheit** die Option **Einstellungen** aus.
1. Wählen Sie den Bildschirm **Datensammlung** aus, und ändern Sie die Einstellungen für Geolocation und/oder Verarbeitung von IP-Adressen entsprechend Ihren Anforderungen.

### <a name="log-analytics-creation"></a>Log Analytics-Erstellung

Wenn Defender für IoT aktiviert ist, wird ein Azure Log Analytics-Standardarbeitsbereich erstellt, um Rohdaten für Sicherheitsereignisse, Warnungen und Empfehlungen für Ihre IoT-Geräte sowie für IoT Edge und IoT Hub zu speichern. Die ersten 5 GB an Daten, die monatlich pro Kunde im Azure Log Analytics-Dienst erfasst werden, sind kostenlos. Jedes GB an Daten, das in Ihrem Azure Log Analytics-Arbeitsbereich erfasst wird, wird für die ersten 31 Tage kostenfrei gespeichert. Weitere Informationen zu den Preisen für Log Analytics finden Sie [hier](https://azure.microsoft.com/pricing/details/monitor/).

So ändern Sie die Arbeitsbereichskonfiguration von Log Analytics:

1. Öffnen Sie Ihren IoT Hub, und wählen Sie im Menü **Sicherheit** die Option **Einstellungen** aus.
1. Wählen Sie den Bildschirm **Datensammlung** aus, und ändern Sie die Arbeitsbereichskonfiguration der Log Analytics-Einstellungen entsprechend Ihren Anforderungen.

### <a name="customize-your-iot-security-solution"></a>Anpassen Ihrer IoT-Sicherheitslösung

Durch Aktivieren von Defender für IoT werden standardmäßig alle IoT Hub-Instanzen in Ihrem Azure-Abonnement automatisch geschützt.

So aktivieren oder deaktivieren Sie den Dienst „Defender für IoT“ für eine bestimmte IoT Hub-Instanz:

1. Öffnen Sie Ihren IoT Hub, und wählen Sie im Menü **Sicherheit** die Option **Einstellungen** aus.
1. Wählen Sie den Bildschirm **Datensammlung** aus, und ändern Sie die Sicherheitseinstellungen eines beliebigen IoT-Hubs in Ihrem Azure-Abonnement entsprechend Ihren Anforderungen.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie Ihre Lösung konfigurieren:

> [!div class="nextstepaction"]
> [Konfigurieren Ihrer IoT-Lösung](quickstart-configure-your-solution.md)