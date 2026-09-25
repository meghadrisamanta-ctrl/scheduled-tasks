import smtplib
import random
import datetime as dt
import os
import pandas as pd

# sending greeting
def greeting(receiver, message):
    my_email = os.environ.get("MY_EMAIL")
    my_email_password = os.environ.get("MY_PASSWORD")
    
    with smtplib.SMTP('smtp.gmail.com', 587) as connection:
        connection.starttls()
        connection.login(my_email, my_email_password)
        connection.sendmail(
            from_addr=my_email, 
            to_addrs=receiver,
            msg=f'Subject:Happy Birthday!\n\n{message}'
        )

# get letters into use
letters = []
for num in range(1, 4):
    with open(f'letter_templates/letter_{num}.txt', 'r') as f:
        letters.append(f.read())

# read data
data = pd.read_csv('birthdays.csv')
operating_data = data.to_dict(orient='records')

# get current date
current_date = dt.datetime.now()
month = current_date.month
day = current_date.day

for birthday in operating_data:
    if birthday['month'] == month and birthday['day'] == day:
        random_letter = random.choice(letters)
        email = birthday['email']
        message = random_letter.replace('[NAME]', birthday['name'])
        greeting(email, message)
        print(f"✅ Successfully sent birthday wish to {birthday['name']} ({email})")
