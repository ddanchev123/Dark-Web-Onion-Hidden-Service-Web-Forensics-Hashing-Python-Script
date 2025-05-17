# Advanced Onion Crawler

## Overview

The Advanced Onion Crawler is a powerful and versatile Python web crawler designed specifically for exploring the depths of the Tor network and indexing `.onion` websites. This crawler goes beyond basic link extraction, incorporating advanced techniques for content analysis, data storage, and ethical web scraping. It leverages a modular architecture, allowing for easy extension and customization to suit various research and analytical needs.

## Features

*   **Tor Network Integration:** Seamlessly operates within the Tor network, ensuring anonymity and access to `.onion` domains.
*   **Configurable Crawling Depth:** Control the breadth of the crawl by setting the maximum recursion depth.
*   **JavaScript Rendering:** Utilizes `requests-html` to render JavaScript-heavy websites, capturing dynamically generated content.
*   **Content Hashing:** Employs multiple hashing algorithms (MD5, SHA1, SHA256) for content identification and duplicate detection.
*   **Image Hashing:** Extracts and hashes images using perceptual hashing (pHash, dHash) and color histograms for visual content analysis.
*   **Text Analysis:** Integrates `sentence-transformers` for generating sentence and document embeddings, enabling semantic content understanding.
*   **Structural Hashing:** Computes hashes of the DOM tree, CSS styles, and JavaScript functionality to capture the website's structure and behavior.
*   **UI Element Hashing:** Focuses on hashing key UI elements, button text, and form structures for identifying website functionality.
*   **Database Storage:** Stores crawled data in a MySQL database for efficient querying and analysis.
*   **Excluded URL List:** Allows specifying a list of URLs to exclude from crawling.
*   **User-Agent Rotation:** Randomly rotates user agents to avoid detection and rate limiting.
*   **Robust Error Handling:** Implements comprehensive error handling and logging to ensure crawler stability.
*   **Progress Tracking:** Provides real-time progress updates using `tqdm`.
*   **Automatic Retries:** Automatically retries failed requests with exponential backoff.
*   **Modular Design:** Features a modular design for easy extension and customization.
*   **Logging:** Comprehensive logging system using the `logging` module that logs to both console and a file.

## Getting Started

### Prerequisites

*   **Python 3.8+:** Ensure you have a compatible Python version installed.
*   **Tor Browser:** The crawler relies on a running Tor instance for proxying requests.
*   **MySQL Server:** You'll need a MySQL server to store the crawled data.
*   **Python Packages:** Install the required Python packages using `pip`.

### Installation

1.  **Clone the Repository:**

    ```bash
    git clone https://github.com/your-username/advanced-onion-crawler.git
    cd advanced-onion-crawler
    ```

2.  **Install Dependencies:**

    ```bash
    pip install -r requirements.txt
    ```

### Configuration

1.  **Database Setup:**

    *   Create a MySQL database with the name specified in `crawler_60.py` (default: `onion_crawler_01`).
    *   Create a user with appropriate permissions for the database.
    *   Update the database connection parameters in `crawler_60.py` (`db_host`, `db_name`, `db_user`, `db_password`).

    ```sql
    CREATE DATABASE onion_crawler_01;
    CREATE USER 'your_username'@'localhost' IDENTIFIED BY 'your_password';
    GRANT ALL PRIVILEGES ON onion_crawler_01.* TO 'your_username'@'localhost';
    FLUSH PRIVILEGES;
    USE onion_crawler_01;    ```

    *   **Important:**  The `crawled_data` table schema is implicitly defined by the `INSERT` statement in `crawler_60.py`.  Ensure your MySQL server allows implicit table creation or create the table manually with appropriate column types and lengths.  A suggested table structure is included below:

    ```sql
    CREATE TABLE crawled_data (
        url_hash VARCHAR(32) NOT NULL PRIMARY KEY,
        is_duplicate BOOLEAN,
        url TEXT,
        md5 VARCHAR(32),
        sha1 VARCHAR(32),
        sha256 VARCHAR(32),
        scripts TEXT,
        initial_depth INT,
        image_md5 VARCHAR(32),
        image_sha1 VARCHAR(32),
        image_sha256 VARCHAR(32),
        image_phash VARCHAR(32),
        image_dhash VARCHAR(32),
        image_color_histogram TEXT,
        sentence_embeddings TEXT,
        document_embeddings TEXT,
        dom_tree_md5 VARCHAR(32),
        dom_tree_sha1 VARCHAR(32),
        dom_tree_sha256 VARCHAR(32),
        css_style_md5 VARCHAR(32),
        css_style_sha1 VARCHAR(32),
        css_style_sha256 VARCHAR(32),
        js_functionality_md5 VARCHAR(32),
        js_functionality_sha1 VARCHAR(32),
        js_functionality_sha256 VARCHAR(32),
        key_elements_md5 VARCHAR(32),
        key_elements_sha1 VARCHAR(32),
        key_elements_sha256 VARCHAR(32),
        button_text_md5 VARCHAR(32),
        button_text_sha1 VARCHAR(32),
        button_text_sha256 VARCHAR(32),
        form_structure_md5 VARCHAR(32),
        form_structure_sha1 VARCHAR(32),
        form_structure_sha256 VARCHAR(32)
    );
    ```

2.  **Tor Proxy:**

    *   Ensure Tor Browser is running. The crawler defaults to using `127.0.0.1:9150` as the SOCKS5 proxy.  You can adjust `proxy_ip` and `proxy_port` in `crawler_60.py` if your Tor instance is configured differently.

3.  **Configuration File (`crawler_60.py`):**

    *   Modify the configuration variables at the end of the script to match your environment.  Key variables include:
        *   `output_dir`: The directory where crawled data will be stored.
        *   `max_depth`: The maximum crawling depth.
        *   `render_js`: Enable or disable JavaScript rendering.
        *   `db_host`, `db_name`, `db_user`, `db_password`: Your MySQL database credentials.
        *   `exclude_file`: The path to the file containing URLs to exclude.

4.  **Exclude File (`exclude.txt`):**

    *   Create a file named `exclude.txt` (or whatever you specify in `exclude_file`) and add URLs to be excluded from crawling, one URL per line.  This is crucial for avoiding crawling unwanted or potentially harmful sites.  Ensure these are `.onion` addresses.

5.  **Input File (`input.txt`):**

    *   Create a file named `input.txt` and add the initial `.onion` URLs you want to start crawling from, one URL per line.

### Running the Crawler

```bash
python crawler_60.py
```The crawler will start crawling from the URLs specified in `input.txt`, following links up to the configured `max_depth`, and storing the extracted data in the MySQL database.  Progress will be displayed in the console using `tqdm`, and errors will be logged to both the console and `error.txt`.

## Code Structure

The code is organized into the following classes and functions:

*   **`AdvancedWebCrawler`:** The main class responsible for crawling websites.
    *   `__init__`: Initializes the crawler with configuration parameters.
    *   `create_database_connection`: Establishes a connection to the MySQL database.
    *   `check_tor_connectivity`: Verifies that Tor is running and accessible.
    *   `rotate_user_agent`: Rotates the user agent for each request.
    *   `_is_allowed`: Checks if a URL is allowed to be crawled (must be a `.onion` URL and not in the exclude list).
    *   `fetch_onion_content`: Fetches content from `.onion` URLs using `requests`.
    *   `crawl`: Crawls the web recursively, extracting links and hashing content.
    *   `_extract_links`: Extracts valid links from a BeautifulSoup object.
    *   `_clean_url`: Cleans a URL by removing fragments and query parameters.
    *   `_hash_css_js`: Hashes CSS and JavaScript files.
    *   `load_urls_from_file`: Loads URLs from the input file.
    *   `load_excluded_urls`: Loads excluded URLs from the exclude file.
    *   `run`: Runs the crawler.
    *   `__del__`: Closes the database connection when the object is destroyed.
*   **`HashingUtils`:** A utility class for calculating hashes.
    *   `calculate_hashes`: Calculates MD5, SHA1, and SHA256 hashes for content.
    *   `generate_unique_hash`: Generates a unique hash for a URL, even if it's a duplicate.
*   **`ImageHashing`:** (Imported from `onion_crawler.image_hashing`) Handles image hashing tasks.
*   **`TextHashing`:** (Imported from `onion_crawler.text_hashing`) Handles text hashing and embedding generation.
*   **`StructuralHashing`:** (Imported from `onion_crawler.structural_hashing`) Handles structural hashing of DOM, CSS, and JavaScript.
*   **`UIElementHashing`:** (Imported from `onion_crawler.ui_element_hashing`) Handles hashing of UI elements.

## Modules

The crawler leverages several external modules for specific functionalities:

*   **`requests`:** For making HTTP requests.
*   **`beautifulsoup4`:** For parsing HTML content.
*   **`mysql-connector-python`:** For connecting to the MySQL database.
*   **`sentence-transformers`:** For generating sentence and document embeddings.
*   **`pillow`:** For image processing.
*   **`urllib3`:** For handling HTTP requests.
*   **`tqdm`:** For displaying progress bars.
*   **`numpy`:** For numerical operations (used in image hashing and text embeddings).
*   **`nltk`:** Natural Language Toolkit (may be used in text hashing).
*   **`imagehash`:** For perceptual image hashing.
*   **`requests_html`:** (Optional) For rendering JavaScript (requires Chromium).

## Advanced Usage and Customization

### JavaScript Rendering

To enable JavaScript rendering, you need to install `requests-html` and optionally specify the path to a Chromium executable.  If no path is provided, `requests-html` will attempt to download Chromium automatically.  Enable JavaScript rendering by setting `render_js = True` in `crawler_60.py`.

```python
render_js = True
chromium_path = "/path/to/chromium"  # Optional
