# Scripts written by me for XSS

## XSS Combiner

```
import sys

def combine_urls_with_payload(xss_payload, urls):
    combined_urls = []
    for url in urls:
        if "FUZZ" in url:
            combined_url = url.replace("FUZZ", "") + xss_payload
        else:
            if "?=" in url:
                combined_url = url.replace("?=", "?" + xss_payload + "&")
            else:
                combined_url = url + "?" + xss_payload
        combined_urls.append(combined_url)
    return combined_urls

def main():
    # Check if the XSS payload is provided as a command line argument
    if len(sys.argv) < 2:
        print("Error: XSS payload is missing.", file=sys.stderr)
        print("Usage: python script.py <xss_payload>", file=sys.stderr)
        sys.exit(1)

    # Read the XSS payload from the command line argument
    xss_payload = sys.argv[1]

    # Read URLs from stdin (piped input)
    urls = [url.strip() for url in sys.stdin]

    # Combine each URL with the XSS payload, removing the "FUZZ" keyword if present
    combined_urls = combine_urls_with_payload(xss_payload, urls)

    # Print the combined URLs
    for combined_url in combined_urls:
        print(combined_url)


if __name__ == "__main__":
    main()
```

## XSS Checker

```
import sys
import os
import asyncio
import aiohttp
from bs4 import BeautifulSoup
import argparse
from colorama import Fore, Style
from alive_progress import alive_bar

async def check_xss_payload(url, output_file, verbose):
    try:
        async with aiohttp.ClientSession() as session:
            async with session.get(url, timeout=10) as response:
                if response.status == 200:
                    html = await response.text()
                    soup = BeautifulSoup(html, 'html.parser')
                    if soup.find('img', {'src': 'x', 'onerror': 'alert(\'XSS\');'}):
                        if verbose:
                            print(f"{Fore.GREEN}XSS payload found in: {url}{Style.RESET_ALL}")
                        return url
    except (aiohttp.ClientError, asyncio.TimeoutError):
        pass

    return None

async def process_url(url, output_file, verbose, counter):
    result = await check_xss_payload(url, output_file, verbose)
    if result:
        if not verbose:
            print(result, flush=True)
    counter += 1  # Increment the counter
    return result

async def process_urls(urls, num_threads, output_file, verbose):
    xss_payloads_found = []
    counter = 0  # Counter for tracking progress

    with alive_bar(len(urls), bar='blocks', spinner='dots_waves2', enrich_print=False) as progress_bar:
        semaphore = asyncio.Semaphore(num_threads)
        async with semaphore:
            tasks = []
            for url in urls:
                url = url.strip()
                tasks.append(process_url(url, output_file, verbose, counter))  # Pass the counter

            for task in asyncio.as_completed(tasks):
                result = await task
                if result:
                    xss_payloads_found.append(result)
                progress_bar()
    return xss_payloads_found

def check_xss_payloads_parallel(urls_file, num_threads, output_file=None, verbose=False):
    if not os.path.isfile(urls_file):
        print(f"{Fore.RED}Error: File '{urls_file}' does not exist.{Style.RESET_ALL}")
        return

    with open(urls_file, 'r') as file:
        urls = file.readlines()

    loop = asyncio.get_event_loop()
    xss_payloads = loop.run_until_complete(process_urls(urls, num_threads, output_file, verbose))

    print(f"\n{Fore.CYAN}-------- Summary --------{Style.RESET_ALL}")
    print(f"{Fore.YELLOW}Total URLs: {len(urls)}")
    print(f"XSS Payloads Found: {len(xss_payloads)}{Style.RESET_ALL}")

    if verbose:
        if len(xss_payloads) > 0:
            print(f"\n{Fore.CYAN}URLs with XSS Payloads:{Style.RESET_ALL}")
            for url in xss_payloads:
                print(url)

def main():
    parser = argparse.ArgumentParser(description='XSS Payload Checker')
    parser.add_argument('urls_file', metavar='urls_file', type=str, help='Path to the file containing URLs')
    parser.add_argument('--threads', type=int, default=10, help='Number of threads for parallel processing')
    parser.add_argument('--output', type=str, help='Path to the output file to store URLs with XSS payloads')
    parser.add_argument('--verbose', action='store_true', help='Enable verbose output')

    args = parser.parse_args()
    check_xss_payloads_parallel(args.urls_file, args.threads, args.output, args.verbose)

if __name__ == '__main__':
    main()
```
