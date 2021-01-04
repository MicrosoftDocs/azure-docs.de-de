---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 648c2e4ffc63dad42a87888e7cb5629fb7301eea
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026095"
---
Beachten Sie diese bewährten Methoden:

- Es empfiehlt sich, alle Kennwörter an einem sicheren Ort zu speichern, sodass Sie ein Kennwort nicht zurücksetzen müssen, wenn Sie es vergessen haben. Der Verwaltungsdienst kann vorhandene Kennwörter nicht abrufen. Er kann diese nur über das Azure-Portal zurücksetzen. Wenn Sie ein Kennwort zurücksetzen, dürfen Sie nicht vergessen, zuvor alle Benutzer zu benachrichtigen.
- Auf die Windows PowerShell-Oberfläche Ihres Geräts können Sie remote über HTTP zugreifen. Aus Sicherheitsgründen empfiehlt es sich, HTTP nur in vertrauenswürdigen Netzwerken zu verwenden.
- Gerätekennwörter müssen sicher und gut geschützt sein. Berücksichtigen Sie die [bewährten Methoden für Kennwörter](../articles/security/fundamentals/identity-management-best-practices.md#enable-password-management).