---
title: Migrieren von Daten aus Amazon S3 zu Azure Storage
description: Es wird beschrieben, wie Sie Azure Data Factory zum Migrieren von Daten von Amazon S3 zu Azure Storage verwenden.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: be1cb7abbc243e3f79e183223fbbb32380f5d02d
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638039"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Verwenden von Azure Data Factory zum Migrieren von Daten von Amazon S3 zu Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Von Azure Data Factory wird ein performanter, stabiler und kostengünstiger Mechanismus bereitgestellt, um Daten bedarfsabhängig von Amazon S3 zu Azure Blob Storage oder Azure Data Lake Storage Gen2 zu migrieren.  Dieser Artikel enthält für Datentechniker und Entwickler Informationen zu den folgenden Bereichen: 

> [!div class="checklist"]
> * Leistung 
> * Resilienz beim Kopieren
> * Netzwerksicherheit
> * Allgemeine Lösungsarchitektur 
> * Bewährte Methoden für die Implementierung  

## <a name="performance"></a>Leistung

ADF verfügt über eine serverlose Architektur, die Parallelität auf unterschiedlichen Ebenen ermöglicht. Entwickler können dann Pipelines erstellen, um Ihre Netzwerkbandbreite vollständig zu nutzen – sowie IOPS und Bandbreite des Speichers, um den Durchsatz für die Datenverschiebung Ihrer Umgebung zu maximieren. 

Kunden haben erfolgreiche Migrationen von Daten im Petabyte-Bereich durchgeführt, bei denen mit einem dauerhaften Durchsatz von mindestens 2 GBit/s Hunderte Millionen Dateien aus Amazon S3 in Azure Blob Storage verschoben wurden. 

![Abbildung: Verschiedene Dateipartitionen in einem AWS S3-Speicher mit verknüpften Kopieraktionen an Azure Blob Storage ADLS Gen2.](media/data-migration-guidance-s3-to-azure-storage/performance.png)

In der obigen Abbildung ist dargestellt, wie Sie für unterschiedliche Parallelitätsebenen hohe Geschwindigkeiten bei der Datenverschiebung erzielen:
 
- Für eine Kopieraktivität können skalierbare Computeressourcen genutzt werden: Bei Verwendung der Azure Integration Runtime können Sie [bis zu 256 Datenintegrationseinheiten (DIUs)](./copy-activity-performance.md#data-integration-units) serverlos für jede Kopieraktivität angeben. Wenn Sie die selbstgehostete Integration Runtime nutzen, können Sie den Computer manuell hochskalieren oder das Aufskalieren auf mehrere Computer durchführen ([bis zu vier Knoten](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)). Bei einer einzelnen Kopieraktivität wird die Dateigruppe dann auf alle Knoten verteilt. 
- Für eine Kopieraktivität werden mehrere Threads genutzt, um für den Datenspeicher Lese- und Schreibvorgänge durchzuführen. 
- Mit der ADF-Ablaufsteuerung können mehrere Kopieraktivitäten parallel gestartet werden, z. B. per [foreach-Schleife](./control-flow-for-each-activity.md). 

## <a name="resilience"></a>Resilienz

Bei Ausführung einer einzelnen Kopieraktivität verfügt ADF über einen integrierten Wiederholungsmechanismus, damit in den Datenspeichern oder im zugrunde liegenden Netzwerk eine bestimmte Ebene vorübergehender Fehler verarbeitet werden kann. 

Beim Durchführen von binären Kopiervorgängen von S3 zu Blob Storage und von S3 zu ADLS Gen2 werden von ADF automatisch Prüfpunkte gesetzt.  Wenn bei einer Ausführung der Kopieraktivität ein Fehler oder Timeout aufgetreten ist, wird die Kopie bei einer nachfolgenden Wiederholung ab dem letzten Fehlerpunkt fortgesetzt und nicht wieder vom Anfang gestartet. 

## <a name="network-security"></a>Netzwerksicherheit 

Standardmäßig werden Daten mit ADF von Amazon S3 in Azure Blob Storage oder Azure Data Lake Storage Gen2 übertragen, indem eine verschlüsselte Verbindung per HTTPS-Protokoll genutzt wird.  HTTPS ermöglicht die Datenverschlüsselung während der Übertragung und verhindert Abhör- und Man-in-the-Middle-Angriffe. 

Falls Sie nicht möchten, dass Daten über das öffentliche Internet übertragen werden, können Sie auch eine bessere Sicherheit erzielen, indem Sie Daten über einen privaten Peeringlink zwischen AWS Direct Connect und Azure ExpressRoute übertragen.  Unten im Abschnitt zur Lösungsarchitektur ist beschrieben, wie Sie dies erreichen. 

## <a name="solution-architecture"></a>Lösungsarchitektur

Migrieren von Daten über das öffentliche Internet:

![Abbildung: Migration über das Internet mithilfe von HTTP von einem AWS S3-Speicher über Azure Integration Runtime in ADF Azure zu Azure Storage Die Runtime verfügt über einen Steuerungskanal mit Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- Bei dieser Architektur werden Daten auf sichere Weise per HTTPS über das öffentliche Internet übertragen. 
- Sowohl die Amazon S3-Quelle als auch das Azure Blob Storage- bzw. Azure Data Lake Storage Gen2-Ziel sind so konfiguriert, dass Datenverkehr von allen IP-Netzwerkadressen zulässig ist.  Unten unter der zweiten Architektur ist beschrieben, wie Sie den Netzwerkzugriff auf einen bestimmten IP-Bereich beschränken können. 
- Sie können die Leistung leicht serverlos hochskalieren, um Ihre Netzwerk- und Speicherbandbreite vollständig zu nutzen, damit Sie den besten Durchsatz für Ihre Umgebung erzielen. 
- Mit dieser Architektur ist sowohl die Migration der Anfangsmomentaufnahme als auch der Deltadaten möglich. 

Migrieren von Daten über einen privaten Link: 

![Abbildung: Migration über eine private Peeringverbindung von einem AWS S3-Speicher und über eine selbstgehostete Integration Runtime auf virtuellen Azure-Computern zu V Net-Dienstendpunkten zu Azure Storage. Die Runtime verfügt über einen Steuerungskanal mit Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- Bei dieser Architektur wird die Datenmigration über einen privaten Peeringlink zwischen AWS Direct Connect und Azure ExpressRoute durchgeführt, damit Daten niemals über das öffentliche Internet übertragen werden.  Hierfür ist die Verwendung von AWS VPC und eines virtuellen Azure-Netzwerks erforderlich. 
- Sie müssen die selbstgehostete Integration Runtime von ADF auf einer Windows-VM in Ihrem virtuellen Azure-Netzwerk installieren, um diese Architektur zu erhalten.  Sie können Ihre VMs mit selbstgehosteter IR manuell hochskalieren oder auf mehrere VMs aufskalieren (bis zu vier Knoten), um IOPS und Bandbreite für Netzwerk und Speicher vollständig zu nutzen. 
- Wenn die Übertragung von Daten per HTTPS akzeptabel ist, Sie den Netzwerkzugriff auf die S3-Quelle aber auf einen bestimmten IP-Bereich begrenzen möchten, können Sie eine Variante dieser Architektur einrichten, indem Sie AWS VPC entfernen und den privaten Link durch HTTPS ersetzen.  Es ist ratsam, das virtuelle Azure-Netzwerk und die selbstgehostete IR auf der Azure-VM beizubehalten, damit Sie eine IP-Adresse für Filterzwecke verwenden können, die öffentlich geroutet werden kann. 
- Mit dieser Architektur ist sowohl die Migration der Daten einer Anfangsmomentaufnahme als auch der Deltadaten möglich. 

## <a name="implementation-best-practices"></a>Bewährte Methoden für die Implementierung 

### <a name="authentication-and-credential-management"></a>Authentifizierung und Verwaltung von Anmeldeinformationen 

- Zum Durchführen der Authentifizierung für das Amazon S3-Konto müssen Sie den [Zugriffsschlüssel für das IAM-Konto](./connector-amazon-simple-storage-service.md#linked-service-properties) verwenden. 
- Für die Verbindungsherstellung mit Azure Blob Storage werden mehrere Authentifizierungstypen unterstützt.  Die Verwendung von [verwalteten Identitäten für Azure-Ressourcen](./connector-azure-blob-storage.md#managed-identity) wird dringend empfohlen: Sie basieren auf einer automatisch verwalteten ADF-Identität in Azure AD und ermöglichen Ihnen die Konfiguration von Pipelines, ohne dass in der Definition des verknüpften Diensts Anmeldeinformationen angegeben werden müssen.  Alternativ können Sie die Authentifizierung für Azure Blob Storage auch per [Dienstprinzipal](./connector-azure-blob-storage.md#service-principal-authentication), [Shared Access Signature](./connector-azure-blob-storage.md#shared-access-signature-authentication) oder [Speicherkontoschlüssel](./connector-azure-blob-storage.md#account-key-authentication) durchführen. 
- Auch für die Verbindungsherstellung mit Azure Data Lake Storage Gen2 werden mehrere Authentifizierungstypen unterstützt.  Die Verwendung von [verwalteten Identitäten für Azure-Ressourcen](./connector-azure-data-lake-storage.md#managed-identity) wird dringend empfohlen. Es können aber auch ein [Dienstprinzipal](./connector-azure-data-lake-storage.md#service-principal-authentication) oder ein [Speicherkontoschlüssel](./connector-azure-data-lake-storage.md#account-key-authentication) verwendet werden. 
- Wenn Sie keine verwalteten Identitäten für Azure-Ressourcen nutzen, ist das [Speichern der Anmeldeinformationen in Azure Key Vault](./store-credentials-in-key-vault.md) dringend zu empfehlen, um das zentrale Verwalten und Rotieren von Schlüsseln ohne Änderung der verknüpften ADF-Dienste zu vereinfachen.  Dies ist auch eine der [bewährten Methoden für CI/CD](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migration der Daten der Anfangsmomentaufnahme 

Die Datenpartition ist besonders zu empfehlen, wenn mehr als 100 TB Daten migriert werden.  Nutzen Sie zum Partitionieren der Daten die Einstellung „Präfix“, um die Ordner und Dateien in Amazon S3 nach Name zu filtern. So kann dann mit jedem ADF-Kopierauftrag jeweils eine Partition kopiert werden.  Sie können mehrere ADF-Kopieraufträge gleichzeitig ausführen, um einen besseren Durchsatz zu erzielen. 

Falls Kopieraufträge aufgrund eines vorübergehenden Problems mit dem Netzwerk oder dem Datenspeicher nicht erfolgreich sind, können Sie den fehlerhaften Kopierauftrag erneut ausführen, um diese spezifische Partition erneut aus AWS S3 zu laden.  Alle anderen Kopieraufträge, bei denen andere Partitionen geladen werden, werden nicht beeinträchtigt. 

### <a name="delta-data-migration"></a>Migration von Deltadaten 

Die beste Möglichkeit zum Identifizieren von neuen oder geänderten Dateien aus AWS S3 ist die Verwendung der Namenskonvention mit Zeitpartitionierung. Wenn für Ihre Daten in AWS S3 die Zeitpartitionierung mit Zeitrauminformationen im Datei- oder Ordnernamen durchgeführt wurde (z. B. „/jjjj/mm/tt/file.csv), kann Ihre Pipeline leicht ermitteln, welche Dateien bzw. Ordner inkrementell kopiert werden müssen. 

Wenn Ihre Daten in AWS S3 nicht über eine Zeitpartitionierung verfügen, kann ADF neue oder geänderte Dateien anhand des letzten Änderungsdatums (LastModifiedDate) identifizieren.   Hierbei scannt ADF alle Dateien aus AWS S3 und kopiert nur die neuen und aktualisierten Dateien, deren Zeitstempel der letzten Änderung größer als ein bestimmter Wert ist.  Beachten Sie Folgendes, wenn Sie in S3 über eine große Zahl von Dateien verfügen: Der erste Scanvorgang für die Dateien kann unabhängig davon, wie viele Dateien die Filterbedingung erfüllen, sehr lange dauern.  In diesem Fall sollten Sie die Daten zuerst partitionieren, indem Sie die gleiche „Präfix“-Einstellung für die Migration der Anfangsmomentaufnahme verwenden, damit die Dateiuntersuchung parallel erfolgen kann.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Szenarien mit selbstgehosteter Integration Runtime auf einer Azure-VM 

Unabhängig davon, ob Sie Daten über einen privaten Link migrieren oder in der Amazon S3-Firewall einen bestimmten IP-Adressbereich angeben möchten, müssen Sie die selbstgehostete Integration Runtime auf einer Azure Windows-VM installieren. 

- Die empfohlene Konfiguration, mit der für jeden virtuellen Azure-Computer begonnen werden sollte, ist „Standard_D32s_v3“ mit 32 vCPUs und 128 GB Arbeitsspeicher.  Sie können die Auslastung der CPU und des Arbeitsspeichers für die IR-VM während der Datenmigration weiter überwachen. So können Sie ermitteln, ob Sie die VM weiter hochskalieren müssen, um die Leistung zu verbessern, oder herunter, um Kosten zu sparen. 
- Sie können auch aufskalieren, indem Sie bis zu vier VM-Knoten einer selbstgehosteten Integration Runtime zuordnen.  Bei einem einzelnen Kopierauftrag, der für eine selbstgehostete IR ausgeführt wird, wird die Dateigruppe automatisch partitioniert, und alle VM-Knoten werden genutzt, um die Dateien parallel zu kopieren.  Zur Sicherstellung von Hochverfügbarkeit ist es ratsam, mit zwei VM-Knoten zu beginnen, um bei der Datenmigration einen Single Point of Failure zu vermeiden. 

### <a name="rate-limiting"></a>Ratenbegrenzung 

Die bewährte Methode besteht darin, mit einem repräsentativen Beispieldataset einen Proof of Concept-Vorgang in Bezug auf die Leistung durchzuführen, damit Sie eine geeignete Partitionsgröße ermitteln können. 

Beginnen Sie mit einer einzelnen Partition und einer einzelnen Kopieraktivität mit der DIU-Standardeinstellung.  Erhöhen Sie die DIU-Einstellung nach und nach, bis Sie das Bandbreitenlimit Ihres Netzwerks oder das IOPS-/Bandbreitenlimit der Datenspeicher erreichen bzw. bis die maximal zulässigen 256 DIUs pro Kopieraktivität erreicht sind. 

Erhöhen Sie anschließend schrittweise die Anzahl gleichzeitiger Kopieraktivitäten, bis Sie die Grenzwerte Ihrer Umgebung erreicht haben. 

Wenn für die ADF-Kopieraktivität Drosselungsfehler gemeldet werden, sollten Sie entweder die Parallelitäts- oder die DIU-Einstellung in ADF reduzieren oder erwägen, die IOPS-/Bandbreitenlimits für das Netzwerk und die Datenspeicher zu erhöhen.  

### <a name="estimating-price"></a>Schätzen des Preises 

> [!NOTE]
> Dies ist ein hypothetisches Preisbeispiel.  Der tatsächliche Preis richtet sich nach dem tatsächlichen Durchsatz in Ihrer Umgebung.

Sehen Sie sich die folgende Pipeline für die Migration von Daten aus S3 zu Azure Blob Storage an: 

![Abbildung: Pipeline für die Migration von Daten, bei der für jede Partition ein manueller Auslöser an Lookup übertragen, das an ForEach übertragen, das an eine Subpipeline übertragen wird, eine Kopieraktivität enthält, die an eine gespeicherte Prozedur übertragen wird. Außerhalb der Pipeline werden gespeicherte Prozeduren an Azure SQL DB übertragen, das an Lookup übertragen wird, und AWS S3 wird an die Kopieraktivität übertragen, die an Blob Storage übertragen wird.](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Wir nehmen Folgendes an: 

- Das gesamte Datenvolumen beträgt 2 PB. 
- Daten werden mit der ersten Lösungsarchitektur per HTTPS migriert. 
- Die 2 PB sind in Partitionen von 1 K unterteilt, und bei jedem Kopiervorgang wird eine Partition verschoben. 
- Für jeden Kopiervorgang ist „DIU = 256“ konfiguriert, und es wird ein Durchsatz von 1 GBit/s erzielt. 
- Die Foreach-Parallelität ist auf den Wert 2 festgelegt, und der Aggregatdurchsatz beträgt 2 GBit/s. 
- Insgesamt dauert es 292 Stunden, bis die Migration abgeschlossen ist. 

Hier ist der geschätzte Preis basierend auf den obigen Annahmen angegeben: 

![Screenshot: Tabelle mit geschätztem Preis](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Zusätzliche Verweise 
- [Amazon Simple Storage Service-Connector](./connector-amazon-simple-storage-service.md)
- [Azure Blob Storage-Connector](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2-Connector](./connector-azure-data-lake-storage.md)
- [Handbuch zur Leistung und Optimierung der Kopieraktivität](./copy-activity-performance.md)
- [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](./create-self-hosted-integration-runtime.md)
- [Selbstgehostete Integration Runtime: Hochverfügbarkeit und Skalierbarkeit](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Sicherheitsüberlegungen für Datenverschiebung in Azure Data Factory](./data-movement-security-considerations.md)
- [Speichern von Anmeldeinformationen in Azure Key Vault](./store-credentials-in-key-vault.md)
- [Inkrementelles Kopieren neuer Dateien basierend auf dem zeitpartitionierten Dateinamen und mithilfe des Tools „Daten kopieren“](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Inkrementelles Kopieren neuer und geänderter Dateien auf Basis von LastModifiedDate und mithilfe des Tools zum Kopieren von Daten](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Azure Data Factory: Datenpipelines – Preise](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Vorlage

Mit dieser [Vorlagen](solution-template-migration-s3-azure.md) können Sie Petabytes an Daten mit mehreren hundert Millionen Dateien von Amazon S3 zu Azure Data Lake Storage Gen2 migrieren.

## <a name="next-steps"></a>Nächste Schritte

- [Kopieren von Dateien aus mehreren Containern mit Azure Data Factory](solution-template-copy-files-multiple-containers.md)