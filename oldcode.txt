#!/usr/bin/env python

"""
records library for class assignment
"""

import requests
import pandas as pd

class Records:
    def __init__(self, q=None, interval=None):
        
        self.q = q
        self.interval = interval
        self.input_interval = ','.join(str(i) for i in self.interval)
        self.params = {
        "q": self.q, 
        "year": self.input_interval, 
        "basisOfRecord": "PRESERVED_SPECIMEN",
        "hasCoordinate": "true",
        "hasGeospatialIssue": "false",
        "country": "US",
        "offset": "0",
        "limit": "300"
        }
        self.df = self._get_all_records()

        
        
    def _get_all_records(self):
        "iterate until end of records"
        start = 0
        data = []
       
        baseurl = "http://api.gbif.org/v1/occurrence/search?"
        
        while 1:
            # make request and store results
            res = requests.get(
                url=baseurl, 
                #url="http://api.gbif.org/v1/occurrence/search?",
                params=self.params,
            )
            # increment counter
            self.params["offset"] = str(int(self.params["offset"]) + 300)
        
            # concatenate data 
            idata = res.json()
            data += idata["results"]
        
            # stop when end of record is reached
            if idata["endOfRecords"]:
                break
        
        return pd.DataFrame(data)