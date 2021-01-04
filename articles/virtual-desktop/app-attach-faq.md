---
title: 'Häufig gestellte Fragen zu Windows Virtual Desktop: MSIX-Feature zum Anfügen von Apps – Azure'
description: Häufig gestellte Fragen zum MSIX-Feature zum Anfügen von Apps für Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 73fb9bf436c043e903977fafbb5a502e2edc5488
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518684"
---
# <a name="msix-app-attach-faq"></a>Häufig gestellte Fragen zum MSIX-Feature zum Anfügen von Apps

In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen zum MSIX-Feature zum Anfügen von Apps für Windows Virtual Desktop.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Worin besteht der Unterschied zwischen MSIX und dem MSIX-Feature zum Anfügen von Apps?

MSIX ist ein Verpackungsformat für Apps, während das MSIX-Feature zum Anfügen von Apps MSIX-Pakete an Ihre Bereitstellung übergibt.

## <a name="does-msix-app-attach-use-fslogix"></a>Verwendet das MSIX-Feature zum Anfügen von Apps FSLogix?

Das MSIX-Feature zum Anfügen von Apps verwendet FSLogix nicht. Das MSIX-Feature zum Anfügen von Apps und FSLogix sind jedoch so konzipiert, dass sie zusammen verwendet werden können, um eine einheitliche Benutzererfahrung zu ermöglichen.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kann ich das MSIX-Feature zum Anfügen von Apps außerhalb von Windows Virtual Desktop verwenden?

Die APIs, die das MSIX-Feature zum Anfügen von Apps benötigt, sind für Windows 10 Enterprise verfügbar. Diese APIs können außerhalb von Windows Virtual Desktop verwendet werden. Es gibt jedoch keine Verwaltungsebene für das MSIX-Feature zum Anfügen von Apps außerhalb von Windows Virtual Desktop.

## <a name="how-do-i-get-an-msix-package"></a>Wie rufe ich ein MSIX-Paket ab?

Sie erhalten ein MSIX-Paket bei Ihrem Softwarehersteller. Sie können auch Nicht-MSIX-Pakete in das MSIX-Format konvertieren. Weitere Informationen finden Sie unter [Verschieben vorhandener Installationsprogramme in MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Welche Betriebssysteme unterstützen das MSIX-Feature zum Anfügen von Apps?

Windows 10 Enterprise und Windows 10 Enterprise Multisession, mindestens Version 2004.

## <a name="is-msix-app-attach-currently-generally-available"></a>Ist das MSIX-Feature zum Anfügen von Apps zurzeit allgemein verfügbar?

Das MSIX-Feature zum Anfügen von Apps ist ein Teil von Windows 10 Enterprise und Windows 10 Enterprise Multisession, mindestens Version 2004. Beide Betriebssysteme sind zurzeit allgemein verfügbar. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kann ich das MSIX-Feature zum Anfügen von Apps außerhalb von Windows Virtual Desktop verwenden?

Die APIs für MSIX und das MSIX-Feature zum Anfügen von Apps sind Teil von Windows 10 Enterprise und Windows 10 Enterprise Multisession, mindestens Version 2004. Wir bieten derzeit außerhalb von Windows Virtual Desktop keine Verwaltungssoftware für das MSIX-Feature zum Anfügen von Apps.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Kann ich zwei Versionen derselben Anwendung gleichzeitig ausführen?

Damit zwei Versionen derselben MSIX-Anwendungen gleichzeitig ausgeführt werden können, muss in der Datei „appxmanifest.xml“ für jede App eine andere MSIX-Paketfamilie definiert werden.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Sollte ich die automatische Aktualisierung deaktivieren, wenn ich das MSIX-Feature zum Anfügen von Apps verwende?

Ja. Das MSIX-Feature zum Anfügen von Apps unterstützt keine automatische Aktualisierung für MSIX-Anwendungen.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Wie funktionieren Berechtigungen in Verbindung mit dem MSIX-Feature zum Anfügen von Apps?

Alle virtuellen Computer (VMs) in einem Hostpool, der das MSIX-Feature zum Anfügen von Apps verwendet, müssen über Leseberechtigungen für die Dateifreigabe verfügen, in der die MSIX-Images gespeichert sind. Wenn er auch Azure Files verwendet, müssen ihnen sowohl rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) als auch NTFS-Berechtigungen (New Technology File System) gewährt werden.

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Kann ich das MSIX-Feature zum Anfügen von Apps für HTTP oder HTTPS verwenden?

Alle VMs, die Teil eines Hostpools sind, der das MSIX-Feature zum Anfügen von Apps verwendet, müssen über Leseberechtigungen für die Dateifreigabe verfügen, in der MSIX-Images gespeichert sind. Wenn Azure Files verwendet wird, müssen sowohl RBAC- als auch NTFS-Berechtigungen erteilt werden.

## <a name="can-i-restage-the-same-msix-application"></a>Kann ich dieselbe MSIX-Anwendung erneut bereitstellen?

Ja. Sie können bereits bereitgestellte Anwendungen erneut bereitstellen. Dies sollte keine Fehler verursachen.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>Unterstützt das MSIX-Feature zum Anfügen von Apps selbstsignierte Zertifikate?

Die Verwendung des MSIX-Features zum Anfügen von Apps über HTTP oder HTTPS wird derzeit nicht unterstützt.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über das MSIX-Feature zum Anfügen von Apps finden Sie in unserer [Übersicht](what-is-app-attach.md) und im [Glossar](app-attach-glossary.md). Oder legen Sie direkt mit dem [Einrichten des MSIX-Features zum Anfügen von Apps](app-attach.md) los.
