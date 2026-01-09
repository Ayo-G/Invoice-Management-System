# **Deployment Guide**

A high-level outline for users who want to replicate the setup.

## **1. Prepare Sheets**

- Create Pricing
- Create Invoice Data with auto invoice_id formula

```
=MAP(Invoice_Data[date], LAMBDA(a, IF(ISBLANK(a), "", "INV-" & TEXT(ROW(a)-1, "000"))))
```

- Create Invoice Items with dropdowns + calculations
- Build Invoice Template and connect formulas

## **2. Setup Apps Script Project**

- Create new Script in Apps Script window
    - Paste in contents from [`downloadInvoice.gs`](https://github.com/Ayo-G/Invoice-Management-System/blob/main/code/downloadInvoice.gs) file
- Create new HTML file in Apps Script window
    - Paste in the contents from [`download.html`](https://github.com/Ayo-G/Invoice-Management-System/blob/main/code/download.html) file
- Paste in contents from web app script ([`pdf_downloader.gs`](https://github.com/Ayo-G/Invoice-Management-System/blob/main/code/pdf_downloader.gs))
    - Deploy as Web App (execute as: Me, access: Anyone with link)
- Paste in contents from [`pdf_generator.gs`](https://github.com/Ayo-G/Invoice-Management-System/blob/main/code/pdf_generator.gs) file. This is to enable invoice PDF to be exported to Google Drive.
    - Make sure to include a valid Google Drive folder ID
- Paste in contents from [`menu_creator.gs`](https://github.com/Ayo-G/Invoice-Management-System/blob/main/code/menu_creator.gs) file. This is to create a Google Sheets menu that allows easy access to the functions created above.
    - You can make changes to the menu names, but the function names must match the ones in the scripts.

## **3. Integrate Back Into Sheet**

- Add “Download Invoice” button or hyperlink
- Link the script to the drawing/button
- Test with sample invoice
- Use the options in the newly created menu to test

## **4. Test Workflow**

- Add a new invoice entry
- Add items
- Load invoice in template
- Export to drive
- Download via browser

If done correctly, everything should work fine and be fully automated.
