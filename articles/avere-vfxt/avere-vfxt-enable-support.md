---
title: Aktivieren der Unterstützung für Avere vFXT – Azure
description: Aktivieren von Supportuploads über Avere vFXT für Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: fe096b2e2a75cc89e3ce5ef905d8e4c347cc153a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409839"
---
# <a name="enable-support-uploads"></a>Aktivieren von Supportuploads

Avere vFXT für Azure kann automatisch Supportdaten über Ihren Cluster hochladen. Diese Uploads ermöglichen es den Supportmitarbeitern, den bestmöglichen Kundendienst zu bieten.

## <a name="steps-to-enable-uploads"></a>Schritte zum Aktivieren von Uploads

Führen Sie die folgenden Schritte über die Avere-Systemsteuerung aus, um den Support zu aktivieren. (Informationen zum Öffnen der Avere-Systemsteuerung finden Sie unter [Zugreifen auf den vFXT-Cluster](avere-vfxt-cluster-gui.md).)

1. Navigieren Sie am oberen Rand zur Registerkarte **Einstellungen**.
1. Klicken Sie links auf den Link **Support**, und akzeptieren Sie die Datenschutzrichtlinie.

   ![Screenshot der Avere-Systemsteuerung und des Popupfensters mit der Bestätigungsschaltfläche zum Akzeptieren der Datenschutzrichtlinie](media/avere-vfxt-privacy-policy.png)

1. Klicken Sie auf das Dreieck links neben **Kundeninformationen**, um den Abschnitt zu erweitern.
1. Klicken Sie auf die Schaltfläche **Revalidate upload information** (Uploadinformationen erneut überprüfen).
1. Legen Sie den Supportnamen des Clusters unter **Eindeutiger Clustername** fest – stellen Sie sicher, dass er Ihren Cluster eindeutig identifiziert, um die Supportmitarbeiter zu unterstützen.
1. Aktivieren Sie die Kontrollkästchen für **Statistiküberwachung**, **Upload allgemeiner Informationen** und **Upload von Absturzinformationen**.
1. Klicken Sie auf **Submit**.

   ![Screenshot der Supporteinstellungsseite mit ausgefüllten Kundeninformationen](media/avere-vfxt-support-info.png)

1. Klicken Sie auf das Dreieck links neben **Secure Proactive Support (SPS)** , um den Abschnitt zu erweitern.
1. Aktivieren Sie das Kontrollkästchen für **SPS-Link aktivieren**.
1. Klicken Sie auf **Submit**.

   ![Screenshot der Supporteinstellungsseite mit ausgefülltem SPS-Abschnitt](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie dem Cluster ein lokales Speichersystem oder ein vorhandenes Cloudspeichersystem hinzufügen müssen, folgen Sie den Anweisungen unter [Konfigurieren von Speicher](avere-vfxt-add-storage.md). 

Wenn Sie bereit sind, Clients an den Cluster anzufügen, lesen Sie [Einbinden des Avere vFXT-Clusters](avere-vfxt-mount-clients.md).