---
title: 'Azure Active Directory Domain Services: Aktualisieren der DNS-Einstellungen für das virtuelle Azure-Netzwerk | Microsoft-Dokumentation'
description: Erste Schritte mit Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: 4727c24c603e95aeee6214546e25a273aa652f4c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60417322"
---
# <a name="enable-azure-active-directory-domain-services"></a>Aktivieren von Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Aufgabe 4: Aktualisieren der DNS-Einstellungen für das virtuelle Azure-Netzwerk
In den vorangegangenen Konfigurationsaufgaben haben Sie Azure Active Directory Domain Services für Ihr Verzeichnis erfolgreich aktiviert. Ermöglichen Sie nun Computern im virtuellen Netzwerk, eine Verbindung mit diesen Diensten herzustellen und sie zu nutzen. In diesem Artikel aktualisieren Sie die DNS-Servereinstellungen für Ihr virtuelles Netzwerk so, dass sie auf die beiden IP-Adressen verweisen, unter denen Azure Active Directory Domain Services im virtuellen Netzwerk verfügbar ist.

Führen Sie die folgenden Schritte aus, um die DNS-Servereinstellungen für das virtuelle Netzwerk zu aktualisieren, in dem Sie Azure Active Directory Domain Services aktiviert haben:


1. Die Registerkarte **Übersicht** enthält Liste mit **erforderlichen Konfigurationsschritten**, die ausgeführt werden müssen, nachdem die verwaltete Domäne vollständig bereitgestellt wurde. Der erste Konfigurationsschritt ist das **Aktualisieren von DNS-Servereinstellungen für Ihr virtuelles Netzwerk**.

    ![Domänendienste – Registerkarte „Übersicht“](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > Wird dieser Konfigurationsschritt nicht angezeigt? Wenn die DNS-Servereinstellungen für Ihr virtuelles Netzwerk auf dem neuesten Stand sind, wird die Kachel „DNS-Servereinstellungen für Ihr virtuelles Netzwerk aktualisieren“ auf der Registerkarte „Übersicht“ nicht angezeigt.
    >
    >

2. Klicken Sie auf die Schaltfläche **Konfigurieren**, um die DNS-Servereinstellungen für das virtuelle Netzwerk zu aktualisieren.

> [!NOTE]
> Virtuelle Computer im Netzwerk übernehmen die neuen DNS-Einstellungen erst nach einem Neustart. Wenn die DNS-Einstellungen sofort aktualisiert werden sollen, lösen Sie über das Verwaltungsportal, PowerShell oder CLI einen Neustart aus.
>
>

## <a name="next-step"></a>Nächster Schritt
[Aufgabe 5: Aktivieren der Kennworthashsynchronisierung für Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
