# Velkommen til Ngrok-demo

## Intro

- Hvor mange kjenner Ngrok?
- Hvor mange har forsøkt det?
- Hvor mange har hatt behov for å vise noe som kjører på maskina si?

## Kjapt oppsett av noen nettsteder

```
npx create-nuxt-app demo
```

Gjøre litt tekstendringer, smekke inn helseopplysninger, og fyre opp server

```
npm run dev
```

Nuxt kjører default på http://localhost:3000

## Oppsett av enkel webside

Lage en mappe, og index.html inni den. Bruker html:5 boilerplate i vscode.

Bruke `http-server` for å servere den

```
npm i -g http-server

http-server -p 8888
```

Serveren serverer da katalogen den står i på http://127.0.0.1:8888. Om vi ikke angir port så bruker den 8080.

## En tredje webside, Origo labs

Fyr opp labs lokalt, på http://localhost:8080

## Installasjon av Ngrok

Enklest:

`npm i -g ngrok`

Alternativt gå til ngrok.com og se hva som er greiest for ditt miljø.

## Enkleste ngrok-kjøring

```
ngrok http 8888
```

Dette setter opp en åpen reverse proxy til det som måtte kjøre på port 8888, nå er det vår simple webside.

Strengt talt trenger vi ikke `http-server` i dette tilfellet. Ngrok har innebygget en enkel filserver.

## Vi skrur på filserver, må logge inn

```
ngrok http file:///Users/erlend/dev/oslo-kommune/ozone/webside/
```

Om vi vil blodtrimme litt, så kan vi legge på brukernavn og passord (basic auth):

```
ngrok http file:///Users/erlend/dev/oslo-kommune/ozone/webside/ -auth="user:pass"
```

## Enkle parametre for å sette region

```
ngrok http 3000
```

Dette setter opp en åpen reverse proxy til det som måtte kjøre på port 3000, som er vår Nuxt-server.

(Vise ngrok-url) Men her har vi et problem; noen som ser hva?

Helseopplysninger. (Vis frem Region)

```
ngrok http 3000 -region=eu
```

## Enkle parametre for å få labs til å virke

(Vis frem labs på https://localhost:8080)

Sett opp en enkel Ngrok-proxy:

```
ngrok http 8080
```

Dette setter opp en åpen reverse proxy til det som måtte kjøre på port 8080.

Problemet er at labs ikke svarer for dette hostnavnet.

(Vis loggen fra labs)

Løsningen er enkel

```
ngrok http -host-header=rewrite localhost:8080
```

som gjør det samme som

```
ngrok http -host-header=localhost 8080
```

## Innlogging kreves på et tidspunkt

```
erlend@Erlends-iMac ~/dev/oslo-kommune/ozone $ ngrok http https://nrk.no
Forwarding to local port 443 or a local https:// URL is only available after you sign up.
Sign up at: https://ngrok.com/signup

If you have already signed up, make sure your authtoken is installed.
Your authtoken is available on your dashboard: https://dashboard.ngrok.com/auth/your-authtoken

ERR_NGROK_340
```

Fiks for den over selv om man er logget inn:

```
ngrok http -host-header=rewrite https://www.nrk.no
```

```
erlend@Erlends-iMac ~/dev/oslo-kommune/ozone $ ngrok http -subdomain=nerdegutt -host-header=localhost 8080
Only paid plans may bind custom subdomains.
Failed to bind the custom subdomain 'nerdegutt' for an unauthenticated client.
Sign up at: https://ngrok.com/signup

If you have already signed up, make sure your authtoken is installed.
Your authtoken is available on your dashboard: https://dashboard.ngrok.com/auth/your-authtoken

ERR_NGROK_305
```

(Vis frem innlogging på ngrok.com)

## Config-filer

Det kan være slitsomt når vi trenger mange parametre. Da er det en god idé å ta i bruk config-filer.

https://ngrok.com/docs#config

Default config ligger på `~/.ngrok2/ngrok.yml`

Letteste måte å opprette en config med riktige filrettigheter:

```
ngrok authtoken 2JM6xGAnpByr6YPecCQ1A_3o9iDP5D3hxBgHS2ptC8x
```

Eksempel på config:

```
authtoken: 2JM6xGAnpByr6YPecCQ1A_3o9iDP5D3hxBgHS2ptC8x
region: eu

tunnels:
  labs:
    addr: 8080
    proto: http
    host_header: localhost
  helse:
    addr: 8888
    proto: http
  nuxt:
    addr: 3000
    proto: http
```
