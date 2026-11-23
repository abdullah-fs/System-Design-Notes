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
