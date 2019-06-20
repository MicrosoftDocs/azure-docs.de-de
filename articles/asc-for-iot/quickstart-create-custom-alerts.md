---
title: Erstellen benutzerdefinierter Benachrichtigungen für Azure Security Center für IoT (Vorschauversion) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie benutzerdefinierte Benachrichtigungen für Azure Security Center für IoT erstellen und zuweisen.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 3b4c5e4700b0ef718a6b079ecc6ab3ad80f4eab6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786180"
---
# <a name="quickstart-create-custom-alerts"></a>Schnellstart: Erstellen benutzerdefinierter Benachrichtigungen

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der Public Preview-Phase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mithilfe benutzerdefinierter Sicherheitsgruppen und Benachrichtigungen können Sie End-to-End-Sicherheitsinformationen und kategorische Geräteinformationen optimal nutzen, um die Sicherheit Ihrer gesamten IoT-Lösung zu verbessern. 

## <a name="why-use-custom-alerts"></a>Vorteile benutzerdefinierter Benachrichtigungen 

Sie kennen Ihre IoT-Geräte am besten.

Kunden, die umfassend mit dem erwarteten Verhalten ihrer Geräte vertraut sind, können mit Azure Security Center (ASC) für IoT auf der Grundlage dieses Wissens eine Verhaltensrichtlinie für Geräte erstellen, sodass bei jeder Abweichung vom erwarteten Normalverhalten eine Benachrichtigung ausgelöst wird.

## <a name="security-groups"></a>Sicherheitsgruppen

Mithilfe von Sicherheitsgruppen können Sie logische Gruppen von Geräten definieren und deren Sicherheitsstatus zentral verwalten.

Diese Gruppen können Geräte mit bestimmter Hardware, bereitgestellte Geräte eines bestimmten Standorts oder eine beliebige andere Gruppe darstellen, die Ihren spezifischen Anforderungen entspricht.

Sicherheitsgruppen werden durch eine Tag-Eigenschaft namens **SecurityGroup** für Sicherheitsmodulzwillinge definiert. Ändern Sie den Wert dieser Eigenschaft, um die Sicherheitsgruppe eines Geräts zu ändern.  

Standardmäßig besitzt jede IoT-Lösung in IoT Hub eine Sicherheitsgruppe namens **Standard**.

Verwenden Sie Sicherheitsgruppen, um Ihre Geräte in logischen Kategorien zu gruppieren. Die erstellten Gruppen können Sie dann den benutzerdefinierten Benachrichtigungen Ihrer Wahl zuweisen, um eine möglichst effektive End-to-End-Lösung zu erhalten. 

## <a name="customize-an-alert"></a>Anpassen einer Benachrichtigung

1. Öffnen Sie Ihre IoT Hub-Instanz. 
2. Klicken Sie im Abschnitt **Sicherheit** auf **Benutzerdefinierte Benachrichtigungen**. 
3. Wählen Sie eine Sicherheitsgruppe aus, auf die Sie die Anpassung anwenden möchten. 
4. Klicken Sie auf **Add a custom alert** (Benutzerdefinierte Benachrichtigung hinzufügen). 
5. Wählen Sie in der Dropdownliste ein Verhalten für die benutzerdefinierte Benachrichtigung aus. 
6. Bearbeiten Sie die erforderlichen Eigenschaften, und klicken Sie anschließend auf **OK**.
7. Klicken Sie auf **SPEICHERN**. Wenn Sie die neue Benachrichtigung nicht speichern, wird sie beim nächsten Schließen von IoT Hub gelöscht.

 
## <a name="alerts-available-for-customization"></a>Anpassbare Benachrichtigungen

Die folgende Tabelle enthält eine Zusammenfassung der anpassbaren Benachrichtigungen:

| Severity | NAME                                                                                                    | Data source | BESCHREIBUNG                                                                                                                                     |
|----------|---------------------------------------------------------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl von Cloud-zu-Gerät-Nachrichten im AMQP-Protokoll liegt nicht innerhalb des zulässigen Bereichs.          | IoT Hub     | Die Menge der Cloud-zu-Gerät-Nachrichten (AMQP-Protokoll) in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                  |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl abgelehnter Cloud-zu-Gerät-Nachrichten im AMQP-Protokoll liegt nicht innerhalb des zulässigen Bereichs. | IoT Hub     | Die Menge der Cloud-zu-Gerät-Nachrichten (AMQP-Protokoll), die in einem Zeitfenster durch das Gerät abgelehnt wurden, liegt nicht innerhalb des konfigurierten zulässigen Bereichs. |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl von Gerät-zu-Cloud-Nachrichten im AMQP-Protokoll liegt nicht innerhalb des zulässigen Bereichs.          | IoT Hub     | Die Menge der Gerät-zu-Cloud-Nachrichten (AMQP-Protokoll) in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                  |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl direkter Methodenaufrufe liegt nicht innerhalb des zulässigen Bereichs.                              | IoT Hub     | Die Menge direkter Methodenaufrufe in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                                     |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl von Dateiuploads liegt nicht innerhalb des zulässigen Bereichs.                                       | IoT Hub     | Die Menge der Dateiuploads in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                                              |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl von Cloud-zu-Gerät-Nachrichten im HTTP-Protokoll liegt nicht innerhalb des zulässigen Bereichs.          | IoT Hub     | Die Menge der Cloud-zu-Gerät-Nachrichten (HTTP-Protokoll) in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                  |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl abgelehnter Cloud-zu-Gerät-Nachrichten im HTTP-Protokoll liegt nicht innerhalb des zulässigen Bereichs. | IoT Hub     | Die Menge der Cloud-zu-Gerät-Nachrichten (HTTP-Protokoll), die in einem Zeitfenster durch das Gerät abgelehnt wurden, liegt nicht innerhalb des konfigurierten zulässigen Bereichs. |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl von Gerät-zu-Cloud-Nachrichten im HTTP-Protokoll liegt nicht innerhalb des zulässigen Bereichs.          | IoT Hub     | Die Menge der Gerät-zu-Cloud-Nachrichten (HTTP-Protokoll) in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                  |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl von Cloud-zu-Gerät-Nachrichten im MQTT-Protokoll liegt nicht innerhalb des zulässigen Bereichs.          | IoT Hub     | Die Menge der Cloud-zu-Gerät-Nachrichten (MQTT-Protokoll) in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                  |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl abgelehnter Cloud-zu-Gerät-Nachrichten im MQTT-Protokoll liegt nicht innerhalb des zulässigen Bereichs. | IoT Hub     | Die Menge der Cloud-zu-Gerät-Nachrichten (MQTT-Protokoll), die in einem Zeitfenster durch das Gerät abgelehnt wurden, liegt nicht innerhalb des konfigurierten zulässigen Bereichs. |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl von Gerät-zu-Cloud-Nachrichten im MQTT-Protokoll liegt nicht innerhalb des zulässigen Bereichs.          | IoT Hub     | Die Menge der Gerät-zu-Cloud-Nachrichten (MQTT-Protokoll) in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                  |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl von Befehlswarteschlangenbereinigungen liegt nicht innerhalb des zulässigen Bereichs.                               | IoT Hub     | Die Menge der Befehlswarteschlangenbereinigungen in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                                      |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl von Zwillingsaktualisierungen liegt nicht innerhalb des zulässigen Bereichs.                                       | IoT Hub     | Die Menge der Zwillingsaktualisierungen in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                                              |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl nicht autorisierter Vorgänge liegt nicht innerhalb des zulässigen Bereichs.                            | IoT Hub     | Die Menge nicht autorisierter Vorgänge in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                                   |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl aktiver Verbindungen liegt nicht innerhalb des zulässigen Bereichs.                                        | Agent       | Die Menge der aktiven Verbindungen in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                                        |
| Niedrig      | Benutzerdefinierte Warnung: Es wurde eine Verbindung mit einer unzulässigen IP-Adresse erstellt.                              | Agent       | Es wurde eine Verbindung mit einer unzulässigen IP-Adresse erstellt.                                                                                  |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Die Anzahl nicht erfolgreicher lokaler Anmeldungen liegt nicht innerhalb des zulässigen Bereichs.                                | Agent       | Die Menge der nicht erfolgreichen lokalen Anmeldungen in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs.                                                       |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Unzulässige Benutzeranmeldung                                                      | Agent       | Bei dem Gerät hat sich ein unzulässiger lokaler Benutzer angemeldet.                                                                                        |
| Niedrig      | Benutzerdefinierte Benachrichtigung: Ausführung eines unzulässigen Prozesses                                               | Agent       | Auf dem Gerät wurde ein unzulässiger Prozess ausgeführt. |          |

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie einen Sicherheits-Agent bereitstellen:

> [!div class="nextstepaction"]
> [Select and deploy a security agent on your IoT device](how-to-deploy-agent.md) (Auswählen und Bereitstellen eines Sicherheits-Agents auf Ihrem IoT-Gerät)
