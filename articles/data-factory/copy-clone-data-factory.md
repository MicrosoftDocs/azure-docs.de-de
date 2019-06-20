---
title: Kopieren oder Klonen einer Data Factory in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Data Factory in Azure Data Factory kopieren oder klonen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 96ea8142e2f7794d3c15c6efb436eafa585bc8fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60780929"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopieren oder Klonen einer Data Factory in Azure Data Factory

In diesem Artikel wird beschrieben, wie Sie eine Data Factory in Azure Data Factory kopieren oder klonen.

## <a name="use-cases-for-cloning-a-data-factory"></a>Anwendungsfälle für das Klonen einer Data Factory

Im folgenden finden Sie einige Situationen, in denen es hilfreich sein kann, eine Data Factory zu kopieren oder zu klonen:

-   **Umbenennen von Ressourcen.** Azure unterstützt die Umbenennung von Ressourcen nicht. Wenn Sie eine Data Factory umbenennen möchten, können Sie sie unter einem anderen Namen klonen und dann die vorhandene löschen.

-   **Debuggen von Änderungen**, wenn die Debugfeatures nicht ausreichen. Um die Änderungen zu testen, ist es manchmal sinnvoll, die Änderungen in einer anderen Factory zu testen, bevor Sie sie auf Ihre Hauptfactory anwenden. In den meisten Szenarien können Sie das Debuggen nutzen. Änderungen in Triggern, z.B. das Verhalten von Änderungen beim automatischen Aufrufen eines Triggers oder über ein Zeitintervall, können jedoch möglicherweise nicht einfach ohne Einchecken getestet werden. In diesen Fällen ist es sehr sinnvoll, die Factory zu klonen und die Änderungen dort anzuwenden. Da Azure Data Factory hauptsächlich über die Anzahl von Ausführungen abgerechnet wird, führt die zweite Factory nicht zu zusätzlichen Gebühren.

## <a name="how-to-clone-a-data-factory"></a>Klonen einer Data Factory

1. Über die Data Factory-Benutzeroberfläche im Azure-Portal können Sie die gesamte Nutzlast Ihrer Data Factory zusammen mit einer Parameterdatei in eine Resource Manager-Vorlage exportieren, in der Sie Werte ändern können, bevor Sie Ihre Factory klonen.

1. Als Voraussetzung hierfür müssen Sie die Ziel-Data Factory über das Azure-Portal erstellen.

1. Wenn Sie eine SelfHosted IntegrationRuntime in Ihrer Quellfactory haben, müssen Sie sie mit dem gleichen Namen in der Zielfactory vorab erstellen. Wenn Sie die SelfHosted IRs zwischen verschiedenen Factorys freigeben möchten, können Sie das [hier](author-visually.md#best-practices-for-git-integration) veröffentlichte Muster verwenden.

1. Wenn Sie sich im Git-Modus befinden, wird bei jedem Veröffentlichen über das Portal die Resource Manager-Vorlage der Factory in Git im Branch „adf_publish“ des Repositorys gespeichert.

1. In anderen Szenarien kann die Resource Manager-Vorlage durch Klicken auf die Schaltfläche **Export Resource Manager template** (Resource Manager-Vorlage exportieren) im Portal heruntergeladen werden.

1. Nach dem Herunterladen der Resource Manager-Vorlage können Sie sie mit Standardbereitstellungsmethoden für Resource Manager-Vorlagen bereitstellen.

1. Aus Sicherheitsgründen enthält die generierte Resource Manager-Vorlage keine geheimen Informationen wie Kennwörter für verknüpfte Dienste. Daher müssen Sie diese Kennwörter als Bereitstellungsparameter angeben. Wenn die Bereitstellung von Parametern nicht erwünscht ist, müssen Sie die Verbindungszeichenfolgen und die Kennwörter der verknüpfte Dienste aus Azure Key Vault abrufen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Anleitung zum Erstellen einer Data Factory im Azure-Portal unter [Erstellen einer Data Factory über die Azure Data Factory-Benutzeroberfläche](quickstart-create-data-factory-portal.md).
