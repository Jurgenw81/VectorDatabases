# 🔎 Local Semantic Search

A private, local semantic search engine for your computer.

Search your **images using natural language**, find **visually similar images**, and perform **semantic search across text files** using vector embeddings.

Everything runs locally after the models are downloaded.

```text
"dog running outside"
        ↓
      CLIP
        ↓
 Query Vector
        ↓
      FAISS
        ↓
 Most Similar Images
```

## ✨ Features

- 🖼️ Search images using natural-language descriptions
- 🔍 Find images similar to another image
- 📝 Semantic search across `.txt` and `.md` files
- 🧠 CLIP embeddings for image/text matching
- ⚡ Fast vector similarity search with FAISS
- 📁 Recursive directory indexing
- 🔒 Local-first — your files do not need to be uploaded to a search service
- 💻 Works on Windows, macOS, and Linux

## How It Works

Traditional file search mostly relies on filenames and exact text matches.

This project converts your files into **vector embeddings** that represent their semantic meaning.

For image search:

```text
Images
  │
  ▼
CLIP
  │
  ▼
Image Embeddings
  │
  ▼
FAISS Vector Index
```

When you search for:

```text
sunset over mountains
```

the query is also converted into a CLIP embedding:

```text
"sunset over mountains"
          │
          ▼
        CLIP
          │
          ▼
     Query Vector
          │
          ▼
        FAISS
          │
          ▼
   Closest Image Vectors
          │
          ▼
      Matching Files
```

This means the words **do not have to appear in the filename**.

A file named:

```text
IMG_4837.jpg
```

can still appear when searching for:

```text
dog playing in snow
```

if the image content is semantically similar to the query.

## Tech Stack

The project uses:

- **Python**
- **Sentence Transformers**
- **CLIP**
- **FAISS**
- **Pillow**
- **NumPy**

### Models

Image search uses:

```text
sentence-transformers/clip-ViT-B-32
```

Text search uses:

```text
sentence-transformers/all-MiniLM-L6-v2
```

CLIP places images and text into a compatible embedding space, allowing natural-language queries to retrieve images.

The text embedding model creates semantic representations of document chunks for text-to-text retrieval.

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
cd YOUR_REPOSITORY
```

Replace `YOUR_USERNAME` and `YOUR_REPOSITORY` with your GitHub username and repository name.

### 2. Create a virtual environment

Recommended:

```bash
python -m venv .venv
```

Activate it on Windows:

```bash
.venv\Scripts\activate
```

On macOS/Linux:

```bash
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install sentence-transformers faiss-cpu pillow numpy tqdm
```

The embedding models will automatically be downloaded the first time they are used.

## Usage

### Index Your Images

Point the program at a directory containing your images.

Windows:

```bash
python local_search.py index-images "C:\Users\me\Pictures"
```

macOS/Linux:

```bash
python local_search.py index-images ~/Pictures
```

The directory is scanned recursively.

Supported image formats include:

```text
.jpg
.jpeg
.png
.webp
.bmp
.gif
```

The resulting vector database is stored in:

```text
.local_vector_db/
├── images.faiss
└── images.json
```

`images.faiss` contains the vector index.

`images.json` maps vectors back to the original files.

## Search Images With Text

Once your images have been indexed:

```bash
python local_search.py search-images "dog running outside"
```

Other examples:

```bash
python local_search.py search-images "sunset over mountains"
```

```bash
python local_search.py search-images "screenshot of programming code"
```

```bash
python local_search.py search-images "food on a plate"
```

```bash
python local_search.py search-images "car parked at night"
```

By default, the top 10 matches are returned.

Change the number of results with:

```bash
python local_search.py search-images "mountains" --limit 20
```

Example output:

```text
Image results for: "mountains"
======================================================================

 1. similarity=0.3124
    C:\Users\me\Pictures\vacation\IMG_4821.jpg

 2. similarity=0.2941
    C:\Users\me\Pictures\alps\DSC_1029.jpg

 3. similarity=0.2812
    C:\Users\me\Pictures\wallpapers\mountain.png
```

Higher similarity scores generally indicate a closer semantic match.

## Find Similar Images

You can also use an existing image as your search query.

```bash
python local_search.py similar-image example.jpg
```

The program embeds the supplied image and searches the database for images with similar vectors.

For example:

```bash
python local_search.py similar-image "C:\Users\me\Pictures\car.jpg"
```

To return more results:

```bash
python local_search.py similar-image car.jpg --limit 25
```

This can be useful for finding:

- similar photos
- duplicate-like images
- images of similar objects
- visually related screenshots
- photos from similar environments

## Text Search

The project can also create a separate semantic search index for text files.

Currently supported:

```text
.txt
.md
```

### Index Documents

Windows:

```bash
python local_search.py index-text "C:\Users\me\Documents"
```

macOS/Linux:

```bash
python local_search.py index-text ~/Documents
```

Documents are divided into smaller chunks before being embedded.

The resulting database is stored as:

```text
.local_vector_db/
├── text.faiss
└── text.json
```

## Search Documents

Search using normal language:

```bash
python local_search.py search-text "notes about machine learning"
```

You can also use conceptual queries:

```bash
python local_search.py search-text "where did I write about database performance?"
```

or:

```bash
python local_search.py search-text "ideas for improving the website"
```

The search does not require an exact keyword match.

For example, a query for:

```text
database performance
```

may find a paragraph discussing:

```text
reducing SQL query latency by adding indexes
```

because the embedding model considers the meanings semantically related.

## Project Structure

A simple repository structure looks like this:

```text
local-semantic-search/
│
├── local_search.py
├── README.md
├── requirements.txt
├── .gitignore
│
└── .local_vector_db/
    ├── images.faiss
    ├── images.json
    ├── text.faiss
    └── text.json
```

The `.local_vector_db` directory is generated automatically.

It should normally be excluded from Git.

## requirements.txt

You can create a `requirements.txt` containing:

```text
sentence-transformers
faiss-cpu
pillow
numpy
tqdm
```

Then installation becomes:

```bash
pip install -r requirements.txt
```

## Recommended .gitignore

```gitignore
# Vector database
.local_vector_db/

# Python
__pycache__/
*.py[cod]
*.pyo

# Virtual environments
.venv/
venv/
env/

# IDE
.vscode/
.idea/

# OS files
.DS_Store
Thumbs.db
```

## Commands

| Command | Description |
|---|---|
| `index-images <folder>` | Index all supported images |
| `search-images <query>` | Search images using natural language |
| `similar-image <image>` | Find images similar to another image |
| `index-text <folder>` | Index `.txt` and `.md` documents |
| `search-text <query>` | Semantic document search |

Examples:

```bash
# Index images
python local_search.py index-images ~/Pictures

# Search images
python local_search.py search-images "red sports car"

# Find similar images
python local_search.py similar-image car.jpg

# Index documents
python local_search.py index-text ~/Documents

# Search documents
python local_search.py search-text "machine learning ideas"
```

## Vector Search

Embeddings are normalized before being added to FAISS.

The project then uses:

```python
faiss.IndexFlatIP(dimension)
```

With normalized vectors, the inner product can be used for cosine-similarity-style ranking.

The basic process is:

```text
File
 ↓
Embedding Model
 ↓
Vector
 ↓
Normalize
 ↓
FAISS
```

A query follows the same process:

```text
Query
 ↓
Embedding Model
 ↓
Query Vector
 ↓
Normalize
 ↓
FAISS Search
 ↓
Nearest Vectors
 ↓
Files
```

## Privacy

The application is designed to run locally.

Your indexed files and vector database remain on your computer.

The embedding models need to be downloaded initially, but your local images and documents do not need to be uploaded to an external search service for normal indexing and searching.

## Limitations

This is intentionally a simple implementation.

Currently:

- Text indexing only supports `.txt` and `.md`
- Documents use basic character-based chunking
- Re-indexing recreates the index
- Deleted files are not automatically removed from an existing index
- There is no graphical interface
- Search results are printed to the terminal
- Image thumbnails are not displayed
- Metadata filtering is not implemented

For a few thousand or tens of thousands of personal files, the simple architecture is useful for experimentation. Much larger collections may benefit from approximate indexes, batching, metadata databases, and incremental indexing.

## Possible Improvements

Future versions could add:

- 🖥️ Web or desktop UI
- 🖼️ Image thumbnail results
- 📄 PDF indexing
- 📝 DOCX indexing
- 📊 XLSX indexing
- 🔄 Incremental indexing
- 👀 File-system watching
- 🗑️ Automatic deleted-file cleanup
- 🏷️ Metadata and tag filtering
- 📅 Date filtering
- 📂 Folder filtering
- 🧠 Better embedding models
- ⚡ GPU acceleration
- 🔀 Hybrid keyword + vector search
- 📸 EXIF metadata search
- 🗃️ SQLite metadata storage
- 🔎 Unified image + document search

A future interface could look conceptually like:

```text
┌─────────────────────────────────────────────────────┐
│ 🔎 Search your computer                             │
│                                                     │
│  dog playing in snow                         Search │
├─────────────────────────────────────────────────────┤
│                                                     │
│  🖼 IMG_4837.jpg       🖼 DSC_1021.jpg              │
│                                                     │
│  🖼 vacation.jpg       🖼 snow_trip.png             │
│                                                     │
│  📄 travel_notes.md                                 │
│     "...we took the dog hiking through..."          │
│                                                     │
└─────────────────────────────────────────────────────┘
```

The long-term goal is essentially a **private semantic search engine for your own computer**.

Instead of remembering:

> "What was that image called?"

you can search:

> "picture of my dog near a lake"

Instead of remembering:

> "Which file had those database notes?"

you can search:

> "notes where I talked about making SQL faster"

## License

Add the license you want to use for the project.

For an open-source project, the MIT License is a common permissive option.

## Contributing

Contributions, issues, and feature requests are welcome.

Potential areas to contribute include document parsers, UI improvements, indexing performance, additional embedding models, metadata search, and cross-platform support.
