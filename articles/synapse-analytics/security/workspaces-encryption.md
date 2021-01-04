---
title: Verschlüsselung in Azure Synapse Analytics
description: In diesem Artikel wird die Verschlüsselung in Azure Synapse Analytics erläutert.
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: d9a9d3c303739e68b5b8ef28053d6cf0b071f955
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501055"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Verschlüsselung für Azure Synapse Analytics-Arbeitsbereiche

In diesem Artikel wird Folgendes beschrieben:
* Verschlüsselung ruhender Daten in Synapse Analytics-Arbeitsbereichen
* Konfiguration von Synapse-Arbeitsbereichen zum Aktivieren der Verschlüsselung mit einem vom Kunden verwalteten Schlüssel
* Verwalten von Schlüsseln, die zum Verschlüsseln von Daten in Arbeitsbereichen verwendet werden

## <a name="encryption-of-data-at-rest"></a>Verschlüsselung für ruhende Daten

Mit einer Komplettlösung für die Verschlüsselung ruhender Daten wird sichergestellt, dass die Daten niemals in unverschlüsselter Form gespeichert werden. Die doppelte Verschlüsselung ruhender Daten verringert Bedrohungen durch zwei separate Verschlüsselungsebenen, um vor Kompromittierungen einer einzelnen Ebene zu schützen. Azure Synapse Analytics bietet eine zweite Verschlüsselungsebene für die Daten in Ihrem Arbeitsbereich mit einem vom Kunden verwalteten Schlüssel. Dieser Schlüssel wird in Ihrer [Azure Key Vault](../../key-vault/general/overview.md)-Instanz geschützt, sodass Sie den Besitz der Schlüsselverwaltung und -rotation übernehmen können.

Die erste Verschlüsselungsebene für Azure-Dienste wird mit plattformseitig verwalteten Schlüsseln aktiviert. Standardmäßig werden Azure-Datenträger und Daten in Azure Storage-Konten automatisch im Ruhezustand verschlüsselt. Weitere Informationen zur Verwendung der Verschlüsselung in Microsoft Azure finden Sie in der [Übersicht über die Azure-Verschlüsselung](../../security/fundamentals/encryption-overview.md).

## <a name="azure-synapse-encryption"></a>Azure Synapse-Verschlüsselung

Dieser Abschnitt vermittelt Ihnen ein besseres Verständnis dafür, wie die Verschlüsselung mit einem vom Kunden verwalteten Schlüssel in Synapse-Arbeitsbereichen aktiviert und erzwungen wird. Bei dieser Verschlüsselung werden vorhandene Schlüssel oder neue Schlüssel verwendet, die in Azure Key Vault generiert wurden. Alle Daten in einem Arbeitsbereich werden mit einem einzigen Schlüssel verschlüsselt. Synapse-Arbeitsbereiche unterstützen RSA-Schlüssel mit 2048- und 3072-Byte-Schlüsseln.

> [!NOTE]
> Synapse-Arbeitsbereiche unterstützen nicht die Verwendung von ECC-Schlüsseln (Elliptic Curve Cryptography) für die Verschlüsselung.

Die Daten in den folgenden Synapse-Komponenten werden mit dem vom Kunden verwalteten Schlüssel verschlüsselt, der auf der Arbeitsbereichsebene konfiguriert wurde:
* SQL-Pools
 * Dedizierte SQL-Pools
 * Serverlose SQL-Pools
* Apache Spark-Pools
* Integration Runtimes, Pipelines und Datasets von Azure Data Factory.

## <a name="workspace-encryption-configuration"></a>Konfiguration der Arbeitsbereichsverschlüsselung

Bei der Erstellung können Arbeitsbereiche so konfiguriert werden, dass die doppelte Verschlüsselung mit einem vom Kunden verwalteten Schlüssel aktiviert wird. Wählen Sie beim Erstellen Ihres neuen Arbeitsbereichs auf der Registerkarte „Sicherheit“ die Option „Doppelte Verschlüsselung mit einem vom Kunden verwalteten Schlüssel aktivieren“ aus. Sie können einen Schlüsselbezeichner-URI eingeben oder aus einer Liste von Schlüsseltresoren auswählen, die sich in **derselben Region** befinden wie der Arbeitsbereich. Für den Schlüsseltresor selbst muss der **Löschschutz aktiviert** sein.

> [!IMPORTANT]
> Die Konfigurationseinstellung für die doppelte Verschlüsselung kann nach der Einstellung des Arbeitsbereichs nicht mehr geändert werden.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="In diesem Diagramm sehen Sie die Option, die ausgewählt werden muss, um einen Arbeitsbereich für die doppelte Verschlüsselung mit einem vom Kunden verwalteten Schlüssel zu aktivieren.":::

### <a name="key-access-and-workspace-activation"></a>Schlüsselzugriff und Arbeitsbereichsaktivierung

Beim Azure Synapse-Verschlüsselungsmodell mit vom Kunden verwalteten Schlüsseln greift der Arbeitsbereich auf die Schlüssel in Azure Key Vault zu, um nach Bedarf Ver- und Entschlüsselungsvorgänge auszuführen. Die Schlüssel werden dem Arbeitsbereich entweder über eine Zugriffsrichtlinie oder den Azure Key Vault RBAC-Zugriff ([Vorschau](../../key-vault/general/rbac-guide.md)) zugänglich gemacht. Wenn Sie Berechtigungen über eine Azure Key Vault-Zugriffsrichtlinie erteilen, wählen Sie während der Richtlinienerstellung die Option [„Nur Anwendung“](../../key-vault/general/secure-your-key-vault.md#key-vault-authentication-options) aus (wählen Sie die verwaltete Identität des Arbeitsbereichs aus, und fügen Sie diese nicht als autorisierte Anwendung hinzu).

 Der verwalteten Identität des Arbeitsbereichs müssen die erforderlichen Berechtigungen für den Schlüsseltresor erteilt werden, bevor der Arbeitsbereich aktiviert werden kann. Durch diesen phasenbasierten Ansatz für die Arbeitsbereichsaktivierung wird sichergestellt, dass die Daten im Arbeitsbereich mit dem vom Kunden verwalteten Schlüssel verschlüsselt werden. Beachten Sie, dass die Verschlüsselung für dedizierte SQL-Pools aktiviert oder deaktiviert werden kann. Nicht jeder Pool ist standardmäßig für die Verschlüsselung aktiviert.

#### <a name="permissions"></a>Berechtigungen

Um ruhende Daten verschlüsseln oder entschlüsseln zu können, muss die verwaltete Identität des Arbeitsbereichs über die folgenden Berechtigungen verfügen:
* WrapKey (um beim Erstellen eines neuen Schlüssels einen Schlüssel in Key Vault einzufügen)
* UnwrapKey (um den Schlüssel für die Entschlüsselung abzurufen)
* Get (um den öffentlichen Teil eines Schlüssels zu lesen)

#### <a name="workspace-activation"></a>Arbeitsbereichsaktivierung

Nachdem Ihr Arbeitsbereich (mit aktivierter doppelter Verschlüsselung) erstellt wurde, behält er den Status „Ausstehend“, bis die Aktivierung erfolgreich ist. Der Arbeitsbereich muss aktiviert werden, damit Sie alle Funktionen vollständig nutzen können. Beispielsweise können Sie erst nach erfolgreicher Aktivierung einen neuen dedizierten SQL-Pool erstellen. Gewähren Sie der verwalteten Identität des Arbeitsbereichs Zugriff auf den Schlüsseltresor, und klicken Sie im Azure-Portal auf dem Arbeitsbereichsbanner auf den Aktivierungslink. Nachdem die Aktivierung erfolgreich abgeschlossen wurde, kann Ihr Arbeitsbereich mit der Gewissheit verwendet werden, dass alle darin enthaltenen Daten durch Ihren vom Kunden verwalteten Schlüssel geschützt sind. Wie bereits erwähnt, muss für den Schlüsseltresor der Löschschutz aktiviert sein, damit die Aktivierung erfolgreich durchgeführt werden kann.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="In diesem Diagramm ist das Banner mit dem Aktivierungslink für den Arbeitsbereich dargestellt.":::


### <a name="manage-the-workspace-customer-managed-key"></a>Verwalten des vom Kunden verwalteten Schlüssels für den Arbeitsbereich 

Sie können den vom Kunden verwalteten Schlüssel, der zum Verschlüsseln von Daten verwendet wird, im Azure-Portal auf der Seite **Verschlüsselung** ändern. Hier können Sie auch einen neuen Schlüssel mithilfe eines Schlüsselbezeichners auswählen oder einen Schlüssel aus Schlüsseltresoren auswählen, auf die Sie Zugriff haben und die sich in derselben Region befinden wie der Arbeitsbereich. Wenn Sie einen Schlüssel in einem anderen Schlüsseltresor als den zuvor verwendeten auswählen, erteilen Sie der verwalteten Identität des Arbeitsbereichs die Berechtigungen zum Abrufen, Packen und Entpacken von Schlüsseln für den neuen Schlüsseltresor. Der Arbeitsbereich überprüft den Zugriff auf den neuen Schlüsseltresor, und alle Daten im Arbeitsbereich werden mit dem neuen Schlüssel erneut verschlüsselt.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="In diesem Diagramm sehen Sie den Bereich „Verschlüsselung“ für den Arbeitsbereich im Azure-Portal.":::

>[!IMPORTANT]
>Wenn Sie den Verschlüsselungsschlüssel eines Arbeitsbereichs ändern, behalten Sie den Schlüssel bei, bis Sie ihn im Arbeitsbereich durch einen neuen Schlüssel ersetzen. Dies ermöglicht das Entschlüsseln von Daten mit dem alten Schlüssel, bevor sie mit dem neuen Schlüssel erneut verschlüsselt werden.

Azure Key Vault-Richtlinien für die automatische, regelmäßige Rotation von Schlüsseln oder Aktionen für die Schlüssel können zur Erstellung neuer Schlüsselversionen führen. Sie können auswählen, dass alle Daten im Arbeitsbereich mit der aktuellen Version des aktiven Schlüssels erneut verschlüsselt werden. Wenn die Daten erneut verschlüsselt werden sollen, ändern Sie im Azure-Portal den Schlüssel in einen temporären Schlüssel, und wechseln Sie dann zurück zu dem Schlüssel, den Sie für die Verschlüsselung verwenden möchten. Wenn Sie beispielsweise die Datenverschlüsselung mit der neuesten Version des aktiven Schlüssels „Schlüssel1“ aktualisieren möchten, ändern Sie den vom Kunden verwalteten Schlüssel für den Arbeitsbereich in den temporären Schlüssel „Schlüssel2“. Warten Sie, bis die Verschlüsselung mit Schlüssel2 abgeschlossen ist. Ändern Sie dann den vom Kunden verwalteten Schlüssel für den Arbeitsbereich wieder in Schlüssel1. Die Daten im Arbeitsbereich werden dann mit der neuesten Version von Schlüssel1 erneut verschlüsselt.

> [!NOTE]
> Das Azure Synapse Analytics-Team arbeitet aktiv daran, Funktionen hinzuzufügen, um Daten automatisch erneut zu verschlüsseln, wenn neue Schlüsselversionen erstellt werden. Bis diese Funktionalität verfügbar ist, erzwingen Sie zur Gewährleistung der Konsistenz in Ihrem Arbeitsbereich die erneute Verschlüsselung der Daten mit dem oben beschriebenen Vorgang.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>SQL Transparent Data Encryption mit dienstseitig verwalteten Schlüsseln

SQL Transparent Data Encryption (TDE) ist für dedizierte SQL-Pools in Arbeitsbereichen verfügbar, die *nicht* für die doppelte Verschlüsselung aktiviert sind. Bei diesem Typ von Arbeitsbereich wird ein vom Dienst verwalteter Schlüssel verwendet, um die doppelte Verschlüsselung der Daten in den dedizierten SQL-Pools zu ermöglichen. TDE mit dem vom Dienst verwalteten Schlüssel kann für einzelne dedizierte SQL-Pools aktiviert oder deaktiviert werden.

## <a name="next-steps"></a>Nächste Schritte

[Verwenden integrierter Azure Policy-Richtliniendefinitionen zum Implementieren von Verschlüsselungsschutz für Synapse-Arbeitsbereiche](../policy-reference.md)

