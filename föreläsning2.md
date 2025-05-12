Föreläsning 2 moduler och Async-funktioner
¤skapa egna moduler 
*skape egna moduler genom att använda "export" innan funktionsnamnet. och kan sedan importeras genom att använda require.'

#Kod exempel 1
![image](https://github.com/user-attachments/assets/f8626640-ce85-4585-b2b1-519318279948)

Denna kod implementerar en enkel HTTP-server i Node.js som lyssnar på port 3005 och hanterar inkommande HTTP-förfrågningar. Här är en genomgång av vad koden gör:

1. **Import av moduler**:
   - `http`: Används för att skapa en HTTP-server.
   - `fs`: Används för att interagera med filsystemet (läsa och kontrollera filer).

2. **Export av funktion**:
   - Funktionen `myServer` definieras och exporteras, vilket innebär att den kan användas i andra filer.

3. **Skapande av server**:
   - En server skapas med `http.createServer`. Servern hanterar varje förfrågan med en callback-funktion som tar emot `req` (förfrågan) och `res` (svar).

4. **Hantera inkommande förfrågningar**:
   - Servern loggar URL:en från förfrågan (`req.url`) till konsolen.
   - Om URL:en är `'/'` (rot), sätts filnamnet till `'index.html'`.
   - Annars används URL:en som filnamn, men utan första `/` (med hjälp av `substring(1)`).

5. **Kontrollera om filen finns**:
   - Funktionen `fs.stat` används för att kontrollera om filen existerar.
   - Om filen inte finns (`err`):
     - Servern svarar med statuskod 404 och ett meddelande som informerar användaren om att sidan inte finns.
   - Om filen finns:
     - Filens innehåll läses synkront med `fs.readFileSync` och konverteras till en sträng.
     - Filens innehåll skickas tillbaka till klienten med statuskod 200.

6. **Lyssna på port**:
   - Servern lyssnar på port 3005.

### Exempel på beteende:
- Om du navigerar till `http://localhost:3005/` i en webbläsare, försöker servern ladda och returnera innehållet i filen `index.html`.
- Om du navigerar till `http://localhost:3005/annanfil.html`, försöker servern ladda filen `annanfil.html`.
- Om filen inte finns, visas ett 404-felmeddelande med texten "Sidan finns inte!".

### Möjliga förbättringar:
- Använd asynkrona metoder för att läsa filer istället för `fs.readFileSync` för att undvika blockering av huvudtråden.
- Lägg till hantering av MIME-typer så att andra filer än HTML kan serveras korrekt (t.ex. CSS, JavaScript, bilder).

#kod exempel 2
![image](https://github.com/user-attachments/assets/e65cf108-c4d7-4fc2-a742-260a2ba19e56)
Den här koden är en Node.js-applikation som läser innehållet i en fil och räknar hur många gånger bokstaven "a" förekommer i filen. Här är en beskrivning av vad varje del av koden gör:

1. **Importera `fs`-modulen** (rad 2): `fs` används för att läsa filinnehåll från filsystemet.

2. **Huvudflöde med asynkron funktion**:
   - Funktionen `rakna()` körs och returnerar ett löfte (`Promise`) som antingen löser sig med ett värde eller kastar ett fel.
   - `rakna()` används med `.then()` för att logga resultatet (antalet "a"-tecken) eller med `.catch()` för att hantera fel.

3. **Funktionen `rakna()`**:
   - **Läser filen (rad 17)**: Filen `stor-fil.txt` läses synkront med `fs.readFileSync`, och innehållet konverteras till en sträng.
   - **Loopar och räknar (rad 21-27)**: Två loopar används:
     - Den yttre loopen körs 1000 gånger.
     - Den inre loopen går igenom varje tecken i filen och räknar antalet "a".
   - **Returnerar resultatet (rad 32)**: När räkningen är klar returneras resultatet (antalet "a") som ett löfte.

4. **Loggning**: Flera `console.log()`-utskrifter används för att spåra programmets framsteg, till exempel när filen läses och när beräkningarna är klara.

### Problem med koden:
- **Prestanda**: Att köra loopen 1000 gånger gör att programmet blir långsamt om filen är stor.
- **Synkron filinläsning**: `fs.readFileSync` kan blockera huvudtråden medan filen läses, vilket inte är idealiskt i Node.js.

### Sammanfattning:
Koden räknar antalet "a" i en fil genom att läsa hela filen till minnet och loopa igenom dess innehåll 1000 gånger. Resultatet loggas till konsolen.


