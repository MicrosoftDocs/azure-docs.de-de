---
title: Verwenden eindeutiger Schlüssel in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie eindeutige Schlüssel in Ihrer Azure Cosmos-Datenbank verwenden.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: af3c7771ce977cf248c5f1b61ba1c535a10ccd3c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242514"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Einschränkungen für eindeutige Schlüssel in Azure Cosmos DB

Eindeutige Schlüssel fügen einem Azure Cosmos-Container eine Datenintegritätsebene hinzu. Eine Richtlinie für eindeutige Schlüssel wird erstellt, wenn Sie einen Azure Cosmos-Container erstellen. Mit eindeutigen Schlüsseln stellen Sie die Eindeutigkeit von Werten innerhalb einer logischen Partition sicher. Sie können auch die Eindeutigkeit pro [Partitionsschlüssel](partition-data.md) gewährleisten. 

Nachdem Sie einen Container mit einer Richtlinie für eindeutige Schlüssel erstellt haben, wird verhindert, dass innerhalb einer logischen Partition neue oder aktualisierte Kopien vorhandener Elemente erstellt werden (gemäß der Einschränkung für eindeutige Schlüssel). Die Kombination aus Partitionsschlüssel und eindeutigem Schlüssel garantiert die Eindeutigkeit eines Elements innerhalb des Containerbereichs.

Stellen Sie sich beispielsweise einen Azure Cosmos-Container mit E-Mail-Adresse als Einschränkung für eindeutige Schlüssel und `CompanyID` als Partitionsschlüssel vor. Wenn Sie die E-Mail-Adresse des Benutzers mit einem eindeutigen Schlüssel konfigurieren, verfügt jedes Element innerhalb einer bestimmten Unternehmens-ID (`CompanyID`) über eine eindeutige e-Mail-Adresse. Es können keine zwei Elemente mit der gleichen E-Mail-Adresse und dem gleichen Partitionsschlüsselwert erstellt werden. 

Um Elemente mit derselben E-Mail-Adresse, nicht jedoch mit derselben Kombination aus Vorname, Nachname und E-Mail-Adresse, zu erstellen, fügen Sie der Richtlinie für eindeutige Schlüssel weitere Pfade hinzu. Anstelle eines eindeutigen Schlüssels, der nur auf der E-Mail-Adresse basiert, können Sie auch einen eindeutigen Schlüssel mit einer Kombination aus Vorname, Nachname und E-Mail-Adresse erstellen. Dieser Schlüssel wird als einen zusammengesetzter eindeutiger Schlüssel bezeichnet. In diesem Fall ist jede eindeutige Kombination der drei Werte innerhalb einer bestimmten Unternehmens-ID (`CompanyID`) zulässig. 

Der Container kann also beispielsweise Elemente mit den folgenden Werten enthalten, wobei jedes Element der Einschränkung für eindeutige Schlüssel entspricht.

|CompanyID|Vorname|Nachname|E-Mail-Adresse|
|---|---|---|---|
|Contoso|Helga|Kohler|gaby@contoso.com |
|Contoso|Helga|Kohler|gaby@fabrikam.com|
|Fabrikam|Helga|Kohler|gaby@fabrikam.com|
|Fabrikam|Simon|Kohler|gaby@fabrikam.com|
|Fabrikam|   |Kohler|gaby@fabraikam.com|
|Fabrikam|   |   |gaby@fabraikam.com|

Wenn Sie versuchen, ein anderes Element mit den in der vorherigen Tabelle aufgeführten Kombinationen einzufügen, erhalten Sie einen Fehler. Der Fehler weist darauf hin, dass die Einschränkung für eindeutige Schlüssel nicht erfüllt wurde. Sie erhalten als Antwortnachricht `Resource with specified ID or name already exists` oder `Resource with specified ID, name, or unique index already exists`. 

## <a name="define-a-unique-key"></a>Definieren eines eindeutigen Schlüssels

Eindeutige Schlüssel können Sie nur beim Erstellen eines Azure Cosmos-Containers definieren. Ein eindeutiger Schlüssel ist auf eine logische Partition begrenzt. Wenn Sie den Container im vorherigen Beispiel auf der Grundlage der Postleitzahl partitionieren, kommt es in jeder logischen Partition zu duplizierten Elementen. Berücksichtigen Sie beim Erstellen eindeutiger Schlüssel die folgenden Eigenschaften:

* Sie können einen bereits vorhandenen Container nicht für die Verwendung eines anderen eindeutigen Schlüssels aktualisieren. Anders ausgedrückt: Nachdem ein Container mit einer Richtlinie für eindeutige Schlüssel erstellt wurde, kann die Richtlinie nicht mehr geändert werden.

* Um einen eindeutigen Schlüssel für einen bestehenden Container festzulegen, erstellen Sie einen neuen Container mit der Einschränkung für eindeutige Schlüssel. Verwenden Sie das entsprechende Datenmigrationstool, um die Daten aus dem vorhandenen Container in den neuen Container zu verschieben. Verwenden Sie für SQL-Container das [Datenmigrationstool](import-data.md), um die Daten zu verschieben. Verwenden Sie für MongoDB-Container [„mongoimport.exe“ oder „mongorestore.exe“](mongodb-migrate.md), um die Daten zu verschieben.

* Eine Richtlinie für eindeutige Schlüssel kann maximal 16 Pfadwerte enthalten. Die Werte können z. B. `/firstName`, `/lastName` und `/address/zipCode` lauten. Jede Richtlinie für eindeutige Schlüssel kann höchstens 10 Einschränkungen auf eindeutige Schlüssel oder Kombinationen enthalten. Die kombinierten Pfade für jede Einschränkung für eindeutige Schlüssel dürfen 60 Byte nicht überschreiten. Die Kombination aus Vorname, Nachname und E-Mail-Adresse im vorherigen Beispiel ist eine Einschränkung. Diese Einschränkung verwendet drei der 16 möglichen Pfade.

* Wenn ein Container über eine Richtlinie für eindeutige Schlüssel verfügt, erhöhen sich die Kosten für [Anforderungseinheiten (Request Units, RUs)](request-units.md) zum Erstellen, Aktualisieren und Löschen eines Elements geringfügig.

* Eindeutige Schlüssel mit geringer Dichte werden nicht unterstützt. Fehlende Werte für eindeutige Pfade werden als Nullwert behandelt und als solche in die Eindeutigkeitseinschränkung einbezogen. Aus diesem, Grunde kann nur ein einzelnes Element mit Nullwert vorhanden sein, um diese Einschränkung zu erfüllen.

* Bei den Namen eindeutiger Schlüssel wird die Groß-/Kleinschreibung berücksichtigt. Stellen Sie sich beispielsweise einen Container vor, dessen Einschränkung für eindeutige Schlüssel auf `/address/zipcode` festgelegt ist. Wenn Ihre Daten ein Feld mit dem Namen `ZipCode` enthalten, fügt Azure Cosmos DB „null“ als eindeutigen Schlüssel ein, da sich `zipcode` von `ZipCode` unterscheidet. Aufgrund der Berücksichtigung der Groß-/Kleinschreibung können die übrigen Datensätze mit „ZipCode“ nicht eingefügt werden, da der duplizierte Nullwert gegen die Einschränkung für eindeutige Schlüssel verstößt.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [logische Partitionen](partition-data.md).
* Erkunden Sie, [wie eindeutige Schlüssel beim Erstellen eines Containers definiert werden](how-to-define-unique-keys.md)
