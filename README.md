## How to Use the Notebook

### Step 1: Install Aktus Client

**Note**: This library requires Python 3.9 or above.

```python
# Uninstall existing version (if any)
!pip uninstall -y aktus_unify-0.1.0-py3-none-any.whl

# Install the Aktus library
!pip install aktus_unify-0.1.0-py3-none-any.whl
```

### Step 2: Import Packages
```python
# Import necessary modules from the Aktus library
from aktus_unify import chat, embedding, tasks
```

### Step 3: Define Domain, Usecase and Start Ingestion
```python
# Define the embedding endpoint
EMBEDDING_ENDPOINT = "http://<your-embedding-endpoint>:8080/vectorindex/embed"

# Define the domain and use case
domain = ""  # Update with your domain
usecase = ""  # Update with your usecase

# Create an embedding for all documents in the domain/usecase folder
embedding_response = embedding.create_embedding(
    domain=domain, 
    usecase=usecase, 
    id="0", 
    endpoint_url=EMBEDDING_ENDPOINT
)

# Print the response from the embedding creation
print(embedding_response)
```

### Step 4: Check Task Status
```python
# Define the database manager endpoint
DB_MANAGER_ENDPOINT = "http://<your-db-manager-endpoint>"

# Get tasks status
tasks_response = tasks.check_tasks_status(
    task_ids=embedding_response["task_ids"], 
    endpoint_url=DB_MANAGER_ENDPOINT
)

# Print status for each document
for k, v in tasks_response.items():
    print(f"Document_name: {k} | Status: {v}")
```

**Expected Output Format:**
```
<document-name>:
    {
        'id': '<some-uuid>',
        'status': 'SUCCESS',
        'date_created': '<some-utc-timestamp>',
        'date_updated': '<some-utc-timestamp>',
        'expiration_date': None,
        'result':
            {
                'result': None,
                'task_name': 'notify_vdb_endpoint'
            },
        'service_name': 'service-ocr',
        'task_metadata':
            {
                'llm_model': None,
                'document_path': '<path-to-document-on-azure-blob>'
            }
    }
```

**Important Notes:**
- Multiple dictionaries may be printed if multiple files exist in the specified folder.
- The ingestion for a specific file is complete only when:
  - `status` is `SUCCESS`
  - `task_name` is `notify_vdb_endpoint`
- If these conditions are not met, rerun the status check to see updates.

### Step 5: Chat with Your Documents
```python
from IPython.display import display, Markdown

# Define the chat endpoint
CHAT_ENDPOINT = "http://<your-chat-endpoint>:8080/chat/copilot"

# Ask questions about your documents
async for line in chat.ask_question(
    user_question="",  # Add your question here
    domain=domain,
    usecase=usecase,
    endpoint_url=CHAT_ENDPOINT
):
    display(Markdown(line))
```
