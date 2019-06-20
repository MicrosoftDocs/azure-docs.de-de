---
title: Einrichten der Azure Stream Analytics-Tools für Visual Studio
description: In diesem Artikel werden die Installationsanforderungen und die Einrichtung der Azure Stream Analytics-Tools für Visual Studio beschrieben.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 673f4935dce28b30c10e6abf4c7d22e00c1dd73a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60762201"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Installieren der Azure Stream Analytics-Tools für Visual Studio
Die Azure Stream Analytics-Tools unterstützen Visual Studio 2017, 2015 und 2013. In diesem Artikel wird beschrieben, wie Sie die Tools installieren und deinstallieren.

Weitere Informationen zur Nutzung dieser Tools finden Sie unter [Verwenden der Azure Stream Analytics-Tools für Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Installieren
### <a name="recommended-visual-studio-2019-and-2017"></a>Empfohlen: Visual Studio 2019 und 2017
* Laden Sie [Visual Studio 2019 (Vorschauversion 2 oder höher) und Visual Studio 2017 (15.3 oder höher)](https://www.visualstudio.com/) herunter. Die Editionen Enterprise (Ultimate/Premium), Professional und Community werden unterstützt. Die Express Edition wird nicht unterstützt. Visual Studio 2017 auf einem Mac wird nicht unterstützt. 
* Die Stream Analytics-Tools sind Teil der Workloads **Azure-Entwicklung** und **Datenspeicherung und -verarbeitung** in Visual Studio 2017. Aktivieren Sie eine dieser beiden Workloads im Rahmen Ihrer Visual Studio-Installation.

Aktivieren Sie die Workload **Datenspeicherung und -verarbeitung** wie gezeigt:

![Ausgewählte Workload „Datenspeicherung und -verarbeitung“](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Aktivieren Sie die Workload **Azure-Entwicklung** wie gezeigt:

![Ausgewählte Workload „Azure-Entwicklung“](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)

* Wählen Sie im Menü „Extras“ die Option **Erweiterungen und Updates** aus. Suchen Sie die Azure Data Lake- und Stream Analytics-Tools in den installierten Erweiterungen, und klicken Sie auf **Aktualisieren**, um die neueste Erweiterung zu installieren. 

![Visual Studio-Erweiterungen und -Updates](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-extensions-updates.png)

### <a name="visual-studio-2015-2013"></a>Visual Studio 2015, 2013
* Installieren Sie Visual Studio 2015 oder Visual Studio 2013 Update 4. Die Editionen Enterprise (Ultimate/Premium), Professional und Community werden unterstützt. Die Express Edition wird nicht unterstützt. 
* Installieren Sie das Microsoft Azure SDK für .NET Version 2.7.1 oder höher mit dem [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx).
* Installieren Sie die [Azure Stream Analytics-Tools für Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Aktualisieren

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 und 2017
Die neue Versionserinnerung wird in der Visual Studio-Benachrichtigung angezeigt.

![Visual Studio – Neue Versionserinnerung](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-new-version-reminder-vs-tools.png)

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 und 2013
Die installierten Stream Analytics-Tools für Visual Studio prüfen automatisch auf neue Versionen. Befolgen Sie die Anweisungen im Popupfenster, um die neueste Version zu installieren. 


## <a name="uninstall"></a>Deinstallieren

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 und 2017
Doppelklicken Sie auf den Visual Studio-Installer, und wählen Sie **Ändern** aus. Deaktivieren Sie das Kontrollkästchen **Azure Data Lake- und Stream Analytics-Tools** in der Workload **Datenspeicherung und -verarbeitung** oder **Azure-Entwicklung**.

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 und 2013
Wechseln Sie zur Systemsteuerung, und deinstallieren Sie **Microsoft Azure Data Lake- und Stream Analytics-Tools für Visual Studio**.





