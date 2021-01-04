---
title: 'Problembehandlung: Azure Functions-Runtime ist nicht erreichbar'
description: Erfahren Sie, wie Sie Problembehandlung für ein ungültiges Speicherkonto durchführen.
ms.topic: article
ms.date: 09/05/2018
ms.openlocfilehash: 0b6778a08bf04367f2a0ef10f7cd4fe29a52dd61
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579010"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Problembehandlung: „Azure Functions-Runtime ist nicht erreichbar“

Dieser Artikel enthält Informationen zur Problembehandlung für die folgende Fehlerzeichenfolge, die im Azure-Portal angezeigt wird:

> „Fehler: Die Azure Functions-Runtime ist nicht erreichbar. Klicken Sie hier, um Informationen zur Speicherkonfiguration zu erhalten.“

Dieses Problem tritt auf, wenn die Azure Functions-Runtime nicht gestartet werden kann. Der häufigste Grund für das Problem ist, dass für die Funktions-App kein Zugriff auf das Speicherkonto mehr besteht. Weitere Informationen finden Sie unter [Anforderungen an das Speicherkonto](./functions-create-function-app-portal.md#storage-account-requirements).

Der Rest dieses Artikels hilft Ihnen bei der Problembehandlung der folgenden Ursachen dieses Fehlers, einschließlich der Vorgehensweise zum Identifizieren und Auflösen jedes Falls.

## <a name="storage-account-was-deleted"></a>Speicherkonto wurde gelöscht

Jede Funktions-App benötigt für den Betrieb ein Speicherkonto. Wenn dieses Konto gelöscht wird, funktioniert Ihre Funktion nicht.

Schlagen Sie zunächst den Namen Ihres Speicherkontos in den Anwendungseinstellungen nach. Der Name Ihres Speicherkontos (eingeschlossen in eine Verbindungszeichenfolge) ist entweder in `AzureWebJobsStorage` oder in `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` enthalten. Weitere Informationen finden Sie unter [Referenz zu App-Einstellungen für Azure Functions](./functions-app-settings.md#azurewebjobsstorage).

Suchen Sie im Azure-Portal nach Ihrem Speicherkonto, um zu ermitteln, ob es noch vorhanden ist. Wenn es gelöscht wurde, müssen Sie das Speicherkonto neu erstellen und Ihre Verbindungszeichenfolgen für den Speicher ersetzen. Ihr Funktionscode ist verloren gegangen, und Sie müssen ihn erneut bereitstellen.

## <a name="storage-account-application-settings-were-deleted"></a>Anwendungseinstellungen des Speicherkontos wurden gelöscht

Wenn Sie im vorherigen Schritt keine Verbindungszeichenfolge für das Speicherkonto finden konnten, wurde sie wahrscheinlich gelöscht oder überschrieben. Das Löschen von Anwendungseinstellungen wird normalerweise durchgeführt, wenn Sie Bereitstellungsslots oder Azure Resource Manager-Skripts zum Festlegen von Anwendungseinstellungen verwenden.

### <a name="required-application-settings"></a>Erforderliche Anwendungseinstellungen

* Erforderlich:
    * [`AzureWebJobsStorage`](./functions-app-settings.md#azurewebjobsstorage)
* Erforderlich für Funktionen des Verbrauchs- und Premium-Tarifs:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](./functions-app-settings.md)
    * [`WEBSITE_CONTENTSHARE`](./functions-app-settings.md)

Weitere Informationen finden Sie unter [Referenz zu App-Einstellungen für Azure Functions](./functions-app-settings.md).

### <a name="guidance"></a>Anleitungen

* Aktivieren Sie für keine dieser Einstellungen „Sloteinstellung“. Wenn Sie die Bereitstellungsslots tauschen, wird die Funktions-App beschädigt.
* Ändern Sie diese Einstellungen im Rahmen von automatisierten Bereitstellungen nicht.
* Diese Einstellungen müssen zur Erstellungszeit angegeben werden und gültig sein. Eine automatisierte Bereitstellung, die diese Einstellungen nicht enthält, führt zu einer Funktions-App, die nicht läuft, selbst wenn die Einstellungen später hinzugefügt werden.

## <a name="storage-account-credentials-are-invalid"></a>Anmeldeinformationen des Speicherkontos sind ungültig

Die oben erwähnten Verbindungszeichenfolgen für Speicherkonten müssen aktualisiert werden, wenn Sie Speicherschlüssel neu erstellen. Weitere Informationen zur Verwaltung von Speicherschlüsseln finden Sie unter [Erstellen eines Azure-Speicherkontos](../storage/common/storage-account-create.md).

## <a name="storage-account-is-inaccessible"></a>Kein Zugriff auf das Speicherkonto

Ihre Funktions-App muss auf das Speicherkonto zugreifen können. Häufige Probleme, die den Zugriff einer Funktions-App auf ein Speicherkonto blockieren, sind:

* Die Funktions-App wurde in Ihrer App Service-Umgebung ohne die richtigen Netzwerkregeln bereitgestellt, die Datenverkehr zum und vom Speicherkonto zulassen.

* Die Firewall des Speicherkontos ist aktiviert und nicht für das Zulassen von Datenverkehr von und zu Functions konfiguriert. Weitere Informationen finden Sie unter [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>Tägliches Ausführungskontingent erreicht

Wenn Sie ein tägliches Ausführungskontingent konfiguriert haben, wird Ihre Funktions-App vorübergehend deaktiviert, was dazu führt, dass viele der Portalsteuerelemente nicht mehr zur Verfügung stehen. 

Um das Kontingent im [Azure-Portal](https://portal.azure.com) zu überprüfen, wählen Sie in Ihrer Funktions-App die Option **Plattformfeatures** > **Funktions-App-Einstellungen** aus. Wenn Sie das von Ihnen festgelegte **tägliche Nutzungskontingent** überschreiten, wird die folgende Meldung angezeigt:

  > „Für die Funktions-App wurde das Kontingent für die tägliche Nutzung erreicht, und die App wurde bis zum nächsten 24-Stunden-Zeitraum beendet.“

Um dieses Problem zu beheben, entfernen oder erhöhen Sie das tägliche Kontingent, und starten Sie Ihre App anschließend neu. Andernfalls wird die Ausführung Ihrer App bis zum nächsten Tag blockiert.

## <a name="app-is-behind-a-firewall"></a>App ist hinter einer Firewall

Es kann einen der folgenden Gründe haben, warum Ihre Functions-Runtime ggf. nicht erreichbar ist:

* Ihre Funktions-App wird in einer [App Service-Umgebung mit internem Lastenausgleich](../app-service/environment/create-ilb-ase.md) gehostet und ist für das Blockieren von eingehendem Datenverkehr aus dem Internet konfiguriert.

* Ihre Funktions-App verfügt über [IP-Einschränkungen für eingehenden Datenverkehr](functions-networking-options.md#inbound-access-restrictions), für die der Internetzugriff blockiert ist. 

Das Azure-Portal sendet Aufrufe direkt an die ausgeführte App, um die Liste mit den Funktionen abzurufen, und führt HTTP-Aufrufe des Kudu-Endpunkts durch. Die Einstellungen auf Plattformebene auf der Registerkarte **Plattformfeatures** sind weiterhin verfügbar.

Überprüfen Sie Ihre Konfiguration der App Service-Umgebung wie folgt:
1. Navigieren Sie zur Netzwerksicherheitsgruppe (NSG) des Subnetzes, in dem sich die App Service-Umgebung befindet.
1. Überprüfen Sie die Eingangsregeln, um Datenverkehr über die öffentliche IP-Adresse des Computers zuzulassen, mit dem Sie auf die Anwendung zugreifen. 
   
Sie können das Portal auch auf einem Computer verwenden, der mit dem virtuellen Netzwerk verbunden ist, in dem Ihre App ausgeführt wird, oder auf einem virtuellen Computer, der in Ihrem virtuellen Netzwerk ausgeführt wird. 

Weitere Informationen zur Konfiguration der Regeln für eingehenden Datenverkehr finden Sie unter [Überlegungen zum Netzwerkbetrieb in einer App Service-Umgebung](../app-service/environment/network-info.md#network-security-groups) im Abschnitt „Netzwerksicherheitsgruppen“.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Überwachung Ihrer Funktions-Apps:

> [!div class="nextstepaction"]
> [Überwachen von Azure Functions](functions-monitoring.md)
