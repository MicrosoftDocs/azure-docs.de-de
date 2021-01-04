---
title: Problembehandlung bei der Datenverschlüsselung – Azure Database for PostgreSQL (Einzelserver)
description: Erfahren Sie, wie Sie Probleme bei der Datenverschlüsselung auf dem Azure Database for PostgreSQL-Einzelserver beheben.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: c315e1df473f3d23bab7e2a78ce166f22272ee70
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242244"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Problembehandlung bei der Datenverschlüsselung in Azure Database for PostgreSQL (Einzelserver)

Dieser Artikel enthält Informationen zum Ermitteln und Beheben häufiger Probleme, die bei der Einzelserverbereitstellung von Azure Database for PostgreSQL auftreten können, wenn die Datenverschlüsselung mit einem kundenseitig verwalteten Schlüssel konfiguriert ist.

## <a name="introduction"></a>Einführung

Wenn Sie die Datenverschlüsselung so konfigurieren, dass in Azure Key Vault ein kundenseitig verwalteter Schlüssel verwendet wird, benötigt der Server kontinuierlichen Zugriff auf den Schlüssel. Wenn der Server keinen Zugriff mehr auf den kundenseitig verwalteten Schlüssel in Azure Key Vault hat, lehnt er alle Verbindungen ab, gibt die entsprechende Fehlermeldung zurück und ändert seinen Status im Azure-Portal in **Zugriff nicht möglich**.

Wenn Sie einen unzugänglichen Azure Database for PostgreSQL-Server nicht mehr benötigen, können Sie ihn löschen, um Kosten zu vermeiden. Keine anderen Aktionen sind auf dem Server zulässig, bis der Zugriff auf den Schlüsseltresor wiederhergestellt wurde und der Server verfügbar ist. Das Ändern der Datenverschlüsselungsoption aus `Yes` (kundenseitig verwaltet) in `No` (dienstseitig verwaltet) ist auf einem nicht zugänglichen Server ebenfalls nicht möglich, wenn dieser mit einem kundenseitig verwalteten Schlüssel verschlüsselt ist. Sie müssen den Schlüssel manuell erneut validieren, bevor Sie erneut auf den Server zugreifen können. Diese Aktion ist notwendig, um die Daten vor nicht autorisierten Zugriffen zu schützen, wenn die Berechtigungen für den kundenseitig verwalteten Schlüssel widerrufen werden.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Häufige Fehler, die zu nicht zugänglichen Servern führen

Die folgenden Fehlkonfigurationen führen zu den meisten Problemen bei der Datenverschlüsselung, bei der Azure Key Vault-Schlüssel verwendet werden:

- Der Schlüsseltresor ist nicht verfügbar oder nicht vorhanden:
  - Der Schlüsseltresor wurde versehentlich gelöscht.
  - Ein zeitweiliger Netzwerkfehler führt dazu, dass der Schlüsseltresor nicht verfügbar ist.

- Sie besitzen keine Berechtigungen für den Zugriff auf den Schlüsseltresor, oder der Schlüssel ist nicht vorhanden:
  - Der Schlüssel ist abgelaufen oder wurde versehentlich gelöscht oder deaktiviert.
- Die verwaltete Identität der Azure Database for PostgreSQL-Instanz wurde versehentlich gelöscht.
  - Die Schlüsselberechtigungen der verwalteten Identität der Azure Database for PostgreSQL-Instanz sind unzureichend. Beispielsweise enthalten die Berechtigungen nicht Get, Wrap und Unwrap.
  - Die Berechtigungen der verwalteten Identität für die Azure Database for PostgreSQL-Instanz wurden widerrufen oder gelöscht.

## <a name="identify-and-resolve-common-errors"></a>Identifizieren und Beheben von häufigen Fehlern

### <a name="errors-on-the-key-vault"></a>Fehler in Key Vault

#### <a name="disabled-key-vault"></a>Key Vault deaktiviert

- `AzureKeyVaultKeyDisabledMessage`
- **Erläuterung**: Der Vorgang konnte auf dem Server nicht ausgeführt werden, weil der Azure Key Vault-Schlüssel deaktiviert ist.

#### <a name="missing-key-vault-permissions"></a>Fehlende Berechtigungen für Key Vault

- `AzureKeyVaultMissingPermissionsMessage`
- **Erläuterung** : Der Server verfügt nicht über die erforderlichen Get-, Wrap- und Unwrap-Berechtigungen für Azure Key Vault. Erteilen Sie dem Dienstprinzipal mit der entsprechenden ID alle fehlenden Berechtigungen.

### <a name="mitigation"></a>Minderung

- Vergewissern Sie sich, dass der kundenseitig verwaltete Schlüssel im Schlüsseltresor vorhanden ist.
- Identifizieren Sie den Schlüsseltresor, und navigieren Sie dann im Azure-Portal zum Schlüsseltresor.
- Stellen Sie sicher, dass der Schlüssel-URI einen Schlüssel angibt, der vorhanden ist.

## <a name="next-steps"></a>Nächste Schritte

[Verwenden des Azure-Portals zum Einrichten der Datenverschlüsselung mit einem kundenseitig verwalteten Schlüssel für Azure Database for PostgreSQL](howto-data-encryption-portal.md)
