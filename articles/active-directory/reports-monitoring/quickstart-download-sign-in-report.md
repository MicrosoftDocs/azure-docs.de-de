---
title: 'Schnellstart: Herunterladen eines Anmeldeberichts über das Azure-Portal | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie einen Anmeldebericht über das Azure-Portal herunterladen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86bc72f69903134afa3750ad6b72486a713b6cc0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011913"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Schnellstart: Herunterladen eines Anmeldeberichts über das Azure-Portal

In diesem Schnellstart erfahren Sie, wie Sie die Anmeldedaten Ihres Mandanten für die letzten 24 Stunden herunterladen. Sie können bis zu 250.000 Datensätze aus dem Azure-Portal herunterladen. Die Datensätze werden nach Aktualität sortiert, sodass Sie standardmäßig die neuesten 250.000 Datensätze erhalten. 

## <a name="prerequisites"></a>Voraussetzungen

Erforderlich:

* Ein Azure Active Directory-Mandant mit einer Premium-Lizenz zum Anzeigen des Berichts zu Anmeldeaktivitäten. Unter [Erste Schritte mit Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) erfahren Sie, wie Sie ein Upgrade für Ihre Azure Active Directory-Edition durchführen. Beachten Sie Folgendes: Wenn Sie vor dem Upgrade über keine Aktivitätsdaten verfügten, dauert es ein paar Tage, bis die Daten in den Berichten angezeigt werden, nachdem Sie ein Upgrade auf eine Premium-Lizenz durchgeführt haben.
* Ein Benutzer, der über die Rolle **Sicherheitsadministrator**, **Sicherheitsleseberechtigter**, **Benutzer mit Leseberechtigung für Berichte** oder **globaler Administrator** für den Mandanten verfügt. Darüber hinaus kann jeder Benutzer im Mandanten auf die eigenen Anmeldungen zugreifen.

## <a name="quickstart-download-a-sign-in-report"></a>Schnellstart: Herunterladen eines Anmeldeberichts

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Wählen Sie im linken Navigationsbereich die Option **Azure Active Directory** aus, und klicken Sie auf die Schaltfläche **Verzeichnis wechseln**, um Ihr Active Directory-Verzeichnis auszuwählen.
3. Wählen Sie im Dashboard **Azure Active Directory** und dann **Anmeldungen** aus. 
4. Wählen Sie im Dropdownmenü **Datum** den Eintrag **Letzte 24 Stunden** und dann **Anwenden** aus, um die Anmeldungen der letzten 24 Stunden anzuzeigen. 
5. Wählen Sie die Schaltfläche **Herunterladen** und als Dateiformat **CSV** aus. Geben Sie dann einen Dateinamen an, um eine CSV-Datei mit den gefilterten Datensätzen herunterzuladen. 

![Berichterstellung](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Nächste Schritte

* [Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal](concept-sign-ins.md)
* [Vermerkdauer für Azure Active Directory-Berichte](reference-reports-data-retention.md)
* [Latenzen bei Azure Active Directory-Berichten – Vorschau](reference-reports-latencies.md)
