---
title: Überlegungen zur Sicherheit | Microsoft-Dokumentation
description: Dieses Thema enthält eine allgemeine Anleitung zum Schützen von SQL Server bei Ausführung auf einem virtuellen Azure-Computer.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 54010359f226fe02336f039e3dcbb98075e9b06a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360083"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Überlegungen zur Sicherheit von SQL Server auf Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dieses Thema enthält allgemeine Sicherheitsrichtlinien, die dazu beitragen sollen, den Zugriff auf SQL Server-Instanzen auf einem virtuellen Azure-Computer (VM) zu schützen.

Azure erfüllt mehrere Branchenvorgaben und Standards, mit denen Sie eine konforme Lösung mit SQL Server auf einem virtuellen Computer erstellen können. Weitere Informationen zur Einhaltung von Vorschriften durch Azure finden Sie unter [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-virtual-machine"></a>Steuern des Zugriffs auf die SQL-VM

Wenn Sie Ihren virtuellen SQL Server-Computer erstellen, sollten Sie sorgfältig kontrollieren können, welche Personen Zugriff auf den Computer und SQL Server haben. Im Allgemeinen sind folgende Maßnahmen ratsam:

- Beschränken Sie den Zugriff auf SQL Server auf die Anwendungen und Clients, die diesen Zugriff benötigen.
- Befolgen Sie die bewährten Methoden zum Verwalten von Benutzerkonten und Kennwörtern.

Die folgenden Abschnitte enthalten Vorschläge dazu, wie Sie diese Punkte planen können.

## <a name="secure-connections"></a>Sichere Verbindungen

Wenn Sie einen virtuellen SQL Server-Computer mit einem Katalogimage erstellen, können Sie bei der Option **SQL Server Connectivity** zwischen **Lokal (nur innerhalb der VM)** , **Privat (innerhalb des virtuellen Netzwerks)** und **Öffentlich (Internet)** wählen.

![SQL Server Connectivity](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

Wählen Sie für Ihr Szenario die Option mit den höchsten Einschränkungen, um die bestmögliche Sicherheit zu erzielen. Wenn Sie beispielsweise eine Anwendung ausführen, bei der auf SQL Server auf derselben VM zugegriffen wird, ist **Lokal** die Option mit der höchsten Sicherheit. Bei Ausführung einer Azure-Anwendung, für die Zugriff auf SQL Server erforderlich ist, wird mit **Privat** die Kommunikation mit SQL Server nur innerhalb des angegebenen [virtuellen Azure-Netzwerks](../../../virtual-network/virtual-networks-overview.md) geschützt. Falls Sie Zugriff vom Typ **Öffentlich (Internet)** auf die SQL Server-VM benötigen, sollten Sie auf jeden Fall auch die weiteren bewährten Methoden in diesem Thema befolgen, um Ihre Angriffsfläche zu reduzieren.

Für die ausgewählten Optionen im Portal werden Sicherheitsregeln für eingehenden Datenverkehr für die [Netzwerksicherheitsgruppe](../../../active-directory/identity-protection/concept-identity-protection-security-overview.md) (NSG) der VM verwendet, um den Netzwerkdatenverkehr für Ihren virtuellen Computer zuzulassen oder zu verweigern. Sie können neue NSG-Regeln für eingehenden Datenverkehr ändern oder erstellen, um den Datenverkehr zum SQL Server-Port (Standard: 1433) zuzulassen. Außerdem können Sie bestimmte IP-Adressen angeben, die über diesen Port kommunizieren können.

![Netzwerksicherheitsgruppen-Regeln](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

Zusätzlich zu NSG-Regeln zum Einschränken des Netzwerkdatenverkehrs können Sie auch die Windows-Firewall auf dem virtuellen Computer verwenden.

Wenn Sie Endpunkte mit dem klassischen Bereitstellungsmodell verwenden, sollten Sie alle Endpunkte auf dem virtuellen Computer entfernen, sofern Sie sie nicht nutzen. Weitere Informationen zur Verwendung von ACLs für Endpunkte finden Sie unter [Verwalten der ACL für einen Endpunkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint) Dies ist nicht für VMs erforderlich, für die Azure Resource Manager verwendet wird.

Erwägen Sie schließlich die Aktivierung von verschlüsselten Verbindungen für die Instanz der SQL Server-Datenbank-Engine auf Ihrem virtuellen Azure-Computer. Konfigurieren Sie die SQL Server-Instanz mit einem signierten Zertifikat. Weitere Informationen finden Sie unter [Aktivieren von verschlüsselten Verbindungen mit der Datenbank-Engine](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) und [Syntax für Verbindungszeichenfolgen](/dotnet/framework/data/adonet/connection-string-syntax).

## <a name="encryption"></a>Verschlüsselung

Verwaltete Datenträger bieten serverseitige Verschlüsselung und Azure Disk Encryption. Die [serverseitige Verschlüsselung](../../../virtual-machines/disk-encryption.md) bietet eine Verschlüsselung ruhender Daten und schützt Ihre Daten, um die Sicherheits- und Compliancevorgaben Ihrer Organisation zu erfüllen. [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) nutzt entweder BitLocker- oder DM-Crypt-Technologie und lässt sich mit Azure Key Vault integrieren, um sowohl das Betriebssystem als auch die Datenträger zu verschlüsseln. 

## <a name="use-a-non-default-port"></a>Verwenden eines Ports, der kein Standardport ist

Standardmäßig lauscht SQL Server über den bekannten Port 1433. Konfigurieren Sie für SQL Server das Lauschen über einen Port, der nicht dem Standard entspricht, z.B. 1401, um die Sicherheit zu erhöhen. Wenn Sie ein SQL Server-Katalogimage im Azure-Portal bereitstellen, können Sie diesen Port auf dem Blatt **SQL Server-Einstellungen** angeben.

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Sie haben zwei Möglichkeiten, dies nach der Bereitstellung zu konfigurieren:

- Wählen Sie für Resource Manager-VMs **Sicherheit** in der [SQL-VM-Ressource](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) aus. Sie finden dort eine Option zum Ändern des Ports.

  ![Änderung des TCP-Ports im Portal](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- Für klassische virtuelle Computer oder SQL Server-VMs, die nicht über das Portal bereitgestellt wurden, können Sie den Port manuell konfigurieren, indem Sie eine Remoteverbindung mit der VM herstellen. Informationen zu den Konfigurationsschritten finden Sie unter [Configure a Server to Listen on a Specific TCP Port](/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port) (Konfigurieren eines Servers für das Lauschen über einen bestimmten TCP-Port). Wenn Sie dieses manuelle Verfahren nutzen, müssen Sie zusätzlich eine Windows-Firewallregel hinzufügen, um eingehenden Datenverkehr für den TCP-Port zuzulassen.

> [!IMPORTANT]
> Das Angeben eines nicht dem Standard entsprechenden Ports ist ratsam, wenn Ihr SQL Server-Port für öffentliche Internetverbindungen geöffnet ist.

Wenn SQL Server über einen nicht dem Standard entsprechenden Port lauscht, müssen Sie beim Herstellen der Verbindung den Port angeben. Stellen Sie sich beispielsweise ein Szenario vor, bei dem die IP-Adresse des Servers 13.55.255.255 lautet und SQL Server über Port 1401 lauscht. Zum Herstellen der Verbindung mit SQL Server geben Sie `13.55.255.255,1401` in der Verbindungszeichenfolge an.

## <a name="manage-accounts"></a>Konten verwalten

Sie möchten verhindern, dass Angreifer Kontonamen oder Kennwörter leicht erraten können. Beachten Sie die folgenden Tipps:

- Erstellen Sie ein eindeutiges lokales Administratorkonto, das nicht den Namen **Administrator** hat.

- Verwenden Sie komplexe, sichere Kennwörter für alle Konten. Weitere Informationen zum Erstellen eines sicheren Kennworts finden Sie im Artikel [Erstellen eines sicheren Kennworts](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password).

- Standardmäßig wird für Azure während des Einrichtens von virtuellen Computern mit SQL Server die Windows-Authentifizierung ausgewählt. Aus diesem Grund wird beim Einrichten die **SA** -Anmeldung deaktiviert und ein Kennwort zugewiesen. Es wird empfohlen, die **SA**-Anmeldung nicht zu verwenden oder zu aktivieren. Verwenden Sie eine der folgenden Strategien, falls Sie eine SQL-Anmeldung benötigen:

  - Erstellen Sie ein SQL-Konto mit einem eindeutigen Namen, das über eine **sysadmin**-Mitgliedschaft verfügt. Hierfür können Sie das Portal verwenden, indem Sie während der Bereitstellung die Option **SQL-Authentifizierung** aktivieren.

    > [!TIP] 
    > Wenn Sie die SQL-Authentifizierung während der Bereitstellung nicht aktivieren, müssen Sie den Authentifizierungsmodus manuell in **SQL Server- und Windows-Authentifizierungsmodus** ändern. Weitere Informationen finden Sie unter [Ändern des Serverauthentifizierungsmodus](/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Falls Sie die **SA**-Anmeldung verwenden müssen, sollten Sie die Anmeldung nach der Bereitstellung aktivieren und ein neues sicheres Kennwort zuweisen.

## <a name="additional-best-practices"></a>Weitere Best Practices

Neben den in diesem Artikel beschriebenen bewährten Methoden wird empfohlen, dass Sie auch die bewährten Methoden für lokale Sicherheit und VM-Sicherheit berücksichtigen und implementieren. 

Weitere Informationen über lokale Sicherheitsmethoden finden Sie unter [Überlegungen zur Sicherheit bei SQL Server-Installationen](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) und im [Sicherheitscenter](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Weitere Informationen zur Sicherheit von VMs finden Sie in der [Sicherheitsübersicht über virtuelle Azure-Computer](../../../security/fundamentals/virtual-machines-overview.md).


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu bewährten Methoden in Bezug auf die Leistung finden Sie unter [Bewährte Methoden für die Leistung von SQL Server auf virtuellen Azure-Computern](performance-guidelines-best-practices.md).

Weitere Informationen zum Ausführen von SQL Server auf virtuellen Azure-Computern finden Sie in der [Übersicht zu SQL Server auf virtuellen Azure-Computern](sql-server-on-azure-vm-iaas-what-is-overview.md). Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-faq.md) weitere Informationen.