---
title: 'Unterstützte Versionen: Azure Database for MariaDB'
description: Erfahren Sie, welche Versionen des MariaDB-Servers im Dienst Azure Database for MariaDB unterstützt werden.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: dbf32626714200e6712c67b701ebc597c4a7ba7e
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541027"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Unterstützte Serverversionen von Azure Database for MariaDB

Azure Database for MariaDB wurde mithilfe der InnoDB-Engine auf der Grundlage von [MariaDB Server](https://downloads.mariadb.org/) entwickelt, einem Open-Source-Produkt.

MariaDB verwendet das Benennungsschema „X.Y.Z“. X ist die Hauptversion, Y die Nebenversion und Z die Patchversion.

> [!NOTE]
> Im Dienst wird ein Gateway verwendet, um die Verbindungen an die Serverinstanzen umzuleiten. Sobald die Verbindung hergestellt ist, zeigt der MySQL-Client die im Gateway festgelegte Version von MariaDB an, nicht die tatsächliche Version, die auf Ihrer MariaDB-Serverinstanz ausgeführt wird. Wenn Sie die Version Ihrer MariaDB-Serverinstanz ermitteln möchten, verwenden Sie den Befehl `SELECT VERSION();`.

Azure Database for MariaDB unterstützt derzeit die folgende Version:

## <a name="mariadb-version-102"></a>MariaDB Version 10.2

Patchversion: 10.2.32

Weitere Informationen zu Verbesserungen und Fehlerbehebungen in dieser Version finden Sie in der [Dokumentation zu MariaDB](https://mariadb.com/kb/en/mariadb-10232-release-notes/).

## <a name="mariadb-version-103"></a>MariaDB Version 10.3

Patchversion: 10.3.23

Weitere Informationen zu Verbesserungen und Fehlerbehebungen in dieser Version finden Sie in der [Dokumentation zu MariaDB](https://mariadb.com/kb/en/mariadb-10323-release-notes/).

## <a name="managing-updates-and-upgrades"></a>Verwalten von Updates und Upgrades
Der Dienst verwaltet Upgrades für Patchupdates automatisch. Beispiel: 10.2.21 auf 10.2.23.  

Zurzeit werden Upgrades von Neben- und Hauptversionen nicht unterstützt. Ein Upgrade von MariaDB 10.2 auf MariaDB 10.3 wird beispielsweise nicht unterstützt. Wenn Sie von 10.2 auf 10.3 aktualisieren möchten, führen Sie eine [Sicherung und Wiederherstellung](./howto-migrate-dump-restore.md) auf einem Server aus, der mit der neuen Modulversion erstellt wurde.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu bestimmten Ressourcenkontingenten und -beschränkungen basierend auf Ihrer **Dienstebene** finden Sie unter [Dienstebenen](./concepts-pricing-tiers.md).
