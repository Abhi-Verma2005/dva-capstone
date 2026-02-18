# Data Cleaning & Preparation Process

This document details the steps taken to prepare the raw dataset for analysis. The goal was to create a representative and clean dataset for analyzing the geography of privatization in Indian higher education.

## 1. Dataset Sampling
The original dataset contained **47,590 rows**. To ensure manageable processing while maintaining statistical significance, we created a working dataset of **9,000 rows**.

We used a **Stratified Random Sampling** approach to ensure every state was represented adequately.

### Methodology:
1.  **Minimum Allocation:** We allocated a minimum of 30 colleges to each state to ensure smaller states were not excluded.
2.  **Proportional Distribution:** The remaining rows were distributed proportionally based on the total number of colleges in each state.
3.  **Random Selection:** Within each state, colleges were selected randomly to avoid bias.

### Python Script
The following script was used to generate the sampled dataset:

```python
import pandas as pd

# Load the raw data
df = pd.read_csv("Cleaned College.csv")

TARGET = 9000
MIN_PER_STATE = 30

# Calculate counts per state
state_counts = df['State'].value_counts()
states = state_counts.index

# Step 1: Initial allocation (min 30 per state)
allocation = {state: min(MIN_PER_STATE, state_counts[state]) for state in states}
current_total = sum(allocation.values())
remaining = TARGET - current_total

# Step 2: Distribute remaining rows proportionally
capacity = {state: state_counts[state] - allocation[state] for state in states}

while remaining > 0:
    for state in states:
        if capacity[state] > 0:
            allocation[state] += 1
            capacity[state] -= 1
            remaining -= 1
            if remaining == 0:
                break

# Step 3: Perform the sampling
sampled_df = pd.concat([
    df[df['State'] == state].sample(n=allocation[state], random_state=42)
    for state in states
])

print("Final rows:", len(sampled_df))
print(sampled_df['State'].value_counts())
```

## 2. Handling Missing Values
To ensure data completeness and prevent errors during analysis:
- Empty cells were identified across all columns.
- Missing values were replaced with placeholders like **"Not Specified"** or **"Not Defined"**.
- This approach allowed us to keep the rows for other valuable data points rather than discarding them.

## 3. Data Type Formatting
- Columns were reviewed to ensure they had the correct data types (e.g., Integer for 'Year of Establishment', String for 'State').
- This step ensures consistency and allows for accurate aggregation and date-based analysis.

## 4. Data Splitting & parsing
- **University/College Names:** The original dataset often combined IDs with names. we used delimiter-based splitting to separate the `University ID` from `University Name` and `College ID` from `College Name`.
- This separation allows for cleaner grouping and visualization of specific institutions.
