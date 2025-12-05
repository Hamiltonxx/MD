```python
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText

SMTP_SERVER = 'email-smtp.us-east-1.amazonaws.com'
SMTP_PORT = 587
SMTP_USERNAME = 'AKIATCKAOH63ODJKFPWQ'
SMTP_PASSWORD = 'BOfB9ZXxY2R2aw9/i36EHsWdoKHAfrb0kW5HOuz/3tFI'

SENDER = 'no-reply@bongos.ai'
#RECEIPIENT = 'huangguozheng@corp.the9.com'
RECEIPIENTS = ['huangguozheng@corp.the9.com','shili@corp.the9.com','hamiltonhgz@gmail.com']

# 邮件内容
SUBJECT = 'GPU Server Alert'
BODY_TEXT = f"cpu_usage:{cpu_usage}, mem_usage:{mem_usage}, gpu_usage:{gpu_usage}, gpu_mem_usage:{gpu_info[1]}/{gpu_info[2]}={gpu_mem_usage}"
BODY_HTML = f"""<html>
<head></head>
<body>
  <h2>GPU Server Alert</h2>
  <p>cpu_usage:{cpu_usage} <br> mem_usage:{mem_usage} <br> gpu_usage:{gpu_usage} <br> gpu_mem_usage:{gpu_info[1]}/{gpu_info[2]}={gpu_mem_usage}</p>
</body>
</html>"""

# 创建 MIME 对象
msg = MIMEMultipart('alternative')
msg['Subject'] = SUBJECT
msg['From'] = SENDER
#msg['To'] = RECEIPIENT
msg['To'] = ", ".join(RECEIPIENTS)

# 将纯文本和 HTML 内容添加到 MIME 对象
part1 = MIMEText(BODY_TEXT, 'plain')
part2 = MIMEText(BODY_HTML, 'html')
msg.attach(part1)
msg.attach(part2)

with smtplib.SMTP(SMTP_SERVER, SMTP_PORT) as server:
    server.starttls()
    server.login(SMTP_USERNAME, SMTP_PASSWORD)
#   server.sendmail(SENDER, RECEIPIENT, msg.as_string())
    server.sendmail(SENDER, RECEIPIENTS, msg.as_string())
    print('EMAIL sent successfully!')
```