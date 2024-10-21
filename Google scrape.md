import requests
from bs4 import BeautifulSoup
import pandas as pd
import json

# Step 1: Make a request to the URL
url = "https://www.google.com/about/careers/applications/jobs/results/?location=India"ss
response = requests.get(url)

# Check if the request was successful
if response.status_code != 200:
    print("Failed to retrieve the page") # Indented this line
    exit() # Indented this line

# Step 2: Parse the HTML using BeautifulSoup
soup = BeautifulSoup(response.content, 'html.parser')
# Step 3: Extract job listings
job_listings = soup.find_all('div', class_='sMn82b') # Adjust the class based on actual site structure
# Initialize lists to hold extracted data
data = []
max_jobs = 20 # Limit to 20 companies
job_count = 0
# Loop through each job listing
for job in job_listings:
    # Indentation added here to indicate the start of the for loop's code block
    if job_count >= max_jobs:
        break
    # Extract the company name
    company_name = job.find('span', class_='RP7SMd').text.strip()
    # Extract the job title
    job_title = job.find('h3', class_='QJPWVe').text.strip()
    # Extract the job location(s)
    location_elements = job.find_all('span', class_='r0wTof')
    locations = [location.text.strip() for location in location_elements]
    job_location = ", ".join(locations)
    # Extract the job description (minimum qualifications)
    qualifications = job.find('div', class_='Xsxa1e').find_all('li')
    job_description = "\n".join([qualification.text.strip() for qualification in qualifications])
    # Store the data
    data.append({s
        'Company Name': company_name,
        'Job Title': job_title,
        'Job Location': job_location,
        'Job Description': job_description
    })
    job_count += 1
# Step 4: Save data to CSV
df = pd.DataFrame(data)
df.to_csv('job_listings.csv', index=False)
# Step 5: Save data to JSON
with open('job_listings.json', 'w') as json_file:
    json.dump(data, json_file, indent=4)
# Print the extracted data
for job in data:
    print(f"Company Name: {job['Company Name']}")
    print(f"Job Title: {job['Job Title']}")
    print("Job Location:", job['Job Location'])
    print("Job Description:")
    print(job['Job Description'])
    print("\n" + "-" * 40 + "\n")