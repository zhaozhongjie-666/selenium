selenium的常用方法

Python WebDriver 工作原理
webdriver 是按照 server – client 的经典设计模式设计的。 server 端就是 remote server，可以是任意的浏览器。当我们的脚本启动浏览器后，该浏览器就是remote server，它的职责就是等待 client 发送请求并做出响应； client 端简单说来就是我们的测试代码，我们测试代码中的一些行为，比如打开浏览器，转跳到特定的 url 等操作是以 http 请求的方式发送给被 测试浏览器，也就是 remote server；remote server 接受请求，并执行相应操作，并在 response 中返回执行状态、返回值等信息； 

webdriver 的工作流程： 
1. WebDriver 启动目标浏览器， 并绑定到指定端口。 该启动的浏览器实例， 做为 web driver 的 remote server。 
2. Client 端通过 CommandExcuter 发送 HTTPRequest 给 remote server 的侦听端口（通信协议： the webriver wire protocol） 
3. Remote server 需要依赖原生的浏览器组件（如：IEDriverServer .exe、chromedriver .exe） ，来转化转化浏览器的 native 调用。 
 

Python版Selenium提供了一套用于编写功能测试及验收测试的API。利用这套简单的API，不仅可以很直观的接触到Selenium WebDriver的所有功能，而且还可以很方便的访问各类WebDrivers，如Firefox、Ie、Chrome、Remote等。目前Selenium支持的Python版本：2.7、3.2、3.3和3.4.


Selenium可以从PyPI page for selenium package下载，但更好的方法是使用pip工具安装，Python3.4的标准库已经内置了pip工具。使用pip工具，可用如下命令安装Selenium：
pip install selenium


始编写第一个脚本。
[python] view plain copy print?
#coding = utf-8     
from selenium import webdriver
from selenium.webdriver.common.keys import keys  
  
driver = webdriver.Firefox()  
driver.get('http://www.python.org')  
assert 'python' in driver.title  
elem = driver.find_element_by_name('q')  
elem.send_keys('pycon')  
elem.send_keys(keys.RETURN)  
assert 'No results found.' not in driver.page_source  
driver.close()  
保存该脚本为python_org_search.py,并运行该脚本：
Python python_org_search.py
实例分析

Selenium.webdriver模块提供了webdriver的实现方法，目前支持这些方法的有Firefox、Chrome、IE和Remote。其中，Keys类提供了操作键盘的快捷键，如RETURE、F1、ALT等。

1）导入相关的包
[python] view plain copy print?
from selenium import webdriver  
from selenium.webdriver.common.keys importKeys  

2）接下来，创建Firefoxwebdriver实例。
driver = webdriver.Firefox()

3）driver.get()尝试打开URL指定的网页，webdriver会等待网页元素加载完成之后才把控制权交回脚本。但是，如果要打开了页面在加载的过程中包含了很多AJAX，webdriver可能无法准确判断页面何时加载完成。
driver.get("http://www.python.org")

4）然后，使用断言判断页面标题包含“Python”：
assert "Python" in driver.title

5）webdriver提供了很多如find_element_by_*的方法来匹配要查找的元素。如，利用name属性查找方法find_element_by_name来定位输入框。元素定位方法可以参考后面章节-元素定位方法。
elem = driver.find_element_by_name("q")

6）send_keys方法可以用来模拟键盘操作，但首先要从selenium.webdriver.common.keys导入Keys类：
elem.send_keys("pycon")
elem.send_keys(Keys.RETURN)

7）然后，提交请求页面并获得返回结果，另外，为了判断结果是否成功返回，可加入断言：
assert "No results found." not in driver.page_source

8）最后，操作完成并关闭浏览器。当然，也可以调用quit（）方法，两者的区别在于，quit（）方法会退出浏览器，而close（）方法只是关闭页面，但如果只有一个页面被打开，close（）方法同样会退出浏览器。
driver.close()
 
编写测试脚本

Selenium可用来编写测试用例，虽然Selenium并没有提供测试框架，但可以调用python的unittest模块，或py.test和nose。
本节给出了一个实现搜索python.org功能的测试用例，其中使用的是unittest框架。
[python] view plain copy print?
import unittest  
from selenium import webdriver  
from selenium.webdriver.common.keys import Keys  
   
class PythonOrgSearch(unittest.TestCase):  
    def setUp(self):  
        self.driver = webdriver.Firefox()  
   
  	 def test_search_in_python_org(self):  
        driver = self.driver  
        driver.get("http://www.python.org")  
        self.assertIn("Python", driver.title)  
        elem = driver.find_element_by_name("q")  
        elem.send_keys("pycon")  
        elem.send_keys(Keys.RETURN)  
        assert "No results found." not in driver.page_source  
   
   
    def tearDown(self):  
        self.driver.close()  
   
if __name__ == "__main__":  
  unittest.main()  

 
使用如下命令即可运行上面的测试脚本：
python test_python_org_search.py
.
----------------------------------------------------------------------
Ran 1 test in 15.566s
 
OK
2.4 分析2.3中测试脚本
1）首先需要导入相关模块，其中unittest是python的内置模块，它提供了组织测试用例的框架，而selenium.webdriver提供了所有WebDriver的实现，目前支持FireFox、Chrome、Ie和Remote。Keys类提供了关键字，如RETURN、F1、ALT等。代码如下：

[python] view plain copy print?
importunittest  
fromselenium import webdriver  
fromselenium.webdriver.common.keys import Keys  

2）测试用例继承于unittest，代码如下：
class PythonOrgSearch(unittest.TestCase):

3）SetUp方法是初始化的一部分，它会在每个测试功能开始之前被调用，创建firefox webdriver实例的代码如下：

defsetUp(self):
  self.driver=webdriver.Firefox()
  
4）创建测试用例，测试用例的方法名尽量以test字符串开头，代码的第一行创建webdriver实例对象的引用：
deftest_search_in_python_org(self):
  driver=self.driver
  
5）driver.get()方法打开URL定义的网址，webdriver会等待到页面完全加载完成后将控制权重新交给测试脚本，代码如下：
driver.get("http://www.python.org")

6）然后，使用断言判断页面标题包含“Python”：
assert "Python" in driver.title

7）webdriver提供了很多如find_element_by_*的方法来匹配要查找的元素。如，利用name属性查找方法find_element_by_name来定位输入框。元素定位方法可以参考后面章节-元素定位方法。
elem = driver.find_element_by_name("q")

8）send_keys方法可以用来模拟键盘操作，但首先要从selenium.webdriver.common.keys导入Keys类：
elem.send_keys("pycon")
elem.send_keys(Keys.RETURN)

9）然后，提交请求页面并获得返回结果，另外，为了判断结果是否成功返回，可加入断言：
assert "No results found." not in driver.page_source

10)所有测试用例执行完毕后会调用tearDown方法，这个方法主要执行清理工作。在本脚本中，tearDown方法执行关闭浏览器的操作，当然，也可以调用quit（）方法，两者的区别在于，quit（）方法会退出浏览器，而close（）方法只是关闭页面，但如果只有一个页面被打开，close（）方法同样会退出浏览器。
deftearDown(self):
  self.driver.close()
  
11)代码最后两行是执行测试套件的固定写法，代码如下：
if__name__=="__main__":
  unittest.main()

2.5 使用remote WebDriver
使用remote WebDriver之前，需要先启动selenium server，命令如下：
java -jar selenium-server-standalone-2.x.x.jar
selenium server运行之后会看到如下信息：
15:43:07.541 INFO - RemoteWebDriver instances should connect to: http://127.0.0.1:4444/wd/hub
上面的信息指明了连接seleniumserver的地址http://127.0.0.1:4444/wd/hub，下面是实例代码：
[python] view plain copy print?
fromselenium.webdriver.common.desired_capabilities import DesiredCapabilities  
   
driver = webdriver.Remote(  
   command_executor='http://127.0.0.1:4444/wd/hub',  
   desired_capabilities=DesiredCapabilities.CHROME)  
   
driver = webdriver.Remote(  
   command_executor='http://127.0.0.1:4444/wd/hub',  
   desired_capabilities=DesiredCapabilities.OPERA)  
   
driver = webdriver.Remote(  
   command_executor='http://127.0.0.1:4444/wd/hub',  
   desired_capabilities=DesiredCapabilities.HTMLUNITWITHJS)  

Desiredcapabilities是字典类型，因此除了使用默认值，也可以重新定义字典的值，代码如下：
[python] view plain copy print?
driver = webdriver.Remote(  
   command_executor='http://127.0.0.1:4444/wd/hub',  
   desired_capabilities={'browserName':'htmlunit',  
     'version':'2',  
    'javascriptEnabled':True})<span style="font-family: Arial, Helvetica, sans-serif; background-color: rgb(255, 255, 255);"> </span>  




模拟表单登陆qq企业邮箱
#coding:utf-8

import time

from selenium import webdriver
from selenium.webdriver.common.by import By

options = webdriver.ChromeOptions()
driver = webdriver.Firefox()

driver.get('http://exmail.qq.com/login')

test_user = {
'username': 'XXX',
'password': 'XXX',
}
#模拟表单输入账号名密码
user = driver.find_element(By.XPATH, '//input[@id="inputuin"]')
user.send_keys(test_user['username'])
time.sleep(1)
password = driver.find_element(By.XPATH, '//input[@id="pp"]')
password.send_keys(test_user['password'])
time.sleep(1)
btnSubmit = driver.find_element(By.XPATH, '//input[@id="btlogin"]')
btnSubmit.click()

selenium 技术：  
元素定位的几种方法  
WebDriver  API ，selenium  IDE，selenium grid

举例
百度搜索： 
# coding = utf-8 
from selenium import webdriver 
browser = webdriver.Firefox() 
browser.get("http://www.baidu.com") 
browser.find_element_by_id("kw1").send_keys("selenium") 
browser.find_element_by_id("su1").click() browser.quit() 


webdriver python： 元素定位 
常用的有以下几种 
id 
name 
class 
name
tag 
name 
link 
text 
partial link text 
xpath 
css selector
分别对应 python webdriver 中的方法为：
find_element_by_id() 
find_element_by_name() 
find_element_by_class_name() 
find_element_by_tag_name()
find_element_by_link_text() 
find_element_by_partial_link_text() 
find_element_by_xpath()
find_element_by_css_selector()  



id\name\class name\tag name : 
百度搜索框前端代码（通过firebug查看） 
<input  id="kw1" class="s_ipt" type="text" maxlength="100" name="wd" autocomplete="off">
find_element_by_id(‘kw1’) 
find_element_by_name(‘wd’) 
find_element_by_class_name(‘s_ipt’)
 find_element_by_tag_name(‘input’) 
 注：页面上的元素tag name 相同的几率很高 


link\partial link : 百度首页文字链接： 
<a href="http://news.baidu.com" name="tj_news">新 闻</a> 
<a href="http://tieba.baidu.com" name="tj_tieba">贴 吧</a> 
<a href="http://zhidao.baidu.com" name="tj_zhidao">知 道</a> 
find_element_by_link_text(u‘新 闻’) 
find_element_by_partial_link_text(‘新’) 
find_element_by_link_text(u‘贴 吧’) ... 
注：中文字符串加u 是将中文转换成unicode,防止编码问题。 




find_element_by_xpath(‘//input[@name='wd']’) find_element_by_xpath(‘//input[@class='s_ipt']’) find_element_by_xpath(‘//span[@class='bg s_iptwr']/input’) find_element_by_xpath(‘//form[@id='form1']/span/input’) .... find_element_by_xpath(‘/html/body/div/div[4]/div[2]/div/form/span/input’)


css常见语法


css

</form> 
<div class="subdiv">  
<ul id="recordlist">     
<p>Heading</p> 

定位</from> 
find_element_by_css_selector(‘from’) 

定位<div class="subdiv"> find_element_by_css_selector(‘.subdiv’) find_element_by_css_selector(‘from+div’) 

定位<ul id="recordlist"> find_element_by_css_selector(‘#recordlist’) find_element_by_css_selector(‘ul#recordlist’) find_element_by_css_selector(‘div>ul’) 

定位<p>Heading</p> find_element_by_css_selector(‘div>ul’) find_element_by_css_selector(‘div.subdiv > ul > p’) 

webdriver API 
浏览器最大化: maximize_window() 
设置浏览器宽、高： set_window_size(480, 800) 
控制浏览器后退，前进： back() forward() 
浏览器最大化   
driver.maximize_window() #将浏览器最大化显示 
浏览器设置宽高   driver.set_window_size(800, 600) 
控制浏览器前进、后退   
… 
driver.get('http://www.baidu.com') #访问百度首页 
driver.get(‘http://news.baidu.com’) #访问新闻页面 
driver.back() #返回（后退）到百度首页 
driver.forward() #前进到新闻页


WebElement接口常用方法: 
clear     清除元素的内容 
send_keys   在元素上模拟按键输入 (这里需要注意的是，我们一般会在脚本开始声明代码的编码格式为utf-8，所以当我们使用中文字符串操作时，应在字符串前面加小u，进行转码标识.   send_keys(u“中文内容”)
click     单击元素,单击任何可以点击的元素，文字/图片连接，按钮，   下拉按钮等。  
submit     提交表单，提交对象是一个表单  
size    返回元素的尺寸
text    获取元素的文本 
get_attribute(name)  获得属性值 
is_displayed()    设置该元素是否用户可见 

… 
driver.find_element_by_id(“username").clear() 
driver.find_element_by_id(" username ").send_keys("username") 
driver.find_element_by_id(“password").clear() 
driver.find_element_by_id(" password ").send_keys("password") 
driver.find_element_by_id("loginBtn").click() #通过 submit() 来提交表单 
#driver.find_element_by_id("loginBtn").submit() 
… 


 ActionChains 类鼠标操作的常用方法： 
context_click()  右击 
double_click()   双击
drag_and_drop()  拖动
move_to_element()  鼠标悬停在一个元素上 
click_and_hold()   按下鼠标左键在一个元素上 


ActionChains 类鼠标操作的常用方法： 
context_click()  右击 

#引入ActionChains类 
from selenium.webdriver.common.action_chains import ActionChains .... 


#定位到要右击的元素 
right =driver.find_element_by_xpath("xx") 


#对定位到的元素执行鼠标右键操作 
ActionChains(driver).context_click(right).perform() .... 


ActionChains 类鼠标操作的常用方法：  
drag_and_drop()  拖动 

#引入ActionChains类 
from selenium.webdriver.common.action_chains import ActionChains ... 

#定位元素的原位置 
element = driver.find_element_by_name("xxx") 

#定位元素要移动到的目标位置 
target =  driver.find_element_by_name("xxx") 

#执行元素的移动操作 
ActionChains(driver).drag_and_drop(element,target).perform() 


ActionChains 类鼠标操作的常用方法：  　　
move_to_element()   鼠标悬停 

#引入ActionChains类 
from selenium.webdriver.common.action_chains import ActionChains ... 

#定位元素的原位置 
element = driver.find_element_by_name("xxx") 

#定位元素要移动到的目标位置 
target =  driver.find_element_by_name("xxx") 

#执行元素的移动操作 
ActionChains(driver).drag_and_drop(element,target).perform() 

Keys 类键盘操作的常用方法： 　　
send_keys(Keys.BACK_SPACE) 删除键（BackSpace） 　　
send_keys(Keys.SPACE)  空格键(Space) 　　
send_keys(Keys.TAB)  制表键(Tab) 　　
send_keys(Keys.ESCAPE)  回退键（Esc） 　　
send_keys(Keys.ENTER) 回车键（Enter） 　　
send_keys(Keys.CONTROL,'a') 全选（Ctrl+A） 　　
send_keys(Keys.CONTROL,'c') 复制（Ctrl+C） 　　
send_keys(Keys.CONTROL,'x') 剪切（Ctrl+X） 　　
send_keys(Keys.CONTROL,'v') 粘贴（Ctrl+V） 


ctionChains 类鼠标操作的常用方法：  　　
move_to_element()   鼠标悬停 

... 
#输入框输入内容 driver.find_element_by_id("kw1").send_keys("seleniumm") 
time.sleep(3) 

 #删除多输入的一个m driver.find_element_by_id("kw1").send_keys(Keys.BACK_SPACE) 
time.sleep(3) 
... 

打印信息（断言的信息）： 　　
title  　　返回当前页面的标题 　　
current_url 　　获取当前加载页面的URL 　　
text  　　获取元素的文本信息 


打印信息（126邮箱）： 
#获得前面title，打印 
title = driver.title 
print title 

#获得前面URL，打印 
now_url = driver.current_url 
print now_url    

#获得登录成功的用户，打印 
now_user=driver.find_element_by_id("spnUid").text 
print now_user 


 脚本中的等待时间： 
sleep()：    python提供设置固定休眠时间的方法。 
implicitly_wait()：    是webdirver 提供的一个超时等待。 
WebDriverWait()：    同样也是webdirver 提供的方法。 

time.sleep(2)

Python WebDriver API-对话框处理
现在很多前端框架的对话框是 div 形式的，也有一些弹出框iframe处理比较麻烦，如上一节所说的。使用百度登录页面演示一下，这个登录对话框是个div

#coding=utf-8 
from selenium import webdriver 
driver = webdriver.Firefox() 
driver.get("http://www.baidu.com/") 
#点击登录链接 
driver.find_element_by_name("tj_login").click() 
#通过二次定位找到用户名输入框 
div=driver.find_element_by_class_name("tang-content").find_element_by_name("userName") 

div.send_keys("username") 
#输入登录密码 driver.find_element_by_name("password").send_keys("password")
#点击登录 
driver.find_element_by_id("TANGRAM__PSP_10__submit").click() 
driver.quit() 


webdriver提供定位一组对象的方法： 
find_elements_by_id() 
find_elements_by_name() 
find_elements_by_class_name() 
find_elements_by_tag_name() 
find_elements_by_link_text() 
find_elements_by_partial_link_text() 
find_elements_by_xpath() 
find_elements_by_css_selector()  


定位一组对象，例一：
…… 
# 选择页面上所有的tag name 为input的元素 
inputs = driver.find_elements_by_tag_name('input') 
#然后从中过滤出tpye为checkbox的元素，单击勾选 
for input in inputs:   
  if input.get_attribute('type') == 'checkbox':    
    input.click() 
……  

定位一组对象，例二：
 …… 
# 选择所有的type为checkbox的元素并单击勾选 
checkboxes = driver.find_elements_by_css_selector('input[type=checkbox]') 
for checkbox in checkboxes:
   checkbox.click()
……
 

层级定位： 
…… 
#点击Link1链接（弹出下拉列表） 
driver.find_element_by_link_text('Link1').click() 

#在父亲元件下找到link为Action的子元素 
menu = driver.find_element_by_id('dropdown1').find_element_by_link_text('Another action') 

#鼠标移动到子元素上 
ActionChains(driver).move_to_element(menu).perform()
…… 

frame表单嵌套的定位： 
switch_to_frame  方法 
…… 
#先找到到ifrome1（id = f1） 
driver.switch_to_frame("f1") 

#再找到其下面的ifrome2(id =f2) 
driver.switch_to_frame("f2") 

 #下面就可以正常的操作元素了 
driver.find_element_by_id("kw1").send_keys("selenium")
…… 


div弹窗的处理：
…… 
#点击登录链接 
driver.find_element_by_name("tj_login").click() 

#通过二次定位找到用户名输入框 
div=driver.find_element_by_class_name("tang-content").find_element_by_name("userName") div.send_keys("username") 
…… 

多窗口的处理： 
current_window_handle   　　获得当前窗口句柄 
window_handles 　　返回的所有窗口的句柄到当前会话 
switch_to_window()     用于处理多窗口之前切换  


多窗口的处理：
#获得当前窗口 
nowhandle=driver.current_window_handle 

#打开注册新窗口 
driver.find_element_by_name("tj_reg").click()

#获得所有窗口 
allhandles=driver.window_handles 

#循环判断窗口是否为当前窗口 
for handle in allhandles:
  if handle != nowhandle: 
    driver.switch_to_window(handle)
    print 'now register window!' 
    #切换到邮箱注册标签 
    driver.find_element_by_id("mailRegTab").click()  
    driver.close()
driver.switch_to_window(nowhandle)#回到原先的窗口 


alert/confirm/prompt处理： 
switch_to_alert()   用于获取网页上的警告信息。 
text      返回 alert/confirm/prompt 中的文字信息。
accept    点击确认按钮。 
dismiss    点击取消按钮，如果有的话。 
send_keys     输入值，这个alert\confirm没有对话框就不能用了，不然会报错。

driver = driver driver.find_element_by_name("username").clear() 
driver.find_element_by_name("username").send_keys("") 
driver.find_element_by_name("password").clear() 
driver.find_element_by_name("password").send_keys("") 
time.sleep(2) 
driver.find_element_by_name("Submit").click() 
try:
  alertstr = driver.switch_to_alert()
  alertTF = True 
except:
  alertTF = False 
if alertTF:
  print alertstr.text   
  alertstr.accept() 


下拉框也是 web页面上非常常见的功能，webdriver 对于一般的下拉框处理起来也相当简单，要想定位下拉框中的内容，首先需要定位到下拉框 使用教务系统中的添加课程窗口中的下拉框选择进行举例，使用不同方式进行实现。 
下拉框处理： 二次定位： 
driver.find_element_by_xx('xx').find_element_by_xx('xx').click() 
…… 
#先定位到下拉框 m=driver.find_element_by_id("ShippingMethod") 

#再点击下拉框下的选项 
m.find_element_by_xpath("//option[@value='10.69']").click() …… 





文件上传：
driver.find_element_by_xx('xx').send_keys('d:/abc.txt')  
…… 
#定位上传按钮，添加本地文件 
driver.find_element_by_name("file").send_keys('D:\\selenium_use_case\upload_file.txt')
…… 

文件下载： 
 确定Content-Type ： 下载文件的类型 
 方法一： curl -I URL | grep "Content-Type" 
 方法二： 
import requests 
print requests.head(’http://www.python.org’).headers[’content-type’] 


文件下载：
fp = webdriver.FirefoxProfile()
fp.set_preference("browser.download.folderList",2) 
fp.set_preference("browser.download.manager.showWhenStarting",False) 
fp.set_preference("browser.download.dir", os.getcwd()) 
fp.set_preference("browser.helperApps.neverAsk.saveToDisk", "application/octet-stream") 

browser = webdriver.Firefox(firefox_profile=fp) 
browser.get("http://pypi.python.org/pypi/selenium") 
browser.find_element_by_partial_link_text("selenium-2").click()
 


调用javaScript： 
execute_script()  调用js方法 

#隐藏文字信息 
driver.execute_script('$("#tooltip").fadeOut();') 

 #隐藏按钮： 
button = driver.find_element_by_class_name('btn') 
driver.execute_script('$(arguments[0]).fadeOut()',button) 


控制浏览器滚动条： 
#将页面滚动条拖到底部 
js="var q=document.documentElement.scrollTop=10000" 
driver.execute_script(js)

#将滚动条移动到页面的顶部 
js_="var q=document.documentElement.scrollTop=0" 
driver.execute_script(js_) 

cookie处理： 
get_cookies()    获得所有cookie信息 
get_cookie(name)    返回特定name 有cookie信息 
add_cookie(cookie_dict)    添加cookie，必须有name 和value 值 
delete_cookie(name)    删除特定(部分)的cookie信息 
delete_all_cookies()    删除所有cookie信息 

… 
driver = webdriver.Firefox() 
driver.get("http://www.youdao.com") 
#向 cookie 的 name 和 value 添加会话信息。
driver.add_cookie({'name':'key-aaaaaaa', 'value':'value-bbbb'}) 
#遍历 cookies 中的 name 和 value信息打印，当然还有上面添加的信息 
for cookie in driver.get_cookies(): 
  print "%s -> %s" % (cookie['name'], cookie['value']) 
##### 下面可以通过两种方式删除 cookie ##### 
# 删除一个特定的 
cookie driver.delete_cookie("CookieName") 
# 删除所有 
cookie driver.delete_all_cookies() 
time.sleep(2) 
… 

Python WebDriver API-获取对象属性
 获取测试对象的属性能够帮我们更好的进行对象的定位。比如页面上有很多标签为 input 元素，而我们需要定位其中 1 个有具有 data-node 属性不一样的元素。由于 webdriver 是不支持直接使用 data-node 来定位对象的，所以我们只能先把所有标签为 input都找到，然后遍历这些 input，获取想要的元素。


通过 find_elements 获得一组元素，通过循环遍历找到想要的元素：
# 选择页面上所有的 tag name 为 input 的元素 
inputs = driver.find_elements_by_tag_name('input') 
#然后循环遍历出 data-node 为594434493的元素，单击勾选 
for input in inputs:
  if input.get_attribute('data-node') == '594434493':   
    input.click() 
…… 









