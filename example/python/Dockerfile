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