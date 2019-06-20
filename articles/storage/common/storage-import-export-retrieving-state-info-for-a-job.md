---
title: Abrufen von Statusinformationen für einen Azure Import/Export-Auftrag | Microsoft-Dokumentation
description: Informationen zum Abrufen von Statusinformationen für Aufträge des Microsoft Azure Import/Export-Diensts.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 1a878b5a9f0502ff9acd411359895d7431fb76f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478653"
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Abrufen von Statusinformationen für einen Import/Export-Auftrag
Sie können den Vorgang [Get Job](/rest/api/storageimportexport/jobs) zum Abrufen von Informationen zu Import- und Exportaufträgen aufrufen. Die zurückgegebenen Informationen enthalten:

-   Den aktuellen Status des Auftrags.

-   Den geschätzten Fertigstellungsprozentsatz jedes einzelnen Auftrags.

-   Den aktuellen Status jedes Laufwerks.

-   URIs für Blobs, die Fehlerprotokolle und Informationen zur ausführlichen Protokollierung (sofern aktiviert) enthalten.

In den folgenden Abschnitten werden die Informationen erläutert, die der `Get Job`-Vorgang zurückgibt.

## <a name="job-states"></a>Auftragsstatus
Die Tabelle und das Statusdiagramm unten beschreiben die Status, die ein Auftrag während seines Lebenszyklus durchläuft. Der aktuelle Status des Auftrags kann durch Aufrufen des `Get Job`-Vorgangs ermittelt werden.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

Die folgende Tabelle beschreibt die einzelnen Status, die ein Auftrag durchlaufen kann.

|Auftragsstatus|BESCHREIBUNG|
|---------------|-----------------|
|`Creating`|Nachdem Sie den „Put Job“-Vorgang aufgerufen haben, wird ein Auftrag erstellt und sein Status auf `Creating` festgelegt. Während der Auftrag sich im `Creating`-Status befindet, geht der Import/Export-Dienst davon aus, dass die Laufwerke noch nicht an das Rechenzentrum versendet wurden. Ein Auftrag verbleibt bis zu zwei Wochen im `Creating`-Status. Anschließend wird er automatisch vom Dienst gelöscht.<br /><br /> Wenn Sie den „Update Job Properties“-Vorgang aufrufen, während der Auftrag sich im `Creating`-Status befindet, verbleibt der Auftrag im `Creating`-Status, und das Timeoutintervall wird auf zwei Wochen zurückgesetzt.|
|`Shipping`|Nachdem Sie Ihr Paket gesendet haben, sollten Sie den Update Job Properties-Vorgang aufrufen, um den Status des Auftrags auf `Shipping` zu aktualisieren. Der Versandstatus kann nur dann festgelegt werden, wenn die Eigenschaften `DeliveryPackage` (Transportfirma und Nachverfolgungsnummer) und `ReturnAddress` für den Auftrag festgelegt wurden.<br /><br /> Der Auftrag verbleibt bis zu zwei Wochen im Status „Wird versendet“. Wenn die Laufwerke nach zwei Wochen noch nicht eingetroffen sind, werden die Import/Export-Dienstanbieter benachrichtigt.|
|`Received`|Nachdem das Rechenzentrum alle Laufwerke empfangen hat, wird der Status des Auftrags auf „Received“ gesetzt.|
|`Transferring`|Wenn nach Eingang der Laufwerke im Rechenzentrum die Verarbeitung mindestens eines Laufwerks begonnen hat, wird der Status des Auftrags auf `Transferring` gesetzt. Ausführliche Informationen finden Sie im `Drive States`-Abschnitt.|
|`Packaging`|Nachdem die Verarbeitung aller Laufwerke abgeschlossen ist, wird der Auftrag in den `Packaging`-Status gesetzt, bis die Laufwerke an den Kunden zurückgesendet werden.|
|`Completed`|Nachdem alle Laufwerke an den Kunden zurückgesendet worden sind, und der Auftrag ohne Fehler abgeschlossen wurde, wird der Auftrag in den `Completed`-Status gesetzt. Nach 90 Tagen im `Completed`-Status wird der Auftrag automatisch gelöscht.|
|`Closed`|Nachdem alle Laufwerke an den Kunden zurückgesendet worden sind, und während der Verarbeitung des Auftrags Fehler aufgetreten sind, wird der Auftrag in den `Closed`-Status gesetzt. Nach 90 Tagen im `Closed`-Status wird der Auftrag automatisch gelöscht.|

Sie können einen Auftrag nur in bestimmten Status abbrechen. Für einen abgebrochenen Auftrag wird der Datenkopierschritt übersprungen, aber ansonsten unterliegt er den gleichen Statusübergängen wie ein nicht abgebrochener Auftrag.

Die folgende Tabelle beschreibt die Fehler, die in den einzelnen Auftragsstatus auftreten können, sowie die Auswirkungen, die ein Fehler auf den Auftrag hat.

|Auftragsstatus|Ereignis|Lösung / Nächste Schritte|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Mindestens ein Laufwerk ist für einen Auftrag eingetroffen, aber der Auftrag befindet sich nicht im `Shipping`-Status, oder der Dienst enthält keinen Eintrag für den Auftrag.|Das Import/Export-Dienstbetriebsteam versucht, mit dem Kunden Kontakt aufzunehmen, um den Auftrag mit den Informationen zu erstellen oder zu aktualisieren, die erforderlich sind, um den Auftrag voran zu bringen.<br /><br /> Wenn das Betriebsteam innerhalb von zwei Wochen keinen Kontakt mit dem Kunden aufnehmen kann, versucht es, die Laufwerke zurückzusenden.<br /><br /> Wenn die Laufwerke nicht zurückgesendet werden können und der Kunde nicht erreicht werden kann, werden die Laufwerke nach 90 Tagen auf sichere Weise zerstört.<br /><br /> Beachten Sie, dass ein Auftrag erst dann verarbeitet werden kann, wenn sein Status auf `Shipping` aktualisiert ist.|
|`Shipping`|Das Paket für einen Auftrag ist seit über zwei Wochen nicht eingegangen.|Das Betriebsteam benachrichtigt den Kunden über das fehlende Paket. Je nach Antwort des Kunden wird das Betriebsteam entweder das Wartezeitintervall für den Eingang des Pakets erweitern oder den Auftrag abbrechen.<br /><br /> Wenn der Kunde nicht kontaktiert werden kann oder nicht innerhalb von 30 Tagen reagiert, veranlasst das Betriebsteam, dass der Auftrag direkt aus dem `Shipping`- in den `Closed`-Status verschoben wird.|
|`Completed/Closed`|Die Laufwerke haben die Rücksendeadresse nicht erreicht oder wurden während des Versands beschädigt (gilt nur für einen Exportauftrag).|Wenn die Laufwerke die Rücksendeadresse nicht erreichen, sollte der Kunde zuerst den „Get Job“-Vorgang aufrufen oder den Auftragsstatus im Portal überprüfen, um sicherzustellen, dass die Laufwerke versendet wurden. Wenn die Laufwerke versendet wurden, sollte der Kunde sich an den Versandanbieter wenden, um zu versuchen, die Laufwerke ausfindig zu machen.<br /><br /> Wenn die Laufwerke während des Versands beschädigt werden, kann der Kunde einen anderen Exportauftrag anfordern oder die fehlenden Blobs herunterladen.|
|`Transferring/Packaging`|Rücksendeadresse des Auftrags ist falsch oder fehlt.|Das Betriebsteam wird die Kontaktperson um Angabe der richtigen Adresse für den Auftrag bitten.<br /><br /> Falls der Kunde nicht erreicht werden kann, werden die Laufwerke nach 90 Tagen auf sichere Weise zerstört.|
|`Creating / Shipping/ Transferring`|Ein Laufwerk, das nicht in der Liste der zu importierenden Laufwerke enthalten ist, befindet sich im Versandpaket.|Die zusätzlichen Laufwerke werden nicht verarbeitet und an den Kunden zurückgegeben, wenn der Auftrag abgeschlossen ist.|

## <a name="drive-states"></a>Laufwerkstatus
Die Tabelle und das Diagramm im Folgenden beschreiben den Lebenszyklus eines einzelnen Laufwerks mit seinen Übergängen in einem Import- oder Exportauftrag. Sie können den aktuellen Status des Laufwerks abrufen, indem Sie den `Get Job`-Vorgang aufrufen und das `State`-Element der `DriveList`-Eigenschaft überprüfen.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

Die folgende Tabelle beschreibt die einzelnen Status, die ein Laufwerk durchlaufen kann.

|Laufwerkstatus|BESCHREIBUNG|
|-----------------|-----------------|
|`Specified`|Für einen Importauftrag, der mit dem „Put Job“-Vorgang erstellt wurde, befindet sich ein Laufwerk anfänglich im `Specified`-Status. Für einen Exportauftrag befindet sich ein Laufwerk anfänglich im `Received`-Status, da beim Erstellen des Auftrags kein Laufwerk angegeben wird.|
|`Received`|Das Laufwerk wechselt in den `Received`-Status, wenn der Import/Export-Dienstanbieter die Laufwerke verarbeitet hat, die vom Transportunternehmen für einen Importauftrag empfangen wurden. Für einen Exportauftrag ist der anfängliche Laufwerkstatus der `Received`-Status.|
|`NeverReceived`|Das Laufwerk wechselt in den `NeverReceived`-Status, wenn das Paket für einen Auftrag eintrifft, aber kein Laufwerk enthält. Ein Laufwerk kann auch in diesen Status verschoben werden, wenn zwei Wochen verstrichen sind, seitdem der Dienst die Versandinformationen erhalten hat, aber das Paket noch nicht im Rechenzentrum eingetroffen ist.|
|`Transferring`|Ein Laufwerk wechselt in den `Transferring`-Status, wenn der Dienst beginnt, Daten aus dem Laufwerk in Microsoft Azure Storage zu übertragen.|
|`Completed`|Ein Laufwerk wechselt in den `Completed`-Status, wenn der Dienst alle Daten ohne Fehler erfolgreich übertragen hat.|
|`CompletedMoreInfo`|Ein Laufwerk wechselt in den `CompletedMoreInfo`-Status, wenn der Dienst beim Kopieren von Daten entweder von dem Laufwerk oder auf das Laufwerk auf Probleme gestoßen ist. Die Informationen können Fehler, Warnungen oder Informationsmeldungen zum Überschreiben von Blobs umfassen.|
|`ShippedBack`|Das Laufwerk wechselt in den `ShippedBack`-Status, wenn es vom Rechenzentrum an die Rücksendeadresse zurückgesendet wurde.|

Die folgende Tabelle beschreibt die Laufwerkfehlerstatus und die Aktionen, die für jeden Status durchgeführt werden.

|Laufwerkstatus|Ereignis|Lösung / Nächster Schritt|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Ein Laufwerk mit der Kennzeichnung `NeverReceived` (da es im Rahmen des Versands des Auftrags nicht eingegangen ist) geht in einer anderen Sendung ein.|Das Betriebsteam versetzt das Laufwerk in den `Received`-Status.|
|`N/A`|Ein Laufwerk, das nicht Teil eines Auftrags ist, trifft als Teil eines anderen Auftrags im Rechenzentrum ein.|Das Laufwerk wird als zusätzliches Laufwerk gekennzeichnet und nach Abschluss des Auftrags, der dem Originalpaket zugeordnet ist, an den Kunden zurückgegeben.|

## <a name="faulted-states"></a>Fehlerstatus
Wenn ein Auftrag oder Laufwerk den erwarteten Lebenszyklus nicht normal durchläuft, wird der Auftrag bzw. das Laufwerk in einen `Faulted`-Status verschoben. Zu diesem Zeitpunkt nimmt das Betriebsteam per E-Mail oder telefonisch Kontakt mit dem Kunden auf. Nachdem das Problem behoben wurde, wird der fehlerhafte Auftrag bzw. das Laufwerk aus dem `Faulted`- Status in den entsprechenden anderen Status verschoben.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden der REST-API des Import/Export-Diensts](storage-import-export-using-the-rest-api.md)
