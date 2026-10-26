# WhiteRock document form

The page students use to send their application documents to WhiteRock Educational Services. One static file (`index.html`), published by GitHub Pages from `main` (`.github/workflows/static.yml`).

## What is here

- `index.html`: the page (markup, styles and script in one file).
- `assets/`: the WhiteRock logo, icons and the three fonts (Bricolage Grotesque, Figtree, IBM Plex Mono). Fonts are served from here, not from Google.
- `whiterock-admissions-form (1).html`: an older copy of the form from before the rebrand. Nothing links to it.

## What must not change without changing the Apps Script and the portal too

- **Where it posts.** One `POST` to `SCRIPT_URL` (the Apps Script web app) with a `FormData` field `data` holding JSON. It is sent once and never repeated: if the answer cannot be read the page still carries on, because the script has already run.
- **The JSON keys, in this order:** `name, email, phone, country, city, gender, dob, age, counselor, applicationStatus, notes, source, school, program, files`. `files` is a list of `{ name, base64 }`, every file as a PDF, named `<Student name> - <document type>.pdf`.
- **The document types** (`DOC_TYPES`) end up in file names, and the portal matches on them. The spellings (including `REFRENCE`) are part of that contract.
- **School names** are the visible text of the school options, for example `Regent College London (RCL)`.
- **Lead source.** The answer to "How did you hear about WhiteRock?" is sent as `source` and as `Lead source: Instagram` in `notes`. A link ending in `?src=instagram` pre-selects it (`prefillLeadSource`).
- **Counselor.** "I don't have a counselor yet" means no owner yet; the portal treats it as such.

## How it works

- The helper libraries (jsPDF, mammoth, pdf.js, from cdnjs) load only when a file needs them, so the page itself shows straight away. A download that has not finished after 30 seconds ends with a clear message instead of an endless spinner.
- PDFs are sent as they are. Images, `.docx` and `.txt` are turned into PDFs in the browser. Word `.doc`, RTF, PowerPoint and Excel files are read as plain text, which does not give a readable PDF, so the page warns the student to send a PDF instead.
- Progress (the side steps and the thin line across the top) follows the same seven required answers plus whether every file has a type.
