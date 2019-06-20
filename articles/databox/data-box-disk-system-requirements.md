---
title: Systemanforderungen für Microsoft Azure Data Box-Datenträger | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie mehr über die Software- und Netzwerkanforderungen für Ihre Azure Data Box-Datenträger.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 067bb6b806ddd9b83d4ea755876a980ca45d76e9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60387467"
---
# <a name="azure-data-box-disk-system-requirements"></a>Systemanforderungen für Azure Data Box Disk

In diesem Artikel werden wichtige Systemanforderungen für Ihre Microsoft Azure Data Box-Datenträgerlösung und die Clients beschrieben, die mit dem Data Box-Datenträger verbunden sind. Sie sollten die Informationen sorgfältig lesen, bevor Sie Ihren Data Box-Datenträger bereitstellen. Auch später sollten Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Die Systemanforderungen enthalten die unterstützten Plattformen für Clients, die eine Verbindung mit Datenträgern, unterstützten Speicherkonten und Speichertypen herstellen.


## <a name="supported-operating-systems-for-clients"></a>Unterstützte Betriebssysteme für Clients

Im Folgenden finden Sie eine Liste der unterstützten Betriebssysteme für Datenträgerentsperrungen und Datenkopiervorgänge über die Clients, die mit dem Data Box-Datenträger verbunden sind.

| **Betriebssystem** | **Getestete Version** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Andere erforderliche Software für Windows-Clients

Für den Windows-Client sollte darüber hinaus noch Folgendes installiert sein.

| **Software**| **Version** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Andere erforderliche Software für Linux-Clients

Für den Linux-Client installiert das Data Box-Datenträgertoolset die folgende erforderliche Software:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Unterstützte Verbindung

Der Clientcomputer, auf dem sich die Daten befinden, muss über einen USB 3.0-Anschluss (oder höher) verfügen. Die Datenträger werden mit dem bereitgestellten Kabel an diesen Client angeschlossen.

## <a name="supported-storage-accounts"></a>Unterstützte Speicherkonten

Im Folgenden finden Sie eine Liste der unterstützten Speichertypen für den Data Box-Datenträger.

| **Speicherkonto** | **Hinweise** |
| --- | --- |
| Klassisch | Standard |
| Allgemeiner Zweck  |Standard: sowohl V1 als auch V2 werden unterstützt. Es werden sowohl „heiße“ als auch „kalte“ Ebenen unterstützt. |
| Blob-Speicherkonto | |

>[!NOTE]
> Azure Data Lake Storage Gen 2-Konten werden nicht unterstützt.


## <a name="supported-storage-types-for-upload"></a>Unterstützte Speichertypen für den Upload

Im Folgenden finden Sie eine Liste der Speichertypen, die für den Upload in Azure mithilfe von Data Box Disk unterstützt werden.

| **Dateiformat** | **Hinweise** |
| --- | --- |
| Azure-Blockblob | |
| Azure-Seitenblob  | |
| Azure Files  | |
| Managed Disks | |


## <a name="next-step"></a>Nächster Schritt

* [Bereitstellen des Azure Data Box-Datenträgers](data-box-disk-deploy-ordered.md)

