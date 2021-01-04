---
title: Behandeln von Problemen bei der Konnektivität zwischen Synapse Studio und Speicher
description: Behandeln von Problemen bei der Konnektivität zwischen Synapse Studio und Speicher
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: cee6d030a9639a7203a32a3c0957733cecb1f8b6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445316"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Behandeln von Problemen bei der Konnektivität zwischen Azure Synapse Analytics Studio und Speicher

In Synapse Studio können Sie die Datenressourcen untersuchen, die sich in Ihrem verknüpften Speicher befinden. Dieser Leitfaden wird Ihnen helfen, Konnektivitätsprobleme zu beheben, wenn Sie versuchen, auf Ihre Datenressourcen zuzugreifen. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Fall 1: Dem Speicherkonto fehlen geeignete Berechtigungen

Wenn Ihr Speicherkonto nicht über die erforderlichen Berechtigungen verfügt, können Sie die Speicherstruktur nicht über „Daten“ --> „Verknüpft“ in Synapse Studio erweitern. Sehen Sie sich hierzu den folgenden Screenshot des Problemsymptoms an. 

Die detaillierte Fehlermeldung kann variieren, aber die generelle Bedeutung der Fehlermeldung ist: „Diese Anforderung ist zum Durchführen dieses Vorgangs nicht autorisiert.“.

![Speicherkonnektivitätsproblem 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue.1.png)

**LÖSUNG**: Informationen, wie Sie Ihr Konto der erforderlichen Rolle zuweisen, finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](../../storage/common/storage-auth-aad-rbac-portal.md).


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Fall 2: Fehler beim Senden der Anforderung an den Speicherserver

Wenn Sie den Pfeil auswählen, um die Speicherstruktur in „Daten“ --> „Verknüpft“ in Synapse Studio zu erweitern, wird möglicherweise das Problem „REQUEST_SEND_ERROR“ im linken Bereich angezeigt. Sehen Sie sich hierzu den folgenden Screenshot des Problemsymptoms an:

![Speicherkonnektivitätsproblem 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue.2.png)

Für dieses Problem kann es mehrere mögliche Ursachen geben:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>Die Speicherressource befindet sich hinter einem vNet, und ein privater Speicherendpunkt muss konfiguriert werden.

**LÖSUNG**: In diesem Fall müssen Sie den privaten Speicherendpunkt für Ihr Speicherkonto konfigurieren. Informationen zum Konfigurieren des privaten Speicherendpunkts für vNet finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](../security/how-to-connect-to-workspace-from-restricted-network.md).

Sie können den Befehl „nslookup \<storage-account-name\>.dfs.core.windows.net“ verwenden, um die Konnektivität zu überprüfen, nachdem der private Speicherendpunkt konfiguriert wurde. Er sollte eine Zeichenfolge zurückgeben, die folgender ähnelt: „\<storage-account-name\>.privatelink.dfs.core.windows.net“.

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>Die Speicherressource befindet sich nicht hinter einem vNet, aber der Blob-Dienst (Azure AD)-Endpunkt ist wegen der konfigurierten Firewall nicht erreichbar.

**LÖSUNG**: In diesem Fall müssen Sie Ihr Speicherkonto im Azure-Portal öffnen. Scrollen Sie im linken Navigationsbereich nach unten zu **Support und Problembehandlung**, und wählen Sie **Konnektivitätsprüfung** aus, um den Verbindungsstatus des **Blob-Diensts (Azure AD)** zu überprüfen. Wenn er nicht erreichbar ist, befolgen Sie den erwähnten Leitfaden, um die Konfiguration von **Firewalls und virtuellen Netzwerken** auf der Seite Ihres „Speicherkontos“ zu überprüfen. Weitere Informationen zu Speicherfirewalls finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../../storage/common/storage-network-security.md).

### <a name="other-issues-to-check"></a>Weitere zu überprüfende Probleme 

* Die Speicherressource, auf die Sie zugreifen, ist Azure Data Lake Storage Gen2 und befindet sich gleichzeitig hinter einer Firewall und einem vNet (wobei der private Speicherendpunkt konfiguriert ist).
* Die Containerressource, auf die Sie zugreifen, wurde gelöscht oder ist nicht vorhanden.


## <a name="next-steps"></a>Nächste Schritte
Wenn die vorherigen Schritte nicht zur Lösung Ihres Problems beitragen, [erstellen Sie ein Supportticket](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
