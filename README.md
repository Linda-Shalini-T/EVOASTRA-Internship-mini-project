# Cars24 Web Scraping Project - Used Car Data Collection
This project demonstrates automated web scraping of used car listings from Cars24.com, specifically focusing on **Hyundai** vehicles. The project uses **Selenium WebDriver** to extract car details including name, year, mileage, fuel type, transmission, and pricing information.


## Understanding the Project
The project aims to collect comprehensive used car data from the Cars24 platform for **data analysis** and **market research** purposes.

- The web scraping tool navigates to Cars24's Hyundai car listings and systematically loads all available vehicles using **dynamic scrolling** and **pagination handling**.

- The scraper extracts **6 key attributes** for each car listing: **Car Name**, **Year**, **Kilometers Driven**, **Fuel Type**, **Transmission**, and **Price**.

- The data is parsed using **regular expressions** to extract structured information from unstructured web page text.

- All collected data is exported to a **CSV file** with a timestamp for easy tracking and analysis.


## Technology Stack

The project utilizes the following technologies and libraries:

- **Selenium WebDriver**: For browser automation and web scraping
- **ChromeDriver**: Chrome browser driver for Selenium
- **Python Regular Expressions (re)**: For text pattern matching and data extraction
- **CSV Module**: For structured data export
- **Pandas**: For data loading and display
- **Datetime**: For timestamp generation


## Web Scraping Methodology

### 1. WebDriver Initialization
The project initializes a headless Chrome browser with specific configurations to ensure smooth scraping:

```python
def web_driver():
  options = webdriver.ChromeOptions()
  options.add_argument("--verbose")
  options.add_argument('--headless')
  options.add_argument('--no-sandbox')
  options.add_argument('--disable-gpu')
  options.add_argument('--disable-dev-shm-usage')
  driver = webdriver.Chrome(options=options)
  return driver
```

**Key Options Explained:**
- **--headless**: Runs browser without GUI for faster execution
- **--no-sandbox**: Bypasses OS security model (useful in containerized environments)
- **--disable-gpu**: Disables GPU hardware acceleration
- **--disable-dev-shm-usage**: Overcomes limited resource problems in Docker containers


### 2. Data Extraction Function
The `extract_car_data()` function parses raw text from car listings and extracts structured information:

- **Car Name & Year**: Extracted using regex pattern `\b(19|20)\d{2}\b` to identify 4-digit years
- **Kilometers Driven**: Identified by 'km' keyword with numeric patterns (e.g., "34.11k km")
- **Fuel Type**: Matches keywords like 'petrol', 'diesel', 'cng', 'electric', 'hybrid'
- **Transmission**: Identifies 'manual', 'auto', or 'automatic' transmission types
- **Price**: Extracts prices in lakh format using pattern `₹[\d.]+\s*lakh`


### 3. Dynamic Page Loading Strategy
The scraper implements a sophisticated multi-method approach to load all available car listings:

**Method 1: Incremental Scrolling**
```python
for i in range(5):
    driver.execute_script("window.scrollBy(0, 300);")
    time.sleep(0.2)
```

**Method 2: Full Page Scroll**
```python
driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
```

**Method 3: Container-Specific Scrolling**
- Targets specific div containers that may have internal scrolling

**Method 4: Pagination Button Detection**
- Searches for "Next", "Load More", and pagination buttons using multiple XPath selectors
- Attempts both standard click and JavaScript-based clicking for maximum compatibility


### 4. Intelligent Loading Detection
The scraper monitors page changes to determine when all listings have been loaded:

- **Maximum Iterations**: 100 scroll attempts to prevent infinite loops
- **No Change Threshold**: Stops after 8 consecutive iterations without new content
- **Dynamic Count Tracking**: Monitors car element count using class name `styles_outer__NTVth`
- **Progress Reporting**: Provides real-time feedback on loading progress


## Data Schema

The extracted data follows this structured format:

| Field Name | Data Type | Description | Example |
|:-----------|:---------:|:------------|:--------|
| Car_Name | String | Full car name with year | 2019 Hyundai Creta 1.6 SX Plus |
| Year | String | Manufacturing year | 2019 |
| Kilometers_Driven | String | Odometer reading | 34.11k km |
| Fuel_Type | String | Fuel type | Diesel |
| Transmission | String | Transmission type | Manual |
| Price | String | Listed price | ₹3.35 lakh |


## Execution Workflow

### Step 1: Environment Setup
- Install Selenium package using `!pip install selenium`
- Update system packages with `!apt-get update`
- Install ChromiumDriver using `!apt install chromium-driver`
- Initialize WebDriver with custom configuration

### Step 2: Web Navigation
- Navigate to Cars24 Hyundai listings page with pre-applied filters
- Wait 5 seconds for initial page load
- Target URL: `https://www.cars24.com/buy-used-car/?f=make%3A%3D%3Ahyundai`

### Step 3: Dynamic Loading
- Execute iterative scrolling (up to 100 iterations)
- Attempt to click pagination/load-more buttons
- Monitor element count changes
- Stop when no new listings load for 8 consecutive attempts

### Step 4: Data Extraction
- Locate all car listing elements by class name `styles_outer__NTVth`
- Extract text content from each element
- Parse text using `extract_car_data()` function
- Store extracted data in list of dictionaries

### Step 5: Data Export
- Generate timestamp-based filename (format: `cars24_data_YYYYMMDD_HHMMSS.csv`)
- Write data to CSV file using `csv.DictWriter`
- Include header row with field names
- Provide summary statistics (total records extracted)

### Step 6: Data Loading & Verification
- Mount Google Drive (for Colab environment)
- Load CSV file using Pandas
- Display data for verification


## Error Handling

The project implements robust error handling:

- **InvalidSessionIdException**: Catches WebDriver session loss and provides recovery guidance
- **NoSuchElementException**: Handles missing page elements gracefully
- **FileNotFoundError**: Provides clear error messages when CSV files are not found
- **Generic Exception Catching**: Captures and logs unexpected errors during extraction


## Output Format

The scraper generates CSV files with the following characteristics:

- **Filename Format**: `cars24_data_YYYYMMDD_HHMMSS.csv`
- **Encoding**: UTF-8 (supports Indian Rupee symbol ₹ and other special characters)
- **Headers**: Car_Name, Year, Kilometers_Driven, Fuel_Type, Transmission, Price
- **Record Count**: Variable based on available listings (typically 50-200+ records)


## Key Features

✓ **Automated Dynamic Loading**: Handles infinite scroll and pagination automatically
✓ **Robust Element Detection**: Multiple fallback strategies for button clicking
✓ **Intelligent Stopping**: Prevents infinite loops with smart detection
✓ **Real-time Progress Monitoring**: Provides detailed console output
✓ **Structured Data Export**: Clean CSV output ready for analysis
✓ **Error Recovery**: Comprehensive exception handling
✓ **Timestamp Tracking**: Each scraping session generates uniquely named output
✓ **Session Management**: Proper browser cleanup in finally block


## Execution Environment

The notebook is designed to run in **Google Colab** environment, which provides:

- Pre-installed Python 3.x environment
- Linux-based OS for apt package management
- Jupyter notebook interface
- Google Drive integration for data storage
- Free GPU/CPU resources for execution


## Usage Instructions

1. **Open the notebook** in Google Colab or Jupyter environment
2. **Run the Initialization cell** to install dependencies and set up WebDriver
3. **Execute the Data Extraction cell** to define the parsing function
4. **Run the Web Scraping cell** to start the automated scraping process
5. **Monitor the console output** for progress updates
6. **Wait for completion** - the scraper will automatically stop when all listings are loaded
7. **Mount Google Drive** (optional) if you want to save data to Drive
8. **Load and verify the data** using the final cell


## Potential Enhancements

The project can be extended with the following features:

- **Multi-brand Support**: Extend scraping to other car manufacturers
- **Advanced Filtering**: Add support for price range, year range, and location filters
- **Data Validation**: Implement data quality checks and cleaning
- **Database Integration**: Store data in SQL/NoSQL databases instead of CSV
- **Scheduling**: Add cron job support for periodic automated scraping
- **Price Tracking**: Monitor price changes over time for market trend analysis
- **Visualization**: Add charts and graphs for data insights
- **API Development**: Create REST API for programmatic data access


## Important Notes

⚠️ **Web Scraping Ethics**: Always respect the website's `robots.txt` and Terms of Service

⚠️ **Rate Limiting**: The scraper includes deliberate delays to avoid overwhelming the server

⚠️ **Session Management**: Properly closes browser sessions to free system resources

⚠️ **Dynamic Content**: Website structure may change; scraper may require updates

⚠️ **Data Accuracy**: Extracted data should be validated before use in production systems


## Project Structure

```
Final Cars24/
├── Cars24TeamC.ipynb          # Main Jupyter notebook with scraping code
├── cars24.py                   # Standalone Python script version
├── cars24_data_20251018_120724.csv  # Sample output data
├── README.md                   # This documentation file
└── Sample_Report.md           # Reference documentation template
```


## Dependencies

- Python 3.7+
- selenium >= 4.0.0
- pandas >= 1.0.0
- Chrome/Chromium browser
- ChromeDriver (compatible version with Chrome)


## Conclusion

This project successfully demonstrates automated web scraping of real-world e-commerce data using Selenium. The scraper is robust, handles dynamic content loading, and produces clean structured output suitable for further analysis, machine learning, or business intelligence applications.
