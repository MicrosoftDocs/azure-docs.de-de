---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: b5f642eafa7792872bdd6f49141d2b6b1412cb78
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581097"
---
Beachten Sie diese bewährten Methoden:

- Es empfiehlt sich, alle Kennwörter an einem sicheren Ort zu speichern, sodass Sie ein Kennwort nicht zurücksetzen müssen, wenn Sie es vergessen haben. Der Verwaltungsdienst kann vorhandene Kennwörter nicht abrufen. Er kann diese nur über das Azure-Portal zurücksetzen. Wenn Sie ein Kennwort zurücksetzen, dürfen Sie nicht vergessen, zuvor alle Benutzer zu benachrichtigen.
- Auf die Windows PowerShell-Oberfläche Ihres Geräts können Sie remote über HTTP zugreifen. Aus Sicherheitsgründen empfiehlt es sich, HTTP nur in vertrauenswürdigen Netzwerken zu verwenden.
- Gerätekennwörter müssen sicher und gut geschützt sein. Berücksichtigen Sie die [bewährten Methoden für Kennwörter](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices#enable-password-management).