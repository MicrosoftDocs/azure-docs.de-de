---
title: Übersicht über die Azure-Verschlüsselung | Microsoft-Dokumentation
description: Lernen Sie die Verschlüsselungsoptionen in Azure kennen. Lesen Sie die Informationen zur Verschlüsselung ruhender Daten, zur Verschlüsselung in Aktion und zur Schlüsselverwaltung mit Azure Key Vault.
services: security
author: msmbaldwin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: ff023ad98c7ffa269223b5d0b4a1cecc5fde1feb
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410214"
---
# <a name="azure-encryption-overview"></a>Übersicht über die Azure-Verschlüsselung

Dieser Artikel bietet eine Übersicht über die Verwendung der Verschlüsselung in Microsoft Azure. Er behandelt die wichtigsten Bereiche der Verschlüsselung, einschließlich Verschlüsselung ruhender Daten, Verschlüsselung in Aktion und Schlüsselverwaltung mit Azure Key Vault. Jeder Abschnitt enthält Links zu ausführlicheren Informationen.

## <a name="encryption-of-data-at-rest"></a>Verschlüsselung für ruhende Daten

Ruhende Daten umfassen Informationen, die in einem beliebigen digitalen Format im dauerhaften Speicher auf physischen Medien gespeichert sind. Zu den Medien gehören Dateien auf Magnet- oder optischen Datenträgern, archivierte Daten und Datensicherungen. Microsoft Azure bietet eine Reihe von Datenspeicherlösungen für verschiedene Anforderungen, darunter Datei-, Daten-, Blob- und Tabellenspeicher. Microsoft bietet zudem eine Verschlüsselung zum Schutz von [Azure SQL-Datenbank](../../azure-sql/database/sql-database-paas-overview.md), [Azure Cosmos DB](../../data-factory/introduction.md) und Azure Data Lake.

Die Verschlüsselung ruhender Daten ist für Dienste in allen Software-as-a-Service- (SaaS), Platform-as-a-Service- (PaaS) und Infrastructure-as-a-Service-Cloudmodellen (IaaS) verfügbar. In diesem Artikel werden Ressourcen zusammenfassend beschrieben und bereitgestellt, mit denen Sie die Verschlüsselungsoptionen von Azure nutzen können.

Eine ausführliche Erörterung zur Verschlüsselung ruhender Daten in Azure finden Sie unter [Azure-Datenverschlüsselung ruhender Daten](encryption-atrest.md).

## <a name="azure-encryption-models"></a>Azure-Verschlüsselungsmodelle

Azure unterstützt verschiedene Verschlüsselungsmodelle, darunter die serverseitige Verschlüsselung unter Verwendung dienstverwalteter Schlüssel, unter Verwendung kundenverwalteter Schlüssel in Key Vault oder unter Verwendung kundenverwalteter Schlüssel auf vom Kunden gesteuerter Hardware. Mit der clientseitigen Verschlüsselung können Sie Schlüssel lokal oder an anderen sicheren Speicherorten verwalten und speichern.

### <a name="client-side-encryption"></a>Clientseitige Verschlüsselung

Die clientseitige Verschlüsselung erfolgt außerhalb von Azure. Sie hat folgenden Inhalt:

- Daten, die über eine im Rechenzentrum des Kunden ausgeführte Anwendung oder über eine Dienstanwendung verschlüsselt werden
- Daten, die beim Empfang in Azure bereits verschlüsselt sind

Bei der clientseitigen Verschlüsselung hat der Clouddienstanbieter keinen Zugriff auf die Verschlüsselungsschlüssel und kann diese Daten nicht entschlüsseln. Sie behalten die vollständige Kontrolle über die Schlüssel.

### <a name="server-side-encryption"></a>Serverseitige Verschlüsselung

Die drei Modelle für die serverseitige Verschlüsselung bieten unterschiedliche Merkmale der Schlüsselverwaltung, die Sie je nach Ihren Anforderungen auswählen können:

- **Dienstverwaltete Schlüssel:** bieten eine Kombination aus Kontrolle und Benutzerfreundlichkeit mit geringem Mehraufwand

- **Kundenverwaltete Schlüssel:** bieten Ihnen die Kontrolle über die Schlüssel, einschließlich der Möglichkeit, die BYOK-Funktion (Bring your Own Key) zu verwenden oder neue Schlüssel zu generieren

- **Dienstverwaltete Schlüssel auf vom Kunden gesteuerter Hardware:** ermöglichen es Ihnen, Schlüssel in Ihrem proprietären Repository zu verwalten, das sich außerhalb des Einflussbereichs von Microsoft befindet. Dies wird als „Host Your Own Key“ (HYOK) bezeichnet. Die Konfiguration ist jedoch komplex, und die meisten Azure-Dienste unterstützen dieses Modell nicht.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Sie können virtuelle Windows- und Linux-Computer mithilfe von [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) schützen. Hierbei werden die [Windows BitLocker](/previous-versions/windows/it-pro/windows-vista/cc766295(v=ws.10))-Technologie und Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) verwendet, um Betriebssystemdatenträger sowie Datenträger mit vollständiger Volumeverschlüsselung zu schützen.

Verschlüsselungsschlüssel und Geheimnisse werden in Ihrem [Azure Key Vault-Abonnement](../../key-vault/general/overview.md) geschützt. Mithilfe des Azure Backup-Diensts können Sie verschlüsselte virtuelle Computer, die mit der KEK-Konfiguration (Key Encryption Key) verschlüsselt sind, sichern und wiederherstellen.

### <a name="azure-storage-service-encryption"></a>Azure Storage Service Encryption

Ruhende Daten in Azure Blob Storage und Azure-Dateifreigaben können in serverseitigen und clientseitigen Szenarien verschlüsselt werden.

Die [Azure-Speicherdienstverschlüsselung](../../storage/common/storage-service-encryption.md) (Storage Service Encryption, SSE) kann Daten automatisch verschlüsseln, bevor sie gespeichert werden, und entschlüsselt sie automatisch beim Abrufen. Der Vorgang erfolgt für Benutzer vollkommen unbemerkt. Storage Service Encryption verwendet die [Advanced Encryption Standard-Verschlüsselung (AES)](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) mit 256 Bit, die zu den stärksten verfügbaren Blockchiffren gehört. AES verarbeitet die Verschlüsselung, Entschlüsselung und Schlüsselverwaltung auf im Hintergrund.

### <a name="client-side-encryption-of-azure-blobs"></a>Clientseitige Verschlüsselung von Azure-Blobs

Sie können die clientseitige Verschlüsselung von Azure-Blobs auf verschiedene Weise durchführen.

Sie können das NuGet-Paket „Azure Storage-Clientbibliothek für .NET“ verwenden, um Daten in Ihren Clientanwendungen vor dem Hochladen in Azure Storage zu verschlüsseln.

Weitere Informationen zum Verwenden und Herunterladen des NuGet-Pakets „Azure Storage-Clientbibliothek für .NET“ finden Sie unter [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Bei Verwendung der clientseitigen Verschlüsselung mit Key Vault werden Ihre Daten mit einem einmaligen symmetrischen Inhaltsverschlüsselungsschlüssel (CEK, Content Encryption Key) verschlüsselt, der vom Azure Storage Client SDK generiert wird. Der CEK wird mit einem Schlüsselverschlüsselungsschlüssel (KEK, Key Encryption Key) verschlüsselt, bei dem es sich entweder um ein symmetrisches oder ein asymmetrisches Schlüsselpaar handeln kann. Sie können ihn lokal verwalten oder in Key Vault speichern. Die verschlüsselten Daten werden dann in Azure Storage hochgeladen.

Weitere Informationen zur clientseitigen Verschlüsselung mit Key Vault und den ersten Schritten mit entsprechenden Anweisungen finden Sie unter [Tutorial: Verschlüsseln und Entschlüsseln von Blobs in Microsoft Azure Storage per Azure Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Schließlich können Sie auch die Azure Storage-Clientbibliothek für Java verwenden, um die clientseitige Verschlüsselung vor dem Hochladen von Daten in Azure Storage und die Entschlüsselung der Daten beim Herunterladen auf den Client durchzuführen. Um die Schlüsselverwaltung für Speicherkonten zu ermöglichen, unterstützt diese Bibliothek zudem die Integration in [Key Vault](https://azure.microsoft.com/services/key-vault/).

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Verschlüsselung ruhender Daten mit Azure SQL-Datenbank

[Azure SQL-Datenbank](../../azure-sql/database/sql-database-paas-overview.md) ist ein relationaler Datenbankdienst in Azure für allgemeine Zwecke, der Strukturen wie relationale Daten, JSON, räumliche Daten und XML unterstützt. SQL-Datenbank unterstützt die serverseitige Verschlüsselung über das TDE-Feature (Transparent Data Encryption) sowie die clientseitige Verschlüsselung über das Always Encrypted-Feature.

#### <a name="transparent-data-encryption"></a>Transparente Datenverschlüsselung

[TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) wird verwendet, um Datendateien aus [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL-Datenbank](../../azure-sql/database/sql-database-paas-overview.md) und [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) in Echtzeit mit einem Datenbankverschlüsselungsschlüssel (DEK, Database Encryption Key) zu verschlüsseln, der für die Verfügbarkeit während der Wiederherstellung im Datenbank-Startdatensatz gespeichert ist.

TDE schützt die Daten- und Protokolldateien über die Verschlüsselungsalgorithmen AES und Triple Data Encryption Standard (3DES). Die Verschlüsselung der Datenbankdatei erfolgt auf Seitenebene. Die Seiten in einer verschlüsselten Datenbank werden verschlüsselt, bevor sie auf den Datenträger geschrieben werden, und entschlüsselt, bevor sie in den Arbeitsspeicher eingelesen werden. TDE ist jetzt bei neu erstellten Azure SQL-Datenbanken standardmäßig aktiviert.

#### <a name="always-encrypted-feature"></a>Always Encrypted-Feature

Mit dem [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)-Feature in Azure SQL können Sie Daten in Clientanwendungen vor dem Speichern in Azure SQL-Datenbank verschlüsseln. Außerdem können Sie die Delegierung der lokalen Datenbankverwaltung an Drittanbieter aktivieren und die Trennung zwischen den Benutzern, die die Daten besitzen und anzeigen können, und den Benutzern, die die Daten verwalten, jedoch keinen Zugriff darauf haben sollen, aufrechterhalten.

#### <a name="cell-level-or-column-level-encryption"></a>Verschlüsselung auf Zellen- oder Spaltenebene

Mit Azure SQL-Datenbank können Sie mithilfe von Transact-SQL eine symmetrische Verschlüsselung auf eine Datenspalte anwenden. Dieser Ansatz wird als [Verschlüsselung auf Zellenebene oder Verschlüsselung auf Spaltenebene (CLE)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) bezeichnet, da Sie bestimmte Datenspalten oder sogar bestimmte Datenzellen mit unterschiedlichen Verschlüsselungsschlüsseln verschlüsseln können. Diese Verschlüsselungsfunktion ist differenzierter als die TDE, bei der Daten in Seiten verschlüsselt werden.

CLE verfügt über integrierte Funktionen, mit denen Sie Daten über symmetrische oder asymmetrische Schlüssel, dem öffentlichen Schlüssel eines Zertifikats oder einer Passphrase über 3DES verschlüsseln können.

### <a name="cosmos-db-database-encryption"></a>Cosmos DB-Datenbankverschlüsselung

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) ist eine global verteilte Datenbank von Microsoft mit mehreren Modellen. Die in Cosmos DB in nichtflüchtigem Speicher (Solid State Drives) gespeicherten Benutzerdaten werden standardmäßig verschlüsselt. Es sind keine Steuerelemente zum Aktivieren oder Deaktivieren vorhanden. Die Verschlüsselung ruhender Daten wird über eine Reihe von Sicherheitstechnologien implementiert, einschließlich der Speichersysteme für sichere Schlüssel, verschlüsselter Netzwerke und Kryptografie-APIs. Die Verschlüsselungsschlüssel werden von Microsoft verwaltet und entsprechend internen Microsoft-Richtlinien rotiert.

### <a name="at-rest-encryption-in-data-lake"></a>Verschlüsselung ruhender Daten in Data Lake

[Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) ist ein unternehmensweites Repository für alle Arten von Daten, die vor jeglicher formalen Definition von Anforderungen oder Schemas an einer zentralen Stelle gesammelt werden. Data Lake Store unterstützt die standardmäßig aktivierte Verschlüsselung von ruhenden Daten im Hintergrund, die während der Erstellung Ihres Kontos eingerichtet wird. Standardmäßig verwaltet Azure Data Lake Store die Schlüssel, Sie haben jedoch die Möglichkeit, diese selbst zu verwalten.

Drei Arten von Schlüsseln werden beim Verschlüsseln und Entschlüsseln von Daten verwendet: Masterverschlüsselungsschlüssel (MEK, Master Encryption Key), Datenverschlüsselungsschlüssel (DEK, Data Encryption Key) und Blockverschlüsselungsschlüssel (BEK, Block Encryption Key). Der MEK wird zum Verschlüsseln des DEK verwendet, der auf persistenten Medien gespeichert wird. Der BEK wird vom DEK und dem Datenblock abgeleitet. Wenn Sie Ihre eigenen Schlüssel verwalten, können Sie den MEK rotieren.

## <a name="encryption-of-data-in-transit"></a>Verschlüsselung von Daten während der Übertragung

Azure bietet viele Verfahren zum Schutz von Daten beim Übertragen zwischen verschiedenen Speicherorten.

### <a name="data-link-layer-encryption-in-azure"></a>Verschlüsselung in der Sicherungsschicht in Azure

Immer wenn Datenverkehr von Azure-Kunden zwischen Rechenzentren fließt (außerhalb von physischen Grenzen, die nicht von Microsoft (oder im Auftrag von Microsoft) kontrolliert werden), wird eine Verschlüsselungsmethode für die Sicherungsschicht mit dem [Standard IEEE 802.1AE MAC Security](https://1.ieee802.org/security/802-1ae/) (auch MACsec genannt) von Punkt zu Punkt auf der zugrunde liegenden Netzwerkhardware angewendet. Die Pakete werden auf den Geräten vor dem Senden verschlüsselt und entschlüsselt. Dadurch werden physische Man-in-the-Middle-Angriffe und Spionage-/Abhörangriffe verhindert. Da diese Technologie in die Netzwerkhardware selbst integriert ist, bietet sie Verschlüsselung auf der Netzwerkhardware mit der Leitungsrate ohne eine messbar höhere Verbindungslatenz. Diese MACsec-Verschlüsselung ist standardmäßig für den gesamten Azure-Datenverkehr aktiviert, der innerhalb einer Region oder zwischen Regionen fließt, und der Kunde muss keine Aktion zum Aktivieren ausführen. 

### <a name="tls-encryption-in-azure"></a>TLS-Verschlüsselung in Azure

Microsoft gibt Kunden die Möglichkeit, das [Transport Layer Security-Protokoll](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) zum Schutz von Daten bei der Übertragung zwischen den Clouddiensten und Kunden zu verwenden. Die Microsoft-Rechenzentren verhandeln eine TLS-Verbindung mit Clientsystemen, die eine Verbindung mit Azure-Diensten herstellen. TLS bietet strenge Authentifizierung, Datenschutz von Nachrichten und Integrität (ermöglicht die Erkennung von Manipulation, Abfangen und Fälschung von Nachrichten), Interoperabilität, Algorithmusflexibilität sowie einfache Bereitstellung und Verwendung.

[Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) schützt Verbindungen zwischen den Clientsystemen von Kunden und den Clouddiensten von Microsoft durch eindeutige Schlüssel. Die Verbindungen verwenden zudem RSA-basierte Verschlüsselungsschlüssellängen von 2.048 Bit. Diese Kombination erschwert das Abfangen von Daten während der Übertragung und den Zugriff darauf.

### <a name="azure-storage-transactions"></a>Transaktionen in Azure Storage

Wenn Sie mit Azure Storage über das Azure-Portal interagieren, erfolgen alle Transaktionen über HTTPS. Für die Interaktion mit Azure Storage können Sie auch die Storage-REST-API über HTTPS verwenden. Sie können die Verwendung von HTTPS beim Aufruf von REST-APIs für den Zugriff auf Objekte in Speicherkonten erzwingen, indem Sie die Option „Sichere Übertragung erforderlich“ aktivieren.

Mit Shared Access Signatures ([SAS](../../storage/common/storage-sas-overview.md)), die zum Delegieren des Zugriffs auf Azure Storage-Objekte verwendet werden können, können Sie festlegen, dass bei Verwendung von Shared Access Signatures nur das HTTPS-Protokoll verwendet werden darf. Mit diesem Ansatz wird sichergestellt, dass jeder, der Links mit SAS-Token sendet, das richtige Protokoll verwendet.

[SMB 3.0](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn551363(v=ws.11)#BKMK_SMBEncryption), das für den Zugriff auf Azure Files-Freigaben verwendet wird, unterstützt die Verschlüsselung und ist in Windows Server 2012 R2, Windows 8, Windows 8.1 und Windows 10 verfügbar. Dies macht einen regionsübergreifenden Zugriff und sogar den Zugriff auf dem Desktop möglich.

Die Daten werden mit der clientseitigen Verschlüsselung verschlüsselt, bevor sie an Ihre Azure Storage-Instanz gesendet werden, sodass sie während der Übertragung im Netzwerk verschlüsselt sind.

### <a name="smb-encryption-over-azure-virtual-networks"></a>SMB-Verschlüsselung in virtuellen Azure-Netzwerken 

Mit [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) auf virtuellen Computern unter Windows Server 2012 oder höher haben Sie die Möglichkeit der sicheren Datenübertragung durch Verschlüsselung der Daten während der Übertragung in virtuellen Azure-Netzwerken. Durch die Datenverschlüsselung können Sie Daten vor Manipulationen und Abfangen schützen. Administratoren können die SMB-Verschlüsselung für den gesamten Server oder nur für bestimmte Freigaben aktivieren.

Nachdem die SMB-Verschlüsselung für eine Freigabe oder einen Server aktiviert wurde, können standardmäßig nur SMB 3.0-Clients auf die verschlüsselten Freigaben zugreifen.

## <a name="in-transit-encryption-in-vms"></a>Verschlüsselung während der Übertragung zwischen virtuellen Computern

Daten, die auf, von und zwischen virtuellen Computern unter Windows übertragen werden, können je nach Art der Verbindung auf unterschiedliche Weise verschlüsselt werden.

### <a name="rdp-sessions"></a>RDP-Sitzungen

Sie können über das [Remotedesktopprotokoll (RDP)](/windows/win32/termserv/remote-desktop-protocol) über einen Windows-Clientcomputer oder einen Macintosh-Computer mit installiertem RDP-Client eine Verbindung mit einem virtuellen Computer herstellen und sich bei diesem anmelden. Daten während der Übertragung über das Netzwerk in RDP-Sitzungen können durch TLS geschützt werden.

Sie können auch über Remotedesktop eine Verbindung mit einem virtuellen Linux-Computer in Azure herstellen.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Sicherer Zugriff auf virtuelle Linux-Computer mit SSH

Für die Remoteverwaltung können Sie [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) zum Herstellen von Verbindungen mit in Azure ausgeführten virtuellen Linux-Computern verwenden. SSH ist ein Protokoll für verschlüsselte Verbindungen, das die sichere Anmeldung über ungesicherte Verbindungen ermöglicht. Es ist das Standardverbindungsprotokoll für in Azure gehostete virtuelle Linux-Computer. Durch die Verwendung von SSH-Schlüsseln für die Authentifizierung sind keine Kennwörter für die Anmeldung erforderlich. SSH verwendet ein Schlüsselpaar aus einem öffentlichen und privaten Schlüssel (asymmetrische Verschlüsselung) für die Authentifizierung.

## <a name="azure-vpn-encryption"></a>Azure-VPN-Verschlüsselung

Sie können die Verbindung mit Azure über ein virtuelles privates Netzwerk herstellen, das einen sicheren Tunnel zum Datenschutz der über das Netzwerk gesendeten Daten erstellt.

### <a name="azure-vpn-gateways"></a>Azure-VPN-Gateways

Mit einem [Azure-VPN-Gateway](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) können Sie verschlüsselten Datenverkehr zwischen Ihrem virtuellen Netzwerk und Ihrem lokalen Standort über eine öffentliche Verbindung oder zwischen virtuellen Netzwerken senden.

Site-to-Site-VPN-Verbindungen verwenden [IPsec](https://en.wikipedia.org/wiki/IPsec) für die Verschlüsselung beim Datentransport. Azure-VPN-Gateways verwenden einen Satz von Standardvorschlägen. Sie können Azure-VPN-Gateways so konfigurieren, dass anstelle der Azure-Standardrichtliniensätze eine benutzerdefinierte IPsec/IKE-Richtlinie mit bestimmten Kryptografiealgorithmen und Schlüssellängen verwendet wird.

### <a name="point-to-site-vpns"></a>Point-to-Site-VPN-Verbindungen

Über Point-to-Site-VPN-Verbindungen können einzelne Clientcomputer auf ein virtuelles Azure-Netzwerk zugreifen. Das [Secure Socket Tunneling-Protokoll (SSTP)](/previous-versions/technet-magazine/cc162322(v=msdn.10)) wird verwendet, um den VPN-Tunnel zu erstellen. Das Protokoll kann Firewalls durchlaufen (der Tunnel wird als HTTPS-Verbindung angezeigt). Sie können für Point-to-Site-Verbindungen Ihre eigene interne PKI-Stammzertifizierungsstelle (Public Key-Infrastruktur) verwenden.

Sie können eine Point-to-Site-VPN-Verbindung mit einem virtuellen Netzwerk über das Azure-Portal mit Zertifikatauthentifizierung oder über PowerShell konfigurieren.

Weitere Informationen zu Point-to-Site-VPN-Verbindungen mit virtuellen Azure-Netzwerken finden Sie in folgenden Artikeln:

[Konfigurieren einer Point-to-Site-Verbindung mit einem VNET unter Verwendung der Zertifikatauthentifizierung: Azure-Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurieren einer Point-to-Site-Verbindung mit einem VNET unter Verwendung der Zertifikatauthentifizierung: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Site-to-Site-VPN-Verbindungen 

Sie können eine Site-to-Site-VPN-Gatewayverbindung verwenden, um Ihr lokales Netzwerk über einen IPsec/IKE-VPN-Tunnel (IKEv1 oder IKEv2) mit einem virtuellen Azure-Netzwerk zu verbinden. Für diese Art von Verbindung wird ein lokales VPN-Gerät benötigt, dem eine extern zugängliche, öffentliche IP-Adresse zugewiesen ist.

Sie können eine Site-to-Site-VPN-Verbindung mit einem virtuellen Netzwerk über das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle konfigurieren.

Weitere Informationen finden Sie unter

[Erstellen einer Site-zu-Site-Verbindung im Azure-Portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Erstellen einer Site-zu-Site-Verbindung in PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Erstellen eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung per CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Verschlüsselung von Daten während der Übertragung in Data Lake

Auch übertragene Daten (Daten in Bewegung) werden in Data Lake Store immer verschlüsselt. Zusätzlich zur Verschlüsselung von Daten vor dem Speichern auf persistenten Medien werden Daten auch während der Übertragung immer über HTTPS geschützt. Für die Data Lake Store-REST-Schnittstellen wird ausschließlich das HTTPS-Protokoll unterstützt.

Weitere Informationen zur Verschlüsselung von Daten während der Übertragung in Data Lake finden Sie unter [Datenverschlüsselung in Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Schlüsselverwaltung mit Key Vault

Ohne angemessenen Schutz und die richtige Verwaltung der Schlüssel ist Verschlüsselung unbrauchbar. Key Vault ist die von Microsoft empfohlene Lösung zur Verwaltung und Steuerung des Zugriffs auf die in Clouddiensten verwendeten Verschlüsselungsschlüssel. Berechtigungen für Zugriffsschlüssel können Diensten oder Benutzern über Azure Active Directory-Konten zugewiesen werden.

Mit Key Vault gehören für Organisationen das Konfigurieren, Patchen und Verwalten von HSMs (Hardware Security Modules) und wichtiger Verwaltungssoftware der Vergangenheit an. Mit Key Vault behalten Sie die Kontrolle. Microsoft erhält niemals Einblick in Ihre Schlüssel, und Anwendungen haben keinen direkten Zugriff auf die Schlüssel. Sie können zudem Schlüssel in HSMs importieren oder generieren.

## <a name="next-steps"></a>Nächste Schritte

- [Azure-Sicherheitsübersicht](./overview.md)
- [Die Netzwerksicherheit in Azure in der Übersicht](network-overview.md)
- [Übersicht über die Sicherheit der Azure-Datenbank](../../azure-sql/database/security-overview.md)
- [Virtuelle Azure-Computer – Sicherheitsübersicht](virtual-machines-overview.md)
- [Datenverschlüsselung ruhender Daten](encryption-atrest.md)
- [Bewährte Methoden für Datensicherheit und Verschlüsselung](data-encryption-best-practices.md)