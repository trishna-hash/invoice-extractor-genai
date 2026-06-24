# Automated Invoice Data Extractor

An automated Data Extraction Pipeline built to ingest multi-vendor, unstructured PDF invoices, parse their raw text layouts, and dynamically map them into standardized structures using a local Large Language Model (LLM).

This project uses **Llama 3.2:1b** running locally via **Ollama**, allowing the extraction execution to perform completely offline without incurring external cloud API costs, subscription billing, or data privacy risks.

---

##  Pipeline Architecture
The system processes data sequentially across a 5-step engineering pipeline:
1. **Extraction:** Opens target PDFs and harvests multi-page document layers using `pdfplumber`.
2. **Sanitization:** Trims whitespace and purges empty trailing lines to reduce context window token usage.
3. **Inference:** Routes the text into a local `Llama 3.2` model using a strict system prompt instruction set.
4. **Validation:** Safe-checks the LLM raw string via a programmatic JSON parsing try-except layout block.
5. **Serialization:** Automatically generates target storage paths and drops clean `.json` output configurations.

---

##  Standardized JSON Target Fields
Every input vendor schema is normalized dynamically down into an explicit, **13-field structural standard**:

| JSON Key | Target Information Description | Null-Handling Condition |
| :--- | :--- | :--- |
| `invoice_number` | Unique ID/Reference identifier | String code |
| `invoice_date` | Date issued formatted to **DD-MM-YYYY** | String mapping |
| `due_date` | Exact payment timeline deadline date | Set to `null` if missing |
| `billed_by` | Issuing vendor/person company name | String mapping |
| `billed_to` | Target customer or client being invoiced | String mapping |
| `line_items` | Array containing item details `[{"item", "amount"}]` | Structural dictionary array |
| `subtotal` | Base balance calculated before taxes/discounts | Numerical calculation string |
| `discount` | Any explicit reductions applied | Set to `null` if missing |
| `tax_or_gst` | Calculated internal tax parameters | Set to `null` if missing |
| `total_amount` | Grand total final payload figure | Numerical calculation string |
| `currency` | Code tracking identifier (e.g. INR, USD, EUR) | Normalized string standard |
| `payment_method` | Selected medium parameters (e.g., Bank Transfer) | Set to `null` if missing |
| `notes` | Extra text, guidelines, or instruction entries | Set to `null` if missing |

---

##  Dependencies & Prerequisites

Before running the code, ensure you have the required prerequisites set up on your machine:

1. **Ollama Installation:** Download and install Ollama from [ollama.com](https://ollama.com).
2. **Model Download:** Open your Terminal or Command Prompt and run the following command to download the optimized model:
   ```bash
   ollama pull llama3.2:1b

## Python Libraries: Install the core development processing packages:

bash
pip install openai pdfplumber
#(Note: The openai library is utilized because Ollama natively exposes an identical, OpenAI-compliant REST engine structure on localhost:11434)

##  Project Directory Mapping
To execute the pipeline smoothly, make sure your project directories are organized as follows:

 
invoice-extractor-genai/
│
├── invoices/                 # Put your raw input invoices here
│   ├── invoice-1.pdf
│   ├── invoice-2.pdf
│   └── invoice-3.pdf
│
├── outputs/                  # Auto-generated JSON files will save here
│   ├── output_invoice_1.json
│   ├── output_invoice_2.json
│   └── output_invoice_3.json
│
├── requirements.txt          # Python environments build track
├── .gitignore                # Repository protection track
└── invoice_extractor.ipynb   # Main Jupyter pipeline execution notebook


## Execution Guide
1. Launch the Ollama application on your machine.

2. Place your target vendor documents inside the /invoices directory named matching your mapping index rules (invoice-1.pdf, etc.).

3. Run the cells in the Jupyter Notebook sequentially (Shift + Enter).

4. Check the /outputs folder for your cleanly formatted JSON extraction results!
