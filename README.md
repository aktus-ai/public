## How to Use the Notebook

### Step 1: Install Aktus Client

**Note**: This library requires Python 3.9 or above.

```python
# Install the Aktus library
!pip install aktus_unify-0.1.0-py3-none-any.whl
```

### Step 2: Import Packages
```python
# Import necessary modules from the Aktus library
from aktus_unify import ocr, embedding, chat
```

### Step 3: Define `domain` and `usecase`
```python
# Define the domain and use case for which embeddings are created.
# Update these variables with the specific folder paths you wish to process.
domain = ""  # Path to the domain folder containing documents
usecase = ""  # Sub-folder or specific use case within the domain

# Create an embedding for all documents in the specified folder
# Replace 'domain' and 'usecase' with appropriate values.
embedding_response = embedding.create_embedding(domain=domain, usecase=usecase, id="0")

# Print the response from the embedding creation to track progress
print(embedding_response)
```

**Expected Output:**
The code will output a dictionary containing the embedding status for each file in the specified folder. Example:

```plaintext
handbook-revenue-recognition-40-47-7.pdf: {
    'id': 'unique-identifier',
    'status': 'SUCCESS',
    'date_created': 'timestamp',
    'date_updated': 'timestamp',
    'expiration_date': None,
    'result': {'result': None, 'task_name': 'notify_vdb_endpoint'},
    'service_name': 'service-ocr',
    'task_metadata': {
        'llm_model': None,
        'document_path': '/data/blob/document_upload/<filename>.pdf'
    }
}
```

**Important Notes:**
- Multiple dictionaries may be printed if multiple files exist in the specified folder.
- Ensure the `status` is `SUCCESS` and `task_name` is `notify_vdb_endpoint` to confirm ingestion.

---

### Step 4: Check Task Status
```python
# Use the task IDs from the embedding response to check their status
# Replace 'embedding_response["task_ids"]' with the appropriate field from your response.
tasks.check_tasks_status(embedding_response["task_ids"])
```

---

### Step 5: Ask Questions
```python
from IPython.display import display, Markdown

# Ask a question based on the processed embeddings
# Replace 'user_question' with the actual question string.
# 'domain' and 'usecase' should match the values provided earlier.
async for line in chat.ask_question(
    user_question="",  # Add the user question here as a string
    domain=domain,
    usecase=usecase
):
    # Display the output in Markdown format for better readability
    display(Markdown(line))
```
