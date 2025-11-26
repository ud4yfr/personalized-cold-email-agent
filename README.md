# Personalized Cold Email / Icebreaker Agent

A multi-stage scraping + summarization + personalization workflow built in **n8n**.  
This agent enriches leads, scrapes their company website, summarizes the pages, and generates a personalized cold email opener (“icebreaker”) using OpenRouter LLMs.  
Results are logged in Google Sheets for later use.

This workflow uses **Apify**, **OpenRouter GPT-4o**, **Google Sheets**, and multiple scraping/HTML parsing steps.  

---

## 🚀 What It Does

- Pulls lead data (first name, last name, email, website, headline, etc.)
- Uses **Apify** to enrich the lead and get:
  - personal emails  
  - work emails  
  - organization metadata  
- Scrapes the company website homepage
- Extracts **all internal links**
- Visits up to **3 pages** for deeper context
- Cleans every URL (normalizing absolute/relative paths)
- Extracts markdown from each page
- Summarizes each page into a structured abstract (using GPT-4o)
- Aggregates multiple abstracts into a single summary
- Generates a **personalized cold email opener (“icebreaker”)** using:
  - Lead name  
  - Lead headline  
  - Website findings  
  - Niche-specific details  
  - Spartan, non-cringe tone  
- Logs final results to Google Sheets:
  - first name  
  - last name  
  - email  
  - website URL  
  - phone number (if available)  
  - generated icebreaker  

---

## 🧰 Tech Stack

- **n8n** workflow automation  
- **Apify Actor** (website + email enrichment)  
- **OpenRouter GPT-4o**  
- **Google Sheets** (read + append rows)  
- **HTML extractor**  
- **Markdown node**  
- **Aggregation node**  
- **JavaScript Code node** for URL/path cleanup  

---

## 🔧 Workflow Overview

### **1. Lead Source**
Starts from:
- manual trigger or  
- imported list of URLs/leads.

### **2. Enrichment (Apify)**
Calls an Apify actor to retrieve:
- emails  
- organization profile  
- website  

### **3. Website Scrape**
- Scrapes homepage HTML  
- Extracts `<a>` tags  
- Splits them into individual URLs  
- Normalizes paths:  
  - relative → leave as is  
  - absolute → convert to clean pathname  
  - strips trailing slashes  

### **4. Page Fetching**
Visits up to **3 pages** to avoid overload.

### **5. Markdown Conversion**
HTML → Markdown for better LLM summarization.

### **6. Page Summaries**
Each page is summarized into a structured JSON object:
```json
{ "abstract": "..." }
```
via GPT-4o.

### **7. Abstract Aggregation**
All individual abstracts → merged into one combined summary.

### **8. Personalized Icebreaker Generation**
LLM produces a custom opener using:
- lead’s name  
- company nickname  
- city/country  
- **non-obvious insights** from the scraped pages  
- your custom cold-email template  

### **9. Log Output**
Writes a new row into Google Sheets with:
- name  
- email  
- website  
- location  
- icebreaker text  

---

## 📂 Repo Structure

```text
/workflows/cold_email_agent.json
/README.md
```

---

## 📝 Setup

1. Add **OpenRouter API** credentials in n8n  
2. Add **Apify API token**  
3. Connect **Google Sheets OAuth**  
4. Prepare Google Sheet columns:
   - first_name  
   - last_name  
   - email  
   - website_url  
   - location  
   - phone_number  
   - multiline_icebreaker  
5. Import `cold_email_agent.json` into n8n  
6. Update:
   - Google Sheet ID (inside Append Row node)  
   - Apify actor URL (if changed)  

---

## ▶️ Usage

1. Add or update lead URLs/names in your Google Sheet  
2. Run the workflow in n8n  
3. Check:
   - whether the website scraped correctly  
   - whether meaningful abstracts were generated  
   - whether the icebreaker sounds coherent and relevant  
4. Export your icebreakers for your cold email campaigns  

---

## ⚠️ Accuracy Notes

This system works very well **most of the time**, but keep in mind:

- If a company’s website has **very little info**, the scraper may produce a generic or random detail  
- Some pages contain junk content; the summarizer tries to fix it but isn’t perfect  
- Icebreakers depend entirely on what the scraper finds  
- Errors happen rarely, but they **do happen**  
- Still, the workflow is **fully usable** for real cold outreach as long as you sanity-check results  

This is normal for any automated scraping → LLM pipeline.

---

## ❓ Why I Built This

To automate the most time-consuming part of cold outreach: researching a lead and writing a personalized opener from scratch.

---

## 📄 License

MIT
