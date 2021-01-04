---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001166"
---
Fluentd ist ein Open-Source-Datensammler für die einheitliche Protokollierung. Die `Fluentd`-Einstellungen verwalten die Verbindung des Containers mit einem [Fluentd](https://www.fluentd.org)-Server. Der Container enthält einen Fluentd-Protokollanbieter, der es Ihrem Container ermöglicht, Protokolldaten (und optional auch Metrikdaten) auf einen Fluentd-Server zu schreiben.

In der folgenden Tabelle werden die Konfigurationseinstellungen beschrieben, die unter dem Abschnitt `Fluentd` unterstützt werden.

| Name | Datentyp | BESCHREIBUNG |
|------|-----------|-------------|
| `Host` | String | Die IP-Adresse oder der DNS-Hostname des Fluentd-Servers. |
| `Port` | Integer | Der Port des Fluentd-Servers.<br/> Standardwert: 24224 |
| `HeartbeatMs` | Integer | Das Heartbeatintervall in Millisekunden. Wurde bis zum Ablauf dieses Intervalls kein Ereignisdatenverkehr gesendet, wird ein Heartbeat an den Fluentd-Server gesendet. Standardwert: 60.000 Millisekunden (eine Minute) |
| `SendBufferSize` | Integer | Der für Sendevorgänge zugeordnete Netzwerkpufferspeicher (in Byte). Standardwert: 32.768 Byte (32 KB) |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Das Timeout (in Millisekunden) für die Herstellung einer SSL/TLS-Verbindung mit dem Fluentd-Server. Der Standardwert beträgt 10.000 Millisekunden (zehn Sekunden).<br/> Wenn `UseTLS` auf FALSE festgelegt ist, wird dieser Wert ignoriert. |
| `UseTLS` | Boolean | Gibt an, ob der Container für die Kommunikation mit dem Fluentd-Server SSL/TLS verwenden soll. Der Standardwert ist „FALSE“. |