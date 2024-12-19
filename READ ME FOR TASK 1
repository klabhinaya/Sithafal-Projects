 - IMPORT NECESSARY LIBRARIES:

   A) PyPDFLoader: Loads the PDF document.
   B) CharacterTextSplitter: Splits the document into smaller chunks for efficient processing.
   C) OpenAIEmbeddings: Creates embeddings for the document chunks using OpenAI's embedding model.
   D) FAISS: Creates a vector store for efficient similarity search using FAISS.
   E) RetrievalQA: Defines the RetrievalQA chain for question answering.

 - LOAD AND SPLIT THE PDF:

   A) PyPDFLoader loads the PDF document.
   B) CharacterTextSplitter splits the document into smaller chunks.
   
  - CREATE EMBEDDINGS AND VECTOR STORE:

    A) OpenAIEmbeddings creates embeddings for each chunk.
    B) FAISS creates a vector store to efficiently store and search the embeddings.
    
  - DEFINE THE LLM:

    A)OpenAI initializes the OpenAI LLM with the specified model name.
    
  - CREATE THE RETRIEVALQA CHAIN:
    
    A)RetrievalQA.from_chain_type() creates a RetrievalQA chain that combines the LLM and the vector store.
    
  -  GET USER INPUT:

     A)Prompts the user to enter their question.
     
   - GENERATE ANSWER:
     
     A)The qa_chain.run() method retrieves relevant chunks from the vector store based on the user's query and generates an answer using the LLM.
