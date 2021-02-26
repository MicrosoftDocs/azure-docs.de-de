---
title: Einrichten eines SOLIDWORKS-Labs für Engineeringkurse mit Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von SOLIDWORKS ein Lab für Engineeringkurse einrichten.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 3a7e8c47977f0518a3a3e9f8a6fd2e57454e1c42
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626103"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>Einrichten eines Labs für Engineeringkurse mithilfe von SOLIDWORKS

[SOLIDWORKS](https://www.solidworks.com/) stellt eine 3D-CAD-Umgebung (Computer-Aided Design) zum Modellieren von Volumenkörperobjekten bereit und wird in verschiedenen Engineeringbereichen verwendet.  Mit SOLIDWORKS können Ingenieure ihre Entwürfe ganz einfach erstellen, visualisieren, simulieren und dokumentieren.

Eine von Hochschulen häufig verwendete Lizenzierungsoption ist die Netzwerklizenzierung von SOLIDWORKS.   Mit dieser Option verwenden Benutzer einen Pool von Lizenzen gemeinsam, die von einem Lizenzierungsserver verwaltet werden.  Diese Art von Lizenz wird manchmal als „übergreifende“ Lizenz bezeichnet, da Sie nur über genügend Lizenzen für die Anzahl gleichzeitiger Benutzer verfügen müssen.  Wenn ein Benutzer seine Arbeit mit SOLIDWORKS beendet hat, geht die Lizenz wieder zurück in den zentral verwalteten Lizenzpool und kann von einem anderen Benutzer wiederverwendet werden.

In diesem Artikel erfahren Sie, wie Sie einen Kurs einrichten, in dem SOLIDWORKS 2019 und die Netzwerklizenzierung verwendet werden.

## <a name="license-server"></a>Lizenzserver

Die SOLIDWORKS-Netzwerklizenzierung erfordert, dass Sie den Lizenz-Manager von SolidNetWork auf dem Lizenzserver installiert und aktiviert haben.  Dieser Lizenzserver befindet sich in der Regel in Ihrem lokalen Netzwerk oder in einem privaten Netzwerk in Azure.  Weitere Informationen zum Einrichten des Lizenz-Managers von SolidNetWork auf Ihrem Server finden Sie unter [Installieren und Aktivieren eines Lizenz-Managers](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) im SOLIDWORKS-Installationsleitfaden.  Notieren Sie sich beim Einrichten die verwendete **Portnummer** und [**Seriennummer**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm), da beide Angaben in späteren Schritten benötigt werden.

Nach dem Einrichten des Lizenzservers müssen Sie eine Peerverbindung zwischen dem [virtuellen Netzwerk (VNET)](./how-to-connect-peer-virtual-network.md) und Ihrem [Lab-Konto](./tutorial-setup-lab-account.md) herstellen.  Das Netzwerkpeering muss vor dem Erstellen des Labs erfolgen, damit die virtuellen Lab-Computer auf den Lizenzserver zugreifen können und umgekehrt.

> [!NOTE]
> Vergewissern Sie sich, dass die entsprechenden Ports Ihrer Firewalls geöffnet sind, um die Kommunikation zwischen den virtuellen Lab-Computern und dem Lizenzserver zu erlauben.  Informationen hierzu finden Sie beispielsweise in den Anleitungen unter [Ändern der Lizenz-Manager-Computerports für die Windows-Firewall](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm), die zeigen, wie der Firewall des Lizenzservers Eingangs- und Ausgangsregeln hinzugefügt werden.  Möglicherweise müssen Sie auch Ports für die virtuellen Lab-Computer öffnen.  Führen Sie die Schritte im Artikel [Firewalleinstellungen für Labs](./how-to-configure-firewall-settings.md) aus, um weitere Informationen zu diesem Thema zu erhalten, einschließlich Informationen dazu, wie Sie die öffentliche IP-Adresse des Labs abrufen.

## <a name="lab-configuration"></a>Labkonfiguration

Zum Einrichten dieses Labs benötigen Sie zunächst ein Azure-Abonnement und ein Lab-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie ein neues Lab-Konto in Azure Lab Services erstellen. Weitere Informationen zum Erstellen eines neuen Lab-Kontos finden Sie im Tutorial zum [Einrichten eines Lab-Kontos](./tutorial-setup-lab-account.md). Sie können auch ein vorhandenes Lab-Konto verwenden.

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen

Aktivieren Sie die Einstellungen für das Lab-Konto, die in der nachfolgenden Tabelle beschrieben werden. Weitere Informationen zum Aktivieren von Marketplace-Images finden Sie im Artikel zum [Angeben von für Lab-Ersteller verfügbaren Marketplace-Images](./specify-marketplace-images.md).

| Lab-Kontoeinstellungen | Instructions |
| ------------------- | ------------ |
|Marketplace-Image| Aktivieren Sie das Windows 10 Pro-Image zur Verwendung in Ihrem Lab-Konto.|

> [!NOTE]
> Zusätzlich zu Windows 10 unterstützt SOLIDWORKS auch andere Versionen von Windows.  Weitere Informationen hierzu finden Sie unter [Systemanforderungen für SOLIDWORKS](https://www.solidworks.com/sw/support/SystemRequirements.html).

### <a name="lab-settings"></a>Lab-Einstellungen

Verwenden Sie beim Einrichten eines Classroom-Labs die Einstellungen in der unten stehenden Tabelle. Weitere Informationen zum Erstellen eines Classroom-Labs finden Sie im Tutorial zum Einrichten eines Classroom-Labs.

| Lab-Einstellungen | Wert/Anweisungen |
| ------------ | ------------------ |
|VM-Größe| **Kleine GPU (Visualisierung)** .  Diese VM eignet sich am besten für Remotevisualisierung, Streaming, Gaming und Codierung mit Frameworks wie OpenGL und DirectX.|  
|VM-Image| Windows 10 Pro|

> [!NOTE]
> Die VM-Größe **Kleine GPU (Visualisierung)** ist so konfiguriert, dass eine leistungsstarke Grafikdarstellung ermöglicht wird.  Weitere Informationen zu dieser VM-Größe finden Sie im Artikel zum [Einrichten eines Labs mit GPUs](./how-to-setup-lab-gpu.md).

> [!WARNING]
> Vergessen Sie nicht, **vor** dem Erstellen des Labs ein [Peering für das virtuelle Netzwerk](./how-to-connect-peer-virtual-network.md) für das Lab-Konto und das virtuelle Netzwerk für den Lizenzserver durchzuführen.

## <a name="template-virtual-machine-configuration"></a>Vorlage für die Konfiguration des virtuellen Computers

Die Schritte in diesem Abschnitt veranschaulichen, wie Sie die Vorlagen-VM einrichten, indem Sie die SOLIDWORKS-Installationsdateien herunterladen und die Clientsoftware installieren:

1. Starten Sie die Vorlagen-VM, und stellen Sie über RDP eine Verbindung mit dem Computer her.

1. Laden Sie die Installationsdateien für die SOLIDWORKS-Clientsoftware herunter. Für den Download stehen zwei Optionen zur Verfügung:
   - Download aus dem [SOLIDWORKS-Kundenportal](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F).
   - Download aus einem Verzeichnis auf einem Server.  Wenn Sie diese Option verwendet haben, müssen Sie sicherstellen, dass die Vorlagen-VM auf den Server zugreifen kann.  Beispielsweise befindet sich dieser Server möglicherweise im gleichen virtuellen Netzwerk, für das Peering mit Ihrem Lab-Konto ausgeführt wurde.
  
    Weitere Informationen finden Sie unter [Installation auf einzelnen Computern in SOLIDWORKS](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0) im SOLIDWORKS-Installationsleitfaden.

1. Nachdem Sie die Installationsdateien heruntergeladen haben, installieren Sie die Clientsoftware mit dem SOLIDWORKS-Installations-Manager. Weitere Informationen finden Sie unter [Installieren eines Lizenzclients](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) im SOLIDWORKS-Installationsleitfaden.

    > [!NOTE]
    > Im Dialogfeld **Server hinzufügen** werden Sie aufgefordert, die **Portnummer** anzugeben, die für Ihren Lizenzserver verwendet wird, sowie die IP-Adresse des Lizenzservers.

## <a name="cost"></a>Kosten

Betrachten wir eine mögliche Kostenschätzung für diesen Kurs. Diese Schätzung enthält nicht die Kosten für das Ausführen des Lizenzservers. Wir setzen einen Kurs mit 25 Kursteilnehmern an. Es ist eine Kursdauer von 20 Stunden geplant. Zudem erhält jeder Kursteilnehmer ein Kontingent von 10 Stunden für Hausaufgaben und Aufgaben außerhalb der regulären Kurszeiten. Als VM-Größe wurde **Kleine GPU (Visualisierung)** ausgewählt, was 160 Lab-Einheiten entspricht.

25 Kursteilnehmer \* (20 geplante Stunden + 10 Kontingentstunden) \* 160 Lab-Einheiten * 0,01 USD pro Stunde = 1.200,00 USD

>[!IMPORTANT]
> Diese Kostenschätzung dient ausschließlich zu Beispielzwecken.  Weitere Informationen zu den aktuellen Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Einrichtungsschritte sind für alle Labs gleich.

- [Erstellen und Verwalten einer Vorlage](how-to-create-manage-template.md)
- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)