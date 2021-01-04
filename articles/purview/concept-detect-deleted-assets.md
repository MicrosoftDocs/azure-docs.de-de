---
title: So werden gelöschte Ressourcen bei Scans erkannt
description: In diesem Artikel wird erläutert, wie ein Azure Purview-Konto gelöschte Assets während Überprüfungen erkennt.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551067"
---
# <a name="how-scans-detect-deleted-assets"></a>So werden gelöschte Ressourcen bei Scans erkannt

In diesem Artikel wird beschrieben, wie Azure Purview Überprüfungsergebnisse zum Erkennen gelöschter Ressourcen verwendet.

## <a name="background-info"></a>Hintergrundinformationen

Ein Azure Purview-Katalog kennt den Status eines Datenspeichers nur, wenn er ihn überprüft. Damit der Katalog weiß, ob eine Datei, eine Tabelle oder ein Container gelöscht wurde, vergleicht er die letzte Scanausgabe mit der aktuellen Scanausgabe. Angenommen, die letzte Überprüfung eines Azure Data Lake Storage Gen2-Kontos umfasste einen Ordner mit dem Namen *folder1*. Bei erneuter Überprüfung desselben Kontos ist *folder1* nicht vorhanden. Daher geht der Katalog davon aus, dass der Ordner gelöscht wurde.

## <a name="detecting-deleted-files"></a>Erkennen gelöschter Dateien

Die Logik zum Erkennen fehlender Dateien funktioniert sowohl bei mehreren Scans durch denselben Benutzer als auch durch andere Benutzer. Beispiel: Ein Benutzer führt einen einmaligen Scan für einen Data Lake Storage Gen2-Datenspeicher in den Ordnern A, B und C aus. Zu einem späteren Zeitpunkt führt ein anderer Benutzer im gleichen Konto eine andere einmalige Überprüfung der Ordner C, D und E desselben Datenspeichers aus. Da der Ordner C zweimal gescannt wurde, überprüft der Katalog ihn auf mögliche Löschungen. Die Ordner A, B, D und E wurden jedoch nur einmal gescannt, und der Katalog überprüft sie nicht auf gelöschte Ressourcen.

Um gelöschte Dateien aus Ihrem Katalog herauszuhalten, ist es wichtig, reguläre Scans auszuführen. Das Überprüfungsintervall ist wichtig, da der Katalog gelöschte Ressourcen erst erkennen kann, wenn eine andere Überprüfung ausgeführt wird. Wenn Sie also einmal pro Monat in einem bestimmten Speicher Überprüfungen ausführen, kann der Katalog gelöschte Datenressourcen in diesem Speicher erst erkennen, wenn Sie einen Monat später die nächste Überprüfung ausführen.

Wenn Sie große Datenspeicher wie Data Lake Storage Gen2 auflisten, gibt es mehrere Möglichkeiten (einschließlich Enumerationsfehlern und gelöschter Ereignisse), Informationen zu übersehen. Eine bestimmte Überprüfung könnte übersehen, dass eine Datei erstellt oder gelöscht wurde. Solange der Katalog nicht sicher ist, dass eine bestimmte Datei gelöscht wurde, wird sie nicht aus dem Katalog gelöscht. Diese Strategie bedeutet, dass Fehler auftreten können, wenn eine Datei, die im überprüften Datenspeicher nicht vorhanden ist, noch im Katalog vorhanden ist. In einigen Fällen muss ein Datenspeicher möglicherweise zwei oder drei Mal gescannt werden, bevor bestimmte gelöschte Ressourcen abgefangen werden.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Azure Purview-Katalogen finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md)
