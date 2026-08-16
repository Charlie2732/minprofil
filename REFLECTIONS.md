Reflektion

1
Vad var problemet och vilken OWASP-kategori tillhör det?

Problemet med inloggningen var att användarens indata klistrades in direkt
i SQL-frågan. Skrev man ' OR '1'='1' -- som användarnamn blev villkoret alltid sant,
och lösenordskontrollen kommenterades bort med --. På så sätt kunde man logga in utan att kunna lösenordet.
Detta är en SQL injektion A03.

Vilken åtgärd valde ni?

Fixet var att byta till en parametriserad fråga,
där användarnamn och lösenord skickas som separata parametrar istället för att klistras in i SQL-strängen.

Varför passar just den åtgärden i den här koden?

När värdena skickas som separata parametrar behandlar databasen dom som ren text-data
istället för som kod. Därför kan inte längre ' OR '1'='1' ändra frågans logik.

Vilket alternativ övervägde ni och valde bort och varför?

Jag valde bort att bara ta bort (') eftersom det inte stoppar alla SQL-injektioner.
Det kan också skapa problem med namn som O'Brien, O'Connor osv. Därför valde jag parametrisering,
som är en säkrare lösning.

2
Vad var problemet och vilken OWASP-kategori tillhör det?

Problemet var att profiltexten visades med MarkupString,
vilket stängde av Blazors automatiska skydd. Det gjorde att text som en användare sparade kördes som kod,
istället för att visas som bokstäver. Sparade man <script>alert(document.cookie)</script>
kördes det som skript hos alla som såg profilen.
Detta är en Stored XSS injektion A03.

Vilken åtgärd valde ni?

Jag tog bort MarkupString från Profile och från members.
Det gjorde att Blazors automatiska skydd slogs på igen,
så att texten nu visas som vanlig text istället för att köras som kod.

Varför passar just den åtgärden i den här koden?

En presentation behöver bara visas som text,
och eftersom den kommer från användare och visas för andra vore det farligt att köra den som kod.

Vilket alternativ övervägde ni och valde bort och varför?

Ett annat alternativ kunde ha varit att använda HtmlSanitizer,
men jag valde bort det eftersom presentationen inte behöver innehålla fet stil.
Det räcker att visa texten som vanlig text.

3
Vad var problemet och vilken OWASP-kategori tillhör det?

Cookien saknade skydd. Den saknade HttpOnly, vilket gjorde att JavaScript
(t.ex. ett XSS-skript) kunde läsa sessionscookien och potentiellt stjäla den.
Detta är en Security Misconfiguration A05.

Vilken åtgärd valde ni?

Jag lade till Httponly, Secure och SameSite på cookien.

Varför passar just den åtgärden i den här koden?

HttpOnly gör att JavaScript inte kan läsa cookien.
Det är extra viktigt för en sessionscookie eftersom den används för att hålla användaren inloggad.
Om någon lyckas stjäla den kan de i värsta fall använda användarens session och komma åt kontot.
Secure gör att cookien bara skickas över HTTPS, och SameSite minskar risken att cookien skickas med från andra webbplatser.

Vilket alternativ övervägde ni och valde bort och varför?

Ett annat alternativ är att inte lägga till flaggor alls.
Jag valde bort det eftersom cookien skulle ha varit oskyddad.

4
Vad var problemet och vilken OWASP-kategori tillhör det?

När man loggade ut togs bara cookien bort i webbläsaren, men sessionen fanns kvar på servern.
Det betyder att en hackare som fått tag på cookie värdet fortfarande kan använda det för att logga in.
Detta är en Identification and Authentication Failures A07.

Vilken åtgärd valde ni?

Fixen var att lägga till ett anrop till Invalidate i utloggningen,
som tar bort sessionen på serversidan.

Varför passar just den åtgärden i den här koden?

Det passar för att en utloggning måste avsluta sessionen på servern helt och hållet.
Annars kan en cookie fortfarande användas för att logga in även om användaren har loggat ut, så med Invalidate blir token oanvändbar.

Vilket alternativ övervägde ni och valde bort och varför?

Alternativ var att bara ta bort cookien i webbläsaren utan att avsluta sessionen på servern,
men jag valde bort det eftersom sessionen då skulle leva kvar och kunna återanvändas.