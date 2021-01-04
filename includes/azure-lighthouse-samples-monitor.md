---
title: include file
description: include file
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/11/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0056e18b6cb3aad2a4504bbe20b3b3421793489e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356265"
---
Diese Beispiele zeigen, wie Sie Azure Monitor zum Erstellen von Warnungen für Abonnements verwenden, für die ein Onboarding für die delegierte Azure-Ressourcenverwaltung durchgeführt wurde.

| **Vorlage** | **Beschreibung** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Fragt die Aktivitäten des letzten Tages ab und [erstellt einen Bericht über alle hinzugefügten oder entfernten Delegierungen](../articles/lighthouse/how-to/monitor-delegation-changes.md) (bzw. nicht erfolgreiche Versuche).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Mit dieser Vorlage werden eine Azure-Warnung erstellt und eine Verbindung mit einer vorhandenen Aktionsgruppe hergestellt.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Erstellt mehrere Protokollwarnungen basierend auf Kusto-Abfragen.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Stellt eine Warnung in einem Mandanten bereit, wenn ein Benutzer ein Abonnement an einen verwalteten Mandanten delegiert.|
| [workbook-activitylogs-by-domain](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) | Hiermit werden Azure-Aktivitätsprotokolle abonnementübergreifend mit einer Option zum Filtern nach Domänenname angezeigt. |
