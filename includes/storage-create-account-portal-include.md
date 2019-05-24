---
title: Includedatei
description: Includedatei
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 56545b02c35d68455497c93582073c73d6c75da5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114658"
---
Führen Sie diese Schritte aus, wenn Sie ein allgemeines Speicherkonto vom Typ „General Purpose v2“ über das Azure-Portal erstellen möchten:

1. Wählen Sie im Azure-Portal **Alle Dienste** aus. Geben Sie in der Liste mit den Ressourcen **Speicherkonten** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Speicherkonten**.
1. Klicken Sie im angezeigten Fenster **Speicherkonten** auf **Hinzufügen**.
1. Wählen Sie das Abonnement aus, in dem das Speicherkonto erstellt werden soll.
1. Wählen Sie unter dem Feld **Ressourcengruppe** die Option **Neu erstellen**. Geben Sie einen Namen für die neue Ressourcengruppe an, wie in der folgenden Abbildung dargestellt.

    ![Screenshot: Erstellen einer Ressourcengruppe im Portal](./media/storage-create-account-portal-include/create-resource-group.png)

1. Geben Sie als Nächstes einen Namen für Ihr Speicherkonto ein. Der gewählte Name muss innerhalb von Azure eindeutig sein. Der Name muss ebenfalls zwischen 3 und 24 Zeichen lang sein und darf nur Zahlen und Kleinbuchstaben enthalten.
1. Wählen Sie einen Standort für Ihr Speicherkonto aus, oder verwenden Sie den Standardstandort.
1. Behalten Sie die Standardwerte für diese Felder bei:

   |Feld  |Wert  |
   |---------|---------|
   |Bereitstellungsmodell     |Ressourcen-Manager         |
   |Leistung     |Standard         |
   |Kontoart     |StorageV2 (universell v2)         |
   |Replikation     |Georedundanter Speicher mit Lesezugriff (RA-GRS)         |
   |Zugriffsebene     |Heiß         |

1. Wählen Sie **Überprüfen + erstellen**, um die Speicherkontoeinstellungen zu überprüfen und das Konto zu erstellen.
1. Klicken Sie auf **Erstellen**.

Weitere Informationen zu den Arten von Speicherkonten und anderen Speicherkontoeinstellungen finden Sie unter [Übersicht über Azure Storage-Konten](https://docs.microsoft.com/azure/storage/common/storage-account-overview). Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
