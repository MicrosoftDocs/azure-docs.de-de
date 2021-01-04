---
title: Installieren von Micro Focus Enterprise Server 5.0 und Enterprise Developer 5.0 in Azure | Microsoft-Dokumentation
description: In diesem Artikel wird gezeigt, wie Sie Micro Focus Enterprise Server 5.0 und Enterprise Developer 5.0 in Microsoft Azure installieren.
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: d9e5f9b531fc28caf8f3162a70318927d40bb923
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483061"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>Installieren von Micro Focus Enterprise Server 5.0 und Enterprise Developer 5.0 in Azure

In diesem Artikel wird gezeigt, wie Sie [Micro Focus Enterprise Server 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) und [Micro Focus Enterprise Developer 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50/) in Microsoft Azure einrichten.

Eine gängige Workload für Azure ist eine Entwicklungs- und Testumgebung. Dieses Szenario ist gängig, weil es so kostengünstig sowie einfach bereitzustellen und zu beenden ist. Mit Enterprise Server hat Micro Focus eine der größten verfügbaren Mainframe-Rehostingplattformen erstellt. Sie können z/OS-Workloads auf einer weniger teuren x86-Plattform in Azure mithilfe von wahlweise Windows- oder Linux-VMs ausführen.

Dieses Setup verwendet Azure-VMs, die das Windows Server 2016-Image aus dem Azure Marketplace mit bereits installiertem Microsoft SQL Server 2017 ausführen. Dieses Setup ist auch für Azure Stack gültig.

Die entsprechende Entwicklungsumgebung für Enterprise Server ist Enterprise Developer und wird wahlweise in Microsoft Visual Studio 2017 oder höher, Visual Studio Community (kostenloser Download) oder Eclipse ausgeführt. In diesem Artikel erfahren Sie, wie Sie es in einer Windows Server 2016-VM mit installiertem Visual Studio 2017 oder höher bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

Überprüfen Sie zunächst diese Voraussetzungen:

-   Ein Azure-Abonnement. Sollten Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

-   Die Micro Focus-Software und eine gültige Lizenz (oder Testlizenz). Wenn Sie ein Micro Focus-Bestandskunde sind, wenden Sie sich an Ihren Micro Focus-Vertreter. Andernfalls können Sie [eine Testversion anfordern](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

-   Rufen Sie die Dokumentation zu [Enterprise Server und Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/ed50/) ab.

    > [!Note]
    > Es gibt einige Optionen, mit denen Sie Zugriff auf Ihre VMs steuern können:
    > -   Eine bewährte Methode ist das Einrichten von [Azure Bastion](https://azure.microsoft.com/services/azure-bastion/).
    > -   Ein [Site-to-Site-VPN-Tunnel](../../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) (virtuelles privates Netzwerk).
    > -   Eine Jumpbox-VM.

## <a name="install-enterprise-server"></a>Installieren von Enterprise Server

1.  Um bessere Sicherheit und Verwaltbarkeit zu erzielen, sollten Sie die Erstellung einer neuen Ressourcengruppe eigens für dieses Projekt in Erwägung ziehen – beispielsweise **RGMicroFocusEntServer**. Verwenden Sie den ersten Teil des Namens in Azure, um den Typ der Ressource auszuwählen, damit sie sich in einer Liste leichter finden lässt.

2.  Erstellen Sie eine VM. Wählen Sie im Azure Marketplace den virtuellen Computer und das Betriebssystem Ihrer Wünsche aus. Ein empfohlenes Setup finden Sie hier:

    -   **Enterprise Server:** Wählen Sie **ES2 v3 VM** (mit 2 vCPUs und 16 GB Arbeitsspeicher) mit installiertem Windows Server 2016 und SQL Server 2017 aus. Dieses Image ist im Azure Marketplace verfügbar. Enterprise Server kann ebenso gut Azure SQL-Datenbank verwenden.

    -   **Enterprise Developer:** Wählen Sie **B2ms VM** (mit 2 vCPUs und 8 GB Arbeitsspeicher) mit installiertem Windows 10 und Visual Studio aus. Dieses Image ist im Azure Marketplace verfügbar.

3.  Geben Sie im Abschnitt **Grundlegende Einstellungen** Ihren Benutzernamen und Ihr Kennwort ein. Wählen Sie das **Abonnement** und **den Standort/die Region** aus, die Sie für die VMs verwenden möchten. Wählen Sie **RGMicroFocusEntServer** für die Ressourcengruppe aus.

4.  Platzieren Sie beide VMs im gleichen virtuellen Netzwerk, damit sie miteinander kommunizieren können.

5.  Übernehmen Sie für den Rest der Einstellungen die Standardwerte. Merken Sie sich den Benutzernamen und das Kennwort, die Sie für den Administrator dieser VMs erstellen.

6.  Wenn die virtuellen Computer erstellt wurden, öffnen Sie die eingehenden Ports **9003, 86** und **80** für HTTP, **3389** für Remote Desktop Protocol (RDP) auf dem Enterprise Server-Computer sowie **3389** auf dem Developer-Computer.

7.  Um sich bei der Enterprise Server-VM anzumelden, wählen Sie im Azure-Portal die ES2 v3 VM aus. Navigieren Sie zum Abschnitt **Übersicht**, und wählen Sie **Verbinden** aus, um eine RDP-Sitzung zu starten. Melden Sie sich mit den Anmeldeinformationen an, die Sie für die VM erstellt haben.

8.  Laden Sie aus der RDP-Sitzung die beiden folgenden Dateien. Da Sie Windows verwenden, können Sie die Dateien per Drag & Drop in die RDP-Sitzung ziehen:

    -   `es\_50.exe`, die Enterprise Server-Installationsdatei.

    -   `mflic`, die entsprechende Lizenzdatei – Enterprise Server lädt ohne sie nicht.

9.  Doppelklicken Sie auf die Datei, um die Installation zu starten. Wählen Sie im ersten Fenster den Installationsspeicherort aus, und stimmen Sie dem Endbenutzer-Lizenzvertrag zu.

    ![Der Screenshot zeigt das Dialogfeld „Micro Focus Enterprise Server“, in dem Sie die Installation starten können.](media/install-image-1.png)

    Wenn das Setup abgeschlossen ist, wird die folgende Nachricht angezeigt:

    ![Der Screenshot zeigt eine Erfolgsmeldung im Dialogfeld „Micro Focus Enterprise Server“.](media/install-image-2.png)

 ### <a name="check-for-updates"></a>Suchen nach Updates

Achten Sie nach der Installation darauf, auf eventuell verfügbare Updates zu prüfen, da eine Reihe von Voraussetzungen zusammen mit Enterprise Server installiert wird, darunter Microsoft C++ Redistributable und .NET Framework.

### <a name="upload-the-license"></a>Hochladen der Lizenz

1.  Starten der Micro Focus-Lizenzverwaltung.

2.  Wählen Sie **Start** \> **Micro Focus-Lizenz-Manager** \> **Lizenzverwaltung** aus, und klicken Sie dann auf die Registerkarte **Installieren**. Wählen Sie den Typ des hochzuladenden Lizenzformats aus: eine Lizenzdatei oder ein 16-stelliger Lizenzcode. Navigieren Sie beispielsweise für eine Datei unter **Lizenzdatei** zu der Datei „*`mflic`, die Sie zuvor in die VM hochgeladen haben, und wählen Sie **Lizenzen installieren** aus.

    ![Der Screenshot zeigt das Dialogfeld für die Micro Focus-Lizenzverwaltung, in dem Sie „Lizenzen installieren“ auswählen können.](media/install-image-3.png)

3.  Überprüfen Sie, ob Enterprise Server geladen wird. Versuchen Sie, die Enterprise Server Administration-Website in einem Browser zu starten, indem Sie diese URL verwenden: `http://localhost:86/`. Die Enterprise Server Administration-Seite wird wie dargestellt angezeigt.

    ![Enterprise Server Administration-Seite](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Installieren von Enterprise Developer auf dem Entwicklercomputer

1.  Wählen Sie die zuvor erstellte Ressourcengruppe (beispielsweise **RGMicroFocusEntServer**) und dann das Entwicklerimage aus.

2.  Um sich beim virtuellen Computer anzumelden, wechseln Sie zum Abschnitt **Übersicht**, und wählen Sie **Verbinden** aus. Durch diese Anmeldung wird eine RDP-Sitzung gestartet. Melden Sie sich mit den Anmeldeinformationen an, die Sie für die VM erstellt haben.

3.  Laden Sie aus der RDP-Sitzung die beiden folgenden Dateien (per Drag & Drop, wenn Sie möchten):

    -   `edvs2017.exe`, die Enterprise Server-Installationsdatei.

    -   `mflic`, die entsprechende Lizenzdatei (Enterprise Developer lädt ohne sie nicht).

4.  Doppelklicken Sie auf die **edvs2017.exe**-Datei, um die Installation zu starten. Wählen Sie im ersten Fenster den Installationsspeicherort aus, und stimmen Sie dem Endbenutzer-Lizenzvertrag zu. Wenn gewünscht, wählen Sie **Rumba 9.5 installieren** aus, um diesen Terminalemulator zu installieren, den Sie wahrscheinlich benötigen.

    ![Micro Focus Enterprise Developer für Visual Studio 2017-Setupdialogfeld](media/install-image-5.png)

5.  Nachdem das Setup abgeschlossen ist, wird die folgende Nachricht angezeigt:

    ![Setup erfolgreich-Nachricht](media/install-image-6.png)

6.  Starten Sie den Micro Focus-Lizenz-Manager, genau so, wie Sie das für Enterprise Server getan haben. Wählen Sie **Start** \> **Micro Focus-Lizenz-Manager** \> **Lizenzverwaltung** aus, und klicken Sie auf die Registerkarte **Installieren**.

7.  Wählen Sie den Typ des hochzuladenden Lizenzformats aus: eine Lizenzdatei oder ein 16-stelliger Lizenzcode. Navigieren Sie beispielsweise für eine Datei unter **Lizenzdatei** zu der `mflic`-Datei, die Sie zuvor in die VM hochgeladen haben, und wählen Sie **Lizenzen installieren** aus.

    ![Micro Fokus-Lizenzverwaltung (Dialogfeld)](media/install-image-7.png)

Wenn Enterprise Developer geladen wird, ist Ihre Bereitstellung einer Micro Focus-Entwicklungs- und Testumgebung in Azure abgeschlossen!

**Nächste Schritte**

-   [Einrichten der BankDemo-Anwendung](./demo.md)

-   [Ausführen von Enterprise Server in Docker-Containern](./run-enterprise-server-container.md)

-   [Migration von Mainframeanwendungen](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)