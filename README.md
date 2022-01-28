# -*- coding: utf-8 -*-
import urllib3
from lxml import etree
import html
import re

blogUrl = 'https://blog.csdn.net/fly910905'

headers={'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.89 Safari/537.36'} 

def addIntro(f):
	txt = ''' 
7年技术博主，CSDN认证博客专家，阿里云认证技术专家,Kubernetes中文社区合作者
现为某厂Java后端开发工程师，拥有丰富的 挖坑 踩坑 填坑 背锅经验     
专注于Java，对操作系统、网络、编译原理也有涉猎
''' 
	f.write(txt)

def addProjectInfo(f):
	txt ='''
### 开源项目  
- [java-treasurebox](https://github.com/FLY-Open-DevOps/java-treasurebox)JAVA百宝箱   

   
[查看更多](https://github.com/Fly0905/)     
	''' 
	f.write(txt) 


def addBlogInfo(f):  
	http = urllib3.PoolManager(num_pools=5, headers = headers)
	resp = http.request('GET', blogUrl)
	resp_tree = etree.HTML(resp.data.decode("utf-8"))
	html_data = resp_tree.xpath(".//div[@class='article-item-box csdn-tracking-statistics']/h4") 
	f.write("\n### 我的博客\n")
	cnt = 0
	for i in html_data: 
		if cnt >= 5:
			break
		title = i.xpath('./a/text()')[1].strip()
		url = i.xpath('./a/@href')[0] 
		item = '- [%s](%s)\n' % (title, url)
		f.write(item)
		cnt = cnt + 1
	f.write('\n[查看更多](https://blog.csdn.net/fly910905/)\n')

f = open('README.md', 'w+')
addIntro(f)
f.write('<table><tr>\n')
f.write('<td valign="top" width="50%">\n')

addProjectInfo(f)
f.write('\n</td>\n')
f.write('<td valign="top" width="50%">\n')
addBlogInfo(f)
f.write('\n</td>\n')
f.write('</tr></table>\n')
f.close
