## วิธีที่ 1: ใช้ ngrok (แนะนำสำหรับ testing)
```
# 1. ติดตั้ง ngrok
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null
echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list
sudo apt update && sudo apt install ngrok

# 2. รัน Group ID Finder
node utils/findGroupId.js

# 3. รัน ngrok (Terminal ใหม่)
ngrok http 3201

# 4. Copy HTTPS URL ไปใส่ใน LINE Developers Console
# เช่น: https://abc123.ngrok.io/webhook/find-group
```

## วิธีที่ 2: ใช้ Cloudflare Tunnel (ไม่ต้องสมัคร)
```
# 1. ติดตั้ง
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64
chmod +x cloudflared-linux-amd64
sudo mv cloudflared-linux-amd64 /usr/local/bin/cloudflared

# 2. รัน Group ID Finder (Terminal 1)
node utils/findGroupId.js

# 3. รัน tunnel (Terminal 2)
cloudflared tunnel --url http://localhost:3201
```

###จะได้ผลลัพธ์แบบนี้:
```
2025-05-23T10:30:45Z INF +--------------------------------------------------------------------------------------------+
2025-05-23T10:30:45Z INF |  Your quick Tunnel has been created! Visit it at (it may take some time to be reachable): |
2025-05-23T10:30:45Z INF |  https://abc-def-ghi.trycloudflare.com                                                    |
2025-05-23T10:30:45Z INF +--------------------------------------------------------------------------------------------+
```

##ใช้ URL นี้ใน LINE Developers Console:
```
https://abc-def-ghi.trycloudflare.com/webhook/find-group
```

## วิธีที่ 3 วิธีใดเร็วที่สุดตอนนี้?
### ถ้าต้องการเร็วที่สุดและไม่ต้องสมัครอะไร ให้ใช้ localtunnel:
```
# 1. ติดตั้ง localtunnel
npm install -g localtunnel

# 2. รัน Group ID Finder
node utils/findGroupId.js

# 3. รัน localtunnel (Terminal ใหม่)
lt --port 3201

# จะได้ URL เช่น: https://funny-turkey-12.loca.lt
# ใช้: https://funny-turkey-12.loca.lt/webhook/find-group
```
