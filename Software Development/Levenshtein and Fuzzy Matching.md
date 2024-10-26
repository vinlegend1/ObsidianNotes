- Levenshtein's distance↔intends to calculate the dissimilitude between two strings
    - This metric represents the number of operations that need to be applied to one of the strings to make it equal to the other one.
    - Such operations include the following>>>
        - insert
        - delete
        - substitution
- Levenshtein's distance in [PostgreSQL](../Software Development/PostgreSQL.md) 
- .
    ```
    CREATE EXTENSION fuzzystrmatch;
    ```
- .
    ```
    SELECT name FROM user WHERE levenshtein('Marcus', name) <= 1;
    ```
- .
    ```
    SELECT *, LEVENSHTEIN(name || "stockNumber", '8271')
FROM "Item"
ORDER BY LEVENSHTEIN(name || "stockNumber", '8271') ASC
LIMIT 5
    ```
- 
