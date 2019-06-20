---
title: Verschieben von Azure-Ressourcen in ein neues Abonnement oder eine neue Ressourcengruppe | Microsoft-Dokumentation
description: Verwenden Sie Azure Resource Manager, um Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement verschieben.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: tomfitz
ms.openlocfilehash: bb7715a7eaa2cb499ed80b78130571f315ef0c95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083540"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement

In diesem Artikel wird erklärt, wie Sie Azure-Ressourcen entweder in ein anderes Azure-Abonnement oder in eine andere Ressourcengruppe im gleichen Abonnement verschieben. Sie können das Azure-Portal, Azure PowerShell, die Azure-Befehlszeilenschnittstelle oder die REST-API verwenden, um Ressourcen zu verschieben.

Beim Verschieben wird sowohl die Quell- als auch die Zielgruppe gesperrt. Schreib- und Löschvorgänge in den Ressourcengruppen werden bis zum Abschluss der Verschiebung blockiert. Diese Sperre bedeutet, dass Sie in Ressourcengruppen keinen Ressourcen hinzufügen, aktualisieren oder löschen können. Es heißt aber nicht, dass die Ressourcen eingefroren sind. Wenn Sie beispielsweise eine SQL Server-Instanz und ihre Datenbank in eine neue Ressourcengruppe verschieben, weist eine Anwendung, die die Datenbank nutzt, keine Ausfallzeiten auf. Sie hat weiterhin Lese- und Schreibzugriff auf die Datenbank.

Wenn Sie ein Ressource verschieben, wird sie nur in eine neue Ressourcengruppe verschoben. Durch den Vorgang des Verschiebens kann der Speicherort der Ressource nicht geändert werden. Die neue Ressourcengruppe hat möglicherweise einen anderen Speicherort, das heißt jedoch nicht, dass der Speicherort der Ressource geändert wird.

> [!NOTE]
> In diesem Artikel wird beschrieben, wie Sie Ressourcen zwischen vorhandenen Azure-Abonnements verschieben. Wenn Sie ein Upgrade für Ihr Azure-Abonnement durchführen möchten (z.B. Wechsel vom kostenlosen Abonnement zur nutzungsbasierten Bezahlung), müssen Sie Ihr Abonnement konvertieren.
> * Falls Sie für eine kostenlose Testversion ein Upgrade durchführen möchten, helfen Ihnen die Informationen unter [Aktualisieren Ihrer kostenlosen Testversion oder Ihres Microsoft Imagine Azure-Abonnements auf nutzungsbasierte Bezahlung](..//billing/billing-upgrade-azure-subscription.md) weiter.
> * Informationen zum Ändern eines Kontos mit nutzungsbasierter Bezahlung finden Sie unter [Ändern Ihres Azure-Abonnements mit nutzungsbasierter Bezahlung in ein anderes Angebot](../billing/billing-how-to-switch-azure-offer.md).
> * Wenn Sie das Abonnement nicht konvertieren können, können Sie [eine Azure-Supportanfrage erstellen](../azure-supportability/how-to-create-azure-support-request.md). Wählen Sie **Abonnementverwaltung** als Problemtyp aus.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="when-to-call-azure-support"></a>Kontaktaufnahme mit dem Azure-Support

Sie können die meisten Ressourcen mithilfe der in diesem Artikel gezeigten Self-Service-Vorgänge verschieben. Verwenden Sie die Self-Service-Vorgänge für Folgendes:

* Verschieben von Resource Manager-Ressourcen
* Verschieben von klassischen Ressourcen unter Berücksichtigung der [Einschränkungen bei der klassischen Bereitstellung](#classic-deployment-limitations)

Wenden Sie sich in folgenden Fällen an den [Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview):

* Sie verschieben Ihre Ressourcen in ein neues Azure-Konto (und einen neuen Azure Active Directory-Mandanten) und benötigen Unterstützung für die Anweisungen im vorherigen Abschnitt.
* Verschieben von klassischen Ressourcen, wenn Probleme durch Einschränkungen auftreten

## <a name="services-that-can-be-moved"></a>Dienste, die verschoben werden können

Die folgende Liste enthält eine allgemeine Zusammenfassung von Azure-Diensten, die in eine neue Ressourcengruppe oder ein neues Abonnement verschoben werden können. Eine Liste der Ressourcentypen, die das Verschieben unterstützen, finden Sie unter [Unterstützung des Verschiebevorgangs für Ressourcen](move-support-resources.md).

* Analysis Services
* API Management
* App Service-Apps (Web-Apps) – siehe [App Service-Einschränkungen](#app-service-limitations)
* App Service-Zertifikate – siehe [Einschränkungen von App Service Certificate](#app-service-certificate-limitations)
* Automation – Runbooks müssen in der gleichen Ressourcengruppe wie das Automation-Konto vorhanden sein.
* Azure Active Directory B2C
* Azure Cache for Redis – wenn die Azure Cache for Redis-Instanz mit einem virtuellen Netzwerk konfiguriert ist, kann die Instanz nicht in ein anderes Abonnement verschoben werden. Siehe [Einschränkungen von virtuellen Netzwerken](#virtual-networks-limitations).
* Azure Cosmos DB
* Azure-Daten-Explorer
* Azure Database for MariaDB
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure DevOps: Führen Sie die Schritte unter [Ändern des zur Abrechnung verwendeten Azure-Abonnements](/azure/devops/organizations/billing/change-azure-subscription?view=azure-devops) aus.
* Azure Maps
* Azure Monitor-Protokolle
* Azure Relay
* Azure Stack: Registrierungen
* Batch
* BizTalk Services
* Botdienst
* CDN
* Cloud Services – siehe [Einschränkungen bei der klassischen Bereitstellung](#classic-deployment-limitations)
* Cognitive Services
* Containerregistrierung
* Content Moderator
* Cost Management
* Customer Insights
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Grid
* Event Hubs
* Front Door
* HDInsight-Cluster – siehe [HDInsight-Einschränkungen](#hdinsight-limitations)
* Iot Central
* IoT Hubs
* Key Vault: Für die Datenträgerverschlüsselung verwendete Schlüsseltresore können nicht in Ressourcengruppen im gleichen Abonnement oder zwischen Abonnements verschoben werden.
* Load Balancer: Load Balancer der SKU „Basic“ kann verschoben werden. Load Balancer der SKU „Standard“ kann nicht verschoben werden.
* Logic Apps
* Machine Learning – Machine Learning Studio-Webdienste können in eine Ressourcengruppe im gleichen Abonnement verschoben werden, aber nicht in ein anderes Abonnement. Andere Machine Learning-Ressourcen können über Abonnements hinweg verschoben werden.
* Verwaltete Datenträger – Verwaltete Datenträger in Verfügbarkeitszonen können nicht in ein anderes Abonnement verschoben werden.
* Media Services
* Überwachen: Stellen Sie sicher, dass das Verschieben in neue Abonnements nicht die [Abonnementkontingente](../azure-subscription-service-limits.md#monitor-limits) überschreitet.
* Notification Hubs
* Operational Insights
* Operations Management
* Dashboards im Portal
* Power BI – sowohl Power BI Embedded als auch Power BI-Arbeitsbereichssammlung
* Öffentliche IP-Adresse: Die öffentliche IP-Adresse der SKU „Basic“ kann verschoben werden. Öffentliche IP-Adresse der SKU „Standard“ kann nicht verschoben werden.
* Recovery Services-Tresor – registrieren Sie sich für eine [Vorschauversion](#recovery-services-limitations).
* SAP HANA in Azure
* Scheduler
* Azure Search: Es ist nicht möglich, mehrere Search-Ressourcen in verschiedenen Regionen gleichzeitig zu verschieben. Verschieben Sie diese stattdessen in mehreren Vorgängen.
* Service Bus
* Service Fabric
* Service Fabric Mesh
* SignalR Service
* Storage – Speicherkonten in verschiedenen Regionen können nicht im selben Vorgang verschoben werden. Stattdessen müssen Sie für jede Region einen separaten Vorgang durchführen.
* Speicher (klassisch) – siehe [Einschränkungen bei der klassischen Bereitstellung](#classic-deployment-limitations)
* Speichersynchronisierungsdienst
* Stream Analytics – Stream Analytics-Aufträge können nicht verschoben werden, wenn sie ausgeführt werden.
* SQL-Datenbank-Server – die Datenbank und der Server müssen sich in derselben Ressourcengruppe befinden. Wenn Sie eine SQL Server-Instanz verschieben, werden auch alle ihre Datenbanken verschoben. Dieses Verhalten gilt für Azure SQL-Datenbank und Azure SQL Data Warehouse-Datenbanken.
* Time Series Insights
* Traffic Manager
* Virtual Machines – siehe [Einschränkungen von virtuellen Computern](#virtual-machines-limitations)
* Virtual Machines (klassisch) – siehe [Einschränkungen bei der klassischen Bereitstellung](#classic-deployment-limitations)
* VM-Skalierungsgruppen – siehe [Einschränkungen von virtuellen Computern](#virtual-machines-limitations)
* Virtual Network – siehe [Einschränkungen von virtuellen Netzwerken](#virtual-networks-limitations)
* VPN Gateway

### <a name="services-that-cannot-be-moved"></a>Dienste, die nicht verschoben werden können

Die folgende Liste enthält eine allgemeine Zusammenfassung von Azure-Diensten, die nicht in eine neue Ressourcengruppe oder ein neues Abonnement verschoben werden können. Ausführliche Informationen finden Sie unter [Unterstützung des Verschiebevorgangs für Ressourcen](move-support-resources.md).

* AD Domain Services
* AD Hybrid Health Service
* Application Gateway
* Azure-Datenbankmigration
* Azure Databricks
* Azure Firewall
* Azure Kubernetes Service (AKS)
* Azure Migrate
* Azure NetApp Files
* Certificates – App Service Certificates kann verschoben werden, hochgeladene Zertifikate haben jedoch [Einschränkungen](#app-service-limitations).
* Klassische Anwendungen
* Container Instances
* Container Service
* Data Box
* Dev Spaces
* Dynamics LCS
* ExpressRoute
* Lab-Dienst – Classroom-Labs können nicht in eine neue Ressourcengruppe oder ein neues Abonnement verschoben werden.rschoben werden. DevTest-Labs können in eine neue Ressourcengruppe im gleichen Abonnement verschoben werden, das abonnementübergreifende Verschieben ist jedoch nicht möglich.
* Verwaltete Anwendungen
* Verwaltete Identität – vom Benutzer zugewiesen
* Microsoft Genomics
* Sicherheit
* Site Recovery
* StorSimple-Geräte-Manager
* Virtual Networks (klassisch) – siehe [Einschränkungen bei der klassischen Bereitstellung](#classic-deployment-limitations)

## <a name="limitations"></a>Einschränkungen

In diesem Abschnitt wird erklärt, wie Sie komplizierte Szenarios beim Verschieben von Ressourcen handhaben können. Die Einschränkungen sind:

* [Einschränkungen von virtuellen Computern](#virtual-machines-limitations)
* [Einschränkungen von virtuellen Netzwerken](#virtual-networks-limitations)
* [App Service-Einschränkungen](#app-service-limitations)
* [Einschränkungen von App Service Certificate](#app-service-certificate-limitations)
* [Einschränkungen bei der klassischen Bereitstellung](#classic-deployment-limitations)
* [Einschränkungen von Recovery Services](#recovery-services-limitations)
* [HDInsight-Einschränkungen](#hdinsight-limitations)

### <a name="virtual-machines-limitations"></a>Einschränkungen von virtuellen Computern

Sie können virtuelle Computer mit verwalteten Datenträgern, Images und Momentaufnahmen sowie Verfügbarkeitsgruppen mit virtuellen Computern, die verwaltete Datenträger verwenden, verschieben. Verwaltete Datenträger in Verfügbarkeitszonen können nicht in ein anderes Abonnement verschoben werden.

Folgende Szenarios werden noch nicht unterstützt:

* Virtuelle Computer mit in Key Vault gespeichertem Zertifikat können in eine neue Ressourcengruppe im gleichen Abonnement verschoben werden, das abonnementübergreifende Verschieben ist jedoch nicht möglich.
* Eine Virtual Machine Scale Sets-Instanz mit Load Balancer der Standard-SKU oder einer öffentlichen IP-Adresse der Standard-SKU kann nicht verschoben werden.
* Von Marketplace-Ressourcen erstellte virtuelle Computer, an die Pläne angefügt sind, können nicht ressourcengruppen- oder abonnementübergreifend verschoben werden. Heben Sie die Bereitstellung des virtuellen Computers im aktuellen Abonnement auf, und stellen Sie ihn im neuen Abonnement erneut bereit.
* Virtuelle Computer in einem vorhandenen virtuellen Netzwerk, in dem der Benutzer nicht beabsichtigt, alle Ressourcen im virtuellen Netzwerk zu verschieben.

Verwenden Sie die folgende Problemumgehung, um mit Azure Backup konfigurierte virtuelle Computer zu verschieben:

* Ermitteln Sie den Speicherort Ihres virtuellen Computers.
* Suchen Sie eine Ressourcengruppe mit dem folgenden Namensmuster: `AzureBackupRG_<location of your VM>_1`, z. B. „AzureBackupRG_westus2_1“
* Wenn Sie das Azure-Portal verwenden, aktivieren Sie die Option „Ausgeblendete Typen anzeigen“
* Wenn Sie PowerShell verwenden, verwenden Sie das Cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Wenn Sie die CLI (Befehlszeilenschnittstelle) verwenden, verwenden Sie den Befehl `az resource list -g AzureBackupRG_<location of your VM>_1`
* Suchen Sie nach der Ressource mit dem Typ `Microsoft.Compute/restorePointCollections` und dem Namensmuster `AzureBackup_<name of your VM that you're trying to move>_###########`.
* Löschen Sie diese Ressource. Dieser Vorgang löscht nur die sofortigen Wiederherstellungspunkte und nicht die gesicherten Daten im Tresor.
* Nach Abschluss des Löschvorgangs können Sie Ihren virtuellen Computer verschieben. Sie können den Tresor und den virtuellen Computer in das Zielabonnement verschieben. Nach dem Verschieben können Sie ohne Datenverlust mit den Sicherungsdateien fortfahren.
* Weitere Informationen zum Verschieben von Recovery Services-Tresoren für die Sicherung finden Sie unter [Einschränkungen von Recovery Services](#recovery-services-limitations).

### <a name="virtual-networks-limitations"></a>Einschränkungen von virtuellen Netzwerken

Wenn Sie ein virtuelles Netzwerk verschieben, müssen Sie auch dessen abhängige Ressourcen verschieben. Bei VPN-Gateways müssen Sie IP-Adressen, Gateways für virtuelle Netzwerke und alle zugehörigen Verbindungsressourcen verschieben. Gateways des lokalen Netzwerks können sich in einer anderen Ressourcengruppe befinden.

Um einen virtuellen Computer mit einer Netzwerkschnittstellenkarte zu verschieben, müssen Sie alle abhängigen Ressourcen verschieben. Sie müssen das virtuelle Netzwerk für die Netzwerkschnittstellenkarte, alle anderen Netzwerkschnittstellenkarten für das virtuelle Netzwerk und die VPN-Gateways verschieben.

Um ein mittels Peering verknüpftes virtuelles Netzwerk zu verschieben, müssen Sie zunächst das Peering des virtuellen Netzwerks deaktivieren. Nach der Deaktivierung können Sie das virtuelle Netzwerk verschieben. Aktivieren Sie nach der Verschiebung das Peering des virtuellen Netzwerks wieder.

Ein virtuelles Netzwerk kann nicht in ein anderes Abonnement verschoben werden, wenn das virtuelle Netzwerk ein Subnetz mit Navigationslinks für Ressourcen enthält. Beispiel: Wenn eine Azure Cache for Redis-Ressource in einem Subnetz bereitgestellt wird, verfügt dieses Subnetz über einen Navigationslink für die Ressource.

### <a name="app-service-limitations"></a>App Service-Einschränkungen

Die Einschränkungen beim Verschieben von App Service-Ressourcen unterscheiden sich abhängig davon, ob Sie die Ressourcen innerhalb eines Abonnements oder in ein neues Abonnement verschieben. Wenn Ihre Web-App ein App Service-Zertifikat verwendet, finden Sie unter [Einschränkungen von App Service Certificate](#app-service-certificate-limitations) weitere Informationen.

#### <a name="moving-within-the-same-subscription"></a>Verschieben innerhalb desselben Abonnements

Beim Verschieben einer Web-App _innerhalb desselben Abonnements_ können SSL-Zertifikate von Drittanbietern nicht verschoben werden. Sie können eine Web-App jedoch in die neue Ressourcengruppe verschieben, ohne das zugehörige SSL-Zertifikat eines Drittanbieters zu verschieben. Die SSL-Funktionalität Ihrer App wird in diesem Fall nicht beeinträchtigt.

Wenn Sie das SSL-Zertifikat mit der Web-App verschieben möchten, gehen Sie folgendermaßen vor:

1. Löschen Sie das Zertifikat des Drittanbieters aus der Web-App, bewahren Sie aber eine Kopie des Zertifikats auf.
2. Verschieben Sie die Web-App.
3. Laden Sie das Zertifikat des Drittanbieters in die verschobene Web-App hoch.

#### <a name="moving-across-subscriptions"></a>Abonnementübergreifendes Verschieben

Beim Verschieben einer Web-App _zwischen Abonnements_ gelten die folgenden Einschränkungen:

- Die Zielressourcengruppe darf keine App Service-Ressourcen besitzen. Zu App Service-Ressourcen zählen:
    - Web-Apps
    - App Service-Pläne
    - Hochgeladene oder importierte SSL-Zertifikate
    - App Service-Umgebungen
- Alle App Service-Ressourcen in der Ressourcengruppe müssen zusammen verschoben werden.
- App Service-Ressourcen können nur aus der Ressourcengruppe verschoben werden, in der sie ursprünglich erstellt wurden. Wenn eine App Service-Ressource sich nicht mehr in ihrer ursprünglichen Ressourcengruppe befindet, muss sie erst zurück in die ursprüngliche Ressourcengruppe verschoben werden, bevor sie zwischen Abonnements verschoben werden kann.

Wenn Sie sich nicht an die ursprüngliche Ressourcengruppe erinnern, können Sie diese mithilfe der Diagnosefunktion finden. Wählen Sie für Ihre Web-App **Diagnose und Problembehandlung** aus. Wählen Sie dann **Konfiguration und Verwaltung** aus.

![„Diagnose“ auswählen](./media/resource-group-move-resources/select-diagnostics.png)

Wählen Sie **Migrationsoptionen** aus.

![Migrationsoptionen auswählen](./media/resource-group-move-resources/select-migration.png)

Wählen Sie die Option für empfohlene Schritte, um die Web-App zu verschieben.

![„Empfohlene Schritte“ auswählen](./media/resource-group-move-resources/recommended-steps.png)

Sie sehen die empfohlenen Maßnahmen, die Sie ergreifen sollten, bevor Sie die Ressourcen verschieben. Die Informationen enthalten die ursprüngliche Ressourcengruppe für die Web-App.

![Empfehlungen](./media/resource-group-move-resources/recommendations.png)

### <a name="app-service-certificate-limitations"></a>Einschränkungen von App Service Certificate

Sie können Ihr App Service-Zertifikat in eine neue Ressourcengruppe oder ein neues Abonnement verschieben. Wenn Ihr App Service-Zertifikat an eine Web-App gebunden ist, müssen Sie einige Schritte ausführen, bevor Sie die Ressourcen in ein neues Abonnement verschieben. Löschen Sie die SSL-Bindung und das private Zertifikat vor dem Verschieben der Ressourcen aus der Web-App. Das App Service-Zertifikat muss nicht gelöscht werden, nur das private Zertifikat in der Web-App.

### <a name="classic-deployment-limitations"></a>Einschränkungen bei der klassischen Bereitstellung

Die Optionen zum Verschieben von Ressourcen, die über das klassische Modell bereitgestellt wurden, unterscheiden sich abhängig davon, ob Sie die Ressourcen innerhalb eines Abonnements oder in ein neues Abonnement verschieben.

#### <a name="same-subscription"></a>Gleiches Abonnement

Beim Verschieben von Ressourcen aus einer Ressourcengruppe in eine andere innerhalb des gleichen Abonnements gelten die folgenden Einschränkungen:

* Virtuelle Netzwerke (klassisch) können nicht verschoben werden.
* Virtuelle Computer (klassisch) müssen mit dem Clouddienst verschoben werden.
* Der Clouddienst kann nur verschoben werden, wenn der Verschiebevorgang alle dazugehörigen virtuellen Computer umfasst.
* Es kann immer nur ein Clouddienst gleichzeitig verschoben werden.
* Es kann immer nur ein Speicherkonto (klassisch) gleichzeitig verschoben werden.
* Ein Speicherkonto (klassisch) kann nicht im selben Vorgang mit einem virtuellen Computer oder Clouddienst verschoben werden.

Um klassische Ressourcen in eine neue Ressourcengruppe innerhalb des gleichen Abonnements zu verschieben, verwenden Sie die standardmäßigen Verschiebevorgänge im [Portal](#use-portal), in Azure PowerShell, in der Azure-Befehlszeilenschnittstelle (CLI) oder in der REST-API. Sie verwenden hierbei die gleichen Vorgänge wie beim Verschieben von Resource Manager-Ressourcen.

#### <a name="new-subscription"></a>Neues Abonnement

Beim Verschieben von Ressourcen in ein neues Abonnement gelten die folgenden Einschränkungen:

* Alle klassische Ressourcen im Abonnement müssen im selben Vorgang verschoben werden.
* Das Zielabonnement darf keine anderen klassischen Ressourcen enthalten.
* Der Verschiebevorgang kann nur über eine separate REST-API für klassische Verschiebevorgänge angefordert werden. Die Resource Manager-Standardbefehle für das Verschieben funktionieren beim Verschieben klassischer Ressourcen in ein neues Abonnement nicht.

Verwenden Sie zum Verschieben von klassischen Ressourcen in ein neues Abonnement die REST-Vorgänge, die für klassische Ressourcen spezifisch sind. Führen Sie zum Verwenden von REST die folgenden Schritte aus:

1. Überprüfen Sie, ob das Quellabonnement an einem abonnementübergreifenden Verschiebevorgang teilnehmen kann. Gehen Sie folgendermaßen vor:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     Fügen Sie Folgendes in den Anforderungstext ein:

   ```json
   {
    "role": "source"
   }
   ```

     Die Antwort für den Überprüfungsvorgang erfolgt in folgendem Format:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

2. Überprüfen Sie, ob das Zielabonnement an einem abonnementübergreifenden Verschiebevorgang teilnehmen kann. Gehen Sie folgendermaßen vor:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     Fügen Sie Folgendes in den Anforderungstext ein:

   ```json
   {
    "role": "target"
   }
   ```

     Die Antwort liegt im gleichen Format vor wie bei der Überprüfung des Quellabonnements.
3. Wenn beide Abonnements die Überprüfung bestehen, verschieben Sie alle klassischen Ressourcen mithilfe des folgenden Vorgangs aus einem Abonnement in ein anderes:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    Fügen Sie Folgendes in den Anforderungstext ein:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Dieser Vorgang kann einige Minuten dauern.

### <a name="recovery-services-limitations"></a>Einschränkungen von Recovery Services

 Führen Sie die folgenden Schritte aus, um einen Recovery Services-Tresor zu verschieben: [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../backup/backup-azure-move-recovery-services-vault.md).

Derzeit können Sie jeweils einen Recovery Services-Tresor pro Region verschieben. Tresore, die Azure Files, die Azure-Dateisynchronisierung oder SQL auf IaaS-VMs sichern, können nicht verschoben werden.

Wenn eine VM nicht zusammen mit dem Tresor verschoben wird, verbleiben die aktuellen VM-Wiederherstellungspunkte bis zu ihrem Ablauf im Tresor. Unabhängig davon, ob die VM mit dem Tresor verschoben wird oder nicht, können Sie die VM anhand des Sicherungsverlaufs im Tresor wiederherstellen.

Der Recovery Services-Tresor unterstützt keine abonnementübergreifenden Sicherungen. Wenn Sie einen Tresor mit VM-Sicherungsdaten abonnementübergreifend verschieben, müssen Sie Ihre VMs in das gleiche Abonnement verschieben und die gleiche Zielressourcengruppe verwenden, um weiterhin Sicherungen durchführen zu können.

Für den Tresor definierte Sicherungsrichtlinien bleiben nach dem Verschieben des Tresors erhalten. Die Berichterstellung und Überwachung müssen nach dem Verschieben erneut für den Tresor eingerichtet werden.

So verschieben Sie eine VM in ein neues Abonnement, ohne den Recovery Services-Tresor zu verschieben:

 1. Halten Sie die Sicherung vorübergehend an.
 1. [Löschen Sie den Wiederherstellungspunkt.](#virtual-machines-limitations) Dieser Vorgang löscht nur die sofortigen Wiederherstellungspunkte und nicht die gesicherten Daten im Tresor.
 1. Verschieben Sie die VMs in das neue Abonnement.
 1. Schützen Sie die VMs erneut unter einem neuen Tresor in diesem Abonnement.

Das Verschieben von Speicher-, Netzwerk- und Computeressourcen, die dazu dienen, eine Notfallwiederherstellung mit Azure Site Recovery einzurichten, ist nicht möglich. Angenommen, Sie haben die Replikation Ihrer lokalen Computer in ein Speicherkonto (Storage1) eingerichtet, und möchten, dass der geschützte Computer nach einem Failover zu Azure als virtueller Computer (VM1) angezeigt wird, der an ein virtuelles Netzwerk (Network1) angeschlossen ist. Sie können dann die Azure-Ressourcen „Storage1“, „VM1“ und „Network1“ nicht zwischen Ressourcengruppen im selben Abonnement oder zwischen Abonnements verschieben.

### <a name="hdinsight-limitations"></a>HDInsight-Einschränkungen

Sie können HDInsight-Cluster in ein neues Abonnement oder eine neue Ressourcengruppe verschieben. Dagegen können die mit dem HDInsight-Cluster verknüpften Netzwerkressourcen (z.B. virtuelles Netzwerk, NIC oder Load Balancer) nicht zwischen Abonnements verschoben werden. Darüber hinaus kann eine NIC, die an einen virtuellen Computer für den Cluster angefügt ist, nicht in eine neue Ressourcengruppe verschoben werden.

Beim Verschieben eines HDInsight-Clusters in ein neues Abonnement sollten Sie zunächst andere Ressourcen (z.B. das Speicherkonto) verschieben. Verschieben Sie erst anschließend den HDInsight-Cluster.

## <a name="checklist-before-moving-resources"></a>Checkliste vor dem Verschieben von Ressourcen

Vor dem Verschieben einer Ressource müssen einige wichtige Schritte ausgeführt werden. Indem Sie diese Bedingungen überprüfen, können Sie Fehler vermeiden.

1. Quellen- und Zielabonnements müssen aktiv sein. Wenn beim Aktivieren eines Kontos, das deaktiviert wurde, Schwierigkeiten auftreten, [erstellen Sie eine Azure-Supportanfrage](../azure-supportability/how-to-create-azure-support-request.md). Wählen Sie **Abonnementverwaltung** als Problemtyp aus.

1. Quell- und Zielabonnement müssen im selben [Azure Active Directory-Mandanten](../active-directory/develop/quickstart-create-new-tenant.md) vorhanden sein. Um zu überprüfen, ob beide Abonnements die gleiche Mandanten-ID aufweisen, verwenden Sie Azure PowerShell oder die Azure-Befehlszeilenschnittstelle.

   Verwenden Sie für Azure PowerShell Folgendes:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Wenn die Mandanten-IDs für das Quell- und das Zielabonnement nicht gleich sind, verwenden Sie die folgenden Methoden, um die Mandanten-IDs aufeinander abzustimmen:

   * [Übertragen des Besitzes eines Azure-Abonnements auf ein anderes Konto](../billing/billing-subscription-transfer.md)
   * [Zuweisen oder Hinzufügen eines Azure-Abonnements zu Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Das Zielabonnement muss für den Ressourcenanbieter der verschobenen Ressource registriert sein. Andernfalls erhalten Sie eine Fehlermeldung, die besagt, dass das **Abonnement nicht für einen Ressourcentyp registriert ist**. Dieser Fehler kann auftreten, wenn eine Ressource in ein neues Abonnement verschoben wird, dieses aber noch nie mit diesem Ressourcentyp verwendet wurde.

   Verwenden Sie für PowerShell die folgenden Befehle zum Abrufen des Registrierungsstatus:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Verwenden Sie zum Registrieren eines Ressourcenanbieters Folgendes:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Verwenden Sie für die Azure CLI die folgenden Befehle zum Abrufen des Registrierungsstatus:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Verwenden Sie zum Registrieren eines Ressourcenanbieters Folgendes:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Das Konto, das die Ressourcen verschiebt, muss mindestens über folgende Berechtigungen verfügen:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** für die Quellressourcengruppe.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** für die Zielressourcengruppe.

1. Überprüfen Sie vor dem Verschieben der Ressource die Abonnementkontingente für das Abonnement, zu dem Sie die Ressourcen verschieben. Wenn das Verschieben der Ressourcen bedeutet, dass das Abonnement seine Einschränkungen überschreitet, müssen Sie prüfen, ob Sie eine Erhöhung des Kontingents anfordern können. Eine vollständige Liste zu diesen Einschränkungen und Informationen zur Anforderung einer Kontingenterhöhung finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).

1. Unterteilen Sie große Verschiebevorgänge nach Möglichkeit in separate Verschiebevorgänge. Resource Manager gibt sofort einen Fehler aus, wenn ein einziger Vorgang mehr als 800 Ressourcen umfasst. Beim Verschieben von weniger als 800 Ressourcen kann jedoch ebenfalls ein Fehler durch ein Timeout auftreten.

1. Der Dienst muss die Möglichkeit bieten, Ressourcen zu verschieben. Um zu ermitteln, ob die Verschiebung erfolgreich verläuft, [überprüfen Sie Ihre Verschiebungsanforderung](#validate-move). In den nachstehenden Abschnitten in diesem Artikel wird erläutert, welche [Dienste das Verschieben von Ressourcen](#services-that-can-be-moved) ermöglichen und welche [nicht](#services-that-cannot-be-moved).

## <a name="validate-move"></a>Überprüfen der Verschiebung

Der [Vorgang zum Überprüfen der Verschiebung](/rest/api/resources/resources/validatemoveresources) ermöglicht Ihnen, Ihr Verschiebungsszenario zu testen, ohne tatsächlich Ressourcen zu verschieben. Verwenden Sie diesen Vorgang, um zu ermitteln, ob die Verschiebung erfolgreich ausgeführt wird. Um diesen Vorgang ausführen zu können, benötigen Sie Folgendes:

* Name der Quellressourcengruppe
* Ressourcen-ID der Zielressourcengruppe
* Ressourcen-ID der einzelnen zu verschiebenden Ressourcen
* [Zugriffstoken](/rest/api/azure/#acquire-an-access-token) für Ihr Konto

Senden Sie die folgende Anforderung:

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

Mit dem Anforderungstext:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Wenn die Anforderung ordnungsgemäß formatiert ist, gibt der Vorgang Folgendes zurück:

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Die Statuscode 202 gibt an, dass die Überprüfungsanforderung akzeptiert wurde, jedoch noch nicht ermittelt wurde, ob der Verschiebungsvorgang erfolgreich verläuft. Die Wert `location` enthält eine URL, über die Sie den Status des lang andauernden Vorgangs überprüfen können.  

Um den Status zu überprüfen, senden Sie die folgende Anforderung:

```
GET <location-url>
Authorization: Bearer <access-token>
```

Während der Vorgang ausgeführt wird, wird weiterhin der Statuscode 202 angezeigt. Warten Sie die Anzahl von Sekunden, die im Wert `retry-after` angegeben ist, bevor Sie es erneut versuchen. Wenn der Verschiebungsvorgang erfolgreich überprüft wurde, erhalten Sie den Statuscode 204. Wenn bei der Verschiebungsüberprüfung ein Fehler auftritt, erhalten Sie eine Fehlermeldung, z.B.:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="move-resources"></a>Verschieben von Ressourcen

### <a name="a-nameuse-portal-by-using-azure-portal"></a><a name="use-portal" />Mit dem Azure-Portal

Um Ressourcen zu verschieben, wählen Sie die Ressourcengruppe mit den jeweiligen Ressourcen aus, und klicken Sie dann auf die Schaltfläche **Verschieben**.

![Verschieben von Ressourcen](./media/resource-group-move-resources/select-move.png)

Wählen Sie aus, ob Sie die Ressource in eine neue Ressourcengruppe oder ein neues Abonnement verschieben.

Wählen Sie die zu verschiebenden Ressourcen und die Zielressourcengruppe aus. Bestätigen, dass Sie Skripts für diese Ressourcen aktualisieren müssen, und wählen Sie **OK**aus. Wenn Sie im vorherigen Schritt das Symbol „Abonnement bearbeiten“ ausgewählt haben, müssen Sie auch das Zielabonnement auswählen.

![Ziel auswählen](./media/resource-group-move-resources/select-destination.png)

Unter **Benachrichtigungen**wird angezeigt, dass der Verschiebevorgang ausgeführt wird.

![Verschiebestatus anzeigen](./media/resource-group-move-resources/show-status.png)

Sobald der Vorgang abgeschlossen ist, werden Sie über das Ergebnis informiert.

![Ergebnis des Verschiebens anzeigen](./media/resource-group-move-resources/show-result.png)

### <a name="by-using-azure-powershell"></a>Mit Azure PowerShell

Verwenden Sie zum Verschieben vorhandener Ressourcen in eine andere Ressourcengruppe oder ein anderes Abonnement den Befehl [Move-AzResource](/powershell/module/az.resources/move-azresource). Im folgenden Beispiel wird veranschaulicht, wie mehrere Ressourcen in eine neue Ressourcengruppe verschoben werden.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Um Ressourcen in ein neues Abonnement zu verschieben, schließen Sie einen Wert für den Parameter `DestinationSubscriptionId` ein.

### <a name="by-using-azure-cli"></a>Mit der Azure CLI

Verwenden Sie zum Verschieben vorhandener Ressourcen in eine andere Ressourcengruppe oder ein anderes Abonnement den Befehl [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move). Geben Sie die Ressourcen-IDs der zu verschiebenden Ressourcen an. Im folgenden Beispiel wird veranschaulicht, wie mehrere Ressourcen in eine neue Ressourcengruppe verschoben werden. Geben Sie im `--ids`-Parameter eine durch Leerzeichen getrennte Liste der zu verschiebenden Ressourcen-IDs an.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Um Ressourcen in ein neues Abonnement zu verschieben, geben Sie den Parameter`--destination-subscription-id` an.

### <a name="by-using-rest-api"></a>Mit der REST-API

Führen Sie zum Verschieben vorhandener Ressourcen in eine andere Ressourcengruppe oder ein anderes Abonnement Folgendes aus:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

Geben Sie im Anforderungstext die Zielgruppe und die zu verschiebenden Ressourcen an. Weitere Informationen zur REST-Verschiebung finden Sie unter [Verschieben von Ressourcen](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den PowerShell-Cmdlets zum Verwalten Ihrer Ressourcen finden Sie unter [Verwenden von Azure PowerShell mit Resource Manager](manage-resources-powershell.md).
* Informationen zu den Befehlen der Azure CLI zum Verwalten Ihrer Ressourcen finden Sie unter [Verwenden der Azure CLI mit Resource Manager](manage-resources-cli.md).
* Informationen zu Portalfeatures zum Verwalten Ihres Abonnements finden Sie unter [Verwenden des Azure-Portals zum Verwalten von Ressourcen](resource-group-portal.md).
* Informationen zum Anwenden einer logischen Organisation auf Ihre Ressourcen finden Sie unter [Verwenden von Tags zum Organisieren von Ressourcen](resource-group-using-tags.md).
