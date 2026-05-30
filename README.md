# getxapi-examples

> Official code samples for [GetXAPI](https://getxapi.com), the cheapest Twitter / X data API. Pay-per-call from $0.001. 45 endpoints. No subscription, no Twitter API key.

[![Docs](https://img.shields.io/badge/docs-getxapi.com-7B2DBF)](https://docs.getxapi.com)
[![OpenAPI 3.1](https://img.shields.io/badge/OpenAPI-3.1-7B2DBF)](https://docs.getxapi.com/openapi.json)
[![License: MIT](https://img.shields.io/badge/License-MIT-7B2DBF)](LICENSE)
[![Price](https://img.shields.io/badge/from-%240.001%2Fcall-7B2DBF)](https://getxapi.com/pricing)

Code samples for every supported language: curl, Python, Node.js, Go, Rust, PHP, Ruby, Java.

## Quick start

1. Sign up at [getxapi.com](https://getxapi.com) (gets $0.10 free credit, no card required).
2. Copy your API key from the dashboard.
3. Set as env var: `export GETXAPI_KEY=...`
4. Run any example below.

Base URL: `https://api.getxapi.com`
Authentication: `Authorization: Bearer ${GETXAPI_KEY}` header on every request.

## curl

```bash
# Fetch a single tweet by ID
curl -H "Authorization: Bearer $GETXAPI_KEY" \
  "https://api.getxapi.com/twitter/tweet/detail?id=1234567890123456789"

# Search for tweets matching a query
curl -H "Authorization: Bearer $GETXAPI_KEY" \
  "https://api.getxapi.com/twitter/tweet/advanced_search?query=from:elonmusk&count=20"

# Get user profile by username
curl -H "Authorization: Bearer $GETXAPI_KEY" \
  "https://api.getxapi.com/twitter/user/info?userName=elonmusk"

# Get user followers
curl -H "Authorization: Bearer $GETXAPI_KEY" \
  "https://api.getxapi.com/twitter/user/followers?userName=elonmusk&count=100"
```

## Python

Install: `pip install requests`

```python
import os
import requests

API_KEY = os.environ["GETXAPI_KEY"]
BASE = "https://api.getxapi.com"
HEADERS = {"Authorization": f"Bearer {API_KEY}"}


def get_tweet(tweet_id: str) -> dict:
    r = requests.get(f"{BASE}/twitter/tweet/detail", params={"id": tweet_id}, headers=HEADERS)
    r.raise_for_status()
    return r.json()


def search_tweets(query: str, count: int = 20) -> dict:
    r = requests.get(
        f"{BASE}/twitter/tweet/advanced_search",
        params={"query": query, "count": count},
        headers=HEADERS,
    )
    r.raise_for_status()
    return r.json()


def get_user(username: str) -> dict:
    r = requests.get(f"{BASE}/twitter/user/info", params={"userName": username}, headers=HEADERS)
    r.raise_for_status()
    return r.json()


if __name__ == "__main__":
    tweet = get_tweet("1234567890123456789")
    print(tweet["data"]["text"])
```

## Node.js

```javascript
const API_KEY = process.env.GETXAPI_KEY;
const BASE = "https://api.getxapi.com";

async function fetchGetXAPI(path, params = {}) {
  const url = new URL(BASE + path);
  Object.entries(params).forEach(([k, v]) => url.searchParams.set(k, v));
  const res = await fetch(url, {
    headers: { Authorization: `Bearer ${API_KEY}` },
  });
  if (!res.ok) throw new Error(`HTTP ${res.status}: ${await res.text()}`);
  return res.json();
}

async function main() {
  const tweet = await fetchGetXAPI("/twitter/tweet/detail", { id: "1234567890123456789" });
  console.log(tweet.data.text);

  const search = await fetchGetXAPI("/twitter/tweet/advanced_search", {
    query: "from:elonmusk",
    count: 20,
  });
  console.log(`Found ${search.data.tweets.length} tweets`);
}

main().catch(console.error);
```

## Go

```go
package main

import (
	"encoding/json"
	"fmt"
	"io"
	"net/http"
	"net/url"
	"os"
)

const base = "https://api.getxapi.com"

func fetch(path string, params map[string]string) (map[string]any, error) {
	u, _ := url.Parse(base + path)
	q := u.Query()
	for k, v := range params {
		q.Set(k, v)
	}
	u.RawQuery = q.Encode()

	req, _ := http.NewRequest("GET", u.String(), nil)
	req.Header.Set("Authorization", "Bearer "+os.Getenv("GETXAPI_KEY"))

	resp, err := http.DefaultClient.Do(req)
	if err != nil {
		return nil, err
	}
	defer resp.Body.Close()
	body, _ := io.ReadAll(resp.Body)

	var out map[string]any
	json.Unmarshal(body, &out)
	return out, nil
}

func main() {
	tweet, _ := fetch("/twitter/tweet/detail", map[string]string{"id": "1234567890123456789"})
	fmt.Println(tweet)
}
```

## Rust

`Cargo.toml`:
```toml
[dependencies]
reqwest = { version = "0.12", features = ["json"] }
tokio = { version = "1", features = ["full"] }
serde_json = "1"
```

```rust
use serde_json::Value;
use std::env;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let key = env::var("GETXAPI_KEY")?;
    let client = reqwest::Client::new();

    let res: Value = client
        .get("https://api.getxapi.com/twitter/tweet/detail")
        .query(&[("id", "1234567890123456789")])
        .bearer_auth(&key)
        .send()
        .await?
        .json()
        .await?;

    println!("{}", res);
    Ok(())
}
```

## PHP

```php
<?php
$apiKey = getenv("GETXAPI_KEY");
$url = "https://api.getxapi.com/twitter/tweet/detail?id=1234567890123456789";

$ch = curl_init($url);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_HTTPHEADER => ["Authorization: Bearer " . $apiKey],
]);
$res = curl_exec($ch);
curl_close($ch);

$data = json_decode($res, true);
echo $data["data"]["text"] . "\n";
```

## Endpoints reference (45 total)

| Category | Count | Examples |
|---|---|---|
| Tweet | 6 | advanced_search, detail, replies, create, favorite, retweet |
| Article | 7 | get, create, update, list, publish, unpublish, delete |
| User | 18 | search, info, followers, following, tweets, likes, home_timeline, bookmark_search |
| Authentication | 1 | user_login |
| List | 1 | members |
| Direct Messages | 2 | send, list |
| Account | 2 | me, payments |

Full spec: [docs.getxapi.com/openapi.json](https://docs.getxapi.com/openapi.json)

## Pricing

| Operation | Price |
|---|---|
| Standard read | $0.001 per call |
| Write operations | $0.002 per call |
| Send DM | $0.002 per call |
| Free signup credit | $0.10 (no card required) |
| Monthly subscription | $0 |

## Contributing

PRs welcome. Add a new language example by creating a top-level directory named after the language (`/ruby`, `/java`, etc.) and following the existing structure.

## Links

- Website: [getxapi.com](https://getxapi.com)
- Documentation: [docs.getxapi.com](https://docs.getxapi.com)
- OpenAPI 3.1 spec: [docs.getxapi.com/openapi.json](https://docs.getxapi.com/openapi.json)
- Crunchbase: [crunchbase.com/organization/getxapi](https://www.crunchbase.com/organization/getxapi)
- GitHub org: [github.com/getxapi](https://github.com/getxapi)
- Contact: [bozad@getxapi.com](mailto:bozad@getxapi.com)

## License

[MIT](LICENSE)
