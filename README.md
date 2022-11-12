# Linkedin_scraper
领英爬虫可用于自动抓取用户的姓名、学校、曾服务公司及岗位等信息。  
使用教程：  
1.安装pip3数据库 `pip3 install --user linkedin_scraper`  
2.安装驱动，驱动的版本要跟你浏览器版本对应 `export CHROMEDRIVER=~/chromedriver`  
注意：Windows系统安装要跟Google放一个文件夹下，注意路径

Sample Usage  
```from linkedin_scraper import Person, actions
from selenium import webdriver
driver = webdriver.Chrome()
email = "some-email@email.address"
password = "password123"
actions.login(driver, email, password) # if email and password isnt given, it'll prompt in terminal
person = Person("https://www.linkedin.com/in/andre-iguodala-65b48ab5", driver=driver)```
登录账号应设为英文，不然会出错  

保存员工信息
```xlsx = pd.ExcelFile(r'C:\Users\12610\领英\汽车整车制造企业名单_refined.xlsx')
data = pd.read_excel(xlsx,'Sheet1')
for indexs in range(98,313):
    print(indexs)
    count = 0
    key = str(data.loc[indexs].values)[2:-2] + ' site:linkedin.com'
    driver = webdriver.Chrome("C:\Program Files (x86)\Google\Chrome\Application\chromedriver.exe")

email = "wy18927612345@163.com"
password = "wy270260"
actions.login(driver, email, password) # if email and password isnt given, it'll prompt in terminal

    #定位搜索框
    searchingBox = driver.find_element_by_xpath('//*[@id="kw"]')
    searchingBox.send_keys(key)
    click_by_time(driver,'//*[@id="su"]',10)
    while(True):
        count+=1
        time.sleep(5)#防止title未加载是空列表
        titles = driver.find_elements_by_class_name('c-container')
        for title in titles:
            result = title.find_element_by_tag_name('a')
            if ('|' in result.text and str(data.loc[indexs].values)[2:-2] in result.text):
                spans = title.find_elements_by_tag_name('a')
                for span in spans[1:]:
                    href = span.get_attribute('href')
                    if 'www.baidu.com/link' in str(href):
                        with open(r'C:\Users\12610\领英\test_test.txt','a+') as f:
                            f.write(str(href))
                            f.write('\n')
        time.sleep(1)
        try:
            pagebar = driver.find_element_by_xpath('//*[@id="page"]/div') 
        except NoSuchElementException:
            break
        former_next_page = pagebar.find_elements_by_class_name('n')
        if len(former_next_page)==0:
            break
        if count==1:
            next_page = former_next_page[-1]
            next_page.click()
            continue
        if len(former_next_page)==2:
            next_page = former_next_page[-1]
            next_page.click()
        else:
            break```
所有员工的网址都保存在了网址.txt中,下一步就是读取该文件然后进入领英界面爬取要的数据

抓取所需数据
```f = open(r'C:\Users\12610\领英\test_test.txt','r')
urls = f.readlines()
urls = list(set(urls))
urls = urls[2062:]
excelname = r'C:\Users\沈鋆阳\领英\linkedin_all_test_8.xls'
workbook = xlwt.Workbook(encoding='utf-8')
worksheet = workbook.add_sheet('Sheet1',cell_overwrite_ok=True)        
workbook.save(excelname)
count = 0
num = 2062
break_count = 0
ember_adder = 0
warn = ''
none = ''
for url in urls:
    try:
        warn = driver.find_element_by_xpath('//*[@id="app__container"]/main/h1').text
        if warn == '请进行快速的安全验证':#领英已经注意到了你
            break
    except:
        pass
    if break_count == 10:
        break
    print(num)
    num+=1
    chrome_options = Options()
    chrome_options.add_experimental_option("debuggerAddress", "127.0.0.1:9222")
    chrome_driver = r"C:\Users\12610\AppData\Local\Google\Chrome\Application\chromedriver.exe"
    driver = webdriver.Chrome(chrome_driver,options=chrome_options)
    try:
        driver.get(url)
    except:
        driver.refresh()
        time.sleep(10)
    y = 100
    for timer in range(0,20):
        driver.execute_script("window.scrollTo(0, "+str(y)+")")
        y += 100  
        time.sleep(0.5)
    try:
        name = driver.find_element_by_xpath('//*[@id="ember61"]/div[2]/div[2]/div[1]/ul[1]/li[1]').text
        #name = driver.find_element_by_xpath('//*[@id="ember' + str(63+ember_adder*2) + '"]/div[2]/div[2]/div[1]/ul[1]/li[1]').text
    except NoSuchElementException:
        try:
            #name_changed_xpath = '//*[@id="ember' + str(63+2) + '"]/div[2]/div[2]/div[1]/ul[1]/li[1]'
            name = driver.find_element_by_xpath('//*[@id="ember63"]/div[2]/div[2]/div[1]/ul[1]/li[1]').text
            #ember_adder += 1
        except NoSuchElementException:
            name = '无'
    #没有名字的数据无价值,若超过十次说明有意外情况
    if name == '无':
        break_count += 1
        time.sleep(10)
        continue    
    else:
        break_count = 0
    try:
        company = driver.find_element_by_xpath('//*[@id="ember98"]').text 
    except NoSuchElementException:
        try:
            #company_changed_xpath = '//*[@id="ember' + str(101+ember_adder*2)+'"]'
            company = driver.find_element_by_xpath('//*[@id="ember100"]').text
        except NoSuchElementException:
            company = '无'
    try:
        c_j = driver.find_element_by_xpath('//*[@id="ember61"]/div[2]/div[2]/div[1]/h2').text
    except NoSuchElementException:
        try:
            #c_j_changed_xpath = '//*[@id="ember' + str(63+ember_adder*2)+'"]/div[2]/div[2]/div[1]/h2'
            c_j = driver.find_element_by_xpath('//*[@id="ember63"]/div[2]/div[2]/div[1]/h2').text
        except NoSuchElementException:
            c_j = '无'
        
    try:
        district = driver.find_element_by_xpath('//*[@id="ember61"]/div[2]/div[2]/div[1]/ul[2]/li[1]').text
    except NoSuchElementException:
        try:
            #district_changed_xpath = '//*[@id="ember' + str(63+ember_adder*2)+'"]/div[2]/div[2]/div[1]/ul[2]/li[1]'
            district = driver.find_element_by_xpath('//*[@id="ember63"]/div[2]/div[2]/div[1]/ul[2]/li[1]').text
        except NoSuchElementException:
            district = '无'
            
    try:
        experience = driver.find_element_by_xpath('//*[@id="experience-section"]').text
    except NoSuchElementException:
            experience = '无' 
    if experience == '无':
        try:
            experience = driver.find_element_by_xpath('//*[@id="experience-section"]').text
        except NoSuchElementException:
            experience = '无' 
    if experience == '无':
        f = open(r"C:\Users\12610\领英\experience_fail.txt",'a+')
        f.write(url)
        f.close()
        continue
    try:
        education = driver.find_element_by_xpath('//*[@id="education-section"]').text
        if not '学' in str(education):
            education = '无'
    except NoSuchElementException:
        education = '无'
    time.sleep(3)
    oldwb = xlrd.open_workbook(excelname)
    newwb = copy(oldwb)
    ws = newwb.get_sheet(0)
    ws.write(count,0,name)
    ws.write(count,1,company)
    ws.write(count,2,c_j)
    ws.write(count,3,district)
    ws.write(count,4,experience)
    ws.write(count,5,education)
    ws.write(count,6,url)
    newwb.save(excelname)
    count+=1
    driver.quit()
    time.sleep(3)```
    


