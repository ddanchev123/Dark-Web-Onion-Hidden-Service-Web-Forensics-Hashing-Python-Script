# Advanced Onion Crawler

This document provides a comprehensive guide to using the `crawler_60.py` Python script, a sophisticated web crawler designed specifically for exploring the Tor network (onion sites).  It includes detailed explanations of its features, configuration options, usage instructions, and troubleshooting tips.

## Table of Contents

1.  [Introduction](#introduction)
2.  [Features](#features)
3.  [Requirements](#requirements)
4.  [Installation](#installation)
5.  [Configuration](#configuration)
    *   [Command-Line Arguments](#command-line-arguments)
    *   [Configuration File (Optional)](#configuration-file-optional)
6.  [Usage](#usage)
    *   [Input File](#input-file)
    *   [Exclusion File](#exclusion-file)
    *   [Running the Crawler](#running-the-crawler)
7.  [Database Setup](#database-setup)
    *   [Creating the Database](#creating-the-database)
    *   [Creating the Table](#creating-the-table)
8.  [Code Structure and Explanation](#code-structure-and-explanation)
    *   [Class: `AdvancedWebCrawler`](#class-advancedwebcrawler)
        *   [`__init__`](#__init__)
        *   [`create_database_connection`](#create_database_connection)
        *   [`check_tor_connectivity`](#check_tor_connectivity)
        *   [`rotate_user_agent`](#rotate_user_agent)
        *   [`_is_allowed`](#_is_allowed)
        *   [`fetch_onion_content`](#fetch_onion_content)
        *   [`crawl`](#crawl)
        *   [`_extract_links`](#_extract_links)
        *   [`_clean_url`](#_clean_url)
        *   [`_hash_css_js`](#_hash_css_js)
        *   [`load_urls_from_file`](#load_urls_from_file)
        *   [`load_excluded_urls`](#load_excluded_urls)
        *   [`run`](#run)
        *   [`__del__`](#__del__)
    *   [Class: `HashingUtils`](#class-hashingutils)
        *   [`calculate_hashes`](#calculate_hashes)
        *   [`generate_unique_hash`](#generate_unique_hash)
    *   [Hashing Modules](#hashing-modules)
        *   [`onion_crawler.image_hashing`](#onion_crawlerimage_hashing)
        *   [`onion_crawler.text_hashing`](#onion_crawlertext_hashing)
        *   [`onion_crawler.structural_hashing`](#onion_crawlerstructural_hashing)
        *   [`onion_crawler.ui_element_hashing`](#onion_crawlerui_element_hashing)
9.  [Logging](#logging)
10. [Error Handling and Retries](#error-handling-and-retries)
11. [Modular Hashing](#modular-hashing)
    *   [Image Hashing](#image-hashing)
    *   [Text Hashing](#text-hashing)
    *   [Structural Hashing](#structural-hashing)
    *   [UI Element Hashing](#ui-element-hashing)
12. [Database Schema](#database-schema)
13. [Troubleshooting](#troubleshooting)
14. [Contributing](#contributing)
15. [License](#license)

## 1. Introduction

The `crawler_60.py` script is a Python-based web crawler specifically designed to navigate and extract information from websites hosted on the Tor network (onion sites). It leverages the Tor proxy to anonymize its requests and provides advanced features for content analysis, including modular hashing techniques for identifying duplicate or similar content.  The crawler is designed to be robust, handling common issues like connection errors, timeouts, and duplicate content.  It also incorporates detailed logging to aid in debugging and monitoring.

## 2. Features

*   **Tor Network Compatibility:**  Designed to work seamlessly with the Tor network, routing all traffic through the Tor proxy.
*   **Recursive Crawling:**  Crawls websites recursively up to a specified depth, following links to discover new content.
*   **Content Hashing:**  Calculates MD5, SHA1, and SHA256 hashes of web page content for duplicate detection.
*   **JavaScript Rendering (Optional):**  Supports rendering JavaScript using `requests-html` to crawl dynamic websites (requires `requests-html` and Chromium).
*   **User-Agent Rotation:**  Rotates through a list of user agents to avoid detection and blocking.
*   **Exclusion List:**  Allows you to exclude specific URLs from being crawled.
*   **Database Storage:**  Stores crawled data in a MySQL database, including content hashes, URLs, and other metadata.
*   **Error Handling and Retries:**  Implements robust error handling with retry mechanisms for failed requests.
*   **Logging:**  Provides detailed logging of crawler activity, including errors, warnings, and informational messages.
*   **Modular Hashing:** Includes modules for image, text, structural, and UI element hashing.
*   **Image Hashing:** Calculates perceptual hashes (pHash, dHash) and color histograms for images.
*   **Text Hashing:** Generates sentence embeddings and document embeddings using Sentence Transformers.
*   **Structural Hashing:** Hashes the DOM tree, CSS styles, and JavaScript functionality.
*   **UI Element Hashing:** Hashes key UI elements, button text, and form structures.
*   **Progress Bar:** Uses `tqdm` to display a progress bar during crawling.

## 3. Requirements

*   **Python 3.6+:**  The script is written in Python 3 and requires a compatible version.
*   **requests:**  For making HTTP requests.
*   **Beautiful Soup 4 (bs4):**  For parsing HTML content.
*   **urllib3:** For making HTTP requests with advanced features.
*   **requests-html (Optional):**  For rendering JavaScript (if `render_js` is enabled).
*   **mysql-connector-python:**  For connecting to a MySQL database.
*   **sentence-transformers:** For generating sentence embeddings.
*   **nltk:** For natural language processing tasks.
*   **Pillow (PIL):** For image processing.
*   **imagehash:** For calculating image hashes.
*   **tqdm:** For displaying progress bars.
*   **Tor:**  Tor must be installed and running to route traffic through the Tor network.

You can install the necessary Python packages using pip:

```bash
pip install requests beautifulsoup4 urllib3 requests-html mysql-connector-python sentence-transformers nltk Pillow imagehash tqdm
