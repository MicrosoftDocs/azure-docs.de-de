---
title: 'Gewährleisten der Überprüfung von Azure Database for PostgreSQL: Datenverschlüsselung'
description: Erfahren Sie, wie Sie die Verschlüsselung von Azure Database for PostgreSQL (Datenverschlüsselung) mit kundenseitig verwalteten Schlüssel überprüfen.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 7ec27cc4f28151214ca97ffb5113607d6b60ee36
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240578"
---
# <a name="validating-data-encryption-for-azure-database-for-postgresql"></a>Überprüfen der Datenverschlüsselung für Azure Database for PostgreSQL

Dieser Artikel unterstützt Sie bei der Überprüfung, ob die Datenverschlüsselung mit dem kundenseitig verwalteten Schlüssel für Azure Database for PostgreSQL erwartungsgemäß funktioniert.

## <a name="check-the-encryption-status"></a>Überprüfen des Verschlüsselungsstatus

### <a name="from-portal"></a>Über das Portal

1. Wenn Sie überprüfen möchten, ob der Schlüssel des Kunden für die Verschlüsselung verwendet wird, führen Sie die folgenden Schritte aus:

    * Navigieren Sie im Azure-Portal zu **Azure Key Vault** -> **Schlüssel**.
    * Wählen Sie den für Serververschlüsselung verwendeten Schlüssel aus.
    * Legen Sie den Status des Schlüssels **Aktiviert** auf **Nein** fest.
  
       Nach einiger Zeit ( **ungefähr 15 Minuten** ) sollte der **Status** des Azure Database for PostgreSQL-Servers **Zugriff nicht möglich** lauten. Bei allen E/A-Vorgängen, die auf dem Server ausgeführt werden, tritt ein Fehler auf, der bestätigt, dass der Server tatsächlich mit dem Kundenschlüssel verschlüsselt wurde und der Schlüssel aktuell ungültig ist.
    
        Um den Server erneut **Verfügbar** zu machen, können Sie den Schlüssel erneut überprüfen. 
    
    * Legen Sie den Status des Schlüssels in Key Vault auf **Ja** fest.
    * Wählen Sie auf dem Server **Datenverschlüsselung** die Option **Schlüssel erneut überprüfen** aus.
    * Wenn die erneute Überprüfung des Schlüssels erfolgreich war, ändert sich der **Status** des Servers in **Verfügbar**.

2. Wenn Sie im Azure-Portal sicherstellen können, dass der Verschlüsselungsschlüssel festgelegt wurde, werden die Daten mit dem im Portal verwendeten Kundenschlüssel verschlüsselt.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="Zugriffsrichtlinienübersicht":::

### <a name="from-cli"></a>Über die CLI

1. Mit dem Befehl *az CLI* können Sie die Schlüsselressourcen validieren, die für den Azure Database for PostgreSQL-Server verwendet werden.

    ```azurecli-interactive
   az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    Bei einem Server ohne festgelegte Datenverschlüsselung führt dieser Befehl zu einem leeren Satz [].

### <a name="azure-audit-reports"></a>Azure-Überwachungsberichte

[Überwachungsberichte](https://servicetrust.microsoft.com), die Informationen zur Einhaltung von Datenschutzstandards und gesetzlichen Anforderungen bereitstellen, können ebenfalls überprüft werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Datenverschlüsselung finden Sie unter [Datenverschlüsselung auf Azure Database for PostgreSQL-Einzelservern mit kundenseitig verwalteten Schlüsseln](concepts-data-encryption-postgresql.md).