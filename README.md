# Invoice Management System

This repository contains a Google Sheets–based invoice management system powered by Google Apps Script. It supports creating invoices in a structured spreadsheet, exporting them as PDFs, saving them to Google Drive, and downloading them through a browser using a deployed Apps Script web app.

All behaviour described here is based strictly on the files present in this repository and the contents you provided from them.

---

## Repository Structure

```
Invoice-Management-System/
├── code/
│   ├── download.html
│   ├── downloadInvoice.gs
│   ├── menu_creator.gs
│   ├── pdf_downloader.gs
│   └── pdf_generator.gs
├── config/
│   └── SETUP_GUIDE.md
├── samples/
│   └── (contains a PDF file)
├── screenshots/
│   └── (contains image files)
└── README.md (currently empty in the repo)
```

* [`code/`](./code) contains all Google Apps Script and HTML files used to power the system.
* [`config/SETUP_GUIDE.md`](./config/setup_guide.md) documents how to recreate the setup in Google Sheets and Apps Script.
* [`samples/`](./samples) contains a sample PDF output.
* `screenshots/` contains images related to the system (no assumptions are made about their content).

---

## What This Project Does

Based on the scripts and setup guide in this repository, the system:

* Uses Google Sheets as the main interface for managing invoice data.
* Builds invoices using an "Invoice Template" sheet.
* Generates invoice IDs automatically using a formula.
* Exports the invoice sheet as a PDF.
* Saves invoice PDFs to Google Drive.
* Allows invoices to be downloaded in the browser through a deployed web app.
* Adds a custom menu inside Google Sheets for easy access to invoice actions.

---

## Core Components

### 1) Invoice Data in Google Sheets

From `config/SETUP_GUIDE.md`, the system requires these sheets:

* Pricing
* Invoice Data (with auto-generated invoice_id)
* Invoice Items (with dropdowns and calculations)
* Invoice Template (connected to the other sheets with formulas)

The invoice ID formula used is:

```
=MAP(Invoice_Data[date], LAMBDA(a, IF(ISBLANK(a), "", "INV-" & TEXT(ROW(a)-1, "000"))))
```

This creates IDs like `INV-001`, `INV-002`, etc., based on row position.

---

### 2) Google Apps Script Files

#### `menu_creator.gs`

Adds a custom menu to Google Sheets when the file opens:

* Menu name: `Invoice PDF`
* Menu items:

  * `Save Invoice to Drive` → calls `generateInvoicePDF`
  * `Download Invoice` → calls `downloadInvoice`

This is triggered automatically by the `onOpen()` function.

---

#### `pdf_generator.gs`

Function: `generateInvoicePDF()`

What it does:

* Reads invoice ID from cell `D9` in the `Invoice Template` sheet.
* Reads customer name from cell `A10`.
* Builds a Google Sheets export URL for the template sheet.
* Fetches the sheet as a PDF using OAuth.
* Saves the PDF to a Google Drive folder using:

  * A hardcoded `folderId` placeholder.
* Names the file as:

  * `<CustomerName> <InvoiceId>.pdf`

Placeholders that must be replaced by users:

* `FOLDER_ID-FROM-GOOGLE_DRIVE`

---

#### `pdf_downloader.gs`

Acts as a deployed web app.

Main function: `doGet(e)`

What it does:

* Expects a URL parameter: `?invoice=INV-001`.
* Opens a spreadsheet using a hardcoded `SHEET_ID` placeholder.
* Locates the `Invoice Template` sheet.
* Builds a PDF export URL for that sheet.
* Fetches the PDF.
* Encodes it in base64.
* Returns an HTML page that auto-downloads the PDF in the browser using a data URI.

Placeholders that must be replaced:

* `SHEET_ID`

---

#### `downloadInvoice.gs`

Function: `downloadInvoice()`

What it does:

* Reads invoice ID from cell `D9` in `Invoice Template`.
* Builds a URL to the deployed web app with:

  * `?invoice=<invoiceId>`
* Injects that URL into an HTML template (`download.html`).
* Opens a small modal in Google Sheets that triggers the browser download.

Placeholder that must be replaced:

* `LINK TO WEBAPP BASE`

---

#### `download.html`

This is a simple HTML file used as a modal dialog in Google Sheets.

What it does:

* Receives a `url` variable injected from Apps Script.
* Opens that URL in a new browser tab.
* Closes the modal after a short delay.

---

## Setup Overview

The official setup process is defined in `config/SETUP_GUIDE.md`.

High-level flow:

1. Prepare Google Sheets

   * Create Pricing sheet.
   * Create Invoice Data with auto invoice ID formula.
   * Create Invoice Items with dropdowns and calculations.
   * Build Invoice Template and connect formulas.

2. Setup Apps Script

   * Create a new Apps Script project from the Sheet.
   * Add scripts from:

     * `downloadInvoice.gs`
     * `download.html`
     * `pdf_downloader.gs` (deploy as web app)
     * `pdf_generator.gs`
     * `menu_creator.gs`
   * Replace all placeholder values.

3. Integrate With Sheet

   * Add a "Download Invoice" button or drawing.
   * Link it to `downloadInvoice()`.
   * Test using sample invoice data.
   * Use the custom menu to test saving and downloading.

4. Test Workflow

   * Add invoice entry.
   * Add items.
   * Load invoice into template.
   * Export to Drive.
   * Download via browser.

---

## Placeholders You Must Replace

The following values are intentionally left as placeholders in the repo:

* In `downloadInvoice.gs`:

  * `LINK TO WEBAPP BASE`

* In `pdf_downloader.gs`:

  * `SHEET_ID`

* In `pdf_generator.gs`:

  * `FOLDER_ID-FROM-GOOGLE_DRIVE`

These must be replaced with real values for the system to work.

---

## Samples and Screenshots

* `samples/` contains a PDF file. This represents an example output from the system.

### Screenshots

The `screenshots/` folder contains the following files:

* `Invoice Data Sheet.png`
* `Invoice Items Sheet.png`
* `Invoice Template Sheet.png`
* `Pricing Sheet.png`
* `data flow.png`

---

## What This Repo Does Not Include

Based strictly on the repository contents:

* No backend server outside Google Apps Script.
* No authentication layer beyond Google’s own permissions.
* No external database.
* No automated tests.
* No CI/CD or deployment scripts.

---

## Intended Use

This project is designed to:

* Run entirely inside Google Sheets and Google Apps Script.
* Be configured manually by copying scripts into an Apps Script project.
* Be used by non-developers once set up, through custom menu items and buttons.

It is not packaged as a plug-and-play app; setup is manual and documented in [`config/SETUP_GUIDE.md`](./config/setup_guide.md).

---

## License and Usage

A license file is present in the repository. Refer to the [`LICENSE`](./LICENSE) file in the root of the repo for the exact terms and conditions. (This README intentionally does not restate or reinterpret the license.)
