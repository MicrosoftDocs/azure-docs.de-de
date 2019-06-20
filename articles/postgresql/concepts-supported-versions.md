---
title: Unterstützte Versionen in Azure Database for PostgreSQL (Einzelserver)
description: Beschreibung der unterstützten Versionen in Azure Database for PostgreSQL (Einzelserver)
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4dcbaf159fce6b5f5495a6a25a2a3420cad9e5e8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067262"
---
# <a name="supported-postgresql-database-versions"></a>Unterstützte PostgreSQL-Datenbankversionen
Microsoft strebt in Azure Database for PostgreSQL (Einzelserver) die Unterstützung von n-2 Versionen der PostgreSQL-Engine an. Das bedeutet, dass die aktuelle Hauptversion (n) und die beiden vorherigen Hauptversionen (-2) unterstützt werden.

Azure-Datenbank für PostgreSQL unterstützt derzeit die folgenden Versionen:

## <a name="postgresql-version-107"></a>PostgreSQL-Version 10.7
Informationen zu Verbesserungen und Fehlerbehebungen in älteren Versionen finden Sie in der [Dokumentation zu PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html).

## <a name="postgresql-version-9612"></a>PostgreSQL-Version 9.6.12
Informationen zu Verbesserungen und Fehlerbehebungen in älteren Versionen finden Sie in der [Dokumentation zu PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html).

## <a name="postgresql-version-9516"></a>PostgreSQL-Version 9.5.16
Informationen zu Verbesserungen und Fehlerbehebungen in älteren Versionen finden Sie in der [Dokumentation zu PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html).

## <a name="managing-updates-and-upgrades"></a>Verwalten von Updates und Upgrades
Patches für Nebenversionen werden von Azure Database for PostgreSQL automatisch verwaltet. Derzeit werden Upgrades von Hauptversionen nicht unterstützt. Beispielsweise wird das Upgrade von PostgreSQL 9.5 auf PostgreSQL 9.6 nicht unterstützt. Wenn Sie auf die nächste Hauptversion upgraden möchten, erstellen Sie eine Datenbank, und führen Sie für diese eine [Sicherung und Wiederherstellung](./howto-migrate-using-dump-and-restore.md) auf einem Server aus, der mit der neuen Engine-Version erstellt wurde.

## <a name="next-steps"></a>Nächste Schritte
Informationen zur Unterstützung der verschiedenen PostgreSQL-Erweiterungen finden Sie unter [PostgreSQL-Erweiterungen](concepts-extensions.md).
