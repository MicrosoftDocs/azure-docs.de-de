---
title: Schreiben von erweiterten R-Funktionen
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Erfahren Sie, wie Sie mithilfe von Machine Learning Services (Vorschauversion) eine R-Funktion für erweiterte statistische Berechnungen in Azure SQL-Datenbank schreiben.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60702451"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Schreiben erweiterter R-Funktionen in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)

In diesem Artikel wird beschrieben, wie mathematische R-Funktionen und Hilfsfunktionen in einer gespeicherten SQL-Prozedur eingebettet werden. Erweiterte statistische Funktionen, die in T-SQL kompliziert zu implementieren sind, können in R mit nur einer einzigen Codezeile verwirklicht werden.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.

- Zum Ausführen des Beispielcodes in diesen Übungen müssen Sie zuerst eine Azure SQL-Datenbank-Instanz mit Machine Learning Services (mit R) aktivieren. Während der öffentlichen Vorschauphase führt Microsoft für Sie das Onboarding durch und aktiviert das maschinelle Lernen für Ihre vorhandene oder neue Datenbank. Führen Sie die Schritte in [Registrieren für die Vorschauversion](sql-database-machine-learning-services-overview.md#signup) aus.

- Vergewissern Sie sich, dass die aktuelle Version von [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) installiert ist. In diesem Schnellstart verwenden Sie SSMS, obwohl Sie auch mithilfe anderer Datenbankverwaltungen oder Abfragetools R-Skripts ausführen können.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Erstellen einer gespeicherten Prozedur zum Generieren von Zufallszahlen

Der Einfachheit halber verwenden wir das R-Paket `stats`, das standardmäßig mit Azure SQL-Datenbank installiert und geladen wird, mit Machine Learning Services (Vorschau). Das Paket enthält Hunderte von Funktionen für allgemeine statistische Aufgaben, darunter die `rnorm`-Funktion. Diese Funktion generiert eine angegebene Anzahl von Zufallszahlen mit Normalverteilung, einer angegebenen Standardabweichung und einem Mittelwert.

Der folgende R-Code gibt beispielsweise 100 Zahlen mit dem Mittelwert 50 zurück, für die eine Standardabweichung von 3 gilt.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Führen Sie zum Aufrufen dieser Zeile in R aus T-SQL `sp_execute_external_script` aus, und fügen Sie die R-Funktion wie folgt in den Parameter des R-Skripts ein:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Wie gehen Sie vor, wenn Sie das Erstellen eines anderen Satzes von Zufallszahlen vereinfachen möchten?

Das ist in Kombination mit SQL ganz einfach. Sie definieren eine gespeicherte Prozedur, die die Argumente vom Benutzer abfragt und dann diese Argumente als Variablen an das R-Skript übergibt.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- Die erste Zeile definiert alle SQL-Eingabeparameter, die beim Ausführen der gespeicherten Prozedur erforderlich sind.

- In der mit `@params` beginnenden Zeile werden alle Variablen, die im R-Code verwendet werden, und die zugehörigen SQL-Datentypen definiert.

- Die unmittelbar darauf folgenden Zeilen ordnen die SQL-Parameternamen den entsprechenden R-Variablennamen zu.

Nachdem Sie die R-Funktion in eine gespeicherte Prozedur eingeschlossen haben, können die Funktion ganz einfach wie folgt aufrufen und dabei jeweils andere Werte übergeben:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Verwenden von R-Hilfsfunktionen für die Problembehandlung

Das standardmäßig installierte Paket `utils` bietet eine Vielzahl von Hilfsfunktionen für die Untersuchung der aktuellen R-Umgebung. Diese Funktionen können insbesondere dann hilfreich sein, wenn Sie Abweichungen zwischen der Ausführung Ihres R-Codes in SQL und in externen Umgebungen feststellen. Sie können z. B. die R-Funktion `memory.limit()` verwenden, um den Arbeitsspeicher für die aktuelle R-Umgebung abzurufen.

Da das Paket `utils` installiert ist, aber nicht standardmäßig geladen wird, laden Sie es zunächst mit der `library()`-Funktion.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Viele Benutzer verwenden gerne die Systemzeitfunktionen in R, z. B. `system.time` und `proc.time`, um die für R-Prozesse aufgewendete Zeit zu erfassen und Leistungsprobleme zu analysieren.
