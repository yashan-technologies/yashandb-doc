## What is Vector Database

A vector is simply a set of numbers that represents the features of an object, like a document, an image, a video, or audio file.Compared to traditional mature structured data storage and computation, the processing and computation of vector data primarily rely on vector calculations to identify entities in object data that exhibit a certain degree of similarity.

A vector database can natively store and manage vector embeddings.

The vector databases emergence fills the huge gap traditional relational database have in handling unstructured data and semantic understanding.  

## Introduction of Yashan Vector Database

Meanwhile, versions of established favorites database have incorporated vectors as a native data type alongside many other data types.This allows searches on a combination of business and semantic data to be faster and more precise, as both data types are available in a single database.

Combining relational data, JSON documents, graphs, geospatial data, text, and vectors in a single database, avoids the data consistency problems introduced when using a separate.

Simultaneously, Yashan Vector Database has undergone performance optimization tailored for common CPU instruction sets, resulting in outstanding vector calculation performance.

The Yashan Vector Database offers the following capabilities and characteristics:

- Vectorization: Vectors can be created to describe the contents or features of unstructured data.

- Indexing: Vector databases use vector indexes to organize vectors in a hierarchical manner, allowing for their efficient search and retrieval.

    The YashanDB implements vector indexing using the HNSW (Hierarchical Navigable Small World) algorithm. Its purpose is to efficiently locate the k nearest neighbors within an extremely large candidate set.

    Currently, the HNSW algorithm holds the greatest influence in industrial applications among graph-based approximate nearest neighbor indexing algorithms. It employs a hierarchical graph data structure, partitioning nodes into different levels. The algorithm traverses elements from higher levels until it reaches a local minimum, then switches to the next level, using the local minimum from the previous level as a new starting point for traversal. This process continues until the lowest level is exhausted.

- Querying Vector data: Vector databases perform vector distance operations using a query vector.The closer vectors are mathematically, the more similar are the objects that the vectors represent.

- Post processing: After a vector database retrieves a query vector's nearest neighbors, it may optionally re-rank the rows of the result set.

## Core Workflow

The principal workflow involves:

1. Collect raw unstructured data (text, images, audio, video, etc.), and then clean, standardize, and format them;
2. Generate high-dimensional feature vectors through embedding models (e.g., BERT, CLIP, ResNet, etc.), and normalize vectors to unit length for easier similarity calculation
3. Establish mapping relationships between vector IDs and original files/content, and choose a vector database to manage them;
4. Choose appropriate vector index algorithm based on data scale and performance requirements. The YashanDB implements HNSW indexing.
5. During querying, use the input vector values to search for similar vectors in the database:
    - Cosine similarity
    - Euclidean distance
    - Dot product similarity
6. Sort the similarity results and associate and return the original data of the similar objects.

## Use Cases  

The vector database is used to cover fields that require "understanding" content of the object rather than simple keyword matching.  

|Scenarios             |Application             |Description        |
| ---------------------------------- | ------------------------------------------------ | ------------------------------------------------------------ |
| Search & Recommendation        | Semantic Search             | Users search in natural language (e.g., "mood music for a rainy day"), the system understands intent and returns relevant results, not just keyword matches. |
|                                    | Personalized Recommendation                  | Finds the most matching new content for recommendation based on user historical behavior vectors (representing interests) and content vectors. |
|                                    | E-commerce Search                            | Search for similar items by photo, or find products using textual descriptions (e.g., "ceramic mug with retro patterns"). |
| AI & Machine Learning          | Large Language Model (LLM) Enhancement       | Core component of Retrieval-Augmented Generation (RAG). Provides real-time, accurate external knowledge bases for LLMs, addressing "hallucinations" and outdated knowledge issues. |
|                                    | Multimodal AI                                | Unifies processing of text, images, and videos, enabling cross-modal retrieval such as "search images by text" and "search videos by image". |
|                                    | Model Feature Repository                     | Stores and quickly retrieves feature vectors generated by machine learning models for comparison and inference. |
| Content & Knowledge Management | Enterprise Intelligent Knowledge Base        | Vectorizes internal documents, emails, and meeting minutes, enabling employees to ask questions naturally as if consulting an expert to obtain information. |
|                                    | Content Deduplication & Copyright Protection | Identifies highly similar images, articles, or videos through vector comparison to detect plagiarism or duplicate content. |
|                                    | Intelligent Customer Service & Q&A           | Quickly locates the most semantically matching answer to user questions from massive Q&A pairs and documents. |
| Bioinformatics & Security      | Facial Recognition/Biometric Identification  | Stores facial features as vectors for millisecond-level comparison and identity verification. |
|                                    | Fraud Detection                              | Vectorizes user transaction behavior to quickly identify anomalous behavior similar to known fraud patterns. |
|                                    | Drug Discovery                               | Represents molecular structures as vectors to search for candidate molecules with similar pharmacological properties in vast compound libraries. |

## Core Advantages 

Yashan vector database advantages Over Traditional Relational Databases are as follows:

|Item         |Description         | Comparison     |
| ------------------ | ------------------- | ----------------- |
| Efficient Similarity Search                    | Uses advanced indexing algorithms like HNSW to achieve millisecond retrieval across billions of vectors. | Traditional databases' B-Tree indexes cannot effectively support similarity calculations (e.g., cosine similarity) for high-dimensional vectors; they only allow exact matches with poor performance. |
| Powerful Semantic Understanding                | Measures semantic similarity between data based on vector distance (e.g., Euclidean distance, inner product), rather than literal matching. | Traditional keyword search cannot handle "synonyms" (e.g., "computer" and "laptop") or "semantic associations" (e.g., "king" and "queen"). |
| Native Support for High-Dim, Unstructured Data |  Data models, query languages, and storage engines are designed around vectors, seamlessly integrating with AI workflows. | Traditional databases need to store vectors as BLOBs or arrays, cannot build effective indexes, and require full table scans for queries, resulting in unacceptable performance. |
| Flexibility                                    | Typically schema-less or dynamic schema, facilitating rapid iteration and addition of new data vector types. | Relational databases require pre-defined strict table structures with high change costs. |
