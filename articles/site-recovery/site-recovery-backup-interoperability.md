---
title: Unterstützung für die Verwendung von Azure Site Recovery mit Azure Backup | Microsoft-Dokumentation
description: Bietet einen Überblick darüber, wie Azure Site Recovery und Azure Backup zusammen verwendet werden können.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035773"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Unterstützung für die Verwendung von Site Recovery mit Azure Backup

Dieser Artikel fasst die Unterstützung für die Verwendung von [Site Recovery](site-recovery-overview.md) mit [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) zusammen.

**Aktion** | **Site Recovery-Unterstützung** | **Details**
--- | --- | ---
**Gemeinsame Bereitstellung der Dienste** | Unterstützt | Dienste sind interoperabel und können zusammen konfiguriert werden.
**Datensicherung/-wiederherstellung** | Unterstützt | Wenn Sicherung und Replikation für eine VM aktiviert sind und Sicherungen durchgeführt werden, gibt es kein Problem bei der Wiederherstellung von Dateien auf den quellseitigen VMs oder Gruppen von VMs. Die Replikation wird wie gewohnt fortgesetzt, ohne dass sich der Zustand der Replikation ändert.
**Datenträgersicherung/-wiederherstellung** | Aktuell keine Unterstützung | Wenn Sie einen gesicherten Datenträger wiederherstellen, müssen Sie die Replikation für die VM erneut deaktivieren und wieder aktivieren.
**VM-Sicherung/-Wiederherstellung** | Aktuell keine Unterstützung | Wenn Sie eine VM oder eine Gruppe von VMs sichern oder wiederherstellen, müssen Sie die Replikation für die VM deaktivieren und wieder aktivieren.  


