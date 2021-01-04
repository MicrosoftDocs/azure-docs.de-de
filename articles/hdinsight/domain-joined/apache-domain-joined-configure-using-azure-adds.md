---
title: Konfigurieren von Clustern für die Active Directory-Integration
titleSuffix: Azure HDInsight
description: Erfahren Sie, wie Sie einen in Active Directory integrierten HDInsight-Cluster mithilfe von Azure Active Directory Domain Services und dem Feature „Enterprise-Sicherheitspaket“ einrichten und konfigurieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seodec18,seoapr2020, contperf-fy21q2
ms.date: 10/30/2020
ms.openlocfilehash: 248d909e633607271aec7c2c9b8a373f111f7d98
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031481"
---
# <a name="configure-hdinsight-clusters-for-active-directory-integration-with-enterprise-security-package"></a>Konfigurieren von HDInsight-Clustern für die Active Directory-Integration mit dem Enterprise-Sicherheitspaket

In diesem Artikel erfahren Sie, wie Sie einen in Active Directory integrierten HDInsight-Cluster erstellen und konfigurieren, indem Sie ein Feature namens „Enterprise-Sicherheitspaket (ESP)“, Azure Active Directory Domain Services (Azure AD DS) und Ihr bereits vorhandenes lokales Active Directory verwenden.

Ein Tutorial zum Einrichten und Konfigurieren einer Domäne in Azure und zum Erstellen eines ESP-aktivierten Clusters finden Sie unter [Erstellen und Konfigurieren von Clustern mit dem Enterprise-Sicherheitspaket in Azure HDInsight](apache-domain-joined-create-configure-enterprise-security-cluster.md).

## <a name="background"></a>Hintergrund

Das Enterprise-Sicherheitspaket (ESP) stellt die Active Directory-Integration für Azure HDInsight bereit. Diese Integration ermöglicht es Domänenbenutzern, ihre Domänenanmeldeinformationen für die Authentifizierung bei HDInsight-Clustern und zum Ausführen von Big Data-Aufträgen zu verwenden.

> [!NOTE]  
> ESP ist in HDInsight 3.6 und 4.0 für diese Clustertypen allgemein verfügbar: Apache Spark, Interactive, Hadoop und HBase. ESP für den Clustertyp Apache Kafka befindet sich in der Vorschauphase mit bestmöglichem Support. ESP-Cluster, die vor dem Datum der allgemeinen Verfügbarkeit von ESP (1. Oktober 2018) erstellt wurden, werden nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie einen ESP-aktivierten HDInsight-Cluster erstellen können, müssen einige Voraussetzungen erfüllt sein:

- Aktivieren Sie Azure AD DS.
- Überprüfen Sie den Integritätsstatus von Azure AD DS, um sicherzustellen, dass die Synchronisierung abgeschlossen ist.
- Erstellen und autorisieren Sie eine verwaltete Identität.
- Schließen Sie die Einrichtung des Netzwerks für DNS und verwandte Probleme ab.

Jeder dieser Punkte wird im Folgenden ausführlich erörtert.

### <a name="enable-azure-ad-ds"></a>Aktivieren von Azure AD DS

Das Aktivieren von Azure AD DS ist eine Voraussetzung zum Erstellen eines HDInsight-Clusters mit ESP. Weitere Informationen finden Sie unter [Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals](../../active-directory-domain-services/tutorial-create-instance.md).

Wenn Azure AD DS aktiviert ist, beginnen alle Benutzer und Objekte standardmäßig mit der Synchronisierung von Azure Active Directory (Azure AD) zu Azure AD DS. Die Dauer des Synchronisierungsvorgangs hängt von der Anzahl von Objekten in Azure AD ab. Die Synchronisierung kann bei Hunderttausenden von Objekten einige Tage dauern.

Der mit Azure AD DS verwendete Domänenname darf maximal 39 Zeichen lang sein, da er ansonsten nicht mit HDInsight verwendet werden kann.

Sie können auswählen, nur die Gruppen zu synchronisieren, die Zugriff auf die HDInsight-Cluster benötigen. Diese Option, nur bestimmte Gruppen zu synchronisieren, wird als *bereichsbezogene Synchronisierung* bezeichnet. Anweisungen finden Sie unter [Konfigurieren der bereichsbezogenen Synchronisierung von Azure AD mit Ihrer verwalteten Domäne](../../active-directory-domain-services/scoped-synchronization.md).

Wenn Sie Secure LDAP aktivieren, geben Sie den Domänennamen im Ausstellernamen an. Geben Sie außerdem den alternativen Ausstellernamen im Zertifikat an. Wenn Ihr Domänenname beispielsweise *contoso100.onmicrosoft.com* lautet, stellen Sie sicher, dass der genaue Name im Antragstellernamen und im alternativen Antragstellernamen des Zertifikats vorhanden ist. Weitere Informationen finden Sie unter [Konfigurieren von sicherem LDAP (LDAPS) für eine durch Azure AD Domain Services verwaltete Domäne](../../active-directory-domain-services/tutorial-configure-ldaps.md).

Im folgenden Beispiel wird ein selbstsigniertes Zertifikat erstellt. Der Domänenname *contoso100.onmicrosoft.com* befindet sich sowohl in `Subject` (Antragstellername) als auch in `DnsName` (alternativer Antragstellername).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

> [!NOTE]  
> Nur Mandantenadministratoren verfügen über die Berechtigungen zum Aktivieren von Azure AD DS. Wenn Azure Data Lake Storage Gen1 oder Gen2 als Clusterspeicher verwendet wird, müssen Sie die Azure AD Multi-Factor Authentication nur für Benutzer deaktivieren, die mit grundlegenden Kerberos-Authentifizierungen auf den Cluster zugreifen müssen.
>
> Sie können [vertrauenswürdige IP-Adressen](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) oder [bedingten Zugriff](../../active-directory/conditional-access/overview.md) verwenden, um Multi-Factor Authentication *nur* für bestimmte Benutzer zu deaktivieren, wenn diese auf den IP-Adressbereich für das virtuelle Netzwerk des HDInsight-Clusters zugreifen. Bei Verwendung von bedingtem Zugriff muss der Active Directory-Dienstendpunkt im virtuellen HDInsight-Netzwerk aktiviert sein.
>
> Deaktivieren Sie Multi-Factor Authentication nicht, wenn der Clusterspeicher Azure Blob Storage ist.

### <a name="check-azure-ad-ds-health-status"></a>Überprüfen des AD DS-Integritätsstatus

Zeigen Sie den Integritätsstatus von den Azure Active Directory Domain Services an, indem Sie **Integrität** unter der Kategorie **Verwalten** auswählen. Stellen Sie sicher, dass der Status von AD DS grün (wird ausgeführt) und die Synchronisierung abgeschlossen ist.

![Azure AD DS-Integrität](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

### <a name="create-and-authorize-a-managed-identity"></a>Erstellen und Autorisieren einer verwalteten Identität

Sie können eine *benutzerseitig zugewiesene verwaltete Identität* verwenden, um Domänendienstvorgänge zu vereinfachen und zu schützen. Wenn Sie der verwalteten Identität die Rolle **HDInsight-Domänendienste: Mitwirkender** zuweisen, kann sie Domänendienstvorgänge lesen, erstellen, ändern und löschen.

Bestimmte Domänendienstvorgänge wie das Erstellen von Organisationseinheiten und Dienstprinzipalen sind für das HDInsight-Enterprise-Sicherheitspaket erforderlich. Sie können in jedem Abonnement verwaltete Identitäten erstellen. Weitere allgemeine Informationen zu verwalteten Identitäten finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md). Weitere Informationen zur Funktionsweise verwalteter Identitäten in Azure HDInsight finden Sie unter [Verwaltete Identitäten in Azure HDInsight](../hdinsight-managed-identities.md).

Erstellen Sie zum Einrichten von ESP-Clustern eine benutzerseitig zugewiesene verwaltete Identität, falls noch keine vorhanden ist. Siehe [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Weisen Sie der verwalteten Identität anschließend in der **Zugriffssteuerung** für Azure AD DS die Rolle **HDInsight-Domänendienste: Mitwirkender** zu. Sie benötigen Azure AD DS-Administratorrechte, um diese Rollenzuweisung vorzunehmen.

![Azure Active Directory Domain Services-Zugriffssteuerung](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Die Zuweisung der Rolle **HDInsight-Domänendienste: Mitwirkender** stellt sicher, dass die Identität über den richtigen Zugriff (`on behalf of`) verfügt, um Domänendienstvorgänge in der Azure AD DS-Domäne auszuführen. Diese Vorgänge umfassen das Erstellen und Löschen von Organisationseinheiten.

Nachdem der verwalteten Identität die Rolle zugewiesen wurde, verwaltet der Azure AD DS-Administrator, wer sie verwenden kann. Zuerst wählt der Administrator die verwaltete Identität im Portal aus. Dann wählt er unter **Übersicht** die Option **Zugriffssteuerung (IAM)** aus. Anschließend weist der Administrator den Benutzern oder Gruppen die Rolle **Operator für verwaltete Identität** zu, die ESP-Cluster erstellen möchten.

Beispielsweise kann der Azure AD DS-Administrator diese Rolle der Gruppe **MarketingTeam** für die verwaltete Identität **sjmsi** zuweisen. In der folgenden Abbildung ist ein Beispiel angegeben. Diese Zuweisung stellt sicher, dass die richtigen Personen in der Organisation die verwaltete Identität zum Erstellen von ESP-Clustern verwenden können.

![Rollenzuweisung „Operator für verwaltete Identität“ in HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

### <a name="network-configuration"></a>Netzwerkkonfiguration

> [!NOTE]  
> Azure AD DS muss in einem Azure Resource Manager-basierten virtuellen Netzwerk bereitgestellt werden. Klassische virtuelle Netzwerke werden für Azure AD DS nicht unterstützt. Weitere Informationen finden Sie unter [Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Aktivieren Sie Azure AD DS. Dies führt dazu, dass ein lokaler DNS-Server (Domain Name System) auf den Azure Directory-VMs ausgeführt wird. Konfigurieren Sie Ihr virtuelles Azure AD DS-Netzwerk, um diese benutzerdefinierten DNS-Server zu verwenden. Wählen Sie **Eigenschaften** in der Kategorie **Verwalten** aus, und sehen Sie unter **IP-Adresse im virtuellen Netzwerk** nach, um die richtigen IP-Adressen zu finden.

![Suchen von IP-Adressen für lokale DNS-Server](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Ändern Sie die Konfiguration der DNS-Server im virtuellen Azure AD DS-Netzwerk. Wählen Sie zur Verwendung dieser benutzerdefinierten IP-Adressen in der Kategorie **Einstellungen** die Option **DNS-Server** aus. Wählen Sie dann die Option **Benutzerdefiniert** aus, geben Sie die erste IP-Adresse in das Textfeld ein, und wählen Sie **Speichern** aus. Gehen Sie genauso vor, um weitere IP-Adressen hinzuzufügen.

![Aktualisieren der DNS-Konfiguration des virtuellen Netzwerks](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Es ist einfacher, die Azure AD DS-Instanz und den HDInsight-Cluster im gleichen virtuellen Azure-Netzwerk zu platzieren. Wenn Sie verschiedene virtuelle Netzwerke verwenden möchten, müssen Sie ein Peering für diese virtuellen Netzwerke ausführen, sodass der Domänencontroller für HDInsight-VMs sichtbar ist. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken](../../virtual-network/virtual-network-peering-overview.md).

Nachdem die virtuellen Netzwerke über ein Peering verknüpft wurden, konfigurieren Sie das virtuelle HDInsight-Netzwerk so, dass es einen benutzerdefinierten DNS-Server verwendet. Geben Sie dann die privaten Azure AD DS-IP-Adressen als DNS-Serveradressen ein. Wenn beide virtuellen Netzwerke die gleichen DNS-Server verwenden, wird Ihr benutzerdefinierter Domänenname in die richtige IP-Adresse aufgelöst und ist über HDInsight erreichbar. Wenn Ihr Domänenname also beispielsweise `contoso.com` lautet, muss `ping contoso.com` nach diesem Schritt in die richtige Azure AD DS-IP-Adresse aufgelöst werden.

![Konfigurieren von benutzerdefinierten DNS-Servern für ein virtuelles Netzwerk mit Peering](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Wenn Sie in Ihrem HDInsight-Subnetz Regeln für Netzwerksicherheitsgruppen (NSG) verwenden, müssen Sie die [erforderlichen IP-Adressen](../hdinsight-management-ip-addresses.md) für den eingehenden und ausgehenden Datenverkehr zulassen.

Fügen Sie zum Testen Ihrer Netzwerkeinrichtung eine Windows-VM in das virtuelle HDInsight-Netzwerk oder -Subnetz ein, und pingen Sie den Domänennamen. Dies sollte in eine IP-Adresse aufgelöst werden. Führen Sie **ldp.exe** aus, um auf die Azure AD DS-Domäne zuzugreifen. Verknüpfen Sie dann diese Windows-VM mit der Domäne, um zu bestätigen, dass alle erforderlichen RPC-Aufrufe zwischen Client und Server erfolgreich sind.

Verwenden Sie **nslookup**, um den Netzwerkzugriff auf Ihr Speicherkonto zu bestätigen. Oder bestätigen Sie den Zugriff auf eine externe Datenbank, die Sie möglicherweise verwenden (beispielsweise einen externen Hive-Metastore oder eine Ranger-DB). Stellen Sie sicher, dass alle [erforderlichen Ports](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) in den NSG-Regeln des Azure AD DS-Subnetzes zugelassen sind, wenn zum Schutz von Azure AD DS eine Netzwerksicherheitsgruppe eingesetzt wird. Wenn der Domänenbeitritt dieses virtuellen Windows-Computers erfolgreich ist, können Sie mit dem nächsten Schritt fortfahren und ESP-Cluster erstellen.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Erstellen eines HDInsight-Clusters mit ESP

Wenn Sie die vorherigen Schritte korrekt festgelegt haben, erstellen Sie im nächsten Schritt den HDInsight-Cluster mit aktiviertem ESP. Beim Erstellen eines HDInsight-Clusters können Sie das Enterprise-Sicherheitspaket auf der Registerkarte **Sicherheit + Netzwerkbetrieb** aktivieren. Verwenden Sie für eine Azure Resource Manager-Vorlage für die Bereitstellung einmalig die Portalbenutzeroberfläche. Laden Sie anschließend die vorausgefüllte Vorlage auf der Seite **Überprüfen + erstellen** für die zukünftige Wiederverwendung herunter.

Sie können das Feature [HDInsight-Identitätsbroker](identity-broker.md) während der Clustererstellung aktivieren. Mit dem Feature „Identitätsbroker“ können Sie sich bei Ambari anmelden, indem Sie Multi-Factor Authentication verwenden und die erforderlichen Kerberos-Tickets erhalten, ohne dass Kennworthashes in Azure AD DS erforderlich sind.

> [!NOTE]  
> Die ersten sechs Zeichen des ESP-Clusternamens müssen in Ihrer Umgebung eindeutig sein. Wählen Sie beispielsweise bei mehreren ESP-Clustern in verschiedenen virtuellen Netzwerken eine Namenskonvention aus, die sicherstellt, dass die ersten sechs Zeichen des Clusternamens eindeutig sind.

![Domänenüberprüfung für das Azure HDInsight Enterprise-Sicherheitspaket](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Sobald Sie ESP aktivieren, werden häufige Fehlkonfigurationen im Zusammenhang mit Azure AD DS automatisch erkannt und validiert. Nachdem Sie diese Fehler behoben haben, können Sie mit dem nächsten Schritt fortfahren.

![Fehlgeschlagene Domänenüberprüfung beim Azure HDInsight Enterprise-Sicherheitspaket](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Zum Erstellen eines HDInsight-Clusters mit ESP müssen Sie die folgenden Parameter angeben:

* **Clusteradministrator**: Wählen Sie aus Ihrer synchronisierten Azure AD DS-Instanz einen Administrator für Ihren Cluster aus. Dieses Domänenkonto muss bereits synchronisiert und in Azure AD DS verfügbar sein.

* **Clusterzugriffsgruppen**: Die Sicherheitsgruppen, deren Benutzer Sie synchronisieren möchten und deren Benutzer Zugriff auf den Cluster haben sollen, sollten in Azure AD DS verfügbar sein. Ein Beispiel hierfür ist die Gruppe HiveUsers. Weitere Informationen dazu finden Sie in [Erstellen einer Gruppe in Azure Active Directory und Hinzufügen von Mitgliedern](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **LDAPS-URL**: z. B. `ldaps://contoso.com:636`.

Die von Ihnen erstellte verwaltete Identität kann beim Erstellen eines neuen Clusters aus der Dropdownliste für die **benutzerseitig zugewiesene verwaltete Identität** ausgewählt werden.

![Verwaltete Active Directory Domain Services-Identität für Azure HDInsight-Sicherheitspaket (ESP)](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Konfigurieren von Hive-Richtlinien und zum Ausführen von Hive-Abfragen finden Sie unter [Konfigurieren von Apache Hive-Richtlinien in HDInsight mit dem Enterprise-Sicherheitspaket](apache-domain-joined-run-hive.md).
* Informationen zum Verwenden von HDInsight-Clustern mit ESP finden Sie unter [Verwenden von SSH mit Linux-basiertem Apache Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
