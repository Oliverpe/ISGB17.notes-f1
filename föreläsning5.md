Föreläsing 5 dubbelriktad kommunikation med socket.io

Översikt Föreläsningen fokuserade på modern kommunikation på webben med JavaScript, med ett särskilt fokus på dubbelsidig (bidirektionell) kommunikation med hjälp av websockets och biblioteket Socket.io.. Genom att jämföra olika tekniker gavs en klar bild av varför äldre metoder inte räcker till i dagens realtidsapplikationer.

Traditionella metoder Först presenterades hur kommunikationen traditionellt hanterats med XHR (XMLHttpRequest). Denna metod innebär att klienten skickar ett komplett HTTP-anrop och väntar på ett komplett svar från servern, vilket begränsar kommunikationen till att vara enkelriktad – servern kan inte skicka data på egen hand utan att en klient först initierat en begäran. Detta är grunden för till exempel AJAX, men passar inte för applikationer med krav på omedelbar återkoppling.

Server-Sent Events (SSE) Nästa teknik, SSE, beskrives som en metod för att effektivt streama textbaserad data. Här initierar klienten en anslutning, varefter servern skickar uppdateringar kontinuerligt. Det centrala med SSE är att den möjliggör realtidsuppdateringar, till exempel för börskurser eller sportresultat, men kommunikationen är fortfarande enkelriktad – det är bara servern som sänder data.

Websockets Huvudfokus lades på websockets, som är den första tekniken som möjliggör äkta dubbelsidig kommunikation över en enda TCP-anslutning. Med websockets kan både klienten och servern skicka och ta emot meddelanden när som helst, vilket är avgörande för applikationer som kräver omedelbar interaktivitet, såsom spel eller chattprogram. Detta är ett steg bort från de tidigare metoderna som var beroende av upprepade förfrågningar från klientens sida.

Socket.io För att göra hanteringen av websockets smidigare introducerades biblioteket Socket.io.. Detta tredjepartsbibliotek erbjuder flera fördelar:

Det använder automatiskt websockets om de är tillgängliga, men kan även falla tillbaka på andra tekniker vid behov.

Biblioteket hanterar asynkrona operationer och promise-objekt, vilket underlättar utvecklarens arbete.

På serversidan integreras Socket.io med hjälp av NPM och require, medan klienten vanligtvis laddar in socket.io-client genom t.ex.. ett CDN.

############################################################################################################################
'use strict';

//Import av kodbibliotek
const express = require('express');
const {Server} = require('socket.io'); //Hämtar enbart Server-klassen från kodbiblioteket socket.io och döper det till "Server"
const app = express();

//Startar upp express http-server och sparar en kopia av servern med namnet httpserver
const httpserver = app.listen(3001, function(){
    console.log('Servern kör!');
});

//Startar upp en instans av Serverklassen med express-http-servern som parameter
const io = new Server(httpserver);

//Middleware för att "servera" klienten med alla filer i mappen clientsscripts under url'en /silverfisk
app.use('/silverfisk', express.static(__dirname + '/clientscripts'));

//Root endpoint 
app.get('/', function(req,res){
    res.sendFile(__dirname + '/index.html');
});

//Endpoint för att öppna upp favicon
app.get('/favicon.ico', function(req,res){
    res.sendFile(__dirname + '/favicon.ico');
});

//Lyssnare för socket-anslutningar
io.on('connection', function(socket) {
    console.log('En användare anslöt via socket!');

    //Socketlyssnare som lyssnar efter sockethändelsen banan
    socket.on('banan', function() {

        console.log('banan!!!!');

        let r = Math.floor(Math.random() * 256);
        let g = Math.floor(Math.random() * 256);
        let b = Math.floor(Math.random() * 256);

        //Skicka socket-händelsen silverfisk till ALLA anslutna klienter
        io.emit('silverfisk', {
            'red': r,
            'green': g,
            'blue': b
        });

    });



    //Lyssnare för sockethändelsen disconnect
    socket.on('disconnect',function(){
        console.log('En användare stängde anslutningen');
    })

} );

############################################################################################################################
Kodexempel och funktionaliteter Föreläsningen visade hur Socket.io används i praktiken:

Genom att lyssna på anslutningshändelsen med io.on('connection', (socket) => { ... }) kan man hantera varje ny klient.

Med metoder som io.emit(kommando, data) skickas meddelanden till alla anslutna klienter, medan io.broadcast.emit(kommando, data) utesluter den klient som utlöste händelsen.

För att rikta meddelanden till en enskild klient används io.to(socket.id).emit(kommando, data).

En extra funktionalitet som framhölls var stödet för "rum", där klienter kan grupperas och meddelanden riktas till en specifik grupp, vilket ger ytterligare flexibilitet.

Sammanfattning Sammanfattningsvis visar föreläsningen att Socket.io och websockets är kraftfulla verktyg för att skapa applikationer med realtidskommunikation. Med dessa tekniker kan utvecklare bygga interaktiva system där både klient och server kan kommunicera fritt och omedelbart, vilket är en klar fördel jämfört med äldre, mer begränsade metoder. Detta gör det möjligt att utveckla mer responsiva och dynamiska webbapplikationer, exempelvis för chatt, spel och andra applikationer där snabb feedback är avgörande.
