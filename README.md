# Document Chatbot – CSV & Website URL

A Streamlit chatbot that lets you chat with your own data. Upload a CSV file or enter a website URL, and the app loads the content as context for an AI model. You can then ask questions about it in a chat interface.

## Features

- **CSV ingestion**: upload a `.csv` file and see its shape, its column names and a preview of the first rows.
- **Website ingestion**: enter a URL and the app fetches the page and pulls the text from its `<p>` paragraphs. A short snippet of that text is shown on screen.
- **Chat interface**: the chat uses user and AI message bubbles and keeps the conversation history for the session.
- **AI processing**: questions go through a [LangGraph](https://github.com/langchain-ai/langgraph) workflow run with the [Euri AI](https://euron.one/euri) SDK. The default model is `gpt-4.1-nano`.

## Project Structure

```
chatbot_csv_url/
├── app.py            # Streamlit UI: sidebar ingestion, data details, chat
├── backend.py        # CSV loading, website crawling, Euri AI / LangGraph pipeline
├── requirements.txt  # Python dependencies
├── .env.example      # Template for environment variables
└── .gitignore
```

### How it works

1. `app.py` reads either an uploaded CSV (`ingest_csv`) or a URL (`crawl_website`) and stores the text in the Streamlit session state.
2. When you send a message, the stored context and your question are combined into one prompt.
3. `index_data` in `backend.py` passes that prompt to a single-node LangGraph workflow (`processor`) built with `EuriaiLangGraph`. The response is shown in the chat.

## Packages

| Package            | Purpose                                              |
|--------------------|------------------------------------------------------|
| `euriai[langgraph]`| Euri AI SDK with the LangGraph integration (LLM calls) |
| `langgraph`        | Graph-based orchestration of the AI workflow         |
| `streamlit`        | Web UI and chat interface                            |
| `pandas`           | Reading and previewing CSV files                     |
| `requests`         | Fetching website content                             |
| `beautifulsoup4`   | Parsing HTML and extracting page text                |
| `python-dotenv`    | Loading the API key from `.env`                      |

## Installation

### Prerequisites

- Python 3.10 or later
- A Euri AI API key

### Steps

1. **Clone the repository**

   ```bash
   git clone <repository-url>
   cd chatbot_csv_url
   ```

2. **Create and activate a virtual environment**

   ```bash
   python3 -m venv venv
   source venv/bin/activate        # On Windows: venv\Scripts\activate
   ```

3. **Install the dependencies**

   ```bash
   pip install -r requirements.txt
   ```

4. **Configure environment variables**

   Copy the example file and add your API key:

   ```bash
   cp .env.example .env
   ```

   ```env
   EURI_API_KEY="your-euri-api-key-here"
   ```

5. **Run the app**

   ```bash
   streamlit run app.py
   ```

   The app opens at `http://localhost:8501`.

## Usage

1. In the sidebar, either upload a CSV file or enter a website URL.
2. Check the **Data Details** section to confirm the data loaded correctly.
3. Type a question in the chat box at the bottom of the page.

## Notes

- The whole CSV or page text is sent as context with every message, so very large files or pages can exceed the model's context limit.
- The crawler only reads `<p>` tags from a single page. It does not follow links.
- `.env` is listed in `.gitignore`. Never commit your API key.
