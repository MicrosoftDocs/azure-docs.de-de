---
title: Kopieren oder Klonen einer Data Factory in Azure Data Factory
description: Erfahren Sie, wie Sie eine Data Factory in Azure Data Factory kopieren oder klonen.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 1ea16785502ce8a82087b79dd8e7f0014c0059fc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375237"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopieren oder Klonen einer Data Factory in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird beschrieben, wie Sie eine Data Factory in Azure Data Factory kopieren oder klonen.

## <a name="use-cases-for-cloning-a-data-factory"></a>Anwendungsfälle für das Klonen einer Data Factory

Im folgenden finden Sie einige Situationen, in denen es hilfreich sein kann, eine Data Factory zu kopieren oder zu klonen:

- **Verschieben einer Data Factory** in eine neue Region. Wenn Sie Ihre Data Factory in eine andere Region verschieben möchten, empfiehlt es sich, eine Kopie in der Zielregion zu erstellen und die vorhandene Factory zu löschen.

- **Umbenennen der Data Factory**. Azure unterstützt die Umbenennung von Ressourcen nicht. Wenn Sie eine Data Factory umbenennen möchten, können Sie sie unter einem anderen Namen klonen und die vorhandene löschen.

- **Debuggen von Änderungen**, wenn die Debugfeatures nicht ausreichen. In den meisten Szenarien können Sie das [Debuggen](iterative-development-debugging.md) nutzen. In anderen Szenarien ist das Testen von Änderungen in einer geklonten Sandboxumgebung sinnvoller. Das Verhalten der parametrisierten ETL-Pipelines beim Auslösen eines Triggers bei Dateieingang im Vergleich zu einem rollierenden Zeitfenster kann möglicherweise nicht einfach allein durch Debuggen getestet werden. In diesen Fällen empfiehlt es sich, eine Sandboxumgebung zum Testen zu klonen. Da Azure Data Factory hauptsächlich über die Anzahl von Ausführungen abgerechnet wird, führt eine zweite Factory nicht zu zusätzlichen Gebühren.

## <a name="how-to-clone-a-data-factory"></a>Klonen einer Data Factory

1. Als Voraussetzung müssen Sie zunächst die Ziel-Data Factory im Azure-Portal erstellen.

1. Im Git-Modus:
    1. Bei jedem Veröffentlichen über das Portal wird die Resource Manager-Vorlage der Factory in Git im Branch „adf\_publish“ des Repositorys gespeichert.
    1. Verbinden Sie die neue Factory mit _demselben_ Repository, und erstellen Sie sie über den Branch „adf\_publish“. Ressourcen wie z. B. Pipelines, Datasets und Trigger werden übertragen.

1. Im Livemodus:
    1. In der Data Factory-Benutzeroberfläche können Sie die gesamte Payload der Data Factory in eine Resource Manager-Vorlagendatei und eine Parameterdatei exportieren. Auf diese Dateien kann im Portal in der ARM-Vorlage über die Schaltfläche **Export Resource Manager template** (Resource Manager-Vorlage exportieren) zugegriffen werden.
    1. Sie können entsprechende Änderungen an der Parameterdatei vornehmen und neue Werte für die neue Factory eingeben.
    1. Dann können Sie sie mit Standardbereitstellungsmethoden für Resource Manager-Vorlagen bereitstellen.

1. Wenn Sie eine SelfHosted IntegrationRuntime in Ihrer Quellfactory haben, müssen Sie sie mit dem gleichen Namen in der Zielfactory vorab erstellen. Wenn Sie die selbstgehostete Integration Runtime zwischen verschiedenen Factorys freigeben möchten, können Sie das [hier](create-shared-self-hosted-integration-runtime-powershell.md) veröffentlichte entsprechende Muster verwenden.

1. Aus Sicherheitsgründen enthält die generierte Resource Manager-Vorlage keine Geheimnisinformationen wie Kennwörter für verknüpfte Dienste. Daher müssen Sie die Anmeldeinformationen als Bereitstellungsparameter angeben. Wenn die manuelle Eingabe von Anmeldeinformationen in Ihren Einstellungen nicht vorgesehen ist, können Sie Verbindungszeichenfolgen und Kennwörter stattdessen aus Azure Key Vault abrufen. [Weitere Informationen](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Anleitung zum Erstellen einer Data Factory im Azure-Portal unter [Erstellen einer Data Factory über die Azure Data Factory-Benutzeroberfläche](quickstart-create-data-factory-portal.md).
