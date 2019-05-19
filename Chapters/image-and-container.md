# มาทำความเข้าใจกับ Image กับ Container กันดีกว่า

ก่อนที่เราจะไปทำความเข้าใจกับ Container ผมขอเกริ่นเรื่องนี้ก่อนละกัน ปรกติเวลาที่เราจะไปเขียนโปรแกรมอะไรก็แล้วแต่ เราจะต้องมีการเตรียม environment ต่างๆให้ตรงกับที่เราจะใช้ใช่ปะ? (เช่นจะเขียน Python ก็ต้องติดตั้ง Python runtime เอาไว้ไรงี้) ซึ่งเจ้า Docker ก็เข้าใจความวุ่นวายในการเตรียม environment พวกนี้ดี ดังนั้นตัวมันเลยมีความสามารถในการสร้างสิ่งที่เรียกว่า **`Image`** ซึ่งเจ้าตัว image นี้เปรียบเสมือนกับเป็นต้นแบบในการสร้าง environment ตามที่เราอยากได้นั่นเอง คราวนี้เวลาที่เราอยากจะเขียน Python เราก็ไม่ต้องไปติดตั้งอะไรวุ่นวายอีกแล้ว เราก็แค่เอาเจ้า Image นี้ไปใช้ เราก็จะได้ environment ตามที่เราต้องการเลยทันที! ซึ่งเจ้าตัว Image จะถูกเขียนเป็นคำสั่งไว้ในไฟล์ที่ชื่อว่า **`Dockerfile`**

จากที่ว่ามา ถ้าเราอยากได้ environment แบบไหน เราก็แค่ไปสร้างไฟล์ **`Dockerfile`** ไว้ซะ

## Dockerfile
คือชุดคำสั่งที่เราเตรียมให้กับ Docker เอาไปสร้างเป็น **Image** โดยชุดคำสั่งพวกนี้สามารถกำหนดได้หมดเลยว่า เราจะให้มันติดตั้งอะไรให้เราบ้าง เช่น Python version อะไร network เป็นแบบไหน จะเปิด port อะไรบ้าง จะให้ copy หรือทำอะไรกับไฟล์ก่อนไหม บลาๆๆ (เยอะม๊วก)

> ในตัวอย่างนี้เราจะสร้างเว็บกากๆขึ้นมาตัวนึงที่เขียนโดยใช้ Python แต่เพื่อให้มันไม่กากจนเกินไป เราจะมีการนับด้วยว่ามีการเข้ามาดูหน้าเว็บกี่ครั้งแล้ว โดยใช้ Redis เป็น database (เหมือนจะกากแต่มีการใช้ Redis ด้วยนะเฟร้ย!!) ปะลองไปสร้างไฟล์ต่างๆกันเลย

ในตัวอย่างนี้ผมจะสร้างไฟล์ทุกอย่างไว้ใน `c:\python` นะ ใครอยากเอาไปไว้ที่อื่นก็ตามสะดวก

**Dockerfile**
```
# กำหนดให้มันติดตั้ง Python version 2.7 slim
FROM python:2.7-slim

# กำหนด folder ที่จะทำงานด้วยไว้ที่ /app
WORKDIR /app

# สั่งให้มัน Copy ไฟล์ทุกอย่างใน folder ปัจจุบันไปใส่ไว้ใน /app ที่อยู่ใน container
COPY . /app

# ติดตั้ง packages จากไฟล์ requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# เปิด port 80 ให้กับ container เพื่อให้เครื่องเราสามารถเข้าใช้งาน port นี้ได้
EXPOSE 80

# ตั้งค่าตัวแปรต่างๆเพื่อเอาไปใช้ในการสร้าง environment (ตัวอย่างการใช้อยู่ในไฟล์ app.py)
ENV NAME World

# สั่งให้มันรัน app.py เมื่อ container ถูกสร้าง
CMD ["python", "app.py"]
```
> จากคำสั่งที่เราเขียนไว้ มันอาจจะอ้างถึงไฟล์อีก 2 ไฟล์คือ `requirements.txt` และ `app.py` ดังนั้นเราก็ไปสร้างไฟล์ 2 ตัวนั้นด้วย

**requirements.txt**
```
Flask
Redis
```
> เป็นตัวกำหนดว่าเราจะติดตั้ง Package อะไรบ้างให้กับ environment ของเรา ซึ่งในที่นี้จะติดตั้ง 2 package คือ `Flask` กับ `Redis`

**app.py**
```
from flask import Flask
from redis import Redis, RedisError
import os
import socket

# Connect to Redis
redis = Redis(host="redis", db=0, socket_connect_timeout=2, socket_timeout=2)

app = Flask(__name__)

@app.route("/")
def hello():
    try:
        visits = redis.incr("counter")
    except RedisError:
        visits = "<i>cannot connect to Redis, counter disabled</i>"

    html = "<h3>Hello {name}!</h3>" \
           "<b>Hostname:</b> {hostname}<br/>" \
           "<b>Visits:</b> {visits}"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```
> เป็นคำสั่งภาษา Python ที่จะสร้างเว็บขึ้นมาบนเครื่องเรา และคอยนับว่ามีการเปิดหน้าเว็บนี้กี่ครั้งแล้ว

อะเคร๊!! จากที่สร้างทั้ง 3 ไฟล์ขึ้นมา เราก็พร้อมแล้วที่จะไปสร้าง `Image` ละ แล้วจะรอช้าอยู่ใยไปสร้างกันเลย

## การสร้าง Image
การสร้าง image เราจะต้องใช้ command line ดังนั้นจงเปิด command prompt, powerShell หรือ terminal ขึ้นมาซะ (เลือกเปิดแค่ตัวเดียวก็พอนะ -_-'') แล้วพอเปิดขึ้นมาละ ก็ให้มันไปอยู่ที่ folder ของเราซะ (ของผมอยู่ที่ `c:\python` ใครสร้างที่อื่นก็จงไปตามทางที่ท่านสร้าง) ซึ่งภายในนั้นจะต้องมีไฟล์ 3 ไฟล์ที่เราสร้างไว้นะ `Dockerfile`, `requirements.txt`, `app.py` ซึ่งถ้ามีครบแล้วก็ใช้คำสั่งในการสร้าง Image กันเลยโดยใช้คำสั่งด้านล่าง
```
docker build -t python-img .
```
> คำสั่งด้านบนคือสั่งให้ Docker ทำการสร้าง Image จาก Dockerfile ที่เราสร้างไว้ และให้มันตั้งชื่อ Image นี้ว่า `python-img` (ไม่ถูกใจจะตั้งชื่ออื่นก็แล้วแต่)

หลังจากที่เราใช้คำสั่งด้านบนไปละ Docker ก็จะทำการสร้าง Image ให้เราที่ชื่อว่า `python-img` ดังนั้นเราลองไปดูกันว่าเรามี Image ในเครื่องเป็นยังไงบ้างจากคำสั่งด้านล่าง
```
docker images
```
ตัว Docker ก็จะทำการโชว์ Image ทั้งหมดที่อยู่ในเครื่องเราออกมาประมาณนี้
```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
python-img          latest              d34e7d8d3aae        6 minutes ago       131MB
python              2.7-slim            eb40dcfcbc42        11 days ago         120MB
```
(ถ้าเราเคยเล่น Docker ไปบ้างอาจจะมี Image มากกว่าในตัวอย่างนะ) เราจะเห็น image หลักๆ 2 ตัวคือ `python-img` ที่ได้มาจากการคำสั่ง build ด้านบน และจะเห็น image อีกตัวคือ `python` ซึ่งมาจากที่ไฟล์ Dockerfile เราระบุไว้ว่าจะใช้ python version 2.7 slim ไงละ

โอเคร๊ะ!! สรุปคือเราสร้าง Image จาก Dockerfile ได้แล้วนะ คราวนี้เราจะมาลอง เอา Image ไปใช้งานกันดีกว่า

## การเอา Image ไปใช้ (สร้าง container)
การนำ Image ไปใช้นั้น เราก็ต้องใช้ Command line ตัวด้านล่างนี้ พิมพ์ตามเบย
```
docker run -p 4000:80 python-img
```
> ถ้าขั้นตอนนี้มี error ประมาณนี้ `docker: Error response from daemon: driver failed programming external connectivity on endpoint unruffled_galileo` ให้ restart docker 1 รอบนะครับ (คลิกขวาที่ Docker แล้วเลือก Restart)

คำสั่งนี้จะเอา image ที่ชื่อว่า python-img ไปจัดเตรียม environment ให้กับเรา ซึ่งอยู่ในรูปของสิ่งที่เรียกว่า **`Container`** ซึ่งเจ้า container เราระบุไว้ใน Dockerfile ว่าให้มันเปิด port 80 (port 80 คือ port ที่ใช้กับเว็บ) ดังนั้นถ้าเราอยากจะเข้าไปดูเว็บของเรา เราจะต้อง map port ของเราให้เข้ากับ port 80 ของ container ซึ่งในคำสั่งด้านบนเราจะ map port 4000 เข้ากับ port 80 ของ container (อ่านไม่รู้เรื่องก็พิมพ์ตามไปเหอะ)

ต่อมาเราก็จะลองเข้าไปดูเว็บไซต์ของเรา โดยเปิด chrome, IE, firefox, Safari อะไรก็ได้ขึ้นมาซักตัว แล้วพิมพ์ลงไปในช่อง URL ว่า [http://localhost:4000](http://localhost:4000) (หรือกดลิงค์นี้เลยก็ได้ -__-'') แล้วเราก็จะพบหน้าเว็บของเราประมาณนี้

![img](../imgs/app-in-browser.png)

ถ้าได้ตามนี้แล้วให้กับไปที่ command prompt, powerShell หรือ terminal อะไรก็ตามแต่ แล้วกด `CTRL + C` เพื่อกลับมาหน้าจอปรกติซะ

จะเห็นว่าเราสามารถสร้างเว็บไซต์จากภาษา python ได้แล้วโดยที่เราไม่จำเป็นต้องลง python และ Redis เลย (แม้ว่า Redis มันจะต่อไม่ได้ก็ตาม -.- ) ถัดมาเราจะมาทำความเข้าใจเรื่อง Container ต่อบ้าง

## Container คืออะไร
พูดแบบบ้านๆ Container คือ environment ที่ถูกสร้างขึ้นมาจาก Image นั่นเอง ซึ่งในตัวอย่างเราสร้าง container จาก Image ที่ชื่อว่า `python-img` ของเรา ซึ่งมันจะเตรียม environment ให้พร้อมกับ python 2.7 slim และมีการติดตั้ง `Flask` กับ `Redis` ให้เรายังไงละ

ดังนั้นเราลองไปดู container ต่างๆที่อยู่ในเครื่องเราบ้าง โดยใช้คำสั่งด้านล่างนี้
```
docker ps
```
แล้ว docker ก็จะโชว์ container ทั้งหมดออกมา ซึ่งก็จะได้ประมาณนี้
```
CONTAINER ID        IMAGE               COMMAND             CREATED
0fc9aede3718        python-img          "python app.py"     10 minutes ago
```

ซึ่งในตอนนี้ ถ้าเราไปเปิดเว็บ [http://localhost:4000](http://localhost:4000) ของเรา ก็จะพบว่ามันยังทำงานได้ตามปรกติ ดังนั้นถัดมาเราจะสั่งให้ docker หยุดการทำงานของ container นั้น โดยใช้คำสั่งด้านล่าง
```
docker stop 0fc
```
> รหัส `0fc` ของเครื่องแต่ละคนจะไม่เหมือนกัน ให้เพื่อนๆใส่ Container ID ที่โชว์ในเครื่องลงไปนะครับ (ไม่จำเป็นต้องใส่หมดก็ได้)

พอใช้คำสั่งด้านบนเสร็จ ลองกลับเข้าไปดูเว็บใหม่อีกครั้ง [http://localhost:4000](http://localhost:4000) ก็จะพบว่ามันไม่ทำงานแล้ว เพราะเราสั่งให้มันหยุดทำงานจากคำสั่งด้านบนนั่นเอง

คราวนี้ลองดู container ใหม่อีกครั้งด้วยคำสั่งเดิมดูดิ๊
```
docker ps
```
เราจะเห็นว่ามันโชว์ container ออกมาเป็นตามด้านล่าง คือไม่มี container อยู่เลย
```
CONTAINER ID        IMAGE               COMMAND             CREATED
```
จริงๆคำสั่ง `docker ps` มันจะโชว์เฉพาะ container ที่กำลังทำงานอยู่เท่านั้น แต่ถ้าเราอยากเห็น container ทุกตัว ให้เราต่อท้ายคำสั่งด้วย `-a`
```
docker ps -a
```
มันก็จะโชว์ container ทั้งหมดขึ้นมาตามเดิม แต่ตัวสถานะคือมันหยุดการทำงานไปแล้ว
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS
0fc9aede3718        python-img          "python app.py"     18 minutes ago      Exited (137) 3 minutes ago
```
ถ้าเราต้องการให้ container ที่หยุดไปแล้ว กลับมาทำงานใหม่อีกครั้ง เราสามารถทำได้โดยใช้คำสั่งด้านล่างนี้
```
docker start 0fc
```
หลังจากใช้คำสั่งด้านบนไปแล้ว ลองกลับเข้าไปดูเว็บใหม่อีกครั้ง [http://localhost:4000](http://localhost:4000) ก็จะพบว่ามันใช้งานได้เป็นปรกติแล้ว (ยกเว้น Redis เช่นเดิม -_-'')

ในตอนนี้เรามี Image ที่เตรียมไว้สำหรับทำงานกับ Python เรียบร้อยละ แล้วถ้าเกิดเราอยากเอาไปแชร์ให้เพื่อนใช้งานด้วย เพื่อให้มี environment แบบเดียวกับเราบ้างล่ะ? หรือจะเอาไปติดตั้งที่เซิฟเวอร์บ้างล่ะ? อย่างรอช้ากลับไปดูบทถัดไปกันเบย

## [การแชร์ Image ที่สร้างไว้](Share-your-image.md)