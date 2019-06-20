---
title: Durchführen eines Upgrades für den Azure Backup-Agent
description: In diesem Artikel wird erläutert, warum Sie ein Upgrade für den Azure Backup-Agent durchführen sollten und wo das Upgrade heruntergeladen werden kann.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 56310b7356dd9e263238234cf3e28bd498fa70fc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178447"
---
## <a name="upgrade-the-mars-agent"></a>Durchführen eines Upgrades für den MARS-Agent

Versionen des MARS-Agents (Microsoft Azure Recovery Service) unter 2.0.9083.0 weisen eine Abhängigkeit vom Azure Access Control Service (ACS) auf. Der MARS-Agent wird auch als Azure Backup-Agent bezeichnet. 2018 wird [der Azure Access Control Service (ACS) in Azure als veraltet gekennzeichnet](../articles/active-directory/develop/active-directory-acs-migration.md). Seit dem 19. März 2018 treten in allen Versionen des MARS-Agents unter 2.0.9083.0 Sicherungsfehler auf. Um Sicherungsfehler zu vermeiden oder zu beheben, [führen Sie ein Upgrade für den MARS-Agent auf die neueste Version durch](https://go.microsoft.com/fwlink/?linkid=229525). Um Server zu ermitteln, bei denen ein Upgrade für den MARS-Agent erforderlich ist, führen Sie die Schritte im [Sicherungsblog zur Durchführung eines Upgrades für MARS-Agents](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/) durch. Der MARS-Agent wird verwendet, um Dateien, Ordner und Systemstatusdaten in Azure sichern. System Center DPM und Azure Backup Server verwenden den MARS-Agent zum Sichern von Daten in Azure.
