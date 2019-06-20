---
title: ESP-Konfiguration (Enterprise-Sicherheitspaket) mithilfe von Azure Active Directory Domain Services – Azure HDInsight
description: Erfahren Sie mehr über das Einrichten und Konfigurieren eines HDInsight-Clusters Enterprise-Sicherheitspaket mithilfe von Azure Active Directory Domain Services.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/23/2019
ms.openlocfilehash: e1bc99cdc089050fbfa931bbbc7b9a6a316a3a75
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240176"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurieren eines HDInsight-Clusters mit Enterprise-Sicherheitspaket (Enterprise Security Package, ESP) mithilfe von Azure Active Directory Domain Services

ESP-Cluster ermöglichen Mehrbenutzerzugriff in Azure HDInsight-Clustern. HDInsight-Cluster mit ESP sind mit einer Domäne verbunden, sodass Domänenbenutzer ihre Domänenanmeldeinformationen zum Authentifizieren beim Cluster verwenden und Big Data-Aufträge ausführen können.

In diesem Artikel erfahren Sie, wie Sie einen HDInsight-Cluster mit ESP mit Azure Active Directory Domain Services (Azure AD DS) einrichten und konfigurieren.

> [!NOTE]  
> ESP ist in HDInsight 3.6 und 4.0 für Clustertypen allgemein verfügbar: Apache Spark, Interactive, Apache Hadoop und HBase. ESP für den Clustertyp Apache Kafka befindet sich in der Vorschauphase.

## <a name="enable-azure-ad-ds"></a>Aktivieren von Azure AD DS

> [!NOTE]  
> Nur Mandantenadministratoren verfügen über die Berechtigungen zum Aktivieren von Azure AD DS. Wenn Azure Data Lake Storage (ADLS) Gen 1 oder Gen 2 als Clusterspeicher verwendet wird, müssen Sie die Multi-Factor Authentication (MFA) nur für Benutzer deaktivieren, die mit grundlegenden Kerberos-Authentifizierungen auf den Cluster zugreifen müssen. Sie können [vertrauenswürdige IP-Adressen](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) oder [bedingten Zugriff](../../active-directory/conditional-access/overview.md) verwenden, um MFA nur für bestimmte Benutzer zu deaktivieren, wenn diese auf den VNET-IP-Adressbereich des HDInsight-Clusters zugreifen. Bei Verwendung von bedingtem Zugriff muss der AD-Dienstendpunkt im HDInsight-VNET aktiviert sein.
>
> Wenn der Clusterspeicher Azure Blob Storage (WASB) ist, deaktivieren Sie MFA nicht.

Das Aktivieren von AD DS ist eine Voraussetzung zum Erstellen eines HDInsight-Clusters mit ESP. Weitere Informationen finden Sie unter [Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals](../../active-directory-domain-services/create-instance.md). 

Wenn Azure AD DS aktiviert ist, beginnen alle Benutzer und Objekte standardmäßig mit der Synchronisierung von Azure Active Directory (AAD) zu Azure AD DS. Die Dauer des Synchronisierungsvorgangs hängt von der Anzahl von Objekten in Azure AD ab. Die Synchronisierung kann bei Hunderttausenden von Objekten einige Tage dauern. 

Sie können auswählen, nur die Gruppen zu synchronisieren, die Zugriff auf die HDInsight-Cluster benötigen. Diese Option, nur bestimmte Gruppen zu synchronisieren, wird als *bereichsbezogene Synchronisierung* bezeichnet. Anweisungen finden Sie unter [Konfigurieren der bereichsbezogenen Synchronisierung von Azure AD mit Ihrer verwalteten Domäne](../../active-directory-domain-services/scoped-synchronization.md).

Wenn Sie Secure LDAP aktivieren, geben Sie den Domänennamen im Antragstellernamen und im alternativen Antragstellernamen im Zertifikat ein. Wenn Ihr Domänenname beispielsweise *contoso100.onmicrosoft.com* lautet, stellen Sie sicher, dass der genaue Name im Antragstellernamen und im alternativen Antragstellernamen des Zertifikats vorhanden ist. Weitere Informationen finden Sie unter [Konfigurieren von sicherem LDAP für eine durch Azure AD DS verwaltete Domäne](../../active-directory-domain-services/configure-ldaps.md). Nachfolgend sehen Sie ein Beispiel zum Erstellen eines selbstsignierten Zertifikats mit dem Domänennamen (*contoso100.onmicrosoft.com*) im Antragstellernamen und im alternativen Antragstellernamen (DnsName):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Überprüfen des AD DS-Integritätsstatus
Zeigen Sie den Integritätsstatus Ihrer Azure Active Directory Domain Services an, indem Sie **Integrität** unter der Kategorie **Verwalten** auswählen. Stellen Sie sicher, dass der Status von AD DS grün (wird ausgeführt) und die Synchronisierung abgeschlossen ist.

![Integrität von Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Erstellen und Autorisieren einer verwalteten Identität

Eine **benutzerseitig zugewiesene verwaltete Identität** wird verwendet, um Domänendienstvorgänge zu vereinfachen und zu schützen. Wenn Sie der verwalteten Identität die Rolle „HDInsight-Domänendienste: Mitwirkender“ zuweisen, kann sie Domänendienstvorgänge lesen, erstellen, ändern und löschen. Bestimmte Domänendienstvorgänge wie das Erstellen von Organisationseinheiten und Dienstprinzipalen sind für das HDInsight-Enterprise-Sicherheitspaket erforderlich. Verwaltete Identitäten können in jedem Abonnement erstellt werden. Weitere allgemeine Informationen zu verwalteten Identitäten finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md). Weitere Informationen zur Funktionsweise verwalteter Identitäten in Azure HDInsight finden Sie unter [Verwaltete Identitäten in Azure HDInsight](../hdinsight-managed-identities.md).

Erstellen Sie zum Einrichten von ESP-Clustern eine benutzerseitig zugewiesene verwaltete Identität, falls noch keine vorhanden ist. Die genaue Vorgehensweise finden Sie unter [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer benutzerseitig zugewiesenen verwalteten Identität über das Azure-Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Weisen Sie anschließend der verwalteten Identität die Rolle **HDInsight-Domänendienste: Mitwirkender** in der Azure AD DS-Zugriffssteuerung zu. (Für diese Rollenzuweisung sind AAD DS-Administratorrechte erforderlich.)

![Azure Active Directory Domain Services-Zugriffssteuerung](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Die Zuweisung der Rolle **HDInsight-Domänendienste: Mitwirkender** stellt sicher, dass die Identität über den richtigen Zugriff (im Auftrag von) verfügt, um Domänendienstvorgänge (etwa Erstellen oder Löschen von Organisationseinheiten) in der AAD DS-Domäne auszuführen.

Wenn die verwaltete Identität erstellt und der richtigen Rolle zugewiesen wurde, kann der AD DS-Administrator festlegen, wer diese verwaltete Identität verwenden darf. Um Benutzer für die verwaltete Identität einzurichten, muss der Administrator die verwaltete Identität im Portal auswählen und dann unter **Übersicht** auf **Zugriffssteuerung (IAM)** klicken. Weisen Sie dann auf der rechten Seite den Benutzern oder Gruppen, die HDInsight ESP-Cluster erstellen möchten, die Rolle **Operator für verwaltete Identität** zu. Der AAD-DS-Administrator kann diese Rolle z.B. der Gruppe **MarketingTeam** für die verwaltete Identität **sjmsi** zuweisen, wie in der folgenden Abbildung dargestellt. Dadurch wird sichergestellt, dass die entsprechenden Personen in der Organisation Zugriff auf diese verwaltete Identität haben, um ESP-Cluster zu erstellen.

![Rollenzuweisung „Operator für verwaltete Identität“ in HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Überlegungen zum Netzwerkbetrieb

> [!NOTE]  
> Azure AD DS muss in einem Azure Resource Manager (ARM) basierten VNET bereitgestellt werden. Klassische virtuelle Netzwerke werden für Azure AD-DS nicht unterstützt. Weitere Details finden Sie unter [Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals](../../active-directory-domain-services/active-directory-ds-getting-started-network.md).

Wenn Sie AD DS aktiviert haben, wird ein lokaler DNS-Server auf den Azure-VMs ausgeführt. Konfigurieren Sie Ihr virtuelles AD DS-Netzwerk (VNET), um diese benutzerdefinierten DNS-Server zu verwenden. Um die richtigen IP-Adressen zu finden, wählen Sie **Eigenschaften** unter der Kategorie **Verwalten** aus, und sehen Sie sich die unter **IP-Adresse im virtuellen Netzwerk** aufgeführten IP-Adressen an.

![Suchen von IP-Adressen für lokale DNS-Server](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Ändern Sie die Konfiguration der DNS-Server im AD DS-VNET, um diese benutzerdefinierten IP-Adressen zu verwenden, indem Sie in der Kategorie **Einstellungen** **DNS-Server** auswählen. Klicken Sie dann auf das Optionsfeld neben **Benutzerdefiniert**, geben Sie die erste IP-Adresse in das Textfeld unten ein, und klicken Sie auf **Speichern**. Gehen Sie genauso vor, um weitere IP-Adressen hinzuzufügen.

![Aktualisieren der VNET-DNS-Konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Es ist einfacher, die Azure AD DS-Instanz und den HDInsight-Cluster im gleichen virtuellen Azure-Netzwerk zu platzieren. Wenn Sie verschiedene VNETs verwenden möchten, müssen Sie ein Peering für diese virtuellen Netzwerke ausführen, sodass der Domänencontroller für HDI-VMs sichtbar ist. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken](../../virtual-network/virtual-network-peering-overview.md). 

Nachdem die VNETs durchsucht wurden, konfigurieren Sie das HDInsight-VNET so, dass es einen benutzerdefinierten DNS-Server verwendet, und geben Sie die privaten AD DS-IP-Adressen als DNS-Serveradressen ein. Wenn beide VNETs die gleichen DNS-Server verwenden, wird Ihr benutzerdefinierter Domänenname in die richtige IP-Adresse aufgelöst und ist über HDInsight erreichbar. Wenn Ihr Domänenname beispielsweise „contoso.com“ lautet, muss nach diesem Schritt das Pingen von „contoso.com“ in die richtige AD DS-IP-Adresse aufgelöst werden. 

![Konfigurieren von benutzerdefinierten DNS-Servern für VNET mit Peering](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Wenn Sie in Ihrem HDInsight-Subnetz Regeln für Netzwerksicherheitsgruppen (NSG) verwenden, müssen Sie die [erforderlichen IP-Adressen](../hdinsight-extend-hadoop-virtual-network.md) für den eingehenden und ausgehenden Datenverkehr zulassen. 

**Um zu testen**, ob Ihr Netzwerk korrekt eingerichtet ist, verknüpfen Sie eine Windows-VM mit dem HDInsight-VNET/Subnetz, pingen Sie den Domänennamen (er sollte sich in eine IP-Adresse auflösen), und führen Sie dann **ldp.exe** aus, um auf die AD DS-Domäne zuzugreifen. Verknüpfen Sie dann diese **Windows-VM mit der Domäne zum Bestätigen**, dass alle erforderlichen RPC-Aufrufe zwischen Client und Server erfolgreich sind. Sie können auch **nslookup** verwenden, um den Netzwerkzugriff auf Ihr Speicherkonto oder eine beliebige externe Datenbank zu bestätigen (z.B. externer Hive-Metastore oder Ranger-Datenbank).
Achten Sie darauf, dass alle [benötigten Ports](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) in der Whitelist der Regeln der Netzwerksicherheitsgruppe (NSG) des AAD DS-Subnetzes enthalten sind, wenn AAD DS durch eine NSG gesichert ist. Wenn der Domänenbeitritt dieses virtuellen Windows-Computers erfolgreich ist, können Sie mit dem nächsten Schritt fortfahren und ESP-Cluster erstellen.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Erstellen eines HDInsight-Clusters mit ESP

Wenn Sie die vorherigen Schritte korrekt festgelegt haben, erstellen Sie im nächsten Schritt den HDInsight-Cluster mit aktiviertem ESP. Beim Erstellen eines HDInsight-Clusters können Sie das Enterprise-Sicherheitspaket auf der Registerkarte **Benutzerdefiniert** aktivieren. Wenn Sie die Bereitstellung über eine Azure Resource Manager-Vorlage ausführen möchten, laden Sie im Portal einmalig die vorausgefüllte Vorlage auf der letzten Seite von „Zusammenfassung“ herunter, um diese später wieder verwenden zu können.

> [!NOTE]  
> Die ersten sechs Zeichen des ESP-Clusternamens müssen in Ihrer Umgebung eindeutig sein. Bei mehreren ESP-Clustern in verschiedenen VNETs müssen Sie beispielsweise eine Benennungskonvention wählen, die sicherstellt, dass die ersten sechs Zeichen des Clusternamens eindeutig sind.

![Azure HDInsight Enterprise-Sicherheitspaket – Domänenüberprüfung](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Sobald Sie ESP aktivieren, werden häufige Fehlkonfigurationen im Zusammenhang mit Azure AD DS automatisch erkannt und validiert. Nachdem Sie diese Fehler behoben haben, können Sie mit dem nächsten Schritt fortfahren: 

![Azure HDInsight Enterprise-Sicherheitspaket – fehlgeschlagene Domänenüberprüfung](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Zum Erstellen eines HDInsight-Clusters mit ESP müssen Sie die folgende Parameter angeben:

- **Clusteradministrator**: Wählen Sie einen Administrator für Ihren Cluster aus Ihrem synchronisierten Azure AD DS aus. Dieses Domänenkonto muss bereits synchronisiert und in Azure AD DS verfügbar sein.

- **Clusterzugriffsgruppen**: Die Sicherheitsgruppen, deren Benutzer Sie synchronisieren möchten und deren Benutzer Zugriff auf den Cluster haben sollen, sollten in Azure AD DS verfügbar sein. Zum Beispiel die Gruppe HiveUsers. Weitere Informationen dazu finden Sie in [Erstellen einer Gruppe in Azure Active Directory und Hinzufügen von Mitgliedern](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS-URL**: Beispiel: ldaps://contoso.com:636.

Der folgende Screenshot zeigt eine erfolgreiche Konfigurationen im Azure-Portal:

![Konfiguration von Active Directory Domain Services für Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Die von Ihnen erstellte verwaltete Identität kann beim Erstellen eines neuen Clusters aus dem Dropdownmenü für die vom Benutzer zugewiesene verwaltete Identität ausgewählt werden.

![Konfiguration von Active Directory Domain Services für Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Konfigurieren von Hive-Richtlinien und zum Ausführen von Hive-Abfragen finden Sie unter [Konfigurieren von Apache Hive-Richtlinien in HDInsight mit dem Enterprise-Sicherheitspaket](apache-domain-joined-run-hive.md).
* Informationen zum Verwenden von HDInsight-Clustern mit ESP finden Sie unter [Verwenden von SSH mit Linux-basiertem Apache Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
