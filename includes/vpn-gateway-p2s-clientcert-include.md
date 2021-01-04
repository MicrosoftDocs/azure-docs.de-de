---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: 34986ac80a309bcfd495e5782496ba560f84c5f7
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041591"
---
Auf jedem Clientcomputer, für den Sie eine Point-to-Site-Verbindung mit einem VNet herstellen, muss ein Clientzertifikat installiert sein. Sie generieren es aus dem Stammzertifikat und installieren es auf jedem Clientcomputer. Wenn Sie kein gültiges Clientzertifikat installieren, und der Client versucht, eine Verbindung mit dem VNET herzustellen, tritt bei der Authentifizierung ein Fehler auf.

Sie können entweder ein eindeutiges Zertifikat für jeden Client generieren, oder Sie können dasselbe Zertifikat für mehrere Clients verwenden. Der Vorteil beim Generieren von eindeutigen Clientzertifikaten besteht darin, dass Sie ein einzelnes Zertifikat widerrufen können. Falls mehrere Clients das gleiche Clientzertifikat zum Authentifizieren verwenden, und Sie dieses Zertifikat widerrufen, müssen Sie für alle Clients, die das Zertifikat verwenden, neue Zertifikate generieren und installieren.

Sie können mithilfe der folgenden Methoden Clientzertifikate generieren:

* **Unternehmenszertifikat:**

  * Generieren Sie bei Verwendung einer Unternehmenszertifikatlösung ein Clientzertifikat mit dem gängigen Name-Wert-Format *name\@ihredomäne.com*. Verwenden Sie dieses Format anstelle des Formats *Domänenname\Benutzername*.

  * Stellen Sie sicher, dass das Clientzertifikat auf einer Benutzerzertifikatvorlage basiert, für die *Clientauthentifizierung* als erstes Element in der Benutzerliste enthalten ist. Überprüfen Sie das Zertifikat, indem Sie darauf doppelklicken und **Erweiterte Schlüsselverwendung** auf der **Registerkarte Details** anzeigen.

* **Selbstsigniertes Stammzertifikat** : Führen Sie die Schritte in einem der folgenden Artikel zu P2S-Zertifikaten aus, damit die von Ihnen erstellten Clientzertifikate mit Ihren P2S-Verbindungen kompatibel sind.

  Wenn Sie ein Clientzertifikat auf der Grundlage eines selbstsignierten Stammzertifikats generieren, wird es automatisch auf dem Computer installiert, den Sie für die Generierung verwendet haben. Falls Sie ein Clientzertifikat auf einem anderen Clientcomputer installieren möchten, exportieren Sie es zusammen mit der gesamten Zertifikatkette als PFX-Datei. Dadurch wird eine PFX-Datei mit Angaben zum Stammzertifikat erstellt, die der Client zur Authentifizierung benötigt.

  Mit den Schritten in diesen Artikeln wird ein kompatibles Clientzertifikat generiert, das Sie anschließend exportieren und verteilen können.

  * [Generieren und Exportieren von Zertifikaten für Punkt-zu-Site-Verbindungen mithilfe von PowerShell unter Windows 10](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Für diese Anweisungen sind Windows 10 und PowerShell erforderlich, um Zertifikate zu generieren. Die generierten Zertifikate können auf jedem unterstützten P2S-Client installiert werden.

  * [MakeCert-Anweisungen](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Wenn Sie keinen Zugriff auf einen Windows 10-Computer haben, verwenden Sie MakeCert, um Zertifikate zu generieren. MakeCert ist zwar veraltet, kann aber trotzdem zum Generieren von Zertifikaten verwendet werden. Sie können die generierten Zertifikate auf jedem unterstützten P2S-Client installieren.

  * [Linux-Anweisungen](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)