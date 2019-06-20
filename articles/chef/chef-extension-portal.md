---
title: Installieren des Chef-Clients aus dem Azure-Portal
description: Informationen zum Bereitstellen und Konfigurieren des Chef-Clients aus dem Azure-Portal
keywords: Azure, Chef, Devops, Client, Installation, Portal
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: cf7afb50006fb273b4d685f9e4259be1cb60fe4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60563842"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Installieren des Chef-Clients aus dem Azure-Portal
Im Azure-Portal können Sie einem Linux- oder Windows-Computer direkt die Chef-Clienterweiterung hinzufügen. Dieser Artikel führt Sie mithilfe eines neuen virtuellen Linux-Computers durch den Prozess.

## <a name="prerequisites"></a>Voraussetzungen

- **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

- **Chef**: Wenn Sie nicht über ein aktives Chef-Konto verfügen, melden Sie sich für eine [kostenlose Testversion von Hosted Chef](https://manage.chef.io/signup) an. Um die Anweisungen in diesem Artikel zu befolgen, benötigen Sie die folgenden Werte aus Ihrem Chef-Konto:
  - organization_validation-Schlüssel
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Installieren der Chef-Erweiterung auf einen neuen virtuellen Linux-Computer
In diesem Abschnitt verwenden Sie zuerst das Azure-Portal, um eine Linux-VM zu erstellen. Während des Prozesses sehen Sie auch, wie die Chef-Erweiterung auf dem neuen virtuellen Computer installiert wird.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Wählen Sie im Menü auf der linken Seite die Option **Virtuelle Computer** aus. Wenn die Option **Virtuelle Computer** nicht vorhanden ist, wählen Sie **Alle Dienste** und dann **Virtuelle Computer** aus.

1. Wählen Sie auf der Registerkarte **Virtuelle Computer** die Option **Hinzufügen** aus.

    ![Hinzufügen eines neuen virtuellen Computers im Azure-Portal](./media/chef-extension-portal/add-vm.png)

1. Wählen Sie auf der Registerkarte **Compute** das gewünschte Betriebssystem aus. Für diese Demo ist **Ubuntu Server** ausgewählt.

1. Wählen Sie auf der Registerkarte **Ubuntu Server** die Option **Ubuntu Server 16.04 LTS** aus.

    ![Wenn Sie eine Ubuntu-VM erstellen, geben Sie die benötigte Version an.](./media/chef-extension-portal/ubuntu-server-version.png)

1. Wählen Sie auf der Registerkarte **Ubuntu Server 16.04 LTS** die Option **Erstellen** aus.

    ![Ubuntu bietet zusätzliche Informationen zu dem Produkt.](./media/chef-extension-portal/create-vm.png)

1. Wählen Sie auf der Registerkarte **Virtuellen Computer erstellen** die Option **Grundlagen** aus.

1. Geben Sie auf der Registerkarte **Grundlagen** die folgenden Werte an, und wählen Sie dann **OK** aus.

   - **Name**: Geben Sie einen Namen für den neuen virtuellen Computer ein.
   - **VM-Datenträgertyp**: Geben Sie entweder **SSD** oder **HDD** für den Speicherdatenträgertyp ein. Weitere Informationen zu Datenträgertypen für virtuelle Computer in Azure finden Sie im Artikel [Auswählen eines Datenträgertyps](../virtual-machines/windows/disks-types.md).
   - **Benutzername**: Geben Sie einen Benutzernamen ein, dem Administratorrechte auf dem virtuellen Computer erteilt werden.
   - **Authentifizierungstyp**: Wählen Sie **Kennwort** aus. Sie können auch **Öffentlicher SSH-Schlüssel** auswählen und einen Wert für einen öffentlichen SSH-Schlüssel eingeben. Für die Zwecke dieser Demo (und in den Screenshots) ist **Kennwort** ausgewählt.
   - **Kennwort** und **Kennwort bestätigen**: Geben Sie ein Kennwort für den Benutzer ein.
   - **Anmelden mit Azure Active Directory**: Wählen Sie **Deaktiviert** aus.
   - **Abonnement**: Wenn Sie über mehrere Abonnements verfügen, wählen Sie das gewünschte Abonnement aus.
   - **Ressourcengruppe**: Geben Sie einen Namen für Ihre Ressourcengruppe ein.
   - **Standort**: Wählen Sie **USA, Osten** aus.

     ![Registerkarte „Grundlagen“ zum Erstellen eines virtuellen Computers](./media/chef-extension-portal/add-vm-basics.png)

1. Wählen Sie auf der Registerkarte **Größe wählen** eine Größe für den virtuellen Computer und dann **Auswählen** aus.

1. Auf der Registerkarte **Einstellungen** werden die meisten Werte für Sie auf Basis der Werte aufgefüllt, die Sie auf vorherigen Registerkarten ausgewählt haben. Wählen Sie **Erweiterungen**.

     ![Erweiterungen werden virtuellen Computern über die Registerkarte „Einstellungen“ hinzugefügt](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Wählen Sie auf der Registerkarte **Erweiterungen** die Option **Erweiterung hinzufügen** aus.

     ![„Erweiterung hinzufügen“ auswählen, um einem virtuellen Computer eine Erweiterung hinzuzufügen](./media/chef-extension-portal/add-vm-add-extension.png)

1. Wählen Sie auf der Registerkarte **Neue Ressource** die Option **Linux Chef-Erweiterung (1.2.3)** aus.

     ![Chef bietet Erweiterungen für virtuelle Linux- und Windows-Computer](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Wählen Sie auf der Registerkarte **Linux Chef-Erweiterung** die Option **Erstellen** aus.

1. Geben Sie auf der Registerkarte **Erweiterung installieren** die folgenden Werte an, und wählen Sie dann **OK** aus.

    - **URL des Chef-Servers:** Geben Sie die URL des Chef-Servers ein, die den Namen der Organisation enthält, z.B. *https://api.chef.io/organization/mycompany* .
    - **Chef-Knotenname**: Geben Sie den Namen des Chef-Knotens ein. Dies kann ein beliebiger Wert sein.
    - **Ausführungsliste**: Geben Sie die Chef-Ausführungsliste ein, die dem Computer hinzugefügt wird. Dieser Wert kann leer bleiben.
    - **Name des Überprüfungsclients**: Geben Sie den Namen des Chef-Überprüfungsclients ein. Beispiel: *Tarcher-Validierungssteuerelement*.
    - **Validierungsschlüssel**: Wählen Sie eine Datei aus, die den Validierungsschlüssel enthält, der beim Bootstrapping Ihrer Computer verwendet wird.
    - **Clientkonfigurationsdatei**: Wählen Sie eine Konfigurationsdatei für den Chef-Client aus. Dieser Wert kann leer bleiben.
    - **Chef-Clientversion**: Geben Sie die Version des zu installierenden Chef-Clients ein. Dieser Wert kann leer bleiben. Bleibt der Wert leer, wird die aktuelle Version installiert.
    - **SSL-Überprüfungsmodus**: Wählen Sie entweder **Keine** oder **Peer**. Für die Demo wurde *Keine* ausgewählt.
    - **Chef-Umgebung**: Geben Sie die Chef-Umgebung ein, in der dieser Knoten ein Mitglied sein sollte. Dieser Wert kann leer bleiben.
    - **Verschlüsseltes Databag-Geheimnis**: Wählen Sie eine Datei aus, die das Geheimnis für die verschlüsselte Databag enthält, auf die dieser Computer Zugriff haben sollte. Dieser Wert kann leer bleiben.
    - **Chef-Server-SSL-Zertifikat**: Wählen Sie das SSL-Zertifikat aus, das Ihrer Chef-Server-Instanz zugewiesen ist. Dieser Wert kann leer bleiben.

      ![Installieren des Chef-Servers auf einem virtuellen Linux-Computer](./media/chef-extension-portal/install-extension.png)

1. Wählen Sie nach Rückkehr zur Registerkarte **Erweiterungen** die Option **OK** aus.

1. Wählen Sie nach Rückkehr zur Registerkarte **Einstellungen** die Option **OK** aus.

1. Lesen Sie nach Rückkehr zur Registerkarte **Erstellen** (die eine Zusammenfassung der von Ihnen ausgewählten und eingegebenen Optionen darstellt) sowohl die Informationen als auch die **Nutzungsbedingungen**, und wählen Sie **Erstellen** aus.

Wenn der Prozess zum Erstellen und Bereitstellen der VM mit der Chef-Erweiterung abgeschlossen ist, zeigt eine Benachrichtigung an, ob der Vorgang erfolgreich verlaufen ist. Darüber hinaus wird die Ressourcenseite für die neue VM automatisch im Azure-Portal geöffnet, sobald sie erstellt ist.

![Installieren des Chef-Servers auf einem virtuellen Linux-Computer](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines virtuellen Windows-Computers in Azure mithilfe von Chef](/azure/virtual-machines/windows/chef-automation)
