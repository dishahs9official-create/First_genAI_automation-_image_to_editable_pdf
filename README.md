# Image → Editable PDF (n8n workflow)

An [n8n](https://n8n.io) workflow that takes a raster image and returns a PDF where
the original text has been removed from the background and replaced with a
selectable/editable text layer.jgcigcu

## How it works

1. **Form Trigger** – user uploads an image (PNG, JPG, JPEG, TIFF, BMP, WEBP).
2. **Normalize** – converts the upload to PNG and records original dimensions.
3. Two branches run in parallel:
   - **Background branch:** resize to 1024×1024 → **OpenAI Image Edit** (`gpt-image-1.5`)
     removes all visible text and reconstructs the background.
   - **OCR branch:** image → PDF → **ABBYY Cloud OCR** (submit, poll status, download XML).
4. **Parse ABBYY XML** – extracts each word's text, position, font style, and samples
   its color from the original image.
5. **Merge + Build PDF** – draws the cleaned background, then overlays the OCR'd words
   as real, selectable text at their original positions.
6. **Form Ending** – returns the finished PDF for download.

## Setup

Import `First_automation.clean.json` into your n8n instance, then create and attach
these credentials (the export ships with placeholders, not real keys):

| Node          | Credential type        | What to provide                                  |
|---------------|------------------------|--------------------------------------------------|
| OpenAI Edit   | OpenAI API             | Your OpenAI API key                              |
| ABBYY Submit  | HTTP Basic Auth        | Your ABBYY Cloud OCR SDK Application ID + Password |
| GetStatus     | HTTP Basic Auth        | Same ABBYY OCR SDK credentials as above          |

> **Note:** the ABBYY Cloud OCR endpoint used here is the EU region
> (`cloud-eu.ocrsdk.com`). Change it if your ABBYY app lives in another region.

## Requirements

- An n8n instance (self-hosted or cloud) with the `code` node's npm modules
  `jimp` and `pdf-lib` available.
- An OpenAI account with image-edit access.
- An ABBYY Cloud OCR SDK account.

## License

Add a license of your choice (e.g. MIT).
