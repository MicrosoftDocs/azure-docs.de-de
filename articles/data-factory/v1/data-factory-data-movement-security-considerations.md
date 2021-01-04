---
title: Sicherheitsüberlegungen für Datenverschiebung in Azure Data Factory
description: Sie erhalten Informationen über das Schützen von Datenverschiebung in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: c694cf58f4c6b613cbc183753785a34bc15063bd
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093603"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory – Sicherheitsüberlegungen für Datenverschiebung

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Sicherheitsüberlegungen zum Verschieben von Daten mit Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Einführung
In diesem Artikel ist die grundlegenden Sicherheitsinfrastruktur beschrieben, die von Datenverschiebungsdiensten in Azure Data Factory verwendet wird, um Ihre Daten zu schützen. Azure Data Factory-Verwaltungsressourcen setzen auf der Azure Sicherheitsinfrastruktur auf und nutzen alle möglichen Sicherheitsmaßnahmen, die von Azure bereitgestellt werden.

In einer Data Factory-Lösung erstellen Sie mindestens eine [Pipeline](data-factory-create-pipelines.md). Bei einer Pipeline handelt es sich um eine logische Gruppierung von Aktivitäten, die zusammen eine Aufgabe bilden. Diese Pipelines befinden sich in der Region, in der die Data Factory erstellt wurde. 

Obwohl Data Factory nur in den Regionen **USA, Westen**, **USA, Osten** und **Europa, Norden** verfügbar ist, ist der Datenverschiebungsdienst [global in verschiedenen Regionen](data-factory-data-movement-activities.md#global) verfügbar. Der Data Factory-Dienst stellt sicher, dass Daten einen geografischen Bereich/eine geografische Region nicht verlassen, es sei denn, Sie weisen den Dienst ausdrücklich an, eine alternative Region zu verwenden, wenn der Datenverschiebungsdienst noch nicht in dieser Region bereitgestellt wurde. 

Azure Data Factory selbst speichert keine Daten mit Ausnahme für verknüpfte Dienstanmeldeinformationen für Clouddatenspeicher, die mit Zertifikaten verschlüsselt sind. Der Dienst ermöglicht das Erstellen von datengesteuerten Workflows, um die Verschiebung von Daten zwischen [unterstützten Datenspeichern](data-factory-data-movement-activities.md#supported-data-stores-and-formats) und die Verarbeitung von Daten mithilfe von [Compute Services](data-factory-compute-linked-services.md) in anderen Regionen oder in einer lokalen Umgebung zu orchestrieren. Außerdem können Sie mit programmgesteuerten und UI-basierten Mechanismen [Workflows überwachen und verwalten](data-factory-monitor-manage-pipelines.md) .

Datenverschiebung mit Azure Data Factory ist **zertifiziert** für:
-   [HIPAA/HITECH](/compliance/regulatory/offering-hipaa-hitech)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Informationen zur Compliance von Azure und zur eigenständigen Sicherung der Azure-Infrastruktur finden Sie im [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

In diesem Artikel werden Sicherheitsüberlegungen zu den beiden folgenden Datenverschiebungsszenarien erläutert: 

- **Cloudszenario** In diesem Szenario sind sowohl die Quelle als auch das Ziel über das Internet öffentlich zugänglich. Dazu gehören verwaltete Cloudspeicherdienste wie Azure Storage, Azure Synapse Analytics, Azure SQL-Datenbank, Azure Data Lake Store, Amazon S3, Amazon Redshift, SaaS-Dienste wie Salesforce und Webprotokolle wie FTP und OData. Eine vollständige Liste der unterstützten Datenquellen finden Sie [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Hybridszenario** In diesem Szenario befindet sich entweder die Quelle oder das Ziel hinter einer Firewall oder in einem lokalen Unternehmensnetzwerk, oder der Datenspeicher befindet sich in einem privaten Netzwerk/virtuellen Netzwerk (meist die Quelle) und ist nicht öffentlich zugänglich. Zu diesem Szenario zählen auch Datenbankserver, die auf virtuellen Computern gehostet werden.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cloudszenarien
### <a name="securing-data-store-credentials"></a>Schützen von Datenspeicher-Anmeldeinformationen
Azure Data Factory schützt Ihre Datenspeicher-Anmeldeinformationen dadurch, dass sie **verschlüsselt** werden, wozu **von Microsoft verwaltete Zertifikate** verwendet werden. Diese Zertifikate werden alle **zwei Jahre** ausgetauscht (wozu Erneuerung des Zertifikats und Migration von Anmeldeinformationen gehören). Diese verschlüsselten Anmeldeinformationen werden sicher in einem **von Azure Data Factory-Verwaltungsdienste verwalteten Azure Storage** gespeichert. Weitere Informationen zur Azure Storage-Sicherheit finden Sie unter [Übersicht über die Sicherheit von Azure Storage](../../storage/blobs/security-recommendations.md).

### <a name="data-encryption-in-transit"></a>Datenverschlüsselung während der Übertragung
Wenn der Clouddatenspeicher HTTPS oder TLS unterstützt, erfolgen alle Datenübertragungen zwischen Datenverschiebungsdiensten in Data Factory und einem Clouddatenspeicher über einen sicheren Kanal (HTTPS oder TLS).

> [!NOTE]
> Für alle Verbindungen mit **Azure SQL-Datenbank** und **Azure Synapse Analytics** ist eine Verschlüsselung (SSL/TLS) erforderlich, solange Daten in die und aus der Datenbank übertragen werden. Wenn Sie eine Pipeline mit einem JSON-Editor erstellen, fügen Sie die **encryption**-Eigenschaft zur Verbindungszeichenfolge (**connection string**) hinzu, und legen Sie die Eigenschaft auf **true** fest. Wenn Sie den [Kopier-Assistenten](data-factory-azure-copy-wizard.md) verwenden, wird diese Eigenschaft von dem Assistenten standardmäßig festgelegt. Für **Azure Storage** können Sie **HTTPS** in der Verbindungszeichenfolge verwenden.

### <a name="data-encryption-at-rest"></a>Datenverschlüsselung ruhender Daten
Einige Datenspeicher unterstützen die Verschlüsselung von ruhenden Daten. Es empfiehlt sich, dass Sie einen Datenverschlüsselungsmechanismus für solche Datenspeicher aktivieren. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
Transparent Data Encryption (TDE) in Azure Synapse Analytics bietet Schutz vor der Bedrohung durch schädliche Aktivitäten, indem die ruhenden Daten in Echtzeit ver- und entschlüsselt werden. Dieses Verhalten ist für den Client transparent. Weitere Informationen finden Sie unter [Absichern einer Datenbank in Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL-Datenbank
Azure SQL-Datenbank unterstützt auch Transparent Data Encryption (TDE), die Schutz vor der Bedrohung durch schädliche Aktivitäten bietet. Hierzu werden die Daten in Echtzeit ver- und entschlüsselt, ohne dass Änderungen der Anwendung erforderlich sind. Dieses Verhalten ist für den Client transparent. Weitere Informationen finden Sie unter [Transparent Data Encryption mit Azure SQL-Datenbank](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store bietet auch eine Verschlüsselung für Daten, die im Konto gespeichert sind. Wenn diese Option aktiviert ist, verschlüsselt Data Lake Store Daten automatisch vor der dauerhaften Speicherung und entschlüsselt Daten vor dem Abrufen, sodass der Vorgang für den Client, der auf die Daten zugreift, transparent ist. Weitere Informationen finden Sie unter [Sicherheit in Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage und Azure Table Storage
Azure Blob Storage und Azure Table Storage unterstützen die Speicherdienstverschlüsselung (Storage Service Encryption, SSE), bei der Ihre Daten vor der Weitergabe an den Speicher automatisch verschlüsselt und vor dem Abrufen entschlüsselt werden. Weitere Informationen finden Sie unter [Azure Storage Service Encryption für ruhende Daten](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 unterstützt die Client- und Serververschlüsselung von ruhenden Daten. Weitere Informationen finden Sie unter [Schutz von Daten mittels Verschlüsselung](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Derzeit bietet Data Factory keine Unterstützung für Amazon S3 in einer Virtual Private Cloud (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift unterstützt die Clusterverschlüsselung für ruhende Daten. Weitere Informationen finden Sie unter [Amazon Redshift-Datenbankverschlüsselung](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Derzeit bietet Data Factory keine Unterstützung für Amazon Redshift in einer VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce unterstützt Shield Platform Encryption, die eine Verschlüsselung aller Dateien, Anlagen und benutzerdefinierten Felder ermöglicht. Weitere Informationen finden Sie unter [Grundlegendes zum OAuth-Webserver-Authentifizierungsfluss](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hybridszenarios (mit Datenverwaltungsgateway)
Hybridszenarios erfordern, dass Datenverwaltungsgateway in einem lokalen Netzwerk oder einem virtuellen Netzwerk (Azure) oder einer virtuellen privaten Cloud (Amazon) installiert wird. Das Gateway muss auf die lokalen Datenspeicher zugreifen können. Weitere Informationen zum Gateway finden Sie unter [Datenverwaltungsgateway](data-factory-data-management-gateway.md). 

![Kanäle des Datenverwaltungsgateways](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

Der **Befehlskanal** ermöglicht Kommunikation zwischen Datenverschiebungsdiensten in Data Factory und Datenverwaltungsgateway. Die Kommunikation enthält die Informationen, die sich auf die Aktivität beziehen. Der Datenkanal wird dazu verwendet, Daten zwischen lokalen Datenspeichern und Clouddatenspeichern zu übertragen.    

### <a name="on-premises-data-store-credentials"></a>Anmeldeinformationen für lokale Datenspeicher
Die Anmeldeinformationen für Ihre lokalen Datenspeicher sind lokal gespeichert (nicht in der Cloud). Sie können auf drei verschiedene Arten festgelegt werden. 

- Verwenden von **Nur-Text** (weniger sicher) über HTTPS aus dem Azure-Portal/Kopier-Assistenten. Die Anmeldeinformationen werden im Nur-Text-Format an das lokale Gateway übergeben.
- Verwenden der **JavaScript-Kryptografiebibliothek aus dem Kopier-Assistenten**.
- Verwenden der **ClickOnce-basierten Anwendung „Anmeldeinformationsverwaltung“** . Die ClickOnce-Anwendung wird auf dem lokalen Computer ausgeführt, der Zugriff auf das Gateway hat, und legt die Anmeldeinformationen für den Datenspeicher fest. Diese Option und die nächste sind die sichersten Optionen. Die Anwendung „Anmeldeinformationsverwaltung“ verwendet standardmäßig den Port 8050 auf dem Computer mit dem Gateway für sichere Kommunikation.  
- Verwenden Sie das PowerShell-Cmdlet [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue), um Anmeldeinformationen zu verschlüsseln. Das Cmdlet verwendet das Zertifikat, für dessen Verwendung dieses Gateway konfiguriert ist, um die Anmeldeinformationen zu verschlüsseln. Sie können die verschlüsselten Anmeldeinformationen, die von diesem Cmdlet zurückgegeben wurden, dem **EncryptedCredential**-Element des **connectionString**-Werts in der JSON-Datei, die Sie mit dem [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice)-Cmdlet verwenden, oder im JSON-Codeausschnitt im Data Factory-Editor im Portal hinzufügen. Diese Option und die ClickOnce-Anwendung sind die sichersten Optionen. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Verschlüsselung über die JavaScript-Kryptografiebibliothek
Sie können Datenspeicher-Anmeldeinformationen über die [JavaScript-Kryptografiebibliothek](https://www.microsoft.com/download/details.aspx?id=52439) aus dem [Kopier-Assistenten](data-factory-copy-wizard.md) verschlüsseln. Wenn Sie diese Option auswählen, ruft der Kopier-Assistent den öffentlichen Schlüssel des Gateways ab und verwendet diesen Schlüssel, um die Datenspeicher-Anmeldeinformationen zu verschlüsseln. Die Anmeldeinformationen werden vom Gatewaycomputer entschlüsselt und durch Windows-[DPAPI](/previous-versions/ms995355(v=msdn.10)) geschützt.

**Unterstützte Browser:** IE8, IE9, IE10, IE11, Microsoft Edge und der aktuelle Firefox-, Chrome-, Opera- und Safari-Browser. 

#### <a name="click-once-credentials-manager-app"></a>ClickOnce-Anwendung „Anmeldeinformationsverwaltung“
Sie können die ClickOnce-basierte Anwendung „Anmeldeinformationsverwaltung“ aus dem Azure-Portal/Kopier-Assistenten starten, wenn Sie Pipelines erstellen. Diese Anwendung stellt sicher, dass Anmeldeinformationen nicht im Nur-Text-Format übertragen werden. Standardmäßig verwendet die Anwendung den Port **8050** auf dem Computer mit dem Gateway für sichere Kommunikation. Dieser Port kann bei Bedarf geändert werden.  
  
![HTTPS-Port für das Gateway](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Derzeit verwendet das Datenverwaltungsgateway ein einzelnes **Zertifikat**. Dieses Zertifikat wird während der Gatewayinstallation erstellt (gilt für Datenverwaltungsgateway, das nach November 2016 erstellt wurde, und Version 2.4.xxxx.x oder höher). Sie können dieses Zertifikat durch Ihr eigenes SSL/TLS-Zertifikat ersetzen. Dieses Zertifikat wird von der Anwendung „Anmeldeinformationsverwaltung“ verwendet, um sicher eine Verbindung mit dem Gatewaycomputer zum Festlegen von Datenspeicher-Anmeldeinformationen herzustellen. Die Anwendung speichert die Datenspeicher-Anmeldeinformationen sicher lokal, indem sie die Windows-[DPAPI](/previous-versions/ms995355(v=msdn.10)) auf dem Computer mit dem Gateway verwendet. 

> [!NOTE]
> Ältere Gateways, die vor November 2016 installiert wurden oder die Version 2.3.xxxx.x haben, verwenden weiterhin Anmeldeinformationen, die in der Cloud verschlüsselt und gespeichert sind. Selbst wenn Sie das Gateway auf die neueste Version aktualisieren, werden die Anmeldeinformationen nicht auf einen lokalen Computer migriert.    
  
| Gatewayversion (während der Erstellung) | Gespeicherte Anmeldeinformationen | Verschlüsselung/Sicherheit von Anmeldeinformationen | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | In der Cloud | Verschlüsselt mit Zertifikat (ein anderes als das, das von der Anwendung „Anmeldeinformationsverwaltung“ verwendet wird) | 
| > = 2.4.xxxx.x | Lokal | Geschützt über DPAPI | 
  

### <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung
Alle Datenübertragungen erfolgen über den sicheren Kanal **HTTPS** und **TLS über TCP**, um Man-in-the-Middle-Angriffe während der Kommunikation mit Azure-Diensten zu verhindern.
 
Sie können auch [IPSec-VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) oder [ExpressRoute](../../expressroute/expressroute-introduction.md) verwenden, um den Kommunikationskanal zwischen Ihrem lokalen Netzwerk und Azure zusätzlich zu schützen.

Das virtuelle Netzwerk ist eine logische Darstellung Ihres Netzwerks in der Cloud. Sie können ein lokales Netzwerk mit Ihrem virtuellen Azure-Netzwerk (VNet) verbinden, indem Sie IPSec-VPN (Standort-zu-Standort) oder ExpressRoute (privates Peering) einrichten.     

In der folgenden Tabelle sind die Empfehlungen für die Netzwerk- und Gatewaykonfiguration zusammengefasst, die sich aus verschiedenen Kombinationen von Quell- und Zielstandort für hybride Datenverschiebung ergeben.

| `Source` | Destination | Netzwerkkonfiguration | Gatewaysetup |
| ------ | ----------- | --------------------- | ------------- | 
| Lokal | Virtuelle Computer und Clouddienste, die in virtuellen Netzwerken bereitgestellt werden | IPSec-VPN (Punkt-zu-Standort oder Standort-zu-Standort) | Gateway kann entweder lokal oder auf einem virtuellen Azure-Computer in VNet installiert sein | 
| Lokal | Virtuelle Computer und Clouddienste, die in virtuellen Netzwerken bereitgestellt werden | ExpressRoute (privates Peering) | Gateway kann entweder lokal oder auf einem virtuellen Azure-Computer installiert sein | 
| Lokal | Azure-basierte Dienste, die einen öffentlichen Endpunkt haben | ExpressRoute (öffentliches Peering) | Gateway muss lokal installiert sein | 

Die folgenden Abbildungen veranschaulichen die Verwendung von Datenverwaltungsgateway zum Verschieben von Daten zwischen einer lokalen Datenbank und Azure-Diensten über ExpressRoute und IPSec-VPN (mit virtuellem Netzwerk):

**ExpressRoute:**
 
![ExpressRoute mit Gateway verwenden](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec-VPN:**

![IPSec-VPN mit Gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-filtering-ip-address-of-gateway"></a>Firewallkonfigurationen und Filtern der IP-Adresse des Gateways

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Firewallanforderungen für lokales/privates Netzwerk  
In einem Unternehmen wird eine **Unternehmensfirewall** auf dem zentralen Router der Organisation ausgeführt. Außerdem wird die **Windows-Firewall** als Daemon auf dem lokalen Computer ausgeführt, auf dem das Gateway installiert ist. 

Die folgende Tabelle enthält die Anforderungen für **ausgehende Ports** und die Domänenanforderungen für die **Unternehmensfirewall**.

| Domänennamen | Ausgehende Ports | BESCHREIBUNG |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Erforderlich für das Gateway, um Verbindungen mit Datenverschiebungsdiensten in Data Factory herzustellen. |
| `*.core.windows.net` | 443 | Wird vom Gateway verwendet, um Verbindungen mit dem Azure Storage-Konto herzustellen, wenn Sie das Feature [gestaffeltes Kopieren](data-factory-copy-activity-performance.md#staged-copy) verwenden. | 
| `*.frontend.clouddatahub.net` | 443 | Erforderlich für das Gateway, um Verbindungen mit dem Azure Data Factory-Dienst herzustellen. | 
| `*.database.windows.net` | 1433   | (OPTIONAL) Erforderlich, wenn Ihr Ziel Azure SQL-Datenbank/Azure Synapse Analytics ist. Verwenden Sie das Feature „gestaffeltes Kopieren“ um Daten nach Azure SQL-Datenbank/Azure Synapse Analytics zu kopieren, ohne Port 1433 zu öffnen. | 
| `*.azuredatalakestore.net` | 443 | (OPTIONAL) Erforderlich, wenn Ihr Ziel Azure Data Lake Store ist. | 

> [!NOTE] 
> Sie müssen Ports bzw. Filterdomänen je nach Vorgabe der jeweiligen Datenquelle möglicherweise auf Ebene der Unternehmensfirewall verwalten. In dieser Tabelle werden nur Azure SQL-Datenbank, Azure Synapse Analytics und Azure Data Lake Store als Beispiele verwendet.   

Die folgende Tabelle enthält die Anforderungen für **eingehende Ports** für die **Windows-Firewall**.

| Eingehende Ports | BESCHREIBUNG | 
| ------------- | ----------- | 
| 8050 (TCP) | Ist für die Anwendung „Anmeldeinformationsverwaltung“ erforderlich, um Anmeldeinformationen für lokale Datenspeicher sicher auf dem Gateway festzulegen. | 

![Gatewayportanforderungen](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurationsfiltering-in-data-store"></a>Konfiguration und Filtern der IP-Adresse im Datenspeicher
Einige Datenspeicher in der Cloud erfordern auch die Genehmigung der IP-Adresse des Computers, über den auf sie zugegriffen wird. Stellen Sie sicher, dass die IP-Adresse des Gatewaycomputers ordnungsgemäß in der Firewall genehmigt bzw. konfiguriert ist.

Die folgenden Clouddatenspeicher erfordern die Genehmigung der IP-Adresse des Gatewaycomputers. Einige dieser Datenspeicher erfordern möglicherweise standardmäßig keine Genehmigung der IP-Adresse. 

- [Azure SQL-Datenbank](../../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/how-to-configure-firewall.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Frage:** Kann das Gateway für unterschiedliche Data Factorys gemeinsam genutzt werden?
**Antwort:** Dieses Feature wird noch nicht unterstützt. Wir arbeiten mit Hochdruck daran.

**Frage:** Welche Portanforderungen müssen erfüllt sein, damit das Gateway funktioniert?
**Antwort:** Das Gateway stellt ausgehende HTTP-basierte Verbindungen mit dem offenen Internet her. Die **ausgehenden Ports 443 und 80** müssen geöffnet sein, damit das Gateway diese Verbindung herstellen kann. Öffnen Sie den **eingehenden Port 8050** nur auf Computerebene (nicht auf Ebene der Unternehmensfirewall) für die Anwendung „Anmeldeinformationsverwaltung“. Wenn Azure SQL-Datenbank oder Azure Synapse Analytics als Quelle/Ziel verwendet wird, müssen Sie auch Port **1433** öffnen. Weitere Informationen finden Sie im Abschnitt [Firewallkonfigurationen und Filtern von IP-Adressen](#firewall-configurations-and-filtering-ip-address-of gateway). 

**Frage:** Welche Zertifikatanforderungen gelten für das Gateway?
**Antwort:** Das aktuelle Gateway benötigt ein Zertifikat, das von der Anwendung „Anmeldeinformationsverwaltung“ verwendet wird, um Datenspeicher-Anmeldeinformationen sicher festzulegen. Dieses Zertifikat ist ein selbst signiertes Zertifikat, das beim Gatewaysetup erstellt und konfiguriert wird. Sie können stattdessen Ihr eigenes TLS/SSL-Zertifikat verwenden. Weitere Informationen finden Sie im Abschnitt [ClickOnce-Anwendung „Anmeldeinformationsverwaltung“](#click-once-credentials-manager-app). 

## <a name="next-steps"></a>Nächste Schritte
Informationen zur Leistung der Kopieraktivität finden Sie im [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md).