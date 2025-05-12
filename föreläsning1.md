föreläsnigs 1 matrial Node.js
*Node.js används för att skapa webbappar spa o streaming tjänster och är en händelsestyrd, icke-blockande i / o modell som gör
den lätt o effektiv

#Egenskaper
*Asykron o händelsedriven
*singel threaded but highly scalable

#kodexempel 1 
![image](https://github.com/user-attachments/assets/6accfd10-7174-43b9-9e4e-42d2378dd9ea)
This file, `exempel-3.js`, demonstrates a simple server-side JavaScript example for processing HTTP requests using Node.js. Below is an explanation of the code:

### Key Points:
1. **Modules Imported:**
   - `fs`: The file system module is used to read files from the server.
   - `http`: The HTTP module is used to create a basic web server.
   - `upper-case`: This module is used to convert a string to uppercase.

2. **Server Creation:**
   - The `http.createServer()` function is used to create an HTTP server.
   - The server listens for incoming requests and responds to them.

3. **Request Handling:**
   - When a request is received, the server logs "nätverksanrop!" (Swedish for "network call") to the console.
   - The server reads the contents of a file named `minHtmlFil.html` synchronously using `fs.readFileSync()` and converts it to a string.

4. **Response Preparation:**
   - The server sets the `Content-Type` of the HTTP response to `text/html` using `res.writeHead(200, { 'Content-Type' : 'text/html' })`.
   - The content of `minHtmlFil.html` is converted to uppercase using `uc.upperCase()`.

5. **Response Sending:**
   - The server sends the uppercase content of the HTML file as the HTTP response using `res.end()`.

6. **Server Listening:**
   - The server listens on port `3005` for incoming requests.

### Summary:
This code creates a simple HTTP server that reads an HTML file (`minHtmlFil.html`), converts its contents to uppercase, and serves it as a response. It demonstrates basic file handling, server creation, and string manipulation in Node.js.

If you have any specific questions about the code, feel free to ask!
