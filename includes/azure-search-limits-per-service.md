---
title: include file
description: include file
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b298c87d802314bd865bd6f38c35e4361eb69f3f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178471"
---
Der Speicher ist durch den Festplattenspeicher oder einen festen Grenzwert für die *maximale Anzahl* von Indizes, Dokumenten oder anderen allgemeinen Ressourcen beschränkt (je nachdem, was zuerst erreicht wird). In der folgenden Tabelle sind die Speicherbegrenzungen dokumentiert. Obergrenzen für Indizes, Dokumente und andere Objekte finden Sie unter [Grenzwerte nach Ressource](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resource | Kostenlos | Basic<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Vereinbarung zum Servicelevel (SLA)<sup>3</sup>  |Nein |Ja |Ja |Ja |Ja |Ja |Ja |Ja |
| Speicherkapazität pro Partition |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partitionen pro Dienst |– |1 |12 |12 |12 |3 |12 |12 |
| Partitionsgröße |– |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Replikate |– |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic verfügt über eine feste Partition. Bei diesem Tarif werden zusätzliche Sucheinheiten verwendet, um weitere Replikate für umfangreichere Abfrageworkloads zuzuordnen.

<sup>2</sup> Für S3 HD gilt ein festes Limit von drei Partitionen. (Das Limit ist also geringer als bei S3.) Die Untergrenze für Partitionen wurde festgelegt, da die Indexanzahl für S3 HD wesentlich höher ist. Da sowohl für Computeressourcen (Speicher und Verarbeitung) als auch für Inhalte (Indizes und Dokumente) Dienstlimits gelten, wird das Inhaltslimit zuerst erreicht.

<sup>3</sup> Vereinbarungen zum Servicelevel werden für abrechenbare Dienste auf dedizierten Ressourcen angeboten. Für kostenlose Dienste und Vorschaufunktionen gelten keine SLA. Für alle abrechenbaren Dienste gelten SLAs, wenn Sie genügend Redundanz für Ihren Dienst bereitstellen. Zwei oder mehr Replikate sind für die Abfrage-SLAs (Lesezugriff) erforderlich. Drei oder mehr Replikate sind für die Abfrage- und Indizierungs-SLAs (Lese-/Schreibzugriff) erforderlich. Die Anzahl der Partitionen wird für eine SLA nicht berücksichtigt. 