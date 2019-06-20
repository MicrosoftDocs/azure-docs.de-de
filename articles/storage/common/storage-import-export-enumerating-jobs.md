---
title: Auflisten aller Azure Import/Export-Aufträge | Microsoft Docs
description: Erfahren Sie, wie Sie alle Aufträge des Azure Import/Export-Diensts in einem Abonnement auflisten.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478690"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Auflisten von Aufträgen im Azure Import/Export-Dienst
Rufen Sie den Vorgang [List Jobs](/rest/api/storageimportexport/jobs) auf, um alle Aufträge in einem Abonnement aufzulisten. `List Jobs` gibt eine Liste der Aufträge sowie die folgenden Attribute zurück:

-   Typ des Auftrags (Import oder Export)

-   Aktueller Auftragsstatus

-   Zugeordnetes Speicherkonto des Auftrags

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden der REST-API des Import/Export-Diensts](storage-import-export-using-the-rest-api.md)
