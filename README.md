# Trace-back
Trace back phishing attack to Source machine

The main goal of this script is to trace the geolocation of a phishing URL by retrieving its IP address and querying its geolocation information. The script prompts the user to input a phishing URL, checks its validity, retrieves the IP address, and uses the ipaddress library to retrieve the geolocation information for that IP address. The script handles errors and exceptions that could occur during these steps and logs them into a log file named "phishing_log.txt".
