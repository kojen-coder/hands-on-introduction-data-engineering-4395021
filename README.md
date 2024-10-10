# 🚀 Airflow Coding Practice: Building ETL Workflows with Codespaces and SQLite

In my data engineering journey, I’ve always been drawn to hands-on learning, and Apache Airflow is a tool that piqued my interest for automating data workflows. This document outlines my practice with Airflow, where I leveraged GitHub Codespaces and SQLite to build DAGs (Directed Acyclic Graphs), solving real-world ETL challenges. Taking the "Hands-on-introduction-data-engineering" course on LinkedIn Learning allowed me to explore Airflow and its capabilities. Here, I’ll cover the lessons learned, the challenges faced, and how I overcame them.

## DAG 1: Extract DAG

### What I Did

The Extract DAG was the starting point for getting familiar with Airflow. I focused on using the `BashOperator` to download a CSV file from a remote URL. This helped me understand how to define tasks and structure a DAG. A key part of this process was validating that the data was downloaded correctly.

- **Key Concepts Learned:**
  - **BashOperator**: Running shell commands within Airflow.
  - **Task Orchestration**: Learned to schedule and sequence a single task.
  - **Data Validation**: I always checked the downloaded file to ensure it was fetched correctly and not corrupted.

### Challenges Faced

I initially used the `wget -c` command to download a file, but it fetched an HTML page instead of the expected CSV. The problem arose because `wget` was unable to handle the HTTP redirect properly. After investigating, I switched to `curl` because it’s better at handling redirects. I also added the `-L` flag to ensure that any redirects were followed.

- **Solution:** 
  Using `curl -L`, I successfully downloaded the correct CSV file. The `-L` option tells `curl` to follow redirects, which `wget -c` didn’t handle properly in this case. The switch worked because `curl` has built-in capabilities to follow multiple redirection steps automatically. I also noticed that using `wget --content-disposition` was effective as well.



## DAG 2: One Task DAG

### What I Did

In this DAG, I built a simple workflow that used a single task to print a message and write it to a file. While the task itself was straightforward, it allowed me to focus on Airflow’s scheduling and task execution capabilities. Once again, I ensured that the task completed successfully by manually checking the output file.

- **Key Concepts Learned:**
  - **Default Arguments**: Learned to set defaults for retries, task ownership, and email notifications.
  - **Manual Validation**: Ensured that the output was being written to the correct file.



## DAG 3: Two Task DAG (Business Context: Top-Level Domain Processing)

### What I Did

For this DAG, I tackled a real-world scenario where I had to extract a list of top-level domains (TLDs), filter out the generic ones, and save the results locally. This was a typical ETL process:
1. **Extract**: Download a CSV file with TLDs from the internet.
2. **Transform**: Filter the CSV to only retain generic TLDs (like .com, .net, etc.).
3. **Load**: Save the filtered list to my local machine for further processing.

This DAG was an exercise in chaining tasks together, ensuring proper dependencies and execution order. I used task sequencing to control the flow of the tasks from extraction to transformation and loading.

- **Key Concepts Learned:**
  - **Task Dependencies**: Used the `>>` operator to enforce sequential execution.
  - **ETL Workflow**: This was my first multi-task ETL pipeline, ensuring tasks completed in order.

### Business Context

In this workflow, the goal was to automate the process of maintaining a current list of generic top-level domains (gTLDs). This list is crucial for any service handling domain names, ensuring that only valid and commonly used domains are accepted. By automating this process, I could ensure that the data stays up-to-date without manual intervention.

### Challenges Faced

A key learning from this DAG was the importance of validating the data before moving on to the transformation step. If the data downloaded was incomplete or corrupt, moving ahead without validation would introduce errors further down the line.

- **Solution:** 
  I added a data validation step to confirm the integrity of the downloaded data, ensuring that the file was properly formatted and contained the expected fields. This step is important because it catches issues early in the pipeline, preventing errors during the transformation or loading phases.



## DAG 4: Challenge ETL DAG (Business Context: S&P 500 Sector Aggregation)

### What I Did

This was the most complex DAG in my practice, simulating a business scenario where I needed to:
1. **Extract**: Download data about S&P 500 companies.
2. **Transform**: Aggregate the companies by sector, calculating the daily count of companies in each sector.
3. **Load**: Store the aggregated data in an SQLite database for later analysis.

I used the `PythonOperator` to handle the transformation, where I grouped the companies by sector and added a date column. The final step was loading the data into an SQLite database.

- **Key Concepts Learned:**
  - **PythonOperator**: Used to run custom Python code for the transformation step.
  - **Database Integration**: Learned to use `BashOperator` to load data into SQLite, making the pipeline more dynamic.

### Business Context

In a business setting, this workflow could be used to track the composition of the S&P 500 companies by sector over time. By automatically aggregating and loading this data into a database, it becomes easier to analyze sector trends and make data-driven decisions for investment or business strategies.

### Challenges Faced

I ran into issues when trying to launch the Airflow webserver and scheduler using `airflow webserver -D` and `airflow scheduler -D`. The UI wouldn’t open, and I couldn’t start the webserver correctly. I tried various troubleshooting steps like killing the Airflow processes and reinitializing the database, but they didn’t resolve the issue. After investigating the issue, I realized this problem occurred because I was using Codespaces, which resets the environment after each session. As a result, the Airflow metadata database was no longer initialized, and the webserver couldn’t function properly.

After researching the issue, I found that the `airflow standalone` command provides a simplified setup that initializes both the database and webserver in one step, which is particularly useful for development environments like Codespaces.

- **Solution:** 
  Running the `airflow standalone` command worked because it not only starts the webserver but also handles the database initialization, ensuring that everything was set up correctly. This command helped bypass the need for separate commands to launch and initialize Airflow services, making it ideal for quick setups in development environments like Codespaces. This finding suggests that in transient environments like Codespaces, it’s important to initialize Airflow every time a new session starts to avoid issues with the database or webserver.

---

## Reflections and Key Takeaways

Practicing Airflow with Codespaces and SQLite allowed me to get a hands-on experience with data pipelines. I learned how to manage task dependencies, integrate custom Python logic, and handle systems like SQLite. Some DAGs posed its own challenges, from handling file downloads and redirects to managing task order and transforming data.

Despite the challenges, the ability to automate and manage complex workflows in a scalable way excites me, and I look forward to applying these skills to larger projects and more dynamic data environments!
