---
title: Azure Notification Hubs und die Google FCM-Migration (Firebase Cloud Messaging)
description: Hier erfahren Sie, wie Azure Notification Hubs die Migration von Google GCM zu FCM behandelt.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458296"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Azure Notification Hubs und die Google FCM-Migration (Firebase Cloud Messaging)

## <a name="current-state"></a>Aktueller Status

Als Google die Migration von Google Cloud Messaging (GCM) zu Firebase Cloud Messaging (FCM) ankündigte, mussten Pushdienste wie der unsere angepasst werden, um die Änderung zu berücksichtigen, wie Benachrichtigungen an Android-Geräte gesendet werden.

Wir haben unser Dienst-Back-End aktualisiert und dann bei Bedarf Updates für unsere API und SDKs veröffentlicht. Mit unserer Implementierung haben wir uns entschieden, die Kompatibilität mit bestehenden GCM-Benachrichtigungsschemata aufrechtzuerhalten, um die Auswirkungen auf den Kunden zu minimieren. Das bedeutet, dass wir derzeit Benachrichtigungen über FCM im FCM Legacymodus an Android-Geräte senden. Schließlich möchten wir die volle Unterstützung für FCM hinzufügen, einschließlich der neuen Features und des Nutzlastformats. Das ist eine längerfristige Änderung, und die aktuelle Migration konzentriert sich auf die Beibehaltung der Kompatibilität mit bestehenden Anwendungen und SDKs. Sie können entweder die GCM- oder FCM-SDKs in Ihrer App verwenden (zusammen mit unserem SDK) und wir stellen sicher, dass die Benachrichtigung ordnungsgemäß gesendet wird.

Einige Kunden erhielten kürzlich eine E-Mail von Google mit einer Warnung vor Apps, die einen GCM-Endpunkt für Benachrichtigungen verwenden. Dies war nur eine Warnung und es gibt keine Fehler – die Android-Benachrichtigungen Ihrer App werden immer noch zur Verarbeitung an Google gesendet und Google verarbeitet sie auch weiterhin. Einige Kunden, die den GCM-Endpunkt in ihrer Dienstkonfiguration explizit angegeben haben, verwendeten weiterhin den veralteten Endpunkt. Wir hatten diese Lücke bereits erkannt und arbeiteten an der Behebung des Problems, als Google die E-Mail versandte.

Wir haben diesen veralteten Endpunkt ersetzt und die Lösung wird bereitgestellt.

## <a name="going-forward"></a>Zukünftige Entwicklung

In den häufig gestellten Fragen zu FCM von Google steht, dass Sie keine weiteren Schritte unternehmen müssen. In den [FCM FAQ](https://developers.google.com/cloud-messaging/faq) äußerte sich Google wie folgt: „Client-SDKs und GCM-Token werden auf unbestimmte Zeit weiter funktionieren. Sie können jedoch nicht auf die neueste Version von Google Play Services in Ihrer Android-App zugreifen, sofern Sie nicht zu FCM migrieren.“

Wenn Ihre App die GCM-Bibliothek verwendet, folgen Sie den Anweisungen von Google, um ein Upgrade auf die FCM-Bibliothek in Ihrer App durchzuführen. Unser SDK ist mit beiden kompatibel, sodass Sie in Ihrer App auf unserer Seite keine Aktualisierung vornehmen müssen (sofern Sie mit unserer SDK-Version auf dem neuesten Stand sind).

## <a name="questions-and-answers"></a>Fragen und Antworten

Hier sind einige Antworten auf häufig gestellte Fragen, die wir von Kunden erhalten haben:

**F:** Wie muss ich vorgehen, um die Kompatibilität bis zum Stichtag zu erreichen (Googles aktueller Stichtag ist der 29. Mai. Dieses Datum kann sich noch ändern)?

**A:** Nichts. Wir werden die Kompatibilität mit dem bestehenden GCM-Benachrichtigungsschema beibehalten. Ihr GCM-Schlüssel funktioniert weiterhin wie gewohnt, ebenso wie alle von Ihrer Anwendung verwendeten GCM-SDKs und -Bibliotheken.

Wenn Sie sich entscheiden, ein Upgrade auf die FCM-SDKs und -Bibliotheken durchzuführen, um die Vorteile neuer Features zu nutzen, funktioniert Ihr GCM-Schlüssel weiterhin. Sie können bei Bedarf zur Verwendung eines FCM-Schlüssels wechseln, aber stellen Sie sicher, dass Sie beim Erstellen des neuen Firebase-Projekts auch Firebase zu Ihrem bestehenden GCM-Projekt hinzufügen. Dies stellt die Abwärtskompatibilität mit Ihren Kunden sicher, die ältere Versionen der App ausführen, die noch GCM-SDKs und -Bibliotheken verwenden.

Wenn Sie ein neues FCM-Projekt erstellen und nicht an das bestehende GCM-Projekt anhängen, verlieren Sie die Möglichkeit, Benachrichtigungen per Push an Ihre aktuellen App-Installationen zu senden, sobald Sie Notification Hubs mit dem neuen FCM-Geheimnis aktualisieren, da der neue FCM-Schlüssel keine Verknüpfung mit dem alten GCM-Projekt aufweist.

**F:** Warum erhalte ich diese E-Mail über die Verwendung alter GCM-Endpunkte? Was muss ich tun?

**A:** Nichts. Wir haben auf die neuen Endpunkte migriert und werden in Kürze fertig sein, sodass keine Änderungen erforderlich sind. Es liegt kein Fehler vor. Der eine von uns übersehene Endpunkt hat einfach dazu geführt, dass Warnmeldungen von Google gesendet wurden.

**F:** Wie kann ich zu den neuen FCM-SDKs und -Bibliotheken übergehen, ohne bestehende Benutzer zu beeinträchtigen?

A: Ein Upgrade ist jederzeit möglich. Google hat noch nicht angekündigt, dass bestehende GCM-SDKs und -Bibliotheken eingestellt werden. Um sicherzustellen, dass für Ihre bestehenden Benutzer bei Pushbenachrichtigungen keine Probleme auftreten, vergewissern Sie sich bei der Erstellung des neuen Firebase-Projekts, das Sie es dem bestehenden GCM-Projekt zuordnen. Dadurch wird sichergestellt, dass neue Firebase-Geheimnisse für Benutzer, die ältere Versionen Ihrer App mit GCM-SDKs und -Bibliotheken verwenden, sowie für neue Benutzer Ihrer App mit FCM-SDKs und -Bibliotheken funktionieren.

**F:** Wann kann ich neue FCM-Features und -Schemas für meine Benachrichtigungen verwenden?

**A:** Sobald wir ein Update für unsere API und SDKs veröffentlicht haben, bleiben Sie dran – wir erwarten, dass wir in den kommenden Monaten eine entsprechende Lösung bereitstellen können.
