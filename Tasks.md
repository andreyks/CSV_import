## Main Task

### Background

All the different teams in our office use Excel to save information relevant to their individual teams. For example, the Facilities team use it to store the stock levels of drinks in the fridge. The Sales team use it to log upcoming contract renewals. HR uses it to store information about the employees.
Overnight, Excel has been deemed insecure. The board has met and decided we need to transfer the data from the Excel files to SQL tables. 

### Your Task
As a developer you are required to write a **PHP Symfony Command Line Console application** that:
- Accepts a CSV file as input (representing a single table)
- Reads the header row for column names
- Processes subsequent data rows
- Outputs an SQL CREATE TABLE statement suitable for storing this data

Note: You do not need to create the table itself or populate it with the data from the CSV files.

### Sample CSV
Below is an example CSV that you could run your code against.

    Name,Age,Grade,Salary
    Alice Smith,29,L3,55000.50
    Bob Johnson,34,L4,62000
    Charlie Lee,41,L5,72000.75
    Diana Patel,27,L2,48000
    Edward Kim,38,L4,67000.25
    Fiona Brown,25,L1,53000
    George White,32,L3,58900.80
    Hannah Black,36,L4,65000

### Submission Instructions

- Push your code to a **public repository**  
- Include a README.md with setup and execution instructions
- Email us the repository link
- Expected time commitment: 2-3 hours

## Additional Credit: Demonstrate your ElasticSearch knowledge

BackgroundThe board was impressed with your SQL migration script. However, they've realized that with data now spread across dozens of SQL tables (Facilities, Sales, HR, etc.), finding specific information quickly has become challenging. The architecture team has decided to implement a centralized search using **ElasticSearch** or another open search option.

### Your Task

How would you extend your existing Symfony application to handle indexing and searching the employee data?

Points to consider:

- What would the document and mapping file look like?
- These CSVs could potentially grow in size to 100k+ rows.
- Should any fields support fuzzy matching?
- How would we apply filters or custom sorting?

How you complete this task is up to you. You could fully code it, use some sudo code focusing on key elements that you would need to produce or share whiteboard / drawings that you think might be useful, but **please create at least document and mapping file**.

In your README.md, please include a brief paragraph (150-200 words) explaining:

"How would you architect keeping the SQL database and ElasticSearch in sync in a production environment when a user updates an employee's salary via a web interface?"