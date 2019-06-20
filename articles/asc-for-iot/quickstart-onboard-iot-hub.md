---
title: Aktivieren des Diensts „Azure Security Center für IoT“ in IoT Hub (Vorschauversion) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den Dienst „Azure Security Center für IoT“ in Ihrer IoT Hub-Instanz aktivieren.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: 7030ae1c3a28cdd74671dc95dce59cf86cacf4c9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786160"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Schnellstart: Aktivieren des Diensts in IoT Hub

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der Public Preview-Phase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel erfahren Sie, wie Sie die Vorschauversion des Diensts „ASC für IoT“ (Azure Security Center) in Ihrer IoT Hub-Instanz aktivieren.  

> [!NOTE]
> Azure Security Center für IoT unterstützt derzeit nur IoT Hub-Instanzen im Standard-Tarif.
> Azure Security Center für IoT ist eine Lösung für einen einzelnen Hub. Sollten Sie mehrere Hubs benötigen, sind mehrere Lösungen erforderlich. 

## <a name="prerequisites-for-enabling-the-service"></a>Voraussetzungen für die Dienstaktivierung

- Log Analytics-Arbeitsbereich
  - Von ASC für IoT werden in Ihrem Log Analytics-Arbeitsbereich standardmäßig zwei Arten von Informationen gespeichert: **Sicherheitswarnungen** und **Empfehlungen**. 
  - Sie können noch einen weiteren Informationstyp hinzufügen: **Rohereignisse**. Hinweis: Für die Speicherung von **Rohereignissen** in Log Analytics fallen zusätzliche Speicherkosten an. 
- IoT Hub (Standard-Tarif)
- Erfüllen aller [Dienstvoraussetzungen](service-prerequisites.md) 
- Unterstützte Servicebereiche
  - USA (Mitte)
  - Nordeuropa
  - Asien, Südosten

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Aktivieren von ASC für IoT in Ihrer IoT Hub-Instanz 

Führen Sie die folgenden Schritte aus, um die Sicherheit in Ihrer IoT Hub-Instanz zu aktivieren: 

1. Öffnen Sie im Azure-Portal Ihre Instanz von **IoT Hub**. 
2. Klicken Sie im Menü **Sicherheit** auf **Übersicht** und dann auf **Vorschauversion starten**. 
3. Wählen Sie **Enable IoT Security** (IoT-Sicherheit aktivieren) aus. 
4. Geben Sie die Details Ihres Azure Log Analytics-Arbeitsbereichs an. 
   - Legen Sie die Einstellung für **Rohereignisse** auf **Ein** fest, um zusätzlich zu den gespeicherten Standardinformationen auch **Rohereignisse** zu speichern. 
   - Legen Sie die Einstellung für **twin collection** (Zwillingserfassung) auf **Ein** fest, um die Zwillingserfassung zu **aktivieren**. 
5. Klicken Sie auf **Speichern**. 

Glückwunsch! Sie haben ASC für IoT in Ihrer IoT Hub-Instanz aktiviert. 

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie Ihre Lösung konfigurieren:

> [!div class="nextstepaction"]
> [Quickstart: Configure your IoT solution](quickstart-configure-your-solution.md) (Schnellstart: Konfigurieren Ihrer IoT-Lösung)
