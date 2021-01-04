---
title: Wichtige Unterschiede bei Machine Learning Services (Vorschauversion)
description: In diesem Artikel werden die wichtigsten Unterschiede zwischen Machine Learning Services in SQL Managed Instance und SQL Server Machine Learning Services beschrieben.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 10/26/2020
ms.openlocfilehash: c806c0a13f9f5f13588b780054d1f285beb44802
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324532"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Wichtige Unterschiede zwischen Machine Learning Services in Azure SQL Managed Instance und SQL Server

Die Funktionalität von [Machine Learning Services in SQL Managed Instance (Vorschauversion)](machine-learning-services-overview.md) ist nahezu identisch mit [SQL Server Machine Learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning). Nachfolgend finden Sie einige wesentliche Unterschiede.

> [!IMPORTANT]
> Machine Learning Services in SQL Managed Instance befindet sich in der öffentlichen Vorschauphase. Informationen zur Registrierung finden Sie unter [Registrieren für die Vorschauversion](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Einschränkungen der Vorschau

Während der Vorschauphase gelten die folgenden Einschränkungen für den Dienst:

- Loopbackverbindungen funktionieren nicht (siehe [Loopbackverbindung zu SQL Server über ein Python- oder R-Skript](/sql/machine-learning/connect/loopback-connection)).
- Externe Ressourcenpools werden nicht unterstützt.
- Nur Python und R werden unterstützt. Externe Sprachen wie Java können nicht hinzugefügt werden.
- Szenarios, in denen [Message Passing Interface](/message-passing-interface/microsoft-mpi) (MPI) verwendet wird, werden nicht unterstützt.

Aktualisieren Sie das Servicelevelziel im Fall einer Änderung an diesem, und erstellen Sie ein Supportticket, um die dedizierten Ressourceneinschränkungen für R/Python wieder zu aktivieren.

## <a name="language-support"></a>Sprachunterstützung

Das [Erweiterbarkeitsframework](/sql/advanced-analytics/concepts/extensibility-framework) von Python und R wird in Machine Learning Services in SQL Managed Instance und SQL Server unterstützt. Folgende wichtige Unterschiede bestehen:

- Die ersten Versionen von Python und R unterscheiden sich zwischen Machine Learning Services in SQL Managed Instance und SQL Server:

  | System               | Python | R     |
  |----------------------|--------|-------|
  | Verwaltete SQL-Instanz | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Es ist nicht erforderlich, `external scripts enabled` über `sp_configure` zu konfigurieren. Nachdem Sie für die Vorschauversion [registriert](machine-learning-services-overview.md#signup) sind, wird für Ihre Azure SQL Managed Instance maschinelles Lernen aktiviert.

## <a name="packages"></a>Pakete

Die Paketverwaltung von Python und R unterscheidet sich zwischen SQL Managed Instance und SQL Server. Die folgenden Unterschiede bestehen:

- Es gibt keine Unterstützung für Pakete, die von externen Runtimes (z.B. Java) abhängig sind oder Zugriff auf Betriebssystem-APIs für die Installation oder Verwendung benötigen.
- Pakete können ausgehende Netzwerkaufrufe tätigen (Änderung einer früheren Version der Vorschau). Sie können die entsprechenden Sicherheitsregeln für ausgehenden Datenverkehr auf Ebene von [Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md) festlegen, um ausgehende Netzwerkaufrufe zuzulassen.

Weitere Informationen zum Verwalten von Python- und R-Paketen finden Sie unter:

- [Abrufen von Paketinformationen für Python](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Abrufen von R-Paketinformationen](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Ressourcengovernance

Es ist nicht möglich, R-Ressourcen durch [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) und externe Ressourcenpools zu beschränken.

In der öffentlichen Vorschauversion sind R-Ressourcen auf ein Maximum von 20 % der SQL Managed Instance-Ressourcen festgelegt. Das richtet sich nach der ausgewählten Dienstebene. Weitere Informationen finden Sie unter [Kaufmodelle für Azure SQL-Datenbank](../database/purchasing-models.md).

### <a name="insufficient-memory-error"></a>Fehler bei nicht ausreichendem Arbeitsspeicher

Wenn für R nicht genügend Arbeitsspeicher verfügbar ist, erhalten Sie eine Fehlermeldung. Häufige Fehlermeldungen:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Die Arbeitsspeichernutzung hängt von der in R-Skripts verwendeten Anzahl und von der Anzahl parallel ausgeführter Abfragen ab. Wenn die obigen Fehlermeldungen angezeigt werden, können Sie Ihre Datenbank zur Lösung dieses Problems auf eine höhere Dienstebene hochstufen.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Machine Learning Services in Azure SQL Managed Instance](machine-learning-services-overview.md).
- Weitere Informationen zur Verwendung von Python in Machine Learning Services finden Sie unter [Ausführen von Python-Skripts](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Informationen zur Verwendung von R in Machine Learning Services finden Sie unter [Schnellstart: Ausführen einfacher R-Skripts mit SQL Machine Learning](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).