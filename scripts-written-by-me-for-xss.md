# Scripts written by me for XSS

## XSS Combiner

```python
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

```python
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

## Written in Go

```go
package main

import (
	"bufio"
	"fmt"
	"io/ioutil"
	"net/http"
	"os"
	"strings"
	"sync"
)

type Result struct {
	URL     string
	HasXSS  bool
}

func checkXSSPayload(url string, outputFileName string, verbose bool, resultChan chan<- Result, wg *sync.WaitGroup) {
	defer wg.Done()

	resp, err := http.Get(url)
	if err != nil {
		resultChan <- Result{URL: url, HasXSS: false}
		return
	}
	defer resp.Body.Close()

	if resp.StatusCode == http.StatusOK {
		html, err := ioutil.ReadAll(resp.Body)
		if err != nil {
			resultChan <- Result{URL: url, HasXSS: false}
			return
		}

		if strings.Contains(string(html), "xalgord") {
			if verbose {
				fmt.Printf("\033[1;33mXSS payload found in:\033[0m \033[1;32m%s\033[0m\n", url)
			}

			if outputFileName != "" {
				f, err := os.OpenFile(outputFileName, os.O_APPEND|os.O_WRONLY|os.O_CREATE, 0644)
				if err != nil {
					resultChan <- Result{URL: url, HasXSS: false}
					return
				}
				defer f.Close()

				if _, err := f.WriteString(url + "\n"); err != nil {
					resultChan <- Result{URL: url, HasXSS: false}
					return
				}
			}

			resultChan <- Result{URL: url, HasXSS: true}
			return
		}
	}

	resultChan <- Result{URL: url, HasXSS: false}
}

func processURLs(urlsFile string, numThreads int, outputFileName string, verbose bool) {
	file, err := os.Open(urlsFile)
	if err != nil {
		fmt.Printf("\033[1;31mError: Failed to open file '%s'.\033[0m\n", urlsFile)
		return
	}
	defer file.Close()

	scanner := bufio.NewScanner(file)
	urls := make([]string, 0)
	for scanner.Scan() {
		urls = append(urls, strings.TrimSpace(scanner.Text()))
	}

	totalURLs := len(urls)
	resultChan := make(chan Result, totalURLs)

	var wg sync.WaitGroup
	wg.Add(totalURLs)

	fmt.Println("Checking URLs...")
	for _, url := range urls {
		go checkXSSPayload(url, outputFileName, verbose, resultChan, &wg)
	}

	wg.Wait()
	close(resultChan)

	xssPayloadsFound := make([]string, 0)
	for result := range resultChan {
		if result.HasXSS {
			xssPayloadsFound = append(xssPayloadsFound, result.URL)
		}
	}

	fmt.Println()
	fmt.Printf("\033[1;33m-------- Summary --------\033[0m\n")
	fmt.Printf("Total URLs: %d\n", totalURLs)
	fmt.Printf("XSS Payloads Found: %d\n", len(xssPayloadsFound))
	fmt.Printf("URLs with XSS Payloads: %d/%d\n", len(xssPayloadsFound), totalURLs)

	if verbose && len(xssPayloadsFound) > 0 {
		fmt.Printf("\033[1;33m-------- URLs with XSS Payloads --------\033[0m\n")
		for _, url := range xssPayloadsFound {
			fmt.Println(url)
		}
	}
}

func main() {
	if len(os.Args) < 2 {
		fmt.Println("Usage: reflectioner urls_file [num_threads] [output_file] [verbose]")
		return
	}

	urlsFile := os.Args[1]
	numThreads := 1
	outputFile := ""
	verbose := false

	if len(os.Args) > 2 {
		numThreads = parseThreads(os.Args[2])
	}

	if len(os.Args) > 3 {
		outputFile = os.Args[3]
	}

	if len(os.Args) > 4 && os.Args[4] == "true" {
		verbose = true
	}

	processURLs(urlsFile, numThreads, outputFile, verbose)
}

func parseThreads(input string) int {
	threads := 1
	_, err := fmt.Sscanf(input, "%d", &threads)
	if err != nil {
		threads = 1
	}
	return threads
}
```
