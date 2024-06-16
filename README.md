# Web-Scraping
import requests
from bs4 import BeautifulSoup
import pandas as pd

def scrape_eventbrite_events(url):
    response = requests.get(url)
    if response.status_code == 200:
        soup = BeautifulSoup(response.content, 'html.parser')
        events = []

        # Find all event cards
        event_cards = soup.find_all('div', class_='eds-media-card-content__content-container')

        for card in event_cards:
            # Initialize variables to store extracted data
            event_name = ''
            event_date = ''
            event_location = ''
            event_description = ''

            # Extract event name if available
            event_name_tag = card.find('div', class_='eds-is-hidden-accessible')
            if event_name_tag:
                event_name = event_name_tag.text.strip()

            # Extract event date if available
            event_date_tag = card.find('div', class_='eds-text-bs--fixed eds-text-color--grey-600 eds-l-mar-top-1')
            if event_date_tag:
                event_date = event_date_tag.text.strip()

            # Extract event location if available
            event_location_tag = card.find('div', class_='card-text--truncated__one')
            if event_location_tag:
                event_location = event_location_tag.text.strip()

            # Extract event description if available
            event_description_tag = card.find('div', class_='eds-text-bs--fixed eds-l-mar-top-1 eds-text-color--grey-600')
            if event_description_tag:
                event_description = event_description_tag.text.strip()

            # Append event details to list
            events.append({
                'Event Name': event_name,
                'Event Date': event_date,
                'Event Location': event_location,
                'Event Description': event_description,
            })

        return events
    else:
        print(f"Failed to retrieve {url}")
        return None

# URL of Eventbrite page
url = 'https://www.eventbrite.com/d/online/all-events/'

# Scrape event data
events_data = scrape_eventbrite_events(url)

if events_data:
    # Convert to DataFrame
    events_df = pd.DataFrame(events_data)

    # Save to CSV
    events_df.to_csv('eventbrite_events.csv', index=False)

    print("Scraping complete. Data saved to eventbrite_events.csv")
