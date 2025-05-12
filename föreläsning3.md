förläsning 3 Express

#HTTP och MIME-types
*HTTP-grunder: Genomgång av Hypertext Transfer Protocol samt relevanta begrepp som HTTP-metoder (exempelvis GET, POST) och HTTP-svarskoder.
*MIME-typer: Förståelse för hur olika filtyper identifieras och hanteras via MIME (Multipurpose Internet Mail Extensions).
*Länkar till MDN-dokumentation används som referens för att fördjupa sig i dessa områden.

#kodExmpel 1  
¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤
'use strict';
const http = require('http');
const fs = require('fs');

  
  http.createServer(function (req, res) {

    

    if(req.url === '/') {
      res.writeHead(200, {'Content-Type': 'text/html'});
      res.write(fs.readFileSync('index.html').toString());
      res.end();
    }
    else {
      let fileToSend;
      let url = req.url;
      console.log('url:', url);

      url = url.substring(1);
      console.log('filename: ', url);

      fs.stat(url, function(err, stats) {
        if(err) {
          res.writeHead(404, {'Content-Type': 'text/html'});
          res.end();
        }
        else {

          let lastDot = url.lastIndexOf('.'); 
          console.log('Sista punkten finns på postion: ', lastDot);
          let fileExt = url.substring(lastDot + 1);
          console.log('filändelsen är: ', fileExt);

          switch(fileExt) {

            case 'png':
              res.writeHead(200, {'Content-Type': 'image/png'}); //png
              fileToSend = fs.readFileSync(url); //Binär
              break;
              
            default: 
              res.writeHead(200, {'Content-Type': 'text/html'}); //html
              fileToSend = fs.readFileSync(url).toString(); //Text
              break;

          }

          res.write(fileToSend);
          res.end();
        }
      
      });
    }


    
  }).listen(3000);

  Skapa servern: Med http.createServer() skapas en server som hanterar inkommande HTTP-förfrågningar via en callback-funktion. Servern lyssnar på port 3000.

Hantera förfrågningar till rot-URL:

Om den inkommande URL:en är exakt '/' sätts HTTP-status till 200 (OK) och Content-Type headern till 'text/html'.

Sedan läses filen index.html synkront (med fs.readFileSync), konverteras till en textsträng och skickas som svar.

Svaret avslutas med res.end().

Hantera andra förfrågningar:

Om URL:en inte är rot ('/') antas att den begär en specifik fil. Först tas den inledande snedstrecket (/) bort med substring(1) så att den faktiska filvägen får framträda.

Programmet loggar den begärda URL:en och filnamnet för att hjälpa vid felsökning.

Kontrollera om filen finns:

Med fs.stat() (en asynkron metod) kontrolleras om filen faktiskt existerar i filsystemet.

Om ett fel uppstår (t.ex. om filen inte finns) skickas ett svar med HTTP-status 404 (Not Found) och svaret avslutas.

Bestämma filtyp och innehåll:

Om filen finns, letar koden upp den sista punkten (.) i filnamnet för att extrahera filändelsen.

Beroende på filändelsen används en switch-sats:

För .png-filer: HTTP-status sätts till 200 med Content-Type 'image/png'. Filen läses in synkront i binärt läge.

För andra filändelser (standard): HTTP-status sätts till 200 med Content-Type 'text/html', och filen läses in synkront som en textsträng.

Skicka svar:

Den inlästa filens innehåll skrivs ut med res.write(), varefter svaret avslutas med res.end().
¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤


#Express och tredjepartsmoduler
Express: Introduktion till Express som ett Node.js-ramverk för att bygga robusta webb- och mobilapplikationer. Eftersom Express är en tredjepartsmodul måste den installeras (till exempel med npm install express).

Ytterligare moduler: Exempel på andra användbara moduler som jsdom och cookie-parser ges, vilka visar på hur man kan utöka funktionaliteten i sina applikationer.

MEAN Stacken: En kort introduktion till MEAN, där MongoDB, Express, Angular och Node.js ingår, visar på en modern arkitektur för webbutveckling.

Postman för API-utveckling

Verktyget Postman: En genomgång av hur man använder Postman för att designa, testa och övervaka API-anrop.

Praktiska instruktioner ges för att skapa nya HTTP-anrop (GET/POST) mot en lokal server (exempelvis localhost:3000), samt hur man skickar med parametrar eller body-data.
###########################################################################################################################
const express = require('express');

let app = express();

app.listen(3000, function() {
    console.log('Japp servern är igång på port 3000!');
});

app.get('/', function(request, response) {

    console.log( request.method, request.url);

    response.sendFile(__dirname + '/static/html/index.html', function( err ) {
        //felmeddelande till klienten...
    });

});

app.use('/diverse', express.static(__dirname + '/static'));
app.use(express.urlencoded( { extended : true } ));

app.post('/', function(request, response) {

    console.log(request.body, request.body.courseCode, request.body.btnSend);


});

Serverinstans och lyssning

Först importeras Express och en applikationsinstans skapas med const express = require('express'); och let app = express();.

app.listen(3000, ...) startar servern på port 3000, och när servern är igång skrivs ett meddelande ut i konsolen.

GET-route för rot-URL ("/")

När någon anropar rot-adressen ("/") loggas anropsmetoden och URL:en (t.ex. GET /) i konsolen för felsökning.

Med response.sendFile(...) skickas filen index.html som ligger i mappen static/html tillbaka som svar till klienten.

Det finns en callbackfunktion i sendFile där man kan hantera eventuella fel om filen inte kunde skickas korrekt.

Middleware för att servera statiska filer

app.use('/diverse', express.static(__dirname + '/static')); konfigurerar servern att hantera alla anrop med prefixet /diverse genom att servera filer direkt från mappen static.

Detta gör att filer som ligger i den katalogen (t.ex. bilder, CSS-filer eller JavaScript-filer) blir direkt åtkomliga via URL:er som börjar med /diverse.

Middleware för att hantera formulärdata

Med app.use(express.urlencoded({ extended: true })); ställs en middleware in för att parsa URL-enkodade data i inkommande POST-förfrågningar.

Detta är användbart när man skickar formulärdata (t.ex. från ett HTML-formulär) så att den blir tillgänglig som ett objekt i request.body.

POST-route för rot-URL ("/")

När servern tar emot en POST-förfrågan till roten anropas en callback-funktion där innehållet från request.body skrivs ut i konsolen.

Specifikt loggas hela requestbodyn samt fälten courseCode och btnSend om de finns.

Denna route är tydligt avsedd för att hantera formulärsubmission eller data som skickas via POST från klienten.
¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤¤

Middleware i Express

Vad är middleware? Middleware definieras som en mekanism för att kapsla in funktionalitet som opererar på inkommande HTTP-begäran innan den når den slutgiltiga hanteraren.

Implementering: I Express används metoden .use() för att applicera middleware på applikationen. Egna middleware-funktioner tar ofta emot de två parametrarna request och response.

Detta segment belyser hur man kan strukturera och organisera sin applikationslogik med hjälp av middleware.

Lärandemål enligt kursplanen

Kursplan 1: Efter kursens slut förväntas studenterna kunna producera Node.js-program, förklara hur Node.js-tillämpningar exekveras, samt skapa och hantera HTTP-klienter och servrar.

Kursplan 2: Studenterna ska dessutom lära sig att implementera beständig tvåvägskommunikation (exempelvis med websockets) samt kunna välja och reflektera kring användningen av tredjepartsmoduler vid utvecklingen av Node.js-applikationer.
