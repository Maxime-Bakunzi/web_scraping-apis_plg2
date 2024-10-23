
# Web Scraping and API Project

This project demonstrates the use of APIs and web scraping techniques in Python. It showcases how to fetch data from APIs, scrape websites, and manage the data through CSV files and images.

## Group Members
- [Maxime Guy Bakunzi](https://github.com/Maxime-Bakunzi/)
- [Davy Mbuto Nkurunziza](https://github.com/davyleroy)
- [Alhassan Alimamy Dumbuya](https://github.com/aadumbuya)
- [Prince Ndanyuzwe](https://github.com/NdanyuzweP)

## Features

1. **API Integration (SWAPI)**:
   - Retrieve a list of Star Wars starships that can hold a specified number of passengers using the [Star Wars API](https://swapi.dev/).
   - Handle API pagination to ensure all results are fetched.
   
2. **Web Scraping (Hockey Teams)**:
   - Scrape data from the [Scrape This Site](https://www.scrapethissite.com/pages/forms/) website to collect hockey team statistics and save them into a CSV file.

3. **Amazon Product Simulation**:
   - Simulate scraping of products from Amazon for different categories (e.g., automotive, baby, electronics).
   - Save product data (title, price, rating, reviews) and download example product images.

## Table of Contents
- [Web Scraping and API Project](#web-scraping-and-api-project)
  - [Group Members](#group-members)
  - [Features](#features)
  - [Table of Contents](#table-of-contents)
  - [Installation](#installation)
    - [Required Libraries](#required-libraries)
  - [Usage](#usage)
    - [1. SWAPI API Implementation](#1-swapi-api-implementation)
      - [Code Example:](#code-example)
      - [Example Usage:](#example-usage)
    - [2. Hockey Team Data Scraping](#2-hockey-team-data-scraping)
      - [Code Example:](#code-example-1)
      - [Example Usage:](#example-usage-1)
    - [3. Amazon Product Scraping](#3-amazon-product-scraping)
      - [Code Example:](#code-example-2)
      - [Example Usage:](#example-usage-2)

## Installation

To run this project, you need to install the required dependencies. You can do so by running the following command:

```bash
!pip install requests beautifulsoup4 pandas
```

### Required Libraries
- `requests`: For making API requests.
- `beautifulsoup4`: For parsing HTML data from websites.
- `pandas`: For working with structured data (DataFrames) and CSV file management.

## Usage

### 1. SWAPI API Implementation

This section uses the [Star Wars API (SWAPI)](https://swapi.dev/) to retrieve a list of starships that can hold a given number of passengers.

#### Code Example:

```python
def availableShips(passengerCount):
    """
    Returns a list of ships that can hold a given number of passengers.
    Args:
        passengerCount (int): The minimum number of passengers the ship should hold.
    Returns:
        list: Names of ships that can hold the specified number of passengers.
    """
    base_url = "https://swapi.dev/api/starships/"
    ships = []
    page = 1

    while True:
        response = requests.get(f"{base_url}?page={page}")
        if response.status_code != 200:
            break

        data = response.json()

        for ship in data['results']:
            try:
                ship_capacity = ship['passengers'].replace(',', '')
                if ship_capacity not in ['n/a', 'unknown'] and int(ship_capacity) >= passengerCount:
                    ships.append(ship['name'])
            except ValueError:
                continue

        if not data.get('next'):
            break

        page += 1

    return ships
```

#### Example Usage:

```python
test_passenger_count = 4
ships = availableShips(test_passenger_count)
print(f"Ships that can hold {test_passenger_count} passengers:")
for ship in ships:
    print(f"- {ship}")
```

### 2. Hockey Team Data Scraping

This component scrapes hockey team data from the [Scrape This Site](https://www.scrapethissite.com/pages/forms/) website and saves the data to a CSV file.

#### Code Example:

```python
def scrape_hockey_teams():
    base_url = "https://www.scrapethissite.com/pages/forms/"
    response = requests.get(base_url)
    soup = BeautifulSoup(response.text, 'html.parser')

    teams = soup.find_all('tr', class_='team')
    team_data = []

    for team in teams:
        team_info = {
            'Team Name': team.find('td', class_='name').text.strip(),
            'Year': team.find('td', class_='year').text.strip(),
            'Wins': team.find('td', class_='wins').text.strip(),
            'Losses': team.find('td', class_='losses').text.strip(),
            'Goals For': team.find('td', class_='gf').text.strip(),
            'Goals Against': team.find('td', class_='ga').text.strip()
        }
        team_data.append(team_info)

    df = pd.DataFrame(team_data)
    df.to_csv('hockey_teams.csv', index=False)
    return df
```

#### Example Usage:

```python
hockey_df = scrape_hockey_teams()
hockey_df.head()
```

### 3. Amazon Product Scraping

In this component, product details from different Amazon categories are simulated, and example images are saved.

#### Code Example:

```python
def scrape_amazon_products():
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36',
    }
    categories = {'automotive': '/s?k=automotive', 'baby': '/s?k=baby', 'electronics': '/s?k=electronics'}
    
    products = []
    for category, url in categories.items():
        response = requests.get(f"https://www.amazon.com{url}", headers=headers)
        soup = BeautifulSoup(response.text, 'html.parser')
        
        product = soup.find('div', class_='a-section a-spacing-base')
        if product:
            title = product.find('span', class_='a-size-base-plus a-color-base a-text-normal').text.strip()
            products.append({'category': category, 'title': title})
        
    df = pd.DataFrame(products)
    return df
```

#### Example Usage:

```python
products_df = scrape_amazon_products()
products_df.head()
```
