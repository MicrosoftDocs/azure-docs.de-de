---
title: 'Tutorial: Erstellen der Infrastruktur für einen Service Fabric-Cluster in AWS – Azure Service Fabric | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die AWS-Infrastruktur zum Ausführen eines Service Fabric-Clusters einrichten.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 69508628356a5f33073311e4d062d66875509192
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302474"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>Tutorial: Erstellen der AWS-Infrastruktur zum Hosten eines Service Fabric-Clusters

Mit eigenständigen Service Fabric-Clustern können Sie Ihre eigene Umgebung wählen und einen Cluster im Rahmen des Service Fabric-Konzepts „Jedes Betriebssystem, jede Cloud“ erstellen. In dieser Tutorialreihe erstellen Sie einen eigenständigen, in AWS gehosteten Cluster und installieren darin eine Anwendung.

Dieses Tutorial ist der erste Teil einer Serie. In diesem Artikel generieren Sie die AWS-Ressourcen, die zum Hosten Ihres eigenständigen Service Fabric-Clusters benötigt werden. In den Folgeartikeln installieren Sie die eigenständige Service Fabric-Suite sowie eine Beispielanwendung in Ihrem Cluster und bereinigen schließlich Ihren Cluster.

Im ersten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Reihe von EC2-Instanzen
> * Ändern der Sicherheitsgruppe
> * Anmelden bei einer der Instanzen
> * Vorbereiten der Instanz für Service Fabric

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie ein AWS-Konto.  Falls Sie noch kein Konto haben, navigieren Sie zur [AWS-Konsole](https://aws.amazon.com/), um eins zu erstellen.

## <a name="create-ec2-instances"></a>Erstellen von EC2-Instanzen

Melden Sie sich bei der AWS-Konsole an, geben Sie **EC2** in das Suchfeld ein, und klicken Sie auf **EC2 Virtual Servers in the Cloud** (Virtuelle EC2-Server in der Cloud).

![Suche in der AWS-Konsole][aws-console]

Klicken Sie auf **Launch Instance** (Instanz starten), und klicken Sie im nächsten Bildschirm neben „Microsoft Windows Server 2016 Base“ auf **Select** (Auswählen).

![EC2-Instanzauswahl][aws-ec2instance]

Wählen Sie **t2.medium** und anschließend **Next: Configure Instance Details** (Nächster Schritt: Instanzdetails konfigurieren) aus. Ändern Sie auf dem nächsten Bildschirm die Instanzenanzahl in `3`, und wählen Sie dann **Advanced Details** (Erweiterte Details) aus, um diesen Abschnitt zu erweitern.

Damit Ihre virtuellen Computer in Service Fabric miteinander verbunden werden können, müssen die virtuellen Computer, die Ihre Infrastruktur hosten, die gleichen Anmeldeinformationen besitzen.  Konsistente Anmeldeinformationen lassen sich grundsätzlich auf zwei Arten erreichen: Sie können alle der gleichen Domäne hinzufügen oder auf jedem virtuellen Computer das gleiche Administratorkennwort festlegen.  In diesem Tutorial verwenden Sie ein Benutzerdatenskript, um alle EC2-Instanzen mit dem gleichen Kennwort zu konfigurieren.  In einer Produktionsumgebung sollten die Hosts aus Sicherheitsgründen einer Windows-Domäne hinzugefügt werden.

Geben Sie im Benutzerdatenfeld der Konsole das folgende Skript ein:

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

Klicken Sie nach der Eingabe des PowerShell-Skripts auf **Review and Launch** (Überprüfen und starten).

![EC2 überprüfen und starten][aws-ec2configure2]

Klicken Sie im Prüfbildschirm auf **Launch** (Starten).  Wählen Sie dann in der Dropdownliste die Option **Proceed without a key pair** (Ohne Schlüsselpaar fortfahren) aus, und aktivieren Sie das Kontrollkästchen, um anzugeben, dass Sie das Kennwort kennen.

![Auswahl für AWS-Schlüsselpaar][aws-keypair]

Klicken Sie abschließend auf **Launch Instances** (Instanzen starten) und dann auf **View Instances** (Instanzen anzeigen).  Damit haben Sie die Grundlage für Ihren Service Fabric-Cluster erstellt. Als Nächstes müssen den Instanzen noch einige abschließende Konfigurationen hinzugefügt werden, um sie für die Service Fabric-Konfiguration vorzubereiten.

## <a name="modify-the-security-group"></a>Ändern der Sicherheitsgruppe

Für Service Fabric müssen einige Ports zwischen den Hosts in Ihrem Cluster geöffnet sein. Um diese Ports in der AWS-Infrastruktur zu öffnen, wählen Sie eine der erstellten Instanzen aus. Wählen Sie den Namen der Sicherheitsgruppe aus (beispielsweise **launch-wizard-1**). Klicken Sie auf die Registerkarte **Inbound** (Eingehend).

Öffnen Sie diese Ports nur für Hosts in der gleichen Sicherheitsgruppe, um sie nicht zugänglich zu machen. Notieren Sie sich die Sicherheitsgruppen-ID (**sg-c4fb1eba** in diesem Beispiel).  Klicken Sie anschließend auf **Edit** (Bearbeiten).

Fügen Sie der Sicherheitsgruppe als Nächstes vier Regeln für Dienstabhängigkeiten und anschließend drei weitere für Service Fabric selbst hinzu. Die erste Regel dient zum Zulassen von ICMP-Datenverkehr für grundlegende Konnektivitätsprüfungen. Die anderen Regeln öffnen die erforderlichen Ports für SMB und Remoteregistrierung.

Klicken Sie für die erste Regel auf **Add Rule** (Regel hinzufügen), und wählen Sie dann im Dropdownmenü die Option **All ICMP - IPv4** (Vollständiges ICMP – IPv4) aus. Klicken Sie auf das Eingabefeld neben „Custom“ (Benutzerdefiniert), und geben Sie Ihre Sicherheitsgruppen-ID von weiter oben ein.

Die Vorgehensweise für die letzten drei Abhängigkeiten ist ähnlich.  Klicken Sie auf **Add Rule** (Regel hinzufügen), wählen Sie in der Dropdownliste die Option **Custom TCP Rule** (Benutzerdefinierte TCP-Regel) aus, und geben Sie pro Regel jeweils einen der folgenden Portbereiche ein: `135`, `137-139` und `445`. Geben Sie abschließend Ihre Sicherheitsgruppen-ID in das Quellfeld ein.

![Sicherheitsgruppenports][aws-ec2securityports]

Nachdem Sie nun über geöffnete Ports für die Abhängigkeiten verfügen, müssen Sie das gleiche Verfahren für die Ports durchführen, die Service Fabric selbst für die Kommunikation verwendet. Klicken Sie auf **Add Rule** (Regel hinzufügen), wählen Sie in der Dropdownliste die Option **Custom TCP Rule** (Benutzerdefinierte TCP-Regel) aus, geben Sie den Portbereich `20001-20031` ein, und geben Sie anschließend die Sicherheitsgruppe in das Quellfeld ein.

Fügen Sie als Nächstes eine Regel für den kurzlebigen Portbereich hinzu.  Klicken Sie auf **Add Rule** (Regel hinzufügen), wählen Sie in der Dropdownliste die Option **Custom TCP Rule** (Benutzerdefinierte TCP-Regel) aus, und geben Sie den Portbereich `20606-20861` ein. Geben Sie abschließend Ihre Sicherheitsgruppen-ID in das Quellfeld ein.

Machen Sie die Ports bei den letzten beiden Service Fabric-Regeln öffentlich zugänglich, damit Sie Ihren Service Fabric-Cluster über Ihren Personalcomputer verwalten können. Klicken Sie auf **Add Rule** (Regel hinzufügen), wählen Sie in der Dropdownliste die Option **Custom TCP Rule** (Benutzerdefinierte TCP-Regel) aus, geben Sie den Portbereich `19000-19003` bzw. `19080-19081` ein, und wählen Sie in der Dropdownliste für die Quelle die Option „Anywhere“ (Beliebiger Ort) aus.

Nun muss nur noch der Port 8080 geöffnet werden, damit Sie die Anwendung nach der Bereitstellung anzeigen können. Klicken Sie auf **Add Rule** (Regel hinzufügen), wählen Sie in der Dropdownliste die Option **Custom TCP Rule** (Benutzerdefinierte TCP-Regel) aus, geben Sie den Portbereich `8080` ein, und wählen Sie in der Dropdownliste für die Quelle die Option „Anywhere“ (Beliebiger Ort) aus.

Alle Regeln sind nun eingegeben. Wählen Sie **Speichern** aus.

## <a name="connect-to-an-instance-and-validate-connectivity"></a>Herstellen einer Verbindung mit einer Instanz und Überprüfen der Konnektivität

Klicken Sie auf der Registerkarte „Security Group“ (Sicherheitsgruppe) im linken Menü auf die Option **Instances** (Instanzen).  Wählen Sie die einzelnen Instanzen aus, die Sie erstellt haben, und notieren Sie sich jeweils die private IP-Adresse. In den folgenden Beispielen werden `172.31.21.141` und `172.31.20.163` verwendet.

Nachdem Sie über alle IP-Adressen verfügen, wählen Sie zur Verbindungsherstellung eine der Instanzen aus, klicken Sie mit der rechten Maustaste darauf, und klicken Sie anschließend auf **Connect** (Verbinden).  Hier können Sie die RDP-Datei für diese spezielle Instanz herunterladen.  Klicken Sie auf **Download Remote Desktop File** (Remotedesktopdatei herunterladen), und öffnen Sie die heruntergeladene Datei, um die RDP-Verbindung (Remotedesktopverbindung) mit dieser Instanz herzustellen.  Geben Sie nach entsprechender Aufforderung Ihr Kennwort (`serv1ceF@bricP@ssword`) ein.

![Herunterladen der Remotedesktopdatei][aws-rdp]

Vergewissern Sie sich nach erfolgreicher Verbindungsherstellung mit Ihrer Instanz, dass Sie eine Verbindung zwischen ihnen herstellen und auch Dateien freigeben können.  Sie haben die IP-Adressen für alle Instanzen erfasst. Wählen Sie eine Instanz, mit der momentan keine Verbindung besteht. Geben Sie unter **Start** die Zeichenfolge `cmd` ein, und klicken Sie auf **Eingabeaufforderung**.

In diesen Beispielen wurde die RDP-Verbindung mit der folgenden IP-Adresse hergestellt: 172.31.21.141. Alle Konnektivitätstests werden daher für die andere IP-Adresse durchgeführt: 172.31.20.163.

Verwenden Sie zur Überprüfung der grundlegenden Konnektivität den Pingbefehl.

```
ping 172.31.20.163
```

Wenn viermal eine Ausgabe wie `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` zurückgegeben wird, funktioniert die Verbindung zwischen den Instanzen.  Überprüfen Sie nun mithilfe des folgenden Befehls, ob Ihre SMB-Freigabe funktioniert:

```
net use * \\172.31.20.163\c$
```

Die zurückgegebene Ausgabe sollte in etwa wie folgt aussehen: `Drive Z: is now connected to \\172.31.20.163\c$.`.

## <a name="prep-instances-for-service-fabric"></a>Vorbereiten der Instanzen für Service Fabric

Wenn Sie alles von Grund auf neu erstellen, sind noch einige Zusatzschritte erforderlich.  In diesem Fall müssten Sie sich vergewissern, dass die Remoteregistrierung ausgeführt wird, und Sie müssten SMB aktivieren und die erforderlichen Ports für SMB und Remoteregistrierung öffnen.

Zur Vereinfachung haben Sie alle diese Schritte beim Bootstrapping der Instanzen mit Ihrem Benutzerdatenskript eingebettet.

SMB wurde mithilfe des folgenden PowerShell-Befehls aktiviert:

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

Zum Öffnen der Firewallports wurde der folgende PowerShell-Befehl verwendet:

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>Nächste Schritte

Im ersten Teil der Reihe haben Sie gelernt, wie Sie drei EC2-Instanzen starten und für die Service Fabric-Installation konfigurieren:

> [!div class="checklist"]
> * Erstellen einer Reihe von EC2-Instanzen
> * Ändern der Sicherheitsgruppe
> * Anmelden bei einer der Instanzen
> * Vorbereiten der Instanz für Service Fabric

Im zweiten Teil der Reihe erfahren Sie, wie Sie Service Fabric in Ihrem Cluster konfigurieren.

> [!div class="nextstepaction"]
> [Installieren und Erstellen eines Service Fabric-Clusters](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png