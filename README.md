[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-718a45dd9cf7e7f842a935f5ebbe5719a5e09af4491e668f4dbf3b35d5cca122.svg)](https://classroom.github.com/online_ide?assignment_repo_id=14229458&assignment_repo_type=AssignmentRepo)
# Homework3: Crawling, Merging, Vertical Search

Code submitted by : SAUMYA GUPTA | CS6200 | 002299840

## Objective
Implement your own index that would replace the one from elasticsearch used in HW1, then index the document collection used in HW1. Your index should be able to handle large numbers of documents and terms without using excessive memory or disk I/O.

This assignment involves writing two programs:

1. Task 1 - Crawler
2. Task 2 - Link Graph
3. Task3: Merging team indexes
3. Task4: Vertical Search (MS students only)

## Notes

This Homework combines individual responsibility with team collaboration. While you will work closely with your team members, your individual effort in developing the crawler, collecting documents, and following the politeness policy is crucial for the project's success.


## Links
- [Assignment Details](https://course.ccs.neu.edu/cs6200f20/assignments/3.html)
- [Report Template Document](https://docs.google.com/document/d/1jvxts0fFDExHlTEJqGOz3m0NP0YQKVl-fFKWmO5pxjE/edit?usp=sharing)

## Task Description

### CS6200 Information Retrieval
**Homework 3: Crawling, Merging, Vertical Search**

#### Objective
In this assignment, you will work with a team to create a vertical search engine using Elasticsearch. Please read these instructions carefully: although you are working with teammates, you will be graded individually for most of the assignment.

You will write a web crawler, and crawl Internet documents to construct a document collection focused on a particular topic. Your crawler must conform strictly to a particular politeness policy. Once the documents are crawled, you will pool them together.

Form a team of three students with your classmates. Your team will be assigned a single query with few associated seed URLs. You will each crawl web pages starting from a set of common seeds and different seed URLs. When you have each collected your individual documents, you will pool them together, index them and implement search.

---

### Task 1: Crawling Documents

Each individual is responsible for writing their own crawler and crawling from their own seed URLs.

- Set up Elasticsearch with your teammates to have the same cluster name and index name
- Your crawler will manage a **frontier** of URLs to be crawled, initially containing just your seed URLs
- Crawl at least **30,000 documents** (10,000 for undergrads), starting from seed URLs
- Choose the next URL using a **best-first strategy** (see Frontier Management below)
- Your crawler must strictly conform to the **politeness policy** detailed below
- Only crawl **HTML documents**
- Find all outgoing links, canonicalize them, and add new ones to the frontier
- For each page, store the following fields in Elasticsearch:
  1. ID
  2. URL
  3. HTTP headers
  4. Page contents cleaned (with term positions)
  5. Raw HTML
  6. List of all in-links and out-links

Once crawling is done, merge indexes with teammates so all members end up with the merged index.

---

### Politeness Policy

Your crawler must strictly observe this policy at all times, including during development and testing.

- Make **no more than one HTTP request per second** from any given domain
- You may crawl multiple pages from different domains simultaneously
- Before crawling any domain, fetch its **robots.txt** file and strictly obey it
- Use a third-party library to parse robots.txt

---

### Frontier Management

The frontier stores pages to be crawled, including the canonicalized URL and in-link count.

Prioritization criteria:
1. Seed URLs crawled first
2. Must use **BFS wave number** as baseline graph traversal
3. Prefer pages with higher in-link counts
4. Prefer URLs with matching keywords in link or anchor text
5. Prefer URLs extracted from a relevant page
6. Prefer certain domains
7. Prefer recent URLs
8. If multiple pages have maximal in-link counts, choose the one longest in the queue
9. If the next page is at a recently crawled domain, crawl from a different domain instead

---

### URL Canonicalization

Apply the following rules to all URLs encountered:

- Convert scheme and host to lowercase
- Remove port 80 from HTTP URLs and port 443 from HTTPS URLs
- Make relative URLs absolute
- Remove fragments beginning with `#`
- Remove duplicate slashes

---

### Document Processing

Once a page is downloaded:

- Extract all links in `<a>` tags, canonicalize, and update frontier
- Extract document text stripped of HTML, JavaScript, and CSS
- Store entire HTTP response separately

**Document format:**
```xml
<DOC>
  <DOCNO>http://www.example1.com/something.html</DOCNO>
  <HEAD>The page title</HEAD>
  <TEXT>The body text from the document</TEXT>
  <OUTLINK>http://www.example2.com/something.html</OUTLINK>
</DOC>
```

---

### Task 2: Link Graph

Write a link graph reporting all out-links and in-links from each crawled URL.

**Option 1:** Store canonical links as `inlinks` and `outlinks` fields in Elasticsearch per document

**Option 2:** Maintain a separate links file where each line is a tab-separated list: first URL is the crawled document, remaining URLs are out-links

---

### Task 3: Merging Team Indexes

- Merge individual crawls into one Elasticsearch index
- Merging must happen as **independent agents**, not in a master-slave manner
- All team members must be connected and run merging code simultaneously

---

### Task 4: Vertical Search *(MS students only)*

- Add all 90,000 documents to an Elasticsearch index using canonical URL as document ID
- Create a simple HTML page to run queries against the index
- Result list must contain at minimum the URL of the crawled page
- Run several queries and evaluate result quality

---

### Extra Credit

| # | Task | Description |
|---|------|-------------|
| EC1 | Crawl more documents | Expand team crawl to 180,000 documents |
| EC2 | Crawl into merged index | Crawl directly into a distributed ES index dynamically |
| EC3 | Frontier Management | Experiment with different URL selection techniques |
| EC4 | Speed Improvements | Optimize crawler speed without violating politeness policy |
| EC5 | Search Interface | Improve search UI with snippets, layout, or custom operators |
