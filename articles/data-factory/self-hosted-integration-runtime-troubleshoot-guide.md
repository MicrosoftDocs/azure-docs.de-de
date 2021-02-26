---
title: Problembehandlung bei der selbstgehosteten Integration Runtime in Azure Data Factory
description: Hier erfahren Sie, wie Sie eine Problembehandlung im Fall von Problemen bei der selbstgehosteten Integration Runtime in Azure Data Factory durchführen können.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: lle
ms.openlocfilehash: 3735d7b21faf62905ed4c06dbef80c9737e7329e
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551075"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Problembehandlung bei der selbstgehosteten Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden allgemeine Methoden zur Behandlung von Problemen mit der selbstgehosteten Integration Runtime (IR) in Azure Data Factory erläutert.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Erfassen von Protokollen der selbstgehosteten IR aus Azure Data Factory

Bei fehlgeschlagenen Aktivitäten in der selbstgehosteten oder freigegebenen Integration Runtime (IR) unterstützt Azure Data Factory das Anzeigen und Hochladen von Fehlerprotokollen. Befolgen Sie die hier erwähnten Anweisungen, um die Fehlerberichts-ID abzurufen, und geben Sie dann die Berichts-ID ein, um nach verwandten bekannten Problemen zu suchen.

1. Wählen Sie in Azure Data Factory **Pipelineausführungen** aus.

1. Wählen Sie unter **Aktivitätsausführungen** in der Spalte **Fehler** die hervorgehobene Schaltfläche aus, um die Aktivitätsprotokolle anzuzeigen (siehe folgender Screenshot):

    ![Screenshot des Abschnitts „Aktivitätsausführungen“ im Bereich „Alle Pipelineausführungen“](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    Die Aktivitätsprotokolle für die fehlgeschlagene Aktivitätsausführung werden angezeigt.

    ![Screenshot mit den Aktivitätsprotokollen für die fehlgeschlagene Aktivität](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. Wählen Sie **Protokolle senden** aus, wenn Sie weitere Hilfe benötigen.
 
   Das Fenster **Protokolle der selbstgehosteten Integration Runtime (IR) für Microsoft freigeben** wird geöffnet.

    ![Screenshot des Fensters „Protokolle der selbstgehosteten Integration Runtime (IR) für Microsoft freigeben“](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Wählen Sie die Protokolle aus, die Sie senden möchten. 
    * Bei einer *selbstgehosteten IR* können Sie Protokolle im Zusammenhang mit einer fehlgeschlagenen Aktivität oder alle Protokolle auf dem Knoten der selbstgehosteten IR hochladen. 
    * Bei einer *freigegebenen IR* können Sie nur Protokolle hochladen, die mit der fehlgeschlagenen Aktivität in Zusammenhang stehen.

1. Notieren Sie sich beim Hochladen der Protokolle die Berichts-ID und bewahren Sie sie für später auf, falls Sie weitere Unterstützung bei der Problemlösung benötigen.

    ![Screenshot des Fensters mit dem Uploadfortschritt und der angezeigten Berichts-ID für die IR-Protokolle](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Die Anforderungen zum Anzeigen und Hochladen von Protokollen werden auf allen selbstgehosteten IR-Instanzen ausgeführt, die online sind. Wenn Protokolle fehlen, stellen Sie sicher, dass alle selbstgehosteten IR-Instanzen online sind. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Allgemeiner Fehler oder Fehler bei der selbstgehosteten IR

### <a name="out-of-memory-issue"></a>Arbeitsspeicherproblem

#### <a name="symptoms"></a>Symptome

Ein Fehler vom Typ „OutOfMemoryException“ (OOM) tritt auf, wenn Sie versuchen, mit einer verknüpften oder selbstgehosteten IR eine Lookup-Aktivität auszuführen.

#### <a name="cause"></a>Ursache

Eine neue Aktivität kann einen OOM-Fehler auslösen, wenn auf dem IR-Computer eine vorübergehende hohe Speicherauslastung auftritt. Das Problem wird möglicherweise durch eine große Anzahl gleichzeitiger Aktivitäten verursacht. Der Fehler ist systembedingt.

#### <a name="resolution"></a>Lösung

Überprüfen Sie die Ressourcennutzung und gleichzeitige Aktivitätsausführung auf dem IR-Knoten. Passen Sie die interne und die Auslösezeit von Aktivitätsausführungen an, um zu viele gleichzeitige Ausführungen auf einem einzelnen IR-Knoten zu vermeiden.

### <a name="concurrent-jobs-limit-issue"></a>Problem beim Limit für gleichzeitige Aufträge

#### <a name="symptoms"></a>Symptome

Wenn Sie versuchen, auf der Azure Data Factory-Benutzeroberfläche den Grenzwert für gleichzeitige Aufträge zu erhöhen, bleibt der Vorgang im Status *Wird aktualisiert* hängen.

Beispielszenario: Der Höchstwert für gleichzeitige Aufträge ist aktuell auf „24“ festgelegt, und Sie möchten die Anzahl erhöhen, damit Aufträge schneller ausgeführt werden können. Der Mindestwert, den Sie eingeben können, ist „3“, und der Höchstwert beträgt „32“. Sie erhöhen den Wert von 24 auf 32 und wählen dann die Schaltfläche **Aktualisieren** aus. Der Prozess bleibt in Status *Wird aktualisiert* hängen, wie aus dem folgenden Screenshot hervorgeht. Sie aktualisieren die Seite, und es wird immer noch der Wert 24 angezeigt. Er wurde nicht wie erwartet auf 32 aktualisiert.

![Screenshot des Bereichs „Knoten“ der Integration Runtime, in dem der Vorgang mit dem Status „Wird aktualisiert“ angezeigt wird](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Ursache

Der Grenzwert für die Anzahl gleichzeitiger Aufträge hängt vom logischen Kern des Computers und vom Arbeitsspeicher ab. Versuchen Sie, den Wert in einen niedrigeren Wert (z. B. 24) zu ändern, und zeigen Sie dann das Ergebnis an.

> [!TIP] 
> - Weitere Informationen zur Anzahl der logischen Kerne und zum Ermitteln der Anzahl der logischen Kerne Ihres Computers finden Sie unter [Vier Möglichkeiten zum Ermitteln der Anzahl der CPU-Kerne unter Windows 10](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Informationen zum Berechnen der Math.log-Funktion finden Sie im [Logarithmusrechner](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>Problem beim SSL-Zertifikat für die Hochverfügbarkeit der selbstgehosteten IR

#### <a name="symptoms"></a>Symptome

Der Arbeitsknoten der selbstgehosteten IR hat den folgenden Fehler gemeldet:

„Fehler beim Abrufen der Freigabezustände vom primären Knoten net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Aktivitäts-ID: XXXXX Fehler beim Erstellen der X.509-Zertifikatskette CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft. Das Zertifikat, das verwendet wurde, besitzt eine Vertrauenswürdigkeitskette, die nicht überprüft werden kann. Ersetzen Sie das Zertifikat, oder ändern Sie certificateValidationMode. Die Sperrfunktion konnte die Sperrung nicht überprüfen, da der Sperrserver offline war.“

#### <a name="cause"></a>Ursache

Bei der Behandlung von Fällen im Zusammenhang mit dem SSL/TLS-Handshake können einige Probleme bei der Überprüfung der Zertifikatskette auftreten. 

#### <a name="resolution"></a>Lösung

- Nachstehend finden Sie eine schnelle und intuitive Möglichkeit zum Beheben von Fehlern beim Erstellen einer X.509-Zertifikatkette:
 
    1. Exportieren Sie das Zertifikat, das überprüft werden muss. Führen Sie hierzu folgende Schritte aus:
    
       a. Wählen Sie in Windows **Start** aus, beginnen Sie mit der Eingabe von **Zertifikate**, und wählen Sie dann **Computerzertifikate verwalten** aus.
       
       b. Suchen Sie im Datei-Explorer im linken Bereich nach dem Zertifikat, das Sie überprüfen möchten. Klicken Sie mit der rechten Maustaste darauf, und wählen Sie dann **Alle Tasks** > **Exportieren** aus.
    
        ![Screenshot des Bereichs zum Verwalten von Computerzertifikaten mit der Steuerung „Alle Tasks“ > „Exportieren“ für ein Zertifikat](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Kopieren Sie das exportierte Zertifikat auf den Clientcomputer. 
    3. Führen Sie clientseitig in einem Eingabeaufforderungsfenster den folgenden Befehl aus: Achten Sie darauf, dass Sie *\<certificate path>* und *\<output txt file path>* durch die tatsächlichen Pfade ersetzen.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Beispiel:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Überprüfen Sie die TXT-Ausgabedatei auf Fehler. Die Fehlerübersicht finden Sie am Ende der TXT-Datei.

        Beispiel: 

        ![Screenshot der Fehlerübersicht am Ende einer TXT-Datei](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Wenn am Ende der Protokolldatei kein Fehler angezeigt wird (siehe folgender Screenshot), können Sie davon ausgehen, dass die Zertifikatkette auf dem Clientcomputer erfolgreich erstellt wurde.
        
        ![Screenshot einer Protokolldatei ohne Fehleranzeige](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Wenn in der Zertifikatdatei die Dateinamenerweiterungen AIA (Authority Information Access), CDP (CRL Distribution Point) oder OCSP (Online Certificate Status Protocol) konfiguriert sind, können Sie eine intuitivere Überprüfung vornehmen:
 
    1. Rufen Sie diese Informationen ab, indem Sie die Zertifikatdetails überprüfen (siehe folgender Screenshot):
    
        ![Screenshot mit den Zertifikatdetails](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. Führen Sie den folgenden Befehl aus. Achten Sie darauf, *\<certificate path>* durch den tatsächlichen Pfad des Zertifikats zu ersetzen.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        Das URL-Abrufprogramm wird geöffnet. 
        
    1. Wählen Sie **Abrufen** aus, um Zertifikate mit den Dateinamenerweiterungen AIA, CDP und OCSP zu überprüfen.

        ![Screenshot des URL-Abrufprogramms mit der Schaltfläche „Abrufen“](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Die Zertifikatkette wurde erfolgreich erstellt, wenn der Zertifikatstatus von AIA *Überprüft* und der Zertifikatstatus von CDP oder OCSP ebenfalls *Überprüft* lautet.

        Wenn beim Abrufen von AIA oder CDP ein Fehler auftritt, wenden Sie sich an das Netzwerkteam, um den Clientcomputer zum Verbinden mit der Ziel-URL einzurichten. Es reicht aus, wenn entweder der HTTP-Pfad oder der LDAP-Pfad (Lightweight Directory Access Protocol) überprüft werden kann.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Selbstgehostete IR konnte Datei oder Assembly nicht laden

#### <a name="symptoms"></a>Symptome

Es wird folgende Fehlermeldung angezeigt:

„Die Datei oder Assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' oder eine Abhängigkeit davon konnte nicht geladen werden. Die angegebene Datei wurde nicht gefunden. Aktivitäts-ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3“
 
Nachstehend eine genauere Fehlermeldung: 

"Die Datei oder Assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' oder eine Abhängigkeit davon konnte nicht geladen werden. Die angegebene Datei wurde nicht gefunden. Aktivitäts-ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3“

#### <a name="cause"></a>Ursache

In Process Monitor können Sie folgendes Ergebnis anzeigen:

[![Screenshot der Pfadliste in Process Monitor](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> In Process Monitor können Sie Filter festlegen (siehe folgender Screenshot).
>
> Die vorherige Fehlermeldung besagt, dass sich die DLL „System.ValueTuple“ weder im zugehörigen Ordner *Globaler Assemblycache* (GAC), noch im Ordner *C:\Programme\Microsoft Integration Runtime\4.0\Gateway* oder im Ordner *C:\Programme\Microsoft Integration Runtime\4.0\Shared* befindet.
>
> Grundsätzlich lädt der Prozess die DLL zuerst aus dem Ordner *GAC*, dann aus dem Ordner *Shared* und schließlich aus dem Ordner *Gateway*. Daher können Sie die DLL aus jedem Pfad laden, der hilfreich ist.

<br>

![Screenshot der Seite „Process Monitor-Filter“ mit den Filtern für die DLL](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Lösung

Die Datei *System.ValueTuple.dll* befindet sich im Pfad *C:\Programme\Microsoft Integration Runtime\4.0\Gateway\DataScan*. Um das Problem zu beheben, kopieren Sie die Datei *System.ValueTuple.dll* in den Ordner *C:\Programme\Microsoft Integration Runtime\4.0\Gateway*.

Mit derselben Methode können Sie andere Probleme mit fehlenden Dateien oder Assemblys beheben.

#### <a name="more-information-about-this-issue"></a>Weitere Informationen zu diesem Problem

Dass die Datei *System.ValueTuple.dll* unter *%windir%\Microsoft.NET\assembly* und *%windir%\assembly* angezeigt wird, liegt in einem .NET-Verhalten begründet. 

Beim folgenden Fehler können Sie deutlich erkennen, dass die Assembly *System.ValueTuple* fehlt. Dieses Problem tritt auf, wenn die Anwendung versucht, die Assembly *System.ValueTuple.dll* zu überprüfen.
 
"\<LogProperties>\<ErrorInfo>[{"Code":0,"Message":"Der Typinitialisierer für 'Npgsql.PoolManager' hat eine Ausnahme ausgelöst.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Datei oder Assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' oder eine Abhängigkeit davon konnte nicht geladen werden. Das System kann die angegebene Datei nicht finden.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]\</ErrorInfo>\</LogProperties>"
 
Weitere Informationen zu GAC finden Sie unter [Globaler Assemblycache](/dotnet/framework/app-domains/gac).


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>Fehlender Authentifizierungsschlüssel für selbstgehostete Integration Runtime

#### <a name="symptoms"></a>Symptome

Die selbstgehostete Integration Runtime wird plötzlich ohne Authentifizierungsschlüssel offline geschaltet, und im Ereignisprotokoll wird die folgende Fehlermeldung angezeigt: 

„Der Authentifizierungsschlüssel wurde noch nicht zugewiesen.“

![Screenshot des Integration Runtime-Ereignisbereichs, in dem anzeigt wird, dass noch kein Authentifizierungsschlüssel zugewiesen wurde](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Ursache

- Der Knoten der selbstgehosteten IR oder die logische selbstgehostete IR im Azure-Portal wurde gelöscht.
- Es wurde eine saubere Deinstallation durchgeführt.

#### <a name="resolution"></a>Lösung

Wenn keiner der vorstehenden Gründe zutrifft, können Sie zum Ordner *%programdata%\Microsoft\Data Transfer\DataManagementGateway* navigieren und überprüfen, ob die Datei *Configurations* gelöscht wurde. Wenn sie gelöscht wurde, befolgen Sie die Anweisungen im Netwrix-Artikel [Wer hat eine Datei von den Windows-Dateiservern gelöscht?](https://www.netwrix.com/how_to_detect_who_deleted_file.html)

![Screenshot des Bereichs mit den Ereignisprotokolldetails zum Überprüfen der Datei „Configurations“](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>Zwei lokale Datenspeicher können mit der selbstgehosteten IR nicht überbrückt werden

#### <a name="symptoms"></a>Symptome

Nachdem Sie selbstgehostete IRs für Quell- und Zieldatenspeicher erstellt haben, möchten Sie die beiden IRs miteinander verbinden, um eine Kopieraktivität abzuschließen. Wenn die Datenspeicher in verschiedenen virtuellen Netzwerken konfiguriert wurden oder den Gatewaymechanismus nicht verstehen können, wird einer der folgenden Fehler ausgegeben: 

* „Der Treiber der Quelle wurde in der Ziel-IR nicht gefunden“
* „Die Ziel-IR kann nicht auf die Quelle zugreifen“
 
#### <a name="cause"></a>Ursache

Die selbstgehostete IR ist als zentraler Knoten für eine Kopieraktivität konzipiert und nicht als Client-Agent, der für jeden Datenspeicher installiert werden muss.
 
In diesem Fall sollte der verknüpfte Dienst für jeden Datenspeicher mit derselben IR erstellt werden, und die IR sollte über das Netzwerk auf beide Datenspeicher zugreifen können. Es spielt keine Rolle, ob die IR im Quell- oder Zieldatenspeicher oder auf einem dritten Computer installiert wird. Wenn zwei verknüpfte Dienste mit unterschiedlichen IRs erstellt, aber in derselben Kopieraktivität verwendet werden, wird die Ziel-IR verwendet, und Sie müssen die Treiber für beide Datenspeicher auf dem Ziel-IR-Computer installieren.

#### <a name="resolution"></a>Lösung

Installieren Sie Treiber für den Quell- und Zieldatenspeicher in der Ziel-IR, und stellen Sie sicher, dass diese auf den Quelldatenspeicher zugreifen kann.
 
Wenn der Datenverkehr das Netzwerk zwischen zwei Datenspeichern (die z. B. in zwei virtuellen Netzwerken konfiguriert wurden) nicht durchlaufen kann, können Sie den Kopiervorgang in einer Aktivität eventuell auch dann nicht beenden, wenn die IR installiert wurde. Wenn Sie den Kopiervorgang in einer einzelnen Aktivität nicht beenden können, können Sie zwei Kopieraktivitäten mit zwei IRs (jeweils in einem VNET) erstellen: 
* Kopieren Sie eine IR aus Datenspeicher 1 in Azure Blob Storage.
* Kopieren Sie eine andere IR aus Azure Blob Storage in Datenspeicher 2. 

Diese Lösung könnte die Anforderung simulieren, die IR zum Erstellen einer Brücke zu verwenden, die zwei getrennte Datenspeicher miteinander verbindet.


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>Synchronisierungsproblem bei Anmeldeinformationen führt zum Verlust von Anmeldeinformationen beim Hochverfügbarkeitsmodus

#### <a name="symptoms"></a>Symptome

Wenn die Anmeldeinformationen „XXXXXXXXXX“ für die Datenquelle aus dem aktuellen Integration Runtime-Knoten mit Nutzlast gelöscht werden, erhalten Sie die folgende Fehlermeldung:

„Wenn Sie den Linkdienst im Azure-Portal gelöscht haben oder der Task die falsche Nutzlast hat, erstellen Sie einen neuen Linkdienst mit Ihren Anmeldinformationen.“

#### <a name="cause"></a>Ursache

Die selbstgehostete IR wird im Hochverfügbarkeitsmodus mit zwei Knoten erstellt, die Anmeldeinformationen auf den Knoten sind jedoch nicht synchronisiert. Dies bedeutet, dass die im Verteilerknoten gespeicherten Anmeldeinformationen nicht mit anderen Workerknoten synchronisiert werden. Wenn ein Failover vom Verteilerknoten zum Workerknoten erfolgt, die Anmeldeinformationen aber nur im bisherigen Verteilerknoten vorhanden sind, schlägt der Task beim Versuch eines Zugriffs auf die Anmeldeinformationen fehl, und der vorstehende Fehler wird angezeigt.

#### <a name="resolution"></a>Lösung

Die einzige Möglichkeit zur Vermeidung dieses Problems besteht darin sicherzustellen, dass die Anmeldeinformationen auf den beiden Knoten stets synchron sind. Wenn sie nicht synchron sind, müssen Sie die Anmeldeinformationen für den neuen Verteiler erneut eingeben.


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>Das Zertifikat kann nicht ausgewählt werden, da der private Schlüssel fehlt

#### <a name="symptoms"></a>Symptome

* Sie haben eine PFX-Datei in den Zertifikatspeicher importiert.
* Wenn Sie das Zertifikat auf der Benutzeroberfläche von Integration Runtime Configuration Manager ausgewählt haben, erhalten Sie die folgende Fehlermeldung:

   „Der Verschlüsselungsmodus für die Intranetkommunikation konnte nicht geändert werden. Wahrscheinlich besitzt das Zertifikat '\<*certificate name*>' keinen privaten Schlüssel, der für den Schlüsselaustausch geeignet ist, oder der Prozess verfügt möglicherweise nicht über Zugriffsrechte für den privaten Schlüssel. Weitere Details finden Sie in der inneren Ausnahme.“

    ![Screenshot des Bereichs mit den Einstellungen des Integration Runtime Configuration Manager mit der Fehlermeldung „Privater Schlüssel fehlt“](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Ursache

- Das Benutzerkonto verfügt über eine niedrige Berechtigungsstufe und kann nicht auf den privaten Schlüssel zugreifen.
- Das Zertifikat wurde als Signatur, nicht als Schlüsselaustausch generiert.

#### <a name="resolution"></a>Lösung

* Verwenden Sie ein Konto mit entsprechenden Berechtigungen für den Zugriff auf den privaten Schlüssel, um die Benutzeroberfläche verwenden zu können.  
* Importieren Sie das Zertifikat mithilfe des folgenden Befehls:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>Einrichten der selbstgehosteten IR

### <a name="integration-runtime-registration-error"></a>Integration Runtime-Registrierungsfehler 

#### <a name="symptoms"></a>Symptome

Möglicherweise möchten Sie gelegentlich aus einem der folgenden Gründe eine selbstgehostete IR in einem anderen Konto ausführen:
- Die Unternehmensrichtlinie lässt das Dienstkonto nicht zu.
- Eine Authentifizierung ist erforderlich.

Nachdem Sie das Dienstkonto im Dienstbereich geändert haben, stellen Sie möglicherweise fest, dass die Integration Runtime nicht mehr funktioniert, und Sie erhalten die folgende Fehlermeldung:

„Bei dem Knoten von Integration Runtime (selbstgehostet) ist bei der Registrierung ein Fehler aufgetreten. Es kann keine Verbindung zum Hostdienst von Integration Runtime (selbstgehostet) hergestellt werden.“

![Screenshot des Fensters "Integration Runtime Configuration Manager", in dem ein IR-Registrierungsfehler angezeigt wird](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Ursache

Viele Ressourcen werden nur dem Dienstkonto gewährt. Wenn Sie das Dienstkonto in ein anderes Konto ändern, bleiben die Berechtigungen für alle abhängigen Ressourcen unverändert erhalten.

#### <a name="resolution"></a>Lösung

Navigieren Sie zum Integration Runtime-Ereignisprotokoll, um den Fehler zu überprüfen.

![Screenshot des IR-Ereignisprotokolls mit dem aufgetretenen Laufzeitfehler](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* Wenn der Fehler im Ereignisprotokoll „UnauthorizedAccessException“ lautet, führen Sie folgende Schritte aus:

    1. Überprüfen Sie im Windows-Dienstbereich das Dienstanmeldekonto *DIAHostService*.

        ![Screenshot des Eigenschaftenbereichs für das Dienstanmeldekonto](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Überprüfen Sie, ob das Dienstanmeldekonto Lese-/Schreibberechtigung für den Ordner *%programdata%\Microsoft\DataTransfer\DataManagementGateway* hat.

        - Standardmäßig sollte das Dienstanmeldekonto Lese-/Schreibberechtigung haben, falls es nicht geändert wurde.

            ![Screenshot des Bereichs mit den Dienstberechtigungen](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - Wenn Sie das Dienstanmeldekonto geändert haben, führen Sie die folgenden Schritte zur Behebung des Problems aus:
 
            a. Führen Sie eine saubere Deinstallation der aktuellen selbstgehosteten IR durch.   
            b. Installieren Sie die Bits der selbstgehosteten IR.  
            c. Ändern Sie das Dienstkonto mit folgenden Schritten:  

             i. Navigieren Sie zum Installationsordner der selbstgehosteten IR, und wechseln Sie dann zum Ordner *Microsoft Integration Runtime\4.0\Shared*.  
             ii. Öffnen Sie mit erhöhten Rechten ein Eingabeaufforderungsfenster. Ersetzen Sie *\<user>* und *\<password>* durch Ihren eigenen Benutzernamen und Ihr Kennwort, und führen Sie dann den folgenden Befehl aus:   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. Wenn Sie zum Konto „LocalSystem“ wechseln möchten, müssen Sie für dieses Konto das richtige Format verwenden: `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                Verwenden Sie *keinesfalls* das Format `dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`   .  
             iv. Da das Konto „LocalSystems“ höhere Berechtigungen als der Administrator hat, können Sie es optional auch direkt in „Dienste“ ändern.  
             v. Sie können einen lokalen Benutzer/Domänenbenutzer für das IR-Dienstanmeldekonto verwenden.            

            d. Registrieren Sie die Integration Runtime.

* Wenn der Fehler „Der Dienst ‚Integration Runtime Service‘ (DIAHostService) konnte nicht gestartet werden. Überprüfen Sie, ob Sie ausreichende Berechtigungen zum Starten von Systemdiensten besitzen“ lautet, gehen Sie wie folgt vor:

    1. Überprüfen Sie im Windows-Dienstbereich das Dienstanmeldekonto *DIAHostService*.
    
        ![Screenshot des Anmeldebereichs für das Dienstkonto](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Überprüfen Sie, ob das Dienstanmeldekonto über die Berechtigung **Als Dienst anmelden** zum Starten des Windows-Diensts verfügt:

        ![Screenshot des Eigenschaftsbereichs „Als Dienst anmelden“](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Weitere Informationen

Wenn in Ihrem Fall keines der beiden oben genannten Lösungsmuster zutrifft, versuchen Sie, die folgenden Windows-Ereignisprotokolle zu sammeln: 
- Anwendungs- und Dienstprotokolle > Integration Runtime
- Windows-Protokolle > Anwendung

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>Schaltfläche „Registrieren“ zum Registrieren einer selbstgehosteten IR wurde nicht gefunden    

#### <a name="symptoms"></a>Symptome

Wenn Sie eine selbstgehostete IR registrieren, wird die Schaltfläche **Registrieren** im Configuration Manager-Bereich nicht angezeigt.

![Screenshot des Configuration Manager-Bereichs, in dem eine Meldung angezeigt wird, dass der Integration Runtime-Knoten nicht registriert wurde](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Ursache

Mit der Veröffentlichung der Integration Runtime 3.0 wurde die Schaltfläche **Registrieren** für vorhandene Integration Runtime-Knoten entfernt, um die Umgebung übersichtlicher und sicherer zu gestalten. Wenn ein Knoten für eine Integration Runtime registriert wurde (ganz gleich, ob online oder nicht), müssen Sie ihn bei einer anderen Integration Runtime registrieren. Dazu müssen Sie den bisherigen Knoten deinstallieren, anschließend neu installieren und dann registrieren.

#### <a name="resolution"></a>Lösung

1. Deinstallieren Sie in der Systemsteuerung die vorhandene Integration Runtime.

    > [!IMPORTANT] 
    > Wählen Sie **Ja** im folgenden Prozess aus. Bewahren Sie beim Deinstallationsvorgang keine Daten auf.

    ![Screenshot der Schaltfläche „Ja“ zum Löschen aller Benutzerdaten aus der Integration Runtime](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Wenn Sie keine MSI-Installationsdatei für die Integration Runtime haben, wechseln Sie zum [Download Center](https://www.microsoft.com/en-sg/download/details.aspx?id=39717), um die aktuelle Integration Runtime herunterzuladen.
1. Installieren Sie die MSI-Datei, und registrieren Sie die Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>Die selbstgehostete IR kann aufgrund von Localhost nicht registriert werden    

#### <a name="symptoms"></a>Symptome

Die selbstgehostete IR kann bei Verwendung von „get_LoopbackIpOrName“ auf einem neuen Computer nicht registriert werden.

**Debug:** Ein Laufzeitfehler ist aufgetreten.
Der Typinitialisierer für „Microsoft.DataTransfer.DIAgentHost.DataSourceCache“ hat eine Ausnahme ausgelöst.
Beim Datenbankaufruf ist ein nicht behebbarer Fehler aufgetreten.
 
**Ausnahmedetail:** System.TypeInitializationException: Der Typinitialisierer für „Microsoft.DataTransfer.DIAgentHost.DataSourceCache“ hat eine Ausnahme ausgelöst. ---> System.Net.Sockets.SocketException: Beim Datenbankaufruf unter „System.Net.Dns.GetAddrInfo(String Name)“ ist ein nicht behebbarer Fehler aufgetreten.

#### <a name="cause"></a>Ursache

Das Problem tritt normalerweise auf, wenn Localhost aufgelöst wird.

#### <a name="resolution"></a>Lösung

Verwenden Sie zum Hosten der Datei und zum Beheben des Problems die Localhost-IP-Adresse 127.0.0.1.

### <a name="self-hosted-setup-failed"></a>Fehler beim selbstgehosteten Setup    

#### <a name="symptoms"></a>Symptome

Sie können weder eine vorhandene IR deinstallieren, noch eine neue IR installieren oder eine vorhandene IR auf eine neue IR aktualisieren.

#### <a name="cause"></a>Ursache

Die Installation der Integration Runtime ist abhängig vom Windows Installer-Dienst. Installationsprobleme treten möglicherweise aus folgenden Gründen auf:
- Es steht nicht genügend Speicherplatz zur Verfügung
- Berechtigungen fehlen
- Der Windows NT-Dienst ist gesperrt
- Die CPU-Auslastung ist zu hoch
- Die MSI-Datei wird in einem langsamen Netzwerkspeicherort gehostet
- Es wurde versehentlich auf einige Systemdateien oder Registrierungen zugegriffen

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>Fehler des IR-Dienstkontos beim Zertifikatzugriff

#### <a name="symptoms"></a>Symptome

Beim Installieren der selbstgehosteten IR über Microsoft Integration Runtime Configuration Manager wird ein Zertifikat mit einer vertrauenswürdigen Zertifizierungsstelle generiert. Das Zertifikat konnte nicht angewendet werden, um die Kommunikation zwischen zwei Knoten zu verschlüsseln, und die folgende Fehlermeldung wird angezeigt: 

„Der Verschlüsselungsmodus für die Intranetkommunikation konnte nicht geändert werden: Fehler beim Gewähren des Zugriffs auf das Zertifikat ‚\<*certificate name*>‘ für das Integration Runtime-Dienstkonto. Fehlercode 103“

![Screenshot mit der Fehlermeldung „... Fehler beim Gewähren des Zugriffs auf das Zertifikat für das Integration Runtime-Dienstkonto“](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Ursache

Das Zertifikat verwendet Speicher von Schlüsselspeicheranbietern (KSP-Speicher), der noch nicht unterstützt wird. Bisher unterstützt die selbstgehostete IR nur Speicher von Kryptografiedienstanbietern (CSP-Speicher).

#### <a name="resolution"></a>Lösung

In diesem Fall wird die Verwendung von CSP-Zertifikaten empfohlen.

**Lösung 1** 

Importieren Sie das Zertifikat mithilfe des folgenden Befehls:

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![Screenshot des Befehls „certutil“ zum Importieren des Zertifikats](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Lösung 2** 

Konvertieren Sie das Zertifikat mit den folgenden Befehlen:

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

Vor und nach der Konvertierung:

![Screenshot des Ergebnisses vor der Zertifikatskonvertierung](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Screenshot des Ergebnisses nach der Zertifikatskonvertierung](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Selbstgehostete Integration Runtime, Version 5.x
Für das Upgrade auf Version 5.x der selbstgehosteten Integration Runtime von Azure Data Factory wird mindestens **.NET Framework Runtime 4.7.2** oder höher benötigt. Auf der Downloadseite befinden sich Downloadlinks für die aktuelle 4.x-Version und die beiden neuen 5.x-Versionen. 

Für Kunden von Azure Data Factory v2 gilt Folgendes:
- Wenn die automatische Aktualisierung aktiviert ist und Sie bereits ein Upgrade auf .NET Framework Runtime 4.7.2 oder höher durchgeführt haben, erfolgt automatisch ein Upgrade der selbstgehosteten Integration Runtime auf die neueste Version 5.x.
- Wenn die automatische Aktualisierung aktiviert ist und Sie noch kein Upgrade auf .NET Framework Runtime 4.7.2 oder höher durchgeführt haben, erfolgt kein automatisches Upgrade der selbstgehosteten Integration Runtime auf die neueste Version 5.x. Die aktuelle Version 4.x bleibt für die selbstgehostete Integration Runtime unverändert erhalten. Im Portal und im Client der selbstgehosteten Integration Runtime wird eine Warnung zum Upgrade von .NET Framework Runtime angezeigt.
- Wenn die automatische Aktualisierung deaktiviert ist und Sie bereits ein Upgrade auf .NET Framework Runtime 4.7.2 oder höher durchgeführt haben, können Sie die neueste Version 5.x manuell herunterladen und auf Ihrem Computer installieren.
- Wenn die automatische Aktualisierung deaktiviert ist und Sie noch kein Upgrade auf .NET Framework Runtime 4.7.2 oder höher durchgeführt haben, passiert Folgendes: Wenn Sie versuchen, Version 5.x der selbstgehosteten Integration Runtime manuell zu installieren und den Schlüssel zu registrieren, werden Sie aufgefordert, zuerst ein Upgrade für Ihre .NET Framework Runtime-Version durchführen.


Für Kunden von Azure Data Factory v1 gilt Folgendes:
- Version 5.x der selbstgehosteten Integration Runtime bietet keine Unterstützung für Azure Data Factory v1.
- Es wird ein automatisches Upgrade der selbstgehosteten Integration Runtime auf die aktuelle Version 4.x durchgeführt. Und für die aktuelle Version 4.x ist kein Ablaufdatum festgelegt. 
- Wenn Sie versuchen, Version 5.x der selbstgehosteten Integration Runtime manuell zu installieren und den Schlüssel zu registrieren, werden Sie darüber informiert, dass Version 5.x der selbstgehosteten Integration Runtime Azure Data Factory v1 nicht unterstützt.


## <a name="self-hosted-ir-connectivity-issues"></a>Konnektivitätsprobleme bei der selbstgehosteten Integration Runtime

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>Die selbstgehostete Integration Runtime kann keine Verbindung zum Clouddienst herstellen

#### <a name="symptoms"></a>Symptome

Wenn Sie versuchen, die selbstgehostete Integration Runtime zu registrieren, wird in Configuration Manager die folgende Fehlermeldung angezeigt:

„Bei dem Knoten von Integration Runtime (selbstgehostet) ist bei der Registrierung ein Fehler aufgetreten.“

![Screenshot der Nachricht „Bei dem Knoten von Integration Runtime (selbstgehostet) ist bei der Registrierung ein Fehler aufgetreten“](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Ursache 

Die selbstgehostete Integration Runtime kann keine Verbindung zum Azure Data Factory-Dienst (Back-End) herstellen. Dieses Problem wird in der Regel durch Netzwerkeinstellungen in der Firewall verursacht.

#### <a name="resolution"></a>Lösung

1. Überprüfen Sie, ob der Integration Runtime-Dienst ausgeführt wird. Wenn ja, fahren Sie mit Schritt 2 fort.
    
   ![Screenshot mit der Anzeige, dass der selbstgehostete IR-Dienst ausgeführt wird](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Wenn kein Proxy für die selbstgehostete Integration Runtime konfiguriert ist (Standardeinstellung), führen Sie den folgenden PowerShell-Befehl auf dem Computer aus, auf dem die selbstgehostete Integration Runtime installiert ist:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > Die Dienst-URL kann je nach Speicherort Ihrer Azure Data Factory-Instanz variieren. Sie finden die Dienst-URL unter **ADF-Benutzeroberfläche** > **Verbindungen** > **Integration Runtimes** > **Selbstgehostete IR bearbeiten** > **Knoten** > **Dienst-URLs anzeigen**.
            
    Die folgende Antwort wird erwartet:
            
    ![Screenshot der Antwort auf den PowerShell-Befehl](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Wenn Sie nicht die erwartete Antwort erhalten, verwenden Sie je nach Bedarf eine der folgenden Methoden:
            
    * Wenn Sie die Meldung erhalten, dass der Remotename nicht aufgelöst werden konnte, gibt es ein Problem mit dem Domain Name System (DNS). Wenden Sie sich an das Netzwerkteam, um dieses Problem zu beheben.
    * Wenn Sie eine Meldung erhalten, dass das SSL/TLS-Zertifikat nicht vertrauenswürdig ist, [überprüfen Sie das Zertifikat](https://wu2.frontend.clouddatahub.net/), ob es auf dem Computer als vertrauenswürdig gilt, und installieren Sie dann mithilfe des Zertifikat-Managers das öffentliche Zertifikat. Durch diese Aktion sollte das Problem behoben werden.
    * Navigieren Sie zu **Windows** > **Ereignisanzeige (Protokolle)**  > **Anwendungs- und Dienstprotokolle** > **Integration Runtime**, und prüfen Sie, ob Fehler auftreten, die durch das DNS, eine Firewallregel oder Unternehmensnetzwerkeinstellungen verursacht werden. Bei einem derartigen Fehler feststellen, müssen Sie das Beenden der Verbindung erzwingen. Da jedes Unternehmen über seine eigenen angepassten Netzwerkeinstellungen verfügt, wenden Sie sich an das Netzwerkteam, um diese Probleme zu beheben.

1. Wenn „Proxy“ in der selbstgehosteten Integration Runtime konfiguriert wurde, überprüfen Sie, ob Ihr Proxyserver auf den Dienstendpunkt zugreifen kann. Ein Beispiel für einen Befehl finden Sie unter [PowerShell, Webanforderungen und Proxys](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Die folgende Antwort wird erwartet:
            
![Screenshot der erwarteten Antwort auf den Powershell-Befehl](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxy-Aspekte:
> * Überprüfen Sie, ob der Proxyserver in die Liste der sicheren Empfänger aufgenommen werden muss. Stellen Sie in diesem Fall sicher, dass [diese Domänen](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) in der Liste der sicheren Empfänger enthalten sind.
> * Überprüfen Sie, ob das SSL/TLS-Zertifikat „wu2.frontend.clouddatahub.net/“ auf dem Proxyserver als vertrauenswürdig gilt.
> * Wenn Sie im Proxy die Active Directory-Authentifizierung verwenden, ändern Sie das Dienstkonto in das Benutzerkonto, das als „Integration Runtime-Dienst“ auf den Proxy zugreifen kann.

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>Fehlermeldung: „Knoten der selbstgehosteten Integration Runtime/logischen selbstgehosteten Integration Runtime befindet sich im Status ‚Inaktiv/Wird ausgeführt (eingeschränkt)‘.“

#### <a name="cause"></a>Ursache 

Der Knoten der selbstgehosteten Integrated Runtime weist möglicherweise den Status **Inaktiv** auf, wie im folgenden Screenshot gezeigt:

![Screenshot des Knotens der selbstgehosteten Integrated Runtime mit inaktivem Status](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Dieses Verhalten tritt auf, wenn Knoten nicht miteinander kommunizieren können.

#### <a name="resolution"></a>Lösung

1. Melden Sie sich bei dem virtuellen Computer an, der auf dem Knoten gehostet wird. Öffnen Sie unter **Anwendungs- und Dienstprotokolle** > **Integration Runtime** die Ereignisanzeige und filtern Sie die Fehlerprotokolle.

1. Überprüfen Sie, ob das Fehlerprotokoll den folgenden Fehler enthält: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Wenn dieser Fehler angezeigt wird, führen Sie in einem Eingabeaufforderungsfenster den folgenden Befehl aus: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Wenn der im folgenden Screenshot gezeigte Befehlszeilenfehler „Verbindung zum Host konnte nicht geöffnet werden“ ausgegeben wird, wenden Sie sich an Ihre IT-Abteilung, um Hilfe bei der Behebung dieses Problems zu erhalten. Wenden Sie sich nach erfolgreicher Telnet-Verwendung an den Microsoft-Support, wenn Sie weiterhin Probleme mit dem Status des Integrated Runtime-Knotens haben.
        
   ![Screenshot mit dem Befehlszeilenfehler „Verbindung zum Host konnte nicht geöffnet werden“](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Überprüfen Sie, ob das Fehlerprotokoll den folgenden Eintrag enthält:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Probieren Sie zur Lösung des Problems eine folgenden Methoden (oder beide) aus:
    - Platzieren Sie alle Knoten in derselben Domäne.
    - Fügen Sie die IP-Adresse zur Hostzuordnung in allen Hostdateien des gehosteten virtuellen Computers hinzu.

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>Konnektivitätsprobleme zwischen der selbstgehosteten IR und Ihrer Azure Data Factory-Instanz oder der selbstgehosteten IR und der Datenquelle oder Senke

Um ein Problem mit der Netzwerkkonnektivität zu beheben, müssen Sie wissen, wie Sie die Netzwerkablaufverfolgung erfassen und verwenden und die [Microsoft Network Monitor (Netmon)-Ablaufverfolgung analysieren](#analyze-the-netmon-trace), bevor Sie die Netmon-Tools in realen Fällen von der selbstgehosteten IR anwenden.

#### <a name="symptoms"></a>Symptome

Möglicherweise müssen Sie gelegentlich bestimmte Konnektivitätsprobleme zwischen der selbstgehosteten IR und Ihrer Azure Data Factory-Instanz (siehe folgender Screenshot) oder zwischen der selbstgehosteten IR und der Datenquelle oder Senke beheben. 

![Screenshot der Meldung „Fehler bei der Verarbeitung der HTTP-Anforderung“](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

In beiden Fällen können möglicherweise folgende Fehler auftreten:

* „Fehler beim Kopieren: Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Kann keine Verbindung mit SQL Server herstellen: ‚IP-Adresse‘“

* „Es ist mindestens ein Fehler aufgetreten. Fehler beim Senden der Anforderung. Die zugrunde liegende Verbindung wurde geschlossen: Unerwarteter Fehler beim Empfangen. Es können keine Daten aus der Transportverbindung gelesen werden: An existing connection was forcibly closed by the remote host. Eine vorhandene Verbindung wurde erzwungenermaßen von der Aktivitäts-ID des Remotehosts geschlossen.“

#### <a name="resolution"></a>Lösung

Wenn die zuvor genannten Fehler auftreten, befolgen Sie die Anweisungen in diesem Abschnitt, um die Probleme zu beheben.

- Erfassen Sie für die Analyse eine Netmon-Ablaufverfolgung: 

    1. Sie können den Filter so festlegen, dass eine Zurücksetzung vom Server zur Clientseite angezeigt wird. Im folgenden Beispielscreenshot sehen Sie, dass der Azure Data Factory-Server die Serverseite darstellt.

        ![Screenshot des Azure Data Factory-Servers](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. Wenn Sie das Zurücksetzungspaket erhalten, finden Sie die Konversation anhand des folgenden TCP-Protokolls (Transmission Control Protocol).

        ![Screenshot der TCP-Konversation](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. Rufen Sie die Konversation zwischen Client und Azure Data Factory-Server ab, indem Sie den Filter entfernen.

        ![Screenshot der Konversationsdetails](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- Eine Analyse der erfassten Netmon-Ablaufverfolgung zeigt, dass die Gültigkeitsdauer (Time to Live, TTL) 64 beträgt. Anhand der im Artikel [Grundlegendes zur IP-Gültigkeitsdauer (Time to Live, TTL) und zur maximalen Anzahl an Weiterleitungen](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) erwähnten Werte (extrahiert in die folgende Liste) können Sie sehen, dass das Linux-System dafür verantwortlich ist, dass das Paket zurückgesetzt und die Verbindung getrennt wird.

    Die Standardwerte für die Gültigkeitsdauer und die maximale Hopanzahl variieren je nach Betriebssystem, wie aus der folgenden Auflistung hervorgeht:
    - Linux-Kernel 2.4 (etwa 2001): 255 für TCP, User Datagram Protocol (UDP) und Internet Control Message Protocol (ICMP)
    - Linux-Kernel 4.10 (2015): 64 für TCP, UDP und ICMP
    - Windows XP (2001): 128 für TCP, UDP und ICMP
    - Windows 10 (2015): 128 für TCP, UDP und ICMP
    - Windows Server 2008: 128 für TCP, UDP und ICMP
    - Windows Server 2019 (2018): 128 für TCP, UDP und ICMP
    - macOS (2001): 64 für TCP, UDP und ICMP

    ![Screenshot: TTL-Wert = 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Im vorherigen Beispiel wird der TTL-Wert 61 anstatt 64 als Gültigkeitsdauer angezeigt, da das Netzwerkpaket, wenn es sein Ziel erreicht, verschiedene Hops (z. B. Router oder Netzwerkgeräte) durchlaufen muss. Die Anzahl der Router oder Netzwerkgeräte wird abgezogen, um den TTL-Wert zu bilden.
    
    In diesem Fall können Sie sehen, dass eine Zurücksetzung vom Linux-System mit TTL 64 gesendet werden kann.

-  Überprüfen Sie den vierten Hop der selbstgehosteten IR, um zu bestätigen, woher das zurücksetzende Gerät stammt.
 
    *Netzwerkpaket von Linux-System A mit TTL 64 -> B TTL 64 minus 1 = 63 -> C TTL 63 minus 1 = 62 -> TTL 62 minus 1 = 61 selbstgehostete IR*

- In einer idealen Situation würde die TTL-Hopanzahl 128 lauten. Das heißt, dass das Windows-Betriebssystem Ihre Azure Data Factory-Instanz ausführt. *128 minus 107 = 21 Hops*, wie im folgenden Beispiel gezeigt. Dies bedeutet, dass beim TCP-3-Wege-Handshake 21 Hops für das Paket von der Azure Data Factory-Instanz an die selbstgehostete IR gesendet wurden.
 
    ![Screenshot: TTL-Wert = 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Daher müssen Sie sich an das Netzwerkteam wenden, um zu den vierten Hop der selbstgehosteten IR zu ermitteln. Wenn es sich um die Firewall handelt (wie beim Linux-System), überprüfen Sie alle Protokolle, um herauszufinden, warum dieses Gerät das Paket nach dem TCP-3-Wege-Handshake zurücksetzt. 
    
    Wenn Sie nicht sicher sind, wo Sie suchen müssen, versuchen Sie, die Netmon-Ablaufverfolgung der selbstgehosteten IR und der Firewall während des problematischen Zeitraums abzurufen. Mit diesem Ansatz können Sie herausfinden, welches Gerät das Paket möglicherweise zurückgesetzt und die Trennung der Verbindung verursacht hat. In diesem Fall müssen Sie sich auch an Ihr Netzwerkteam wenden, um fortzufahren.

### <a name="analyze-the-netmon-trace"></a>Analysieren der Netmon-Ablaufverfolgung

> [!NOTE] 
> Die folgenden Anweisungen gelten für die Netmon-Ablaufverfolgung. Da die Netmon-Ablaufverfolgung derzeit nicht unterstützt wird, können Sie Wireshark für diesen Zweck verwenden.

Wenn Sie versuchen, Telnet **8.8.8.8 888** mit der erfassten Netmon-Ablaufverfolgung zu verwenden, sollte die Ablaufverfolgung wie in den folgenden Screenshots angezeigt werden:

![Screenshot mit der Fehlermeldung „Verbindung zum Host an Port 888 konnte nicht geöffnet werden“](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Screenshot mit Beschreibung der Netmon-Ablaufverfolgung](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Die vorherigen Abbildungen zeigen, dass Sie an Port **888** keine TCP-Verbindung zu **8.8.8.8** auf Server-Seite herstellen konnten. Daher werden dort zwei zusätzliche **SynReTransmit**-Pakete angezeigt. Da die Quelle **SELF-HOST2** mit dem ersten Paket keine Verbindung zu **8.8.8.8** herstellen konnte, versucht sie weiterhin, die Verbindung herzustellen.

> [!TIP]
> Probieren Sie die folgende Lösung aus, um diese Verbindung herzustellen:
> 1. Wählen Sie **Filter laden** > **Standardfilter** > **Adressen** > **IPv4-Adressen** aus.
> 1. Geben Sie **IPv4.Address == 8.8.8.8** ein, und wählen Sie dann **Anwenden** aus, um den Filter anzuwenden. Dann sollte die Kommunikation zwischen dem lokalen Computer und dem Ziel **8.8.8.8** angezeigt werden.

![Screenshot: Adressfilter](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Screenshot: Weitere Adressfilter](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Erfolgreiche Szenarien sind in den folgenden Beispielen dargestellt: 

- Wenn Sie mit Telnet **8.8.8.8 53** ohne Probleme erreichen können, gibt es einen erfolgreichen TCP-3-Wege-Handshake, und die Sitzung wird mit einem TCP-4-Wege-Handshake beendet.

    ![Screenshot eines erfolgreichen Verbindungsszenarios](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![Screenshot mit Details eines erfolgreichen Verbindungsszenarios](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Der vorherige TCP-3-Wege-Handshake erzeugt den folgenden Workflow:

    ![Diagramm eines Workflows mit TCP-3-Wege-Handshake](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- Der TCP-4-Wege-Handshake zum Beenden der Sitzung wird in den folgenden Workflows veranschaulicht:

    ![Screenshot mit Details des TCP-4-Wege-Handshakes](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Diagramm eines Workflows mit TCP-4-Wege-Handshake](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>E-Mail-Benachrichtigung von Microsoft zum Aktualisieren Ihrer Netzwerkkonfiguration

Möglicherweise erhalten Sie die folgende E-Mail-Benachrichtigung, in der eine Aktualisierung Ihrer Netzwerkkonfiguration empfohlen wird, um die Kommunikation mit neuen IP-Adressen für Azure Data Factory zum 8. November 2020 zuzulassen:

   ![Screenshot der E-Mail-Benachrichtigung von Microsoft mit der Aufforderung zum Aktualisieren der Netzwerkkonfiguration](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>Sind Sie von dieser Benachrichtigung betroffen?

Diese Benachrichtigung gilt für die folgenden Szenarios:

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>Szenario 1: Ausgehende Kommunikation von einer selbstgehosteten Integration Runtime, die lokal hinter einer Unternehmensfirewall ausgeführt wird

So bestimmen Sie, ob Sie betroffen sind:

- *Sie sind nicht betroffen*, wenn Sie Firewallregeln anhand von vollqualifizierten Domänennamen (FQDNs) definieren und den unter [Einrichten einer Firewallkonfiguration und Positivliste für IP-Adressen](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-addresses) beschriebenen Ansatz verwenden.

- *Sie sind betroffen*, wenn Sie die Positivliste für ausgehende IP-Adressen in Ihrer Unternehmensfirewall ausdrücklich aktivieren.

   Wenn Sie betroffen sind, gehen Sie wie folgt vor: Benachrichtigen Sie bis zum 8. November 2020 das für die Netzwerkinfrastruktur zuständige Team, damit Ihre Netzwerkkonfiguration aktualisiert und die neuesten IP-Adressen von Azure Data Factory verwendet werden. Navigieren Sie zum Herunterladen der aktuellen IP-Adressen zu [Ermitteln von Diensttags mit herunterladbaren JSON-Dateien](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>Szenario 2: Ausgehende Kommunikation von einer selbstgehosteten Integration Runtime, die auf einem virtuellen Azure-Computer innerhalb eines vom Kunden verwalteten virtuellen Azure-Netzwerks ausgeführt wird

So bestimmen Sie, ob Sie betroffen sind:

- Überprüfen Sie, ob Sie in einem privaten Netzwerk, das die selbstgehostete Integration Runtime enthält, NSG-Regeln (Netzwerksicherheitsgruppen) für den ausgehenden Datenverkehr definiert haben. Wenn keine Ausgangseinschränkungen vorliegen, sind Sie nicht betroffen.

- Wenn Einschränkungen durch Ausgangsregeln bestehen, prüfen Sie, ob Sie Diensttags verwenden. Wenn Sie Diensttags verwenden, sind Sie nicht betroffen. Sie brauchen nichts zu ändern oder hinzuzufügen, da der neue IP-Adressbereich in den Bereich der vorhandenen Diensttags fällt. 

  ![Screenshot einer Zielüberprüfung mit DataFactory als Ziel](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- *Sie sind betroffen*, wenn Sie die Positivliste für ausgehende IP-Adressen in den Einstellungen für die NSG-Regeln im virtuellen Azure-Netzwerk ausdrücklich aktivieren.

   Wenn Sie betroffen sind, gehen Sie wie folgt vor: Benachrichtigen Sie bis zum 8. November 2020 das für die Netzwerkinfrastruktur zuständige Team, damit die NSG-Regeln in der Konfiguration Ihres virtuellen Azure-Netzwerks aktualisiert und die neuesten IP-Adressen von Azure Data Factory verwendet werden. Wechseln Sie zum Herunterladen der aktuellen IP-Adressen zu [Ermitteln von Diensttags mit herunterladbaren JSON-Dateien](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>Szenario 3: Ausgehende Kommunikation von einer SSIS Integration Runtime, die in einem vom Kunden verwalteten virtuellen Azure-Netzwerk ausgeführt wird

So bestimmen Sie, ob Sie betroffen sind:

- Überprüfen Sie, ob Sie in einem privaten Netzwerk, das die SQL Server Integration Services (SSIS) Integration Runtime enthält, NSG-Regeln (Netzwerksicherheitsgruppen) für den ausgehenden Datenverkehr definiert haben. Wenn keine Ausgangseinschränkungen vorliegen, sind Sie nicht betroffen.

- Wenn Einschränkungen durch Ausgangsregeln bestehen, prüfen Sie, ob Sie Diensttags verwenden. Wenn Sie Diensttags verwenden, sind Sie nicht betroffen. Sie brauchen nichts zu ändern oder hinzuzufügen, da der neue IP-Adressbereich in den Bereich der vorhandenen Diensttags fällt.

- *Sie sind betroffen*, wenn Sie die Positivliste für ausgehende IP-Adressen in den Einstellungen für die NSG-Regeln im virtuellen Azure-Netzwerk ausdrücklich aktivieren.

  Wenn Sie betroffen sind, gehen Sie wie folgt vor: Benachrichtigen Sie bis zum 8. November 2020 das für die Netzwerkinfrastruktur zuständige Team, damit die NSG-Regeln in der Konfiguration Ihres virtuellen Azure-Netzwerks aktualisiert und die neuesten IP-Adressen von Azure Data Factory verwendet werden. Wechseln Sie zum Herunterladen der aktuellen IP-Adressen zu [Ermitteln von Diensttags mit herunterladbaren JSON-Dateien](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>Es konnte keine Vertrauensstellung für den sicheren SSL/TLS-Kanal eingerichtet werden 

#### <a name="symptoms"></a>Symptome

Die selbstgehostete Integration Runtime konnte keine Verbindung zum Azure Data Factory-Dienst herstellen.

Wenn Sie das Ereignisprotokoll der selbstgehosteten IR oder die Clientbenachrichtigungsprotokolle in der Tabelle „CustomLogEvent“ überprüfen, finden Sie die folgende Fehlermeldung:

„Die zugrunde liegende Verbindung wurde geschlossen: Es konnte keine Vertrauensstellung für den sicheren SSL/TLS-Kanal eingerichtet werden. System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure." (System.Security.Authentication.AuthenticationException: Das Remotezertifikat ist laut Validierungsverfahren ungültig.)

Am einfachsten überprüfen Sie das Zertifikat des Azure Data Factory-Diensts, indem Sie die URL des Azure Data Factory-Diensts in Ihren Browser eingeben und den Dienst öffnen. Öffnen Sie beispielsweise auf dem Computer, auf dem die selbstgehostete IR installiert ist, den Link [Serverzertifikat überprüfen](https://eu.frontend.clouddatahub.net/), und zeigen Sie dann die Informationen zum Serverzertifikat an.

  ![Screenshot des Bereichs „Serverzertifikat überprüfen“ des Azure Data Factory-Diensts](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Screenshot des Fensters zum Überprüfen des Serverzertifizierungspfads](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Ursache

Für dieses Problem gibt es zwei mögliche Ursachen:

- Ursache 1: Auf dem Computer, auf dem die selbstgehostete Integration Runtime installiert ist, wird die Stammzertifizierungsstelle des Serverzertifikats des Azure Data Factory-Diensts nicht als vertrauenswürdig eingestuft. 
- Ursache 2: Sie verwenden einen Proxy in Ihrer Umgebung. Das Serverzertifikat des Azure Data Factory-Diensts wird durch den Proxy ersetzt, und das ersetzte Serverzertifikat wird auf dem Computer, auf dem die selbstgehostete Integration Runtime installiert ist, nicht als vertrauenswürdig eingestuft.

#### <a name="resolution"></a>Lösung

- Für Ursache 1: Stellen Sie sicher, dass das Azure Data Factory-Serverzertifikat und die zugehörige Zertifikatkette auf dem Computer, auf dem die selbstgehostete Integration Runtime installiert ist, als vertrauenswürdig gelten.
- Für Ursache 2: Stellen Sie entweder auf dem Computer mit der selbstgehosteten Integration Runtime eine Vertrauensstellung zur ersetzten Stammzertifizierungsstelle her, oder konfigurieren Sie den Proxy so, dass das Azure Data Factory-Serverzertifikat nicht ersetzt wird.

Weitere Informationen zum Herstellen einer Vertrauensstellung von Zertifikaten unter Windows finden Sie unter [Installieren des vertrauenswürdigen Stammzertifikats](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate).

#### <a name="additional-information"></a>Zusätzliche Informationen
Wir haben eine neues, von DigiCert signiertes SSL-Zertifikat eingeführt. Überprüfen Sie, ob sich DigiCert Global Root G2 unter den vertrauenswürdigen Stammzertifizierungsstellen befindet.

  ![Screenshot des Ordners „DigiCert Global Root G2“ im Verzeichnis „Vertrauenswürdige Stammzertifizierungsstellen“.](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Wenn sich das Zertifikat nicht unter den vertrauenswürdigen Stammzertifizierungsstellen befindet, können Sie es [hier herunterladen](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 


## <a name="self-hosted-ir-sharing"></a>Freigabe der selbstgehosteten Integration Runtime

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Die Freigabe der selbstgehosteten Integration Runtime von einem anderen Mandanten wird nicht unterstützt. 

#### <a name="symptoms"></a>Symptome

Beim Versuch, die selbstgehostete Integration Runtime über die Azure Data Factory-Benutzeroberfläche übergreifend für Data Factorys in anderen Mandanten freizugeben, werden Ihnen eventuell andere Data Factorys (in verschiedenen Mandanten) angezeigt, und der Versuch schlägt fehl.

#### <a name="cause"></a>Ursache

Die selbstgehostete Integration Runtime kann nicht mandantenübergreifend freigegeben werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Hilfe zur Problembehandlung finden Sie in den folgenden Ressourcen:

*  [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory-Funktionsanfragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
*  [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Anleitung zur Leistung der Mapping Data Flow-Funktion](concepts-data-flow-performance.md)
