# Code-Writing-Guide
---
## 1. Purpose
  The purpose of this guide is to ensure consistency in the project code base, improve readability, and enhance code quality. Adhering to these rules will both facilitate the maintenance of existing code and accelerate the adaptation process for new developers joining the project. 

## 2. Naming Conventions

### 2.1. Variables
•    Format: All variable names should be written in lowercase letters with underscores (_) between words (snake_case).

•    Descriptions: The variable name should clearly define the data it contains. Single-letter or generic names should be avoided.


    Bad
    
    data = ...
    
    sim = ...
    
    x = ...

    Good
    
    user_transactions_df = ...
    
    user_similarity_matrix = ...
    
    customer_id_list = ...

### 2.2. Functions and Methods
•    Format: Function and method names should also be in snake_case format. Snake case separates each word with an underscore character (_). When snake case is used, all letters must be lowercase.

•    Naming: The function name should express the action it performs (usually starting with a verb).

    Bad
    
    rules()   What rule? What does it do?
    
    calculate()
    
    Good
    
    generate__rules()
    
    calculate_request()


### 2.3. Classes
•    Format: Class names should be written as one word with the first letter of each word capitalized (PascalCase or CapWords).

    Good
    
    class RuleGenerator:
    
       ...
        
    class DataPreprocessor:
    
      ...
    


### 2.4. Constants
•    Format: These are constant variables whose value will not change throughout the project. They are written in uppercase letters and snake_case (UPPER_SNAKE_CASE). This prevents “magic values” from circulating in the code. A magic value is a fixed numerical or text value used to define a file format.

      Bad
      if method == 'apriori':
          ...
      if relevance_level > 0.8:
          ...
      Good
      It is defined at the very beginning of the file or class.
      METHOD_NAME = 'name'
      
      if method == METHOD_NAME:
          ...

## 3. Function and Method Design
### 3.1. Single Responsibility Principle
Each function or method should do only one thing and do it well. Functions that are long, contain dozens of lines, and involve multiple logical steps must be broken down.
  •  Rule: If you use the conjunction “and” to describe what a function does, it is probably doing more than one thing. (E.g., “It takes the data and cleans it and generates the rules.”)
  
    Example: Splitting a Long Method
    
    Bad: A single method does too much work
    def generate_association_rules(data, division):
        # 1. Data is filtered and prepared
        division_data = data.groupby(“DIVISION”).get_group(division)
        filtered_data = division_data[division_data[‘FLAG’] == 1]
        ...
        # 2. Find frequent itemsets
        frequent_itemsets = apriori(filtered_data, min_support=0.01, use_colnames=True)
        ...
        # 3. Create rules
        rules = association_rules(frequent_itemsets, metric="lift", min_threshold=1)
        ...
        # 4. Rules are formatted and missing data is filled in
        rules[‘antecedents’] = rules[‘antecedents’].apply(lambda x: ‘, ’.join(list(x)))
        ...
        return rules
        
    Good: Small helper methods, each performing a single task
    class RuleGenerator:
        def generate_rules(self, data, division):
            prepared_data = self._prepare_data_for_division(data, division)
            frequent_itemsets = self._find_frequent_itemsets(prepared_data)
            rules = self._create_association_rules(frequent_itemsets)
            final_rules = self._format_and_finalize_rules(rules)
            return final_rules
### 3.2. Type Hinting
•    Rule: Type hints must be used for all function and method parameters and return values. This improves code readability and enables early error detection.

    Bad
    def function(number, num_of_iter = 50):
        ...
    def process_data(users_dataframe: object):
        ...
      
    Good
    import pandas as pd
    from typing import List
    def function(number: float, num_of_iter: int = 50) -> List[float]:
        ...
    def process_data(users_dataframe: pd.DataFrame) -> pd.DataFrame:
        ...
        
### 3.3. Avoiding Nested Loops
•    Rule: Avoid creating more than 3 levels of nested if/for blocks. Deep indentations make it difficult to follow the logic of the code. Instead, move the inner logic to a separate function or use the “guard clause” method. The Guard Clause method is a technique that flattens nested conditions into a single dimension and allows early return from a function. Instead of using if/else chains, it exits the function when the condition is met without checking other conditions.

## 4. Code Structure and Readability

### 4.1. Avoid Repetition (DRY - Don't Repeat Yourself)
•    Rule: Code should not be duplicated using copy-paste. If the same or very similar code block is used in multiple places, this block should be immediately converted into a function.

### 4.2. Data Structure Selection
•    List: Should be used if the order of elements is important and elements need to be accessed by index (0, 1, 2...).

•    Dictionary (Dict): Should be used if each element needs to be accessed quickly with a unique key. Data lookup performance is much higher than with lists.

•    Set: Should be used when only unique elements need to be stored and the order of the elements is not important. It is the fastest structure for checking whether an element is in the collection.

•    Tuple: Similar to a list but immutable. It is preferred in situations such as when a function returns multiple values or when it is used as a key in a dictionary.

## 5. Comments and Documentation
### 5.1. Docstrings
•    Rule: Every module, class, and function must have a docstring at the beginning.

•    Format: Docstrings should clearly state what the function does, the parameters it takes (Args), and what it returns (Returns). (Google Style or reStructuredText format is recommended).

        def calculate_similarity(user_vector: List[float], item_vector: List[float]) -> float:
            “”"Calculates the cosine similarity between a user and an item vector.
        
            Args:
                user_vector: A list of floats representing the user's preferences.
                item_vector: A list of floats representing the item's features.
        
            Returns:
                The cosine similarity score, a float between -1 and 1.
            “”"
            # ... function code ...
            
### 5.2. Unnecessary Comments and Code
•    Rule: Code that has been commented out (# print(...), # start = time.time()) must be completely removed from the code base. The Git version control system should be used to access older versions of the code. The code base should always be clean and functional.

