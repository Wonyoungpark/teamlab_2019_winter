---
layout: post
title: Python으로 webtoon 정보 crawling & 웹사이트 구동
date: 2020-02-25 14:30:00 +0300
tags: crawling, selenium, crawling with Python, Web Bootcamp, mysql, django, aws
category: Crawling with Python
author: wonyoung park
math: false
published: true
comments: true
---

# NAVER WEBTOON에 대한 정보 갖고 오기

## Crawling
크롤링을 자동으로 하기 위해서 chromedriver를 설치하고 웹드라이버를 자동으로 열 수 있는 코드를 작성한다.
```
# 웹드라이브 열기
options = Options()
options.add_argument('--headless')
driver = webdriver.Chrome(chrome_options=options, executable_path='/home/ubuntu/workspace/workspace/chromedriver')
driver.implicitly_wait(3)
```

![요일 웹툰](https://user-images.githubusercontent.com/48376471/75151980-577a0680-574b-11ea-9698-4246f4019441.png)

각 요일별로 연재되는 웹툰의 정보를 크롤링을 하려고 한다.
먼저 각 요일별 페이지 url을 갖고 온다.
```
def get_url_list():
    urls = ['mon', 'tue', 'wed', 'thu', 'fri', 'sat', 'sun']
    URL_list = []
    for i in range(7):
        url = 'https://comic.naver.com/webtoon/weekdayList.nhn?week=' + urls[i]
        URL_list.append(url)

    return URL_list
```    

월요일 웹툰 페이지에 있는 웹툰 제목과 작가 이름, 평점, 웹툰별 url 등을 갖고 온다.
```
def get_whole_detail():
    soup = BeautifulSoup(source, 'html.parser')

    # 오늘 날짜
    date = soup.find("h3").text

    soup = soup.find('div', {'class': 'list_area daily_img'})

    # 웹툰별 url 받아오기
    hrefs = soup.select("dt > a[href]")
    for hre in hrefs:
        h = hre.get('href')
        hrefs_lists.append(h)

    # 웹툰별 제목 받아오기
    titles = soup.select("dt > a[title]")
    for tit in titles:
        t = tit.get('title')
        titles_lists.append(t)

    # 웹툰별 작가 받아오기
    writers = soup.select("dd[class=desc]")
    for writ in writers:
        w = writ.text.replace('\n', '')
        writers_lists.append(w)

    # 웹툰별 평점 받아오기
    rates = soup.select("div[class=rating_type] > strong")
    for rat in rates:
        r = rat.text
        rates_lists.append(r)

        # 각 웹툰마다 날짜 기입
        date_lists.append(date)

    whole_detail = {'date': date_lists, 'url': hrefs_lists, 'title': titles_lists, 'writer': writers_lists,
                    'rate': rates_lists}

    return whole_detail
```

![개별웹툰](https://user-images.githubusercontent.com/48376471/75152138-b8a1da00-574b-11ea-81e9-9a10e60b212c.png)

위에서 얻어온 요일별 웹툰 정보의 url을 이용하여 각 웹툰 페이지로 들어가서 장르와 이용가능 연령 등의 정보를 갖고 온다.
```
# 각 웹툰의 장르,이용가 받아오기
def get_details():
    genres_lists = []
    ages_lists = []

    for i in range(len(w_detail['url'])):
        each_url = 'https://comic.naver.com' + w_detail['url'][i]

        driver.get(each_url)
        source = driver.page_source
        soup = BeautifulSoup(source, 'html.parser')

        genre = soup.select("p[class=detail_info] > span[class=genre]")
        for gen in genre:
            g = gen.text
            genres_lists.append(g)

        age = soup.select("p[class=detail_info] > span[class=age]")
        for a in age:
            a = a.text
            ages_lists.append(a)

        detail_list = {'genre': genres_lists, 'age': ages_lists}

    return detail_list
```

전체 코드는 다음과 같다.
```
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
import time
import pandas as pd
from bs4 import BeautifulSoup
import requests
import urllib
import re
from urllib.request import urlopen
import selenium
import pandas as pd


def get_url_list():
    urls = ['mon', 'tue', 'wed', 'thu', 'fri', 'sat', 'sun']
    URL_list = []
    for i in range(7):
        url = 'https://comic.naver.com/webtoon/weekdayList.nhn?week=' + urls[i]
        URL_list.append(url)

    return URL_list


def get_whole_detail():
    soup = BeautifulSoup(source, 'html.parser')

    # 오늘 날짜
    date = soup.find("h3").text

    soup = soup.find('div', {'class': 'list_area daily_img'})

    # 웹툰별 url 받아오기
    hrefs = soup.select("dt > a[href]")
    for hre in hrefs:
        h = hre.get('href')
        hrefs_lists.append(h)

    # 웹툰별 제목 받아오기
    titles = soup.select("dt > a[title]")
    for tit in titles:
        t = tit.get('title')
        titles_lists.append(t)

    # 웹툰별 작가 받아오기
    writers = soup.select("dd[class=desc]")
    for writ in writers:
        w = writ.text.replace('\n', '')
        writers_lists.append(w)

    # 웹툰별 평점 받아오기
    rates = soup.select("div[class=rating_type] > strong")
    for rat in rates:
        r = rat.text
        rates_lists.append(r)

        # 각 웹툰마다 날짜 기입
        date_lists.append(date)

    whole_detail = {'date': date_lists, 'url': hrefs_lists, 'title': titles_lists, 'writer': writers_lists,
                    'rate': rates_lists}

    return whole_detail


# 각 웹툰의 장르,이용가 받아오기
def get_details():
    genres_lists = []
    ages_lists = []

    for i in range(len(w_detail['url'])):
        each_url = 'https://comic.naver.com' + w_detail['url'][i]

        driver.get(each_url)
        source = driver.page_source
        soup = BeautifulSoup(source, 'html.parser')

        genre = soup.select("p[class=detail_info] > span[class=genre]")
        for gen in genre:
            g = gen.text
            genres_lists.append(g)

        age = soup.select("p[class=detail_info] > span[class=age]")
        for a in age:
            a = a.text
            ages_lists.append(a)

        detail_list = {'genre': genres_lists, 'age': ages_lists}

    return detail_list
    
if __name__ == '__main__':
    # 웹드라이브 열기
    options = Options()
    options.add_argument('--headless')
    driver = webdriver.Chrome(chrome_options=options, executable_path='/home/ubuntu/workspace/workspace/chromedriver')
    driver.implicitly_wait(3)

    date_lists = []
    hrefs_lists = []
    titles_lists = []
    writers_lists = []
    rates_lists = []

    for u in range(7):
        # 요일별 전체 웹툰 정보 받아오기
        driver.get(get_url_list()[u])
        source = driver.page_source

        w_detail = get_whole_detail()

    # 요일별 각 웹툰으로 들어가기
    each_url = 'https://comic.naver.com' + w_detail['url'][0]
    driver.get(each_url)
    source = driver.page_source
    soup = BeautifulSoup(source, 'html.parser')

    # 요일별 전체웹툰 정보와 각 웹툰 정보 합치기
    w_detail.update(get_details())

    df = pd.DataFrame(w_detail)

    driver.close()
 ```
 다음 크롤링 결과를 csv파일로 저장하기 위한 코드이다.
```
# csv로 저장
df.to_csv("webtoonDetail_list.csv", index=False)
```
![크롤링결과](https://user-images.githubusercontent.com/48376471/75152713-0a972f80-574d-11ea-9f93-6e481cddca10.png)

## pymysql을 이용하여 database화 하기
먼저 pip install pymysql을 이용하여 pymysql 모듈을 설치한다.
mysql에 create문을 이용해 필요한 DB와 table을 생성한다.
pymysql을 이용하여 기존에 있던 정보들을 삭제하고 새로 받아온 DB를 불러온다.(주기적으로 크롤링을 할 것이다. 자세한 것은 뒤에서 다룰 것이다.)
```
def sql_db():
    # mysql DB에 접속
    db = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='', db='WT_DB', charset='utf8')

    cursor = db.cursor(pymysql.cursors.DictCursor)

    # 기존 DB 삭제
    dele = "DELETE FROM WT_DB.WT"
    cursor.execute(dele)
    db.commit()
                                                            
    # csv파일 읽어오기
    csv_data = pd.read_csv('webtoonDetail_list.csv', index_col=False)

    # 새 DB 불러오기
    sql = "INSERT INTO WT(wt_date, wt_url, wt_title, wt_writer, wt_rate, wt_genre,wt_age ) VALUES(%s, %s, %s, %s, %s, %s, %s)"
    for raw in csv_data.values:
        cursor.execute(sql, list(raw)[1:])

    db.commit()
    cursor.close()
```

