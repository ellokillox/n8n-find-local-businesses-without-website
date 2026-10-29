# Envio a la libreria de plantillas de n8n (Creator Hub)

Generado por `build_places_to_sheets.py`. No editar a mano: cambiar `DESCRIPTION`/`TITLE` en el
script y volver a correrlo (la sticky note amarilla y este archivo salen del mismo texto).

## Campos del formulario

- **Title** (no emoji, sin palabras de marketing, verbo + objeto + destino):
  `Find local businesses without a website in any city and save them to Google Sheets`
- **Categories**: `Marketing`, `Google Sheets` (elegir segun las que ofrezca el formulario)
- **Workflow file**: `Find-local-businesses-without-a-website-in-any-city.json`
- **Nodes used** (9): `Run every day at 08:00` -> `Run manually (test with this one)` -> `Set niche, city and sheet name` -> `Search businesses in Google Maps` -> `Flatten results into one row per business` -> `Skip businesses already saved in earlier runs` -> `Keep only businesses with no website` -> `Append rows to Google Sheets` -> `Discarded: this business already has a website`
- **Nodos de comunidad**: ninguno. Solo nodos incluidos en n8n.
- **Version probada**: n8n 2.43.6 y 3.0.0 (ver `TESTING.md` de esta carpeta)
- **Licencia**: MIT

## Descripcion (pegarla tal cual; es la misma sticky note amarilla)

### Who it is for

Agencies, consultants and freelancers who sell websites, SEO, ads or automation to local
businesses. It builds a first prospect list for a new city or niche from official Google data,
without a scraping subscription and without community nodes.

### How it works

1. **Set niche, city and sheet name** holds the four values you edit: the search text
   (`clinica dental`), the city (`Santiago, Chile`), the Google region code (`CL`) and the
   worksheet name.
2. **Search businesses in Google Maps** calls Google Places API (New) `places:searchText` with a
   field mask that asks only for the fields this template uses, because a `*` mask bills a higher
   SKU. It follows `nextPageToken` for up to three pages of 20 results each.
3. **Flatten results into one row per business** turns the pages into one item per place with a
   stable `placeId`, a formatted address, the phone, the rating and the review count.
4. **Skip businesses already saved in earlier runs** drops the `placeId` values seen in previous
   executions, so a daily run appends only new businesses instead of repeating the same list.
5. **Keep only businesses with no website** discards the ones that already have a site: what
   reaches the sheet is the part of the market with a visible digital gap.
6. **Append rows to Google Sheets** writes the rows, one per business, to your worksheet.

### How to set up

1. Open **Set niche, city and sheet name** and edit the four values. `regionCode` is the
   two-letter country code that Google uses for the region bias (`CL`, `AR`, `MX`, `ES`, `US`).
2. Create an **HTTP Header Auth** credential in n8n: header name `X-Goog-Api-Key`, value = your
   Google Places API key. Attach it to **Search businesses in Google Maps**.
3. Create a **Google Sheets OAuth2** credential and attach it to the last node. Put the ID of
   your spreadsheet in that node's *Document ID* field, replacing
   `REPLACE_WITH_YOUR_SPREADSHEET_ID`.
4. Create the worksheet (tab) with the name you wrote in step 1. Click **Execute workflow** to run it
   from the manual trigger; the scheduled trigger is what runs it daily once you activate the workflow.
5. When the run looks right, activate the workflow: it will run every day at 08:00 in the
   instance timezone.

### What you need

- An n8n instance (developed and tested on n8n 2.x and 3.0, self-hosted).
- A Google Cloud project with **Places API (New)** enabled, and an API key restricted to that API.
  Places API is a paid API with a monthly free credit; this template makes at most 3 requests per
  run and asks for a narrow field mask on purpose.
- A Google Sheet (or any Google Drive spreadsheet) where you can create a tab.

### How to extend

The last filter is the place to put your own rule: keep businesses with a rating below 4.5, with
fewer than 30 reviews, or any other signal that matches who you sell to. The rows are plain text
plus numbers, so you can append an AI node to write a first-contact message, or a CRM node to push
the rows into HubSpot, Pipedrive or Notion.

### Limits, stated on purpose

- Google returns at most 60 results per query (three pages of 20); to cover a whole city, change
  the niche or the city and run it again. The dedupe keeps all of those runs in one list.
- Businesses without a website in Google are not necessarily good prospects: some are closed, some
  have a Facebook page only. The filter narrows the list, it does not qualify it.
- No error handling beyond n8n defaults: if Google answers 403, check that the API is enabled and
  that the key allows Places API (New).

## Antes de pulsar enviar (lo que pide la guia oficial)

- [ ] Cuenta de creador verificada por correo en `creators.n8n.io`.
- [ ] Una sola plantilla en revision a la vez (nuevo creador).
- [ ] JSON importado en una instancia limpia y ejecutado con datos de ejemplo.
- [ ] Sin claves, tokens, IDs de hoja ni identificadores personales (ver `check` del generador).
- [ ] Sticky note amarilla con la descripcion completa + notas de seccion neutras.
- [ ] Nombres de nodo que describan la accion (no `Set`, no `HTTP Request`).
- [ ] Release de n8n declarada (2.x y 3.0).

## Pendiente real (declarado, no maquillado)

- El nodo de Google Sheets no se pudo ejecutar en este entorno: exige una credencial OAuth2 de
  Google que no existe aqui. Se probo importacion, ejecucion del resto de la cadena contra una
  respuesta de Places grabada y el filtro final.
- La llamada a Places API (New) se probo contra una respuesta grabada con el mismo formato
  documentado por Google; **no** contra la API en vivo, porque el proyecto no tiene clave de
  Places. Antes de enviar conviene una corrida real de una pagina (coste aproximado: 0,03 USD).
