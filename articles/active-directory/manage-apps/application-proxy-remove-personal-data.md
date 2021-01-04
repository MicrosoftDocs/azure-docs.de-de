---
title: Entfernen persönlicher Daten – Azure Active Directory-Anwendungsproxy
description: Entfernen persönlicher Daten von Connectors, die auf Geräten mit Azure Active Directory-Anwendungsproxy installiert sind
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99fb466debd5a2ee4bb659aea3494469a8bbe8e1
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658416"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Entfernen persönlicher Daten für Azure Active Directory-Anwendungsproxy

Der Azure Active Directory-Anwendungsproxy erfordert, dass Sie Connectors auf Ihren Geräten installieren. Das bedeutet, dass sich möglicherweise persönliche Daten auf Ihren Geräten befinden. Dieser Artikel erläutert, wie Sie persönliche Daten für einen besseren Datenschutz löschen.

## <a name="where-is-the-personal-data"></a>Wo befinden sich persönliche Daten?

Der Anwendungsproxy kann persönliche Daten in die folgenden Protokolltypen schreiben:

- Connector-Ereignisprotokolle
- Windows-Ereignisprotokolle

## <a name="remove-personal-data-from-windows-event-logs"></a>Entfernen persönlicher Daten aus Windows-Ereignisprotokollen

Informationen zum Konfigurieren der Datenaufbewahrung für die Windows-Ereignisprotokolle finden Sie unter [Settings for event logs](https://technet.microsoft.com/library/cc952132.aspx) (Einstellungen für Ereignisprotokolle). Weitere Informationen zu Windows-Ereignisprotokollen finden Sie unter [Verwenden von Windows-Ereignisprotokollen](/windows/win32/wes/using-windows-event-log) (in englischer Sprache).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Entfernen persönlicher Daten aus Connector-Ereignisprotokollen

So können Sie sicherstellen, dass die Protokolle des Anwendungsproxy keine persönlichen Daten enthalten

- Löschen oder Anzeigen von Daten bei Bedarf
- Deaktivieren der Protokollierung

Im Folgenden erfahren Sie, wie Sie persönliche Daten aus Connector-Ereignisprotokollen entfernen können. Sie müssen den Entfernungsvorgang für alle Geräte durchführen, auf denen der Connector installiert ist.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Anzeigen oder Exportieren spezifischer Daten

Suchen Sie nach ähnlichen Einträgen in jedem Connector-Ereignisprotokoll, um bestimmte Daten anzuzeigen oder zu exportieren. Die Protokolle befinden sich hier: `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`.

Da die Protokolle Textdateien sind, können Sie mit dem Befehl [findstr](/windows-server/administration/windows-commands/findstr) nach Texteinträgen mit Bezug zu einem Benutzer suchen.  

Suchen Sie in Protokolldateien nach „UserID“, um persönliche Daten zu finden.

Um persönliche Daten zu finden, die eine Anwendung mit eingeschränkter Kerberos-Delegierung protokolliert hat, suchen Sie nach diesen Komponenten des Benutzernamentyps:

- Lokaler Benutzerprinzipalname
- Benutzernamenteil des Benutzerprinzipalnamens
- Benutzernamenteil des lokalen Benutzerprinzipalnamens
- Lokaler Kontoname der Sicherheitskontenverwaltung (SAM)

### <a name="delete-specific-data"></a>Löschen spezifischer Daten

So löschen Sie spezifische Daten

1. Starten Sie den Microsoft Azure AD-Anwendungsproxyconnector neu, um eine neue Protokolldatei zu generieren. Mithilfe der neuen Protokolldatei können Sie die alten Protokolldateien löschen oder ändern. 
1. Gehen Sie wie unter [Anzeigen oder Exportieren spezifischer Daten](#view-or-export-specific-data) vor, um die Informationen zu finden, die Sie löschen möchten. Durchsuchen Sie alle Connectorprotokolle.
1. Löschen Sie die entsprechenden Protokolldateien oder löschen Sie nur die Felder, die persönliche Daten enthalten. Sie können auch alle alten Protokolldateien löschen, wenn diese nicht mehr benötigt werden.

### <a name="turn-off-connector-logs"></a>Deaktivieren von Connectorprotokollen

Sie können die Protokollgenerierung deaktivieren, um sicherzustellen, dass die Connectorprotokolle keine persönlichen Daten enthalten. Wenn keine Connectorprotokolle mehr generiert werden sollen, entfernen Sie die folgende markierte Zeile in `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`.

![Zeigt einen Codeausschnitt, in dem der zu entfernende Code markiert ist](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über den Anwendungsproxy finden Sie unter [Bereitstellen von sicherem Remotezugriff auf lokale Anwendungen](application-proxy.md).