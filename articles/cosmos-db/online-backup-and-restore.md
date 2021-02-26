---
title: Onlinesicherung und bedarfsgesteuerte Datenwiederherstellung in Azure Cosmos DB
description: In diesem Artikel wird beschrieben, wie die automatische Sicherung und die bedarfsgesteuerte Datenwiederherstellung funktionieren. Außerdem wird der Unterschied zwischen den Modi für die fortlaufende und regelmäßige Sicherung erläutert.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525431"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Onlinesicherung und bedarfsgesteuerte Wiederherstellung in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB erstellt in regelmäßigen Abständen automatisch Sicherungen Ihrer Daten. Die automatischen Sicherungen erfolgen ohne Beeinträchtigung der Leistung oder Verfügbarkeit des Datenbankbetriebs. Alle Sicherungen werden separat in einem Speicherdienst gespeichert. Automatische Sicherungen sind in Situationen hilfreich, in denen Sie versehentlich Ihr Azure Cosmos-Konto, die Datenbank oder einen Container löschen oder aktualisieren und später eine Datenwiederherstellung durchführen möchten. Es gibt zwei Sicherungsmodi:

* **Regelmäßiger Sicherungsmodus:** Dieser Modus ist der Standardsicherungsmodus für alle vorhandenen Konten. In diesem Modus wird in regelmäßigen Abständen eine Sicherung durchgeführt, und die Datenwiederherstellung erfolgt über eine Anforderung an das Supportteam. In diesem Modus konfigurieren Sie ein Sicherungsintervall und eine Aufbewahrungsdauer für Ihr Konto. Die maximale Beibehaltungsdauer beträgt einen Monat. Das Mindestintervall für Sicherungen beträgt eine Stunde.  Weitere Informationen finden Sie im Artikel zum [regelmäßigen Sicherungsmodus](configure-periodic-backup-restore.md).

* **Fortlaufender Sicherungsmodus** (zurzeit in der öffentlichen Vorschau): Sie wählen diesen Modus beim Erstellen des Azure Cosmos DB-Kontos aus. Dieser Modus ermöglicht Ihnen die Wiederherstellung zu jedem beliebigen Zeitpunkt innerhalb der letzten 30 Tage. Weitere Informationen finden Sie in der [Einführung in den fortlaufenden Sicherungsmodus](continuous-backup-restore-introduction.md). Informationen zum Konfigurieren der fortlaufenden Sicherung finden Sie in den Artikeln zum [Azure-Portal](continuous-backup-restore-portal.md), zu [PowerShell](continuous-backup-restore-powershell.md), zur [Befehlszeilenschnittstelle](continuous-backup-restore-command-line.md) und zu [Resource Manager](continuous-backup-restore-template.md).

  > [!NOTE]
  > Wenn Sie ein neues Konto mit fortlaufender Sicherung konfigurieren, können Sie eine Self-Service-Wiederherstellung über Azure-Portal, PowerShell oder die Befehlszeilenschnittstelle durchführen. Wenn Ihr Konto im fortlaufenden Modus konfiguriert ist, können Sie nicht wieder in den regelmäßigen Modus wechseln. Derzeit können bestehende Konten im regelmäßigen Sicherungsmodus nicht auf den fortlaufenden Modus umgestellt werden.  

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich als Nächstes, wie Sie die Modi für fortlaufende und regelmäßige Sicherung für Ihr Konto konfigurieren und verwalten:

* [Konfigurieren und verwalten Sie eine Richtlinie für die regelmäßige Sicherung](configure-periodic-backup-restore.md).
* Was ist der Modus für die [fortlaufende Sicherung](continuous-backup-restore-introduction.md)?
* Konfigurieren und verwalten Sie die fortlaufende Sicherung über das [Azure-Portal](continuous-backup-restore-portal.md) oder aber mithilfe von [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) oder [Azure Resource Manager](continuous-backup-restore-template.md).
* [Verwalten Sie Berechtigungen](continuous-backup-restore-permissions.md), die zum Wiederherstellen von Daten mit dem fortlaufenden Sicherungsmodus erforderlich sind.
