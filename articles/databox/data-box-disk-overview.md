---
title: Microsoft Azure Data Box-Datenträger – Übersicht | Microsoft-Dokumentation
description: Enthält eine Beschreibung der Cloudlösung für Azure Data Box-Datenträger, die zum Übertragen von großen Datenmengen nach Azure dient.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 05/15/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: 194f2b80e9cbf3a69fef6ce382e6755934f1d5bd
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787439"
---
# <a name="what-is-azure-data-box-disk"></a>Was ist ein Azure Data Box-Datenträger?

Mit der Lösung für Microsoft Azure Data Box-Datenträger können Sie lokale Daten im Terabyte-Bereich schnell, kostengünstig und zuverlässig an Azure senden. Die sichere Datenübertragung wird beschleunigt, indem Sie ein bis fünf SSD-Datenträger (Solid-State Disks) erhalten. Diese verschlüsselten Datenträger mit einem Speicherplatz von 8 TB werden über einen regionalen Kurierdienst an Ihr Rechenzentrum gesendet. 

Sie können die Datenträger über den Data Box-Dienst im Azure-Portal schnell konfigurieren, anschließen und entsperren. Kopieren Sie Ihre Daten auf die Datenträger, und senden Sie die Datenträger zurück an Azure. Im Azure-Rechenzentrum werden Ihre Daten automatisch von Laufwerken in die Cloud hochgeladen, indem ein Link für das schnelle Hochladen ins private Netzwerk verwendet wird.

## <a name="use-cases"></a>Anwendungsfälle

Verwenden Sie Data Box-Datenträger, um Daten im TB-Bereich in Fällen zu übertragen, in denen keine oder nur eingeschränkte Netzwerkkonnektivität vorhanden ist. Die Datenverschiebung kann eine einmalige, eine periodische oder eine erste Massenübertragung von Daten sein, auf die regelmäßige Übertragungen folgen. 

- **Einmalige Migration**: Wird verwendet, wenn eine große Menge von lokalen Daten in Azure verschoben wird. Ein Beispiel hierfür ist das Verschieben von Daten von Offlinebändern in den Archivdatenbereich der Speicherebene „Kalt“ von Azure.
- **Inkrementelle Übertragung**: Wird verwendet, wenn eine erste Massenübertragung per Data Box-Datenträger (Seed) durchgeführt wird, gefolgt von inkrementellen Übertragungen über das Netzwerk. Beispielsweise werden Commvault und Data Box-Datenträger verwendet, um Sicherungskopien zurück in Azure zu verschieben. Auf diesen Migrationsvorgang folgt das Kopieren von inkrementellen Daten über das Netzwerk in den Azure-Speicher. 
- **Periodische Uploads**: Werden verwendet, wenn regelmäßig große Datenmengen generiert werden und in Azure verschoben werden müssen. Ein Beispiel hierfür ist die Exploration in der Energiebranche, bei der Videodaten auf Bohrinseln und für Windfarmen generiert werden.

## <a name="the-workflow"></a>Workflow

Der Workflow umfasst üblicherweise die folgenden Schritte:

1. **Auftrag**: Erstellen Sie im Azure-Portal einen Auftrag, und geben Sie die Versandinformationen und das Azure-Zielspeicherkonto für Ihre Daten an. Wenn Datenträger verfügbar sind, werden in Azure die Verschlüsselung, die Vorbereitung und der Versand der Datenträger mit einer ID für die Nachverfolgung durchgeführt.

2. **Empfang**: Nachdem Sie die Datenträger erhalten haben, können Sie sie auspacken und an den Computer anschließen, auf dem sich die zu kopierenden Daten befinden. Entsperren Sie die Datenträger.
    
3. **Datenkopiervorgang**: Kopieren Sie die Daten per Drag & Drop auf die Datenträger.

4. **Rücksendung**: Bereiten Sie die Datenträger vor, und senden Sie sie zurück an das Azure-Rechenzentrum.

5. **Upload**: Die Daten werden automatisch von den Datenträgern in den Speicher von Azure kopiert. Die Datenträger werden gemäß den NIST-Richtlinien (National Institute of Standards and Technology) auf sichere Weise gelöscht.

Bei diesem Prozess werden Sie per E-Mail über alle Statusänderungen informiert. Weitere Informationen zu den Details des Datenflusses finden Sie unter [Bereitstellen von Data Box-Datenträgern im Azure-Portal](data-box-disk-quickstart-portal.md).


## <a name="benefits"></a>Vorteile

Data Box-Datenträger sind so ausgelegt, dass große Datenmengen ohne negative Auswirkungen auf das Netzwerk in Azure verschoben werden können. Diese Lösung hat die folgenden Vorteile:

- **Geschwindigkeit**: Für den Data Box-Datenträger wird eine USB 3.0-Verbindung genutzt, um bis zu 35 TB an Daten in weniger als einer Woche in Azure zu verschieben.   

- **Benutzerfreundlichkeit**: Data Box ist eine benutzerfreundliche Lösung.

    - Für die Datenträger wird die USB-Konnektivität genutzt, bei der fast kein Einrichtungsaufwand anfällt.
    - Die Datenträger haben einen kleinen Formfaktor und sind daher leicht verwendbar.
    - Die Datenträger benötigen keine externe Stromversorgung.
    - Die Datenträger können auf einem Server des Rechenzentrums, einem Desktopcomputer oder einem Laptop verwendet werden.
    - Die Lösung ermöglicht die End-to-End-Nachverfolgung über das Azure-Portal.    

- **Schutz**: Data Box-Datenträger verfügen für die Datenträger, die Daten und den Dienst über einen integrierten Sicherheitsschutz. 
    - Die Datenträger sind manipulationssicher und unterstützen eine Funktion für sichere Updates. 
    - Die Daten auf den Datenträgern sind ununterbrochen durch eine AES 128-Bit-Verschlüsselung geschützt. 
    - Die Datenträger können nur mit einem Schlüssel entsperrt werden, der über das Azure-Portal bereitgestellt wird. 
    - Der Dienst ist durch Azure-Sicherheitsfunktionen geschützt. 
    - Nachdem Ihre Daten in Azure hochgeladen wurden, werden die Datenträger gemäß den NIST-Standards (800-88r1) vollständig bereinigt.  
    
Weitere Informationen finden Sie unter [Azure Data Box-Datenträger – Sicherheit und Schutz von Daten (Vorschauversion)](data-box-disk-security.md).


## <a name="features-and-specifications"></a>Funktionen und Spezifikationen


| Spezifikationen                                          | BESCHREIBUNG              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | Weniger als ca. 900 g pro Paket; Paket enthält maximal fünf Datenträger.                |
| Dimensionen                                              | Datenträger – SSD 2,5 Zoll |            
| Kabel                                                  | 1 USB-3.1-Kabel pro Datenträger|
| Speicherkapazität pro Auftrag                              | 40 TB (ca. 35 TB nutzbar)|
| Kapazität des Datenträgerspeichers                                   | 8 TB (ca. 7 TB nutzbar)|
| Datenschnittstelle                                          | USB   |
| Sicherheit                                                | Vorverschlüsselt per BitLocker und sichere Updatefunktion <br> Per Hauptschlüssel geschützte Datenträger <br> Ununterbrochene Verschlüsselung der Daten  |
| Datenübertragungsrate                                      | bis zu 430 MBit/s je nach Dateigröße      |
|Verwaltung                                               | Azure-Portal |


## <a name="region-availability"></a>Regionale Verfügbarkeit

Informationen zur regionalen Verfügbarkeit finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).


## <a name="pricing"></a>Preise

Informationen zu den Preisen finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Anforderungen für Data Box-Datenträger](data-box-disk-system-requirements.md).
- Machen Sie sich mit den [Einschränkungen für Data Box-Datenträger](data-box-disk-limits.md) vertraut.
- Informieren Sie sich über die schnelle Bereitstellung von [Azure Data Box-Datenträgern](data-box-disk-quickstart-portal.md) im Azure-Portal.
