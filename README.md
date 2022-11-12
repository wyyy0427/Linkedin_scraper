# Linkedin_scraper
领英爬虫可用于自动抓取用户的姓名、学校、曾服务公司及岗位等信息。  
使用教程：  
1.安装pip3数据库 pip3 install --user linkedin_scraper  
2.安装驱动，驱动的版本要跟你浏览器版本对应 export CHROMEDRIVER=~/chromedriver  
注意：Windows系统安装要跟Google放一个文件夹下，注意路径

Sample Usage  
from linkedin_scraper import Person, actions
from selenium import webdriver
driver = webdriver.Chrome()
email = "some-email@email.address"
password = "password123"
actions.login(driver, email, password) # if email and password isnt given, it'll prompt in terminal
person = Person("https://www.linkedin.com/in/andre-iguodala-65b48ab5", driver=driver)
