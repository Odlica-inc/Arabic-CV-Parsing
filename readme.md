# Arabic CV Parsing: 
Our CV parsing API achieves state-of-the-art accuracy in CV parsing and excels with Arabic-language CVs. It supports multilingual CVs and effectively handles various formats, including single-column, multi-column, and complex layouts. The API accepts CVs in PDF, Word documents, images, or plain text formats. It accurately identifies and extracts all key CV details such as personal information, work experience, education, skills, and contact details. This automation streamlines the recruitment process by accelerating candidate evaluation and enhancing talent acquisition. Easy to integrate into existing systems, our API offers seamless functionality, improving HR processes and productivity.

## Follow these steps to obtain credentials for using an API from RapidAPI and subscribing to it.

#### Step 1: Create a RapidAPI Account
1. **Visit RapidAPI Website**: Go to [RapidAPI](https://rapidapi.com/).
2. **Sign Up/Log In**:
    - If you don't have an account, sign up using your email, Google, or GitHub account.
    - If you already have an account, log in.

#### Step 2: Find the API
1. **Open the API through this link**: https://rapidapi.com/parsing-ai-parsing-ai-default/api/cv-parser

#### Step 3: Subscribe to the API
1. **Pricing**: Review the pricing plans available for the API. Some APIs offer free tiers, while others may require a paid subscription.
2. **Subscribe**: Click on the subscription plan that fits your needs. You may need to provide payment information if you choose a paid plan.

#### Step 4: Obtain API Credentials
1. **Navigate to 'Endpoints'**: Once subscribed, go to the 'Endpoints' tab on the API's details page.
2. **Get Credentials**: You will find your unique API key or credentials that you will use to authenticate your requests. This is usually found in the 'Request Headers' or 'Authentication' section.

#### Step 5: Use the API Key
1. **Integrate API Key**: Use the API key in your application by including it in the headers of your API requests. Typically, the API key needs to be passed as follows:
    ```http
    x-rapidapi-key: YOUR_API_KEY
    ```

## Endpoints available for CV parsing:
1. GET `/health` (Requires Authentication): check if API is working.
2. POST `/parse` (Requires Authentication): return a dictionary of the parsed fields of the CV.
You can parse CVs with two different methods: by sending the file as a base64 string or by providing a public download link. We support a wide range of file extensions, including PDF, DOC, DOCX, JPG, JPEG, and PNG.
- **Image: ["png", "jpeg", "jpg"]**:
  ```
        import requests
        import base64
        import os
        import json

        cv_path = "/CV/image/path/.jpg"

        with open(cv_path, 'rb') as image_file:
            base64_cv = base64.b64encode(image_file.read()).decode()

        url = "IP/ADDRESS/parse"

        # Define the payload
        payload = {
            "data_bytes": base64_cv,
            "isbytes": True,
            "file_type" : "jpg" #["png", "jpeg", "jpg"]
        }

        headers = {
        	"x-rapidapi-key": os.getenv("rapid_key"),
        	"x-rapidapi-host": "cv-parser.p.rapidapi.com",
        	"Content-Type": "application/json"
        }

        response = requests.post(url, json=payload, headers=headers)

        print(response.json())
        ```
- **Files: ["pdf", "docx", "doc"]**:
     ```
        import requests
        import base64
        import os

        cv_path = "/CV/PDF/path/.pdf"   #example of pdf extension
        # cv_path = "/CV/Docx/path/.docx" #example of docx extension

        with open(cv_path, 'rb') as f:
            pdf = f.read()

        base64_cv = base64.b64encode(pdf).decode()

        url = "IP/ADDRESS/parse"

        # Define the payload
        payload = {
            "data_bytes": base64_cv,
            "isbytes": True,
            "file_type" : "pdf" #["pdf", "docx", "doc"] pdf by default
        }

        headers = {
        	"x-rapidapi-key": os.getenv("rapid_key"),
        	"x-rapidapi-host": "cv-parser.p.rapidapi.com",
        	"Content-Type": "application/json"
        }

        response = requests.post(url, json=payload, headers=headers)

        print(response.json())
        ```  
- **URL: ["pdf", "docx", "doc","png", "jpeg", "jpg"]**:
    ```
        import requests
        import json
        import os


        drive_id = "1M..."

        url = "https://cv-parser.p.rapidapi.com/parse"

        payload = {
          #the download link could be for any file ["pdf", "docx", "doc","png", "jpeg", "jpg"]

        
        # normal download link from drive
          "file_url": f"https://drive.google.com/uc?export=download&id={drive_id}",

          #export link for docx file as pdf
          # "file_url": f"https://docs.google.com/document/d/{drive_id}/export?format=pdf",

          "isbytes": False,
          "file_type" : "pdf" #["pdf", "docx", "doc","png", "jpeg", "jpg"] pdf by default
        }
        headers = {
          "x-rapidapi-key": os.getenv("rapid_key"),
          "x-rapidapi-host": "cv-parser.p.rapidapi.com",
          "Content-Type": "application/json"
        }

        response = requests.post(url, json=payload, headers=headers)

        print(response.json())


        ```  

**Note that there is a limit for number of cv pages by 5, the user is responsible for the corruption if pages exceed the limit**
 

- **response (NOTE: a field is "None" if not found):**
  - `success (bool)`:
    - `True` if operations are done without error
    - `False` if an error is encountered
  - `msg (string)`: message showing the error in case of error
    - Empty string in case of success
  - `cv_parsing (dictionary)`: The output in case of a successful operation, empty dict in case of error
 
      - `full_name (string)`: Name of the applicant
      - `contact_information (dictionary)`:
        - `email_address (list of strings)`
        - `phone_numbers (list of strings)`
        - `address (dictionary)`:
          - `full_address (string)`
          - `country (string)`
          - `state/governate (string)`
          - `city/town (string)`
      - `linkedin_url (string)`
      - `personal_websites (list of string)`
      - `career_objective (string)`
      - `professional_summary (string)`
      - `profession (string)`: CV title or last job
      - `education (list of dictionaries)`:
        - Each dictionary contains:
          - `institution_name (string)`
          - `degree (string)`: Associates Degree
          - `major (string)`: Specialization
          - `grade (string)`: GPA score, percentage or excellent, v.good, good
          - `start_date (date-time format: YYYY-MM-DD)`
          - `graduation_date (date-time format: YYYY-MM-DD)`
          - `duration (dictionary)`:
            - `years (integer)`
            - `months (integer)`
          - `description (string)`
          - `location (string)`
      - `skills (dictionary)`:
        - `technical_skills (list of strings)`
        - `soft_skills (list of strings)`
        - `inferred_skills (list of strings)`: these are the skills which not mentioned directly as skills in the CV.
      - `projects (list of dictionaries)`:
        - Each dictionary contains:
          - `project_title (string)`
          - `project_type (string)`: educational, professional, personal
          - `description (string)`
      - `publications (list of strings)`
      - `certifications_and_licenses (list of strings)`
      - `languages (list of dictionaries)`:
        - Each dictionary contains:
          - `language (string)`
          - `level (string)`
      - `honors_and_awards (list of strings)`
      - `conferences_and_workshops (list of strings)`
      - `volunteer_experience (list of dictionaries)`:
        - Each dictionary contains:
          - `organization_name (string)`
          - `role (string)`
          - `start_date (date-time format: YYYY-MM-DD)`
          - `end_date (date-time format: YYYY-MM-DD)`
          - `duration (dictionary)`:
            - `years (integer)`
            - `months (integer)`
          - `description (string)`
      - `hobbies_and_interests (list of strings)`
      - `references (list of dictionaries)`:
        - Each dictionary contains:
          - `name (string)`
          - `position (string)`
          - `company (string)`
          - `contact_information (dictionary)`:
            - `email_address (string)`
            - `phone_number (string)`
      - `parsing_date (string)`
      - `CV_source (link)`

