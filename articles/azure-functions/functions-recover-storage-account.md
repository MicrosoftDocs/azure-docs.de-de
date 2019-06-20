---
title: Problembehandlung für „Die Azure Functions-Runtime ist nicht erreichbar“.
description: Erfahren Sie, wie Sie Problembehandlung für ein ungültiges Speicherkonto durchführen.
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: azure-functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 6057fa52cd2f1e9b9fd525723f96ab66983fb5d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61020297"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Problembehandlung für „Die Functions-Runtime ist nicht erreichbar“.


## <a name="error-text"></a>Fehlertext
Dieses Dokument ist für die Problembehandlung des folgenden Fehlers vorgesehen, wenn er im Functions-Portal angezeigt wird.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Zusammenfassung
Dieses Problem tritt auf, wenn die Azure Functions Runtime nicht gestartet werden kann. Die häufigste Ursache für das Auftreten dieses Fehlers besteht darin, dass die Funktions-App den Zugriff auf ihr Speicherkonto verliert. [Lesen Sie hier mehr über die Anforderungen für Speicherkonten](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Problembehandlung
Wir gehen exemplarisch die vier häufigsten Fehlerfälle durch und zeigen, wie sie im Einzelnen bestimmt und behoben werden.

1. Speicherkonto gelöscht
1. Anwendungseinstellungen des Speicherkontos gelöscht
1. Ungültige Anmeldeinformationen des Speicherkontos
1. Kein Zugriff auf das Speicherkonto
1. Tägliches Ausführungskontingent voll

## <a name="storage-account-deleted"></a>Speicherkonto gelöscht

Jede Funktions-App benötigt für den Betrieb ein Speicherkonto. Wenn dieses Konto gelöscht wird, funktioniert Ihre Funktions-App nicht.

### <a name="how-to-find-your-storage-account"></a>Finden Ihres Speicherkontos

Schlagen Sie zunächst den Namen Ihres Speicherkontos in den Anwendungseinstellungen nach. Entweder `AzureWebJobsStorage` oder `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` enthält den Namen Ihres Speicherkontos, in eine Verbindungszeichenfolge eingeschlossen. Weitere spezifische Informationen finden Sie in der [Referenz zu Anwendungseinstellungen hier](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Suchen Sie im Azure-Portal nach Ihrem Speicherkonto, um festzustellen, ob es noch vorhanden ist. Wenn es gelöscht wurde, müssen Sie ein neues Speicherkonto erstellen und Ihre Verbindungszeichenfolgen für den Speicher ersetzen. Ihr Funktionscode ist verloren gegangen, und Sie müssen ihn erneut bereitstellen.

## <a name="storage-account-application-settings-deleted"></a>Anwendungseinstellungen des Speicherkontos gelöscht

Wenn im vorhergegangenen Schritt keine Verbindungszeichenfolge für das Speicherkonto vorhanden war, ist sie wahrscheinlich gelöscht oder überschrieben worden. Das Löschen von App-Einstellungen erfolgt meistens bei der Verwendung von Bereitstellungsslots oder Azure Resource Manager-Skripts zum Festlegen von Anwendungseinstellungen.

### <a name="required-application-settings"></a>Erforderliche Anwendungseinstellungen

* Erforderlich
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Für Verbrauchsplanfunktionen erforderlich
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Lesen Sie hier mehr über diese Anwendungseinstellungen](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Anleitungen

* Aktivieren Sie für keine dieser Einstellungen „Sloteinstellung“. Wenn Sie die Bereitstellungsslots tauschen, wird die Funktion beschädigt.
* Ändern Sie diese Einstellungen im Rahmen von automatisierten Bereitstellungen nicht.
* Diese Einstellungen müssen zur Erstellungszeit angegeben werden und gültig sein. Eine automatisierte Bereitstellung, die diese Einstellungen nicht enthält, führt zu einer nicht funktionsfähigen App, selbst wenn die Einstellungen nach der Bereitstellung hinzugefügt werden.

## <a name="storage-account-credentials-invalid"></a>Ungültige Anmeldeinformationen des Speicherkontos

Die oben erwähnten Verbindungszeichenfolgen für Speicherkonten müssen aktualisiert werden, wenn Sie Speicherschlüssel neu erstellen. [Erfahren Sie hier mehr über die Verwaltung von Speicherschlüsseln](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Kein Zugriff auf das Speicherkonto

Ihre Funktions-App muss auf das Speicherkonto zugreifen können. Dies sind häufige Probleme, die den Zugriff einer Funktions-App auf ein Speicherkonto blockieren:

* Funktions-Apps wurden in App Service-Umgebungen ohne ordnungsgemäße Netzwerkregeln bereitgestellt, die Datenverkehr zum und vom Speicherkonto zulassen
* Die Firewall des Speicherkontos ist aktiviert und nicht für das Zulassen von Datenverkehr von und zu Funktions-Apps konfiguriert. [Lesen Sie hier mehr über die Firewallkonfiguration für Speicherkonten](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Tägliches Ausführungskontingent voll

Wenn Sie ein tägliches Ausführungskontingent konfiguriert haben, wird Ihre Funktions-App vorübergehend deaktiviert, und viele der Portalsteuerelemente stehen nicht mehr zur Verfügung. 

* Um dies zu überprüfen, öffnen Sie „Plattformfeatures > Funktions-App-Einstellungen“ im Portal. Wenn Sie ein Kontingent überschritten haben, wird die folgende Meldung angezeigt
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Entfernen Sie das Kontingent, und starten Sie Ihre App neu, um das Problem zu beheben.

## <a name="next-steps"></a>Nächste Schritte

Jetzt, da Ihre Funktions-App wieder verfügbar und betriebsbereit ist, werfen Sie einen Blick in unsere Schnellstarts und Entwicklerreferenzen, um den Betrieb schnell wieder aufzunehmen!

* [Erstellen Sie Ihre erste Funktion in Azure Functions](functions-create-first-azure-function.md)  
  Legen Sie direkt los, und erstellen Sie Ihre erste Funktion mithilfe der Azure Functions-Schnellstartanleitung. 
* [Entwicklerreferenz zu Azure Functions](functions-reference.md)  
  Enthält weitere technische Informationen zur Azure Functions-Laufzeit sowie eine Referenz für das Programmieren von Funktionen sowie für das Festlegen von Triggern und Bindungen.
* [Testen von Azure Functions](functions-test-a-function.md)  
  Beschreibt verschiedene Tools und Techniken zum Testen Ihrer Funktionen
* [How to scale Azure Functions (Skalieren von Azure Functions) (Skalieren von Azure Functions)](functions-scale.md)  
  Beschreibt die für Azure Functions verfügbaren Servicepläne (einschließlich des Hostingplans „Verbrauchstarif“) und enthält Informationen zur Wahl des geeigneten Plans. 
* [Was ist Azure App Service?](../app-service/overview.md)  
  Azure Functions nutzt Azure App Service für Kernfunktionen wie Bereitstellungen, Umgebungsvariablen und Diagnosen. 
