---
name: web-browse
description: Browse websites, scrape web content, automate web interactions, and capture screenshots using Playwright. Use when the user asks to browse a website, scrape data, take screenshots, automate web tasks, check a URL, download web content, interact with web pages, or perform web-based research requiring page interaction.
user-invocable: true
argument-hint: "[url-or-action] [options]"
---

# Web Browsing with Playwright

Skill for web browsing, scraping, automation, and screenshots using Playwright. Essential for research, data gathering, and web-based tasks.

## Dependencies

```bash
pip install playwright beautifulsoup4 requests
playwright install chromium
```

## Available Actions

### 1. Browse & Extract (`/web-browse <url>`)

Navigate to a URL and extract page content:

```python
import asyncio
from playwright.async_api import async_playwright

async def browse(url, wait_for=None):
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=True)
        context = await browser.new_context(
            user_agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
        )
        page = await context.new_page()
        await page.goto(url, wait_until="networkidle", timeout=30000)
        if wait_for:
            await page.wait_for_selector(wait_for, timeout=10000)
        title = await page.title()
        text = await page.inner_text("body")
        await browser.close()
        return {"title": title, "text": text, "url": url}

result = asyncio.run(browse("https://example.com"))
```

### 2. Screenshot (`/web-browse screenshot <url>`)

Capture full-page or element screenshots:

```python
async def screenshot(url, output_path, full_page=True, selector=None):
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=True)
        page = await browser.new_page(viewport={"width": 1920, "height": 1080})
        await page.goto(url, wait_until="networkidle", timeout=30000)
        if selector:
            element = await page.query_selector(selector)
            if element:
                await element.screenshot(path=output_path)
        else:
            await page.screenshot(path=output_path, full_page=full_page)
        await browser.close()
        return output_path
```

### 3. Scrape Table Data (`/web-browse scrape-tables <url>`)

Extract tables from web pages (financial data, exchange rates, regulatory info):

```python
from bs4 import BeautifulSoup

async def scrape_tables(url):
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=True)
        page = await browser.new_page()
        await page.goto(url, wait_until="networkidle")
        html = await page.content()
        await browser.close()
    soup = BeautifulSoup(html, 'html.parser')
    tables = []
    for table in soup.find_all('table'):
        headers = [th.get_text(strip=True) for th in table.find_all('th')]
        rows = []
        for tr in table.find_all('tr'):
            cells = [td.get_text(strip=True) for td in tr.find_all('td')]
            if cells:
                rows.append(cells)
        tables.append({"headers": headers, "rows": rows})
    return tables
```

### 4. Fill & Submit Forms (`/web-browse fill-form <url>`)

Automate form filling (with proper authorization only):

```python
async def fill_form(url, form_data, submit_selector=None):
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=True)
        page = await browser.new_page()
        await page.goto(url, wait_until="networkidle")
        for selector, value in form_data.items():
            element = await page.query_selector(selector)
            if element:
                tag = await element.evaluate("el => el.tagName.toLowerCase()")
                if tag == "select":
                    await page.select_option(selector, value)
                elif tag == "input":
                    input_type = await element.get_attribute("type")
                    if input_type in ("checkbox", "radio"):
                        if value:
                            await element.check()
                    else:
                        await element.fill(value)
                else:
                    await element.fill(value)
        if submit_selector:
            await page.click(submit_selector)
            await page.wait_for_load_state("networkidle")
        result = await page.content()
        await browser.close()
        return result
```

### 5. Download Files (`/web-browse download <url> <selector>`)

Download files from web pages (forms, regulatory documents).

### 6. Monitor Page Changes (`/web-browse monitor <url>`)

Check for changes on a web page (regulatory updates, gazette notices).

### 7. Multi-Page Crawl (`/web-browse crawl <url> <max_pages>`)

Crawl multiple pages within the same domain (regulatory sites, company registries).

## Use Cases

| Use Case | Action | Example |
|----------|--------|---------|
| Check company info | `browse` | Search company on registries |
| Scrape exchange rates | `scrape-tables` | Get central bank exchange rates |
| Screenshot regulatory notices | `screenshot` | Capture gazette/regulatory updates |
| Download forms | `download` | Government forms, regulatory filings |
| Monitor regulatory changes | `monitor` | Track standard-setter updates |
| Research company background | `crawl` | Due diligence web research |

## Important Notes

- **Authorization**: Only automate form submissions on portals where you have proper authorization
- **Rate limiting**: Add delays between requests to avoid overwhelming servers
- **Headless mode**: Always run headless by default for server environments
- **Error handling**: Playwright operations can timeout — always wrap in try/except
- **Data privacy**: Be mindful of scraped data — do not store personal data without authorization
