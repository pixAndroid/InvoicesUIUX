# Yantrix Invoice Templates

Premium HTML/CSS invoice templates for the Yantrix Invoice SaaS platform.  
Each template is **print-ready (A4)**, **PDF-friendly**, and optimised for browser preview, WhatsApp sharing, and client branding.

---

## Templates

| # | Folder | Style | Accent Colour |
|---|--------|-------|---------------|
| 1 | `template-01-modern-classic` | Clean professional with blue header | `#1a56db` |
| 2 | `template-02-minimal-elegant` | Minimalist serif, premium feel | `#0f4c81` + gold `#c9a84c` |
| 3 | `template-03-bold-corporate` | Dark navy, bold corporate look | `#0d1b2a` + red `#e84545` |
| 4 | `template-04-saas-modern` | Indigo gradient, SaaS aesthetic | `#4f46e5` → `#7c3aed` |
| 5 | `template-05-clean-professional` | Light navy, high-contrast, HSN/SAC column | `#1e3a8a` |

Each folder contains:
- `invoice.html` — the template source
- `preview.png` — screenshot with sample data

---

## Template Variables

All templates use the following Handlebars-style variables:

### Business Info
| Variable | Description |
|----------|-------------|
| `{{businessName}}` | Business / company name |
| `{{businessGstin}}` | GSTIN of the business |
| `{{businessAddress}}` | Street address |
| `{{businessCity}}` | City |
| `{{businessState}}` | State |
| `{{businessPhone}}` | Phone number |
| `{{businessEmail}}` | Email address |
| `{{businessInitial}}` | Single initial (shown in logo placeholder) |
| `{{businessLogo}}` | URL to logo image (if available) |

### Invoice Details
| Variable | Description |
|----------|-------------|
| `{{invoiceNumber}}` | Invoice number, e.g. `INV-2024-0042` |
| `{{invoiceType}}` | E.g. `Tax Invoice`, `Proforma Invoice` |
| `{{issueDate}}` | Date of issue |
| `{{dueDate}}` | Payment due date |

### Customer Info
| Variable | Description |
|----------|-------------|
| `{{customerName}}` | Customer / company name |
| `{{customerGstin}}` | Customer GSTIN (optional) |
| `{{customerPan}}` | Customer PAN (optional) |
| `{{customerAddress}}` | Customer address |
| `{{customerCity}}` | Customer city |
| `{{customerState}}` | Customer state |
| `{{customerEmail}}` | Customer email (optional) |
| `{{customerPhone}}` | Customer phone (optional) |

### Tax & Supply
| Variable | Description |
|----------|-------------|
| `{{placeOfSupply}}` | E.g. `Haryana (06)` |
| `{{taxType}}` | `CGST/SGST` or `IGST` |

### Amounts
| Variable | Description |
|----------|-------------|
| `{{taxableAmount}}` | Total before tax |
| `{{cgst}}` | CGST amount (shown only if non-zero) |
| `{{sgst}}` | SGST amount (shown only if non-zero) |
| `{{igst}}` | IGST amount (shown only if non-zero) |
| `{{total}}` | Grand total |
| `{{amountDue}}` | Amount remaining to be paid |
| `{{amountInWords}}` | Total in words |

### Line Items
| Variable | Description |
|----------|-------------|
| `{{items}}` | Rendered HTML for all `<tr>` rows in the items table |

### Footer
| Variable | Description |
|----------|-------------|
| `{{notes}}` | Optional notes section |
| `{{terms}}` | Optional terms & conditions |

---

## Usage

### With Handlebars (Node.js)
```js
const Handlebars = require('handlebars');
const fs = require('fs');

const source = fs.readFileSync('./templates/template-01-modern-classic/invoice.html', 'utf8');
const template = Handlebars.compile(source);

const html = template({
  businessName: 'Acme Corp',
  invoiceNumber: 'INV-001',
  // ... all other variables
  items: '<tr><td>1</td><td>Web Design</td><td class="r">1</td><td class="r">10000</td><td class="r">18%</td><td class="r">10000</td></tr>'
});

fs.writeFileSync('./output/invoice.html', html);
```

### With Puppeteer (PDF generation)
```js
const puppeteer = require('puppeteer');

const browser = await puppeteer.launch();
const page = await browser.newPage();
await page.setContent(html);
const pdf = await page.pdf({ format: 'A4', printBackground: true });
await browser.close();
```

### Items row format

**Templates 01–04** (6 columns: #, Description, Qty, Rate, GST%, Amount):
```html
<tr>
  <td>1</td>
  <td>
    <div class="item-name">Service / Product Name</div>
    <div class="item-desc">Optional description</div>
  </td>
  <td class="r">1</td>          <!-- Quantity -->
  <td class="r">10,000.00</td>  <!-- Rate -->
  <td class="r">18%</td>        <!-- Tax % -->
  <td class="r">10,000.00</td>  <!-- Amount -->
</tr>
```

**Template 05** (7 columns: #, Description, HSN/SAC, Qty, Rate, GST%, Amount):
```html
<tr>
  <td>1</td>
  <td>
    <div class="item-name">Service / Product Name</div>
    <div class="item-hsn">Optional description</div>
  </td>
  <td>998314</td>               <!-- HSN/SAC code -->
  <td class="c">1</td>          <!-- Quantity -->
  <td class="r">10,000.00</td>  <!-- Rate -->
  <td class="r">18%</td>        <!-- GST % -->
  <td class="r">10,000.00</td>  <!-- Amount -->
</tr>
```

---

## Design Principles

- **A4 print-ready** — `width: 210mm; min-height: 297mm` with `@page { size: A4; margin: 0; }`
- **Print colour preservation** — `-webkit-print-color-adjust: exact; print-color-adjust: exact`
- **System-font fallbacks** — each template uses a Google Font with safe system-font fallback
- **Conditional blocks** — `{{#if cgst}}`, `{{#if igst}}`, `{{#if notes}}` etc. so empty fields produce no whitespace
- **Logo fallback** — if `{{businessLogo}}` is empty the initial letter `{{businessInitial}}` is shown instead
