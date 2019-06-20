---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: ffd17f7a641e1481aa4c88f8b2eb12ec11fa7d8b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178364"
---
Fluentd ist ein Open-Source-Datensammler für die einheitliche Protokollierung. Die `Fluentd`-Einstellungen verwalten die Verbindung des Containers mit einem [Fluentd](https://www.fluentd.org)-Server. Der Container enthält einen Fluentd-Protokollanbieter, der es Ihrem Container ermöglicht, Protokolldaten (und optional auch Metrikdaten) auf einen Fluentd-Server zu schreiben.

In der folgenden Tabelle werden die Konfigurationseinstellungen beschrieben, die unter dem Abschnitt `Fluentd` unterstützt werden.

| NAME | Datentyp | BESCHREIBUNG |
|------|-----------|-------------|
| `Host` | string | Die IP-Adresse oder der DNS-Hostname des Fluentd-Servers. |
| `Port` | Integer | Der Port des Fluentd-Servers.<br/> Standardwert: 24224 |
| `HeartbeatMs` | Integer | Das Heartbeatintervall in Millisekunden. Wurde bis zum Ablauf dieses Intervalls kein Ereignisdatenverkehr gesendet, wird ein Heartbeat an den Fluentd-Server gesendet. Standardwert: 60.000 Millisekunden (eine Minute) |
| `SendBufferSize` | Integer | Der für Sendevorgänge zugeordnete Netzwerkpufferspeicher (in Byte). Standardwert: 32.768 Byte (32 KB) |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Das Timeout (in Millisekunden) für die Herstellung einer SSL/TLS-Verbindung mit dem Fluentd-Server. Der Standardwert beträgt 10.000 Millisekunden (zehn Sekunden).<br/> Wenn `UseTLS` auf FALSE festgelegt ist, wird dieser Wert ignoriert. |
| `UseTLS` | Boolean | Gibt an, ob der Container für die Kommunikation mit dem Fluentd-Server SSL/TLS verwenden soll. Der Standardwert ist „false“. |