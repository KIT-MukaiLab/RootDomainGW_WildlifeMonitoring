import paramiko, datetime, random, json, requests, time, collections as cl


#delete the previous entities
response = requests.delete('http://202.13.160.82:1026/v2/entities/urn:ngsi-ld:HakusanAnimalDetector1:001')

client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.WarningPolicy())
client.connect('10.130.1.1', username='root', password='dragino')

i = 0

buffer = list()

stdin, stdout, stderr = client.exec_command('telnet localhost 6571')
for line in stdout:

    if i == 0:
        message = '\nCreating entity...'
        print(message)
        
        #get the current timedate
        timedata = cl.OrderedDict()
        time = datetime.datetime.now()
        #"2020-10-29 16:05:52.242916"
        timestr = str(time)
        timebuf = timestr.split("-")
        timebuf2 = timebuf[2].split()
        timebuf3 = timebuf2[1].split(":")

        year = timebuf[0]
        month = timebuf[1]
        date = timebuf2[0]
        hour = timebuf3[0]
        minute = timebuf3[1]
        sec =  timebuf3[2]
        
        yearDict = cl.OrderedDict()
        monthDict = cl.OrderedDict()
        dateDict = cl.OrderedDict()
        hourDict = cl.OrderedDict()
        minuteDict = cl.OrderedDict()
        secDict = cl.OrderedDict()

        yearDict["type"] = "Year"
        yearDict["value"] = year
        monthDict["type"] = "Month"
        monthDict["value"] = month
        dateDict["type"] = "Date"
        dateDict["value"] = date
        hourDict["type"] = "Hour"
        hourDict["value"] = hour
        minuteDict["type"] = "Minute"
        minuteDict["value"] = minute
        secDict["type"] = "Sec"
        secDict["value"] = sec
        
        timeval = cl.OrderedDict()
        timeval["year"] = yearDict
        timeval["month"] = monthDict
        timeval["date"] = dateDict
        timeval["hour"] = hourDict
        timeval["minute"] = minuteDict
        timeval["sec"] = secDict
        
        timeDate = cl.OrderedDict()
        timeDate['type']='TimeDate'
        timeDate['value']=timeval
        
        
        locationval = cl.OrderedDict()
        locationdata = cl.OrderedDict()
        #get geo data
        geo_request_url = 'https://get.geojs.io/v1/ip/geo.json'
        geo_data = requests.get(geo_request_url).json()
        latitude = float(geo_data['latitude'])
        longitude = float(geo_data['longitude'])
        #
        locationval['type'] = 'Point'
        locationval['coordinates'] = longitude,latitude
        locationdata['type'] = 'geo:json'
        locationdata['value'] = locationval
        
        
        animaltype = cl.OrderedDict()
        cagedata = cl.OrderedDict()
        cagestat = False
        cagedata['type'] = 'boolean'
        cagedata['value'] = cagestat
        animaltype['type'] = 'Text'
        animaltype['value'] = 'empty'
        
        videoURL = cl.OrderedDict()
        videoURL['type'] = 'Text'
        videoURL['value'] = 'https://www.google.com/'
        
        
        animalDetector = cl.OrderedDict()
        animalDetector['id'] = 'urn:ngsi-ld:HakusanAnimalDetector1:001'
        animalDetector['type'] = 'AnimalDetector1'
        animalDetector['dateissued']=timeDate
        animalDetector['location'] = locationdata
        animalDetector['animalPresent'] = cagedata
        animalDetector['animalType'] = animaltype
        animalDetector['videoURL'] = videoURL
        
        json_ad = json.dumps(animalDetector)
        print json_ad
        
        headers={
                 'content-type':'application/json',
                }
        data = json_ad
        response = requests.post('http://202.13.160.82:1026/v2/entities', headers=headers, data=data)
        print response
        message = 'The AnimalDetector data has been posted.\n\n'
        print (message)
        i = i + 1
        
    elif i == 1:
        buffer = line.split(':')
        if buffer[1] == '101':
            message = 'AnimalDetector for Pattern 3 data received'
            print(message)
            
            
            
            #get the current timedate
            timedata = cl.OrderedDict()
            time = datetime.datetime.now()
            #"2020-10-29 16:05:52.242916"
            timestr = str(time)
            timebuf = timestr.split("-")
            #print timebuf
            timebuf2 = timebuf[2].split()
            #print timebuf2
            timebuf3 = timebuf2[1].split(":")
            #print timebuf3

            year = timebuf[0]
            month = timebuf[1]
            date = timebuf2[0]
            hour = timebuf3[0]
            minute = timebuf3[1]
            sec =  timebuf3[2]
        
            yearDict = cl.OrderedDict()
            monthDict = cl.OrderedDict()
            dateDict = cl.OrderedDict()
            hourDict = cl.OrderedDict()
            minuteDict = cl.OrderedDict()
            secDict = cl.OrderedDict()

            yearDict["type"] = "Year"
            yearDict["value"] = year
            monthDict["type"] = "Month"
            monthDict["value"] = month
            dateDict["type"] = "Date"
            dateDict["value"] = date
            hourDict["type"] = "Hour"
            hourDict["value"] = hour
            minuteDict["type"] = "Minute"
            minuteDict["value"] = minute
            secDict["type"] = "Sec"
            secDict["value"] = sec
        
            timeval["year"] = yearDict
            timeval["month"] = monthDict
            timeval["date"] = dateDict
            timeval["hour"] = hourDict
            timeval["minute"] = minuteDict
            timeval["sec"] = secDict
        
            timeDate = cl.OrderedDict()
            timeDate['type']='TimeDate'
            timeDate['value']=timeval
            
            
            locationval = cl.OrderedDict()
            locationdata = cl.OrderedDict()
            #get geo data
            geo_request_url = 'https://get.geojs.io/v1/ip/geo.json'
            geo_data = requests.get(geo_request_url).json()
            latitude = float(geo_data['latitude'])
            longitude = float(geo_data['longitude'])
            #
            locationval['type'] = 'Point'
            locationval['coordinates']= longitude, latitude
            locationdata['type'] = 'geo:json'
            locationdata['value'] = locationval
            
            
            
            animaltype = cl.OrderedDict()
            cagedata = cl.OrderedDict()
            cage=buffer[3]
            
            if cage == '1':
                cagestat = True
            else:
                cagestat = False
                ##cagestat = True
            
            cagedata['type'] = 'boolean'
            cagedata['value'] = cagestat
            ##animaltype['type'] = 'Text'
            ##animaltype['value'] = 'empty'
            
            animalDetector = cl.OrderedDict()
            ##animalDetector['id'] = 'urn:ngsi-ld:HakusanAnimalDetector3:001'
            ##animalDetector['type'] = 'AnimalDetector3'
            animalDetector['dateissued']=timeDate
            animalDetector['location'] = locationdata
            animalDetector['animalPresent'] = cagedata
            ##animalDetector['animalType'] = animaltype
            
            json_ad = json.dumps(animalDetector)
            print json_ad
        
            headers={
                 'content-type':'application/json',
                    }
            data = json_ad
            response = requests.patch('http://202.13.160.82:1026/v2/entities/urn:ngsi-ld:HakusanAnimalDetector1:001/attrs', headers=headers, data=data)
            print response
            message = 'The AnimalDetector for Pattern 3 data has been updated.\n\n'
            print (message)
            
    else:
        message = 'Erorr'
        print message

client.close()
    