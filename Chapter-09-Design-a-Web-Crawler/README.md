# Chapter 9: Design a Web Crawler

## 1. Introduction

A **Web Crawler**, also known as a Spider or Robot, is an automated program used to discover and collect web content, such as web pages, images, and videos.

**Main Purpose:** Collect web content for search engine indexing.

**Example:** Googlebot is Google's web crawler.
<img width="1344" height="768" alt="image" src="https://github.com/user-attachments/assets/b4586376-0d47-49c9-bf94-c7e0f22e9ba2" />


## 2. Applications of Web Crawlers

1. **Search Engine Indexing:** Collect web pages to create searchable indexes (e.g., Googlebot).
2. **Web Archiving:** Preserve web data for future access (e.g., US Library of Congress).
3. **Web Mining:** Extract useful information from websites for analysis (e.g., financial analysis of shareholder reports).
4. **Web Monitoring:** Detect copyright and trademark infringements.

## 3. Design Challenges

### 1. Scalability
- Handle billions of web pages.
- Use parallel processing and multiple crawler workers.
- Efficiently manage large amounts of data.

### 2. Robustness
- Handle bad HTML and broken links.
- Recover from crashes and server failures.
- Protect against malicious URLs.

### 3. Politeness
- Avoid sending excessive requests to websites.
- Maintain appropriate delays between requests.
- Respect `robots.txt` rules and website crawling policies.

### 4. Extensibility
- Support new content types with minimal changes.
- Allow easy addition of new features.
- Maintain a flexible system architecture.
<img width="675" height="379" alt="image" src="https://github.com/user-attachments/assets/3b2d72c7-564b-4216-97e0-ab010c6db0ec" />


## 4. Daily Life Example: Library
<img width="1290" height="860" alt="image" src="https://github.com/user-attachments/assets/1bcc9fca-68c8-4133-aefd-63fb36ca1e3c" />


Imagine a librarian working in a huge library containing millions of books.

- **Library:** Internet.
- **Books:** Web Pages.
- **Librarian:** Web Crawler.
- **Book Register:** Search Engine Index.
- **Book Directions:** Hyperlinks.

Just like a librarian discovers books, collects information, and organizes them in a register, a Web Crawler discovers websites, collects their content, and helps search engines organize information.

**Example:** When Googlebot visits a website, it discovers links, follows them, collects web page content, and helps Google build its search index.

## Quick Revision

| Concept | Purpose |
|---|---|
| Web Crawler | Discover and collect web content |
| Search Engine Indexing | Create searchable indexes |
| Web Archiving | Preserve web data |
| Web Mining | Extract useful information |
| Web Monitoring | Detect copyright infringement |
| Scalability | Handle billions of pages |
| Robustness | Handle errors and failures |
| Politeness | Avoid overloading websites |
| Extensibility | Support new features easily |



# Step 1: Understanding the Problem

## Requirements
<img width="1510" height="1094" alt="image" src="https://github.com/user-attachments/assets/14380c69-4eb2-4fb7-90af-f1ede99bbf39" />


1. **Crawl 1 Billion Web Pages per Month**
   - Average: 400 pages/second.
   - Peak: 800 QPS (Queries Per Second).

2. **Collect HTML-Only Content**
   - Collect HTML content, including page structure and text.
   - Ignore images, videos, and other media files.

3. **Track New and Updated Pages**
   - Discover new web pages.
   - Detect changes in existing pages and crawl them again.

4. **Ignore Duplicate Content**
   - Identify identical content across different URLs.
   - Avoid storing duplicate data.
   - Use content hashing for duplicate detection.

5. **Store Crawled Data for 5 Years**
   - Estimated storage: approximately 30 PB.
   - Requires a distributed storage system.

## Key Challenges

- High crawling speed and scalability.
- Detecting new and updated pages.
- Duplicate content detection.
- Managing massive storage for 5 years.



## How Does a Web Crawler Discover New Websites?

A Web Crawler discovers new websites and pages through different sources:

1. **Existing Website Links**
   - Crawlers extract links from already crawled web pages.
   - New URLs are added to the URL Frontier for crawling.

2. **Sitemaps**
   - Websites provide `sitemap.xml` files containing their URLs.
   - Crawlers use sitemaps to discover new pages.

3. **Seed URLs**
   - Search engines provide initial URLs to start crawling.
   - Crawlers follow links from these URLs to discover more websites.

4. **Website Submissions**
   - Website owners can submit their websites to search engines for discovery.

**Example:** If a crawler visits `example.com` and finds a link to `newwebsite.com`, it can add that URL to the URL Frontier and crawl it later.

**Key Point:** Crawlers discover new websites mainly by following links from existing websites and reading sitemaps.


# Step 3: Deep Dive into Key Components

## 1. DFS vs BFS

<img width="2000" height="1120" alt="image" src="https://github.com/user-attachments/assets/75f2b8b0-c347-4749-8c0b-988230d8ee14" />


The web can be represented as a directed graph:
- **Nodes:** Web pages.
- **Edges:** Hyperlinks between pages.

**DFS (Depth-First Search):**
- Explores deeply along one path before backtracking.
- Not ideal for web crawling because web depth can be very large.

**BFS (Breadth-First Search):**
- Visits pages level by level.
- Usually preferred for web crawling.

**Limitation:** Standard BFS does not consider URL priority; all pages are treated equally.

## 2. URL Frontier

URL Frontier manages which URLs should be downloaded and when.

### A. Politeness
<img width="817" height="362" alt="image" src="https://github.com/user-attachments/assets/f8a4a1aa-76a3-4a0f-9225-a6f454bf237e" />

Ensures the crawler does not overload websites.

- Send only one request per host at a time.
- Add delays between requests.
- Use separate queues for different hosts.

**Components:**
1. **Queue Router:** Routes URLs to queues based on hostname.
2. **Mapping Table:** Maps each hostname to a queue.
3. **Queue Selector:** Assigns queues to worker threads.
4. **Worker Threads:** Download URLs sequentially from their assigned queues.

### B. Priority
<img width="1669" height="880" alt="image" src="https://github.com/user-attachments/assets/d88ecb7d-280e-408f-8106-11045f7ef7c8" />


Assigns higher priority to important pages.

- **Prioritizer:** Calculates URL priorities.
- **Front Queues:** Organize URLs by priority.
- **Queue Selector:** Selects higher-priority queues more frequently.
- **Back Queues:** Manage host-based queues for politeness.

Priority factors:
- PageRank
- Page importance
- Update frequency

### C. Freshness

Determines when pages should be crawled again.

- Frequently updated pages are recrawled more often.
- Rarely updated pages are recrawled less frequently.
- Page importance also affects recrawling frequency.

## 3. HTML Downloader

Downloads web pages from URLs provided by the URL Frontier.

**Robots.txt Compliance:**
- Respect website crawling rules defined in `robots.txt`.

**Performance Optimizations:**
1. **Distributed Crawling:** Use multiple servers to increase crawling speed.
2. **DNS Cache:** Avoid repeated DNS lookups.
3. **Geographical Distribution:** Place crawler servers in different locations for faster downloads.
4. **Short Timeout:** Avoid waiting too long for unresponsive servers.

## 4. Robustness

Ensures the crawler continues working despite errors and failures.

1. **Consistent Hashing:** Distributes load among servers.
2. **Error Handling:** Handles exceptions and download failures.
3. **Data Validation:** Ensures content integrity and correctness.

## 5. Extensibility
<img width="617" height="423" alt="image" src="https://github.com/user-attachments/assets/362901cd-18fc-49bf-800a-3f7575d4d5f5" />


Allows adding new features or modules without changing the entire system.

Examples:
- PNG Downloader
- Web Monitor
- Copyright Violation Detector

## 6. Avoiding Problematic Content

1. **Duplicate Content:** Detect duplicates using hash comparisons.
2. **Spider Traps:** Avoid infinite crawling loops using URL length limits and crawling limits.
3. **Data Noise:** Filter irrelevant content such as advertisements and spam.

## Quick Revision

| Concept | Purpose |
|---|---|
| BFS | Crawl pages level by level |
| DFS | Explore deeply along one path |
| Politeness | Avoid overloading websites |
| Priority | Crawl important pages first |
| Freshness | Recrawl updated pages |
| Robots.txt | Follow website crawling rules |
| DNS Cache | Reduce repeated DNS lookups |
| Robustness | Handle failures and errors |
| Extensibility | Add new modules easily |
| Spider Traps | Prevent infinite crawling |
| Data Noise | Remove irrelevant content |



# Step 4: Wrap Up

## 1. Key Takeaways

1. **Scalability:** Handle billions of web pages efficiently.
2. **Robustness:** Handle errors and system failures.
3. **Politeness:** Prevent overloading web servers.
4. **Extensibility:** Support new features and modules.
5. **Priority:** Crawl important pages first.
6. **Storage and Error Handling:** Ensure efficient data storage and reliable processing.

## 2. Additional Considerations

1. **Server-Side Rendering (SSR):**
   - Handle dynamic content generated by JavaScript or AJAX.
   - Support JavaScript rendering when required.

2. **Anti-Spam Measures:**
   - Filter low-quality, spam, and irrelevant pages.
   - Maintain search index quality.

3. **Database Sharding:**
   - Distribute large datasets across multiple database servers.
   - Use replication and sharding to improve scalability and availability.

4. **Horizontal Scaling:**
   - Use stateless servers.
   - Distribute crawling jobs across multiple servers.
   - Add more servers as workload increases.

5. **Analytics:**
   - Collect crawling statistics.
   - Analyze crawled pages, failures, and response times.
   - Improve system performance and efficiency.

## Quick Revision

| Concept | Purpose |
|---|---|
| Scalability | Handle large-scale crawling |
| Robustness | Handle errors and failures |
| Politeness | Protect web servers |
| Priority | Crawl important pages first |
| SSR | Handle dynamic JavaScript content |
| Anti-Spam | Filter low-quality pages |
| Sharding | Distribute database storage |
| Horizontal Scaling | Add more servers |
| Analytics | Monitor crawling performance |
