---
title: Entscheidung zwischen Azure-Blobs, Azure Files und Azure-Datenträger
description: In diesem Artikel erfahren Sie, wie Sie Daten auf unterschiedliche Weise in Azure speichern und darauf zugreifen können. So wird Ihnen die Entscheidung erleichtert, welche Technologie Sie verwenden sollten.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 30c7c1c50e59162817d7cfab0d852d8e034457d0
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969423"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Entscheidung zwischen Azure-Blobs, Azure Files und Azure-Datenträger

Microsoft Azure bietet verschiedenen Funktionen in Azure Storage zum Speichern und Zugreifen auf Ihre Daten in der Cloud. Dieser Artikel befasst sich mit Azure Files, Blobs und Datenträger und soll Sie bei der Entscheidung zwischen diesen Funktionen unterstützen.

## <a name="scenarios"></a>Szenarien

In der folgenden Tabelle werden Files, Blobs und Datenträger miteinander verglichen. Darüber hinaus werden jeweils passende Beispielszenarios gegeben.

| Feature | BESCHREIBUNG | Einsatzgebiete |
|--------------|-------------|-------------|
| **Azure Files** | Bietet eine SMB-Schnittstelle, Clientbibliotheken und eine [REST-Schnittstelle](/rest/api/storageservices/file-service-rest-api), mit denen Sie von überall auf gespeicherte Dateien zugreifen können. | Wenn Sie eine Anwendung mit der „Lift and Shift“-Methode in die Cloud verschieben möchten, die bereits die nativen Dateisystem-APIs verwendet, um Daten für andere in Azure ausgeführte Anwendungen freizugeben.<br/><br/>Wenn Sie Tools zum Entwickeln und Debuggen speichern möchten, auf die von vielen virtuellen Computern zugegriffen werden muss. |
| **Azure-Blobs** | Bietet Clientbibliotheken und eine [REST-Schnittstelle](/rest/api/storageservices/blob-service-rest-api), der große Mengen an unstrukturierten Daten in Blockblobs gespeichert und abgerufen werden können.<br/><br/>Blob Storage unterstützt darüber hinaus [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) für Big Data-Analyselösungen auf Unternehmensniveau. | Wenn Sie möchten, dass Ihre Anwendung Szenarios für das Streaming und den zufälligen Zugriff unterstützt.<br/><br/>Wenn Sie die Möglichkeit haben möchten, von überall auf Anwendungsdaten zugreifen zu können.<br/><br/>Sie möchten einen Unternehmens-Data Lake in Azure aufbauen und Big Data-Analysen durchführen. |
| **Azure-Datenträger** | Bietet Clientbibliotheken und eine [REST-Schnittstelle](/rest/api/compute/manageddisks/disks/disks-rest-api), mit der Sie Daten beständig von einer angefügten virtuellen Festplatte speichern und abrufen können. | Wenn Sie eine Anwendung mit der „Lift and Shift“-Methode verschieben möchten, die native Dateisystem-APIs verwenden, um Daten in beständigen Datenträgern zu lesen und dort hinein zu schreiben.<br/><br/>Wenn Sie Daten speichern möchten, auf die nicht von außerhalb des virtuellen Computers zugegriffen werden muss, an den der Datenträger angefügt ist. |

## <a name="comparison-files-and-blobs"></a>Vergleich: Dateien und Blobs

In der folgenden Tabelle wird Azure Files mit Azure-Blobs verglichen.  
  
||||  
|-|-|-|  
|**Attribut**|**Azure-Blobs**|**Azure Files**|  
|Optionen für die Dauerhaftigkeit|LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|Barrierefreiheit|REST-APIs|REST-APIs<br /><br /> SMB 2.1 und SMB 3.0 (Standard-Dateisystem-APIs)|  
|Konnektivität|REST-APIs – Weltweit|REST-APIs – Weltweit<br /><br /> SMB 2.1 – Innerhalb einer Region<br /><br /> SMB 3.0 – Weltweit|  
|Endpunkte|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Verzeichnisse|Flacher Namespace|Echte Verzeichnisobjekte|  
|Beachtung von Groß-/Kleinschreibung bei Namen|Groß-/Kleinschreibung beachten|Keine Beachtung von Groß-/Kleinschreibung, aber die Schreibweise wird beibehalten|  
|Capacity|Bis zu 2 PiB-Kontobegrenzung |5 TiB Dateifreigaben|  
|Throughput|Bis zu 60 MiB/s pro Blockblob|Bis zu 60 MiB/s pro Freigabe|  
|Objektgröße|Bis zu ungefähr 4,75 TiB pro Blockblob|Bis zu 1 TiB pro Datei|  
|Berechnete Kapazität|Basierend auf geschriebenen Bytes|Basierend auf der Dateigröße|  
|Clientbibliotheken|Mehrere Sprachen|Mehrere Sprachen|  
  
## <a name="comparison-files-and-disks"></a>Vergleich: Dateien und Datenträger

Azure Files ergänzt Azure-Datenträger. Ein Datenträger kann immer nur an eine Azure-VM angefügt werden. Datenträger sind VHDs mit festem Format, die als Seitenblobs in Azure Storage gespeichert werden. Sie werden von der VM verwendet, um permanente Daten zu speichern. Auf Dateifreigaben kann in Azure Files genauso zugegriffen werden wie auf den lokalen Datenträger (mit nativen Dateisystem-APIs). Sie können VM-übergreifend freigegeben werden.  

In der folgenden Tabelle wird Azure Files mit Azure-Datenträger verglichen.  

||||  
|-|-|-|  
|**Attribut**|**Azure-Datenträger**|**Azure Files**|  
|`Scope`|Nur für einen einzelnen virtuellen Computer|Freigegebener Zugriff für mehrere virtuelle Computer|  
|Momentaufnahmen und Kopieren|Ja|Ja|  
|Konfiguration|Verbindung beim Start des virtuellen Computers|Verbindung nach dem Start des virtuellen Computers|  
|Authentication|Integriert|Wird mit „net use“ eingerichtet|  
|Zugriff mithilfe von REST|Auf Dateien auf dem virtuellen Datenträger kann nicht zugegriffen werden.|Auf Dateien, die in einer Freigabe gespeicherte sind, kann zugegriffen werden.|  
|Max. Größe|32-TiB-Datenträger|5-TiB-Dateifreigabe und eine 1-TiB-Datei innerhalb der Freigabe|  
|Max. IOPS|20.000 IOPS|1.000 IOPS|  
|Throughput|Bis zu 900 MiB/s pro Datenträger|Ziel ist 60 MiB/s pro Dateifreigabe (kann für höhere E/A-Größen höher sein)|  

## <a name="next-steps"></a>Nächste Schritte

Bei der Entscheidung für die Art und Weise, wie Sie Ihre Daten speichern und auf diese zugreifen möchten, sollten Sie auch die Kosten berücksichtigen. Weitere Informationen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
  
Einige SMB-Funktion sind nicht in der Cloud verfügbar. Weitere Informationen finden Sie unter [Features not supported by the Azure File service (Funktionen, die vom Azure-Dateidienst nicht unterstützt werden)](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Weitere Informationen zu Datenträgern finden Sie in unserer [Einführung zu verwalteten Datenträgern](../../virtual-machines/windows/managed-disks-overview.md) und unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer, der mit dem klassischen Bereitstellungsmodell erstellt wurde](../../virtual-machines/windows/attach-managed-disk-portal.md).
