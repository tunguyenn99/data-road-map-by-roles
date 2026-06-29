# 🌐 Google Analytics 4 & Web Scraping — Learning Guide

## Tổng quan

Guide này gồm 2 phần:
1. **Google Analytics 4 (GA4):** Web/app analytics platform — track user behavior, conversions, engagement
2. **Web Scraping (Selenium + BeautifulSoup):** Extract data từ websites programmatically

**Dùng cho:** DA (marketing/product), Growth, Digital Marketing, DE (data collection)

---

# Part 1: Google Analytics 4 (GA4)

## GA4 Overview

GA4 là thế hệ mới của Google Analytics. Mọi thứ đều là events (không còn pageviews/sessions như Universal Analytics). Tích hợp native với BigQuery (export raw data miễn phí).

---

## Level 1: Beginner (1 tuần)

### Core Concepts
- **Events:** Mọi interaction = event (page_view, scroll, click, purchase...)
- **Parameters:** Metadata của event (page_title, currency, value...)
- **Users:** Identified by Client ID hoặc User ID
- **Sessions:** Group of events trong 1 visit
- **Conversions:** Events quan trọng (purchase, sign_up, lead)

### Key Metrics
```
┌────────────────────────────────────────────────────────┐
│ Metric              │ Ý nghĩa                          │
├─────────────────────┼──────────────────────────────────┤
│ Users               │ Unique visitors                   │
│ Sessions            │ Number of visits                  │
│ Engagement Rate     │ % sessions có meaningful interact │
│ Avg Engagement Time │ Thời gian tương tác trung bình   │
│ Conversions         │ Key actions completed             │
│ Revenue             │ Tổng doanh thu (e-commerce)       │
│ Bounce Rate         │ % sessions không engage           │
└─────────────────────┴──────────────────────────────────┘

Dimensions vs Metrics:
- Dimensions: categorical (page_path, source, country, device)
- Metrics: numeric (users, sessions, revenue, engagement_rate)
```

### GA4 Interface
```
Reports section:
├── Realtime — live data (last 30 min)
├── Life cycle
│   ├── Acquisition — where users come from
│   ├── Engagement — what users do
│   ├── Monetization — revenue, purchases
│   └── Retention — users coming back
├── User
│   ├── Demographics — age, gender, location
│   └── Tech — device, browser, OS
└── Explore — custom analysis (funnel, path, etc.)
```

### Bài tập
1. Explore GA4 Demo Account (Google cung cấp free)
2. Navigate: Acquisition → Engagement → Retention reports
3. Identify top traffic sources, top pages, conversion events
4. Compare date ranges (this month vs last month)
5. Create custom report (dimensions + metrics combo)

### Resources
| Resource | Link | Type |
|---|---|---|
| GA4 Demo Account | [analytics.google.com/analytics/web/demoAccount](https://support.google.com/analytics/answer/6367342) | Free |
| Google Skillshop | [skillshop.google.com](https://skillshop.google.com/) | Free |
| GA4 Documentation | [developers.google.com/analytics](https://developers.google.com/analytics) | Free |

---

## Level 2: Intermediate (2 tuần)

### Custom Events & Parameters
```javascript
// Google Tag Manager (GTM) — track custom events
// Example: Track button click
gtag('event', 'cta_click', {
    'button_text': 'Sign Up Now',
    'page_section': 'hero',
    'user_type': 'new_visitor'
});

// E-commerce: Track purchase
gtag('event', 'purchase', {
    'transaction_id': 'T12345',
    'value': 1500000,
    'currency': 'VND',
    'items': [{
        'item_id': 'SKU001',
        'item_name': 'Product A',
        'price': 500000,
        'quantity': 3
    }]
});
```

### Explorations (Advanced Analysis)
```
Exploration types:
├── Free-form — custom pivot tables
├── Funnel — conversion funnel analysis
├── Path — user navigation paths
├── Segment overlap — compare audiences
├── Cohort — retention by signup cohort
└── User lifetime — LTV analysis

Example Funnel:
Step 1: page_view (product page)
Step 2: add_to_cart
Step 3: begin_checkout
Step 4: purchase
→ See drop-off at each step
```

### UTM Parameters & Attribution
```
UTM structure:
https://example.com/landing?
  utm_source=facebook
  &utm_medium=paid_social
  &utm_campaign=summer_sale_2024
  &utm_content=video_ad_v2
  &utm_term=data+analytics

Attribution models in GA4:
├── Data-driven (default, ML-based)
├── Last click
├── First click
└── Linear (deprecated in newer GA4)
```

### BigQuery Export (Game changer!)
```
Setup: GA4 Admin → BigQuery Links → Link

Export types:
- Daily export: 1 table per day (events_YYYYMMDD)
- Streaming export: real-time (events_intraday_YYYYMMDD)
- Both: recommended for production

Schema: events_* table structure
├── event_date (STRING: "20240615")
├── event_timestamp (INTEGER: microseconds)
├── event_name (STRING: "page_view")
├── event_params (RECORD, REPEATED)
│   ├── key (STRING)
│   └── value (RECORD: string_value, int_value, float_value, double_value)
├── user_properties (RECORD, REPEATED)
├── device (RECORD: category, browser, os)
├── geo (RECORD: country, city, region)
├── traffic_source (RECORD: source, medium, campaign)
└── ecommerce (RECORD: purchase_revenue, items[])
```

### Bài tập
1. Setup custom event tracking (GTM hoặc gtag)
2. Build funnel exploration: product → cart → checkout → purchase
3. Analyze UTM performance by source/medium
4. Enable BigQuery export (nếu có quyền admin)
5. Create audience segment: high-value users

---

## Level 3: Advanced (3 tuần)

### BigQuery GA4 Queries
```sql
-- Basic: Page views by page
SELECT
    event_date,
    (SELECT value.string_value FROM UNNEST(event_params)
     WHERE key = 'page_location') AS page,
    COUNT(*) AS pageviews,
    COUNT(DISTINCT user_pseudo_id) AS unique_users
FROM `project.analytics_123456789.events_*`
WHERE _TABLE_SUFFIX BETWEEN '20240601' AND '20240630'
  AND event_name = 'page_view'
GROUP BY 1, 2
ORDER BY pageviews DESC
LIMIT 50;

-- Funnel analysis in BigQuery
WITH funnel AS (
    SELECT
        user_pseudo_id,
        MAX(IF(event_name = 'page_view', 1, 0)) AS step1_view,
        MAX(IF(event_name = 'add_to_cart', 1, 0)) AS step2_cart,
        MAX(IF(event_name = 'begin_checkout', 1, 0)) AS step3_checkout,
        MAX(IF(event_name = 'purchase', 1, 0)) AS step4_purchase
    FROM `project.analytics_123456789.events_*`
    WHERE _TABLE_SUFFIX BETWEEN '20240601' AND '20240630'
    GROUP BY 1
)
SELECT
    COUNT(*) AS total_users,
    SUM(step1_view) AS viewed,
    SUM(step2_cart) AS added_to_cart,
    SUM(step3_checkout) AS started_checkout,
    SUM(step4_purchase) AS purchased,
    ROUND(SUM(step4_purchase) / SUM(step1_view) * 100, 2) AS conversion_rate
FROM funnel;

-- Session-level aggregation
WITH sessions AS (
    SELECT
        user_pseudo_id,
        (SELECT value.int_value FROM UNNEST(event_params)
         WHERE key = 'ga_session_id') AS session_id,
        MIN(event_timestamp) AS session_start,
        MAX(event_timestamp) AS session_end,
        COUNT(*) AS events_in_session,
        COUNTIF(event_name = 'purchase') AS purchases
    FROM `project.analytics_123456789.events_*`
    WHERE _TABLE_SUFFIX = '20240615'
    GROUP BY 1, 2
)
SELECT
    COUNT(*) AS total_sessions,
    AVG(events_in_session) AS avg_events,
    SUM(purchases) AS total_purchases,
    ROUND(SUM(IF(purchases > 0, 1, 0)) / COUNT(*) * 100, 2) AS session_conversion_rate
FROM sessions;
```

### Measurement Protocol (Server-side)
```python
# Send events server-side (bypass ad blockers)
import requests

MEASUREMENT_ID = "G-XXXXXXXXXX"
API_SECRET = "your_api_secret"

def send_event(client_id, event_name, params=None):
    """Send GA4 event via Measurement Protocol."""
    url = f"https://www.google-analytics.com/mp/collect"
    payload = {
        "client_id": client_id,
        "events": [{
            "name": event_name,
            "params": params or {}
        }]
    }
    response = requests.post(
        url,
        params={"measurement_id": MEASUREMENT_ID, "api_secret": API_SECRET},
        json=payload
    )
    return response.status_code

# Track server-side purchase
send_event(
    client_id="user_123.session_456",
    event_name="purchase",
    params={
        "transaction_id": "T789",
        "value": 2500000,
        "currency": "VND",
        "items": [{"item_id": "SKU001", "item_name": "Product", "quantity": 1}]
    }
)
```

### GA4 Data API (Programmatic Access)
```python
# pip install google-analytics-data

from google.analytics.data_v1beta import BetaAnalyticsDataClient
from google.analytics.data_v1beta.types import (
    RunReportRequest, DateRange, Metric, Dimension
)

client = BetaAnalyticsDataClient()

request = RunReportRequest(
    property=f"properties/123456789",
    date_ranges=[DateRange(start_date="30daysAgo", end_date="today")],
    dimensions=[
        Dimension(name="date"),
        Dimension(name="sessionSource"),
    ],
    metrics=[
        Metric(name="sessions"),
        Metric(name="conversions"),
        Metric(name="totalRevenue"),
    ],
    order_bys=[{"metric": {"metric_name": "sessions"}, "desc": True}],
    limit=100
)

response = client.run_report(request)
for row in response.rows:
    date = row.dimension_values[0].value
    source = row.dimension_values[1].value
    sessions = row.metric_values[0].value
    print(f"{date} | {source}: {sessions} sessions")
```

### Resources (GA4)
| Resource | Link | Type |
|---|---|---|
| GA4 BigQuery Cookbook | [developers.google.com/analytics/bigquery](https://developers.google.com/analytics/bigquery) | Free |
| Measurement Protocol | [developers.google.com/analytics/devguides/collection/protocol/ga4](https://developers.google.com/analytics/devguides/collection/protocol/ga4) | Free |
| GA4 Data API | [developers.google.com/analytics/devguides/reporting/data/v1](https://developers.google.com/analytics/devguides/reporting/data/v1) | Free |

### GA4 Alternatives
| Tool | Type | Strengths | Weaknesses |
|---|---|---|---|
| **GA4** | Free (Google) | Free, BigQuery export, ecosystem | Learning curve, privacy concerns |
| **Mixpanel** | Product analytics | Event-based, great UX, funnels | Expensive at scale |
| **Amplitude** | Product analytics | Cohorts, retention, ML | Cost, complexity |
| **PostHog** | Open-source | Self-hosted, session replay | Newer, smaller community |
| **Plausible** | Privacy-first | Simple, no cookies, GDPR | Limited features |
| **Matomo** | Self-hosted | Full GA alternative, privacy | Setup complexity |

---

# Part 2: Web Scraping (Selenium + BeautifulSoup4)

## Web Scraping Overview

Web scraping = extract data từ websites programmatically. BeautifulSoup cho static HTML, Selenium cho dynamic pages (JavaScript-rendered content).

---

## Level 1: Beginner (1 tuần) — BeautifulSoup4

### Core Concepts
- **HTML Parsing:** Đọc và navigate HTML structure
- **CSS Selectors:** Target specific elements (class, id, tag)
- **requests:** Fetch HTML content từ URL
- **BeautifulSoup:** Parse HTML → extract data
- **robots.txt:** Rules website set cho crawlers

### First Scraper
```python
import requests
from bs4 import BeautifulSoup

# Fetch page
url = "https://quotes.toscrape.com/"
headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"}
response = requests.get(url, headers=headers)

# Parse HTML
soup = BeautifulSoup(response.text, "html.parser")

# Find elements
quotes = soup.find_all("div", class_="quote")

for quote in quotes:
    text = quote.find("span", class_="text").get_text()
    author = quote.find("small", class_="author").get_text()
    tags = [tag.get_text() for tag in quote.find_all("a", class_="tag")]
    print(f"{text}\n  — {author} | Tags: {', '.join(tags)}\n")
```

### Finding Elements
```python
# By tag
soup.find("h1")                          # First <h1>
soup.find_all("a")                       # All <a> tags

# By class
soup.find("div", class_="product-card")
soup.find_all("span", class_="price")

# By ID
soup.find(id="main-content")

# By attribute
soup.find("input", attrs={"name": "email"})
soup.find_all("a", href=True)            # All <a> with href

# CSS Selectors (most flexible)
soup.select("div.product-card h2.title") # Nested selector
soup.select("table#data-table tr td")    # Table cells
soup.select("ul.menu > li > a")          # Direct children
soup.select("[data-price]")              # By data attribute

# Extract data
element = soup.find("a", class_="link")
text = element.get_text(strip=True)      # Inner text
href = element.get("href")               # Attribute value
html = str(element)                      # Raw HTML
```

### Practical Example: Scrape Product List
```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import time

def scrape_products(base_url, pages=5):
    """Scrape product data from multiple pages."""
    all_products = []

    for page in range(1, pages + 1):
        url = f"{base_url}?page={page}"
        response = requests.get(url, headers={"User-Agent": "Mozilla/5.0"})

        if response.status_code != 200:
            print(f"Failed page {page}: {response.status_code}")
            continue

        soup = BeautifulSoup(response.text, "html.parser")
        products = soup.select("div.product-item")

        for product in products:
            name = product.select_one("h3.product-name").get_text(strip=True)
            price_text = product.select_one("span.price").get_text(strip=True)
            price = float(price_text.replace("₫", "").replace(",", "").strip())
            rating = product.select_one("span.rating")
            rating_value = float(rating.get("data-rating", 0)) if rating else None

            all_products.append({
                "name": name,
                "price": price,
                "rating": rating_value,
                "page": page
            })

        print(f"Page {page}: {len(products)} products")
        time.sleep(1)  # Polite delay between requests

    return pd.DataFrame(all_products)

# Run
df = scrape_products("https://example-shop.com/products")
df.to_csv("products.csv", index=False)
print(f"Total: {len(df)} products scraped")
```

### Bài tập
1. Scrape quotes.toscrape.com (all pages)
2. Scrape product listing (name, price, rating) → CSV
3. Practice CSS selectors: find nested elements
4. Add error handling (try/except, status code check)
5. Implement polite scraping (delays, User-Agent)

---

## Level 2: Intermediate (2 tuần) — Selenium

### Why Selenium?
```
BeautifulSoup limitations:
- Only works with static HTML (server-rendered)
- Can't handle JavaScript-rendered content
- Can't interact (click, scroll, type, wait)

Selenium solves:
- Renders JavaScript (SPA, React, Vue apps)
- Simulates user interactions
- Handles dynamic loading (infinite scroll, "Load More")
- Can fill forms, login, navigate
```

### Selenium Setup & Basics
```python
# pip install selenium webdriver-manager

from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

# Setup Chrome driver (auto-download correct version)
options = webdriver.ChromeOptions()
options.add_argument("--headless")  # Run without GUI
options.add_argument("--no-sandbox")
options.add_argument("--disable-dev-shm-usage")
options.add_argument("user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64)")

driver = webdriver.Chrome(
    service=Service(ChromeDriverManager().install()),
    options=options
)

# Navigate
driver.get("https://example.com/dynamic-page")

# Wait for element to appear (important for JS pages!)
wait = WebDriverWait(driver, 10)
element = wait.until(
    EC.presence_of_element_located((By.CSS_SELECTOR, ".data-table"))
)

# Find elements
title = driver.find_element(By.TAG_NAME, "h1").text
items = driver.find_elements(By.CSS_SELECTOR, ".product-card")

for item in items:
    name = item.find_element(By.CSS_SELECTOR, ".name").text
    price = item.find_element(By.CSS_SELECTOR, ".price").text
    print(f"{name}: {price}")

# Always close
driver.quit()
```

### Interactions
```python
from selenium.webdriver.common.keys import Keys
import time

# Click button
button = driver.find_element(By.ID, "load-more")
button.click()

# Type in input
search_box = driver.find_element(By.NAME, "q")
search_box.clear()
search_box.send_keys("data analytics")
search_box.send_keys(Keys.ENTER)

# Select dropdown
from selenium.webdriver.support.ui import Select
dropdown = Select(driver.find_element(By.ID, "category"))
dropdown.select_by_visible_text("Electronics")

# Scroll to bottom (infinite scroll)
driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
time.sleep(2)  # Wait for content to load

# Scroll until no new content
last_height = driver.execute_script("return document.body.scrollHeight")
while True:
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
    time.sleep(2)
    new_height = driver.execute_script("return document.body.scrollHeight")
    if new_height == last_height:
        break
    last_height = new_height
```

### Selenium + BeautifulSoup (Best Combo)
```python
from selenium import webdriver
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
from bs4 import BeautifulSoup
import pandas as pd

def scrape_dynamic_site(url):
    """Use Selenium to render JS, then BeautifulSoup to parse."""
    driver = webdriver.Chrome(options=get_chrome_options())
    driver.get(url)

    # Wait for content to load
    WebDriverWait(driver, 15).until(
        EC.presence_of_element_located((By.CSS_SELECTOR, ".product-grid"))
    )

    # Click "Load More" until all products visible
    while True:
        try:
            load_more = driver.find_element(By.CSS_SELECTOR, "button.load-more")
            load_more.click()
            WebDriverWait(driver, 5).until(
                EC.staleness_of(load_more)
            )
        except Exception:
            break  # No more "Load More" button

    # Pass rendered HTML to BeautifulSoup (faster parsing)
    soup = BeautifulSoup(driver.page_source, "html.parser")
    driver.quit()

    # Extract with BeautifulSoup (familiar API)
    products = []
    for card in soup.select(".product-card"):
        products.append({
            "name": card.select_one(".title").get_text(strip=True),
            "price": card.select_one(".price").get_text(strip=True),
            "rating": card.select_one(".rating").get("data-score"),
            "url": card.select_one("a")["href"]
        })

    return pd.DataFrame(products)

df = scrape_dynamic_site("https://example.com/shop")
```

### Bài tập
1. Scrape JavaScript-rendered page (SPA)
2. Implement infinite scroll scraping
3. Fill form + submit + scrape results
4. Login to site → scrape authenticated content
5. Combine Selenium (render) + BeautifulSoup (parse)

---

## Level 3: Advanced (3 tuần) — Scale & Production

### Anti-bot Bypass Techniques
```python
import random
import time
from fake_useragent import UserAgent

# Random User-Agent
ua = UserAgent()
headers = {"User-Agent": ua.random}

# Random delays (human-like)
def human_delay(min_sec=1, max_sec=3):
    time.sleep(random.uniform(min_sec, max_sec))

# Proxy rotation
proxies_list = [
    "http://proxy1:8080",
    "http://proxy2:8080",
    "http://proxy3:8080",
]

def get_with_proxy(url):
    proxy = random.choice(proxies_list)
    response = requests.get(
        url,
        headers={"User-Agent": ua.random},
        proxies={"http": proxy, "https": proxy},
        timeout=10
    )
    return response

# Selenium: undetected-chromedriver
# pip install undetected-chromedriver
import undetected_chromedriver as uc

driver = uc.Chrome(headless=True)
driver.get("https://protected-site.com")
```

### Scrapy Framework (Large-scale)
```python
# pip install scrapy
# scrapy startproject myproject

# myproject/spiders/products_spider.py
import scrapy

class ProductsSpider(scrapy.Spider):
    name = "products"
    start_urls = ["https://example.com/products"]
    custom_settings = {
        "CONCURRENT_REQUESTS": 8,
        "DOWNLOAD_DELAY": 1,
        "ROBOTSTXT_OBEY": True,
        "USER_AGENT": "Mozilla/5.0 (compatible; MyBot/1.0)",
    }

    def parse(self, response):
        # Extract products
        for product in response.css("div.product-card"):
            yield {
                "name": product.css("h3.title::text").get(),
                "price": product.css("span.price::text").get(),
                "url": response.urljoin(product.css("a::attr(href)").get()),
            }

        # Follow pagination
        next_page = response.css("a.next-page::attr(href)").get()
        if next_page:
            yield response.follow(next_page, self.parse)
```

```bash
# Run spider
scrapy crawl products -o products.json
scrapy crawl products -o products.csv

# With settings override
scrapy crawl products -s CONCURRENT_REQUESTS=16 -o data.json
```

### Playwright (Modern Alternative)
```python
# pip install playwright
# playwright install

from playwright.sync_api import sync_playwright
import pandas as pd

def scrape_with_playwright(url):
    """Playwright: faster, more reliable than Selenium."""
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=True)
        page = browser.new_page()

        # Set viewport & user agent
        page.set_viewport_size({"width": 1920, "height": 1080})

        page.goto(url, wait_until="networkidle")

        # Wait for specific element
        page.wait_for_selector(".product-grid", timeout=10000)

        # Extract data
        products = page.query_selector_all(".product-card")
        data = []
        for product in products:
            data.append({
                "name": product.query_selector(".title").inner_text(),
                "price": product.query_selector(".price").inner_text(),
            })

        browser.close()
        return pd.DataFrame(data)

# Async version (faster for multiple pages)
import asyncio
from playwright.async_api import async_playwright

async def scrape_multiple(urls):
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=True)
        results = []

        for url in urls:
            page = await browser.new_page()
            await page.goto(url, wait_until="networkidle")
            content = await page.content()
            results.append(content)
            await page.close()

        await browser.close()
        return results
```

### Storing & Scheduling
```python
# Store to database
import pandas as pd
from sqlalchemy import create_engine

engine = create_engine("postgresql://user:pass@localhost/scraping_db")
df.to_sql("products", engine, if_exists="append", index=False)

# Store to S3
import boto3
from io import StringIO

s3 = boto3.client("s3")
csv_buffer = StringIO()
df.to_csv(csv_buffer, index=False)
s3.put_object(
    Bucket="data-lake",
    Key=f"scraped/products/{datetime.now().strftime('%Y%m%d')}.csv",
    Body=csv_buffer.getvalue()
)
```

```python
# Schedule with Airflow
from airflow.decorators import dag, task
from datetime import datetime

@dag(schedule="0 6 * * *", start_date=datetime(2024, 1, 1))
def scraping_pipeline():
    @task
    def scrape():
        df = scrape_products("https://example.com/products")
        df.to_csv(f"/data/products_{datetime.now():%Y%m%d}.csv")
        return len(df)

    @task
    def load_to_warehouse(row_count):
        # Load scraped data to Redshift/BigQuery
        pass

    rows = scrape()
    load_to_warehouse(rows)

scraping_pipeline()
```

### Legal & Ethical Considerations
```
✅ OK to scrape:
- Public data (no login required)
- robots.txt allows it
- Terms of Service don't prohibit
- Rate-limited (polite delays)
- For personal/research use

⚠️ Careful:
- Commercial use of scraped data
- Competitor data aggregation
- Personal data (GDPR, privacy)
- Behind login walls
- Overwhelming target server

❌ Don't:
- Ignore robots.txt blocks
- Bypass authentication/paywalls
- Scrape personal data without consent
- DDoS-level request rates
- Violate Terms of Service for profit

Best practices:
1. Check robots.txt first
2. Add delays (1-3 sec between requests)
3. Identify yourself (custom User-Agent with contact)
4. Cache results (don't re-scrape same content)
5. Respect rate limits
```

### Bài tập
1. Build Scrapy spider với pagination
2. Implement proxy rotation (free proxy list)
3. Scrape with Playwright (async, multiple pages)
4. Store scraped data → PostgreSQL + S3
5. Schedule scraping job (cron hoặc Airflow)

### Resources (Web Scraping)
| Resource | Link | Type |
|---|---|---|
| BeautifulSoup Docs | [crummy.com/software/BeautifulSoup/bs4/doc](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) | Free |
| Selenium Python | [selenium-python.readthedocs.io](https://selenium-python.readthedocs.io/) | Free |
| Scrapy Docs | [docs.scrapy.org](https://docs.scrapy.org/) | Free |
| Playwright Python | [playwright.dev/python](https://playwright.dev/python/) | Free |
| quotes.toscrape.com | [quotes.toscrape.com](https://quotes.toscrape.com/) | Free (practice) |

### Web Scraping Alternatives
| Tool | Type | Strengths | Weaknesses |
|---|---|---|---|
| **BeautifulSoup** | Python library | Simple, fast parsing | Static only |
| **Selenium** | Browser automation | JS rendering, interactions | Slow, heavy |
| **Playwright** | Modern browser automation | Faster, async, multi-browser | Newer ecosystem |
| **Scrapy** | Framework | Scale, speed, middleware | Steeper learning |
| **Puppeteer** | Node.js | Chrome-native, fast | JavaScript only |
| **Crawlee** | Node.js framework | Modern, TypeScript, queue | Node.js ecosystem |
| **Octoparse** | No-code | Visual, no programming | Limited, paid |
