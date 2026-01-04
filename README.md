# MoviesDatabase API Documentation

## API Overview

The MoviesDatabase API is a comprehensive movie and TV show information service that provides complete and updated data for over 9 million titles (including movies, series, and episodes) and 11 million actors, crew, and cast members. This powerful API offers rich information including YouTube trailer URLs, awards, full biographies, ratings, plot summaries, cast and crew details, images, and much more. Recent titles are updated weekly, while ratings and episode information are updated daily, ensuring you always have access to the most current data available.

## Version

**API Version:** v3 (accessed via RapidAPI platform)

## Available Endpoints

### Titles Endpoints

- **GET /titles** - Returns an array of titles according to filters and sorting query parameters
- **GET /x/titles-by-ids** - Returns an array of titles based on provided ID list
- **GET /titles/{id}** - Returns specific title details by IMDb ID
- **GET /titles/{id}/ratings** - Returns rating and vote count for a specific title
- **GET /titles/series/{id}** - Returns array of episodes with ID, episode number, and season number in ascending order
- **GET /titles/seasons/{id}** - Returns the number of seasons for a series
- **GET /titles/series/{id}/{season}** - Returns episodes for a specific season
- **GET /titles/episode/{id}** - Returns episode details by IMDb ID
- **GET /titles/x/upcoming** - Returns array of upcoming titles

### Search Endpoints

- **GET /titles/search/keyword/{keyword}** - Search titles by keyword
- **GET /titles/search/title/{title}** - Search titles by title or partial title
- **GET /titles/search/akas/{aka}** - Search titles by alternative titles (exact matches only)

### Actors Endpoints

- **GET /actors** - Returns array of actors based on filters
- **GET /actors/{id}** - Returns specific actor details by IMDb ID

### Utils Endpoints

- **GET /title/utils/titleType** - Returns array of available title types
- **GET /title/utils/genres** - Returns array of available genres
- **GET /title/utils/lists** - Returns array of predefined lists (for 'list' query parameter)

## Request and Response Format

### Request Structure

All requests are made via HTTPS GET requests to the RapidAPI endpoint:

```
https://moviesdatabase.p.rapidapi.com/{endpoint}
```

**Required Headers:**

```
X-RapidAPI-Key: YOUR_API_KEY
X-RapidAPI-Host: moviesdatabase.p.rapidapi.com
```

**Example Request:**

```bash
curl --request GET \
  --url 'https://moviesdatabase.p.rapidapi.com/titles/tt0111161?info=base_info' \
  --header 'X-RapidAPI-Key: YOUR_API_KEY' \
  --header 'X-RapidAPI-Host: moviesdatabase.p.rapidapi.com'
```

### Response Structure

All endpoints return an object with a `results` key. Paginated endpoints include additional keys: `page`, `next`, and `entries`.

**Example Response:**

```json
{
  "page": 1,
  "next": "/titles?page=2",
  "entries": 50,
  "results": [
    {
      "id": "tt0111161",
      "titleText": {
        "text": "The Shawshank Redemption"
      },
      "primaryImage": {
        "url": "https://...",
        "width": 1000,
        "height": 1500
      },
      "titleType": {
        "text": "Movie"
      },
      "releaseDate": {
        "year": 1994
      },
      "genres": ["Drama"],
      "ratingsSummary": {
        "averageRating": 9.3,
        "voteCount": 2500000
      }
    }
  ]
}
```

### Query Parameters (Optional)

- **info** - Specifies which information to retrieve (default: `mini_info`)
  - Predefined options: `base_info`, `mini_info`, `image`, `creators_directors_writers`, `revenue_budget`, `extendedCast`, `rating`, `awards`
  - Can also use any key from the title object
- **limit** - Number of results per page (max 50, default: 10)
- **page** - Page number (default: 1)
- **titleType** - Filter by title type (options from `/title/utils/titleTypes`)
- **startYear/endYear** - Filter by year range
- **year** - Filter by specific year (cannot be used with startYear/endYear)
- **genre** - Filter by genre (case-sensitive, capitalized)
- **sort** - Sort results (`year.incr` or `year.decr`)
- **exact** - Match exact text (for title search, default: false)
- **list** - Predefined lists: `most_pop_movies`, `most_pop_series`, `top_boxoffice_200`, `top_rated_250`, `top_rated_series_250`, etc.

## Authentication

The MoviesDatabase API uses RapidAPI's authentication system. To authenticate your requests:

1. **Sign up for RapidAPI:** Create an account at [rapidapi.com](https://rapidapi.com)
2. **Subscribe to the API:** Visit the MoviesDatabase API page and subscribe to a plan
3. **Get Your API Key:** Your unique API key will be provided in your RapidAPI dashboard

### Required Headers

Every request must include these two headers:

```typescript
{
  'X-RapidAPI-Key': 'your_api_key_here',
  'X-RapidAPI-Host': 'moviesdatabase.p.rapidapi.com'
}
```

### TypeScript Example

```typescript
interface RequestHeaders {
  "X-RapidAPI-Key": string;
  "X-RapidAPI-Host": string;
}

const headers: RequestHeaders = {
  "X-RapidAPI-Key": process.env.RAPIDAPI_KEY!,
  "X-RapidAPI-Host": "moviesdatabase.p.rapidapi.com",
};

const response = await fetch(
  "https://moviesdatabase.p.rapidapi.com/titles/tt0111161",
  { headers }
);
```

## Error Handling

### Common HTTP Status Codes

- **200 OK** - Request succeeded
- **400 Bad Request** - Invalid parameters or malformed request
- **401 Unauthorized** - Invalid or missing API key
- **403 Forbidden** - API key lacks necessary permissions
- **404 Not Found** - Requested resource doesn't exist
- **429 Too Many Requests** - Rate limit exceeded
- **500 Internal Server Error** - Server-side error
- **503 Service Unavailable** - API temporarily offline

### Error Response Format

```json
{
  "message": "You are not subscribed to this API.",
  "info": "https://rapidapi.com/SAdrian/api/moviesdatabase"
}
```

### Handling Errors in TypeScript

```typescript
interface ApiError {
  message: string;
  info?: string;
}

async function fetchMovieData(titleId: string) {
  try {
    const response = await fetch(
      `https://moviesdatabase.p.rapidapi.com/titles/${titleId}`,
      { headers }
    );

    if (!response.ok) {
      const error: ApiError = await response.json();

      switch (response.status) {
        case 401:
          throw new Error("Authentication failed. Check your API key.");
        case 404:
          throw new Error(`Title ${titleId} not found.`);
        case 429:
          throw new Error("Rate limit exceeded. Please wait before retrying.");
        default:
          throw new Error(error.message || "An error occurred");
      }
    }

    return await response.json();
  } catch (error) {
    console.error("API Error:", error);
    throw error;
  }
}
```

## Usage Limits and Best Practices

### Rate Limits

**Basic Plan (Free):**

- 500,000 requests per month
- 1,000 requests per hour
- Hard limit enforced
- 10,240MB bandwidth per month

**Pro Plan (Pay-per-use):**

- $0.01 per request
- 5 requests per second
- 10,240MB bandwidth per month
- Additional bandwidth: $0.001 per MB

### Best Practices

1. **Implement Rate Limiting:**

   ```typescript
   // Simple rate limiter example
   class RateLimiter {
     private requestCount = 0;
     private resetTime = Date.now() + 3600000; // 1 hour

     async checkLimit(): Promise<boolean> {
       if (Date.now() > this.resetTime) {
         this.requestCount = 0;
         this.resetTime = Date.now() + 3600000;
       }
       return this.requestCount < 1000;
     }

     incrementCount() {
       this.requestCount++;
     }
   }
   ```

2. **Cache Responses:** Store frequently accessed data to minimize API calls

   ```typescript
   const cache = new Map<string, { data: any; timestamp: number }>();
   const CACHE_DURATION = 3600000; // 1 hour

   async function getCachedData(key: string, fetchFn: () => Promise<any>) {
     const cached = cache.get(key);
     if (cached && Date.now() - cached.timestamp < CACHE_DURATION) {
       return cached.data;
     }

     const data = await fetchFn();
     cache.set(key, { data, timestamp: Date.now() });
     return data;
   }
   ```

3. **Use Appropriate Info Parameters:** Request only the data you need to reduce bandwidth

   ```typescript
   // Instead of requesting all data:
   // ?info=base_info

   // Request specific fields:
   // ?info=id,titleText,primaryImage,ratingsSummary
   ```

4. **Handle 429 Errors with Exponential Backoff:**

   ```typescript
   async function fetchWithRetry(url: string, maxRetries = 3) {
     for (let i = 0; i < maxRetries; i++) {
       try {
         const response = await fetch(url, { headers });
         if (response.status === 429) {
           const delay = Math.pow(2, i) * 1000;
           await new Promise((resolve) => setTimeout(resolve, delay));
           continue;
         }
         return response;
       } catch (error) {
         if (i === maxRetries - 1) throw error;
       }
     }
   }
   ```

5. **Use Pagination Efficiently:** Process results in batches instead of requesting all data at once

6. **Store API Key Securely:** Use environment variables, never commit keys to version control

   ```typescript
   // .env.local
   RAPIDAPI_KEY = your_api_key_here;

   // In your code
   const apiKey = process.env.RAPIDAPI_KEY;
   ```

7. **Monitor Your Usage:** Regularly check your RapidAPI dashboard to track request counts and avoid unexpected charges
