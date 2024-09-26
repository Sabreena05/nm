import requests
import pandas as pd
from bs4 import BeautifulSoup

class TicketDataExtractor:
    def __init__(self, url):
        self.url = url
        self.data = []

    def fetch_ticket_data(self):
        response = requests.get(self.url)
        if response.status_code == 200:
            self.parse_html(response.content)
        else:
            print("Failed to retrieve data")

    def parse_html(self, html_content):
        soup = BeautifulSoup(html_content, 'html.parser')
        tickets = soup.find_all(class_='ticket-class') # Update with the correct class name

        for ticket in tickets:
            ticket_info = {
                'title': ticket.find(class_='title-class').text.strip(), # Update with the correct class name
                'price': ticket.find(class_='price-class').text.strip(), # Update with the correct class name
                'date': ticket.find(class_='date-class').text.strip(), # Update with the correct class name
                'location': ticket.find(class_='location-class').text.strip() # Update with the correct class name
            }
            self.data.append(ticket_info)

    def save_to_csv(self, filename):
        df = pd.DataFrame(self.data)
        df.to_csv(filename, index=False)

if __name__ == '__main__':
    url = 'https://example.com/tickets' # Replace with the actual URL
    extractor = TicketDataExtractor(url)
    extractor.fetch_ticket_data()
    extractor.save_to_csv('ticket_data.csv')
