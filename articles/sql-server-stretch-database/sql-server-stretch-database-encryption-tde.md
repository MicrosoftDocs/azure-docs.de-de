---
title: Aktivieren von Transparent Data Encryption für Stretch Database
description: Aktivieren von Transparent Data Encryption (TDE) für SQL Server Stretch Database über Azure
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b016987162cc8202b7ad28d4dd8e5ab2953469d1
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024244"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Aktivieren von Transparent Data Encryption (TDE) für Stretch Database in Azure
> [!div class="op_single_selector"]
> * [Azure portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent Data Encryption (TDE) bietet Schutz vor der Bedrohung durch böswillige Aktivitäten. Hierzu werden die Datenbank, die dazugehörigen Sicherungen und die Transaktionsprotokolldateien im Ruhezustand in Echtzeit ver- und entschlüsselt, ohne dass Änderungen der Anwendung erforderlich sind.

TDE verschlüsselt die Speicherung einer gesamten Datenbank, indem ein symmetrischer Schlüssel verwendet wird, der als Datenbankverschlüsselungsschlüssel bezeichnet wird. Der Datenbank-Verschlüsselungsschlüssel ist mit einem integrierten Serverzertifikat geschützt. Das integrierte Serverzertifikat ist für jeden Azure-Server einmalig. Microsoft tauscht diese Zertifikate mindestens alle 90 Tage automatisch untereinander. Eine allgemeine Beschreibung von TDE finden Sie unter [Transparente Datenverschlüsselung (TDE)].

## <a name="enabling-encryption"></a>Aktivieren der Verschlüsselung
Befolgen Sie folgende Schritte zum Aktivieren von TDE für eine Azure-Datenbank, die die Daten speichert, die aus einer SQL Server Datenbank migriert wurden, für die Stretch aktiviert ist:

1. Öffnen Sie die Datenbank im [Azure-Portal](https://portal.azure.com)
2. Klicken Sie im Datenbank-Blatt auf die Schaltfläche **Einstellungen** .
3. Wählen Sie die Option **Transparent Data Encryption** aus. ![Screenshot des Azure-Portals mit angezeigtem Blatt „Einstellungen“. Im Abschnitt „Allgemein“ ist die Option „Transparent Data Encryption“ hervorgehoben.][1]
4. Wählen Sie die Einstellung **Ein** aus und dann **Speichern**.
   ![Screenshot des Azure-Portals mit angezeigtem Blatt „Transparent Data Encryption“. Die Datenverschlüsselung ist aktiviert, und die Schaltfläche „Speichern“ ist hervorgehoben.][2]

## <a name="disabling-encryption"></a>Deaktivieren der Verschlüsselung
Befolgen Sie folgende Schritte zum Deaktivieren von TDE für eine Azure-Datenbank, die die Daten speichert, die aus einer SQL Server-Datenbank migriert wurden, für die Stretch aktiviert ist:

1. Öffnen Sie die Datenbank im [Azure-Portal](https://portal.azure.com)
2. Klicken Sie im Datenbank-Blatt auf die Schaltfläche **Einstellungen** .
3. Wählen Sie die Option **Transparten Data Encryption** aus.
4. Wählen Sie die Einstellung **Aus** aus und wählen Sie anschließend **Speichern** aus.

<!--Anchors-->
[Transparente Datenverschlüsselung (TDE)]: /sql/relational-databases/security/encryption/transparent-data-encryption


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->