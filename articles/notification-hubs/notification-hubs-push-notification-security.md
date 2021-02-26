---
title: Sicherheitsmodell von Notification Hubs
description: Erfahren Sie mehr über das Sicherheitsmodell für Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 07600b1fe0cb7420989fbbfbe55c2f1a4197d2fc
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548249"
---
# <a name="notification-hubs-security"></a>Sicherheit von Notification Hubs

## <a name="overview"></a>Übersicht

Dieses Thema beschreibt das Sicherheitsmodell von Azure Notification Hubs.

## <a name="shared-access-signature-security"></a>SAS-Sicherheit (Shared Access Signature)

Notification Hubs implementieren ein Sicherheitsschema auf Entitätsebene, das SAS (*Shared Access Signature*) genannt wird. Jede Regel enthält einen Namen, einen Schlüsselwert (gemeinsames Geheimnis) und eine Reihe von Rechten, wie später im Abschnitt [Sicherheitsansprüche](#security-claims) erläutert. 

Beim Erstellen eines Hubs werden automatisch zwei Regeln erstellt: eine mit **Lauschberechtigung** (von der Clientanwendung verwendet) und eine mit **allen** Berechtigungen (vom Back-End verwendet):

- **DefaultListenSharedAccessSignature** erteilt nur die **Lauschberechtigung**.
- **DefaultFullSharedAccessSignature** erteilt die Berechtigungen **Lauschen**, **Verwalten** und **Senden**. Diese Richtlinie darf nur in Ihrem App-Back-End verwendet werden. Verwenden Sie sie nicht in Clientanwendungen. Verwenden Sie eine Richtlinie, die nur über den Zugriff zum **Lauschen** verfügt. Informationen zum Erstellen einer neuen benutzerdefinierten Zugriffsrichtlinie mit einem neuen SAS-Token finden Sie später in diesem Artikel unter [SAS-Token für Zugriffsrichtlinien](#sas-tokens-for-access-policies).

Wenn eine Registrierungsverwaltung von Client-Apps aus durchgeführt wird und die über Benachrichtigungen gesendeten Informationen nicht vertraulich sind (z. B. aktuelle Wetterdaten), besteht eine gebräuchliche Möglichkeit zum Zugreifen auf einen Notification Hub darin, dem Schlüsselwert der Regel nur Lauschzugriff für die Client-App zu erteilen und dem Schlüsselwert der Regel Vollzugriff auf das Back-End zu gewähren.

Apps sollten den Schlüsselwert nicht in Windows Store-Client-Apps einbetten. Stattdessen sollte die Client-App ihn beim Start über das App-Back-End abrufen.

Der Schlüssel mit **Lauschzugriff** ermöglicht einer Client-App, sich für jedes beliebige Tag zu registrieren. Wenn Ihre App Registrierungen auf bestimmte Tags für bestimmte Clients einschränken muss (wenn Tags z.B. für Benutzer-IDs stehen), muss das App-Back-End die Registrierungen ausführen. Weitere Informationen finden Sie unter [Registrierungsverwaltung](notification-hubs-push-notification-registration-management.md). Beachten Sie, dass die Clientanwendung auf diese Weise keinen direkten Zugriff auf Notification Hubs hat.

## <a name="security-claims"></a>Sicherheitsansprüche

Ähnlich wie bei anderen Entitäten sind Notification Hub-Vorgänge für drei Sicherheitsansprüche zulässig: **Lauschen**, **Senden** und **Verwalten**.

| Anspruch   | BESCHREIBUNG                                          | Zulässige Vorgänge |
| ------- | ---------------------------------------------------- | ------------------ |
| Lauschen  | Erstellen/Aktualisieren, Lesen und Löschen einzelner Registrierungen | Erstellen/Aktualisieren einer Registrierung<br><br>Lesen der Registrierung<br><br>Lesen aller Registrierungen für ein Handle<br><br>Registrierung löschen |
| Send    | Senden von Nachrichten an den Notification Hub                | Nachricht senden |
| Verwalten  | CRUDs für Notification Hubs (einschließlich des Aktualisierens von PNS-Anmeldeinformationen und Sicherheitsschlüsseln) und Lesen von Registrierungen basierend auf Tags |Erstellen/Aktualisieren/Lesen/Löschen von Hubs<br><br>Lesen von Registrierungen nach Tag |

Notification Hubs akzeptiert SAS-Token mit freigegebenen Schlüsseln, die direkt auf dem Hub konfiguriert wurden.

Es ist nicht möglich, eine Benachrichtigung an mehr als einen Namespace zu senden. Namespaces sind logische Container für Notification Hubs und nicht am Senden von Benachrichtigungen beteiligt.

Verwenden Sie die Zugriffsrichtlinien auf Namespaceebene (Anmeldeinformationen) für Vorgänge auf Namespaceebene. Beispiel: Auflistung von Hubs, Erstellen oder Löschen von Hubs usw. Nur die Zugriffsrichtlinien auf Hubebene gestatten es Ihnen, Benachrichtigungen zu senden.

### <a name="sas-tokens-for-access-policies"></a>SAS-Token für Zugriffsrichtlinien

Gehen Sie wie folgt vor, um einen neuen Sicherheitsanspruch zu erstellen oder vorhandene SAS-Schlüssel anzuzeigen:

1. Melden Sie sich beim Azure-Portal an.
2. Wählen Sie **Alle Ressourcen**.
3. Wählen Sie den Namen des Notification Hubs aus, für den Sie den Anspruch erstellen oder den SAS-Schlüssel anzeigen möchten.
4. Wählen Sie im linken Menü **Zugriffsrichtlinien** aus.
5. Wählen Sie **Neue Richtlinie** aus, um einen neuen Sicherheitsanspruch zu erstellen. Benennen Sie die Richtlinie, und wählen Sie die Berechtigungen aus, die Sie gewähren möchten. Klicken Sie anschließend auf **OK**.
6. Die vollständige Verbindungszeichenfolge (einschließlich des neuen SAS-Schlüssels) wird im Fenster „Zugriffsrichtlinien“ angezeigt. Sie können diese Zeichenfolge zur späteren Verwendung in die Zwischenablage kopieren.

Wenn Sie den SAS-Schlüssel aus einer bestimmten Richtlinie extrahieren möchten, wählen Sie die Schaltfläche **Kopieren** neben der Richtlinie aus, die den gewünschten SAS-Schlüssel enthält. Fügen Sie diesen Wert an einer temporären Position ein, und kopieren Sie dann den SAS-Schlüsselteil der Verbindungszeichenfolge. In diesem Beispiel wird ein Notification Hubs-Namespace namens **mytestnamespace1** und eine Richtlinie namens **policy2** verwendet. Der SAS-Schlüssel ist der Wert in der Nähe des Endes der Zeichenfolge, der durch **SharedAccessKey** angegeben wird:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Abrufen von SAS-Schlüsseln](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Notification Hubs](notification-hubs-push-notification-overview.md)
