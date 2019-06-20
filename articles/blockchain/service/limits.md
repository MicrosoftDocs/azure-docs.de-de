---
title: Einschränkungen von Azure Blockchain
description: Übersicht über die Einschränkungen des Diensts und der Funktionalität des Azure Blockchain-Diensts
services: azure-blockchain
keywords: Blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 169ec7a8ef407af3f754046aa8e3b06793a7e962
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027652"
---
# <a name="limits-in-azure-blockchain-service"></a>Einschränkungen des Azure Blockchain-Diensts

Der Azure Blockchain-Dienst weist dienstliche und funktionale Einschränkungen wie die Anzahl von Knoten, über die ein Mitglied verfügen kann, Konsortiumseinschränkungen und Speichermengen auf.

## <a name="pricing-tier"></a>Tarif

Die maximalen Grenzwerte für Transaktionen und Validierungsknoten hängen davon ab, ob Sie über den Tarif „Basic“ oder „Standard“ für den Azure Blockchain-Dienst verfügen.

| Tarif | Max. Transaktionsknoten | Max. Validierungsknoten |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Das Wechseln zwischen den Tarifen „Basic“ und „Standard“ nach der Erstellung eines Mitglieds wird nicht unterstützt.

## <a name="storage-capacity"></a>Speicherkapazität

Die maximale Speichermenge, die pro Knoten für Ledgerdaten und Protokolle verwendet werden kann, beträgt 1 Terabyte.

Die Speichergröße für Ledger und Protokolle kann nicht verringert werden.

## <a name="consortium-limits"></a>Konsortiumseinschränkungen

* **Namen von Konsortien und Mitgliedern müssen eindeutig sein**, d. h. sie müssen sich von den Namen anderer Konsortien und Mitglieder im Azure Blockchain-Dienst unterscheiden.

* **Die Namen von Mitgliedern und Konsortien können nicht geändert werden.**

* **Alle Mitglieder eines Konsortiums müssen im gleichen Tarif sein.**

* **Alle Mitglieder eines Konsortiums müssen sich in derselben Region befinden.**

    Das erste in einem Konsortium erstellte Mitglied bestimmt die Region. In das Konsortium eingeladene Mitglieder müssen sich in derselben Region wie das erste Mitglied befinden. Durch die Einschränkung der Mitglieder auf eine Region wird sichergestellt, dass der Netzwerkkonsens nicht negativ beeinträchtigt wird.

* **Ein Konsortium muss über mindestens einen Administrator verfügen.**

    Wenn nur ein Administrator in einem Konsortium enthalten ist, kann dieser sich nicht selbst aus dem Konsortium entfernen oder sein Mitglied löschen, es sei denn, ein weiterer Administrator wird im Konsortium hinzugefügt bzw. höher gestuft.

* **Mitglieder, die aus dem Konsortium entfernt wurden, können nicht wieder hinzugefügt werden.**

    Stattdessen müssen diese erneut eingeladen werden, damit sie ein neues Mitglied erstellen können, um dem Konsortium beizutreten. Vorhandene Mitgliederressourcen werden nicht gelöscht, um Transaktionsverläufe beizubehalten.

* **Alle Mitglieder eines Konsortiums müssen dieselbe Ledgerversion verwenden.**

    Weitere Informationen zu Patches, Updates und Ledgerversionen, die für den Azure Blockchain-Dienst verfügbar sind, finden Sie unter [Patches, Updates und Versionen](ledger-versions.md).

## <a name="next-steps"></a>Nächste Schritte

* [Patches, Updates und Versionen](ledger-versions.md)
