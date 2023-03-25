import cx_Oracle
import configparser

# Load configuration from file
config = configparser.ConfigParser()
config.read('config.ini')

# Connect to Oracle database
connection = cx_Oracle.connect(
    config['database']['username'],
    config['database']['password'],
    config['database']['dsn']
)

# Get hierarchy configuration from file
hierarchy = config['hierarchy']

# Define SQL query to retrieve hierarchical data
query = f"""
WITH hierarchy AS (
  SELECT {hierarchy['parent']} AS parent,
         {hierarchy['child']} AS child,
         1 AS level
  FROM {hierarchy['table']}
  WHERE {hierarchy['start']} = '{hierarchy['root']}'
  UNION ALL
  SELECT t.{hierarchy['parent']} AS parent,
         t.{hierarchy['child']} AS child,
         h.level + 1 AS level
  FROM {hierarchy['table']} t
  JOIN hierarchy h ON t.{hierarchy['parent']} = h.child
)
SELECT * FROM hierarchy
"""

# Execute query and print results
cursor = connection.cursor()
cursor.execute(query)
for row in cursor:
    print(row)

# Close database connection
connection.close()
This program uses the cx_Oracle library to connect to an Oracle database, and the configparser library to load a configuration file. The hierarchy configuration is specified in the configuration file using keys such as parent, child, table, start, and root. The SQL query is constructed using these configuration values to retrieve the hierarchical data. Finally, the program executes the query and prints the results.
