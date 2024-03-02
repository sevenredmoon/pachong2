import re
import requests
import os
import time
import json
headers = {
    'User-Agent': 'Chrome/10'
}
def main():
    print('例如：李荣浩的网址是：https://www.kugou.com/singer/93475.html', '\n', 'ID就是：93475')
    ID=input("请输入歌手ID：")
    url='https://www.kugou.com/yy/singer/'+ID+'.html'
    html=requests.get(url,headers).text
    getname(html)
    get_id(html)
def getname(html):
    findlink=re.compile(r"singername = '(.*?)',")
    singername=re.findall(findlink,html)[0]
    creat(singername)
def creat(singername):          #创建文件夹
    if not os.path.exists(singername):
        os.mkdir(singername)  # 如果该目录不存在就创建它
    os.chdir(singername)
def change(name):
    s=name.encode('utf-8').decode('unicode_escape')
    return s
def get_id(html):
    findlink0=re.compile(r',"album_id":(\d*),"')
    findlink1 = re.compile(r'"HASH":"(.*?)"')
    findlink2 = re.compile(r'"album_id":(\d*)')
    findlink3 = re.compile(r'"audio_name":"(.*?)"')
    findlink4 = re.compile(r'songsdata = \[\{(.*?)\}\]; ')
    zhuanjiID = re.findall(findlink0,html)
    html1 = re.findall(findlink4, html)[0]
    names = re.findall(findlink3, html1)
    IDS = re.findall(findlink2, html1)
    HASHs = re.findall(findlink1, html1)
    for ID,HASH,name in zip(IDS,HASHs,names):
        time.sleep(0.3)
        name=change(name)
        php(ID,HASH,name)
def php(ID,HASH,name):
    url='https://wwwapi.kugou.com/yy/index.php?r=play/getdata&callback=jQuery1910956225802756826_1614222592545&hash='+HASH+'&dfid=0iAXFx4XRmAm3c2qw92nfbs2&mid=93e241c9b3b4e69d69d918c14a4b773e&platid=4&album_id='+ID+'&_=1614222592547'
    html=requests.get(url,headers).text
    findlink1=re.compile(r'"play_url":"(.*?)","')
    url=re.findall(findlink1,html)
    urlz='{"song_url":"'+url[0]+'"}'
    urlj=json.loads(urlz)
    with open(name+'.mp3','wb') as f:
        print("《",name,"》","下载中")
        f.write(requests.get(urlj['song_url']).content)
        f.close()
        print("下载完成",'\n')
if __name__ == '__main__':
    main()


