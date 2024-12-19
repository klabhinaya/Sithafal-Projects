# Load required libraries
library(rvest)
library(RSQLite)
library(httr)

# 1. Web Scraping Function to Extract Text and Webpage Object
scrape_website <- function(url) {
  webpage <- read_html(url)  # This function is from the rvest package
  
  # Extract textual content from various HTML tags
  text <- webpage %>%
    html_nodes("p, h1, h2, h3, h4, h5, h6, li") %>%
    html_text() %>%
    paste(collapse = " ")  # Combine all text into one string
  
  return(list(text = text, webpage = webpage))  # Return both text and webpage object
}

# 2. Convert Text to Embeddings Using Hugging Face API
convert_to_embeddings <- function(text) {
  # Hugging Face API URL for sentence-transformers
  api_url <- "https://api-inference.huggingface.co/models/all-MiniLM-L6-v2"  # Use any Hugging Face model
  
  # Set up the headers (Replace with your Hugging Face API key)
  headers <- add_headers(Authorization = paste("Bearer", "your_huggingface_api_key"))
  
  # Request payload (input text for embedding generation)
  body <- list(inputs = text)
  
  # Send POST request to Hugging Face API
  response <- POST(api_url, body = body, encode = "json", headers)
  
  # Parse the response to get embeddings
  embeddings <- content(response)$embeddings
  
  return(embeddings)
}

# 3. Store Embeddings and Metadata in SQLite Database
store_embeddings <- function(embeddings, metadata, db_path = "embeddings.db") {
  conn <- dbConnect(SQLite(), db_path)
  
  # Create table if it doesn't exist
  dbExecute(conn, "CREATE TABLE IF NOT EXISTS embeddings (id INTEGER PRIMARY KEY, vector BLOB, metadata TEXT)")
  
  # Ensure embeddings are vectors and have the same length
  for (i in 1:length(embeddings)) {
    # Convert the embedding to a raw vector (serialize the numeric vector)
    embedding_vector <- raw(embeddings[[i]])  # Convert numeric vector to raw
    
    # Debug: Print the embedding and metadata
    print(embedding_vector)
    print(metadata[i])
    
    # Store embeddings and metadata in the database
    dbExecute(conn, "INSERT INTO embeddings (vector, metadata) VALUES (?, ?)", 
              params = list(embedding_vector, metadata[i]))
  }
  
  dbDisconnect(conn)
}

# 4. Retrieve Relevant Chunks Based on User Query
retrieve_relevant_chunks <- function(query, db_path = "embeddings.db") {
  conn <- dbConnect(SQLite(), db_path)
  
  # Fetch all embeddings and metadata from the database
  res <- dbGetQuery(conn, "SELECT * FROM embeddings")
  
  dbDisconnect(conn)
  
  # Convert query to embeddings
  query_embedding <- convert_to_embeddings(query)
  
  # Compute cosine similarity between query and stored embeddings
  similarities <- sapply(res$vector, function(vec) {
    cosine_similarity(query_embedding, unserialize(vec))  # Use cosine similarity to measure relevance
  })
  
  # Retrieve top N most similar chunks
  top_n_indices <- order(similarities, decreasing = TRUE)[1:5]  # Get top 5 relevant chunks
  top_chunks <- res[top_n_indices, ]
  
  return(top_chunks)
}

# 5. Generate Response Using LLM (Example: OpenAI GPT-3 API)
generate_response <- function(relevant_chunks, query) {
  # Prepare prompt with retrieved context
  context <- paste(relevant_chunks$metadata, collapse = " ")  # Concatenate relevant metadata
  prompt <- paste("Based on the following context, answer the question:", query, "\n\n", context)
  
  # OpenAI API call (Make sure to replace with your actual API key)
  response <- POST(
    "https://api.openai.com/v1/completions", 
    add_headers(Authorization = paste("Bearer", "your_api_key")),
    body = list(
      model = "gpt-3.5-turbo", 
      prompt = prompt,
      max_tokens = 150
    ),
    encode = "json"
  )
  
  # Parse response
  content <- content(response, "parsed")
  return(content$choices[[1]]$text)
}

# Example Usage

# 1. Scrape Website
url <- "https://example.com"
scraped_data <- scrape_website(url)  # Get both scraped text and webpage
scraped_text <- scraped_data$text
webpage <- scraped_data$webpage  # Extracted webpage object for metadata

cat("Scraped Text:\n", scraped_text)

#
