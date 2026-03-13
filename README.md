# 🎬 Movie Recommender System

A full-stack machine learning-powered movie recommendation system that combines TF-IDF similarity matching with TMDB API integration to provide personalized movie suggestions.

---

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Features](#features)
- [Architecture](#architecture)
- [Technology Stack](#technology-stack)
- [Installation & Setup](#installation--setup)
- [How to Run](#how-to-run)
- [API Documentation](#api-documentation)
- [Frontend Guide](#frontend-guide)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
- [File Descriptions](#file-descriptions)

---

## 🎯 Project Overview

The Movie Recommender System is a web application that helps users discover movies based on:

1. **Keyword Search** - Search for movies and browse results
2. **TF-IDF Similarity** - Get recommendations similar to a selected movie using text-based machine learning
3. **Genre-Based Recommendations** - Discover movies in the same genre
4. **Trending/Popular Movies** - Browse trending and popular movies from TMDB

The system uses a FastAPI backend for machine learning and API management, paired with a Streamlit frontend for an interactive user interface.

---

## ✨ Features

### User Features

- 🔍 **Movie Search** - Search by title with autocomplete suggestions
- 💡 **Smart Recommendations** - TF-IDF + Genre-based movie suggestions
- 🎭 **Category Browsing** - Trending, Popular, Top-Rated, Upcoming, and Now Playing
- 📱 **Responsive Design** - Grid-based layout that adapts to screen size
- 🎬 **Movie Details** - Comprehensive movie information including posters, overviews, release dates, and genres

### Technical Features

- 🤖 **Machine Learning** - TF-IDF vectorization for content-based recommendations
- 🔌 **TMDB Integration** - Real-time movie metadata via TheMovieDB API
- ⚡ **Async Processing** - FastAPI async routes for optimal performance
- 🎨 **Modern UI** - Streamlit with custom CSS styling
- 📦 **Caching** - Request caching to reduce API calls

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Streamlit Frontend                   │
│              (app.py - User Interface)                  │
│  Search | Browse | Details | Recommendations           │
└────────────────────┬────────────────────────────────────┘
                     │ HTTP Requests
                     ▼
┌─────────────────────────────────────────────────────────┐
│                   FastAPI Backend                       │
│              (main.py - API Server)                     │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │ TMDB Routes │  │ TF-IDF Recs  │  │ Genre Recs   │   │
│  └─────────────┘  └──────────────┘  └──────────────┘   │
└────────────────────┬────────────────────────────────────┘
                     │
        ┌────────────┴────────────┐
        ▼                         ▼
    ┌─────────────┐         ┌──────────────┐
    │ Local Data  │         │ TMDB API     │
    │ (Pickles)   │         │ (API Key)    │
    │ - df.pkl    │         │              │
    │ - indices   │         │ themoviedb.  │
    │ - tfidf mat │         │ org          │
    │ - tfidf vec │         │              │
    └─────────────┘         └──────────────┘
```

---

## 🛠️ Technology Stack

| Component               | Technology    | Version |
| ----------------------- | ------------- | ------- |
| **Backend API**         | FastAPI       | 0.111.0 |
| **Server**              | Uvicorn       | 0.30.1  |
| **Frontend**            | Streamlit     | 1.36.0  |
| **ML Library**          | Scikit-learn  | 1.5.1   |
| **Data Processing**     | Pandas        | 2.2.2   |
| **Numerical Computing** | NumPy         | 2.0.1   |
| **HTTP Client**         | HTTPX         | 0.27.0  |
| **Environment**         | Python-dotenv | 1.0.1   |
| **Python Version**      | 3.11.9        |

---

## 📦 Installation & Setup

### Prerequisites

- Python 3.11.9
- pip (Python package manager)
- TMDB API Key (get it from [themoviedb.org](https://www.themoviedb.org/settings/api))

### Step 1: Clone/Navigate to Project

```bash
cd "d:\Brainware Univercity\Sem 6\ML LAB\PBL\movie-rec"
```

### Step 2: Create Virtual Environment

```bash
python -m venv venv
```

### Step 3: Activate Virtual Environment

**Windows:**

```bash
.\venv\Scripts\activate
```

**macOS/Linux:**

```bash
source venv/bin/activate
```

### Step 4: Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 5: Configure Environment Variables

Create a `.env` file in the project root:

```
TMDB_API_KEY=your_actual_api_key_here
```

Get your API key:

1. Go to https://www.themoviedb.org/settings/api
2. Sign up/Login
3. Create a new API key
4. Copy the key into `.env`

### Step 6: Ensure ML Data Files Exist

The following pickle files should be in the project root:

- `df.pkl` - Main movie dataframe
- `indices.pkl` - Title to index mapping
- `tfidf_matrix.pkl` - TF-IDF matrix (sparse)
- `tfidf.pkl` - TF-IDF vectorizer
- `movies_metadata.csv` - Raw movie data

---

## 🚀 How to Run

### Terminal 1: Start FastAPI Backend

```bash
.\venv\Scripts\activate
uvicorn main:app --reload --no-use-colors
```

The API will be available at: **http://127.0.0.1:8000**

**API Documentation:** http://127.0.0.1:8000/docs (Swagger UI)

### Terminal 2: Start Streamlit Frontend

```bash
.\venv\Scripts\activate
streamlit run app.py
```

The app will open in your browser at: **http://localhost:8501**

### Verify Both Running

- Backend: Terminal shows "Uvicorn running on http://127.0.0.1:8000"
- Frontend: Browser opens with Movie Recommender app

---

## 📚 API Documentation

### Base URL

```
http://127.0.0.1:8000
```

### Endpoints

#### 1. **Health Check**

```http
GET /health
```

**Response:**

```json
{ "status": "ok" }
```

---

#### 2. **Home Feed (Browse Categories)**

```http
GET /home?category=popular&limit=24
```

**Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `category` | string | popular | trending, popular, top_rated, upcoming, now_playing |
| `limit` | integer | 24 | Number of movies (1-50) |

**Response:**

```json
[
  {
    "tmdb_id": 550,
    "title": "Fight Club",
    "poster_url": "https://...",
    "release_date": "1999-10-15",
    "vote_average": 8.8
  }
]
```

---

#### 3. **TMDB Search (Multiple Results)**

```http
GET /tmdb/search?query=batman&page=1
```

**Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `query` | string | required | Movie title keyword |
| `page` | integer | 1 | Page number (1-10) |

**Response:**

```json
{
  "results": [
    {
      "id": 272,
      "title": "Batman Begins",
      "poster_path": "/...",
      "release_date": "2005-06-15"
    }
  ]
}
```

---

#### 4. **Movie Details (By TMDB ID)**

```http
GET /movie/id/550
```

**Response:**

```json
{
  "tmdb_id": 550,
  "title": "Fight Club",
  "overview": "An insomniac office worker...",
  "release_date": "1999-10-15",
  "poster_url": "https://...",
  "backdrop_url": "https://...",
  "genres": [
    { "id": 18, "name": "Drama" },
    { "id": 53, "name": "Thriller" }
  ]
}
```

---

#### 5. **TF-IDF Recommendations**

```http
GET /recommend/tfidf?title=Fight%20Club&top_n=10
```

**Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `title` | string | required | Movie title from local dataset |
| `top_n` | integer | 10 | Number of recommendations (1-50) |

**Response:**

```json
[
  { "title": "Trainspotting", "score": 0.87 },
  { "title": "Requiem for a Dream", "score": 0.81 }
]
```

---

#### 6. **Genre Recommendations**

```http
GET /recommend/genre?tmdb_id=550&limit=18
```

**Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `tmdb_id` | integer | required | TMDB Movie ID |
| `limit` | integer | 18 | Number of recommendations (1-50) |

**Response:**

```json
[
  {
    "tmdb_id": 272,
    "title": "Batman Begins",
    "poster_url": "https://...",
    "release_date": "2005-06-15",
    "vote_average": 8.3
  }
]
```

---

#### 7. **Bundle: Details + Recommendations**

```http
GET /movie/search?query=Fight%20Club&tfidf_top_n=12&genre_limit=12
```

**Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `query` | string | required | Movie title to search |
| `tfidf_top_n` | integer | 12 | TF-IDF recommendations (1-30) |
| `genre_limit` | integer | 12 | Genre recommendations (1-30) |

**Response:**

```json
{
  "query": "Fight Club",
  "movie_details": { ... },
  "tfidf_recommendations": [
    {
      "title": "Trainspotting",
      "score": 0.87,
      "tmdb": { "tmdb_id": 346, "title": "Trainspotting", ... }
    }
  ],
  "genre_recommendations": [ ... ]
}
```

---

## 🎨 Frontend Guide

### Home Page

1. **Search Box** - Type a movie title (min 2 characters)
2. **Autocomplete Dropdown** - Select from suggestions
3. **Grid Results** - See all matching movies
4. **Sidebar** - Browse by category and adjust grid size

### Details Page

1. **Movie Info** - Title, release date, genres, overview
2. **Posters** - Movie poster and backdrop image
3. **TF-IDF Recommendations** - Movies similar to this one (content-based)
4. **Genre Recommendations** - More movies in the same genre

### Navigation

- Click "Open" on any movie card to view details
- Click "← Back to Home" to return to home page
- Use sidebar to change categories or grid columns

---

## 📁 Project Structure

```
movie-rec/
├── app.py                      # Streamlit frontend (main user interface)
├── main.py                     # FastAPI backend (API server)
├── movies.ipynb               # Jupyter notebook (ML analysis/training)
├── movies_metadata.csv        # Raw movie dataset (from TMDB)
├── requirements.txt           # Python dependencies
├── runtime.txt                # Python version specification
├── .env                       # Environment variables (TMDB API key)
├── venv/                      # Virtual environment (auto-created)
│
├── df.pkl                     # Pickled DataFrame (movie data)
├── indices.pkl                # Pickled title-to-index mapping
├── tfidf_matrix.pkl          # Pickled TF-IDF matrix (sparse)
├── tfidf.pkl                 # Pickled TF-IDF vectorizer
│
└── README.md                  # Project documentation
```

---

## 🧠 How It Works

### 1. **Data Preparation**

- Raw movie data from `movies_metadata.csv` is loaded into a Pandas DataFrame
- Movie titles are vectorized using TF-IDF (Term Frequency-Inverse Document Frequency)
- Indices are created to map titles to vector positions
- All processed data is saved as pickle files for fast loading

### 2. **Search Flow**

```
User Types Query
      ↓
Streamlit sends request to /tmdb/search
      ↓
FastAPI queries TMDB API with keyword
      ↓
Results parsed into movie cards with posters
      ↓
Grid of matching movies displayed
```

### 3. **Recommendation Flow**

```
User Clicks on Movie
      ↓
Streamlit loads movie details via /movie/id/{tmdb_id}
      ↓
Two parallel recommendation requests:
  ├─ /recommend/tfidf → Local TF-IDF similarity
  └─ /recommend/genre → TMDB genre discovery
      ↓
Results combined and displayed as two grids
```

### 4. **TF-IDF Similarity Algorithm**

1. Movie title is normalized and vectorized using learned TF-IDF model
2. Cosine similarity computed against all other movies
3. Top N highest similarity scores returned
4. TMDB posters attached to each recommendation

### 5. **Genre Recommendations**

1. Get movie's primary genre from TMDB
2. Use TMDB's discover endpoint to find other popular movies in that genre
3. Filter out the original movie
4. Return top N results

---

## 📝 File Descriptions

### `app.py` - Streamlit Frontend

**Lines: 375 | Language: Python**

Main user interface application. Handles:

- Page routing (home view, details view)
- Movie search with autocomplete
- Grid-based display of movie posters
- Sidebar navigation and category selection
- Integration with FastAPI backend

**Key Functions:**

- `poster_grid()` - Display movies in grid layout
- `parse_tmdb_search_to_cards()` - Parse TMDB results
- `api_get_json()` - Cached API requests
- `goto_home()`, `goto_details()` - Route management

---

### `main.py` - FastAPI Backend

**Lines: 477 | Language: Python**

REST API server handling business logic. Manages:

- TMDB API integration
- TF-IDF recommendation engine
- Genre-based recommendations
- Movie metadata caching
- Pickle file loading at startup

**Key Functions:**

- `tmdb_get()` - Safe TMDB API requests
- `tfidf_recommend_titles()` - Local similarity recommendations
- `tmdb_movie_details()` - Fetch movie details
- `search_bundle()` - Combined recommendation response

**API Routes:**

- `/health` - Health check
- `/home` - Category browsing
- `/tmdb/search` - Keyword search
- `/movie/id/{tmdb_id}` - Movie details
- `/recommend/tfidf` - TF-IDF recommendations
- `/recommend/genre` - Genre recommendations
- `/movie/search` - Bundle (details + recs)

---

### `movies_metadata.csv`

Raw movie dataset extracted from TMDB. Contains:

- Movie titles
- Genres
- Release dates
- Overviews
- Other metadata

Used for building the TF-IDF model.

---

### `movies.ipynb` - Jupyter Notebook

**Language: Python | Format: Notebook**

ML training and analysis notebook. Includes:

- Data loading and preprocessing
- TF-IDF vectorization
- Model training
- Pickle file generation (df.pkl, tfidf_matrix.pkl, etc.)

**Usage:** For analyzing and retraining the recommendation model.

---

### `requirements.txt`

Python package dependencies:

```
fastapi==0.111.0           # Web framework
uvicorn==0.30.1            # ASGI server
streamlit==1.36.0          # Frontend
scikit-learn==1.5.1        # ML algorithms
pandas==2.2.2              # Data manipulation
numpy==2.0.1               # Numerical computing
httpx==0.27.0              # HTTP client
python-dotenv==1.0.1       # Environment config
scipy==1.13.1              # Scientific computing
```

---

### `runtime.txt`

Specifies Python version for deployment:

```
python-3.11.9
```

---

### `.env` (Create this file)

```
TMDB_API_KEY=your_api_key_here
```

Get from: https://www.themoviedb.org/settings/api

---

## 🔧 Troubleshooting

### Issue: "TMDB_API_KEY missing"

**Solution:** Create `.env` file with your TMDB API key

```
TMDB_API_KEY=your_actual_key
```

### Issue: "ModuleNotFoundError"

**Solution:** Ensure virtual environment is activated

```bash
.\venv\Scripts\activate
```

### Issue: "Port 8000 already in use"

**Solution:** Kill existing process or use different port

```bash
uvicorn main:app --reload --port 8001
```

### Issue: "Pickle files not found"

**Solution:** Run `movies.ipynb` to generate pickle files from CSV, or ensure they're in project root

### Issue: Slow recommendations

**Solution:** Wait for TMDB API responses (typically 1-3 seconds). API calls are cached to improve future requests.

---

## 📊 Performance Notes

- **Search:** ~1-2 seconds (TMDB API + parsing)
- **TF-IDF Recommendations:** <100ms (local computation)
- **Genre Recommendations:** ~1-2 seconds (TMDB API)
- **Home Feed:** ~1 second (TMDB API cached)

---

## 🔐 Security Considerations

- TMDB API key stored in `.env` (never commit to git)
- CORS enabled for localhost (can be restricted in production)
- All TMDB errors handled gracefully (502 errors instead of crashes)
- Input validation on all query parameters

---

## 🚀 Deployment

### Local Deployment (Current)

Already running on:

- Backend: http://127.0.0.1:8000
- Frontend: http://localhost:8501

### Cloud Deployment (Render/Heroku)

Example deployed at: https://movie-rec-466x.onrender.com

**Steps:**

1. Push to GitHub
2. Connect to Render/Heroku
3. Set environment variable: `TMDB_API_KEY`
4. Deploy both services

---

## 📝 License & Credits

**Dataset:** TheMovieDB (TMDB) API - https://www.themoviedb.org

**Frameworks:**

- FastAPI - https://fastapi.tiangolo.com/
- Streamlit - https://streamlit.io/
- Scikit-learn - https://scikit-learn.org/

---

## 👥 Author

**Brainware University** - Sem 6, ML Lab, PBL Project

---

## ❓ Questions / Support

For API issues, check: http://127.0.0.1:8000/docs

For UI issues, check browser console (F12)

---

**Last Updated:** March 8, 2026
