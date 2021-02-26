---
title: Regionale Verfügbarkeit und Datenresidenz für Azure Communication Services
description: Hier finden Sie Informationen zur Datenresidenz und zu Datenschutzangelegenheiten im Zusammenhang mit Azure Communication Services.
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/03/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a047761669920d6460c3d6fb6d74b970effa7846
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572037"
---
# <a name="region-availability-and-data-residency"></a>Regionale Verfügbarkeit und Datenresidenz

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services unterstützt Kunden bei der Erfüllung ihrer Anforderungen in puncto Datenschutz und persönliche Daten. Entwickler, die Communication Services mit einer direkten Beziehung zu Benutzern der Anwendung verwenden, fungieren möglicherweise als Datencontroller. Da Azure Communication Services diese Daten in Ihrem Namen speichert, fungieren wir höchstwahrscheinlich als Datenverarbeiter. Hier erfahren Sie, wie Daten durch den Dienst gespeichert werden und wie Sie diese Daten identifizieren, exportieren und löschen können.

## <a name="data-residency"></a>Datenresidenz

Bei der Erstellung einer Communication Services-Ressource geben Sie kein Azure-Rechenzentrum, sondern eine **geografische Region** an. Alle ruhenden Daten, die von Communication Services gespeichert werden, werden in dieser geografischen Region gespeichert. Das Rechenzentrum wird dabei intern von Communication Services ausgewählt. Daten können jedoch andere geografische Regionen durchlaufen oder in anderen geografischen Regionen verarbeitet werden. Diese globalen Endpunkte sind erforderlich, um Endbenutzern unabhängig von ihrem Standort eine hohe Leistung mit geringer Wartezeit zu bieten.

## <a name="relating-humans-to-azure-communication-services-identities"></a>Verknüpfen von Benutzern mit Azure Communication Services-Identitäten

Ihre Anwendung verwaltet die Beziehung zwischen menschlichen Benutzern und Communication Services-Identitäten. Wenn Sie Daten für einen menschlichen Benutzer löschen möchten, müssen Sie Daten aller für den Benutzer korrelierten Communication Services-Identitäten löschen.

Es gibt zwei Kategorien von Communication Services-Daten:
- **API-Daten:** Diese Daten werden von Communication Services-APIs erstellt und verwaltet. Ein typisches Beispiel wären etwa Chatnachrichten, die über Chat-APIs verwaltet werden.
- **Azure Monitor-Protokolle:** Diese Daten werden vom Dienst erstellt und über die Azure Monitor-Datenplattform verwaltet. Diese Daten beinhalten Telemetriedaten und Metriken, die Ihnen dabei helfen, Ihre Communication Services-Nutzung besser zu verstehen. Dies wird nicht durch Communication Services-APIs verwaltet.

## <a name="api-data"></a>API-Daten

### <a name="identities"></a>Identities

Von Azure Communication Services wird ein Verzeichnis mit Identitäten gepflegt. Verwenden Sie zum Entfernen der Identitäten die API [DeleteIdentity](/rest/api/communication/communicationidentity/delete). Wenn Sie eine Identität löschen, werden alle zugeordneten Zugriffstoken widerrufen, und die zugehörigen Chatnachrichten werden gelöscht. Weitere Informationen zum Entfernen einer Identität finden Sie auf [dieser Seite](../quickstarts/access-tokens.md).

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

Durch die Verwendung des Azure-Portals oder der Azure Resource Manager-APIs mit Communication Services können personenbezogene Daten erstellt werden. Auf [dieser Seite](../../azure-resource-manager/management/resource-manager-personal-data.md) erfahren Sie, wie Sie personenbezogene Daten in Azure Resource Manager-Systemen verwalten.

### <a name="telephone-number-management"></a>Telefonnummernverwaltung

Von Azure Communication Services wird ein Verzeichnis mit Telefonnummern gepflegt, die einer Communication Services-Ressource zugeordnet sind. Verwenden Sie die folgenden APIs, um Telefonnummern abzurufen und zu löschen:
- `Release Phone Number`

### <a name="chat"></a>Chat

Chatthreads und -nachrichten werden aufbewahrt, bis sie explizit gelöscht werden. Ein vollständig im Leerlauf befindlicher Thread wird nach 30 Tagen automatisch gelöscht. Verwenden Sie [Chat-APIs](/rest/api/communication/chat/deletechatmessage/deletechatmessage), um Nachrichten abzurufen, aufzulisten, zu aktualisieren und zu löschen.

- `Get Thread`
- `Get Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>SMS

Gesendete und empfangene SMS-Nachrichten werden flüchtig durch den Dienst verarbeitet und nicht gespeichert. 

### <a name="pstn-voice-calling"></a>Sprachanrufe über das Festnetz

Audio- und Videokommunikation wird flüchtig durch den Dienst verarbeitet, und mit Ausnahme von Azure Monitor-Protokollen werden keine Daten in Ihrer Ressource gespeichert.

### <a name="internet-voice-and-video-calling"></a>Sprach- und Videoanrufe über das Internet

Audio- und Videokommunikation wird flüchtig durch den Dienst verarbeitet, und mit Ausnahme von Azure Monitor-Protokollen werden keine Daten in Ihrer Ressource gespeichert.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor und Log Analytics

Azure Communication Services wird zum besseren Verständnis der Betriebsintegrität und Nutzung des Diensts in Azure Monitor-Protokollierungsdaten eingebunden. Einige dieser Protokolle enthalten Communication Services-Identitäten und -Telefonnummern als Felddaten. Verwenden Sie zum Löschen potenziell personenbezogener Daten [diese Verfahren für Azure Monitor](../../azure-monitor/logs/personal-data-mgmt.md). Unter Umständen empfiehlt es sich auch, die [Standardaufbewahrungsdauer für Azure Monitor](../../azure-monitor/logs/manage-cost-storage.md) zu konfigurieren.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Anträge betroffener Personen für Azure im Rahmen der DSGVO und des CCPA](/microsoft-365/compliance/gdpr-dsr-azure?preserve-view=true&view=o365-worldwide)
- [Microsoft Trust Center](https://www.microsoft.com/trust-center/privacy/data-location)
- [Microsoft Azure: Wo befinden sich meine Kundendaten?](https://azuredatacentermap.azurewebsites.net/)