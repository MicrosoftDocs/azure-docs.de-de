---
title: Hochverfügbarkeit von Azure Analysis Services | Microsoft-Dokumentation
description: Sicherstellung der Hochverfügbarkeit für Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 51a0f560a0e4b6ff791d5ed3f9f221eb2eeb9b4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61036033"
---
# <a name="analysis-services-high-availability"></a>Hochverfügbarkeit für Analysis Services

In diesem Artikel wird die Sicherstellung der Hochverfügbarkeit für Azure Analysis Services-Server beschrieben. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Sicherstellen der Hochverfügbarkeit während einer Dienstunterbrechung

Es kommt zwar selten vor, aber es ist möglich, dass ein Azure-Rechenzentrum ausfällt. Ein solcher Ausfall kann den Geschäftsbetrieb einige wenige Minuten oder mehrere Stunden lahmlegen. Hochverfügbarkeit wird am häufigsten mittels Serverredundanz erreicht. Mit Azure Analysis Services können Sie Redundanz erreichen, indem Sie in einer oder mehreren Regionen zusätzliche sekundäre Server erstellen. Beim Erstellen redundanter Server zum Sicherstellen, dass die Daten und Metadaten auf diesen Servern mit einem Server in einer Region synchron sind, der offline geschaltet wurde, haben Sie folgende Möglichkeiten:

* Bereitstellen von Modellen auf redundanten Servern in anderen Regionen. Diese Methode erfordert die parallele Verarbeitung von Daten auf sowohl Ihrem primären Server als auch auf redundanten Servern, was gewährleistet, dass alle Server synchron sind.

* [Sichern](analysis-services-backup.md) von Datenbanken auf dem primären Server und Wiederherstellung auf redundanten Servern. Sie können beispielsweise über Nacht erfolgende Sicherungen in Azure-Speicher automatisieren und auf anderen redundanten Servern in anderen Regionen wiederherstellen. 

In beiden Fällen müssen Sie, wenn auf dem primären Server ein Ausfall auftritt, die Verbindungszeichenfolgen auf betroffenen Clients so ändern, dass eine Verbindung mit dem Server in einem Rechenzentrum in einer anderen Region hergestellt wird. Diese Änderung sollte als letztes Mittel angesehen werden und nur bei einem katastrophalen Ausfall eines regionalen Rechenzentrums erfolgen. Es ist wahrscheinlicher, dass ein ausgefallenes Rechenzentrum, das Ihren primären Server hostet, wieder online geschaltet wird, bevor Sie die Verbindungen auf allen Clients aktualisiert haben. 

Damit Sie die Verbindungszeichenfolgen auf Clients für die Berichterstellung nicht ändern müssen, können Sie einen [Alias](analysis-services-server-alias.md) für Ihren primären Server erstellen. Wenn der primäre Server ausfällt, können Sie den Alias so ändern, dass dieser auf einen redundanten Server in einer anderen Region zeigt. Sie können das Ändern des Alias für einen Servernamen automatisieren, indem Sie eine Endpunkt-Integritätsprüfung auf dem primären Server programmieren. Wenn bei der Integritätsprüfung ein Fehler auftritt, kann der Endpunkt an einen redundanten Server in einer anderen Region umleiten. 

## <a name="related-information"></a>Verwandte Informationen

[Sichern und Wiederherstellen](analysis-services-backup.md)   
[Verwalten von Azure Analysis Services](analysis-services-manage.md)   
[Aliasservernamen](analysis-services-server-alias.md) 

