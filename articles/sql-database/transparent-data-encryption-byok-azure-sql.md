---
title: TDE – Azure Key Vault-Integration oder BYOK (Bring Your Own Key) – Azure SQL-Datenbank | Microsoft-Dokumentation
description: 'Transparent Data Encryption (TDE) mit Azure Key Vault mit Bring Your Own Key-Unterstützung (BYOK) für Azure SQL-Datenbank und SQL Data Warehouse TDE mit BYOK: Überblick, Funktionsweise, Überlegungen und Empfehlungen'
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: c3a29c6b4d0308b41e29f38fc29d79634727d593
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64926009"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Azure SQL Transparent Data Encryption mithilfe von Schlüsseln, die vom Kunden in Azure Key Vault verwaltet werden: Bring Your Own Key-Unterstützung

[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) mit Azure Key Vault-Integration erlaubt die Verschlüsselung des Datenbank-Verschlüsselungsschlüssels (DEK) mit einem vom Kunden verwalteten asymmetrischen Schlüssel, der als „TDE-Schutzvorrichtung“ bezeichnet wird. Dies wird in der Regel auch als „Bring Your Own Key-Unterstützung“ (BYOK) für Transparent Data Encryption bezeichnet.  Im BYOK-Szenario wird die TDE-Schutzvorrichtung in einer Instanz von [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (dem cloudbasierten, externen Schlüsselverwaltungssystem von Azure) gespeichert, die dem Kunden gehört und von ihm verwaltet wird. Die TDE-Schutzvorrichtung kann vom Schlüsseltresor [generiert](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) oder von einem lokalen HSM-Gerät auf den Schlüsseltresor [übertragen](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) werden. Der auf der Startseite einer Datenbank gespeicherte TDE-DEK wird durch die TDE-Schutzvorrichtung, die in Azure Key Vault gespeichert ist und ihn niemals verlässt, verschlüsselt und entschlüsselt.  SQL-Datenbank müssen die Berechtigungen für den Schlüsseltresor des Kunden gewährt werden, damit der DEK ver- und entschlüsselt werden kann. Wenn die Berechtigungen des logischen SQL-Servers für den Schlüsseltresor widerrufen werden, ist kein Zugriff mehr auf die Datenbank möglich, und alle Daten bleiben verschlüsselt. Bei Azure SQL-Datenbank ist die TDE-Schutzvorrichtung auf der logischen SQL Server-Ebene festgelegt und wird von allen Datenbanken geerbt, die diesem Server zugeordnet sind. Bei der [verwalteten Azure SQL-Instanz](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance) ist die TDE-Schutzvorrichtung auf Instanzebene festgelegt und wird von allen *verschlüsselten* Datenbanken für diese Instanz geerbt. In diesem Dokument bezieht sich der Begriff *Server* sowohl auf den Server als auch die Instanz (sofern nicht anders angegeben).

> [!NOTE]
> Transparent Data Encryption mit Azure Key Vault-Integration (Bring Your Own Key) für die verwaltete Azure SQL-Datenbank-Instanz befindet sich in der Vorschau.

Mit TDE und Azure Key Vault-Integration können Benutzer Aufgaben der Schlüsselverwaltung steuern, z.B. Schlüsselrotationen, Schlüsseltresorberechtigungen und Schlüsselsicherungen. Außerdem können sie die Überwachung und Berichterstellung für alle TDE-Schutzvorrichtungen aktivieren, die Azure Key Vault-Funktionalität nutzen. Key Vault bietet eine zentrale Schlüsselverwaltung, verwendet streng überwachte Hardwaresicherheitsmodule (HSMs) und ermöglicht die Aufgabentrennung zwischen dem Verwalten von Schlüsseln und Daten, um Sicherheitsrichtlinien einzuhalten.  

TDE mit Azure Key Vault-Integration bietet folgende Vorteile:

- Erhöhte Transparenz und differenzierte Kontrolle mit der Möglichkeit, die TDE-Schutzvorrichtung selbst zu verwalten
- Möglichkeit, Berechtigungen jederzeit zu widerrufen, damit auf die Datenbank nicht mehr zugegriffen werden kann
- Zentrale Verwaltung von TDE-Schutzvorrichtung (zusammen mit anderen Schlüsseln und Geheimnissen, die in anderen Azure-Diensten verwendet werden) durch das Hosten in Key Vault
- Trennung der Zuständigkeiten für Schlüssel- und Datenverwaltung innerhalb der Organisation zur Unterstützung der Aufgabentrennung
- Größeres Vertrauen von Ihren eigenen Clients, da Key Vault dafür ausgelegt ist, dass Microsoft Verschlüsselungsschlüssel weder anzeigt noch extrahiert.
- Unterstützung der Schlüsselrotation

> [!IMPORTANT]
> Für Benutzer von dienstverwalteter TDE, die auf die Verwendung von Key Vault umsteigen möchten, bleibt TDE während der Umstellung auf eine TDE-Schutzvorrichtung in Key Vault aktiviert. Es gibt weder Ausfallzeiten noch eine erneute Verschlüsselung der Datenbankdateien. Der Wechsel von einem vom Dienst verwalteten Schlüssel zu einem Key Vault-Schlüssel erfordert lediglich eine erneute Verschlüsselung des Datenbankverschlüsselungsschlüssels (DEK), die schnell und online durchzuführen ist.

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>So funktioniert TDE mit Unterstützung von Azure Key Vault-Integration

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von der Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und den AzureRm-Modulen sind im Wesentlichen identisch.

![Authentifizierung des Servers bei Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Wenn TDE erstmals für die Verwendung einer TDE-Schutzvorrichtung aus Key Vault konfiguriert wird, sendet der Server den DEK jeder TDE-fähigen Datenbank an Key Vault, um eine Schlüsselverpackung anzufordern. Key Vault gibt den verschlüsselten Datenbank-Verschlüsselungsschlüssel zurück, der in der Benutzerdatenbank gespeichert wird.  

> [!IMPORTANT]
> Es ist wichtig zu beachten, dass **eine TDE-Schutzvorrichtung für immer in Azure Key Vault verbleibt, sobald sie darin gespeichert wurde**. Der Server kann nur Anforderungen für Schlüsselvorgänge an Schlüsselmaterial der TDE-Schutzvorrichtung in Key Vault senden. Er **greift nie auf die TDE-Schutzvorrichtung zu oder speichert diese zwischen**. Die Key Vault-Administrator ist dazu berechtigt, Key Vault-Berechtigungen des Servers jederzeit zu widerrufen. In diesem Fall werden alle Verbindungen mit dem Server unterbrochen.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Richtlinien für das Konfigurieren von TDE mit Azure Key Vault

### <a name="general-guidelines"></a>Allgemeine Richtlinien

- Stellen Sie sicher, dass sich Azure Key Vault und Azure SQL-Datenbank/verwaltete Instanz im selben Mandanten befinden werden.  Mandantenübergreifende Interaktionen zwischen Key Vault und Server **werden nicht unterstützt**.
- Entscheiden Sie, welche Abonnements für die erforderlichen Ressourcen verwendet werden: Ein späteres Verschieben des Server zwischen Abonnements erfordert eine erneute Einrichtung von TDE mit BYOKs. Weitere Informationen zum [Verschieben von Ressourcen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- Beim Konfigurieren von TDE mit Azure Key Vault ist es wichtig, die Auslastung des Schlüsseltresors durch wiederholte Packungs- und Entpackungsvorgänge zu berücksichtigen. Beispiel: Da alle Datenbanken, die einem SQL-Datenbank-Server zugeordnet sind, dieselbe TDE-Schutzvorrichtung verwenden, löst ein Failover dieses Servers so viele Schlüsselvorgänge im Tresor aus, wie sich Datenbanken auf dem Server befinden. Basierend auf unseren Erfahrungen und dokumentierten [Grenzwerten des Schlüsseltresors](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits) wird empfohlen, höchstens 500 Datenbanken vom Typ Standard/Universell oder 200 Datenbanken vom Typ Premium/Unternehmenskritisch mit einer Azure Key Vault-Instanz in einem Einzelabonnement zu verknüpfen, um konsistente Hochverfügbarkeit beim Zugriff auf die TDE-Schutzvorrichtung im Tresor sicherzustellen.
- Empfohlen: Speichern Sie eine lokale Kopie der TDE-Schutzvorrichtung.  Dazu muss ein HSM-Gerät lokal eine TDE-Schutzvorrichtung erstellen, und eine Kopie der TDE-Schutzvorrichtung muss in einem Schlüsselhinterlegungssystem gespeichert werden.  Erfahren Sie, [wie ein Schlüssel aus einem lokalen HSM in Azure Key Vault übertragen wird](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).
- Informationen zu Problemen mit vorhandenen Konfigurationen finden Sie unter [Problembehandlung bei TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde).

### <a name="guidelines-for-configuring-azure-key-vault"></a>Richtlinien für das Konfigurieren von Azure Key Vault

- Erstellen Sie einen Schlüsseltresor mit aktivierter [soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)-Eigenschaft, um bei einer versehentlichen Löschung des Schlüssels – oder Schlüsseltresors – Datenverluste zu vermeiden.  Sie müssen [PowerShell verwenden, um die Eigenschaft „soft-delete“](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) für den Schlüsseltresor zu aktivieren (diese Option ist im AKV-Portal nicht verfügbar, aber für Azure SQL erforderlich):  
  - Vorläufig gelöschte Ressourcen werden für einen festgelegten Zeitraum von 90 Tagen beibehalten, sofern sie nicht wiederhergestellt oder endgültig gelöscht werden.
  - Den Aktionen zum **Wiederherstellen** und **Bereinigen** sind in einer Schlüsseltresor-Zugriffsrichtlinie eigene Berechtigungen zugewiesen.
- Legen Sie eine Ressourcensperre für den Schlüsseltresor fest, um zu steuern, wer diese wichtige Ressource löschen kann, und um ein versehentliches oder nicht autorisiertes Löschen zu verhindern.  [Weitere Informationen zu Ressourcensperren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Gewähren Sie dem SQL-Datenbank-Server über dessen Azure Active Directory (Azure AD)-Identität Zugriff auf den Schlüsseltresor.  Wenn Sie die Benutzeroberfläche des Portals zu verwenden, wird die Azure AD-Identität automatisch erstellt, und der Server erhält die Zugriffsberechtigungen für den Schlüsseltresor.  Bei der Verwendung von PowerShell zum Konfigurieren von TDE mit BYOK muss Azure AD-Identität erstellt werden, und der Abschluss des Vorgangs sollte überprüft werden. Unter [Konfigurieren von TDE mit BYOK](transparent-data-encryption-byok-azure-sql-configure.md) und [Konfigurieren von TDE mit BYOK für verwaltete Instanz](https://aka.ms/sqlmibyoktdepowershell) finden Sie ausführliche Anleitungen für die Verwendung von PowerShell.

  > [!NOTE]
  > Wenn die Azure AD-Identität **versehentlich gelöscht wird oder die Serverberechtigungen widerrufen werden** – entweder unter Verwendung der Zugriffsrichtlinie des Schlüsseltresors oder durch versehentliches Verschieben des Servers zu einem anderen Abonnement –, verliert der Server den Zugriff auf den Tresor, und auf mit TDE verschlüsselte Datenbanken kann innerhalb von 24 Stunden nicht mehr zugegriffen werden.  

- Wenn Sie Firewalls und virtuelle Netzwerke mit Azure Key Vault verwenden, müssen Sie Folgendes konfigurieren: 
  - Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?: Wählen Sie „JA“.

  > [!NOTE]
  > Wenn per TDE verschlüsselte SQL-Datenbanken den Zugriff auf den Schlüsseltresor verlieren, weil sie die Firewall nicht umgehen können, kann auf die Datenbanken innerhalb von 24 Stunden nicht mehr zugegriffen werden.

- Aktivieren Sie die Überwachung und Berichterstellung für alle Verschlüsselungsschlüssel: Key Vault stellt Protokolle bereit, die sich problemlos in andere SIEM-Tools (Security Information & Event Management) einfügen lassen. [Azure Monitor-Protokolle](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) in Operations Management Suite (OMS) ist ein Beispiel für einen Dienst, der bereits integriert ist.
- Konfigurieren Sie alle SQL-Datenbank-Server mit zwei Azure Key Vault-Instanzen, die sich in unterschiedlichen Regionen befinden, um Hochverfügbarkeit von verschlüsselten Datenbanken zu gewährleisten.

### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Richtlinien zum Konfigurieren der TDE-Schutzvorrichtung (asymmetrischer Schlüssel)

- Erstellen Sie Ihren Verschlüsselungsschlüssel lokal auf einem lokalen HSM-Gerät. Stellen Sie sicher, dass es sich dabei um einen asymmetrischen RSA 2048-Schlüssel handelt, damit er in Azure Key Vault gespeichert werden kann.
- Hinterlegen Sie den Schlüssel in einem Schlüsselhinterlegungssystem.  
- Importieren Sie die Verschlüsselungsschlüsseldatei (PFX-, BYOK- oder BACKUP-Datei) in Azure Key Vault.

   > [!NOTE]
   > Zu Testzwecken ist das Erstellen eines Schlüssels mit Azure Key Vault möglich, dieser Schlüssel kann jedoch nicht hinterlegt werden, da der private Schlüssel den Schlüsseltresor nie verlassen kann.  Schlüssel, die zum Verschlüsseln von Produktionsdaten verwendet werden, müssen Sie immer sichern und hinterlegen, da der Verlust des Schlüssels (z.B. durch versehentliches Löschen aus dem Schlüsseltresor, oder Ablauf) zu endgültigem Datenverlust führt.

- Verwenden Sie einen Schlüssel ohne Ablaufdatum, und legen Sie niemals ein Ablaufdatum für einen bereits verwendeten Schlüssel fest: **Sobald der Schlüssel abläuft, verlieren die verschlüsselten Datenbanken den Zugriff auf ihre TDE-Schutzvorrichtung, und auf die Datenbanken kann innerhalb von 24 Stunden nicht mehr zugegriffen werden**.
- Stellen Sie sicher, dass der Schlüssel aktiviert ist und über Berechtigungen zum Ausführen der *get*-, *wrap key*- und *unwrap key*-Vorgänge verfügt.
- Erstellen Sie eine Azure Key Vault-Schlüsselsicherung, bevor Sie den Schlüssel zum ersten Mal in Azure Key Vault verwenden. Unter [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) erfahren Sie mehr zu diesem Befehl.
- Erstellen Sie immer eine neue Sicherung, wenn Änderungen am Schlüssel vorgenommen werden (z.B. Hinzufügen von ACLs, Tags oder Schlüsselattributen).
- **Lassen Sie frühere Versionen** des Schlüssels beim Rotieren von Schlüsseln im Schlüsseltresor, damit ältere Datenbanksicherungen wiederhergestellt werden können. Wenn die TDE-Schutzvorrichtung für eine Datenbank geändert wird, werden alte Sicherungen der Datenbank **nicht aktualisiert**, um die aktuelle TDE-Schutzvorrichtung zu verwenden.  Für jede Sicherung ist die TDE-Schutzvorrichtung erforderlich, mit der sie zum Wiederherstellungszeitpunkt erstellt wurde. Schlüsselrotationen können anhand der Anweisungen unter [Rotieren der Transparent Data Encryption-Schutzvorrichtung mithilfe von PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md) durchgeführt werden.
- Behalten Sie nach dem Ändern zurück zum vom Dienst verwalteten Schlüsseln alle zuvor benutzen Schlüssel in Azure Key Vault bei.  Dadurch wird sichergestellt, dass Datenbanksicherungen mit den in Azure Key Vault gespeicherten TDE-Schutzvorrichtungen wiederhergestellt werden können.  Mit Azure Key Vault erstellte TDE-Schutzvorrichtungen müssen beibehalten werden, bis alle gespeicherte Sicherungen mit vom Dienst verwalteten Schlüsseln erstellt wurden.  
- Erstellen Sie mithilfe von [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) wiederherstellbare Sicherungskopien dieser Schlüssel.
- Zum Entfernen eines möglicherweise kompromittierten Schlüssels während eines Sicherheitsvorfalls ohne Risiko von Datenverlust führen Sie die Schritte unter [Entfernen eines möglicherweise kompromittierten Schlüssels](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) durch.

## <a name="high-availability-geo-replication-and-backup--restore"></a>Hochverfügbarkeit, Georeplikation und Sicherung/Wiederherstellung

### <a name="high-availability-and-disaster-recovery"></a>Hochverfügbarkeit und Notfallwiederherstellung

Wie Hochverfügbarkeit mit Azure Key Vault konfiguriert wird, hängt von der Konfiguration Ihrer Datenbank und des SQL-Datenbank-Servers ab, und hier sind die empfohlenen Konfigurationen für zwei verschiedene Fälle.  Der erste Fall ist eine eigenständige Datenbank oder ein SQL-Datenbank-Server ohne konfigurierte geografische Redundanz.  Der zweite Fall ist eine Datenbank oder ein SQL-Datenbank-Server, die bzw. der mit Failovergruppen oder geografischer Redundanz konfiguriert ist. Dabei muss gewährleistet sein, dass jede georedundante Kopie über eine lokale Azure Key Vault-Instanz innerhalb der Failovergruppe verfügt, um sicherzustellen, dass geografische Failover funktionieren.

Wenn Sie im ersten Fall Hochverfügbarkeit einer Datenbank und eines SQL-Datenbank-Servers ohne konfigurierte Georedundanz benötigen, wird dringend empfohlen, den Server für die Verwendung von zwei verschiedenen Schlüsseltresoren in zwei verschiedenen Regionen mit demselben Schlüsselmaterial zu konfigurieren. Dazu wird mithilfe der primären Key Vault-Instanz in der gleichen Region wie der SQL-Datenbank-Server eine TDE-Schutzvorrichtung erstellt, und der Schlüssel wird in einen Schlüsseltresor in einer anderen Azure-Region geklont. So hat der Server Zugriff auf einen zweiten Schlüsseltresor, falls in der primären Schlüsseltresorumgebung ein Ausfall auftritt, während die Datenbank in Betrieb ist. Rufen Sie mit dem Cmdlet „Backup-AzKeyVaultKey“ den Schlüssel in einem verschlüsselten Format aus dem primären Schlüsseltresor ab, und verwenden Sie dann das Cmdlet „Restore-AzKeyVaultKey“ um einen Schlüsseltresor in der zweiten Region anzugeben.

![Hochverfügbarkeit für Einzelserver und keine georedundante Notfallwiederherstellung](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Konfigurieren von georedundanter Notfallwiederherstellung mit Azure Key Vault

Zum Gewährleisten der Hochverfügbarkeit von TDE-Schutzvorrichtungen für verschlüsselte Datenbanken müssen redundante Azure Key Vault-Instanzen basierend auf den vorhandenen oder gewünschten SQL-Datenbank-Failovergruppen oder aktiven Georeplikationsinstanzen konfiguriert werden.  Jeder georeplizierte Server erfordert einen separaten Schlüsseltresor, der sich gemeinsam mit dem Server in derselben Azure-Region befinden muss. Sollte der Zugriff auf eine primäre Datenbank aufgrund eines Ausfalls in einer Region nicht möglich sein und ein Failover ausgelöst werden, kann die sekundäre Datenbank mit dem sekundären Schlüsseltresor übernehmen.

Für georeplizierte Azure SQL-Datenbanken ist die folgende Konfiguration von Azure Key Vault erforderlich:

- Eine primäre Datenbank mit einem Schlüsseltresor in der Region und eine sekundäre Datenbank mit einem Schlüsseltresor in der Region.
- Mindestens eine sekundäres Datenbank ist erforderlich, bis zu vier werden unterstützt.
- Sekundäre Replikate der sekundären Datenbanken (Verkettung) werden nicht unterstützt.

Im folgenden Abschnitt werden die Schritte für Einrichtung und Konfiguration ausführlich behandelt.

### <a name="azure-key-vault-configuration-steps"></a>Schritte der Azure Key Vault-Konfiguration

- Installieren von [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Erstellen Sie zwei Azure Key Vault-Instanzen in zwei verschiedenen Regionen mithilfe von [PowerShell, um die Eigenschaft „soft-delete“ für die Schlüsseltresore zu aktivieren](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) (diese Option ist im AKV-Portal nicht verfügbar, aber für SQL erforderlich).
- Beide Azure Key Vault-Instanzen müssen sich in den beiden Regionen befinden, die im selben geografischen Azure-Raum verfügbar sind, damit Sichern und Wiederherstellen von Schlüsseln funktioniert.  Wenn sich die beiden Schlüsseltresore in unterschiedlichen geografischen Regionen befinden müssen, um die SQL-Anforderungen für die georedundante Notfallwiederherstellung zu erfüllen, können Sie den [BYOK-Prozess](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) befolgen, der das Importieren von Schlüsseln aus einem lokalen HSM zulässt.
- Erstellen Sie einen neuen Schlüssel im ersten Schlüsseltresor:  
  - RSA/RSA-HSA 2048-Schlüssel
  - Keine Ablaufdaten
  - Schlüssel ist aktiviert und verfügt über Berechtigungen zum Ausführen der get-, wrap key- und unwrap key-Vorgänge
- Sichern Sie den primären Schlüssel, und stellen Sie den Schlüssel im zweiten Schlüsseltresor wieder her.  Siehe [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) und [Restore-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey).

### <a name="azure-sql-database-configuration-steps"></a>Konfigurationsschritte für Azure SQL-Datenbank

Die folgenden Konfigurationsschritte unterscheiden sich je nachdem, ob mit einer neuen SQL-Bereitstellung begonnen wird, oder eine bereits vorhandene SQL-Bereitstellung von georedundanter Notfallwiederherstellung verwendet wird.  Wir beschreiben zuerst die Konfigurationsschritte für eine neue Bereitstellung und erklären dann das Zuweisen von in Azure Key Vault gespeicherten TDE-Schutzvorrichtungen zu einer vorhandenen Bereitstellung, in der bereits ein Link für georedundante Notfallwiederherstellung eingerichtet ist.

**Schritte für eine neue Bereitstellung:**

- Erstellen Sie die beiden SQL-Datenbank-Server in denselben zwei Regionen wie die zuvor erstellten Schlüsseltresore.
- Wählen Sie den TDE-Bereich „SQL-Datenbank-Server“ aus und für jeden SQL-Datenbank-Server:  
  - Wählen Sie die AKV-Instanz in der gleichen Region aus.
  - Wählen Sie den als TDE-Schutzvorrichtung zu verwendenden Schlüssel aus. Alle Server verwenden die lokale Kopie der TDE-Schutzvorrichtung.
  - Wenn diese Schritte im Portal ausgeführt werden, wird eine [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) für den SQL-Datenbank-Server erstellt, die zum Zuweisen der Berechtigungen für den Zugriff auf den Schlüsseltresor an den SQL-Datenbank-Server dient. Löschen Sie diese Identität nicht. Der Zugriff kann durch das Entfernen der Berechtigungen in Azure Key Vault statt für den SQL-Datenbank-Server widerrufen werden, der zum Zuweisen der Berechtigungen zum Zugriff auf den Schlüsseltresor an den SQL-Datenbank-Server verwendet wird.
- Erstellen Sie die primäre Datenbank.
- Folgen Sie den [Anleitungen für aktive Georeplikation](sql-database-geo-replication-overview.md) zum Abschließen dieses Szenarios. Mit diesem Schritt wird die sekundäre Datenbank erstellt.

![Failovergruppen und georedundante Notfallwiederherstellung](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> Bevor Sie fortfahren ist unbedingt sicherzustellen, dass dieselben TDE-Schutzvorrichtungen in beiden Schlüsseltresoren vorhanden sind, um den geografischen Link zwischen den Datenbanken einzurichten.

**Schritte für eine vorhandene SQL-Datenbank mit Bereitstellung von georedundanter Notfallwiederherstellung:**

Da die SQL-Datenbank-Server bereits vorhanden und die primäre und sekundäre Datenbank bereits zugewiesen sind, müssen die Schritte zum Konfigurieren von Azure Key Vault in der folgenden Reihenfolge ausgeführt werden:

- Beginnen Sie mit dem SQL-Datenbank-Server, der die sekundäre Datenbank hostet:
  - Weisen Sie den Schlüsseltresor zu, der sich in derselben Region befindet.
  - Weisen Sie die TDE-Schutzvorrichtung zu.
- Wechseln Sie dann zum SQL-Datenbank-Server, der die primäre Datenbank hostet:
  - Wählen Sie dieselbe TDE-Schutzvorrichtung wie für die sekundäre Datenbank aus.

![Failovergruppen und georedundante Notfallwiederherstellung](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>Beim Zuweisen des Schlüsseltresors zum Server ist es wichtig, mit dem sekundären Server zu beginnen.  Im zweiten Schritt weisen Sie den Schlüsseltresor dem primären Server zu und aktualisieren die TDE-Schutzvorrichtung. Der Link für georedundante Notfallwiederherstellung funktioniert weiterhin, da die von der replizierten Datenbank verwendete TDE-Schutzvorrichtung zu diesem Zeitpunkt für beide Server verfügbar ist.

Vor dem Aktivieren von TDE mit vom Kunden verwalteten Schlüsseln in Azure Key Vault für ein Szenario mit georedundanter Notfallwiederherstellung einer SQL-Datenbank ist es wichtig, zwei Azure Key Vault-Instanzen mit identischen Inhalten in denselben Regionen zu erstellen, die für die SQL-Datenbank-Georeplikation verwendet werden.  „Identische Inhalte“ bedeutet konkret, dass beide Schlüsseltresore Kopien der gleichen TDE-Schutzvorrichtung(en) enthalten müssen, damit beide Server Zugriff auf die von allen Datenbanken verwendeten TDE-Schutzvorrichtungen haben.  Von jetzt an müssen die beiden Schlüsseltresore synchronisiert bleiben, d.h. sie müssen nach einer Schlüsselrotation die gleichen Kopien von TDE-Schutzvorrichtungen enthalten, alte Versionen von Schlüsseln für Protokolldateien oder Sicherungen beibehalten, TDE-Schutzvorrichtungen müssen die gleichen Schlüsseleigenschaften beibehalten, und Schlüsseltresore müssen die gleichen Zugriffsberechtigungen für SQL beibehalten.  

Führen Sie die Schritte in [Aktive Georeplikation – Übersicht](sql-database-geo-replication-overview.md) durch, um ein Failover zu testen und auszulösen. Dies sollte in regelmäßigen Abständen erfolgen, um sicherzustellen, dass die Zugriffsberechtigungen für SQL für beide Schlüsseltresore noch gültig sind.

### <a name="backup-and-restore"></a>Sichern und Wiederherstellen

Sobald eine Datenbank mithilfe eines Schlüssels aus Key Vault mit TDE verschlüsselt ist, werden alle generierten Sicherungen ebenfalls mit der gleichen TDE-Schutzvorrichtung verschlüsselt.

Zum Wiederherstellen einer Sicherung, die mit einer TDE-Schutzvorrichtung aus Key Vault verschlüsselt ist, stellen Sie sicher, dass sich das Schlüsselmaterial noch unter dem ursprünglichen Schlüsselnamen im ursprünglichen Tresor befindet. Wenn die TDE-Schutzvorrichtung für eine Datenbank geändert wird, werden alte Sicherungen der Datenbank **nicht aktualisiert**, um die aktuelle TDE-Schutzvorrichtung zu verwenden. Daher wird empfohlen, alle alten Versionen der TDE-Schutzvorrichtung in Key Vault beizubehalten, damit Datenbanksicherungen wiederhergestellt werden können.

Wenn ein Schlüssel, der u.U. für die Wiederherstellung einer Sicherung benötigt wird, nicht mehr in seinem ursprünglichen Schlüsseltresor vorhanden ist, wird die folgende Fehlermeldung zurückgegeben: „Target server `<Servername>` does not have access to all AKV Uris created between <Timestamp #1> and <Timestamp #2>. Please retry operation after restoring all AKV Uris.“ (Zielserver „&lt;Servername&gt;“ kann nicht auf alle zwischen <Timestamp #1> und <Timestamp #2> erstellten AKV-URIs zugreifen. Wiederholen Sie den Vorgang, nachdem alle AKV-URIs wiederhergestellt wurden.)

Um dies zu vermeiden, führen Sie das Cmdlet [Get-AzRmSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) aus, um die Liste der Schlüssel aus Key Vault zurückzugeben, die dem Server hinzugefügt wurden (es sei denn, sie wurden von einem Benutzer gelöscht). Um sicherzustellen, dass alle Sicherungen wiederhergestellt werden können, vergewissern Sie sich, dass der Zielserver für die Sicherung auf alle diese Schlüssel zugreifen kann.

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Weitere Informationen zu Sicherungswiederherstellung für SQL-Datenbank finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank mit automatisierten Datenbanksicherungen](sql-database-recovery-using-backups.md). Weitere Informationen zu Sicherungswiederherstellung für SQL Data Warehouse finden Sie unter [Sicherung und Wiederherstellung in Azure SQL Data Warehouse](../sql-data-warehouse/backup-and-restore.md).

Ein weiterer zu berücksichtigender Aspekt für gesicherte Protokolldateien: Gesicherte Protokolldateien bleiben auch dann mit der ursprünglichen TDE-Verschlüsselung verschlüsselt, wenn die TDE-Schutzvorrichtung rotiert wurde und die Datenbank jetzt eine neue TDE-Schutzvorrichtung verwendet.  Zur Wiederherstellungszeit werden beide Schlüssel benötigt, um die Datenbank wiederherzustellen.  Wenn die Protokolldatei eine in Azure Key Vault gespeicherte TDE-Schutzvorrichtung verwendet, wird dieser Schlüssel zur Wiederherstellungszeit auch dann benötigt, wenn die Datenbank geändert wurde und mittlerweile die von Dienst verwaltete TDE verwendet.
