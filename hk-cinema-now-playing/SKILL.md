---
name: hk-cinema-now-playing
description: Query TMDB API for movies currently playing in Hong Kong cinemas. Returns original titles by default, with additional details (title, overview, poster, genre) available on demand. Handles cleanup of expired movies. Use when user asks what's playing in Hong Kong cinemas, Hong Kong movie times, or similar queries about movies in HK.
---

# HK Cinema Now Playing

## API Call
```
GET https://api.themoviedb.org/3/movie/now_playing?region=HK&language=zh-HK
```

Required headers:
- `Authorization: Bearer ${TMDB_API_KEY}`
- `accept: application/json`

## Display Rules

### Initial Response (Default)
When user asks "what's playing in Hong Kong" or similar, show ONLY:
- Original title of each movie

Format:
```
Now Playing in Hong Kong Cinemas:

1. [Original Title 1]
2. [Original Title 2]
3. [Original Title 3]
...
```

### On-Demand Details
When user wants more info about a specific movie (e.g., "tell me more about movie 1" or "what's the first movie about"), fetch full details and display:

| Field | Source |
|-------|--------|
| Original Title | `original_title` |
| Title | `title` |
| Overview | `overview` |
| Poster | `https://image.tmdb.org/t/p/w500{poster_path}` |
| Genres | Call `/movie/{id}` for genre names |

### Stale Data Cleanup
- Store fetched movie IDs with timestamp
- Before displaying, filter out movies where `release_date` is more than 30 days ago OR movie is no longer in HK "now playing"
- Clear poster images from cache for removed movies

## Response Fields
From `/movie/now_playing`:
- `results[]`: Array of now playing movies
  - `id`: TMDB movie ID
  - `original_title`: Original language title
  - `title`: Localized title
  - `overview`: Synopsis
  - `poster_path`: Poster image path
  - `genre_ids`: Genre category IDs

From `/movie/{id}`:
- `genres[]`: Full genre names

## Error Handling
- If API fails, return friendly error message
- If no movies found in HK region, inform user
