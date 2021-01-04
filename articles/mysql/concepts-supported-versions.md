---
title: 'Unterstützte Versionen: Azure Database for MySQL'
description: Erfahren Sie, welche Versionen des MySQL-Servers im Dienst Azure Database for MySQL unterstützt werden.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8033117d9e3c31f8aa9bba06afb7c3b1b7bba67f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95751027"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Unterstützte Azure-Datenbank für MySQL-Serverversionen

Azure Database for MySQL wurde basierend auf [MySQL Community Edition](https://www.mysql.com/products/community/) mithilfe der InnoDB-Engine entwickelt.

MySQL verwendet das Benennungsschema „X.Y.Z“. X ist die Hauptversion, Y die Nebenversion und Z die Version zur Fehlerbehebung. Weitere Informationen zum Schema finden Sie in der [MySQL-Dokumentation](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).


> [!NOTE]
> Im Dienst wird ein Gateway verwendet, um die Verbindungen an die Serverinstanzen umzuleiten. Sobald die Verbindung hergestellt ist, zeigt der MySQL-Client die im Gateway festgelegte Version von MySQL an, nicht die tatsächliche Version, die auf Ihrer MySQL-Serverinstanz ausgeführt wird. Um die Version Ihrer MySQL-Serverinstanz zu ermitteln, geben Sie den `SELECT VERSION();`-Befehl an der MySQL-Eingabeaufforderung ein.

Azure Database for MySQL unterstützt derzeit die folgenden Versionen:

## <a name="mysql-version-56"></a>MySQL-Version 5.6

Version zur Fehlerbehebung: 5.6.47

Weitere Informationen zu Verbesserungen und Fehlerbehebungen in dieser Version finden Sie in den [Versionshinweisen](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) zu MySQL.

## <a name="mysql-version-57"></a>MySQL-Version 5.7

Version zur Fehlerbehebung: 5.7.29

Weitere Informationen zu Verbesserungen und Fehlerbehebungen in dieser Version finden Sie in den [Versionshinweisen](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) zu MySQL.

## <a name="mysql-version-80"></a>MySQL, Version 8.0

Version zur Fehlerbehebung: 8.0.15

Weitere Informationen zu Verbesserungen und Fehlerbehebungen in dieser Version finden Sie in den [Versionshinweisen](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) zu MySQL.

## <a name="managing-updates-and-upgrades"></a>Verwalten von Updates und Upgrades
Das Patchen von Versionsupdates zur Fehlerbehebung wird vom Dienst automatisch verwaltet. Beispiel: 5.7.20 bis 5.7.21.  

Ein Upgrade der Hauptversion wird zurzeit vom Dienst für Upgrades von MySQL v5.6 auf v5.7 unterstützt. Weitere Einzelheiten finden Sie unter [Upgraden von Hauptversionen](how-to-major-version-upgrade.md). Wenn Sie von 5.7 auf 8.0 upgraden möchten, sollten Sie eine [Sicherung und dann die Wiederherstellung](./concepts-migrate-dump-restore.md) auf einem Server ausführen, der mit der neuen Engine-Version erstellt wurde.

## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Informationen zu Versionsrichtlinien für Azure Database for MySQL finden Sie in [diesem Dokument](concepts-version-policy.md).
- Informationen zu bestimmten Ressourcenkontingenten und -beschränkungen basierend auf Ihrer **Dienstebene** finden Sie unter [Dienstebenen](./concepts-pricing-tiers.md).
