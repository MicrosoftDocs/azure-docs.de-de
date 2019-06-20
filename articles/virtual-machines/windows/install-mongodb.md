---
title: Installieren von MongoDB auf einem virtuellen Windows-Computer in Azure | Microsoft Docs
description: Erfahren Sie, wie Sie MongoDB auf einem virtuellen Azure-Computer installieren, auf dem Windows Server 2012 R2 ausgeführt wird und der mit dem Resource Manager-Bereitstellungsmodell erstellt wurde.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: afd8e6b47fb86985acde062af1fb38ec3af4e902
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711423"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Installieren und Konfigurieren von MongoDB auf einem virtuellen Windows-Computer in Azure
[MongoDB](https://www.mongodb.org) ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. Dieser Artikel führt Sie durch die Installation und Konfiguration von MongoDB auf einem virtuellen Windows Server 2016-Computer in Azure. Sie können [MongoDB auch auf einem virtuellen Linux-Computer in Azure installieren](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Voraussetzungen
Vor der Installation und Konfiguration von MongoDB müssen Sie zunächst einen virtuellen Computer erstellen und ihm im Idealfall einen Datenträger hinzufügen. Informationen zum Erstellen eines virtuellen Computers und Hinzufügen eines Datenträgers finden Sie in den folgenden Artikeln:

* Erstellen Sie eine Windows Server-VM über das [Azure-Portal](quick-create-portal.md) oder per [Azure PowerShell](quick-create-powershell.md).
* Fügen Sie einen Datenträger an eine Windows Server-VM an, indem Sie das [Azure-Portal](attach-managed-disk-portal.md) oder [Azure PowerShell](attach-disk-ps.md) verwenden.

Um mit der Installation und Konfiguration von MongoDB zu beginnen, [melden Sie sich mit Remotedesktop bei Ihrem virtuellen Windows Server-Computer an](connect-logon.md).

## <a name="install-mongodb"></a>Installieren von MongoDB
> [!IMPORTANT]
> MongoDB-Sicherheitsfunktionen wie Authentifizierung und IP-Adressen-Anbindung werden nicht standardmäßig aktiviert. Die Sicherheitsfunktionen sollten aktiviert werden, bevor MongoDB in einer Produktionsumgebung eingesetzt wird. Weitere Informationen finden Sie unter [MongoDB Security and Authentication](https://www.mongodb.org/display/DOCS/Security+and+Authentication) (MongoDB: Sicherheit und Authentifizierung).


1. Wenn Sie über Remotedesktop eine Verbindung mit dem virtuellen Computer hergestellt haben, öffnen Sie Internet Explorer über die Taskleiste.
2. Wählen Sie beim ersten Öffnen von Internet Explorer die Option **Empfohlene Sicherheits-, Datenschutz- und Kompatibilitätseinstellungen verwenden**, und klicken Sie auf **OK**.
3. Die verstärkte Sicherheitskonfiguration für Internet Explorer ist standardmäßig aktiviert. Fügen Sie die MongoDB-Website zur Liste zugelassener Websites hinzu:
   
   * Klicken Sie in der oberen rechten Ecke auf das Symbol **Extras**.
   * Öffnen Sie unter **Internetoptionen** die Registerkarte **Sicherheit**, und wählen Sie anschließend das Symbol **Vertrauenswürdige Sites**.
   * Klicken Sie dann auf die Schaltfläche **Sites**. Fügen Sie *https://\*.mongodb.com* der Liste mit den vertrauenswürdigen Sites hinzu, und schließen Sie dann das Dialogfeld.
     
     ![Konfigurieren der Internet Explorer-Sicherheitseinstellungen](./media/install-mongodb/configure-internet-explorer-security.png)
4. Wechseln Sie zu der Seite [MongoDB – Downloads](https://www.mongodb.com/downloads) (https://www.mongodb.com/downloads).
5. Bei Bedarf wählen Sie die Edition **Community Server** und dann die aktuelle stabile Version für *Windows Server 2008 R2 64 Bit und höher* aus. Klicken Sie zum Herunterladen des Installationsprogramms auf **DOWNLOAD (msi)** .
   
    ![MongoDB-Installationsprogramm herunterladen](./media/install-mongodb/download-mongodb.png)
   
    Führen Sie das Installationsprogramm nach Abschluss des Downloads aus.
6. Lesen und akzeptieren Sie die Lizenzbedingungen. Wählen Sie bei entsprechender Aufforderung die Option zum **Abschließen** der Installation.
7. Sie können auch Compass, eine grafische Benutzeroberfläche für MongoDB, installieren.
8. Klicken Sie auf dem letzten Bildschirm auf **Installieren**.

## <a name="configure-the-vm-and-mongodb"></a>Konfigurieren des virtuellen Computers und von MongoDB
1. Die Pfadvariablen werden vom MongoDB-Installationsprogramm nicht aktualisiert. Ist der MongoDB-Speicherort `bin` in Ihrer Pfadvariablen nicht enthalten, müssen Sie jedes Mal bei Verwendung einer ausführbaren MongoDB-Datei den vollständigen Pfad angeben. So fügen Sie den Speicherort zur Pfadvariablen hinzu:
   
   * Klicken Sie mit der rechten Maustaste auf das **Startmenü**, und wählen Sie **System**.
   * Klicken Sie auf die Registerkarte **Erweiterte Systemeinstellungen** und dann auf **Umgebungsvariablen**.
   * Wählen Sie unter **Systemvariablen** den Eintrag **Path**, und klicken Sie dann auf **Bearbeiten**.
     
     ![Konfigurieren von PATH-Variablen](./media/install-mongodb/configure-path-variables.png)
     
     Fügen Sie den Pfad zum MongoDB-Ordner `bin` hinzu. MongoDB wird normalerweise unter *C:\Programme\MongoDB* installiert. Überprüfen Sie den Installationspfad auf Ihrem virtuellen Computer. Im folgenden Beispiel wird der standardmäßige Installationsspeicherort von MongoDB der `PATH`-Variablen hinzugefügt:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Achten Sie darauf, dass Sie das vorangestellte Semikolon (`;`) hinzufügen, um anzugeben, dass Sie einen Speicherort zur `PATH`-Variablen hinzufügen.

2. Erstellen Sie auf Ihrem Datenträger Daten- und Protokollverzeichnisse für MongoDB. Wählen Sie im Menü **Start** die Option **Eingabeaufforderung**. Die folgenden Beispiele erstellen die Verzeichnisse auf Laufwerk F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Starten Sie mit dem folgenden Befehl eine MongoDB-Instanz, und passen Sie den Pfad zu Ihren Daten- und Protokollverzeichnissen entsprechend an:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Es kann einige Minuten dauern, bis MongoDB die Journaldateien zugeordnet hat und mit dem Lauschen auf Verbindungen beginnt. Alle Protokollmeldungen werden in die Datei *F:\MongoLogs\mongolog.log* geleitet, sobald der Server `mongod.exe` startet und die Journaldateien zuordnet.
   
   > [!NOTE]
   > Die Eingabeaufforderung bleibt bei dieser Aufgabe, während die MongoDB-Instanz ausgeführt wird. Lassen Sie das Eingabeaufforderungsfenster geöffnet, damit MongoDB weiterhin ausgeführt wird. Oder installieren Sie MongoDB als Dienst, wie im nächsten Schritt beschrieben.

4. Installieren Sie für zusätzliche Stabilität von MongoDB `mongod.exe` als Dienst. Die Erstellung eines Diensts bedeutet, dass Sie nicht bei jeder Nutzung von MongoDB eine Eingabeaufforderung geöffnet lassen müssen. Erstellen Sie den Dienst wie folgt, und passen Sie den Pfad zu Ihren Daten- und Protokollverzeichnissen entsprechend an:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Der vorherige Befehl erstellt den Dienst „MongoDB“ mit der Beschreibung „Mongo DB“. Die folgenden Parameter werden ebenfalls angegeben:
   
   * Die Option `--dbpath` gibt den Ort des Datenverzeichnisses an.
   * Zur Festlegung einer Protokolldatei muss die Option `--logpath` verwendet werden, da der laufende Dienst kein Befehlsfenster zur Anzeige der Ausgabe hat.
   * Mit der Option `--logappend` wird festgelegt, dass ein Neustart des Diensts eine Ausgabe erzeugt, die an die vorhandene Protokolldatei angehängt wird.
   
   Führen Sie zum Starten des MongoDB-Diensts den folgenden Befehl aus:
   
    ```
    net start MongoDB
    ```
   
    Weitere Informationen zum Erstellen des MongoDB-Diensts finden Sie unter [Configure a Windows Service for MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service) (Konfigurieren eines Windows-Diensts für MongoDB).

## <a name="test-the-mongodb-instance"></a>Testen der MongoDB-Instanz
Wenn MongoDB als einzelne Instanz ausgeführt wird oder als Dienst installiert wurde, können Sie mit dem Erstellen und Verwenden von Datenbanken beginnen. Starten Sie die MongoDB-Administrator-Shell, indem Sie im Menü **Start** ein weiteres Eingabeaufforderungsfenster öffnen und folgenden Befehl eingeben:

```
mongo  
```

Sie können die Datenbanken mit dem Befehl `db` auflisten. Fügen Sie wie folgt einige Daten ein:

```
db.foo.insert( { a : 1 } )
```

Suchen Sie wie folgt nach Daten:

```
db.foo.find()
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Beenden Sie die `mongo`-Konsole wie folgt:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurieren von Firewall- und Netzwerksicherheitsgruppen-Regeln
Nach der Installation und Ausführung von MongoDB öffnen Sie einen Port in der Windows-Firewall, um eine Remoteverbindung mit MongoDB herzustellen. Wenn Sie eine neue eingehende Regel erstellen möchten, um TCP-Port 27017 zuzulassen, öffnen Sie eine administrative PowerShell-Eingabeaufforderung, und geben Sie den folgenden Befehl ein:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Sie können die Regel auch mithilfe des grafischen Verwaltungstools **Windows-Firewall mit erweiterter Sicherheit** erstellen. Erstellen Sie eine neue eingehende Regel zum Zulassen von TCP-Port 27017.

Erstellen Sie ggf. eine Netzwerksicherheitsgruppen-Regel, um den Zugriff auf MongoDB von außerhalb des vorhandenen Subnetzes Ihres virtuellen Azure-Netzwerks zuzulassen. Sie können die Netzwerksicherheitsgruppen-Regeln im [Azure-Portal](nsg-quickstart-portal.md) oder mit [Azure PowerShell](nsg-quickstart-powershell.md) erstellen. Lassen Sie wie bei den Windows-Firewallregeln TCP-Port 27017 für die virtuelle Netzwerkschnittstelle des virtuellen MongoDB-Computers zu.

> [!NOTE]
> TCP-Port 27017 wird in MongoDB als Standardport verwendet. Sie können diesen Port mithilfe des `--port`-Parameters ändern, wenn sie `mongod.exe` manuell oder über einen Dienst starten. Aktualisieren Sie bei Änderung des Ports unbedingt die Windows-Firewall- und die Netzwerksicherheitsgruppen-Regeln in den vorhergehenden Schritten.


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie erfahren, wie Sie MongoDB auf dem virtuellen Windows-Computer installieren und konfigurieren. Sie können nun auf Ihrem virtuellen Windows-Computer auf MongoDB zugreifen. Lesen Sie dazu die Informationen in den weiterführenden Themen in der [MongoDB-Dokumentation](https://docs.mongodb.com/manual/).

