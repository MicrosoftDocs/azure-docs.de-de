---
title: Azure HDInsight-Architektur mit dem Enterprise-Sicherheitspaket
description: Erfahren Sie, wie Sie Apache HDInsight-Sicherheitsmaßnahmen mit dem Enterprise-Sicherheitspaket planen können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 86d951089e4247d9b959476c812b98e170d92bd8
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547978"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Verwendung des Enterprise-Sicherheitspakets in HDInsight

Der standardmäßige Azure HDInsight-Cluster ist ein Einzelbenutzercluster. Dieser ist für die meisten Unternehmen geeignet, in denen kleinere Anwendungsteams große Datenworkloads erstellen. Jeder Benutzer kann bei Bedarf einen eigenen Cluster erstellen und ihn löschen, wenn er nicht mehr benötigt wird.

Viele Unternehmen führten die Umstellung auf ein Modell durch, bei dem IT-Teams Cluster verwalten und mehrere Anwendungsteams Cluster gemeinsam nutzen. Diese größeren Unternehmen benötigen Zugriff auf den Cluster durch mehrere Benutzer in Azure HDInsight.

HDInsight basiert auf einem häufig verwendeten Identitätsanbieter – Active Directory – in einer verwalteten Methode. Durch die Integration von HDInsight in [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md) können Sie mithilfe der Anmeldeinformationen für die Domäne auf die Cluster zugreifen.

Die virtuellen Computer (VMs) in HDInsight werden in Ihre bereitgestellte Domäne eingebunden. Alle Dienste, die unter HDInsight ausgeführt werden (Apache Ambari, Apache Hive-Server, Apache Ranger, Apache Spark Thrift-Server usw.), funktionieren daher für den authentifizierten Benutzer nahtlos. Administratoren können dann mithilfe von Apache Ranger starke Autorisierungsrichtlinien erstellen, um eine rollenbasierte Zugriffssteuerung für Ressourcen im Cluster zu ermöglichen.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrieren von HDInsight in Active Directory

Open-Source-Apache Hadoop basiert auf dem Kerberos-Protokoll für die Bereitstellung von Authentifizierung und Sicherheit. Daher sind HDInsight-Clusterknoten mit dem Enterprise-Sicherheitspaket (ESP) in eine mit den Azure AD DS verwaltete Domäne eingebunden. Die Kerberos-Sicherheit ist für die Hadoop-Komponenten auf dem Cluster konfiguriert.

Folgendes wird automatisch erstellt:

- Ein Dienstprinzipal für jede Hadoop-Komponente
- Ein Computerprinzipal für jeden Computer, der in die Domäne eingebunden ist
- Eine Organisationseinheit für jeden Cluster zum Speichern dieser Dienst- und Computerprinzipale

Zusammengefasst benötigen Sie zum Einrichten einer Umgebung Folgendes:

- Eine (von Azure AD DS verwaltete) Active Directory-Domäne. **Der Domänenname darf maximal 39 Zeichen lang sein, um mit Azure HDInsight zu funktionieren.**
- In Azure AD DS aktiviertes sicheres LDAP (LDAPS).
- Ordnungsgemäße Konnektivität vom virtuellen HDInsight-Netzwerk zum virtuellen Azure AD DS-Netzwerk, wenn Sie separate virtuelle Netzwerke für sie wählen. Eine VM innerhalb des virtuellen HDInsight-Netzwerks sollte durch das Peering virtueller Netzwerke uneingeschränkten Zugriff auf Azure AD DS haben. Wenn HDInsight und Azure AD DS im gleichen virtuellen Netzwerk bereitgestellt werden, wird die Konnektivität automatisch bereitgestellt, und es ist keine weitere Aktion erforderlich.

## <a name="set-up-different-domain-controllers"></a>Einrichten anderer Domänencontroller

HDInsight unterstützt derzeit nur Azure AD DS als Hauptdomänencontroller, den der Cluster für die Kerberos-Kommunikation verwendet. Aber auch andere komplexe Active Directory-Setups sind möglich, sofern ein solches Setup dazu führt, dass Azure AD DS für den HDInsight-Zugriff freigegeben wird.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure AD DS](../../active-directory-domain-services/overview.md) stellt eine verwaltete Domäne bereit, die vollständig mit Windows Server Active Directory kompatibel ist. Microsoft übernimmt in einem Hochverfügbarkeitssetup (Highly Available, HA) die Verwaltung, das Patchen und das Überwachen der Domäne. Sie können Ihren Cluster bereitstellen, ohne sich Sorgen um die Verwaltung von Domänencontrollern zu machen.

Benutzer, Gruppen und Kennwörter werden in Azure AD synchronisiert. Die unidirektionale Synchronisierung aus Ihrem Azure AD-Instanz zu Azure AD DS gibt Benutzern die Möglichkeit, sich mit den gleichen Anmeldeinformationen des Unternehmens beim Cluster anzumelden.

Weitere Informationen finden Sie unter [Konfigurieren eines HDInsight-Clusters mit dem Enterprise-Sicherheitspaket mit den Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Lokales Active Directory oder Active Directory auf IaaS-VMs

Wenn Sie eine lokale Active Directory-Instanz oder komplexere Active Directory-Setups für Ihre Domäne haben, können Sie diese Identitäten anhand von Azure AD Connect mit Azure synchronisieren. Anschließend können Sie Azure AD DS auf diesem Active Directory-Mandanten aktivieren.

Da Kerberos auf Kennworthashes angewiesen ist, müssen Sie [die Kennworthashsynchronisierung für Azure AD DS aktivieren](../../active-directory-domain-services/tutorial-create-instance.md).

Wenn Sie einen Verbund mit Active Directory-Verbunddienste (AD FS) verwenden, müssen Sie die Kennworthashsynchronisierung aktivieren. (Eine empfohlene Konfiguration finden Sie in [diesem Video](https://youtu.be/qQruArbu2Ew).) Die Kennworthashsynchronisierung hilft bei der Notfallwiederherstellung, falls Ihre AD FS-Infrastruktur ausfällt, und bietet zudem Schutz vor der Offenlegung von Anmeldeinformationen. Weitere Informationen finden Sie unter [Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

Die alleinige Verwendung des lokalen Active Directory oder Active Directory auf IaaS-VMs ohne Azure AD und die Azure AD DS wird für HDInsight-Cluster mit dem ESP nicht unterstützt.

Wenn ein Verbund verwendet wird und Kennworthashes richtig synchronisiert werden, Sie aber Authentifizierungsfehler erhalten, überprüfen Sie, ob die Cloudkennwortauthentifizierung des PowerShell-Dienstprinzipals aktiviert ist. Ist dies nicht der Fall, müssen Sie eine [HRD-Richtlinie (Home Realm Discovery, Startbereichsermittlung)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) für Ihren Azure AD-Mandanten festlegen. So überprüfen Sie die HRD-Richtlinie und legen sie fest

1. Installieren Sie die Vorschauversion des [Azure AD PowerShell-Moduls](/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureAD
   ```

2. Stellen Sie mit den Anmeldeinformationen eines globalen Administrators (Mandantenadministrators) eine Verbindung her.

   ```powershell
   Connect-AzureAD
   ```

3. Überprüfen Sie, ob der Dienstprinzipal „Microsoft Azure PowerShell“ bereits erstellt wurde.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Wenn der Dienstprinzipal nicht vorhanden ist, erstellen Sie ihn.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Erstellen Sie die Richtlinie, und fügen Sie sie an den Dienstprinzipal an.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId

    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren eines HDInsight-Clusters mit dem Enterprise-Sicherheitspaket mithilfe der Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
- [Konfigurieren von Apache Hive-Richtlinien für HDInsight-Cluster mit ESP](apache-domain-joined-run-hive.md)
- [Verwalten von HDInsight-Clustern mit ESP](apache-domain-joined-manage.md)