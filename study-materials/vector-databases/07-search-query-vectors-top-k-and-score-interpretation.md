# Search with Query Vectors, Top-K Results, and Scores

## What this lesson is about

In the earlier lessons I created a collection and stored vectors in it. That is only half of a vector database workflow. The useful part begins when an application receives a question, converts that question into a vector, and asks the database to find the closest stored vectors.

This lesson explains that search flow from the beginning. I will learn what a query vector is, what Top-K means, how to interpret a similarity score, and how to turn search results into useful application data.

## 1. How a vector search request works

Imagine I have stored three support articles:

- Reset a forgotten password.
- Configure multi-factor authentication.
- Update a billing address.

A user asks: “I cannot sign in because I lost access to my password.”

The application does not normally search the vector database using the raw sentence as if it were a normal SQL text column. It sends the sentence to the same compatible embedding model used during document ingestion. The model returns a query vector.

The database compares that query vector with the stored document vectors and returns nearby records.

The flow is:

1. Receive the user's question.
2. Validate the request and identify the user's permitted data scope.
3. Generate an embedding for the question.
4. Search the correct collection using that vector.
5. Request the most relevant K results.
6. Inspect each result's score and metadata.
7. Return useful context to the application or RAG pipeline.

The query embedding model, dimensions, preprocessing, and vector configuration must be compatible with the stored document vectors.

## 2. What does Top-K mean?

K is the maximum number of nearest records requested from the database. If K is 5, I am asking for up to five candidates.

A larger K can improve recall because more potentially relevant records are considered. However, it can also add noise, increase response size, and increase the work required by a later reranker or language model.

A smaller K is faster and simpler, but may omit useful context.

I should not assume that K=5 is correct for every application. I should test values such as 3, 5, 10, and 20 against a set of real questions and known relevant documents.

## 3. Search a Qdrant collection

The following example assumes that the local Qdrant collection from lesson 6 contains three-dimensional demonstration vectors. The vectors are synthetic and are only suitable for learning the API. They are not meaningful production embeddings.

~~~python
from qdrant_client import QdrantClient

client = QdrantClient(path="./qdrant_data")

query_vector = [0.12, 0.85, 0.20]

response = client.query_points(
    collection_name="support_articles",
    query=query_vector,
    limit=3,
    with_payload=True,
)

for result in response.points:
    print("ID:", result.id)
    print("Score:", result.score)
    print("Payload:", result.payload)
~~~

What each important part means:

- collection_name selects the collection to search.
- query is the vector representing the user's question.
- limit is the requested Top-K count.
- with_payload asks Qdrant to include the stored metadata.
- Each returned point contains an ID, a score, and optionally its payload.

The exact score direction and range depend on the configured distance metric and database behavior. I must read the documentation for the metric I selected rather than assume that a larger or smaller number always means better.

## 4. Understand the score

A score is a ranking signal. It is not automatically a probability that a document is correct.

For example, a similarity score of 0.82 does not necessarily mean there is an 82 percent chance that the result answers the question. Scores are affected by the embedding model, metric, domain, text length, and dataset.

I should create an evaluation set with questions and expected relevant documents. Then I can inspect the score distributions for relevant and irrelevant results.

A production application can use a minimum score threshold, but the threshold must be measured with the selected model and dataset. A threshold copied from another project may be inappropriate.

## 5. Search quality concepts

- **Recall:** Of all the relevant documents, how many did the search retrieve?
- **Precision:** Of the retrieved documents, how many were relevant?
- **Latency:** How long did the search request take?
- **Throughput:** How many search requests can the system handle in a time period?
- **Top-K relevance:** Whether the best results are near the beginning of the returned list.

Recall and precision can trade off. Increasing K often helps recall but can reduce precision by adding weaker candidates.

## 6. Use the results in an application

A useful application should not blindly return raw vectors to its user. It should map the returned IDs and payload fields to a clear response model.

For a support knowledge assistant, a result might include:

- Article title.
- Short excerpt.
- Article URL or internal ID.
- Source type.
- Similarity score for diagnostics.
- Version or last-updated information.

The application should still verify that the requesting user is allowed to view each result. Search relevance is not an authorization decision.

## 7. Common mistakes

1. Embedding the query with a different model from the document model.
2. Searching the wrong collection or tenant.
3. Returning too many candidates without measuring quality.
4. Treating similarity as a confidence probability.
5. Ignoring metadata and returning only IDs.
6. Allowing a high score to bypass authorization.
7. Using synthetic example vectors as if they represented real language.

## Practice exercise

1. Run the query example against the local collection from lesson 6.
2. Change the query vector and observe whether the result order changes.
3. Change Top-K from 1 to 3 and then to 5.
4. Print the ID, score, and payload for every result.
5. Write down why a score is a ranking signal rather than a probability.
6. Design five realistic support questions and identify which article should be returned for each.

## Self-check

- What does a query vector represent?
- What does Top-K control?
- Why must the query and stored document vectors be compatible?
- Why is a similarity score not automatically a probability?
- What is the difference between precision and recall?
- Why should authorization be checked separately from relevance?

## Key takeaway

Vector search returns nearby candidates. A good application still needs to interpret those candidates, evaluate relevance, enforce access rules, and present useful source information.
