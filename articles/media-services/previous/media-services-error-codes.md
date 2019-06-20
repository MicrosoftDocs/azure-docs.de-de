---
title: Azure Media Services-Fehlercodes | Microsoft-Dokumentation
description: Dieses Thema bietet eine Übersicht über Azure Media Services-Fehlercodes.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f3c362730e7908e88b363659b7fa580b6f2cddf1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217166"
---
# <a name="azure-media-services-error-codes"></a>Azure Media Services-Fehlercodes
Wenn Sie Microsoft Azure Media Services verwenden, erhalten Sie möglicherweise HTTP-Fehlercodes vom Dienst, abhängig von Problemen wie dem Ablaufen von Authentifizierungstoken und Aktionen, die in Media Services nicht unterstützt werden. Im Folgenden werden die **HTTP-Fehlercodes** aufgelistet, die von Media Services zurückgegeben werden könnten, sowie mögliche Ursachen für diese Fehlercodes.  

## <a name="400-bad-request"></a>400 – Ungültige Anforderung
Die Anforderung enthält ungültige Informationen und wird aufgrund einer der folgenden Gründe abgelehnt:

* Es ist eine nicht unterstützte API-Version angegeben. Die aktuellste Version finden Sie unter [Übersicht über die Media Services-REST-API](media-services-rest-how-to-use.md).
* Die API-Version von Media Services ist nicht angegeben. Weitere Informationen zum Angeben der API-Version finden Sie unter [Media Services Operations REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) (Media Services-Vorgänge – REST-API-Referenz).
  
  > [!NOTE]
  > Wenn Sie .NET oder Java SDKs für die Verbindung mit Media Services verwenden, wird die API-Version für Sie angegeben, wenn Sie versuchen, eine Aktion in Media Services ausführen.
  > 
  > 
* Eine nicht definierte Eigenschaft wurde angegeben. Der Eigenschaftsname ist in der Fehlermeldung. Nur die Eigenschaften, die Elemente einer bestimmten Entität sind, können angegeben werden. Eine Liste von Entitäten und deren Eigenschaften finden Sie unter [Azure Media Services REST API Reference (Referenz zu Azure Media Services-REST-API)](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
* Ein Ungültiger Eigenschaftswert wurde angegeben. Der Eigenschaftsname ist in der Fehlermeldung. Unter dem vorherigen Link finden Sie gültige Eigenschaftstypen und deren Werte.
* Ein Eigenschaftswert fehlt und muss angegeben werden.
* Ein Teil der angegebenen URL enthält einen ungültigen Wert.
* Es wurde versucht, eine WriteOnce-Eigenschaft zu aktualisieren.
* Es wurde versucht, einen Auftrag zu erstellen, der über ein Eingabemedienobjekt mit einer primären AssetFile verfügt, die nicht angegeben wurde oder nicht ermittelt werden konnte.
* Es wurde versucht, einen SAS-Locator zu aktualisieren. SAS-Locator können nur erstellt oder gelöscht werden. Streaminglocators können aktualisiert werden. Weitere Informationen finden Sie unter [Locator](https://docs.microsoft.com/rest/api/media/operations/locator).
* Ein nicht unterstützter Vorgang oder eine nicht unterstützte Abfrage wurde übermittelt.

## <a name="401-unauthorized"></a>401 – Nicht autorisiert
Die Anforderung konnte aus einem der folgenden Gründen nicht authentifiziert werden (bevor sie autorisiert werden kann):

* Fehlender Authentifizierungsheader
* Ungültiger Authentifizierungsheaderwert
  * Das Token ist abgelaufen. 
  * Das Token enthält eine ungültige Signatur.

## <a name="403-forbidden"></a>403 Verboten
Die Anforderung ist aus einem der folgenden Gründe nicht zulässig:

* Das Media Services-Konto konnte nicht gefunden werden oder wurde gelöscht.
* Das Media Services-Konto ist deaktiviert, und der Anforderungstyp ist nicht HTTP GET. Dienstvorgänge geben ebenfalls eine 403-Antwort zurück.
* Das Authentifizierungstoken enthält die Anmeldeinformationen des Benutzers nicht: Kontoname oder Abonnement-ID. Sie finden diese Informationen in der Media Services-Benutzeroberflächenerweiterung für Ihr Media Services-Konto im Azure-Verwaltungsportal.
* Auf die Ressource kann nicht zugegriffen werden.
  
  * Es wurde versucht, einen MediaProcessor zu verwenden, der für Ihr Media Services-Konto nicht verfügbar ist.
  * Es wurde versucht, eine durch Media Services definierte JobTemplate zu aktualisieren.
  * Es wurde versucht, den Locator eines anderen Media Services-Kontos zu überschreiben.
  * Es wurde versucht, den ContentKey eines anderen Media Services-Kontos zu überschreiben.
* Die Ressource konnte aufgrund einer Dienstkontingentgrenze, die für das Media Services-Konto erreicht wurde, nicht erstellt werden. Weitere Informationen zu den Dienstkontingentgrenzen finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 – Nicht gefunden
Die Anforderung ist aus einem der folgenden Gründe auf einer Ressource nicht zulässig:

* Es wurde versucht, eine Entität zu aktualisieren, die nicht vorhanden ist.
* Es wurde versucht, eine Entität zu löschen, die nicht vorhanden ist.
* Es wurde versucht, eine Entität mit einer Verknüpfung zu einer anderen Entität zu erstellen, die nicht vorhanden ist.
* Es wurde versucht, mithilfe von GET eine Entität abzurufen, die nicht vorhanden ist.
* Es wurde versucht, ein Speicherkonto anzugeben, das dem Media Services-Konto nicht zugeordnet ist.  

## <a name="409-conflict"></a>409 – Konflikt
Die Anforderung ist aus einem der folgenden Gründe nicht zulässig:

* Mehr als eine AssetFile im Medienobjekt hat den angegebenen Namen.
* Es wurde versucht, eine zweite primäre AssetFile im Medienobjekt zu erstellen.
* Es wurde versucht, einen ContentKey mit der bereits verwendeten, angegebenen ID zu erstellen.
* Es wurde versucht, einen Locator mit der bereits verwendeten, angegebenen ID zu erstellen.
* Mehr als eine IngestManifestFile im IngestManifest hat den angegebenen Namen.
* Es wurde versucht, einen zweiten ContentKey zur Speicherverschlüsselung mit dem speicherverschlüsselten Medienobjekt zu verknüpfen.
* Es wurde versucht, den gleichen ContentKey mit dem Medienobjekt zu verknüpfen.
* Es wurde versucht, einen Locator für ein Medienobjekt zu erstellen, dessen Speichercontainer fehlt oder dem Medienobjekt nicht mehr zugeordnet ist.
* Es wurde versucht, einen Locator für ein Medienobjekt zu erstellen, das bereits 5 Locators verwendet. (Azure Storage erzwingt den Grenzwert von fünf SAS-Richtlinien in einem Speichercontainer.)
* Das Verknüpfen eines Speicherkontos eines Medienobjekts mit einem IngestManifestAsset ist nicht das gleiche wie das Speicherkonto, das vom übergeordneten IngestManifest verwendet wird.  

## <a name="500-internal-server-error"></a>500 Interner Serverfehler
Während der Verarbeitung der Anforderung tritt bei Media Services ein Fehler auf, der verhindert, dass die Verarbeitung fortgesetzt wird. Dies kann einen der folgenden Gründe haben:

* Beim Erstellen eines Medienobjekts oder eines Auftrags tritt ein Fehler auf, da die Dienstkontingentinformationen des Media Services-Kontos derzeit nicht verfügbar sind.
* Beim Erstellen eines Medienobjekts oder eines IngestManifest-Blob Storage-Containers tritt ein Fehler auf, das die Speicherkontoinformationen des Kontos derzeit nicht verfügbar sind.
* Sonstiger unerwarteter Fehler

## <a name="503-service-unavailable"></a>503 Dienst nicht verfügbar
Der Server kann derzeit keine Anforderungen empfangen. Dieser Fehler kann durch zu viele Anforderungen an den Dienst verursacht werden. Der Drosselungsmechanismus von Media Services schränkt die Ressourcenverwendung für Anwendungen ein, die zu viele Anforderungen an den Dienst ausgeben.

> [!NOTE]
> Überprüfen Sie die Fehlermeldung und die Fehlercode-Zeichenfolge, um ausführlichere Informationen zu dem Grund zu erhalten, aus dem Ihnen der 503-Fehler angezeigt wurde. Dieser Fehler bedeutet nicht immer eine Drosselung.
> 
> 

Mögliche Statusbeschreibungen sind folgende:

* „Der Server ist ausgelastet. Vorherige Ausführungen dieses Anforderungstyps haben mehr als {0} Sekunden gedauert.“
* „Der Server ist ausgelastet. Mehr als {0} Anforderungen pro Sekunde können gedrosselt werden.“
* „Der Server ist ausgelastet. Mehr als {0} Anforderungen in {1} Sekunden können gedrosselt werden.“

Zum Behandeln dieses Fehlers wird empfohlen, die exponentielle Backoff-Wiederholungslogik zu verwenden. Das bedeutet, zunehmend längere Wartezeiten zwischen Wiederholungsversuchen für aufeinander folgende Fehlerantworten zu verwenden.  Weitere Informationen finden Sie unter [What Does the Transient Fault Handling Application Block Do? (Funktion des Anwendungsblocks zum Behandeln vorübergehender Fehler)](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Wenn Sie das [Azure Media Services SDK für .NET](https://github.com/Azure/azure-sdk-for-media-services/tree/master) verwenden, wurde die Wiederholungslogik für den 503-Fehler vom SDK implementiert.  
> 
> 

## <a name="see-also"></a>Siehe auch
[Media Services Management Error Codes (Media Services Management-Fehlercodes)](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

