#Föreläsning 4 cookies
1. HTTP och Klient-Serverinteraktion Föreläsningen börjar med en genomgång av HTTP:s grundläggande funktion: det är ett statslöst protokoll, vilket betyder att servern inte behåller någon information mellan två separata förfrågningar. Detta innebär att varje gång klienten gör en request, så behandlas det som ett fristående samtal. Därför behövs mekanismer för att "mina" användarens tillstånd över flera interaktioner, vilket bland annat görs med hjälp av cookies.

2. Vad är Cookies? Cookies är små dataenheter som skickas från servern och lagras på klientens webbläsare. Genom dessa kan servern spåra användarens session över tid och därigenom komma ihåg t.ex.. inställningar eller identifiera en återkommande användare. Föreläsningen visar hur cookies bidrar till att fortsätta och upprätthålla en dialog mellan klient och server trots HTTP:s statslöshet.

3. Användning av cookie-parser i Express För att hantera cookies i en Express-applikation demonstrerades hur man installerar och använder modulen cookie-parser. Stegen innefattar:

Installera modulen med npm install cookie-parser

Inkludera den i applikationen:

javascript
const cookieParser = require('cookie-parser');
app.use(cookieParser());
Detta middleware gör att inkommande request automatisk blir utrustad med ett cookies (och vid signering även signedCookies) objekt, vilket förenklar åtkomsten till sparade cookies.

4. Hantering och Konfiguration av Cookies Föreläsningen redovisar praktiska exempel på:

Skapa och uppdatera cookies: Använd response.cookie(name, value) för att skicka en cookie till klienten. Med ytterligare inställningar, såsom maxAge, domain, path, secure och httpOnly, kan man skräddarsy dess egenskaper. Exempel:

javascript
response.cookie('color', '(0, 0, 255)', { maxAge: 1000 * 60 * 60 });
Rensa (ta bort) en cookie: Använd response.clearCookie('color') för att radera en specifik cookie på klienten.

Läsa cookies: På serversidan kan man läsa in den sparade cookien med request.cookies.color. Vid signerade cookies används istället request.signedCookies.color.

############################################################################################################################Kodexempel:'use strict';

const express = require('express');
const jsdom = require('jsdom');
const fs = require('fs');

//1 & 2. npm install cookie-parser + se till att den är användbar i er lösning.
const cookieParser = require('cookie-parser');

const app = express();

app.listen(3000, function() {
    console.log('Servern är uppe på port 3000!');
});

app.use('/openDir',express.static(__dirname + '/static'));
app.use(express.urlencoded( {extended : true} ));
//3. Ett middleware för kakor kan vara bra att ha.
app.use(cookieParser());

app.get('/', function(request, response) {

    //GET/POST demo i Postman
    console.log(request.query, request.query.yellow);

    response.sendFile(__dirname + '/static/html/form.html', function(err) {
        if( err ) {

            console.log( err );
            response.send(err.message);

        } else {

            console.log('Allt ok!');
        }
    });
});

//POST på rooten?
app.post('/', function(request, response) {

    let red, green, blue; //Default undefined!

    try {
        console.log( request.body );

        if(request.body !== undefined) { //Säkerställ att body inte är undefined!
            red = request.body.red;
            green = request.body.green;
            blue = request.body.blue;
        }

        if( red === undefined || green === undefined || blue === undefined) {
            throw new Error('Ange ett färgvärde"!');
        }

        red = red.trim();
        green = green.trim();
        blue = blue.trim();

        if( red.length === 0 || green.length === 0 || blue.length === 0) {
            throw new Error('Färg får inte vara tomt!');
        }

        if( isNaN( red ) || isNaN( green ) || isNaN( blue )) {
            throw new Error ('Ange ett heltal!');
        }

        red = parseInt(red);
        green = parseInt(green);
        blue = parseInt(blue);

        if( ( red < 0 || red > 256 ) || ( green < 0 || green > 256 ) || ( blue < 0 || blue > 256 )) {
            throw new Error('Färg ska vara 0-255!');
        }

        //4. Skapa tre kakor: red, green & blue!
        response.cookie('red', red, {maxAge : 60 * 1000 * 5});
        response.cookie('green', green, {maxAge : 60 * 1000 * 5});        
        response.cookie('blue', blue, {maxAge : 60 * 1000 * 5});

        //Allt ok i gränssnittet
        fs.readFile(__dirname + '/static/html/index.html', function( err, data) {

            //Ngt gick fel med att läsa upp filen!
            if( err ) {
                console.log( err );
                response.send( err.toString );
            } else {
                //Allt ok med att läsa upp filen!

                let serverDOM = new jsdom.JSDOM(data);

                serverDOM.window.document.querySelector('#status').style.backgroundColor = 'rgb(' + red.toString() + ',' + green + ',' + blue + ')';

                data = serverDOM.serialize();

                response.send (data );
            }

        });

    }catch( oE ) {
        //Ngt gick fel med valideringen från gränssnittet.
        console.log(oE.message );
         //Allt ok i gränssnittet
         fs.readFile(__dirname + '/static/html/form.html', function( err, data) {

            //Ngt gick fel med att läsa upp filen!
            if( err ) {
                console.log( err );
                response.send( err.toString );
            } else {
            
                let serverDOM = new jsdom.JSDOM(data);

                if( red !== undefined ) { //red, green & blue är synliga eftersom de är skrivna innanför första spetsparantesen!
                    serverDOM.window.document.querySelector('[name=red]').setAttribute('value', red); //Observera att .value inte finns implementerat i jsdom!
                }

                if( green !== undefined ) {
                    serverDOM.window.document.querySelector('[name=green]').setAttribute('value', green);
                }

                if( blue !== undefined ) {
                    serverDOM.window.document.querySelector('[name=blue]').setAttribute('value', blue);
                }
               
                serverDOM.window.document.querySelector('#errorMsg').textContent = oE.message;

                data = serverDOM.serialize();

                response.send (data );
            }

        });
    }

});

//5. get på /reset

//6. get på /start
app.get('/start', function(request, response) {

    console.log(request.cookies, 'start');

    let red = request.cookies.red;
    let green = request.cookies.green;
    let blue = request.cookies.blue;

    if(red !== undefined && green !== undefined && blue !== undefined) {

        fs.readFile(__dirname + '/static/html/index.html', function( err, data) {

            //Ngt gick fel med att läsa upp filen!
            if( err ) {
                console.log( err );
                response.send( err.toString );
            } else {
                //Allt ok med att läsa upp filen!

                let serverDOM = new jsdom.JSDOM(data);

                serverDOM.window.document.querySelector('#status').style.backgroundColor = 'rgb(' + red + ',' + green + ',' + blue + ')';

                data = serverDOM.serialize();

                response.send (data );
            }

        });


    } else {
        response.redirect('/');
    }
});
Koden är en Express‑baserad webbserver skriven i Node.js som demonstrerar hur du kan hantera formulärdata, validera indata, använda cookies och dynamiskt modifiera HTML via jsdom. Här är en genomgång av de viktigaste delarna:

Server- och Middleware‑inställningar
Inkludering av moduler: Koden börjar med att importera nödvändiga moduler:

express: För att skapa webbservern och definiera routes.

jsdom: För att läsa in och manipulera HTML-filer på serversidan.

fs: För att läsa filer från disk.

cookie-parser: Middleware som underlättar hantering av cookies genom att parsa incoming cookies och lägga dem på request.cookies.

Serverstart och Middleware‑konfiguration:

Servern lyssnar på port 3000 och loggar ett meddelande när den startat.

Med app.use('/openDir', express.static(__dirname + '/static')); ställs en statisk sökväg in så att filer i katalogen ./static kan nås via /openDir.

express.urlencoded({ extended: true }) används för att tolka URL‑enkodad data från POST‑förfrågningar (till exempel data från formulär).

app.use(cookieParser()); lägger till cookie‑parsing för alla inkommande request så att du enkelt kan läsa cookies via request.cookies.

GET‑Route för Välkomst-/Formulärsidan
Route: app.get('/') När en GET‑förfrågan görs till root (/), skickas filen form.html (som finns i mappen static/html) till klienten.

Innan filen skickas loggas eventuella query‑parametrar (exempelvis om man skickat med en parameter "yellow").

Om något går fel (t.ex. om filen inte kan läsas) skickas ett felmeddelande istället.

Denna sida fungerar som en ingång för användaren där denne kan fylla i färgvärdena (rött, grönt, blått).

POST‑Route för Hantering av Formulärdata
Route: app.post('/') När användaren skickar formuläret via en POST‑förfrågan sker följande steg:

Extrahering och Validering:

Fälten red, green och blue extraheras ur request.body.

Det kontrolleras om något värde saknas eller är tomt. Om så är fallet kastas ett fel med ett meddelande om att ett färgvärde måste anges.

Värdena trimmas bort från eventuell extra whitespace.

Efter det kontrolleras att de är numeriska. Om inte ett numeriskt värde anges kastas ett fel med uppmaning att ange ett heltal.

Sedan omvandlas värdena till heltal och verifieras att de ligger inom intervallet 0–255 (observera att felmeddelandet nämner "0-255" medan koden faktiskt unikt hanterar värden högst 256).

Skapa Cookies:

När valideringen är klar sparas de tre färgvärdena genom att skapa tre cookies med namnen red, green och blue. Varje cookie har en livslängd på 5 minuter (maxAge = 60 * 1000 * 5).

Dynamisk HTML‑generering med jsdom:

Filen index.html läses in från disken.

Med hjälp av jsdom skapas en DOM‑struktur av HTML‑filen.

Ett specifikt element med ID:t status får sin backgroundColor-stil ändrad till den RGB‑färg som skapats utifrån de angivna värdena (t.ex. rgb(12, 34, 56)).

Den modifierade HTML‑strukturen serialiseras tillbaka till en sträng och skickas som svar till klienten.

Felhantering:

Om något steg i valideringen misslyckas fångas felet i en catch-sats.

Då läses istället form.html in på nytt.

Med jsdom sätts de tidigare angivna färgvärdena (om de fanns) i formulärets input‑fält så att användaren inte behöver skriva om dem.

Ett felmeddelande skrivs in i ett element med ID errorMsg för att informera användaren om vad som gick fel.

Den modifierade HTML‑strukturen skickas tillbaka till klienten.

GET‑Route för /start
Route: app.get('/start') Denna route hanterar scenariot när användaren navigerar till /start.

Den hämtar färgvärdena genom att läsa cookies (red, green, blue) från request.cookies.

Om alla tre cookies finns, läses index.html in och ett element med ID status får sin bakgrundsfärg inställd på den sparade färgen.

Om cookies saknas sker en redirect tillbaka till /, så att användaren omdirigeras till formuläret.

Denna route fungerar som en "start"-sida där användarens tidigare valda färg visas genom bakgrundsfärgen i HTML‑filen.

Sammanfattning
Koden exemplifierar hur man:

Startar en Express‑server med statisk filservering.

Hanterar GET‑ och POST‑förfrågningar.

Tar emot och validerar formulärdata, med specificerad felhantering om datan inte uppfyller kraven.

Använder cookies för att spara användarens färgvärden under en begränsad tid.

Modifierar HTML‑innehåll dynamiskt med hjälp av jsdom för att reflektera användarens input (ändra bakgrundsfärg i ett element).


###########################################################################################################################

