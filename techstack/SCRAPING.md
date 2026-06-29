# 🕷️ Web Scraping (Selenium + BeautifulSoup4) — Learning Guide (Beginner → Advanced)

## Tổng quan

**Web Scraping** là kỹ thuật extract data từ websites tự động. Kết hợp **BeautifulSoup4** (parse HTML tĩnh) và **Selenium** (browser automation cho dynamic pages) để scrape mọi loại website.

**Use cases:**
- Price monitoring & comparison
- Data collection cho analytics/ML
- Content aggregation
- Market research & competitor analysis
- Job listing aggregation

**Ai dùng:** Data Engineers, Data Scientists, Analysts, Researchers

**⚠️ Lưu ý:** Luôn kiểm tra `robots.txt`, Terms of Service, và tuân thủ luật pháp trước khi scrape.

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **HTML Parsing** | Phân tích cấu trúc HTML để extract data |
| **CSS Selectors** | Pattern select elements (`.class`, `#id`, `tag`) |
| **XPath** | XML path language — select nodes in HTML tree |
| **Headless Browser** | Browser chạy không có GUI (background) |
| **Rate Limiting** | Giới hạn requests/giây để tránh bị block |
| **robots.txt** | File quy định crawling rules của website |
| **User-Agent** | Identify browser/bot making requests |

### Setup

```bash
# Install libraries
pip install requests beautifulsoup4 lxml
pip install selenium webdriver-manager
```

### requests + BeautifulSoup4 (Static Pages)

```python
import requests
from bs4 import BeautifulSoup

# Basic scraping
url = "https://quotes.toscrape.com/"
headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"}

response = requests.get(url, headers=headers)
soup = BeautifulSoup(response.text, "lxml")

# Find elements
quotes = soup.find_all("div", class_="quote")

for quote in quotes:
    text = quote.find("span", class_="text").get_text()
    author = quote.find("small", class_="author").get_text()
    tags = [tag.get_text() for tag in quote.find_all("a", class_="tag")]
    print(f"{text}\n  — {author} | Tags: {', '.join(tags)}\n")
```

### CSS Selectors

```python
from bs4 import BeautifulSoup

html = """
<div class="product-list">
  <div class="product" data-id="1">
    <h3 class="product-name">Laptop Pro</h3>
    <span class="price">$999</span>
    <p class="description">High performance laptop</p>
  </div>
  <div class="product" data-id="2">
    <h3 class="product-name">Tablet Air</h3>
    <span class="price">$499</span>
    <p class="description">Lightweight tablet</p>
  </div>
</div>
"""

soup = BeautifulSoup(html, "lxml")

# CSS Selectors
products = soup.select("div.product")             # class selector
names = soup.select("h3.product-name")            # tag + class
prices = soup.select(".product-list .price")      # nested
first_product = soup.select_one("div[data-id='1']")  # attribute selector

# Common CSS selectors:
# .class         — by class name
# #id            — by ID
# tag            — by tag name
# tag.class      — tag with class
# parent > child — direct child
# ancestor desc  — any descendant
# [attr='val']   — attribute equals
# tag:nth-child(n) — nth element

for product in products:
    name = product.select_one(".product-name").text
    price = product.select_one(".price").text
    print(f"{name}: {price}")
```

### find() vs find_all()

```python
# find() — first matching element (or None)
title = soup.find("h1")
title = soup.find("div", {"class": "container", "id": "main"})
title = soup.find("a", href=True)  # any <a> with href attribute

# find_all() — all matching elements (list)
links = soup.find_all("a")
links = soup.find_all("a", class_="nav-link")
links = soup.find_all(["h1", "h2", "h3"])  # multiple tags
links = soup.find_all("div", attrs={"data-type": "article"})

# Extract data
for link in soup.find_all("a", href=True):
    text = link.get_text(strip=True)
    url = link["href"]
    print(f"{text}: {url}")

# Navigate tree
parent = element.parent
siblings = element.find_next_siblings()
children = element.find_all(recursive=False)  # direct children only
```

### Bài tập

1. **Basic scrape** — Scrape quotes from `quotes.toscrape.com` (all pages)
2. **Product listing** — Scrape product names + prices from `books.toscrape.com`
3. **Table extraction** — Scrape HTML table → pandas DataFrame
4. **Pagination** — Handle multi-page scraping with next page links

### Resources

- 📖 [BeautifulSoup Docs](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
- 📖 [requests Docs](https://docs.python-requests.org/)
- 💻 [Practice site: quotes.toscrape.com](https://quotes.toscrape.com/)
- 💻 [Practice site: books.toscrape.com](https://books.toscrape.com/)
- 📖 [CSS Selectors Reference (MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors)


---

## Level 2: Intermediate (2–4 tuần)

### Selenium (Dynamic Pages)

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from webdriver_manager.chrome import ChromeDriverManager

# Setup Chrome (headless)
options = Options()
options.add_argument("--headless=new")
options.add_argument("--no-sandbox")
options.add_argument("--disable-dev-shm-usage")
options.add_argument("user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36")

driver = webdriver.Chrome(
    service=Service(ChromeDriverManager().install()),
    options=options
)

try:
    # Navigate to page
    driver.get("https://quotes.toscrape.com/js/")  # JavaScript-rendered page
    
    # Wait for elements to load
    wait = WebDriverWait(driver, 10)
    quotes = wait.until(
        EC.presence_of_all_elements_located((By.CLASS_NAME, "quote"))
    )
    
    # Extract data
    for quote in quotes:
        text = quote.find_element(By.CLASS_NAME, "text").text
        author = quote.find_element(By.CLASS_NAME, "author").text
        print(f"{text} — {author}")
    
finally:
    driver.quit()
```

### Waits (Critical for Dynamic Content)

```python
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By

wait = WebDriverWait(driver, 10)

# Wait for element to be present in DOM
element = wait.until(EC.presence_of_element_located((By.ID, "results")))

# Wait for element to be visible
element = wait.until(EC.visibility_of_element_located((By.CSS_SELECTOR, ".data-table")))

# Wait for element to be clickable
button = wait.until(EC.element_to_be_clickable((By.XPATH, "//button[text()='Load More']")))

# Wait for text to appear
wait.until(EC.text_to_be_present_in_element((By.ID, "status"), "Complete"))

# Wait for URL change
wait.until(EC.url_contains("/results"))

# Custom wait condition
wait.until(lambda d: len(d.find_elements(By.CLASS_NAME, "item")) > 10)
```

### Headless Chrome & Interactions

```python
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.action_chains import ActionChains
import time

driver.get("https://example-search-site.com")

# Type in search box
search_box = driver.find_element(By.NAME, "q")
search_box.clear()
search_box.send_keys("python scraping")
search_box.send_keys(Keys.RETURN)

# Click button
load_more = wait.until(EC.element_to_be_clickable((By.CLASS_NAME, "load-more")))
load_more.click()

# Scroll down (infinite scroll)
last_height = driver.execute_script("return document.body.scrollHeight")
while True:
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
    time.sleep(2)
    new_height = driver.execute_script("return document.body.scrollHeight")
    if new_height == last_height:
        break
    last_height = new_height

# Select dropdown
from selenium.webdriver.support.ui import Select
dropdown = Select(driver.find_element(By.ID, "sort-by"))
dropdown.select_by_value("price_asc")

# Handle alerts
alert = driver.switch_to.alert
alert.accept()  # or alert.dismiss()
```

### Pagination Handling

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import time

def scrape_all_pages(base_url: str) -> list[dict]:
    """Scrape all pages with pagination."""
    all_data = []
    page = 1
    
    while True:
        url = f"{base_url}/page/{page}/"
        print(f"Scraping page {page}: {url}")
        
        response = requests.get(url, headers={"User-Agent": "Mozilla/5.0"})
        
        if response.status_code == 404:
            break
        
        soup = BeautifulSoup(response.text, "lxml")
        items = soup.select(".product-item")
        
        if not items:
            break
        
        for item in items:
            all_data.append({
                "name": item.select_one(".title").text.strip(),
                "price": item.select_one(".price").text.strip(),
                "rating": item.select_one(".rating")["data-value"],
            })
        
        # Check for next page
        next_btn = soup.select_one("a.next")
        if not next_btn:
            break
        
        page += 1
        time.sleep(1)  # Rate limiting!
    
    return all_data

# Run
data = scrape_all_pages("https://books.toscrape.com/catalogue")
df = pd.DataFrame(data)
df.to_csv("scraped_products.csv", index=False)
print(f"Scraped {len(df)} products")
```

### Bài tập

1. **Selenium basics** — Scrape JS-rendered page (`quotes.toscrape.com/js/`)
2. **Infinite scroll** — Handle infinite scroll page (load all content)
3. **Form interaction** — Login to practice site, scrape protected content
4. **Pagination** — Scrape all 50 pages of `books.toscrape.com`

### Resources

- 📖 [Selenium Python Docs](https://selenium-python.readthedocs.io/)
- 📖 [WebDriver Manager](https://github.com/SergeyPirogov/webdriver_manager)
- 📖 [Selenium Waits](https://selenium-python.readthedocs.io/waits.html)
- 💻 [Practice: quotes.toscrape.com/js](https://quotes.toscrape.com/js/)

---

## Level 3: Advanced (4–8 tuần)

### Scrapy Framework

```python
# Install: pip install scrapy

# Create project
# scrapy startproject myproject
# scrapy genspider books books.toscrape.com
```

```python
# myproject/spiders/books_spider.py
import scrapy

class BooksSpider(scrapy.Spider):
    name = "books"
    start_urls = ["https://books.toscrape.com/"]
    
    def parse(self, response):
        """Parse listing page."""
        for book in response.css("article.product_pod"):
            yield {
                "title": book.css("h3 a::attr(title)").get(),
                "price": book.css(".price_color::text").get(),
                "rating": book.css("p.star-rating::attr(class)").get().split()[-1],
                "url": response.urljoin(book.css("h3 a::attr(href)").get()),
            }
        
        # Follow pagination
        next_page = response.css("li.next a::attr(href)").get()
        if next_page:
            yield response.follow(next_page, callback=self.parse)
    
    def parse_detail(self, response):
        """Parse individual book page."""
        yield {
            "title": response.css("h1::text").get(),
            "price": response.css(".price_color::text").get(),
            "description": response.css("#product_description + p::text").get(),
            "availability": response.css(".availability::text").getall()[-1].strip(),
            "upc": response.css("td::text").get(),
        }
```

```python
# settings.py — Polite scraping
ROBOTSTXT_OBEY = True
DOWNLOAD_DELAY = 1
CONCURRENT_REQUESTS = 8
CONCURRENT_REQUESTS_PER_DOMAIN = 4

# User agent rotation
USER_AGENT = "Mozilla/5.0 (compatible; MyBot/1.0)"

# Auto-throttle
AUTOTHROTTLE_ENABLED = True
AUTOTHROTTLE_START_DELAY = 1
AUTOTHROTTLE_MAX_DELAY = 10
AUTOTHROTTLE_TARGET_CONCURRENCY = 2.0

# Retry
RETRY_TIMES = 3
RETRY_HTTP_CODES = [500, 502, 503, 504, 408, 429]
```

```bash
# Run spider
scrapy crawl books -o books.json
scrapy crawl books -o books.csv
scrapy crawl books -o books.parquet  # with scrapy-parquet
```

### Proxy Rotation & Anti-Detection

```python
# Proxy rotation
import random

PROXIES = [
    "http://proxy1:8080",
    "http://proxy2:8080",
    "http://proxy3:8080",
]

def get_random_proxy():
    return {"http": random.choice(PROXIES), "https": random.choice(PROXIES)}

# Rotating User-Agents
USER_AGENTS = [
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 Chrome/120.0.0.0",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 14_0) AppleWebKit/605.1.15 Safari/17.0",
    "Mozilla/5.0 (X11; Linux x86_64; rv:120.0) Gecko/20100101 Firefox/120.0",
]

session = requests.Session()
session.headers.update({"User-Agent": random.choice(USER_AGENTS)})
response = session.get(url, proxies=get_random_proxy(), timeout=10)
```

```python
# Selenium anti-detection
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

options = Options()
options.add_argument("--headless=new")
options.add_argument("--disable-blink-features=AutomationControlled")
options.add_experimental_option("excludeSwitches", ["enable-automation"])
options.add_experimental_option("useAutomationExtension", False)

driver = webdriver.Chrome(options=options)

# Remove webdriver flag
driver.execute_script(
    "Object.defineProperty(navigator, 'webdriver', {get: () => undefined})"
)

# Randomize viewport
import random
width = random.randint(1024, 1920)
height = random.randint(768, 1080)
driver.set_window_size(width, height)
```

### Async Scraping (aiohttp + Playwright)

```python
# aiohttp — fast async HTTP requests
import asyncio
import aiohttp
from bs4 import BeautifulSoup

async def fetch_page(session: aiohttp.ClientSession, url: str) -> str:
    async with session.get(url) as response:
        return await response.text()

async def scrape_urls(urls: list[str]) -> list[dict]:
    """Scrape multiple URLs concurrently."""
    results = []
    
    async with aiohttp.ClientSession(
        headers={"User-Agent": "Mozilla/5.0"},
        connector=aiohttp.TCPConnector(limit=10)  # max 10 concurrent connections
    ) as session:
        tasks = [fetch_page(session, url) for url in urls]
        pages = await asyncio.gather(*tasks, return_exceptions=True)
        
        for url, page in zip(urls, pages):
            if isinstance(page, Exception):
                print(f"Error: {url} — {page}")
                continue
            
            soup = BeautifulSoup(page, "lxml")
            title = soup.find("h1")
            results.append({
                "url": url,
                "title": title.text if title else "N/A"
            })
    
    return results

# Run
urls = [f"https://books.toscrape.com/catalogue/page-{i}.html" for i in range(1, 51)]
results = asyncio.run(scrape_urls(urls))
print(f"Scraped {len(results)} pages")
```

```python
# Playwright — modern browser automation (async)
# pip install playwright && playwright install
from playwright.async_api import async_playwright
import asyncio

async def scrape_with_playwright():
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=True)
        context = await browser.new_context(
            user_agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
            viewport={"width": 1920, "height": 1080}
        )
        page = await context.new_page()
        
        # Navigate & wait
        await page.goto("https://quotes.toscrape.com/js/")
        await page.wait_for_selector(".quote")
        
        # Extract data
        quotes = await page.query_selector_all(".quote")
        for quote in quotes:
            text = await (await quote.query_selector(".text")).inner_text()
            author = await (await quote.query_selector(".author")).inner_text()
            print(f"{text} — {author}")
        
        # Screenshot for debugging
        await page.screenshot(path="screenshot.png")
        
        await browser.close()

asyncio.run(scrape_with_playwright())
```

### Scrapy + Playwright Integration

```python
# pip install scrapy-playwright

# settings.py
DOWNLOAD_HANDLERS = {
    "http": "scrapy_playwright.handler.ScrapyPlaywrightDownloadHandler",
    "https": "scrapy_playwright.handler.ScrapyPlaywrightDownloadHandler",
}
TWISTED_REACTOR = "twisted.internet.asyncioreactor.AsyncioSelectorReactor"
PLAYWRIGHT_LAUNCH_OPTIONS = {"headless": True}

# spider.py
import scrapy

class DynamicSpider(scrapy.Spider):
    name = "dynamic"
    
    def start_requests(self):
        yield scrapy.Request(
            "https://example.com/spa-page",
            meta={"playwright": True, "playwright_page_methods": [
                {"method": "wait_for_selector", "args": [".data-loaded"]},
            ]}
        )
    
    def parse(self, response):
        # response now contains JS-rendered content
        for item in response.css(".data-item"):
            yield {"name": item.css(".name::text").get()}
```

### Bài tập

1. **Scrapy spider** — Build spider crawl 1000+ pages, export JSON + CSV
2. **Anti-detection** — Implement proxy rotation + UA rotation, scrape protected site
3. **Async scraping** — Scrape 100 URLs concurrently with aiohttp (measure speedup)
4. **Playwright** — Scrape SPA (single-page app) with Playwright, handle JS rendering

### Resources

- 📖 [Scrapy Docs](https://docs.scrapy.org/)
- 📖 [Playwright Python](https://playwright.dev/python/)
- 📖 [aiohttp Docs](https://docs.aiohttp.org/)
- 💻 [Scrapy GitHub](https://github.com/scrapy/scrapy)
- 💻 [scrapy-playwright](https://github.com/scrapy-plugins/scrapy-playwright)
- 📖 [Web Scraping Best Practices](https://scrapfly.io/blog/web-scraping-best-practices/)

---

## Alternatives & Công cụ tương đương

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| **BS4 + requests** | Python library | Simple, lightweight, fast for static | No JS rendering |
| **Selenium** | Browser automation | Handles JS, full browser | Slow, resource-heavy |
| **Scrapy** | Framework | Fast, scalable, middleware | Learning curve, overkill for simple |
| **Playwright** | Browser automation | Modern, async, multi-browser | Heavier than BS4 |
| **Puppeteer** | Node.js browser | Chrome DevTools Protocol, fast | Node.js only |
| **Crawlee** | Full framework | Playwright+Scrapy hybrid, queue | Node.js only, newer |
| **Octoparse** | No-code | Visual, no programming needed | Limited, paid |

### Khi nào chọn gì?

- **BS4 + requests** → Static pages, quick scripts, simple scraping
- **Selenium** → Dynamic pages, form interaction, legacy (being replaced by Playwright)
- **Scrapy** → Large-scale crawling, 1000+ pages, structured pipelines
- **Playwright** → Modern replacement for Selenium, async, better anti-detection
- **Puppeteer** → Node.js team, Chrome-specific, headless Chrome
- **Crawlee** → Production scraping system, anti-blocking, Node.js
- **Octoparse** → Non-developers, simple point-and-click scraping
