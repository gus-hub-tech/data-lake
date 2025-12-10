# NBADataLake
I created this repository with a setup_nba_data_lake.py script that automates the creation of a data lake for NBA analytics using AWS services. My script integrates Amazon S3, AWS Glue, and Amazon Athena, and sets up the infrastructure needed to store and query NBA-related data.

## Overview
My setup_nba_data_lake.py script performs the following actions:

- Creates an Amazon S3 bucket to store raw and processed data
- Uploads sample NBA data (JSON format) to the S3 bucket
- Creates an AWS Glue database and an external table for querying the data
- Configures Amazon Athena for querying data stored in the S3 bucket

## Prerequisites
Before running my script, I ensured I had the following:

### SportsData.io API Key
1. I went to Sportsdata.io and created a free account
2. At the top left, I saw "Developers", and when I hovered over it I saw "API Resources"
3. I clicked on "Introduction & Testing"
4. I clicked on "SportsDataIO API Free Trial" and filled out the information & made sure to select NBA for this tutorial
5. I got an email and at the bottom it said "Launch Developer Portal"
6. By default it took me to the NFL, so on the left I clicked on NBA
7. I scrolled down until I saw "Standings"
8. Under "Query String Parameters", the value in the drop down box was my API key
9. I copied this string because I needed to paste it later in the script

### IAM Role/Permissions
I ensured my user or role running the script had the following permissions:

- **S3**: `s3:CreateBucket`, `s3:PutObject`, `s3:GetObject`, `s3:DeleteObject`, `s3:ListBucket`
- **Glue**: `glue:CreateDatabase`, `glue:CreateTable`, `glue:DeleteDatabase`, `glue:DeleteTable`
- **Athena**: `athena:StartQueryExecution`, `athena:GetQueryResults`

#### Setting Up IAM Permissions
1. I went to the AWS Console and searched for "IAM"
2. I clicked on "Policies" in the left sidebar
3. I clicked "Create Policy"
4. I selected the "JSON" tab
5. I copied the policy from `policies/IAM_Role.json` in this repository (excluding the "Resources" section at the bottom)
6. I pasted it into the policy editor
7. I clicked "Next" and named the policy `NBADataLakePolicy`
8. I clicked "Create Policy"
9. I went to "Users" in the left sidebar and selected my user
10. I clicked "Add Permissions" → "Attach policies directly"
11. I searched for and selected `NBADataLakePolicy`
12. I clicked "Add Permissions"

## Getting Started

### Step 1: Verify IAM Permissions
1. I ensured I had completed the IAM setup from the Prerequisites section above
2. I verified my user had the `NBADataLakePolicy` attached

### Step 2: Open CloudShell Console

1. I went to aws.amazon.com & signed into my account

2. At the top, next to the search bar I saw a square with a >_ inside, I clicked this to open the CloudShell

### Step 3: Create the setup_nba_data_lake.py file
1. In the CLI (Command Line Interface), I typed:
   ```bash
   nano setup_nba_data_lake.py
   ```

2. In another window, I went to [GitHub](https://github.com/gus-hub-tech/data-lake)
   - I copied the contents inside the setup_nba_data_lake.py file
   - I went back to the Cloudshell window and pasted the contents inside the file

3. I pressed `Ctrl+X` to exit, pressed `Y` to save the file, and pressed `Enter` to confirm the file name 


### Step 4: Create .env file
1. In the CLI (Command Line Interface), I typed:
   ```bash
   nano .env
   ```

2. I pasted the following lines into my file, ensuring I replaced `your_sportsdata_api_key` with my actual API key:
   ```bash
   SPORTS_DATA_API_KEY=your_sportsdata_api_key
   NBA_ENDPOINT=https://api.sportsdata.io/v3/nba/scores/json/Players
   ```

3. I pressed `Ctrl+X` to exit, pressed `Y` to save the file, and pressed `Enter` to confirm the file name 


### Step 5: Run the script
1. In the CLI I typed:
   ```bash
   python3 setup_nba_data_lake.py
   ```

2. I saw that the resources were successfully created, the sample data was uploaded successfully and the Data Lake Setup Completed

### Step 6: Verify the Resources

#### Check S3 Bucket
1. In the AWS Search Bar, I typed "S3" and clicked the blue hyperlink
2. I saw a General purpose bucket named "sports-analytics-data-lake"
3. When I clicked the bucket name I saw 3 folders in the bucket
4. I clicked on "raw-data" and saw it contained "nba_player_data.jsonl"
5. I clicked the file name and at the top I saw the option to Open the file
6. I saw a long string of various NBA data

#### Query with Amazon Athena
1. I headed over to Amazon Athena and pasted the following sample query:
   ```sql
   SELECT FirstName, LastName, Position, Team
   FROM nba_players
   WHERE Position = 'PG';
   ```

2. I clicked "Run"
3. I saw an output when I scrolled down under "Query Results"

## Troubleshooting

### Common Issues and Solutions

#### 1. Missing Python Packages
**Error:** `ModuleNotFoundError: No module named 'boto3'` or similar

**Solution:** Install required packages:
```bash
pip3 install boto3 requests python-dotenv
```

#### 2. S3 Bucket Already Exists
**Error:** `BucketAlreadyExists: The requested bucket name is not available`

**Solution:** The script now automatically generates a unique bucket name with a random suffix. If you still get this error, run the script again.

#### 3. API Authentication Failed (401 Error)
**Error:** `401 Client Error` when fetching NBA data

**Solutions:**
- Verify your `.env` file exists in the same directory as the script
- Check that your API key is correct in the `.env` file
- Ensure your SportsData.io API key is active and has NBA access
- Test your API key manually at the SportsData.io developer portal

#### 4. Missing Environment Variables
**Error:** `SPORTS_DATA_API_KEY not found in .env file`

**Solution:** Create or check your `.env` file contains:
```bash
SPORTS_DATA_API_KEY=your_actual_api_key_here
NBA_ENDPOINT=https://api.sportsdata.io/v3/nba/scores/json/Players
```

#### 5. Athena Output Bucket Error
**Error:** `Unable to verify/create output bucket`

**Solution:** This happens when S3 bucket creation fails. Ensure the S3 bucket is created successfully before Athena configuration.

#### 6. IAM Permission Errors
**Error:** Access denied errors for AWS services

**Solution:** Verify you have attached the `NBADataLakePolicy` to your user as described in the Prerequisites section.

## What I Learned
1. Securing AWS services with least privilege IAM policies.
2. Automating the creation of services with a script.
3. Integrating external APIs into cloud-based workflows.
4. Handling common deployment issues and error scenarios.


## Future Enhancements
1. Automate data ingestion with AWS Lambda
2. Implement a data transformation layer with AWS Glue ETL
3. Add advanced analytics and visualizations (AWS QuickSight)

## Cleanup Resources
To avoid ongoing AWS charges, I can delete all created resources:

1. In CloudShell, I typed:
   ```bash
   nano delete_aws_resources.py
   ```

2. I copied the contents from the `delete_aws_resources` file in this repository
3. I pasted it into the CloudShell editor
4. I pressed `Ctrl+X`, then `Y`, then `Enter` to save
5. I ran the cleanup script:
   ```bash
   python3 delete_aws_resources.py
   ```

This will delete:
- The S3 bucket and all its contents
- The Glue database and tables
- Any Athena query results

---

**I'm ready to analyze data at scale! I can run the setup script and start querying in minutes!**

