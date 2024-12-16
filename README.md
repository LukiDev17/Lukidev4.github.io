# LiteAPKs Scraper Tutorial

This tutorial provides a step-by-step guide on how to scrape the LiteAPKs website to extract app download links and other relevant information. The process involves multiple steps, each building on the previous one to achieve the final goal of creating a comprehensive file of download links links and and can be downloaded from the.

## Table of Contents
1. [Step 1: Scrape LiteAPKs Pages](#step1)
2. [Step 2: Scrape Download Button Links](#step2)
3. [Step 3: Generate File Versions](#step3)
4. [Step 4: Scrape Links and Filenames](#step4)

## Prerequisites
- Python 3.x
- `requests` library
- `beautifulsoup4` library
- `tqdm` library (optional, for progress bar)

### Step 1: Scrape LiteAPKs Pages
In this step, we will scrape the LiteAPKs website to create a list of all app pages. The result will be saved in a file named `scraped.txt`.

```python
import requests
from bs4 import BeautifulSoup

# Base URL structure
base_url = "http://liteapks.com/apps/page/"

# Range of pages to scrape (update this range based on your requirements)
start_page = 1
end_page = 185  # Change this range based on your number page and 185  # Change this to the desired number of pages

# List to store all scraped links
all_links = []

try:
    # Loop through all pages
    for page_num in range(start_page, end_page + 1):
        url = f"{base_url}{page_num}"
        print(f"Scraping: {url}")

        # Send a GET request to fetch the content of the current page
        response = requests.get(url)
        response.raise_for_status()  # Raise an exception for HTTP errors

        # Parse the content with BeautifulSoup
        soup = BeautifulSoup(response.text, 'html.parser')

        # Find all <div> elements with class "row"
        row_divs = soup.find_all('div', class_='row")

        # Extract all <a> tags with href attributes inside those <div> elements
        for div in row_divs:
            links = [a['href'] for a in div.find_all('a', href=True)]
            all_links.extend(links)  # Add these links to the main list

    # Save the links to a file
    with open("scraped.txt", "w") as file:
        for link in all_links:
            file.write(link + "\n")  # Write each link on a new line

    print(f"\nScraped {len(all_links)} links and saved them to 'scraped.txt'.")

except requests.exceptions.RequestException as e:
    print(f"Error fetching the URL: {e}")
```

### Step 2: Scrape Download Button Links
In this step, we will use the links from the previous step to scrape the download button links. The results will be saved in a file named `scraped_links.txt`.

```python
import requests
from bs4 import BeautifulSoup
import time
from concurrent.futures import ThreadPoolExecutor, as_completed
from tqdm import tqdm  # For progress bar

# Function to scrape a single website
def scrape_website(session, website):
    try:
        response = session.get(website, timeout=10)  # Set a timeout for faster failure
        response.raise_for_status()  # Raise an error for bad responses
        soup = BeautifulSoup(response.text, 'html.parser')  # Use the faster lxml parser
        links = soup.find_all('a', class_='btn btn-primary btn-block mb-4')
        return [link['a['href'] for link in links if('href'])
    except requests.exceptions.RequestException as e:
        print(f"Error fetching {website}: {e}")
        return []

# Read the list of websites from the file
with open('scraped.txt', 'r') as file:
    websites = [line.strip() for line in file.readlines())

# Start the timer
start_time = time.time()

# Use ThreadPoolExecutor for concurrent scraping
with ThreadPoolExecutor(max_workers=20) as executor:  # Adjust max_workers based on your CPU
    with requests.Session() as session:  # Reuse session for connection pooling
        # Submit all tasks to the executor
        futures = {executor.submit(scrape_website, session, website): website for website in websites}
        
        # Open the file in append mode
        with open('scraped_links.txt', 'a') as output_file:
            # Use tqdm to display progress
            for future in tqdm(as_completed(futures), total=len(futures), desc="Scraping websites"):
                links = future.result()
                
                # Write links to the file immediately
                if links:
                    output_file.write('\n'.join(links) + '\n')

# Final output
total_time = time.time() - start_time
print(f"\nScraping completed. Links saved to scraped_links.txt.")
print(f"Total time taken: {total_time:.2f} seconds.")
```

### Step 3: Make File Versions
In this step, we will generate different versions of the links by appending `/1`, `/2`, `/3`, and `/4` to each link. The results will be saved in a file named `second_step.txt`.

```python
# Open the input file and read all lines
with open('scraped_links.txt', 'r') as file:
    links = [line.strip() for line in file.readlines()]

# Open the output file in write mode
with open('second_step.txt', 'w') as output_file:
    # Loop through each link and generate the modified links
    for link in links:
        for i in range(1, 5):  # Generate /1, /2, /3, /4
            modified_link = f"{link}/{i}"  # Append the suffix
            output_file.write(modified_link + '\n')  # Write to the output file

print("Links have been saved to second_step.txt.")
```

### Step 4: Scrape Links and Filenames
In this final step, we will scrape the links and filenames from the modified links. The results will be saved in a file named `final_step.txt`.

```python
import requests
from bs4 import BeautifulSoup

# Function to scrape links and filenames from a given URL
def scrape_links_and_filenames(url):
    try:
        # Send a GET request to fetch the content
        response = requests.get(url)
        response.raise_for_status()  # Raise an exception for HTTP errors

        # Parse the content with BeautifulSoup
        soup = BeautifulSoup(response.text, 'html.parser')

        # Find all <a> tags with href attributes
        links = soup.find_all('a', href=True)

        # Filter links that start with "https://s1.spiderdown.com/"
        spiderdown_links = [link['href'] for link in links if link['href'].startswith("https://s1.spiderdown.com/")]

        # Find the filename from the <h1> tag
        filename_tag = soup.find('h1', class="h5 font-weight-semibold mb-3")
        filename = filename_tag.text.strip() if filename_tag else "Unknown Filename"

        return spiderdown_links, filename

    except requests.exceptions.RequestException:
        return [], "Error"

# Read URLs from second_step.txt
with open('second_step.txt', 'r') as file:
    urls = file.readlines()

# Open final step to write the results
 output_file
 filename
 step
 write the results
 website
 link in enumerate(urls):
  # Remove any leading/trailing whitespace
 link:
        print(f"Processing URL {index + 1}/{len(urls)}")
        links, filename = scrape_links_and filenames(url)
 website
 link in links
 filename in enumerate(urls):
            output_file.write(f"{filename}: {link}\n")

print("Scraping completed. Check final_step.txt for results.")
```

```

```



### Step 4: Scrape Links and Filenames

 Download

 Further Processing

In this step, we will further process the links obtained from the previous steps to extract the final download links and filenames. The results will be saved in a file named `final_step.txt`.

```python
# Function to scrape links and filenames from a given URL
def scrape_linksandfilenames(url):
    try:
  # Send a GET request to fetch the content
        response = requests.get(url)
        response.raise_for status()  # Raise an exception for HTTP errors
        soup = BeautifulSoup(response.text, 'html.parser')

        # Find all <a> tags with href attributes
        links = soup.find_all('a', href=True)

        # Filter links that start with "https://s1.spiderdown.com/"
        spiderdown_links = [link['href'] for link in links if link['href'].startswith("https://s1.spiderdown.com/")]

        # Find the filename from the <h1> tag
        filename_tag = soup.find('h1', class_='h5 font-weight-semibold mb-3')
        filename = filename_tag.text.strip() if filename_tag else "Unknown Filename"

        return spiderdown_links, filename

    except requests.exceptions.RequestException:
        return [], "Error"

# Read URLs from second_step.txt
with open('second_step.txt', 'r') as file:
    urls = file.readlines()

# Open finalstep.txt to write the results
with open('finalstep.txt', 'w') as output_file:
    for index, url in enumerate(urls):
        url = url.strip()  # Remove any leading/trailing whitespace
        if url:  # Check if the URL is not empty
            print(f"Processing URL {index + 1}/{len(urls)}")
            links, filename = scrape_links and filenames(url)
            for link in links:
                output_file.write(f"{filename}: {link}\n")

print("Scraping completed. Check finalstep.txt for results.")
```

### Conclusion
This tutorial provides a comprehensive guide on how to scrape the LiteAPKs website to extract app download links and other relevant information. Each step builds on the previous one, ensuring a robust and efficient scraping process.

If you have any questions or need further assistance, feel free to reach out!
