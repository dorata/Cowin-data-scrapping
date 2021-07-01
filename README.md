# Cowin-data-scrapping
Here, I have extracted data on vaccination center by type (govt/pvt/total) at India Level. I have further extracted data at district level as well.

import json
import requests
import os
import csv
import pandas as pd
import time

def write_in_file(writer, data):
    for d in data:
        writer.writerow(d)


os.chdir('C:/Users/babu/Desktop/web_scrap')

date_data = pd.read_csv('date.csv')
dist_data=pd.read_csv('cowin_state_dist_list2.csv')

file = open('cowin_dist_level73.csv', 'a', newline='')

# identifying header
header = ['state','district','date', 'total', 'govt', 'pvt']
writer = csv.DictWriter(file, fieldnames=header)
# writing data row-wise into the csv file
writer.writeheader()
data_dict =  dict(date_data)['date'][51:61]
state=dict(dist_data)['state'][244:755]
district=dict(dist_data)['district'][244:755]
data = []
counter = 0
for (k,v), (k2,v2) in zip(state.items(), district.items()):
    for each in data_dict:
            _data = each.split("-")
            _final_date = "{}-{}-{}".format(_data[2], _data[1], _data[0])
            url_nat = "https://api.cowin.gov.in/api/v1/reports/v2/getPublicReports?state_id={}&district_id={}&date={}".format(v,v2,_final_date)
            payload = {}
            response = requests.request("GET", url_nat, data=payload)
            data1 = json.loads(response.content)
            data1 = response.json()
            data2 = data1.get("topBlock")
            data3 = data2.get("sites")
            data.append({'state':v,
                         'district':v2,
                          'date': _final_date,
                         'total': data3['total'],
                         'govt': data3['govt'],
                          'pvt':data3['pvt']
                         })
            counter += 1
            if counter % 204 == 0:
                write_in_file(writer, data)
                data = []
            time.sleep(0.2)
write_in_file(writer, data)
file.close()

##Download national level data
import json
import requests
import os
import csv
import pandas as pd
os.chdir('C:/Users/babu/Desktop/web_scrap')

date_data = pd.read_csv('date.csv')
st_data=pd.read_csv('state_code.csv')

file = open('cowin_national_level4.csv', 'a', newline='')

# identifying header
header = ['date', 'total', 'govt', 'pvt']
writer = csv.DictWriter(file, fieldnames=header)
# writing data row-wise into the csv file
writer.writeheader()
data_dict =  dict(date_data)['date'][51:61]
for each in data_dict:
     _data = each.split("-")
     _final_date = "{}-{}-{}".format(_data[2], _data[1], _data[0])
     url_nat = "https://api.cowin.gov.in/api/v1/reports/v2/getPublicReports?state_id=&district_id=&date={}".format(_final_date)
     payload = {}
     response = requests.request("GET", url_nat, data=payload)
     data = json.loads(response.content)
     # print(response.json)
     data = response.json()
     data1 = data.get("topBlock")
     data2 = data1.get("sites")
     writer.writerow({'date': _final_date,
                     'total': data2['total'],
                     'govt': data2['govt'],
                      'pvt':data2['pvt']
                     })
