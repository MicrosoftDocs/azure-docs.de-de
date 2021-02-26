---
title: Arbeiten mit Gerätebenachrichtigungen
description: Benachrichtigungen enthalten Informationen zu Netzwerkaktivitäten, die möglicherweise Ihre Aufmerksamkeit erfordern, sowie Empfehlungen zum Umgang mit diesen Aktivitäten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2083da329c41afbe95ff570194a1c3ad5c2661be
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523565"
---
# <a name="work-with-device-notifications"></a>Arbeiten mit Gerätebenachrichtigungen

Benachrichtigungen enthalten Informationen zu Netzwerkaktivitäten, die möglicherweise Ihre Aufmerksamkeit erfordern, sowie Empfehlungen zum Umgang mit diesen Aktivitäten. Beispielsweise erhalten Sie in den folgenden Fällen möglicherweise eine Benachrichtigung:

- Ein Gerät ist inaktiv. Wenn das betreffende Gerät nicht mehr zu Ihrem Netzwerk gehört, können Sie es entfernen. Wenn das Gerät inaktiv ist, z. B. weil es versehentlich vom Netzwerk getrennt wurde, verbinden Sie das Gerät erneut, und verwerfen Sie die Benachrichtigung.

- Auf einem Gerät, das aktuell nur über eine MAC-Adresse identifiziert wird, wurde eine IP-Adresse erkannt. Autorisieren Sie als Reaktion auf diese Benachrichtigung die IP-Adresse für das Gerät.

Das Reagieren auf Benachrichtigungen verbessert die Informationen in Gerätezuordnungen, dem Gerätebestand und Data-Mining-Abfragen und -Berichten. Darüber hinaus erhalten Sie dadurch Einblicke in legitime Netzwerkänderungen und potenzielle Netzwerkfehlkonfigurationen.

So greifen Sie auf Benachrichtigungen zu:

- Klicken Sie auf **Systemeinstellungen** und dann auf **Data Enhancement** (Datenverbesserung).

## <a name="notifications-vs-alerts"></a>Benachrichtigungen vs. Warnungen

Neben Benachrichtigungen zur Netzwerkaktivität erhalten Sie möglicherweise auch *Warnungen*. Benachrichtigungen enthalten Informationen zu Netzwerkänderungen oder nicht aufgelösten Geräteeigenschaften, die keine Bedrohung darstellen. Warnungen hingegen enthalten Informationen zu Netzwerkabweichungen und -änderungen, die möglicherweise eine Bedrohung für das Netzwerk darstellen.

So zeigen Sie Benachrichtigungen an:

1. Klicken Sie im linken Menübereich der Konsole auf **Gerätezuordnungen**.

2. Klicken Sie auf das **Benachrichtigungssymbol**. Die rote Zahl über dem Symbol steht für die Anzahl von Benachrichtigungen.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Benachrichtigungssymbol":::

   Im Fenster **Benachrichtigungen** werden alle vom Sensor erkannten Benachrichtigungen angezeigt.

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="Benachrichtigungen.":::

## <a name="filter-the-notifications-window"></a>Filtern des Fensters „Benachrichtigungen“

Verwenden Sie Suchfilter, um Benachrichtigungen anzuzeigen, die für Sie von Interesse sind.

| Filtern nach | BESCHREIBUNG |
|--|--|
| Nach Typ filtern | Zeigen Sie Benachrichtigungen für einen bestimmten Bereich an, für den Sie sich interessieren, z. B. nur Benachrichtigungen für inaktive Geräte. |
| Nach Datumsbereich filtern | Zeigen Sie Benachrichtigungen für einen bestimmten Zeitraum an, z. B. nur in der letzten Woche gesendete Benachrichtigungen. |
| Nach bestimmten Informationen suchen | Suchen Sie nach bestimmten Benachrichtigungen. |

## <a name="notification-events-and-responses"></a>Benachrichtigungsereignisse und Reaktion

In der folgenden Tabelle werden die Benachrichtigungsereignistypen beschrieben, die Sie möglicherweise erhalten, sowie Möglichkeiten für den Umgang damit. Sie können die Geräteinformationen mit einem empfohlenen Wert aktualisieren oder die Benachrichtigung verwerfen. Wenn Sie eine Benachrichtigung verwerfen, werden die Geräteinformationen nicht mit den empfohlenen Informationen aktualisiert. Wenn wieder Datenverkehr erkannt wird, wird die Benachrichtigung erneut gesendet.

| Benachrichtigungsereignistypen | BESCHREIBUNG | Antworten |
|--|--|--|
| Neue IP erkannt | Dem Gerät wurde eine neue IP-Adresse zugeordnet. Die folgenden fünf Szenarios können erkannt werden: <br /><br /> Einem Gerät wurde eine zusätzliche IP-Adresse zugeordnet. Dieses Gerät ist auch einer vorhandenen MAC-Adresse zugeordnet.<br /><br /> Für ein Gerät, das eine vorhandene MAC-Adresse verwendet, wurde eine neue IP-Adresse erkannt. Derzeit kommuniziert dieses Gerät nicht mit einer IP-Adresse.<br /> <br /> Für ein Gerät, das einen NetBIOS-Namen verwendet, wurde eine neue IP-Adresse erkannt. <br /><br /> Für ein Gerät, das einer MAC-Adresse zugeordnet ist, wurde eine IP-Adresse als Verwaltungsschnittstelle erkannt. <br /><br /> Für ein Gerät, das eine virtuelle IP-Adresse verwendet, wurde eine neue IP-Adresse erkannt. | **Set Additional IP to Device** (Zusätzliche IP-Adresse für Gerät festlegen) (Zusammenführen von Geräten) <br /> <br />**Replace Existing IP** (Vorhandene IP-Adresse ersetzen) <br /> <br /> **Verwerfen**<br /> Entfernen Sie die Benachrichtigung. |
| Inaktive Geräte | Auf einem Gerät wurde mehr als 60 Tage lang kein Datenverkehr erkannt. | **Löschen** <br /> Wenn dieses Gerät nicht zu Ihrem Netzwerk gehört, entfernen Sie es. <br /><br />**Verwerfen** <br /> Entfernen Sie die Benachrichtigung, wenn das Gerät Teil Ihres Netzwerks ist. Wenn das Gerät inaktiv ist (z. B. weil es versehentlich vom Netzwerk getrennt wurde), verwerfen Sie die Benachrichtigung, und verbinden Sie das Gerät erneut. |
| Neue OT-Geräte | Ein Subnetz enthält ein OT-Gerät, das nicht in einem ICS-Subnetz definiert ist. <br /><br /> Jedes Subnetz, das mindestens ein OT-Gerät enthält, kann als ICS-Subnetz definiert werden. Dies hilft bei der Unterscheidung zwischen OT- und IT-Geräten in der Zuordnung. | **Set as ICS Subnet** (Als ICS-Subnetz festlegen) <br /> <br /> **Verwerfen** <br />Entfernen Sie die Benachrichtigung, wenn das Gerät nicht zum Subnetz gehört. |
| Keine Subnetze konfiguriert | In Ihrem Netzwerk sind derzeit keine Subnetze konfiguriert. <br /><br /> Konfigurieren Sie Subnetze für eine bessere Darstellung in der Zuordnung und um zwischen OT- und IT-Geräten unterscheiden zu können. | **Öffnen Sie die Subnetzkonfiguration**, und konfigurieren Sie Subnetze. <br /><br />**Verwerfen** <br /> Entfernen Sie die Benachrichtigung. |
| Betriebssystemänderungen | Dem Gerät wurde mindestens ein neues Betriebssystem zugeordnet. | Wählen Sie den Namen des neuen Betriebssystems aus, das Sie dem Gerät zuordnen möchten.<br /><br /> **Verwerfen** <br /> Entfernen Sie die Benachrichtigung. |
| Neue Subnetze | Es wurden neue Subnetze entdeckt. | **Learn**<br />Fügen Sie das Subnetz automatisch hinzu.<br />**Open Subnet Configuration** (Subnetzkonfiguration öffnen)<br />Ergänzen Sie alle fehlenden Subnetzinformationen.<br />**Verwerfen**<br />Entfernen Sie die Benachrichtigung. |
| Gerätetypänderungen | Dem Gerät wurde ein neuer Gerätetyp zugeordnet. | **Set as {…}** (Als {…} festlegen)<br />Ordnen Sie dem Gerät den neuen Typ zu.<br />**Verwerfen**<br />Entfernen Sie die Benachrichtigung. |

## <a name="respond-to-many-notifications-simultaneously"></a>Reagieren auf mehrere Benachrichtigungen gleichzeitig

Möglicherweise müssen Sie sich um mehrere Benachrichtigungen gleichzeitig kümmern. Beispiel:

- Wenn die IT-Abteilung ein Betriebssystemupgrade für eine große Gruppe von Netzwerkservern durchgeführt hat, können Sie den Sensor anweisen, die neuen Serverversionen für alle aktualisierten Server zu ermitteln. 

- Wenn eine Gruppe von Geräten einer bestimmten Familie eingestellt wurde und nicht mehr aktiv ist, können Sie den Sensor anweisen, diese Geräte aus der Konsole zu entfernen.

Sie können den Sensor anweisen, neu erkannte Informationen auf mehrere Geräte anzuwenden oder zu ignorieren.   

So zeigen Sie Benachrichtigungen an und reagieren darauf:

1. Verwenden Sie die Option zum **Filtern nach Typ oder Datumsbereich** oder die Option **Alle auswählen**. Heben Sie die Auswahl von Benachrichtigungen nach Bedarf auf.

2. Weisen Sie den Sensor an, neu erkannte Informationen auf ausgewählte Geräte anzuwenden, indem Sie auf **ÜBERNAHME** klicken. Alternativ können Sie den Sensor anweisen, neu erkannte Informationen zu ignorieren, indem Sie auf **VERWERFEN** klicken. Die Anzahl von Benachrichtigungen, die Sie gleichzeitig übernehmen und verwerfen können, sowie die Anzahl der Benachrichtigungen, um die Sie sich separat kümmern müssen, wird jeweils angezeigt.

Die Ereignisse **New IPs** (Neue IP-Adressen) und **No Subnets configured** (Keine Subnetze konfiguriert) können nicht gleichzeitig verarbeitet werden. Sie müssen manuell bestätigt werden.

## <a name="improve-device-os-classification-data-enhancement"></a>Verbessern der Gerätebetriebssystem-Klassifizierung: Datenverbesserung 

Der Sensor ermittelt fortlaufend automatisch neue OT-Geräte. Außerdem erkennt er automatisch Änderungen an zuvor ermittelten Geräten, einschließlich des Betriebssystemtyps.

Unter bestimmten Umständen werden möglicherweise Konflikte bei ermittelten Betriebssystemen erkannt. Der Grund hierfür kann sein, dass Sie über eine Betriebssystemversion für Desktop- oder Serversysteme verfügen. Wenn ein solcher Konflikt erkannt wird, erhalten Sie eine Benachrichtigung mit optionalen Betriebssystemklassifizierungen.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Enhance Data (Daten verbessern)":::

Sehen Sie sich die Empfehlungen an, um die Betriebssystemklassifizierung anzureichern. Diese Informationen werden unter anderem im Gerätebestand und in Data-Mining-Berichten angezeigt. Mit ihnen kann auch die Genauigkeit von Warnungen, Bedrohungen und Risikoanalysen verbessert werden.

Wenn Sie eine Empfehlung akzeptieren, werden die Informationen zum Betriebssystemtyp im Sensor aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

[Anzeigen von Warnungen](how-to-view-alerts.md)
