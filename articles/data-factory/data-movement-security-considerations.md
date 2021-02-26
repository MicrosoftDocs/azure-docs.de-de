---
title: Sicherheitshinweise
description: Beschreibt die grundlegende Sicherheitsinfrastruktur, die von Datenverschiebungsdiensten in Azure Data Factory verwendet wird, um Ihre Daten zu schützen.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 1a99fbd3d3163808a364e8b26e770563a901dc18
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371327"
---
# <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Sicherheitsüberlegungen für Datenverschiebung in Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
>
> * [Version 1](v1/data-factory-data-movement-security-considerations.md)
> * [Aktuelle Version](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel ist die grundlegende Sicherheitsinfrastruktur beschrieben, die von Datenverschiebungsdiensten in Azure Data Factory verwendet wird, um Ihre Daten zu schützen. Data Factory-Verwaltungsressourcen setzen auf der Azure Sicherheitsinfrastruktur auf und nutzen alle möglichen Sicherheitsmaßnahmen, die von Azure bereitgestellt werden.

In einer Data Factory-Lösung erstellen Sie mindestens eine [Pipeline](concepts-pipelines-activities.md). Bei einer Pipeline handelt es sich um eine logische Gruppierung von Aktivitäten, die zusammen eine Aufgabe bilden. Diese Pipelines befinden sich in der Region, in der die Data Factory erstellt wurde.

Obwohl Data Factory nur in einigen Regionen verfügbar ist, ist der Datenverschiebungsdienst [weltweit verfügbar](concepts-integration-runtime.md#integration-runtime-location), um Datenkonformität, Effizienz und niedrigere Kosten für ausgehenden Netzwerkdatenverkehr sicherzustellen.

Azure Data Factory, einschließlich Azure Integration Runtime und der selbstgehosteten Integration Runtime, speichert temporäre Daten, Cachedaten oder Protokolle außer den Anmeldeinformationen des verknüpften Diensts für Clouddatenspeicher, die mithilfe von Zertifikaten verschlüsselt werden. Mit Data Factory können Sie datengesteuerte Workflows erstellen, um die Verschiebung von Daten zwischen [unterstützten Datenspeichern](copy-activity-overview.md#supported-data-stores-and-formats) und die Verarbeitung von Daten mithilfe von [Computediensten](compute-linked-services.md) in anderen Regionen oder in einer lokalen Umgebung zu orchestrieren. Außerdem haben Sie die Möglichkeit, mithilfe von SDKs und Azure Monitor Workflows zu überwachen und zu verwalten.

Data Factory ist zertifiziert für:

| **[CSA STAR-Zertifizierung](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](/compliance/regulatory/offering-iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](/compliance/regulatory/offering-hipaa-hitech)** |
| **[HITRUST](/compliance/regulatory/offering-hitrust)** |

Informationen zur Konformität von Azure und zur eigenständigen Sicherung der Azure-Infrastruktur finden Sie im [Microsoft Trust Center](https://microsoft.com/trustcenter/default.aspx). Die aktuelle Liste aller Azure-Complianceangebote finden Sie unter https://aka.ms/AzureCompliance.

In diesem Artikel werden Sicherheitsüberlegungen zu den beiden folgenden Datenverschiebungsszenarien erläutert:

- **Cloudszenario**: In diesem Szenario sind sowohl Ihre Quelle als auch das Ziel über das Internet öffentlich zugänglich. Dazu gehören verwaltete Cloudspeicherdienste wie Azure Storage, Azure Synapse Analytics, Azure SQL-Datenbank, Azure Data Lake Storage, Amazon S3, Amazon Redshift, SaaS-Dienste wie Salesforce und Webprotokolle wie FTP und OData. Eine vollständige Liste unterstützter Datenquellen finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).
- **Hybridszenario**: In diesem Szenario befindet sich entweder Ihre Quelle oder Ihr Ziel hinter einer Firewall oder in einem lokalen Unternehmensnetzwerk. Oder der Datenspeicher befindet sich in einem privaten oder virtuellen Netzwerk (meist die Quelle) und ist nicht öffentlich zugänglich. Zu diesem Szenario zählen auch Datenbankserver, die auf virtuellen Computern gehostet werden.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cloudszenarien

### <a name="securing-data-store-credentials"></a>Schützen von Datenspeicher-Anmeldeinformationen

- **Speichern von verschlüsselten Anmeldeinformationen im verwalteten Azure Data Factory-Speicher.** Data Factory schützt Ihre Anmeldeinformationen für den Datenspeicher dadurch, dass sie mit von Microsoft verwalteten Zertifikaten verschlüsselt werden. Diese Zertifikate werden alle zwei Jahre ausgetauscht (wozu Erneuerung der Zertifikate und Migration von Anmeldeinformationen gehören). Weitere Informationen zur Azure Storage-Sicherheit finden Sie unter [Übersicht über die Sicherheit von Azure Storage](../storage/blobs/security-recommendations.md).
- **Speichern von Anmeldeinformationen in Azure Key Vault.** Eine weitere Möglichkeit ist das Speichern der Anmeldeinformationen für den Datenspeicher in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Die Anmeldeinformationen werden dann von Data Factory beim Ausführen einer Aktivität abgerufen. Weitere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Datenverschlüsselung während der Übertragung
Wenn der Clouddatenspeicher HTTPS oder TLS unterstützt, erfolgen alle Datenübertragungen zwischen Datenverschiebungsdiensten in Data Factory und einem Clouddatenspeicher über einen sicheren Kanal (HTTPS oder TLS).

> [!NOTE]
> Für alle Verbindungen mit Azure SQL-Datenbank und Azure Synapse Analytics ist eine Verschlüsselung (SSL/TLS) erforderlich, solange Daten in die und aus der Datenbank übertragen werden. Wenn Sie eine Pipeline mit JSON erstellen, fügen Sie die Verschlüsselungseigenschaft hinzu, und legen Sie die Eigenschaft in der Verbindungszeichenfolge auf **true** fest. Für Azure Storage können Sie **HTTPS** in der Verbindungszeichenfolge verwenden.

> [!NOTE]
> Um die Verschlüsselung während der Datenübertragung von Oracle zu aktivieren, führen Sie eine der unten aufgeführten Optionen aus:
>
> 1. Wechseln Sie auf dem Oracle-Server zu Oracle Advanced Security (OAS) und konfigurieren die Verschlüsselungseinstellungen, die Triple-DES-Verschlüsselung (3DES) and Advanced Encryption Standard (AES) unterstützen. Details dazu finden Sie [hier](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Die ADF handelt automatisch die zu verwendende Verschlüsselungsmethode als diejenige aus, die Sie in OAS beim Herstellen der Verbindung mit Oracle konfigurieren.
> 2. In der ADF können Sie „ EncryptionMethod=1“ in der Verbindungszeichenfolge (im verknüpften Dienst) hinzufügen. Dadurch wird SSL/TLS als Verschlüsselungsmethode verwendet. Zu diesem Zweck müssen Sie Verschlüsselungseinstellungen ohne SSL in OAS auf Oracle-Serverseite deaktivieren, um Verschlüsselungskonflikte zu vermeiden.

> [!NOTE]
> Die verwendete TLS-Version ist 1.2.

### <a name="data-encryption-at-rest"></a>Datenverschlüsselung ruhender Daten

Einige Datenspeicher unterstützen die Verschlüsselung von ruhenden Daten. Es empfiehlt sich, dass Sie einen Datenverschlüsselungsmechanismus für solche Datenspeicher aktivieren. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Transparent Data Encryption (TDE) in Azure Synapse Analytics bietet Schutz vor der Bedrohung durch schädliche Aktivitäten, indem die ruhenden Daten in Echtzeit ver- und entschlüsselt werden. Dieses Verhalten ist für den Client transparent. Weitere Informationen finden Sie unter [Absichern einer Datenbank in Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL-Datenbank

Auch Azure SQL-Datenbank unterstützt Transparent Data Encryption (TDE), die Schutz vor der Bedrohung durch schädliche Aktivitäten bietet. Hierzu werden die Daten in Echtzeit ver- und entschlüsselt, ohne dass Änderungen der Anwendung erforderlich sind. Dieses Verhalten ist für den Client transparent. Weitere Informationen finden Sie unter [Transparente Datenverschlüsselung für SQL-Datenbank und Data Warehouse](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store

Azure Data Lake Store bietet ebenfalls eine Verschlüsselung für Daten, die im Konto gespeichert sind. Wenn diese Option aktiviert ist, verschlüsselt Data Lake Store Daten automatisch vor der dauerhaften Speicherung und entschlüsselt Daten vor dem Abrufen. So ist der Vorgang für den Client, der auf die Daten zugreift, transparent. Weitere Informationen finden Sie unter [Sicherheit in Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage und Azure Table Storage

Azure Blob Storage und Azure Table Storage unterstützen die Speicherdienstverschlüsselung (Storage Service Encryption, SSE), bei der Ihre Daten vor der Weitergabe an den Speicher automatisch verschlüsselt und vor dem Abrufen entschlüsselt werden. Weitere Informationen finden Sie unter [Azure Storage Service Encryption für ruhende Daten](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3

Amazon S3 unterstützt die Client- und Serververschlüsselung von ruhenden Daten. Weitere Informationen finden Sie unter [Schutz von Daten mittels Verschlüsselung](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift

Amazon Redshift unterstützt die Clusterverschlüsselung für ruhende Daten. Weitere Informationen finden Sie unter [Amazon Redshift-Datenbankverschlüsselung](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce

Salesforce unterstützt Shield Platform Encryption, die eine Verschlüsselung aller Dateien, Anlagen und benutzerdefinierten Felder ermöglicht. Weitere Informationen finden Sie unter [Grundlegendes zum OAuth-Webserver-Authentifizierungsfluss](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hybridszenario

Hybridszenarien erfordern, dass die selbstgehostete Integration Runtime in einem lokalen Netzwerk oder einem virtuellen Netzwerk (Azure) oder innerhalb einer virtuellen Private Cloud (Amazon) installiert wird. Die selbstgehostete Integrationslaufzeit muss auf die lokalen Datenspeicher zugreifen können. Weitere Informationen zur selbstgehosteten Integration Runtime finden Sie unter [Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](./create-self-hosted-integration-runtime.md). 

![Kanäle der selbstgehosteten Integrationslaufzeit](media/data-movement-security-considerations/data-management-gateway-channels.png)

Der Befehlskanal ermöglicht Kommunikation zwischen Datenverschiebungsdiensten in Data Factory und der selbstgehosteten Integration Runtime. Die Kommunikation enthält die Informationen, die sich auf die Aktivität beziehen. Der Datenkanal wird dazu verwendet, Daten zwischen lokalen Datenspeichern und Clouddatenspeichern zu übertragen.    

### <a name="on-premises-data-store-credentials"></a>Anmeldeinformationen für lokale Datenspeicher

Die Anmeldeinformationen können in Data Factory gespeichert oder während der Laufzeit von Azure Key Vault von [Data Factory referenziert](store-credentials-in-key-vault.md) werden. Beim Speichern von Anmeldeinformationen innerhalb von Data Factory werden diese immer verschlüsselt für die selbstgehostete Integration Runtime gespeichert. 

   - **Lokales Speichern von Anmeldeinformationen.** Wenn Sie das Cmdlet **Set-AzDataFactoryV2LinkedService** mit den Verbindungszeichenfolgen und Anmeldeinformationen direkt inline im JSON verwenden, wird der verknüpfte Dienst verschlüsselt und für die selbstgehostete Integration Runtime gespeichert.  In diesem Fall werden die Anmeldeinformationen durch den Azure-Back-End-Dienst, der als sehr sicher gilt, zum selbstgehosteten Integrationscomputer übertragen, wo sie schließlich verschlüsselt und gespeichert werden. Die selbstgehostete Integration Runtime verwendet Windows-[DPAPI](/previous-versions/ms995355(v=msdn.10)) zum Verschlüsseln der vertraulichen Daten und Anmeldeinformationen.

   - **Speichern von Anmeldeinformationen in Azure Key Vault.** Eine weitere Möglichkeit ist das Speichern der Anmeldeinformationen für den Datenspeicher in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Die Anmeldeinformationen werden dann von Data Factory beim Ausführen einer Aktivität abgerufen. Weitere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md).

   - **Speichern Sie Anmeldeinformationen lokal, ohne sie über den Azure-Back-End an die selbstgehostete Integration Runtime zu übermitteln**. Wenn Sie Anmeldeinformationen lokal auf der selbstgehosteten Integration Runtime verschlüsseln und speichern möchten, ohne die Anmeldeinformationen über den Data Factory-Back-End zu leiten, führen Sie die Schritte in [Verschlüsseln von Anmeldeinformationen für lokale Datenspeicher in Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md) aus. Diese Option wird von allen Connectors unterstützt. Die selbstgehostete Integration Runtime verwendet Windows-[DPAPI](/previous-versions/ms995355(v=msdn.10)) zum Verschlüsseln der vertraulichen Daten und Anmeldeinformationen. 

   - Verwenden Sie das Cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential**, um Anmeldeinformationen des verknüpften Diensts bzw. vertrauliche Details im verknüpften Dienst zu verschlüsseln. Anschließend können Sie das zurückgegebene JSON (mit dem **EncryptedCredential**-Element in der Verbindungszeichenfolge) verwenden, um mit dem Cmdlet **Set-AzDataFactoryV2LinkedService** einen verknüpften Dienst zu erstellen.  

#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Beim Verschlüsseln des verknüpften Diensts auf der selbstgehosteten Integration Runtime verwendete Ports

Standardmäßig verwendet PowerShell den Port 8060 auf dem Computer mit der selbstgehosteten Integration Runtime für die sichere Kommunikation. Dieser Port kann bei Bedarf geändert werden.  

![HTTPS-Port für das Gateway](media/data-movement-security-considerations/https-port-for-gateway.png)


### <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung

Alle Datenübertragungen erfolgen über den sicheren Kanal HTTPS und TLS über TCP, um Man-in-the-Middle-Angriffe während der Kommunikation mit Azure-Diensten zu verhindern.

Sie können auch [IPSec-VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) oder [Azure ExpressRoute](../expressroute/expressroute-introduction.md) verwenden, um den Kommunikationskanal zwischen Ihrem lokalen Netzwerk und Azure zusätzlich zu schützen.

Azure Virtual Network ist eine logische Darstellung Ihres Netzwerks in der Cloud. Sie können ein lokales Netzwerk mit Ihrem virtuellen Netzwerk verbinden, indem Sie IPSec-VPN (Standort-zu-Standort) oder ExpressRoute (privates Peering) einrichten.

In der folgenden Tabelle sind die Empfehlungen für die Konfiguration von Netzwerk und selbstgehosteter Integrationslaufzeit zusammengefasst, die sich aus verschiedenen Kombinationen von Quell- und Zielstandort für hybride Datenverschiebung ergeben.

| `Source`      | Destination                              | Netzwerkkonfiguration                    | Setup der Integrationslaufzeit                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokal | Virtuelle Computer und Clouddienste, die in virtuellen Netzwerken bereitgestellt werden | IPSec-VPN (Punkt-zu-Standort oder Standort-zu-Standort) | Die selbstgehostete Integration Runtime sollte auf einer Azure-VM im virtuellen Netzwerk installiert sein.  |
| Lokal | Virtuelle Computer und Clouddienste, die in virtuellen Netzwerken bereitgestellt werden | ExpressRoute (privates Peering)           | Die selbstgehostete Integration Runtime sollte auf einer Azure-VM im virtuellen Netzwerk installiert sein.  |
| Lokal | Azure-basierte Dienste, die einen öffentlichen Endpunkt haben | ExpressRoute (Microsoft-Peering)            | Die selbstgehostete Integrationslaufzeit kann lokal oder auf einer Azure-VM installiert sein. |

Die folgenden Abbildungen veranschaulichen die Verwendung der selbstgehosteten Integration Runtime zum Verschieben von Daten zwischen einer lokalen Datenbank und Azure-Diensten über ExpressRoute und IPSec-VPN (mit Azure Virtual Network):

#### <a name="express-route"></a>ExpressRoute

![Verwenden von ExpressRoute mit Gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

#### <a name="ipsec-vpn"></a>IPSec-VPN

![IPSec-VPN mit Gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a> Einrichten von Firewallkonfigurationen und Zulassungsliste für IP-Adressen

> [!NOTE]
> Möglicherweise müssen Sie Ports verwalten oder eine Zulassungsliste für Domänen auf Ebene der Unternehmensfirewall entsprechend den Anforderungen der jeweiligen Datenquellen einrichten. In dieser Tabelle werden nur Azure SQL-Datenbank, Azure Synapse Analytics und Azure Data Lake Store als Beispiele verwendet.

> [!NOTE]
> Weitere Informationen zu Datenzugriffsstrategien über Azure Data Factory finden Sie in [diesem Artikel](./data-access-strategies.md#data-access-strategies-through-azure-data-factory).

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Firewallanforderungen für lokales/privates Netzwerk

In einem Unternehmen wird eine Unternehmensfirewall auf dem zentralen Router der Organisation ausgeführt. Die Windows-Firewall wird als Daemon auf dem lokalen Computer ausgeführt, auf dem die selbstgehostete Integration Runtime installiert ist. 

Die folgende Tabelle enthält die Anforderungen für ausgehende Ports und die Domänenanforderungen für die Unternehmensfirewalls:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE]
> Möglicherweise müssen Sie Ports verwalten oder eine Zulassungsliste für Domänen auf Ebene der Unternehmensfirewall entsprechend den Anforderungen der jeweiligen Datenquellen einrichten. In dieser Tabelle werden nur Azure SQL-Datenbank, Azure Synapse Analytics und Azure Data Lake Store als Beispiele verwendet.   

Die folgende Tabelle enthält die Anforderungen für eingehende Ports für die Windows-Firewall:

| Eingehende Ports | BESCHREIBUNG                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Vom PowerShell-Cmdlet für die Verschlüsselung erforderlich, wie in [Verschlüsseln von Anmeldeinformationen für lokale Datenspeicher in Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md) und für die Anwendung zur Anmeldeinformationsverwaltung beschrieben, um Anmeldeinformationen für die lokalen Datenspeicher auf der selbstgehosteten Integration Runtime sicher festzulegen. |

![Gatewayportanforderungen](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>Einrichten von Firewallkonfigurationen und Zulassungsliste in Datenspeichern

Einige Datenspeicher in der Cloud erfordern auch, dass die IP-Adresse des Computers, über den auf den Speicher zugegriffen wird, zugelassen wird. Stellen Sie sicher, dass die IP-Adresse des Computers der selbstgehosteten Integration Runtime in der Firewall ordnungsgemäß zugelassen bzw. konfiguriert ist.

Die folgenden Clouddatenspeicher erfordern, dass die IP-Adresse des Computers der selbstgehosteten Internet Runtime zugelassen wird. Einige dieser Datenspeicher erfordern standardmäßig möglicherweise keine Zulassungsliste.

* [Azure SQL-Datenbank](../azure-sql/database/firewall-configure.md)
* [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
* [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
* [Azure Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
* [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Kann die selbstgehostete Integration Runtime für unterschiedliche Data Factorys gemeinsam genutzt werden?**

Ja. Ausführlichere Informationen finden Sie [hier](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Welche Portanforderungen sind erforderlich, damit die selbstgehostete Integration Runtime funktioniert?**

Die selbstgehostete Integration Runtime erstellt HTTP-basierte Verbindungen für den Zugriff auf das Internet. Der ausgehende Ports 443 muss geöffnet sein, damit die selbstgehostete Integration Runtime diese Verbindung herstellen kann. Öffnen Sie den eingehenden Port 8060 nur auf Computerebene (nicht auf Ebene der Unternehmensfirewall) für die Anwendung zur Anmeldeinformationsverwaltung. Wenn Azure SQL-Datenbank oder Azure Synapse Analytics als Quelle oder Ziel verwendet wird, müssen Sie auch Port 1433 öffnen. Weitere Informationen finden Sie im Abschnitt [Einrichten von Firewallkonfigurationen und Zulassungsliste für IP-Adressen](#firewall-configurations-and-allow-list-setting-up-for-ip-addresses).

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Leistung der Kopieraktivität von Azure Data Factory finden Sie im [Handbuch zur Leistung und Optimierung der Kopieraktivität](copy-activity-performance.md).
