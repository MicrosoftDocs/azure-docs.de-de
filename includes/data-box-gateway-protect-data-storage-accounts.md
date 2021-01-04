---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 160f85b34b3730b14ed96854d5f60ad81f4eb63b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581099"
---
Ihr Gerät ist einem Speicherkonto zugeordnet, das als Ziel für Ihre Daten in Azure verwendet wird. Der Zugriff auf das Speicherkonto wird über das Abonnement und zwei Zugriffsschlüssel mit 512 Bit gesteuert, die dem Speicherkonto zugeordnet sind.

Einer der Schlüssel dient zur Authentifizierung, wenn das Azure Stack Edge-Gerät auf das Speicherkonto zugreift. Der andere Schlüssel wird in Reserve gehalten, sodass Sie die Schlüssel regelmäßig rotieren können.

Aus Sicherheitsgründen ist in vielen Datencentern eine Schlüsselrotation erforderlich. Es wird empfohlen, diese bewährten Methoden für die Schlüsselrotation zu befolgen:

- Ihr Speicherkontoschlüssel ähnelt dem Stammkennwort für das Speicherkonto. Achten Sie darauf, dass Ihr Kontoschlüssel immer gut geschützt ist. Geben Sie das Kennwort nicht an andere Benutzer weiter, vermeiden Sie, es hart zu codieren, und speichern Sie es nicht als Klartext an einem Ort, auf den andere Benutzer Zugriff haben.
- Generieren Sie Ihren Kontoschlüssel über das Azure-Portal neu, wenn Sie denken, er könnte gefährdet sein. Weitere Informationen finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../articles/storage/common/storage-account-keys-manage.md).
- Der Azure-Administrator sollte den primären oder sekundären Schlüssel regelmäßig über den Abschnitt „Storage“ im Azure-Portal ändern oder neu generieren, um direkt auf das Speicherkonto zuzugreifen.