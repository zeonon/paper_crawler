## paper_crawler Tutorial 
크롤링 아시져? 그거예요 페이지 들어가서 정보 긁어오는거요 
저는 우선 논문사이트 ACS상대로 만들어봤구여 
토픽은 battery anode입니당


```
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time
import os


def createDirectory(directory):
    try:
        if not os.path.exists(directory):
            os.makedirs(directory)
    except OSError:
        print("Error: Failed to create the directory.")

def crawling_img(name):
    driver = webdriver.Chrome("C:\chromedriver_win32\chromedriver.exe")      # 크롬 접속
    driver.get("https://pubs.acs.org/")                                     # ACS 홈페이지 접속
    elem = driver.find_element_by_name("AllField")
    elem.send_keys(name)
    elem.send_keys(Keys.RETURN)

    paper_idx = 0
    paper_skip_idx = 25

    i = 0
    i_end = 3
    while True: #i < i_end:
        i = i + 1
        pagetitle = driver.find_elements_by_class_name('issue-item_title')         # 논문 제목 찾기
        print(pagetitle)

        for li in pagetitle:
            print(paper_idx)
            paper_idx = paper_idx + 1
            if paper_idx <= paper_skip_idx:
                continue


            aTag = li.find_element_by_tag_name("a")
            href = aTag.get_attribute("href")

            print('제목: ', aTag.text)                                             # 논문 제목과 링크 표시
            print('링크: ', href)
            driver2 = webdriver.Chrome("C:\chromedriver_win32\chromedriver.exe")
            driver2.get(href)
            time.sleep(5)
            pageSource = driver2.page_source
            journal_name = aTag.text
            journal_name = journal_name.replace("/", " ")
            journal_name = journal_name.replace(":", " ")
            try:
                fileToWrite = open("./ACS/ht/"+journal_name+".html", "w", encoding='utf8')       # 논문 html 파일 저장
                fileToWrite.write(pageSource)
                fileToWrite.close()

            except:
                print("error(journal name) ", aTag.text)
            driver2.close()


        #
        SCROLL_PAUSE_TIME = 1
        # Get scroll height
        last_height = driver.execute_script("return document.body.scrollHeight")  # 브라우저의 높이를 자바스크립트로 찾음
        while True:
            # Scroll down to bottom
            driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")  # 브라우저 끝까지 스크롤을 내림
            # Wait to load page
            time.sleep(SCROLL_PAUSE_TIME)
            # Calculate new scroll height and compare with last scroll height
            new_height = driver.execute_script("return document.body.scrollHeight")
            if new_height == last_height:
                try:
                    driver.find_element_by.css_selector(".mye4qd").click()
                except:
                    break
            last_height = new_height



        # 페이지 넘기기
        page_bar = driver.find_elements_by_css_selector('#pb-page-content > div > main > div.container > div > div.col-lg-9.col-sm-8 > div > div.search-result_top-pagination > nav > ul')[0]
        pages = page_bar.find_elements_by_css_selector('a')

        page_now = page_bar.find_elements_by_css_selector('a.active')[0].text.replace('현재 페이지', '').strip()

        print("next page")
        print(page_bar)
        print(pages)


        for page in pages:
            page_bar = page.text.strip()

            if page_bar == 'a':
                pass

            elif page_bar == 'paginate--btn__next':
                page.send_keys("\n")
                driver.implicitly_wait(3)
                time.sleep(2)

            elif int(page_bar) > int(page_now):
                page.send_keys("\n")
                driver.implicitly_wait(3)
                time.sleep(2)
                break


    print("마지막 페이지")
    return True


    is_done = False

    while(not is_done):
        is_done = move_next(driver)



    driver.close()
idols = ["battery anode"]


for idol in idols:
    crawling_img(idol)
```



