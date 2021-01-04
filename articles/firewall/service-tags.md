---
title: Übersicht über Azure Firewall-Diensttags
description: Ein Diensttag steht für eine Gruppe von IP-Adressen und hat die Aufgabe, bei der Erstellung von Sicherheitsregeln die Komplexität zu verringern.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 47377817b62d33e8af79e4a0d2dceb68ba9dbdc5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658646"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall-Diensttags

Ein Diensttag steht für eine Gruppe von IP-Adressen und hat die Aufgabe, bei der Erstellung von Sicherheitsregeln die Komplexität zu verringern. Sie können kein eigenes Diensttag erstellen und auch nicht angeben, welche IP-Adressen in einem Tag enthalten sind. Microsoft verwaltet die Adresspräfixe, die mit dem Diensttag abgedeckt werden, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Azure Firewall-Diensttags können im Zielfeld für Netzwerkregeln verwendet werden. Sie können sie anstelle bestimmter IP-Adressen verwenden.

## <a name="supported-service-tags"></a>Unterstützte Diensttags

Eine Liste mit Diensttags, die für Netzwerkregeln der Azure-Firewall verfügbar sind, finden Sie unter [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Firewall-Regeln finden Sie unter [Logik für die Azure Firewall-Regelverarbeitung](rule-processing.md).