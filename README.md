import time
import urllib.request
import urllib.error


def check_website(url, timeout=1):
    request = urllib.request.Request(
        url,
        headers={"User-Agent": "WebsiteMonitor/1.0"},
    )

    start = time.perf_counter()

    try:
        with urllib.request.urlopen(request, timeout=timeout) as response:
            elapsed = time.perf_counter() - start

            return {
                "url": url,
                "status": response.status,
                "response_time_ms": round(elapsed * 1000, 2),
                "available": 200 <= response.status < 400,
            }

    except (urllib.error.URLError, TimeoutError, OSError) as error:
        elapsed = time.perf_counter() - start

        return {
            "url": url,
            "status": "ERROR",
            "response_time_ms": round(elapsed * 1000, 2),
            "available": False,
            "error": str(error),
        }


if __name__ == "__main__":
    url = input("Enter website URL (e.g. https://example.com): ").strip()

    if not url.startswith(("http://", "https://")):
        url = "https://" + url

    result = check_website(url)

    print("\nWebsite Status")
    print("-" * 30)

    for key, value in result.items():
        print(f"{key.replace('_', ' ').title()}: {value}")
