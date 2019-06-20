---
title: Verwenden des Azure Import/Export-Diensts zum Übertragen von Daten in und aus Azure Storage | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Import- und Exportaufträge im Azure-Portal erstellen, um Daten in und aus Azure Storage zu übertragen.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 97a3ac275613b644dfd90144039e4f3127186997
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65603111"
---
# <a name="what-is-azure-importexport-service"></a>Was ist der Azure Import/Export-Dienst?

Mit dem Azure Import/Export-Dienst können Sie große Datenmengen auf sichere Weise in Azure Blob Storage und Azure Files übertragen, indem Sie Festplattenlaufwerke an ein Azure-Rechenzentrum senden. Sie können diesen Dienst auch zum Übertragen von Daten aus Azure Blob Storage auf Festplattenlaufwerke und zum Versand an lokale Standorte nutzen. Daten von einem oder mehreren Datenträgern können in Azure Blob Storage oder in Azure Files importiert werden. 

Stellen Sie eigene Datenträger bereit, und übermitteln Sie Daten mit dem Azure Import/Export-Dienst. Sie können auch Laufwerke verwenden, die von Microsoft bereitgestellt werden. 

Wenn Sie Daten mit den von Microsoft bereitgestellten Datenträgern übermitteln möchten, können Sie mithilfe des [Azure Data Box-Datenträgers](../../databox/data-box-disk-overview.md) Daten in Azure importieren. Microsoft sendet über einen regionalen Transportdienstleister bis zu fünf verschlüsselte SSDs (Solid State Drives) mit einer Gesamtkapazität von 40 TB an Ihr Rechenzentrum. Sie können Datenträger schnell konfigurieren, Daten über eine USB-3.0-Verbindung auf die Datenträger kopieren und diese anschließend wieder an Azure zurücksenden. Weitere Informationen finden Sie in der [Übersicht über den Azure Data Box-Datenträger (Vorschauversion)](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-usecases"></a>Anwendungsfälle für Azure Import/Export

Sie können den Import/Export-Dienst von Azure nutzen, wenn das Hoch- bzw. Herunterladen von Daten über das Netzwerk zu langsam oder das Hinzufügen weiterer Bandbreite aus Kostengründen nicht möglich ist. Verwenden Sie diesen Dienst in folgenden Szenarien:

* **Datenmigration zur Cloud**: Verschieben Sie große Datenmengen schnell und kostengünstig nach Azure.
* **Inhaltsverteilung**: Senden Sie Daten schnell an Ihre Kundenstandorte.
* **Backup** (Sichern): Erstellen Sie Sicherungen Ihrer lokalen Daten für die Speicherung in Azure Storage.
* **Datenwiederherstellung**: Stellen Sie große Datenmengen wieder her, die im Speicher gespeichert sind, und lassen Sie sie an Ihren lokalen Standort schicken.

## <a name="importexport-components"></a>Komponenten von Import/Export

Der Import/Export-Dienst verwendet die folgenden Komponenten:

- **Import/Export-Dienst**: Dieser Dienst ist im Azure-Portal verfügbar und unterstützt Benutzer beim Erstellen und Nachverfolgen von Import- (Upload) und Exportaufträgen (Download) für Daten.  

- **WAImportExport-Tool**: Hierbei handelt es sich um ein Befehlszeilentool zur Ausführung der folgenden Aufgaben: 
    - Vorbereiten der Laufwerke, die für den Import versendet werden
    - Kopieren der Daten auf das Laufwerk
    - Verschlüsseln der Daten auf dem Laufwerk mit BitLocker
    - Generieren der Laufwerks-Journaldateien während der Erstellung des Imports
    - Ermitteln der Anzahl der für Exportaufträge erforderlichen Laufwerke
    
> [!NOTE]
> Das WAImportExport-Tool ist in zwei Versionen verfügbar: Version 1 und 2. Wir empfehlen Folgendes:
> - Version 1 für den Import/Export in Azure Blob Storage 
> - Version 2 für den Import von Daten in Azure Files
>
> Das WAImportExport-Tool ist nur mit dem 64-Bit-Windows-Betriebssystem kompatibel. Informationen zu den unterstützten Betriebssystemversionen finden Sie unter [Azure Import/Export – Anforderungen](storage-import-export-requirements.md#supported-operating-systems).

- **Datenträger**: Sie können Solid State Drives (SSDs) oder Festplattenlaufwerke (HDDs) an das Azure-Rechenzentrum senden. Beim Erstellen eines Importauftrags senden Sie die Laufwerke, die Ihre Daten enthalten. Wenn Sie einen Exportauftrag erstellen, senden Sie leere Laufwerke an das Azure-Rechenzentrum. Informationen zu den Datenträgertypen finden Sie unter [Unterstützte Datenträgertypen](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Funktionsweise von Import/Export

Der Azure Import/Export-Dienst ermöglicht die Datenübertragung an Azure-Blobs und Azure Files durch das Erstellen von Aufträgen. Verwenden Sie das Azure-Portal oder die REST-API des Azure Resource Managers, um Aufträge zu erstellen. Jeder Auftrag ist einem einzelnen Speicherkonto zugeordnet. 

Die Aufträge können Import- oder Exportaufträge sein. Mit einem Importauftrag können Sie Daten in Azure-Blobs oder Azure Files importieren, während ein Exportauftrag das Exportieren von Daten aus Azure-Blobs ermöglicht. Bei einem Importauftrag versenden Sie Laufwerke, die Ihre Daten enthalten. Wenn Sie einen Exportauftrag erstellen, versenden Sie leere Laufwerke an ein Azure-Rechenzentrum. In beiden Fällen können Sie bis zu 10 Festplatten pro Auftrag versenden.

### <a name="inside-an-import-job"></a>Importaufträge

Ein Importauftrag umfasst im Allgemeinen die folgenden Schritte:

1. Bestimmen Sie die zu importierenden Daten, die Anzahl der erforderlichen Laufwerke und den Ziel-Blobspeicherort für Ihre Daten in Azure Storage.
2. Verwenden Sie das WAImportExport-Tool, um die Daten auf die Festplatten zu kopieren. Verschlüsseln Sie die Datenträger mit BitLocker.
3. Erstellen Sie einen Importauftrag in Ihrem Zielspeicherkonto im Azure-Portal. Laden Sie die Laufwerks-Journaldateien hoch.
4. Geben Sie die Rücksendeadresse und die Nummer Ihres Kontos beim Kurierdienst für das Zurücksenden der Festplatten an.
5. Schicken Sie die Festplatten an die Versandadresse, die Sie während der Erstellung des Auftrags erhalten haben.
6. Aktualisieren Sie die Nummer für die Sendungsverfolgung in den Details des Importauftrags, und senden Sie den Importauftrag ab.
7. Die Festplatten kommen im Azure-Rechenzentrum an und werden verarbeitet.
8. Die Festplatten werden über Ihr Kurierdienstkonto an die Rücksendeadresse zurückgeschickt, die im Importauftrag angegeben ist.

> [!NOTE]
> Geben Sie für nationale Lieferungen (innerhalb des Landes/der Region, in dem/der sich das Rechenzentrum befindet) ein inländisches Spediteurkonto an. 
>
> Geben Sie für internationale Lieferungen (außerhalb des Lands/der Region, in dem/der sich das Rechenzentrum befindet) ein internationales Spediteurkonto an.

 ![Abbildung 1: Ablauf eines Importauftrags](./media/storage-import-export-service/importjob.png)

Ausführliche Anweisungen zum Datenimport finden Sie unter:

- [Importieren von Daten in Azure-Blobs](storage-import-export-data-to-blobs.md)
- [Importieren von Daten in Azure Files](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>Exportaufträge

> [!IMPORTANT]
> Der Dienst unterstützt nur das Exportieren von Azure-Blobs. Das Exportieren aus Azure Files wird nicht unterstützt.

Ein Exportauftrag umfasst im Allgemeinen die folgenden Schritte:

1. Legen Sie die zu exportierenden Daten, die Anzahl der erforderlichen Laufwerke und die Quellblobs oder Containerpfade Ihrer Daten im Blobspeicher fest.
3. Erstellen Sie einen Exportauftrag in Ihrem Quellspeicherkonto im Azure-Portal.
4. Geben Sie die Quellblobs oder Containerpfade für die zu exportierenden Daten an.
5. Geben Sie die Rücksendeadresse und die Nummer Ihres Kontos beim Kurierdienst für das Zurücksenden der Festplatten an.
6. Schicken Sie die Festplatten an die Versandadresse, die Sie während der Erstellung des Auftrags erhalten haben.
7. Aktualisieren Sie die Nummer für die Sendungsverfolgung in den Details des Exportauftrags, und senden Sie den Exportauftrag ab.
8. Die Festplatten kommen im Azure-Rechenzentrum an und werden verarbeitet.
9. Die Festplatten werden mit BitLocker verschlüsselt. Die Schlüssel sind über das Azure-Portal verfügbar.  
10. Die Festplatten werden über Ihr Kurierdienstkonto an die Rücksendeadresse zurückgeschickt, die im Importauftrag angegeben ist.

> [!NOTE]
> Geben Sie für nationale Lieferungen (innerhalb des Landes/der Region, in dem/der sich das Rechenzentrum befindet) ein inländisches Spediteurkonto an. 
>
> Geben Sie für internationale Lieferungen (außerhalb des Lands/der Region, in dem/der sich das Rechenzentrum befindet) ein internationales Spediteurkonto an.
  
 ![Abbildung 2: Ablauf eines Exportauftrags](./media/storage-import-export-service/exportjob.png)

Eine ausführliche Anleitung zum Datenexport finden Sie unter [Exportieren von Daten aus Azure-Blobs](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Regionale Verfügbarkeit 

Der Azure Import/Export-Dienst unterstützt das Kopieren von Daten von bzw. in alle Azure Storage-Konten. Sie können die Datenträger an einen der unten angegebenen Standorte schicken. Wenn sich Ihr Speicherkonto in einer Azure-Region befindet, die hier nicht angegeben ist, wird bei der Erstellung des Auftrags ein alternatives Versandziel bereitgestellt.

### <a name="supported-shipping-locations"></a>Unterstützte Versandstandorte


|Land/Region  |Land/Region  |Land/Region  |Land/Region  |
|---------|---------|---------|---------|
|USA (Ost)    | Nordeuropa        | Indien, Mitte        |US Gov Iowa         |
|USA (Westen)     |Europa, Westen         | Indien (Süden)        | US DoD, Osten        |
|USA (Ost) 2    | Asien, Osten        |  Indien, Westen        | US DoD, Mitte        |
|USA, Westen 2     | Asien, Südosten        | Kanada, Mitte        | China, Osten         |
|USA (Mitte)     | Australien (Osten)        | Kanada, Osten        | China, Norden        |
|USA Nord Mitte     |  Australien, Südosten       | Brasilien Süd        | UK, Süden        |
|USA Süd Mitte     | Japan, Westen        |Korea, Mitte         | Deutschland, Mitte        |
|USA, Westen-Mitte     |  Japan, Osten       | US Government, Virginia        | Deutschland, Nordosten        |


## <a name="security-considerations"></a>Sicherheitshinweise

Die Daten auf dem Laufwerk werden mit BitLocker-Laufwerkverschlüsselung verschlüsselt. Durch diese Verschlüsselung sind Ihre Daten beim Transport geschützt.

Für Importaufträge werden die Laufwerke auf zwei Arten verschlüsselt.  


- Geben Sie die Option bei Verwendung der Datei *dataset.csv* an, während das WAImportExport-Tool bei der Vorbereitung des Laufwerks ausgeführt wird. 

- Aktivieren Sie die BitLocker-Verschlüsselung manuell auf dem Laufwerk. Geben Sie den Verschlüsselungsschlüssel in der Datei *driveset.csv* bei der Ausführung der Befehlszeile des WAImportExport-Tools während der Laufwerkvorbereitung an.


Bei Exportaufträgen wendet der Dienst nach dem Kopieren Ihrer Daten auf die Laufwerke jeweils die BitLocker-Verschlüsselung auf das Laufwerk an, bevor es an Sie zurückgeschickt wird. Der Verschlüsselungsschlüssel wird Ihnen über das Azure Portal bereitgestellt.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>Preise

**Bearbeitungsgebühr für Festplatte**

Für jede Festplatte, die im Rahmen eines Import- oder Exportauftrags verarbeitet wird, wird eine Bearbeitungsgebühr berechnet. Informationen hierzu finden Sie unter [Preise für Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Versandkosten**

Wenn Sie Festplatten an Azure schicken, übernehmen Sie die Versandkosten des Kurierdiensts. Wenn die Festplatten von Microsoft an Sie zurückgeschickt werden, werden die Versandkosten Ihrem Kurierdienstkonto belastet, das Sie bei der Erstellung des Auftrags angegeben haben.

**Transaktionskosten**

Für den Import und Export von Daten fallen [Standard-Speichertransaktionsgebühren](https://azure.microsoft.com/pricing/details/storage/) an. Standardgebühren für ausgehende Daten fallen zusammen mit Speichertransaktionsgebühren ebenfalls an, wenn Daten aus Azure Storage exportiert werden. Weitere Informationen zu den Ausgangskosten finden Sie unter [Preisübersicht Bandbreite](https://azure.microsoft.com/pricing/details/data-transfers/).



## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie den Import/Export-Dienst für Folgendes verwenden:
* [Importieren von Daten in Azure-Blobs](storage-import-export-data-to-blobs.md)
* [Exportieren von Daten aus Azure-Blobs](storage-import-export-data-from-blobs.md)
* [Importieren von Daten in Azure Files](storage-import-export-data-to-files.md)

