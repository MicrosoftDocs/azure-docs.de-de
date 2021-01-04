---
title: Azure HDInsight-Cluster mit Datenträgerverschlüsselung verlieren den Zugriff auf Key Vault
description: Problembehandlungsschritte und mögliche Lösungen für Probleme beim Zugriff auf Key Vault, wenn Azure HDInsight-Cluster beteiligt sind.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: bc8162f3a7ca8744a94aba039996275b5f13c727
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533409"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Szenario: Azure HDInsight-Cluster mit Datenträgerverschlüsselung verlieren den Zugriff auf Key Vault

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

In Resource Health Center (RHC) wird die Benachrichtigung `The HDInsight cluster is unable to access the key for BYOK encryption at rest` für Bring Your Own Key-Cluster (BYOK-Cluster) angezeigt, bei denen die Clusterknoten den Zugriff auf die Key Vault-Instanz (KV-Instanz) des Kunden verloren haben. Ähnliche Benachrichtigungen werden auch auf der Apache Ambari-Benutzeroberfläche angezeigt.

## <a name="cause"></a>Ursache

Die Benachrichtigung stellt sicher, dass der Zugriff auf Key Vault von den Clusterknoten aus möglich ist, wodurch die Netzwerkverbindung, die KV-Integrität und die Zugriffsrichtlinie für die benutzerseitig zugewiesene verwaltete Identität sichergestellt werden. Bei dieser Benachrichtigung handelt es sich nur um eine Warnung vor dem bevorstehenden Herunterfahren des Brokers bei nachfolgenden Knotenneustarts, der Cluster wird weiterhin ausgeführt, bis die Knoten neu gestartet werden.

Navigieren Sie zur Apache Ambari-Benutzeroberfläche, um weitere Informationen zur Benachrichtigung über den **Key Vault-Status der Datenträgerverschlüsselung** zu erhalten. Diese Benachrichtigung enthält Details über den Grund für den Überprüfungsfehler.

## <a name="resolution"></a>Lösung

### <a name="kvaad-outage"></a>KV-/AAD-Ausfall

Ausführlichere Informationen finden Sie unter [Azure Key Vault: Verfügbarkeit und Redundanz](../../key-vault/general/disaster-recovery-guidance.md) und auf der Seite „Azure-Status“ (https://status.azure.com/ ).

### <a name="kv-accidental-deletion"></a>Versehentliche Löschung in KV

* Stellen Sie den gelöschten Schlüssel in KV zur automatischen Wiederherstellung wieder her. Weitere Informationen finden Sie unter [Wiederherstellen eines gelöschten Schlüssels](/rest/api/keyvault/recoverdeletedkey).
* Wenden Sie sich an das KV-Team, um Hilfe beim Wiederherstellen nach versehentlichen Löschvorgängen zu erhalten.

### <a name="kv-access-policy-changed"></a>Geänderte KV-Zugriffsrichtlinie

Stellen Sie die Zugriffsrichtlinien für die benutzerseitig zugewiesene verwaltete Identität wieder her, die dem HDI-Cluster für den Zugriff auf die KV-Instanz zugewiesen ist.

### <a name="key-permitted-operations"></a>Für den Schlüssel zulässige Vorgänge

Für jeden Schlüssel in KV können Sie den Satz zulässiger Vorgänge auswählen. Stellen Sie sicher, dass die Vorgänge zum Umschließen und zum Aufheben der Umschließung für den BYOK-Schlüssel aktiviert sind.

### <a name="expired-key"></a>Abgelaufener Schlüssel

Wenn das Ablaufdatum abgelaufen ist und der Schlüssel nicht rotiert wird, stellen Sie den Schlüssel aus dem Sicherungs-HSM wieder her, oder bitten Sie das KV-Team, das Ablaufdatum zu löschen.

### <a name="kv-firewall-blocking-access"></a>Die KV-Firewall blockiert den Zugriff

Ändern Sie die Einstellungen der KV-Firewall so, dass BYOK-Clusterknoten auf die KV-Instanz zugreifen können.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>NSG-Regeln für das virtuelle Netzwerk blockieren den Zugriff

Überprüfen Sie die NSG-Regeln, die dem virtuellen Netzwerk zugeordnet sind, das mit dem Cluster verbunden ist.

## <a name="mitigation-and-prevention-steps"></a>Schritte zur Minderung und Prävention

### <a name="kv-accidental-deletion"></a>Versehentliche Löschung in KV

* Konfigurieren Sie Key Vault mit einer [festgelegten Ressourcensperre](../../azure-resource-manager/management/lock-resources.md).
* Sichern Sie die Schlüssel im zugehörigen Hardwaresicherheitsmodul.

### <a name="key-deletion"></a>Löschen von Schlüsseln

Cluster sollten vor dem Löschen von Schlüsseln gelöscht werden.

### <a name="kv-access-policy-changed"></a>Geänderte KV-Zugriffsrichtlinie

Überprüfen und testen Sie Zugriffsrichtlinien regelmäßig.

### <a name="expired-key"></a>Abgelaufener Schlüssel

* Sichern Sie die Schlüssel in Ihrem HSM.
* Verwenden Sie einen Schlüssel ohne festgelegtes Ablaufdatum.
* Wenn ein Ablaufdatum festgelegt werden muss, rotieren Sie die Schlüssel vor dem Ablaufdatum.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.