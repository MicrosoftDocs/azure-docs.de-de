---
title: Überprüfen des Status von Azure Import/Export-Aufträgen | Microsoft-Dokumentation
description: Informationen zum Anzeigen des Status der Import/Export-Aufträge und verwendeten Laufwerke.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 225164fe00f70839446f8b74155cd3959f745a49
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478024"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Anzeigen des Status von Azure Import/Export-Aufträgen

Dieser Artikel enthält Informationen zum Anzeigen des Laufwerks-und Auftragsstatus für Azure Import/Export-Aufträge. Mit dem Azure Import/Export-Dienst werden große Datenmengen sicher in Azure-Blobs und Azure Files übertragen. Der Dienst wird auch verwendet, um Daten aus dem Azure-Blobspeicher zu exportieren.  

## <a name="view-job-and-drive-status"></a>Anzeigen von Auftrags- und Laufwerksstatus
Sie können den Status Ihrer Import- oder Exportaufträge im Azure-Portal nachverfolgen. Klicken Sie auf die Registerkarte **Import/Export**. Eine Liste Ihrer Aufträge wird auf der Seite angezeigt.

![Anzeigen des Auftragsstatus](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Anzeigen des Auftragsstatus

Je nachdem, an welchem Punkt des Prozesses sich Ihre Festplatte befindet, wird einer der folgenden Auftragsstatus angezeigt.

| Auftragsstatus | BESCHREIBUNG |
|:--- |:--- |
| Wird erstellt | Nachdem ein Auftrag erstellt wurde, ist sein Status auf **Wird erstellt** festgelegt. Während der Auftrag sich im Status **Wird erstellt** befindet, geht der Import/Export-Dienst davon aus, dass die Laufwerke noch nicht an das Rechenzentrum versendet wurden. Ein Auftrag verbleibt bis zu zwei Wochen in diesem Status. Anschließend wird er automatisch vom Dienst gelöscht. |
| Versand | Nachdem Sie Ihr Paket gesendet haben, sollten Sie die Nachverfolgungsinformationen im Azure-Portal aktualisieren.  Dies versetzt den Auftrag in den Zustand **Wird versendet**. Der Auftrag verbleibt bis zu zwei Wochen im Status **Wird versendet**. 
| Empfangen | Nachdem das Rechenzentrum alle Laufwerke empfangen hat, wird der Status des Auftrags auf **Empfangen** gesetzt. |
| Übertragung wird ausgeführt | Nachdem die Verarbeitung mindestens eines Laufwerks begonnen hat, wird der Status des Auftrags auf **Übertragung wird ausgeführt** festgelegt. Weitere Informationen finden Sie unter [Laufwerkstatus](#view-drive-status). |
| Verpackung | Nachdem die Verarbeitung aller Laufwerke abgeschlossen ist, wird der Auftrag in den Status **Verpackung** gesetzt, bis die Laufwerke an den Kunden zurückgesendet werden. |
| Abgeschlossen | Nachdem alle Laufwerke an Sie zurückgesendet worden sind, und der Auftrag ohne Fehler abgeschlossen wurde, wird der Auftrag in den **Abgeschlossen**-Status gesetzt. Nach 90 Tagen im Status **Abgeschlossen** wird der Auftrag automatisch gelöscht. |
| Geschlossen | Nachdem alle Laufwerke an Sie zurückgesendet worden sind, und Fehler während der Verarbeitung des Auftrags aufgetreten sind, wird der Auftrag auf **Geschlossen** festgelegt. Nach 90 Tagen im Status **Geschlossen** wird der Auftrag automatisch gelöscht. |

## <a name="view-drive-status"></a>Anzeigen des Laufwerkstatus

In der folgenden Tabelle wird der Lebenszyklus eines einzelnen Laufwerks mit seinen Übergängen in einen Import- oder Exportauftrag beschrieben. Der aktuelle Status der einzelnen Laufwerke in einem Auftrag wird im Azure-Portal angezeigt.

Die folgende Tabelle beschreibt die einzelnen Status, die jedes Laufwerk in einem Auftrag durchlaufen kann.

| Laufwerkstatus | BESCHREIBUNG |
|:--- |:--- |
| Angegeben | Für einen Importauftrag, der im Azure-Portal erstellt wurde, befindet sich ein Laufwerk anfänglich im Status **Angegeben**. Für einen Exportauftrag befindet sich ein Laufwerk anfänglich im **Empfangen**-Status, da beim Erstellen des Auftrags kein Laufwerk angegeben wird. |
| Empfangen | Das Laufwerk wechselt in den Status **Empfangen**, wenn der Import/Export-Dienstanbieter die Laufwerke verarbeitet hat, die vom Transportunternehmen für einen Importauftrag empfangen wurden. Für einen Exportauftrag ist der anfängliche Laufwerkstatus der Status **Empfangen**. |
| NeverReceived | Das Laufwerk wechselt in den Status **NeverReceived**, wenn das Paket für einen Auftrag eintrifft, aber kein Laufwerk enthält. Ein Laufwerk wird auch in diesen Status verschoben, wenn zwei Wochen verstrichen sind, seitdem der Dienst die Versandinformationen erhalten hat, aber das Paket noch nicht im Rechenzentrum eingetroffen ist. |
| Übertragung wird ausgeführt | Ein Laufwerk wechselt in den Status **Übertragung wird ausgeführt**, wenn der Dienst beginnt, Daten aus dem Laufwerk in Azure Storage zu übertragen. |
| Abgeschlossen | Ein Laufwerk wechselt in den Status **Abgeschlossen**, wenn der Dienst alle Daten ohne Fehler erfolgreich übertragen hat.
| CompletedMoreInfo | Ein Laufwerk wechselt in den Status **CompletedMoreInfo**, wenn der Dienst beim Kopieren von Daten entweder von dem Laufwerk oder auf das Laufwerk auf Probleme gestoßen ist. Die Informationen können Fehler, Warnungen oder Informationsmeldungen zum Überschreiben von Blobs umfassen.
| ShippedBack | Das Laufwerk wechselt in den Status **ShippedBack**, wenn es vom Rechenzentrum an die Rücksendeadresse zurückgesendet wurde. |

Dieser Screenshot des Azure-Portals zeigt den Laufwerkstatus in einem Beispielauftrag:

![Anzeigen des Laufwerkstatus](./media/storage-import-export-service/drivestate.png)

Die folgende Tabelle beschreibt die Laufwerkfehlerstatus und die Aktionen, die für jeden Status durchgeführt werden.

| Laufwerkstatus | Ereignis | Lösung / Nächster Schritt |
|:--- |:--- |:--- |
| NeverReceived | Ein Laufwerk mit der Kennzeichnung **NeverReceived** (da es im Rahmen des Versands des Auftrags nicht eingegangen ist) geht in einer anderen Sendung ein. | Das Betriebsteam verschiebt das Laufwerk nach **Empfangen**. |
| – | Ein Laufwerk, das nicht Teil eines Auftrags ist, trifft als Teil eines anderen Auftrags im Rechenzentrum ein. | Das Laufwerk wird als zusätzliches Laufwerk gekennzeichnet und nach Abschluss des Auftrags, der dem Originalpaket zugeordnet ist, an Sie zurückgegeben. |

## <a name="time-to-process-job"></a>Verarbeitungsdauer des Auftrags
Der Zeitaufwand zum Verarbeiten eines Import/Export-Auftrags variiert basierend auf einer Reihe von Faktoren wie z.B.:

-  Versandzeit
-  Auftragsaufkommen im Rechenzentrum
-  Auftragstyp und Volumen der zu kopierenden Daten
-  Anzahl der Datenträger in einem Auftrag. 

Der Import/Export-Dienst verfügt nicht über eine SLA. Nach Erhalt der Datenträger versucht der Dienst aber, die Kopie in sieben bis zehn Tagen fertigzustellen. Zusätzlich zum Status, der im Azure-Portal angegeben wird, können REST-APIs zum Nachverfolgen des Auftragsstatus verwendet werden. Der API-Aufruf des Vorgangs zum [Auflisten von Aufträgen](/previous-versions/azure/dn529083(v=azure.100)) enthält einen Parameter für die prozentuale Fertigstellung, mit dem Sie den Status des Kopiervorgangs verfolgen können.


## <a name="next-steps"></a>Nächste Schritte

* [Einrichten des WAImportExport-Tools](storage-import-export-tool-how-to.md)
* [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md)
* [Azure Import/Export-REST-API-Beispiel](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
