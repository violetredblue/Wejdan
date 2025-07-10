# Wejdan
Simple Python tool to detect vulnerable links
import requests
from bs4 import BeautifulSoup
from urllib.parse import urljoin

def get_links(url):
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')
    links = set()
    for a_tag in soup.find_all('a', href=True):
        full_url = urljoin(url, a_tag['href'])
        links.add(full_url)
    return links

def is_vulnerable(link):
    test_url = link + "'"
    try:
        res = requests.get(test_url)
        if "sql" in res.text.lower() or "syntax" in res.text.lower():
            return True
    except:
        pass
    return False

def main():
    url = input("Enter URL to scan: ")
    links = get_links(url)
    print(f"[+] Found {len(links)} links.")
    for link in links:
        if is_vulnerable(link):
            print(f"[!] Vulnerable: {link}")
        else:
            print(f"[-] Safe: {link}")

if __name__ == "__main__":
    main()
