---
title: Hochverfügbarkeit und Notfallwiederherstellung – Azure Batch | Microsoft-Dokumentation
description: Informationen zum Entwerfen Ihrer Batch-Anwendung für einen regionalen Ausfall
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: b863785575263fedd144b3d599962a8e1559e0a3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60549751"
---
# <a name="design-your-application-for-high-availability"></a>Entwerfen Ihrer Anwendung für Hochverfügbarkeit

Azure Batch ist ein regionaler Dienst. Batch ist in allen Azure-Regionen verfügbar, aber wenn ein Batch-Konto erstellt wird, muss es einer Region zugeordnet werden. Alle Vorgänge für das Batch-Konto gelten dann für diese Region. Beispielsweise werden Pools und die zugehörigen virtuellen Computer (VMs) in der gleichen Region wie das Batch-Konto erstellt.

Beim Entwerfen einer Anwendung, die Batch verwendet, müssen Sie die Möglichkeit in Betracht ziehen, dass Batch in einer Region nicht verfügbar ist. In seltenen Fällen liegt ein Problem mit der Region als Ganzes, dem gesamten Batch-Dienst in der Region oder mit Ihrem Batch-Konto vor.

Wenn die Batch verwendende Anwendung oder Lösung immer verfügbar sein muss, dann sollte sie entweder für ein Failover zu einer anderen Region konzipiert sein, oder die Workload sollte stets auf zwei oder mehr Regionen aufgeteilt sein. Beide Ansätze erfordern mindestens zwei Batch-Konten, und jedes Konto muss sich in einer anderen Region befinden.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Mehrere Batch-Konten in mehreren Regionen

Bei Verwendung mehrerer Batch-Konten in verschiedenen Regionen kann die Ausführung Ihrer Anwendung fortgesetzt werden, wenn ein Batch-Konto in einer anderen Region nicht mehr verfügbar ist. Die Verwendung mehrerer Konten ist besonders wichtig, wenn Ihre Anwendung hochverfügbar sein muss.

In manchen Fällen kann eine Anwendung so konzipiert sein, dass sie immer mindestens zwei Regionen verwendet. Wenn Sie z.B. eine beträchtliche Kapazität benötigen, kann die Verwendung mehrerer Regionen erforderlich sein, um entweder einer umfangreichen Anwendung gerecht zu werden oder für zukünftiges Wachstum vorzusorgen.

## <a name="design-considerations-for-providing-failover"></a>Entwurfsüberlegungen für eine Failovermöglichkeit

Ein wichtiger Punkt bei der Bereitstellung einer Failovermöglichkeit in eine andere Region ist die Berücksichtigung aller Komponenten in einer Lösung; es reicht nicht aus, einfach nur über ein zweites Batch-Konto zu verfügen. Beispielsweise ist in den meisten Batch-Anwendungen ein Azure-Speicherkonto erforderlich, wobei Speicherkonto und Batch-Konto sich in derselben Region befinden müssen, damit die Leistung akzeptabel ist.

Beim Entwerfen einer Failoverlösung sollten Sie folgende Punkte berücksichtigen:

- Erstellen Sie vorab alle erforderlichen Konten in jeder Region, z.B. das Batch-Konto und das Speicherkonto. Häufig fallen für das Erstellen von Konten keine Gebühren an, sondern erst dann, wenn dort Daten gespeichert werden oder das Konto verwendet wird.
- Stellen Sie sicher, dass die Kontingente für die Konten vorab festgelegt werden, damit Sie dem Batch-Konto die erforderliche Anzahl von Kernen zuordnen können.
- Verwenden Sie Vorlagen und/oder Skripte zum Automatisieren der Bereitstellung der Anwendung in einer Region.
- Halten Sie Binärdateien und Verweisdaten der Anwendung in allen Regionen auf dem neuesten Stand. Wenn die Region auf dem neuesten Stand bleibt, ist sichergestellt, dass sie ohne Warten auf das Hochladen und die Bereitstellung von Dateien schnell online geschaltet werden kann. Wird beispielsweise eine benutzerdefinierte Anwendung, die auf Poolknoten installiert werden soll, mit Batch-Anwendungspaketen gespeichert und referenziert, sollte sie, wenn eine neue Version der Anwendung erstellt wird, auf jedes Batch-Konto hochgeladen und von der Poolkonfiguration referenziert werden (oder machen Sie die neue Version zur Standardversion).
- In der Anwendung, die Batch, Speicher und andere Dienste aufruft, sollte problemlos ein Switchover von Clients oder Last zu der anderen Region durchgeführt werden.
- Eine bewährte Methode, um sicherzustellen, dass ein Failover erfolgreich sein wird, ist ein häufiger Switchover zu einer alternativen Region im Rahmen des normalen Betriebs. Führen Sie z.B. bei zwei Bereitstellungen in unterschiedlichen Regionen jeden Monat einen Switchover zu der anderen Region durch.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Erstellen von Batch-Konten mit dem [Azure-Portal](batch-account-create-portal.md), der [Azure-Befehlszeilenschnittstelle](cli-samples.md), [PowerShell](batch-powershell-cmdlets-get-started.md) oder der [Batch-Verwaltungs-API](batch-management-dotnet.md).
- Standardkontingente werden einem Batch-Konto zugeordnet; in [diesem Artikel](batch-quota-limit.md) werden die Standardkontingentwerte und die Erhöhung von Kontingenten ausführlich beschrieben.
