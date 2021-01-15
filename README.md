# HeyWorld.gitignore


import urllib.request
import time
from bs4 import BeautifulSoup
import smtplib
import pymysql

TotalInvest=3500

AgStock=20.97  #Hypothetical

MetalInvest=TotalInvest*20/100
GoldInvest=MetalInvest*0.4
SilverInvest=MetalInvest*0.6


# Prices GBP - USD

def USDtoGBP(url):

	user_agent = 'Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.0.7) Gecko/2009021910 Firefox/3.0.7'
	headers={'User-Agent':user_agent,} 
	request=urllib.request.Request(url,None,headers) #The assembled request
	html = urllib.request.urlopen(request)
	bs=BeautifulSoup(html.read(), 'html.parser')
	output=bs.find('div', {'class':'rates'})#Defining DJIA price
	
			
	return output

USD2GBP=float(USDtoGBP('https://www.dollars2pounds.com/').find('span', {'id':'ratesC1Value'}).get_text())


#Comodities --- Oil
	# 	European (UK)

def OilBrent(url):

	user_agent = 'Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.0.7) Gecko/2009021910 Firefox/3.0.7'
	headers={'User-Agent':user_agent,} 
	request=urllib.request.Request(url,None,headers) #The assembled request
	html = urllib.request.urlopen(request)
	bs=BeautifulSoup(html.read(), 'html.parser')
	buff=bs.find('div', {'class':'flag_uk'}).parent.next_siblings # In USD
	output=[]
	
	for sibling in buff:
		output.append(sibling)
		
	return float(output[2].get_text())

OilBrent=OilBrent('https://oilprice.com/oil-price-charts/')



def OilWTI(url):

	user_agent = 'Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.0.7) Gecko/2009021910 Firefox/3.0.7'
	headers={'User-Agent':user_agent,} 
	request=urllib.request.Request(url,None,headers) #The assembled request
	html = urllib.request.urlopen(request)
	bs=BeautifulSoup(html.read(), 'html.parser')
	buff=bs.find('div', {'class':'flag_usa'}).parent.next_siblings #Defining DJIA price
	output=[]
	
	for sibling in buff:
		output.append(sibling)
		
	return float(output[2].get_text())

OilWTI=OilWTI('https://oilprice.com/oil-price-charts/')    #In USD




#Getting the FTSE100 --> Index


def OpenFTSE100(url):
	user_agent = 'Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.0.7) Gecko/2009021910 Firefox/3.0.7'
	headers={'User-Agent':user_agent,} 
	request=urllib.request.Request(url,None,headers) #The assembled request
	html = urllib.request.urlopen(request)
	bs=BeautifulSoup(html.read(), 'html.parser')
	output=bs.find('span', {'id':'quote_val'}).get_text()  #Defining DJIA price

	return output

ftse100=OpenFTSE100('https://www.wsj.com/market-data/quotes/index/UK/UKX/historical-prices')


#Getting the DJIA - Dow Jones Industrial Average  --> Index

def OpenDJIA(url):
	user_agent = 'Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.0.7) Gecko/2009021910 Firefox/3.0.7'
	headers={'User-Agent':user_agent,} 
	request=urllib.request.Request(url,None,headers) #The assembled request
	html = urllib.request.urlopen(request)
	bs=BeautifulSoup(html.read(), 'html.parser')
	output=bs.find('span', {'id':'quote_val'}).get_text()  #Defining DJIA price

	return float(output)

DJIA=OpenDJIA('https://www.wsj.com/market-data/quotes/index/DJIA')


# Silver and Gold

def Open(url):
	user_agent = 'Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.0.7) Gecko/2009021910 Firefox/3.0.7'
	headers={'User-Agent':user_agent,} 
	request=urllib.request.Request(url,None,headers) #The assembled request
	html = urllib.request.urlopen(request)
	bs=BeautifulSoup(html.read(), 'html.parser')
	output=bs.find('span', {'name':'current_price_field'}).get_text()   #Defining Silver price

	return output

Ag=float(Open("https://www.gold.co.uk/silver-price/"))


Au=str(Open("https://www.gold.co.uk/gold-price/"))
buff=''
for char in Au:
	if char != ',':
		buff=buff+char
Au=float(buff)



#  We end up with Ag and Au







# 							Then we scrap the stocks of interest related to metals





def Openpaas(url):
	user_agent = 'Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.0.7) Gecko/2009021910 Firefox/3.0.7'
	headers={'User-Agent':user_agent,} 
	request=urllib.request.Request(url,None,headers) #The assembled request
	html = urllib.request.urlopen(request)
	bs=BeautifulSoup(html.read(), 'html.parser')
	buff=bs.find('span', {'class' : 'stock-price'}).get_text()

	output=''

	for char in buff:
		if char not in '$USD':
			output=output+char

	return float(output)


PAASprice=Openpaas('https://www.panamericansilver.com/')   #   Pan American Silver Stock  ===> PAASprice in USD



# def Openbarrick(url):
# 	user_agent = 'Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.0.7) Gecko/2009021910 Firefox/3.0.7'
# 	headers={'User-Agent':user_agent,} 
# 	request=urllib.request.Request(url,None,headers) #The assembled request
# 	html = urllib.request.urlopen(request)
# 	bs=BeautifulSoup(html.read(), 'html.parser')
# 	output=bs.find('span', {'class':'push-data aktien-big-font text-nowrap no-padding-at-all'}).get_text()

# 	return float(output)


# BarrickPrice=Openbarrick('https://markets.businessinsider.com/stocks/gold-stock')    # BarrickGold   ====> BarrickPrice in USD







#						Done with the scraping






Au2Ag=Au/Ag
	
Ag2Au=Ag/Au



def MetalStockDev(metal, stock):
	metal=metal
	stock=stock
	return (stock-metal)**2



class Metal:
	def __init__(self, name):
		self.Name=name
		self.Amount=[]
		self.Price=[]


	def MarketPrice(self):
		return self.Price

	def Oz(self):
		for i in self.Amount: return self.Amount/self.Price

	def purchase(self, amount, price):
		self.Time=time.ctime()
		self.Amount.append(amount)
		self.Price.append(price)


Gold=Metal('Gold')
Silver=Metal('Silver')


""" 						
							Sending e-mail bit


conn=smtplib.SMTP('smtp.gmail.com', 587)
conn.ehlo()
conn.starttls()
conn.login('ddiazsouto@gmail.com', 'alvwtudkkinbadgk')
conn.sendmail('ddiazsouto@gmail.com', 'daniel.souto@laughtondigital.com', 'Subject: Morning Report \n\nGood morning motherfucker!\n\nWhat a great day to make money with this prices\n\nGold is: {}oz\nSilver is: {}\n\n\nGold to Silver ratio: {}\n\n\nScraped on {}'.format(Au, Ag, Au2Ag, time.ctime()))# ignaa91@gmail.com

conn.quit()


"""






print('Gold is:', Au)
print('silver is:', Ag)
print('PAAS:', PAASprice)
#print('Barrick:', BarrickPrice)

print('Gold to Silver:', Au2Ag, 'Silver to Gold', Ag2Au)

print('To invest in Gold:', GoldInvest, '\nTo invest in Silver:', SilverInvest)

print('Metal deviation from PAAS: {:.2f}'.format(MetalStockDev(Ag*1.5, PAASprice)))
#print('Metal deviation from BarrickGold: {:.2f}'.format(MetalStockDev(Au, BarrickPrice*50)))

print('DJIA:', DJIA)

print('FTSE:', ftse100)

print('Crude Brent (Europe):', OilBrent)
print('Crude WTI (USA):', OilWTI)

print('GBP to USD', 1/USD2GBP)

if Ag<19:
	print('Buy Silver')



#		Insert in DATABASE

Make=pymysql.connect(host='127.0.0.1', user='root', passwd='Buddhassister22', db='mysql')
MySQL=Make.cursor()

# 				First price of Silver

MySQL.execute('USE portfolio')
MySQL.execute('INSERT INTO silver (PriceUSD, PriceGBP, Au2Ag_ratio) values ({:.2f}, {:.2f}, {:.2f})'.format(float(Ag)/float(USD2GBP), float(Ag), float(Au2Ag)))

#				Remember this is the side Database

MySQL.execute('USE portfolio')
MySQL.execute('INSERT INTO Gold (PriceUSD, PriceGBP, Au2Ag_ratio) values ({:.2f}, {:.2f}, {:.2f})'.format(float(Au)/float(USD2GBP), float(Au), float(Au2Ag)))


# MySQL.execute('USE portfolio')
# MySQL.execute('INSERT INTO gbp (inUSD) values ({:.4f})'.format(1/USD2GBP))

Make.commit()

Make.close()
MySQL.close()
