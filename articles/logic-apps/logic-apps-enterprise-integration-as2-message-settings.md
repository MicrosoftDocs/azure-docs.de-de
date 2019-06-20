---
title: AS2-Nachrichteneinstellungen – Azure Logic Apps
description: Referenzhandbuch für AS2-Sende- und -Empfangseinstellungen in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "63767653"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referenz für AS2-Nachrichteneinstellungen in Azure Logic Apps mit Enterprise Integration Pack

In dieser Referenz werden die Eigenschaften beschrieben, die Sie zum Angeben festlegen können, wie eine AS2-Vereinbarung die Nachrichten behandelt, die zwischen Parteien gesendet und empfangen werden. Richten Sie die Eigenschaften auf der Grundlage Ihrer Vereinbarung mit dem Partner ein, der Nachrichten mit Ihnen austauscht.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2-Empfangseinstellungen

![Wählen Sie „Empfangseinstellungen“ aus.](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Eigenschaft | Erforderlich | BESCHREIBUNG |
|----------|----------|-------------|
| **Nachrichteneigenschaften überschreiben** | Nein | Setzt die Eigenschaften bei eingehenden Nachrichten mit Ihren Eigenschaftseinstellungen außer Kraft. |
| **Nachricht muss signiert sein** | Nein | Gibt an, ob alle eingehenden Nachrichten digital signiert werden müssen. Wählen Sie zur Validierung der Nachrichtensignatur in der Liste **Zertifikat** ein vorhandenes öffentliches Gastpartnerzertifikat aus, wenn die Signatur erforderlich ist. Erfahren Sie mehr über das [Hinzufügen von Zertifikaten](../logic-apps/logic-apps-enterprise-integration-certificates.md), wenn Sie kein Zertifikat besitzen. |
| **Nachricht muss verschlüsselt sein** | Nein | Gibt an, ob alle eingehenden Nachrichten verschlüsselt werden müssen. Nicht verschlüsselte Nachrichten werden abgelehnt. Wählen Sie zur Entschlüsselung eingehender Nachrichten in der Liste **Zertifikat** ein vorhandenes privates Hostpartnerzertifikat aus, wenn die Verschlüsselung erforderlich ist. Erfahren Sie mehr über das [Hinzufügen von Zertifikaten](../logic-apps/logic-apps-enterprise-integration-certificates.md), wenn Sie kein Zertifikat besitzen. |
| **Nachricht muss komprimiert sein** | Nein | Gibt an, ob alle eingehenden Nachrichten komprimiert werden müssen. Nicht komprimierte Nachrichten werden abgelehnt. |
| **Doppelte Nachrichten-IDs unterbinden** | Nein | Gibt an, ob Nachrichten mit doppelten IDs zugelassen werden. Wenn Sie doppelte IDs unterbinden, wählen Sie die Anzahl der Tage zwischen den Überprüfungen aus. Sie können auch auswählen, ob Duplikate gesperrt werden sollen. |
| **MDN-Text** | Nein | Gibt die standardmäßige Benachrichtigung über den Nachrichtenstatus (MDN) an, die Sie an den Absender der Nachricht senden möchten. |
| **MDN senden** | Nein | Gibt an, ob synchrone MDNs für empfangene Nachrichten gesendet werden sollen.  |
| **Signierte MDN senden** | Nein | Gibt an, ob signierte MDNs für empfangene Nachrichten gesendet werden sollen. Wenn eine Signatur erforderlich sein soll, wählen Sie aus der Liste **MIC-Algorithmus** den Algorithmus aus, der zum Signieren von Nachrichten verwendet werden soll. |
| **Asynchrone MDN senden** | Nein | Gibt an, ob MDNs asynchron gesendet werden sollen. Wenn Sie asynchrone MDNs auswählen, geben Sie im Feld **URL** die URL an, unter der die MDNs gesendet werden. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2-Sendeeinstellungen

![Wählen Sie „Sendeeinstellungen“ aus.](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Eigenschaft | Erforderlich | BESCHREIBUNG |
|----------|----------|-------------|
| **Nachrichtensignatur aktivieren** | Nein | Gibt an, ob alle ausgehenden Nachrichten digital signiert werden müssen. Wenn eine Signatur erforderlich sein soll, wählen Sie diese Werte aus: <p>- Wählen Sie aus der Liste **Signaturalgorithmus** den Algorithmus aus, der zum Signieren von Nachrichten verwendet werden soll. <br>- Wählen Sie zum Signieren von Nachrichten in der Liste **Zertifikat** ein vorhandenes privates Hostpartnerzertifikat aus. Erfahren Sie mehr über das [Hinzufügen von Zertifikaten](../logic-apps/logic-apps-enterprise-integration-certificates.md), wenn Sie kein Zertifikat besitzen. |
| **Nachrichtenverschlüsselung aktivieren** | Nein | Gibt an, ob alle ausgehenden Nachrichten verschlüsselt werden müssen. Wenn eine Verschlüsselung erforderlich sein soll, wählen Sie diese Werte aus: <p>- Wählen Sie in der Liste **Verschlüsselungsalgorithmus** den Algorithmus für das öffentliche Gastpartnerzertifikat aus, der für die Verschlüsselung von Nachrichten verwendet wird. <br>- Wählen Sie zur Entschlüsselung ausgehender Nachrichten in der Liste **Zertifikat** ein vorhandenes privates Gastpartnerzertifikat aus. Erfahren Sie mehr über das [Hinzufügen von Zertifikaten](../logic-apps/logic-apps-enterprise-integration-certificates.md), wenn Sie kein Zertifikat besitzen. |
| **Nachrichtenkomprimierung aktivieren** | Nein | Gibt an, ob alle ausgehenden Nachrichten komprimiert werden müssen. |
| **HTTP-Header erweitern** | Nein | Stellt den HTTP `content-type`-Header in eine einzelne Zeile. |
| **Übertragen des Dateinamens in MIME-Kopfzeile** | Nein | Gibt an, ob der Dateiname in die MIME-Kopfzeile einbezogen werden soll. |
| **MDN anfordern** | Nein | Gibt an, ob für alle ausgehenden Benachrichtigungen über den Nachrichtenstatus (MDNs) empfangen werden sollen. |
| **Signierte MDN anfordern** | Nein | Gibt an, ob für alle ausgehenden Nachrichten signierte MDNs empfangen werden sollen. Wenn eine Signatur erforderlich sein soll, wählen Sie aus der Liste **MIC-Algorithmus** den Algorithmus aus, der zum Signieren von Nachrichten verwendet werden soll. |
| **Asynchrone MDN anfordern** | Nein | Gibt an, ob MDNs asynchron empfangen werden sollen. Wenn Sie asynchrone MDNs auswählen, geben Sie im Feld **URL** die URL an, unter der die MDNs gesendet werden. |
| **NRR aktivieren** | Nein | Gibt an, ob die Nichtabstreitbarkeit des Empfangs (NRR) erforderlich ist. Dieses Kommunikationsattribut liefert den Nachweis, dass die Daten wie angegeben empfangen wurden. |
| **SHA2-Algorithmusformat** | Nein | Gibt das zum Signieren in den Headern von ausgehenden AS2-Nachrichten oder MDN zu verwendende MIC-Algorithmusformat an. |
||||

## <a name="next-steps"></a>Nächste Schritte

[Austauschen von AS2-Nachrichten](../logic-apps/logic-apps-enterprise-integration-as2.md)
