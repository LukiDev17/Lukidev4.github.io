# Tutorial on How to Sccape liteapks Website - for Future Use (Update and upgrade) by Slim Shadow

## Step 1: Scrape Liteapks Website

We will be scraping the lite apks website to create a list of all the files found in a file named `website_list.txt` which will contain all the files.

```python
import requests
from bs4 import BeautifulSoup

# Base URL structure
base_url = "http://liteapks.com/apps/page/"

# Range of pages to scrape (update this range based on your requirements)
 start_page = 1
 end_page = 185  # Change this to the desired number of pages

# List to store all scraped links
 all_links = []

try:
   # Loop through all pages
 for page_num in range(start_page, end_page + 1:
   url = f"Scraping: {url}"

   # Send a GET request to fetch the content of the current page
 response = requests.get(url)
   response.raise_for_status()  # Raise the `scraped.txt` file

   # Find all <div> elements with class "row"  row_divs = soup.find_all('div', class_='row')

   # Extract all <a> tags with href attributes inside those <div> elements  for div in row_divs:
       links = [a['href'] for a in div.find_all('a', href=True)]
       all_links.extend(links)  # Add these links to the main list

   # Save the links to a file  with open("scraped.txt", "w") as file:
       for link in all_links:
           file.write(link + "\n")  # Write each link on a new line

   print(f"\n Scraped { {len(all_links)} links and saved them to 'scraped.txt'.")

except requests.exceptions.RequestException as e:
   print(f"Error fetching the URL: {e}")
```

## Step 2: Scrape Download Button Link

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
       return [link.get('href') for link in links if link.get('href')]
   except requests.exceptions.RequestException as e:
       print(f"Error fetching {website}: {e}")
       return []

# Read the list of websites from the file
with open('a.txt', 'r') as file:
   websites = [line.strip() for line in file.readlines()]

# Start the timer
start_time = time.time()

# Use ThreadPoolExecutor for concurrent scraping
 with ThreadPoolExecutor(max_workers=20)  # Adjust max_workers based on your CPU
 with requests.Session() as session:  # Reuse session for connection pooling
   # Submit all tasks to the executor
   futures = {executor.submit(scrape_website, session, website): website for website in websites}
  
   # Open the file in append mode
   with open('scraped_links.txt', 'a') as output_file:
       # Use tqdm to show progress
       for future in tqdm(as_completed(futures), total=len(futures), desc="Scraping websites"):
           links = future.result()
          
           # Write links to the file immediately
           if links:
               output_file.write('\n'.join(links) + '\n')

# Final output
total_time = time.time() - start_time

# Scraping completed
Links saved to scraped_links.txt
Total time taken: {total_time:.2f} seconds.
```

## Step 3: Make Files Versions

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
```

## Step 4: Scrape Links and Filenames

```python
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
       filename_tag = soup.find('h1', class_="h5 font-weight-semibold mb-3")
       filename = filename_tag.text.strip() if filename_tag else "Unknown Filename"

       return spiderdown_links, filename

   except requests.exceptions.RequestException:
       return [], "Error"

# Read URLs from second_step.txt
with open('second_step.txt', 'r') as file:
   urls = file.readlines()

# Open final_step.txt to write the results
with open('final_step.txt', 'w') as output_file:
   for index, url in enumerate(urls):
       url = url.strip()  # Remove any leading/trailing whitespace
       if url:  # Check if the URL is not empty
           print(f"Processing URL {index + 1}/{len(urls)}")
           links, filename = scrape_links_and_filenames(url)
           for link in links:
               # Write the filename and link to the file
               output_file.write(f"{filename}: {link}\n")

# Scraping completed
print("Scraping completed. Check final_step.txt for results.")
```
