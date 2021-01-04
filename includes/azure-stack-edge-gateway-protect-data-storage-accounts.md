---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465737"
---
Ihr Gerät ist einem Speicherkonto zugeordnet, das als Ziel für Ihre Daten in Azure verwendet wird. Der Zugriff auf das Speicherkonto wird über das Abonnement und zwei Zugriffsschlüssel mit 512 Bit gesteuert, die dem Speicherkonto zugeordnet sind.

Einer der Schlüssel dient zur Authentifizierung, wenn das Data Box Edge-Gerät auf das Speicherkonto zugreift. Der andere Schlüssel wird in Reserve gehalten, sodass Sie die Schlüssel regelmäßig rotieren können.

Aus Sicherheitsgründen ist in vielen Datencentern eine Schlüsselrotation erforderlich. Es wird empfohlen, diese bewährten Methoden für die Schlüsselrotation zu befolgen:

- Ihr Speicherkontoschlüssel ähnelt dem Stammkennwort für das Speicherkonto. Achten Sie darauf, dass Ihr Kontoschlüssel immer gut geschützt ist. Geben Sie das Kennwort nicht an andere Benutzer weiter, vermeiden Sie, es hart zu codieren, und speichern Sie es nicht als Klartext an einem Ort, auf den andere Benutzer Zugriff haben.
- [Generieren Sie Ihren Kontoschlüssel neu](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) über das Azure-Portal, wenn Sie denken, er könnte gefährdet sein.
- Der Azure-Administrator sollte den primären oder sekundären Schlüssel regelmäßig über den Abschnitt „Storage“ im Azure-Portal ändern oder neu generieren, um direkt auf das Speicherkonto zuzugreifen.