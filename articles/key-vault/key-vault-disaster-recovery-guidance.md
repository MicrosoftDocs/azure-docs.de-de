---
title: Vorgehensweise bei einer Azure-Dienstunterbrechung mit Auswirkungen auf Azure Key Vault – Azure Key Vault| Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zur Vorgehensweise bei einer Azure-Dienstunterbrechung mit Auswirkungen auf Azure Key Vault.
services: key-vault
author: barclayn
manager: barbkess
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: barclayn
ms.openlocfilehash: dba1fe91a635f467f4a3aeeaa048897065822869
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236637"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault: Verfügbarkeit und Redundanz

Azure Key Vault-Features bieten mehrere Redundanzebenen, um sicherzustellen, dass Ihre Schlüssel bzw. geheimen Schlüssel Ihrer Anwendung auch dann zur Verfügung stehen, wenn einzelne Komponenten des Dienstes ausfallen sollten.

Der Inhalt Ihres Schlüsseltresors wird innerhalb der Region sowie in eine sekundäre Region repliziert, die mindestens 240 km entfernt ist (jedoch innerhalb des gleichen Gebiets liegt). Dies gewährleistet eine sehr hohe Dauerhaftigkeit Ihrer Schlüssel und geheimen Schlüssel. Einzelheiten zu spezifischen Regionspaaren finden Sie im Dokument [Azure-Regionspaare](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Wenn einzelne Komponenten innerhalb des Key Vault-Diensts ausfallen, springen andere Komponenten in der Region ein, um Ihre Anforderung zu erfüllen, sodass die Funktionalität nicht beeinträchtigt wird. Sie müssen keine Aktion ausführen, um dies auszulösen. Es geschieht automatisch und von Ihnen unbemerkt.

In dem seltenen Fall, dass eine gesamte Azure-Region nicht verfügbar sein sollte, werden die Anforderungen an den Azure Key Vault in dieser Region automatisch an eine sekundäre Region weitergeleitet (was als *Failover* bezeichnet wird). Wenn die primäre Region wieder verfügbar ist, werden Anforderungen wieder zurück an die primäre Region geleitet (was als *Failback* bezeichnet wird). Sie müssen wiederum keine Maßnahmen ergreifen, da diese Schritte automatisch erfolgen.

Durch diesen Entwurf für Hochverfügbarkeit erfordert Azure Key Vault keine Ausfallzeiten für Wartungsarbeiten.

Beachten Sie folgende Einschränkungen:

* Beim Failover einer Region kann es einige Minuten dauern, bis das Failover des Diensts erfolgt. Anforderungen während dieser Zeit können fehlschlagen, bis das Failover abgeschlossen ist.
* Nachdem ein Failover abgeschlossen wurde, befindet sich der Schlüsseltresor im schreibgeschützten Modus. Folgende Anforderungen werden in diesem Modus unterstützt:
  * Auflisten von Schlüsseltresoren
  * Abrufen von Eigenschaften von Schlüsseltresoren
  * Auflisten geheimer Schlüssel
  * Abrufen geheimer Schlüssel
  * Auflisten von Schlüsseln
  * Abrufen (von Eigenschaften) von Schlüsseltresoren
  * Verschlüsseln
  * Entschlüsseln
  * Umschließen
  * Aufheben der Umschließung
  * Überprüfen
  * Signieren
  * Backup
* Sobald auf ein Failover ein Failback erfolgt ist, stehen alle Anforderungstypen (einschließlich Lese- *und* Schreibanforderungen) wieder zur Verfügung.

