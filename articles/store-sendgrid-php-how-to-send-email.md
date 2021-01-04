---
title: Verwenden des E-Mail-Diensts SendGrid (PHP) | Microsoft Docs
description: Erfahren Sie, wie Sie E-Mails mit dem SendGrid-E-Mail-Dienst in Azure senden. Die Codebeispiele wurden in PHP geschrieben.
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: b3a9fee09d1eac6fb4d716af83c348cb2c21f7a9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023787"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Verwenden des E-Mail-Diensts SendGrid aus PHP

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in PHP geschrieben.
Beschrieben werden die Szenarien **Erstellen einer E-Mail**, **Senden einer E-Mail** und **Hinzufügen von Anlagen**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Was ist der SendGrid-E-Mail-Dienst?
SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

* Automatisches Versenden von Empfangsbestätigungen an Kunden
* Verwalten von Verteilungslisten zum Senden von monatlichen e-Flyern und speziellen Angeboten an Kunden
* Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails oder Kundenreaktionsfähigkeit
* Generieren von Berichten zur Identifizierung von Trends
* Weiterleiten von Kundenanfragen
* E-Mail-Benachrichtigungen von Ihrer Anwendung aus

Weitere Informationen finden Sie unter [https://sendgrid.com][https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Erstellen eines SendGrid-Kontos

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Verwenden von SendGrid aus der PHP-Anwendung

Die Verwendung von SendGrid in Azure-PHP-Anwendungen erfordert keine besondere Konfiguration oder Programmierung. Da es sich bei SendGrid um einen Dienst handelt, kann auf ihn von einer Cloudanwendung auf dieselbe Weise zugegriffen werden wie von einer lokalen Anwendung.

## <a name="how-to-send-an-email"></a>Senden von E-Mails

E-Mails können Sie entweder über SMTP oder über die von SendGrid bereitgestellte Web-API senden.

### <a name="smtp-api"></a>SMTP-API

Wenn Sie eine E-Mail über die SendGrid-SMTP-API senden möchten, verwenden Sie *Swift Mailer*– eine komponentenbasierte Bibliothek zum Senden von E-Mails aus PHP-Anwendungen. Sie können die [Swift Mailer-Bibliothek](https://swiftmailer.symfony.com/) v5.3.0 herunterladen (verwenden Sie [Composer], um Swift Mailer zu installieren). Das Senden von E-Mails mithilfe der Bibliothek umfasst das Erstellen von Instanzen der Klassen `Swift\_SmtpTransport`, `Swift\_Mailer` und `Swift\_Message`, das Festlegen der entsprechenden Eigenschaften und das Aufrufen der Methode `Swift\_Mailer::send`.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
 $html = "<html>
       <head></head>
       <body>
           <p>Hi!<br>
               How are you?<br>
           </p>
       </body>
       </html>";
 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');
 // Email recipients
 $to = array(
       'john@contoso.com'=>'Destination 1 Name',
       'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
     // This will let us know how many users received this message
     echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
     echo "Something went wrong - ";
     print_r($failures);
 }
```

### <a name="web-api"></a>Web-API
Um E-Mails über die SendGrid-Web-API zu versenden, verwenden Sie die [Curl -Funktion][curl function] von PHP.

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $params = array(
      'api_user' => $user,
      'api_key' => $pass,
      'to' => 'john@contoso.com',
      'subject' => 'testing from curl',
      'html' => 'testing body',
      'text' => 'testing body',
      'from' => 'anna@contoso.com',
   );

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

Die Web-API von SendGrid ist einer REST-API sehr ähnlich, obwohl es sich nicht wirklich um eine RESTful-API handelt, da in den meisten Aufrufen die Verben GET und POST austauschbar sind.

## <a name="how-to-add-an-attachment"></a>Hinzufügen von Anlagen

### <a name="smtp-api"></a>SMTP-API

Das Senden einer Anlage mithilfe der SMTP-API umfasst eine zusätzliche Codezeile im Beispielskript zum Senden einer E-Mail mit Swift Mailer.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
  $html = "<html>
      <head></head>
      <body>
         <p>Hi!<br>
            How are you?<br>
         </p>
      </body>
      </html>";

 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');

 // Email recipients
 $to = array(
      'john@contoso.com'=>'Destination 1 Name',
      'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
      // This will let us know how many users received this message
      echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
      echo "Something went wrong - ";
      print_r($failures);
 }
```

Die zusätzliche Codezeile lautet wie folgt:

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

Diese Codezeile ruft die Attach-Methode für das `Swift\_Message`-Objekt auf und verwendet die statische Methode `fromPath` für die `Swift\_Attachment`-Klasse, um eine Datei abzurufen und einer Nachricht anzuhängen.

### <a name="web-api"></a>Web-API

Das Senden einer Anlage mithilfe der Web-API ähnelt dem Senden einer E-Mail mithilfe der Web-API. Beachten Sie jedoch, dass im folgenden Beispiel das Parameterarray das folgende Element enthalten muss:

```php
    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
```

#### <a name="example"></a>Beispiel

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $fileName = 'myfile';
 $filePath = dirname(__FILE__);

 $params = array(
     'api_user' => $user,
     'api_key' => $pass,
     'to' =>'john@contoso.com',
     'subject' => 'test of file sends',
     'html' => '<p> the HTML </p>',
     'text' => 'the plain text',
     'from' => 'anna@contoso.com',
     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
 );

 print_r($params);

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von *Filtern*. Hierbei handelt es sich um Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um spezifische Funktionen wie etwa Aktivierung von Klickprotokollierung, Google Analytics, Abonnement-Tracking usw. zu ermöglichen.

Filter können mithilfe der Eigenschaft filters auf eine Nachricht angewendet werden. Jeder Filter wird durch einen Hash mit filterspezifischen Einstellungen festgelegt. Im folgenden Beispiel wird der Filter für Fußzeilen aktiviert und eine Textnachricht angegeben, die an das Ende der E-Mail angefügt wird. In diesem Beispiel wird die [sendgrid-php-Bibliothek]verwendet.

Verwenden Sie [Composer] , um die Bibliothek zu installieren:

```bash
php composer.phar require sendgrid/sendgrid 2.1.1
```

### <a name="example"></a>Beispiel  

```php
<?php
 /*
  * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
  */
 include "vendor/autoload.php";

 $email = new SendGrid\Email();
 // The list of addresses this message will be sent to
 // [This list is used for sending multiple emails using just ONE request to SendGrid]
 $toList = array('john@contoso.com', 'anna@contoso.com');

 // Specify the names of the recipients
 $nameList = array('Name 1', 'Name 2');

 // Used as an example of variable substitution
 $timeList = array('4 PM', '5 PM');

 // Set all of the above variables
 $email->setTos($toList);
 $email->addSubstitution('-name-', $nameList);
 $email->addSubstitution('-time-', $timeList);

 // Specify that this is an initial contact message
 $email->addCategory("initial");

 // You can optionally setup individual filters here, in this example, we have
 // enabled the footer filter
 $email->addFilter('footer', 'enable', 1);
 $email->addFilter('footer', "text/plain", "Thank you for your business");
 $email->addFilter('footer', "text/html", "Thank you for your business");

 // The subject of your email
 $subject = 'Example SendGrid Email';

 // Where is this message coming from. For example, this message can be from
 // support@yourcompany.com, info@yourcompany.com
 $from = 'someone@example.com';

 // If you do not specify a sender list above, you can specify the user here. If
 // a sender list IS specified above, this email address becomes irrelevant.
 $to = 'john@contoso.com';

 # Create the body of the message (a plain-text and an HTML version).
 # text is your plain-text email
 # html is your html version of the email
 # if the receiver is able to view html emails then only the html
 # email will be displayed

 /*
  * Note the variable substitution here =)
  */
 $text = "
 Hello -name-,
 Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
 Regards,
 Fred";

 $html = "
 <html>
 <head></head>
 <body>
 <p>Hello -name-,<br>
 Thank you for your interest in our products. We have set up an appointment
 to call you at -time- EST to discuss your needs in more detail.

 Regards,

 Fred<br>
 </p>
 </body>
 </html>";

 // set subject
 $email->setSubject($subject);

 // attach the body of the email
 $email->setFrom($from);
 $email->setHtml($html);
 $email->addTo($to);
 $email->setText($text);

 // Your SendGrid account credentials
 $username = 'sendgridusername@yourdomain.com';
 $password = 'example';

 // Create SendGrid object
 $sendgrid = new SendGrid($username, $password);

 // send message
 $response = $sendgrid->send($email);

 print_r($response);
 ```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen des E-Mail-Dienstes SendGrid vertraut sind, finden Sie unter diesen Links weitere Informationen.

* SendGrid-Dokumentation: <https://sendgrid.com/docs>
* SendGrid-PHP-Bibliothek: <https://github.com/sendgrid/sendgrid-php>
* Spezielles SendGrid-Angebot für Azure-Kunden: <https://sendgrid.com/windowsazure.html>

Weitere Informationen finden Sie außerdem im [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[cloudbasierter E-Mail-Dienst]: https://sendgrid.com/email-solutions
[transaktionale E-Mail-Übermittlung]: https://sendgrid.com/transactional-email
[sendgrid-php-Bibliothek]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Composer]: https://getcomposer.org/download/
