---
title: 'Schnellstart: Erstellen einer Data Science Virtual Machine-Instanz für Ubuntu'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurieren und erstellen Sie eine Data Science Virtual Machine für Linux (Ubuntu), um Analysen und Machine Learning-Vorgänge durchzuführen.
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: quickstart
ms.date: 03/10/2020
ms.openlocfilehash: b25d9846f3bb678c182fa22d824f7b4171d2d803
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515630"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Schnellstart: Einrichten von Data Science Virtual Machine für Linux (Ubuntu)

Schnelle Betriebsbereitschaft mit einer Data Science Virtual Machine-Instanz unter Ubuntu 18.04

## <a name="prerequisites"></a>Voraussetzungen

Zum Erstellen einer Data Science VM-Instanz unter Ubuntu 18.04 benötigen Sie ein Azure-Abonnement. [Testen Sie Azure kostenlos](https://azure.com/free).

>[!NOTE]
>Kostenlose Azure-Konten unterstützen keine GPU-fähigen VM-SKUs.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Erstellen Ihrer Data Science Virtual Machine für Linux

Mit den folgenden Schritten erstellen Sie eine Instanz der Data Science Virtual Machine unter Ubuntu 18.04:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Falls Sie sich noch nicht bei Ihrem Azure-Konto angemeldet haben, erhalten Sie ggf. eine entsprechende Aufforderung.
1. Suchen Sie nach der Liste mit den virtuellen Computern, indem Sie „Data Science Virtual Machine“ eingeben und „Data Science Virtual Machine – Ubuntu 18.04“ auswählen.

1. Wählen Sie im nächsten Fenster **Erstellen** aus.

1. Sie sollten auf das Blatt „Virtuellen Computer erstellen“ umgeleitet werden.
   
1. Geben Sie die folgenden Informationen ein, um die einzelnen Schritte des Assistenten zu konfigurieren:

    1. **Grundlagen**:
    
       * **Abonnement**: Falls Sie über mehrere Abonnements verfügen, müssen Sie das Abonnement auswählen, unter dem der Computer erstellt und abgerechnet werden soll. Sie müssen für dieses Abonnement über Berechtigungen zum Erstellen von Ressourcen verfügen.
       * **Ressourcengruppe**: Erstellen Sie eine neue Gruppe, oder verwenden Sie eine bereits vorhandene.
       * **Name des virtuellen Computers**: Geben Sie den Namen des virtuellen Computers ein. Dieser Name wird im Azure-Portal verwendet.
       * **Region**: Wählen Sie das am besten geeignete Rechenzentrum aus. Den schnellsten Netzwerkzugriff erreichen Sie mit dem Datencenter, in dem der größte Teil Ihrer Daten gespeichert ist oder das Ihrem physischen Standort am nächsten ist. Informieren Sie sich über die [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Image**: Behalten Sie den Standardwert bei.
       * **Size**: Diese Option sollte automatisch mit einer Größe ausgefüllt werden, die für allgemeine Workloads geeignet ist. Lesen Sie mehr zu [Linux-VM-Größen in Azure](../../virtual-machines/sizes.md).
       * **Authentifizierungstyp:** Wählen Sie für eine schnellere Einrichtung die Option „Kennwort“ aus. 
         
         > [!NOTE]
         > Wenn Sie JupyterHub verwenden möchten, achten Sie darauf, dass „Kennwort“ ausgewählt ist, da JupyterHub *nicht* für die Verwendung von öffentlichen SSH-Schlüsseln konfiguriert ist.

       * **Benutzername**: Geben Sie den Benutzernamen des Administrators ein. Sie verwenden diesen Benutzernamen, um sich bei Ihrem virtuellen Computer anzumelden. Dieser Benutzername braucht nicht mit Ihrem Azure-Benutzernamen übereinzustimmen. Verwenden Sie *keine* Großbuchstaben.
         
         > [!IMPORTANT]
         > Wenn Sie in Ihrem Benutzernamen Großbuchstaben verwenden, funktioniert JupyterHub nicht, und die Meldung „500 – Interner Serverfehler“ wird angezeigt.

       * **Kennwort**: Geben Sie das Kennwort ein, das Sie für die Anmeldung bei Ihrem virtuellen Computer verwenden.    
    
   1. Klicken Sie auf **Überprüfen + erstellen**.
   1. **Überprüfen + erstellen**
      * Stellen Sie sicher, dass alle eingegebenen Informationen richtig sind. 
      * Klicken Sie auf **Erstellen**.
    
    Die Bereitstellung sollte ungefähr 5 Minuten dauern. Der Status wird im Azure-Portal angezeigt.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Zugreifen auf die Data Science VM-Instanz unter Ubuntu

Es gibt drei Methoden für den Zugriff auf die DSVM-Instanz unter Ubuntu:

  * SSH für Terminalsitzungen
  * X2Go für grafische Sitzungen
  * JupyterHub und JupyterLab für Jupyter-Notebooks

### <a name="ssh"></a>SSH

Wenn Sie Ihren virtuellen Computer mit der SSH-Authentifizierung konfiguriert haben, können Sie sich mit den Kontoanmeldeinformationen anmelden, die Sie im Abschnitt **Grundlagen** von Schritt 3 für die textshell-Schnittstelle erstellt haben. Unter Windows können Sie ein SSH-Clienttool, z. B. [PuTTY](https://www.putty.org), herunterladen. Falls Sie einen grafischen Desktop bevorzugen (X Window System), können Sie die X11-Weiterleitung unter PuTTY verwenden.

> [!NOTE]
> Der X2Go-Client hat bei Tests besser abgeschnitten als die X11-Weiterleitung. Es wird empfohlen, den X2Go-Client als grafische Desktop-Benutzeroberfläche zu nutzen.

### <a name="x2go"></a>X2Go

Die Linux-VM ist schon in der Bereitstellung von X2Go-Server enthalten und zum Akzeptieren von Clientverbindungen bereit. Führen Sie auf dem Client die folgenden Schritte aus, um eine Verbindung mit dem grafischen Desktop des virtuellen Linux-Computers herzustellen:

1. Laden Sie den X2Go-Client für Ihre Clientplattform von [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)herunter, und installieren Sie ihn.
1. Notieren Sie sich die öffentliche IP-Adresse des virtuellen Computers. Diese finden Sie im Azure-Portal, indem Sie den erstellten virtuellen Computer öffnen.

   ![IP-Adresse des Ubuntu-Computers](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Führen Sie den X2Go-Client aus. Wenn das Fenster „Neue Sitzung“ nicht automatisch angezeigt wird, wechseln Sie zu „Sitzung“ > „Neue Sitzung“.

1. Geben Sie im daraufhin angezeigten Konfigurationsfenster die folgenden Konfigurationsparameter ein:
   * **Registerkarte „Sitzung“:**
     * **Host**: Geben Sie die IP-Adresse Ihres virtuellen Computers ein, die Sie zuvor notiert haben.
     * **Anmeldung**: Geben Sie den Benutzernamen für die Linux-VM ein.
     * **SSH-Port**: Übernehmen Sie den Standardwert 22.
     * **Sitzungstyp**: Ändern Sie den Wert in **XFCE**. Derzeit unterstützt die Linux-VM nur den XFCE-Desktop.
   * **Registerkarte „Medien“** : Sie können die Soundunterstützung und die Clientausgabe deaktivieren, wenn Sie diese Funktionen nicht benötigen.
   * **Freigegebene Ordner**: Verwenden Sie diese Registerkarte, um das Verzeichnis auf dem Clientcomputer hinzuzufügen, das Sie auf dem virtuellen Computer einbinden möchten. 

   ![X2go-Konfiguration](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Klicken Sie auf **OK**.
1. Klicken Sie im rechten Bereich des X2Go-Fensters auf das Feld, um den Anmeldebildschirm für Ihren virtuellen Computer anzuzeigen.
1. Geben Sie das Kennwort für Ihren virtuellen Computer ein.
1. Klicken Sie auf **OK**.
1. Möglicherweise müssen Sie X2Go die Berechtigung zum Umgehen Ihrer Firewall erteilen, um den Verbindungsvorgang abzuschließen.
1. Nun sollte die grafische Benutzeroberfläche für Ihre Ubuntu-DSVM-Instanz angezeigt werden. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub und JupyterLab

Die Ubuntu-DSVM führt [JupyterHub](https://github.com/jupyterhub/jupyterhub), einen Jupyter-Server für mehrere Benutzer, aus. Führen Sie die folgenden Schritte aus, um eine Verbindung herzustellen:

   1. Notieren Sie sich die öffentliche IP-Adresse für Ihren virtuellen Computer, indem Sie im Azure-Portal nach ihrem virtuellen Computer suchen und ihn auswählen.
      ![IP-Adresse des Ubuntu-Computers](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. Öffnen Sie auf Ihrem lokalen Computer einen Webbrowser, und navigieren Sie zu „https:\//IP-Ihrer-VM:8000“. Ersetzen Sie dabei „IP-Ihrer-VM“ durch die IP-Adresse, die Sie zuvor notiert haben.
   1. Ihr Browser verhindert wahrscheinlich das direkte Öffnen der Seite, und es wird eine Meldung mit dem Hinweis angezeigt, dass ein Zertifikatfehler vorliegt. Für die DSVM wird die Sicherheit anhand eines selbstsignierten Zertifikats erzielt. Bei den meisten Browsern können Sie nach dieser Warnung weiterklicken. In vielen Browsern wird aber während der gesamten Websitzung eine visuelle Warnung zum Zertifikat angezeigt.

      >[!NOTE]
      > Wenn die Fehlermeldung `ERR_EMPTY_RESPONSE` in Ihrem Browser angezeigt wird, stellen Sie sicher, dass Sie auf den Computer zugreifen, indem Sie explizit das *HTTPS*-Protokoll und nicht *HTTP* oder nur die Webadresse verwenden. Wenn Sie die Webadresse ohne `https://` in die Adresszeile eingeben, wird in den meisten Browsern der Standardwert `http` verwendet und dieser Fehler wird angezeigt.

   1. Geben Sie den Benutzernamen mit dem zugehörigen Kennwort ein, den Sie zum Erstellen des virtuellen Computers verwendet haben, und melden Sie sich an. 

      ![Jupyter-Anmeldung eingeben](./media/dsvm-ubuntu-intro/jupyter-login.png)

      >[!NOTE]
      > Wenn Sie in dieser Phase einen 500-Fehler erhalten, haben Sie wahrscheinlich Großbuchstaben in Ihrem Benutzernamen verwendet. Dies ist eine bekannte Interaktion zwischen Jupyter Hub und dem von ihm verwendeten PAMAuthenticator. Wenn ein Fehler mit dem Hinweis angezeigt wird, dass die Seite nicht erreichbar ist, müssen wahrscheinlich die Berechtigungen der Netzwerksicherheitsgruppe angepasst werden. Navigieren Sie im Azure-Portal zur Netzwerksicherheitsgruppen-Ressource in Ihrer Ressourcengruppe. Wenn Sie über das öffentliche Internet auf JupyterHub zugreifen möchten, muss Port 8000 geöffnet sein. (Die Abbildung zeigt, dass dieser virtuelle Computer für den Just-In-Time-Zugriff konfiguriert ist. Dies wird dringend empfohlen.) Informationen finden Sie unter [Sichern Ihrer Verwaltungsports mit Just-in-Time-Zugriff (JIT)](../../security-center/security-center-just-in-time.md). ![Konfiguration der Netzwerksicherheitsgruppe](./media/dsvm-ubuntu-intro/nsg-permissions.png)

   1. Durchsuchen Sie die zahlreichen verfügbaren Beispielnotebooks.

JupyterLab, die nächste Generation von Jupyter-Notebooks, und JupyterHub, sind ebenfalls verfügbar. Für den Zugriff darauf melden Sie sich bei JupyterHub an, und navigieren Sie dann zu der URL „https:\//IP-Ihrer-VM:8000/Benutzer/Ihr-Benutzername/lab“. Ersetzen Sie dabei „Ihr-Benutzername“ durch den Benutzernamen, den Sie beim Konfigurieren des virtuellen Computers gewählt haben. Es kann wieder sein, dass der erste Zugriff auf die Website aufgrund eines Zertifikatfehlers blockiert wird.

Sie können JupyterLab als Standard-Notebookserver festlegen, indem Sie `/etc/jupyterhub/jupyterhub_config.py` die folgende Zeile hinzufügen:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter:

* In der exemplarischen Vorgehensweise [Data Science mit einer Linux Data Science Virtual Machine in Azure](linux-dsvm-walkthrough.md) wird veranschaulicht, wie Sie mehrere allgemeine Data Science-Aufgaben mit der hier bereitgestellten Linux-DSVM ausführen. 
* Entdecken Sie die unterschiedlichen Data Science-Tools der DSVM-Instanz, indem Sie die in diesem Artikel beschriebenen Tools ausprobieren. Sie können auch `dsvm-more-info` in der Shell auf dem virtuellen Computer ausführen, um eine grundlegende Einführung und Hinweise auf weitere Informationen zu den Tools auf der VM zu erhalten.  
* Erfahren Sie, wie Sie mithilfe des [Team Data Science-Prozesses](../team-data-science-process/index.yml) systematisch Analyselösungen erstellen.
* Besuchen Sie die [Azure AI Gallery](https://gallery.azure.ai/), um Beispiele zu Machine Learning und zur Datenanalyse zu erhalten, in denen Azure-KI-Dienste verwendet werden.
* Gehen Sie anhand der entsprechenden [Referenzdokumentation](./reference-ubuntu-vm.md) für diesen virtuellen Computer vor.