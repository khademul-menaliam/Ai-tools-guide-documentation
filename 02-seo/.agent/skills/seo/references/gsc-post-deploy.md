# Post-Deployment & Search Console Guide

This reference details post-launch procedures, Search Console submission workflows, indexing requests, and performance monitoring protocols.

---

## 1. Domain Ownership Verification

### Google Search Console (GSC)
1. Add property in [Google Search Console](https://search.google.com/search-console).
2. Preferred Method: **DNS TXT Record** (covers all subdomains, http/https protocols).
   - Record Type: `TXT`
   - Name/Host: `@` or leave empty
   - Value: `google-site-verification=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`
3. Fallback Method: HTML File upload to site root or HTML Meta Tag in root layout:
   ```html
   <meta name="google-site-verification" content="VERIFICATION_KEY" />
   ```

### Bing Webmaster Tools
1. Access [Bing Webmaster Tools](https://www.bing.com/webmasters).
2. Fast Option: Import verified properties directly from Google Search Console.
3. Manual Option: Add DNS TXT record or HTML file (`BingSiteAuth.xml`).

---

## 2. Sitemap Submission Protocol

Once domain ownership is verified:

1. **Submit XML Sitemap in GSC**:
   - Navigate to **Index -> Sitemaps**.
   - Enter sitemap URL path: `sitemap.xml` (or full URL `https://example.com/sitemap.xml`).
   - Click **Submit**. Verify status reads **"Success"**.

2. **Submit in Bing Webmaster Tools**:
   - Navigate to **Sitemaps -> Submit Sitemap**.
   - Enter full URL `https://example.com/sitemap.xml`.

---

## 3. Immediate Indexing & URL Inspection

For key landing pages, high-priority product launches, or major page redesigns:

1. **URL Inspection Tool (GSC)**:
   - Paste the target URL into the top search bar in Search Console.
   - Click **Test Live URL** to confirm indexability, response code (200 OK), and rendering.
   - Click **Request Indexing** to queue the URL for priority crawl.

2. **Bing IndexNow API**:
   - For fast indexing on Bing and Yandex, submit updated URLs programmatically via IndexNow protocol.

---

## 4. Post-Deployment Audit Checklist

Perform these checks within **24 to 48 hours post-deployment**:

| Category | Verification Item | Tool / Method | Expected Result |
| :--- | :--- | :--- | :--- |
| **Crawl Status** | `robots.txt` availability | Visit `/robots.txt` in browser | HTTP 200, valid disallow rules |
| **Sitemap** | `sitemap.xml` validity | Visit `/sitemap.xml` | HTTP 200, valid XML, absolute URLs |
| **Canonical Headers** | Canonical tag verification | Inspect DOM / curl `-I` | Canonical points to correct production URL |
| **Indexing Status** | Coverage report check | GSC -> Indexing -> Pages | No unexpected "Excluded by noindex" |
| **Structured Data** | Schema validation | Rich Results Test | 0 errors, valid schema types |
| **Core Web Vitals** | Performance baseline | PageSpeed Insights | Green/Pass on LCP, INP, CLS |

---

## 5. Ongoing Crawl & Health Monitoring

- **Weekly Check**: Review **GSC Coverage / Page Indexing** report for new crawl errors, 404s, or canonical mismatches.
- **Monthly Check**: Review **Performance** report (Clicks, Impressions, CTR, Average Position) to identify underperforming keywords or lost traffic.
- **Core Web Vitals (CWV)**: Monitor field data in GSC under **Experience -> Core Web Vitals** for real-user LCP (≤ 2.5s), INP (≤ 200ms), and CLS (≤ 0.1) metrics.
