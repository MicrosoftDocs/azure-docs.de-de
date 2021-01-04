---
title: Manuelles Migrieren von Windows Virtual Desktop (klassisch) – Azure
description: Erfahren Sie, wie Sie manuell von Windows Virtual Desktop (klassisch) zu Windows Virtual Desktop migrieren.
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd292fa6b4f613e0a5f5a80e0cd87675f529baf5
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516170"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Manuelles Migrieren von Windows Virtual Desktop (klassisch)

Windows Virtual Desktop (klassisch) erstellt seine Dienstumgebung mit PowerShell-Cmdlets, REST-APIs und Dienstobjekten. Ein „Objekt“ in einer Windows Virtual Desktop-Dienstumgebung ist ein von Windows Virtual Desktop erstelltes Element. Zu Dienstobjekten gehören Mandanten, Hostpools, Anwendungsgruppen und Sitzungshosts.

Windows Virtual Desktop (klassisch) ist jedoch nicht in Azure integriert. Ohne Azure-Integration werden Objekte, die Sie erstellen, nicht automatisch vom Azure-Portal verwaltet, da sie nicht mit Ihrem Azure-Abonnement verbunden sind.

Das vor Kurzem erfolgte große Update von Windows Virtual Desktop stellt eine Umstellung des Diensts auf eine vollständige Azure-Integration dar. In Windows Virtual Desktop erstellte Objekte werden automatisch vom Azure-Portal verwaltet.

In diesem Artikel wird erläutert, warum Sie eine Migration zur neuesten Version von Windows Virtual Desktop in Erwägung ziehen sollten. Anschließend erfahren Sie, wie Sie manuell von Windows Virtual Desktop (klassisch) zum neuesten Update von Windows Virtual Desktop migrieren.

## <a name="why-migrate"></a>Vorteile der Migration

Umfangreiche Updates können unpraktisch sein, besonders wenn Sie sie manuell durchführen müssen. Es gibt jedoch einige Gründe, warum eine automatische Migration nicht in Frage kommt:

- Vorhandene Dienstobjekte, die mit dem klassischen Release erstellt wurden, sind nicht in Azure vorhanden. Ihr Projektumfang erstreckt sich nicht über den Windows Virtual Desktop-Dienst hinaus.
- Mit dem neuesten Update wurde die Anwendungs-ID des Diensts dahin gehend geändert, dass für Apps keine Einwilligung mehr erfolgen muss, wie es bei Windows Virtual Desktop (klassisch) noch nötig war. Sie können mit Windows Virtual Desktop nur dann neue Azure-Objekte erstellen, wenn sie mit der neuen Anwendungs-ID authentifiziert werden.

Trotz des Aufwands ist eine Migration von der klassischen Version wichtig. Nach der Migration haben Sie folgende Möglichkeiten:

- Verwalten von Windows Virtual Desktop über das Azure-Portal
- Zuweisen von Azure Active Directory-Benutzergruppen (AD) zu Anwendungsgruppen
- Verwenden des verbesserten Log Analytics-Features für die Problembehandlung in Ihrer Bereitstellung
- Verwenden der nativen rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) zum Verwalten des administrativen Zugriffs

## <a name="when-should-i-migrate"></a>Gründe für eine Migration

Wenn Sie sich fragen, ob Sie eine Migration durchführen sollten, berücksichtigen Sie auch die aktuelle und zukünftige Situation Ihrer Bereitstellung.

Es gibt einige Szenarien, in denen eine manuelle Migration besonders empfohlen wird:

- Sie verfügen über einen Testhostpool mit einer geringen Anzahl von Benutzern.
- Sie haben einen Produktionsthostpool mit einer kleinen Anzahl von Benutzern eingerichtet, planen jedoch, diesen zukünftig auf Hunderte von Benutzern zu erweitern.
- Sie verfügen über ein einfaches Setup, das problemlos repliziert werden kann, z. B. wenn für Ihre VMs ein Katalogimage verwendet wird.

> [!IMPORTANT]
> Wenn Sie eine komplexe Konfiguration verwenden, die bis zur Stabilität eine lange Zeit benötigte oder viele Benutzer aufweist, wird die manuelle Migration nicht empfohlen.

## <a name="prepare-for-migration"></a>Vorbereiten der Migration

Bevor Sie beginnen, müssen Sie sicherstellen, dass Ihre Umgebung für die Migration bereit ist.

Sie benötigen Folgendes, um den Migrationsvorgang zu beginnen:

- Ein Azure-Abonnement, in dem Sie neue Azure-Dienstobjekte erstellen.
- Stellen Sie sicher, dass Sie den folgenden Rollen zugewiesen sind:
    
    - Mitwirkender
    - Benutzerzugriffsadministrator
    
    Mit der Rolle „Mitwirkender“ können Sie Azure-Objekte in Ihrem Abonnement erstellen, und mit der Rolle „Benutzerzugriffsadministrator“ können Sie Benutzer Anwendungsgruppen zuweisen.

## <a name="how-to-migrate-manually"></a>Manuelles Migrieren

Nachdem Sie sich auf den Migrationsvorgang vorbereitet haben, folgt nun die eigentliche Migration.

So migrieren Sie manuell von Windows Virtual Desktop (klassisch) zu Windows Virtual Desktop

1. Befolgen Sie die Anweisungen unter [Erstellen eines Hostpools mit dem Azure-Portal](create-host-pools-azure-marketplace.md), um alle Objekte auf hoher Ebene über das Azure-Portal zu erstellen.
2. Wenn Sie die bereits verwendeten virtuellen Computer übernehmen möchten, befolgen Sie die Anweisungen unter [Registrieren der virtuellen Computer für den Windows Virtual Desktop-Hostpool](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool), um sie manuell für den neuen Hostpool zu registrieren, den Sie in Schritt 1 erstellt haben.
3. Erstellen der neuen RemoteApp-App-Gruppen
4. Veröffentlichen Sie Benutzer oder Benutzergruppen in den neuen Desktop- und RemoteApp-App-Gruppen.
5. Aktualisieren Sie Ihre Richtlinie für bedingten Zugriff so, dass die neuen Objekte zugelassen werden, indem Sie die Anweisungen unter [Einrichten der mehrstufigen Authentifizierung](set-up-mfa.md) befolgen.

Um Ausfallzeiten zu vermeiden, sollten Sie zuerst Ihre vorhandenen Sitzungshosts jeweils in kleinen Gruppen bei den in Azure Resource Manager integrierten Hostpools registrieren. Übertragen Sie anschließend Ihre Benutzer langsam in die neuen in Azure Resource Manager integrierten App-Gruppen.

## <a name="next-steps"></a>Nächste Schritte

Nach der Migration können Sie sich in [unseren Tutorials](create-host-pools-azure-marketplace.md) über die Funktionsweise von Windows Virtual Desktop informieren. Informationen zu erweiterten Verwaltungsfunktionen finden Sie unter [Erweitern eines vorhandenen Hostpools](expand-existing-host-pool.md) und [Anpassen der RDP-Eigenschaften](customize-rdp-properties.md).

Informationen zu Dienstobjekten finden Sie unter [Windows Virtual Desktop-Umgebung](environment-setup.md).
