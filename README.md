# WhiteRock document form

The page students use to send their application documents to WhiteRock Educational Services. One static file (`index.html`). Every push to `main` publishes it in three places: GitHub Pages (`.github/workflows/static.yml`), Netlify (site `applicationswr`, https://applicationswr.netlify.app) and Vercel (project `whiterock-document-form-rcl`). Check which address is printed on flyers and QR codes before changing the page. Pull requests get a preview on each host.

## What is here

- `index.html`: the page (markup, styles and script in one file).
- `assets/`: the WhiteRock logo, icons and the three fonts (Bricolage Grotesque, Figtree, IBM Plex Mono). Fonts are served from here, not from Google.
- `whiterock-admissions-form (1).html`: an older copy of the form from before the rebrand. Nothing links to it.

## What must not change without changing the Apps Script and the portal too

- **Where it posts.** One `POST` to `SCRIPT_URL` (the Apps Script web app) with a `FormData` field `data` holding JSON. It is sent once and never repeated: the script has already made the Drive folder, the sheet row and the email by the time it answers, so a second request would double them. The page does not read the answer.
- **The JSON keys, in this order:** `name, email, phone, country, city, gender, dob, age, counselor, applicationStatus, notes, source, school, program, files`. `files` is a list of `{ name, base64 }`, every file as a PDF, named `<Student name> - <document type>.pdf`.
- **The document types** (`DOC_TYPES`) end up in file names, and the portal matches on them. The spellings (including `REFRENCE`) are part of that contract.
- **School names** are the visible text of the school options, for example `Regent College London (RCL)`.
- **Lead source.** The answer to "How did you hear about WhiteRock?" is sent as `source` and as `Lead source: Instagram` in `notes`. A link ending in `?src=instagram` pre-selects it (`prefillLeadSource`).
- **Counselor.** "I don't have a counselor yet" means no owner yet; the portal treats it as such.

## How it works

- The helper libraries (jsPDF, mammoth, pdf.js, from cdnjs) load only when a file needs them, so the page itself shows straight away. A download that has not finished after 30 seconds ends with a clear message instead of an endless spinner.
- PDFs are sent as they are. Images, `.docx` and `.txt` are turned into PDFs in the browser. Word `.doc`, RTF, PowerPoint and Excel files are read as plain text, which does not give a readable PDF, so the page warns the student to send a PDF instead.
- Progress (the side steps, the ring in the seal and the thin line across the top) follows the same seven required answers plus whether every file has a type.
- The page says only that WhiteRock has received the documents. It does not mention Google Drive or the email address, on purpose.
- Everything that moves uses `transform` and `opacity`, and `prefers-reduced-motion` switches all of it off (the confetti, the bursts and the count-ups included). Keep it that way when adding more.
