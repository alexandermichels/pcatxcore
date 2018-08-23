# PCATx CORE

## Table of Contents
---------------------
* Introduction
  * Milestone One
  * Milestone Two
  * Milestone Three
* Milestone One
  * Query Formulator
  * Web Crawler
  * Parser
  * WebResourceManager
* Milestone Two
  * Classifier
  * ProfileManager
  * PCATx_CORE
* Milestone 3
  * Reverb
  * StreamMiner

## Introduction
----------------

PCATx CORE is currently being developed as a web crawling and artificial intelligence framework for Praedicat, Inc. by the 2018 RIPS team at UCLA's Institute for Pure and Applied Mathematics. Our goal is to utilize Natural Language Processing techniques and Artificial Intelligence to automate data collection for the InsurTech company.

Analysts at Praedicat, Inc., need to manually associate each company with a set of business activities. Using this information, analysts attempt to find evidence linking businesses with potentially dangerous practices, such as the use of hazardous chemicals. With a plethora of companies and business activities, manual search is a tedious process. Further, the analysis is generally performed on unstructured, non-uniform, and sporadic Internet sites which makes it difficult to algorithmically search for the information needed and complex to determine the semantic meaning of the documents even when they are found. Our work attempts to tackle these problems by building a web crawler which procures information and comparing the statements found in the documents to a credible knowledge base. Based on computational fact checking, we are hoping this approach will lead to better classification of unstructured text information on the Internet.

For the ability to conform to the [Robots Exclusion Standard](https://en.wikipedia.org/wiki/Robots_exclusion_standard), we have also developed a simple set of functions in [Robots-Exclusion-Standard-Handler](https://github.com/alexandermichels/Robots-Exclusion-Standard-Handler).

To learn more about the concepts we explored while developing this project including Computational Fact-Checking, Information Extraction, and Knowledge Graphs, all of the papers and resources the team consulted over the course of their work can be found here: [RIPS Readings](https://github.com/alexandermichels/RIPSReadings)

<div align="center">
  <img alt="Diagram of PCATx Core Architecture" src="/img/EntireArchitecture.png">
</div>

Our final presentation at IPAM also elaborates on our work: <a href="/docs/PCATFinalPresentation.pdf">View PDF</a>

#### Milestone One

Milestone One is the web crawling part of our work. It consists of designing and implementing an intelligent web crawler that is able to find relevant information for Praedicat, Inc.'s analysts.

<div align="center">
  <img alt="Diagram of Milestone 1 of the PCATx Core Architecture" src="/img/Milestone1.png">
</div>

#### Milestone Two

Milestone 2 is focused on classification and aggregation of information. In this portion of the architecture, we are focused on filtering the data and putting it together in a way which provides the most value to Praedicat analysts.

<div align="center">
  <img alt="Diagram of Milestone 3 of the PCATx Core Architecture" src="/img/Milestone3.png">
</div>

#### Milestone Three

Milestone 3 seeks to put the information we are collecting into a [knowledge graph](https://en.wikipedia.org/wiki/Semantic_network) which can be used to algorithmically check the factual validity of statements found and direct the system's search for new facts using relational machine learning.

<div align="center">
  <img alt="Diagram of Milestone 2 of the PCATx Core Architecture" src="/img/Milestone2.png">
</div>

## Milestone One
-----------------

#### Query Formulator

The **Query Formulator** portion of our architecture's job is to algorithmically design queries for Google which give us better results. This can be done in a variety of ways, by adding aliases, making certain words optional, making some words mandatory, and even specifying the file type.

<div align="center">
  <img alt="Diagram of PCATx Core's Query Formulator" src="/img/QueryFormulator.png">
</div>

#### Web Crawler

**Web Crawler** has the job of navigating the Internet for our architecture. It is given the starting point of the Google results page generated by the query from the **Query Formulator**, but from there the Web Crawler needs to decide which websites to avoid, how far into Google it should traverse, and which links on the pages it finds it should follow.

<div align="center">
  <img alt="Diagram of PCATx Core's Web Crawler" src="/img/WebCrawler.png">
</div>

* [webcrawlAll.py](/webcrawlAll.py) --- [Documentation](docs/webcrawllAll.md)

webcrawlAll is a set of modules to crawl various credible websites (TRI, EPA and SEC). Each of these modules is accessible from the module: `crawlerWrapper` which specifies various *engines*.
  * `google`: calls `search_google`.
  * `sec10k`: [*Deprecated*] constructs the url with `urlmaker_sec` and calls the `search_sec10k` for that CIK code.
  * `sec10kall`: engine is related to `sec10k`, but it runs for a CIK dict rather than a single CIK.
  * `secsic10k`: gets the 10-Ks related to that company for the SEC group.
  * `generalSEC`: make a general query to the SEC website, uses `urlmaker_sec`.
  * `sitespecific`:  Uses *httrack* to download index and PDFs from the input website.
  * `google-subs`: Pulls the subsidaries out of Google
  * `everything-all`: Pulls out the 10Ks, 8Ks, and E-21s for a CIK dictionary

[Site_Crawler_Parser_All.py](Site_Crawler_Parser_All.py) --- [Documentation](docs/Site_Crawler_Parser_All.md)

* A crawler and parser for **Wikipedia pages** that can parse information in a Wikipedia infobox into a Python Dictionary and the article text as a string.
* A crawler and parser for **all-level subsidiaries returned by Google** that can parse subsidiary names on a search result page into a Python Dictionary.
* A crawler and parser for **TRI facility reports** that can parse a facility information table into a Python Dictionary and a chemical usage report into a comma-separated values (CSV) file.
* A crawler and parser for **EWG search results** that can parse name of products by a company and ingredients in a product into a Python Dictionary.
* A crawler and parser for **NPIRS search results** that can parse names of manufacturers that use a certain hazard into a Python Dictionary.

#### Parser

It is not enough to find a set of web pages, we need to extract the information from each of the web pages we find. This is done by the **Parser**. The **Parser** is a general purpose web scraper for getting the visible text from a web page. We also integrated some site-specific web scrapers to get more structured data into [Site_Crawler_Parser_All](docs/Site_Crawler_Parser_All.md).

<div align="center">
  <img alt="Diagram of PCATx Core's Parser" src="/img/Parser.png">
</div>

[PCATParser.py](PCATParser.py) --- [Documentation](docs/PCATParser.md)

Once a list of relevant URLs is generated by the **Web Crawler**, it is pipelined to the **Parser**. The **Parser** transforms the contents of the web pages to text documents that are human-readable and analyzable via natural language processing techniques. Since, (1)~the text is formatted in various ways and (2) not all of the text information on a web page is desirable (e.g. advertisements, headers and footers), a parser should be generalized, such that it can be used to parse highly variable data crawled by the web-crawler. We designed our parser to retrieve visible text on the web pages.

#### WebResourceManager

Once all of the information is extracted from each website, we need to track it so that each assertion can be traced back to its source. This is where **WebResourceManager** comes into play. **WebResourceManager** associates UUIDs with each web resource we find, and saves the source file (HTML/PDF) as well as extracted information alongside the generating query and URL in a queryable format.

<div align="center">
  <img alt="Diagram of PCATx Core's Classifier" src="/img/WebResourceManager.png">
</div>

* [WebResourceManager.py](WebResourceManager.py) --- [Documentation](knowledge_management/docs/WebResourceManager.md)

WebResourceManager is a class for helping manage a database of web resources. WebResourceManager creates a UUID (Universally Unique Identifier) for the web resource, saves the information in a JSON (labeled < UUID >.json), and builds maintain a dictionary from  URL to UUID. Using this uniform data storage system and a simple API, WebResourceManager makes storing and querying the contents and source files (such as HTML and PDF) of web resources much simpler.

## Milestone Two
-----------------

#### Classifier

Our Web Crawling Framework in Milestone One is able to produce a large volume of data, but much of it is not useful for practical applications. Thus, our **Classifier** attempts to filter out the "junk" text from the information found, delivering only the most relevant information to the rest of the system.

<div align="center">
  <img alt="Diagram of PCATx Core's Classifier" src="/img/Classifier.png">
</div>

* [Self-Supervised Classifier](knowledge_management/SelfSupervisedClassifier.py) ---[Documentation](knowledge_management/docs/SelfSupervisedClassifier.md)

A model for classifying sentences as relevant or not. The approach was inspired by [Banko et al.'s 2007 "Open Information Extraction from the Web"](https://www.aaai.org/Papers/IJCAI/2007/IJCAI07-429.pdf) which used a self-supervised learner to perform open information extraction. We are taking much the same approach to relevancy classification by having the learner tag certain sentences as relevant or irrelevant based on keyword input and then Doc2Vec is trained on these tagged sentences to learn more complex features.

#### ProfileManager

Our architecture is focused on accumulating information for businesses and corporate entities,

<div align="center">
  <img alt="Diagram of PCATx Core's Classifier" src="/img/ProfileManager.png">
</div>

* [ProfileManager.py](ProfileManager.py) --- [Documentation](knowledge_management/docs/ProfileManager.md)

ProfileManager is a class designed for the aggregation of information related to corporate entities to support building business profiles. It uses the United States Securities and Exchange Commission (SEC) Central Index Key (CIK) to act as universally unique identifiers (UUIDs) and allows the user to compile a variety of information on corporate entities in an easy to use and query format because each profile is a dictionary. Assisting the accessibility of information, **Profile Manager** includes a series of mappings from CIK codes to names and back, names to aliases, and mappings from industry codes (namely The North American Industry Classification System (NAICS) and Standard Industrial Classification (SIC) codes) and descriptions of them. The hope to provide for a flexible data solution for complex business oriented applications.

#### PCATx_CORE

* [PCATx_CORE.py](PCATx_CORE.py) --- [Documentation](docs/PCATx_CORE.md)

**PCATx_CORE** drives the frameworks described in Milestones One and Two, providing driver functions for supervised and unsupervised runs of the architecture.

## Milestone Three
-----------------

#### Triple Construction

<div align="center">
  <img alt="Diagram of PCATx Core's Computational Triple Construction Component" src="/img/TripleConstruction.png">
</div>

We chose to use [Reverb](http://reverb.cs.washington.edu/) to construct our subject-predicate-object triples

#### Computational Fact-Checking

<div align="center">
  <img alt="Diagram of PCATx Core's Computational Fact-Checking Component" src="/img/ComputationalFact-Checking.png">
</div>

We are using a novel computational fact-checking algorithm that we designed called StreamMiner. It draws much of its inspiration from [Knowledge Stream](https://arxiv.org/abs/1708.07239) and [PredPath](https://arxiv.org/abs/1510.05911).
