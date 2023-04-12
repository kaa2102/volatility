import math
from datetime import date

td_consumerkey=[YOUR CONSUMER KEY]
endpoint = 'https://api.tdameritrade.com/v1/marketdata/{stock_ticker}/quotes?'

today = pd.to_datetime("today")
k=time.mktime(today.timetuple())*1000
k=round(k,0)
k=int(k)
i=k-(24*86400000)

#Fair Value Volatility Calculation
endpoint = 'https://api.tdameritrade.com/v1/marketdata/{stock_ticker}/pricehistory'
full_url = endpoint.format(stock_ticker='SPY')
page = requests.get(url=full_url,
                    params={'apikey' : td_consumerkey,
                            'periodType': 'month',
                            'frequencyType': 'daily',
                            'startDate':i,
                             'endDate':k,
                           })
content = json.loads(page.content)

stockPrice=[]

for i in range(len(content['candles'])):
    d=content['candles'][i]['datetime']
    dia=pd.Timestamp(d, unit='ms')
    dia=dia.strftime('%Y-%m-%d')
    stockPrice.append(content['candles'][i]['close'])    

tag=len(stockPrice)
tally=0
count=0

for x in range(tag):
    if((tag-x-2)):
        unisquared=math.log(stockPrice[tag-x-1]) - math.log(stockPrice[tag-x-2])
        squared=math.pow(unisquared,2)
            
        if(count<=14):
            tally=tally+squared
 
    count=count+1

volatility=math.sqrt(tally/15)*math.sqrt(248)
print(volatility)