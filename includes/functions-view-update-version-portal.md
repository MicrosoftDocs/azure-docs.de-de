---
title: include file
description: include file
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178740"
---
Gehen Sie folgendermaßen vor, um die zurzeit von einer Funktions-App verwendete Runtimeversion anzuzeigen und zu aktualisieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Funktions-App.

1. Wählen Sie unter **Konfigurierte Features** die Option **Funktionen-App-Einstellungen** aus.

    ![„Funktionen-App-Einstellungen“ auswählen](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. Suchen Sie auf der Registerkarte **Funktionen-App-Einstellungen** nach der **Runtimeversion**. Sie sehen dort die spezifische Runtimeversion und die angeforderte Hauptversion. Im nachstehenden Beispiel ist die Version auf `~2` festgelegt.

   ![„Funktionen-App-Einstellungen“ auswählen](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Um Ihre Funktions-App an die Runtime der Version 1.x anzuheften, wählen Sie **~1** unter **Laufzeitversion** aus. Dieser Schalter ist deaktiviert, wenn Ihre App Funktionen enthält.

1. Wenn Sie die Runtimeversion geändert haben, wechseln Sie zurück zur Registerkarte **Übersicht**, und wählen Sie **Neu starten** aus, um die App neu zu starten.  Die Funktions-App wird neu gestartet und mit der Runtime der Version 1.x ausgeführt. Außerdem werden die Vorlagen für Version 1.x verwendet, wenn Sie Funktionen erstellen.