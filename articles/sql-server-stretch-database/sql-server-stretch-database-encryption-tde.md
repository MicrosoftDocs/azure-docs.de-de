---
title: Aktivieren von Transparent Data Encryption für Stretch Database – Azure | Microsoft-Dokumentation
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
ms.openlocfilehash: 61f556476958484b78b9c3dff2583eb6db043637
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003041"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Aktivieren von Transparent Data Encryption (TDE) für Stretch Database in Azure
> [!div class="op_single_selector"]
> * [Azure-Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent Data Encryption (TDE) bietet Schutz vor der Bedrohung durch böswillige Aktivitäten. Hierzu werden die Datenbank, die dazugehörigen Sicherungen und die Transaktionsprotokolldateien im Ruhezustand in Echtzeit ver- und entschlüsselt, ohne dass Änderungen der Anwendung erforderlich sind.

TDE verschlüsselt die Speicherung einer gesamten Datenbank, indem ein symmetrischer Schlüssel verwendet wird, der als Datenbankverschlüsselungsschlüssel bezeichnet wird. Der Datenbank-Verschlüsselungsschlüssel ist mit einem integrierten Serverzertifikat geschützt. Das integrierte Serverzertifikat ist für jeden Azure-Server einmalig. Microsoft führt für diese Zertifikate nach spätestens 90 Tagen automatisch eine Rotation durch. Eine allgemeine Beschreibung von TDE finden Sie unter [Transparente Datenverschlüsselung (TDE)].

## <a name="enabling-encryption"></a>Aktivieren der Verschlüsselung
Befolgen Sie folgende Schritte zum Aktivieren von TDE für eine Azure-Datenbank, die die Daten speichert, die aus einer SQL Server Datenbank migriert wurden, für die Stretch aktiviert ist:

1. Öffnen Sie die Datenbank im [Azure-Portal](https://portal.azure.com)
2. Klicken Sie im Datenbank-Blatt auf die Schaltfläche **Einstellungen** .
3. Wählen Sie die Option **Transparten Data Encryption** aus. ![][1]
4. Wählen Sie die Einstellung **Ein** aus und wählen Sie anschließend **Speichern**
   ![][2] aus.

## <a name="disabling-encryption"></a>Deaktivieren der Verschlüsselung
Befolgen Sie folgende Schritte zum Deaktivieren von TDE für eine Azure-Datenbank, die die Daten speichert, die aus einer SQL Server-Datenbank migriert wurden, für die Stretch aktiviert ist:

1. Öffnen Sie die Datenbank im [Azure-Portal](https://portal.azure.com)
2. Klicken Sie im Datenbank-Blatt auf die Schaltfläche **Einstellungen** .
3. Wählen Sie die Option **Transparten Data Encryption** aus.
4. Wählen Sie die Einstellung **Aus** aus und wählen Sie anschließend **Speichern** aus.

<!--Anchors-->
[Transparente Datenverschlüsselung (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
