# Lab Writeup: DOM-based open redirection

## 📚 Studijní materiál

Tento repozitář slouží jako můj osobní studijní zápisník a přehled řešení laboratoří z platformy PortSwigger Web Security Academy.

---

## 🔍 Technické vysvětlení (Proč jsem šel do View Page Source?)

Když jsem vstoupil na stránku a kliknul na **View Post**, otevřel se konkrétní příspěvek. Potřeboval jsem zjistit, jakým způsobem web zpracovává odkazy nebo tlačítka (například tlačítko "Back to blog" nebo odkazy na stránce). 

Proto jsem kliknul pravým tlačítkem myši, zvolil **View Page Source** (nebo stiskl `Ctrl + U`), abych se podíval do zdrojového kódu stránky. 

Dole ve zdrojáku jsem našel tento JavaScriptový kód:

```javascript
<a href='#' onclick='returnUrl = /url=(https?:\/\/.+)/.exec(location); location.href = returnUrl ? returnUrl[1] : "/"

#Proč to tam je a co to dělá?
Regulární výraz (/url=(https?:\/\/.+)/): Skript v URL adrese aktivně vyhledává parametr url=, za kterým následuje odkaz (začínající na http:// nebo https://).

Přesměrování (location.href): Jakmile skript tento odkaz v parametru najde, vezme ho a okamžitě na něj uživatele přesměruje (location.href = returnUrl[1]). Pokud tam žádný takový odkaz není, vrátí uživatele na hlavní stránku (/).

🛠️ Postup
#Zjištění adresy laboratoře: Vrátil jsem se zpět na hlavní stránku (Home) a zkopíroval jsem si adresu naší laboratoře:
https://0aee002e04b48d8682bc7ab900ca009f.web-security-academy.net/

Sestavení finálního payloadu: Využil jsem strukturu URL pro konkrétní příspěvek a doplnil za ni zranitelný parametr url=, do kterého jsem vložil adresu laboratoře.

Finální podoba URL v prohlížeči:
https://0aee002e04b48d8682bc7ab900ca009f.web-security-academy.net/post?postId=9&url=https://0aee002e04b48d8682bc7ab900ca009f.web-security-academy.net/


Jak zranitelnost funguje v praxi
 
Čtení dat z DOMu (location): Skript si vytahuje kompletní URL adresu přímo z prohlížeče oběti (location).

Extrakce pomocí regulárního výrazu (/url=(https?:\/\/.+)/): Skript aktivně vyhledává parametr url=, za kterým následuje libovolná webová adresa začínající na http:// nebo https://.

Absence jakékoliv validace: Kód v aplikaci vůbec nekontroluje, kam nalezená adresa vede. Nezjišťuje, zda jde o interní doménu aplikace, nebo o cizí server útočníka (phishing).

Slepé přesměrování (location.href): Jakmile skript adresu v parametru najde, bez váhání na ni uživatele přesměruje (location.href = returnUrl[1]).