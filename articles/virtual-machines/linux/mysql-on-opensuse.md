---
title: Installieren von MySQL auf einem virtuellen OpenSUSE-Computer in Azure
description: Erfahren Sie, wie Sie MySQL auf einem virtuellen openSUSE Linux-Computer in Azure installieren.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 7fbdeda4cce737a6220d42642473e5f86ef8e525
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96295960"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installieren von MySQL auf einem virtuellen Computer mit OpenSUSE Linux in Azure

[MySQL](https://www.mysql.com) ist eine beliebte Open Source-SQL-Datenbank. In diesem Tutorial erfahren Sie, wie Sie einen virtuellen Computer mit OpenSUSE Linux erstellen und anschließend MySQL installieren.


Wenn Sie die CLI lokal installieren und verwenden möchten, benötigen Sie die Azure CLI-Version 2.0 oder höher. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Erstellen eines virtuellen Computers mit OpenSUSE Linux

Erstellen Sie zunächst eine Ressourcengruppe. In diesem Beispiel heißt die Ressourcengruppe *mySQSUSEResourceGroup* und wird in der Region *USA, Osten* erstellt.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Erstellen Sie den virtuellen Computer. In diesem Beispiel heißt die VM [myVM](../sizes.md) und weist die VM-Größe *Standard_D2s_v3* auf, Sie sollten jedoch die *VM-Größe* auswählen, von der Sie glauben, dass sie sich für Ihre Workload am besten eignet.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Sie müssen der Netzwerksicherheitsgruppe auch eine Regel hinzufügen, um Datenverkehr über Port 3306 für MySQL zuzulassen.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

Zum Herstellen einer Verbindung mit dem virtuellen Computer verwenden Sie SSH. In diesem Beispiel lautet die öffentliche IP-Adresse der VM *10.111.112.113*. Sie können die IP-Adresse in der Ausgabe sehen, wenn Sie die VM erstellen.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Aktualisieren der VM
 
Nachdem Sie eine Verbindung mit der VM hergestellt haben, installieren Sie Systemupdates und Patches. 
   
```bash
sudo zypper update
```

Folgen Sie den Anweisungen, um Ihre VM zu aktualisieren.

## <a name="install-mysql"></a>Installieren von MySQL 


Installieren Sie MySQL über SSH auf der VM. Führen Sie bei Aufforderungen die erforderlichen Aktionen aus.

```bash
sudo zypper install mysql
```
 
Legen Sie fest, dass MySQL beim Start des Systems gestartet wird. 

```bash
sudo systemctl enable mysql
```
Überprüfen Sie, ob MySQL aktiviert ist.

```bash
systemctl is-enabled mysql
```

Die Ausgabe sollte „enabled“ lauten.

Starten Sie den Server neu.

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL-Kennwort

Nach der Installation ist das MySQL-Stammkennwort standardmäßig leer. Führen Sie das Skript **mysql\_secure\_installation** aus, um MySQL zu sichern. Sie werden vom Skript aufgefordert, das MySQL-Stammkennwort zu ändern, anonyme Benutzerkonten zu entfernen, Remote-Stammanmeldeinformationen zu deaktivieren, Testdatenbanken zu entfernen und die Berechtigungstabelle erneut zu laden. 

Stellen Sie nach dem Neustart des Servers erneut eine SSH-Verbindung mit der VM her.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Anmelden bei MySQL

Sie können sich jetzt anmelden und die MySQL-Eingabeaufforderung öffnen.

```bash  
sudo mysql -u root -p
```
Damit werden Sie zur MySQL-Eingabeaufforderung weitergeleitet, in der Sie SQL-Anweisungen für die Interaktion mit der Datenbank eingeben können.

Erstellen Sie jetzt einen neuen MySQL-Benutzer.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Das Semikolon (;) am Ende der Zeile ist entscheidend für die Beendigung des Befehls.


## <a name="create-a-database"></a>Erstellen einer Datenbank


Erstellen Sie eine Datenbank, und gewähren Sie die Benutzerberechtigungen für `mysqluser`.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Benutzernamen und Kennwörter der Datenbank werden nur von Skripts verwendet, die eine Verbindung mit der Datenbank herstellen.  Benutzernamen für Datenbankkonten sind nicht notwendigerweise tatsächliche Benutzerkonten im System.

Ermöglichen Sie die Anmeldung von einem anderen Computer aus. In diesem Beispiel ist *10.112.113.114* die IP-Adresse des Computers, von dem aus die Anmeldung zugelassen werden soll.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Geben Sie Folgendes ein, um das Verwaltungsdienstprogramm für die MySQL-Datenbank zu beenden:

```    
quit
```


## <a name="next-steps"></a>Nächste Schritte
Details zu MySQL finden Sie in der [MySQL-Dokumentation](https://dev.mysql.com/doc).
