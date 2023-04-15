# Trace-back
Trace back phishing attack to Source machine


import requests
import json

def get_url():
    while True:
        try:
            url = input("Enter the phishing URL to trace back: ")
            response = requests.get(url)
            if response.status_code != 200:
                print("URL is invalid. Please try again.")
            else:
                return url
        except:
            print("Error occurred while retrieving URL. Please try again.")

def get_ip_address(url):
    response = requests.get(url)
    if response.status_code != 200:
        return None
    ip_address = response.json()['origin']
    return ip_address

def get_geolocation(ip_address):
    geo_response = requests.get("http://ip-api.com/json/" + ip_address)
    if geo_response.status_code != 200:
        return None
    geo_data = json.loads(geo_response.content)
    return geo_data['city'], geo_data['regionName'], geo_data['country']

def check_phishing_activity(ip_address):
    phish_response = requests.get("https://checkphish.ai/api/public/api/url?query=" + ip_address)
    if phish_response.status_code != 200:
        return None
    phish_data = json.loads(phish_response.content)
    if phish_data['status'] == 'success':
        return phish_data['result']['is_phishing']
    else:
        return None

def main():
    url = get_url()
    ip_address = get_ip_address(url)
    if ip_address is None:
        print("Error occurred while retrieving IP address. Aborting.")
        return
    print("IP Address: {}".format(ip_address))
    
    geolocation = get_geolocation(ip_address)
    if geolocation is None:
        print("Error occurred while retrieving geolocation. Aborting.")
        return
    print("Geolocation: {}, {}, {}".format(*geolocation))
    
    is_phishing = check_phishing_activity(ip_address)
    if is_phishing is None:
        print("Error occurred while checking phishing activity. Aborting.")
        return
    elif is_phishing:
        print("This URL is associated with phishing activity.")
    else:
        print("No phishing activity associated with this URL.")

if __name__ == '__main__':
    try:
        main()
    except Exception as e:
        with open('phishing_log.txt', 'a') as f:
            f.write(str(e) + '\n')
        print("An error occurred. Please check the log file for details.")
